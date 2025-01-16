
# AWS-Secrets-Manager-with-Lambda-Functions
# Retrieving Secrets from AWS Secrets Manager using Lambda for DynamoDB Access

This project walk-through builds a serverless architecture using AWS Lambda functions and DynamoDB. We're creating Lambda functions to interact with DynamoDB, both hard-coding access keys and using Secrets Manager for secure access. These functions allow us to create tables, insert items, and retrieve data, ensuring safe and efficient data management.




<img width="748" alt="Screenshot 2025-01-10 at 19 23 41" src="https://github.com/user-attachments/assets/9fc47bf2-880f-43ba-b393-f17f23633d97" />



# Activity Guide: Creating Lambda Functions to Interact with DynamoDB and Setting Up a Secure VPC with EC2

1. Create an Amazon VPC (Amazon VPC)
Launch a VPC:
Navigate to the VPC Console and create a new VPC.
Specify an IPv4 CIDR block (e.g., 10.0.0.0/16) for the VPC.

2. Create a Public Subnet (Amazon VPC)
Add a Subnet to the VPC:
Create a subnet within the VPC and assign it a CIDR block (e.g., 10.0.1.0/24).
Ensure the subnet is located in an availability zone where resources will be deployed.
Enable Auto-Assign Public IP:
Modify the subnet to automatically assign public IP addresses to instances launched in it.

3. Create and Attach an Internet Gateway (Amazon VPC)
Create an Internet Gateway:
In the VPC Console, create a new Internet Gateway.
Attach to the VPC:
Attach the Internet Gateway to your VPC to enable internet connectivity.

4. Create a Public Route Table and Associate it with the Subnet (Amazon VPC)
Create a Route Table:
Create a new route table for the VPC and name it appropriately (e.g., Public-Route-Table).
Add Routes:
Add a route to the route table that directs traffic destined for 0.0.0.0/0 to the Internet Gateway.
Associate the Subnet:
Link the public subnet to the newly created route table.

5. Create a Security Group (Amazon EC2)
Define Ingress and Egress Rules:
Allow inbound SSH traffic (port 22) for your IP address to connect to the EC2 instance.
Allow all outbound traffic to enable internet connectivity.

6. Launch an EC2 Instance (Amazon EC2)
Choose an AMI:
Select an Amazon Linux 2 or Ubuntu AMI.
Instance Type:
Choose an instance type such as t2.micro (eligible for the free tier).
Configure Network Settings:
Assign the instance to the public subnet and associate it with the security group created earlier.
Add a Key Pair:
Create or use an existing key pair to securely access the instance.

7. Understand Security Group Rules (Amazon EC2)
Ingress Rules:
Verify that only trusted IP addresses can access the instance over SSH.
Adjust inbound rules to allow other specific traffic (e.g., HTTP/HTTPS) as needed.
Egress Rules:
Ensure all outbound traffic is allowed unless specific restrictions are required.

8. Create Lambda Functions to Interact with DynamoDB (AWS Lambda, Amazon DynamoDB)
Define the Lambda Role:
Create an IAM role with policies granting access to DynamoDB and Secrets Manager.
Attach this role to the Lambda function for secure resource access.
Create DynamoDB Interaction Functions:

# Hard-coded Access Keys (Not Recommended):

Add AWS access keys directly to the Lambda function code for DynamoDB access.
Example:

import boto3

dynamodb = boto3.client('dynamodb', aws_access_key_id='YOUR_ACCESS_KEY', aws_secret_access_key='YOUR_SECRET_KEY')

def lambda_handler(event, context):
    # Your DynamoDB logic here

Using AWS Secrets Manager (Recommended):
# Store credentials in Secrets Manager and fetch them securely in the Lambda function.
Example:
import boto3
import json

def lambda_handler(event, context):
    client = boto3.client('secretsmanager')
    secret = client.get_secret_value(SecretId='your-secret-name')
    credentials = json.loads(secret['SecretString'])

    dynamodb = boto3.client('dynamodb', aws_access_key_id=credentials['aws_access_key_id'], aws_secret_access_key=credentials['aws_secret_access_key'])

    # Your DynamoDB logic here
    
Implement CRUD Operations:
Write functions to create DynamoDB tables, insert items, and retrieve data.

9. Test and Monitor Lambda Functions (AWS Lambda, CloudWatch)
Invoke Functions:
Test the Lambda functions using sample events.
Monitor Logs:
Use CloudWatch Logs to monitor execution and debug issues.

10. Clean Up Resources (Amazon EC2, VPC, DynamoDB, Lambda)
Terminate EC2 Instance:
Stop and terminate the EC2 instance to avoid charges.
Delete the VPC:
Remove the subnets, route tables, Internet Gateway, and the VPC.
Remove DynamoDB Tables:
Delete the created DynamoDB tables.
Delete Lambda Functions:
Remove Lambda functions and associated IAM roles or Secrets Manager secrets.
