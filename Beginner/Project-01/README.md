# 🖥️ Project-03: Linux Administration & AWS EC2 + EBS Volume Management

<div align="center">

![AWS](https://img.shields.io/badge/AWS-EC2-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-Administration-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![EBS](https://img.shields.io/badge/AWS-EBS-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Shell](https://img.shields.io/badge/Shell-Scripting-4EAA25?style=for-the-badge&logo=gnu-bash&logoColor=white)

**A hands-on DevOps project covering real-world Linux server administration and AWS EBS volume management on EC2.**

</div>

---

## 📌 Project Overview

This project simulates what a DevOps engineer does when working with a fresh Linux server in the cloud. It covers two critical areas:

1. **Linux Administration** — User/group management, file system operations, permissions, and text manipulation using the CLI
2. **AWS EBS Volume Management** — Creating, attaching, formatting, mounting, and cleaning up EBS volumes on EC2

> 🎯 **Goal:** Build foundational Linux + AWS skills that are required for every DevOps role.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     AWS Cloud (Region)                       │
│                                                             │
│   ┌──────────────────────────────────────────────────┐     │
│   │           Availability Zone (e.g. us-east-1a)    │     │
│   │                                                  │     │
│   │   ┌───────────────────────┐                     │     │
│   │   │   EC2 Instance        │   ←── SSH (Port 22) ──── Local Machine
│   │   │   (Amazon Linux 2023) │                     │     │
│   │   │   t2.micro / t3.micro │                     │     │
│   │   │                       │                     │     │
│   │   │   Root EBS: 8GB (/)   │                     │     │
│   │   │   Extra EBS: 5GB(/data│◄─── Attach/Detach   │     │
│   │   │                       │                     │     │
│   │   │   Users: user1,2,3    │                     │     │
│   │   │   Group: devops       │                     │     │
│   │   └───────────────────────┘                     │     │
│   │                                                  │     │
│   │   ┌───────────────────────┐                     │     │
│   │   │   EBS Volume (5GB)    │                     │     │
│   │   │   ext4 formatted      │                     │     │
│   │   │   Mounted → /data     │                     │     │
│   │   └───────────────────────┘                     │     │
│   └──────────────────────────────────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tools & Technologies

| Tool | Purpose |
|------|---------|
| **AWS EC2** | Cloud Linux server (compute) |
| **AWS EBS** | Elastic Block Store — persistent disk storage |
| **Amazon Linux 2023** | Server operating system |
| **Linux CLI** | User management, file ops, permissions |
| **SSH** | Secure remote access to EC2 |
| **ext4 filesystem** | Format and use EBS volume |

---

## 📋 Prerequisites

- [ ] AWS Account (Free Tier eligible)
- [ ] EC2 Key Pair (`.pem` file) created in AWS Console
- [ ] SSH client (Terminal / WSL on Windows)
- [ ] IAM permissions: EC2 full access

---

## 🚀 Implementation

### Part 1 — Launch EC2 Instance

**Step 1: Launch EC2**
```bash
# In AWS Console:
# Service → EC2 → Launch Instance
# AMI: Amazon Linux 2023
# Instance Type: t2.micro (Free Tier)
# Key Pair: Select or create your .pem key (devopsproject.pem/any name)
# Security Group: Allow SSH (port 22) from your IP
```

**Step 2: SSH into the instance**
```bash
# Give correct permissions to your .pem key
chmod 400 devopsproject.pem

# SSH into the instance
ssh -i "devopsproject.pem" ec2-user@<your-ec2-public-ip>
```

**Step 3: Switch to root**
```bash
sudo su -
```

---

### Part 2 — Linux Administration Tasks

**Step 4: Create users**
```bash
# Create three users
useradd user1
useradd user2
useradd user3

# Set passwords
passwd user1
passwd user2
passwd user3
```

**Step 5: Create a devops group and add users**
```bash
groupadd devops
usermod -aG devops user1
usermod -aG devops user2
usermod -aG devops user3
```

**Step 6: Build the directory structure**
```bash
# Create the required directory tree
mkdir -p /dir1/dir2/dir3
mkdir -p /dir4
mkdir -p /opt/dir7/dir8
mkdir -p /home/user1
```

**Step 7: Create files in specific directories**
```bash
touch /dir1/f1
touch /dir1/dir2/f2
touch /dir1/dir2/dir3/f3
touch /dir4/f4
touch /opt/dir7/dir8/f5
```

**Step 8: Change group ownership to devops**
```bash
chgrp -R devops /dir1 /dir4 /opt/dir7
```

**Step 9: Change file ownership to user1**
```bash
chown user1 /dir1/f1
chown user1 /dir1/dir2/f2
chown user1 /dir1/dir2/dir3/f3
chown user1 /dir4/f4
chown user1 /opt/dir7/dir8/f5
```

**Step 10: Navigate using relative path and create a file**
```bash
cd /dir1/dir2/dir3
touch f4
ls -la
```

**Step 11: Move a file**
```bash
# Move f4 from dir3 to dir1
mv /dir1/dir2/dir3/f4 /dir1/
ls /dir1/
```

**Step 12: Delete a directory recursively (single command)**
```bash
rm -rf /dir4
```

**Step 13: Write text to a file without using an editor**
```bash
echo "Hello, DevOps fellow! Welcome come to DevOpsquad" > /dir1/f1
cat /dir1/f1
```

**Step 14: Replace text in a file without using an editor**
```bash
# Replace "Hello" with "Hi" using sed
sed -i 's/Hello/Hi/g' /dir1/f1
cat /dir1/f1
```

* **Stream editor**: reads text, applies edits (based on rules you give), and outputs the result.
* Commonly used for “find and replace” in files or pipelines.

### 2) `-i`

* Means **in-place edit**: `sed` will **modify the file directly** instead of printing the changed text to the terminal.
* ⚠️ Note: on some systems (like macOS/BSD), `-i` often needs a backup suffix (e.g., `-i ''` or `-i .bak`). On Ubuntu/Linux, `-i` works as shown.

### 3) `'s/Hello/Hi/g'`

This is the **sed script** (the edit rule), inside single quotes.

It’s in the format:

```
s/<pattern>/<replacement>/<flags>
```

Breakdown:

* `s` = **substitute**
* `Hello` = the **pattern** to search for
* `Hi` = the **replacement** text
* `g` = **global flag** → replace **all matches on each line**, not just the first match per line

`sed -i '0,/Hello/s/Hello/Hi/' /dir1/f1` only once / replace the first one

---

### Part 3 — AWS EBS Volume Management

**Step 15: Create a 5GB EBS Volume**
```
AWS Console → EC2 → Elastic Block Store → Volumes → Create Volume
- Size: 5 GiB
- Volume Type: gp3
- Availability Zone: SAME as your EC2 instance (critical!)
- Click Create Volume
```

**Step 16: Attach EBS to EC2**
```
In Volumes → Right-click your new volume → Attach Volume
- Select your EC2 instance
- Device name: /dev/sdb (or as suggested)
- Click Attach
```

**Step 17: Verify the volume is visible on EC2**
```bash
lsblk
# You should see nvme1n1 listed (5GB, not mounted yet)
# A new like nvme1n1       259:4    0   5G  0 disk
```

**Step 18: Format the volume with ext4 filesystem**
```bash
mkfs.ext4 /dev/sdb
```

**Step 19: Create mount point and mount the volume**
```bash
mkdir /data
mount /dev/sdb /data
```

**Step 20: Verify the mount**
```bash
df -h
# /dev/sdb should appear mounted at /data with ~4.6GB available or something like this
```

**Step 21: Create a file inside /data**
```bash
touch /data/f1
echo "Hello DevOpsQuad Fellow, EBS Volume is working!" > /data/f1
cat /data/f1
ls -l /data/
```

**Step 22: Unmount the volume**
```bash
umount /data
df -h
# /dev/sdb should no longer appear
```

**Step 23: Detach EBS from EC2 (AWS Console)**
```
EC2 → Volumes → Select the 5GB volume
Right-click → Detach Volume → Confirm
```

**Step 24: Delete the EBS Volume**
```
EC2 → Volumes → Select the 5GB volume
Right-click → Delete Volume → Confirm
```

**Step 25: Terminate the EC2 Instance**
```
EC2 → Instances → Select your instance
Instance State → Terminate Instance → Confirm
```

---

## ✅ Validation Checklist

| Task | Command to Verify | Expected Output |
|------|------------------|-----------------|
| Users created | `cat /etc/passwd \| grep user` | user1, user2, user3 listed |
| Group created | `cat /etc/group \| grep devops` | devops group with users |
| Directory structure | `ls -R /dir1` | Nested dirs and files |
| File ownership | `ls -la /dir1/f1` | user1 as owner |
| Group ownership | `ls -la /dir1/` | devops as group |
| Text written | `cat /dir1/f1` | "Hi, DevOps World!" |
| EBS mounted | `df -h` | /data with 5GB |
| File in EBS | `ls /data/` | f1 listed |

---

## 🧠 Key Concepts Learned

| Concept | Commands Used |
|---------|--------------|
| User Management | `useradd`, `passwd`, `usermod` |
| Group Management | `groupadd`, `chgrp` |
| File Operations | `mkdir -p`, `touch`, `mv`, `rm -rf` |
| Ownership & Permissions | `chown`, `chgrp`, `ls -la` |
| Text Manipulation (no editor) | `echo`, `sed -i` |
| Disk Management | `lsblk`, `mkfs.ext4`, `mount`, `umount`, `df -h` |
| AWS EBS Lifecycle | Create → Attach → Format → Mount → Unmount → Detach → Delete |

---

## 🔥 Common Issues & Fixes

| Issue | Cause | Fix |
|-------|-------|-----|
| `Permission denied` on SSH | Wrong `.pem` permissions | `chmod 400 your-key.pem` |
| EBS not visible (`lsblk`) | Wrong Availability Zone | Delete & recreate EBS in same AZ as EC2 |
| `mount: wrong fs type` | Volume not formatted | Run `mkfs.ext4 /dev/sdb` first |
| `target is busy` on unmount | A process is using /data | `cd /` then `umount /data` |
| `sed` not replacing text | Wrong syntax | Use `sed -i 's/old/new/g' filename` |

---

## 📁 Project Structure

```
DevOps-Project-03/
├── README.md               # This file
└── screenshots/            # (Add your own screenshots here)
    ├── ec2-launch.png
    ├── ssh-connected.png
    ├── users-created.png
    ├── directory-structure.png
    ├── ebs-attached.png
    ├── ebs-mounted.png
    └── ebs-deleted.png
```

---

## 📸 Screenshots

> 💡 Add your own screenshots from the AWS Console and terminal to make this portfolio-ready!

| Step | Screenshot |
|------|-----------|
| EC2 Instance Running | *(add screenshot)* |
| SSH Connected | *(add screenshot)* |
| Directory Structure Created | *(add screenshot)* |
| EBS Volume Attached | *(add screenshot)* |
| EBS Mounted at /data | *(add screenshot)* |
| df -h Output | *(add screenshot)* |

---

## 💡 What I Built & Learned

Through this project, I gained practical experience in:

- ✅ Launching and connecting to an AWS EC2 instance using SSH
- ✅ Performing real-world Linux system administration tasks
- ✅ Managing users, groups, and file permissions in Linux
- ✅ Manipulating files and directories using only the CLI
- ✅ Writing and replacing file content without using a text editor (`echo`, `sed`)
- ✅ Full lifecycle management of an AWS EBS volume (create → attach → format → mount → unmount → detach → delete)
- ✅ Understanding AWS compute and storage fundamentals

---

## 🔗 Resources

- [AWS EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [AWS EBS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/AmazonEBS.html)
- [Linux `useradd` man page](https://man7.org/linux/man-pages/man8/useradd.8.html)
- [Linux `sed` command guide](https://www.gnu.org/software/sed/manual/sed.html)


---

## 👨‍💻 Author

**Your Name**
- GitHub: [@munirmahmud](https://github.com/munirmahmud)
- LinkedIn: [munirmahmud6](https://linkedin.com/in/munirmahmud6)
- Twitter/X: [@munirmahmud06](https://x.com/munirmahmud06)
- YouTube: [DevOpsQuad](https://www.youtube.com/@devopsquad)

---

## 🗺️ DevOps Learning Roadmap

This is **Project 1 of 45** in my DevOps learning journey.

| Status | Project |
|--------|---------|
| ✅ Current | Project-01: Linux Basics & AWS EC2 |
| 🔜 Next | Project-02: Scalable VPC Architecture on AWS |
| ⏳ Upcoming | Project-03: Dockerize a Web Application |

> Follow my journey on [LinkedIn](https://www.linkedin.com/in/munirmahmud6) and [Twitter/X](https://x.com/munirmahmud06) as I build real-world DevOps projects!

---

<div align="center">

⭐ **If this helped you, give it a star!** ⭐

*Part of the [DevOps-Projects](https://github.com/munirmahmud/DevOps-Projects/) learning series*

</div>
