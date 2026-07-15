 # Task 12: Build a Serverless Application Using AWS Lambda

## Project Overview

This project demonstrates how to build a simple **serverless application** using **AWS Lambda** and **Amazon S3**. Whenever a file is uploaded to an S3 bucket, an **S3 Object Created Event** automatically triggers an AWS Lambda function. The function processes the uploaded file information and writes execution logs to **Amazon CloudWatch Logs**, allowing you to monitor function execution and performance without managing any servers.

---

# Objective

- Create an Amazon S3 bucket for file uploads.
- Create an IAM Role for Lambda execution.
- Create an AWS Lambda function using Python.
- Configure S3 as an event source for Lambda.
- Automatically trigger Lambda whenever a file is uploaded.
- Verify execution using Amazon CloudWatch Logs.
- Modify the function to process additional file metadata.
- Monitor Lambda performance using CloudWatch metrics.

---

# AWS Services Used

- AWS Lambda
- Amazon S3
- AWS IAM
- Amazon CloudWatch
- CloudWatch Logs

---

# Architecture

```text
             Upload File
                  │
                  ▼
          Amazon S3 Bucket
                  │
       (Object Created Event)
                  │
                  ▼
        AWS Lambda Function
                  │
                  ▼
         Amazon CloudWatch Logs
                  │
                  ▼
      Execution Logs & Metrics
```

---

# Prerequisites

- AWS Account
- IAM permissions to create Lambda, S3, IAM Roles, and CloudWatch Logs
- Basic understanding of Python (optional)

---

# Step 1: Create an S3 Bucket

## Goal

Create an S3 bucket that stores uploaded files and triggers the Lambda function.

### Steps

Navigate to:

```text
AWS Management Console
    → Amazon S3
```

Click:

```text
Create Bucket
```

Configure the bucket:

| Setting | Value |
|----------|-------|
| Bucket Name | my-lambda-upload-demo-12345 (must be globally unique) |
| AWS Region | Your preferred region |

Leave the remaining settings as default.

Click:

```text
Create Bucket
```

### Verify

The bucket should appear in the S3 bucket list.

---

# Step 2: Create an IAM Role for Lambda

## Goal

Provide the Lambda function permission to write execution logs to CloudWatch.

### Steps

Navigate to:

```text
IAM
    → Roles
```

Click:

```text
Create Role
```

Choose:

```text
Trusted Entity

AWS Service

Lambda
```

Click:

```text
Next
```

Attach the AWS managed policy:

```text
AWSLambdaBasicExecutionRole
```

Click:

```text
Next
```

Role Name:

```text
Lambda-S3-ExecutionRole
```

Click:

```text
Create Role
```

### Expected Result

A new IAM role is successfully created.

---

# Step 3: Create a Lambda Function

## Goal

Create a Python Lambda function that executes whenever a file is uploaded to S3.

Navigate to:

```text
AWS Lambda
```

Click:

```text
Create Function
```

Choose:

```text
Author from Scratch
```

Configure:

| Setting | Value |
|----------|-------|
| Function Name | S3FileProcessor |
| Runtime | Python 3.12 (or latest available) |
| Architecture | x86_64 |

Under **Permissions**:

Choose:

```text
Use an Existing Role
```

Select:

```text
Lambda-S3-ExecutionRole
```

Click:

```text
Create Function
```

---

# Step 4: Develop the Lambda Function

Replace the default code with:

```python
import json

def lambda_handler(event, context):

    print("New file uploaded!")

    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        file = record['s3']['object']['key']

        print("Bucket:", bucket)
        print("File:", file)

    return {
        'statusCode': 200,
        'body': json.dumps("File processed successfully")
    }
```

Click:

```text
Deploy
```

### Expected Result

```text
Successfully updated the function.
```

---

# Understanding the Code

The function performs the following tasks:

- Receives the S3 event.
- Reads the bucket name.
- Reads the uploaded file name.
- Prints the information to CloudWatch Logs.
- Returns a successful HTTP response.

---

# Step 5: Configure an S3 Trigger

## Goal

Automatically invoke the Lambda function whenever a new object is uploaded.

Open:

```text
Lambda
    → S3FileProcessor
```

Click:

```text
Add Trigger
```

Configure:

| Setting | Value |
|----------|-------|
| Trigger | S3 |
| Bucket | Select your bucket |
| Event Type | All Object Create Events |

Enable:

```text
Acknowledge Recursive Invocation Warning
```

Click:

```text
Add
```

### Expected Result

The S3 trigger is successfully attached to the Lambda function.

---

# Step 6: Test the Function

## Goal

Verify that Lambda executes automatically after a file upload.

Navigate to:

```text
Amazon S3
```

Open your bucket.

Click:

```text
Upload
```

Upload any file.

Example:

```text
test.txt
```

Wait a few seconds.

Navigate to:

```text
Lambda
    → Monitor
```

Verify that the **Invocations** metric has increased.

---

# Step 7: Review CloudWatch Logs

## Goal

Confirm that the Lambda function executed successfully.

Navigate to:

```text
CloudWatch
    → Logs
    → Log Groups
```

Open:

```text
/aws/lambda/S3FileProcessor
```

Open the latest Log Stream.

You should see logs similar to:

```text
START RequestId: ...

New file uploaded!

Bucket: my-lambda-upload-demo-12345

File: test.txt

END RequestId: ...

REPORT RequestId: ...
```

### Expected Result

CloudWatch confirms successful Lambda execution.

---

# Step 8: Modify the Lambda Function

Update the code to include the uploaded file size.

Replace the existing code with:

```python
import json

def lambda_handler(event, context):

    for record in event['Records']:

        bucket = record['s3']['bucket']['name']
        file = record['s3']['object']['key']
        size = record['s3']['object']['size']

        print("Bucket:", bucket)
        print("File:", file)
        print("Size:", size)

    return {
        'statusCode': 200
    }
```

Click:

```text
Deploy
```

Upload another file to the S3 bucket.

### Verify

Open the latest CloudWatch Log Stream.

You should now see:

```text
Bucket: my-lambda-upload-demo-12345

File: image.png

Size: 20480
```

---

# Step 9: Monitor Lambda Performance

## Goal

Monitor the Lambda function using CloudWatch metrics.

Navigate to:

```text
Lambda
    → S3FileProcessor
        → Monitor
```

Review the following metrics:

| Metric | Description |
|----------|-------------|
| Invocations | Number of times Lambda executed |
| Duration | Time taken to execute |
| Errors | Number of failed executions |
| Throttles | Number of rejected requests |

### Verify

- Invocations increase after every upload.
- Duration remains low.
- Errors remain **0**.
- Throttles remain **0**.

---

# AWS Console Navigation

## Create S3 Bucket

```text
Amazon S3
    → Create Bucket
```

---

## Create IAM Role

```text
IAM
    → Roles
        → Create Role
```

---

## Create Lambda Function

```text
AWS Lambda
    → Create Function
```

---

## Configure S3 Trigger

```text
Lambda
    → Function
        → Add Trigger
```

---

## View CloudWatch Logs

```text
CloudWatch
    → Logs
        → Log Groups
            → /aws/lambda/S3FileProcessor
```

---

## Monitor Lambda

```text
Lambda
    → Monitor
```

---

# Python Code Used

## Initial Lambda Function

```python
import json

def lambda_handler(event, context):

    print("New file uploaded!")

    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        file = record['s3']['object']['key']

        print("Bucket:", bucket)
        print("File:", file)

    return {
        'statusCode': 200,
        'body': json.dumps("File processed successfully")
    }
```

---

## Updated Lambda Function

```python
import json

def lambda_handler(event, context):

    for record in event['Records']:

        bucket = record['s3']['bucket']['name']
        file = record['s3']['object']['key']
        size = record['s3']['object']['size']

        print("Bucket:", bucket)
        print("File:", file)
        print("Size:", size)

    return {
        'statusCode': 200
    }
```

---

# Verification Checklist

- S3 Bucket created successfully.
- IAM Role created with Lambda execution permissions.
- Lambda Function created successfully.
- Python code deployed successfully.
- S3 Trigger configured.
- Lambda invoked automatically after file upload.
- CloudWatch Logs generated successfully.
- File details displayed in logs.
- Function updated to include file size.
- CloudWatch metrics monitored.
- Invocations increased after each upload.
- Errors remained **0**.
- Throttles remained **0**.

---

# Skills Gained

- AWS Lambda
- Amazon S3 Event Notifications
- IAM Roles
- Python for Serverless Computing
- CloudWatch Logs
- CloudWatch Metrics
- Event-Driven Architecture
- Serverless Computing
- AWS Monitoring

---

# Expected Outcome

By completing this project, you will be able to:

- Build a serverless application using AWS Lambda.
- Automatically trigger Lambda functions from S3 events.
- Process uploaded file information without managing servers.
- Monitor Lambda execution using CloudWatch Logs.
- Analyze Lambda performance through CloudWatch metrics.
- Understand event-driven application architecture in AWS.

---

# Conclusion

Successfully built a **serverless application** using **AWS Lambda** and **Amazon S3**, where file uploads automatically triggered a Lambda function for processing. Configured an IAM execution role, deployed Python code, integrated S3 event notifications, verified execution through CloudWatch Logs, enhanced the function to display uploaded file size, and monitored performance using CloudWatch metrics. This project demonstrates the fundamentals of **event-driven serverless computing** and highlights how AWS Lambda enables scalable, automated, and infrastructure-free application development.

