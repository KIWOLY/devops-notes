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
