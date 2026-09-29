# AZ-104 Practice Questions — English Set 4
**Coverage**: Questions derived from real exam chat review — NSG stateful behavior, Load Balancer HA Ports, ARM/Bicep validation and deployment modes, Metric Alerts, Storage firewall (VNet rules, Service Endpoint Policy), Application Gateway session affinity, Azure Firewall FQDN Tags, Azure File Sync cloud tiering, VM Extensions, Azure Container Instances, App Service tiers, Azure SQL PITR, SSPR, ARM What-if, Azure Backup consistency types, Blob archive rehydration, Diagnostic Settings category groups.

Total: 20 questions | Pass target: 700/1000

---

## 1) Networking

### Question 1
You have an Azure virtual machine that runs a web application on port 80. Users report that they can access the web application but cannot receive responses. You verify that an NSG inbound rule allows traffic to port 80. You need to determine if you must create an outbound rule to allow response traffic. What should you conclude?

- A. Create an outbound NSG rule on port 80 to allow return traffic
- B. No outbound rule is required because NSGs are stateful
- C. Create an outbound NSG rule on port 443 to allow encrypted responses
- D. Configure a UDR to allow return traffic through the virtual network

**Answer: B**
**Explanation:**
Azure Network Security Groups are **stateful**: once inbound traffic is allowed over a port, the corresponding outbound return traffic is automatically permitted. The NSG tracks connection state using flow records, so established connections are allowed without a matching outbound rule.

If users can access the application but receive no response, the likely causes are:
- Application-level bug or misconfiguration (web server not sending responses)
- OS-level firewall (iptables, Windows Firewall)
- Load balancer health probe failure
- NIC-level network issue

**Key rule**: NSG outbound rule is NOT required for response traffic on an already-allowed inbound port.

---

### Question 2
You plan to deploy an internal Azure Standard Load Balancer to provide high availability for network virtual appliances (NVAs). The load balancer must load balance all TCP and UDP traffic on all ports to the NVAs. You need to configure the appropriate load balancing rule. Which values should you use for the Protocol and Port settings?

- A. Protocol: TCP, Port: 443
- B. Protocol: UDP, Port: 0
- C. Protocol: All, Port: 0
- D. Protocol: TCP, Port: Any

**Answer: C**
**Explanation:**
For NVAs (firewalls, IDS/IPS, proxies), all traffic must be forwarded without port-level filtering. This requires an **HA Ports rule** on an Azure Standard Load Balancer:

| Setting | Value | Reason |
|---------|-------|--------|
| Protocol | All | Handles both TCP and UDP |
| Port | 0 | Represents all ports (wildcard) |

This combination creates an **HA Ports rule**, which is specifically designed for NVA scenarios where every packet — regardless of protocol or port — must be inspected by the appliance.

**Contrast with a typical web LB rule**: Protocol TCP, Port 80/443 — only handles specific web traffic.

---

### Question 3
You are deploying Azure virtual machines using an ARM template with Azure CLI. The deployment uses the `az deployment group create` command. You need to validate the template syntax before deploying to ensure no resources are created during validation. Which command should you run?

- A. `az deployment group create --what-if`
- B. `az deployment group validate`
- C. `az deployment group preview`
- D. `az template validate`

**Answer: B**
**Explanation:**
| Command | Purpose | Creates resources? |
|---------|---------|-------------------|
| `az deployment group create` | Actual deployment | Yes |
| `az deployment group validate` | Syntax + schema + parameter validation | **No** |
| `az deployment group what-if` | Preview resource changes | No |

**`az deployment group validate`** checks:
- JSON syntax correctness
- Required parameter presence
- ARM template schema compliance
- Basic deployment validation

It does **not** deploy any resources. Use it as a pre-flight check before `create`.

**`what-if`** also makes no changes but goes further — it shows exactly what resources would be created, modified, or deleted.

---

### Question 4
You are creating a metric alert rule for an Azure virtual machine. You add two conditions to the alert rule: Percentage CPU greater than 90 percent AND Available Memory Bytes less than 1 GB. When does the alert fire?

- A. When either condition has been true at any point within the evaluation period
- B. When both conditions have been true at any point within the evaluation period, even at different times
- C. When both conditions are simultaneously true during the same evaluation period
- D. When the average of both conditions exceeds their respective thresholds

**Answer: C**
**Explanation:**
Azure Metric Alerts with **AND** logic fire only when **all conditions are simultaneously true** during the same evaluation data point — not when they are each true at different points within the window.

**Example:**
| Time | CPU > 90%? | Memory < 1GB? | Alert fires? |
|------|-----------|--------------|-------------|
| 10:01 | ✅ Yes | ❌ No | ❌ No |
| 10:04 | ❌ No | ✅ Yes | ❌ No |
| 10:07 | ✅ Yes | ✅ Yes | ✅ Yes |

**To achieve "true at any point within the window" behavior**, use aggregations like **Max** for CPU (e.g., Max CPU > 90% over 5 minutes) and **Min** for memory (Min Memory < 1GB over 5 minutes). This way, if the threshold was breached at any point during the window, the aggregated value reflects it.

---

### Question 5
You configure an Azure storage account with a storage firewall to restrict access. You add the public IP address of an on-premises server to the IP network rules. Users in an Azure virtual machine in the East US region report they cannot access the storage account even though their virtual network is in the same region. You need to enable the virtual machine to access the storage account. What should you do?

- A. Add the VM's public IP address to the storage account IP network rules
- B. Enable the storage account for public access
- C. Enable a service endpoint for Microsoft.Storage on the VM's subnet and add a virtual network rule to the storage account
- D. Create a peering connection between the storage account and the virtual network

**Answer: C**
**Explanation:**
Azure VMs communicate with Storage through Azure's internal network, **not via a predictable public IP**. Adding an IP network rule does not reliably cover VM traffic.

The correct approach:
1. **Enable a service endpoint** (`Microsoft.Storage`) on the VM's subnet — this routes traffic through the Azure backbone instead of the public internet
2. **Add a VNet rule** on the storage account that allows the specific subnet

| Rule type | Controls | Suitable for Azure VMs? |
|-----------|---------|------------------------|
| IP network rule | Public IP addresses | ❌ (VM has no fixed public IP) |
| VNet rule | Subnet identity | ✅ |

**Key insight**: The storage firewall evaluates VNet rules based on subnet identity (not IP), so VMs inside the allowed subnet are granted access automatically once the service endpoint is active.

---

### Question 6
You are developing a Bicep file to deploy a storage account. You need to conditionally deploy the storage account only when a parameter named `deployStorage` is set to `true`. Which syntax should you use in the resource declaration?

- A. `resource storageAccount '...' = condition (deployStorage) { ... }`
- B. `resource storageAccount '...' = if (deployStorage) { ... }`
- C. `resource storageAccount '...' [if deployStorage] { ... }`
- D. `resource storageAccount '...' = when (deployStorage) { ... }`

**Answer: B**
**Explanation:**
Bicep uses `= if (condition)` to conditionally declare a resource:

```bicep
param deployStorage bool

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) {
  name: 'mystorageaccount'
  location: resourceGroup().location
  sku: { name: 'Standard_LRS' }
  kind: 'StorageV2'
}
```

**Bicep vs ARM JSON conditional syntax:**

| | ARM JSON | Bicep |
|-|----------|-------|
| Syntax | `"condition": "[parameters('deployStorage')]"` | `= if (deployStorage)` |
| Style | JSON property inside resource block | Inline after `=` |
| Readability | Verbose | Concise |

When `deployStorage` is `false`, the resource block is skipped entirely — no error, no deployment.

---

### Question 7
You have an Azure Application Gateway with multiple backend servers in a backend pool. You need to ensure that subsequent requests from the same user session are directed to the same backend server. The solution must use Application Gateway features. What should you configure?

- A. Source IP affinity on the load balancing rule
- B. Cookie-based session affinity on the HTTP settings
- C. URL path-based routing rules
- D. Host-based routing rules

**Answer: B**
**Explanation:**
**Cookie-based session affinity** (also called sticky sessions) ensures that requests from the same client are always routed to the same backend server. Application Gateway sets a cookie (`ARRAffinity`) on the first response; subsequent requests include this cookie, allowing the gateway to identify and route to the same backend.

**Why not source IP affinity?**
- Source IP affinity is an Azure Load Balancer (L4) concept
- Application Gateway operates at L7 and uses cookies for stickiness
- Source IPs can change (NAT, proxy, mobile networks)

| Feature | Layer | Stickiness mechanism |
|---------|-------|---------------------|
| Application Gateway | L7 | Cookie (ARRAffinity) |
| Azure Load Balancer | L4 | Source IP hash |

---

### Question 8
You have an Azure Firewall policy with application rules configured. You need to allow outbound HTTP/S traffic to specific fully qualified domain names (FQDNs) for Azure services such as Windows Update. You want to simplify the rule configuration and ensure the rules automatically update when Microsoft changes the required endpoints. What should you use in your application rules?

- A. Service Tags
- B. A manually maintained list of FQDNs
- C. FQDN Tags
- D. IP Groups

**Answer: C**
**Explanation:**
**FQDN Tags** are Microsoft-managed collections of FQDNs associated with well-known Azure services. When you use an FQDN Tag in an Application Rule, Azure Firewall automatically resolves and keeps the underlying endpoint list current — no manual updates required.

| Option | What it contains | Auto-updates? | Used in |
|--------|-----------------|--------------|---------|
| FQDN Tags | Pre-defined FQDN groups (e.g., `WindowsUpdate`) | ✅ Yes | Application rules |
| Service Tags | IP address ranges | ✅ Yes | NSG / Network rules |
| IP Groups | Custom IP address collections | ❌ No | Network rules |

**Exam keyword**: "automatically update when Microsoft changes endpoints" → **FQDN Tags**

Available FQDN Tags include: `WindowsUpdate`, `AzureCloud`, `MicrosoftIntune`, `AzureKubernetesService`, and others.

---

### Question 9
You are planning an Azure File Sync deployment that includes cloud tiering. You need to configure cloud tiering policies to ensure that files not accessed within 30 days are tiered to Azure Files, while maintaining at least 25 percent free space on the local volume. Which two policies should you configure? (Choose two.)

- A. Cache size policy set to 75 percent
- B. Date policy set to 30 days
- C. Volume free space policy set to 25 percent
- D. Tiering threshold policy set to 30 days idle
- E. Bandwidth throttle policy

**Answer: B, C**
**Explanation:**
Azure File Sync cloud tiering has two independent policies:

| Policy | Controls | Setting |
|--------|---------|---------|
| **Date policy** | Time-based tiering: files not accessed within N days are eligible for tiering | 30 days |
| **Volume free space policy** | Keeps a minimum percentage of free space on the local volume by aggressively tiering older files | 25% |

These two policies operate **independently**:
- **Date policy** defines *when* a file becomes eligible for tiering
- **Volume free space policy** defines *how aggressively* tiering occurs based on available disk space

Both policies are required to satisfy the two requirements in the question.

**Common trap**: The two policies are not mutually exclusive — both can (and should) be active simultaneously.

---

### Question 10
You are deploying an Azure virtual machine using an ARM template. The template includes the Custom Script Extension to configure the VM after deployment. The VM is deployed in a virtual network that has an NSG with restrictive outbound rules. The Custom Script Extension fails to run. You confirm the VM Agent is installed and running. Which IP address must be accessible from the VM for extensions to function correctly?

- A. 10.0.0.1
- B. 169.254.169.254
- C. 168.63.129.16
- D. 100.64.0.0

**Answer: C**
**Explanation:**
`168.63.129.16` is Azure's **special virtual public IP** used for platform-level communication:

| Purpose | Detail |
|---------|--------|
| VM Agent communication | Extensions receive commands from the Azure platform |
| Extension download and execution | Scripts/packages are fetched via this IP |
| DNS resolution | Internal Azure DNS queries |
| Health probe | Azure Load Balancer health checks |

When an NSG has restrictive outbound rules, traffic to `168.63.129.16` may be blocked, preventing the VM Agent from communicating with the Azure platform. This causes extension provisioning to fail even if the VM Agent itself is running.

**Fix**: Add an outbound NSG rule to allow traffic to `168.63.129.16` on port 80/443, or use the `AzurePlatformDNS` / `AzurePlatformIMDS` service tag.

**Note**: `169.254.169.254` is the Instance Metadata Service (IMDS) endpoint — used for metadata and managed identity tokens, but not for extensions.

---

### Question 11
You plan to deploy a container group that contains multiple containers to Azure Container Instances (ACI). One container will run a web application, and a second container will serve as a logging sidecar. Which operating system type must you use for the container group?

- A. Windows
- B. Linux
- C. Either Windows or Linux
- D. Nano Server

**Answer: B**
**Explanation:**
Azure Container Instances supports multi-container groups (sidecar pattern) **only on Linux**:

| OS | Multi-container group | Sidecar pattern |
|----|-----------------------|----------------|
| Linux | ✅ Full support | ✅ Supported |
| Windows | ❌ Limited / not supported | ❌ Not recommended |

**Why Linux only?**
- ACI was designed with Linux-first architecture
- Windows container isolation uses a different hypervisor model with higher overhead
- The sidecar pattern (logging, proxy, monitoring alongside the main container) is a Linux-native paradigm

**Multi-container group structure (Linux):**
```
Container Group (Linux)
 ├── Web Container (port 80)
 └── Logging Sidecar Container
```

Containers in the same group share network namespace (localhost) and storage volumes.

---

## 2) Storage (Advanced)

### Question 12
You have an Azure subscription that contains a virtual network with a subnet that has a service endpoint for Microsoft.Storage enabled. You need to restrict the subnet to only access two specific storage accounts and prevent data exfiltration to other storage accounts. What should you configure?

- A. VNet rules on both storage accounts
- B. Private endpoints for both storage accounts
- C. Service endpoint policies on the subnet
- D. Storage account firewall IP rules

**Answer: C**
**Explanation:**
A service endpoint alone allows a subnet to reach **all** Azure Storage accounts in the region — including accounts in other tenants, which creates a data exfiltration risk.

**Layer comparison:**

| Layer | Mechanism | Direction | Controls what? |
|-------|-----------|-----------|----------------|
| Service Endpoint | Fast path to Azure Storage | Subnet → Azure Storage | Routing |
| VNet rule (storage side) | Allows specific subnets in | Storage → Subnet | Ingress filter |
| **Service Endpoint Policy** | Filters egress to specific accounts | **Subnet → Storage accounts** | Egress filter ⭐ |

**Service Endpoint Policy** is applied on the **subnet** and specifies which storage accounts the subnet is allowed to reach. Traffic to any storage account not listed in the policy is blocked.

```
Subnet (Service Endpoint Policy applied)
  ✅ → Storage Account A (in policy)
  ✅ → Storage Account B (in policy)
  ❌ → Storage Account C (not in policy) — blocked
```

**Exam trigger phrase**: "prevent data exfiltration to other storage accounts" → **Service Endpoint Policy**

---

### Question 13
You have an Azure App Service web app running on a Free (F1) App Service plan. You need to configure a custom domain and secure it with a TLS/SSL certificate. You want to minimize costs while meeting the requirements. Which App Service plan tier should you upgrade to?

- A. Shared (D1)
- B. Basic (B1)
- C. Standard (S1)
- D. Premium (P1V3)

**Answer: B**
**Explanation:**
| Tier | Custom Domain | SSL/TLS Binding | Cost |
|------|--------------|----------------|------|
| Free (F1) | ❌ | ❌ | $0 |
| Shared (D1) | ✅ | ❌ | Low |
| **Basic (B1)** | ✅ | ✅ | **Lowest with SSL** |
| Standard (S1) | ✅ | ✅ + SNI/IP SSL | Higher |
| Premium | ✅ | ✅ + advanced | Highest |

**Basic (B1)** is the **minimum tier** that supports both custom domains AND SSL certificate bindings. It satisfies both requirements at the lowest possible cost.

Standard and above add features like autoscaling, deployment slots, and Traffic Manager integration — unnecessary for this scenario.

---

### Question 14
You have an Azure SQL Database in the vCore purchasing model using the General Purpose service tier. A user accidentally deletes critical data from a table. You need to restore the database to a point in time 10 minutes before the deletion occurred. The solution must minimize administrative effort. What should you do?

- A. Perform a geo-restore from the most recent backup
- B. Use point-in-time restore (PITR) to restore the database
- C. Export a BACPAC file and import it to a new database
- D. Fail over to a secondary replica using a failover group

**Answer: B**
**Explanation:**
**Point-in-Time Restore (PITR)** is the correct tool when you need to recover data to a specific past timestamp with minimal effort.

Azure SQL Database automatically maintains:
- Full backups (weekly)
- Differential backups (every 12–24 hours)
- Transaction log backups (every 5–12 minutes)

This allows restoration to any point within the retention period (7–35 days depending on tier).

**How PITR works:**
1. PITR creates a **new database** from the backup chain (does not overwrite the original)
2. You specify the exact timestamp (e.g., 10 minutes ago)
3. No manual backup management required

| Method | Use case | Effort |
|--------|---------|--------|
| PITR | Specific timestamp recovery | **Minimal** ✅ |
| Geo-restore | Region disaster recovery | Higher |
| BACPAC export/import | Manual archive/migration | High |
| Failover group | HA/DR failover | Not for data recovery |

---

### Question 15
You have an ARM template that deploys a virtual machine. The template uses a linked template stored in an Azure storage account to deploy a network interface. You need to configure the deployment mode for the linked template. Which deployment mode can be used for the linked template?

- A. Complete only
- B. Incremental only
- C. Both Complete and Incremental
- D. No deployment mode is required for linked templates

**Answer: B**
**Explanation:**
ARM linked templates (nested deployments that reference an external template file) support **only Incremental deployment mode**.

| Mode | Behavior | Linked template support |
|------|---------|------------------------|
| **Incremental** | Adds/updates resources defined in template; leaves others untouched | ✅ Supported |
| Complete | Deletes resources NOT in the template | ❌ **Not supported** for linked templates |

**Why Complete mode is excluded:**
Complete mode evaluates the entire resource group scope. In a linked template, only a subset of resources is declared. Allowing Complete mode would risk deleting resources managed by the parent template or other linked templates.

**Deployment structure:**
```
Main template (Incremental) → deploys VM
  └── Linked template (Incremental only) → deploys NIC
```

---

## 3) Identity and Governance

### Question 16
You have a Microsoft Entra tenant. You configure self-service password reset (SSPR) with the following settings:
- Number of methods required to reset: **2**
- Methods available to users: Mobile app notification, Mobile app code, Email, Mobile phone

A user named User1 registers only **one** authentication method for SSPR. User1 forgets their password and attempts to use SSPR to reset it. What happens when User1 tries to reset their password?

- A. The password reset succeeds using the one registered method
- B. User1 is prompted to register an additional method before resetting
- C. The password reset fails because User1 has not registered enough methods
- D. An administrator receives a notification to manually reset the password

**Answer: C**
**Explanation:**
SSPR enforces the configured **minimum number of required methods**. If a user has not registered enough methods, the reset is denied.

| Config | Value |
|--------|-------|
| Methods required | 2 |
| User1 registered | 1 |
| Result | ❌ Cannot reset password |

**Key distinction**: "Methods available to users" defines the pool of allowed methods. "Number of methods required" is enforced per-user at reset time based on what the user has **actually registered**.

**Security rationale**: Requiring multiple methods prevents account takeover — an attacker who compromises one channel (e.g., email) cannot reset the password alone.

**Resolution**: An administrator must either manually reset User1's password or User1 must register a second method (which itself requires admin intervention or a separate authentication step).

---

## 4) ARM / Bicep / Deployment

### Question 17
You have an ARM template that deploys Azure virtual machines. Before deploying, you need to preview the changes the template will make to your environment and validate that no unexpected modifications will occur. The solution must not create or modify any resources. What should you use?

- A. `az deployment group validate`
- B. `az deployment group create --mode Complete`
- C. `az deployment group what-if`
- D. ARM template test toolkit

**Answer: C**
**Explanation:**
The **What-if operation** previews all changes an ARM template deployment would make — without applying any of them.

Sample output:
```
+ create  Microsoft.Compute/virtualMachines/vm1
~ modify  Microsoft.Network/networkInterfaces/nic1
- delete  Microsoft.Storage/storageAccounts/old-storage
```

| Command | Validates syntax? | Shows resource changes? | Creates resources? |
|---------|-----------------|------------------------|-------------------|
| `validate` | ✅ | ❌ | ❌ |
| `what-if` | ✅ | ✅ | ❌ |
| `create` | ✅ | (after deploy) | ✅ |

**When to use each:**
- `validate` → catch syntax errors before any planning
- `what-if` → verify impact of deployment on existing environment
- `create` → execute the actual deployment

---

## 5) Backup and Recovery

### Question 18
You have an Azure virtual machine that is protected by Azure Backup using an Enhanced backup policy. The consistency type is configured as **Crash-consistent only**. Which statement accurately describes this backup configuration?

- A. The backup guarantees application-level consistency including in-flight database transactions
- B. The backup captures OS, memory, and application state simultaneously
- C. The backup captures a VM disk snapshot without guaranteeing application or memory consistency
- D. The backup uses VSS to quiesce applications before taking the snapshot

**Answer: C**
**Explanation:**
Azure Backup supports three snapshot consistency types:

| Type | What it captures | App/DB safe? | Speed |
|------|-----------------|-------------|-------|
| **Crash-consistent** | Disk state only (no memory/transactions) | ❌ | Fastest |
| File-consistent | Disk + OS file system flush | Partial | Medium |
| **Application-consistent** | Disk + OS + app quiesce via VSS | ✅ | Slowest |

**Crash-consistent** is analogous to the state after an abrupt power loss:
- Disk data is captured as-is
- In-memory data and in-flight transactions are **not** included
- Databases may need recovery on restore (transaction log replay)
- Fastest and lowest overhead

**Application-consistent** uses VSS (Windows Volume Shadow Copy Service) or pre/post scripts (Linux) to pause application writes, flush transactions to disk, and then take the snapshot — providing a clean recovery point for databases.

---

### Question 19
You have an Azure Storage account that contains blobs in the archive tier. You need to rehydrate several blobs for an urgent business requirement. The blobs are less than 10 GB in size and must be available as quickly as possible. Which rehydration priority should you select?

- A. Standard priority
- B. High priority
- C. Immediate priority
- D. Express priority

**Answer: B**
**Explanation:**
Azure Blob Storage archive tier rehydration options:

| Priority | Typical time | Cost | When to use |
|----------|-------------|------|-------------|
| **Standard** | Up to 15+ hours | Lower | Non-urgent restores |
| **High** | < 1 hour for blobs < 10 GB | Higher | Urgent restores ✅ |

**High priority** rehydration processes the request ahead of the standard queue. For blobs smaller than 10 GB, high priority typically completes within **under 1 hour**.

The question specifies:
- "urgent business requirement" → High priority
- "as quickly as possible" → High priority
- "less than 10 GB" → qualifies for fast high-priority completion

**Process**: Set the tier from Archive to Hot or Cool (not both simultaneously) and specify priority:
```
az storage blob set-tier --account-name ... --container-name ... --name blob.dat \
  --tier Hot --rehydrate-priority High
```

---

## 6) Monitoring

### Question 20
You have an Azure subscription that contains several storage accounts. You need to configure diagnostic settings to send resource logs from the storage accounts to a Log Analytics workspace. You want to use category groups to simplify log collection. Which two category groups are available when configuring diagnostic settings? (Choose two.)

- A. metrics
- B. allLogs
- C. audit
- D. diagnosticLogs
- E. operationalLogs

**Answer: B, C**
**Explanation:**
Azure diagnostic settings provide **category groups** as a simplified way to collect predefined sets of log categories without selecting each one individually.

The two standard category groups available across most Azure resources (including Storage accounts) are:

| Category Group | What it includes |
|---------------|-----------------|
| **allLogs** | All supported resource log categories for that resource type |
| **audit** | Security and access-related log categories (authentication, authorization, access events) |

**Why the others are wrong:**
- `metrics` — Metrics are a separate dimension (not a log category group)
- `diagnosticLogs` — Not a recognized Azure category group name
- `operationalLogs` — Not a standard Azure category group name

**Usage example** (Azure Portal): When adding a diagnostic setting, under "Logs" you see checkboxes for individual categories OR category groups (allLogs, audit). Selecting `allLogs` automatically includes all current and future log categories for that resource.

---
