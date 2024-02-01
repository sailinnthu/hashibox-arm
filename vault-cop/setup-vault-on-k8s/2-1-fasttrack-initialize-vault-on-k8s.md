# access Vault UI
kubectl port-forward pod/vault-0 -n vault 8200:8200 --address 0.0.0.0
(or)
kubectl port-forward svc/vault -n vault 8200:8200 --address 0.0.0.0

#### Install Vault
#### This creates three Vault server instances with an Integrated Storage (Raft) backend.
```
kubectl create ns vault
helm install vault hashicorp/vault --values helm-vault-raft-values.yaml -n vault
```
# shell access
kubectl exec --stdin=true --tty=true vault-0 -n vault -- /bin/sh

# vault operator init on vault-0
kubectl exec --stdin=true --tty=true vault-0 -n vault -- vault operator init

Unseal Key 1: 7Sxd+xHSKX4uoBthJ8sR0AGOLs1igttKIghQAPZtJiNt
Unseal Key 2: WQufr2Ak07h0gHGhyrBRYPf+C3mNrhyjAXNU1U2Ee5s5
Unseal Key 3: kO+c1eXxzHSWl/t67v+RBcOWtxmGq7CblihF9yonrGTL
Unseal Key 4: KBR6g59vlKR3iARrLkTqEA8hJ/KCcXPTm6ePDRnQhJRO
Unseal Key 5: 2thaLeqmVIhS88ith0+jzrEfEMcCyIvhS16WW4/FVvzO

Initial Root Token: hvs.vtyZbjOqkUT8GyejfVcYb0wo

# unseal vault-0
kubectl exec --stdin=true --tty=true vault-0 -n vault -- vault operator unseal

# vault-1
kubectl exec --stdin=true --tty=true vault-1 -n vault -- /bin/sh
vault operator raft join http://vault-0.vault-internal:8200
vault operator unseal

# vault-2
kubectl exec --stdin=true --tty=true vault-2 -n vault -- /bin/sh
vault operator raft join http://vault-0.vault-internal:8200
vault operator unseal