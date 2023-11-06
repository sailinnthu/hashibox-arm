#### Accessing Vault UI via port-forwarding
```
kubectl port-forward pod/vault-0 -n vault 8200:8200 --address 0.0.0.0
(or)
kubectl port-forward svc/vault -n vault 8200:8200 --address 0.0.0.0
```
#### Access via Browser
```
http://192.168.56.85:8200/
```