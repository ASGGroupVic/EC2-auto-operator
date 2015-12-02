# Resi Data AWS Infrastructure Setup

This repository contains environment setup for AWS accounts owned by Resi Data & Analytics team. SSH Keys and passwords have been stored in [RatticDB](https://rattic.eqx.realestate.com.au/cred/list-by-group/195/)

##AWS Accounts

Dev: `residata-dev` (177242442824)
Prod: `residata-prod` (563560393941)  

##Repo Layout

- [base-cloud-formation/](base-cloud-formation/):  templates common across all environments
- [residata-dev/](residata-dev/):  templates & parameters for Dev
- [residata-prod/](residata-prod/):  templates & parameters for Prod

##{Env} Setup

Instructions below for {Env} = production, substitute prod for dev in Dev.

Firstly, claim one of pre-configured AWS accounts and fill in the account details [here] (https://community.rea-group.com/docs/DOC-36453). The Admin login will be provided by GIA, once the account is ready for you to access via: https://idp.realestate.com.au/

###Install Tools

Assumes Ruby 2.1 or higher is installed.
```
git clone git@git.realestate.com.au:resi-lob/residata-aws-infrastructure.git
cd residata-aws-infrastructure
bundle install
bundle exec rake -T

Output:
List of tasks that create/update cloudformation deployment stacks in AWS

Example:
rake residata_dev:create_dev_route53   # Create DNS zone and recordsets in route53 in your ResiDataDEV VPC
```

###Create & Register Hosted Zone Name (Once-off)

`rake residata_prod:onceoff_create_prod_route53`

Tell GIA the name, they will register it with UltraDNS

- Dev is done: `resi-data-dev.realestate.com.au`
- Prod: `resi-data-prod.realestate.com.au`  

This CloudFormation stack can't be updated. You have to delete it before rerun.

###CIDR block

Request GIA to provide you a CIDR block for the new VPC. 

- Dev is done: `10.49.36.0/22` 
- Prod: 10.49.60.0/22

###SSH Keypair

Created a [SSH keypair - residata.ap-southeast-2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair). Then upload the key file to rattic.

Dev: https://rattic.eqx.realestate.com.au/cred/detail/4254/
Prod: https://rattic.eqx.realestate.com.au/cred/list-by-group/228/

###VPC
 
Set subnet [CIDR params](residata-prod/parameters/vpc-params.json) based on [dev pattern](residata-dev/parameters/rddev-vpc-params.json) but using the prod CIDR block. The main rule is to ensure the IP ranges don't overlap.

`rake residata_prod:deploy_prod_vpc`

###Security Group

Edit [params](residata-prod/parameters/security-group-params.json) to set `VpcId` from output of above.

`rake residata_prod:create_prod_SG`

###S3 Buckets

Ensure [params](residata-prod/parameters/create-bucket-params.json) are OK, and bucket name must be lowercase. 

`rake residata_prod:create_prod_bucket`

###Shipper Role

[amiRegisterBucketName](residata-prod/parameters/shipper-role-params.json) should point at bucket created in last step

`rake residata_prod:create_prod_shipperrole`

### Assume Role for the application residata-extractor

To be able to access s3 bucket across different AWS accounts, you need to provide your new AWS account ID to ConsumerData team (Danial Pearce), then he will come back with the CD_AssumeRole ARN number that you can use to update the [parameter](/residata-prod/parameters/residata-extractor-params.json) .

The Money team created a [data services](https://git.realestate.com.au/the-money/residata-extractor/blob/master/cloudformation/residata-cdm-bucket.json) for us. Please make sure they are using the correct extractor role from us.
The role ARN number created in this task should be given to them for the across account access.


`rake residata_prod:prod_residata_extractor_role `

###SNS Topics

`rake residata_allEnv:create_sns_topics`

###Splunk Forwarder

Before setup the Forwarder, create a index called resi-data via the pull request from [GIA repository](https://git.realestate.com.au/infrastructure/splunk-deployment/blob/master/ansible/roles/indexer-master/files/idxcluster/resi/local/indexes.conf)
Then run the rake task to setup a splunk forwarder server pointing to Skynet splunk

`rake residata_prod:create_prod_SplunkFw`

    +------+-----+------------------------+             +-------------------------------------+
    |  RESI-DATA                          |             | SKYNET                              |
    |                                     |             |                                     |
    |  +-------+                          |             |                                     |
    |  |  WEB  +-----+      +-------------+             +-------------+      +-------------+  |
    |  +-------+     |      |splunkfw-rd  |             |             |      |             |  |
    |                |      |             |   SSL       |             |      |             |  |
    |                | 9997 |   SPLUNK    |   443       |  SPLUNK     | 9997 | splunk.     |  |
    |  +-------+     |      |             |             |             |      | skynet.     |  |
    |  |  API  +------------>  FORWARDER  +------------->  IndexersLB +------> realestate. |  |
    |  +-------+     |      |             |             |             |      | com.au      |  |
    |                |      |             |             |             |      |             |  |
    |  +-------+     |      |             |             |             |      |             |  |
    |  | INDEX +-----+      +-------------+             +-------------+      +-------------+  |
    |  +-------+                          |             |                                     |
    |                                     |             |                                     |
    +-------------------------------------+             +-------------------------------------+

###Setup Role Permissions for Admin and Normaluser
Go to IAM console in AWS, manually Create a NormalUser role similar to the Admin login.Then grant permssions via the following command:
`rake residata_prod:create_prod_iam_role_policies`



##ToDo List

- Refactor the code to pull all of parameter files out of this repo and place them into a private repo.
- Document some verification steps to test whether the environment is working correctly.




