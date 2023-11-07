### Create a Vault policy, token role, and token suitable for use by Vault administrators

#### - `Revoke your initial root token` since it is `more secure to not have root tokens in existence` except when absolutely needed.

#### - The token will be `a periodic token` that will automatically be renewed and never expire.

#### - It is possible to create new root tokens from your unseal or recovery keys.

#### - Inspect the `admin-policy.hcl`

#### - This is fairly long but gives a Vault administrator capabilities for most paths that they will need assuming they enable secrets engines and auth methods on the standard paths.

#### - In an actual Vault deployment, you would `determine which paths are needed in advance` and add them to this policy before revoking your root token.

#### - Additionally, the policy gives a token created from it access to common system paths and to all Vault Enterprise paths.

#### - Register the `admin` policy
```
# vault policy write admin /root/config-files/policies/admin-policy.hcl
Success! Uploaded policy: admin

# vault policy list
admin
default
root

# vault policy read admin
```

#### - Create an admin token (https://developer.hashicorp.com/vault/api-docs/auth/token#createupdate-token-role)
#### - Create a `Vault token role` called `admin` that is assigned the `admin policy` and is configured so that tokens created from it are `periodic and orphaned.`
```
vault write auth/token/roles/admin allowed_policies=admin token_period=2h orphan=true
```

#### Note that `a periodic token` is `automatically renewed` by Vault at the end of each period (two hours in this case) and `never expires.`

```
# vault read auth/token/roles/admin
Key                         Value
---                         -----
allowed_entity_aliases      <nil>
allowed_policies            [admin]
allowed_policies_glob       []
disallowed_policies         []
disallowed_policies_glob    []
explicit_max_ttl            0s
name                        admin
orphan                      true
path_suffix                 n/a
period                      0s
renewable                   true
token_explicit_max_ttl      0s
token_no_default_policy     false
token_period                2h
token_type                  default-service
```
#### `What is orphan token?`

#### We want the token created from the admin role to be `an orphan token` so that `it will not be revoked when you revoke the initial root token below.`

#### Generate a periodic, orphaned token with display name `admin-1` from the admin token role:
```
vault token create -display-name=admin-1 -role=admin
```
#### This should return something like this
```
# vault token create -display-name=admin-1 -role=admin
Key                  Value
---                  -----
token                hvs.CAESIFVPx8OyR6ZfS_7WJOLAG6Y1TD9LL1sJzEz4SMwHsqSrGiAKHGh2cy5KNzNySGJIcXVLTXZXa1oxelA3MjRDd24QIQ
token_accessor       rUTpcsnfjnfPyqPf1EJmop4n
token_duration       2h
token_renewable      true
token_policies       ["admin" "default"]
identity_policies    []
policies             ["admin" "default"]
```

#### We specified the token's display name as `admin-1` rather than admin since you might later want to create additional tokens from the admin role and could give them display names like `admin-2`, `admin-3`, etc.

#### Set the `VAULT_TOKEN` environment variable to your admin token with the following command.
```
export VAULT_TOKEN=hvs.CAESIFVPx8OyR6ZfS_7WJOLAG6Y1TD9LL1sJzEz4SMwHsqSrGiAKHGh2cy5KNzNySGJIcXVLTXZXa1oxelA3MjRDd24QIQ
```
#### You also need to replace the initial root token with your admin token in your `.bash_profile` file:
```
sed -i '/VAULT_TOKEN/d' /root/.bash_profile
echo "export VAULT_TOKEN=$VAULT_TOKEN" >> /root/.bash_profile
```

#### test that your token allows you to list Vault secrets engines and auth methods by running these commands:
#### These should return the currently activated secrets engines and auth methods.
```
vault secrets list
vault auth list
```

#### Finally, now that you know your admin token is working, you should revoke your root token with these commands:
```
export root_token=$(cat /root/config-files/initialization.txt | grep "Initial Root Token" | cut -d":" -f2 | cut -d" " -f2)

vault token revoke $root_token
```
#### This should return
```
# vault token revoke $root_token
Success! Revoked token (if it existed)
```
#### In the next challenge, you will generate a renewable token with limited TTL (time-to-live) and learn how to renew it before it expires.