### Generate a renewable token with limited TTL (time-to-live) and learn how to renew it before it expires.

#### https://developer.hashicorp.com/vault/docs/concepts/tokens#token-time-to-live-periodic-tokens-and-explicit-max-ttls

#### Create a renewable token
#### Learn how to extend it's life by renewing it before it's TTL (time-to-live) expires. You will also see that you cannot extend its life beyond its maximum TTL.

#### create a new token with the admin policy that has a TTL of 2 minutes and a max TTL of 4 minutes
```
vault token create -display-name=renewable-admin -ttl=2m -explicit-max-ttl=4m -renewable=true -policy=admin
Key                  Value
---                  -----
token                hvs.CAESIKOXs4xvq3WNaCEOQHK-yuClpqPJMUGdpZAhGeCgVsA7GiAKHGh2cy5tUXpPUWtXOFRVbGU1bElmZjZVZU9BNmoQLA
token_accessor       A7PrdRsB9k8KJHB8HoE2xcto
token_duration       2m
token_renewable      true
token_policies       ["admin" "default"]
identity_policies    []
policies             ["admin" "default"]
```
#### Note that `token_duration` is 2m which means 2 minutes. You actually `did not need to include -renewable=true` since that is the default. But we wanted to make it explicit that the token is renewable.
```
export VAULT_TOKEN=hvs.CAESIKOXs4xvq3WNaCEOQHK-yuClpqPJMUGdpZAhGeCgVsA7GiAKHGh2cy5tUXpPUWtXOFRVbGU1bElmZjZVZU9BNmoQLA
```
#### View the TTL of the token (aka) Check the time left on the token.
```
# vault token lookup | grep ttl
creation_ttl        2m
explicit_max_ttl    4m
ttl                 12s
```
#### You can re-run the above command periodically to check the time left on the token.
```
vault token renew

```
#### After waiting about 90 seconds, renew the token again:
```
vault token renew
```
#### You will probably now get a message like:
```
WARNING! The following warnings were returned from Vault:

  * TTL of "2m" exceeded the effective max_ttl of "1m40s"; TTL value is capped
  accordingly
```