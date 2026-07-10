# Task 2: Manage Amazon EBS Volumes and Snapshots

## Project Overview

This project demonstrates how to create, attach, configure, and manage Amazon Elastic Block Store (EBS) volumes with an Amazon EC2 instance. It also covers formatting disks, mounting storage, configuring persistent mounting using the `/etc/fstab` file, creating EBS snapshots, and restoring data from snapshots for backup and disaster recovery.

---

# Objective

- Create two Amazon EBS volumes.
- Attach both volumes to an EC2 instance.
- Format and mount the volumes.
- Store sample data.
- Configure automatic mounting after reboot.
- Create snapshots for backup.
- Restore an EBS volume from a snapshot.
- Verify that the restored data is intact.

---

# AWS Services Used

- Amazon EC2
- Amazon Elastic Block Store (EBS)
- Amazon EBS Snapshots
- Security Groups
- Ubuntu Linux

---

# Architecture

```
                    Amazon EC2
                        │
        ┌───────────────┴───────────────┐
        │                               │
        ▼                               ▼
     EBS Volume 1                  EBS Volume 2
        │                               │
     Mounted at                    Mounted at
     /data1                         /data2
        │                               │
        └───────────────┬───────────────┘
                        │
                 Store Sample Data
                        │
                        ▼
              Create EBS Snapshots
                        │
                        ▼
          Restore Volume from Snapshot
                        │
                        ▼
            Verify Restored Sample Data
```

---

# Prerequisites

- AWS Account
- Ubuntu EC2 Instance
- SSH Access
- Existing Key Pair
- Two available EBS volumes in the same Availability Zone as the EC2 instance

---

# Step 1: Create Two Amazon EBS Volumes

1. Login to the AWS Management Console.

2. Navigate to:

```
EC2 → Elastic Block Store → Volumes
```

3. Click **Create Volume**.

Configure the first volume.

| Setting | Value |
|----------|-------|
| Volume Type | gp3 (or gp2) |
| Size | 5 GiB (Example) |
| Availability Zone | Same as EC2 Instance |

Click **Create Volume**.

Repeat the same process to create the second volume.

---

# Step 2: Attach the EBS Volumes

Select the first volume.

Click

```
Actions
    → Attach Volume
```

Choose your EC2 instance.

Example Device Name:

```
/dev/sdf
```

Repeat for the second volume.

Example Device Name:

```
/dev/sdg
```

Wait until both volumes show the status:

```
In-use
```

---

# Step 3: Verify the Attached Volumes

Connect to the EC2 instance.

```bash
ssh -i mykey.pem ubuntu@<Public-IP>
```

List block devices.

```bash
lsblk
```

Example Output

```
xvda
xvdf
xvdg
```

You can also verify using:

```bash
sudo fdisk -l
```

---

# Step 4: Format the EBS Volumes

Format the first volume.

```bash
sudo mkfs.ext4 /dev/xvdf
```

Format the second volume.

```bash
sudo mkfs.ext4 /dev/xvdg
```

---

# Step 5: Create Mount Directories

Create directories.

```bash
sudo mkdir /data1
```

```bash
sudo mkdir /data2
```

---

# Step 6: Mount the Volumes

Mount the first volume.

```bash
sudo mount /dev/xvdf /data1
```

Mount the second volume.

```bash
sudo mount /dev/xvdg /data2
```

Verify the mounted volumes.

```bash
df -h
```

Expected Output

```
/dev/xvdf   mounted on /data1
/dev/xvdg   mounted on /data2
```

---

# Step 7: Store Sample Data

Create sample files.

```bash
echo "This is Volume 1" | sudo tee /data1/file1.txt
```

```bash
echo "This is Volume 2" | sudo tee /data2/file2.txt
```

Verify the files.

```bash
ls /data1
```

```bash
ls /data2
```

View the contents.

```bash
cat /data1/file1.txt
```

```bash
cat /data2/file2.txt
```

---

# Step 8: Configure Persistent Mounting

Retrieve the UUID of each volume.

```bash
sudo blkid
```

Example Output

```
UUID="xxxxxxxx-xxxx"
/dev/xvdf

UUID="yyyyyyyy-yyyy"
/dev/xvdg
```

Edit the fstab file.

```bash
sudo nano /etc/fstab
```

Add the following entries.

```
UUID=xxxxxxxx-xxxx  /data1  ext4  defaults,nofail  0  2
UUID=yyyyyyyy-yyyy  /data2  ext4  defaults,nofail  0  2
```

Save the file.

Press

```
CTRL + O
Enter
CTRL + X
```

Test the configuration.

```bash
sudo mount -a
```

If there are no errors, the configuration is successful.

Verify again.

```bash
df -h
```

---

# Step 9: Create EBS Snapshots

Navigate to

```
EC2 → Volumes
```

Select the first volume.

Click

```
Actions
    → Create Snapshot
```

Provide a description.

Example

```
Backup of Volume 1
```

Click

```
Create Snapshot
```

Repeat the same process for the second volume.

Wait until both snapshots reach the status:

```
Completed
```

---

# Step 10: Restore an EBS Volume from Snapshot

Navigate to

```
EC2 → Snapshots
```

Select the snapshot.

Click

```
Actions
    → Create Volume from Snapshot
```

Configure

| Setting | Value |
|----------|-------|
| Volume Type | gp3 |
| Availability Zone | Same as EC2 Instance |

Click

```
Create Volume
```

---

# Step 11: Attach the Restored Volume

Select the restored volume.

Click

```
Actions
    → Attach Volume
```

Attach it to the EC2 instance.

Example Device Name

```
/dev/sdh
```

---

# Step 12: Mount the Restored Volume

Verify the device.

```bash
lsblk
```

Create a mount directory.

```bash
sudo mkdir /restore
```

Mount the restored volume.

```bash
sudo mount /dev/xvdh /restore
```

Verify the restored data.

```bash
ls /restore
```

Display the contents.

```bash
cat /restore/file1.txt
```

The original data should be present.

---

# Linux Commands Used

## Verify Disks

```bash
lsblk
```

```bash
sudo fdisk -l
```

---

## Format Volumes

```bash
sudo mkfs.ext4 /dev/xvdf
```

```bash
sudo mkfs.ext4 /dev/xvdg
```

---

## Create Mount Directories

```bash
sudo mkdir /data1
```

```bash
sudo mkdir /data2
```

---

## Mount Volumes

```bash
sudo mount /dev/xvdf /data1
```

```bash
sudo mount /dev/xvdg /data2
```

---

## Verify Mounted Storage

```bash
df -h
```

---

## Create Sample Files

```bash
echo "This is Volume 1" | sudo tee /data1/file1.txt
```

```bash
echo "This is Volume 2" | sudo tee /data2/file2.txt
```

---

## Verify Files

```bash
cat /data1/file1.txt
```

```bash
cat /data2/file2.txt
```

---

## Retrieve UUID

```bash
sudo blkid
```

---

## Edit fstab

```bash
sudo nano /etc/fstab
```

---

## Test fstab

```bash
sudo mount -a
```

---

# Verification Checklist

- Two EBS volumes created successfully.
- Both volumes attached to the EC2 instance.
- Linux detected both disks.
- Both volumes formatted with the ext4 filesystem.
- Mount points created successfully.
- Volumes mounted successfully.
- Sample files stored on each volume.
- Persistent mounting configured using `/etc/fstab`.
- EBS snapshots created successfully.
- New EBS volume restored from the snapshot.
- Restored volume attached and mounted.
- Original data verified successfully.

---

# Skills Gained

- Amazon Elastic Block Store (EBS)
- Block Storage Management
- Linux File Systems
- Disk Formatting
- Mounting Storage
- Persistent Storage Configuration
- `/etc/fstab`
- EBS Snapshots
- Backup and Recovery
- AWS Storage Administration

---

# Conclusion

Successfully created and managed Amazon EBS volumes by attaching them to an EC2 instance, formatting and mounting them with the ext4 filesystem, storing sample data, and configuring persistent mounting using `/etc/fstab`. Performed backup operations by creating EBS snapshots and restored a new volume from the snapshot, verifying that the original data was preserved. This project demonstrates essential AWS storage management and disaster recovery techniques.
