# Resi Data AWS Infrastructure Setup

This repository contains environment setup for AWS accounts owned by Resi Data & Analytics team. SSH Keys and passwords have been stored in [RatticDB](https://rattic.eqx.realestate.com.au/cred/list-by-group/195/)

##AWS Accounts

Dev: `residata-dev` (177242442824)

##{Env} Setup

Instructions below for {Env} = production, substitute prod for dev in Dev.

Assumes you have access with TMI Admin rights, via https://idp.realestate.com.au/

###Install Tools

Assumes Ruby 2.1 or higher is installed.
```
bundle install
bundle exec rake -T

Output:
List of tasks that create/update stacks in AWS

Example:
rake residata_dev:create_dev_route53   # Create DNS zone and recordsets in route53 in your ResiDataDEV VPC
```

###Create & Register Hosted Zone Name (Once-off)

`rake residata_prod:onceoff_create_prod_route53`

Tell GIA the name, they will register with UltraDNS

- Dev is done: `resi-data-dev.realestate.com.au` 

###CIDR block

Request a CIDR block for your VPC

- Dev is done: `10.49.36.0/22` 

###SSH Keypair

Created a SSH keypair from EC2 Console in your AWS account

Dev: https://rattic.eqx.realestate.com.au/cred/detail/4254/

###VPC
 
Set subnet [CIDR params](residata-prod/parameters/vpc-params.json) based on [dev pattern](residata-dev/parameters/rddev-vpc-params.json) but using the prod CIDR block

`rake residata_prod:deploy_prod_vpc`

###Security Group

Edit [params](residata-prod/parameters/security-group-params.json) to set `VpcId` from output of above.

`rake residata_prod:create_prod_SG`

###S3 Buckets

Ensure [bucket names](residata-prod/parameters/create-bucket-params.json) are OK 

`rake residata_prod:create_prod_bucket`

###Shipper Role

[amiRegisterBucketName](residata-prod/parameters/shipper-role-params.json) should point at bucket created in last step

`rake residata_prod:create_prod_shipperrole`


##ToDo List

- Create Developer IAM role that can be associated with LDAP. Currently there is only Admin. We have a [starting point](base-cloud-formation/iam-role-policy.json) but need to understand permissions better.




