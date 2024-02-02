### Boundary and Vault Enterprise Packages

- [Vault](https://releases.hashicorp.com/vault/1.15.5+ent/)
- [Boundary](https://releases.hashicorp.com/boundary/0.14.3+ent/)
    - For Mac ARM64, Download boundary_0.14.3+ent_darwin_arm64.zip
    - Unzip
    - `sudo mv boundary /usr/local/bin/boundary`

# enterprise boundary package for ARM64
wget https://releases.hashicorp.com/boundary/0.14.3+ent/boundary_0.14.3+ent_linux_arm64.zip
unzip boundary_0.14.3+ent_linux_arm64.zip
sudo cp boundary /usr/local/bin
boundary version

# OSS for ARM64
wget https://releases.hashicorp.com/boundary/0.14.3/boundary_0.14.3_linux_arm64.zip
unzip boundary_0.14.3_linux_arm64.zip
sudo cp boundary /usr/local/bin