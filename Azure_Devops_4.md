# Senior Azure DevOps Engineer Interview Questions & Answers (Part 3)

---

# 17. In a YAML Pipeline, what is a Template?

## Answer

A **YAML Template** is a reusable YAML file that helps eliminate code duplication across multiple Azure DevOps pipelines.

Instead of writing the same build, test, or deployment logic in every pipeline, we create templates and reference them from the main pipeline.

---

## Why Use Templates?

Benefits:

- Code reusability
- Standardization
- Easy maintenance
- Reduced duplication
- Consistent deployments
- Easier governance

---

## Project Structure

```text
azure-pipelines.yml

templates/
│
├── build.yml
├── deploy.yml
├── terraform-plan.yml
├── terraform-apply.yml
└── helm-deploy.yml
```

---

## Main Pipeline

```yaml
trigger:
- main

stages:

- template: templates/build.yml

- template: templates/deploy.yml
```

---

## Template Example

```yaml
# build.yml

steps:

- task: Maven@4

- task: PublishBuildArtifacts@1
```

---

## Template with Parameters

```yaml
parameters:

- name: environment

  type: string

steps:

- script: echo Deploying to ${{ parameters.environment }}
```

Call it:

```yaml
- template: deploy.yml

  parameters:

    environment: prod
```

---

## Types of Templates

### Step Template

Reusable steps.

Example:

```text
Login

Build

Test
```

---

### Job Template

Reusable jobs.

Example:

```text
Build Job

Security Scan Job

Deploy Job
```

---

### Stage Template

Reusable deployment stages.

Example:

```text
Dev

QA

Production
```

---

## Interview Highlight

> "In enterprise environments, I use YAML templates extensively to standardize CI/CD pipelines across multiple applications. This improves maintainability and ensures every project follows the same deployment standards."

---

# 18. What is the difference between Parameters and Variables in a Pipeline?

| Parameters | Variables |
|------------|-----------|
| Evaluated at compile time | Evaluated at runtime |
| Immutable | Can change during execution |
| Used for pipeline structure | Used for runtime values |
| Strongly typed | Mostly string values |
| Cannot be updated | Can be updated |

---

## Parameters Example

```yaml
parameters:

- name: environment

  type: string

  default: dev
```

Usage:

```yaml
${{ parameters.environment }}
```

---

## Variables Example

```yaml
variables:

  BuildConfiguration: Release
```

Usage:

```yaml
$(BuildConfiguration)
```

---

## When to Use Parameters

- Select environment
- Select Terraform workspace
- Enable/Disable stages
- Choose deployment strategy

---

## When to Use Variables

- Build Number
- Subscription ID
- Resource Group
- Docker Tag
- Runtime values

---

## Interview Highlight

> "Parameters define how the pipeline is compiled before execution, while variables hold values that can change during runtime. I use parameters for environment selection and variables for dynamic configuration."

---

# 19. How do you change a username using a Git command?

## Answer

Git username can be configured at two levels:

---

## Local Repository

Applies only to the current repository.

```bash
git config user.name "Rahul Sharma"

git config user.email "rahul@example.com"
```

---

## Global Configuration

Applies to all repositories.

```bash
git config --global user.name "Rahul Sharma"

git config --global user.email "rahul@example.com"
```

---

## Verify Configuration

```bash
git config --list
```

or

```bash
git config user.name
```

---

## Remove Existing Username

```bash
git config --unset user.name
```

---

## Interview Highlight

> "I typically configure Git globally for my workstation and use local repository settings only when working with multiple client accounts requiring different identities."

---

# 20. If we have a microservices-based architecture and a user accesses a particular domain, how does the request reach the specific Pod? Explain the complete request flow.

## Answer

This is one of the most commonly asked Kubernetes architecture questions.

---

## Complete Request Flow

```text
User
 │
 │ HTTPS Request
 ▼
DNS
 │
 ▼
Azure Front Door / Azure DNS
 │
 ▼
Azure Application Gateway
 │
 ▼
Ingress Controller (NGINX / AGIC)
 │
 ▼
Ingress Rules
 │
 ▼
Kubernetes Service (ClusterIP)
 │
 ▼
Endpoints
 │
 ▼
Pod
 │
 ▼
Application Container
```

---

## Step-by-Step Explanation

### Step 1: User Accesses Domain

Example:

```text
https://api.company.com
```

DNS resolves the domain to the public IP of:

- Azure Front Door
- Azure Application Gateway
- Azure Load Balancer

---

### Step 2: Azure Application Gateway

Responsibilities:

- SSL Termination
- Web Application Firewall (WAF)
- Load Balancing
- Health Checks

---

### Step 3: Ingress Controller

Ingress receives the request and evaluates routing rules.

Example:

```yaml
Host: api.company.com

Path: /orders
```

Routes traffic to:

```text
orders-service
```

---

### Step 4: Kubernetes Service

Service discovers healthy Pods using labels.

Example:

```yaml
selector:

  app: orders
```

---

### Step 5: kube-proxy

kube-proxy forwards traffic to one of the available Pods using:

- iptables
- IPVS

---

### Step 6: Pod Receives Request

The application processes the request and sends the response back through the same path.

---

## Complete Flow

```text
Client

↓

DNS

↓

Azure Front Door

↓

Application Gateway

↓

Ingress Controller

↓

Kubernetes Service

↓

Pod

↓

Application

↓

Response
```

---

## Enterprise Enhancements

Additional components:

- Azure Front Door
- Azure CDN
- Azure API Management
- Azure Firewall
- Microsoft Defender for Cloud
- Azure Monitor

---

## Interview Highlight

> "In AKS, traffic typically flows from Azure Front Door or Application Gateway to the Ingress Controller, then to a Kubernetes Service, which load-balances requests across healthy Pods."

---

# 21. Explain the process of designing and deploying infrastructure on Azure.

## Answer

I follow a structured Infrastructure as Code (IaC) approach using **Terraform** and Azure DevOps.

---

## Phase 1: Requirement Gathering

Understand:

- Business requirements
- Security
- Compliance
- Availability
- DR
- Budget

---

## Phase 2: Azure Landing Zone

Create:

- Management Groups
- Subscriptions
- RBAC
- Azure Policies
- Hub-and-Spoke Network
- Azure Firewall
- Log Analytics
- Microsoft Defender for Cloud

---

## Phase 3: Network Design

```text
Hub VNet

│

├── Azure Firewall

├── VPN Gateway

├── Bastion

└── DNS

      │

VNet Peering

      │

Production Spoke

Development Spoke

Testing Spoke
```

---

## Phase 4: Infrastructure Modules

Terraform modules:

```text
modules/

network

storage

aks

acr

keyvault

monitor

sql

appservice
```

---

## Phase 5: CI/CD Pipeline

```text
Git Commit

↓

Terraform Validate

↓

Terraform Plan

↓

Approval

↓

Terraform Apply

↓

Post Validation
```

---

## Phase 6: Monitoring

Configure:

- Azure Monitor
- Log Analytics
- Managed Prometheus
- Managed Grafana
- Alerts
- Dashboards

---

## Phase 7: Security

Implement:

- Azure Policy
- RBAC
- Managed Identity
- Key Vault
- Private Endpoints
- NSGs
- Microsoft Defender for Cloud

---

## Architecture Overview

```text
Azure Landing Zone

↓

Networking

↓

Identity

↓

Terraform Modules

↓

Azure DevOps Pipeline

↓

Infrastructure Deployment

↓

Monitoring

↓

Operations
```

---

## Interview Highlight

> "I always begin with a secure Azure Landing Zone, implement reusable Terraform modules, automate deployments through Azure DevOps pipelines, and integrate monitoring and security from day one."

---

# 22. Explain CI/CD deployment using GitHub.

## Answer

GitHub Actions provides native CI/CD automation for building, testing, and deploying applications.

---

## Workflow

```text
Developer

↓

Git Push

↓

GitHub Repository

↓

GitHub Actions

↓

Build

↓

Unit Tests

↓

Security Scan

↓

Docker Build

↓

Push Image to Azure Container Registry (ACR)

↓

Terraform / Helm

↓

Deploy to AKS

↓

Smoke Test

↓

Production
```

---

## GitHub Actions Example

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - main

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

    - uses: actions/checkout@v4

    - name: Build Docker Image
      run: docker build -t app .

    - name: Push to ACR
      run: docker push myacr.azurecr.io/app:latest
```

---

## Authentication

Use:

- OpenID Connect (OIDC) (Preferred)
- Service Principal
- GitHub Secrets

Avoid storing credentials in code.

---

## Deployment to AKS

Use:

- Helm
- kubectl
- Argo CD (GitOps)
- Flux CD (GitOps)

---

## Production Pipeline

```text
Git Push

↓

GitHub Actions

↓

Build

↓

Unit Tests

↓

Code Quality Scan

↓

Docker Build

↓

Push to ACR

↓

Terraform (Infrastructure)

↓

Helm Deployment

↓

AKS

↓

Health Check

↓

Production
```

---

## Best Practices

- Protect the `main` branch.
- Require pull request reviews.
- Use reusable GitHub workflows.
- Store secrets in GitHub Secrets or Azure Key Vault.
- Use OIDC for Azure authentication.
- Add security scanning (e.g., Trivy, CodeQL).
- Enable rollback strategies for production deployments.

---

## Interview Highlight

> "In GitHub Actions, I build a reusable workflow that performs code checkout, testing, security scanning, Docker image creation, pushes the image to Azure Container Registry, provisions infrastructure with Terraform if required, and deploys to AKS using Helm. For Azure authentication, I prefer OIDC over long-lived service principals."

---

# Final Senior Azure DevOps Interview Tips

### Mention These Technologies

- Azure Landing Zone
- Terraform Modules
- Azure DevOps YAML Pipelines
- GitHub Actions
- Azure Kubernetes Service (AKS)
- Azure Container Registry (ACR)
- Azure Key Vault
- Azure Monitor & Log Analytics
- Managed Prometheus & Grafana
- Azure Policy
- RBAC
- Managed Identity
- Azure Front Door
- Application Gateway + WAF
- Azure API Management
- Hub-and-Spoke Networking
- Private Endpoints
- Microsoft Defender for Cloud

---

### Common Real-World Practices

- Infrastructure as Code with Terraform
- Reusable YAML templates
- Multi-stage CI/CD pipelines
- Separate Dev, QA, UAT, and Production environments
- Remote Terraform state with Azure Storage
- Approval gates for Production deployments
- Blue-Green or Canary deployment strategies
- Automated testing and security scanning
- Monitoring, logging, and alerting from day one

---

## Interview Closing Statement

> "My approach as a Senior Azure DevOps Engineer is to build secure, scalable, and automated platforms using Azure Landing Zones, Terraform, Azure DevOps or GitHub Actions, and AKS. I focus on Infrastructure as Code, governance, security, observability, and reusable CI/CD pipelines to ensure consistent and reliable deployments across all environments."
