# Azure Landing Zone Interview Notes (Q70–Q82)

# Identity & Access Management + Azure Policy & Governance

---

# Q70. What is RBAC?

## Answer

**Azure Role-Based Access Control (RBAC)** is Azure's authorization system used to control **who can perform what actions on which Azure resources**.

RBAC follows the **principle of least privilege**, ensuring users receive only the permissions required to perform their job.

### RBAC Components

| Component          | Description                                              |
| ------------------ | -------------------------------------------------------- |
| Security Principal | User, Group, Service Principal, Managed Identity         |
| Role Definition    | Collection of allowed permissions                        |
| Scope              | Management Group, Subscription, Resource Group, Resource |

### Common Built-in Roles

* Owner
* Contributor
* Reader
* User Access Administrator
* Virtual Machine Contributor
* Storage Blob Data Contributor
* Key Vault Secrets User

### Scope Hierarchy

```text
Management Group
      ↓
Subscription
      ↓
Resource Group
      ↓
Resource
```

Permissions inherited downward.

### Example

Developers:

* Contributor on Dev Subscription

Operations:

* Reader on Production

Security Team:

* Security Admin across Management Group

### Best Practices

* Assign permissions to Groups instead of users.
* Use Managed Identity for applications.
* Prefer built-in roles.
* Create custom roles only when necessary.
* Use PIM for privileged roles.

### Interview Tip

> RBAC controls **who can access resources**, not how resources should be configured.

---

# Q71. Difference between RBAC and Azure Policy?

| RBAC               | Azure Policy            |
| ------------------ | ----------------------- |
| Authorization      | Governance              |
| Controls Who       | Controls What           |
| Identity-based     | Resource-based          |
| Grants permissions | Enforces standards      |
| Uses Roles         | Uses Policy Definitions |

### Example

RBAC:

> DevOps team can create VMs.

Policy:

> Only Standard_D2s_v5 VM size is allowed.

RBAC:

> User can create Storage Account.

Policy:

> Storage Account must use HTTPS.

### Easy Interview Line

> RBAC decides **who can perform an action**, while Azure Policy decides **whether the resource configuration is allowed**.

---

# Q72. What are Custom Roles?

## Answer

Custom Roles allow organizations to create **fine-grained permissions** beyond Azure's built-in roles.

### Why use Custom Roles?

Built-in Contributor may allow too many actions.

Example:

Need:

* Start VM
* Stop VM
* Restart VM

But NOT:

* Delete VM
* Create VM

Create a custom role.

### Example JSON

```json
{
  "Name": "VM Operator",
  "Actions": [
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Compute/virtualMachines/deallocate/action"
  ],
  "NotActions": [
    "Microsoft.Compute/virtualMachines/delete"
  ]
}
```

### Best Practices

* Use built-in roles first.
* Keep custom roles minimal.
* Document custom permissions.
* Review regularly.

---

# Q73. How do you implement Least Privilege Access?

## Answer

Least Privilege means users receive **only the minimum permissions needed**.

### Steps

1. Assign permissions to Azure AD Groups.
2. Use Reader instead of Contributor where possible.
3. Use custom roles for special cases.
4. Enable PIM for admin roles.
5. Use Managed Identities.
6. Remove permanent Owner access.
7. Perform periodic access reviews.

### Production Example

Developers:

* Contributor on Dev

Production:

* Reader

Operations:

* VM Contributor

Security:

* Security Admin

### Best Practices

* No shared admin accounts.
* No permanent Global Admins.
* Just-In-Time elevation.
* MFA mandatory.

---

# Q74. What is PIM?

## Answer

**Privileged Identity Management (PIM)** provides **Just-In-Time privileged access**.

Instead of permanent admin rights, users activate elevated permissions only when needed.

### Workflow

```text
Eligible User
      ↓
Request Access
      ↓
Approval (Optional)
      ↓
MFA
      ↓
Temporary Role Activation
      ↓
Automatic Expiration
```

### Benefits

* Reduces attack surface
* Time-bound access
* Approval workflow
* Audit logs
* MFA enforcement

### Example

Cloud Engineer:
Eligible Owner

Needs production deployment:
Activate Owner role for 2 hours.

After 2 hours:
Access automatically removed.

---

# Q75. What is Just-In-Time (JIT) Access?

## Answer

JIT provides **temporary privileged access only when required**.

Common implementations:

* Azure PIM
* Defender for Cloud JIT VM Access

### JIT for VM Access

Normally:

```text
RDP
3389 Open
```

JIT:

```text
3389 Closed
↓
User Requests Access
↓
Approved
↓
Port Opens
↓
Automatically Closes
```

### Benefits

* Blocks brute-force attacks
* Minimizes exposure
* Temporary access
* Auditable

---

# Q76. How do you secure privileged accounts?

## Answer

Production privileged accounts require multiple security controls.

### Best Practices

* MFA mandatory
* PIM enabled
* No permanent Owners
* Break-glass accounts
* Conditional Access
* Passwordless authentication
* Access Reviews
* Dedicated admin accounts
* Azure AD Identity Protection
* Defender for Cloud monitoring

### Production Example

Never:

Developer logs into production with daily account.

Instead:

Dedicated Admin Account
+
MFA
+
PIM
+
Approval
+
Time-limited access

---

# Q77. What is Azure Policy?

## Answer

Azure Policy is a governance service that ensures Azure resources comply with organizational standards.

It evaluates resources during deployment and continuously after deployment.

### Policy Effects

* Deny
* Audit
* Modify
* DeployIfNotExists
* Append
* Disabled

### Example Policies

* Deny Public IPs
* Enforce Tags
* Restrict Regions
* Require Encryption
* Enable Diagnostics

### Interview Tip

RBAC = Permission

Policy = Compliance

---

# Q78. Difference between Policy and RBAC?

| Azure Policy        | RBAC               |
| ------------------- | ------------------ |
| Resource Governance | User Authorization |
| Evaluates Resources | Evaluates Users    |
| Enforces Standards  | Grants Access      |
| Uses Effects        | Uses Roles         |

Example:

RBAC:
Developer can create Storage Account.

Policy:
Storage Account must enable Secure Transfer.

Both work together.

---

# Q79. What are Policy Initiatives?

## Answer

A Policy Initiative is a **collection of related Azure Policies** grouped together.

Instead of assigning 20 policies individually, assign one Initiative.

### Example

Security Initiative:

* No Public IP
* Encryption Required
* Tags Required
* HTTPS Only
* Diagnostics Enabled

Assign once.

All policies apply together.

### Benefits

* Easier management
* Central governance
* Compliance reporting
* Version control

---

# Q80. How do you enforce tagging using Policies?

## Answer

Use Azure Policy with **Modify** or **Append** effects.

### Example

Mandatory Tags

```text
Environment
Owner
Application
BusinessUnit
CostCenter
```

If a tag is missing:

Policy automatically adds it or denies deployment.

### Sample Rule

```text
IF tag "Environment" is missing
THEN Deny deployment
```

or

```text
IF CostCenter missing
THEN Modify → Add default value
```

### Benefits

* Accurate billing
* Governance
* Automation
* Reporting

---

# Q81. How do you prevent Public IP creation?

## Answer

Use Azure Policy with the **Deny** effect.

### Rule

```text
IF
Resource Type = Public IP

THEN

Deny
```

Apply at:

* Management Group
* Subscription

### Enterprise Practice

Exceptions only for:

* Application Gateway
* Azure Firewall
* Bastion
* Load Balancer

All other workloads remain private.

---

# Q82. How do you enforce location restrictions?

## Answer

Azure Policy can restrict deployments to approved Azure regions.

### Example

Allowed Locations

* Central India
* South India

Attempting deployment in East US:

```text
Deployment Denied
```

### Why?

* Data residency
* Compliance
* Reduced latency
* Cost optimization

### Production Best Practice

Large organizations usually maintain an approved region list per business unit or country.

Example:

* India Business → Central India, South India
* Europe Business → West Europe, North Europe

This ensures compliance with regional regulations while maintaining governance.

---

# Senior Architect Summary (30-Second Revision)

* **RBAC** → Controls **who** can access Azure resources.
* **Azure Policy** → Controls **how** resources are configured.
* **Least Privilege** → Grant only the minimum required permissions.
* **PIM** → Just-In-Time privileged access with MFA and approvals.
* **JIT VM Access** → Opens management ports only temporarily.
* **Custom Roles** → Fine-grained permissions when built-in roles are too broad.
* **Policy Initiatives** → Group multiple policies for centralized governance.
* **Tagging Policies** → Enforce mandatory metadata for governance and cost tracking.
* **Deny Public IP Policy** → Prevents accidental exposure of workloads.
* **Location Restriction Policy** → Ensures deployments stay within approved regions for compliance.
