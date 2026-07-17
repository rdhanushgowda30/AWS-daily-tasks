# Task 10: Deploy Amazon RDS (MySQL)

## Project Overview

This project demonstrates how to deploy a managed Amazon RDS MySQL database inside private subnets of a custom Amazon VPC. The project includes creating a DB Subnet Group, configuring secure networking, connecting an EC2 instance to the database, performing SQL operations, creating a manual database snapshot, and restoring the database from the snapshot while following AWS security best practices.

---

# Objective

- Create a DB Subnet Group using private subnets.
- Deploy an Amazon RDS MySQL database.
- Configure secure networking using Security Groups.
- Connect an EC2 instance to the database.
- Perform basic MySQL database operations.
- Verify secure connectivity.
- Create a manual database snapshot.
- Restore a new database instance from the snapshot.

---

# AWS Services Used

- Amazon RDS (MySQL)
- Amazon EC2
- Amazon VPC
- DB Subnet Groups
- Security Groups
- Amazon RDS Snapshots

---

# Architecture

```
                    Internet
                        │
                        │
                (No Direct Access)
                        │
                        ▼
              Public Subnet (EC2)
             Web/Application Server
                        │
          Security Group (MySQL 3306)
                        │
                        ▼
        Private Subnet 1        Private Subnet 2
                │                     │
                └─────────┬───────────┘
                          │
                  DB Subnet Group
                          │
                          ▼
               Amazon RDS MySQL
                          │
                          ▼
                 Manual Snapshot
                          │
                          ▼
             Restored RDS Instance
```

---

# Prerequisites

- AWS Account
- Custom VPC
- Two Private Subnets
- Running EC2 Instance
- SSH Key Pair
- Existing Security Groups

---

# Step 1: Create a DB Subnet Group

Navigate to

```
Amazon RDS
    → Subnet Groups
```

Click

```
Create DB Subnet Group
```

Configure

| Setting | Value |
|----------|-------|
| Name | my-db-subnet-group |
| Description | Subnet group for MySQL database |
| VPC | Custom-VPC |

Select the two private subnets created in Task 5.

Click

```
Create
```

### Expected Result

A DB Subnet Group is successfully created and available for use during RDS deployment.

---

# Step 2: Create an Amazon RDS MySQL Instance

Navigate to

```
Amazon RDS
    → Databases
```

Click

```
Create Database
```

Configure

| Setting | Value |
|----------|-------|
| Creation Method | Standard Create |
| Engine | MySQL |
| Template | Free Tier (if eligible) |
| DB Instance Identifier | mydb-instance |
| Master Username | admin |
| Master Password | Create a strong password |
| Instance Class | db.t3.micro |
| Storage Type | General Purpose SSD (gp3) |
| Allocated Storage | 20 GB |

---

# Step 3: Configure Network Settings

Configure

| Setting | Value |
|----------|-------|
| VPC | Custom-VPC |
| DB Subnet Group | my-db-subnet-group |
| Public Access | No |
| Security Group | RDS-SG |

### Why Disable Public Access?

The database should only be accessible from trusted resources (such as an EC2 instance) inside the VPC and should not be exposed to the public internet.

---

# Step 4: Configure Database Options

Configure

| Setting | Value |
|----------|-------|
| Initial Database Name | studentdb |
| Backup Retention | 7 Days (Default) |
| Enhanced Monitoring | Optional (Disabled for this project) |

Leave the remaining settings at their default values.

---

# Step 5: Launch the Database

Review all configuration settings.

Click

```
Create Database
```

Wait until the database status changes from

```
Creating
```

to

```
Available
```

This process may take several minutes.

---

# Step 6: Configure Security Group Rules

Navigate to

```
EC2
    → Security Groups
```

Open the security group attached to the RDS instance (**RDS-SG**).

Edit the inbound rules.

| Type | Port | Source |
|------|------|--------|
| MySQL/Aurora | 3306 | EC2 Security Group |

### Why Use the EC2 Security Group?

Using the EC2 Security Group as the source ensures that **only EC2 instances associated with that security group can connect to the database**, even if  their private IP addresses change.

Verify that:

- **Public Access = No**
- Only the EC2 Security Group has access to port **3306**

---

# Step 7: Connect the EC2 Instance to the Database

Connect to the EC2 instance.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Update package information.

```bash
sudo apt update
```

Install the MySQL client.

```bash
sudo apt install mysql-client -y
```

Copy the **RDS Endpoint** from the Amazon RDS console.

Connect to the database.

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

Example

```bash
mysql -h mydb-instance.xxxxxx.us-east-1.rds.amazonaws.com -u admin -p
```

Enter the database password when prompted.

---

# Step 8: Perform Database Operations

If you created an initial database during RDS creation:

```sql
USE studentdb;
```

Otherwise, create one.

```sql
CREATE DATABASE studentdb;
```

```sql
USE studentdb;
```

### Create a Table

```sql
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    course VARCHAR(50)
);
```

### Insert Sample Records

```sql
INSERT INTO students VALUES (1,'Dhanush','AWS');
```

```sql
INSERT INTO students VALUES (2,'Rahul','DevOps');
```

### Retrieve Stored Records

```sql
SELECT * FROM students;
```

### Expected Output

| id | name | course |
|----|------|---------|
| 1 | Dhanush | AWS |
| 2 | Rahul | DevOps |

---

# Step 9: Test Connectivity

### Test from the EC2 Instance

The database connection should succeed.

### Test from an External System

Attempting to connect directly from your laptop should fail because:

- Public Access is disabled.
- The Security Group allows only the EC2 Security Group.

This confirms that the database is securely protected.

---

# Step 10: Create a Database Snapshot

Navigate to

```
Amazon RDS
    → Databases
```

Select your RDS instance.

Click

```
Actions
    → Take Snapshot
```

Snapshot Identifier

```
studentdb-snapshot
```

Click

```
Take Snapshot
```

Navigate to

```
Snapshots
```

Wait until the snapshot status becomes

```
Available
```

---

# Step 11: Restore the Database

Navigate to

```
Amazon RDS
    → Snapshots
```

Select

```
studentdb-snapshot
```

Click

```
Restore Snapshot
```

Configure

| Setting | Value |
|----------|-------|
| DB Instance Identifier | studentdb-restored |

Review the configuration.

Click

```
Restore
```

Wait until the restored RDS instance status becomes

```
Available
```

Copy the new endpoint.

Connect using the MySQL client.

```bash
mysql -h <RESTORED-ENDPOINT> -u admin -p
```

Verify the data.

```sql
USE studentdb;
```

```sql
SELECT * FROM students;
```

The restored database should contain the same records that existed before the snapshot.

---

# AWS Console Navigation

## Create DB Subnet Group

```
Amazon RDS
    → Subnet Groups
        → Create DB Subnet Group
```

---

## Create Database

```
Amazon RDS
    → Databases
        → Create Database
```

---

## Configure Security Group

```
EC2
    → Security Groups
```

---

## Create Snapshot

```
Amazon RDS
    → Databases
        → Actions
            → Take Snapshot
```

---

## Restore Snapshot

```
Amazon RDS
    → Snapshots
        → Restore Snapshot
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

## Install MySQL Client

```bash
sudo apt install mysql-client -y
```

---

## Connect to the Database

```bash
mysql -h <RDS-ENDPOINT> -u admin -p
```

---

# SQL Commands Used

## Create Database

```sql
CREATE DATABASE studentdb;
```

---

## Use Database

```sql
USE studentdb;
```

---

## Create Table

```sql
CREATE TABLE students (
    id INT PRIMARY KEY,
    name VARCHAR(50),
    course VARCHAR(50)
);
```

---

## Insert Records

```sql
INSERT INTO students VALUES (1,'Dhanush','AWS');
INSERT INTO students VALUES (2,'Rahul','DevOps');
```

---

## Retrieve Records

```sql
SELECT * FROM students;
```

---

# Verification Checklist

- DB Subnet Group created successfully.
- Amazon RDS MySQL instance deployed.
- Database placed inside private subnets.
- Public access disabled.
- Security Group configured correctly.
- EC2 instance connected successfully to the database.
- Database and table created.
- Sample records inserted and retrieved successfully.
- External access to the database blocked.
- Manual snapshot created successfully.
- Database restored from the snapshot.
- Restored database contained all original data.

---

# Skills Gained

- Amazon RDS (MySQL)
- DB Subnet Groups
- Amazon VPC Networking
- Security Groups
- Private Database Deployment
- MySQL Client
- SQL Fundamentals
- Database Snapshots
- Backup and Restore
- AWS Database Administration

---

# Conclusion

Successfully deployed a managed Amazon RDS MySQL database within private subnets of a custom VPC, ensuring secure network isolation and controlled access through Security Groups. Connected an EC2 instance to the database, performed essential SQL operations, and verified secure communication. Implemented backup and disaster recovery by creating a manual database snapshot and restoring a new RDS instance from it, demonstrating best practices for secure, highly available, and manageable database deployments on AWS.
