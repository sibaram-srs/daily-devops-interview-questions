# Azure Landing Zone Interview Notes (Q126–Q140)

# Expert Level Architecture Scenarios (Senior Azure Architect)

---

# Q126. Design an Azure Landing Zone for a banking application

## Answer

A banking Landing Zone must be **highly secure, compliant, zero-trust, and resilient**.

## Architecture Principles

* Zero Trust security model
* Strict regulatory compliance (PCI-DSS, ISO 27001)
* Strong identity governance
* Network isolation (no public exposure)
* High availability + DR
* Full auditability

## Architecture

```text id="bank1"
Management Groups
   ├── Platform
   ├── Landing Zones
   │     ├── Banking-Prod
   │     ├── Banking-DR
   └── Sandbox
```

## Core Components

### Identity

* Microsoft Entra ID
* PIM + MFA + Conditional Access
* Separate admin identities

### Network

* Hub-Spoke architecture
* Azure Firewall Premium
* Private Endpoints (mandatory)
* DDoS Protection Standard

### Security

* Microsoft Defender for Cloud
* Key Vault HSM
* Sentinel SIEM

### Data

* Encryption at rest + in transit
* Geo-redundant storage

## DR Strategy

* Active-Passive multi-region
* RTO: 15–30 min
* RPO: < 5 min

## Key Principle

> No banking workload should have a public endpoint.

---

# Q127. Design a Landing Zone for 500 microservices

## Answer

This requires **scalability, automation, and strong governance boundaries**.

## Architecture

```text id="ms1"
Platform MG
   ├── Shared Services
   ├── Networking Hub
   ├── Observability
   └── Security

Landing Zones
   ├── Microservice Subscriptions (x50–100)
```

## Key Design Decisions

### Compute

* AKS per domain or shared AKS with namespaces
* API Gateway (APIM / Front Door)

### Networking

* Hub-Spoke
* Service Mesh (Istio/Linkerd optional)

### CI/CD

* GitOps (Flux / ArgoCD)
* Per-microservice pipelines

### Observability

* Central Log Analytics
* Distributed tracing (App Insights)

## Key Challenge

Avoid **subscription explosion vs isolation trade-off**

---

# Q128. Global enterprise (10 countries) Landing Zone

## Answer

This is a **geo-distributed governance model**.

## Structure

```text id="geo1"
Root MG
 ├── Platform
 ├── Region-India
 ├── Region-Europe
 ├── Region-US
 └── Region-APAC
```

## Key Design

### Identity

* Single global Entra ID tenant
* Conditional Access by region

### Network

* Azure WAN or ExpressRoute Global Reach
* Regional hubs

### Data Residency

* Country-specific compliance policies

### Deployment Model

* Region-based subscriptions
* Local DR per region

## Key Principle

> Each country behaves like a semi-independent cloud unit under central governance.

---

# Q129. Migrate 100 on-prem applications to Azure

## Answer

Use **phased migration strategy (6R model)**.

## Phases

### 1. Discovery

* Azure Migrate
* Dependency mapping

### 2. Categorization

* Rehost
* Refactor
* Rebuild
* Retire

### 3. Landing Zone Setup

* Networking
* Identity
* Security baseline

### 4. Migration Strategy

| Type      | Approach              |
| --------- | --------------------- |
| VM Apps   | Rehost (ASR)          |
| Web Apps  | App Service migration |
| Databases | Azure SQL / MI        |
| Legacy    | Rebuild               |

### 5. Execution

* Wave-based migration
* Pilot → bulk migration

### 6. Optimization

* Cost + performance tuning

---

# Q130. Secure internet-facing workloads

## Answer

Internet-facing systems require **multi-layer security**.

## Architecture

```text id="web1"
User
 ↓
Azure Front Door (WAF)
 ↓
Application Gateway (WAF)
 ↓
Backend (Private)
```

## Security Layers

* WAF (Front Door + App Gateway)
* DDoS Protection
* NSG restrictions
* Private Endpoints
* Azure Firewall outbound control
* TLS termination at edge

## Best Practice

> Backend must never be directly exposed to internet.

---

# Q131. Zero Trust in Azure

## Answer

Zero Trust = **Never trust, always verify**

## Pillars

* Identity first
* Device trust
* Least privilege
* Continuous monitoring

## Implementation

* Entra ID + MFA
* Conditional Access policies
* PIM
* Private networking
* Defender for Cloud
* Sentinel monitoring

## Key Rule

> No implicit trust based on network location.

---

# Q132. Multi-region architecture

## Answer

Goal: **High availability + disaster recovery**

## Pattern

* Active-Active OR Active-Passive

## Components

* Azure Front Door (global routing)
* Geo-redundant DB
* Multi-region AKS/App Services
* Traffic Manager fallback

## Architecture

```text id="multi1"
Region A (Primary)
Region B (Secondary)
   ↑         ↑
Azure Front Door
```

## Decision

* Latency-sensitive → Active-Active
* Cost-sensitive → Active-Passive

---

# Q133. Landing Zone for AKS

## Answer

AKS requires strong **networking, identity, and observability controls**.

## Design

### Cluster Model

* One AKS per environment OR per domain

### Security

* AAD integration
* Workload Identity
* Private AKS cluster

### Networking

* Azure CNI
* Ingress Controller (NGINX / App Gateway)

### CI/CD

* GitOps (Flux/ArgoCD)
* Container scanning (Trivy)

### Observability

* Container Insights
* Prometheus + Grafana

---

# Q134. Landing Zone for SAP

## Answer

SAP workloads require **extreme performance + HA + certification compliance**.

## Key Requirements

* High memory VMs (E-series, M-series)
* Premium SSD / Ultra Disk
* Low latency networking

## Architecture

* Dedicated SAP subscription
* Hub-Spoke networking
* ExpressRoute mandatory
* Zone-redundant SAP HANA

## DR

* HANA System Replication
* Azure Site Recovery

---

# Q135. Reduce cloud cost without impacting availability

## Answer

Cost optimization must not reduce SLA.

## Strategies

* Right-sizing VMs
* Reserved Instances / Savings Plans
* Autoscaling (AKS, VMSS)
* Storage tiering
* Stop non-prod schedules
* Use Spot VMs for batch workloads
* Remove unused resources

## Governance

* Azure Cost Management
* Budgets + alerts
* Tag-based cost allocation

---

# Q136. Handle a security breach in Azure

## Answer

Follow **Incident Response lifecycle**

## Steps

### 1. Detect

* Defender alerts
* Sentinel incidents

### 2. Contain

* Isolate VM / subnet
* Disable compromised identities

### 3. Eradicate

* Remove malware
* Patch vulnerabilities

### 4. Recover

* Restore from backups
* Validate integrity

### 5. Post-Incident

* RCA
* Policy improvements

---

# Q137. Healthcare Landing Zone

## Answer

Healthcare requires **HIPAA/GDPR compliance + strict data control**.

## Requirements

* Data encryption
* Audit logging
* Patient data isolation
* Regional data residency

## Architecture

* Private endpoints only
* Key Vault HSM
* Defender + Sentinel
* Strict RBAC

## Compliance

* HIPAA
* ISO 27001
* Local healthcare regulations

---

# Q138. Governance for 500 subscriptions

## Answer

Use **Management Group hierarchy + Policy inheritance**

## Structure

```text id="gov1"
Root MG
 ├── Platform
 ├── Prod
 ├── Non-Prod
 ├── Sandbox
 └── Shared Services
```

## Controls

* Azure Policy initiatives
* RBAC at MG level
* Central logging
* Standard tagging enforcement

---

# Q139. Deploy Landing Zone using Terraform

## Answer

Use **modular Terraform + CI/CD pipelines**

## Structure

* modules/
* environments/
* pipelines/

## Flow

```text id="tf1"
Git
 ↓
Terraform Modules
 ↓
Pipeline
 ↓
Azure Landing Zone
```

## Key Components

* Management Groups (azurerm_management_group)
* Policy (azurerm_policy_assignment)
* Networking (VNets, Firewall)
* Identity (RBAC)

---

# Q140. End-to-end production Landing Zone architecture

## Answer

This is a **full enterprise cloud platform design**.

## Layers

### 1. Management Group Layer

* Governance hierarchy
* Policy inheritance

### 2. Subscription Layer

* Platform subscriptions
* Workload subscriptions

### 3. Network Layer

* Hub-Spoke
* Azure Firewall
* ExpressRoute

### 4. Identity Layer

* Entra ID
* PIM + MFA

### 5. Security Layer

* Defender for Cloud
* Sentinel SIEM
* Key Vault

### 6. Application Layer

* AKS / App Service / VMSS

### 7. Observability Layer

* Azure Monitor
* Log Analytics
* Application Insights

### 8. DevSecOps Layer

* Terraform + GitOps
* CI/CD pipelines
* Policy-as-code

## End Architecture

```text id="final1"
Users
 ↓
Front Door (WAF)
 ↓
App Gateway
 ↓
AKS / Apps
 ↓
Private Data Layer
 ↓
Key Vault + DB + Storage
```

## Key Principle

> A production Landing Zone is not just infrastructure — it is a governed cloud operating system.

---

# FINAL SENIOR ARCHITECT SUMMARY

* Landing Zone = **foundation of enterprise Azure adoption**
* Security = Zero Trust + Private networking
* Governance = Management Groups + Azure Policy
* Automation = Terraform + CI/CD
* Observability = Azure Monitor + Sentinel
* DR = Multi-region + ASR
* Identity = Entra ID + PIM + MFA
* Architecture = Hub-Spoke + Secure ingress/egress

---
