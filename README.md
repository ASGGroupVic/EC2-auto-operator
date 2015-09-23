# Resi Data AWS Infrastructure Setup

This repository contains environment setup for AWS accounts owned by Resi Data & Analytics team. SSH Keys and passwords have been stored in RatticDB


Prerequisites:
- A hosted zone name has been registered by GIA for your new VPC from your new account
- An account is ready in AWS
- Been assigned a CIDR block for your VPC
- Created a SSH keypair from EC2 in your AWS account
- You've login as Admin in your AWS session


## Usage
```
bundle install
bundle exec rake -T

Output:

List of tasks that create/update stacks in AWS


Example:

rake residata_dev:create_dev_route53   # Create DNS zone and recordsets in route53 in your ResiDataDEV VPC

```

