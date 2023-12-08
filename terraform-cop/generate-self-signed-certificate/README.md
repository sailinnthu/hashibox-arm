## How to generate self-signed TLS certificate for Vault

```
cd generate-self-signed-certificate/
```

#### Run `terraform init`
```
$ terraform init

Initializing the backend...

Initializing provider plugins...
- Finding hashicorp/tls versions matching ">= 4.0.4"...
- Finding hashicorp/local versions matching "2.4.0"...
- Installing hashicorp/tls v4.0.5...
- Installed hashicorp/tls v4.0.5 (signed by HashiCorp)
- Installing hashicorp/local v2.4.0...
- Installed hashicorp/local v2.4.0 (signed by HashiCorp)

Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.

Terraform has been successfully initialized!
```

#### Run `terraform plan`

```
$ terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.ca will be created
  + resource "local_file" "ca" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./ca.pub"
      + id                   = (known after apply)
    }

  # local_file.private_key will be created
  + resource "local_file" "private_key" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./vault-private.key"
      + id                   = (known after apply)
    }

  # local_file.pub_key_no_root will be created
  + resource "local_file" "pub_key_no_root" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./vault-public.pub"
      + id                   = (known after apply)
    }

  # tls_cert_request.server will be created
  + resource "tls_cert_request" "server" {
      + cert_request_pem = (known after apply)
      + dns_names        = [
          + "*.hashibox.io",
          + "localhost",
          + "*.ec2.internal",
        ]
      + id               = (known after apply)
      + ip_addresses     = [
          + "127.0.0.1",
        ]
      + key_algorithm    = (known after apply)
      + private_key_pem  = (sensitive value)

      + subject {
          + common_name = "*.*.hashibox.io"
        }
    }

  # tls_locally_signed_cert.server will be created
  + resource "tls_locally_signed_cert" "server" {
      + allowed_uses          = [
          + "client_auth",
          + "digital_signature",
          + "key_agreement",
          + "key_encipherment",
          + "server_auth",
        ]
      + ca_cert_pem           = (known after apply)
      + ca_key_algorithm      = (known after apply)
      + ca_private_key_pem    = (sensitive value)
      + cert_pem              = (known after apply)
      + cert_request_pem      = (known after apply)
      + early_renewal_hours   = 0
      + id                    = (known after apply)
      + is_ca_certificate     = false
      + ready_for_renewal     = false
      + set_subject_key_id    = false
      + validity_end_time     = (known after apply)
      + validity_period_hours = 720
      + validity_start_time   = (known after apply)
    }

  # tls_private_key.ca will be created
  + resource "tls_private_key" "ca" {
      + algorithm                     = "RSA"
      + ecdsa_curve                   = "P224"
      + id                            = (known after apply)
      + private_key_openssh           = (sensitive value)
      + private_key_pem               = (sensitive value)
      + private_key_pem_pkcs8         = (sensitive value)
      + public_key_fingerprint_md5    = (known after apply)
      + public_key_fingerprint_sha256 = (known after apply)
      + public_key_openssh            = (known after apply)
      + public_key_pem                = (known after apply)
      + rsa_bits                      = 2048
    }

  # tls_private_key.server will be created
  + resource "tls_private_key" "server" {
      + algorithm                     = "RSA"
      + ecdsa_curve                   = "P224"
      + id                            = (known after apply)
      + private_key_openssh           = (sensitive value)
      + private_key_pem               = (sensitive value)
      + private_key_pem_pkcs8         = (sensitive value)
      + public_key_fingerprint_md5    = (known after apply)
      + public_key_fingerprint_sha256 = (known after apply)
      + public_key_openssh            = (known after apply)
      + public_key_pem                = (known after apply)
      + rsa_bits                      = 2048
    }

  # tls_self_signed_cert.ca will be created
  + resource "tls_self_signed_cert" "ca" {
      + allowed_uses          = [
          + "cert_signing",
          + "crl_signing",
        ]
      + cert_pem              = (known after apply)
      + early_renewal_hours   = 0
      + id                    = (known after apply)
      + is_ca_certificate     = true
      + key_algorithm         = (known after apply)
      + private_key_pem       = (sensitive value)
      + ready_for_renewal     = false
      + set_authority_key_id  = false
      + set_subject_key_id    = false
      + validity_end_time     = (known after apply)
      + validity_period_hours = 720
      + validity_start_time   = (known after apply)

      + subject {
          + common_name = "ca.*.hashibox.io"
        }
    }

Plan: 8 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + ca      = "./ca.pub"
  + private = "./vault-private.key"
  + public  = "./vault-public.pub"
```

#### Run `terraform apply`

```
$ terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # local_file.ca will be created
  + resource "local_file" "ca" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./ca.pub"
      + id                   = (known after apply)
    }

  # local_file.private_key will be created
  + resource "local_file" "private_key" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./vault-private.key"
      + id                   = (known after apply)
    }

  # local_file.pub_key_no_root will be created
  + resource "local_file" "pub_key_no_root" {
      + content              = (known after apply)
      + content_base64sha256 = (known after apply)
      + content_base64sha512 = (known after apply)
      + content_md5          = (known after apply)
      + content_sha1         = (known after apply)
      + content_sha256       = (known after apply)
      + content_sha512       = (known after apply)
      + directory_permission = "0777"
      + file_permission      = "0777"
      + filename             = "./vault-public.pub"
      + id                   = (known after apply)
    }

  # tls_cert_request.server will be created
  + resource "tls_cert_request" "server" {
      + cert_request_pem = (known after apply)
      + dns_names        = [
          + "*.hashibox.io",
          + "localhost",
          + "*.ec2.internal",
        ]
      + id               = (known after apply)
      + ip_addresses     = [
          + "127.0.0.1",
        ]
      + key_algorithm    = (known after apply)
      + private_key_pem  = (sensitive value)

      + subject {
          + common_name = "*.*.hashibox.io"
        }
    }

  # tls_locally_signed_cert.server will be created
  + resource "tls_locally_signed_cert" "server" {
      + allowed_uses          = [
          + "client_auth",
          + "digital_signature",
          + "key_agreement",
          + "key_encipherment",
          + "server_auth",
        ]
      + ca_cert_pem           = (known after apply)
      + ca_key_algorithm      = (known after apply)
      + ca_private_key_pem    = (sensitive value)
      + cert_pem              = (known after apply)
      + cert_request_pem      = (known after apply)
      + early_renewal_hours   = 0
      + id                    = (known after apply)
      + is_ca_certificate     = false
      + ready_for_renewal     = false
      + set_subject_key_id    = false
      + validity_end_time     = (known after apply)
      + validity_period_hours = 720
      + validity_start_time   = (known after apply)
    }

  # tls_private_key.ca will be created
  + resource "tls_private_key" "ca" {
      + algorithm                     = "RSA"
      + ecdsa_curve                   = "P224"
      + id                            = (known after apply)
      + private_key_openssh           = (sensitive value)
      + private_key_pem               = (sensitive value)
      + private_key_pem_pkcs8         = (sensitive value)
      + public_key_fingerprint_md5    = (known after apply)
      + public_key_fingerprint_sha256 = (known after apply)
      + public_key_openssh            = (known after apply)
      + public_key_pem                = (known after apply)
      + rsa_bits                      = 2048
    }

  # tls_private_key.server will be created
  + resource "tls_private_key" "server" {
      + algorithm                     = "RSA"
      + ecdsa_curve                   = "P224"
      + id                            = (known after apply)
      + private_key_openssh           = (sensitive value)
      + private_key_pem               = (sensitive value)
      + private_key_pem_pkcs8         = (sensitive value)
      + public_key_fingerprint_md5    = (known after apply)
      + public_key_fingerprint_sha256 = (known after apply)
      + public_key_openssh            = (known after apply)
      + public_key_pem                = (known after apply)
      + rsa_bits                      = 2048
    }

  # tls_self_signed_cert.ca will be created
  + resource "tls_self_signed_cert" "ca" {
      + allowed_uses          = [
          + "cert_signing",
          + "crl_signing",
        ]
      + cert_pem              = (known after apply)
      + early_renewal_hours   = 0
      + id                    = (known after apply)
      + is_ca_certificate     = true
      + key_algorithm         = (known after apply)
      + private_key_pem       = (sensitive value)
      + ready_for_renewal     = false
      + set_authority_key_id  = false
      + set_subject_key_id    = false
      + validity_end_time     = (known after apply)
      + validity_period_hours = 720
      + validity_start_time   = (known after apply)

      + subject {
          + common_name = "ca.*.hashibox.io"
        }
    }

Plan: 8 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + ca      = "./ca.pub"
  + private = "./vault-private.key"
  + public  = "./vault-public.pub"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

tls_private_key.server: Creating...
tls_private_key.ca: Creating...
tls_private_key.server: Creation complete after 0s [id=f25ba86e5c975832041eab5eb1164a51f07aec21]
local_file.private_key: Creating...
tls_cert_request.server: Creating...
tls_private_key.ca: Creation complete after 0s [id=07d5cef3f081d40c90d49bddc3019864994d74b4]
local_file.private_key: Creation complete after 0s [id=cbbd5c47b44b0bfcc8abc3cb45aab76d4f0a6876]
tls_cert_request.server: Creation complete after 0s [id=c0841988324e9642abb9ea527f4842aa3e4b2966]
tls_self_signed_cert.ca: Creating...
tls_self_signed_cert.ca: Creation complete after 0s [id=245586057039120093646698208130807265708]
local_file.ca: Creating...
tls_locally_signed_cert.server: Creating...
local_file.ca: Creation complete after 0s [id=7feff31d2159ec355346874fa64be5dc25d0a4b8]
tls_locally_signed_cert.server: Creation complete after 0s [id=58755056115588933138243139442412566826]
local_file.pub_key_no_root: Creating...
local_file.pub_key_no_root: Creation complete after 0s [id=6c8c612c5919b69ba92ab6229db3af22132b94e7]

Apply complete! Resources: 8 added, 0 changed, 0 destroyed.

Outputs:

ca = "./ca.pub"
private = "./vault-private.key"
public = "./vault-public.pub"
```

#### Verify using `openssl` command

```
$ openssl x509 --noout --text -in ca.pub 
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            b8:c2:27:74:3f:a6:27:32:2c:b7:e3:d9:d6:86:79:ac
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = ca.*.hashibox.io
        Validity
            Not Before: Dec  8 14:23:05 2023 GMT
            Not After : Jan  7 14:23:05 2024 GMT
        Subject: CN = ca.*.hashibox.io
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:b6:58:70:96:70:56:8e:bf:30:f9:6e:8c:1c:68:
                    40:5b:de:68:a9:3d:15:1f:36:78:0f:1b:d2:b5:f1:
                    c9:7a:6d:8a:bd:59:91:6c:4f:f2:6d:26:ef:56:57:
                    46:3c:c1:52:f1:ed:80:30:0a:b1:e7:29:ed:6b:c7:
                    51:b8:3e:4d:b5:0e:9b:0b:d0:05:95:cd:60:b3:29:
                    97:7e:bb:17:a2:f0:bc:a0:f3:65:82:15:99:94:ec:
                    64:65:59:2d:e1:40:78:7b:54:b2:8a:c2:56:4f:20:
                    b8:2e:b6:69:3a:db:98:43:90:71:aa:f6:e1:a5:40:
                    8a:3d:1e:ee:53:4f:a6:4d:f0:89:e0:dc:ab:54:d1:
                    d7:b0:63:f0:c3:bd:d6:41:ea:52:3b:76:0e:b2:12:
                    72:1d:34:e8:49:a5:be:d2:c6:51:24:3a:70:a5:a4:
                    53:d0:b9:b5:30:75:af:0b:a6:09:18:48:16:73:f4:
                    76:95:c1:32:e7:ec:b1:7d:03:52:2b:95:80:d6:df:
                    32:16:aa:8c:28:7d:96:6e:bd:be:41:0a:fa:9b:60:
                    72:aa:31:d6:68:08:e8:c0:36:58:4d:b1:f2:46:54:
                    8f:a9:e9:a6:64:62:65:13:8d:e0:81:5c:aa:d2:ae:
                    02:11:80:b6:77:89:b6:47:35:89:3c:9d:02:8d:8b:
                    e6:43
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Certificate Sign, CRL Sign
            X509v3 Basic Constraints: critical
                CA:TRUE
            X509v3 Subject Key Identifier: 
                BA:51:38:45:4F:99:E9:CC:F9:56:20:09:8E:25:68:64:43:90:5A:A4
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        1f:1d:2f:56:be:e5:b8:32:be:a6:3d:1e:62:ee:89:54:78:46:
        a9:98:3c:f6:6c:7f:6f:f0:fd:22:40:d7:af:37:88:fe:75:fb:
        9c:80:9c:b1:39:84:62:d4:14:18:73:aa:3d:f1:77:d7:89:d1:
        b3:11:00:91:c7:65:d5:a0:65:8f:13:9a:eb:ea:61:0c:b6:28:
        90:d8:5b:0f:d3:8f:6a:ef:4d:a7:9e:60:f1:dc:62:14:f8:0a:
        91:4c:f5:98:69:26:a4:54:67:68:1f:08:5a:e5:01:80:1b:ae:
        6c:86:be:c1:91:f8:47:28:c2:c5:52:db:ef:78:96:6d:d1:26:
        62:6a:97:61:cd:4d:87:d7:ca:ed:4b:40:42:c3:96:3a:85:ef:
        58:b6:10:78:40:8b:a9:93:d3:ab:73:fe:e1:46:3e:4e:73:12:
        cc:e4:4e:66:fa:15:34:24:14:bd:5a:90:e2:ea:3d:e1:4d:0d:
        ce:ef:22:55:fc:96:b5:c5:bc:f1:79:a8:07:d9:15:9e:89:ad:
        2e:54:62:a3:ce:00:1e:ca:15:47:b2:b5:8b:8e:56:1f:77:dd:
        b3:e6:84:27:29:fc:97:18:e6:3c:73:82:ed:6d:c1:95:9a:07:
        1b:2b:7c:c4:16:b8:55:cf:69:3a:a7:f3:dd:af:b1:77:da:8b:
        02:b3:5e:e8
```

```
$ openssl x509 --noout --text -in vault-public.pub 
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            2c:33:cf:ec:0e:f8:5e:57:9b:26:7d:8b:7c:eb:39:2a
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: CN = ca.*.hashibox.io
        Validity
            Not Before: Dec  8 14:23:05 2023 GMT
            Not After : Jan  7 14:23:05 2024 GMT
        Subject: CN = *.*.hashibox.io
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:b2:0c:2e:0c:13:5d:7d:2d:f0:ea:5c:a2:3f:6e:
                    25:dd:ad:ee:90:13:c7:f9:ea:8f:cf:70:0f:a0:0c:
                    68:94:6b:3b:c9:db:73:f4:33:a1:71:63:ec:76:84:
                    da:7a:ab:9f:df:68:55:40:53:41:b2:07:27:ef:00:
                    7a:d5:1b:df:32:b0:48:72:54:e8:e1:cf:46:11:49:
                    67:48:3c:54:a6:fe:a9:52:e6:fb:62:16:be:f0:e1:
                    18:2f:9c:a5:b0:53:b5:d8:ae:76:e6:5c:0c:35:ca:
                    77:d3:33:8d:50:19:2b:9d:9c:7b:2e:2d:be:81:63:
                    90:5d:bc:8e:21:8b:45:8f:35:e1:5d:23:cd:ef:d7:
                    ee:97:5f:30:7d:37:de:a4:79:f3:52:b3:0d:d1:e4:
                    d1:57:2a:18:1b:61:e2:e2:62:95:bb:81:67:77:c9:
                    e8:d5:0b:84:1f:19:1c:27:ef:32:22:e6:a1:3d:a4:
                    d6:12:5b:15:6a:00:c2:13:da:9b:0a:f6:2a:3c:c4:
                    4e:a1:41:0d:c0:03:bc:7e:95:75:6d:b4:95:92:02:
                    ad:4a:4c:89:e1:0a:77:69:36:76:77:f9:9a:92:86:
                    26:4e:2c:22:07:6b:14:13:9b:68:64:4b:38:09:65:
                    28:c0:c9:99:68:61:7d:59:53:e9:76:29:af:8c:cb:
                    26:07
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Key Usage: critical
                Digital Signature, Key Encipherment, Key Agreement
            X509v3 Extended Key Usage: 
                TLS Web Client Authentication, TLS Web Server Authentication
            X509v3 Basic Constraints: critical
                CA:FALSE
            X509v3 Authority Key Identifier: 
                BA:51:38:45:4F:99:E9:CC:F9:56:20:09:8E:25:68:64:43:90:5A:A4
            X509v3 Subject Alternative Name: 
                DNS:*.hashibox.io, DNS:localhost, DNS:*.ec2.internal, IP Address:127.0.0.1
    Signature Algorithm: sha256WithRSAEncryption
    Signature Value:
        66:cb:63:5c:14:aa:65:64:47:7c:27:1e:1c:76:b0:9e:5e:e2:
        88:7c:48:61:57:e4:78:43:fe:05:f5:95:04:8a:84:f7:f8:04:
        1f:54:6f:f6:43:80:1d:76:f4:c8:b2:58:88:40:d9:08:7d:19:
        b7:b1:8a:20:63:3f:a1:c1:7d:c0:5b:4b:14:97:4d:f6:7f:38:
        d5:1e:f9:ec:fe:17:c1:00:b9:f7:10:09:d1:f5:67:cd:b7:19:
        12:58:2a:26:f3:9d:41:e3:77:ae:18:be:5d:2f:29:87:a5:bd:
        22:0d:cc:37:56:21:ad:14:8b:46:4c:c2:d3:94:0e:da:b4:44:
        37:0a:d1:31:a6:a1:eb:f5:8c:61:46:24:fc:83:4d:80:c7:ac:
        e3:c4:8e:88:e3:a6:6d:35:01:09:73:9e:22:30:f3:9e:f9:4c:
        ab:67:7d:91:95:42:29:d1:c4:90:92:e6:0c:dc:63:8f:21:20:
        86:63:ed:fc:1e:39:18:82:21:8d:69:5d:d3:00:77:a4:db:53:
        f3:f4:d1:be:0b:a4:84:8b:b8:f7:c6:b0:9f:50:d4:62:47:79:
        9a:37:72:2f:f7:30:3c:2c:c9:d1:69:24:6a:4d:e0:89:08:89:
        5a:0b:11:9a:2a:a1:0e:9c:9c:18:93:7f:f8:4e:ca:ad:e6:ea:
        86:30:b7:c2
```