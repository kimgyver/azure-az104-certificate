# AZ-104 Practice Questions — English Set 6
**Coverage**: Derived from 2.txt chat reviews — Custom Script Extension logs on Linux, Microsoft Graph PowerShell (Get-MgUser/Update-MgUser), Azure Policy effects and operations, VM Insights, NSG rule priority, Resource Locks, Load Balancer health probe, ASR Re-protect/Commit, Availability Sets, App Service slots/auto-swap/SSL, Azure Storage lifecycle/SAS/File Sync/auth, Private Endpoints/DNS, Networking (Application Gateway, Azure Firewall, Route Tables), Azure Monitor alerts, Bicep templates, Key Vault, Dynamic Groups.

Total: 44 questions | Pass target: 700/1000

---

## 1) Compute

### Question 1
You have an Azure Linux virtual machine that has the Custom Script Extension installed. The extension is showing a failed status, and you need to troubleshoot the issue by reviewing the extension logs. Where should you look for the extension logs on the virtual machine?

- A. `/var/log/waagent.log`
- B. `/var/log/azure/custom-script-extension/handler.log`
- C. `/var/lib/waagent/custom-script/work/`
- D. `/var/log/messages`

**Answer: B**
**Explanation:**
On Azure Linux VMs, extension-specific logs are stored under the Azure agent's extension directory:

```
/var/log/azure/<ExtensionName>/
```

For the Custom Script Extension specifically:
```
/var/log/azure/custom-script-extension/handler.log
```

`handler.log` contains script execution output, error details, and status information — it is the primary file to inspect when a Custom Script Extension shows a failed status.

**Related paths:**
| Path | Purpose |
|------|---------|
| `/var/log/azure/custom-script-extension/handler.log` | Extension execution details and errors ✅ |
| `/var/log/waagent.log` | Azure Linux Agent (broader platform-level troubleshooting) |
| `/var/lib/waagent/` | Extension binaries, settings, and downloaded scripts |

**On Windows VMs**, the equivalent path is:
```
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\<version>\
```

**Exam tip**: "Linux VM Custom Script Extension failed" → start at `/var/log/azure/custom-script-extension/handler.log`.

---

## 2) Identity and Governance

### Question 2
You have a Microsoft Entra tenant with multiple user accounts. You need to use PowerShell to change the usage location for all users in the Accounting department to France. Which combination of Microsoft Graph PowerShell cmdlets should you use?

- A. `Get-AzureADUser` and `Set-AzureADUser`
- B. `Get-MgUser` and `Update-MgUser`
- C. `Get-MsolUser` and `Set-MsolUser`
- D. `Get-AzADUser` and `Update-AzADUser`

**Answer: B**
**Explanation:**
The **Microsoft Graph PowerShell SDK** is the current recommended module for managing Microsoft Entra ID (formerly Azure AD). The two cmdlets needed are:

| Cmdlet | Purpose |
|--------|---------|
| **`Get-MgUser`** | Retrieve users; supports `-Filter` for OData queries |
| **`Update-MgUser`** | Update user properties including `UsageLocation` |

**Example usage:**
```powershell
# Step 1: Get all Accounting department users
$users = Get-MgUser -Filter "department eq 'Accounting'" -All

# Step 2: Update usage location for each user
foreach ($user in $users) {
    Update-MgUser -UserId $user.Id -UsageLocation "FR"
}
```

**Why not the other options?**
| Module | Status |
|--------|--------|
| `AzureAD` (`Get-AzureADUser`) | Deprecated; retirement announced |
| `MSOnline` (`Get-MsolUser`) | Deprecated; retirement announced |
| `Az` module (`Get-AzADUser`) | Azure resource management, not Entra user properties |

**Exam tip**: Any question about managing Entra ID users with PowerShell in a current/recommended context → **Microsoft Graph PowerShell** (`Get-MgUser` / `Update-MgUser`).

---

### Question 3
You are creating a custom Azure Policy definition that automatically adds a tag to resources when the tag is missing. You need to configure the policy to add the tag during resource creation **and** allow remediation of existing non-compliant resources. Which effect should you use in the policy definition?

- A. `append`
- B. `deployIfNotExists`
- C. `modify`
- D. `audit`

**Answer: C**
**Explanation:**
The **`modify`** effect is designed specifically for adding, updating, or removing resource properties (including tags) and supports both deployment-time enforcement and remediation of existing resources.

**Effect comparison for tag management:**
| Effect | Adds tag at creation? | Remediates existing resources? | Use case |
|--------|----------------------|-------------------------------|---------|
| `append` | ✅ (append-only) | ❌ Limited | Add array values; cannot modify existing tags |
| **`modify`** | ✅ | ✅ **via Remediation Task** | **Add/update/remove tags or properties** |
| `deployIfNotExists` | Deploys related resources | ✅ | Deploy missing companion resources (e.g., diagnostic settings) |
| `audit` / `deny` | ❌ | ❌ | Evaluate compliance only / block deployment |

**Key characteristics of `modify`:**
- Works at deployment time (intercepts create/update operations)
- Supports **Remediation Tasks** that can be run against existing non-compliant resources
- Requires a **managed identity** on the policy assignment (for remediation authorization)
- Supports tags, location, SKU, and other resource properties

**Sample policy rule (add tag if missing):**
```json
{
  "if": {
    "field": "tags['Environment']",
    "exists": "false"
  },
  "then": {
    "effect": "modify",
    "details": {
      "roleDefinitionIds": [
        "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
      ],
      "operations": [{
        "operation": "add",
        "field": "tags['Environment']",
        "value": "Production"
      }]
    }
  }
}
```

---

### Question 4
Your company has an Azure subscription that contains a resource group named RG1. RG1 contains several virtual machines and storage accounts. A user named Admin1 has the Contributor role for RG1. Admin1 needs to apply a CanNotDelete lock to a virtual machine named VM1 within RG1. What should you do to enable Admin1 to apply the lock?

- A. Assign the User Access Administrator role to Admin1 at the RG1 scope
- B. Assign the Owner role to Admin1 at the RG1 scope
- C. Assign the Resource Policy Contributor role to Admin1 at the VM1 scope
- D. No additional action is needed; Contributor can manage locks

**Answer: B**
**Explanation:**
Managing resource locks requires the `Microsoft.Authorization/locks/*` permission. Of the built-in roles, only **Owner** and **User Access Administrator** include this permission.

**Role capabilities:**
| Role | Manage resources | Assign RBAC roles | Manage Locks |
|------|-----------------|------------------|-------------|
| Reader | ❌ | ❌ | ❌ |
| Contributor | ✅ | ❌ | ❌ |
| User Access Administrator | ❌ (resources) | ✅ | ✅ |
| **Owner** | ✅ | ✅ | ✅ |

**Why Owner (not User Access Administrator)?**
Both technically grant the required lock permission. However, Microsoft's official exam documentation for this scenario specifies **Owner** as the answer, because Owner includes both resource management rights (already held by Contributor) and the additional authorization permissions needed for locks. When the question does not specify "minimum privilege", the unambiguous answer that satisfies all conditions is Owner.

**Reference**: `Microsoft.Authorization/*` — only Owner and User Access Administrator are granted these actions among built-in roles.

**Exam pattern:**
- "Minimum privilege to manage locks only" → User Access Administrator
- "Enable Admin1 (already a Contributor) to apply a lock" → **Owner**

---

## 3) Monitoring

### Question 5
You have an Azure subscription that contains a virtual machine named VM1. VM1 has VM Insights enabled. You want to use the **Map feature** in VM Insights to view running processes on VM1 and their dependencies. Which additional component must be installed on VM1 to enable the Map feature?

- A. Log Analytics Agent only
- B. Azure Monitor Agent only
- C. Dependency Agent
- D. Azure Diagnostics Extension

**Answer: C**
**Explanation:**
VM Insights consists of two components with distinct roles:

| Component | Role | Required for Map? |
|-----------|------|------------------|
| **Azure Monitor Agent** (or Log Analytics Agent) | Collects performance metrics and logs | Partial (enables Performance view) |
| **Dependency Agent** | Collects process-level and network connection data | ✅ **Required for Map feature** |

**What the Map feature visualizes:**
- Running processes on the VM
- TCP connections between processes
- Inbound and outbound network connections
- External/internal service dependencies

The Map feature requires **process-level data** that only the Dependency Agent can collect. The Azure Monitor Agent alone provides CPU/memory/disk metrics but cannot enumerate processes or map network connections.

**Installation**: The Dependency Agent runs as a service on the VM and sends data to the Log Analytics workspace. It requires the Azure Monitor Agent (or Log Analytics Agent) to already be installed.

**Exam tip**: "Map feature", "running processes", "dependencies" → **Dependency Agent** is the required additional component.

---

## 4) Networking

### Question 6
You have an Azure subscription that contains a virtual machine named VM1. VM1 has a network interface named NIC1 that is connected to a subnet named Subnet1. An NSG named NSG1 is associated with Subnet1. NSG1 contains the following custom inbound rules:

| Rule | Priority | Action | Protocol | Port | Source |
|------|----------|--------|----------|------|--------|
| Rule1 | 100 | Allow | TCP | 443 | 10.0.0.0/8 |
| Rule2 | 200 | Deny | TCP | 443 | Any |
| Rule3 | 300 | Allow | TCP | 443 | Internet |

A user attempts to connect to VM1 on TCP port 443 from the internet using a public IP address. What is the result of the connection attempt?

- A. The connection is allowed by Rule3
- B. The connection is allowed by Rule1 since the internet is within 10.0.0.0/8
- C. The connection is denied by Rule2
- D. The connection is denied by the default Deny All rule

**Answer: C**
**Explanation:**
NSG rules are evaluated in **priority order** — lower number = higher priority. Processing stops at the **first matching rule**.

**Evaluation for internet source IP (e.g., 52.168.1.50) on TCP 443:**

| Rule | Priority | Source match? | Result |
|------|----------|---------------|--------|
| Rule1 | 100 | ❌ `52.168.1.50` is not in `10.0.0.0/8` | Skip |
| **Rule2** | **200** | ✅ **`Any` matches all sources** | **🚫 DENY — stops here** |
| Rule3 | 300 | Never reached | — |

**Key concepts:**
- `Any` in Rule2 matches the internet source before Rule3 can be evaluated
- Rule3 (Allow Internet, priority 300) is never reached because Rule2 (Deny Any, priority 200) matches first
- **NSG evaluation stops at the first match** — lower priority rules are not consulted once a match is found

**Common exam trap**: Rule3 appears to allow internet traffic, but because Rule2 with a lower priority number appears first and uses `Any`, internet traffic is blocked before Rule3 is evaluated.

**To fix**: Move Rule3 above Rule2 (assign it a lower priority number, e.g., 150) to allow internet traffic before the deny-all catches it.

---

### Question 7
You have an Azure subscription that contains a Standard SKU internal load balancer named LB1. LB1 has a backend pool containing three virtual machines. Users report intermittent connectivity issues. You discover that health probes are failing. Which IP address must the virtual machines allow inbound traffic from for the health probes to succeed?

- A. `10.0.0.1` (Azure default gateway)
- B. `169.254.169.254` (Azure Instance Metadata Service)
- C. `168.63.129.16`
- D. `172.16.0.1` (load balancer internal IP)

**Answer: C**
**Explanation:**
Azure Load Balancer health probes originate from the Azure infrastructure address **`168.63.129.16`**. This is a special non-routable virtual IP used exclusively by Azure platform services.

**What uses `168.63.129.16`:**
| Service | Use |
|---------|-----|
| **Load Balancer health probes** | Probe VMs to determine health |
| VM Agent heartbeat | Report VM status to Azure fabric |
| Custom Script Extension | Status reporting during execution |
| DHCP / DNS resolution | Internal Azure DNS responses |

**Why health probes fail:**
If an NSG rule or OS firewall (Windows Firewall / iptables) blocks inbound traffic from `168.63.129.16`, the load balancer cannot reach the VM's health probe port and marks the VM as **unhealthy**, removing it from the backend pool.

**Resolution — NSG rule to add:**
```
Direction: Inbound
Source: 168.63.129.16 (or service tag AzureLoadBalancer)
Destination: Any
Port: <health probe port>
Protocol: TCP
Action: Allow
Priority: (must be lower number than any Deny rule)
```

**Note**: The service tag `AzureLoadBalancer` covers `168.63.129.16` in NSG rules — either can be used.

**Exam tip**: "Health probes failing on Standard Load Balancer" → check NSG for inbound allow from `168.63.129.16` / `AzureLoadBalancer`.

---

### Question 8
You have an Azure subscription that contains an availability set named AS1 configured with 2 fault domains and 5 update domains. AS1 contains 10 virtual machines. Microsoft performs planned maintenance that requires VM restarts. What is the maximum number of VMs in AS1 that will be unavailable at any given time during the planned maintenance?

- A. 1
- B. 2
- C. 5
- D. 10

**Answer: B**
**Explanation:**
Planned maintenance is applied one **Update Domain (UD)** at a time. With 5 UDs and 10 VMs, Azure distributes 2 VMs per UD.

| Update Domain | VMs |
|---------------|-----|
| UD1 | VM1, VM2 |
| UD2 | VM3, VM4 |
| UD3 | VM5, VM6 |
| UD4 | VM7, VM8 |
| UD5 | VM9, VM10 |

Microsoft restarts one UD at a time → maximum **2 VMs** unavailable simultaneously.

**Key rules:**
- Fault Domains are for hardware failure isolation — **not** used for planned maintenance scheduling
- Update Domains determine the **sequential batch** for planned maintenance restarts
- Formula: VMs per UD = Total VMs ÷ UD count = 10 ÷ 5 = **2**

**Exam tip**: "Planned maintenance + Availability Set" → focus on **Update Domains**, not Fault Domains.

---

### Question 9
You are creating an Azure Resource Manager template to deploy an availability set for virtual machines that will use managed disks. You need to ensure that the availability set supports managed disks. Which property must you configure in the availability set resource definition?

- A. Set `properties.managed` to `true`
- B. Set `sku.name` to `Aligned`
- C. Set `properties.platformFaultDomainCount` to 3
- D. Set `sku.name` to `Classic`

**Answer: B**
**Explanation:**
For availability sets that contain VMs using managed disks, you must set `sku.name` to **`Aligned`**. This ensures storage fault domains are aligned with compute fault domains, providing proper isolation.

**ARM template snippet:**
```json
{
  "type": "Microsoft.Compute/availabilitySets",
  "apiVersion": "2023-03-01",
  "sku": {
    "name": "Aligned"
  },
  "properties": {
    "platformFaultDomainCount": 2,
    "platformUpdateDomainCount": 5
  }
}
```

| SKU name | Use case |
|----------|---------|
| **`Aligned`** | Managed disks (current standard) |
| `Classic` | Unmanaged disks (legacy) |

**Common trap**: `managed: true` is not a valid property for this purpose — the correct setting is `sku.name = Aligned`.

**Exam tip**: Availability Set + Managed Disk → set `sku.name` to `Aligned`.

---

### Question 10
You have an Azure App Service web app named WebApp1 running on a Standard tier App Service plan. WebApp1 has a staging deployment slot. The staging slot has several application settings, and you need to ensure that an application setting named **DatabaseConnection** remains with the staging slot when you swap the staging slot to production. What should you configure for the DatabaseConnection setting?

- A. Configure it as a Connection String
- B. Mark it as a **Slot setting** (deployment slot setting)
- C. Set it as an environment variable in the deployment pipeline
- D. No configuration is needed; settings always stay with the slot

**Answer: B**
**Explanation:**
By default, application settings **move with the code** during a slot swap. To keep a setting **pinned to a specific slot**, mark it as a **Slot setting** (also called a "sticky" setting).

| Setting type | Behavior during swap |
|-------------|---------------------|
| Regular app setting | Moves with the slot content to the target slot |
| **Slot setting (sticky)** | **Stays in the slot where it was configured** |

**After configuring DatabaseConnection as a slot setting:**
- Staging slot retains its `DatabaseConnection` value after swap
- Production slot retains its own `DatabaseConnection` value

**Exam tip**: "Setting must remain with the staging slot after swap" → **Slot setting**.

---

### Question 11
You have an Azure App Service web app named WebApp1. You plan to configure automatic deployment to production using deployment slots. You need to configure the staging deployment slot so that when code is deployed to the staging slot, the app is automatically swapped into production after warming up. Which configuration should you enable on the staging slot?

- A. Slot settings
- B. Continuous deployment
- C. Auto swap
- D. Blue-green deployment

**Answer: C**
**Explanation:**
**Auto swap** automatically swaps the staging slot into production after the app finishes warming up (startup/health check). No manual intervention is needed.

**Auto swap workflow:**
1. Code is deployed to the staging slot
2. App warms up in staging (health checks pass)
3. Azure automatically swaps staging → production

| Feature | Purpose |
|---------|---------|
| Slot setting | Pin a setting to a specific slot |
| **Auto swap** | Automatically swap to production after warm-up |
| Continuous deployment | Trigger deployments from source control |

**Exam tip**: "Automatically swapped into production after warming up" → **Auto swap** on the staging slot.

---

### Question 12
You have an Azure App Service web app with a custom domain configured. You need to secure the custom domain with a TLS/SSL certificate. The solution must allow multiple domains to share the same IP address. Which TLS/SSL binding type should you use?

- A. IP-based SSL
- B. SNI SSL
- C. Shared SSL
- D. Wildcard SSL

**Answer: B**
**Explanation:**
**SNI SSL (Server Name Indication)** allows multiple domains to share the **same IP address** by transmitting the target domain name during the TLS handshake, enabling the server to select the correct certificate.

| Binding type | IP usage | Multiple domains on same IP |
|-------------|----------|----------------------------|
| IP-based SSL | Dedicated IP per domain | ❌ |
| **SNI SSL** | **Shared IP** | ✅ |

**Why SNI works:**
During TLS handshake, the client sends the target hostname (Server Name Indication) → server selects the matching certificate → certificate is presented without needing a dedicated IP.

**Exam tip**: "Multiple domains on the same IP" → **SNI SSL**.

---

### Question 13
You have an Azure virtual machine named VM1 that is replicated to a secondary region using Azure Site Recovery. A disaster occurs in the primary region. You successfully fail over VM1 to the secondary region and commit the failover. The primary region becomes available again. You need to enable protection so that VM1 in the secondary region replicates back to the primary region. What should you do first?

- A. Create a new replication policy
- B. Perform a Re-protect operation
- C. Run a test failover
- D. Delete the VM in the secondary region and redeploy

**Answer: B**
**Explanation:**
After a failover + commit, the original replication relationship is broken. To replicate back from secondary to primary, you must first **Re-protect** the VM. Re-protect reverses the replication direction.

**Post-failover workflow:**
```
Failover → Commit → [Primary recovers] → Re-protect → (Replicate back) → Failback
```

| Step | Action |
|------|--------|
| Failover | Switch to secondary region |
| Commit | Finalize — recovery points are deleted |
| **Re-protect** | **Reverse replication direction (secondary → primary)** |
| Failback | Switch back to primary region |

**Key point**: Re-protect is **mandatory** before failback — you cannot failback without first re-establishing replication.

**Exam tip**: "After failover + commit, replicate back to primary" → **Re-protect**.

---

### Question 14
You are creating a new backup policy for Azure virtual machines in a Recovery Services vault. You plan to use the Enhanced policy sub type. You configure the policy to take backups every 6 hours. What is the default retention period for instant recovery snapshots with this configuration?

- A. 1 day
- B. 2 days
- C. 7 days
- D. 30 days

**Answer: C**
**Explanation:**
Azure VM Backup (Enhanced policy) stores backups in two layers:

| Layer | Location | Default retention |
|-------|----------|------------------|
| **Instant Recovery Snapshot** | Near the VM (disk storage) | **7 days** |
| Vault backup | Recovery Services Vault | Per retention policy |

The instant recovery snapshot enables fast restore (local restore without vault download). With the Enhanced policy at 6-hour frequency, the **default** snapshot retention is **7 days**.

**Exam tip**: Enhanced policy + instant recovery snapshot → default retention = **7 days**.

---

## 2) Identity and Governance

### Question 15
You are creating a custom Azure Policy definition that automatically adds a tag to resources when the tag is missing. You need to configure the policy to add the tag during resource creation **and** allow remediation of existing non-compliant resources. Which effect should you use in the policy definition? *(Moved to Q3 — see above)*

---

### Question 15
You are creating a custom Azure Policy definition. You need to configure the policy to prevent users from deploying resources that do not meet your organization's requirements. Which effect should you configure in the policy rule?

- A. `audit`
- B. `append`
- C. `modify`
- D. `deny`

**Answer: D**
**Explanation:**
The **`deny`** effect blocks non-compliant resource deployments at creation/update time.

| Effect | Behavior |
|--------|---------|
| `audit` | Logs non-compliance; does **not** block |
| `append` | Adds properties; does not block |
| `modify` | Adds/updates properties; does not block creation |
| **`deny`** | **Blocks deployment of non-compliant resources** |

**Exam tip**: "Prevent deployment" / "block non-compliant resources" → **`deny`**.

---

### Question 16
You have an Azure subscription with a resource group named RG1 that has an `Environment` tag with a value of `Production`. You need to ensure that all resources deployed to RG1 automatically inherit the `Environment` tag and value from the parent resource group. The solution must **not** override existing `Environment` tag values on resources. Which `modify` operation should you use in the Azure Policy definition?

- A. `addOrReplace`
- B. `remove`
- C. `add`
- D. `copy`

**Answer: C**
**Explanation:**
The `modify` effect supports multiple operations for tag management:

| Operation | Behavior |
|-----------|---------|
| **`add`** | **Adds the tag only if it does not already exist** |
| `addOrReplace` | Adds or overwrites — ignores existing values |
| `remove` | Deletes the tag |
| `copy` | Copies a value from one field to another |

Because the requirement says **"must not override existing values"**, only `add` satisfies both conditions: inherit the tag when missing, preserve it when present.

**Exam tip**: "Inherit tag but never overwrite existing value" → `modify` effect with **`add`** operation.

---

### Question 17
You have an Azure subscription with a policy assignment that uses a deny effect. You need to test the policy on existing resources without blocking any resource creation or update requests. The solution must still evaluate resources for compliance. What should you configure on the policy assignment?

- A. Change the effect from `deny` to `audit`
- B. Set the Enforcement mode to **Disabled**
- C. Delete the policy assignment and recreate it with `audit`
- D. Add an exemption for all resource groups

**Answer: B**
**Explanation:**
Setting **Enforcement mode to Disabled** on a policy assignment preserves compliance evaluation but removes the blocking behavior. Resources will be assessed and marked as compliant/non-compliant, but no deployments will be blocked.

| Enforcement mode | Compliance evaluated? | Blocks deployments? |
|-----------------|----------------------|---------------------|
| Enabled (default) | ✅ | ✅ |
| **Disabled** | **✅** | **❌** |

This is ideal for **testing** a `deny` policy on existing resources without impacting ongoing operations.

**Exam tip**: "Test policy without blocking" → set enforcement mode to **Disabled**.

---

### Question 18
You are creating a Bicep file to deploy multiple storage accounts. The storage accounts should only be deployed if a parameter named `createStorage` is set to `true`. You need to combine a loop with conditional deployment. Which syntax should you use?

- A. `if(createStorage) [for i in range(0, storageCount): { ... }]`
- B. `[for i in range(0, storageCount): if(createStorage) { ... }]`
- C. `for i in range(0, storageCount): { condition: createStorage, ... }`
- D. `[for i in range(0, storageCount): { condition: createStorage }]`

**Answer: B**
**Explanation:**
In Bicep, when combining a loop with a condition, the **`for` expression must come first**, followed by the **`if` expression**:

```bicep
resource storageAccounts 'Microsoft.Storage/storageAccounts@2023-05-01' = [
  for i in range(0, storageCount): if(createStorage) {
    name: '${i}storage${uniqueString(resourceGroup().id)}'
    location: location
    sku: {
      name: 'Standard_LRS'
    }
    kind: 'StorageV2'
  }
]
```

**Rule**: `[for ... : if(...) { }]` — NOT `if(...) [for ... : { }]` (invalid syntax).

| Syntax order | Valid? |
|-------------|--------|
| `for` → `if` → body | ✅ |
| `if` → `for` → body | ❌ |

**Exam tip**: Bicep loop + condition → **`for` always comes before `if`**.

---

### Question 19
You have a main Bicep file that calls a module named `storageModule.bicep` to deploy a storage account. The module has an output named `storageEndpoint` that returns the primary blob endpoint. You need to reference this output value in the main Bicep file. Which syntax should you use?

- A. `storageModule.output.storageEndpoint`
- B. `storageModule.outputs.storageEndpoint`
- C. `outputs.storageModule.storageEndpoint`
- D. `storageModule['storageEndpoint']`

**Answer: B**
**Explanation:**
Bicep module outputs are accessed using the pattern: `<moduleAlias>.outputs.<outputName>`

```bicep
// Module declaration
module storageModule './storageModule.bicep' = {
  name: 'storageDeploy'
  params: { ... }
}

// Referencing the module output
var endpoint = storageModule.outputs.storageEndpoint
```

**Key rule**: The keyword is **`outputs`** (plural), not `output`.

**Exam tip**: Bicep module output reference → `moduleName.outputs.outputName`.

---

### Question 20
You have an Azure management group named MG1 that contains two subscriptions named Sub1 and Sub2. A user named User1 needs to be able to read all resources in both subscriptions. You need to grant User1 access using the minimum number of role assignments. What should you do?

- A. Assign the Reader role to User1 at the Sub1 scope and at the Sub2 scope
- B. Assign the Reader role to User1 at the MG1 management group scope
- C. Assign the Contributor role to User1 at the MG1 scope
- D. Assign the Reader role at the tenant root group scope

**Answer: B**
**Explanation:**
RBAC role assignments are **inherited downward** through the scope hierarchy:

```
Management Group (MG1)
   └── Sub1 (inherits MG1 assignments)
   └── Sub2 (inherits MG1 assignments)
```

Assigning Reader at **MG1** provides read access to **all resources in Sub1 and Sub2** with a **single role assignment** — the minimum possible.

| Approach | Role assignments required |
|---------|--------------------------|
| Sub1 + Sub2 separately | 2 |
| **MG1 (management group)** | **1** |

**Exam tip**: "Minimum role assignments across multiple subscriptions" → assign at the **management group scope**.

---

### Question 21
You have a Microsoft Entra tenant with a Microsoft Entra ID Free license. You plan to create a dynamic membership group that automatically adds users based on their department attribute. What should you do first before creating the dynamic group?

- A. Enable Security defaults
- B. Upgrade to Microsoft Entra ID Premium P1 or P2
- C. Enable Microsoft Entra ID Join for all devices
- D. Configure Self-Service Password Reset

**Answer: B**
**Explanation:**
**Dynamic membership groups** (groups where membership is automatically managed based on user/device attributes) require **Microsoft Entra ID Premium P1 or P2**. The feature is not available in the Free tier.

| License tier | Dynamic groups |
|-------------|---------------|
| Free | ❌ |
| Microsoft 365 Apps | ❌ |
| **Premium P1** | **✅** |
| **Premium P2** | **✅** |

**How dynamic groups work:**
- You define a rule (e.g., `department -eq "IT"`)
- Azure automatically adds/removes users whose attributes match the rule
- No manual group management required

**Exam tip**: "Dynamic membership group" → requires **Premium P1 or P2** first.

---

### Question 22
You have an Azure subscription that contains a resource group named RG1. RG1 contains five virtual machines. You apply a CanNotDelete lock at the RG1 scope. A user with the Owner role at the subscription scope attempts to delete one of the virtual machines. What is the expected result?

- A. The delete succeeds because Owner has full permissions
- B. The delete fails because locks override RBAC permissions
- C. The delete succeeds because the lock is at RG1 level, not VM level
- D. The delete fails because the Owner role does not include delete permissions

**Answer: B**
**Explanation:**
Management locks **override RBAC** — even the Owner role cannot delete a resource protected by a `CanNotDelete` lock.

**Key rules:**
- Locks applied to a parent scope (resource group) are **inherited by all child resources**
- The `CanNotDelete` lock prevents deletion regardless of the user's role
- Owner, Contributor, and all other roles are subject to lock enforcement

| Role | Can delete without lock? | Can delete with CanNotDelete lock? |
|------|--------------------------|-----------------------------------|
| Owner | ✅ | ❌ |
| Contributor | ✅ | ❌ |
| Custom role with delete | ✅ | ❌ |

**Exam tip**: "CanNotDelete lock + Owner tries to delete" → **delete fails**. Locks always win over RBAC.

---

## 3) Storage

### Question 23
You have an Azure Storage account that contains block blobs and append blobs. You create a lifecycle management policy to automatically transition blobs to cooler tiers. Which blob type will be transitioned by the tiering actions in the lifecycle management policy?

- A. Both block blobs and append blobs
- B. Append blobs only
- C. Block blobs only
- D. Page blobs only

**Answer: C**
**Explanation:**
Azure Storage Lifecycle Management tiering (Hot → Cool → Archive) applies **only to block blobs**.

| Blob type | Lifecycle tiering supported? |
|-----------|------------------------------|
| **Block blob** | ✅ |
| Append blob | ❌ |
| Page blob | ❌ |

**Why append blobs are excluded:**
Append blobs are designed for sequential append operations (e.g., logging). They do not support tier changes.

**Exam tip**: Lifecycle Management tiering → **block blobs only**.

---

### Question 24
You are configuring a lifecycle management policy for an Azure Storage account containing block blobs. You define a rule that includes both a `tierToCool` action after 30 days and a `delete` action after 30 days for the same blob type. Which action will lifecycle management apply to blobs that meet both conditions?

- A. `tierToCool` — tiering always takes priority
- B. `delete` — the least expensive action is applied
- C. Both actions are applied simultaneously
- D. Neither action — conflicting rules are ignored

**Answer: B**
**Explanation:**
When multiple lifecycle actions match the same blob simultaneously, Azure applies the **least expensive action**:

```
delete  <  tierToArchive  <  tierToCool
(cheapest)                  (most expensive)
```

Since `delete` is cheaper than `tierToCool` (deleted data costs nothing to store), `delete` wins.

**Reference**: *"If you define more than one action on the same blob, lifecycle management applies the least expensive action."* — Microsoft Learn

**Exam tip**: Multiple lifecycle actions on same blob → **least expensive action wins** (usually `delete`).

---

### Question 25
You have a storage account named storage1. Storage1 contains an Azure file share named share1 that is protected by Azure Backup using a snapshot-based backup policy. The backup policy has been running for several months. You attempt to create an on-demand backup but receive an error that the maximum snapshot limit has been reached. What is the maximum number of snapshots allowed per file share?

- A. 50
- B. 100
- C. 200
- D. 500

**Answer: C**
**Explanation:**
Azure file shares support a maximum of **200 snapshots** per file share. When this limit is reached, new snapshots (including on-demand backups) cannot be created until older snapshots are deleted.

**Resolution when limit is reached:**
1. Delete older/expired snapshots manually
2. Adjust the backup retention policy to reduce snapshot accumulation

**Exam tip**: Azure file share snapshot limit = **200**.

---

### Question 26
You have an Azure subscription that contains a storage account named storage1. Storage1 contains a blob container named container1. You have a service SAS that is associated with a stored access policy named policy1. You need to immediately revoke access to the SAS without regenerating the storage account keys. What should you do?

- A. Delete the SAS token from Azure Key Vault
- B. Regenerate the storage account access keys
- C. Delete the stored access policy named policy1
- D. Rotate the user delegation key

**Answer: C**
**Explanation:**
A service SAS associated with a stored access policy can be revoked instantly by **deleting the stored access policy**. The SAS contains a reference to the policy — when the policy is deleted, the SAS can no longer be validated.

| Revocation method | Effect | Impacts other SAS? |
|------------------|--------|-------------------|
| Delete SAS token | Only works if token is intercepted — not stored server-side | No |
| **Delete stored access policy** | **Immediately invalidates all SAS tokens referencing that policy** | Only those referencing the policy |
| Regenerate storage account keys | Invalidates all SAS tokens (account-key-based) | All SAS tokens |

**Exam tip**: "Revoke SAS without regenerating keys" → **delete the stored access policy**.

---

### Question 27
You have a blob container named container1 in a storage account. You need to create stored access policies for the container. What is the maximum number of stored access policies you can set on container1?

- A. 3
- B. 5
- C. 10
- D. 25

**Answer: B**
**Explanation:**
Azure Blob containers support a maximum of **5 stored access policies** per container.

**Exam tip**: Stored access policy limit per container = **5**.

---

### Question 28
You have an Azure storage account with an Azure file share. The storage account is currently configured for Microsoft Entra Domain Services authentication. You need to change the authentication to use on-premises AD DS instead. What should you do first?

- A. Enable AD DS authentication before disabling Entra Domain Services authentication
- B. Create a new storage account with AD DS authentication enabled
- C. Disable Microsoft Entra Domain Services authentication on the storage account
- D. Join the storage account to the on-premises domain

**Answer: C**
**Explanation:**
Azure Files supports only **one identity source** at a time for identity-based authentication. You cannot enable a second identity source while the first is still active.

**Required order:**
1. **Disable** Microsoft Entra Domain Services authentication ← do this first
2. Enable on-premises AD DS authentication

| Step | Action |
|------|--------|
| **Step 1** | **Disable Entra Domain Services authentication** |
| Step 2 | Prepare on-premises AD DS environment |
| Step 3 | Enable AD DS authentication on the storage account |

**Exam tip**: Azure Files identity-based auth → only one identity source at a time; always **disable the existing source first**.

---

### Question 29
You have an Azure File Sync deployment. Users upload files directly to the Azure file share using the Azure portal. You discover that changes made directly to the Azure file share are taking more than 24 hours to appear on the on-premises server endpoint. What should you do to reduce the sync delay?

- A. Increase the Azure File Sync agent version
- B. Restart the Storage Sync Service
- C. Run the `Invoke-AzStorageSyncChangeDetection` cmdlet
- D. Reduce the cloud tiering free space percentage

**Answer: C**
**Explanation:**
When files are changed directly in an Azure file share (via portal, REST API, or SMB from another client), a **cloud change detection job** must discover those changes before they can sync to server endpoints. This job runs **automatically only once every 24 hours**.

To immediately trigger change detection without waiting 24 hours, run:

```powershell
Invoke-AzStorageSyncChangeDetection `
  -ResourceGroupName "RG1" `
  -StorageSyncServiceName "SyncService1" `
  -SyncGroupName "SyncGroup1" `
  -CloudEndpointName "CloudEndpoint1" `
  -Path "/"
```

**Exam tip**: "Changes made directly to Azure file share taking too long to sync" → run **`Invoke-AzStorageSyncChangeDetection`**.

---

### Question 30
You have an Azure File Sync deployment with two Windows file servers named Server1 and Server2. Both servers are registered with the Storage Sync Service. A sync group named SyncGroup1 has a cloud endpoint pointing to an Azure file share. Server1 is added to SyncGroup1 with a server endpoint path of `D:\Data`. You need to add Server2 to SyncGroup1 to sync the same data. What is a valid server endpoint configuration for Server2?

- A. The server endpoint must be the same path `D:\Data` as Server1
- B. Any valid local NTFS path on Server2 (e.g., `E:\SharedData`)
- C. The server endpoint must point to a UNC path
- D. The server endpoint must be an Azure file share path

**Answer: B**
**Explanation:**
Each server endpoint in a sync group must be a **local NTFS volume path** on the registered server. The path does **not** need to match Server1's path — it can be any valid local folder.

**Valid server endpoint requirements:**
- Must be on an NTFS volume (not ReFS)
- Must be a local path (not UNC, not Azure file share path)
- Azure File Sync agent must be installed on the server
- Server must be registered with the Storage Sync Service

| Path example | Valid? |
|-------------|--------|
| `D:\Data` | ✅ |
| `E:\SharedData` | ✅ |
| `\\server\share` (UNC) | ❌ |
| Azure file share URL | ❌ |
| `C:\Windows` (system folder) | ❌ |

**Exam tip**: Azure File Sync server endpoint = any valid **local NTFS path**; does not need to match other servers in the sync group.

---

### Question 31
You have an Azure subscription that contains a storage account named storage1. You plan to manually rotate the access keys for storage1 periodically. You need to receive a reminder when it is time to rotate the keys. What should you configure for storage1?

- A. A Key Vault rotation policy
- B. An Azure Monitor alert
- C. A key expiration policy
- D. A managed identity rotation schedule

**Answer: C**
**Explanation:**
A **key expiration policy** enables you to configure a reminder for storage account access key rotation. When the specified interval elapses and the keys have not been rotated, Azure displays a reminder notification.

**Configuration path in Azure Portal:**
`Storage account → Access keys → Set rotation reminder`

**Key facts:**
- This is a **reminder only** — it does not automatically rotate the keys
- Access keys do not automatically expire; manual rotation is required
- The reminder is displayed in the portal when the interval has elapsed

| Feature | Purpose |
|---------|---------|
| Key regeneration | Actually rotates the key |
| **Key expiration policy** | **Sends a reminder when rotation is due** |
| Key Vault rotation | Automates rotation via Key Vault (different service) |

**Exam tip**: "Reminder when it is time to rotate keys" → **key expiration policy**.

---

### Question 32
You have an Azure storage account named storage1 that contains blob containers. A user named User1 has the **Storage Account Contributor** role assigned at the storage account scope. User1 accesses blob data through the Azure portal. Which authentication method does User1 use to access the blob data?

- A. Microsoft Entra ID (Azure RBAC)
- B. Account access key (Shared Key authorization)
- C. SAS token
- D. Anonymous public access

**Answer: B**
**Explanation:**
This is a common exam trap. The Azure portal determines the authentication method based on a priority rule:

**Portal authentication priority for blob access:**
1. If the user has `Microsoft.Storage/storageAccounts/listKeys/action` permission → **Shared Key (Account Access Key)**
2. If the user does NOT have listKeys permission → **Microsoft Entra ID (Azure AD)**

The **Storage Account Contributor** role **includes** `listKeys/action` → portal defaults to **Shared Key authorization**.

| Role | Includes listKeys? | Portal auth method |
|------|-------------------|--------------------|
| Storage Blob Data Reader | ❌ | Entra ID (Azure AD) |
| **Storage Account Contributor** | **✅** | **Shared Key (Access Key)** |
| Owner | ✅ | Shared Key (Access Key) |

**Exam tip**: Storage Account Contributor accessing blob data via portal → **Shared Key** (not Azure AD, even though RBAC is involved).

---

### Question 33
You have an Azure subscription that contains a virtual network named VNet1 and a storage account named storage1. You create a private endpoint named `pe-blob` for the blob subresource of storage1 in VNet1. You select the option to integrate with a private DNS zone during the private endpoint creation. Which private DNS zone is created?

- A. `privatelink.storage.core.windows.net`
- B. `privatelink.blob.core.windows.net`
- C. `privatelink.azure.blob.net`
- D. `blob.privatelink.core.windows.net`

**Answer: B**
**Explanation:**
When creating a private endpoint for Azure Storage with private DNS zone integration, the DNS zone name depends on the **subresource type**:

| Storage subresource | Private DNS zone |
|--------------------|-----------------|
| **blob** | **`privatelink.blob.core.windows.net`** |
| file | `privatelink.file.core.windows.net` |
| queue | `privatelink.queue.core.windows.net` |
| table | `privatelink.table.core.windows.net` |

**Purpose of the private DNS zone:**
Resolves `storage1.blob.core.windows.net` → private IP of the private endpoint (instead of the public IP).

**Exam tip**: Blob private endpoint + DNS integration → **`privatelink.blob.core.windows.net`**.

---

## 4) Networking

### Question 34
You have an Azure subscription that contains a storage account named storage1. A network administrator from a different subscription creates a private endpoint that targets storage1. The private endpoint connection appears in storage1 with a Connection state of **Pending**. You need to allow the private endpoint to connect to storage1. What should you do?

- A. Create a new private endpoint from the storage1 side
- B. Enable public network access on storage1
- C. Approve the private endpoint connection in storage1
- D. Configure a service endpoint on the VNet

**Answer: C**
**Explanation:**
When a private endpoint is created from a **different subscription**, the target resource owner must manually **approve** the connection. The `Pending` status indicates awaiting approval.

**Approval path:**
`storage1 → Networking → Private endpoint connections → [select connection] → Approve`

| Connection state | Meaning |
|-----------------|---------|
| Pending | Awaiting approval from resource owner |
| **Approved** | Connection is active |
| Rejected | Connection denied |
| Disconnected | Previously approved, now disconnected |

**Note**: If the private endpoint is created from within the same subscription and the user has appropriate permissions, it may auto-approve.

**Exam tip**: Private endpoint from a different subscription → `Pending` state → resource owner must **Approve**.

---

### Question 35
You have an Azure private DNS zone named `private.contoso.com`. You need to configure automatic registration of DNS records for virtual machines deployed in a virtual network named VNet1. You also need virtual machines in VNet2 to resolve records in the private DNS zone without automatic registration. What should you configure?

- A. Create a single VNet link to VNet1 with auto-registration enabled; VNet2 does not need a link
- B. Create a VNet link to VNet1 with auto-registration enabled, and a VNet link to VNet2 with auto-registration disabled
- C. Create VNet links to both VNet1 and VNet2 with auto-registration enabled
- D. Create a VNet link to VNet2 only; VNet1 registers records automatically

**Answer: B**
**Explanation:**
Azure Private DNS VNet links support two modes:

| Link mode | DNS registration | DNS resolution |
|-----------|----------------|---------------|
| **Auto-registration enabled** | ✅ VM A records created automatically | ✅ |
| **Auto-registration disabled** | ❌ No automatic records | ✅ Resolution only |

**Configuration:**
- VNet1 → link with **auto-registration = enabled** (VMs get DNS records automatically)
- VNet2 → link with **auto-registration = disabled** (can resolve records, but no auto-registration)

**Important**: VNet2 **must still have a VNet link** to resolve records in the private DNS zone.

**Exam tip**: "Auto-register DNS" → enable; "resolve only" → disable. Both VNets need a link to resolve.

---

### Question 36
You have an Azure subscription that contains a virtual network named VNet1. VNet1 has a subnet with a service endpoint enabled for `Microsoft.Storage`. Multiple virtual machines in the subnet access various Azure Storage accounts across different subscriptions. You need to ensure that virtual machines can only access a **single specific** storage account. What should you configure?

- A. A Network Security Group rule
- B. A private endpoint
- C. A service endpoint policy
- D. A firewall rule on the storage account

**Answer: C**
**Explanation:**
A **service endpoint policy** filters VNet traffic to specific Azure resources over service endpoints. Without a policy, enabling `Microsoft.Storage` service endpoint on a subnet allows access to **all** Azure Storage accounts globally. A service endpoint policy restricts access to only the listed accounts.

| Mechanism | Restricts to specific storage account? |
|-----------|----------------------------------------|
| Service Endpoint (alone) | ❌ Allows all Azure Storage |
| NSG rule | ❌ IP-based, not resource-based |
| **Service Endpoint Policy** | **✅ Restricts to specified accounts** |
| Private Endpoint | ✅ But more complex; different mechanism |

**Exam tip**: "Service endpoint but restrict to one specific storage account" → **service endpoint policy**.

---

### Question 37
You have an Azure subscription that contains a virtual machine named VM1. VM1 has VM Insights enabled. You want to use the **Map feature** in VM Insights to view running processes on VM1 and their dependencies. Which additional component must be installed on VM1 to enable the Map feature? *(See Question 5 above)*

---

### Question 37
You have an Azure Application Gateway named AppGW1. A backend health check shows that all servers in a backend pool are unhealthy. The servers are running and responding correctly when accessed directly. You need to configure a custom health probe to resolve this issue. Where should you associate the custom health probe?

- A. Listener
- B. Routing rule
- C. Backend pool
- D. Backend HTTP settings

**Answer: D**
**Explanation:**
In Azure Application Gateway, the **health probe is associated with Backend HTTP settings**, not the backend pool or listener directly.

**Application Gateway component flow:**
```
Listener → Routing Rule → Backend HTTP Settings → Backend Pool
                              ↑
                        Health probe is here
```

**Why Backend HTTP settings?**
Backend HTTP settings define how the gateway communicates with the backend (port, protocol, probe configuration). The probe is logically part of this communication configuration.

| Component | Role |
|-----------|------|
| Listener | Receives incoming requests |
| Routing rule | Maps listeners to backends |
| **Backend HTTP settings** | **Defines communication + health probe** |
| Backend pool | Contains the target VMs/IPs |

**Exam tip**: "Custom health probe for Application Gateway" → associate with **Backend HTTP settings**.

---

### Question 38
You are configuring an Azure Application Gateway that will host three websites: `www.contoso.com`, `www.fabrikam.com`, and `www.adatum.com`. All three websites use port 443 with HTTPS. Each website must be routed to its own backend pool. What type of listener should you create for each website?

- A. Basic listener
- B. Multi-site listener
- C. Path-based listener
- D. Redirect listener

**Answer: B**
**Explanation:**
A **multi-site listener** routes traffic based on the **HTTP Host header** (domain name), allowing multiple websites to share the same IP address and port.

| Listener type | Routing basis | Multiple domains on same IP? |
|--------------|--------------|------------------------------|
| Basic | IP + port only | ❌ Only one site |
| **Multi-site** | **Host header (domain name)** | **✅ Multiple sites** |
| Path-based | URL path (`/api`, `/images`) | Different concept (URL routing) |

**Traffic flow with multi-site listeners:**
```
HTTPS :443
  ├─ Host: www.contoso.com  → Backend Pool 1
  ├─ Host: www.fabrikam.com → Backend Pool 2
  └─ Host: www.adatum.com   → Backend Pool 3
```

**Exam tip**: Multiple hostnames on same Application Gateway → **multi-site listener** for each hostname.

---

### Question 39
You are configuring an Azure Firewall policy to allow outbound access to Windows Update endpoints. You want to use the simplest method that requires minimal maintenance. Which type of rule and configuration should you use?

- A. A network rule allowing TCP port 80 and 443 to all IPs
- B. An application rule with individual Windows Update FQDNs listed
- C. An application rule with the `WindowsUpdate` FQDN tag
- D. A DNAT rule for Windows Update traffic

**Answer: C**
**Explanation:**
**FQDN tags** in Azure Firewall represent groups of FQDNs for well-known Microsoft services. The `WindowsUpdate` FQDN tag encompasses all Windows Update endpoints and is **automatically maintained by Microsoft** — no manual updates required.

| Rule type | Best for |
|-----------|---------|
| Network rule | IP/port-based (Layer 3/4) |
| **Application rule + FQDN tag** | **Domain-based (Layer 7) with auto-managed FQDNs** |
| Application rule + individual FQDNs | Domain-based but requires manual maintenance |

**Why FQDN tag is optimal:**
- Windows Update uses many dynamically changing endpoints
- FQDN tags are updated by Microsoft automatically
- Minimal maintenance required

**Exam tip**: "Allow Windows Update with minimal maintenance" → Application rule + **`WindowsUpdate` FQDN tag**.

---

### Question 40
You have an Azure subscription that contains a virtual network named VNet1 in the **East US** region. VNet1 contains Subnet1. You create a route table named RouteTable1 in the **West US** region. You need to associate RouteTable1 with Subnet1. What should you do first?

- A. Move VNet1 to the West US region using Azure Resource Mover
- B. Move RouteTable1 to the East US region using Azure Resource Mover
- C. Delete RouteTable1 and create a new route table in the East US region
- D. Create a VNet peering between East US and West US to enable the association

**Answer: C**
**Explanation:**
A route table can only be associated with subnets in virtual networks that exist in the **same Azure region and subscription**. Azure does not support moving or changing the region of a route table — it must be **deleted and recreated** in the correct region.

| Option | Valid? |
|--------|--------|
| Move VNet1 to West US | ❌ Disruptive; wrong direction |
| Move RouteTable1 to East US | ❌ Route tables cannot be moved between regions |
| **Delete + recreate RouteTable1 in East US** | **✅ Only valid solution** |
| VNet peering | ❌ Unrelated to route table region constraint |

**Exam tip**: Route table and subnet must be in the **same region**. If they're not, delete the route table and create a new one in the correct region.

---

## 5) Monitoring

### Question 41
You have an Azure subscription that contains multiple resources. You create an action group named AG1 that includes email notifications to `admin@contoso.com`. You need to ensure that email notifications from AG1 can be sent to `admin@contoso.com` without exceeding Azure Monitor service limits. What is the maximum number of emails that can be sent to `admin@contoso.com` per hour?

- A. 10
- B. 100
- C. 1,000
- D. Unlimited

**Answer: B**
**Explanation:**
Azure Monitor Action Group email notifications are rate-limited to a maximum of **100 emails per hour per email address**. If this threshold is exceeded, no additional emails are sent until the rate limit period expires. A notification is sent to communicate when rate limiting is applied.

| Notification type | Rate limit |
|------------------|-----------|
| **Email** | **100 per hour per address** |
| SMS | 1 per 5 minutes per number |
| Voice | 1 per 5 minutes per number |

**Exam tip**: Azure Monitor Action Group email limit = **100 per hour** per recipient. Common trap: 1,000 is incorrect.

---

### Question 42
You have an Azure subscription that contains a virtual machine named VM1. You plan to enable VM Insights on VM1 to monitor performance. You need to identify the minimum required components that must be configured before enabling VM Insights. Which two components are required?

- A. Log Analytics workspace and Dependency Agent
- B. **Log Analytics workspace and Azure Monitor Agent**
- C. Azure Diagnostics Extension and Log Analytics workspace
- D. Azure Monitor Agent and Network Watcher

**Answer: B**
**Explanation:**
The minimum required components for VM Insights are:

| Component | Role |
|-----------|------|
| **Log Analytics workspace** | Stores collected performance data and logs |
| **Azure Monitor Agent (AMA)** | Collects data from the VM and sends it to the workspace |

**Note on Dependency Agent:**
The Dependency Agent is required specifically for the **Map feature** (process/network visualization) but is **not required** for the base VM Insights performance monitoring. For minimum enablement, only AMA + workspace are needed.

| Component | Required for VM Insights (basic)? | Required for Map feature? |
|-----------|----------------------------------|--------------------------|
| Log Analytics workspace | ✅ | ✅ |
| Azure Monitor Agent | ✅ | ✅ |
| Dependency Agent | ❌ (optional) | ✅ |

**Exam tip**: VM Insights minimum = **Log Analytics workspace + Azure Monitor Agent**.

---

## 6) Backup and Recovery

### Question 43
You have an Azure virtual machine named VM1 that is replicated to a secondary region using Azure Site Recovery. You perform a failover of VM1 to the secondary region and verify the VM is running correctly. You then commit the failover. What happens to the recovery points after the commit operation?

- A. Recovery points are retained for the configured retention period
- B. Recovery points are moved to the secondary region vault
- C. Recovery points are deleted
- D. Recovery points are archived to cold storage

**Answer: C**
**Explanation:**
After a **commit** operation in Azure Site Recovery, all existing recovery points are **deleted**. Commit finalizes the failover — it confirms that the secondary region VM is now the production VM, making rollback impossible.

**ASR failover lifecycle:**

| Stage | Recovery points | Rollback possible? |
|-------|-----------------|-------------------|
| Failover (before commit) | Retained | ✅ Can failback |
| **Commit** | **Deleted** | **❌ No rollback** |
| After commit | None | ❌ Must Re-protect first |

**After commit**, to replicate back to the primary region, you must perform a **Re-protect** operation (see Question 13).

**Exam tip**: ASR commit → recovery points **deleted**, rollback not possible.

---

## 7) Security

### Question 44
You have an Azure App Service web app named WebApp1 with a system-assigned managed identity enabled. You have an Azure Key Vault named Vault1 that uses the Azure role-based access control permission model. WebApp1 needs to read secrets from Vault1 by using Key Vault references in application settings. Which Azure role should you assign to the managed identity at minimum?

- A. Key Vault Reader
- B. Key Vault Secrets User
- C. Key Vault Contributor
- D. Key Vault Administrator

**Answer: B**
**Explanation:**
For reading secrets from Key Vault using the **RBAC permission model**, the minimum required role is **Key Vault Secrets User**.

| Role | Read secrets? | Write secrets? | Manage Key Vault config? |
|------|--------------|----------------|--------------------------|
| Key Vault Reader | ❌ (metadata only) | ❌ | ❌ |
| **Key Vault Secrets User** | **✅** | **❌** | **❌** |
| Key Vault Secrets Officer | ✅ | ✅ | ❌ |
| Key Vault Contributor | ❌ (control plane) | ❌ (data plane) | ✅ |
| Key Vault Administrator | ✅ | ✅ | ✅ |

**Key Vault reference flow:**
```
App Service (WebApp1)
  → Managed Identity → Azure AD token
  → Key Vault Secrets User role
  → Read secret from Vault1
  → Inject into app settings
```

**Exam tip**: App Service reads Key Vault secrets via managed identity → assign **Key Vault Secrets User** (minimum privilege).

---
