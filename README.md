# Azure Landing Zone Interview Questions (Senior DevOps Engineer Answers)

---

# 1) Suppose your organization wants to ensure that no resources are deployed outside specific regions. How would you enforce this?

### Answer

The best practice is to use **Azure Policy** with a **Deny** effect.

### Implementation Steps

- Create an Azure Policy using the built-in policy:
  - **Allowed Locations**
- Define the list of approved Azure regions (for example: East US, Central India, West Europe).
- Assign the policy at the appropriate scope:
  - Management Group (recommended)
  - Subscription
  - Resource Group (if required)
- Set the effect to **Deny**, so deployments outside the allowed regions are blocked.

### Enterprise Approach

In large organizations, I assign the policy at the **Management Group** level so every subscription automatically inherits it.

Example hierarchy:

```
Tenant
 ├── Platform
 ├── Production
 ├── NonProduction
 └── Sandbox
```

Assigning the policy at the Management Group ensures governance across all subscriptions.

### Example Policy

```json
Allowed Locations:
[
  "Central India",
  "East US",
  "West Europe"
]
```

### Interview Highlight

> "I prefer assigning the policy at the Management Group level rather than individual subscriptions because it provides centralized governance and prevents configuration drift."

---

# 2) Explain the structure of subscriptions in a Landing Zone for multiple environments.

### Answer

A Landing Zone follows a scalable subscription design where workloads are isolated by environment and responsibility.

Typical Enterprise Scale Architecture:

```
Tenant Root
│
├── Platform Management Group
│     ├── Connectivity Subscription
│     ├── Identity Subscription
│     └── Management Subscription
│
├── Landing Zones
│     ├── Production Subscription
│     ├── Non-Production Subscription
│     ├── Development Subscription
│     └── Test Subscription
│
└── Sandbox Subscription
```

### Platform Subscription

Contains shared infrastructure:

- Hub VNet
- Azure Firewall
- VPN Gateway
- ExpressRoute
- Bastion
- DNS
- Shared Services

### Management Subscription

Contains operational services:

- Log Analytics
- Azure Monitor
- Defender for Cloud
- Update Management
- Backup
- Automation Accounts

### Identity Subscription

Contains:

- Domain Controllers (if required)
- Azure AD DS
- Identity-related services

### Workload Subscriptions

Separate subscriptions for:

- Development
- QA
- UAT
- Production

Benefits:

- Better RBAC
- Separate billing
- Independent quotas
- Improved security
- Easier governance

### Interview Highlight

> "I recommend separating Production and Non-Production into different subscriptions to reduce blast radius, improve access control, and simplify cost management."

---

# 3) How can you integrate Azure Landing Zone deployment with DevOps pipelines?

### Answer

Landing Zones are fully automatable using Infrastructure as Code (IaC) integrated with Azure DevOps or GitHub Actions.

Typical pipeline flow:

```
Developer
      │
      ▼
Git Repository
      │
      ▼
Azure DevOps Pipeline
      │
      ▼
Terraform / Bicep Validation
      │
      ▼
Terraform Plan
      │
      ▼
Approval
      │
      ▼
Terraform Apply
      │
      ▼
Azure Landing Zone
```

### Pipeline Stages

1. Validate code
2. Static analysis (Terraform fmt, validate, Checkov)
3. Terraform Plan
4. Manual Approval (Production)
5. Terraform Apply
6. Post-deployment validation

### Resources Deployed

- Management Groups
- Subscriptions
- Azure Policies
- RBAC
- VNets
- NSGs
- Route Tables
- Azure Firewall
- Log Analytics
- Diagnostic Settings
- Resource Locks

### Best Practices

- Store Terraform state in Azure Storage.
- Use Azure Key Vault for secrets.
- Use Service Connections with Managed Identity or Service Principal.
- Use separate pipelines for Platform and Application deployments.

### Interview Highlight

> "In enterprise environments, Landing Zone deployment is fully automated through Terraform modules in Azure DevOps pipelines with approval gates for production."

---

# 4) Suppose you are migrating on-prem workloads to Azure. How does a Landing Zone help?

### Answer

A Landing Zone provides a secure, governed, and standardized Azure environment before migrating workloads.

Without a Landing Zone:

- No governance
- No RBAC standards
- Inconsistent networking
- Security gaps
- No monitoring
- Manual configurations

With a Landing Zone:

- Standardized networking
- Centralized identity
- Security baseline
- Monitoring enabled
- Backup configured
- Azure Policies enforced
- Resource organization
- Cost management

Migration Flow:

```
On-Premises
     │
Azure Migrate
     │
Landing Zone
     │
Production Subscription
```

Landing Zone Components:

- Hub-Spoke Network
- Azure Firewall
- Azure Policy
- Azure Monitor
- Defender for Cloud
- Backup
- RBAC
- Key Vault

### Benefits

- Faster migration
- Better governance
- Consistent security
- Easier operations
- Compliance readiness

### Interview Highlight

> "I always recommend deploying the Landing Zone first. It establishes the governance, networking, security, and operational foundation before any workloads are migrated."

---

# 5) Can you explain Azure Policy Assignment?

### Answer

Azure Policy Assignment is the process of applying a policy definition to a specific Azure scope.

### Components

```
Policy Definition
        │
Policy Assignment
        │
Scope
```

### Assignment Scopes

- Management Group
- Subscription
- Resource Group
- Individual Resource

### Example

Policy:

```
Allowed Locations
```

Assignment:

```
Scope:
Production Management Group
```

Effect:

```
Deny
```

Result:

All subscriptions under that Management Group inherit the policy.

### Common Policy Effects

| Effect | Purpose |
|----------|----------|
| Deny | Blocks non-compliant deployments |
| Audit | Reports non-compliant resources |
| Append | Adds properties during deployment |
| Modify | Automatically modifies resources |
| DeployIfNotExists | Deploys missing configurations |
| AuditIfNotExists | Checks required configurations |

### Real-world Examples

- Enforce mandatory tags
- Restrict allowed VM SKUs
- Restrict deployment regions
- Require encryption
- Enforce diagnostic settings
- Require HTTPS on Storage Accounts
- Require Private Endpoints

### Interview Highlight

> "Policy Definitions define the rule, while Policy Assignments determine where that rule is enforced."

---

# 6) Explain Hub-and-Spoke Network Architecture.

### Answer

Hub-and-Spoke is Microsoft's recommended enterprise networking architecture for Azure.

```
                   On-Prem
                      │
               VPN / ExpressRoute
                      │
                 Hub VNet
        ┌──────────┼──────────┐
        │          │          │
 Azure Firewall  Bastion   DNS
        │
────────┼────────────────────────────
        │
   VNet Peering
        │
 ┌──────┴─────────────┐
 │                    │
Spoke VNet 1      Spoke VNet 2
Production        Development
 │                    │
VMs               AKS
App Service       SQL
```

### Hub Contains

- Azure Firewall
- VPN Gateway
- ExpressRoute Gateway
- Bastion
- DNS
- Shared Services

### Spoke Contains

- Application VMs
- AKS Clusters
- Databases
- App Services
- Storage Accounts

### Communication Flow

```
Spoke → Hub → Internet

Spoke → Hub → On-Prem

Spoke → Hub → Another Spoke
```

Traffic is inspected through Azure Firewall or NVAs before reaching its destination.

### Benefits

- Centralized security
- Shared connectivity
- Reduced cost
- Easier governance
- Scalable architecture
- Better network segmentation
- Simplified management

### Best Practices

- Use VNet Peering.
- Centralize Azure Firewall in the Hub.
- Use User Defined Routes (UDRs) to force traffic through the firewall.
- Enable Network Watcher and NSG Flow Logs.
- Use Private DNS Zones for private endpoints.
- Separate Production and Non-Production spokes.

### Interview Highlight

> "Hub-and-Spoke enables centralized networking and security while allowing workload isolation in individual spokes. It is the recommended architecture for enterprise Azure Landing Zones."

---

# Senior DevOps Interview Tips

- Emphasize **Management Groups** for governance.
- Mention **Azure Policy + RBAC** together for security.
- Highlight **Infrastructure as Code (Terraform/Bicep)**.
- Explain how **Azure DevOps or GitHub Actions** automate Landing Zone deployments.
- Reference **Enterprise Scale Landing Zone (ESLZ)** when discussing large organizations.
- Stress **Hub-and-Spoke** as the standard networking architecture.
- Mention **Azure Monitor**, **Log Analytics**, and **Microsoft Defender for Cloud** for operational excellence.
```
