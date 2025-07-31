<div align="center">

# 🔵 Kubernetes Essential Guide

<img src="https://raw.githubusercontent.com/devicons/devicon/master/icons/kubernetes/kubernetes-plain-wordmark.svg" alt="Kubernetes" width="200"/>

*Comprehensive guide to container orchestration concepts, architecture, and production deployments*

[![Documentation](https://img.shields.io/badge/Kubernetes-Documentation-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/docs/)
[![API Reference](https://img.shields.io/badge/Kubernetes-API%20Reference-326CE5?logo=kubernetes&logoColor=white)](https://kubernetes.io/docs/reference/kubernetes-api/)

</div>

## Table of Contents
- [Core Concepts](#core-concepts)
- [Architecture](#architecture)
- [Workloads](#workloads)
- [Services and Networking](#services-and-networking)
- [Storage](#storage)
- [Configuration Management](#configuration-management)
- [Security](#security)
- [Scaling and Automation](#scaling-and-automation)
- [Monitoring and Observability](#monitoring-and-observability)
- [Best Practices](#best-practices)
- [Real-World Production Example](#real-world-production-example)

## Core Concepts

### What is Kubernetes?
Kubernetes (K8s) is an open-source container orchestration platform that automates the deployment, scaling, and management of containerized applications. It provides a framework for running distributed systems resiliently, handling scaling and failover for your applications.

### Key Benefits
- **Container Orchestration**: Automated deployment, scaling, and management
- **Service Discovery**: Built-in load balancing and service discovery
- **Storage Orchestration**: Automatic mounting of storage systems
- **Automated Rollouts/Rollbacks**: Progressive deployment strategies
- **Self-Healing**: Automatic restart, replacement, and rescheduling
- **Secret Management**: Centralized config and secret handling
- **Horizontal Scaling**: Scale applications up and down automatically

💡 **Tip**: Start with learning Pods and Deployments first - they're the foundation for understanding all other Kubernetes concepts.

### Core Objects
```
Kubernetes Objects
├── Workloads
│   ├── Pod (smallest deployable unit)
│   ├── Deployment (manages replica sets)
│   ├── StatefulSet (stateful applications)
│   ├── DaemonSet (one pod per node)
│   └── Job (batch workloads)
├── Services & Networking
│   ├── Service (stable network endpoint)
│   ├── Ingress (HTTP/HTTPS routing)
│   └── NetworkPolicy (traffic control)
├── Storage
│   ├── PersistentVolume (storage resource)
│   ├── PersistentVolumeClaim (storage request)
│   └── StorageClass (storage types)
└── Configuration
    ├── ConfigMap (configuration data)
    ├── Secret (sensitive data)
    └── Namespace (resource isolation)
```

## Architecture

### Cluster Components

#### Control Plane (Master)
The control plane manages the cluster and makes global decisions about the cluster (scheduling, scaling, storing cluster data).

```
Control Plane Components
├── kube-apiserver
│   ├── REST API server
│   ├── Authentication & authorization
│   ├── Admission controllers
│   └── etcd communication
├── etcd
│   ├── Distributed key-value store
│   ├── Cluster state storage
│   ├── Configuration data
│   └── Consistent data storage
├── kube-scheduler
│   ├── Pod placement decisions
│   ├── Resource requirements matching
│   ├── Node selection logic
│   └── Constraint satisfaction
├── kube-controller-manager
│   ├── Node controller (node health)
│   ├── Replication controller (desired state)
│   ├── Endpoints controller (service endpoints)
│   └── Service account controller
└── cloud-controller-manager
    ├── Cloud provider integration
    ├── Load balancer management
    └── Volume management
```

#### Worker Nodes
Worker nodes run your application workloads and provide the Kubernetes runtime environment.

```
Worker Node Components
├── kubelet
│   ├── Pod lifecycle management
│   ├── Container runtime communication
│   ├── Volume mounting
│   └── Node status reporting
├── kube-proxy
│   ├── Network proxy service
│   ├── Service load balancing
│   ├── Network rules management
│   └── Service discovery
├── Container Runtime
│   ├── containerd (recommended)
│   ├── CRI-O
│   ├── Docker (deprecated)
│   └── Container lifecycle management
└── Add-ons
    ├── DNS (CoreDNS)
    ├── Dashboard
    ├── Monitoring agents
    └── Logging agents
```

### Networking Model
Kubernetes networking addresses four main concerns:
- **Pod-to-Pod Communication**: All pods can communicate without NAT
- **Pod-to-Service Communication**: Services provide stable endpoints
- **External-to-Service Communication**: Ingress controllers handle external traffic
- **Container-to-Container Communication**: Containers in a pod share network namespace

## Workloads

### Pods
Pods are the smallest deployable units in Kubernetes. A pod represents a single instance of a running process and can contain one or more containers.

**Pod Characteristics:**
- Shared network namespace (IP address and port space)
- Shared storage volumes  
- Atomic deployment unit
- Ephemeral by nature
- Usually managed by higher-level controllers

**What it does**: Group one or more containers that work closely together, sharing network and storage resources.
**Why use it**: Enable tight coupling between containers (like app + sidecar), simplify networking between containers, and provide the smallest deployable unit in Kubernetes.

📖 **Learn More**: [Pods Documentation](https://kubernetes.io/docs/concepts/workloads/pods/) | [Pod Lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/)

**Multi-Container Patterns:**
- **Sidecar**: Helper container alongside main container
- **Ambassador**: Proxy container for external connections
- **Adapter**: Transform output from main container

⚠️ **Warning**: Avoid putting multiple applications in a single pod unless they're tightly coupled and share resources. Each pod should represent a single deployable unit.

### Deployments
Deployments provide declarative updates for Pods and ReplicaSets. They're the most common way to deploy applications.

**Deployment Features:**
- Rolling updates with zero downtime
- Rollback to previous versions
- Scaling replicas up/down
- Pausing and resuming rollouts
- Clean up old replica sets

**What it does**: Manage replica pods with declarative updates, rolling deployments, and automatic rollback capabilities.
**Why use it**: Deploy applications with zero downtime, easily scale based on demand, rollback problematic releases, and maintain desired application state automatically.

📖 **Learn More**: [Deployments Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) | [Rolling Updates](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/)

**Update Strategies:**
- **RollingUpdate**: Gradually replace old pods with new ones
- **Recreate**: Kill all existing pods before creating new ones

### StatefulSets
StatefulSets manage stateful applications that require stable network identities, persistent storage, and ordered deployment/scaling.

**StatefulSet Guarantees:**
- Stable, unique network identifiers
- Stable, persistent storage
- Ordered, graceful deployment and scaling
- Ordered, automated rolling updates

**Use Cases:**
- Databases (MySQL, PostgreSQL, MongoDB)
- Distributed systems (Kafka, Elasticsearch)
- Applications requiring stable hostnames

📖 **Learn More**: [StatefulSets Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) | [StatefulSet Basics](https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/)

### DaemonSets
DaemonSets ensure that all (or some) nodes run a copy of a pod. Useful for cluster-wide services.

**Common Use Cases:**
- Node monitoring agents (Prometheus Node Exporter)
- Log collection agents (Fluentd, Logstash)
- Storage daemons (Ceph, Gluster)
- Network proxies

**What it does**: Ensure exactly one pod runs on each node (or selected nodes) in the cluster, automatically adding pods to new nodes.
**Why use it**: Deploy cluster-wide services that need to run on every node, like monitoring agents, log collectors, or storage daemons.

📖 **Learn More**: [DaemonSets Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

### Jobs and CronJobs
Jobs run pods to completion for batch workloads, while CronJobs run jobs on a schedule.

**Job Types:**
- **Single Job**: Run once to completion
- **Parallel Jobs**: Run multiple pods in parallel
- **Work Queue**: Process items from a queue

**What it does**: Run pods to completion for batch processing, data processing, or scheduled tasks with automatic retry and failure handling.
**Why use it**: Execute one-time tasks (database migrations), batch processing (report generation), or scheduled operations (backups) reliably.

💡 **Tip**: Use Jobs for one-time tasks and CronJobs for recurring scheduled operations. Both ensure tasks complete successfully with built-in retry logic.

📖 **Learn More**: [Jobs Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/job/) | [CronJobs Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

## Services and Networking

### Service Types
Services provide stable network endpoints for accessing pods.

```
Service Types
├── ClusterIP (Default)
│   ├── Internal cluster access only
│   ├── Stable internal IP address
│   └── Load balances across pod replicas
├── NodePort
│   ├── Exposes service on each node's IP
│   ├── Accessible from outside cluster
│   └── Port range: 30000-32767
├── LoadBalancer
│   ├── Cloud provider load balancer
│   ├── External IP address
│   └── Integrates with cloud services
└── ExternalName
    ├── Maps to external DNS name
    ├── No proxying involved
    └── DNS CNAME record
```

📖 **Learn More**: [Services Documentation](https://kubernetes.io/docs/concepts/services-networking/service/) | [Service Types](https://kubernetes.io/docs/tutorials/kubernetes-basics/expose/expose-intro/)

### Ingress
Ingress manages external access to services, typically HTTP/HTTPS traffic.

**Ingress Features:**
- Host-based routing (different domains)
- Path-based routing (URL paths)
- SSL/TLS termination
- Load balancing
- Name-based virtual hosting

**Ingress Controllers:**
- NGINX Ingress Controller
- Traefik
- HAProxy Ingress
- Cloud provider controllers (ALB, GCE)

### Network Policies
Network policies act as firewalls for pods, controlling traffic flow at the IP address or port level.

**Policy Types:**
- **Ingress**: Controls incoming traffic to pods
- **Egress**: Controls outgoing traffic from pods
- **Default Deny**: Block all traffic by default
- **Allow Specific**: Allow traffic from specific sources

## Storage

### Storage Concepts
Kubernetes provides several storage abstractions to manage persistent data.

```
Storage Architecture
├── Volumes
│   ├── Pod-level storage
│   ├── Lifetime tied to pod
│   └── Various volume types
├── Persistent Volumes (PV)
│   ├── Cluster-level storage resource
│   ├── Independent of pod lifecycle
│   └── Provisioned by admin or dynamically
├── Persistent Volume Claims (PVC)
│   ├── Request for storage by user
│   ├── Specifies size and access modes
│   └── Bound to available PV
└── Storage Classes
    ├── Storage type definitions
    ├── Dynamic provisioning
    └── Provider-specific parameters
```

### Volume Types
- **emptyDir**: Temporary storage, deleted when pod removed
- **hostPath**: Mount file/directory from host node
- **configMap/secret**: Mount configuration data
- **persistentVolumeClaim**: Mount persistent storage
- **nfs**: Network file system mount
- **cloud volumes**: AWS EBS, Azure Disk, GCE Persistent Disk

### Access Modes
- **ReadWriteOnce (RWO)**: Mounted read-write by single node
- **ReadOnlyMany (ROX)**: Mounted read-only by many nodes
- **ReadWriteMany (RWX)**: Mounted read-write by many nodes

## Configuration Management

### ConfigMaps
ConfigMaps store non-confidential configuration data in key-value pairs.

**Usage Patterns:**
- Environment variables
- Command-line arguments
- Configuration files in volumes
- Application configuration

**Data Sources:**
- Literal values
- Files
- Directories
- Environment files

### Secrets
Secrets store sensitive information like passwords, OAuth tokens, and SSH keys.

**Secret Types:**
- **Opaque**: Generic secret (default)
- **kubernetes.io/dockerconfigjson**: Docker registry credentials
- **kubernetes.io/tls**: TLS certificate and key
- **kubernetes.io/service-account-token**: Service account token

**Best Practices:**
- Use external secret management systems
- Enable encryption at rest
- Limit access with RBAC
- Rotate secrets regularly

### Environment Variables
Three ways to set environment variables:
- **Direct values**: Hardcoded in pod spec
- **ConfigMap references**: Dynamic configuration
- **Secret references**: Sensitive data
- **Field references**: Pod/container metadata
- **Resource references**: Resource limits/requests

## Security

### Authentication and Authorization

#### Service Accounts
Service accounts provide identities for processes running in pods.

**Types:**
- **Default**: Automatically created per namespace
- **Custom**: Created for specific applications
- **System**: Used by Kubernetes components

#### Role-Based Access Control (RBAC)
RBAC regulates access to Kubernetes resources based on roles.

```
RBAC Components
├── Subjects (Who)
│   ├── Users (human users)
│   ├── Groups (user groups)
│   └── Service Accounts (pod identities)
├── Resources (What)
│   ├── API resources (pods, services, etc.)
│   ├── Resource names (specific instances)
│   └── Subresources (logs, exec, etc.)
├── Verbs (Actions)
│   ├── get, list, watch
│   ├── create, update, patch
│   ├── delete, deletecollection
│   └── Custom verbs
└── Bindings (Assignments)
    ├── RoleBinding (namespace-scoped)
    └── ClusterRoleBinding (cluster-scoped)
```

### Pod Security
Pod security involves controlling what pods can do and how they run.

**Security Contexts:**
- User and group IDs
- Privilege escalation controls
- Read-only root filesystem
- Security profiles (AppArmor, SELinux)
- Capabilities management

**Pod Security Standards:**
- **Privileged**: Unrestricted policy
- **Baseline**: Minimally restrictive policy
- **Restricted**: Heavily restricted policy

### Network Security
Network policies provide firewall rules for pods.

**Policy Components:**
- Pod selector (which pods)
- Policy types (ingress/egress)
- Rules (allowed traffic)
- Namespaces and labels

## Scaling and Automation

### Horizontal Pod Autoscaler (HPA)
HPA automatically scales the number of pods based on observed metrics.

**Scaling Metrics:**
- CPU utilization
- Memory utilization
- Custom metrics (requests per second, queue length)
- External metrics (cloud monitoring)

**Scaling Behavior:**
- Scale-up policies (how fast to scale up)
- Scale-down policies (how fast to scale down)
- Stabilization windows (prevent flapping)

### Vertical Pod Autoscaler (VPA)
VPA automatically adjusts CPU and memory requests/limits for containers.

**VPA Modes:**
- **Off**: Only provide recommendations
- **Initial**: Set requests on pod creation
- **Recreation**: Update requests by recreating pods
- **Auto**: Automatically update requests

### Cluster Autoscaler
Cluster Autoscaler automatically adjusts the number of nodes in a cluster.

**Scaling Decisions:**
- Scale up when pods can't be scheduled
- Scale down when nodes are underutilized
- Respect pod disruption budgets
- Consider node group constraints

## Monitoring and Observability

### The Three Pillars of Observability

#### Metrics
Quantitative measurements of system behavior over time.

**Metric Types:**
- **Counter**: Cumulative values that only increase
- **Gauge**: Values that can go up and down
- **Histogram**: Distribution of values in buckets
- **Summary**: Quantiles of observed values

**Key Metrics:**
- Resource utilization (CPU, memory, disk, network)
- Application metrics (request rate, latency, errors)
- Business metrics (user signups, revenue)

#### Logging
Structured records of events that occurred in the system.

**Log Levels:**
- ERROR: Error conditions
- WARN: Warning conditions
- INFO: Informational messages
- DEBUG: Detailed debugging information

**Centralized Logging:**
- Log aggregation from all pods
- Structured logging (JSON format)
- Log retention policies
- Search and alerting capabilities

#### Tracing
Tracks requests as they flow through distributed systems.

**Distributed Tracing Concepts:**
- **Trace**: Complete journey of a request
- **Span**: Individual operation within a trace
- **Context**: Metadata passed between services
- **Sampling**: Control tracing overhead

### Monitoring Stack
Common monitoring tools in Kubernetes:

- **Prometheus**: Metrics collection and alerting
- **Grafana**: Metrics visualization and dashboards
- **Fluentd/Fluent Bit**: Log collection and forwarding
- **Elasticsearch**: Log storage and search
- **Kibana**: Log visualization
- **Jaeger/Zipkin**: Distributed tracing
- **Alertmanager**: Alert routing and management

## Best Practices

### Resource Management
- Set appropriate resource requests and limits
- Use Quality of Service classes effectively
- Implement resource quotas per namespace
- Monitor resource utilization regularly

### High Availability
- Deploy across multiple zones/regions
- Use anti-affinity rules for critical workloads
- Implement proper health checks
- Plan for node failures and upgrades

### Security
- Follow principle of least privilege
- Use network policies to restrict traffic
- Keep container images up to date
- Scan images for vulnerabilities
- Enable audit logging

### Configuration
- Use ConfigMaps for non-sensitive configuration
- Store secrets in proper secret management systems
- Version your configurations
- Separate configuration from code

### Deployment
- Use rolling updates for zero-downtime deployments
- Implement proper readiness and liveness probes
- Set appropriate resource limits
- Use namespaces for environment separation

### Monitoring
- Monitor both infrastructure and application metrics
- Set up alerting for critical conditions
- Implement distributed tracing for complex applications
- Use structured logging

## Essential kubectl Syntax Reference

Complete syntax guide covering the most important kubectl commands and Kubernetes YAML patterns for practical cluster management:

### Basic kubectl Commands

#### Cluster and Node Information
```bash
# Get cluster information
kubectl cluster-info

# Get node information
kubectl get nodes
kubectl describe node <node-name>

# Get cluster resource usage
kubectl top nodes
kubectl top pods --all-namespaces
```

**What it does**: Display cluster status, node details, and resource consumption across the cluster.
**Why use it**: Monitor cluster health, identify resource bottlenecks, and troubleshoot node-level issues before they affect applications.

📖 **Learn More**: [kubectl Overview](https://kubernetes.io/docs/reference/kubectl/overview/) | [kubectl Commands](https://kubernetes.io/docs/reference/kubectl/kubectl/)

#### Resource Management Commands
```bash
# Get resources
kubectl get pods                           # List pods in current namespace
kubectl get pods -A                        # List pods in all namespaces
kubectl get deployment,service,ingress     # List multiple resource types
kubectl get pods -o wide                   # Show additional information
kubectl get pods --show-labels            # Show resource labels

# Describe resources (detailed information)
kubectl describe pod <pod-name>
kubectl describe service <service-name>
kubectl describe deployment <deployment-name>

# Watch resources in real-time
kubectl get pods -w
kubectl get events -w
```

**What it does**: List, inspect, and monitor Kubernetes resources with filtering and output formatting options.
**Why use it**: Track resource states, debug issues, monitor changes in real-time, and gather detailed information for troubleshooting.

#### Resource Creation and Management
```bash
# Apply configurations
kubectl apply -f deployment.yaml          # Apply single file
kubectl apply -f ./manifests/             # Apply all files in directory
kubectl apply -k ./kustomize/             # Apply Kustomize configuration

# Create resources imperatively
kubectl create deployment nginx --image=nginx:1.21
kubectl create service clusterip nginx --tcp=80:80
kubectl create configmap app-config --from-file=config.properties

# Delete resources
kubectl delete -f deployment.yaml
kubectl delete deployment nginx
kubectl delete pod <pod-name> --force --grace-period=0
```

**What it does**: Create, update, and delete Kubernetes resources using YAML files or imperative commands.
**Why use it**: Deploy applications, update configurations, and clean up resources. Apply is idempotent and safe for production.

#### Pod Operations and Troubleshooting
```bash
# Execute commands in pods
kubectl exec -it <pod-name> -- /bin/bash
kubectl exec <pod-name> -- ls -la /app
kubectl exec <pod-name> -c <container-name> -- env

# View logs
kubectl logs <pod-name>                    # Current logs
kubectl logs <pod-name> -f                 # Follow logs
kubectl logs <pod-name> --previous         # Previous container logs
kubectl logs <pod-name> -c <container-name> # Specific container
kubectl logs -l app=nginx --tail=100       # Logs from labeled pods

# Port forwarding
kubectl port-forward pod/<pod-name> 8080:80
kubectl port-forward service/<service-name> 8080:80
kubectl port-forward deployment/<deployment-name> 8080:80
```

**What it does**: Debug running containers, view application logs, and create temporary network access to services.
**Why use it**: Troubleshoot application issues, debug container problems, test services locally, and inspect running applications.

#### Scaling and Updates
```bash
# Scale deployments
kubectl scale deployment nginx --replicas=5
kubectl scale statefulset database --replicas=3

# Rolling updates
kubectl set image deployment/nginx nginx=nginx:1.22
kubectl rollout status deployment/nginx
kubectl rollout history deployment/nginx
kubectl rollout undo deployment/nginx
kubectl rollout undo deployment/nginx --to-revision=2

# Patch resources
kubectl patch deployment nginx -p '{"spec":{"replicas":3}}'
kubectl patch service nginx -p '{"spec":{"type":"LoadBalancer"}}'
```

**What it does**: Adjust replica counts, update container images, and manage deployment rollouts with rollback capabilities.
**Why use it**: Scale applications based on demand, deploy new versions safely, and quickly rollback if issues occur.

### Essential Kubernetes YAML Patterns

#### Pod Configuration
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-pod
  labels:
    app: web
    version: v1
  annotations:
    deployment.kubernetes.io/revision: "1"
spec:
  serviceAccountName: web-service-account
  securityContext:
    runAsNonRoot: true
    runAsUser: 1000
    fsGroup: 2000
  containers:
  - name: web
    image: nginx:1.21
    ports:
    - containerPort: 80
      name: http
    env:
    - name: ENV
      value: "production"
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: db-secret
          key: password
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 500m
        memory: 512Mi
    livenessProbe:
      httpGet:
        path: /health
        port: 80
      initialDelaySeconds: 30
      periodSeconds: 10
    readinessProbe:
      httpGet:
        path: /ready
        port: 80
      initialDelaySeconds: 5
      periodSeconds: 5
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
    - name: data-volume
      mountPath: /var/data
  volumes:
  - name: config-volume
    configMap:
      name: web-config
  - name: data-volume
    persistentVolumeClaim:
      claimName: web-pvc
  nodeSelector:
    disktype: ssd
  tolerations:
  - key: "environment"
    operator: "Equal"
    value: "production"
    effect: "NoSchedule"
```

**What it does**: Define a complete pod specification with security, resource management, health checks, storage, and scheduling constraints.
**Why use it**: Create production-ready containers with proper security contexts, resource limits, health monitoring, and configuration management.

📖 **Learn More**: [Pod Spec Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/#pod-v1-core) | [Pod Security Context](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)

#### Deployment Configuration
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  labels:
    app: web
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
        version: v1
    spec:
      containers:
      - name: web
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: web
              topologyKey: kubernetes.io/hostname
```

**What it does**: Deploy multiple replica pods with rolling update strategy, health checks, and anti-affinity rules to distribute pods across nodes.
**Why use it**: Ensure high availability, enable zero-downtime deployments, distribute load across multiple instances, and prevent single points of failure.

📖 **Learn More**: [Deployment API Reference](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.28/#deployment-v1-apps) | [Pod Anti-Affinity](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#affinity-and-anti-affinity)

#### Service Configuration
```yaml
# ClusterIP Service (internal access)
apiVersion: v1
kind: Service
metadata:
  name: web-service
  labels:
    app: web
spec:
  type: ClusterIP
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
    name: http

---
# LoadBalancer Service (external access)
apiVersion: v1
kind: Service
metadata:
  name: web-external
  annotations:
    service.beta.kubernetes.io/aws-load-balancer-type: nlb
spec:
  type: LoadBalancer
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
    name: http

---
# NodePort Service (node-based access)
apiVersion: v1
kind: Service
metadata:
  name: web-nodeport
spec:
  type: NodePort
  selector:
    app: web
  ports:
  - port: 80
    targetPort: 80
    nodePort: 30080
```

**What it does**: Provide stable network endpoints for pod access using different service types for internal, external, and node-based connectivity.
**Why use it**: Enable service discovery, load balancing across pod replicas, and expose applications internally or externally based on requirements.

#### ConfigMap and Secret Management
```yaml
# ConfigMap for application configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  app.properties: |
    environment=production
    debug=false
    max_connections=100
  database.url: "postgresql://db.example.com:5432/app"
  redis.url: "redis://cache.example.com:6379"
  
---
# Secret for sensitive data
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
type: Opaque
data:
  db-password: cGFzc3dvcmQxMjM=  # base64 encoded
  api-key: YWJjZGVmZ2hpams=      # base64 encoded
stringData:
  jwt-secret: "my-jwt-secret-key"  # automatically base64 encoded

---
# Pod using ConfigMap and Secret
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: myapp:v1
    env:
    - name: DATABASE_URL
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database.url
    - name: DB_PASSWORD
      valueFrom:
        secretKeyRef:
          name: app-secrets
          key: db-password
    envFrom:
    - configMapRef:
        name: app-config
    - secretRef:
        name: app-secrets
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
    - name: secret-volume
      mountPath: /etc/secrets
      readOnly: true
  volumes:
  - name: config-volume
    configMap:
      name: app-config
  - name: secret-volume
    secret:
      secretName: app-secrets
      defaultMode: 0400
```

**What it does**: Store and inject configuration data and sensitive information into pods through environment variables and mounted volumes.
**Why use it**: Separate configuration from code, manage environment-specific settings, secure sensitive data, and enable configuration updates without rebuilding images.

📖 **Learn More**: [ConfigMaps Documentation](https://kubernetes.io/docs/concepts/configuration/configmap/) | [Secrets Documentation](https://kubernetes.io/docs/concepts/configuration/secret/)

#### Persistent Storage Configuration
```yaml
# StorageClass definition
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-ssd
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp3
  fsType: ext4
  encrypted: "true"
allowVolumeExpansion: true
reclaimPolicy: Delete

---
# PersistentVolumeClaim
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: database-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: fast-ssd
  resources:
    requests:
      storage: 20Gi

---
# StatefulSet using persistent storage
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: database
spec:
  serviceName: database
  replicas: 3
  selector:
    matchLabels:
      app: database
  template:
    metadata:
      labels:
        app: database
    spec:
      containers:
      - name: postgres
        image: postgres:14
        env:
        - name: POSTGRES_DB
          value: myapp
        - name: POSTGRES_USER
          value: postgres
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: db-secret
              key: password
        volumeMounts:
        - name: data
          mountPath: /var/lib/postgresql/data
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
          limits:
            cpu: 1000m
            memory: 2Gi
  volumeClaimTemplates:
  - metadata:
      name: data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 20Gi
```

**What it does**: Provision persistent storage with storage classes, claims, and StatefulSet volume templates for stateful applications.
**Why use it**: Provide durable storage for databases, maintain data across pod restarts, enable automatic volume provisioning, and ensure data persistence.

#### Ingress and Traffic Management
```yaml
# Ingress for HTTP/HTTPS routing
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: web-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - app.example.com
    - api.example.com
    secretName: app-tls-secret
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: web-service
            port:
              number: 80
  - host: api.example.com
    http:
      paths:
      - path: /api/v1
        pathType: Prefix
        backend:
          service:
            name: api-service
            port:
              number: 80
      - path: /health
        pathType: Exact
        backend:
          service:
            name: health-service
            port:
              number: 8080
```

**What it does**: Route external HTTP/HTTPS traffic to internal services based on hostnames and paths with SSL termination and rate limiting.
**Why use it**: Expose multiple services through a single entry point, handle SSL certificates automatically, implement traffic policies, and provide external access control.

#### Horizontal Pod Autoscaler
```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-deployment
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  - type: Pods
    pods:
      metric:
        name: requests_per_second
      target:
        type: AverageValue
        averageValue: "50"
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300
      policies:
      - type: Percent
        value: 10
        periodSeconds: 60
    scaleUp:
      stabilizationWindowSeconds: 60
      policies:
      - type: Pods
        value: 2
        periodSeconds: 60
```

**What it does**: Automatically scale pod replicas based on CPU, memory, and custom metrics with configurable scaling behavior and stabilization windows.
**Why use it**: Handle traffic spikes automatically, optimize resource utilization, maintain performance under load, and reduce costs during low-traffic periods.

📖 **Learn More**: [HPA Documentation](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) | [HPA Walkthrough](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale-walkthrough/)

#### Network Policy for Security
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: web-network-policy
spec:
  podSelector:
    matchLabels:
      app: web
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Allow traffic from ingress controller
  - from:
    - namespaceSelector:
        matchLabels:
          name: ingress-nginx
  # Allow traffic from same namespace
  - from:
    - podSelector:
        matchLabels:
          role: frontend
    ports:
    - protocol: TCP
      port: 80
  egress:
  # Allow DNS queries
  - to: []
    ports:
    - protocol: UDP
      port: 53
    - protocol: TCP
      port: 53
  # Allow database access
  - to:
    - podSelector:
        matchLabels:
          app: database
    ports:
    - protocol: TCP
      port: 5432
  # Allow external HTTPS
  - to: []
    ports:
    - protocol: TCP
      port: 443
```

**What it does**: Control network traffic between pods and external services using firewall-like rules based on labels, namespaces, and ports.
**Why use it**: Implement zero-trust networking, isolate services, prevent lateral movement in security breaches, and enforce compliance requirements.

📖 **Learn More**: [Network Policies Documentation](https://kubernetes.io/docs/concepts/services-networking/network-policies/) | [Network Policy Recipes](https://github.com/ahmetb/kubernetes-network-policy-recipes)

This comprehensive syntax reference provides the essential Kubernetes patterns needed for production deployments with proper security, scalability, and operational practices.

## Tool Integration Guide

Kubernetes works best when combined with other DevOps tools for a complete infrastructure solution:

### Infrastructure Provisioning
🔗 **Related**: See [Terraform Essential Guide](./TERRAFORM.md) for infrastructure setup
- **Terraform**: Provision cloud infrastructure (VPCs, subnets, managed K8s clusters)
- **Cloud Provider Managed Services**: EKS (AWS), AKS (Azure), GKE (GCP)
- **Infrastructure Setup**: Create cluster, node groups, networking, and IAM roles

### Configuration Management  
🔗 **Related**: See [Ansible Essential Guide](./ANSIBLE.md) for system configuration
- **Ansible**: Configure worker nodes, install dependencies, manage OS-level settings
- **Node Bootstrap**: Install container runtime, configure networking, security hardening
- **Application Deployment**: Use Ansible to deploy initial applications or update configurations

### Integration Workflow
```
1. Terraform → Provision Infrastructure
   ├── Create VPC and networking
   ├── Set up managed Kubernetes cluster  
   ├── Configure IAM roles and policies
   └── Provision storage and networking components

2. Ansible → Configure Nodes and Dependencies  
   ├── Configure worker node OS settings
   ├── Install monitoring agents
   ├── Set up log collection
   └── Deploy cluster-wide tools

3. Kubernetes → Deploy and Manage Applications
   ├── Deploy application workloads
   ├── Manage secrets and configurations
   ├── Set up services and ingress
   └── Monitor and scale applications
```

💡 **Best Practice**: Use infrastructure-as-code tools (Terraform) for cluster provisioning, configuration management tools (Ansible) for node setup, and Kubernetes native tools (kubectl, Helm) for application management.

## Quick Troubleshooting Guide

### Common Issues and Solutions

#### Pod Issues
```bash
# Pod won't start
kubectl describe pod <pod-name>              # Check events and conditions
kubectl logs <pod-name>                      # Check application logs
kubectl get events --sort-by=.metadata.creationTimestamp  # Recent events

# Common causes:
# - Image pull failures → Check image name and registry access
# - Resource limits → Check requests/limits vs node capacity  
# - Configuration errors → Check environment variables and config maps
```

#### Service/Networking Issues  
```bash
# Service not accessible
kubectl get svc                              # Check service configuration
kubectl get endpoints <service-name>         # Check if pods are selected
kubectl describe svc <service-name>          # Check selector and ports

# DNS issues
kubectl exec -it <pod-name> -- nslookup <service-name>  # Test DNS resolution
kubectl get pods -n kube-system -l k8s-app=kube-dns    # Check CoreDNS pods
```

#### Resource Issues
```bash
# Node resource problems  
kubectl top nodes                            # Check node resource usage
kubectl describe node <node-name>            # Check node conditions
kubectl get pods --all-namespaces -o wide    # See pod distribution

# Storage issues
kubectl get pv,pvc                          # Check persistent volumes
kubectl describe pvc <pvc-name>             # Check volume binding status
```

⚠️ **Pro Tip**: Always check `kubectl get events` first - it shows the most recent cluster events and often reveals the root cause of issues.

📝 **Debug Workflow**:
1. Check pod status: `kubectl get pods`
2. Examine pod details: `kubectl describe pod <name>`  
3. Check logs: `kubectl logs <name>`
4. Check events: `kubectl get events`
5. Verify configuration: Check services, configmaps, secrets
6. Test connectivity: Use debug pods for network testing

## Real-World Production Example

### E-commerce Microservices Platform on Kubernetes

This example demonstrates a production-ready microservices e-commerce platform deployed on Kubernetes with best practices for scalability, security, and observability.

```yaml
# Namespace for the application
apiVersion: v1
kind: Namespace
metadata:
  name: ecommerce-prod
  labels:
    pod-security.kubernetes.io/enforce: baseline
    pod-security.kubernetes.io/audit: restricted
    pod-security.kubernetes.io/warn: restricted
```

**What it does**: Create an isolated namespace for the e-commerce application with Pod Security Standards to enforce security policies.
**Why use it**: Separate production workloads from other environments, apply consistent security policies, and organize resources logically.

```yaml
---
# ConfigMap for application configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: ecommerce-prod
data:
  database.host: "postgresql.ecommerce-prod.svc.cluster.local"
  database.port: "5432"
  database.name: "ecommerce"
  redis.host: "redis.ecommerce-prod.svc.cluster.local"
  redis.port: "6379"
  rabbitmq.host: "rabbitmq.ecommerce-prod.svc.cluster.local"
  rabbitmq.port: "5672"
  log.level: "info"
  metrics.enabled: "true"
  tracing.enabled: "true"
  jaeger.endpoint: "http://jaeger-collector:14268/api/traces"
```

**What it does**: Store non-sensitive configuration data like service endpoints, ports, and feature flags for all microservices.
**Why use it**: Centralize configuration management, enable environment-specific settings, and update configurations without rebuilding container images.

```yaml
---
# Secret for database credentials
apiVersion: v1
kind: Secret
metadata:
  name: database-secret
  namespace: ecommerce-prod
type: Opaque
data:
  username: cG9zdGdyZXM=  # postgres
  password: c3VwZXJzZWNyZXQ=  # supersecret
```

**What it does**: Securely store sensitive database credentials using base64 encoding and Kubernetes secret management.
**Why use it**: Protect sensitive data from unauthorized access, enable secure credential distribution to pods, and maintain security compliance.

```yaml
---
# PostgreSQL StatefulSet
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgresql
  namespace: ecommerce-prod
spec:
  serviceName: postgresql
  replicas: 1
  selector:
    matchLabels:
      app: postgresql
  template:
    metadata:
      labels:
        app: postgresql
    spec:
      containers:
      - name: postgresql
        image: postgres:14
        ports:
        - containerPort: 5432
          name: postgresql
        env:
        - name: POSTGRES_DB
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.name
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: password
        volumeMounts:
        - name: postgresql-data
          mountPath: /var/lib/postgresql/data
        resources:
          requests:
            cpu: 500m
            memory: 1Gi
          limits:
            cpu: 1000m
            memory: 2Gi
        livenessProbe:
          exec:
            command:
            - pg_isready
            - -U
            - postgres
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          exec:
            command:
            - pg_isready
            - -U
            - postgres
          initialDelaySeconds: 5
          periodSeconds: 5
  volumeClaimTemplates:
  - metadata:
      name: postgresql-data
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: fast-ssd
      resources:
        requests:
          storage: 20Gi
```

**What it does**: Deploy a PostgreSQL database as a StatefulSet with persistent storage, health checks, and resource management.
**Why use it**: Provide stateful database storage with stable network identity, persistent data across pod restarts, and proper resource allocation for database workloads.

```yaml
---
# PostgreSQL Service
apiVersion: v1
kind: Service
metadata:
  name: postgresql
  namespace: ecommerce-prod
spec:
  selector:
    app: postgresql
  ports:
  - port: 5432
    targetPort: 5432
  type: ClusterIP
```

**What it does**: Create a ClusterIP service to provide stable network access to the PostgreSQL database within the cluster.
**Why use it**: Enable other services to connect to the database using a consistent DNS name, load balance connections, and abstract database pod details.

```yaml
---
# Redis Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  namespace: ecommerce-prod
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
    spec:
      containers:
      - name: redis
        image: redis:7-alpine
        ports:
        - containerPort: 6379
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 200m
            memory: 256Mi
        livenessProbe:
          tcpSocket:
            port: 6379
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          tcpSocket:
            port: 6379
          initialDelaySeconds: 5
          periodSeconds: 5

---
# Redis Service
apiVersion: v1
kind: Service
metadata:
  name: redis
  namespace: ecommerce-prod
spec:
  selector:
    app: redis
  ports:
  - port: 6379
    targetPort: 6379

---
# User Service Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
  namespace: ecommerce-prod
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
        version: v1
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
        prometheus.io/path: "/metrics"
    spec:
      serviceAccountName: ecommerce-sa
      securityContext:
        runAsNonRoot: true
        runAsUser: 1000
        fsGroup: 1000
      containers:
      - name: user-service
        image: ecommerce/user-service:v1.2.3
        ports:
        - containerPort: 8080
          name: http
        - containerPort: 8081
          name: management
        env:
        - name: DATABASE_URL
          value: "postgresql://$(POSTGRES_USER):$(POSTGRES_PASSWORD)@$(DATABASE_HOST):$(DATABASE_PORT)/$(DATABASE_NAME)"
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: password
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.host
        - name: DATABASE_PORT
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.port
        - name: DATABASE_NAME
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database.name
        - name: REDIS_URL
          value: "redis://$(REDIS_HOST):$(REDIS_PORT)"
        - name: REDIS_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: redis.host
        - name: REDIS_PORT
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: redis.port
        envFrom:
        - configMapRef:
            name: app-config
        resources:
          requests:
            cpu: 200m
            memory: 256Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8081
          initialDelaySeconds: 60
          periodSeconds: 10
          timeoutSeconds: 5
        readinessProbe:
          httpGet:
            path: /ready
            port: 8081
          initialDelaySeconds: 10
          periodSeconds: 5
          timeoutSeconds: 3
        startupProbe:
          httpGet:
            path: /startup
            port: 8081
          initialDelaySeconds: 10
          periodSeconds: 10
          failureThreshold: 30
        securityContext:
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: true
          capabilities:
            drop:
            - ALL
        volumeMounts:
        - name: tmp-volume
          mountPath: /tmp
      volumes:
      - name: tmp-volume
        emptyDir: {}
      affinity:
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app: user-service
              topologyKey: kubernetes.io/hostname

---
# User Service Service
apiVersion: v1
kind: Service
metadata:
  name: user-service
  namespace: ecommerce-prod
  annotations:
    prometheus.io/scrape: "true"
    prometheus.io/port: "8080"
spec:
  selector:
    app: user-service
  ports:
  - name: http
    port: 80
    targetPort: 8080
  - name: management
    port: 8081
    targetPort: 8081

---
# Product Service Deployment (similar structure)
apiVersion: apps/v1
kind: Deployment
metadata:
  name: product-service
  namespace: ecommerce-prod
spec:
  replicas: 5
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 2
  selector:
    matchLabels:
      app: product-service
  template:
    metadata:
      labels:
        app: product-service
        version: v1
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
    spec:
      serviceAccountName: ecommerce-sa
      containers:
      - name: product-service
        image: ecommerce/product-service:v1.2.3
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: app-config
        env:
        - name: DATABASE_URL
          value: "postgresql://$(POSTGRES_USER):$(POSTGRES_PASSWORD)@$(DATABASE_HOST):$(DATABASE_PORT)/$(DATABASE_NAME)"
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: password
        resources:
          requests:
            cpu: 300m
            memory: 384Mi
          limits:
            cpu: 800m
            memory: 768Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5

---
# Product Service Service
apiVersion: v1
kind: Service
metadata:
  name: product-service
  namespace: ecommerce-prod
spec:
  selector:
    app: product-service
  ports:
  - port: 80
    targetPort: 8080

---
# Order Service Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  namespace: ecommerce-prod
spec:
  replicas: 4
  selector:
    matchLabels:
      app: order-service
  template:
    metadata:
      labels:
        app: order-service
        version: v1
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8080"
    spec:
      serviceAccountName: ecommerce-sa
      containers:
      - name: order-service
        image: ecommerce/order-service:v1.2.3
        ports:
        - containerPort: 8080
        envFrom:
        - configMapRef:
            name: app-config
        env:
        - name: DATABASE_URL
          value: "postgresql://$(POSTGRES_USER):$(POSTGRES_PASSWORD)@$(DATABASE_HOST):$(DATABASE_PORT)/$(DATABASE_NAME)"
        - name: POSTGRES_USER
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: username
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: database-secret
              key: password
        resources:
          requests:
            cpu: 250m
            memory: 320Mi
          limits:
            cpu: 600m
            memory: 640Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 60
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 10
          periodSeconds: 5

---
# Order Service Service
apiVersion: v1
kind: Service
metadata:
  name: order-service
  namespace: ecommerce-prod
spec:
  selector:
    app: order-service
  ports:
  - port: 80
    targetPort: 8080

---
# API Gateway Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: api-gateway
  namespace: ecommerce-prod
spec:
  replicas: 3
  selector:
    matchLabels:
      app: api-gateway
  template:
    metadata:
      labels:
        app: api-gateway
        version: v1
    spec:
      serviceAccountName: ecommerce-sa
      containers:
      - name: api-gateway
        image: ecommerce/api-gateway:v1.2.3
        ports:
        - containerPort: 8080
        env:
        - name: USER_SERVICE_URL
          value: "http://user-service.ecommerce-prod.svc.cluster.local"
        - name: PRODUCT_SERVICE_URL
          value: "http://product-service.ecommerce-prod.svc.cluster.local"
        - name: ORDER_SERVICE_URL
          value: "http://order-service.ecommerce-prod.svc.cluster.local"
        resources:
          requests:
            cpu: 200m
            memory: 256Mi
          limits:
            cpu: 500m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5

---
# API Gateway Service
apiVersion: v1
kind: Service
metadata:
  name: api-gateway
  namespace: ecommerce-prod
spec:
  selector:
    app: api-gateway
  ports:
  - port: 80
    targetPort: 8080

---
# Ingress for external access
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ecommerce-ingress
  namespace: ecommerce-prod
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/rate-limit: "100"
    nginx.ingress.kubernetes.io/rate-limit-window: "1m"
spec:
  tls:
  - hosts:
    - api.ecommerce.com
    secretName: ecommerce-tls
  rules:
  - host: api.ecommerce.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: api-gateway
            port:
              number: 80

---
# Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: ecommerce-sa
  namespace: ecommerce-prod

---
# HPA for User Service
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: user-service-hpa
  namespace: ecommerce-prod
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: user-service
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80

---
# HPA for Product Service
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: product-service-hpa
  namespace: ecommerce-prod
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: product-service
  minReplicas: 5
  maxReplicas: 20
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70

---
# Pod Disruption Budget
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: user-service-pdb
  namespace: ecommerce-prod
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: user-service

---
# Network Policy
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: ecommerce-network-policy
  namespace: ecommerce-prod
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
  ingress:
  # Allow ingress from same namespace
  - from:
    - namespaceSelector:
        matchLabels:
          name: ecommerce-prod
  # Allow ingress from ingress controller
  - from:
    - namespaceSelector:
        matchLabels:
          name: ingress-nginx
  egress:
  # Allow DNS
  - to: []
    ports:
    - protocol: UDP
      port: 53
  # Allow egress within namespace
  - to:
    - namespaceSelector:
        matchLabels:
          name: ecommerce-prod
  # Allow HTTPS to external services
  - to: []
    ports:
    - protocol: TCP
      port: 443
```

**What it does**: Implement network security policies that control traffic flow between pods and external services using label-based rules.
**Why use it**: Create zero-trust networking, prevent unauthorized access between services, restrict egress traffic, and comply with security requirements.

### Essential Kubernetes Concepts Explained

The production example above demonstrates key Kubernetes concepts with practical applications:

#### Resource Organization
- **Namespace**: Isolates the e-commerce application from other workloads
- **Labels and Selectors**: Organize and connect related resources
- **Annotations**: Store metadata for tools like Prometheus and cert-manager

#### Workload Management
- **StatefulSet (PostgreSQL)**: Provides stable storage and network identity for database
- **Deployment (Services)**: Manages replicated application instances with rolling updates
- **Pod Security Context**: Runs containers with non-root users and restricted privileges

#### Configuration and Secrets
- **ConfigMap**: Stores non-sensitive configuration like service endpoints and feature flags
- **Secret**: Securely manages database passwords and API keys
- **Environment Variables**: Injects configuration into containers at runtime

#### Networking and Traffic
- **Services**: Provide stable endpoints for pod-to-pod communication
- **Ingress**: Routes external HTTP/HTTPS traffic to internal services with SSL termination
- **Network Policies**: Control traffic flow for security isolation

#### Storage and Persistence
- **PersistentVolumeClaim**: Requests storage for database data
- **VolumeClaimTemplates**: Automatically creates storage for each StatefulSet replica
- **StorageClass**: Defines storage characteristics and provisioning

#### Scaling and Availability
- **HorizontalPodAutoscaler**: Automatically scales based on CPU/memory metrics
- **Pod Anti-Affinity**: Distributes replicas across different nodes
- **Pod Disruption Budget**: Ensures minimum availability during maintenance

#### Health and Monitoring
- **Liveness Probes**: Restart unhealthy containers automatically
- **Readiness Probes**: Remove unready pods from service load balancing  
- **Resource Requests/Limits**: Ensure proper scheduling and prevent resource exhaustion

### Architecture Overview

This production example demonstrates:

```
E-commerce Microservices Architecture
├── External Layer
│   ├── Ingress Controller (NGINX)
│   ├── SSL/TLS Termination
│   ├── Rate Limiting
│   └── External DNS
├── API Gateway Layer
│   ├── Request Routing
│   ├── Authentication
│   ├── Rate Limiting
│   └── Request/Response Transformation
├── Microservices Layer
│   ├── User Service (3-10 replicas)
│   ├── Product Service (5-20 replicas)
│   ├── Order Service (4 replicas)
│   └── Auto-scaling based on CPU/Memory
├── Data Layer
│   ├── PostgreSQL (StatefulSet)
│   ├── Redis Cache (Deployment)
│   └── Persistent Storage
└── Cross-cutting Concerns
    ├── Service Mesh (Istio/Linkerd)
    ├── Monitoring (Prometheus/Grafana)
    ├── Logging (Fluentd/Elasticsearch)
    ├── Tracing (Jaeger)
    └── Security (RBAC/Network Policies)
```

### Key Production Features

**High Availability:**
- Multi-replica deployments across nodes
- Pod anti-affinity rules
- Pod disruption budgets
- Health checks (liveness, readiness, startup)

**Scalability:**
- Horizontal Pod Autoscaler based on CPU/memory
- Resource requests/limits for optimal scheduling
- Load balancing across service replicas

**Security:**
- Network policies for traffic control
- Service accounts with minimal privileges
- Secret management for credentials
- Pod security contexts
- Read-only root filesystems

**Observability:**
- Prometheus metrics scraping
- Structured logging
- Distributed tracing with Jaeger
- Health check endpoints

**Operational Excellence:**
- Rolling updates with zero downtime
- Configuration management with ConfigMaps
- Resource quotas and limits
- Proper labeling and organization

This example showcases how Kubernetes enables you to run complex, production-ready microservices architectures with enterprise-grade features for scalability, security, and reliability.