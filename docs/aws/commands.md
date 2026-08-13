# AWS CLI v2 Commands & CloudFormation Reference ⚡

Categorized command sheet for managing EC2, S3, IAM, VPC, and CloudFormation stacks.

---

## 1. S3 (Simple Storage Service) Commands

```bash
# Create a new S3 bucket
aws s3 mb s3://my-company-data-bucket-2026 --region us-east-1

# List all S3 buckets
aws s3 ls

# Sync local folder to S3 bucket
aws s3 sync ./dist s3://my-company-data-bucket-2026/dist

# Remove bucket and all its contents forcefully
aws s3 rb s3://my-company-data-bucket-2026 --force
```

---

## 2. EC2 (Elastic Compute Cloud) Commands

```bash
# List running EC2 instances
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running" --query "Reservations[*].Instances[*].[InstanceId,InstanceType,PublicIpAddress]" --output table

# Launch a new EC2 instance
aws ec2 run-instances   --image-id ami-0c7217cdde317cfec   --count 1   --instance-type t3.micro   --key-name my-ssh-key   --security-group-ids sg-0123456789abcdef0   --subnet-id subnet-0123456789abcdef0

# Stop / Start / Terminate instance
aws ec2 stop-instances --instance-ids i-0123456789abcdef0
aws ec2 start-instances --instance-ids i-0123456789abcdef0
```

---

## 3. IAM (Identity and Access Management) Commands

```bash
# List IAM Users
aws iam list-users --output table

# Create a new IAM User
aws iam create-user --user-name devops-admin

# Attach AdministratorAccess policy to user
aws iam attach-user-policy --user-name devops-admin --policy-arn arn:aws:iam::aws:policy/AdministratorAccess
```

---

## 4. Sample CloudFormation Template (`template.yaml`)

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: 'Basic AWS S3 Bucket CloudFormation Template'

Resources:
  MyS3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: 'my-cloudformation-bucket-2026'
      VersioningConfiguration:
        Status: Enabled

Outputs:
  BucketARN:
    Value: !GetAtt MyS3Bucket.Arn
    Description: 'ARN of the created S3 Bucket'
```