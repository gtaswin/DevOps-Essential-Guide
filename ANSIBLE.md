<div align="center">

# 🔴 Ansible Essential Guide

<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/ansible/ansible-original-wordmark.svg" alt="Ansible" width="200"/>

*Comprehensive guide to configuration management, automation, and deployment strategies*

[![Documentation](https://img.shields.io/badge/Ansible-Documentation-EE0000?logo=ansible&logoColor=white)](https://docs.ansible.com/ansible/latest/)
[![Galaxy](https://img.shields.io/badge/Ansible-Galaxy-EE0000?logo=ansible&logoColor=white)](https://galaxy.ansible.com/)

</div>

## Table of Contents
- [Core Concepts](#core-concepts)
- [Architecture](#architecture)
- [Inventory Management](#inventory-management)
- [Playbooks and Tasks](#playbooks-and-tasks)
- [Modules](#modules)
- [Variables and Facts](#variables-and-facts)
- [Templates and Files](#templates-and-files)
- [Handlers](#handlers)
- [Roles](#roles)
- [Best Practices](#best-practices)
- [Real-World Production Example](#real-world-production-example)

## Core Concepts

### What is Ansible?
Ansible is an open-source automation platform that automates configuration management, application deployment, intraservice orchestration, and provisioning. It uses a simple, human-readable language (YAML) and operates agentlessly over SSH.

### Key Benefits
- **Agentless Architecture**: No software installation required on target systems
- **Simple Syntax**: Uses YAML for readable automation scripts
- **Idempotent Operations**: Safe to run multiple times without side effects
- **Push-Based Model**: Control node pushes configuration to managed nodes
- **Extensive Module Library**: 3000+ built-in modules for various tasks
- **Cross-Platform**: Supports Linux, Windows, network devices, and cloud platforms

💡 **Tip**: Ansible's biggest advantage is its simplicity - if you can write a YAML file and understand basic Linux commands, you can start automating immediately.

📖 **Learn More**: [Ansible Documentation](https://docs.ansible.com/ansible/latest/) | [Getting Started Guide](https://docs.ansible.com/ansible/latest/getting_started/index.html)

### Ansible vs Other Tools
```
Configuration Management Comparison
├── Ansible (Push-based, Agentless)
│   ├── No agents on target systems
│   ├── SSH/WinRM communication
│   ├── YAML playbooks
│   └── Immediate execution
├── Puppet (Pull-based, Agent)
│   ├── Puppet agent on each node
│   ├── Ruby DSL configuration
│   ├── Master-slave architecture
│   └── Periodic configuration pulls
├── Chef (Pull-based, Agent)
│   ├── Chef client on each node
│   ├── Ruby-based cookbooks
│   ├── Chef server infrastructure
│   └── Convergence-based model
└── SaltStack (Push/Pull hybrid)
    ├── Salt minion agents
    ├── YAML/Jinja2 templates
    ├── Event-driven architecture
    └── High-speed communication
```

## Architecture

### Ansible Control Flow
Ansible follows a simple control flow from control node to managed nodes.

```
Ansible Execution Model
├── Control Node
│   ├── Ansible Core Engine
│   ├── Inventory (host lists)
│   ├── Playbooks (automation scripts)
│   ├── Modules (task units)
│   ├── Plugins (extend functionality)
│   └── Configuration (ansible.cfg)
├── Connection Layer
│   ├── SSH (Linux/Unix)
│   ├── WinRM (Windows)
│   ├── Network APIs (Network devices)
│   └── Cloud APIs (Cloud platforms)
├── Transport & Execution
│   ├── Copy module to target
│   ├── Execute on managed node
│   ├── Return results to control
│   └── Clean up temporary files
└── Managed Nodes
    ├── Python interpreter (Linux)
    ├── PowerShell (Windows)
    ├── No persistent agent
    └── Native system tools
```

### Core Components

#### Control Node
The machine where Ansible is installed and automation is orchestrated.

**Requirements:**
- Python 3.8+ (Python 2.7 deprecated)
- SSH client
- Cannot be Windows (WSL supported)

**Key Functions:**
- Parse and execute playbooks
- Manage inventory
- Establish connections to managed nodes
- Aggregate and report results

#### Managed Nodes
Target systems that Ansible manages and configures.

**Requirements:**
- SSH daemon (Linux/Unix)
- Python 2.7+ or Python 3.5+
- PowerShell 3.0+ (Windows)
- Network connectivity to control node

**No Agent Required:**
- Ansible copies modules temporarily
- Executes tasks using native interpreters
- Removes temporary files after execution

📖 **Learn More**: [Ansible Architecture](https://docs.ansible.com/ansible/latest/dev_guide/overview_architecture.html) | [Control Node Requirements](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#control-node-requirements)

## Inventory Management

### What is Inventory?
Inventory defines which hosts Ansible will manage and how to connect to them.

### Inventory Types
```
Ansible Inventory
├── Static Inventory
│   ├── INI format files
│   ├── YAML format files
│   ├── Simple host lists
│   └── Group definitions
├── Dynamic Inventory
│   ├── Cloud providers (AWS, Azure, GCP)
│   ├── Virtualization (VMware, OpenStack)
│   ├── Container platforms (Docker, Kubernetes)
│   └── Custom scripts
└── Inventory Plugins
    ├── Built-in plugins
    ├── Community plugins
    └── Custom plugins
```

### Host Groups and Variables
Organize hosts into logical groups for efficient management.

**Group Types:**
- **Web Servers**: Frontend application servers
- **Database Servers**: Backend data storage
- **Load Balancers**: Traffic distribution
- **Environment Groups**: dev, staging, production

**Variable Precedence:**
1. Extra vars (-e command line)
2. Task vars
3. Block vars
4. Role and include vars
5. Play vars
6. Host facts
7. Host vars
8. Group vars
9. Default vars

📖 **Learn More**: [Inventory Documentation](https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html) | [Variable Precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)

## Playbooks and Tasks

### Playbooks
Playbooks are YAML files that define automation workflows.

**Playbook Structure:**
- **Plays**: Map hosts to tasks
- **Tasks**: Actions to perform on hosts
- **Handlers**: Special tasks triggered by changes
- **Variables**: Dynamic values for reusability
- **Templates**: Dynamic file generation

### Task Execution Model
```
Task Execution Flow
├── Pre-tasks (Setup)
│   ├── Gather facts
│   ├── Setup variables
│   └── Validate prerequisites
├── Roles (if defined)
│   ├── Role dependencies
│   ├── Role tasks
│   ├── Role handlers
│   └── Role variables
├── Tasks (Main execution)
│   ├── Sequential execution
│   ├── Conditional logic
│   ├── Loops and iterations
│   └── Error handling
├── Handlers (Change triggers)
│   ├── Service restarts
│   ├── Configuration reloads
│   ├── Notification tasks
│   └── Cleanup operations
└── Post-tasks (Cleanup)
    ├── Status verification
    ├── Reporting
    └── Final cleanup
```

### Idempotency
Ansible ensures operations are idempotent - safe to run multiple times.

**Idempotent Modules:**
- Check current state before making changes
- Only modify when necessary
- Report changed/unchanged status
- Maintain desired state consistently

📖 **Learn More**: [Playbooks Introduction](https://docs.ansible.com/ansible/latest/user_guide/playbooks_intro.html) | [Idempotency](https://docs.ansible.com/ansible/latest/reference_appendices/glossary.html#term-Idempotency)

## Modules

### What are Modules?
Modules are discrete units of code that perform specific tasks on managed nodes.

### Module Categories
```
Ansible Module Library
├── System Modules
│   ├── Package management (yum, apt, pip)
│   ├── Service management (systemd, service)
│   ├── User/group management (user, group)
│   └── File operations (file, copy, template)
├── Commands Modules
│   ├── Shell commands (shell, command)
│   ├── Script execution (script)
│   ├── Raw commands (raw)
│   └── Expect interactions (expect)
├── Cloud Modules
│   ├── AWS (ec2, s3, rds, elb)
│   ├── Azure (azure_rm_*)
│   ├── GCP (gcp_*)
│   └── Multi-cloud (terraform)
├── Network Modules
│   ├── Cisco (ios_*, nxos_*)
│   ├── Juniper (junos_*)
│   ├── F5 (bigip_*)
│   └── Generic (net_*)
└── Database Modules
    ├── MySQL (mysql_*)
    ├── PostgreSQL (postgresql_*)
    ├── MongoDB (mongodb_*)
    └── Redis (redis_*)
```

### Module Return Values
All modules return structured data about their execution.

**Common Return Fields:**
- **changed**: Boolean indicating if changes were made
- **failed**: Boolean indicating task failure
- **msg**: Human-readable message
- **rc**: Return code (for command modules)
- **stdout/stderr**: Command output
- **ansible_facts**: Discovered system information

📖 **Learn More**: [Module Index](https://docs.ansible.com/ansible/latest/collections/index_module.html) | [Module Development](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html)

## Variables and Facts

### Variables
Variables make playbooks flexible and reusable across different environments.

### Variable Types
- **Strings**: Text values
- **Numbers**: Integer and float values
- **Booleans**: True/false values
- **Lists**: Ordered collections
- **Dictionaries**: Key-value mappings
- **Complex nested structures**

### Facts
Facts are system information automatically gathered by Ansible.

**Fact Categories:**
```
Ansible Facts
├── System Facts
│   ├── Operating system details
│   ├── Hardware information
│   ├── Network configuration
│   └── Storage details
├── Network Facts
│   ├── IP addresses
│   ├── Network interfaces
│   ├── Routing tables
│   └── DNS configuration
├── Hardware Facts
│   ├── CPU information
│   ├── Memory details
│   ├── Disk information
│   └── System architecture
└── Custom Facts
    ├── Local facts (fact.d)
    ├── External fact scripts
    ├── Dynamic fact gathering
    └── Cached facts
```

📖 **Learn More**: [Variables Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html) | [Facts and Magic Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_vars_facts.html)

## Templates and Files

### Jinja2 Templates
Ansible uses Jinja2 templating engine for dynamic file generation.

**Real-World Example**:
```
Your web server configuration needs environment-specific settings:
- Database connections vary per environment (dev/staging/prod)
- SSL certificates different for each domain
- Resource limits based on server capacity
- Perfect for: Configuration files, environment-specific deployments
```

**Template Features:**
- **Variable substitution**: Insert dynamic values
- **Conditional logic**: Include/exclude content based on conditions
- **Loops**: Generate repetitive content
- **Filters**: Transform data (uppercase, date formatting, etc.)

💡 **Tip**: Use Jinja2 templates for any file that needs dynamic content - don't hardcode environment-specific values!

⚠️ **Warning**: Always validate template output in staging before production deployment to catch syntax errors.
- **Macros**: Reusable template components

### File Management
Ansible provides multiple ways to manage files on target systems.

**File Operations:**
- **copy**: Copy files from control to managed nodes
- **template**: Generate files from Jinja2 templates
- **file**: Manage file attributes and directories
- **lineinfile**: Modify specific lines in files
- **blockinfile**: Manage blocks of text in files
- **fetch**: Copy files from managed nodes to control

📖 **Learn More**: [Templates Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html) | [Jinja2 Template Designer](https://jinja.palletsprojects.com/en/3.1.x/templates/)

## Handlers

### What are Handlers?
Handlers are special tasks that run only when triggered by changes in other tasks.

**What it does**: Execute specific tasks (like service restarts) only when notified by other tasks that made changes to the system.
**Why use it**: Automatically restart services when configuration files change, reload applications after updates, or trigger cleanup operations only when needed.

⚠️ **Warning**: Handlers only run at the end of a play, not immediately when notified. Use `meta: flush_handlers` to force immediate execution if needed.

### Handler Characteristics
- **Event-driven**: Only run when notified
- **Idempotent**: Safe to run multiple times
- **End-of-play execution**: Run after all tasks complete
- **Unique names**: Each handler has a unique identifier
- **Service restarts**: Common use case for configuration changes

### Handler Use Cases
```
Common Handler Patterns
├── Service Management
│   ├── Restart web server after config change
│   ├── Reload firewall rules
│   ├── Restart database after parameter updates
│   └── Reload systemd daemon
├── Application Deployment
│   ├── Clear application caches
│   ├── Rebuild application indexes
│   ├── Invalidate CDN caches
│   └── Update load balancer pools
└── System Configuration
    ├── Update system timezone
    ├── Regenerate SSH host keys
    ├── Update certificate stores
    └── Refresh DNS caches
```

📖 **Learn More**: [Handlers Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_handlers.html) | [Handler Best Practices](https://docs.ansible.com/ansible/latest/user_guide/playbooks_best_practices.html#handlers-listening-and-topics)

## Roles

### What are Roles?
Roles are reusable units of automation that group related tasks, variables, files, and templates.

### Role Structure
```
Ansible Role Directory Structure
role_name/
├── tasks/
│   ├── main.yml (primary task file)
│   └── install.yml (additional task files)
├── handlers/
│   └── main.yml (handler definitions)
├── templates/
│   └── config.j2 (Jinja2 templates)
├── files/
│   └── app.conf (static files)
├── vars/
│   └── main.yml (role variables)
├── defaults/
│   └── main.yml (default variables)
├── meta/
│   └── main.yml (role metadata)
├── library/
│   └── custom_module.py (custom modules)
└── README.md (role documentation)
```

### Role Benefits
- **Modularity**: Break complex automation into manageable pieces
- **Reusability**: Share roles across multiple projects
- **Organization**: Logical grouping of related functionality
- **Testing**: Easier to test individual components
- **Collaboration**: Teams can develop roles independently

### Ansible Galaxy
Community hub for sharing and discovering Ansible roles.

**Galaxy Features:**
- **Role discovery**: Search thousands of community roles
- **Role installation**: Install roles with dependencies
- **Role development**: Tools for creating and testing roles
- **Collections**: Distribute related roles, modules, and plugins together

📖 **Learn More**: [Roles Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html) | [Ansible Galaxy](https://docs.ansible.com/ansible/latest/galaxy/user_guide.html)

## Best Practices

### Playbook Organization
- Use descriptive names for plays and tasks
- Group related tasks into roles
- Separate configuration from code using variables
- Document playbooks with comments and README files

### Security
- Use Ansible Vault for sensitive data
- Implement least privilege access
- Regularly rotate secrets and credentials
- Audit playbook execution logs

### Performance
- Use fact caching to reduce gather time
- Implement parallel execution where possible
- Optimize task conditions to skip unnecessary work
- Use delegation and local actions efficiently

### Testing
- Test playbooks in non-production environments
- Use molecule for role testing
- Implement syntax checking (ansible-lint)
- Validate configuration changes before deployment

### Version Control
- Store playbooks in version control systems
- Use branching strategies for different environments
- Tag releases for production deployments
- Document changes in commit messages

## Real-World Production Example

### Enterprise Infrastructure Automation Platform

This example demonstrates a production-ready enterprise infrastructure automation using Ansible best practices for multi-tier application deployment, configuration management, and operational excellence.

```
Enterprise Infrastructure Automation
├── Infrastructure Layer
│   ├── Multi-environment management (dev, staging, prod)
│   ├── Network configuration and security groups
│   ├── Load balancer setup and SSL termination
│   └── Database cluster configuration
├── Application Layer
│   ├── Web server configuration (Nginx/Apache)
│   ├── Application server deployment (Node.js, Python, Java)
│   ├── Process management and monitoring
│   └── Static asset management
├── Database Layer
│   ├── Database server installation and tuning
│   ├── Replication and backup setup
│   ├── Performance monitoring
│   └── Security hardening
├── Monitoring & Logging
│   ├── Centralized log collection
│   ├── Metrics gathering and alerting
│   ├── Health check automation
│   └── Performance monitoring
└── Security & Compliance
    ├── User and access management
    ├── SSL/TLS certificate deployment
    ├── Firewall and network policies
    └── Security updates and patching
```

### Automation Components

#### Inventory Management
The enterprise platform uses dynamic inventory across multiple environments:

**Environment Structure:**
- **Development**: Rapid iteration and testing environment
- **Staging**: Production-like testing with real data volumes
- **Production**: Live environment with high availability requirements

**Host Organization:**
- Load balancers for traffic distribution
- Web servers for application hosting
- Database servers for data persistence
- Cache servers for performance optimization
- Monitoring servers for observability

#### Playbook Organization
```
Ansible Project Structure
├── environments/
│   ├── development/
│   ├── staging/
│   └── production/
├── group_vars/
│   ├── all.yml (common variables)
│   ├── webservers.yml
│   └── databases.yml
├── host_vars/
│   └── specific host configurations
├── roles/
│   ├── common/ (base system setup)
│   ├── webserver/ (nginx/apache config)
│   ├── database/ (mysql/postgresql)
│   └── monitoring/ (prometheus/grafana)
├── playbooks/
│   ├── site.yml (main deployment)
│   ├── database.yml (database setup)
│   └── monitoring.yml (monitoring setup)
└── templates/
    ├── nginx.conf.j2
    ├── mysql.cnf.j2
    └── application.properties.j2
```

#### Role-Based Automation
The platform leverages reusable roles for consistent deployments:

**Common Role**: Base system configuration
- System updates and package management
- User account and SSH key management
- Security hardening and compliance
- Basic monitoring agent installation

**Database Role**: Database server setup
- Database software installation and configuration
- Performance tuning based on server specs
- Backup and recovery procedure setup
- Replication configuration for high availability

**Web Server Role**: Application hosting setup
- Web server installation (Nginx/Apache)
- SSL certificate management
- Application deployment automation
- Health check and monitoring integration

**Monitoring Role**: Observability setup
- Metrics collection agent deployment
- Log aggregation configuration
- Dashboard and alerting setup
- Performance monitoring integration

### Production Features

#### High Availability Architecture
- **Load Balancer Redundancy**: Multiple load balancers with health checking
- **Application Server Clustering**: Horizontal scaling with session management
- **Database Replication**: Master-slave setup with automatic failover
- **Multi-Zone Deployment**: Geographic distribution for disaster recovery

#### Security Implementation
- **Access Control**: Role-based access with SSH key management
- **Network Security**: Firewall rules and network segmentation
- **Encryption**: SSL/TLS for all communication channels
- **Secrets Management**: Ansible Vault for credential protection
- **Compliance**: Automated security policy enforcement

#### Operational Excellence
- **Configuration Drift Prevention**: Regular configuration validation
- **Automated Backups**: Scheduled backup procedures with retention policies
- **Rolling Deployments**: Zero-downtime application updates
- **Environment Consistency**: Identical configurations across environments

#### Monitoring and Alerting
- **Infrastructure Monitoring**: CPU, memory, disk, network metrics
- **Application Monitoring**: Response times, error rates, throughput
- **Business Metrics**: User engagement, transaction volumes
- **Alerting**: Proactive notification of issues and anomalies

#### Deployment Pipeline Integration
- **Version Control**: Git-based playbook and role management
- **Testing**: Automated syntax checking and role testing
- **Staging**: Deployment validation in staging environment
- **Production**: Controlled production deployments with rollback capabilities

### Key Enterprise Benefits

**Standardization**: Consistent configuration across all environments reduces configuration drift and operational complexity.

**Scalability**: Template-based automation allows rapid provisioning of new infrastructure components as demand grows.

**Reliability**: Idempotent operations and comprehensive testing ensure reliable deployments without unexpected side effects.

**Security**: Centralized security policy management and automated compliance checking maintain security standards.

**Efficiency**: Automated routine tasks free up operations teams to focus on strategic initiatives and innovation.

**Cost Optimization**: Standardized configurations and automated scaling help optimize resource utilization and reduce operational costs.

This enterprise platform demonstrates how Ansible enables organizations to achieve infrastructure automation at scale while maintaining security, reliability, and operational excellence standards required for production environments.

### Essential Syntax Reference

Complete syntax guide covering the most important Ansible patterns for practical automation:

#### Playbook Structure and Components
```yaml
---
- name: Complete playbook example
  hosts: webservers
  become: yes
  gather_facts: yes
  vars:
    http_port: 80
    app_user: webapp
  vars_files:
    - secrets.yml
  vars_prompt:
    - name: deploy_version
      prompt: "Enter deployment version"
      private: no
      
  pre_tasks:
    - name: Update package cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"
      
  tasks:
    - name: Install required packages
      package:
        name: "{{ item }}"
        state: present
      loop:
        - nginx
        - git
        - python3-pip
      tags: [packages, base]
      
    - name: Create application user
      user:
        name: "{{ app_user }}"
        system: yes
        shell: /bin/bash
        home: "/opt/{{ app_user }}"
        create_home: yes
      tags: [users]
      
  post_tasks:
    - name: Ensure services are running
      service:
        name: "{{ item }}"
        state: started
        enabled: yes
      loop:
        - nginx
        - ssh
      tags: [services]
      
  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
    - name: reload nginx
      service:
        name: nginx
        state: reloaded
```

**What it does**: Complete playbook structure with organized execution phases, multiple variable sources, and event-driven handlers.
**Why use it**: Provides systematic workflow with preparation (pre_tasks), main work (tasks), cleanup (post_tasks), and automatic service restarts (handlers) when configurations change.

📖 **Learn More**: [Playbook Keywords](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html) | [Task Keywords](https://docs.ansible.com/ansible/latest/reference_appendices/playbooks_keywords.html#task)

#### Variables and Data Types
```yaml
# String variables
server_name: "web.example.com"
app_version: "{{ lookup('env', 'APP_VERSION') | default('latest') }}"

# Number variables
max_connections: 1000
timeout_seconds: 30

# Boolean variables
ssl_enabled: true
debug_mode: false

# List variables
packages:
  - nginx
  - git
  - vim
  - htop

# Dictionary variables
database_config:
  host: db.example.com
  port: 3306
  name: webapp
  user: dbuser

# Complex nested structures
users:
  - name: alice
    groups: [admin, developers]
    ssh_keys:
      - "ssh-rsa AAAAB3NzaC1yc2E..."
      - "ssh-rsa AAAAB3NzaC1yc2E..."
  - name: bob
    groups: [developers]
    shell: /bin/zsh

# Using variables in tasks
- name: Configure database connection
  template:
    src: database.conf.j2
    dest: /etc/app/database.conf
  vars:
    db_host: "{{ database_config.host }}"
    db_port: "{{ database_config.port }}"
```

**What it does**: Store different data types (strings, numbers, lists, dictionaries) to make playbooks flexible across environments.
**Why use it**: Enables same playbook to work in dev/staging/production with different values, and provides structured data for templates and conditionals.

📖 **Learn More**: [Variable Types](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#variable-types) | [Using Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#using-variables)

#### Loops and Iterations
```yaml
# Simple loop
- name: Install packages
  package:
    name: "{{ item }}"
    state: present
  loop:
    - git
    - vim
    - htop

# Loop with dictionaries
- name: Create users with specific settings
  user:
    name: "{{ item.name }}"
    groups: "{{ item.groups | join(',') }}"
    shell: "{{ item.shell | default('/bin/bash') }}"
  loop: "{{ users }}"

# Loop with conditions
- name: Install development packages
  package:
    name: "{{ item }}"
    state: present
  loop:
    - nodejs
    - npm
    - python3-dev
  when: environment == "development"

# Loop with register
- name: Check service status
  command: systemctl is-active "{{ item }}"
  register: service_status
  loop:
    - nginx
    - ssh
    - mysql
  failed_when: false
  changed_when: false

- name: Display service status
  debug:
    msg: "{{ item.item }} is {{ item.stdout }}"
  loop: "{{ service_status.results }}"

# Loop with subelements
- name: Add SSH keys for users
  authorized_key:
    user: "{{ item.0.name }}"
    key: "{{ item.1 }}"
  loop: "{{ users | subelements('ssh_keys', skip_missing=True) }}"

# Loop with ranges
- name: Create numbered directories
  file:
    path: "/tmp/dir{{ item }}"
    state: directory
  loop: "{{ range(1, 6) | list }}"

# Loop until condition
- name: Wait for service to be ready
  uri:
    url: "http://{{ ansible_host }}:8080/health"
    method: GET
    status_code: 200
  register: health_check
  until: health_check.status == 200
  retries: 30
  delay: 10
```

**What it does**: Execute the same task multiple times with different values, eliminating code duplication.
**Why use it**: Install multiple packages, create multiple users, or handle dynamic lists without writing repetitive tasks. Use `until:` for waiting on services to start.

📖 **Learn More**: [Loops Documentation](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html) | [Loop Control](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html#loop-control)

#### Conditionals and Logic
```yaml
# Simple conditions
- name: Install package on Ubuntu
  apt:
    name: snapd
    state: present
  when: ansible_distribution == "Ubuntu"

# Multiple conditions (AND)
- name: Install on Ubuntu 20.04
  apt:
    name: docker.io
    state: present
  when:
    - ansible_distribution == "Ubuntu"
    - ansible_distribution_version == "20.04"

# Multiple conditions (OR)
- name: Install on RedHat family
  yum:
    name: docker
    state: present
  when: ansible_os_family == "RedHat" or ansible_os_family == "CentOS"

# Complex conditions
- name: Configure firewall for production
  ufw:
    rule: allow
    port: "{{ item }}"
  loop:
    - 22
    - 80
    - 443
  when:
    - environment == "production"
    - configure_firewall | default(true) | bool
    - ansible_os_family == "Debian"

# Condition based on previous task
- name: Check if application exists
  stat:
    path: /opt/myapp/app.jar
  register: app_file

- name: Deploy application
  copy:
    src: app.jar
    dest: /opt/myapp/app.jar
  when: not app_file.stat.exists or force_deploy | default(false)

# Condition based on command output
- name: Check disk space
  shell: df -h / | tail -1 | awk '{print $5}' | sed 's/%//'
  register: disk_usage
  changed_when: false

- name: Fail if disk full
  fail:
    msg: "Disk usage is {{ disk_usage.stdout }}% - too high!"
  when: disk_usage.stdout | int > 90
```

**What it does**: Run tasks only when specific conditions are met, using system facts, variables, or previous task results.
**Why use it**: Create cross-platform playbooks (Ubuntu vs CentOS), environment-specific behavior (dev vs production), and safety checks before destructive operations.

#### Templates and Jinja2
```yaml
# Basic template usage
- name: Configure nginx
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    owner: root
    group: root
    mode: '0644'
    backup: yes
  notify: restart nginx

# Template with variables
- name: Generate application config
  template:
    src: app.properties.j2
    dest: "/opt/{{ app_name }}/config/application.properties"
    owner: "{{ app_user }}"
    group: "{{ app_user }}"
    mode: '0600'
  vars:
    database_url: "jdbc:mysql://{{ db_host }}:{{ db_port }}/{{ db_name }}"
    max_connections: "{{ app_max_connections | default(100) }}"
```

Template file example (nginx.conf.j2):
```jinja2
user {{ nginx_user | default('www-data') }};
worker_processes {{ nginx_worker_processes | default(ansible_processor_vcpus) }};

events {
    worker_connections {{ nginx_worker_connections | default(1024) }};
}

http {
    # Basic settings
    sendfile on;
    keepalive_timeout {{ nginx_keepalive_timeout | default(65) }};
    
    # Gzip compression
    {% if nginx_gzip_enabled | default(true) %}
    gzip on;
    gzip_types text/plain text/css application/json application/javascript;
    {% endif %}
    
    # Virtual hosts
    {% for vhost in nginx_vhosts | default([]) %}
    server {
        listen {{ vhost.port | default(80) }};
        server_name {{ vhost.server_name }};
        root {{ vhost.document_root | default('/var/www/html') }};
        
        {% if vhost.ssl | default(false) %}
        listen 443 ssl;
        ssl_certificate {{ vhost.ssl_cert }};
        ssl_certificate_key {{ vhost.ssl_key }};
        {% endif %}
        
        location / {
            try_files $uri $uri/ =404;
        }
    }
    {% endfor %}
}
```

**What it does**: Generate dynamic configuration files using variables, conditionals, and loops in Jinja2 template format.
**Why use it**: Create environment-specific configs (different server names, ports, SSL settings) from a single template instead of maintaining separate files.

#### File and Directory Operations
```yaml
# Create files and directories
- name: Create application directory
  file:
    path: /opt/myapp
    state: directory
    owner: myapp
    group: myapp
    mode: '0755'
    recurse: yes

# Copy files
- name: Copy configuration file
  copy:
    src: app.conf
    dest: /etc/myapp/app.conf
    owner: myapp
    group: myapp
    mode: '0644'
    backup: yes

# Download files
- name: Download application package
  get_url:
    url: "https://releases.example.com/app-{{ app_version }}.tar.gz"
    dest: "/tmp/app-{{ app_version }}.tar.gz"
    checksum: "sha256:{{ app_checksum }}"
    timeout: 300

# Extract archives
- name: Extract application
  unarchive:
    src: "/tmp/app-{{ app_version }}.tar.gz"
    dest: /opt/myapp
    remote_src: yes
    creates: "/opt/myapp/bin/app"
    owner: myapp
    group: myapp

# Manage file content
- name: Add line to file
  lineinfile:
    path: /etc/hosts
    line: "192.168.1.100 app.local"
    regexp: "^192\.168\.1\.100"

- name: Add block to file
  blockinfile:
    path: /etc/ssh/sshd_config
    block: |
      Match User deployer
          PasswordAuthentication no
          PubkeyAuthentication yes
    marker: "# {mark} ANSIBLE MANAGED BLOCK - deployer"

# Set file attributes
- name: Set immutable attribute
  file:
    path: /etc/passwd
    attributes: +i
```

**What it does**: Manage files, directories, archives, and file content across different systems with proper permissions and ownership.
**Why use it**: Deploy configuration files, create directory structures, extract applications, and maintain file permissions consistently across environments.

#### Package Management
```yaml
# Generic package management
- name: Install packages
  package:
    name: "{{ packages }}"
    state: present

# APT (Debian/Ubuntu)
- name: Update package cache
  apt:
    update_cache: yes
    cache_valid_time: 3600

- name: Install specific packages
  apt:
    name:
      - nginx=1.18.*
      - mysql-server
      - python3-pip
    state: present

- name: Remove packages
  apt:
    name: apache2
    state: absent
    purge: yes

# YUM/DNF (RedHat/CentOS)
- name: Install packages with yum
  yum:
    name:
      - nginx
      - mysql-server
    state: latest

# Snap packages
- name: Install snap packages
  snap:
    name: "{{ item }}"
    state: present
  loop:
    - code
    - discord

# Python packages
- name: Install Python packages
  pip:
    name:
      - django==3.2
      - requests
      - boto3
    virtualenv: /opt/myapp/venv
    virtualenv_python: python3.9
```

**What it does**: Install, update, and remove software packages across different Linux distributions and package managers.
**Why use it**: Maintain consistent software versions, install dependencies, and manage package repositories across Ubuntu (apt), CentOS (yum/dnf), and other systems.

#### Service Management
```yaml
# Basic service operations
- name: Start and enable services
  service:
    name: "{{ item }}"
    state: started
    enabled: yes
  loop:
    - nginx
    - mysql

# Systemd specific operations
- name: Manage systemd service
  systemd:
    name: myapp
    state: started
    enabled: yes
    daemon_reload: yes

# Service with configuration
- name: Create systemd service file
  template:
    src: myapp.service.j2
    dest: /etc/systemd/system/myapp.service
  notify:
    - reload systemd
    - restart myapp

# Check service status
- name: Get service facts
  service_facts:

- name: Display service status
  debug:
    msg: "Nginx is {{ ansible_facts.services['nginx.service'].state }}"
  when: "'nginx.service' in ansible_facts.services"
```

**What it does**: Control system services (start, stop, restart, enable) and manage systemd units with status checking and daemon reloading.
**Why use it**: Ensure services are running after configuration changes, enable services to start on boot, and manage service lifecycle reliably across deployments.

#### Error Handling and Blocks
```yaml
# Basic error handling
- name: Attempt risky operation
  command: /usr/local/bin/risky-command
  ignore_errors: yes
  register: risky_result

- name: Handle error
  debug:
    msg: "Command failed: {{ risky_result.stderr }}"
  when: risky_result.failed

# Block with rescue and always
- name: Deploy application with rollback
  block:
    - name: Stop application
      service:
        name: myapp
        state: stopped

    - name: Deploy new version
      unarchive:
        src: "app-{{ new_version }}.tar.gz"
        dest: /opt/myapp

    - name: Start application
      service:
        name: myapp
        state: started

    - name: Verify deployment
      uri:
        url: "http://localhost:8080/health"
        status_code: 200
      retries: 5
      delay: 10

  rescue:
    - name: Rollback on failure
      unarchive:
        src: "app-{{ current_version }}.tar.gz"
        dest: /opt/myapp

    - name: Start old version
      service:
        name: myapp
        state: started

    - name: Log failure
      lineinfile:
        path: /var/log/deployment.log
        line: "{{ ansible_date_time.iso8601 }} - Deployment failed, rolled back"

  always:
    - name: Cleanup temp files
      file:
        path: "/tmp/app-*"
        state: absent

# Custom failure conditions
- name: Check application health
  uri:
    url: "http://{{ ansible_host }}:8080/health"
    method: GET
  register: health_check
  failed_when: 
    - health_check.status != 200
    - "'healthy' not in health_check.content"
```

**What it does**: Handle task failures gracefully using blocks, rescue sections, and always clauses with custom failure conditions and error recovery.
**Why use it**: Implement deployment rollbacks, handle expected failures, ensure cleanup operations run, and create robust automation that can recover from errors.

### Production Example Configuration

#### Inventory Structure
```yaml
# inventory/production.yml
all:
  children:
    webservers:
      hosts:
        web[1:3].company.com:
    databases:
      hosts:
        db[1:2].company.com:
    loadbalancers:
      hosts:
        lb[1:2].company.com:
  vars:
    environment: production
    backup_enabled: true
```

#### Main Deployment Playbook
```yaml
# playbooks/site.yml
---
- name: Configure Database Servers
  hosts: databases
  become: yes
  roles:
    - role: database
      vars:
        mysql_max_connections: 500
        backup_schedule: "0 2 * * *"

- name: Configure Web Servers  
  hosts: webservers
  become: yes
  roles:
    - role: webserver
      vars:
        worker_processes: "{{ ansible_processor_vcpus }}"
        ssl_enabled: true
    - role: application
      vars:
        app_version: "{{ deploy_version }}"
        
- name: Configure Load Balancers
  hosts: loadbalancers
  become: yes
  roles:
    - role: loadbalancer
      vars:
        backend_servers: "{{ groups['webservers'] }}"
```

#### Role Structure Example
```yaml
# roles/webserver/tasks/main.yml
---
- name: Install web server packages
  package:
    name: "{{ webserver_packages }}"
    state: present

- name: Configure web server
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    backup: yes
  notify: restart nginx

- name: Start and enable service
  service:
    name: nginx
    state: started
    enabled: yes

# roles/webserver/handlers/main.yml
---
- name: restart nginx
  service:
    name: nginx
    state: restarted
```

#### Rolling Deployment
```yaml
# playbooks/deploy.yml
---
- name: Rolling Application Deployment
  hosts: webservers
  serial: 1  # Deploy to one server at a time
  become: yes
  
  pre_tasks:
    - name: Remove from load balancer
      uri:
        url: "http://{{ lb_host }}/api/disable/{{ inventory_hostname }}"
        method: POST
      delegate_to: localhost
      
  tasks:
    - name: Deploy application
      unarchive:
        src: "app-{{ app_version }}.tar.gz"
        dest: /opt/app
        backup: yes
        
    - name: Restart application
      service:
        name: myapp
        state: restarted
        
    - name: Health check
      uri:
        url: "http://{{ ansible_host }}:8080/health"
        status_code: 200
      retries: 5
      delay: 10
        
  post_tasks:
    - name: Add back to load balancer
      uri:
        url: "http://{{ lb_host }}/api/enable/{{ inventory_hostname }}"
        method: POST
      delegate_to: localhost
```

## Essential Syntax Reference

### Advanced Conditional Statements
```yaml
# Failed_when and changed_when for custom state control
- name: Check service status
  command: systemctl is-active nginx
  register: nginx_status
  failed_when: 
    - nginx_status.rc != 0
    - "'inactive' not in nginx_status.stdout"
  changed_when: false

- name: Deploy configuration
  template:
    src: app.conf.j2
    dest: /etc/app/app.conf
  register: config_result
  changed_when: config_result.checksum != config_result.backup_file | default('')

# Complex conditional expressions with filters
- name: Install packages based on OS
  package:
    name: "{{ packages[ansible_os_family] }}"
    state: present
  when: 
    - ansible_os_family in packages
    - packages[ansible_os_family] | length > 0
    - not skip_package_install | default(false)

# Group membership and fact-based conditionals
- name: Configure database servers
  template:
    src: db.conf.j2
    dest: /etc/mysql/my.cnf
  when:
    - "'database' in group_names"
    - ansible_memory_mb.real.total > 4096
    - ansible_processor_vcpus >= 2

# Assertion for validation checks
- name: Validate environment requirements
  assert:
    that:
      - ansible_distribution_version is version('18.04', '>=')
      - ansible_memory_mb.real.total >= 2048
      - disk_space.size_available > 10737418240  # 10GB
    fail_msg: "System does not meet minimum requirements"
    success_msg: "System validation passed"
```

📖 **Learn More**: [Conditionals](https://docs.ansible.com/ansible/latest/user_guide/playbooks_conditionals.html) | [Tests](https://docs.ansible.com/ansible/latest/user_guide/playbooks_tests.html)

### Advanced Loop Constructs and Control
```yaml
# Loop control with pause, labels, and indexing
- name: Deploy application instances
  template:
    src: app-config.j2
    dest: "/etc/app/instance-{{ loop_index0 }}.conf"
  loop: "{{ app_instances }}"
  loop_control:
    pause: 3                    # Wait 3 seconds between iterations
    label: "{{ item.name }}"    # Show only name in output
    index_var: loop_index0      # Zero-based index variable
    extended: yes               # Enable extended loop variables

# Complex data structure iteration with subelements
- name: Create users with multiple SSH keys
  authorized_key:
    user: "{{ item.0.username }}"
    key: "{{ item.1 }}"
    exclusive: no
  loop: "{{ users | subelements('ssh_keys') }}"
  when: item.0.state == 'present'

# Nested loops with product filter
- name: Create directory structure
  file:
    path: "/data/{{ item.0 }}/{{ item.1 }}"
    state: directory
    mode: '0755'
  loop: "{{ environments | product(applications) | list }}"

# Dictionary iteration with complex transformations
- name: Configure virtual hosts
  template:
    src: vhost.conf.j2
    dest: "/etc/nginx/sites-available/{{ item.key }}"
  loop: "{{ virtual_hosts | dict2items }}"
  loop_control:
    label: "{{ item.key }}"
  notify: reload nginx

# File-based loops
- name: Process configuration files
  template:
    src: "{{ item | basename }}"
    dest: "/etc/app/{{ item | basename | regex_replace('.j2$', '') }}"
  with_fileglob:
    - "../templates/configs/*.j2"

# Sequence loops for dynamic scaling
- name: Create numbered resources
  file:
    path: "/data/partition{{ item }}"
    state: directory
  loop: "{{ range(1, worker_count + 1) | list }}"

# Until loops for waiting conditions
- name: Wait for service to be ready
  uri:
    url: "http://{{ ansible_host }}:8080/health"
    method: GET
  register: health_check
  until: health_check.status == 200
  retries: 30
  delay: 10
  failed_when: health_check.status is undefined
```

📖 **Learn More**: [Loops](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html) | [Loop Control](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html#loop-control)

### Advanced Error Handling and Recovery
```yaml
# Advanced block/rescue/always patterns
- name: Deploy with comprehensive error handling
  block:
    - name: Stop existing service
      service:
        name: "{{ app_service }}"
        state: stopped
      register: service_stop
      
    - name: Backup current version  
      archive:
        path: "{{ app_path }}"
        dest: "/backup/{{ app_service }}-{{ ansible_date_time.epoch }}.tar.gz"
        format: gz
      when: backup_enabled | default(true)
      
    - name: Deploy new version
      unarchive:
        src: "{{ artifact_url }}"
        dest: "{{ app_path }}"
        remote_src: yes
        backup: yes
      register: deployment_result
      
    - name: Update configuration
      template:
        src: "{{ item }}.j2"
        dest: "{{ app_path }}/config/{{ item }}"
        backup: yes
      loop:
        - application.yml
        - database.yml
        - logging.yml
        
    - name: Start service
      service:
        name: "{{ app_service }}"
        state: started
        enabled: yes
      register: service_start
      
    - name: Verify deployment
      uri:
        url: "{{ health_check_url }}"
        status_code: 200
      retries: 10
      delay: 5
      
  rescue:
    - name: Log deployment failure
      debug:
        msg: "Deployment failed: {{ ansible_failed_result | default('Unknown error') }}"
        
    - name: Rollback to previous version
      unarchive:
        src: "{{ deployment_result.backup_file }}"
        dest: "{{ app_path }}"
      when: deployment_result.backup_file is defined
      
    - name: Restore service
      service:
        name: "{{ app_service }}"
        state: started
      ignore_errors: yes
      
    - name: Send failure notification
      mail:
        to: "{{ ops_email }}"
        subject: "Deployment Failed: {{ inventory_hostname }}"
        body: "Deployment failed and rollback initiated"
      delegate_to: localhost
      
    - name: Fail the playbook
      fail:
        msg: "Deployment failed and rollback completed"
        
  always:
    - name: Clean up temporary files
      file:
        path: "{{ item }}"
        state: absent
      loop:
        - /tmp/deployment.lock
        - /tmp/artifact.tar.gz
      ignore_errors: yes
      
    - name: Update deployment log
      lineinfile:
        path: /var/log/deployments.log
        line: "{{ ansible_date_time.iso8601 }} - {{ inventory_hostname }} - {{ deployment_status | default('FAILED') }}"
        create: yes
```

📖 **Learn More**: [Error Handling](https://docs.ansible.com/ansible/latest/user_guide/playbooks_error_handling.html) | [Blocks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_blocks.html)

### Async Operations and Performance Optimization
```yaml
# Async operations for long-running tasks
- name: Long running backup process
  command: /usr/local/bin/backup-database.sh
  async: 3600              # Maximum runtime: 1 hour
  poll: 0                  # Fire and forget
  register: backup_job

- name: Continue with other tasks while backup runs
  package:
    name: "{{ packages }}"
    state: present

- name: Check backup completion
  async_status:
    jid: "{{ backup_job.ansible_job_id }}"
  register: backup_result
  until: backup_result.finished
  retries: 60
  delay: 30
  when: backup_job.ansible_job_id is defined

# Parallel execution with async
- name: Download multiple files in parallel
  get_url:
    url: "{{ item.url }}"
    dest: "{{ item.dest }}"
  async: 300
  poll: 0
  loop: "{{ download_files }}"
  register: download_jobs

- name: Wait for all downloads to complete
  async_status:
    jid: "{{ item.ansible_job_id }}"
  loop: "{{ download_jobs.results }}"
  register: download_results
  until: download_results.finished
  retries: 10
  delay: 10

# Performance optimization with delegation and run_once
- name: Generate configuration once
  template:
    src: shared-config.j2
    dest: /tmp/shared-config.yml
  delegate_to: localhost
  run_once: true
  register: shared_config

- name: Distribute configuration to all hosts
  copy:
    src: /tmp/shared-config.yml
    dest: /etc/app/config.yml
  when: shared_config is succeeded

# Memory and connection optimization
- name: Batch operations for performance
  yum:
    name: "{{ ansible_play_batch }}"
    state: present
  throttle: 5              # Limit to 5 hosts at once
  serial: "30%"            # Process 30% of hosts at a time
```

📖 **Learn More**: [Async Actions](https://docs.ansible.com/ansible/latest/user_guide/playbooks_async.html) | [Strategies](https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html)

### Advanced Variable Manipulation and Jinja2
```yaml
# Complex variable transformations with filters
- name: Process server inventory
  set_fact:
    processed_servers: >-
      {{
        groups.webservers 
        | map('extract', hostvars) 
        | selectattr('server_role', 'defined')
        | selectattr('server_role', 'equalto', 'frontend')
        | map(attribute='ansible_host')
        | list
      }}
    
    grouped_by_region: >-
      {{
        groups.all
        | map('extract', hostvars)
        | groupby('aws_region')
        | dict
      }}

# Dynamic variable registration with complex logic
- name: Discover service configuration
  shell: |
    if systemctl is-active {{ item }} &>/dev/null; then
      echo "active:$(systemctl show -p ExecStart {{ item }} --value)"
    else
      echo "inactive:none"
    fi
  loop: "{{ services_to_check }}"
  register: service_discovery
  changed_when: false

- name: Build service inventory
  set_fact:
    service_inventory: >-
      {{
        service_inventory | default({}) |
        combine({
          item.item: {
            'status': item.stdout.split(':')[0],
            'exec_start': item.stdout.split(':')[1] if ':' in item.stdout else 'unknown',
            'host': inventory_hostname
          }
        })
      }}
  loop: "{{ service_discovery.results }}"

# Magic variables and cross-host data access
- name: Configure load balancer with backend servers
  template:
    src: haproxy.cfg.j2
    dest: /etc/haproxy/haproxy.cfg
  vars:
    backend_servers: >-
      {{
        groups['webservers']
        | map('extract', hostvars, 'ansible_host')
        | map('regex_replace', '^(.*)$', '\1:8080')
        | list
      }}
    total_memory: >-
      {{
        groups['all']
        | map('extract', hostvars, 'ansible_memory_mb')
        | map(attribute='real.total')
        | sum
      }}
  when: "'loadbalancer' in group_names"

# Complex conditionals with custom tests and filters
- name: Validate network configuration
  assert:
    that:
      - network_config.subnet | ipaddr('network') == expected_network
      - network_config.gateway | ipaddr('address')
      - dns_servers | map('ipaddr') | reject('false') | list | length == dns_servers | length
    fail_msg: "Network configuration validation failed"

# Template inheritance and includes
- name: Generate complex configuration
  template:
    src: application.conf.j2
    dest: "/etc/{{ app_name }}/application.conf"
  vars:
    config_sections:
      database:
        host: "{{ groups['database'][0] }}"
        port: 5432
        name: "{{ app_name }}_{{ env }}"
      cache:
        servers: "{{ groups['cache'] | map('extract', hostvars, 'ansible_host') | map('regex_replace', '$', ':6379') | list }}"
      monitoring:
        enabled: "{{ env == 'production' }}"
        endpoint: "{{ monitoring_endpoint | default('http://localhost:9090') }}"
```

📖 **Learn More**: [Jinja2 Templating](https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html) | [Filters](https://docs.ansible.com/ansible/latest/user_guide/playbooks_filters.html)

### Ansible Vault and Security Operations
```yaml
# Multiple vault IDs and passwords
- name: Deploy with multiple vault sources
  hosts: all
  vars_files:
    - group_vars/vault_common.yml          # Common secrets
    - group_vars/vault_{{ env }}.yml       # Environment-specific secrets
    - host_vars/{{ inventory_hostname }}/vault.yml  # Host-specific secrets
  
  tasks:
    - name: Configure application with mixed secrets
      template:
        src: app.conf.j2
        dest: /etc/app/app.conf
        mode: '0600'
      vars:
        # Mix of vaulted and plain variables
        database_password: "{{ vault_db_password }}"
        api_key: "{{ vault_api_keys[env] }}"
        debug_mode: "{{ app_debug | default(false) }}"
      no_log: true

# Runtime vault operations and inline encryption
- name: Handle secrets dynamically
  block:
    - name: Generate temporary password
      set_fact:
        temp_password: "{{ lookup('password', '/dev/null chars=ascii_letters,digits length=16') }}"
      no_log: true
      
    - name: Create encrypted string inline
      debug:
        msg: "{{ temp_password | vault(vault_password) }}"
      no_log: true
      
    - name: Store encrypted credentials
      copy:
        content: |
          temp_user_password: {{ temp_password | vault(vault_password) }}
          created_at: {{ ansible_date_time.iso8601 }}
        dest: "/secure/temp_credentials_{{ ansible_date_time.epoch }}.yml"
        mode: '0600'
      no_log: true

# Vault file mixing strategies
- name: Complex vault configuration
  include_vars: "{{ item }}"
  with_first_found:
    - files:
        - "vault_{{ inventory_hostname }}.yml"
        - "vault_{{ group_names[0] }}.yml" 
        - "vault_{{ env }}.yml"
        - "vault_default.yml"
      paths:
        - "{{ playbook_dir }}/group_vars"
        - "{{ playbook_dir }}/host_vars"
      skip: true
```

📖 **Learn More**: [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html) | [Vault Examples](https://docs.ansible.com/ansible/latest/user_guide/vault.html#encrypting-content-with-ansible-vault)

### Testing and Quality Assurance
```yaml
# Testing strategies with check mode and diff
- name: Dry run deployment validation
  block:
    - name: Validate configuration templates
      template:
        src: "{{ item }}.j2"
        dest: "/tmp/validate_{{ item }}"
      loop:
        - nginx.conf
        - app.conf
        - logrotate.conf
      check_mode: yes
      diff: yes
      register: template_validation
      
    - name: Test configuration syntax
      command: "{{ item.command }}"
      loop:
        - { command: "nginx -t -c /tmp/validate_nginx.conf", name: "nginx" }
        - { command: "python -m py_compile /tmp/validate_app.conf", name: "app" }
      changed_when: false
      register: syntax_validation
      
    - name: Verify all validations passed
      assert:
        that:
          - template_validation is succeeded
          - syntax_validation is succeeded
        fail_msg: "Configuration validation failed"

# Integration testing patterns
- name: Integration test suite
  block:
    - name: Test database connectivity
      postgresql_ping:
        host: "{{ db_host }}"
        port: "{{ db_port }}"
        login_user: "{{ db_user }}"
        login_password: "{{ vault_db_password }}"
      delegate_to: localhost
      
    - name: Test service endpoints
      uri:
        url: "{{ item.url }}"
        method: "{{ item.method | default('GET') }}"
        status_code: "{{ item.expected_status | default(200) }}"
        timeout: 30
      loop: "{{ service_endpoints }}"
      register: endpoint_tests
      
    - name: Test file permissions and ownership
      stat:
        path: "{{ item.path }}"
      register: file_stats
      failed_when: 
        - file_stats.stat.mode != item.expected_mode
        - file_stats.stat.pw_name != item.expected_owner
      loop: "{{ critical_files }}"

# Ansible-lint integration
- name: Lint validation
  hosts: localhost
  gather_facts: no
  tasks:
    - name: Run ansible-lint on playbooks
      command: ansible-lint {{ playbook_dir }}/*.yml
      changed_when: false
      register: lint_results
      failed_when: lint_results.rc != 0
      
    - name: Display lint results
      debug:
        var: lint_results.stdout_lines
      when: lint_results.rc != 0
```

📖 **Learn More**: [Testing Strategies](https://docs.ansible.com/ansible/latest/reference_appendices/test_strategies.html) | [Ansible Lint](https://ansible-lint.readthedocs.io/)

### Advanced Configuration and Optimization
```yaml
# Ansible configuration optimization in ansible.cfg
[defaults]
host_key_checking = False
gathering = smart
fact_caching = jsonfile
fact_caching_connection = /tmp/ansible_cache
fact_caching_timeout = 3600
timeout = 30
forks = 20
callback_whitelist = profile_tasks, timer

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o UserKnownHostsFile=/dev/null
pipelining = True
control_path_dir = /tmp/.ansible-cp

# Performance tuning in playbooks
- name: Optimized deployment playbook
  hosts: webservers
  gather_facts: yes
  fact_caching: memory
  serial: "25%"              # Process 25% of hosts at a time
  max_fail_percentage: 10    # Continue if less than 10% fail
  
  tasks:
    - name: Batch package installation
      package:
        name: "{{ packages | batch(5) | list }}"
        state: present
      throttle: 10             # Limit concurrent executions
      
    - name: Use connection persistence
      setup:
        filter: ansible_distribution*
      delegate_facts: true     # Cache facts for reuse
      run_once: true
      
# Custom callback plugins for monitoring
- name: Deployment with custom callbacks
  hosts: all
  vars:
    callback_plugins: 
      - profile_tasks          # Task timing
      - dense                 # Compact output
      - json                  # JSON formatted output
  
  tasks:
    - name: Monitored task execution
      command: "{{ long_running_command }}"
      register: command_result
      notify: send metrics
      
  handlers:
    - name: send metrics
      uri:
        url: "{{ metrics_endpoint }}"
        method: POST
        body_format: json
        body:
          host: "{{ inventory_hostname }}"
          task: "{{ ansible_task.name }}"
          duration: "{{ ansible_task.duration }}"
          status: "{{ ansible_task.status }}"
      delegate_to: localhost
```

📖 **Learn More**: [Ansible Configuration](https://docs.ansible.com/ansible/latest/reference_appendices/config.html) | [Performance Tuning](https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html)

#### Ansible Vault Usage
```yaml
# Using encrypted variables
- name: Deploy with secrets
  hosts: webservers
  vars_files:
    - secrets.yml  # encrypted with ansible-vault
  tasks:
    - name: Configure database connection
      template:
        src: db_config.j2
        dest: /etc/app/database.conf
        mode: '0600'
      vars:
        db_password: "{{ vault_db_password }}"
      no_log: true
```

These examples demonstrate the essential Ansible patterns used in production environments, focusing on scalability, security, and maintainability.
