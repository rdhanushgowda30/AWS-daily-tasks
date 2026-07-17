# Task 6: Deploy an Application Load Balancer (ALB)

## Project Overview

This project demonstrates how to deploy multiple Amazon EC2 web servers behind an AWS Application Load Balancer (ALB). The ALB distributes incoming HTTP traffic across multiple EC2 instances deployed in different Availability Zones, providing high availability, scalability, and fault tolerance. The project also covers creating a Target Group, configuring health checks, and testing failover by stopping one of the web servers.

---

# Objective

- Launch multiple EC2 web servers.
- Configure Apache web servers.
- Create a Target Group.
- Configure health checks.
- Deploy an Internet-facing Application Load Balancer.
- Register EC2 instances with the Target Group.
- Verify load balancing.
- Test high availability and fault tolerance.
- Review monitoring information.

---

# AWS Services Used

- Amazon EC2
- Application Load Balancer (ALB)
- Target Groups
- Amazon VPC
- Security Groups
- Elastic Load Balancing (ELB)

---

# Architecture

```
                      Internet
                          │
                          ▼
              Application Load Balancer
                     (HTTP : 80)
                          │
          ┌───────────────┴───────────────┐
          │                               │
          ▼                               ▼
     Web Server 1                    Web Server 2
   Public Subnet-A                Public Subnet-B
          │                               │
          └───────────────┬───────────────┘
                          │
                   Target Group
                    Health Checks
```

---

# Prerequisites

- AWS Account
- Custom VPC
- Two Public Subnets
- Internet Gateway
- Security Group allowing HTTP (80) and SSH (22)
- EC2 Key Pair

---

# Step 1: Launch Two EC2 Web Servers

Navigate to

```
EC2
    → Launch Instance
```

Launch two Ubuntu EC2 instances.

| Setting | Web Server 1 | Web Server 2 |
|----------|--------------|--------------|
| Name | WebServer-1 | WebServer-2 |
| AMI | Ubuntu 22.04 | Ubuntu 22.04 |
| Instance Type | t2.micro | t2.micro |
| Subnet | Public-Subnet-1 | Public-Subnet-2 |
| Auto Assign Public IP | Enabled | Enabled |

Attach the same Security Group allowing:

| Protocol | Port |
|----------|------|
| SSH | 22 |
| HTTP | 80 |

Verify both instances are in the **Running** state.

---

# Step 2: Configure the Web Servers

Connect to **Web Server 1**.

```bash
ssh -i mykey.pem ubuntu@<Public-IP-1>
```

Update packages.

```bash
sudo apt update
```

Install Apache.

```bash
sudo apt install apache2 -y
```

Enable Apache.

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```

Create the web page.

```bash
echo "<h1>Web Server 1</h1>" | sudo tee /var/www/html/index.html
```

---

Repeat the same steps for **Web Server 2**.

```bash
ssh -i mykey.pem ubuntu@<Public-IP-2>
```

```bash
sudo apt update
```

```bash
sudo apt install apache2 -y
```

```bash
sudo systemctl enable apache2
sudo systemctl start apache2
```

```bash
echo "<h1>Web Server 2</h1>" | sudo tee /var/www/html/index.html
```

Verify each server individually.

```
http://<Public-IP-1>
```

```
http://<Public-IP-2>
```

Each instance should  display different content.

---

# Step 3: Create a Target Group

Navigate to

```
EC2
    → Target Groups
```

Click

```
Create Target Group
```

Configure

| Setting | Value |
|----------|-------|
| Target Type | Instances |
| Protocol | HTTP |
| Port | 80 |
| VPC | Custom-VPC |

Click

```
Next
```

Register

- WebServer-1
- WebServer-2

Click

```
Create Target Group
```

---

# Step 4: Configure Health Checks

Open the Target Group.

Navigate to

```
Health Checks
```

Configure

| Setting | Value |
|----------|-------|
| Protocol | HTTP |
| Path | / |

Save the configuration.

Wait until both instances display:

```
Healthy
```

---

# Step 5: Create an Application Load Balancer

Navigate to

```
EC2
    → Load Balancers
```

Click

```
Create Load Balancer
```

Select

```
Application Load Balancer
```

Configure

| Setting | Value |
|----------|-------|
| Scheme | Internet-facing |
| IP Type | IPv4 |
| VPC | Custom-VPC |
| Availability Zones | Public-Subnet-1 & Public-Subnet-2 |

Attach the Security Group allowing:

| Protocol | Port |
|----------|------|
| HTTP | 80 |

Configure Listener

| Protocol | Port |
|----------|------|
| HTTP | 80 |

---

# Step 6: Associate the Target Group

Choose

```
Forward Requests To
```

Select

```
Web-Target-Group
```

Review the configuration.

Click

```
Create Load Balancer
```

Wait until the status becomes

```
Active
```

---

# Step 7: Verify the Load Balancer

Copy the ALB DNS Name.

Example

```
http://my-alb-123456.us-east-1.elb.amazonaws.com
```

Open the DNS name in a web browser.

Refresh the page multiple times.

Expected Output

```
Web Server 1
```

Refresh again

```
Web Server 2
```

The ALB distributes incoming requests across both instances.

---

# Step 8: Test High Availability

Stop **WebServer-2**.

Navigate to

```
EC2
    → Instances
```

Select

```
WebServer-2
```

Click

```
Instance State
    → Stop Instance
```

Wait until the Target Group marks the instance as

```
Unhealthy
```

Refresh the ALB DNS URL.

Only

```
Web Server 1
```

should respond.

Restart **WebServer-2**.

Wait until the Target Group status changes back to

```
Healthy
```

Refresh the ALB DNS URL again.

Traffic should now be distributed between both web servers.

---

# Step 9: Review Monitoring Information

Navigate to

```
EC2
    → Target Groups
```

Review

- Target Health
- Health Status
- Registered Targets

Navigate to

```
EC2
    → Load Balancers
```

Review

- Active Targets
- Listener Configuration
- Request Routing
- Availability Zones

---

# AWS Console Navigation

## Launch EC2 Instances

```
EC2
    → Launch Instance
```

---

## Create Target Group

```
EC2
    → Target Groups
        → Create Target Group
```

---

## Configure Health Checks

```
EC2
    → Target Groups
        → Health Checks
```

---

## Create Load Balancer

```
EC2
    → Load Balancers
        → Create Load Balancer
```

---

## Register Targets

```
Target Group
    → Register Targets
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

## Install Apache

```bash
sudo apt install apache2 -y
```

---

## Enable Apache

```bash
sudo systemctl enable apache2
```

```bash
sudo systemctl start apache2
```

---

## Create Web Page (Server 1)

```bash
echo "<h1>Web Server 1</h1>" | sudo tee /var/www/html/index.html
```

---

## Create Web Page (Server 2)

```bash
echo "<h1>Web Server 2</h1>" | sudo tee /var/www/html/index.html
```

---

## Verify Apache Status

```bash
sudo systemctl status apache2
```

---

# Verification Checklist

- Two EC2 web servers launched successfully.
- Apache installed on both instances.
- Different webpages configured on each server.
- Target Group created successfully.
- Both EC2 instances registered as targets.
- Health checks passed successfully.
- Application Load Balancer created.
- Target Group associated with the ALB.
- ALB distributed traffic evenly across both servers.
- High availability verified by stopping one instance.
- ALB routed traffic only to the healthy instance.
- Restarted instance became healthy and resumed receiving traffic.
- Target health and load balancer monitoring verified.

---

# Skills Gained

- Amazon EC2
- Application Load Balancer (ALB)
- Target Groups
- Health Checks
- Elastic Load Balancing
- High Availability
- Fault Tolerance
- Traffic Distribution
- Security Groups
- AWS Networking

---

# Conclusion

Successfully deployed an Application Load Balancer (ALB) to distribute HTTP traffic across two EC2 web servers hosted in separate public subnets within a custom VPC. Configured a Target Group with health checks to monitor instance availability and validated high availability by simulating a server failure. The ALB automatically routed traffic only to healthy instances and resumed balanced traffic distribution once the failed instance recovered, demonstrating scalable, fault-tolerant, and highly available application architecture on AWS.
