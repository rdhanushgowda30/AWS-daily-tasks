# Task 7: Configure an Auto Scaling Group (ASG)

## Project Overview

This project demonstrates how to configure an Amazon EC2 Auto Scaling Group (ASG) to automatically launch and terminate EC2 instances based on application demand. The Auto Scaling Group is integrated with an existing Application Load Balancer (ALB) to provide high availability, fault tolerance, and scalability by distributing traffic across healthy instances.

---

# Objective

- Create a Launch Template for EC2 instances.
- Configure an Auto Scaling Group (ASG).
- Deploy instances across multiple Availability Zones.
- Attach the ASG to an existing Application Load Balancer.
- Configure EC2 and ELB health checks.
- Create dynamic scaling policies using Amazon CloudWatch metrics.
- Test automatic scale-out and scale-in operations.
- Verify traffic distribution through the ALB.

---

# AWS Services Used

- Amazon EC2
- Auto Scaling Groups (ASG)
- Launch Templates
- Application Load Balancer (ALB)
- Target Groups
- Amazon CloudWatch
- Amazon VPC

---

# Architecture

```
                           Internet
                               │
                               ▼
                  Application Load Balancer
                           (HTTP : 80)
                               │
                      Target Group (Healthy)
                               │
          ┌────────────────────┼────────────────────┐
          │                    │                    │
          ▼                    ▼                    ▼
      EC2 Instance 1      EC2 Instance 2     EC2 Instance 3
          │                    │                    │
          └────────────────────┼────────────────────┘
                               │
                    Auto Scaling Group (ASG)
                               │
                     Launch Template (AMI)
                               │
                        CloudWatch Alarm
                               │
                CPU Utilization Scaling Policy
```

---

# Prerequisites

- AWS Account
- Existing Custom VPC
- Public Subnets in Multiple Availability Zones
- Existing Application Load Balancer
- Existing Target Group
- Existing Web Server AMI or EC2 Instance
- EC2 Key Pair

---

# Step 1: Create a Launch Template

Navigate to

```
EC2
    → Launch Templates
```

Click

```
Create Launch Template
```

Configure the template.

| Setting | Value |
|----------|-------|
| Launch Template Name | WebServer-Template |
| AMI | Existing Web Server AMI |
| Instance Type | t2.micro |
| Key Pair | Existing Key Pair |
| Security Group | Web Server Security Group |
| IAM Role | Existing EC2 IAM Role (Optional) |
| Storage | Default or Custom |

Click

```
Create Launch Template
```

---

# Step 2: Create an Auto Scaling Group

Navigate to

```
EC2
    → Auto Scaling Groups
```

Click

```
Create Auto Scaling Group
```

Configure

| Setting | Value |
|----------|-------|
| Auto Scaling Group Name | WebServer-ASG |
| Launch Template | WebServer-Template |

Click

```
Next
```

---

# Step 3: Configure Network Settings

Select

| Setting | Value |
|----------|-------|
| VPC | Custom-VPC |
| Subnets | Public-Subnet-1, Public-Subnet-2 |

Deploying instances across multiple Availability Zones improves high availability.

---

# Step 4: Attach the Application Load Balancer

Select

```
Attach to an Existing Load Balancer
```

Choose

```
Application Load Balancer
```

Select the existing Target Group created in Task 6.

Example

```
Web-Target-Group
```

Click

```
Next
```

---

# Step 5: Configure Group Size

Configure the Auto Scaling capacity.

| Capacity | Value |
|----------|-------|
| Minimum Capacity | 2 |
| Desired Capacity | 2 |
| Maximum Capacity | 4 |

These values can be adjusted based on application requirements.

---

# Step 6: Configure Health Checks

Enable the following health checks.

| Health Check | Enabled |
|--------------|---------|
| EC2 | Yes |
| Elastic Load Balancer (ELB) | Yes |

Health Check Grace Period

```
300 Seconds
```

This allows newly launched instances enough time to initialize before health evaluation.

---

# Step 7: Configure Scaling Policies

Select

```
Target Tracking Scaling Policy
```

Configure

| Setting | Value |
|----------|-------|
| Metric | Average CPU Utilization |
| Target Value | 50% |

Scaling Behavior

### Scale Out

- Add new EC2 instances when average CPU utilization exceeds 50%.

### Scale In

- Remove EC2 instances when CPU utilization drops below the target threshold.

Save the scaling policy.

---

# Step 8: Review and Create the Auto Scaling Group

Review all settings.

Verify

- Launch Template
- Network Configuration
- Load Balancer
- Target Group
- Health Checks
- Scaling Policy

Click

```
Create Auto Scaling Group
```

The desired number of EC2 instances will launch automatically.

---

# Step 9: Verify Auto Scaling Functionality

Navigate to

```
EC2
    → Auto Scaling Groups
```

Verify

- Desired Capacity
- Healthy Instances
- Instance Lifecycle

Navigate to

```
Target Groups
```

Confirm all EC2 instances show the status

```
Healthy
```

Open the ALB DNS name in a browser.

Verify that requests are distributed among healthy instances.

---

# Step 10: Test Auto Scaling

Connect to one of the EC2 instances.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Install the stress utility.

```bash
sudo apt update
```

```bash
sudo apt install stress -y
```

Generate CPU load.

```bash
stress --cpu 2 --timeout 300
```

Alternatively, use

```bash
yes > /dev/null
```

Monitor

```
EC2
    → Auto Scaling Groups
```

When CPU utilization exceeds the configured threshold:

- A new EC2 instance should launch automatically.

Wait until the new instance becomes

```
Healthy
```

Stop the workload.

If using stress

```bash
Ctrl + C
```

If using yes

```bash
pkill yes
```

After CPU utilization decreases, verify that the Auto Scaling Group terminates excess instances and returns to the desired capacity.

---

# AWS Console Navigation

## Create Launch Template

```
EC2
    → Launch Templates
        → Create Launch Template
```

---

## Create Auto Scaling Group

```
EC2
    → Auto Scaling Groups
        → Create Auto Scaling Group
```

---

## Attach Load Balancer

```
Auto Scaling Group
    → Load Balancing
```

---

## Configure Scaling Policy

```
Auto Scaling Group
    → Automatic Scaling
```

---

## View Target Health

```
EC2
    → Target Groups
```

---

## View CloudWatch Metrics

```
CloudWatch
    → Metrics
```

---

# Linux Commands Used

## Connect to EC2

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

---

## Update Packages

```bash
sudo apt update
```

---

## Install Stress Utility

```bash
sudo apt install stress -y
```

---

## Generate CPU Load

```bash
stress --cpu 2 --timeout 300
```

---

## Alternative CPU Load

```bash
yes > /dev/null
```

---

## Stop CPU Load

```bash
pkill yes
```

or

```bash
Ctrl + C
```

---

# Verification Checklist

- Launch Template created successfully.
- Auto Scaling Group created successfully.
- Custom VPC and public subnets selected.
- Existing Application Load Balancer attached.
- Existing Target Group associated.
- Minimum, Desired, and Maximum capacities configured.
- EC2 and ELB health checks enabled.
- Dynamic scaling policy configured.
- Desired EC2 instances launched automatically.
- Instances registered as healthy in the Target Group.
- Traffic distributed through the Application Load Balancer.
- Additional EC2 instances launched when CPU utilization increased.
- Extra instances terminated automatically after CPU utilization decreased.

---

# Skills Gained

- Amazon EC2
- Launch Templates
- Auto Scaling Groups (ASG)
- Dynamic Scaling Policies
- Amazon CloudWatch Metrics
- Application Load Balancer (ALB)
- Target Groups
- High Availability
- Fault Tolerance
- Horizontal Scaling
- Elastic Infrastructure
- AWS Compute Services

---

# Conclusion

Successfully configured an Amazon EC2 Auto Scaling Group (ASG) using a Launch Template and integrated it with an existing Application Load Balancer (ALB). Implemented dynamic scaling policies based on Amazon CloudWatch CPU Utilization metrics, enabling automatic scale-out during increased demand and scale-in during reduced utilization. Verified that new instances were automatically registered with the Target Group and that traffic continued to be distributed across healthy instances, demonstrating a highly available, fault-tolerant, and scalable AWS architecture.
