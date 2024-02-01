### SIGHUP doesn’t trigger pod restart or leader re-election.
### Ref: https://support.hashicorp.com/hc/en-us/articles/5767318985107-Vault-SIGHUP-Behavior

### Verify using openssl tool (Before Cert Rotation)
```
cd /home/vagrant/k8s-cop/1-singlecluster/sample-apps
kubectl apply -f openssltool.yaml -n vault
kubectl exec -it openssltool -n vault -- sh
```

### VAULT_API_ADDR is `vault-active` svc `10.123.97.23` in this case
```
openssl s_client -showcerts -connect 10.123.97.23:8200
```

###
kubectl exec --stdin=true --tty=true vault-0 -n vault -- /bin/sh
###

* we can verify `cat /vault/userconfig/vault-ha-tls/vault.crt`
* we can verify `cat /vault/userconfig/vault-ha-tls/vault.key`
* we can verify `cat /vault/userconfig/vault-ha-tls/vault.ca`

### Let's rotate `kill -SIGHUP $(pidof vault)`
```
kubectl exec --stdin=true --tty=true vault-0 -n vault -- sh 
kill -SIGHUP $(pidof vault)

kubectl exec --stdin=true --tty=true vault-1 -n vault -- sh
kill -SIGHUP $(pidof vault) 

kubectl exec --stdin=true --tty=true vault-2 -n vault -- sh
kill -SIGHUP $(pidof vault)
```

### Recommendation
* Update the new certificate to K8s secrets.
* Send a SIGHUP to Vault nodes one by one.
* Certs on Vault nodes will be updated.

### Notes
1. Can be done on any sequence - even start on leader.
2. There will be no leader re-election.
3. There will be no change to voter state.
4. There will be no removal of any nodes, and rejoin of nodes back to cluster.
5. Least impactful.

### Verify in Browser
```
kubectl port-forward svc/vault -n vault 8200:8200 --address 0.0.0.0

https://192.168.56.85:8200
```

### Verify using openssl tool (After Cert Rotation)
```
cd /home/vagrant/k8s-cop/1-singlecluster/sample-apps
```
```
kubectl apply -f openssltool.yaml -n vault
kubectl exec -it openssltool -n vault -- sh
```
### VAULT_API_ADDR is `vault-active` svc `10.123.97.23` in this case
```
openssl s_client -showcerts -connect 10.123.97.23:8200
```