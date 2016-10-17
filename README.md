# AWS EC2-AutoOperator

This repository contains auto deployment script in Ruby to rollout two cloudformation stacks into your AWS account. The CF scripts will setup an AWS application to start and stop your EC2 instances automatically in all regions to control their usage. The app is a highly-available, fault-tolerant, cost-efficient scalable solution for any business who wants to optimize their EC2 costs and still keep the performance & capacity requires.

##Repo Layout

- [base-cloud-formation/](base-cloud-formation/):  templates common across all environments
- [idm-dev/](idm-dev/):  templates & parameters for Dev


###Install Tools

Assumes Ruby 2.1 or higher is installed.
```
git clone https://github.com/SMSManagementAndTechnology/EC2-auto-operator.git
cd ec2-auto-operator
bundle install
bundle exec rake -T

Output:
List of tasks that create/update cloudformation deployment stacks in AWS

Example:
rake IDM_allEnv:create_sns_topics   # step1:create sns event topics
```
###Deployment

Assumes you've configured AWS command line interface via the command

####aws configure

####+Upload the file ec2-operator.py to your S3 bucket
####+Create a SNS with your administrator's email for AWS account

Edit [Admin Email paramerter](idm-dev/parameters/idm-sns-topics-params.json) to set `AdminEmailAddress`.

`rake IDM_allEnv:create_sns_topics`

####+Deploy EC2 auto operator

Edit [params](idm-dev/parameters/idm-auto-ec2-params.json) to set
```
SourceCodeFullPathS3 - S3 bucket https url where stores the ec2-operator.py
SourceCodeS3Bucket - The S3 bucket name and path contain the ec2-operator.py
snsTopic - ARN number of a SNS from the output above
KeyName - Name of an existing keypair - pem file in your AWS
```

`rake IDM_allEnv:create_auto_ec2_operator`
NOTE:This process may take a while to run, before returning an newly created ASG name. Would you like to view the process details or troubleshoot, feel free to login AWS console-> CloudFormation -> Tick the stack 'auto-operator-EC2' -> Click Events tab.

###How to use it
Set up a schedule with Cron format via the tag feature in your EC2 instances. 
For Example:
```
KEY         VALUE
auto:start  0 9 * * 1-5
```
