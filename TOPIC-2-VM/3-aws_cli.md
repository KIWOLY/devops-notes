# AWS CLI — Reference Notes

## 1. What It Is

The **AWS CLI** is a command-line tool that sends API requests to AWS services — everything you can click in the Console, you can run as a command.

> Think of the Console as the steering wheel and the CLI as the engine control unit — the Console is for looking around, the CLI is for actually driving automation.

## 2. Console vs CLI

| Feature        | Console (Browser)         | CLI (Terminal)          |
|-----------------|---------------------------|--------------------------|
| Best for        | Beginners, exploring       | Automation, real work    |
| Speed           | Slow, lots of clicking     | Fast, one command        |
| Automation      | ❌ Cannot automate         | ✅ Script everything     |
| Repeatability   | Manual every time          | Run the same command 1000x |
| In production   | Risky (human error)        | Safer, consistent        |

**For DevOps, the CLI wins** — it's what scripts, CI/CD pipelines, and tools like Terraform and Ansible call under the hood.

## 3. Install & Verify

```bash
# Linux/Mac (CLI v2)
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip && sudo ./aws/install

aws --version
```

## 4. Configure Credentials

```bash
aws configure --profile myprofile
```

You'll be asked for:

| Field                 | Where to find it                          |
|------------------------|--------------------------------------------|
| AWS Access Key ID      | IAM → Security credentials                 |
| AWS Secret Access Key  | Shown once at key creation — save it       |
| Default region         | e.g. `us-east-1`                           |
| Output format           | `json` (recommended)                       |

Verify who you are logged in as:

```bash
aws sts get-caller-identity --profile myprofile
```

Run this any time something feels off — it's the fastest way to confirm your credentials and account are what you expect.

## 5. Command Structure

Every command follows the same shape:

```
aws <service> <action> [--options]
```

- **service** → which AWS product (`s3`, `ec2`, `iam`, `lambda`...)
- **action** → what you want to do (`list-buckets`, `describe-instances`...)
- **options** → flags to filter or specify things

Get help without leaving the terminal:

```bash
aws ec2 help                        # all ec2 actions
aws ec2 describe-instances help     # options for one action
```

## 6. Profiles & Environment Variables

- Named profiles: `--profile name`, or `export AWS_PROFILE=name` for the session.
- Env vars **override** profile values: `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_SESSION_TOKEN`, `AWS_DEFAULT_REGION`.
- Lock down the credentials files:

```bash
chmod 600 ~/.aws/credentials ~/.aws/config
```

Named profiles matter once you're juggling more than one AWS account — they stop you from accidentally running a command against the wrong one.

## 7. Everyday Commands

```bash
# List S3 buckets
aws s3 ls

# Copy a file to S3
aws s3 cp local-file.txt s3://my-bucket/path/

# List EC2 instances, as a table
aws ec2 describe-instances --output table

# List IAM users
aws iam list-users

# Check current CLI config
aws configure list
```

### Filtering output with `--query`

Output is JSON by default and gets noisy fast. `--query` uses JMESPath to pull just what you need:

```bash
aws ec2 describe-instances \
  --query "Reservations[].Instances[].[InstanceId,State.Name]" \
  --output table
```

### First practice exercise (S3)

```bash
aws s3 mb s3://your-unique-bucket-name-12345
aws s3 cp test.txt s3://your-unique-bucket-name-12345/
aws s3 ls s3://your-unique-bucket-name-12345/
aws s3 rb s3://your-unique-bucket-name-12345 --force
```

## 8. Connecting to EC2

Prerequisites: instance is `running`, and you have either a key pair (`.pem`) for SSH, or the instance has the SSM Agent + an IAM role that allows SSM.

### Option A — SSH with a key pair

```bash
chmod 400 ~/keys/my-key.pem       # SSH refuses loose permissions

# Amazon Linux
ssh -i ~/keys/my-key.pem ec2-user@PUBLIC_IP

# Ubuntu
ssh -i ~/keys/my-key.pem ubuntu@PUBLIC_IP
```

Get the public IP via CLI if you don't have it handy:

```bash
aws ec2 describe-instances --instance-ids i-0123456789abcdef0 \
  --query "Reservations[0].Instances[0].PublicIpAddress" --output text
```

### Option B — EC2 Instance Connect (browser SSH)

Console → EC2 → Instances → select instance → **Connect** → **EC2 Instance Connect**. Needs an AMI that supports it and port 22 open.

### Option C — Session Manager (SSM)

Console → EC2 → Instances → **Connect** → **Session Manager**, or:

```bash
aws ssm start-session --target i-0123456789abcdef0
```

Works **without a public IP** — the instance just needs the SSM Agent running and an IAM role with `AmazonSSMManagedInstanceCore`.

> SSM is the "no exposed port 22" way of reaching a box — closer to how production fleets are actually accessed.

### Option D — Push a temporary SSH key (EC2 Instance Connect CLI)

```bash
aws ec2-instance-connect send-ssh-public-key \
  --instance-id i-0123456789abcdef0 \
  --availability-zone us-east-1a \
  --instance-os-user ec2-user \
  --ssh-public-key file://~/.ssh/id_rsa.pub

ssh ec2-user@PUBLIC_IP
```

## 9. Root Account (only if necessary)

```bash
aws configure --profile root
aws sts get-caller-identity --profile root
```

- Create the access key via Console → Account name → **My Security Credentials** → Create access key (secret shown once).
- Enable MFA, and **delete** root access keys once you're done — prefer IAM users/roles for everyday work.

## 10. SSO & Assume-Role (short)

```bash
# AWS SSO
aws configure sso
aws sso login --profile name

# Assume a role (cross-account / elevated tasks)
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/RoleName \
  --role-session-name cli-session
# → export the returned AccessKeyId, SecretAccessKey, SessionToken
```

## 11. Troubleshooting

| Symptom                          | Likely cause                                             |
|-----------------------------------|------------------------------------------------------------|
| `Permission denied (publickey)`   | Wrong key, wrong username, or loose key file permissions — `chmod 400` |
| `AccessDenied` / `UnauthorizedOperation` | Wrong profile/role, or IAM policy doesn't allow the action |
| SSM session fails                 | SSM Agent not running, or instance role missing SSM permissions |
| SSH times out                     | Security group doesn't allow port 22, or no public IP     |

## 12. Best Practices

- Avoid root keys — use IAM users/roles or SSO with least privilege.
- Use named profiles; rotate credentials regularly; enable MFA for privileged users.
- Automate infra with Terraform, images with Packer, first-boot config with cloud-init.
- Watch CPU steal, I/O wait, and thin-provisioned storage capacity on instances.

## 13. One-Liners to Remember

```bash
chmod 400 key.pem                            # fix SSH key permissions
ssh -i key.pem ubuntu@IP                      # SSH to Ubuntu
aws configure --profile name                  # set up a CLI profile
aws sts get-caller-identity --profile name    # "who am I"
aws ssm start-session --target i-...          # SSM connect, no public IP needed
```

## 14. Further Help

```bash
aws help
aws <service> help
```