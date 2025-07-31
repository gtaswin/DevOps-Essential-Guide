# Kubernetes Essential Guide

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

**Multi-Container Patterns:**
- **Sidecar**: Helper container alongside main container
- **Ambassador**: Proxy container for external connections
- **Adapter**: Transform output from main container

### Deployments
Deployments provide declarative updates for Pods and ReplicaSets. They're the most common way to deploy applications.

**Deployment Features:**
- Rolling updates with zero downtime
- Rollback to previous versions
- Scaling replicas up/down
- Pausing and resuming rollouts
- Clean up old replica sets

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

### DaemonSets
DaemonSets ensure that all (or some) nodes run a copy of a pod. Useful for cluster-wide services.

**Common Use Cases:**
- Node monitoring agents (Prometheus Node Exporter)
- Log collection agents (Fluentd, Logstash)
- Storage daemons (Ceph, Gluster)
- Network proxies

### Jobs and CronJobs
Jobs run pods to completion for batch workloads, while CronJobs run jobs on a schedule.

**Job Types:**
- **Single Job**: Run once to completion
- **Parallel Jobs**: Run multiple pods in parallel
- **Work Queue**: Process items from a queue

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