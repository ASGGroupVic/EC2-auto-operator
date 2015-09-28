require 'json'

def update_stack(stack_name, template_file, settings_file = nil)
  if settings_file
    sh("rea-aws", "--region", "ap-southeast-2", "cf", "stack", stack_name, "apply", "--settings", settings_file, template_file)
  else
    sh("rea-aws", "--region", "ap-southeast-2", "cf", "stack", stack_name, "apply", template_file)
  end
end

namespace :residata_dev do
  desc "Onceoff task-Create DNS records in route53 in ResiDataDEV account"
  task :Onceoff_create_dev_route53 do
    update_stack('DEV-dns-route53', 'base-cloud-formation/dns-route53-vpc.json', 'residata-dev/parameters/rddev-dns-route53-params.json')
  end

  desc "Create a shipper role in ResiDataDEV account"
  task :create_dev_shipperrole do
    update_stack('DEV-iam-role', 'residata-dev/cloud-formation/iam-shipper-role.json')
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

  desc "Create DEV S3 Bucket for data storage"
  task :create_dev_bucket do
  update_stack('DEV-bucket', 'base-cloud-formation/create-bucket.json', 'residata-dev/parameters/rddev-bucket-params.json')
end
end


namespace :residata_prod do

  desc "Onceoff-Deploy route53 stack to ResiData-Prod account"
  task :onceoff_create_prod_route53 do
    update_stack('prod-route53', 'residata-prod/cloud-formation/additional-route53-zone.json', 'residata-prod/parameters/dns-route53-params.json')
    end

  desc "Create a shipper role to ResiData-Prod account"
  task :create_dev_shipperrole do
    update_stack('prod-iam-role', 'residata-prod/cloud-formation/iam-shipper-role.json')
    end

  desc "Create IAM Managed Policy for federated roles"
  task :create_prod_iam_role_policies do
    update_stack('prod-iam-role-policies', 'base-cloud-formation/iam-role-policy.json', 'residata-prod/parameters/iam-managed-role-policy-params.json')
  end

  desc "Create S3 Bucket in ResiData-Prod account"
  task :create_prod_bucket do
    update_stack('prod-bucket', 'base-cloud-formation/create-bucket.json', 'residata-prod/parameters/create-bucket-params.json')
  end

  desc "Deploy VPC stack to ResiData-Prod account"
  task :deploy_prod_vpc do
    update_stack('prod-vpc', 'base-cloud-formation/resi-data-vpc.json', 'residata-prod/parameters/vpc-params.json')
  end


  desc "Add additional ACL to PublicSubnet in ResiData-Prod account"
  task :add_additional_ACL do
    update_stack('prod-additional-ACL', 'residata-prod/cloud-formation/additional-public-subnet-acl-rules.json', 'residata-prod/parameters/additional-public-subnet-acl-rules-params.json')
  end


end
