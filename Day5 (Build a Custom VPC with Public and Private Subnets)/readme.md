# Task 5: Build a Custom VPC with Public and Private Subnets

## Project Overview

This project demonstrates how to design and configure a secure Amazon Virtual Private Cloud (VPC) by creating public and private subnets across multiple Availability Zones. The implementation includes configuring an Internet Gateway (IGW), NAT Gateway, route tables, Bastion Host, and private EC2 instance to provide secure internet connectivity while following AWS networking best practices.

---

# Objective

- Create a custom Amazon VPC.
- Create public and private subnets in multiple Availability Zones.
- Configure an Internet Gateway for public internet access.
- Configure a NAT Gateway for private outbound internet access.
- Create and associate route tables.
- Launch a Bastion Host in a public subnet.
- Launch a private EC2 instance without a public IP.
- Access the private instance securely through the Bastion Host.
- Verify internet connectivity for both public and private instances.

---

# AWS Services Used

- Amazon VPC
- Amazon EC2
- Internet Gateway (IGW)
- NAT Gateway
- Elastic IP (EIP)
- Route Tables
- Security Groups

---

# Architecture

```
                          Internet
                              │
                     Internet Gateway
                              │
              ┌───────────────┴───────────────┐
              │                               │
      Public Subnet 1                 Public Subnet 2
      (AZ-1)                          (AZ-2)
              │                               │
        Bastion Host                     NAT Gateway
              │                               │
              └───────────────┬───────────────┘
                              │
                    Private Route Table
                              │
              ┌───────────────┴───────────────┐
              │                               │
      Private Subnet 1                Private Subnet 2
          (AZ-1)                          (AZ-2)
              │                               │
         Private EC2                    Future Resources
```

---

# Network Configuration

| Resource | Example CIDR |
|----------|--------------|
| VPC | 10.0.0.0/16 |
| Public Subnet 1 | 10.0.1.0/24 |
| Public Subnet 2 | 10.0.2.0/24 |
| Private Subnet 1 | 10.0.3.0/24 |
| Private Subnet 2 | 10.0.4.0/24 |

---

# Prerequisites

- AWS Account
- EC2 Key Pair
- AWS Management Console Access

---

# Step 1: Create a Custom VPC

Navigate to

```
VPC
    → Your VPCs
```

Click

```
Create VPC
```

Configure

| Setting | Value |
|----------|-------|
| Name | Custom-VPC |
| IPv4 CIDR | 10.0.0.0/16 |
| Tenancy | Default |

Click

```
Create VPC
```

Verify the VPC is created successfully.

---

# Step 2: Create Public Subnets

Navigate to

```
VPC
    → Subnets
```

Create two public subnets.

| Subnet | Availability Zone | CIDR |
|---------|-------------------|------|
| Public-Subnet-1 | us-east-1a | 10.0.1.0/24 |
| Public-Subnet-2 | us-east-1b | 10.0.2.0/24 |

Click

```
Create Subnet
```

---

# Step 3: Create Private Subnets

Create two private subnets.

| Subnet | Availability Zone | CIDR |
|---------|-------------------|------|
| Private-Subnet-1 | us-east-1a | 10.0.3.0/24 |
| Private-Subnet-2 | us-east-1b | 10.0.4.0/24 |

Ensure CIDR blocks do not overlap.

---

# Step 4: Create and Attach an Internet Gateway

Navigate to

```
VPC
    → Internet Gateways
```

Click

```
Create Internet Gateway
```

Name

```
Custom-IGW
```

Create the gateway.

Attach it to the custom VPC.

```
Actions
    → Attach to VPC
```

Select

```
Custom-VPC
```

---

# Step 5: Create a Public Route Table

Navigate to

```
VPC
    → Route Tables
```

Create

```
Public-Route-Table
```

Add Route

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | Internet Gateway |

Associate

- Public-Subnet-1
- Public-Subnet-2

---

# Step 6: Create a NAT Gateway

Navigate to

```
VPC
    → NAT Gateways
```

Click

```
Create NAT Gateway
```

Configure

| Setting | Value |
|----------|-------|
| Subnet | Public-Subnet-1 |
| Connectivity Type | Public |

Allocate a new Elastic IP.

Click

```
Create NAT Gateway
```

Wait until the status becomes

```
Available
```

---

# Step 7: Create a Private Route Table

Create

```
Private-Route-Table
```

Add Route

| Destination | Target |
|-------------|--------|
| 0.0.0.0/0 | NAT Gateway |

Associate

- Private-Subnet-1
- Private-Subnet-2

---

# Step 8: Configure Public Subnets

Navigate to

```
Subnets
```

Select each public subnet.

Choose

```
Actions
    → Edit Subnet Settings
```

Enable

```
Auto-assign Public IPv4 Address
```

Save the changes.

Verify that new EC2 instances launched in these subnets automatically receive public IP addresses.

---

# Step 9: Launch a Bastion Host

Navigate to

```
EC2
    → Launch Instance
```

Configure

| Setting | Value |
|----------|-------|
| Name | Bastion-Host |
| AMI | Ubuntu 22.04 |
| Instance Type | t2.micro |
| Subnet | Public-Subnet-1 |
| Auto Assign Public IP | Enable |

Security Group

| Type | Port |
|------|------|
| SSH | 22 |

Launch the instance.

Connect using SSH.

```bash
chmod 400 mykey.pem
```

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

---

# Step 10: Launch a Private EC2 Instance

Launch another EC2 instance.

| Setting | Value |
|----------|-------|
| Name | Private-Server |
| AMI | Ubuntu 22.04 |
| Instance Type | t2.micro |
| Subnet | Private-Subnet-1 |
| Auto Assign Public IP | Disable |

Security Group

| Type | Source |
|------|--------|
| SSH | Bastion Host Security Group |

Launch the instance.

Verify that no public IP address is assigned.

---

# Step 11: Configure Secure Access to the Private Instance

Copy the PEM key to the Bastion Host.

```bash
scp -i mykey.pem mykey.pem ubuntu@<Bastion-Public-IP>:~
```

On the Bastion Host.

```bash
chmod 400 mykey.pem
```

Connect to the private instance.

```bash
ssh -i mykey.pem ubuntu@<Private-IP>
```

Successful login confirms secure access through the Bastion Host.

---

# Step 12: Verify Internet Access

### Verify Internet Access from Bastion Host

```bash
ping google.com
```

or

```bash
curl https://aws.amazon.com
```

Internet access should work.

---

### Verify Internet Access from Private EC2

Connect to the private instance.

Run

```bash
ping google.com
```

or

```bash
sudo apt update
```

The commands should work through the NAT Gateway.

---

### Verify No Direct Internet Access

Attempt to SSH directly to the private instance from your local machine.

```bash
ssh ubuntu@<Private-IP>
```

Expected Result

```
Connection Failed
```

The private instance is inaccessible from the internet, confirming that it is protected within the private subnet.

---

# AWS Console Navigation

## Create VPC

```
VPC
    → Your VPCs
        → Create VPC
```

---

## Create Subnets

```
VPC
    → Subnets
        → Create Subnet
```

---

## Create Internet Gateway

```
VPC
    → Internet Gateways
        → Create Internet Gateway
```

---

## Create NAT Gateway

```
VPC
    → NAT Gateways
        → Create NAT Gateway
```

---

## Create Route Tables

```
VPC
    → Route Tables
```

---

## Launch EC2 Instances

```
EC2
    → Launch Instance
```

---

# Linux Commands Used

## Connect to Bastion Host

```bash
chmod 400 mykey.pem
```

```bash
ssh -i mykey.pem ubuntu@<Bastion-Public-IP>
```

---

## Copy PEM File to Bastion Host

```bash
scp -i mykey.pem mykey.pem ubuntu@<Bastion-Public-IP>:~
```

---

## Connect to Private Instance

```bash
ssh -i mykey.pem ubuntu@<Private-IP>
```

---

## Verify Internet Access

```bash
ping google.com
```

```bash
curl https://aws.amazon.com
```

```bash
sudo apt update
```

---

# Verification Checklist

- Custom VPC created successfully.
- Two public subnets created.
- Two private subnets created.
- Internet Gateway attached to the VPC.
- Public route table configured correctly.
- NAT Gateway created with an Elastic IP.
- Private route table configured successfully.
- Public subnets auto-assign public IP addresses.
- Bastion Host deployed in the public subnet.
- Private EC2 instance launched without a public IP.
- SSH access to the private instance works through the Bastion Host.
- Bastion Host has internet connectivity.
- Private EC2 accesses the internet through the NAT Gateway.
- Private EC2 cannot be accessed directly from the internet.

---

# Skills Gained

- Amazon VPC
- Public and Private Subnets
- CIDR Planning
- Internet Gateway (IGW)
- NAT Gateway
- Elastic IP
- Route Tables
- Security Groups
- Bastion Host Architecture
- Secure SSH Access
- Private Networking
- AWS Network Design

---

# Conclusion

Successfully designed and deployed a secure AWS networking architecture by creating a custom VPC with public and private subnets across multiple Availability Zones. Configured an Internet Gateway for public connectivity, a NAT Gateway for secure outbound internet access from private resources, and implemented route tables to control network traffic. Deployed a Bastion Host for secure administrative access to a private EC2 instance, demonstrating AWS networking best practices and secure infrastructure design.
