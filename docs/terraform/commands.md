# Terraform Complete Commands & HCL Reference ⚡

Categorized command sheet for state manipulation, provisioning, workspace management, and HCL code blocks.

---

## 1. Essential Workflow Commands

| Command | Description | Example |
| :--- | :--- | :--- |
| `terraform init` | Initializes working directory, downloads plugins and modules. | `terraform init` |
| `terraform plan` | Shows execution plan and infrastructure diff. | `terraform plan -out=tfplan` |
| `terraform apply` | Applies configuration changes to cloud target. | `terraform apply tfplan` |
| `terraform destroy` | Destroys all infrastructure managed by current configuration. | `terraform destroy --auto-approve` |
| `terraform fmt` | Formats configuration files into canonical HCL style. | `terraform fmt -recursive` |
| `terraform validate` | Validates configuration syntax and consistency. | `terraform validate` |

---

## 2. State & Workspace Management

```bash
# Inspect current state file content
terraform show

# List all resources tracked in state
terraform state list

# Show detailed properties of a single tracked resource
terraform state show aws_instance.web_server

# Move or rename resource in state without destroying
terraform state mv aws_instance.old_name aws_instance.new_name

# Import existing unmanaged cloud resource into Terraform state
terraform import aws_s3_bucket.my_bucket my-bucket-name-in-aws

# Workspace Management (Environment Isolation)
terraform workspace list
terraform workspace new staging
terraform workspace select staging
```

---

## 3. Basic HCL Template (`main.tf`)

```hcl
terraform {
  required_version = ">= 1.5.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  backend "s3" {
    bucket         = "my-company-tfstate-bucket"
    key            = "prod/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-locks"
  }
}

provider "aws" {
  region = "us-east-1"
}

resource "aws_vpc" "main_vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

  tags = {
    Name = "production-vpc"
  }
}

output "vpc_id" {
  value       = aws_vpc.main_vpc.id
  description = "The ID of the created VPC"
}
```