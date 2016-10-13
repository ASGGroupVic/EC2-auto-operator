require 'json'
require 'aws-sdk'

def cloudformation
  @cloudformation ||= Aws::CloudFormation::Client.new()
end
def run(stack_name, template_file, settings_file = nil)
  raise 'stack_name missing' unless stack_name
  if settings_file
   file = File.read(settings_file)
   updated_data = file.gsub("ParameterKey", "parameter_key").gsub("ParameterValue", "parameter_value")
   param_hash = JSON.parse(updated_data)
  end
  templatefile = File.read(template_file)
  cmd = `aws cloudformation describe-stacks --query 'Stacks[].StackName' --output text`
  if cmd =~ /#{stack_name}/i
      puts 'Update stack...'
      tocreate = false
  else
      puts 'Creating stack...'
      tocreate = true
  end
  launch_stack(tocreate,stack_name, templatefile, param_hash)
  puts 'Waiting to be complete ...'
  resp = tocreate ? create_stack_waiter(stack_name) : update_stack_waiter(stack_name)
  ov = resp[:stacks][0][:outputs].find { |o| o[:output_key] == 'output' }[:output_value]
  puts "=== Output value == \n" + ov unless ov == nil
  true
end

def launch_stack(tocreate, stack_name, template_file, settings_file = nil)
  if settings_file
      if tocreate
        resp = cloudformation.create_stack(
                stack_name: stack_name,
                template_body: template_file,
                parameters: settings_file,
                capabilities: ["CAPABILITY_IAM"]
        )
      else
        resp = cloudformation.update_stack(
                stack_name: stack_name,
                template_body: template_file,
                parameters: settings_file,
                capabilities: ["CAPABILITY_IAM"]
        )
      end
  else
      if tocreate
        resp = cloudformation.create_stack(
            stack_name: stack_name,
            template_body: template_file,
            capabilities: ["CAPABILITY_IAM"]
        )
      else
        resp = cloudformation.update_stack(
            stack_name: stack_name,
            template_body: template_file,
            capabilities: ["CAPABILITY_IAM"]
        )
      end
    end
end

def create_stack_waiter(stack_name)
  return cloudformation.wait_until :stack_create_complete, stack_name: stack_name
end

def update_stack_waiter(stack_name)
  return cloudformation.wait_until :stack_update_complete, stack_name: stack_name
end

namespace :IDM_allEnv do
  desc "step1:create sns event topics"
  task :create_sns_topics do
    run('sns-idm-event', 'base-cloud-formation/sns-topics.json','idm-dev/parameters/idm-sns-topics-params.json')
    end

  desc "step2:Launch an auto EC2 Operator"
  task :create_auto_ec2_operator do
    run('auto-operator-EC2', 'base-cloud-formation/auto-start-stop-ec2.json','idm-dev/parameters/idm-auto-ec2-params.json')
  end
end
