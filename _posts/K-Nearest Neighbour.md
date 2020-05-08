Automate your infrastructure using Lambda function


AWS lambda is is an event-driven, serverless architecture that allows developers to create and configure a small function AWS (Amazon Web Services) console and execute the code without the need to provision physical or virtual servers and paying only for the resources used during the execution. The code automatically manages the computing resources required by that code.
Symbol of AWS LambdaUsually, lambda code and dependencies can be wrapped in a Terraform deployment package to fully automate the whole infrastructure to build a cloud agnostic IaC. But for this tutorial, we will manually deploy a lambda function.
Setting up a Lambda function consists of three steps -
Provide IAM permission
 In order to deploy to run the lambda function, we need to provide an execution role and IAM policy to the Lambda function so that it has enough permissions to access and alter the AWS services and resources.
In order to build the IAM policy 
In the navigation pane on the left of the AWS console, choose Policies.
Choose Create policy.
Choose the JSON tab.
Type your respective policy for the Lambda function, for my example it looks something like this:

{ "Version": "2020–04–22", 
"Statement": [ 
 { 
  "Effect": "Allow", 
  "Action": [ 
  "logs:CreateLogGroup", 
  "logs:CreateLogStream", 
  "logs:PutLogEvents" 
  ], 
  "Resource": "arn:aws:logs:*:*:*" 
 }, 
 { 
  "Effect": "Allow", 
  "Action": [ 
  "ec2:Start*", 
  "ec2:Stop*" 
   ], 
  "Resource": "*" 
 }
5. When you are finished, choose Review policy. The Policy Validator reports any syntax errors.
Once you have IAM role, next step to create a user which carries these permissions and will be assigned to the Lambda function to be able to perform the operations.
To create a role for an AWS service (console)
In the navigation pane of the IAM console, choose Roles, and then choose Create role.
For Select type of trusted entity, choose AWS service.
In the policy, insert the newly created policy 
Choose Next: Review.

For Role name, choose the appropriate role name which defines the purpose of the Lambda function e.g. EC2-reboot. The role name should be unique per AWS account and cannot be distinguished by case.
Review the role and then choose Create role.

Create a Lambda function
Most important step of setting up a Lambda is to build the code for the one.
In order to create a Lambda function: 
Go to the Lambda Console and choose create function. 
Under Permissions, expand Choose or create an execution role.
Under Execution role, choose Use an existing role.
In role, choose the newly created role.  

Permissions screen while creating the Lambda functionNext step to write the function that you want to execute. There are three ways to write a Lambda function:
Use a template of the sample code provided by AWS
Write from scratch
Look for repository from the other developers and corporate 

1. Use a template of the sample code provided by AWS
If you are new with AWS lambda and coding or isn't interested in writing a code for yourself. AWS provides several Lambda template for basic executions  related to it's services such as EC2 and S3. This can be good started point in understanding and usage of this service. 
In order to choose the can chosen from the Blueprints while creating function. 
2. Author from Scratch
If you are already at your advance stage of programming or couldn't find the function that you want your Lambda to perform, then either you can modify the already provided blueprints or write the code from the scratch. 
In order to build a function, choose the run-time language that you want to use to create the function. AWS provides the possibility to write the function on the following languages
C#/ Powershell
Python
Java
Node.js
Ruby

We will write the function in Python 2.7 as it seems to have to best start-up performance both in terms of RAM and CPU time.
Syntax of the code looks like this: 
import boto3
region = 'eu-central-1'
instances = [<instance>]
def lambda_handler(event, context):
ec2 = boto3.client('ec2', region_name=region)
ec2.reboot_instances(InstanceIds=instances)
print 'started your instances: ' + str(instances)
In region you have replace eu-central-1 with your region and in instance, you have to fill the ID of the EC2 instance you want to start
This function will start your EC2 instance. Here, boto3 is a AWS's Software Development Kit (SDK) for Python.
Similarly, the code to stop the instance would be same but we just have to replace reboot with stop 
import boto3
region = 'eu-central-1'
instances = [<instance>]
def lambda_handler(event, context):
ec2 = boto3.client('ec2', region_name=region)
ec2.stop_instances(InstanceIds=instances)
print 'stoped your instances: ' + str(instances)
3. Use Serverless Application Repository 
source: https://aws.amazon.com/serverless/serverlessrepo/AWS a great repository of already build serverless applications which were published by independent programmer, companies and their partner. 
It contains many common use case and platfrom is open to public to enrich the repository. The codes are available free of cost in order to encourage the re-usablity and facilitate new users ot use AWS services. 
Once you have build or configured your codes, press save on the bottom to move to last step. 
Invoking the Lambda function
maybe add Alexa too?
