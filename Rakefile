require 'json'

def update_stack(stack_name, template_file, settings_file = nil)
  if settings_file
    sh("rea-aws", "--region", "ap-southeast-2", "cf", "stack", stack_name, "apply", "--settings", settings_file, template_file)
  else
    sh("rea-aws", "--region", "ap-southeast-2", "cf", "stack", stack_name, "apply", template_file)
  end
end

namespace :residata_allEnv do
  desc "create sns event topics"
  task :create_sns_topics do
    update_stack('sns-topics', 'base-cloud-formation/sns-topics.json')
  end

  desc "ASG Notification Access Role"
  task :create_asg_role do
    update_stack('asg-notify-role', 'base-cloud-formation/autoscallinggroup-notification-role.json')
  end

end

namespace :residata_dev do
  desc "Create assume role for Residata-Extractor in ResiDataDEV"
  task :dev_residata_extractor_role do
    update_stack('DEV-residata-extractor-role', 'base-cloud-formation/residata-extractor-role.json','residata-dev/parameters/rddev-residata-extractor-params.json')
  end

  desc "Create a deployment role in ResiDataDEV account"
  task :create_dev_shipperrole do
    update_stack('DEV-deployment-role', 'base-cloud-formation/iam-shipper-role.json','residata-dev/parameters/rddev-shipper-role-params.json')
  end

  desc "Create IAM Policy for federated role in DEV"
  task :create_dev_iam_federated_role_policies do
    update_stack('DEV-iam-federated-role-policies', 'base-cloud-formation/iam-role-policy.json', 'residata-dev/parameters/rddev-iam-role-policy-params.json')
  end

  desc "Deploy VPC stack to ResiDataDEV account"
  task :deploy_dev_vpc do
    update_stack('VPC-stack-1440726816', 'base-cloud-formation/resi-data-vpc.json', 'residata-dev/parameters/rddev-vpc-initial-deploy-params.json')
  end

  desc "Update VPC stack to ResiDataDEV account"
  task :update_dev_vpc do
    update_stack('VPC-stack-1440726816', 'base-cloud-formation/resi-data-vpc.json', 'residata-dev/parameters/rddev-vpc-params.json')
  end

  desc "Onceoff task-Create DNS records in route53 in ResiDataDEV account"
  task :Onceoff_create_dev_route53 do
    update_stack('DEV-dns-route53', 'base-cloud-formation/dns-route53-vpc.json', 'residata-dev/parameters/rddev-dns-route53-params.json')
  end

  desc "Create DEV S3 Bucket for data storage"
  task :create_dev_bucket do
  update_stack('DEV-bucket', 'base-cloud-formation/create-bucket.json', 'residata-dev/parameters/rddev-bucket-params.json')
  end

  desc "Create SSH,HTTP,HTTPS only Seucrity Groups"
  task :create_dev_SG do
    update_stack('DEV-SecurityGroup', 'base-cloud-formation/security-group.json', 'residata-dev/parameters/rddev-securitygroup-params.json')
  end

  desc "Add additional ACL to PublicSubnet in DEV"
  task :additional_dev_ssh_ACL do
    update_stack('DEV-additional-ACL', 'residata-dev/cloud-formation/additional-public-subnet-acl-rules.json', 'residata-dev/parameters/rddev-addpublicsubnet-acl-rules-params.json')
  end

  desc "Create a DEV Splunkforwarder Server"
  task :create_dev_SplunkFw do
    update_stack('DEV-SplunkFw', 'base-cloud-formation/splunkforwarder.json', 'residata-dev/parameters/rddev-splunkforwarder-params.json')
  end

  desc "Allow Dev extractor access IREData S3 bucket"
  task :dev_IREs3bucketAccess do
    update_stack('s3AccessPolicy', 'base-cloud-formation/cross-acct-bucketpolicy.json', 'residata-dev/parameters/rddev-cross-acct-bucket-params.json')
  end
end


namespace :residata_prod do


  desc "Create assume role for Residata-Extractor in PROD"
  task :prod_residata_extractor_role do
    update_stack('residata-extractor-role', 'base-cloud-formation/residata-extractor-role.json','residata-prod/parameters/residata-extractor-params.json')
  end

  desc "Create a deployment role to Prod"
  task :create_prod_shipperrole do
    update_stack('deployment-role', 'base-cloud-formation/iam-shipper-role.json','residata-prod/parameters/shipper-role-params.json')
    end

  desc "Create IAM Managed Policy for federated roles"
  task :create_prod_iam_role_policies do
    update_stack('federated-role-policies', 'base-cloud-formation/iam-role-policy.json', 'residata-prod/parameters/iam-managed-role-policy-params.json')
  end

  desc "Create S3 Bucket in Prod account"
  task :create_prod_bucket do
    update_stack('s3bucket', 'base-cloud-formation/create-bucket.json', 'residata-prod/parameters/create-bucket-params.json')
  end

  desc "Deploy VPC stack to ResiData-Prod account"
  task :deploy_prod_vpc do
    update_stack('vpc', 'base-cloud-formation/resi-data-vpc.json', 'residata-prod/parameters/vpc-params.json')
  end


  desc "Onceoff-Deploy route53 stack to Prod"
  task :onceoff_create_prod_route53 do
    update_stack('route53', 'residata-prod/cloud-formation/additional-route53-zone.json', 'residata-prod/parameters/dns-route53-params.json')
  end

  desc "Add additional ACL to PublicSubnet in Prod account"
  task :add_additional_ACL do
    update_stack('additional-ACL', 'residata-prod/cloud-formation/additional-public-subnet-acl-rules.json', 'residata-prod/parameters/additional-public-subnet-acl-rules-params.json')
  end

  desc "Create SSH,HTTP,HTTPS only Seucrity Groups"
  task :create_prod_SG do
    update_stack('SSHSecurityGroup', 'base-cloud-formation/security-group.json', 'residata-prod/parameters/security-group-params.json')
  end

  desc "Create a Splunkforwarder Server to Prod"
  task :create_prod_SplunkFw do
    update_stack('SplunkFw', 'base-cloud-formation/splunkforwarder.json', 'residata-prod/parameters/splunkforwarder-params.json')
  end

  desc "Allow extractor sync IREData S3 bucket"
  task :IREs3bucketAccess do
    update_stack('s3AccessPolicy', 'base-cloud-formation/cross-acct-bucketpolicy.json', 'residata-prod/parameters/cross-acct-bucket-params.json')
  end
end
