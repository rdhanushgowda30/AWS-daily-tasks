# Task 1: Launch and Configure an EC2 Web Server using Amazon EC2 and AMI

## Project Overview

This project demonstrates how to deploy a Linux-based web server on Amazon EC2, host a static HTML website, create an Amazon Machine Image (AMI), and launch another EC2 instance from the AMI. The newly launched instance serves the same website without requiring any additional configuration.

---

# Objective

- Launch an Ubuntu EC2 instance.
- Configure it as a web server.
- Deploy a simple HTML webpage.
- Create an Amazon Machine Image (AMI).
- Launch another EC2 instance using the created AMI.
- Verify that the website works on the new instance.

---

# AWS Services Used

- Amazon EC2
- Amazon Machine Image (AMI)
- Security Groups
- Key Pair
- Ubuntu Linux
- Apache Web Server

---

# Architecture

```
               Internet
                    │
                    ▼
        Security Group (22,80)
                    │
                    ▼
          EC2 Ubuntu Instance
                    │
          Apache Web Server
                    │
           Static HTML Website
                    │
          Create Amazon AMI
                    │
                    ▼
        Launch New EC2 Instance
                    │
                    ▼
        Same Website Available
```

---

# Prerequisites

- AWS Account
- EC2 Key Pair (.pem)
- Basic Linux Commands
- SSH Client (Git Bash / Terminal / PuTTY)

---

# Step 1: Launch an EC2 Instance

1. Login to the AWS Management Console.

2. Search for **EC2**.

3. Click **Launch Instance**.

4. Configure the instance:

| Setting | Value |
|----------|-------|
| Name | WebServer-1 |
| AMI | Ubuntu Server 22.04 LTS |
| Instance Type | t2.micro |
| Key Pair | Existing or Create New |
| Network | Default VPC |
| Auto Assign Public IP | Enable |

---

## Configure Security Group

Allow the following inbound rules:

| Type | Protocol | Port |
|------|----------|------|
| SSH | TCP | 22 |
| HTTP | TCP | 80 |

Launch the instance.

---

# Step 2: Connect to the EC2 Instance

Open Git Bash or Terminal.

Navigate to the folder containing your key pair.

```bash
cd Downloads
```

Change the permission of the key file.

```bash
chmod 400 mykey.pem
```

Connect to the EC2 instance.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Example

```bash
ssh -i mykey.pem ubuntu@54.xx.xx.xx
```

---

# Step 3: Update the Server

Update the package repository.

```bash
sudo apt update
```

Upgrade installed packages.

```bash
sudo apt upgrade -y
```

---

# Step 4: Install Apache Web Server

Install Apache.

```bash
sudo apt install apache2 -y
```

Start Apache.

```bash
sudo systemctl start apache2
```

Enable Apache to start automatically after reboot.

```bash
sudo systemctl enable apache2
```

Verify Apache status.

```bash
sudo systemctl status apache2
```

---

# Step 5: Create a Simple HTML Website

Navigate to the Apache web directory.

```bash
cd /var/www/html
```

Create or edit the index page.

```bash
sudo nano index.html
```

Example HTML:

```html
<!DOCTYPE html>
<html>
<head>
    <title>AWS EC2 Web Server</title>
</head>
<body>
    <h1>Welcome to My AWS Web Server</h1>
    <h2>Hosted on Amazon EC2</h2>
    <p>This website is deployed using Apache Web Server.</p>
</body>
</html>
```

Save the file.

Press

```
CTRL + O
Enter
CTRL + X
```

---

# Step 6: Verify the Website

Copy the Public IPv4 Address of the EC2 instance.

Open the browser.

```
http://<Public-IP>
```

Example

```
http://54.xx.xx.xx
```

Expected Output

```
Welcome to My AWS Web Server
Hosted on Amazon EC2
This website is deployed using Apache Web Server.
```

---

# Step 7: Create an Amazon Machine Image (AMI)

1. Go to EC2 Console.

2. Select the configured EC2 instance.

3. Click

```
Actions
    → Image and Templates
        → Create Image
```

4. Enter

```
Image Name:
WebServer-AMI
```

5. Click

```
Create Image
```

6. Wait until the image status changes to

```
Available
```

---

# Step 8: Launch a New EC2 Instance from the AMI

Go to

```
EC2
    → AMIs
```

Select the created AMI.

Click

```
Launch Instance from AMI
```

Configure:

| Setting | Value |
|----------|-------|
| Name | WebServer-2 |
| Instance Type | t2.micro |
| Key Pair | Same Key Pair |
| Security Group | Allow SSH and HTTP |

Launch the instance.

---

# Step 9: Verify the New Instance

Wait until the instance is running.

Copy the new Public IP.

Open

```
http://<New-Public-IP>
```

The same webpage should appear immediately without reinstalling Apache or recreating the HTML page.

This confirms that the AMI successfully preserved the server configuration and website files.

---

# Linux Commands Used

## Update Packages

```bash
sudo apt update
sudo apt upgrade -y
```

## Install Apache

```bash
sudo apt install apache2 -y
```

## Start Apache

```bash
sudo systemctl start apache2
```

## Enable Apache

```bash
sudo systemctl enable apache2
```

## Check Apache Status

```bash
sudo systemctl status apache2
```

## Navigate to Web Directory

```bash
cd /var/www/html
```

## Edit HTML Page

```bash
sudo nano index.html
```

## SSH Connection

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

---

# Verification Checklist

- Ubuntu EC2 instance launched successfully.
- SSH connection established.
- Apache installed successfully.
- Apache service started and enabled.
- HTML webpage created.
- Website accessible using Public IP.
- AMI created successfully.
- New EC2 instance launched from the AMI.
- Same webpage displayed on the new instance without additional configuration.

---

# Skills Gained

- Amazon EC2
- Linux Administration
- SSH Connectivity
- Apache Web Server Installation
- Static Website Hosting
- Security Group Configuration
- Amazon Machine Image (AMI)
- EC2 Deployment from AMI
- Basic AWS Infrastructure Management

---

# Conclusion

Successfully deployed an Ubuntu-based web server on Amazon EC2, hosted a static HTML website using Apache, created an Amazon Machine Image (AMI), and launched a new EC2 instance from the AMI. The new instance served the same website without requiring any manual configuration, demonstrating the use of AMIs for rapid and consistent infrastructure deployment.
