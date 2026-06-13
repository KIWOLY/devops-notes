# AWS Resource Usage Reporter

## Overview

Cloud computing offers numerous advantages that organizations leverage for their infrastructure needs. Below are the key benefits:

### Lower Costs
- Organizations can rent computing resources as needed instead of buying and maintaining expensive servers
- Reduces upfront hardware costs and maintenance expenses

### Scalability
- Cloud services can quickly increase or decrease resources based on demand
- Example: An online store can handle surge traffic during sales without purchasing extra permanent servers

### Accessibility
- Data and applications can be accessed from anywhere with an internet connection
- Supports remote work and collaboration

### Reliability and Backup
- Major cloud providers offer data replication, backups, and disaster recovery options
- If one server fails, services can continue running on another

### Faster Deployment
- New servers, databases, and applications can be set up in minutes rather than days or weeks
- Enables organizations to innovate more quickly

### Security Features
- Cloud providers invest heavily in security tools, monitoring, and compliance certifications
- Many organizations benefit from built-in security protections

### Automatic Updates
- Cloud services handle infrastructure updates and patching automatically
- Reduces administrative workload




---

## Project Purpose

This project aims to create a **comprehensive report of AWS resource usage** by leveraging shell scripts to automatically collect and display information about various AWS services.

### Supported AWS Services
- **AWS S3** - Simple Storage Service for object storage
- **AWS EC2** - Elastic Compute Cloud for virtual machines
- **AWS Lambda** - Serverless compute service
- **AWS IAM** - Identity and Access Management for user management

---

## Cron Scheduler Overview

A **cron daemon** is a time-based job scheduler that checks a schedule and executes commands or scripts at specified times.

### Common Use Cases
- Running backups every night
- Sending automated reports
- Cleaning temporary files
- Updating databases
- Monitoring system health

---

## AWS Resource Reporter Script

### Script Details
**Author:** Kiwoly  
**Date:** 13-Jun-2026  
**Version:** V1.0  
**Description:** This script generates a report of AWS resource usage across multiple services

### Script Implementation

```bash
#!/bin/bash

############################################
# AWS Resource Usage Reporter
# Author: Kiwoly
# Date: 13-Jun-2026
# Version: V1.0
#
# Purpose: Generate a comprehensive report
#          of AWS resource usage
#
# Services Tracked:
#  - AWS S3 (Buckets)
#  - AWS EC2 (Instances)
#  - AWS Lambda (Functions)
#  - AWS IAM (Users)
############################################

echo "================================"
echo "AWS Resource Usage Report"
echo "================================"
echo ""

# List S3 Buckets
echo "[1] AWS S3 Buckets"
echo "---"
aws s3 ls
echo ""

# List EC2 Instances
echo "[2] AWS EC2 Instances"
echo "---"
aws ec2 describe-instances | jq '.Reservations[].Instances[].InstanceId'
echo ""

# List Lambda Functions
echo "[3] AWS Lambda Functions"
echo "---"
aws lambda list-functions
echo ""

# List IAM Users
echo "[4] AWS IAM Users"
echo "---"
aws iam list-users

echo ""
echo "================================"
echo "Report Generation Complete"
echo "================================"
```

### Prerequisites
- AWS CLI installed and configured
- Appropriate AWS credentials and permissions
- `jq` tool installed for JSON processing
- Bash shell environment

### Usage
```bash
chmod +x script.sh
./script.sh
```

### Scheduling with Cron
To run this script daily at 9:00 AM, add to crontab:
```bash
0 9 * * * /path/to/script.sh
``` 
