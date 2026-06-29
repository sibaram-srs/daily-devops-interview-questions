# Azure Landing Zone Interview Notes (Q83–Q95)

# Azure Key Vault + DevSecOps

---

# Q83. Why use Azure Key Vault?

## Answer

**Azure Key Vault** is a managed Azure service used to securely store and manage:

* Secrets (Passwords, API Keys, Connection Strings)
* Certificates
* Encryption Keys
* SSH Keys

Instead of hardcoding secrets in applications or pipelines, they are securely stored in Key Vault and retrieved only at runtime.

## Features

* Centralized secret management
* Hardware Security Module (HSM) support
* Automatic certificate renewal
* Secret versioning
* Access logging
* Soft Delete & Purge Protection
* RBAC and Managed Identity integration

## Enterprise Use Cases

* Database connection strings
* Storage Account keys
* Azure Service Principal credentials
* SSL certificates
* API tokens
* Encryption keys

## Architecture

```text
Application
      │
Managed Identity
      │
Azure Key Vault
      │
Secrets / Certificates / Keys
```

## Best Practices

* Never store secrets in code.
* Use Managed Identity instead of Service Principals where possible.
* Enable Soft Delete and Purge Protection.
* Restrict public network access using Private Endpoints.
* Rotate secrets regularly.

### Interview Tip

> Key Vault is the central security vault for secrets, certificates, and encryption keys in Azure.

---

# Q84. How do applications access secrets securely?

## Answer

Applications should authenticate using **Managed Identity**, which removes the need to store credentials.

## Flow

```text
Application
      │
Managed Identity
      │
Azure AD Authentication
      │
Azure Key Vault
      │
Returns Secret
```

No username or password is stored.

## Other Authentication Methods

* Managed Identity (Preferred)
* Service Principal
* Azure CLI (Development)
* Visual Studio Credentials
* Certificate-based Authentication

## Best Practices

* Prefer Managed Identity.
* Grant only required permissions.
* Retrieve secrets at runtime.
* Cache secrets when appropriate.
* Avoid logging secret values.

---

# Q85. Access Policies vs RBAC?

## Answer

Azure Key Vault supports two authorization models.

| Access Policies                | Azure RBAC                  |
| ------------------------------ | --------------------------- |
| Legacy model                   | Recommended model           |
| Key Vault specific             | Azure-wide authorization    |
| Separate permission management | Centralized role management |
| Limited auditing               | Better governance           |

## RBAC Roles

* Key Vault Administrator
* Key Vault Secrets Officer
* Key Vault Secrets User
* Key Vault Certificate Officer

## Recommendation

For new deployments:

* Enable Azure RBAC
* Disable Access Policies where possible
* Manage permissions using Azure AD groups

### Interview Tip

> Microsoft recommends Azure RBAC because it provides centralized access management and better governance.

---

# Q86. How do you rotate secrets?

## Answer

Secret rotation minimizes the risk of credential compromise by periodically replacing secrets.

## Rotation Methods

### Automatic Rotation

* Azure Key Vault Rotation Policies
* Azure Automation
* Azure Functions
* Event Grid

### Manual Rotation

* Create new secret version
* Update application
* Remove old version after validation

## Example

```text
Password v1
      │
Create v2
      │
Applications use latest version
      │
Delete v1
```

## Best Practices

* Enable secret versioning.
* Automate rotation wherever possible.
* Use Managed Identity to reduce secret usage.
* Monitor secret expiration.

---

# Q87. How do you secure certificates?

## Answer

Certificates should be centrally managed in Azure Key Vault.

## Best Practices

* Store certificates in Key Vault.
* Enable automatic renewal.
* Use trusted Certificate Authorities.
* Restrict export permissions.
* Enable audit logging.
* Use Private Endpoints.

## Enterprise Example

```text
App Gateway
      │
Retrieves SSL Certificate
      │
Azure Key Vault
```

No manual certificate installation is required.

---

# Q88. What is DevSecOps?

## Answer

**DevSecOps** integrates security into every phase of the Software Development Lifecycle (SDLC), making security a shared responsibility.

Instead of performing security checks only before production, security is automated throughout the CI/CD pipeline.

## Traditional DevOps

```text
Plan
↓
Develop
↓
Build
↓
Deploy
↓
Security
```

## DevSecOps

```text
Plan
↓
Develop
↓
Security
↓
Build
↓
Security
↓
Deploy
↓
Security
↓
Monitor
```

## Benefits

* Early vulnerability detection
* Faster remediation
* Continuous compliance
* Reduced deployment risk
* Automated security validation

### Interview Tip

> DevSecOps shifts security from the end of the pipeline to every stage of the development lifecycle.

---

# Q89. How do you integrate security into CI/CD?

## Answer

Security should be automated at every stage of the pipeline.

## Typical Azure DevOps Pipeline

```text
Code Commit
      │
SAST Scan
      │
Secret Scan
      │
Dependency Scan
      │
IaC Scan
      │
Container Scan
      │
Build
      │
Deploy
      │
DAST Scan
      │
Production
```

## Security Controls

* Branch Protection
* Pull Request Reviews
* Code Scanning
* Terraform Validation
* Azure Policy Validation
* Container Image Scanning
* Artifact Signing
* Deployment Approvals

---

# Q90. What security scans should run in a pipeline?

## Answer

A production-grade DevSecOps pipeline includes multiple security scans.

| Scan            | Purpose                           |
| --------------- | --------------------------------- |
| SAST            | Detect insecure code              |
| Secret Scan     | Detect passwords/API keys         |
| Dependency Scan | Find vulnerable libraries         |
| IaC Scan        | Validate Terraform/Bicep          |
| Container Scan  | Detect OS/package vulnerabilities |
| License Scan    | Check open-source licenses        |
| Malware Scan    | Detect malicious files            |
| DAST            | Test running applications         |

## Common Tools

* Microsoft Defender for DevOps
* GitHub Advanced Security
* Checkov
* Trivy
* SonarQube
* OWASP ZAP
* Snyk

### Best Practice

Block production deployments if high or critical vulnerabilities are detected.

---

# Q91. What is Shift Left Security?

## Answer

Shift Left means performing security testing **earlier** in the software development lifecycle.

## Traditional

```text
Develop
↓
Deploy
↓
Security Testing
```

## Shift Left

```text
Develop
↓
Security Scan
↓
Build
↓
Deploy
```

## Benefits

* Lower remediation cost
* Faster fixes
* Improved code quality
* Reduced production issues

### Interview Tip

> The earlier vulnerabilities are identified, the cheaper and easier they are to fix.

---

# Q92. How do you scan Infrastructure as Code?

## Answer

Infrastructure as Code (IaC) should be scanned before deployment to identify security and compliance issues.

## Common Tools

* Checkov
* Trivy
* Terrascan
* tfsec
* Microsoft Defender for DevOps

## Example Checks

* Public Storage Accounts
* Open NSG Rules
* Missing Tags
* Unencrypted Disks
* Public SQL Databases
* Missing Diagnostics

## Pipeline Example

```text
Terraform Validate
      │
Terraform Plan
      │
Checkov Scan
      │
Approval
      │
Terraform Apply
```

## Best Practice

Fail the pipeline if critical policy violations are detected.

---

# Q93. How do you manage secrets in pipelines?

## Answer

Secrets should never be stored directly in source code or pipeline YAML files.

## Recommended Approach

```text
Azure DevOps Pipeline
      │
Managed Identity / Service Connection
      │
Azure Key Vault
      │
Retrieve Secrets at Runtime
```

## Best Practices

* Store secrets in Azure Key Vault.
* Use Variable Groups linked to Key Vault.
* Mask secrets in pipeline logs.
* Rotate secrets regularly.
* Use Managed Identity whenever possible.

### Never Store

* Passwords
* API Keys
* Connection Strings
* Certificates
* Tokens

inside Git repositories.

---

# Q94. How do you secure Terraform state files?

## Answer

Terraform state files contain sensitive infrastructure information and must be protected.

## Recommended Backend

Azure Storage Account

Features:

* Private Endpoint
* RBAC
* Storage Firewall
* Encryption at Rest
* Blob Versioning
* Soft Delete
* State Locking

## Architecture

```text
Terraform
      │
Remote Backend
      │
Azure Storage Account
      │
Blob Container
```

## Best Practices

* Never commit state files to Git.
* Enable blob versioning.
* Restrict access using RBAC.
* Use separate state files per environment.
* Encrypt state data.

---

# Q95. How do you implement Policy-as-Code?

## Answer

Policy-as-Code means defining governance policies in source control and deploying them automatically through CI/CD pipelines.

Instead of manually configuring policies in the Azure portal, policy definitions are version-controlled and deployed as code.

## Workflow

```text
Git Repository
      │
Policy Definition (JSON)
      │
Pull Request
      │
CI Validation
      │
Approval
      │
Deploy via Azure DevOps
      │
Assign Policy
```

## Technologies

* Azure Policy JSON
* Terraform
* Bicep
* ARM Templates
* Azure DevOps Pipelines
* GitHub Actions

## Benefits

* Version control
* Peer reviews
* Automated deployment
* Consistent governance
* Easier rollback
* Auditability

### Enterprise Example

Policies deployed automatically:

* Deny Public IPs
* Require Tags
* Restrict Azure Regions
* Enable Diagnostic Settings
* Enforce HTTPS
* Require Managed Disks

---

# Senior Architect Quick Revision

* **Azure Key Vault** → Centralized storage for secrets, certificates, and encryption keys.
* **Managed Identity** → Preferred method for applications to access Key Vault without storing credentials.
* **Azure RBAC** → Recommended authorization model for Key Vault over legacy Access Policies.
* **Secret Rotation** → Automate using Key Vault rotation policies, Azure Functions, or Automation.
* **DevSecOps** → Integrate security into every phase of CI/CD.
* **Pipeline Security** → Include SAST, DAST, Dependency, Secret, Container, and IaC scans.
* **Shift Left Security** → Detect vulnerabilities early in development.
* **IaC Scanning** → Validate Terraform/Bicep with tools like Checkov or tfsec before deployment.
* **Pipeline Secrets** → Store in Azure Key Vault and retrieve securely at runtime.
* **Terraform State** → Store remotely in Azure Storage with RBAC, encryption, versioning, and state locking.
* **Policy-as-Code** → Manage Azure Policies as version-controlled code and deploy them automatically through CI/CD.
