# Task 4: Identity and Access Management (IAM)

## Project Overview

This project demonstrates how to implement secure access control in AWS using Identity and Access Management (IAM). It covers creating IAM users, groups, managed policies, custom policies, and IAM roles while following the Principle of Least Privilege (PoLP). The project also includes attaching an IAM role to an EC2 instance and verifying secure access to AWS resources without using access keys.

---

# Objective

- Create IAM users for different responsibilities.
- Organize users into IAM groups.
- Assign appropriate AWS managed policies.
- Verify user permissions.
- Create and attach a custom IAM policy.
- Create an IAM role for Amazon EC2.
- Attach the IAM role to an EC2 instance.
- Verify secure access to AWS resources using the IAM role.

---

# AWS Services Used

- AWS Identity and Access Management (IAM)
- Amazon EC2
- IAM Policies
- IAM Roles

---

# Architecture

```
                     AWS IAM
                        │
        ┌───────────────┼────────────────┐
        │               │                │
        ▼               ▼                ▼
   EC2Admins       S3Admins      Administrators
        │               │                │
        ▼               ▼                ▼
   ec2-user        s3-user       admin-user

                        │
                        ▼
                 IAM Custom Policy

                        │
                        ▼
                  IAM Role (EC2)

                        │
                        ▼
                 Amazon EC2 Instance

                        │
                        ▼
          Secure AWS Resource Access
       (Without Access Keys)
```

---

# Prerequisites

- AWS Account
- AWS Management Console Access
- Existing EC2 Instance
- Administrator permissions

---

# Step 1: Create IAM Users

Login to the AWS Management Console.

Navigate to

```
IAM → Users
```

Click

```
Create User
```

Create the following users:

| User Name |
|------------|
| ec2-user |
| s3-user |
| admin-user |

For each user:

- Enable **AWS Management Console access**.
- Set a temporary password.
- Enable **Users must create a new password at next sign-in**.

Click

```
Create User
```

---

# Step 2: Create IAM Groups

Navigate to

```
IAM → User Groups
```

Create the following groups:

| Group Name |
|------------|
| EC2Admins |
| S3Admins |
| Administrators |

Click

```
Create Group
```

---

# Step 3: Add Users to Groups

Assign users to their respective groups.

| User | Group |
|------|-------|
| ec2-user | EC2Admins |
| s3-user | S3Admins |
| admin-user | Administrators |

Save the changes.

---

# Step 4: Assign Permissions

Attach AWS Managed Policies to each group.

| Group | AWS Managed Policy |
|--------|--------------------|
| EC2Admins | AmazonEC2FullAccess |
| S3Admins | AmazonS3FullAccess |
| Administrators | AdministratorAccess |

Navigate to

```
IAM
    → User Groups
        → Permissions
            → Add Permissions
```

Attach the required policy.

---

# Step 5: Verify User Permissions

Sign in using each IAM user.

Verify access permissions.

### ec2-user

Should have access to:

- Amazon EC2

Should **not** have access to:

- Amazon S3
- IAM

---

### s3-user

Should have access to:

- Amazon S3

Should **not** have access to:

- Amazon EC2
- IAM

---

### admin-user

Should have unrestricted access to all AWS services.

---

# Step 6: Create a Custom IAM Policy

Navigate to

```
IAM
    → Policies
```

Click

```
Create Policy
```

Choose

```
JSON
```

Example Custom Policy (Read-only access to a specific S3 bucket)

> Replace **your-bucket-name** with your bucket name.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::your-bucket-name",
                "arn:aws:s3:::your-bucket-name/*"
            ]
        }
    ]
}
```

Click

```
Next
```

Policy Name

```
S3ReadOnlyCustomPolicy
```

Click

```
Create Policy
```

---

# Step 7: Attach the Custom Policy

Navigate to

```
IAM
    → Users
```

or

```
IAM
    → User Groups
```

Click

```
Add Permissions
```

Attach

```
S3ReadOnlyCustomPolicy
```

Save the changes.

Verify that the assigned permissions work correctly.

---

# Step 8: Create an IAM Role

Navigate to

```
IAM
    → Roles
```

Click

```
Create Role
```

Trusted Entity

```
AWS Service
```

Use Case

```
EC2
```

Click

```
Next
```

Attach a policy.

Example

```
AmazonS3ReadOnlyAccess
```

Role Name

```
EC2-S3-Access-Role
```

Click

```
Create Role
```

---

# Step 9: Attach the IAM Role to an EC2 Instance

Navigate to

```
EC2
    → Instances
```

Select the EC2 instance.

Click

```
Actions
    → Security
        → Modify IAM Role
```

Select

```
EC2-S3-Access-Role
```

Click

```
Update IAM Role
```

---

# Step 10: Verify IAM Role Access

Connect to the EC2 instance.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Verify that the IAM role is attached.

```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

The attached IAM Role name should be displayed.

Example

```
EC2-S3-Access-Role
```

Install AWS CLI if not already installed.

```bash
aws --version
```

Verify access to Amazon S3.

```bash
aws s3 ls
```

If the IAM role has permission, the command lists accessible S3 buckets without requiring AWS Access Keys.

---

# AWS Console Navigation

## Create Users

```
IAM
    → Users
        → Create User
```

---

## Create Groups

```
IAM
    → User Groups
        → Create Group
```

---

## Assign Policies

```
IAM
    → User Groups
        → Permissions
```

---

## Create Custom Policy

```
IAM
    → Policies
        → Create Policy
```

---

## Create IAM Role

```
IAM
    → Roles
        → Create Role
```

---

## Attach Role to EC2

```
EC2
    → Instances
        → Actions
            → Security
                → Modify IAM Role
```

---

# Commands Used

## Connect to EC2

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

---

## Verify AWS CLI

```bash
aws --version
```

---

## Check Attached IAM Role

```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```

---

## List S3 Buckets

```bash
aws s3 ls
```

---

# AWS Managed Policies Used

| Group | Policy |
|--------|--------|
| EC2Admins | AmazonEC2FullAccess |
| S3Admins | AmazonS3FullAccess |
| Administrators | AdministratorAccess |

---

# Verification Checklist

- Three IAM users created successfully.
- Three IAM groups created successfully.
- Users added to the correct groups.
- AWS managed policies assigned successfully.
- User permissions verified.
- Unauthorized services inaccessible.
- Custom IAM policy created.
- Custom policy attached successfully.
- IAM role created for EC2.
- IAM role attached to EC2 instance.
- EC2 accessed AWS resources securely without using access keys.

---

# Skills Gained

- AWS Identity and Access Management (IAM)
- IAM Users
- IAM Groups
- AWS Managed Policies
- Custom IAM Policies
- Principle of Least Privilege (PoLP)
- IAM Roles
- EC2 Role Attachment
- Secure AWS Authentication
- AWS CLI Authentication Using IAM Roles

---

# Conclusion

Successfully implemented secure access control in AWS using IAM by creating users, groups, managed policies, custom policies, and IAM roles. Applied the Principle of Least Privilege to restrict access based on user responsibilities and securely enabled an EC2 instance to access AWS resources through an IAM role without storing access keys. This project demonstrates best practices for identity management and secure resource access in AWS.
