---
layout: post
title: Automate your infrastructure using Lambda function
---


AWS Lambda is an event-driven, serverless architecture that allows developers to create and configure a desired function in an AWS (Amazon Web Services) console as well as execute the code without provisionning physical or virtual servers, thus paying only for the resources used during the execution. The code automatically manages computing resources required to run it.

Symbol of AWS Lambda
Usually, Lambda code and dependencies can be wrapped in a Terraform deployment package to fully automate the whole infrastructure in order to build a cloud agnostic IaC. For the purposes of this tutorial, we will manually deploy a Lambda function in an AWS console.
Setting up a Lambda function consists of three steps -
Provide IAM permission
To run a Lambda function, we need to assign an execution role and IAM policy to the function so that it has enough permissions to access and interact with the targeted AWS services and resources.
Following are the steps to build the IAM policy:
In the navigation pane on the left side of the AWS console, choose Policies.
Click Create policy.
Choose the JSON tab.
Type your respective policy for the Lambda function. Please see the example below.
When you are finished, choose Review policy. The Policy Validator reports any syntax errors.
{ “Version”: “2020–04–22”, 
“Statement”: [ 
 { 
  “Effect”: “Allow”, 
  “Action”: [ 
  “logs:CreateLogGroup”, 
  “logs:CreateLogStream”, 
  “logs:PutLogEvents” 
  ], 
  “Resource”: “arn:aws:logs:*:*:*” 
 }, 
 { 
  “Effect”: “Allow”, 
  “Action”: [ 
  “ec2:Start*”, 
  “ec2:Stop*” 
   ], 
  “Resource”: “*” 
 }
Sample IAM policy
Once you have an IAM role, the next step is to create a user who carries these permissions and will be later assigned to the new created Lambda function to perform the desired operations.
This is how you can create a role for an AWS service (console):
In the navigation pane of the IAM console, choose Roles, and then choose Create role.
For Select type of trusted entity, pick AWS service.
In the Policy, insert the newly created policy.
Choose Next: Review.
For Role name, give an appropriate name which defines the purpose of the Lambda function e.g. EC2-reboot. The role name should be unique within a given AWS account and cannot be distinguished by case i.e. EC2 is same as Ec2.
Review the role and then choose Create role.
Create a Lambda function
The most critical step of setting up a Lambda function is to write its code.
In order to build a code for a Lambda function, follow the below steps:
Go to the Lambda Console and choose create function.
Under Permissions, expand Choose or create an execution role.
Under Execution role, choose Use an existing role.
In role, choose the newly created role.

Permissions screen while creating the Lambda function
Next step is to write the function that you want to execute. There are three ways to write a Lambda function:
Use a template of the sample code provided by AWS; or
Write it from scratch; or
Search for it in the repository from the other developers and corporates.
1. Use a template of the sample code provided by AWS
If you are new to AWS Lambda and coding or aren’t interested in writing code yourself, AWS provides several Lambda templates for basic executions related to its services such as EC2 and S3. This can be a good starting point in understanding and usage of this service.
You can pick from the Blueprints while creating function in this way.

2. Author from scratch
If you are already at your advance stage of programming or couldn’t find the function that you want your Lambda to perform, then either you can modify the one already provided in the Blueprints or write the code from scratch.
In order to build a function, choose the run-time language you want to use for this purpose. AWS provides the possibility to write a function using the following languages:
C#/ Powershell
Python
Java
Node.js
Ruby
We will write the function in Python 2.7 as it seems to have the best start-up performance both in terms of RAM and CPU time.
Syntax of the code looks like this:
import boto3
region = ‘eu-central-1’
instances = [<instance>]
def lambda_handler(event, context):
ec2 = boto3.client(‘ec2’, region_name=region)
ec2.reboot_instances(InstanceIds=instances)
print ‘started your instances: ‘ + str(instances)
In region you have to replace eu-central-1 with your region and in instance, you have to fill the ID of the EC2 instance you want to start. This function will start your EC2 instance. Here, boto3 is an AWS Software Development Kit (SDK) for Python.
Similarly, the code to stop the instance would be the same but we need to replace reboot with stop.
import boto3
region = ‘eu-central-1’
instances = [<instance>]
def lambda_handler(event, context):
ec2 = boto3.client(‘ec2’, region_name=region)
ec2.stop_instances(InstanceIds=instances)
print ‘stoped your instances: ‘ + str(instances)
3. Use AWS Serverless Application Repository

source: https://aws.amazon.com/serverless/serverlessrepo/
AWS has a great repository of already built serverless applications which were published by independent programmers, companies and their partners.
It contains many common use cases, and the platform is open to public to enrich the repository. The codes are available free of cost in order to encourage the re-usablity and encourage new users to opt for AWS services.
Once you have built or configured your codes, press save on the bottom to move to the last step.
Invoke the Lambda function
The final step is to build a trigger which will intiate the function.
Trigger can be configured in a response to an event, such as a change in a DynamoDB table, a new file uploaded to S3 or a similar AWS event. You can also configure the Lambda function to respond to requests to AWS API Gateway, or based on a timer triggered by AWS Cloudwatch, using Alexa or using on-demand SDK from Amazon.
For this tutorial, we will use the sample event data provided by AWS:
In the upper right corner of console, choose Test.
In the Configure test event page, choose Create new test event and in Event template, leave the default Hello World option. Enter an Event name and note the following sample event template:
{ "key3": "value3", "key2": "value2", "key1": "value1" }
3. Choose Create and then choose Test. Each user can create up to 10 test events per function. Those test events are not available to other users.
4. AWS Lambda executes your function on your behalf. The handler in your Lambda function receives and then processes the sample event.
5. Run the Lambda function a few times to gather some metrics that you can view in the next step.
6. Upon successful execution, view results in the console. Choose Monitoring. This page shows graphs for the metrics that Lambda sends to CloudWatch.
