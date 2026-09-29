# AZ-104 Practice Questions — English Set 5
**Coverage**: Derived from 1-1.txt and 1-2.txt chat reviews — Load Balancer Floating IP/DSR, Storage redundancy (LRS/ZRS/GRS/GZRS), Bulk user creation CSV, Custom Script Extension logs and timeout, SAS with stored access policy, Disable storage shared key, App Service Linux auto swap, Azure Firewall Premium SNI filtering, VM Encryption at Host, AKS Spot node pool, Management Groups, Budget Time Grain, Custom role Id, Firewall child policy threat intelligence, Service Health retention, RBAC additive model (NotActions vs Deny), Custom role Actions/DataActions, Hierarchy Settings Administrator.

Total: 20 questions | Pass target: 700/1000

---

## 1) Networking

### Question 1
You are deploying a Windows Server Failover Cluster behind an Azure Standard Load Balancer. The cluster requires that inbound traffic arrives at the backend VM with the Load Balancer Frontend IP still intact as the destination IP (not translated to the VM's own IP). Which load balancing rule feature must you enable?

- A. TCP Reset on idle timeout
- B. HA Ports rule
- C. Floating IP (Direct Server Return)
- D. Session persistence (Source IP affinity)

**Answer: C**
**Explanation:**
**Floating IP (Direct Server Return / DSR)** preserves the load balancer's Frontend IP as the destination IP in packets delivered to backend VMs. The VM must have a loopback adapter (Windows: loopback adapter; Linux: lo interface) configured with the Frontend IP so the OS accepts the packet.

**When Floating IP is required:**
| Scenario | Why Floating IP? |
|---------|----------------|
| SQL Server Always On Availability Group | Listener expects its own virtual IP, not the VM's IP |
| Windows/Linux Failover Cluster | Cluster IP must "float" across nodes |
| NVA with symmetrical routing | Return path bypasses LB |
| Multiple apps on same VM, same backend port | Port reuse across rules |

**DSR traffic flow:**
```
Inbound:  Client → Load Balancer → VM (dest IP = LB Frontend IP, not translated)
Outbound: VM → Client (direct, bypasses Load Balancer entirely)
```

**Required OS configuration**: Create a loopback adapter and assign the LB Frontend IP. Without this, the VM drops packets because it doesn't recognize the destination IP as its own.

---

### Question 2
You are selecting an Azure Storage replication option. Your requirements are: data must survive a single datacenter outage within the primary region, and you want to minimize cost. Which replication type should you choose?

- A. LRS (Locally Redundant Storage)
- B. ZRS (Zone-Redundant Storage)
- C. GRS (Geo-Redundant Storage)
- D. GZRS (Geo-Zone-Redundant Storage)

**Answer: B**
**Explanation:**
| Type | Replication scope | Survives datacenter failure? | Survives regional disaster? | Cost |
|------|------------------|-----------------------------|-----------------------------|------|
| LRS | 3 copies, 1 datacenter | ❌ | ❌ | Lowest |
| **ZRS** | 3 copies, 3 availability zones, 1 region | ✅ | ❌ | Moderate |
| GRS | LRS in primary + LRS in secondary region | ❌ (primary DC) | ✅ | Higher |
| GZRS | ZRS in primary + LRS in secondary region | ✅ | ✅ | Highest |

The requirement is: survive a datacenter outage within the primary region → **ZRS** (synchronous replication across 3 zones, same region, lower cost than GRS/GZRS).

**Common exam trap**: GRS protects against regional disasters but does NOT protect against a single datacenter failure in the primary region (it uses LRS locally, not ZRS).

---

### Question 3
You are choosing between GRS and GZRS for a critical business application. The application requires that data remains available even if a single availability zone in the primary region fails AND must survive a complete regional outage. Which replication type should you choose?

- A. LRS
- B. ZRS
- C. GRS
- D. GZRS

**Answer: D**
**Explanation:**
**GZRS (Geo-Zone-Redundant Storage)** combines the zone-level resilience of ZRS with cross-region redundancy of GRS:

| | GRS | GZRS |
|-|-----|------|
| Primary region protection | LRS (3 copies, 1 datacenter) | ZRS (3 copies, 3 zones) |
| Secondary region protection | LRS | LRS |
| Zone failure in primary | Possible downtime | ✅ Transparent (no downtime) |
| Regional disaster | ✅ Protected | ✅ Protected |
| Cost | Lower | Higher |

The requirement has TWO conditions: survive zone failure AND survive regional outage → only **GZRS** satisfies both. GRS only satisfies the second condition.

**Read-access variants**: RA-GRS / RA-GZRS allow reading from the secondary region at any time (not just after failover).

---

## 2) Identity and Governance

### Question 4
You are performing bulk user creation in the Microsoft Entra admin center. You download the CSV template and add 50 new users. Which four values are **required** in the CSV template for each user?

- A. Display name, User principal name, Initial password, Block sign in (Yes/No)
- B. Name, User principal name, Initial password, Block sign in (Yes/No)
- C. Name, Email address, Initial password, Department
- D. Display name, Email address, Job title, Initial password

**Answer: B**
**Explanation:**
The Entra admin center bulk user creation CSV requires the following fields per user:

| Required field | Example |
|---------------|---------|
| **Name** | Jane Smith |
| **User principal name** | jsmith@contoso.com |
| **Initial password** | TempPass123! |
| **Block sign in** | No |

**Important exam note**: The required field is **Name** (not "Display name" — that is the display name column header in the UI but the CSV field is labeled `name`). All four fields must be present or the row will fail validation.

Optional fields: Department, Job title, Usage location, etc.

---

### Question 5
Your organization wants new Azure subscriptions to be automatically placed in a specific management group named **MG-Onboarding** instead of the root management group. You need to configure the default management group for new subscriptions. Which Azure built-in role is required to configure this hierarchy setting?

- A. Management Group Contributor
- B. Owner
- C. Hierarchy Settings Administrator
- D. Global Administrator

**Answer: C**
**Explanation:**
The **Hierarchy Settings Administrator** role contains the specific permissions needed to configure management group hierarchy settings:

- `Microsoft.Management/managementGroups/settings/write`
- `Microsoft.Management/managementGroups/settings/read`

These permissions allow configuring:
- The **default management group** for new subscriptions
- Policies that protect the root management group hierarchy

**Why not Owner?** Owner on the root management group would work but violates least-privilege. Hierarchy Settings Administrator is the correct least-privilege role.

**Root management group**: Cannot be moved or deleted. All subscriptions and child management groups ultimately fold up to the root management group.

---

### Question 6
You are creating a custom role using Azure PowerShell. You create a JSON file with the role definition. Which property must be set to `null` in the JSON file when creating a new custom role?

- A. AssignableScopes
- B. Name
- C. Id
- D. Actions

**Answer: C**
**Explanation:**
When defining a new custom role, the **`Id`** property must be set to `null`:

```json
{
  "Id": null,
  "Name": "Storage Data Monitor",
  "Description": "...",
  "Actions": [ "Microsoft.Compute/*/read" ],
  "NotActions": [],
  "AssignableScopes": [ "/subscriptions/<subscription-id>" ]
}
```

Setting `Id` to `null` tells Azure to generate a new unique GUID for the role. If you specify an existing ID, the operation tries to update that role (or fails with a conflict).

**Common trap**: `AssignableScopes` must **not** be null — you must specify at least one valid scope (e.g., subscription or resource group). A null `AssignableScopes` will cause the role definition to be rejected.

---

### Question 7
You need to create a custom role that allows users to **read storage account properties** (management plane) and **read and write blob data** (data plane). Which two properties in the role definition should you configure?

- A. Actions and NotActions
- B. Actions and DataActions
- C. NotActions and NotDataActions
- D. DataActions and NotDataActions

**Answer: B**
**Explanation:**
Azure RBAC role definitions have separate properties for management-plane and data-plane operations:

| Property | Scope | Example operations |
|---------|-------|-------------------|
| **Actions** | Management plane (ARM) | `Microsoft.Storage/storageAccounts/read` |
| **NotActions** | Subtract from Actions | Removes specific management permissions |
| **DataActions** | Data plane | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`, `write` |
| **NotDataActions** | Subtract from DataActions | Removes specific data permissions |

For this scenario:
- `Actions`: `["Microsoft.Storage/storageAccounts/read"]` (management plane)
- `DataActions`: `["Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"]` (data plane)

**NotActions ≠ Deny**: `NotActions` subtracts permissions within the same role definition. It does NOT deny access granted by another role assignment — if a second role grants delete, the user can still delete.

---

### Question 8
A user has a custom role with `Actions: ['Microsoft.Storage/*']` and `NotActions: ['Microsoft.Storage/storageAccounts/delete']`. The user is also assigned the **Storage Account Contributor** built-in role at the same scope. Can the user delete storage accounts?

- A. No, because NotActions explicitly denies delete
- B. No, because the custom role restricts Storage operations
- C. Yes, because Azure RBAC is an additive model
- D. Yes, only if the user is a subscription Owner

**Answer: C**
**Explanation:**
Azure RBAC uses an **additive model**: effective permissions are the union of all role assignments minus `NotActions` within each individual role.

**Critical concept**: `NotActions` is **not a deny rule**. It only subtracts permissions from the `Actions` list within the **same role definition**. It has no effect on permissions granted by other role assignments.

**Effective permissions here:**
| Role | Grants delete? |
|------|--------------|
| Custom role | `*` minus `delete` = ❌ delete via this role |
| Storage Account Contributor | ✅ includes `Microsoft.Storage/storageAccounts/delete` |
| **Combined (additive)** | ✅ **Can delete** |

**To actually block delete across all roles**: Use an Azure Policy with `Deny` effect, or use a Resource Lock. There is no "deny" in RBAC unless a Role Assignment explicitly uses an Azure Policy or a deny assignment.

---

### Question 9
You plan to create a budget to monitor costs. The budget must reset automatically every three months. Which **Time Grain** value should you select when creating the budget?

- A. Monthly
- B. Quarterly
- C. Annual
- D. BillingMonth

**Answer: B**
**Explanation:**
Azure Cost Management budget **Time Grain** controls how often the budget period resets:

| Time Grain | Reset period |
|-----------|-------------|
| Monthly | Every calendar month |
| **Quarterly** | Every 3 months |
| Annual | Every 12 months |

The question specifies "reset automatically every three months" → **Quarterly**.

**Budget anatomy**:
- Amount: budget threshold (e.g., $1,000)
- Time Grain: how often it resets
- Alert conditions: % of budget or actual amount thresholds
- Alert recipients: email addresses or action groups

---

### Question 10
Your organization uses Azure Management Groups. A security administrator asks about the root management group. Which statement correctly describes a characteristic of the root management group?

- A. The root management group can be deleted if it contains no subscriptions
- B. Multiple root management groups can exist in a tenant
- C. The root management group cannot be moved or deleted
- D. Global Administrators cannot access the root management group by default

**Answer: C**
**Explanation:**
The **root management group** has these fixed characteristics:
- Created automatically when Azure Management Groups are first used
- **Cannot be moved or deleted** — it is the permanent top-level container
- All subscriptions and management groups ultimately fold up into the single root
- One root management group per Entra tenant

**Access**: Global Administrators can elevate themselves to User Access Administrator at the root scope (`/`) using the "Access management for Azure resources" toggle in Entra ID — but this must be explicitly done; they do not have root access by default.

**Naming**: The root management group's display name defaults to "Tenant Root Group" but can be renamed. Its ID matches the Entra tenant ID and cannot be changed.

---

## 3) Storage

### Question 11
You have an Azure Storage account. You need to provide time-limited access to a blob container for external partners. The solution must allow you to **revoke access without regenerating storage account keys**. Which type of SAS should you use?

- A. Account SAS
- B. User Delegation SAS
- C. Service SAS with a stored access policy
- D. Service SAS without a stored access policy

**Answer: C**
**Explanation:**
| SAS type | Revocable without key rotation? | How to revoke |
|---------|--------------------------------|--------------|
| Account SAS | ❌ (signed with account key) | Must rotate key |
| Service SAS (no policy) | ❌ (signed with account key) | Must rotate key |
| **Service SAS + stored access policy** | ✅ | Delete or modify the stored access policy |
| User Delegation SAS | ✅ (revoke via Entra ID token) | Revoke the user delegation key |

**Service SAS with a stored access policy**: The SAS token references the policy by name. To revoke all SAS tokens linked to that policy, simply delete or modify the policy — no key rotation required.

**User Delegation SAS** (option B) also allows revocation, but requires Entra ID authentication and is scoped to blob/queue operations only. The question specifies "without regenerating keys" without specifying Entra — stored access policy is the canonical exam answer for this pattern.

---

### Question 12
You have an Azure subscription that contains a storage account. Your company security policy requires that applications use **Microsoft Entra ID** to authorize requests. You need to prevent applications from using storage account access keys to access the storage account. The solution must still allow user delegation SAS tokens. What should you do?

- A. Rotate the storage account access keys
- B. Delete the storage account access keys
- C. Disable shared key authorization on the storage account
- D. Set the storage account access tier to Cool

**Answer: C**
**Explanation:**
**Disabling shared key authorization** (`AllowSharedKeyAccess = false`) on the storage account:
- ✅ Blocks access using account keys or any SAS signed with an account key
- ✅ Allows **User Delegation SAS** (signed with a Microsoft Entra identity credential)
- ✅ Allows direct Entra ID authentication (RBAC)

**Why not rotate or delete keys?**
- Rotating keys generates new keys but doesn't disable key-based auth
- You cannot permanently delete storage account keys — they can only be rotated

**User Delegation SAS** is signed using a user delegation key obtained through Entra ID authentication, not the storage account key. It remains fully functional even when shared key authorization is disabled.

```
az storage account update \
  --name <storage-account-name> \
  --resource-group <rg-name> \
  --allow-shared-key-access false
```

---

### Question 13
You have an Azure Storage account. You attempt to create a key expiration policy, but the **Set rotation reminder** button is grayed out in the Azure portal. What should you do to enable the key expiration policy configuration?

- A. Upgrade the storage account to StorageV2 kind
- B. Enable Microsoft Defender for Storage on the storage account
- C. Enable key rotation using Azure Key Vault and configure the storage account to use Key Vault managed keys
- D. Assign the Storage Account Key Operator Service Role to the storage account's managed identity

**Answer: D**
**Explanation:**
To configure automatic key rotation reminders and expiration policies for storage account access keys, the storage account needs to be enrolled in Azure Key Vault key rotation:

1. Enable a **system-assigned managed identity** on the storage account
2. Assign the **Storage Account Key Operator Service Role** to that identity at the storage account scope
3. This allows Key Vault to manage the key rotation schedule
4. Once enrolled, the "Set rotation reminder" button becomes active in the portal

The button is grayed out because the storage account lacks the permissions needed for Key Vault to manage its keys.

---

## 4) Compute

### Question 14
You have an Azure virtual machine named VM1 running Windows Server 2022. The Custom Script Extension fails to execute a PowerShell script. You need to review the detailed logs to troubleshoot the issue. Where should you look for the extension logs on VM1?

- A. `C:\Windows\Logs\CustomScript\`
- B. `C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\`
- C. `C:\ProgramData\AzureExtensions\Logs\`
- D. `C:\Users\AzureUser\AppData\Local\Temp\`

**Answer: B**
**Explanation:**
On Windows VMs, Azure VM extension logs are stored at:

```
C:\WindowsAzure\Logs\Plugins\<ExtensionName>\<version>\
```

For Custom Script Extension specifically:
```
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\<version>\
```

Related paths:
- Extension status files: `C:\WindowsAzure\Logs\Plugins\...\RuntimeSettings\`
- Script download location: `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>\Downloads\`

**On Linux VMs**, extension logs are at:
```
/var/log/azure/<ExtensionName>/
```

**Common troubleshooting flow**: Check extension logs → check VM Agent logs (`C:\WindowsAzure\Logs\WaAppAgent.log`) → check NSG outbound rules for `168.63.129.16`.

---

### Question 15
You are using the Custom Script Extension to run a PowerShell script that installs and configures a web application. The script execution is expected to take approximately **75 minutes**. What should you consider regarding the script execution time?

- A. The script will fail because the maximum execution time is 60 minutes
- B. The script should complete within the 90-minute default timeout limit
- C. The script will fail because extensions cannot run for more than 30 minutes
- D. There is no execution time limit for Custom Script Extension

**Answer: B**
**Explanation:**
The **Custom Script Extension** has a default execution timeout of **90 minutes**. A script that takes 75 minutes is within this limit and should complete successfully under normal conditions.

| Extension timeout | Duration |
|------------------|---------|
| Custom Script Extension default | **90 minutes** |
| Custom Script Extension maximum configurable | 90 minutes (fixed) |

**Important considerations for long-running scripts:**
- If the script approaches 90 minutes, consider breaking it into stages
- Ensure `168.63.129.16` is accessible for the entire duration (for status reporting)
- Progress/status is reported back to Azure through the VM Agent — long gaps may look like hangs

**Exam trap**: Common wrong answers use 30 or 60 minutes. The actual limit is 90 minutes.

---

### Question 16
You have an Azure virtual machine. Your organization requires that all **temp disks and disk caches** are encrypted at rest. The solution must **not use VM CPU resources** for encryption. Which encryption method should you use?

- A. Azure Disk Encryption (ADE) with BitLocker
- B. Server-Side Encryption (SSE) with platform-managed keys
- C. Encryption at Host
- D. Confidential disk encryption

**Answer: C**
**Explanation:**
| Encryption method | What it encrypts | Uses VM CPU? |
|------------------|-----------------|-------------|
| SSE (default) | Managed disks at rest on Azure storage | ❌ |
| **Azure Disk Encryption (ADE)** | OS + data disks using BitLocker/DM-Crypt | ✅ (runs inside VM) |
| **Encryption at Host** | **Temp disk, disk caches, OS+data disks** | ❌ (done at storage host level) |
| Confidential disk encryption | Full disk with vTPM in confidential VMs | Varies |

**Encryption at Host** encrypts data at the storage host (Azure hardware) before it reaches Azure Storage — this covers temp disks and disk caches that standard SSE does not cover, and it does NOT consume VM CPU resources.

**ADE** uses BitLocker (Windows) or DM-Crypt (Linux) — these run **inside the VM** and consume CPU.

**Exam keyword**: "temp disks and disk caches" + "no VM CPU resources" → **Encryption at Host**

---

## 5) App Service

### Question 17
You have an Azure App Service named App1 that runs on a **Linux-based App Service plan**. App1 has a deployment slot named `staging`. You want to configure **auto swap** so that code deployed to the staging slot is automatically swapped to production after warm-up. What should you do?

- A. Enable the auto swap setting directly in the staging slot configuration
- B. Configure a startup command in the App Service that triggers a swap
- C. Implement a swap step in your CI/CD pipeline (Azure Pipelines or GitHub Actions) after deployment
- D. Use Azure App Configuration to trigger the swap automatically

**Answer: C**
**Explanation:**
**Auto swap is not supported for Linux-based App Service plans** (including containers). It is only available for Windows App Service plans.

| Platform | Auto swap support |
|---------|-----------------|
| Windows App Service | ✅ Supported (via portal setting) |
| **Linux App Service** | ❌ Not supported |
| Web App for Containers | ❌ Not supported |

**Workaround for Linux**: Implement the swap operation as a **post-deployment step in a CI/CD pipeline**:

```yaml
# Azure Pipelines example
- task: AzureAppServiceManage@0
  inputs:
    action: 'Swap Slots'
    webAppName: 'App1'
    sourceSlot: 'staging'
```

This achieves the same result: deploy to staging → warm up → swap to production — but triggered by the pipeline rather than automatically by App Service.

---

## 6) Azure Firewall

### Question 18
You have Azure Firewall Premium with a Firewall Policy. You need to filter outbound HTTP/S traffic to allow access only to specific FQDNs. Two different FQDNs (`api.contoso.com` and `app.contoso.com`) resolve to the **same IP address**. The filtering must distinguish between them. Which type of rule should you use?

- A. Network rules (FQDN-based)
- B. Application rules
- C. NAT rules
- D. DNAT rules

**Answer: B**
**Explanation:**
**Application rules** in Azure Firewall Premium use an **application-level transparent proxy** that inspects the **Server Name Indication (SNI) TLS extension** (for HTTPS) and the HTTP `Host` header. This allows filtering by FQDN even when multiple FQDNs share the same IP address.

| Rule type | Filtering basis | Can distinguish same-IP FQDNs? |
|-----------|----------------|-------------------------------|
| **Application rules** | FQDN (via SNI/Host header) | ✅ Yes |
| Network rules | IP address + port | ❌ No — same IP = same rule |
| NAT rules | Destination IP + port (translation) | ❌ No |

**Why SNI matters**: When a client connects to HTTPS, the TLS handshake includes the SNI field containing the target hostname. Azure Firewall reads this before decryption to determine which FQDN is being requested, enabling per-FQDN filtering without needing SSL inspection.

---

## 7) AKS

### Question 19
You have an Azure Kubernetes Service (AKS) cluster with one system node pool. You need to add a **Spot node pool** for batch processing workloads. What is a requirement for adding the Spot node pool?

- A. The Spot node pool must use the same VM size as the system node pool
- B. The cluster must be in a region that supports availability zones
- C. The Spot node pool mode must be set to User (not System)
- D. Spot node pools require Azure CNI networking

**Answer: C**
**Explanation:**
AKS Spot node pools have the following constraints:

| Requirement | Detail |
|-------------|--------|
| **Pool mode** | Must be **User** (Spot pools cannot be system node pools) |
| VM size | Can differ from system pool |
| Eviction | Azure can evict Spot VMs with 30-second notice |
| Workload suitability | Stateless, fault-tolerant, interruption-tolerant (batch processing, CI/CD agents) |

**System vs User node pool:**
- **System**: Runs critical AKS components (CoreDNS, metrics-server). Must always have at least 1 running node.
- **User**: Runs application workloads. Spot VMs are only supported in User mode.

**Key consideration**: Spot VMs can be evicted at any time. Workloads must handle interruption gracefully (checkpointing, retries).

---

### Question 20
You have an Azure Firewall Policy (parent) with threat intelligence set to **Alert and deny**. You create a child policy that inherits from the parent policy. Which threat intelligence modes can you configure on the child policy?

- A. Off only
- B. Alert only
- C. Alert and deny only
- D. Off, Alert, or Alert and deny

**Answer: C**
**Explanation:**
Azure Firewall child policies can only be configured with a threat intelligence mode that is **the same as or stricter than the parent policy**. Since **Alert and deny** is the strictest available mode, a child policy inheriting from a parent set to "Alert and deny" can only be set to **Alert and deny**.

**Threat intelligence modes (strictness order):**

| Mode | Action on malicious traffic |
|------|----------------------------|
| Off | No action |
| Alert | Log and allow |
| **Alert and deny** | Log and block (strictest) |

**Inheritance rule**: child mode ≥ parent mode (strictness). Since the parent is already at maximum strictness, the child has no option to set a less-strict mode.

**Why this design?** Parent policies enforce organizational security baselines. Child policies can add more restrictions for specific environments but cannot weaken inherited settings.

---
