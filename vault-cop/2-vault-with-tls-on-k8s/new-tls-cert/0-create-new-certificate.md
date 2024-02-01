export WORKDIR=/home/vagrant/vault-cop/2-vault-with-tls-on-k8s/new-tls-cert
export VAULT_K8S_NAMESPACE="vault"
export VAULT_HELM_RELEASE_NAME="vault"
export VAULT_SERVICE_NAME="vault-internal"
export K8S_CLUSTER_NAME="cluster.local"

# generate private key
openssl genrsa -out ${WORKDIR}/vault.key 2048

# create CSR
cat > ${WORKDIR}/vault-csr.conf <<EOF
[req]
default_bits = 2048
prompt = no
encrypt_key = yes
default_md = sha256
distinguished_name = kubelet_serving
req_extensions = v3_req
[ kubelet_serving ]
O = system:nodes
CN = system:node:*.${VAULT_K8S_NAMESPACE}.svc.${K8S_CLUSTER_NAME}
[ v3_req ]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth, clientAuth
subjectAltName = @alt_names
[alt_names]
DNS.1 = *.${VAULT_SERVICE_NAME}
DNS.2 = *.${VAULT_SERVICE_NAME}.${VAULT_K8S_NAMESPACE}.svc.${K8S_CLUSTER_NAME}
DNS.3 = *.${VAULT_K8S_NAMESPACE}
IP.1 = 127.0.0.1
EOF

# generate CSR - vault.csr will out
openssl req -new -key ${WORKDIR}/vault.key -out ${WORKDIR}/vault.csr -config ${WORKDIR}/vault-csr.conf

# verify
openssl req --noout --text --in vault.csr

# `[new k8s resource] vaultnew.svc` Issue the Certificate with `86400000`
# create csr yaml file to send it to k8s (aka) creating k8s CSR resource
cat > ${WORKDIR}/csr.yaml <<EOF
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: vaultnew.svc
spec:
  signerName: kubernetes.io/kubelet-serving
  expirationSeconds: 86400000
  request: $(cat ${WORKDIR}/vault.csr|base64|tr -d '\n')
  usages:
  - digital signature
  - key encipherment
  - server auth
EOF


# send the CSR to k8s
kubectl create -f ${WORKDIR}/csr.yaml

kubectl get csr

# approve the CSR in k8s `vaultnew`
kubectl certificate approve vaultnew.svc

# confirm the certificate `vaultnew` was issued
kubectl get csr vaultnew.svc

# retrieve the `vaultnew.crt` certificate
kubectl get csr vaultnew.svc -o jsonpath='{.status.certificate}' | openssl base64 -d -A -out ${WORKDIR}/vault.crt

# verify - 2y270d expiry
openssl x509 --text -noout --in vault.crt

# retrieve k8s CA
kubectl config view \
--raw \
--minify \
--flatten \
-o jsonpath='{.clusters[].cluster.certificate-authority-data}' \
| base64 -d > ${WORKDIR}/vault.ca

# verify
openssl verify -CAfile vault.ca vault.crt

# [not required] create namespace for vault
kubectl create namespace $VAULT_K8S_NAMESPACE

# `Override` the TLS secret `(override the secret)`
# `--dry-run=client`
# `--save-config`
kubectl create secret generic vault-ha-tls \
   -n $VAULT_K8S_NAMESPACE \
   --dry-run=client \
   --save-config \
   --from-file=vault.key=${WORKDIR}/vault.key \
   --from-file=vault.crt=${WORKDIR}/vault.crt \
   --from-file=vault.ca=${WORKDIR}/vault.ca -o yaml | kubectl apply -f -

# next step is to rotate cert
# go to `1-rotating-tls-cert.md`