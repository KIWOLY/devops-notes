# AWS CLI

---

## What is AWS CLI?

The **AWS CLI** (Command Line Interface) is a tool that lets you control AWS services directly from your terminal instead of clicking around the AWS web console.

---

## AWS Console vs AWS CLI

| Feature | AWS Console (Browser) | AWS CLI (Terminal) |
|---------|----------------------|-------------------|
| Best for | Beginners, visual exploration | Automation, real work |
| Speed | Slow, lots of clicking | Fast, one command |
| Automation | ❌ Cannot automate | ✅ Script everything |
| Repeatability | Manual every time | Run same command 1000x |
| In production | Risky (human error) | Safer, consistent |
| Learning curve | Easy to start | Takes practice |

> **For DevOps — CLI wins, always.**

---

## Why CLI Wins for DevOps

- **Scripts & automation** — put CLI commands in bash scripts, CI/CD pipelines, cron jobs
- **Infrastructure as Code** — Terraform and Ansible call AWS under the hood the same way
- **Speed** — deploy, query, and manage resources in seconds
- **No clicking mistakes in production**

---

## Getting Started with AWS CLI

### 1. Install It

```bash
# Linux/Mac
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip && sudo ./aws/install

# Verify installation
aws --version
```

### 2. Configure It (Connect to Your AWS Account)

```bash
aws configure
```

It will ask for:

| Field | Description |
|-------|-------------|
| AWS Access Key ID | From your AWS security credentials |
| AWS Secret Access Key | From your AWS security credentials |
| Default region | e.g., `us-east-1` |
| Output format | `json` recommended |

> You can find your **Access Key ID** and **Secret Access Key** under **Security Credentials** in your AWS profile.

---

## Connecting to an EC2 instance

Prerequisites:
- Instance is `running`.
- You have the instance ID or public DNS/IP.
- Either: a key pair (.pem) for SSH access, or the instance has the SSM Agent + an IAM role that allows SSM.

There are three common ways to connect:

1) AWS Console — EC2 Instance Connect (browser SSH)

- In the AWS Console go to **EC2 > Instances**, select the instance.
- Click **Connect** then choose **EC2 Instance Connect (browser-based SSH)**.
- This requires the instance OS to support Instance Connect and the security group to allow SSH (port 22). It works for many Amazon Linux and Ubuntu AMIs.

2) AWS Console — Session Manager (SSM)

- In the AWS Console go to **EC2 > Instances**, select the instance.
- Click **Connect** then choose **Session Manager**.
- Session Manager works without a public IP if the instance has the SSM Agent and an instance role with `AmazonSSMManagedInstanceCore` (or the needed SSM permissions).

3) AWS CLI — SSH using key pair

- Retrieve the public IP or DNS from the console or with the CLI:

```bash
# get public IP
aws ec2 describe-instances --instance-ids i-0123456789abcdef0 \
	--query "Reservations[0].Instances[0].PublicIpAddress" --output text
```

- SSH (correct user depends on AMI):

```bash
# Amazon Linux / Amazon Linux 2
ssh -i ~/keys/my-key.pem ec2-user@PUBLIC_IP_OR_DNS

# Ubuntu
ssh -i ~/keys/my-key.pem ubuntu@PUBLIC_IP_OR_DNS
```

Note: make sure your private key file has restrictive permissions before using it:

```bash
chmod 400 ~/keys/my-key.pem
```
If permissions are too open, SSH will refuse to use the key.

4) AWS CLI — Session Manager (SSM) start-session

- Install the Session Manager plugin (if required) and ensure your CLI is configured.

```bash
# Start an interactive session to an instance
aws ssm start-session --target i-0123456789abcdef0
```

- Notes: the instance needs SSM Agent and an IAM role with SSM permissions. Useful for instances without public IPs.

5) AWS CLI — EC2 Instance Connect (temporary SSH key)

- You can push a temporary SSH public key to the instance using EC2 Instance Connect, then SSH normally. Example:

```bash
# send public key (replace zone, instance-id, and user)
aws ec2-instance-connect send-ssh-public-key \
	--instance-id i-0123456789abcdef0 \
	--availability-zone us-east-1a \
	--instance-os-user ec2-user \
	--ssh-public-key file://~/.ssh/id_rsa.pub

# then SSH to the instance public IP
ssh ec2-user@PUBLIC_IP
```

---

Quick troubleshooting tips:
- If SSH times out, check the instance security group allows port 22 and the instance has a public IP.
- If `aws ssm start-session` fails, ensure the instance IAM role has `AmazonSSMManagedInstanceCore` and the SSM Agent is running.
- For browser-based Instance Connect failures, confirm the AMI supports Instance Connect and the instance's OS user (ec2-user/ubuntu) matches the selected option.



