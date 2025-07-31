<div align="center">

# 🟣 Terraform Essential Guide

<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/terraform/terraform-original-wordmark.svg" alt="Terraform" width="200"/>

*Comprehensive guide to Infrastructure as Code concepts, state management, and production examples*

[![Documentation](https://img.shields.io/badge/Terraform-Documentation-623CE4?logo=terraform&logoColor=white)](https://developer.hashicorp.com/terraform/docs)
[![Registry](https://img.shields.io/badge/Terraform-Registry-623CE4?logo=terraform&logoColor=white)](https://registry.terraform.io/)

</div>

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

📖 **Learn More**: [Terraform Documentation](https://developer.hashicorp.com/terraform/docs) | [Terraform Introduction](https://developer.hashicorp.com/terraform/intro)

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

📖 **Learn More**: [Infrastructure as Code](https://developer.hashicorp.com/terraform/tutorials/aws-get-started/infrastructure-as-code) | [Terraform vs Other Tools](https://developer.hashicorp.com/terraform/intro/vs)

## State Management

### What is Terraform State?
State is Terraform's way of tracking which real-world resources correspond to your configuration. It's a JSON file that maps your configuration to actual infrastructure.

🔥 **Critical**: State files contain sensitive data (passwords, private keys). Never commit terraform.tfstate to version control!

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

📖 **Learn More**: [State Management](https://developer.hashicorp.com/terraform/language/state) | [Remote State](https://developer.hashicorp.com/terraform/language/state/remote)

### Why Remote State?
- **Team Collaboration**: Multiple people can work on same infrastructure
- **Security**: State contains sensitive information
- **Backup**: Automatic backup and versioning
- **Locking**: Prevents conflicts during simultaneous operations

⚠️ **Warning**: Always configure state locking in production to prevent corruption from simultaneous terraform runs.

✅ **Best Practice**: Use separate state files for different environments (dev/staging/prod) to prevent accidental cross-environment changes.

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

📖 **Learn More**: [Providers Documentation](https://developer.hashicorp.com/terraform/language/providers) | [Provider Registry](https://registry.terraform.io/browse/providers)

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

## Essential Syntax Reference

Complete syntax guide covering the most important Terraform patterns for practical Infrastructure as Code implementation:

### Provider Configuration
```hcl
# AWS Provider with multiple configuration options
provider "aws" {
  region     = var.aws_region
  access_key = var.aws_access_key
  secret_key = var.aws_secret_key
  
  # Alternative: use AWS profiles
  profile = "production"
  
  # Default tags for all resources
  default_tags {
    tags = {
      Environment = var.environment
      ManagedBy   = "Terraform"
    }
  }
}

# Multiple provider configurations (aliases)
provider "aws" {
  alias  = "us_east_1"
  region = "us-east-1"
}

provider "aws" {
  alias  = "us_west_2"
  region = "us-west-2"
}

# Using aliased providers
resource "aws_s3_bucket" "east_bucket" {
  provider = aws.us_east_1
  bucket   = "my-east-bucket"
}
```

**What it does**: Configure cloud provider authentication, regions, and default settings for all resources.
**Why use it**: Set up multi-region deployments, apply consistent tags across all resources, and use different credentials for different environments.

📖 **Learn More**: [Provider Configuration](https://developer.hashicorp.com/terraform/language/providers/configuration) | [Provider Requirements](https://developer.hashicorp.com/terraform/language/providers/requirements)

### Variables and Type Constraints
```hcl
# String variable
variable "environment" {
  description = "Environment name"
  type        = string
  default     = "development"
  
  validation {
    condition     = contains(["development", "staging", "production"], var.environment)
    error_message = "Environment must be development, staging, or production."
  }
}

# Number variable
variable "instance_count" {
  description = "Number of instances to create"
  type        = number
  default     = 1
  
  validation {
    condition     = var.instance_count >= 1 && var.instance_count <= 10
    error_message = "Instance count must be between 1 and 10."
  }
}

# Boolean variable
variable "enable_monitoring" {
  description = "Enable detailed monitoring"
  type        = bool
  default     = false
}

# List variable
variable "availability_zones" {
  description = "List of availability zones"
  type        = list(string)
  default     = ["us-west-2a", "us-west-2b", "us-west-2c"]
}

# Map variable
variable "instance_types" {
  description = "Instance types per environment"
  type        = map(string)
  default = {
    development = "t3.micro"
    staging     = "t3.small"
    production  = "t3.medium"
  }
}

# Object variable (complex type)
variable "database_config" {
  description = "Database configuration"
  type = object({
    engine_version    = string
    instance_class    = string
    allocated_storage = number
    backup_retention  = number
    multi_az         = bool
  })
  default = {
    engine_version    = "8.0"
    instance_class    = "db.t3.micro"
    allocated_storage = 20
    backup_retention  = 7
    multi_az         = false
  }
}

# Using variables
resource "aws_instance" "web" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_types[var.environment]
  availability_zone      = var.availability_zones[0]
  monitoring             = var.enable_monitoring
  vpc_security_group_ids = [aws_security_group.web.id]
  
  tags = {
    Name = "${var.environment}-web-server"
  }
}
```

**What it does**: Define input parameters with types, defaults, and validation rules to make configurations reusable.
**Why use it**: Same Terraform code works across environments (dev/prod) with different values, and validation catches configuration errors early.

📖 **Learn More**: [Input Variables](https://developer.hashicorp.com/terraform/language/values/variables) | [Variable Validation](https://developer.hashicorp.com/terraform/language/values/variables#custom-validation-rules)

### Local Values and Expressions
```hcl
locals {
  # String interpolation and concatenation
  name_prefix = "${var.project_name}-${var.environment}"
  
  # Conditional expressions
  instance_type = var.environment == "production" ? "t3.large" : "t3.micro"
  
  # Complex expressions
  subnet_cidrs = [
    for i in range(length(var.availability_zones)) : 
    cidrsubnet(var.vpc_cidr, 8, i + 1)
  ]
  
  # Map transformations
  availability_zone_mapping = {
    for idx, az in var.availability_zones : 
    az => local.subnet_cidrs[idx]
  }
  
  # Common tags
  common_tags = merge(
    {
      Environment = var.environment
      Project     = var.project_name
      ManagedBy   = "Terraform"
    },
    var.additional_tags
  )
  
  # Environment-specific configurations
  config = {
    development = {
      instance_count = 1
      backup_enabled = false
      multi_az      = false
    }
    staging = {
      instance_count = 2
      backup_enabled = true
      multi_az      = false
    }
    production = {
      instance_count = 3
      backup_enabled = true
      multi_az      = true
    }
  }
  
  current_config = local.config[var.environment]
}
```

**What it does**: Calculate derived values, create reusable expressions, and organize complex logic into named references.
**Why use it**: Avoid repeating complex expressions, create environment-specific configurations, and make code more readable with meaningful names.

### Data Sources
```hcl
# Get latest AMI
data "aws_ami" "ubuntu" {
  most_recent = true
  owners      = ["099720109477"] # Canonical
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  
  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}

# Get current AWS region
data "aws_region" "current" {}

# Get current AWS caller identity
data "aws_caller_identity" "current" {}

# Get existing VPC
data "aws_vpc" "existing" {
  filter {
    name   = "tag:Name"
    values = ["${var.environment}-vpc"]
  }
}

# Get Route53 hosted zone
data "aws_route53_zone" "main" {
  name         = var.domain_name
  private_zone = false
}

# Get existing security group
data "aws_security_group" "existing_sg" {
  count = var.use_existing_sg ? 1 : 0
  
  filter {
    name   = "group-name"
    values = ["${var.environment}-web-sg"]
  }
}

# Get availability zones
data "aws_availability_zones" "available" {
  state = "available"
  
  filter {
    name   = "opt-in-status"
    values = ["opt-in-not-required"]
  }
}
```

**What it does**: Query existing cloud resources and retrieve information for use in your Terraform configuration.
**Why use it**: Reference existing VPCs, get latest AMI IDs, find availability zones, or integrate with resources created outside Terraform.

📖 **Learn More**: [Data Sources](https://developer.hashicorp.com/terraform/language/data-sources) | [AWS Data Sources](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ami)

### Resource Configuration and Dependencies
```hcl
# VPC with explicit dependencies
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-vpc"
    }
  )
}

# Internet Gateway (implicit dependency on VPC)
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-igw"
    }
  )
}

# Subnets with dynamic creation
resource "aws_subnet" "public" {
  count = length(var.availability_zones)
  
  vpc_id                  = aws_vpc.main.id
  cidr_block              = local.subnet_cidrs[count.index]
  availability_zone       = var.availability_zones[count.index]
  map_public_ip_on_launch = true
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-public-${count.index + 1}"
      Type = "Public"
    }
  )
}

# Security Group with dynamic rules
resource "aws_security_group" "web" {
  name_prefix = "${local.name_prefix}-web-"
  vpc_id      = aws_vpc.main.id
  description = "Security group for web servers"
  
  # Dynamic ingress rules
  dynamic "ingress" {
    for_each = var.allowed_ports
    content {
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
      description = ingress.value.description
    }
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
    description = "All outbound traffic"
  }
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-web-sg"
    }
  )
  
  # Explicit dependency
  depends_on = [aws_vpc.main]
}

# Launch template with advanced configuration
resource "aws_launch_template" "web" {
  name_prefix   = "${local.name_prefix}-web-"
  image_id      = data.aws_ami.ubuntu.id
  instance_type = local.current_config.instance_type
  key_name      = var.key_pair_name
  
  vpc_security_group_ids = [aws_security_group.web.id]
  
  user_data = base64encode(templatefile("${path.module}/user_data.sh", {
    app_name    = var.app_name
    environment = var.environment
    db_endpoint = aws_db_instance.main.endpoint
  }))
  
  block_device_mappings {
    device_name = "/dev/sda1"
    ebs {
      volume_type           = "gp3"
      volume_size           = var.root_volume_size
      delete_on_termination = true
      encrypted             = true
    }
  }
  
  monitoring {
    enabled = var.enable_detailed_monitoring
  }
  
  tag_specifications {
    resource_type = "instance"
    tags = merge(
      local.common_tags,
      {
        Name = "${local.name_prefix}-web"
      }
    )
  }
  
  lifecycle {
    create_before_destroy = true
  }
}
```

**What it does**: Define cloud resources with specific configurations, establish dependencies between resources, and manage resource relationships.
**Why use it**: Create infrastructure components (servers, databases, networks) with proper dependencies, ensuring resources are created in correct order and configured consistently.

### Loops and Conditionals
```hcl
# Count-based resource creation
resource "aws_instance" "web" {
  count                  = var.create_instances ? var.instance_count : 0
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public[count.index % length(aws_subnet.public)].id
  vpc_security_group_ids = [aws_security_group.web.id]
  
  tags = {
    Name = "${local.name_prefix}-web-${count.index + 1}"
  }
}

# For_each with map
resource "aws_s3_bucket" "app_buckets" {
  for_each = var.s3_buckets
  
  bucket        = "${local.name_prefix}-${each.key}"
  force_destroy = each.value.force_destroy
  
  tags = merge(
    local.common_tags,
    {
      Name    = "${local.name_prefix}-${each.key}"
      Purpose = each.value.purpose
    }
  )
}

# For_each with set
resource "aws_iam_user" "developers" {
  for_each = toset(var.developer_names)
  
  name = each.value
  path = "/developers/"
  
  tags = local.common_tags
}

# For_each with complex objects
resource "aws_route_table" "private" {
  for_each = {
    for idx, az in var.availability_zones : az => {
      cidr_block = local.subnet_cidrs[idx + length(var.availability_zones)]
      nat_gateway_id = aws_nat_gateway.main[idx].id
    }
  }
  
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = each.value.nat_gateway_id
  }
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-private-rt-${each.key}"
    }
  )
}

# Conditional resource creation
resource "aws_eip" "nat" {
  count = var.enable_nat_gateway ? length(var.availability_zones) : 0
  
  domain = "vpc"
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-nat-eip-${count.index + 1}"
    }
  )
  
  depends_on = [aws_internet_gateway.main]
}

# Conditional expressions in resources
resource "aws_db_instance" "main" {
  identifier     = "${local.name_prefix}-db"
  engine         = "mysql"
  engine_version = var.database_config.engine_version
  instance_class = var.database_config.instance_class
  
  allocated_storage     = var.database_config.allocated_storage
  max_allocated_storage = var.environment == "production" ? 1000 : 100
  storage_encrypted     = var.environment == "production" ? true : false
  
  db_name  = var.db_name
  username = var.db_username
  password = var.db_password
  
  multi_az               = var.database_config.multi_az
  backup_retention_period = var.database_config.backup_retention
  backup_window          = var.environment == "production" ? "03:00-04:00" : "07:00-08:00"
  maintenance_window     = var.environment == "production" ? "sun:04:00-sun:05:00" : "sun:08:00-sun:09:00"
  
  skip_final_snapshot = var.environment != "production"
  final_snapshot_identifier = var.environment == "production" ? "${local.name_prefix}-db-final-snapshot" : null
  
  tags = local.common_tags
}
```

**What it does**: Create multiple similar resources using count (numeric) or for_each (maps/sets) with dynamic configuration.
**Why use it**: Deploy multiple servers, create resources per environment, or handle dynamic lists without duplicating code. Use count for simple scaling, for_each for complex scenarios.

📖 **Learn More**: [The count Meta-Argument](https://developer.hashicorp.com/terraform/language/meta-arguments/count) | [The for_each Meta-Argument](https://developer.hashicorp.com/terraform/language/meta-arguments/for_each)

### Advanced For Expressions and Filtering
```hcl
locals {
  # Complex for expressions with filtering
  production_instances = {
    for name, config in var.instances : name => config
    if config.environment == "production" && config.enabled
  }
  
  # Transform and filter lists
  public_subnet_ids = [
    for subnet in aws_subnet.public : subnet.id
    if subnet.availability_zone != "us-west-2d"
  ]
  
  # Nested for expressions for complex data transformation
  security_group_rules = {
    for sg_name, sg_config in var.security_groups : sg_name => {
      ingress_rules = [
        for rule in sg_config.ingress_rules : {
          from_port   = rule.from_port
          to_port     = rule.to_port
          protocol    = rule.protocol
          cidr_blocks = rule.cidr_blocks
          description = "${rule.protocol}/${rule.from_port}-${rule.to_port}"
        }
        if rule.enabled != false
      ]
      egress_rules = [
        for rule in sg_config.egress_rules : {
          from_port   = rule.from_port
          to_port     = rule.to_port
          protocol    = rule.protocol
          cidr_blocks = rule.cidr_blocks
        }
        if rule.protocol != "icmp" || var.allow_icmp
      ]
    }
  }
  
  # Group by and aggregate patterns
  instances_by_az = {
    for instance_key, instance in var.instances :
    instance.availability_zone => instance_key...
  }
  
  # Flatten nested structures
  all_route_table_associations = flatten([
    for subnet_key, subnet in aws_subnet.private : [
      for rt_key, rt in aws_route_table.private : {
        subnet_id      = subnet.id
        route_table_id = rt.id
        association_id = "${subnet_key}-${rt_key}"
      }
      if subnet.availability_zone == rt.tags.AvailabilityZone
    ]
  ])
}

# Using for expressions in resource blocks
resource "aws_route_table_association" "private" {
  for_each = {
    for assoc in local.all_route_table_associations : 
    assoc.association_id => assoc
  }
  
  subnet_id      = each.value.subnet_id
  route_table_id = each.value.route_table_id
}
```

**What it does**: Create complex data transformations using for expressions with filtering, grouping, and nested iterations.
**Why use it**: Transform input data structures, filter resources based on multiple conditions, create dynamic associations, and build complex resource configurations from simple inputs.

### Essential Built-in Functions
```hcl
locals {
  # String manipulation functions
  resource_name_clean = replace(lower(var.resource_name), "_", "-")
  domain_parts       = split(".", var.domain_name)
  formatted_name     = join("-", [var.environment, var.project, "app"])
  trimmed_description = trim(var.description, " \t\n")
  
  # Pattern matching and extraction
  vpc_id_from_arn = regex("vpc-[a-zA-Z0-9]+", var.vpc_arn)
  environment_suffix = substr(var.environment, 0, 3)
  
  # Collection functions
  all_subnet_ids = flatten([
    aws_subnet.public[*].id,
    aws_subnet.private[*].id,
    aws_subnet.database[*].id
  ])
  
  unique_availability_zones = distinct([
    for subnet in aws_subnet.public : subnet.availability_zone
  ])
  
  subnet_id_set = toset(local.all_subnet_ids)
  tag_map = tomap({
    for key, value in var.additional_tags : key => tostring(value)
  })
  
  # Network calculation functions
  private_subnet_cidrs = [
    for i in range(var.private_subnet_count) :
    cidrsubnet(var.vpc_cidr, 8, i + 10)
  ]
  
  first_host_ip = cidrhost(var.vpc_cidr, 1)
  subnet_netmask = cidrnetmask(var.vpc_cidr)
  
  # Date and cryptographic functions
  backup_timestamp = formatdate("YYYY-MM-DD-hhmm", timestamp())
  resource_hash = substr(sha256("${var.project}-${var.environment}"), 0, 8)
  config_encoded = base64encode(jsonencode(var.application_config))
  
  # Type conversion and validation
  port_number = tonumber(var.port_string)
  enabled_features = tolist(var.feature_set)
  
  # Logical functions
  backup_enabled = alltrue([
    var.environment == "production",
    var.enable_backup,
    var.retention_days > 0
  ])
  
  any_public_access = anytrue([
    for rule in var.security_rules : rule.cidr_blocks == ["0.0.0.0/0"]
  ])
  
  # Conditional functions with error handling
  instance_type = try(var.instance_types[var.environment], var.default_instance_type)
  
  # Test if configuration is valid
  valid_config = can(regex("^[a-zA-Z0-9-]+$", var.resource_name))
  
  # Complex conditional logic
  final_tags = merge(
    var.default_tags,
    var.environment == "production" ? var.production_tags : {},
    var.enable_monitoring ? { "Monitoring" = "enabled" } : {},
    {
      "ManagedBy"   = "Terraform"
      "Environment" = var.environment
      "Timestamp"   = local.backup_timestamp
    }
  )
}

# Using functions in resource configuration
resource "aws_instance" "app" {
  for_each = local.production_instances
  
  ami           = data.aws_ami.latest.id
  instance_type = try(each.value.instance_type, local.instance_type)
  subnet_id     = element(local.unique_availability_zones, index(keys(local.production_instances), each.key) % length(local.unique_availability_zones))
  
  user_data = base64encode(templatefile("${path.module}/user_data.sh", {
    environment    = var.environment
    resource_hash  = local.resource_hash
    config_encoded = local.config_encoded
  }))
  
  tags = merge(local.final_tags, {
    Name = "${local.formatted_name}-${each.key}-${local.environment_suffix}"
  })
  
  lifecycle {
    precondition {
      condition     = local.valid_config
      error_message = "Resource name must contain only alphanumeric characters and hyphens."
    }
  }
}
```

**What it does**: Use built-in functions for string manipulation, data transformation, network calculations, validation, and error handling.
**Why use it**: Clean and transform data, perform complex calculations, validate inputs, handle errors gracefully, and create dynamic configurations based on computed values.

📖 **Learn More**: [Built-in Functions](https://developer.hashicorp.com/terraform/language/functions) | [Expression Syntax](https://developer.hashicorp.com/terraform/language/expressions)

### Modules and Module Configuration
```hcl
# Using external module
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "~> 3.0"
  
  name = "${local.name_prefix}-vpc"
  cidr = var.vpc_cidr
  
  azs             = var.availability_zones
  private_subnets = local.private_subnet_cidrs
  public_subnets  = local.public_subnet_cidrs
  
  enable_nat_gateway = var.enable_nat_gateway
  enable_vpn_gateway = var.enable_vpn_gateway
  single_nat_gateway = var.environment != "production"
  
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = local.common_tags
  
  vpc_tags = {
    Name = "${local.name_prefix}-vpc"
  }
  
  public_subnet_tags = {
    "kubernetes.io/role/elb" = "1"
  }
  
  private_subnet_tags = {
    "kubernetes.io/role/internal-elb" = "1"
  }
}

# Using local module
module "security_groups" {
  source = "./modules/security-groups"
  
  vpc_id          = module.vpc.vpc_id
  name_prefix     = local.name_prefix
  allowed_cidrs   = var.allowed_cidrs
  environment     = var.environment
  
  tags = local.common_tags
}

# Module with for_each
module "application_servers" {
  source = "./modules/ec2-instance"
  
  for_each = var.application_configs
  
  name               = "${local.name_prefix}-${each.key}"
  instance_type      = each.value.instance_type
  subnet_id          = module.vpc.private_subnets[each.value.subnet_index]
  security_group_ids = [module.security_groups.app_sg_id]
  user_data          = each.value.user_data
  
  tags = merge(
    local.common_tags,
    {
      Application = each.key
      Tier        = each.value.tier
    }
  )
}
```

**What it does**: Create reusable infrastructure components that can be shared across projects and environments with customizable inputs and outputs.
**Why use it**: Avoid code duplication, standardize infrastructure patterns, and enable team collaboration by packaging common configurations into reusable modules.

### State Management and Backends
```hcl
# S3 backend with state locking
terraform {
  required_version = ">= 1.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.1"
    }
  }
  
  backend "s3" {
    bucket         = "my-terraform-state-bucket"
    key            = "environments/production/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-state-lock"
    
    # Optional: use role assumption
    role_arn = "arn:aws:iam::123456789012:role/TerraformStateRole"
  }
}

# Remote state data source
data "terraform_remote_state" "networking" {
  backend = "s3"
  
  config = {
    bucket = "shared-terraform-state"
    key    = "networking/terraform.tfstate"
    region = "us-west-2"
  }
}

# Using remote state data
resource "aws_instance" "app" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = var.instance_type
  subnet_id              = data.terraform_remote_state.networking.outputs.private_subnet_ids[0]
  vpc_security_group_ids = [data.terraform_remote_state.networking.outputs.app_security_group_id]
}
```

**What it does**: Configure where Terraform stores state files, enabling team collaboration and state locking to prevent concurrent modifications.
**Why use it**: Share state across team members, prevent state corruption from simultaneous runs, and enable remote state storage with versioning and backup.

### Outputs and Data Export
```hcl
# Simple outputs
output "vpc_id" {
  description = "ID of the VPC"
  value       = aws_vpc.main.id
}

output "public_subnet_ids" {
  description = "IDs of the public subnets"
  value       = aws_subnet.public[*].id
}

# Sensitive output
output "database_password" {
  description = "Database administrator password"
  value       = aws_db_instance.main.password
  sensitive   = true
}

# Complex outputs
output "instance_details" {
  description = "Details of created instances"
  value = {
    for instance in aws_instance.web :
    instance.id => {
      public_ip    = instance.public_ip
      private_ip   = instance.private_ip
      availability_zone = instance.availability_zone
    }
  }
}

# Conditional output
output "load_balancer_dns" {
  description = "DNS name of load balancer"
  value       = var.create_load_balancer ? aws_lb.main[0].dns_name : null
}

# Output with depends_on
output "application_url" {
  description = "URL of the application"
  value       = "https://${aws_route53_record.main.fqdn}"
  depends_on  = [aws_route53_record.main]
}
```

**What it does**: Export values from Terraform configurations for use by other systems, CI/CD pipelines, or subsequent Terraform runs.
**Why use it**: Share resource IDs, connection strings, or endpoints with applications, pass values between Terraform workspaces, or integrate with external tools.

📖 **Learn More**: [Output Values](https://developer.hashicorp.com/terraform/language/values/outputs) | [Output Configuration](https://developer.hashicorp.com/terraform/language/values/outputs#output-configuration)

### Lifecycle Management
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  # Lifecycle rules
  lifecycle {
    # Prevent destruction of critical resources
    prevent_destroy = var.environment == "production"
    
    # Create replacement before destroying
    create_before_destroy = true
    
    # Ignore changes to specific attributes
    ignore_changes = [
      ami,
      user_data,
      tags["LastModified"]
    ]
    
    # Replace resource when specific attributes change
    replace_triggered_by = [
      aws_launch_template.web
    ]
  }
  
  tags = {
    Name         = "${local.name_prefix}-web"
    LastModified = timestamp()
  }
}

# Resource with precondition and postcondition
resource "aws_s3_bucket" "app_data" {
  bucket = "${local.name_prefix}-app-data"
  
  lifecycle {
    precondition {
      condition     = length(var.bucket_name) > 3
      error_message = "Bucket name must be more than 3 characters."
    }
    
    postcondition {
      condition     = self.bucket_domain_name != ""
      error_message = "Bucket domain name should be available after creation."
    }
  }
}
```

**What it does**: Control resource creation, updates, and deletion behavior with rules for preventing accidental changes or managing resource replacement.
**Why use it**: Protect critical resources from accidental deletion, manage zero-downtime deployments, and control when resources should be recreated vs updated.

### State Operations and Management
```bash
# Import existing resources into Terraform state
terraform import aws_instance.example i-1234567890abcdef0
terraform import aws_s3_bucket.existing my-existing-bucket
terraform import module.vpc.aws_vpc.main vpc-12345678

# State manipulation commands
terraform state list                           # List all resources in state
terraform state show aws_instance.example     # Show detailed state of resource
terraform state mv aws_instance.old aws_instance.new    # Rename resource in state
terraform state rm aws_instance.unused        # Remove resource from state
terraform state pull > terraform.tfstate.backup        # Backup current state

# Refresh state from real infrastructure
terraform refresh                              # Update state from real resources
terraform plan -refresh-only                  # Preview state refresh changes
terraform apply -refresh-only                 # Apply state refresh

# Workspace management
terraform workspace list                       # List all workspaces
terraform workspace new development           # Create new workspace
terraform workspace select production         # Switch to workspace
terraform workspace delete old-workspace      # Delete workspace

# State replacement (when resources are recreated outside Terraform)
terraform state replace-provider -from=hashicorp/aws -to=hashicorp/aws
terraform apply -replace=aws_instance.example # Force recreation of resource

# Working with modules in state
terraform state list module.vpc               # List resources in module
terraform state mv module.old_vpc module.new_vpc      # Move entire module
```

**What it does**: Manage Terraform state, import existing resources, manipulate state structure, and handle workspace operations.
**Why use it**: Bring existing infrastructure under Terraform management, fix state inconsistencies, reorganize resource structure, and manage multiple environments.

### Provisioners and Post-Deployment Configuration
```hcl
# Local provisioner - run commands on local machine
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  key_name      = aws_key_pair.deployer.key_name
  
  provisioner "local-exec" {
    command = "echo 'Instance ${self.id} created with IP ${self.public_ip}' >> deployment.log"
    
    # Run different commands based on OS
    command = var.is_windows ? "echo ${self.public_ip} >> instances.txt" : "echo '${self.public_ip}' >> instances.txt"
    
    # Set working directory and environment variables
    working_dir = "${path.module}/scripts"
    environment = {
      INSTANCE_ID = self.id
      PUBLIC_IP   = self.public_ip
    }
  }
  
  provisioner "local-exec" {
    when    = destroy
    command = "echo 'Instance ${self.id} destroyed' >> deployment.log"
  }
}

# Remote provisioner - run commands on remote resource
resource "aws_instance" "app_server" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = "t3.small"
  key_name              = aws_key_pair.deployer.key_name
  vpc_security_group_ids = [aws_security_group.web.id]
  subnet_id             = aws_subnet.public[0].id
  
  # Connection configuration for remote provisioners
  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("${path.module}/keys/deployer.pem")
    host        = self.public_ip
    timeout     = "5m"
  }
  
  # File provisioner - copy files to remote resource
  provisioner "file" {
    source      = "${path.module}/app/"
    destination = "/tmp/app"
  }
  
  provisioner "file" {
    content = templatefile("${path.module}/config.tpl", {
      database_url = aws_db_instance.main.endpoint
      redis_url    = aws_elasticache_cluster.main.cache_nodes[0].address
    })
    destination = "/tmp/app.conf"
  }
  
  # Remote-exec provisioner - run commands on remote resource
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y docker.io",
      "sudo systemctl start docker",
      "sudo systemctl enable docker",
      "sudo usermod -aG docker ubuntu"
    ]
  }
  
  provisioner "remote-exec" {
    script = "${path.module}/scripts/deploy-app.sh"
  }
  
  # Cleanup provisioner (runs on destroy)
  provisioner "remote-exec" {
    when = destroy
    inline = [
      "sudo docker stop $(sudo docker ps -q)",
      "sudo docker system prune -f"
    ]
  }
}

# Windows instance with WinRM connection
resource "aws_instance" "windows_server" {
  ami           = data.aws_ami.windows.id
  instance_type = "t3.medium"
  
  connection {
    type     = "winrm"
    user     = "Administrator"
    password = var.admin_password
    host     = self.public_ip
    timeout  = "10m"
  }
  
  provisioner "remote-exec" {
    inline = [
      "powershell.exe Install-WindowsFeature -Name Web-Server -IncludeManagementTools",
      "powershell.exe New-Website -Name 'MyApp' -Port 80 -PhysicalPath C:\\inetpub\\myapp"
    ]
  }
}

# Using null_resource for standalone provisioning
resource "null_resource" "database_setup" {
  depends_on = [aws_db_instance.main]
  
  triggers = {
    db_instance_id = aws_db_instance.main.id
    schema_version = var.schema_version
  }
  
  provisioner "local-exec" {
    command = "python ${path.module}/scripts/setup-database.py"
    environment = {
      DB_HOST     = aws_db_instance.main.endpoint
      DB_NAME     = aws_db_instance.main.db_name
      DB_USER     = aws_db_instance.main.username
      DB_PASSWORD = var.db_password
    }
  }
}

# Advanced provisioner with error handling
resource "aws_instance" "monitored" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  
  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file(var.private_key_path)
    host        = self.public_ip
    timeout     = "5m"
  }
  
  provisioner "remote-exec" {
    inline = [
      "set -e",  # Exit on any error
      "sudo apt-get update",
      "sudo apt-get install -y monitoring-agent",
      "sudo systemctl enable monitoring-agent",
      "sudo systemctl start monitoring-agent"
    ]
    
    on_failure = continue  # Continue even if this provisioner fails
  }
  
  provisioner "local-exec" {
    command     = "ansible-playbook -i '${self.public_ip},' playbook.yml"
    working_dir = "${path.module}/ansible"
    
    on_failure = fail  # Fail the entire resource if this fails
  }
}
```

**What it does**: Execute commands locally or remotely after resource creation, copy files to remote systems, and perform post-deployment configuration tasks.
**Why use it**: Bootstrap instances with software installation, deploy applications, configure services, run database migrations, and integrate with external systems.

⚠️ **Provisioner Warning**: Provisioners are a last resort. Use cloud-init, user data, or configuration management tools (like Ansible) when possible for better reliability and idempotency.

📖 **Learn More**: [State CLI Commands](https://developer.hashicorp.com/terraform/cli/commands/state) | [Provisioners](https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax)

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