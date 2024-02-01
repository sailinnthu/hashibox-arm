### access vault UI
```
kubectl port-forward pod/vault-0 -n vault 8200:8200 --address 0.0.0.0
(or)
kubectl port-forward svc/vault -n vault 8200:8200 --address 0.0.0.0
```

### shell access
```
kubectl exec --stdin=true --tty=true vault-0 -n vault -- /bin/sh
```

### `export VAULT_SKIP_VERIFY=true`
* if `VAULT_CACERT` is not provided as `extraEnvironmentVars`, we need to `export VAULT_SKIP_VERIFY=true` before initialize.
* Technically, it is optional for Vault initialization.
* It is purely for cli usage.

### vault operator init on vault-0
```
kubectl exec --stdin=true --tty=true vault-0 -n vault -- vault operator init
```

```
Unseal Key 1: sgNHQrL493OdMl0tELhA4QI5VKYm1vcteb/L9ovjDra1
Unseal Key 2: Z5KzWsO9wCpM8tkQ9MZPY9Nb+QPTwshcowgiiYLQzHq8
Unseal Key 3: J0M96YyndlTU4ffHTLAblv/nfDOGYY1tRmDuSWzButCs
Unseal Key 4: vf4tPPMWSrQjeyy7aUJgj5rAjtIObcAmxO0u1VnVT1sT
Unseal Key 5: 7C9Vqslrsnvn1tqwqdd9VrbzuKwhtrKphyMopU3n3g+5

Initial Root Token: hvs.lzwk8Hk7CAAbgLHYeHvWOIp9
```

### unseal vault-0
```
kubectl exec --stdin=true --tty=true vault-0 -n vault -- vault operator unseal
```

### vault-1
```
kubectl exec --stdin=true --tty=true vault-1 -n vault -- /bin/sh
```
```
<!-- vault operator raft join http://vault-0.vault-internal:8200 -->
```
```
vault operator raft join -address=https://vault-1.vault-internal:8200 -leader-ca-cert="$(cat /vault/userconfig/vault-ha-tls/vault.ca)" -leader-client-cert="$(cat /vault/userconfig/vault-ha-tls/vault.crt)" -leader-client-key="$(cat /vault/userconfig/vault-ha-tls/vault.key)" https://vault-0.vault-internal:8200

vault operator unseal
```

### vault-2
```
kubectl exec --stdin=true --tty=true vault-2 -n vault -- /bin/sh
```
```
<!-- vault operator raft join http://vault-0.vault-internal:8200 -->
```
```
vault operator raft join -address=https://vault-2.vault-internal:8200 -leader-ca-cert="$(cat /vault/userconfig/vault-ha-tls/vault.ca)" -leader-client-cert="$(cat /vault/userconfig/vault-ha-tls/vault.crt)" -leader-client-key="$(cat /vault/userconfig/vault-ha-tls/vault.key)" https://vault-0.vault-internal:8200

vault operator unseal
```

## Let's rotate the certificate
### go to new-tls-cert directory