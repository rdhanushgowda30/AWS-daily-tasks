# Task 8: Monitor AWS Resources Using Amazon CloudWatch

## Project Overview

This project demonstrates how to monitor Amazon EC2 instances using Amazon CloudWatch. It includes verifying EC2 performance metrics, creating a CloudWatch dashboard, configuring CloudWatch alarms, integrating Amazon SNS for email notifications, and generating CPU load to test monitoring and alerting capabilities.

---

# Objective

- Verify CloudWatch metrics for an EC2 instance.
- Create a CloudWatch Dashboard.
- Configure an Amazon SNS topic for notifications.
- Subscribe an email address to receive alerts.
- Create a CloudWatch Alarm based on CPU utilization.
- Generate CPU load to trigger the alarm.
- Verify email notifications and dashboard updates.

---

# AWS Services Used

- Amazon CloudWatch
- Amazon EC2
- Amazon SNS (Simple Notification Service)
- CloudWatch Dashboard
- CloudWatch Alarms

---

# Architecture

```
                EC2 Instance
                     │
           CloudWatch Metrics
                     │
        ┌────────────┴────────────┐
        │                         │
        ▼                         ▼
 CloudWatch Dashboard      CloudWatch Alarm
                                      │
                                      ▼
                              Amazon SNS Topic
                                      │
                                      ▼
                           Email Notification
```

---

# Prerequisites

- AWS Account
- Running Amazon EC2 Instance
- SSH Key Pair
- AWS Management Console Access
- Verified Email Address for SNS Notifications

---

# Step 1: Verify CloudWatch Metrics

Navigate to

```
CloudWatch
    → Metrics
```

Select

```
EC2
    → Per-Instance Metrics
```

Verify the following metrics are available for your EC2 instance:

- CPUUtilization
- NetworkIn
- NetworkOut
- DiskReadBytes
- DiskWriteBytes

### Expected Result

The EC2 instance metrics should be visible and updating automatically.

---

# Step 2: Create a CloudWatch Dashboard

Navigate to

```
CloudWatch
    → Dashboards
```

Click

```
Create Dashboard
```

Dashboard Name

```
EC2-Monitoring
```

Click

```
Create Dashboard
```

Add a widget.

Choose

```
Line Graph
```

Select

```
EC2
    → Per-Instance Metrics
```

Choose your EC2 instance.

Add the following metric:

```
CPUUtilization
```

Click

```
Create Widget
```

Save the dashboard.

### Expected Result

A dashboard displaying the EC2 CPU Utilization graph.

---

# Step 3: Create an Amazon SNS Topic

Navigate to

```
Amazon SNS
    → Topics
```

Click

```
Create Topic
```

Configure

| Setting | Value |
|----------|-------|
| Type | Standard |
| Topic Name | EC2-Alerts |

Click

```
Create Topic
```

---

# Step 4: Create an Email Subscription

Open the newly created topic.

Click

```
Create Subscription
```

Configure

| Setting | Value |
|----------|-------|
| Protocol | Email |
| Endpoint | your-email@example.com |

Click

```
Create Subscription
```

Open your email inbox.

Click the

```
Confirm Subscription
```

link received from Amazon SNS.

### Expected Result

Subscription status changes to

```
Confirmed
```

---

# Step 5: Create a CloudWatch Alarm

Navigate to

```
CloudWatch
    → Alarms
```

Click

```
Create Alarm
```

Choose Metric

```
EC2
    → Per-Instance Metrics
```

Select

```
CPUUtilization
```

Choose your EC2 instance.

Click

```
Select Metric
```

Configure

| Setting | Value |
|----------|-------|
| Statistic | Average |
| Threshold Type | Static |
| Condition | Greater than |
| Threshold | 70% |

Click

```
Next
```

---

# Step 6: Configure Alarm Notifications

Configure the notification settings.

| Setting | Value |
|----------|-------|
| Alarm State | In Alarm |
| SNS Topic | EC2-Alerts |

Click

```
Next
```

Alarm Name

```
HighCPUAlarm
```

Click

```
Create Alarm
```

### Expected Result

The alarm state initially displays:

```
OK
```

---

# Step 7: Generate CPU Load

Connect to the EC2 instance.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

Update packages.

```bash
sudo apt update
```

Install the stress utility.

```bash
sudo apt install stress -y
```

Generate CPU load.

```bash
stress --cpu 2 --timeout 300
```

### Command Explanation

| Command | Description |
|----------|-------------|
| stress | Generates artificial CPU load |
| --cpu 2 | Uses two CPU workers |
| --timeout 300 | Runs the test for 300 seconds (5 minutes) |

---

# Step 8: Monitor the Alarm

Navigate to

```
CloudWatch
    → Alarms
```

Observe the alarm status.

It changes from

```
OK
```

to

```
In Alarm
```

within a few minutes.

An email notification is sent through Amazon SNS.

### Expected Result

- Alarm state changes to **In Alarm**
- Email notification is received

---

# Step 9: Stop the Stress Test

Wait for the timeout to complete automatically.

or

Press

```
Ctrl + C
```

CPU utilization returns to normal.

CloudWatch automatically changes the alarm state back to

```
OK
```

A second email notification is sent indicating the alarm has returned to the OK state.

---

# Step 10: Review the CloudWatch Dashboard

Navigate to

```
CloudWatch
    → Dashboards
```

Open

```
EC2-Monitoring
```

Verify the dashboard displays:

- CPU Utilization graph
- CPU spike during the stress test
- CPU utilization returning to normal after the test

---

# AWS Console Navigation

## View EC2 Metrics

```
CloudWatch
    → Metrics
        → EC2
            → Per-Instance Metrics
```

---

## Create Dashboard

```
CloudWatch
    → Dashboards
        → Create Dashboard
```

---

## Create SNS Topic

```
Amazon SNS
    → Topics
        → Create Topic
```

---

## Create Subscription

```
Amazon SNS
    → Topics
        → EC2-Alerts
            → Create Subscription
```

---

## Create Alarm

```
CloudWatch
    → Alarms
        → Create Alarm
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

## Stop Stress Test

```bash
Ctrl + C
```

---

# Verification Checklist

- CloudWatch metrics verified successfully.
- CloudWatch Dashboard created.
- CPU Utilization widget added.
- Amazon SNS topic created.
- Email subscription confirmed.
- CloudWatch Alarm created.
- SNS notifications configured.
- CPU load generated successfully.
- Alarm changed from **OK** to **In Alarm**.
- Email notification received.
- Alarm returned to **OK** after CPU utilization decreased.
- Dashboard displayed the CPU utilization spike.

---

# Skills Gained

- Amazon CloudWatch
- CloudWatch Metrics
- CloudWatch Dashboards
- CloudWatch Alarms
- Amazon SNS
- Email Notifications
- EC2 Monitoring
- Performance Monitoring
- Resource Utilization Analysis
- AWS Observability

---

# Conclusion

Successfully configured Amazon CloudWatch to monitor an EC2 instance by verifying performance metrics, creating a centralized monitoring dashboard, and setting up CloudWatch Alarms integrated with Amazon SNS for email notifications. Simulated high CPU utilization using the **stress** utility to validate automatic alarm triggering and notification delivery. This project demonstrates how AWS CloudWatch enables proactive monitoring, alerting, and operational visibility for maintaining reliable and highly available cloud infrastructure.
