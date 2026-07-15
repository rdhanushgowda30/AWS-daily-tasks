# Task 11: Provision AWS Infrastructure Using AWS CloudFormation

## Project Overview

This project demonstrates how to automate AWS infrastructure deployment using **AWS CloudFormation** by creating a YAML template that provisions an entire networking environment along with an EC2 web server. The infrastructure includes a custom VPC, Public Subnet, Internet Gateway, Route Table, Security Group, and an EC2 instance.

Instead of manually creating AWS resources through the AWS Management Console, all infrastructure is defined as **Infrastructure as Code (IaC)** using a CloudFormation template. The template is validated, deployed as a CloudFormation Stack, updated, monitored, and finally deleted, ensuring repeatable and consistent deployments.

---

# Objective

- Design AWS infrastructure before deployment.
- Create a reusable CloudFormation YAML template.
- Provision a VPC, Public Subnet, Internet Gateway, Route Table, Security Group, and EC2 Instance.
- Validate the CloudFormation template.
- Deploy the infrastructure using CloudFormation Stack.
- Verify all created AWS resources.
- Update the CloudFormation Stack.
- Monitor stack creation and update events.
- Delete the stack and automatically remove all AWS resources.

---

# AWS Services Used

- AWS CloudFormation
- Amazon EC2
- Amazon VPC
- Internet Gateway
- Route Tables
- Security Groups
- AWS CloudShell / AWS CLI

---

# Architecture

```text
                CloudFormation Stack
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
      VPC         Internet Gateway   Route Table
        │               │                │
        └───────────────┼────────────────┘
                        │
                 Public Subnet
                        │
                 Security Group
                        │
                        ▼
                  EC2 Web Server
```

---

# Prerequisites

- AWS Account
- Existing EC2 Key Pair
- IAM permissions for CloudFormation
- AWS CLI or AWS CloudShell (Optional for validation)

---

# Step 1: Design the Infrastructure

Before writing the template, identify all the AWS resources required.

## Resources

| Resource | Purpose |
|----------|----------|
| VPC | Create an isolated network |
| Public Subnet | Launch the EC2 instance |
| Internet Gateway | Provide internet access |
| Route Table | Route internet traffic |
| Security Group | Allow SSH and HTTP access |
| EC2 Instance | Host the web server |

---

## CIDR Planning

| Resource | CIDR Block |
|----------|------------|
| VPC | 10.0.0.0/16 |
| Public Subnet | 10.0.1.0/24 |

---

## Parameters

The following values will be configurable during deployment.

- EC2 Key Pair Name
- EC2 Instance Type

---

# Step 2: Create the CloudFormation Template

Open **VS Code**, **Notepad**, or any text editor.

Create a new file named:

```text
cloudformation.yaml
```

Paste the following CloudFormation template.

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: Deploy a VPC with a public subnet and EC2 instance

Parameters:

  KeyPairName:
    Description: EC2 Key Pair
    Type: AWS::EC2::KeyPair::KeyName

  InstanceType:
    Description: EC2 Instance Type
    Type: String
    Default: t2.micro

Resources:

  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: MyVPC

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: 10.0.1.0/24
      MapPublicIpOnLaunch: true
      AvailabilityZone: us-east-1a
      Tags:
        - Key: Name
          Value: PublicSubnet

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: MyIGW

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref InternetGateway

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      RouteTableId: !Ref PublicRouteTable
      SubnetId: !Ref PublicSubnet

  WebSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH and HTTP
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: 0.0.0.0/0

        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: !Ref InstanceType
      KeyName: !Ref KeyPairName
      ImageId: ami-0c02fb55956c7d316

      NetworkInterfaces:
        - AssociatePublicIpAddress: true
          DeviceIndex: 0
          GroupSet:
            - !Ref WebSecurityGroup
          SubnetId: !Ref PublicSubnet

      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install httpd -y
          systemctl start httpd
          systemctl enable httpd
          echo "<h1>CloudFormation Web Server</h1>" > /var/www/html/index.html

Outputs:

  InstanceID:
    Value: !Ref WebServer

  PublicIP:
    Value: !GetAtt WebServer.PublicIp

  VPCID:
    Value: !Ref MyVPC
```

Save the file.

---

# Understanding the Template

## AWSTemplateFormatVersion

Specifies the CloudFormation template version.

---

## Description

Provides a description of the infrastructure that will be created.

---

## Parameters

Allows users to provide values during stack creation.

Examples:

- EC2 Key Pair
- EC2 Instance Type

---

## Resources

Defines all AWS resources to be created.

Resources include:

- VPC
- Public Subnet
- Internet Gateway
- Route Table
- Route
- Security Group
- EC2 Instance

---

## Outputs

Displays useful information after deployment.

Examples:

- Instance ID
- Public IP Address
- VPC ID

---

# Step 3: Validate the Template

Open **AWS CloudShell** or configure the **AWS CLI**.

Run the following command:

```bash
aws cloudformation validate-template \
--template-body file://cloudformation.yaml
```

### Expected Output

```text
Template validated successfully.
```

### Troubleshooting

If validation fails:

- Verify YAML indentation.
- Ensure all `Properties:` sections are correctly indented.
- Confirm the AMI ID is valid for your AWS Region.

---

# Step 4: Create the CloudFormation Stack

Navigate to:

```text
CloudFormation
    → Stacks
```

Click:

```text
Create Stack
```

Choose:

```text
With new resources (standard)
```

Upload:

```text
cloudformation.yaml
```

Click **Next**.

Configure the stack.

| Setting | Value |
|----------|-------|
| Stack Name | CF-WebServer |
| KeyPairName | Existing EC2 Key Pair |
| InstanceType | t2.micro |

Click **Next** twice.

If prompted, acknowledge IAM capabilities.

Click **Submit**.

---

# Step 5: Deploy the Stack

CloudFormation starts provisioning the infrastructure.

Monitor the stack status.

```text
CREATE_IN_PROGRESS
```

↓

```text
CREATE_COMPLETE
```

### Expected Result

CloudFormation creates:

- VPC
- Public Subnet
- Internet Gateway
- Route Table
- Route
- Security Group
- EC2 Instance

---

# Step 6: Verify the Resources

Navigate to:

```text
EC2
    → Instances
```

Verify the EC2 instance is running.

Copy the Public IPv4 address.

Open a web browser and visit:

```text
http://<Public-IP>
```

You should see:

```text
CloudFormation Web Server
```

Also verify:

- VPC exists.
- Public Subnet belongs to the VPC.
- Internet Gateway is attached.
- Route Table contains `0.0.0.0/0` pointing to the Internet Gateway.
- Security Group allows inbound SSH (22) and HTTP (80).

---

# Step 7: Update the Stack

Modify the template.

Change:

```yaml
Default: t2.micro
```

to

```yaml
Default: t3.micro
```

Save the template.

Navigate to:

```text
CloudFormation
    → Stacks
```

Select:

```text
CF-WebServer
```

Click:

```text
Update
```

Choose:

```text
Replace current template
```

Upload the updated template.

Review the changes.

Click **Submit**.

Monitor the stack until the status changes to:

```text
UPDATE_COMPLETE
```

Verify that the EC2 instance type has been updated (if supported).

---

# Step 8: Review Stack Events

Navigate to:

```text
CloudFormation
    → Stacks
        → Events
```

Monitor events such as:

```text
CREATE_IN_PROGRESS
```

```text
CREATE_COMPLETE
```

```text
UPDATE_IN_PROGRESS
```

```text
UPDATE_COMPLETE
```

If any event fails:

- Review the error message.
- Correct the template.
- Update or recreate the stack.

---

# Step 9: Delete the Stack

Navigate to:

```text
CloudFormation
    → Stacks
```

Select:

```text
CF-WebServer
```

Click:

```text
Delete
```

Confirm the deletion.

Monitor the status.

```text
DELETE_IN_PROGRESS
```

↓

```text
DELETE_COMPLETE
```

### Expected Result

CloudFormation automatically removes:

- EC2 Instance
- Security Group
- Route Table
- Internet Gateway
- Public Subnet
- VPC

No unnecessary AWS resources remain.

---

# AWS Console Navigation

## Create Stack

```text
CloudFormation
    → Stacks
        → Create Stack
```

---

## Validate Template (CLI)

```bash
aws cloudformation validate-template \
--template-body file://cloudformation.yaml
```

---

## Update Stack

```text
CloudFormation
    → Stacks
        → Update
```

---

## View Stack Events

```text
CloudFormation
    → Stacks
        → Events
```

---

## Delete Stack

```text
CloudFormation
    → Stacks
        → Delete
```

---

# Commands Used

## Validate Template

```bash
aws cloudformation validate-template \
--template-body file://cloudformation.yaml
```

---

## Connect to EC2

```bash
ssh -i mykey.pem ec2-user@<Public-IP>
```

---

# CloudFormation Stack Status

```text
CREATE_IN_PROGRESS
```

```text
CREATE_COMPLETE
```

```text
UPDATE_IN_PROGRESS
```

```text
UPDATE_COMPLETE
```

```text
DELETE_IN_PROGRESS
```

```text
DELETE_COMPLETE
```

---

# Verification Checklist

- Infrastructure planned successfully.
- CloudFormation YAML template created.
- Template validated without errors.
- CloudFormation Stack deployed.
- VPC created.
- Public Subnet created.
- Internet Gateway attached.
- Route Table configured.
- Security Group created.
- EC2 instance launched.
- Web server accessible using the Public IP.
- Stack updated successfully.
- Stack events monitored.
- Stack deleted successfully.
- All AWS resources removed automatically.

---

# Skills Gained

- AWS CloudFormation
- Infrastructure as Code (IaC)
- YAML Templates
- Amazon VPC
- Public Subnets
- Internet Gateway
- Route Tables
- Security Groups
- Amazon EC2
- Stack Creation
- Stack Updates
- Stack Events
- Stack Deletion
- Infrastructure Automation

---

# Expected Outcome

By completing this project, you will be able to:

- Automate AWS infrastructure deployment using Infrastructure as Code.
- Create complete networking and compute resources from a single YAML template.
- Deploy identical environments consistently and repeatedly.
- Update infrastructure without manual configuration.
- Monitor stack operations and troubleshoot deployment issues.
- Cleanly remove all infrastructure by deleting the CloudFormation stack.
- Build a strong foundation in AWS-native Infrastructure as Code before learning advanced tools such as Terraform.

---

# Conclusion

Successfully automated the deployment of AWS infrastructure using AWS CloudFormation by creating a reusable YAML template that provisions a custom VPC, Public Subnet, Internet Gateway, Route Table, Security Group, and an EC2 web server. Validated the template, deployed the infrastructure as a CloudFormation Stack, verified the deployed resources, updated the stack with infrastructure changes, monitored stack events throughout the deployment lifecycle, and safely removed all resources by deleting the stack. This project demonstrates the core principles of **Infrastructure as Code (IaC)** and highlights how CloudFormation enables consistent, repeatable, and fully automated infrastructure provisioning in modern AWS and DevOps environments.
