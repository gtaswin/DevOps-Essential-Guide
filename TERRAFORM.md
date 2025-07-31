# Terraform Essential Guide

## Table of Contents
- [Core Concepts](#core-concepts)
- [Infrastructure as Code](#infrastructure-as-code)
- [State Management](#state-management)
- [Providers](#providers)
- [Resources and Data Sources](#resources-and-data-sources)
- [Variables and Outputs](#variables-and-outputs)
- [Modules](#modules)
- [Workflow](#workflow)
- [Best Practices](#best-practices)
- [Real-World Production Example](#real-world-production-example)

## Core Concepts

### What is Terraform?
Terraform is an Infrastructure as Code (IaC) tool that allows you to define, provision, and manage infrastructure using declarative configuration files. Instead of manually creating resources through cloud consoles, you write code that describes your desired infrastructure state.

### Key Benefits
- **Declarative**: Describe what you want, not how to get there
- **Multi-Cloud**: Works with AWS, Azure, GCP, and 100+ providers
- **Version Control**: Infrastructure changes tracked in Git
- **Collaboration**: Teams can work together on infrastructure
- **Reproducible**: Same code creates identical infrastructure
- **Cost Tracking**: Preview changes before applying

### Core Components
```
Terraform Architecture
├── Configuration Files (.tf)
│   ├── Resources (what to create)
│   ├── Variables (input parameters)
│   ├── Outputs (return values)
│   └── Providers (cloud platforms)
├── State File
│   ├── Current infrastructure state
│   ├── Resource metadata
│   └── Dependency tracking
├── Providers
│   ├── AWS, Azure, GCP
│   ├── Kubernetes, Docker
│   └── Third-party services
└── Modules
    ├── Reusable components
    ├── Input/output interfaces
    └── Best practice templates
```

## Infrastructure as Code

### Traditional vs IaC Approach

**Traditional Infrastructure:**
- Manual creation through web consoles
- Click-and-configure approach
- Documentation gets outdated
- Hard to replicate environments
- No change tracking
- Error-prone manual processes

**Infrastructure as Code:**
- Code defines infrastructure
- Automated provisioning
- Version-controlled changes
- Consistent environments
- Peer review process
- Rollback capabilities

### Declarative vs Imperative

**Declarative (Terraform):**
- Describes desired end state
- Terraform figures out how to get there
- Idempotent operations
- Safe to run multiple times

**Imperative (Scripts):**
- Describes step-by-step actions
- Must handle current state logic
- Order of operations matters
- Can break if run multiple times

## State Management

### What is Terraform State?
State is Terraform's way of tracking which real-world resources correspond to your configuration. It's a JSON file that maps your configuration to actual infrastructure.

### State Storage Options
```
State Management
├── Local State (Default)
│   ├── terraform.tfstate file
│   ├── Single user only
│   └── Risk of loss/corruption
├── Remote State (Recommended)
│   ├── AWS S3 + DynamoDB
│   ├── Azure Storage Account
│   ├── GCS Bucket
│   └── Terraform Cloud
└── State Locking
    ├── Prevents concurrent modifications
    ├── DynamoDB for AWS
    ├── Storage account for Azure
    └── Automatic with Terraform Cloud
```

### Why Remote State?
- **Team Collaboration**: Multiple people can work on same infrastructure
- **Security**: State contains sensitive information
- **Backup**: Automatic backup and versioning
- **Locking**: Prevents conflicts during simultaneous operations

## Providers

### What are Providers?
Providers are plugins that allow Terraform to interact with cloud platforms, SaaS providers, and other APIs. Each provider offers a set of resource types and data sources.

### Popular Providers
```
Provider Ecosystem
├── Cloud Providers
│   ├── AWS (hashicorp/aws)
│   ├── Azure (hashicorp/azurerm)
│   ├── Google Cloud (hashicorp/google)
│   └── DigitalOcean, Linode, etc.
├── Container Platforms
│   ├── Kubernetes (hashicorp/kubernetes)
│   ├── Docker (kreuzwerker/docker)
│   └── Helm (hashicorp/helm)
├── SaaS Providers
│   ├── GitHub (integrations/github)
│   ├── Datadog (datadog/datadog)
│   └── PagerDuty, Slack, etc.
└── Infrastructure Tools
    ├── Vault (hashicorp/vault)
    ├── Consul (hashicorp/consul)
    └── DNS providers
```

### Provider Configuration
- Authentication credentials
- Default regions/locations
- API endpoints
- Version constraints

## Resources and Data Sources

### Resources
Resources are the most important element in Terraform language. They define infrastructure objects you want to create, update, or delete.

**Examples:**
- Virtual machines (EC2, Azure VM, Compute Engine)
- Networks (VPC, Virtual Network, VPC Network)
- Databases (RDS, SQL Database, Cloud SQL)
- Storage (S3 bucket, Storage Account, Cloud Storage)

### Data Sources
Data sources allow Terraform to fetch information from existing infrastructure or external systems.

**Use Cases:**
- Get latest AMI ID
- Fetch existing VPC information
- Look up DNS zones
- Reference outputs from other Terraform configurations

### Resource Dependencies
```
Resource Dependencies
├── Implicit Dependencies
│   ├── Automatic detection
│   ├── Reference other resources
│   └── Terraform builds dependency graph
├── Explicit Dependencies
│   ├── depends_on argument
│   ├── Force dependency order
│   └── Handle edge cases
└── Dependency Graph
    ├── Parallel resource creation
    ├── Proper destruction order
    └── Error handling
```

## Variables and Outputs

### Input Variables
Variables make your configurations flexible and reusable. They allow you to customize behavior without modifying the code.

**Variable Types:**
- String, Number, Boolean
- List, Map, Object
- Complex nested structures

**Variable Sources:**
- Default values in configuration
- terraform.tfvars files
- Environment variables
- Command-line flags
- Interactive prompts

### Output Values
Outputs return information about your infrastructure after creation. They're useful for:
- Displaying important information
- Passing data between configurations
- Integration with other tools

### Local Values
Local values assign names to expressions, making configurations more readable and maintainable.

## Modules

### What are Modules?
Modules are containers for multiple resources that are used together. They're the key to writing reusable, maintainable Terraform code.

### Module Structure
```
Module Organization
├── Root Module
│   ├── Main configuration files
│   ├── Calls child modules
│   └── Defines providers
├── Child Modules
│   ├── main.tf (resources)
│   ├── variables.tf (inputs)
│   ├── outputs.tf (outputs)
│   └── versions.tf (requirements)
└── Module Registry
    ├── Terraform Registry
    ├── Private registries
    └── Git repositories
```

### Module Benefits
- **Reusability**: Write once, use many times
- **Organization**: Logical grouping of resources
- **Abstraction**: Hide complexity behind simple interfaces
- **Testing**: Easier to test smaller components
- **Collaboration**: Teams can share and contribute modules

### Module Sources
- Local file paths
- Terraform Registry
- Git repositories
- HTTP URLs
- Private registries

## Workflow

### Core Terraform Commands
```
Terraform Workflow
├── terraform init
│   ├── Initialize working directory
│   ├── Download providers
│   ├── Configure backend
│   └── Install modules
├── terraform plan
│   ├── Create execution plan
│   ├── Show proposed changes
│   ├── Validate configuration
│   └── Safety check before apply
├── terraform apply
│   ├── Execute planned changes
│   ├── Create/update/delete resources
│   ├── Update state file
│   └── Show outputs
└── terraform destroy
    ├── Destroy managed infrastructure
    ├── Remove all resources
    └── Clean up state
```

### Development Workflow
1. **Write**: Create or modify Terraform configuration
2. **Plan**: Review what changes will be made
3. **Apply**: Execute the changes
4. **Version**: Commit configuration to version control
5. **Collaborate**: Share changes with team

### Environment Management
```
Environment Strategy
├── Separate State Files
│   ├── dev.tfstate
│   ├── staging.tfstate
│   └── prod.tfstate
├── Workspaces
│   ├── terraform workspace new prod
│   ├── Isolated state per workspace
│   └── Same configuration, different variables
└── Directory Structure
    ├── environments/dev/
    ├── environments/staging/
    └── environments/prod/
```

## Best Practices

### Code Organization
- Use consistent naming conventions
- Organize files logically (main.tf, variables.tf, outputs.tf)
- Group related resources together
- Use modules for reusable components

### State Management
- Always use remote state for team environments
- Enable state locking
- Regular state backups
- Never edit state file manually

### Security
- Don't commit secrets to version control
- Use environment variables or secret management systems
- Encrypt state files
- Limit access to state storage

### Version Control
- Commit .tf files, ignore .tfstate files
- Use .gitignore for Terraform
- Tag releases for environments
- Document changes in commit messages

### Testing and Validation
- Use `terraform validate` to check syntax
- Run `terraform plan` before apply
- Use `terraform fmt` for consistent formatting
- Implement automated testing where possible

### Performance
- Use data sources efficiently
- Avoid large state files
- Implement proper resource dependencies
- Use parallelism wisely

## Real-World Production Example

### Multi-Tier Web Application Infrastructure on AWS

This example demonstrates a production-ready, scalable web application infrastructure using Terraform best practices.

```hcl
# Provider configuration
terraform {
  required_version = ">= 1.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
  
  backend "s3" {
    bucket         = "mycompany-terraform-state"
    key            = "production/infrastructure.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
  }
}

provider "aws" {
  region = var.aws_region
  
  default_tags {
    tags = {
      Environment   = var.environment
      Project       = var.project_name
      ManagedBy     = "Terraform"
      CostCenter    = var.cost_center
      Owner         = var.owner_email
    }
  }
}

# Variables
variable "environment" {
  description = "Environment name"
  type        = string
  default     = "production"
}

variable "project_name" {
  description = "Project name"
  type        = string
  default     = "webapp"
}

variable "aws_region" {
  description = "AWS region"
  type        = string
  default     = "us-west-2"
}

variable "availability_zones" {
  description = "Availability zones"
  type        = list(string)
  default     = ["us-west-2a", "us-west-2b", "us-west-2c"]
}

variable "cost_center" {
  description = "Cost center for billing"
  type        = string
  default     = "engineering"
}

variable "owner_email" {
  description = "Owner email for resources"
  type        = string
  default     = "devops@company.com"
}

# Data sources
data "aws_ami" "amazon_linux" {
  most_recent = true
  owners      = ["amazon"]
  
  filter {
    name   = "name"
    values = ["amzn2-ami-hvm-*-x86_64-gp2"]
  }
}

data "aws_caller_identity" "current" {}

# Local values
locals {
  account_id = data.aws_caller_identity.current.account_id
  
  vpc_cidr = "10.0.0.0/16"
  
  public_subnet_cidrs = [
    "10.0.1.0/24",
    "10.0.2.0/24", 
    "10.0.3.0/24"
  ]
  
  private_subnet_cidrs = [
    "10.0.11.0/24",
    "10.0.12.0/24",
    "10.0.13.0/24"
  ]
  
  database_subnet_cidrs = [
    "10.0.21.0/24",
    "10.0.22.0/24"
  ]
  
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
  }
}

# VPC Module
module "vpc" {
  source = "./modules/vpc"
  
  name                 = "${var.project_name}-${var.environment}"
  cidr                 = local.vpc_cidr
  availability_zones   = var.availability_zones
  public_subnet_cidrs  = local.public_subnet_cidrs
  private_subnet_cidrs = local.private_subnet_cidrs
  
  enable_nat_gateway   = true
  enable_vpn_gateway   = false
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = local.common_tags
}

# Security Groups Module
module "security_groups" {
  source = "./modules/security-groups"
  
  vpc_id      = module.vpc.vpc_id
  vpc_cidr    = local.vpc_cidr
  environment = var.environment
  
  tags = local.common_tags
}

# Application Load Balancer Module
module "alb" {
  source = "./modules/alb"
  
  name            = "${var.project_name}-${var.environment}-alb"
  vpc_id          = module.vpc.vpc_id
  public_subnets  = module.vpc.public_subnet_ids
  security_groups = [module.security_groups.alb_security_group_id]
  
  certificate_arn = aws_acm_certificate.main.arn
  
  tags = local.common_tags
}

# Auto Scaling Group Module
module "asg" {
  source = "./modules/asg"
  
  name                = "${var.project_name}-${var.environment}-asg"
  vpc_id              = module.vpc.vpc_id
  private_subnets     = module.vpc.private_subnet_ids
  security_groups     = [module.security_groups.web_security_group_id]
  target_group_arns   = [module.alb.target_group_arn]
  
  ami_id                    = data.aws_ami.amazon_linux.id
  instance_type            = "t3.medium"
  min_size                 = 2
  max_size                 = 10
  desired_capacity         = 3
  health_check_type        = "ELB"
  health_check_grace_period = 300
  
  user_data = base64encode(templatefile("${path.module}/user-data.sh", {
    db_host     = module.rds.db_instance_endpoint
    redis_host  = module.elasticache.redis_endpoint
    environment = var.environment
  }))
  
  tags = local.common_tags
}

# RDS Database Module
module "rds" {
  source = "./modules/rds"
  
  identifier = "${var.project_name}-${var.environment}-db"
  
  engine            = "mysql"
  engine_version    = "8.0"
  instance_class    = "db.r5.large"
  allocated_storage = 100
  
  db_name  = "${var.project_name}${var.environment}"
  username = "admin"
  password = random_password.db_password.result
  
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.database_subnet_ids
  security_groups = [module.security_groups.rds_security_group_id]
  
  backup_retention_period = 7
  backup_window          = "03:00-04:00"
  maintenance_window     = "Sun:04:00-Sun:05:00"
  
  skip_final_snapshot = false
  final_snapshot_identifier = "${var.project_name}-${var.environment}-db-final-snapshot"
  
  tags = local.common_tags
}

# ElastiCache Redis Module
module "elasticache" {
  source = "./modules/elasticache"
  
  cluster_id = "${var.project_name}-${var.environment}-redis"
  
  node_type    = "cache.r5.large"
  num_cache_nodes = 1
  
  vpc_id          = module.vpc.vpc_id
  subnet_ids      = module.vpc.private_subnet_ids
  security_groups = [module.security_groups.redis_security_group_id]
  
  tags = local.common_tags
}

# S3 Bucket for static assets
resource "aws_s3_bucket" "assets" {
  bucket = "${var.project_name}-${var.environment}-assets-${local.account_id}"
}

resource "aws_s3_bucket_versioning" "assets" {
  bucket = aws_s3_bucket.assets.id
  versioning_configuration {
    status = "Enabled"
  }
}

resource "aws_s3_bucket_server_side_encryption_configuration" "assets" {
  bucket = aws_s3_bucket.assets.id
  
  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}

# CloudFront Distribution
resource "aws_cloudfront_distribution" "main" {
  origin {
    domain_name = module.alb.dns_name
    origin_id   = "ALB-${module.alb.dns_name}"
    
    custom_origin_config {
      http_port              = 80
      https_port             = 443
      origin_protocol_policy = "https-only"
      origin_ssl_protocols   = ["TLSv1.2"]
    }
  }
  
  origin {
    domain_name = aws_s3_bucket.assets.bucket_regional_domain_name
    origin_id   = "S3-${aws_s3_bucket.assets.id}"
    
    s3_origin_config {
      origin_access_identity = aws_cloudfront_origin_access_identity.main.cloudfront_access_identity_path
    }
  }
  
  enabled             = true
  is_ipv6_enabled     = true
  default_root_object = "index.html"
  
  aliases = ["www.${var.domain_name}", var.domain_name]
  
  default_cache_behavior {
    allowed_methods        = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
    cached_methods         = ["GET", "HEAD"]
    target_origin_id       = "ALB-${module.alb.dns_name}"
    compress               = true
    viewer_protocol_policy = "redirect-to-https"
    
    forwarded_values {
      query_string = false
      cookies {
        forward = "none"
      }
    }
    
    min_ttl     = 0
    default_ttl = 3600
    max_ttl     = 86400
  }
  
  ordered_cache_behavior {
    path_pattern     = "/static/*"
    allowed_methods  = ["GET", "HEAD", "OPTIONS"]
    cached_methods   = ["GET", "HEAD", "OPTIONS"]
    target_origin_id = "S3-${aws_s3_bucket.assets.id}"
    compress         = true
    
    forwarded_values {
      query_string = false
      cookies {
        forward = "none"
      }
    }
    
    min_ttl                = 0
    default_ttl            = 86400
    max_ttl                = 31536000
    viewer_protocol_policy = "redirect-to-https"
  }
  
  restrictions {
    geo_restriction {
      restriction_type = "none"
    }
  }
  
  viewer_certificate {
    acm_certificate_arn = aws_acm_certificate.main.arn
    ssl_support_method  = "sni-only"
  }
  
  tags = local.common_tags
}

resource "aws_cloudfront_origin_access_identity" "main" {
  comment = "OAI for ${var.project_name}-${var.environment}"
}

# ACM Certificate
resource "aws_acm_certificate" "main" {
  domain_name               = var.domain_name
  subject_alternative_names = ["*.${var.domain_name}"]
  validation_method         = "DNS"
  
  lifecycle {
    create_before_destroy = true
  }
  
  tags = local.common_tags
}

# Random password for database
resource "random_password" "db_password" {
  length  = 32
  special = true
}

# Store database password in AWS Secrets Manager
resource "aws_secretsmanager_secret" "db_password" {
  name = "${var.project_name}-${var.environment}-db-password"
}

resource "aws_secretsmanager_secret_version" "db_password" {
  secret_id     = aws_secretsmanager_secret.db_password.id
  secret_string = random_password.db_password.result
}

# Route53 DNS records
data "aws_route53_zone" "main" {
  name         = var.domain_name
  private_zone = false
}

resource "aws_route53_record" "main" {
  zone_id = data.aws_route53_zone.main.zone_id
  name    = var.domain_name
  type    = "A"
  
  alias {
    name                   = aws_cloudfront_distribution.main.domain_name
    zone_id                = aws_cloudfront_distribution.main.hosted_zone_id
    evaluate_target_health = false
  }
}

resource "aws_route53_record" "www" {
  zone_id = data.aws_route53_zone.main.zone_id
  name    = "www.${var.domain_name}"
  type    = "A"
  
  alias {
    name                   = aws_cloudfront_distribution.main.domain_name
    zone_id                = aws_cloudfront_distribution.main.hosted_zone_id
    evaluate_target_health = false
  }
}

# Outputs
output "vpc_id" {
  description = "ID of the VPC"
  value       = module.vpc.vpc_id
}

output "alb_dns_name" {
  description = "DNS name of the load balancer"
  value       = module.alb.dns_name
}

output "cloudfront_domain_name" {
  description = "CloudFront distribution domain name"
  value       = aws_cloudfront_distribution.main.domain_name
}

output "rds_endpoint" {
  description = "RDS instance endpoint"
  value       = module.rds.db_instance_endpoint
  sensitive   = true
}

output "redis_endpoint" {
  description = "Redis cluster endpoint"
  value       = module.elasticache.redis_endpoint
  sensitive   = true
}

output "s3_bucket_name" {
  description = "Name of the S3 bucket for static assets"
  value       = aws_s3_bucket.assets.id
}
```

### Architecture Overview

This production example creates:

```
Production Architecture
├── Global Layer
│   ├── CloudFront CDN (Global)
│   ├── Route53 DNS (Global)
│   └── ACM SSL Certificate
├── Application Layer
│   ├── Application Load Balancer
│   ├── Auto Scaling Group (Multi-AZ)
│   ├── EC2 Instances (3+ across AZs)
│   └── Security Groups
├── Data Layer
│   ├── RDS MySQL (Multi-AZ)
│   ├── ElastiCache Redis
│   └── S3 Static Assets
└── Network Layer
    ├── VPC with public/private subnets
    ├── Internet Gateway
    ├── NAT Gateways (Multi-AZ)
    └── Route Tables
```

### Key Features Demonstrated

**Infrastructure as Code Best Practices:**
- Modular design with reusable components
- Environment-specific configurations
- Proper resource tagging
- Remote state management with locking

**Security:**
- Resources in private subnets
- Security groups with least privilege
- Secrets stored in AWS Secrets Manager
- SSL/TLS encryption throughout

**High Availability:**
- Multi-AZ deployment
- Auto Scaling Group with health checks
- Load balancer health monitoring
- Database backups and maintenance windows

**Performance:**
- CloudFront CDN for global distribution
- ElastiCache for application caching
- Optimized instance types and storage

**Operational Excellence:**
- Comprehensive outputs for integration
- Proper naming conventions
- Cost center tagging for billing
- Automated backups and maintenance

This example shows how Terraform enables you to define complex, production-ready infrastructure as code while maintaining best practices for security, availability, and operational excellence.