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