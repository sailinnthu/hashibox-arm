#### Initialize vault-server `vault-0` using `vault operator init` command.
#### Initialization is the process by which Vault's storage backend is prepared to receive data.
#### Since Vault servers share the same storage backend in HA mode, you `only need to initialize one Vault` to initialize the storage backend.
```
$ kubectl -n vault exec vault-0 -- vault operator init

Unseal Key 1: +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Unseal Key 2: xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Unseal Key 3: P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Unseal Key 4: k9ueYoNGSyqOsnCYDwR1mRULCxCWTNHrjHPUO0u24hNV
Unseal Key 5: ssUbluO640DSNBnY3Td/uDr0IVGqUmeHD59Rs4QV5AZJ

Initial Root Token: hvs.vFmEdPa3EJXH6OpfKM9j50LC

Vault initialized with 5 key shares and a key threshold of 3. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 3 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated root key. Without at least 3 keys to
reconstruct the root key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.

```
#### `vault operator init` command cannot be run against already-initialized Vault cluster.
```
$ kubectl -n vault exec vault-0 -- vault operator init
Error initializing: Error making API request.

URL: PUT http://127.0.0.1:8200/v1/sys/init
Code: 400. Errors:

* Vault is already initialized
command terminated with exit code 2
```
#### Exploring the vault-server PODs status using `vault status` command again
#### `Initialized` is `true` now
#### `Sealed` is still `true`
#### `Total Shares` is `5`
#### `Threshold` is `3`
#### `Unseal Progress` is `0/3` now

```
$ kubectl -n vault exec vault-0 -- vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        false
Sealed             true
Total Shares       0
Threshold          0
Unseal Progress    0/0
Unseal Nonce       n/a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true
command terminated with exit code 2

```
#### 1st Unseal for vault-0
```
$ kubectl -n vault exec vault-0 -- vault operator unseal +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    1/3
Unseal Nonce       32e5f949-22b6-f707-53cb-e81c55ad2c25
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

```
#### 2nd Unseal for vault-0
```
$ kubectl -n vault exec vault-0 -- vault operator unseal xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    2/3
Unseal Nonce       32e5f949-22b6-f707-53cb-e81c55ad2c25
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

```
#### 3rd Unseal for vault-0
```
$ kubectl -n vault exec vault-0 -- vault operator unseal P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
Version                 1.15.1
Build Date              2023-10-20T19:16:11Z
Storage Type            raft
Cluster Name            vault-cluster-bbf0c932
Cluster ID              899e17f9-97fc-bc21-50db-a4eb1cf7defc
HA Enabled              true
HA Cluster              https://vault-0.vault-internal:8201
HA Mode                 active
Active Since            2023-11-06T10:08:30.248994017Z
Raft Committed Index    52
Raft Applied Index      52

```
#### Exploring the vault-server PODs status using `vault status` command again
#### `Sealed` is still `false` now
#### Take note of `Cluster Name`, `Cluster ID`, `HA Enabled`, `HA Cluster`, and `HA Mode` now

```
$ kubectl -n vault exec vault-0 -- vault status
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
Version                 1.15.1
Build Date              2023-10-20T19:16:11Z
Storage Type            raft
Cluster Name            vault-cluster-bbf0c932
Cluster ID              899e17f9-97fc-bc21-50db-a4eb1cf7defc
HA Enabled              true
HA Cluster              https://vault-0.vault-internal:8201
HA Mode                 active
Active Since            2023-11-06T10:08:30.248994017Z
Raft Committed Index    53
Raft Applied Index      53

```
#### `vault-0` pod is up and running now.
```
$ kubectl get pods -n vault
NAME                                    READY   STATUS    RESTARTS   AGE
vault-0                                 1/1     Running   0          5m9s
vault-1                                 0/1     Running   0          5m9s
vault-2                                 0/1     Running   0          5m9s
vault-agent-injector-5789598656-km5nb   1/1     Running   0          5m10s

```

#### Before Unsealing vault-1, join to the RAFT Clusters `http://vault-0.vault-internal:8200`
```
$ kubectl -n vault exec vault-1 -- vault operator raft join http://vault-0.vault-internal:8200
Key       Value
---       -----
Joined    true

$ kubectl -n vault exec vault-1 -- vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    0/3
Unseal Nonce       n/a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true
command terminated with exit code 2

```
#### 1st, 2nd and 3rd Unseal for vault-1
```
Unseal Key 1: +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Unseal Key 2: xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Unseal Key 3: P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Unseal Key 4: k9ueYoNGSyqOsnCYDwR1mRULCxCWTNHrjHPUO0u24hNV
Unseal Key 5: ssUbluO640DSNBnY3Td/uDr0IVGqUmeHD59Rs4QV5AZJ

Initial Root Token: hvs.vFmEdPa3EJXH6OpfKM9j50LC

kubectl -n vault exec vault-1 -- vault operator unseal +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
kubectl -n vault exec vault-1 -- vault operator unseal xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
kubectl -n vault exec vault-1 -- vault operator unseal P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6

$ kubectl -n vault exec vault-1 -- vault operator unseal +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    1/3
Unseal Nonce       ca26377c-fb23-66cf-b498-988ff56f038a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-1 -- vault operator unseal xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    2/3
Unseal Nonce       ca26377c-fb23-66cf-b498-988ff56f038a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-1 -- vault operator unseal P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    0/3
Unseal Nonce       n/a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-1 -- vault status
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
Version                 1.15.1
Build Date              2023-10-20T19:16:11Z
Storage Type            raft
Cluster Name            vault-cluster-bbf0c932
Cluster ID              899e17f9-97fc-bc21-50db-a4eb1cf7defc
HA Enabled              true
HA Cluster              https://vault-0.vault-internal:8201
HA Mode                 standby
Active Node Address     http://10.247.1.6:8200
Raft Committed Index    63
Raft Applied Index      63

```
#### To Verify RAFT list-peers, vault login to vault-0 using Root Token
```
$ kubectl -n vault exec vault-0 -- vault login hvs.vFmEdPa3EJXH6OpfKM9j50LC

Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                hvs.vFmEdPa3EJXH6OpfKM9j50LC
token_accessor       hKY7puYVfs30OLeFmbyE286J
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]

$ kubectl -n vault exec vault-0 -- vault operator raft list-peers
Node                                    Address                        State       Voter
----                                    -------                        -----       -----
1c254522-2109-103a-6f6b-86e8313bd934    vault-0.vault-internal:8201    leader      true
dc1968b5-620a-cd8c-284c-c6badefe09eb    vault-1.vault-internal:8201    follower    true

```
#### Before Unsealing vault-2, join to the RAFT Clusters `http://vault-0.vault-internal:8200`
```
$ kubectl -n vault exec vault-2 -- vault operator raft join http://vault-0.vault-internal:8200
Key       Value
---       -----
Joined    true

$ kubectl -n vault exec vault-2 -- vault status
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    0/3
Unseal Nonce       n/a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true
command terminated with exit code 2

```

#### 1st, 2nd and 3rd Unseal for vault-2
```
Unseal Key 1: +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Unseal Key 2: xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Unseal Key 3: P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Unseal Key 4: k9ueYoNGSyqOsnCYDwR1mRULCxCWTNHrjHPUO0u24hNV
Unseal Key 5: ssUbluO640DSNBnY3Td/uDr0IVGqUmeHD59Rs4QV5AZJ

Initial Root Token: hvs.vFmEdPa3EJXH6OpfKM9j50LC

kubectl -n vault exec vault-2 -- vault operator unseal +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
kubectl -n vault exec vault-2 -- vault operator unseal xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
kubectl -n vault exec vault-2 -- vault operator unseal P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6

$ kubectl -n vault exec vault-2 -- vault operator unseal +G06RPGlc9UDzev2WhKn1XWf4XtDShPbpxwyt0yPoere
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    1/3
Unseal Nonce       11115e8b-fa22-6373-818a-f7ef0c25c48e
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-2 -- vault operator unseal xgVf6m6oLmK54LSnt+bE85dtDk47q3olpEQUS39dxT0r
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    2/3
Unseal Nonce       11115e8b-fa22-6373-818a-f7ef0c25c48e
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-2 -- vault operator unseal P+rTmpjUWdv0cIZZ+eeAK0TK+xZ2Hbk7gj+MwU88LoZ6
Key                Value
---                -----
Seal Type          shamir
Initialized        true
Sealed             true
Total Shares       5
Threshold          3
Unseal Progress    0/3
Unseal Nonce       n/a
Version            1.15.1
Build Date         2023-10-20T19:16:11Z
Storage Type       raft
HA Enabled         true

$ kubectl -n vault exec vault-2 -- vault status
Key                     Value
---                     -----
Seal Type               shamir
Initialized             true
Sealed                  false
Total Shares            5
Threshold               3
Version                 1.15.1
Build Date              2023-10-20T19:16:11Z
Storage Type            raft
Cluster Name            vault-cluster-bbf0c932
Cluster ID              899e17f9-97fc-bc21-50db-a4eb1cf7defc
HA Enabled              true
HA Cluster              https://vault-0.vault-internal:8201
HA Mode                 standby
Active Node Address     http://10.247.1.6:8200
Raft Committed Index    72
Raft Applied Index      72
```
#### Verify RAFT list-peers
```
$ kubectl -n vault exec vault-0 -- vault operator raft list-peers
Node                                    Address                        State       Voter
----                                    -------                        -----       -----
1c254522-2109-103a-6f6b-86e8313bd934    vault-0.vault-internal:8201    leader      true
dc1968b5-620a-cd8c-284c-c6badefe09eb    vault-1.vault-internal:8201    follower    true
092c22d7-5234-4b8e-4d72-96b479488d17    vault-2.vault-internal:8201    follower    true

```
#### vault-server pods are running now.
```
$ kubectl get all -n vault -o wide
NAME                                        READY   STATUS    RESTARTS   AGE   IP           NODE          NOMINATED NODE   READINESS GATES
pod/vault-0                                 1/1     Running   0          16m   10.247.1.6   127-worker    <none>           <none>
pod/vault-1                                 1/1     Running   0          16m   10.247.2.4   127-worker2   <none>           <none>
pod/vault-2                                 1/1     Running   0          16m   10.247.1.5   127-worker    <none>           <none>
pod/vault-agent-injector-5789598656-km5nb   1/1     Running   0          16m   10.247.1.2   127-worker    <none>           <none>

NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)             AGE   SELECTOR
service/vault                      ClusterIP      10.127.30.155    <none>           8200/TCP,8201/TCP   16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault,component=server
service/vault-active               ClusterIP      10.127.136.53    <none>           8200/TCP,8201/TCP   16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault,component=server,vault-active=true
service/vault-agent-injector-svc   ClusterIP      10.127.186.88    <none>           443/TCP             16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault-agent-injector,component=webhook
service/vault-internal             ClusterIP      None             <none>           8200/TCP,8201/TCP   16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault,component=server
service/vault-standby              ClusterIP      10.127.125.186   <none>           8200/TCP,8201/TCP   16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault,component=server,vault-active=false
service/vault-ui                   LoadBalancer   10.127.79.186    172.18.255.220   8200:30372/TCP      16m   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault,component=server

NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS         IMAGES                      SELECTOR
deployment.apps/vault-agent-injector   1/1     1            1           16m   sidecar-injector   hashicorp/vault-k8s:1.3.1   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault-agent-injector,component=webhook

NAME                                              DESIRED   CURRENT   READY   AGE   CONTAINERS         IMAGES                      SELECTOR
replicaset.apps/vault-agent-injector-5789598656   1         1         1       16m   sidecar-injector   hashicorp/vault-k8s:1.3.1   app.kubernetes.io/instance=vault,app.kubernetes.io/name=vault-agent-injector,component=webhook,pod-template-hash=5789598656

NAME                     READY   AGE   CONTAINERS   IMAGES
statefulset.apps/vault   3/3     16m   vault        hashicorp/vault:1.15.1
```