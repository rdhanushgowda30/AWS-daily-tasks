# Task 11: Provision AWS Infrastructure Using AWS CloudFormation

## Project Overview

This project demonstrates how to automate AWS infrastructure deployment using **AWS CloudFormation**, an Infrastructure as Code (IaC) service. Instead of manually creating AWS resources through the Management Console, a CloudFormation template written in **YAML** is used to provision, update, and delete infrastructure consistently and repeatedly.

The project includes designing infrastructure, creating a CloudFormation template, validating it, deploying a stack, updating resources, monitoring stack events, and deleting the stack.

---

# Objective

- Design AWS infrastructure using Infrastructure as Code (IaC).
- Create a CloudFormation template in YAML.
- Validate the template before deployment.
- Deploy AWS resources using CloudFormation.
- Verify the deployed resources.
- Update the infrastructure using stack updates.
- Monitor stack events.
- Delete the stack and automatically remove associated resources.

---

# AWS Services Used

- AWS CloudFormation
- Amazon EC2
- Amazon VPC (Default)
- Amazon Security Groups
- Amazon EC2 Key Pair
- AWS Management Console

---

# Architecture

```
                CloudFormation Template
                        (YAML)
                           │
                           ▼
                 AWS CloudFormation Stack
                           │
        ┌──────────────────┴──────────────────┐
        │                                     │
        ▼                                     ▼
 Security Group                     EC2 Instance
        │                                     │
        └──────────────────┬──────────────────┘
                           │
                           ▼
                     Stack Outputs
                  (Public IP / Instance ID)
```

---

# Prerequisites

- AWS Account
- Existing EC2 Key Pair
- Default VPC (or Custom VPC)
- IAM permissions to create CloudFormation stacks

---

# Step 1: Design the Infrastructure

Before writing the template, identify the AWS resources to be deployed.

### Resources

- Amazon EC2 Instance
- Security Group

### Relationships

- EC2 instance uses the Security Group.
- EC2 instance launches inside the default VPC.

### Configurable Parameters

- EC2 Key Pair
- Instance Type
- AMI ID

Planning the infrastructure before writing the template makes it easier to manage and update later.

---

# Step 2: Create the CloudFormation Template

Create a new file named

```
ec2-template.yaml
```

Paste the following YAML template.

```yaml
AWSTemplateFormatVersion: '2010-09-09'

Description: Launch an EC2 Instance using CloudFormation

Parameters:

  KeyName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Existing EC2 Key Pair

Resources:

  WebSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: Allow SSH and HTTP
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
      ImageId: ami-0b6d9d3d33ba97d99
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      SecurityGroups:
        - !Ref WebSecurityGroup

Outputs:

  InstanceId:
    Value: !Ref WebServer

  PublicIP:
    Value: !GetAtt WebServer.PublicIp
```

Save the file.

### Template Components

- **Parameters** – User inputs during stack creation.
- **Resources** – AWS resources to create.
- **Outputs** – Important values displayed after deployment.

---

# Step 3: Validate the Template

Navigate to

```
AWS CloudFormation
```

Click

```
Create Stack
```

Upload the template.

CloudFormation automatically validates the YAML syntax.

If validation errors occur:

- Verify indentation.
- Check resource names.
- Ensure the YAML format is correct.

### Expected Result

Template validation completes successfully.

---

# Step 4: Create a CloudFormation Stack

Navigate to

```
CloudFormation
    → Stacks
```

Click

```
Create Stack
```

Choose

```
With New Resources (Standard)
```

Upload

```
ec2-template.yaml
```

Click

```
Next
```

Configure

| Setting | Value |
|----------|-------|
| Stack Name | EC2-WebServer-Stack |
| Key Pair | Existing EC2 Key Pair |

Click

```
Next
```

Leave the remaining options as default.

Click

```
Next
```

Review the configuration.

Check

```
I acknowledge that AWS CloudFormation might create IAM resources.
```

(if prompted)

Click

```
Submit
```

---

# Step 5: Deploy the Stack

CloudFormation begins provisioning resources.

Navigate to

```
CloudFormation
    → Stacks
```

Monitor the stack status.

Status progression:

```
CREATE_IN_PROGRESS
```

↓

```
CREATE_COMPLETE
```

### Expected Result

The EC2 instance and Security Group are created successfully.

---

# Step 6: Verify the Resources

Navigate to

```
EC2
    → Instances
```

Verify that the EC2 instance is running.

Navigate to

```
EC2
    → Security Groups
```

Verify that the Security Group has been created.

Copy the Public IP address.

Connect using SSH.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Verify connectivity.

---

# Step 7: Update the Stack

Modify the CloudFormation template.

Example:

Change the EC2 instance type.

From

```yaml
InstanceType: t2.micro
```

To

```yaml
InstanceType: t3.micro
```

Save the template.

Navigate to

```
CloudFormation
    → Stacks
```

Select

```
EC2-WebServer-Stack
```

Click

```
Update
```

Upload the updated template.

Click

```
Next
```

Review the changes.

Click

```
Submit
```

### Expected Result

CloudFormation updates only the modified resources instead of recreating the entire infrastructure.

---

# Step 8: Review Stack Events

Navigate to

```
CloudFormation
    → Stacks
        → Events
```

Monitor each operation.

Example event statuses:

```
CREATE_IN_PROGRESS
```

```
CREATE_COMPLETE
```

```
UPDATE_IN_PROGRESS
```

```
UPDATE_COMPLETE
```

If a deployment fails, the **Events** tab provides detailed error messages to help identify and troubleshoot the issue.

---

# Step 9: Delete the Stack

Navigate to

```
CloudFormation
    → Stacks
```

Select

```
EC2-WebServer-Stack
```

Click

```
Delete
```

Confirm the deletion.

CloudFormation automatically removes all resources created by the stack.

Monitor the status.

```
DELETE_IN_PROGRESS
```

↓

```
DELETE_COMPLETE
```

### Expected Result

- EC2 Instance deleted.
- Security Group deleted.
- No unnecessary AWS resources remain.

---

# AWS Console Navigation

## Create Stack

```
CloudFormation
    → Stacks
        → Create Stack
```

---

## Upload Template

```
CloudFormation
    → Upload Template File
```

---

## Update Stack

```
CloudFormation
    → Stacks
        → Update
```

---

## View Stack Events

```
CloudFormation
    → Stacks
        → Events
```

---

## Delete Stack

```
CloudFormation
    → Stacks
        → Delete
```

---

# Linux Commands Used

## Connect to EC2

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

---

# Sample CloudFormation Statuses

```
CREATE_IN_PROGRESS
```

```
CREATE_COMPLETE
```

```
UPDATE_IN_PROGRESS
```

```
UPDATE_COMPLETE
```

```
DELETE_IN_PROGRESS
```

```
DELETE_COMPLETE
```

---

# Verification Checklist

- Infrastructure design completed.
- CloudFormation template created.
- YAML syntax validated successfully.
- CloudFormation Stack created.
- EC2 Instance launched.
- Security Group created.
- EC2 instance accessible through SSH.
- Stack updated successfully.
- Stack events reviewed.
- CloudFormation Stack deleted successfully.
- All associated AWS resources removed automatically.

---

# Skills Gained

- AWS CloudFormation
- Infrastructure as Code (IaC)
- YAML Templates
- CloudFormation Stacks
- Stack Parameters
- Stack Outputs
- Stack Updates
- Stack Events
- Stack Deletion
- AWS Infrastructure Automation
- Resource Provisioning
- AWS Resource Management

---

# Expected Outcome

By completing this project, you will be able to:

- Automate AWS infrastructure deployment using Infrastructure as Code.
- Deploy identical environments consistently and repeatedly.
- Update infrastructure through CloudFormation templates without manual configuration.
- Monitor stack creation, updates, and rollback events.
- Remove infrastructure cleanly by deleting the CloudFormation stack.
- Build a strong foundation in AWS-native Infrastructure as Code before learning advanced IaC tools such as Terraform.

---

# Conclusion

Successfully provisioned AWS infrastructure using AWS CloudFormation by creating a reusable YAML template to automate the deployment of an EC2 instance and its associated Security Group. Validated the template, deployed the infrastructure as a CloudFormation stack, updated resources through stack updates, monitored stack events, and cleanly removed all resources by deleting the stack. This project demonstrates the core principles of **Infrastructure as Code (IaC)**, enabling consistent, repeatable, and automated infrastructure management, which is a fundamental skill in modern Cloud and DevOps environments.
