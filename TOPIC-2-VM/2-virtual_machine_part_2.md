# Virtual Machines — Part 2

---

## Creating a VM on AWS

When you create a VM (EC2) on the AWS Console:

1. You send a request to create the VM
2. AWS uses its **hypervisor** to provision it
3. AWS returns it to you with a **public IP address**

> Every VM creation request goes through the **EC2 API**.

---

## Scaling: What If You Need 100 VMs?

Doing it manually 100 times is not realistic. This is where **DevOps automation** comes in.

A DevOps engineer writes a **script** that calls the AWS EC2 API and requests 100 instances at once. AWS responds with 100 EC2 instances — provided the request is valid and the user is **authenticated and authorized**.

### Automation Tools for AWS

| Tool | Description |
|------|-------------|
| **AWS CLI** | Command-line interface to control AWS from terminal |
| **AWS API** | Direct API calls from code |
| **AWS CFT** (CloudFormation) | Infrastructure as code using AWS templates |
| **Terraform** | Infrastructure as code — works across multiple cloud platforms |

---

## How to Create an AWS EC2 Instance

1. Go to [AWS Management Console](https://aws.amazon.com/console/) and sign in
2. Open **Amazon EC2**
3. Click **Launch Instance**
4. Enter a name for your instance (e.g., `MyServer`)
5. Choose an operating system image (AMI):
   - Ubuntu Server
   - Amazon Linux
6. Select an instance type (e.g., `t2.micro` or `t3.micro` — eligible for AWS Free Tier)
7. Create or select a **key pair** (`.pem` file) for SSH access and download it safely
8. Configure network settings:
   - Allow **SSH** (port 22) for Linux
   - Allow **HTTP** (port 80) and **HTTPS** (port 443) if hosting a website
9. Configure storage (default size is usually sufficient for testing)
10. Click **Launch Instance**

---

## Connecting to an AWS EC2 Instance via SSH

```bash
# Step 1 — Go to the folder with your .pem key file
cd ~/Downloads/aws_key

# Step 2 — Set correct permissions on the key file
chmod 400 key_aws_air.pem

# Step 3 — Connect using the instance's public IP
ssh -i key_aws_air.pem ubuntu@PUBLIC_IP

# Example
ssh -i key_aws_air.pem ubuntu@54.123.45.67
```

When prompted the first time:

```
Are you sure you want to continue connecting (yes/no)?
```

Type `yes`.

---

## Understanding `chmod 400`

`chmod 400` sets file permissions so that **only you (the owner) can read it** — nothing else.

### How chmod Numbers Work

`chmod` takes a 3-digit number. Each digit represents a group:

| Digit | Who |
|-------|-----|
| First `4` | Owner (you) |
| Second `0` | Group |
| Third `0` | Others |

Each digit is a sum of:

| Value | Permission |
|-------|-----------|
| `4` | Read |
| `2` | Write |
| `1` | Execute |
| `0` | No permission |

So `chmod 400` = **owner can read only**, group and others have zero access.
