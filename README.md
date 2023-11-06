## hashibox-arm
#### Up and Running vagrant box
```
git clone git@github.com:sailinnthu/hashibox-arm.git
cd hashibox-arm
mkdir .ssh
cd .ssh
ssh-keygen
/Users/sai.linnthu/hashisai/hashi-cop/hashibox-arm/.ssh/id_rsa
cd ../
vagrant up
```

#### Spin up k8s cluster
```
vagrant ssh
cd k8s-cop/1-single-cluster/setup/
./setup-kindcluster123.sh
```

#### aws config
```
cat ~/.aws/config
```

```
# gritworks-nonprod
[profile gritworks-nonprod]
region = ap-southeast-1

# gritworks-nonprod-terraform-role
[profile gritworks-dev-terraform-role]
region = ap-southeast-1
source_profile = gritworks-nonprod
role_arn = arn:aws:iam::xxxxxxxxxxxx:role/gritworks-nonprod-terraform-role

# gritworks-dev-terraform-role
[profile gritworks-dev-terraform-role]
region = ap-southeast-1
source_profile = gritworks-nonprod
role_arn = arn:aws:iam::xxxxxxxxxxxx:role/gritworks-dev-terraform-role

# gritworks-security-terraform-role
[profile gritworks-security-terraform-role]
region = ap-southeast-1
source_profile = gritworks-nonprod
role_arn = arn:aws:iam::xxxxxxxxxxxx:role/gritworks-security-terraform-role

```
#### aws credentials
```
cat ~/.aws/credentials
```
```
# gritworks-nonprod
[gritworks-nonprod]
aws_access_key_id =
aws_secret_access_key =
```
