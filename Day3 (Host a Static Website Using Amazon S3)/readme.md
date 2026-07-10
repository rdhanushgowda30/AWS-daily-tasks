
# Task 3: Host a Static Website Using Amazon S3

## Project Overview

This project demonstrates how to host a static website using Amazon Simple Storage Service (Amazon S3). It includes creating an S3 bucket, uploading website files, enabling static website hosting, configuring public access using a bucket policy, enabling object versioning, and implementing lifecycle management for efficient storage administration.

---

# Objective

- Create an Amazon S3 bucket.
- Upload static website files.
- Enable Static Website Hosting.
- Configure public read access using a bucket policy.
- Access the website using the S3 Website Endpoint.
- Enable object versioning.
- Configure lifecycle management for stored objects.

---

# AWS Services Used

- Amazon S3
- S3 Bucket Policies
- Static Website Hosting
- Object Versioning
- Lifecycle Rules

---

# Architecture

```
                  Internet
                      │
                      ▼
              S3 Website Endpoint
                      │
                      ▼
               Amazon S3 Bucket
                      │
      ┌───────────────┼────────────────┐
      │               │                │
      ▼               ▼                ▼
 index.html      style.css        image/logo
                      │
                      ▼
             Bucket Versioning
                      │
                      ▼
             Lifecycle Management
```

---

# Prerequisites

- AWS Account
- AWS Management Console Access
- Basic HTML Knowledge
- Static Website Files (HTML, CSS, Images)

---

# Step 1: Create an Amazon S3 Bucket

1. Login to the AWS Management Console.

2. Search for **Amazon S3**.

3. Click **Create Bucket**.

Configure the bucket.

| Setting | Value |
|----------|-------|
| Bucket Name | Your globally unique bucket name |
| AWS Region | Desired Region (e.g., us-east-1) |
| Object Ownership | ACLs Disabled (Recommended) |
| Block Public Access | Uncheck "Block all public access" |

Acknowledge the warning regarding public access.

Click **Create Bucket**.

---

# Step 2: Prepare Website Files

Create an `index.html` file.

Example:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Amazon S3 Static Website</title>
</head>
<body>
    <h1>Welcome to My Amazon S3 Static Website</h1>
    <h2>Hosted using Amazon S3</h2>
    <p>This website is hosted without using any web server.</p>
</body>
</html>
```

Verify that the webpage opens correctly in your local browser.

---

# Step 3: Upload Website Files

Open the created S3 bucket.

Click

```
Upload
```

Select

```
index.html
```

Click

```
Upload
```

Verify that the upload is completed successfully.

---

# Step 4: Enable Static Website Hosting

Navigate to

```
Bucket
    → Properties
```

Scroll down to

```
Static Website Hosting
```

Click

```
Edit
```

Enable

```
Static Website Hosting
```

Configure

| Setting | Value |
|----------|-------|
| Hosting Type | Host a Static Website |
| Index Document | index.html |

Click

```
Save Changes
```

---

# Step 5: Configure Public Access

Navigate to

```
Permissions
```

Open

```
Bucket Policy
```

Paste the following policy.

> Replace **your-bucket-name** with your actual S3 bucket name.

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"PublicReadGetObject",
      "Effect":"Allow",
      "Principal":"*",
      "Action":"s3:GetObject",
      "Resource":"arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

Click

```
Save Changes
```

---

# Step 6: Verify Public Access

Open the uploaded object.

Copy the

```
Object URL
```

or

Copy the

```
Static Website Endpoint
```

Open it in a web browser.

Example

```
http://your-bucket-name.s3-website-us-east-1.amazonaws.com
```

Expected Output

```
Welcome to My Amazon S3 Static Website

Hosted using Amazon S3

This website is hosted without using any web server.
```

---

# Step 7: Enable Bucket Versioning

Navigate to

```
Bucket
    → Properties
```

Scroll to

```
Bucket Versioning
```

Click

```
Edit
```

Select

```
Enable
```

Click

```
Save Changes
```

---

# Step 8: Upload a New Version

Modify the contents of `index.html`.

Example

```html
<h1>Amazon S3 Version 2 Website</h1>
```

Upload the updated file to the same bucket.

Choose

```
Replace existing object
```

Upload the file.

Navigate to

```
Bucket
    → Show Versions
```

Verify that multiple versions of the object are available.

---

# Step 9: Configure a Lifecycle Rule

Navigate to

```
Bucket
    → Management
```

Click

```
Create Lifecycle Rule
```

Configure

| Setting | Value |
|----------|-------|
| Rule Name | Delete-Old-Versions |
| Scope | Apply to All Objects |
| Lifecycle Action | Expire Current Versions or Transition Objects |

Example Configuration

```
Expire Current Objects after 30 Days
```

or

```
Move Objects to Standard-IA after 30 Days
```

Click

```
Create Rule
```

---

# Verification Checklist

- Amazon S3 bucket created successfully.
- Website files uploaded.
- Static Website Hosting enabled.
- Bucket policy configured correctly.
- Website accessible through the S3 Website Endpoint.
- Bucket versioning enabled.
- Multiple object versions available.
- Lifecycle rule created successfully.

---

# AWS Console Navigation

## Create Bucket

```
Amazon S3
    → Create Bucket
```

---

## Upload Files

```
Bucket
    → Upload
```

---

## Enable Static Website Hosting

```
Bucket
    → Properties
        → Static Website Hosting
```

---

## Configure Bucket Policy

```
Bucket
    → Permissions
        → Bucket Policy
```

---

## Enable Versioning

```
Bucket
    → Properties
        → Bucket Versioning
```

---

## Configure Lifecycle Rule

```
Bucket
    → Management
        → Lifecycle Rules
```

---

# Website Files Used

## index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Amazon S3 Static Website</title>
</head>
<body>
    <h1>Welcome to My Amazon S3 Static Website</h1>
    <h2>Hosted using Amazon S3</h2>
    <p>This website is hosted without using any web server.</p>
</body>
</html>
```

---

# Bucket Policy Used

```json
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Sid":"PublicReadGetObject",
      "Effect":"Allow",
      "Principal":"*",
      "Action":"s3:GetObject",
      "Resource":"arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

---

# Skills Gained

- Amazon S3
- Static Website Hosting
- Bucket Policies
- Public Access Configuration
- Object Storage
- Website Deployment
- Bucket Versioning
- Lifecycle Management
- AWS Storage Services

---

# Conclusion

Successfully hosted a static website using Amazon S3 by creating a bucket, uploading website files, enabling Static Website Hosting, configuring public access through a bucket policy, and verifying website accessibility using the S3 Website Endpoint. Additionally, enabled bucket versioning to maintain multiple object versions and configured lifecycle rules to automate object transition or expiration, demonstrating effective storage management and cost optimization using Amazon S3.
