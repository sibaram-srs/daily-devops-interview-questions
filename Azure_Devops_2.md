# Senior Azure DevOps Engineer Interview Questions & Answers (Part 1)

---

# 1. Trying to clone a repository and getting a 403 error. What will you do?

## Answer

A **403 Forbidden** error means authentication succeeded (or reached the server), but the authenticated user does not have permission to access the repository.

### Troubleshooting Steps

### 1. Verify Repository Access

Check whether:
- The repository exists.
- My user account has Read/Clone permission.
- The repository is private or public.

For Azure DevOps:
- Project Settings → Repositories → Security

For GitHub:
- Repository → Settings → Collaborators & Teams

---

### 2. Verify Authentication Method

If using HTTPS:

```bash
git remote -v
```

Example:

```bash
https://github.com/company/project.git
```

If credentials have expired:

- Update Personal Access Token (PAT)
- Clear cached credentials

Windows:

```text
Credential Manager
```

Linux:

```bash
git credential-cache exit
```

---

### 3. Verify Personal Access Token (PAT)

Common issues:

- PAT expired
- Missing Repository Read permission
- Organization policy changed

Generate a new PAT and update Git credentials.

---

### 4. Verify SSH Key (if using SSH)

Check:

```bash
ssh -T git@github.com
```

or

```bash
ssh -T git@ssh.dev.azure.com
```

If authentication fails:

- Generate new SSH key
- Add public key to GitHub/Azure DevOps

---

### 5. Verify Repository URL

Sometimes the remote URL is incorrect.

Check:

```bash
git remote -v
```

Update if necessary:

```bash
git remote set-url origin <repo-url>
```

---

### 6. Check Organization Policies

Large organizations may restrict:

- IP addresses
- Conditional Access
- MFA
- VPN access

Verify with the Azure DevOps or GitHub administrator.

---

## Interview Highlight

> "Whenever I see a 403 error, I first verify repository permissions, then validate the authentication method (PAT or SSH), check token expiration, and finally review organization security policies."

---

# 2. How to reduce Docker image size if it's 2.5 GB?

## Answer

A 2.5 GB Docker image is usually caused by unnecessary packages, build tools, caches, or large base images.

### Best Practices

### 1. Use Smaller Base Images

Instead of:

```dockerfile
FROM ubuntu
```

Use:

```dockerfile
FROM alpine
```

or

```dockerfile
FROM python:3.12-alpine
```

---

### 2. Multi-Stage Builds

Example:

```dockerfile
FROM maven:3.9 AS build

COPY . .

RUN mvn clean package

FROM eclipse-temurin:17-jre

COPY --from=build target/app.jar app.jar

ENTRYPOINT ["java","-jar","app.jar"]
```

Build dependencies remain in the first stage only.

---

### 3. Remove Package Cache

Example:

```dockerfile
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

---

### 4. Use .dockerignore

Example:

```text
.git
node_modules
logs
tests
*.md
```

---

### 5. Combine RUN Commands

Bad:

```dockerfile
RUN apt update

RUN apt install curl

RUN apt clean
```

Good:

```dockerfile
RUN apt update && \
    apt install -y curl && \
    apt clean
```

Reduces image layers.

---

### 6. Remove Unused Files

Delete:

- Temp files
- Logs
- Build artifacts
- Test data

---

### 7. Scan Image

Use:

```bash
docker image inspect
docker history image-name
dive image-name
```

---

## Interview Highlight

> "In production, I primarily use multi-stage builds, lightweight base images such as Alpine or Distroless, and `.dockerignore` to significantly reduce image size and improve deployment speed."

---

# 3. Difference between CMD and ENTRYPOINT in Docker

| CMD | ENTRYPOINT |
|------|------------|
| Provides default command | Defines the main executable |
| Can be overridden easily | Always executes unless explicitly overridden |
| Optional | Usually mandatory |
| Best for default arguments | Best for container startup |

---

### Example CMD

```dockerfile
FROM nginx

CMD ["nginx","-g","daemon off;"]
```

Override:

```bash
docker run image ls
```

The `ls` command replaces CMD.

---

### Example ENTRYPOINT

```dockerfile
ENTRYPOINT ["python"]
```

Run:

```bash
docker run image app.py
```

Actually executes:

```bash
python app.py
```

---

### Best Practice

```dockerfile
ENTRYPOINT ["python"]

CMD ["app.py"]
```

Result:

```bash
python app.py
```

Can override only the filename.

---

## Interview Highlight

> "I use ENTRYPOINT to define the container's executable and CMD to provide default arguments, offering both consistency and flexibility."

---

# 4. Difference between Ingress Controller and API Gateway

| Ingress Controller | API Gateway |
|-------------------|-------------|
| Kubernetes resource | API management layer |
| Routes HTTP/HTTPS traffic | Manages APIs |
| L7 Load Balancer | API proxy |
| Path-based routing | Authentication |
| SSL termination | Rate limiting |
| Basic routing | JWT validation |
| Works inside Kubernetes | Can serve external clients |

---

### Ingress Flow

```
Internet
     │
Ingress Controller
     │
Ingress Rules
     │
Service
     │
Pod
```

---

### API Gateway Flow

```
Client
   │
API Gateway
   │
Authentication
   │
Rate Limiting
   │
Logging
   │
Microservice
```

---

### Azure Example

Ingress:

- NGINX Ingress Controller
- Azure Application Gateway Ingress Controller (AGIC)

API Gateway:

- Azure API Management (APIM)

---

## Interview Highlight

> "Ingress exposes Kubernetes services internally within the cluster, whereas an API Gateway provides enterprise API capabilities such as authentication, throttling, versioning, and analytics."

---

# 5. Difference between PV and PVC in Kubernetes

## Persistent Volume (PV)

A Persistent Volume is the actual storage resource in the cluster.

Examples:

- Azure Disk
- Azure Files
- NFS
- AWS EBS

---

## Persistent Volume Claim (PVC)

A PVC is a request for storage made by a Pod.

---

### Relationship

```
Pod
 │
PVC
 │
PV
 │
Azure Disk
```

---

### Example

PV:

```yaml
capacity:
  storage: 20Gi
```

PVC:

```yaml
resources:
  requests:
    storage: 10Gi
```

The PVC binds to a matching PV.

---

## Interview Highlight

> "Think of a PV as the storage resource and a PVC as the storage request from an application. Kubernetes binds them automatically based on size and access mode."

---

# 6. How do you monitor Kubernetes applications?

## Answer

Monitoring typically consists of four pillars:

- Metrics
- Logs
- Events
- Traces

---

### Common Monitoring Stack

```
Pods
 │
Prometheus
 │
Grafana

Logs
 │
Fluent Bit
 │
Azure Monitor / Log Analytics

Tracing
 │
OpenTelemetry
 │
Jaeger
```

---

### Azure AKS Monitoring

I typically use:

- Azure Monitor
- Log Analytics Workspace
- Container Insights
- Managed Prometheus
- Managed Grafana
- Microsoft Defender for Cloud

---

### Metrics Monitored

Infrastructure:

- CPU
- Memory
- Node health
- Disk
- Network

Application:

- Response time
- Error rate
- Request count
- Pod restarts
- Availability

---

### Alerts

Configure alerts for:

- High CPU
- High Memory
- CrashLoopBackOff
- Pending Pods
- Node NotReady
- Disk utilization

---

## Interview Highlight

> "In AKS, I use Azure Monitor with Container Insights for infrastructure monitoring, Managed Prometheus and Grafana for metrics visualization, and Log Analytics for centralized logging and alerting."

---

# 7. Explain the working of Container Network Interface (CNI) in Kubernetes

## Answer

CNI (Container Network Interface) is responsible for assigning IP addresses and configuring networking for Pods.

---

### Flow

```
Pod Created
     │
Kubelet
     │
CNI Plugin
     │
Assign IP
     │
Configure Routes
     │
Pod Ready
```

---

### Popular CNI Plugins

- Azure CNI
- Azure CNI Overlay
- Calico
- Cilium
- Flannel

---

### Azure CNI

Each Pod receives an IP address from the Azure VNet.

Benefits:

- Direct communication
- NSG support
- Private networking
- Simplified integration with Azure services

---

## Interview Highlight

> "In AKS, I commonly use Azure CNI, where Pods receive IP addresses directly from the Azure Virtual Network, enabling seamless communication with other Azure resources."

---

# 8. Why do we require multiple containers in a Pod?

## Answer

A Pod can contain multiple tightly coupled containers that need to share the same lifecycle, storage, and network namespace.

---

### Common Use Cases

- Sidecar logging
- Monitoring agents
- Reverse proxy
- Service mesh proxy
- Configuration synchronization

---

### Example

```
Pod
│
├── Application Container
├── Fluent Bit Sidecar
├── Envoy Proxy
└── Monitoring Agent
```

---

### Shared Resources

Containers in the same Pod share:

- Network namespace
- localhost
- Volumes
- Lifecycle

---

### Advantages

- Simplified communication using localhost
- Shared storage
- Coordinated startup and shutdown
- Easier deployment of supporting services

---

## Interview Highlight

> "Multiple containers in a Pod are used when they are tightly coupled and need to work together. A common example is an application container with a Fluent Bit sidecar for log forwarding or an Envoy proxy for service mesh."

---

# Part 1 Summary

Covered Topics:

- Git 403 Troubleshooting
- Docker Image Optimization
- CMD vs ENTRYPOINT
- Ingress vs API Gateway
- Persistent Volume vs PVC
- Kubernetes Monitoring
- Container Network Interface (CNI)
- Multi-Container Pods
