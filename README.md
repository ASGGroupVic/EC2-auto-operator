# AWS EC2-AutoOperator

This repository contains cloudformation scripts to setup an EC2 Auto Operator in an AWS account.
The Auto Operator is a t2.micro EC2 instance to be fired up at 7am and again 5pm, which will be running for a seven-hour-period.
The reason is that some of our Qlik instances needs to be up till 9pm in HK time, where is 3 hours behind us in Summer.

##Repo Layout

- [base-cloud-formation/](base-cloud-formation/):  templates common across all environments
- [idm-dev/](idm-dev/):  templates & parameters for Dev


###Install Tools

Assumes Ruby 2.1 or higher is installed.
```
git clone git@git.realestate.com.au:resi-lob/residata-aws-infrastructure.git
cd ec2-auto-operator
bundle install
bundle exec rake -T

Output:
List of tasks that create/update cloudformation deployment stacks in AWS

Example:
rake IDM_allEnv:create_sns_topics   # step1:create sns event topics
```
###Start to deploy

Assumes you've configured AWS command line interface via the command

aws configure
```

####Create a SNS with your administrator's email for AWS account

Edit [Admin Email paramerter](idm-dev/parameters/idm-sns-topics-params.json) to set `AdminEmailAddress`.

`rake IDM_allEnv:create_sns_topics`


####Deploy EC2 Auto Operator

Edit [params](idm-dev/parameters/idm-auto-ec2-params.json) to set

`SourceCodeFullPathS3` - S3 bucket https url where stores the ec2-operator.py
`SourceCodeS3Bucket` - The S3 bucket name and path contain the ec2-operator.py
`snsTopic` - ARN number of a SNS from the output above
`KeyName` - Name of an existing keypair - pem file in your AWS


`rake IDM_allEnv:create_auto_ec2_operator`
