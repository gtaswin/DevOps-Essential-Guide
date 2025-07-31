<div align="center">

# 🟧 AWS Essential Guide

<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/amazonwebservices/amazonwebservices-original-wordmark.svg" alt="AWS" width="200"/>

*Comprehensive guide to Amazon Web Services core concepts, services, and production architectures*

[![Documentation](https://img.shields.io/badge/AWS-Documentation-FF9900?logo=amazon-aws&logoColor=white)](https://docs.aws.amazon.com/)
[![Well-Architected](https://img.shields.io/badge/AWS-Well--Architected-232F3E?logo=amazon-aws&logoColor=white)](https://aws.amazon.com/architecture/well-architected/)

</div>

## Table of Contents
- [Core Concepts](#core-concepts)
- [Identity and Access Management (IAM)](#identity-and-access-management-iam)
- [Virtual Private Cloud (VPC)](#virtual-private-cloud-vpc)
- [Compute Services](#compute-services)
- [Storage Services](#storage-services)
- [Database Services](#database-services)
- [Networking](#networking)
- [Security](#security)
- [Monitoring and Logging](#monitoring-and-logging)
- [DevOps and CI/CD](#devops-and-cicd)
- [Best Practices](#best-practices)

## Core Concepts

### AWS Global Infrastructure
- **Regions**: Geographic areas containing multiple Availability Zones (e.g., us-east-1, eu-west-1)
- **Availability Zones (AZs)**: Isolated data centers within a region for high availability
- **Edge Locations**: CloudFront content delivery network endpoints worldwide (400+ locations)
- **Local Zones**: Extensions of AWS Regions for ultra-low latency applications
- **Wavelength Zones**: Ultra-low latency applications for 5G devices
- **AWS Outposts**: Fully managed service extending AWS to on-premises

📖 **Learn More**: [AWS Global Infrastructure](https://aws.amazon.com/about-aws/global-infrastructure/) | [Regions and Availability Zones](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-regions-availability-zones.html)

### AWS Resource Hierarchy
AWS organizes resources in a hierarchical structure from top to bottom:

```
AWS Organization (Optional - Top Level)
├── Organizational Units (OUs)
│   └── AWS Accounts
│       └── Regions
│           └── Availability Zones
│               └── Resources (VPCs, Subnets, EC2, etc.)
└── Service Control Policies (SCPs)
```

**1. AWS Organization** (Top Level - Optional)
- Root container for multiple AWS accounts
- **Master/Management Account**: Manages billing and organizational policies
- **Organizational Units (OUs)**: Group accounts for easier management (e.g., Production, Development, Security)
- **Member Accounts**: Individual AWS accounts within organization
- **Service Control Policies (SCPs)**: Guardrails for account permissions
- **Consolidated Billing**: Single bill for all accounts

**2. AWS Account** (Account Level)
- Isolated container for AWS resources
- **Root User**: Full administrative access (should be secured with MFA)
- **Account ID**: 12-digit unique identifier
- **Billing Boundary**: Separate billing and cost allocation
- **IAM Users/Roles**: Identities with specific permissions
- **Resource Limits**: Service quotas per account

**3. AWS Regions** (Geographic Level)
- Geographic areas containing multiple Availability Zones
- **Data Sovereignty**: Data remains within selected region
- **Service Availability**: Not all services available in all regions
- **Latency Optimization**: Choose regions close to users
- **Disaster Recovery**: Deploy across multiple regions

**4. Availability Zones (AZs)** (Infrastructure Level)
- Isolated data centers within a region
- **High Availability**: Deploy across multiple AZs
- **Physical Separation**: Separate power, cooling, networking
- **Low Latency**: High-speed connections between AZs

**5. Resources** (Service Level)
- Individual AWS services deployed within AZs/Regions
- **Global Services**: IAM, CloudFront, Route 53 (no region)
- **Regional Services**: S3, Lambda, DynamoDB
- **Zonal Services**: EC2 instances, EBS volumes

**6. Resource Naming and Identification**
- **Amazon Resource Names (ARNs)**: Unique resource identifiers
  - Format: `arn:partition:service:region:account-id:resource-type/resource-id`
  - Example: `arn:aws:s3:::my-bucket/my-object`
- **Tags**: Key-value pairs for resource organization
  - Cost allocation and tracking
  - Access control and automation
  - Resource lifecycle management

### AWS Well-Architected Framework
Five pillars for building secure, high-performing, resilient, and efficient infrastructure:
1. **Operational Excellence**: Running and monitoring systems
2. **Security**: Protecting information and systems
3. **Reliability**: Ensuring workload performs correctly
4. **Performance Efficiency**: Using computing resources efficiently
5. **Cost Optimization**: Avoiding unnecessary costs

💡 **Tip**: Start with the Well-Architected Framework principles when designing any AWS solution - they provide proven best practices for building secure, reliable, and cost-effective systems.

## Identity and Access Management (IAM)

### Core Components

#### Users
Individual accounts for people or applications

**Real-World Example**: 
```
You're setting up access for a new developer "Sarah" joining your team:
- Create IAM user: sarah.developer
- Add her to "Developers" group with read-only S3 access
- She gets temporary AWS console access but no production permissions
```

#### Groups  
Collections of users with similar permissions

**Real-World Example**:
```
Your company has different teams needing different access:
- "Developers" group: EC2 read, S3 read/write for dev buckets
- "DevOps" group: Full EC2, RDS, CloudFormation access
- "Auditors" group: Read-only access to CloudTrail, billing
```

#### Roles
Temporary permissions that can be assumed by trusted entities

**Real-World Example**:
```
Your application running on EC2 needs to access S3:
- Create "AppServerRole" with S3 access
- Attach role to EC2 instance
- Application automatically gets temporary credentials
- No need to hardcode access keys in your code!
```

#### Policies
JSON documents defining permissions

**Real-World Example**:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-app-uploads/*"
    }
  ]
}
```
*This policy allows reading files from a specific S3 bucket - perfect for an app that displays user uploads*

⚠️ **Security Warning**: Never use root account for daily tasks. Create IAM users with minimal required permissions and enable MFA for all accounts.

📖 **Learn More**: [AWS IAM Documentation](https://docs.aws.amazon.com/iam/) | [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

### Key Concepts
- **Principal**: Entity that can make requests (user, role, service)
- **Action**: What can be performed (e.g., s3:GetObject)
- **Resource**: AWS resource the action can be performed on
- **Condition**: Circumstances under which policy applies
- **Effect**: Allow or Deny for the policy statement
- **Statement ID (Sid)**: Optional identifier for policy statements

### Policy Types
- **AWS Managed Policies**: Created and maintained by AWS
- **Customer Managed Policies**: Created and maintained by you
- **Inline Policies**: Directly attached to single user, group, or role
- **Resource-based Policies**: Attached to resources (S3 bucket policies)
- **Permission Boundaries**: Maximum permissions an entity can have
- **Service Control Policies (SCPs)**: Organization-wide permission guardrails

### Advanced IAM Features
- **IAM Access Analyzer**: External access analysis and unused access findings
- **IAM Credential Report**: Account-wide credential usage report
- **IAM Access Advisor**: Service-level access activity
- **Cross-Account Access**: AssumeRole for accessing resources across accounts
- **Federation**: SAML 2.0, OpenID Connect, custom identity broker
- **IAM Identity Center (SSO)**: Centralized access management

### Multi-Factor Authentication (MFA)
- **Virtual MFA Device**: Smartphone app (Google Authenticator, Authy)
- **Hardware MFA Device**: Physical token (YubiKey)
- **SMS Text Message**: Not recommended for root accounts
- **U2F Security Key**: FIDO Alliance standard

### Best Practices
- Enable MFA for root account and privileged users
- Use roles instead of sharing credentials
- Apply principle of least privilege
- Regularly rotate access keys
- Use IAM Access Analyzer to review permissions
- Monitor with CloudTrail and access patterns
- Use temporary credentials whenever possible
- Implement strong password policies

## Virtual Private Cloud (VPC)

### What is VPC?
A logically isolated section of AWS cloud where you can launch resources in a virtual network you define. Think of it as your own private data center in the cloud.

📖 **Learn More**: [Amazon VPC Documentation](https://docs.aws.amazon.com/vpc/) | [VPC User Guide](https://docs.aws.amazon.com/vpc/latest/userguide/)

### Core Components

#### Subnets
- **Public Subnet**: Has direct route to Internet Gateway
- **Private Subnet**: No direct internet access, uses NAT for outbound  
- **Database Subnet**: Typically private, isolated for database resources
- Each subnet exists in one Availability Zone

**Real-World Example**:
```
You're building an e-commerce website:
- Public Subnet (10.0.1.0/24): Web servers facing internet
- Private Subnet (10.0.2.0/24): Application servers (APIs, business logic)
- Database Subnet (10.0.3.0/24): RDS MySQL database (completely isolated)

Users → Public Subnet (Web) → Private Subnet (API) → Database Subnet (MySQL)
```

#### Internet Gateway (IGW)
- Horizontally scaled, redundant, highly available VPC component
- Allows communication between VPC and internet
- Performs NAT for instances with public IP addresses
- One IGW per VPC

**Real-World Example**:
```
Your blog website needs internet access:
- Attach IGW to your VPC
- Route table: 0.0.0.0/0 → IGW (for public subnets)
- EC2 instances in public subnet can now serve web traffic
- Users worldwide can access your blog at your-blog.com
```

#### NAT Gateway/Instance
- **NAT Gateway**: Managed service for outbound internet access from private subnets
- **NAT Instance**: EC2 instance performing NAT (legacy approach)
- Located in public subnet, allows private subnet resources to reach internet
- Does not allow inbound connections from internet

**Real-World Example**:
```
Your API servers in private subnet need to download software updates:
- Place NAT Gateway in public subnet
- Private subnet route: 0.0.0.0/0 → NAT Gateway
- API servers can download updates, install packages
- But internet users can't directly access your API servers
- Perfect for security: outbound allowed, inbound blocked
```

#### Route Tables
- Contains rules (routes) determining where network traffic is directed
- Each subnet must be associated with a route table
- Main route table: Default for all subnets not explicitly associated

#### Security Groups
- Virtual firewall controlling inbound and outbound traffic
- Stateful: Return traffic automatically allowed
- Applied at instance level
- Default: All outbound allowed, no inbound allowed
- Up to 5 security groups per network interface
- Can reference other security groups as sources/destinations
- Rules evaluated as a whole (all allow rules)

#### Network ACLs (NACLs)
- Additional layer of security at subnet level
- Stateless: Must explicitly allow return traffic
- Numbered rules processed in order (1-32766)
- Default: Allow all inbound and outbound
- One NACL per subnet, but can be associated with multiple subnets
- Separate rules for inbound and outbound traffic

### VPC Flow Logs
- Capture network traffic information for VPC, subnet, or ENI
- Published to CloudWatch Logs, S3, or Kinesis Data Firehose
- Monitor traffic patterns and troubleshoot connectivity
- Custom flow log formats available
- Can capture accepted, rejected, or all traffic

💡 **Tip**: Enable VPC Flow Logs for security analysis and troubleshooting - they only capture metadata, not actual packet contents.

📖 **Learn More**: [VPC Flow Logs](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs.html) | [Flow Logs Examples](https://docs.aws.amazon.com/vpc/latest/userguide/flow-logs-records-examples.html)

### Elastic Network Interfaces (ENI)
- Virtual network interface you can attach to instances
- Includes private IPv4/IPv6 addresses, security groups
- Can be moved between instances for failover
- Hot-attach, warm-attach, or cold-attach

⚠️ **Warning**: When moving ENIs between instances, ensure the instances are in the same Availability Zone.

📖 **Learn More**: [Elastic Network Interfaces](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) | [ENI Scenarios](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/scenarios-enis.html)

### VPC Peering
- Network connection between two VPCs
- Route traffic privately using private IP addresses
- No single point of failure or bandwidth bottleneck
- Cannot peer overlapping CIDR blocks
- Cross-region and cross-account peering supported
- No transitive routing (hub-and-spoke requires multiple peerings)

🔥 **Important**: VPC Peering is not transitive - if VPC A peers with VPC B, and VPC B peers with VPC C, then VPC A cannot communicate with VPC C directly.

📖 **Learn More**: [VPC Peering](https://docs.aws.amazon.com/vpc/latest/peering/) | [Peering Configurations](https://docs.aws.amazon.com/vpc/latest/peering/peering-configurations.html)

### Transit Gateway
- Network transit hub connecting VPCs and on-premises networks
- Simplifies network architecture (star configuration)
- Supports thousands of VPC connections
- Route tables for granular routing control

✅ **Best Practice**: Use Transit Gateway instead of multiple VPC peering connections when you have 3+ VPCs to connect.

📖 **Learn More**: [AWS Transit Gateway](https://docs.aws.amazon.com/vpc/latest/tgw/) | [Transit Gateway Examples](https://docs.aws.amazon.com/vpc/latest/tgw/TGW_Scenarios.html)
- Cross-region peering and multicast support

### VPC Endpoints
Private connectivity to AWS services without internet gateway:

```
VPC Endpoints
├── Gateway Endpoints (Free)
│   ├── S3 (Simple Storage Service)
│   │   ├── Route table entries
│   │   ├── No data processing charges
│   │   └── Policy-based access control
│   └── DynamoDB
│       ├── Route table entries
│       ├── No additional network charges
│       └── VPC-only access
└── Interface Endpoints (PrivateLink)
    ├── Most AWS Services (100+ services)
    │   ├── Elastic Network Interface (ENI)
    │   ├── Private IP in your subnet
    │   ├── DNS resolution within VPC
    │   └── Security group controls
    ├── Pricing
    │   ├── Endpoint hours ($0.01/hour)
    │   └── Data processing ($0.01/GB)
    └── Features
        ├── Cross-AZ redundancy
        ├── Policy-based access control
        └── Private DNS names
```

### AWS PrivateLink
- Private connectivity between VPCs and AWS services
- Traffic doesn't traverse internet
- Interface endpoints powered by PrivateLink
- Can expose your own services to other VPCs

### DHCP Options Sets
- Configure DHCP for your VPC
- Domain name, domain name servers, NTP servers
- NetBIOS name servers and node type
- One DHCP options set per VPC

## Compute Services

### EC2 (Elastic Compute Cloud)
Virtual servers in the cloud with various instance types optimized for different use cases.

📖 **Learn More**: [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/) | [EC2 Instance Types](https://aws.amazon.com/ec2/instance-types/)

#### Instance Types

**General Purpose**: t3, m5 (balanced CPU, memory, networking)
**Real-World Example**: 
```
Perfect for your company website or small web application:
- t3.micro: Development/testing environment ($8/month)
- t3.medium: Small production website (10,000 visitors/day)
- m5.large: Medium business application with database
```

**Compute Optimized**: c5 (CPU-intensive applications)
**Real-World Example**:
```
Your startup is building a video encoding service:
- c5.xlarge: Process video uploads, convert formats
- c5.4xlarge: Handle batch processing of 100+ videos
- Perfect for CPU-heavy tasks that don't need much memory
```

**Memory Optimized**: r5, x1 (in-memory databases, real-time analytics)
**Real-World Example**:
```
Your e-commerce site needs fast product recommendations:
- r5.large: Redis cache for session storage
- r5.xlarge: In-memory analytics for real-time personalization
- x1e.xlarge: Apache Spark for big data processing
```

**Storage Optimized**: i3, d2 (high I/O performance)
**Real-World Example**:
```
You're running a high-traffic database:
- i3.large: NoSQL database needing fast SSD storage
- d2.xlarge: Data warehouse with massive local storage
- Perfect for databases requiring thousands of IOPS
```

**Accelerated Computing**: p3, g4 (GPU workloads, ML)
**Real-World Example**:
```
Your AI startup is training machine learning models:
- p3.2xlarge: Train image recognition models
- g4dn.xlarge: Real-time inference for recommendation engine
- Perfect for deep learning, video rendering, scientific computing
```

#### Pricing Models

**On-Demand**: Pay per hour/second, no commitment
**Real-World Example**:
```
Perfect for unpredictable workloads:
- Development/testing environments (start/stop as needed)
- New applications (don't know usage patterns yet)
- Short-term projects (3-month marketing campaign)
```

**Reserved Instances**: 1-3 year commitment, up to 75% savings
**Real-World Example**:
```
Your production web servers run 24/7:
- Save $2,000/year on each m5.large instance
- Perfect for steady-state applications
- Database servers, web servers, always-on services
```

**Spot Instances**: Bid on spare capacity, up to 90% savings
**Real-World Example**:
```
Your data science team processes daily reports:
- Run batch jobs overnight when prices are low
- c5.4xlarge for $0.20/hour instead of $0.68/hour
- Perfect for fault-tolerant workloads (can handle interruptions)
```

**Dedicated Hosts**: Physical server dedicated to your use
**Real-World Example**:
```
Your company has strict compliance requirements:
- Banking application requiring physical isolation
- Software licensing tied to physical cores
- HIPAA compliance for healthcare applications
```

#### Storage Options
- **EBS (Elastic Block Store)**: Persistent, high availability block storage
- **Instance Store**: Temporary storage physically attached to host
- **EFS (Elastic File System)**: Managed NFS for multiple instances

#### Instance Metadata and User Data
- **Instance Metadata**: Information about running instance
  - Accessible via http://169.254.169.254/latest/meta-data/
  - Instance ID, AMI ID, security groups, IAM roles
  - Instance Metadata Service v2 (IMDSv2) with session tokens
- **User Data**: Bootstrap scripts run at instance launch
  - Base64 encoded, up to 16 KB
  - Run with root privileges
  - Accessible via metadata service

#### Placement Groups
- **Cluster**: Low latency, high network performance (single AZ)
- **Partition**: Spread across partitions (different hardware)
- **Spread**: Small number of instances on distinct hardware

#### EC2 Instance Lifecycle
- **Pending**: Instance is launching
- **Running**: Instance is running
- **Stopping**: Instance is shutting down
- **Stopped**: Instance is shut down
- **Shutting-down**: Instance is terminating
- **Terminated**: Instance is permanently deleted

#### Auto Scaling Groups
- Automatically adjust number of instances
- Health checks and instance replacement
- Integration with ELB health checks
- Scaling policies based on metrics
- Scheduled scaling for predictable workloads
- Lifecycle hooks for custom actions

#### Elastic Load Balancing

**Application Load Balancer (ALB)**: Layer 7, HTTP/HTTPS
**Real-World Example**:
```
Your e-commerce website with multiple services:
- Route /api/* requests to API servers
- Route /images/* requests to static content servers  
- Route /admin/* requests to admin panel servers
- Support for HTTPS termination and WebSocket connections
- Perfect for: Web applications, microservices, API routing
```

**Network Load Balancer (NLB)**: Layer 4, TCP/UDP
**Real-World Example**:
```
Your gaming platform needing ultra-low latency:
- Handle millions of TCP connections for real-time gaming
- Static IP addresses for DNS configuration
- Preserve client source IP for analytics/security
- Handle 40,000+ requests/second with <1ms latency
- Perfect for: Gaming, IoT, high-performance applications
```

**Gateway Load Balancer (GWLB)**: Layer 3, network traffic
**Real-World Example**:
```
Your enterprise security setup with network appliances:
- Route all traffic through Palo Alto firewalls
- Deploy intrusion detection systems (IDS)
- Scale security appliances automatically
- Perfect for: Network security, traffic inspection, compliance
```

**Classic Load Balancer (CLB)**: Legacy, Layer 4 and 7
**Real-World Example**:
```
Your legacy application migration:
- Existing application built for Classic Load Balancer
- Simple HTTP/HTTPS and TCP load balancing
- Note: Consider migrating to ALB or NLB for better features
- Perfect for: Legacy applications, simple use cases
```

#### AMI (Amazon Machine Image)
- Template for launching instances
- Includes OS, applications, and configuration
- Can be public, private, or shared
- AMI lifecycle: create, copy, share, deprecate
- EBS-backed vs Instance Store-backed AMIs

### Lambda
Serverless compute service running code without managing servers.

📖 **Learn More**: [AWS Lambda Documentation](https://docs.aws.amazon.com/lambda/) | [Lambda Developer Guide](https://docs.aws.amazon.com/lambda/latest/dg/)

#### Key Features
- Automatic scaling based on requests
- Pay only for compute time consumed
- Supports multiple programming languages
- Event-driven execution
- 15-minute maximum execution time

#### Runtime Support
- **Native Runtimes**: Python, Node.js, Java, .NET, Go, Ruby
- **Custom Runtimes**: Using Runtime API
- **Container Images**: Deploy functions as container images

#### Lambda Layers
Share code and dependencies across multiple functions:

```
Lambda Function
├── Function Code (Required)
│   ├── Handler function
│   ├── Business logic
│   └── Function-specific code
├── Lambda Layers (Up to 5 per function)
│   ├── Layer 1: Runtime Dependencies
│   │   ├── Python packages (requests, boto3)
│   │   ├── Node.js modules (express, lodash)
│   │   └── Java libraries (.jar files)
│   ├── Layer 2: Custom Runtime
│   │   ├── Custom language runtime
│   │   ├── Runtime bootstrap scripts
│   │   └── Runtime configuration
│   ├── Layer 3: Application Libraries
│   │   ├── Internal company libraries
│   │   ├── Shared utility functions
│   │   └── Common configurations
│   ├── Layer 4: Monitoring/Observability
│   │   ├── APM agents (New Relic, DataDog)
│   │   ├── Logging libraries
│   │   └── Metrics collection tools
│   └── Layer 5: Security/Compliance
│       ├── Encryption libraries
│       ├── Authentication helpers
│       └── Compliance tools
└── Benefits
    ├── Reduced deployment package size
    ├── Faster deployment times
    ├── Version management per layer
    ├── Share common code across functions
    └── Separate development workflows
```

#### Event Sources
- **Synchronous**: API Gateway, ALB, CloudFront
- **Asynchronous**: S3, SNS, EventBridge
- **Stream-based**: Kinesis Data Streams, DynamoDB Streams
- **Poll-based**: SQS, MSK, self-managed Apache Kafka

#### Configuration Options
- **Memory**: 128 MB to 10,240 MB (affects CPU allocation)
- **Timeout**: 1 second to 15 minutes
- **Environment Variables**: Configuration without code changes
- **VPC Configuration**: Access resources in private subnets
- **Dead Letter Queues**: Handle failed async invocations
- **Reserved Concurrency**: Limit or guarantee function capacity
- **Provisioned Concurrency**: Pre-warm functions for consistent performance

#### Lambda@Edge
- Run code at CloudFront edge locations
- Customize content delivery
- Viewer request/response, origin request/response events
- Lower latency for global applications

#### Error Handling and Retry
- **Synchronous**: Client handles errors and retries
- **Asynchronous**: Lambda retries (2 attempts), then DLQ
- **Stream-based**: Lambda retries until success or expiry

#### Use Cases

**Real-time file processing**
**Real-World Example**:
```
Your photo sharing app needs to create thumbnails:
- User uploads photo to S3 bucket
- S3 triggers Lambda function automatically
- Lambda resizes image and saves thumbnail
- Total cost: $0.0001 per image processed
```

**Web application backends**
**Real-World Example**:
```
Building a REST API for your mobile app:
- API Gateway receives HTTP requests
- Lambda processes business logic (user login, data retrieval)
- Lambda connects to RDS database
- No servers to manage, scales automatically
```

**Scheduled tasks (cron jobs)**
**Real-World Example**:
```
Your e-commerce site needs daily inventory reports:
- CloudWatch Events triggers Lambda at 2 AM daily
- Lambda queries DynamoDB for product data
- Generates CSV report and emails to managers
- Runs only when needed, costs $0.02/month
```

**Image/video processing**
**Real-World Example**:
```
Your social media platform needs content moderation:
- User uploads video to S3
- Lambda function calls Amazon Rekognition
- Detects inappropriate content automatically
- Sends notification to moderation team if needed
```

### ECS (Elastic Container Service)
Fully managed container orchestration service.

#### Launch Types
- **EC2**: Run containers on EC2 instances you manage
  - You manage the underlying infrastructure
  - More control over compute environment
  - Can use Spot instances for cost savings
- **Fargate**: Serverless containers, AWS manages infrastructure
  - No server management required
  - Pay only for compute resources used
  - Automatic scaling and patching

#### Core Components
- **Task Definition**: Blueprint for running containers
  - CPU and memory requirements
  - Container image and port mappings
  - Environment variables and secrets
  - IAM task role and execution role
  - Network mode and logging configuration
  - Volume mounts and storage
- **Task**: Running instance of task definition
- **Service**: Ensures desired number of tasks running
  - Auto Scaling based on metrics
  - Load balancer integration
  - Service discovery with AWS Cloud Map
  - Rolling updates and deployments
- **Cluster**: Logical grouping of compute resources
  - Can span multiple AZs
  - Container Insights for monitoring

#### Container Agent
- Runs on each EC2 instance in ECS cluster
- Communicates with ECS service
- Starts and stops containers
- Monitors resource utilization

#### Service Discovery
- AWS Cloud Map integration
- DNS-based service discovery
- Service mesh compatibility
- Health checking and failover

#### Deployment Strategies
- **Rolling Update**: Gradually replace tasks
- **Blue/Green**: Deploy to new instances, switch traffic
- **Circuit Breaker**: Stop deployment on failures

#### ECS Anywhere
- Run ECS tasks on customer-managed infrastructure
- Hybrid and on-premises container management
- Consistent APIs and tooling

### EKS (Elastic Kubernetes Service)
Managed Kubernetes service for running containerized applications.

#### EKS Architecture
```
EKS Cluster
├── Control Plane (AWS Managed)
│   ├── API Server
│   ├── etcd Database
│   ├── Controller Manager
│   └── Scheduler
├── Data Plane (Customer Managed)
│   ├── Managed Node Groups
│   │   ├── EC2 Instances
│   │   ├── Auto Scaling Groups
│   │   └── Launch Templates
│   ├── Fargate Profiles
│   │   ├── Serverless Pods
│   │   ├── No Node Management
│   │   └── Per-Pod Billing
│   └── Self-Managed Nodes
│       ├── Custom AMIs
│       ├── Advanced Configuration
│       └── Full Control
└── Add-ons
    ├── VPC CNI Plugin
    ├── CoreDNS
    ├── kube-proxy
    └── AWS Load Balancer Controller
```

#### Key Features
- **Multi-AZ Control Plane**: Highly available across 3 AZs
- **IAM Integration**: Native AWS IAM for authentication
- **VPC Integration**: Secure networking with VPC
- **Fargate Support**: Serverless container execution
- **Managed Node Groups**: Automated node lifecycle management
- **Service Mesh**: AWS App Mesh integration
- **Observability**: CloudWatch Container Insights

#### Security Features
- **Pod Security Standards**: Kubernetes security policies
- **IRSA (IAM Roles for Service Accounts)**: Fine-grained permissions
- **Secrets Management**: Integration with AWS Secrets Manager
- **Network Policies**: Calico network policies
- **Image Scanning**: ECR vulnerability scanning

#### Cluster Management
- **Cluster Autoscaler**: Automatic node scaling
- **Horizontal Pod Autoscaler**: Pod-level auto-scaling
- **Vertical Pod Autoscaler**: Resource optimization
- **Cluster Upgrades**: In-place Kubernetes version upgrades
- **Add-on Management**: Managed add-ons with versioning

## Storage Services

### S3 (Simple Storage Service)
Object storage service offering industry-leading scalability, data availability, security, and performance.

📖 **Learn More**: [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/) | [S3 User Guide](https://docs.aws.amazon.com/AmazonS3/latest/userguide/)

#### Storage Classes
Optimize costs based on access patterns and durability requirements:

**Real-World Examples by Use Case**:
```
Your company's data storage strategy:

📱 User Profile Pictures (Standard)
- Accessed daily by mobile app users
- Need instant load times
- Cost: $0.023/GB/month

📊 Monthly Reports (Standard-IA) 
- Accessed few times per month by managers
- Can wait 1-2 seconds for retrieval
- Cost: $0.0125/GB/month (50% savings)

🗂️ Legal Documents (Glacier Flexible)
- Accessed once per year for compliance
- Can wait 3-5 hours when needed
- Cost: $0.004/GB/month (83% savings)

📜 7-Year Tax Records (Glacier Deep Archive)
- Accessed almost never (regulatory backup)
- Can wait 12-48 hours if audited
- Cost: $0.00099/GB/month (96% savings!)
```

```
S3 Storage Classes
├── Frequent Access
│   └── Standard
│       ├── 99.999999999% (11 9's) durability
│       ├── 99.99% availability SLA
│       ├── Low latency and high throughput
│       └── No minimum storage duration
├── Intelligent Optimization
│   └── Intelligent-Tiering
│       ├── Automatic tier transitions
│       ├── Monitors access patterns (30+ days)
│       ├── Frequent Access tier
│       ├── Infrequent Access tier
│       ├── Archive Instant Access tier
│       ├── Archive Access tier (90-270 days)
│       ├── Deep Archive Access tier (180+ days)
│       └── Small object monitoring fee
├── Infrequent Access
│   ├── Standard-IA
│   │   ├── Lower storage cost, higher retrieval cost
│   │   ├── 30-day minimum storage duration
│   │   ├── 99.9% availability SLA
│   │   └── Multi-AZ storage
│   └── One Zone-IA
│       ├── 20% less cost than Standard-IA
│       ├── Single AZ storage
│       ├── 99.5% availability SLA
│       └── 30-day minimum storage duration
└── Archive Storage
    ├── Glacier Instant Retrieval
    │   ├── Millisecond retrieval
    │   ├── 90-day minimum storage duration
    │   ├── Same performance as Standard-IA
    │   └── 40% less cost than Standard-IA
    ├── Glacier Flexible Retrieval
    │   ├── Retrieval Options:
    │   │   ├── Expedited (1-5 minutes)
    │   │   ├── Standard (3-5 hours)
    │   │   └── Bulk (5-12 hours)
    │   ├── 90-day minimum storage duration
    │   └── Up to 10% less cost than Glacier Instant
    └── Glacier Deep Archive
        ├── Retrieval Options:
        │   ├── Standard (12 hours)
        │   └── Bulk (48 hours)
        ├── 180-day minimum storage duration
        ├── Lowest cost storage class
        └── Up to 75% less cost than Glacier Flexible
```

#### Lifecycle Management
- Automatically transition objects between storage classes
- Delete objects after specified time
- Rules based on prefixes and tags
- Incomplete multipart upload cleanup
- Current and previous versions management
- Filter by object size

#### Versioning
- Keep multiple versions of objects
- Protect against accidental deletion/modification
- Version ID for each object version
- Can be suspended (not disabled)
- MFA Delete for additional protection
- Lifecycle rules can manage versions

#### Server-Side Encryption
- **SSE-S3**: Amazon S3 managed keys (AES-256)
- **SSE-KMS**: AWS KMS managed keys
  - Additional audit trail via CloudTrail
  - User control over key rotation
  - Can use customer managed keys
- **SSE-C**: Customer provided keys
  - Customer manages encryption keys
  - AWS performs encryption/decryption
- **DSSE-KMS**: Dual-layer encryption with KMS

#### Cross-Region Replication (CRR) & Same-Region Replication (SRR)
- Automatic replication of objects
- Different storage classes for replicas
- Replica Time Control (RTC) for predictable replication
- Replication of delete markers and versions
- Cross-account replication supported
- Bi-directional replication rules

#### Access Control
- **Bucket Policies**: JSON-based resource policies
- **Access Control Lists (ACLs)**: Legacy access control
- **IAM Policies**: User-based permissions
- **Access Points**: Named network endpoints for buckets
- **Object Ownership**: Control object ownership and ACLs
- **Block Public Access**: Account and bucket level settings

#### S3 Transfer Acceleration
- Uses CloudFront edge locations
- Faster uploads to S3 buckets
- Compatible with multipart uploads
- Speed comparison tool available

#### S3 Event Notifications
- Trigger actions when objects are created, deleted, etc.
- Destinations: SNS, SQS, Lambda
- Filter by prefix, suffix, or content type
- EventBridge for advanced routing

#### Multipart Upload
- Upload large objects in parts
- Improved throughput and resilience
- Parallel uploads of parts
- Minimum part size: 5MB (except last part)
- Maximum 10,000 parts per object

#### S3 Batch Operations
- Perform actions on billions of objects
- Built-in operations: copy, tag, ACL, restore
- Custom operations with Lambda
- Job completion reports

#### S3 Object Lock
- Write Once Read Many (WORM) model
- **Governance Mode**: Can't delete with special permissions
- **Compliance Mode**: Can't delete until retention period
- Legal hold for indefinite retention
- Requires versioning enabled

#### S3 Analytics and Insights
- **Storage Class Analysis**: Optimize storage class transitions
- **S3 Inventory**: Report on objects and metadata
- **CloudWatch Metrics**: Monitor request rates and errors
- **Access Logging**: Log requests to bucket
- **CloudTrail**: API-level logging

#### Performance Optimization
- Request rate: 3,500 PUT/COPY/POST/DELETE, 5,500 GET/HEAD per prefix
- Multipart upload for objects >100MB
- Range GETs for large objects
- Use CloudFront for frequently accessed content
- Avoid sequential key names for high request rates

#### S3 Select
- Retrieve subset of object data using SQL
- Works with CSV, JSON, and Parquet
- Reduce data transfer and costs
- Server-side filtering

#### Key Features Summary
- 99.999999999% (11 9's) durability
- Virtually unlimited storage capacity
- Versioning and lifecycle management
- Server-side encryption
- Cross-region replication
- Event notifications and triggers
- Strong consistency model
- Pay-as-you-go pricing

### EBS (Elastic Block Store)
High-performance block storage for EC2 instances.

#### Volume Types
- **gp3 (General Purpose SSD)**: Latest generation
  - 3,000 IOPS baseline, up to 16,000 IOPS
  - 125 MiB/s baseline throughput, up to 1,000 MiB/s
  - Independent IOPS and throughput provisioning
  - 1 GiB - 16 TiB size range
- **gp2 (General Purpose SSD)**: Previous generation
  - 3 IOPS per GiB, burst to 3,000 IOPS
  - Baseline throughput based on volume size
  - 1 GiB - 16 TiB size range
- **io2 (Provisioned IOPS SSD)**: Latest high-performance
  - Up to 64,000 IOPS and 1,000 MiB/s throughput
  - 99.999% durability (higher than other types)
  - Sub-millisecond latency
  - 4 GiB - 16 TiB, up to 500 IOPS per GiB
- **io1 (Provisioned IOPS SSD)**: Previous high-performance
  - Up to 64,000 IOPS and 1,000 MiB/s throughput
  - 4 GiB - 16 TiB, up to 50 IOPS per GiB
- **st1 (Throughput Optimized HDD)**: Low-cost, high throughput
  - Up to 500 MiB/s throughput
  - Burst performance model
  - 125 GiB - 16 TiB, big data and data warehouses
- **sc1 (Cold HDD)**: Lowest cost
  - Up to 250 MiB/s throughput
  - Infrequently accessed data
  - 125 GiB - 16 TiB

#### EBS Snapshots
- Point-in-time snapshots stored in S3
- Incremental backups (only changed blocks)
- Cross-region and cross-account copying
- Fast Snapshot Restore (FSR) for instant access
- EBS Direct APIs for direct snapshot access
- Snapshot lifecycle management with Data Lifecycle Manager
- Recycle Bin for accidental deletion protection

#### Encryption
- **Encryption at Rest**: AES-256 encryption
- **Encryption in Transit**: Between instance and volume
- **Key Management**: AWS managed or customer managed KMS keys
- **Boot Volume Encryption**: Supported for all volume types
- **Snapshot Encryption**: Encrypted volumes create encrypted snapshots
- **Copy and Share**: Encrypted snapshots can be shared across accounts

#### Multi-Attach
- Available for io1 and io2 volumes
- Attach single volume to multiple instances
- All instances must be in same AZ
- Cluster-aware file systems required
- Up to 16 instances per volume

#### Performance Features
- **EBS-Optimized Instances**: Dedicated bandwidth for EBS
- **Nitro System**: Enhanced networking and storage performance
- **NVMe Interface**: Lower latency access
- **CloudWatch Integration**: Detailed monitoring metrics

#### Volume Management
- **Elastic Volumes**: Modify size, performance, and type online
- **Volume Recovery**: Automatic recovery from drive failures
- **Hibernation Support**: Save instance state to EBS root volume
- **Root Volume Replacement**: Replace root volume without stopping instance

#### Data Lifecycle Manager (DLM)
- Automate EBS snapshot creation and deletion
- Schedule-based or event-based policies
- Cross-region copy automation
- Resource tagging for policy targeting
- Integration with Amazon EventBridge

#### EBS Direct APIs
- Direct access to EBS snapshot data
- Read snapshot data without creating volumes
- Incremental data transfer
- Custom backup and disaster recovery solutions

### EFS (Elastic File System)
Fully managed NFS file system for EC2 instances.

#### Benefits
- Scales automatically from gigabytes to petabytes
- Concurrent access from multiple instances
- Regional service spanning multiple AZs
- POSIX-compliant file system

## Database Services

### RDS (Relational Database Service)
Managed relational database service supporting multiple database engines.

📖 **Learn More**: [Amazon RDS Documentation](https://docs.aws.amazon.com/rds/) | [RDS User Guide](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/)

#### Supported Engines

**Amazon Aurora** (MySQL and PostgreSQL compatible)
**Real-World Example**:
```
Your high-traffic e-commerce platform needs maximum performance:
- Aurora MySQL: Handles 100,000+ orders/day with 5x MySQL performance
- Aurora Serverless: Automatically scales during Black Friday traffic spikes
- Aurora Global: Serve customers in US, Europe, Asia with <100ms latency
- Perfect for: High-performance applications, global applications, variable workloads
```

**MySQL** (5.7, 8.0)
**Real-World Example**:
```
Your WordPress blog or content management system:
- Familiar MySQL for developers migrating from on-premises
- Perfect compatibility with existing PHP/WordPress applications
- Cost-effective option: db.t3.micro for $12/month
- Perfect for: Web applications, content management, existing MySQL apps
```

**PostgreSQL** (11, 12, 13, 14, 15)
**Real-World Example**:
```
Your analytics platform with complex queries:
- Advanced JSON support for flexible data structures
- Complex analytical queries with window functions
- ACID compliance for financial applications
- Perfect for: Analytics, geospatial applications, complex data types
```

**Microsoft SQL Server** (2017, 2019, 2022)
**Real-World Example**:
```
Your .NET enterprise application needs SQL Server:
- Migrate existing on-premises SQL Server databases
- Integration with Active Directory authentication
- Business Intelligence and Reporting Services
- Perfect for: .NET applications, enterprise systems, Microsoft ecosystem
```

#### Deployment Options

**Single-AZ**: Single database instance
**Real-World Example**:
```
Your development/testing environment or cost-sensitive application:
- Development team's staging database
- Internal tools with minimal downtime requirements
- Cost savings: 50% less than Multi-AZ
- Perfect for: Dev/test, internal apps, cost optimization
```

**Multi-AZ**: Synchronous replication for high availability
**Real-World Example**:
```
Your production e-commerce database requiring high availability:
- Automatic failover during maintenance or outages (<60 seconds)
- Customer orders never lost due to database failures
- 99.95% availability SLA
- Perfect for: Production systems, customer-facing apps, business-critical data
```

**Multi-AZ Cluster**: Up to 2 readable standby instances
**Real-World Example**:
```
Your analytics platform needing both high availability and read scaling:
- Primary handles writes (orders, user updates)
- 2 standby instances handle read queries (reports, analytics)
- Faster failover (<35 seconds) for better user experience
- Perfect for: Read-heavy workloads, analytics, reporting systems
```

#### Read Replicas
- **Cross-AZ, Cross-Region**: Scale read workloads
- **Asynchronous Replication**: Eventual consistency
- **Multiple Read Replicas**: Up to 5 per source DB (15 for Aurora)
- **Promotion**: Can be promoted to standalone DB
- **Different Instance Types**: Optimize for read workloads

#### Backup and Recovery
- **Automated Backups**: Point-in-time recovery up to 35 days
- **Manual Snapshots**: User-initiated, persist until deleted
- **Cross-Region Backup**: Automated snapshots copied to other regions
- **Backup Retention**: 0-35 days for automated backups
- **Restoration**: Create new DB instance from backup

#### Security Features
- **Encryption at Rest**: KMS encryption for storage and snapshots
- **Encryption in Transit**: SSL/TLS connections
- **Network Isolation**: VPC with subnets and security groups
- **IAM Authentication**: Database access using IAM roles
- **Kerberos Authentication**: For SQL Server and Oracle
- **Transparent Data Encryption (TDE)**: For SQL Server and Oracle

#### Performance Monitoring
- **Enhanced Monitoring**: OS-level metrics via CloudWatch
- **Performance Insights**: Database performance analysis
- **CloudWatch Integration**: Database and OS metrics
- **Database Activity Streams**: Near real-time monitoring

#### Maintenance and Patching
- **Maintenance Windows**: Scheduled maintenance periods
- **Automatic Minor Version Upgrades**: Optional automatic updates
- **Major Version Upgrades**: Manual process with testing
- **Blue/Green Deployments**: Zero-downtime deployments

#### Connection Management
- **RDS Proxy**: Connection pooling and management
  - Reduce connection overhead
  - Improve failover times
  - IAM and Secrets Manager integration
- **Connection Limits**: Based on instance class

#### Parameter Groups
- **DB Parameter Groups**: Engine configuration parameters
- **DB Cluster Parameter Groups**: Aurora cluster settings
- **Custom Parameter Groups**: Customize engine behavior
- **Dynamic vs Static**: Parameters requiring restart

#### Option Groups
- **Engine-Specific Features**: Additional functionality
- **Oracle**: Advanced Security, APEX, OEM
- **SQL Server**: SQL Server Agent, SSIS, SSRS
- **MySQL/MariaDB**: memcached, audit plugins

#### Database Subnet Groups
- **VPC Configuration**: Specify subnets for RDS deployment
- **Multi-AZ Requirements**: Subnets in different AZs
- **Private Subnets**: Database isolation from internet

#### Reserved Instances
- **1 or 3 Year Terms**: Significant cost savings
- **Payment Options**: All upfront, partial upfront, no upfront
- **Instance Flexibility**: Size flexibility within family

#### Aurora Specific Features
- **Storage Auto-Scaling**: Up to 128 TiB automatically
- **Aurora Replicas**: Up to 15 read replicas
- **Custom Endpoints**: Reader and writer endpoints
- **Parallel Query**: Analytical queries on Aurora MySQL
- **Backtrack**: Rewind database to previous time (MySQL)
- **Clone**: Create copy of database quickly and cost-effectively

### DynamoDB
Fully managed NoSQL database service.

**Real-World Example - E-commerce Product Catalog**:
```
Your online store's product database:
- Table: "Products"
- Items: Each product (iPhone, MacBook, etc.)
- Attributes: name, price, category, inventory, description
- Primary Key: ProductID (partition key)
- Perfect for: Product catalogs, user profiles, gaming leaderboards
```

#### Core Concepts
- **Tables**: Collection of items (like rows in relational DB)
- **Items**: Collection of attributes (like rows) 
- **Attributes**: Data elements (like columns)
- **Primary Key**: Partition key or partition key + sort key
- **Secondary Indexes**: Alternative query patterns

#### Billing Modes

**On-Demand**: Pay per request, automatic scaling
**Real-World Example**:
```
Your startup's user activity tracking with unpredictable traffic:
- New mobile app with unknown usage patterns
- Traffic varies from 100 to 10,000 requests/minute
- No capacity planning needed - DynamoDB scales automatically
- Perfect for: New applications, unpredictable workloads, spiky traffic
```

**Provisioned**: Pre-allocated read/write capacity
**Real-World Example**:
```
Your established e-commerce site with predictable patterns:
- 1,000 product lookups/minute during business hours
- 500 order writes/minute during peak times
- Pre-provision capacity: 1,200 read/600 write units
- Perfect for: Steady workloads, cost optimization, predictable traffic
```

#### Primary Key Types
- **Partition Key**: Simple primary key, must be unique
- **Composite Key**: Partition key + sort key combination
  - Multiple items can have same partition key
  - Sort key must be unique within partition

#### Secondary Indexes
- **Global Secondary Index (GSI)**:
  - Different partition key and optional sort key
  - Own provisioned throughput settings
  - Eventually consistent reads
  - Can be created anytime
- **Local Secondary Index (LSI)**:
  - Same partition key, different sort key
  - Shares throughput with base table
  - Strongly consistent reads available
  - Must be created at table creation

#### Consistency Models
- **Eventually Consistent Reads**: Default, may not reflect recent writes
- **Strongly Consistent Reads**: Returns most up-to-date data
- **Transactional Reads**: ACID transactions across items

#### DynamoDB Transactions
- **TransactWriteItems**: Up to 25 items across tables
- **TransactReadItems**: Up to 25 items with snapshot isolation
- **ACID Properties**: Atomicity, Consistency, Isolation, Durability
- **Conditional Operations**: Prevent conflicts

#### DynamoDB Streams
- **Change Data Capture**: Record data modification events
- **Stream Records**: Item-level changes with before/after images
- **Integration**: Lambda, Kinesis Client Library
- **Retention**: 24-hour retention period
- **Shard Management**: Automatic scaling and management

#### Global Tables
- **Multi-Region Replication**: Active-active replication
- **Last Writer Wins**: Conflict resolution
- **Eventually Consistent**: Cross-region consistency
- **Local Read/Write**: Low latency access globally

#### Backup and Restore
- **On-Demand Backup**: Full table backups
- **Point-in-Time Recovery (PITR)**: Restore to any second in last 35 days
- **Cross-Region Backup**: Copy backups to other regions
- **Automatic Backups**: Continuous backups with PITR

#### DynamoDB Accelerator (DAX)
- **In-Memory Cache**: Microsecond latency for reads
- **Write-Through Cache**: Automatic cache population
- **Multi-AZ**: High availability across AZs
- **Encryption**: At rest and in transit
- **VPC Endpoints**: Private connectivity

#### Security Features
- **Encryption at Rest**: KMS managed keys
- **Encryption in Transit**: HTTPS/TLS
- **IAM Integration**: Fine-grained access control
- **VPC Endpoints**: Private network access
- **Resource-Based Policies**: Cross-account access
- **CloudTrail Integration**: API call logging

#### Performance Features
- **Auto Scaling**: Automatic capacity adjustment
- **Burst Capacity**: Handle traffic spikes
- **Adaptive Capacity**: Distribute hot partitions
- **Contributor Insights**: Identify most accessed items

#### Data Types
- **Scalar**: String, Number, Binary, Boolean, Null
- **Document**: List, Map
- **Set**: String Set, Number Set, Binary Set
- **TTL**: Time to Live for automatic item expiration

#### Query Operations
- **GetItem**: Retrieve single item by primary key
- **Query**: Find items with same partition key
- **Scan**: Examine every item in table
- **BatchGetItem**: Retrieve up to 100 items
- **BatchWriteItem**: Put or delete up to 25 items

#### Conditional Operations
- **Condition Expressions**: Prevent overwrites
- **Update Expressions**: Modify attributes atomically
- **Projection Expressions**: Return only specified attributes
- **Filter Expressions**: Filter results after query/scan

#### PartiQL Support
- **SQL-Like Queries**: Familiar syntax for DynamoDB
- **Select, Insert, Update, Delete**: Standard SQL operations
- **Batch Operations**: Multiple statements in single request

#### Key Features Summary
- Single-digit millisecond performance at any scale
- Automatic scaling based on traffic patterns
- Built-in security, backup, and restore
- Global tables for multi-region deployment
- On-demand and provisioned billing modes
- Serverless architecture
- Integration with AWS services

### ElastiCache
In-memory caching service supporting Redis and Memcached.

## Networking

### CloudFront
Global content delivery network (CDN) service.

#### Benefits
- Low latency content delivery
- DDoS protection
- SSL/TLS encryption
- Integration with AWS services
- Custom domain support

### Route 53
Scalable domain name system (DNS) web service.

#### Features
- Domain registration
- DNS routing
- Health checking
- Traffic flow (visual editor for routing)
- Resolver (hybrid DNS)

### Direct Connect
Dedicated network connection from on-premises to AWS.

#### Benefits
- Consistent network performance
- Reduced bandwidth costs
- Private connectivity to VPC
- Support for VLANs

## Security

### AWS Shield
DDoS protection service.

#### Tiers
- **Standard**: Automatic protection against common attacks (free)
- **Advanced**: Enhanced protection and 24/7 DDoS response team

### WAF (Web Application Firewall)
Protects web applications from common web exploits.

#### Features
- SQL injection protection
- Cross-site scripting (XSS) protection
- Rate limiting
- Geographic blocking
- Custom rules

### KMS (Key Management Service)
Managed service for creating and managing encryption keys.

#### Key Types
- **AWS Managed Keys**: Created and managed by AWS services
  - Free to use, automatic rotation
  - Key policy managed by AWS
  - Cannot be deleted or disabled
- **Customer Managed Keys**: Created and managed by you
  - Full control over key policy and usage
  - Manual or automatic rotation (annual)
  - Can be deleted or disabled
- **AWS Owned Keys**: Used by AWS services, not visible to customers
  - No additional charges
  - Used for default encryption

#### Key Specifications
- **Symmetric Keys**: Single key for encrypt/decrypt (AES-256)
- **Asymmetric Keys**: Key pairs for encrypt/decrypt or sign/verify
  - RSA key pairs (2048, 3072, 4096 bits)
  - Elliptic curve key pairs (ECC_NIST_P256, ECC_NIST_P384, ECC_NIST_P521)
  - ECDSA and RSA key pairs for signing

#### Key Usage
- **ENCRYPT_DECRYPT**: Symmetric encryption operations
- **SIGN_VERIFY**: Asymmetric signing operations
- **Key Usage Policies**: Control how keys can be used

#### Key Policies
- **JSON-based Policies**: Define who can use and manage keys
- **Principal**: AWS accounts, IAM users/roles, AWS services
- **Actions**: kms:Encrypt, kms:Decrypt, kms:GenerateDataKey
- **Resources**: Key ARNs and aliases
- **Conditions**: Additional context for access control

#### Key Rotation
- **Automatic Rotation**: Annual rotation for customer managed keys
- **Manual Rotation**: Create new key version manually
- **Rotation History**: Track key versions and rotation dates
- **Backward Compatibility**: Old versions remain for decryption

#### Data Keys
- **GenerateDataKey**: Create data encryption key
- **Encrypt/Decrypt Data**: Use data keys for client-side encryption
- **Data Key Caching**: Improve performance with AWS Encryption SDK
- **Envelope Encryption**: Encrypt data keys with KMS keys

#### Multi-Region Keys
- **Primary Key**: Original key in home region
- **Replica Keys**: Copies in other regions
- **Synchronized**: Same key material and key ID
- **Independent Policies**: Different key policies per region
- **Disaster Recovery**: Failover capabilities

#### Key Stores
- **AWS KMS**: Default key store with AWS managed HSMs
- **Custom Key Store**: Your own CloudHSM cluster
  - Dedicated HSM for your keys
  - Single-tenant key storage
  - Your own HSM certificates

#### Integration with AWS Services
- **S3**: Server-side encryption with KMS keys
- **EBS**: Volume and snapshot encryption
- **RDS**: Database encryption at rest
- **Lambda**: Environment variable encryption
- **Secrets Manager**: Secret encryption
- **Parameter Store**: Secure string parameters

#### Monitoring and Auditing
- **CloudTrail Integration**: Log all KMS API calls
- **CloudWatch Metrics**: Key usage metrics
- **Key Usage Grants**: Delegate key usage permissions
- **Grant Tokens**: Temporary access for key operations

#### Import Your Own Key (BYOK)
- **Key Material Import**: Import your own key material
- **Key Wrapping**: Encrypt key material for import
- **Expiration**: Set expiration for imported keys
- **Delete Key Material**: Remove imported key material

#### Cross-Account Access
- **Key Policies**: Grant access to other AWS accounts
- **Cross-Account Grants**: Programmatic access delegation
- **ViaService Condition**: Restrict access through specific services

#### Features Summary
- Hardware security modules (HSMs)
- Audit key usage with CloudTrail
- Integration with AWS services
- Customer managed keys
- AWS managed keys
- Automatic and manual key rotation
- Multi-region key replication
- Fine-grained access control

### Secrets Manager
Managed service for storing and retrieving secrets.

#### Benefits
- Automatic rotation of secrets
- Fine-grained access control
- Audit secret access
- Integration with RDS and other services

## Monitoring and Logging

### CloudWatch
Monitoring and observability service.

#### CloudWatch Metrics
- **Basic Monitoring**: 5-minute intervals (free)
- **Detailed Monitoring**: 1-minute intervals (additional cost)
- **Custom Metrics**: Application and business metrics
- **High-Resolution Metrics**: Sub-minute intervals (1 second)
- **Metric Filters**: Extract metrics from log data
- **Composite Alarms**: Combine multiple alarms

#### CloudWatch Logs
- **Log Groups**: Container for log streams
- **Log Streams**: Sequence of log events from same source
- **Log Events**: Timestamped messages
- **Retention Settings**: 1 day to 10 years, never expire
- **Log Insights**: Interactive log analytics
- **Subscription Filters**: Real-time log processing
- **Cross-Account Log Sharing**: Share logs across accounts

#### CloudWatch Alarms
- **Metric Alarms**: Based on metric values
- **Composite Alarms**: Combine multiple alarms with logic
- **Alarm States**: OK, ALARM, INSUFFICIENT_DATA
- **Alarm Actions**: SNS, Auto Scaling, EC2 actions
- **Alarm History**: Track state changes
- **Missing Data Treatment**: How to handle missing data points

#### CloudWatch Events (EventBridge)
- **Event Sources**: AWS services, custom applications
- **Event Rules**: Match events and route to targets
- **Event Targets**: Lambda, SNS, SQS, Kinesis, etc.
- **Custom Events**: Send custom events to EventBridge
- **Scheduled Events**: Cron-like scheduling
- **Cross-Account Events**: Share events across accounts

#### CloudWatch Dashboards
- **Widgets**: Visualize metrics and logs
- **Widget Types**: Line, stacked area, number, text
- **Custom Dashboards**: Organize widgets by application/service
- **Sharing**: Share dashboards with other users
- **Auto-refresh**: Automatic dashboard updates

#### CloudWatch Synthetics
- **Canaries**: Monitor endpoints and APIs
- **Runtime Versions**: Node.js and Python
- **Blueprint Canaries**: Pre-built monitoring scripts
- **Visual Monitoring**: Screenshot comparison
- **Performance Metrics**: Load time, availability

#### CloudWatch Application Insights
- **Application Monitoring**: Monitor .NET and SQL Server apps
- **Automated Setup**: Discover and configure monitoring
- **Problem Detection**: ML-powered anomaly detection
- **Root Cause Analysis**: Correlate metrics and logs

#### CloudWatch Container Insights
- **ECS and EKS Monitoring**: Container and cluster metrics
- **Performance Monitoring**: CPU, memory, network, storage
- **Log Aggregation**: Container logs in CloudWatch Logs
- **Built-in Dashboards**: Pre-configured monitoring views

#### CloudWatch Lambda Insights
- **Lambda Function Monitoring**: Performance and cost optimization
- **Cold Start Detection**: Identify initialization latency
- **Memory Usage**: Right-size function memory
- **Duration and Cost**: Track execution metrics

#### Components Summary
- **Metrics**: Performance data from AWS services
- **Logs**: Centralized log management
- **Alarms**: Notifications based on metric thresholds
- **Events**: React to changes in AWS resources
- **Dashboards**: Customizable monitoring views
- **X-Ray Integration**: Distributed request tracing
- **Service Lens**: Service map and traces

### CloudTrail
Service for governance, compliance, and audit of AWS account activity.

#### Features
- Records API calls made in your AWS account
- File integrity validation
- Multi-region logging
- Integration with CloudWatch Logs

### AWS Config
Service for assessing, auditing, and evaluating AWS resource configurations.

#### Use Cases
- Compliance monitoring
- Security analysis
- Change tracking
- Troubleshooting

## DevOps and CI/CD

### CodeCommit
Fully managed source control service hosting Git repositories.

### CodeBuild
Fully managed continuous integration service.

#### Features
- Scales automatically
- Pay only for build time
- Supports multiple programming languages
- Integration with other AWS services

### CodeDeploy
Automated deployment service for applications.

#### Deployment Types
- In-place: Updates instances with new application version
- Blue/green: Deploys to new instances, then shifts traffic

### CodePipeline
Continuous integration and continuous delivery service.

#### Benefits
- Visual workflow for release process
- Integration with third-party tools
- Parallel execution of actions
- Automated testing and deployment

### CloudFormation
Infrastructure as Code service using templates.

#### Template Components
- **AWSTemplateFormatVersion**: Template format version
- **Description**: Template description
- **Parameters**: Input values for template
- **Mappings**: Static lookup tables
- **Conditions**: Control resource creation
- **Resources**: AWS resources to create (required)
- **Outputs**: Return values from stack
- **Metadata**: Additional information about template

#### Template Formats
- **JSON**: JavaScript Object Notation
- **YAML**: YAML Ain't Markup Language (more readable)

#### Stack Operations
- **Create Stack**: Deploy resources from template
- **Update Stack**: Modify existing resources
- **Delete Stack**: Remove all stack resources
- **Change Sets**: Preview changes before applying

#### Stack Sets
- Deploy stacks across multiple accounts and regions
- Centralized management of infrastructure
- Service-managed and self-managed permissions
- Automatic deployment to new accounts (Organizations integration)

#### Drift Detection
- Compare actual resource configuration with template
- Identify manual changes to resources
- Stack-level and resource-level drift information
- Integration with AWS Config

#### Nested Stacks
- Reference other CloudFormation templates
- Modular and reusable template components
- Pass parameters between parent and child stacks
- Cross-stack references with Outputs/Imports

#### Custom Resources
- Extend CloudFormation with custom logic
- Lambda-backed or SNS-backed custom resources
- Handle resources not natively supported
- Custom resource lifecycle (Create, Update, Delete)

#### CloudFormation Registry
- Third-party resource types and modules
- AWS Public Extensions and Partner Extensions
- Private extensions for organization
- Resource type versioning

#### CloudFormation Hooks
- **CreationPolicy**: Wait for resource signals
- **UpdatePolicy**: Control rolling updates
- **DeletionPolicy**: Protect resources from deletion
- **UpdateReplacePolicy**: Control replacement behavior

#### Intrinsic Functions
- **Ref**: Reference parameters and resources
- **Fn::GetAtt**: Get resource attributes
- **Fn::Join**: Concatenate strings
- **Fn::Sub**: Substitute variables in strings
- **Fn::If**: Conditional resource creation
- **Fn::ImportValue**: Cross-stack references

#### Benefits
- Version control infrastructure
- Rollback on failure
- Drift detection
- Cross-region deployment
- Cost estimation before deployment
- Dependency management
- Repeatable infrastructure deployment

## Container and Orchestration Services

### Amazon ECR (Elastic Container Registry)
Fully managed Docker container registry.

#### Key Features
- **Private Repositories**: Secure container image storage
- **Image Scanning**: Vulnerability scanning with Inspector
- **Lifecycle Policies**: Automated image cleanup
- **Cross-Region Replication**: Multi-region image distribution
- **OCI Artifact Support**: Store any OCI-compatible artifacts
- **Immutable Tags**: Prevent tag overwriting

### AWS Batch
Fully managed batch computing service.

#### Components
```
AWS Batch
├── Job Definitions
│   ├── Container Properties
│   ├── Resource Requirements
│   └── Job Parameters
├── Job Queues
│   ├── Priority Levels
│   ├── Compute Environment Association
│   └── Job Scheduling
├── Compute Environments
│   ├── Managed Compute
│   │   ├── EC2 (On-Demand/Spot)
│   │   └── Fargate
│   └── Unmanaged Compute
│       └── Custom EC2 Instances
└── Jobs
    ├── Job Submission
    ├── Dependency Management
    └── Array Jobs
```

## Data and Analytics Services

### AWS Glue
Serverless data integration service.

#### Core Components
- **Data Catalog**: Centralized metadata repository
- **ETL Jobs**: Serverless data transformation
- **Crawlers**: Automatic schema discovery
- **Data Quality**: Data validation and monitoring
- **DataBrew**: Visual data preparation

### Amazon EMR (Elastic MapReduce)
Big data platform for processing large datasets.

#### Supported Frameworks
- Apache Spark, Hadoop, HBase, Presto, Flink
- Jupyter Notebooks, Zeppelin, Livy
- Custom applications and frameworks

## Additional Services

### AWS Systems Manager
Operational data and automation across AWS resources.

#### Key Components
- **Session Manager**: Browser-based shell access to instances
- **Parameter Store**: Secure storage for configuration data
  - String, StringList, SecureString parameters
  - Parameter hierarchies and policies
  - Integration with KMS for encryption
- **Patch Manager**: Automate OS and software patching
- **Run Command**: Execute commands remotely on instances
- **State Manager**: Maintain consistent configuration
- **Inventory**: Collect metadata about instances
- **Maintenance Windows**: Schedule maintenance tasks
- **Automation**: Simplify maintenance and deployment

### AWS Secrets Manager
Manage, retrieve, and rotate database credentials and other secrets.

#### Secret Types and Rotation
```
AWS Secrets Manager
├── Database Secrets
│   ├── RDS (MySQL, PostgreSQL, SQL Server)
│   │   ├── Automatic Rotation (30-365 days)
│   │   ├── Lambda-based Rotation
│   │   └── Multi-User Rotation Strategy
│   ├── DocumentDB
│   │   ├── MongoDB-compatible rotation
│   │   └── Replica set support
│   └── Redshift
│       ├── Cluster and user rotation
│       └── JDBC connection strings
├── Other Secrets
│   ├── API Keys and Tokens
│   ├── SSH Keys and Certificates
│   ├── Application Configuration
│   └── Third-party Service Credentials
├── Access Control
│   ├── IAM Policies
│   ├── Resource-based Policies
│   ├── VPC Endpoints
│   └── Cross-account Access
└── Integration
    ├── AWS SDKs
    ├── Parameter Store Integration
    ├── CloudFormation Dynamic References
    └── Kubernetes Secrets Store CSI Driver
```

#### Advanced Features
- **Automatic Rotation**: Built-in rotation for RDS, DocumentDB, Redshift
- **Cross-Region Replication**: Replicate secrets across regions
- **Fine-Grained Access Control**: IAM and resource-based policies
- **VPC Endpoints**: Private network access
- **Lambda Integration**: Custom rotation functions
- **CloudFormation Integration**: Manage secrets as code
- **Versioning**: Track secret changes and rollback
- **Replica Secrets**: Read-only replicas in different regions

### Amazon EventBridge (CloudWatch Events)
Serverless event routing service.

#### Core Concepts
- **Event Buses**: Receive and route events
- **Rules**: Match events and send to targets
- **Targets**: Destinations for matched events
- **Event Sources**: AWS services, SaaS applications, custom apps
- **Event Patterns**: JSON patterns to match events
- **Scheduled Rules**: Cron-like scheduling

### AWS Step Functions
Orchestrate distributed applications using visual workflows.

#### State Types
- **Task**: Execute work (Lambda, ECS, SNS, etc.)
- **Choice**: Branch based on input
- **Wait**: Delay for specified time
- **Succeed/Fail**: End execution with status
- **Parallel**: Execute branches in parallel
- **Map**: Process array of items

### Amazon SNS (Simple Notification Service)
Pub/sub messaging for decoupling applications.

#### Features
- **Topics**: Communication channels for messages
- **Subscriptions**: Endpoints that receive messages
- **Message Filtering**: Deliver subset of messages
- **Message Attributes**: Metadata for messages
- **Dead Letter Queues**: Handle delivery failures
- **FIFO Topics**: Ordered message delivery

### Amazon SQS (Simple Queue Service)
Managed message queuing service.

#### Queue Types
- **Standard Queues**: At-least-once delivery, best-effort ordering
- **FIFO Queues**: Exactly-once processing, first-in-first-out

#### Features
- **Visibility Timeout**: Hide messages during processing
- **Dead Letter Queues**: Handle processing failures
- **Long Polling**: Reduce empty responses
- **Message Deduplication**: Prevent duplicate processing
- **Batch Operations**: Send/receive/delete multiple messages

## Tool Integration with AWS

AWS works seamlessly with popular DevOps tools for complete infrastructure automation:

### Infrastructure as Code Integration
🔗 **Related**: See [Terraform Essential Guide](./TERRAFORM.md) for infrastructure automation
- **Terraform**: Provision and manage AWS resources declaratively
- **AWS CloudFormation**: Native IaC service for AWS resources
- **Pulumi**: Multi-language infrastructure as code platform
- **AWS CDK**: Define cloud infrastructure using programming languages

### Configuration Management
🔗 **Related**: See [Ansible Essential Guide](./ANSIBLE.md) for configuration automation
- **Ansible**: Configure EC2 instances and manage applications
- **AWS Systems Manager**: Native configuration management and patching
- **Chef/Puppet**: Traditional configuration management on AWS infrastructure

### Container Orchestration  
🔗 **Related**: See [Kubernetes Essential Guide](./KUBERNETES.md) for container management
- **Amazon EKS**: Managed Kubernetes service on AWS
- **Amazon ECS**: Native container orchestration service
- **AWS Fargate**: Serverless container compute engine

### DevOps Workflow Integration
```
1. Code → Version Control (Git)
2. Infrastructure → Terraform/CloudFormation  
3. Configuration → Ansible/Systems Manager
4. Applications → EKS/ECS/Lambda
5. Monitoring → CloudWatch/X-Ray
6. Security → IAM/Security Hub/GuardDuty
```

### Best Practice Integration Patterns
💡 **Recommended Stack**:
- **Development**: Use Terraform for infrastructure, Ansible for configuration, EKS for applications
- **CI/CD**: Integrate with CodePipeline, GitHub Actions, or Jenkins
- **Monitoring**: Combine CloudWatch with Prometheus/Grafana for comprehensive observability
- **Security**: Layer IAM, Security Groups, WAF, and third-party tools for defense in depth

⚠️ **Integration Considerations**:
- Use AWS managed services when possible to reduce operational overhead
- Implement proper IAM roles for service-to-service authentication
- Consider AWS service limits and quotas when designing integrations
- Plan for cross-region scenarios and disaster recovery

## Best Practices

### Security
- **Identity and Access**:
  - Enable MFA for all users, especially root account
  - Use IAM roles instead of sharing credentials
  - Implement least privilege principle
  - Regularly audit permissions with Access Analyzer
  - Use temporary credentials and assume roles
- **Data Protection**:
  - Encrypt data at rest and in transit
  - Use KMS for key management
  - Enable VPC Flow Logs and CloudTrail
  - Implement network segmentation with security groups
- **Infrastructure Security**:
  - Keep systems patched and updated
  - Use AWS Systems Manager for patch management
  - Implement defense in depth
  - Regular security assessments

### Cost Optimization
- **Right Sizing**:
  - Use appropriate instance types and sizes
  - Monitor utilization with CloudWatch
  - Use AWS Compute Optimizer recommendations
- **Purchase Options**:
  - Use Reserved Instances for predictable workloads
  - Leverage Spot Instances for fault-tolerant workloads
  - Consider Savings Plans for flexible commitments
- **Resource Management**:
  - Implement auto-scaling
  - Schedule resources (start/stop based on usage)
  - Monitor and optimize unused resources
  - Use lifecycle policies for storage
  - Set up billing alerts and budgets

### High Availability and Disaster Recovery
- **Multi-AZ Deployment**:
  - Deploy across multiple Availability Zones
  - Use Auto Scaling Groups for resilience
  - Implement health checks and automated recovery
- **Backup and Recovery**:
  - Regular automated backups
  - Cross-region backup replication
  - Test disaster recovery procedures
  - Document RTO and RPO requirements
- **Design Patterns**:
  - Design for failure
  - Use managed services when possible
  - Implement circuit breakers and retries
  - Graceful degradation strategies

### Performance Optimization
- **Content Delivery**:
  - Use CloudFront for global content delivery
  - Implement caching strategies (ElastiCache)
  - Optimize images and static content
- **Database Performance**:
  - Use read replicas for read-heavy workloads
  - Implement connection pooling (RDS Proxy)
  - Choose appropriate database engines and sizes
- **Compute Optimization**:
  - Choose appropriate instance types
  - Use placement groups for high network performance
  - Monitor application performance with X-Ray
  - Optimize Lambda function memory and timeout

### Operational Excellence
- **Automation**:
  - Automate operational procedures
  - Use Infrastructure as Code (CloudFormation, CDK)
  - Implement CI/CD pipelines
  - Automate testing and deployment
- **Monitoring and Observability**:
  - Comprehensive monitoring with CloudWatch
  - Centralized logging and log analysis
  - Set up appropriate alarms and notifications
  - Use distributed tracing for microservices
- **Change Management**:
  - Make frequent, small, reversible changes
  - Use blue/green and canary deployments
  - Implement proper testing strategies
  - Document procedures and runbooks
- **Learning and Improvement**:
  - Conduct post-incident reviews
  - Share knowledge across teams
  - Regular architecture reviews
  - Stay updated with AWS best practices

## Real-World Production Architecture

### Enterprise E-Commerce Platform on AWS

This production architecture demonstrates how all the essential AWS components work together in a real-world, high-scale e-commerce platform serving millions of users globally.

#### Architecture Overview

```
Enterprise E-Commerce Platform Architecture
┌─────────────────────────────────────────────────────────────────────┐
│                            Global Layer                              │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │   Route 53 (DNS)  │───┼───│ CloudFront (CDN) │───┼───│ WAF + Shield │  │
│  │   - Health Checks  │   │   │   - Static Assets   │   │   │   - DDoS Protect│  │
│  │   - Failover       │   │   │   - Edge Caching    │   │   │   - Rate Limiting│  │
│  └───────────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│              Primary Region (us-east-1) - Production               │
├─────────────────────────────────────────────────────────────────────┤
│                            VPC (10.0.0.0/16)                       │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                    Public Subnets (DMZ)                     │  │
│  │                                                               │  │
│  │  AZ-1a: 10.0.1.0/24    AZ-1b: 10.0.2.0/24    AZ-1c: 10.0.3.0/24 │  │
│  │  ┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐ │  │
│  │  │ ALB (Primary)     │  │ ALB (Secondary)   │  │ NAT Gateway      │ │  │
│  │  │ - SSL Termination │  │ - Health Checks   │  │ - Outbound Access │ │  │
│  │  │ - Path Routing    │  │ - Auto Scaling    │  │ - HA Deployment   │ │  │
│  │  └───────────────────┘  └───────────────────┘  └───────────────────┘ │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                   │                               │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                   Private Subnets (App Tier)                │  │
│  │                                                               │  │
│  │  AZ-1a: 10.0.10.0/24   AZ-1b: 10.0.20.0/24   AZ-1c: 10.0.30.0/24│  │
│  │                                                               │  │
│  │  ┌─────────────────────────────────────────────────────────┐  │  │
│  │  │                EKS Cluster (Kubernetes)                  │  │  │
│  │  │                                                         │  │  │
│  │  │  ┌───────────────────┐  ┌───────────────────┐  │  │  │
│  │  │  │ Web App Pods       │  │ API Service Pods   │  │  │  │
│  │  │  │ - React Frontend   │  │ - Node.js/Python   │  │  │  │
│  │  │  │ - NGINX Ingress    │  │ - REST APIs        │  │  │  │
│  │  │  │ - HPA Enabled      │  │ - Service Mesh     │  │  │  │
│  │  │  └───────────────────┘  └───────────────────┘  │  │  │
│  │  │                                                         │  │  │
│  │  │  ┌───────────────────┐  ┌───────────────────┐  │  │  │
│  │  │  │ Worker Pods        │  │ Cache Layer        │  │  │  │
│  │  │  │ - Background Jobs  │  │ - Redis Cluster    │  │  │  │
│  │  │  │ - Queue Processing │  │ - Session Store    │  │  │  │
│  │  │  │ - Cron Jobs        │  │ - ElastiCache      │  │  │  │
│  │  │  └───────────────────┘  └───────────────────┘  │  │  │
│  │  └─────────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────┘  │
│                                   │                               │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │                   Private Subnets (Data Tier)               │  │
│  │                                                               │  │
│  │  AZ-1a: 10.0.100.0/24  AZ-1b: 10.0.200.0/24  AZ-1c: 10.0.300.0/24│  │
│  │                                                               │  │
│  │  ┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐ │  │
│  │  │ RDS Aurora (MySQL) │  │ DynamoDB Tables   │  │ OpenSearch      │ │  │
│  │  │ - Multi-AZ Cluster │  │ - Product Catalog │  │ - Search Index   │ │  │
│  │  │ - Read Replicas    │  │ - User Sessions   │  │ - Log Analytics  │ │  │
│  │  │ - Automated Backup │  │ - Shopping Carts  │  │ - Multi-AZ       │ │  │
│  │  └───────────────────┘  └───────────────────┘  └───────────────────┘ │  │
│  └───────────────────────────────────────────────────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│                    Serverless & Storage Layer                     │
│                                                                     │
│  ┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐  │
│  │ Lambda Functions    │  │ S3 Buckets          │  │ SQS/SNS Queues      │  │
│  │ - Image Processing  │  │ - Static Assets     │  │ - Event Processing  │  │
│  │ - Order Processing  │  │ - User Uploads      │  │ - Async Jobs        │  │
│  │ - Email Notifications│  │ - Backup Storage    │  │ - Dead Letter Q     │  │
│  │ - Payment Webhooks  │  │ - Data Lake         │  │ - Fan-out Patterns  │  │
│  └───────────────────┘  └───────────────────┘  └───────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│               Monitoring, Security & DevOps Layer                 │
│                                                                     │
│  ┌───────────────────┐  ┌───────────────────┐  ┌───────────────────┐  │
│  │ CloudWatch Suite    │  │ Security Services   │  │ DevOps Pipeline     │  │
│  │ - Metrics & Logs    │  │ - IAM Roles/Policies│  │ - CodeCommit        │  │
│  │ - X-Ray Tracing     │  │ - KMS Encryption    │  │ - CodeBuild         │  │
│  │ - Alarms & Dashboards│  │ - Secrets Manager   │  │ - CodeDeploy        │  │
│  │ - Container Insights│  │ - GuardDuty/Macie   │  │ - CodePipeline      │  │
│  └───────────────────┘  └───────────────────┘  └───────────────────┘  │
├─────────────────────────────────────────────────────────────────────┤
│            Secondary Region (us-west-2) - Disaster Recovery         │
│                                                                     │
│  ┌───────────────────────────────────────────────────────────────┐  │
│  │              Warm Standby Infrastructure                    │  │
│  │                                                               │  │
│  │  • RDS Cross-Region Read Replicas (Promoted on Failover)      │  │
│  │  • S3 Cross-Region Replication (Automatic)                   │  │
│  │  • Lambda Functions (Deployed via CodePipeline)               │  │
│  │  • EKS Cluster (Scaled down, can be scaled up)                │  │
│  │  • DynamoDB Global Tables (Active-Active Replication)         │  │
│  │  • Route 53 Health Checks (Automatic Failover)                │  │
│  └───────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────┘
```

#### Architecture Components Breakdown

##### **1. Global Layer**
```
Global Services Integration
├── Route 53 (DNS Management)
│   ├── Health Checks: Multi-region endpoint monitoring
│   ├── Failover Routing: Automatic DR region switching
│   ├── Geolocation Routing: Route users to closest region
│   └── Weighted Routing: A/B testing and gradual rollouts
├── CloudFront (CDN)
│   ├── 400+ Edge Locations: Global content delivery
│   ├── Static Asset Caching: Images, CSS, JS files
│   ├── Dynamic Content Acceleration: API response caching
│   └── Lambda@Edge: Edge computing for personalization
└── AWS WAF + Shield
    ├── DDoS Protection: Layer 3/4 and Layer 7 attacks
    ├── Rate Limiting: API and web request throttling
    ├── Geo-blocking: Country-based access control
    └── Custom Rules: SQL injection, XSS protection
```

##### **2. Network Infrastructure**
```
VPC Network Design (us-east-1)
├── VPC CIDR: 10.0.0.0/16 (65,536 IP addresses)
├── Public Subnets (DMZ Layer)
│   ├── us-east-1a: 10.0.1.0/24 (254 IPs)
│   ├── us-east-1b: 10.0.2.0/24 (254 IPs)
│   ├── us-east-1c: 10.0.3.0/24 (254 IPs)
│   ├── Resources: ALB, NAT Gateway, Bastion Hosts
│   └── Internet Gateway: 0.0.0.0/0 route
├── Private Subnets (Application Layer)
│   ├── us-east-1a: 10.0.10.0/24 (254 IPs)
│   ├── us-east-1b: 10.0.20.0/24 (254 IPs)
│   ├── us-east-1c: 10.0.30.0/24 (254 IPs)
│   ├── Resources: EKS Nodes, Application Servers
│   └── NAT Gateway: 0.0.0.0/0 route
└── Private Subnets (Database Layer)
    ├── us-east-1a: 10.0.100.0/24 (254 IPs)
    ├── us-east-1b: 10.0.200.0/24 (254 IPs)
    ├── us-east-1c: 10.0.300.0/24 (254 IPs)
    ├── Resources: RDS, DynamoDB, ElastiCache
    └── No Internet Routes: Maximum security
```

##### **3. Compute and Container Platform**
```
EKS Cluster Architecture
├── Control Plane (AWS Managed)
│   ├── Kubernetes Version: 1.28
│   ├── Multi-AZ Deployment: 3 AZs for HA
│   ├── Endpoint Access: Private + Public with CIDR restrictions
│   └── Audit Logging: CloudWatch Logs integration
├── Managed Node Groups
│   ├── Web Tier: t3.large (2 vCPU, 8GB RAM)
│   │   ├── Min Size: 3, Max Size: 20, Desired: 6
│   │   ├── Auto Scaling: CPU > 70%, Memory > 80%
│   │   └── Workloads: React apps, NGINX ingress
│   ├── API Tier: m5.xlarge (4 vCPU, 16GB RAM)
│   │   ├── Min Size: 2, Max Size: 15, Desired: 4
│   │   ├── Auto Scaling: Custom metrics (requests/sec)
│   │   └── Workloads: Node.js APIs, Python services
│   └── Worker Tier: c5.large (2 vCPU, 4GB RAM)
│       ├── Min Size: 1, Max Size: 10, Desired: 2
│       ├── Auto Scaling: Queue depth based
│       └── Workloads: Background jobs, cron tasks
└── Add-ons and Tools
    ├── AWS Load Balancer Controller: ALB integration
    ├── Cluster Autoscaler: Node scaling
    ├── Metrics Server: HPA/VPA support
    ├── Container Insights: CloudWatch monitoring
    └── AWS EBS CSI Driver: Persistent volume support
```

#### Operational Characteristics

##### **Performance Metrics**
- **Response Time**: < 200ms API response time (99th percentile)
- **Throughput**: 50,000 requests/minute peak capacity
- **Availability**: 99.99% uptime SLA (4.38 minutes downtime/month)
- **Scalability**: Auto-scale from 100 to 10,000+ concurrent users
- **Global Reach**: < 100ms response time worldwide via CloudFront

##### **Security Implementation**
- **Zero Trust Architecture**: Every request authenticated and authorized
- **Encryption**: End-to-end encryption (TLS 1.3, KMS keys)
- **Network Security**: Private subnets, NACLs, security groups
- **Access Control**: IAM roles, least privilege principle
- **Monitoring**: CloudTrail, GuardDuty, Security Hub integration

##### **Disaster Recovery Strategy**
- **RTO (Recovery Time Objective)**: 4 hours
- **RPO (Recovery Point Objective)**: 15 minutes
- **Strategy**: Warm standby in us-west-2
- **Failover**: Automated via Route 53 health checks
- **Data Replication**: Cross-region read replicas, S3 CRR

This production architecture demonstrates how AWS services integrate to create a scalable, secure, and highly available e-commerce platform capable of handling millions of users while maintaining operational excellence and cost efficiency.