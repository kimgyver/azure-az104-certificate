# AZ-104 Practice Questions — English Set 3

**Coverage**: Topics under-represented in Set 1 and Set 2 — Managed Identities, Key Vault, Entra Connect, Bicep/ARM, AKS, App Service advanced, NAT Gateway, ExpressRoute, VPN P2S, Traffic Manager, Azure Front Door, Private DNS, NSG Flow Logs, Backup advanced, VM Encryption at Host, Storage replication, AzCopy, Azure Policy, Azure Advisor, Monitor Insights, KQL, Diagnostic Settings, Alert processing rules, Azure Migrate.

Total: 40 questions | Pass target: 700/1000

---

## 1) Identity and Governance (Advanced)

### Question 1

A VM needs to access an Azure Key Vault without storing credentials anywhere in the code or configuration. Which feature enables this?

- A. Service SAS
- B. System-assigned managed identity
- C. B2B guest account
- D. Account key

**Answer: B**
**Explanation:**
A **managed identity** is an Entra ID identity automatically managed by Azure — no credentials for the developer to rotate or store.

- **System-assigned**: Tied to the lifetime of a specific resource (VM, App Service, etc.); deleted when the resource is deleted
- **User-assigned**: Created independently; can be shared across multiple resources; persists independently

**Workflow for Key Vault access:**

1. Enable system-assigned identity on the VM
2. In Key Vault → Access policies: add the VM's managed identity with Get/List secret permissions (or use RBAC with Key Vault Secrets User role)
3. App code calls the Azure Instance Metadata Service (IMDS) endpoint to obtain a token — no credentials in code

**Exam trap**: Service principal with client secret also works but requires credential management. Managed identity is the preferred credential-free approach.

---

### Question 2

You created a user-assigned managed identity `app-identity`. You want both `vm-web` and `vm-api` to use the same identity. What is the correct approach?

- A. Create separate system-assigned identities for each VM
- B. Attach `app-identity` to both `vm-web` and `vm-api`
- C. Create a new managed identity per VM for security
- D. Use a service principal with certificate for both VMs

**Answer: B**
**Explanation:**
**User-assigned managed identity** is the correct choice when you need one identity shared across multiple resources. Attach it to each resource independently. This avoids permission drift (both VMs have the same role assignments automatically).

**System-assigned vs User-assigned comparison:**

| Feature   | System-assigned            | User-assigned      |
| --------- | -------------------------- | ------------------ |
| Lifecycle | Tied to resource           | Independent        |
| Sharing   | One resource only          | Multiple resources |
| Deletion  | Auto-deleted with resource | Manual             |
| Use case  | Single resource            | Shared / reusable  |

---

### Question 3

An Entra Connect sync encountered an issue where a user's on-premises Active Directory attribute change did not appear in Azure AD after 30 minutes. The fastest way to force synchronization is:

- A. Restart the Entra Connect server
- B. Run `Start-ADSyncSyncCycle -PolicyType Delta`
- C. Delete and re-create the user in Azure AD
- D. Wait 24 hours for the full sync cycle

**Answer: B**
**Explanation:**
Entra Connect (formerly Azure AD Connect) runs two types of sync cycles:

- **Delta sync**: Synchronizes only changes since the last sync cycle (default interval: 30 minutes). Fast — use this for on-demand updates
- **Full sync**: Re-processes all objects. Slow — only needed after schema changes or connector reconfiguration

Run on the Entra Connect server via PowerShell:

```
Start-ADSyncSyncCycle -PolicyType Delta    # sync recent changes
Start-ADSyncSyncCycle -PolicyType Initial  # full resync
```

**Other Entra Connect modes**: Staging mode (install second server, reads but doesn't write to Azure AD — used for disaster recovery and testing config changes).

---

### Question 4

Your organization uses Entra Connect. The security team wants user passwords from on-prem AD to sync to Azure AD (hash of hash). Which authentication method provides this?

- A. Pass-through Authentication (PTA)
- B. Federation with AD FS
- C. Password Hash Sync (PHS)
- D. Certificate-based authentication

**Answer: C**
**Explanation:**
Entra Connect authentication methods:

| Method                   | Where auth happens | Password in cloud? | Requires on-prem infra? |
| ------------------------ | ------------------ | ------------------ | ----------------------- |
| Password Hash Sync (PHS) | Azure AD           | Yes (hash of hash) | Only Entra Connect      |
| Pass-through Auth (PTA)  | On-prem AD         | No                 | PTA agents on-prem      |
| Federation (AD FS)       | AD FS server       | No                 | AD FS + WAP servers     |

PHS sends a salted, hashed version of the password hash to Azure AD. Microsoft cannot reverse-engineer the original password. Provides cloud resilience if on-prem AD goes down.

PTA sends the authentication request to on-prem agents — requires on-prem connectivity for every login.

---

### Question 5

You need to deploy Azure infrastructure using a template language that is cleaner and more concise than JSON ARM templates, with native support for modules and type checking. What should you use?

- A. PowerShell DSC
- B. Azure Bicep
- C. Azure CLI scripts
- D. Ansible

**Answer: B**
**Explanation:**
**Azure Bicep** is a domain-specific language (DSL) that compiles to ARM JSON templates. It is the Microsoft-recommended approach for IaC on Azure.

Bicep vs ARM JSON:

| Feature       | ARM JSON                            | Bicep             |
| ------------- | ----------------------------------- | ----------------- |
| Syntax        | Verbose JSON                        | Concise, readable |
| Modules       | Linked templates (complex)          | `module` keyword  |
| Type checking | Limited                             | First-class       |
| State file    | None (Azure is the source of truth) | None              |
| Conversion    | `az bicep decompile`                | `az bicep build`  |

To convert an existing ARM template to Bicep:

```
az bicep decompile --file template.json
```

To deploy:

```
az deployment group create --resource-group rg1 --template-file main.bicep
```

---

### Question 6

You want to enforce a policy that automatically adds a `CostCenter` tag to all new resource groups where the tag is missing. Which Azure Policy effect should you use?

- A. Deny
- B. Audit
- C. Modify
- D. DeployIfNotExists

**Answer: C**
**Explanation:**
Azure Policy effects:

| Effect            | What it does                                                                      |
| ----------------- | --------------------------------------------------------------------------------- |
| Deny              | Blocks non-compliant resource creation/update                                     |
| Audit             | Allows resource but marks as non-compliant                                        |
| Modify            | Adds/replaces tags or properties on the resource                                  |
| DeployIfNotExists | Deploys a related resource if it doesn't exist (e.g., enable diagnostic settings) |
| AuditIfNotExists  | Audits if a related resource is missing                                           |
| Append            | Adds fields to the resource without replacing existing values                     |

**Modify** is used specifically for tag operations on existing or newly created resources. After assigning a Modify policy, run a **remediation task** to fix already-existing non-compliant resources.

---

## 2) Storage (Advanced)

### Question 7

You need to copy 10 TB of on-premises data to Azure Blob Storage. The network has limited bandwidth (50 Mbps). Which tool provides the fastest transfer approach while minimizing internet usage?

- A. AzCopy
- B. Azure Data Box
- C. Storage Explorer
- D. Azure File Sync

**Answer: B**
**Explanation:**
At 50 Mbps, uploading 10 TB would take approximately **18+ days** of continuous transfer. **Azure Data Box** ships physical hardware to your location; you load data locally and ship it back to Microsoft's datacenter for import.

**When to use which tool:**

| Tool                          | Best for                                     | Network required?  |
| ----------------------------- | -------------------------------------------- | ------------------ |
| AzCopy                        | Command-line bulk copy, small-to-medium data | Yes                |
| Storage Explorer              | GUI-based file management                    | Yes                |
| Azure Data Box (80 TB device) | Offline transfer of large datasets           | No (offline)       |
| Azure Data Box Disk           | Up to 35 TB (smaller shipments)              | No (offline)       |
| Azure File Sync               | Hybrid file server caching                   | Yes (ongoing sync) |

**AzCopy** tip: Use `azcopy copy 'source' 'destination?SAS-token' --recursive` for blob containers. Supports Server-to-Server copy (no local intermediary).

---

### Question 8

You configure object replication between two Blob Storage accounts. Which conditions must be met?

- A. Both accounts must be in the same region
- B. Blob versioning must be enabled on both source and destination
- C. LRS replication must be selected on both accounts
- D. Accounts must be in the same subscription

**Answer: B**
**Explanation:**
**Azure Blob Object Replication** copies blobs asynchronously from a source container to a destination container (can be cross-region, cross-subscription).

Prerequisites:

- **Blob versioning** must be enabled on both the source AND destination storage account
- **Change feed** must be enabled on the source account
- Accounts can be in different regions and subscriptions

Use cases: geo-redundancy beyond built-in replication, compliance copies in different regions, reduce read latency by serving from a closer copy.

**Object replication vs built-in redundancy:**

- GRS/GZRS: Microsoft-managed, no direct access to secondary unless failover
- Object replication: You control source/destination, filter by prefix/date, accessible directly

---

### Question 9

A developer wants to access Azure Files share using their Entra ID credentials (no storage account key). The files are accessed from an Azure VM joined to Entra ID. What must be configured?

- A. Enable "Allow blob public access"
- B. Assign "Storage File Data SMB Share Contributor" RBAC role to the user
- C. Generate a SAS token with list permissions
- D. Enable blob soft delete

**Answer: B**
**Explanation:**
**Identity-based access for Azure Files** (Kerberos over SMB) eliminates the need to share the storage account key.

Setup steps:

1. Enable Entra ID Kerberos authentication on the storage account (or AD DS integration for on-prem join)
2. Assign the appropriate RBAC role at the file share level:
   - **Storage File Data SMB Share Reader** — read-only
   - **Storage File Data SMB Share Contributor** — read/write
   - **Storage File Data SMB Share Elevated Contributor** — includes ACL modification

**Exam trap**: Azure Blob Storage uses Entra ID authorization through different roles (Storage Blob Data Reader/Contributor). Azure Files uses separate "SMB Share" roles. They are NOT interchangeable.

---

## 3) Compute (Advanced)

### Question 10

You want to protect VM disks from unauthorized access even if Microsoft datacenter staff have physical access. Which feature provides encryption using a key that only YOUR organization controls?

- A. Azure Disk Encryption (ADE) with BitLocker
- B. Encryption at Host with Customer-Managed Keys (CMK)
- C. SSE with Platform-Managed Keys (PMK)
- D. Azure Confidential Computing

**Answer: B**
**Explanation:**
Azure provides multiple layers of disk encryption:

| Option                             | Encryption location                                    | Key control         |
| ---------------------------------- | ------------------------------------------------------ | ------------------- |
| SSE with PMK                       | Storage service (Azure-managed key)                    | Microsoft           |
| SSE with CMK (Disk Encryption Set) | Storage service                                        | You (via Key Vault) |
| ADE (BitLocker/DM-Crypt)           | Guest OS                                               | You (via Key Vault) |
| **Encryption at Host**             | On the host server itself, before data reaches storage | You (PMK or CMK)    |

**Encryption at Host** prevents temporary disk data (OS disk cache, temp disk) from being exposed at the Azure host layer. Must be enabled at the subscription level first:

```
az feature register --name EncryptionAtHost --namespace Microsoft.Compute
```

Then set on the VM: `--encryption-at-host true`

**Disk Encryption Set** is required to link a CMK (Customer-Managed Key in Key Vault) to managed disks.

---

### Question 11

Your AKS cluster is running Kubernetes 1.28. You need to upgrade to 1.30. What is the correct upgrade sequence?

- A. Upgrade control plane directly from 1.28 to 1.30 (skip 1.29)
- B. Upgrade control plane to 1.29 first, then node pools, then to 1.30
- C. Delete and recreate the cluster with version 1.30
- D. Upgrade node pools first, then the control plane

**Answer: B**
**Explanation:**
AKS Kubernetes upgrade rules:

1. **Upgrades are sequential** — you cannot skip a minor version. Must go 1.28 → 1.29 → 1.30
2. **Control plane upgrades first**, then node pools (control plane must always be >= node pool version)
3. AKS supports running node pools one minor version behind the control plane

Upgrade command:

```bash
# Step 1: Upgrade control plane only
az aks upgrade --resource-group rg1 --name aks1 --kubernetes-version 1.29.x --control-plane-only

# Step 2: Upgrade node pools
az aks nodepool upgrade --resource-group rg1 --cluster-name aks1 --name nodepool1 --kubernetes-version 1.29.x
```

**Node pools**: AKS supports multiple node pools with different VM sizes (system pool for system pods, user pools for app workloads). You can add/remove node pools independently.

---

### Question 12

An App Service web app is deployed to the West US region. You want the app to reach a SQL Database in a private VNet in the same region. Which feature routes the app's outbound traffic into the VNet?

- A. Private Endpoint on the App Service
- B. VNet Integration
- C. Access Restrictions
- D. Service Endpoint on the App Service

**Answer: B**
**Explanation:**

- **VNet Integration**: Routes **outbound** traffic from App Service into a VNet. The app can reach resources inside the VNet (e.g., a VM, private database endpoint) using private IPs. Requires a dedicated subnet (delegated to `Microsoft.Web/serverFarms`).
- **Private Endpoint** on App Service: Gives the App Service a private IP inside the VNet — controls **inbound** access (only VNet clients can reach the app). NOT for outbound routing.

**Typical pattern:**

- SQL DB with Private Endpoint in VNet → App Service with VNet Integration → App reaches SQL via private IP
- External users → Private Endpoint on App Service (inbound private) — app is not internet-accessible

**Access Restrictions**: IP allow/deny list for inbound HTTP requests. Does not affect routing.

---

## 4) Virtual Networking (Advanced)

### Question 13

Your organization has multiple Azure VMs that need to initiate outbound connections to the internet without individual public IP addresses. You want a scalable, managed outbound NAT solution. What should you deploy?

- A. Azure Load Balancer (public) with outbound rules
- B. NAT Gateway
- C. Route table to the internet via NVA
- D. Individual public IP on each VM

**Answer: B**
**Explanation:**
**Azure NAT Gateway** provides managed outbound SNAT (Source Network Address Translation) for VMs in a subnet.

Key characteristics:

- Attach one or more public IP addresses or public IP prefixes
- All VMs in the associated subnet share the NAT Gateway's IPs for outbound connections
- Supports up to **64,000 concurrent connections per public IP** (multiple IPs for more scale)
- Fully managed — no SNAT port exhaustion issues common with Azure Load Balancer
- **Does NOT handle inbound** traffic — inbound requires separate public IP or Load Balancer

**Configuration**: Assign NAT Gateway to a subnet in VNet settings. All VMs in that subnet automatically use it.

**Exam trap**: Azure Load Balancer with outbound rules also provides outbound SNAT, but NAT Gateway is preferred for dedicated outbound scenarios (simpler, more scalable, no inbound/outbound port conflict).

---

### Question 14

Your company has an ExpressRoute circuit connecting on-premises to Azure. The networking team asks what type of peering provides access to Azure PaaS services (like Azure Storage, SQL Database) without going through the public internet.

- A. Private peering
- B. Microsoft peering
- C. Public peering (deprecated)
- D. VNet peering

**Answer: B**
**Explanation:**
ExpressRoute peering types:

| Peering type          | What it connects to                      | Still available? |
| --------------------- | ---------------------------------------- | ---------------- |
| **Private peering**   | Azure VNets via virtual network gateway  | Yes              |
| **Microsoft peering** | Azure PaaS services (Storage, SQL, M365) | Yes              |
| Public peering        | Azure public IPs                         | Deprecated       |

- **Private peering**: On-prem connects to Azure VNets — for IaaS (VMs, internal services)
- **Microsoft peering**: On-prem connects to Azure PaaS services and Microsoft 365 via private circuit, bypassing the public internet

**ExpressRoute vs VPN:**

| Feature    | ExpressRoute              | VPN Gateway         |
| ---------- | ------------------------- | ------------------- |
| Connection | Dedicated private circuit | IPSec over internet |
| Bandwidth  | Up to 100 Gbps            | Up to ~10 Gbps      |
| SLA        | 99.95%                    | 99.9%               |
| Setup time | Weeks (ISP provisioning)  | Minutes             |

---

### Question 15

Users working from home need to connect to Azure VMs using their corporate credentials (Azure AD / Entra ID). No on-premises VPN device is available. Which Azure VPN Gateway configuration is appropriate?

- A. Site-to-Site VPN
- B. ExpressRoute
- C. Point-to-Site (P2S) VPN with Azure AD authentication
- D. VNet peering

**Answer: C**
**Explanation:**
**Point-to-Site (P2S) VPN** creates an individual encrypted tunnel from a user's device to the Azure VNet — no on-premises VPN device needed.

Authentication options for P2S:

- **Azure certificate**: Self-signed or CA-issued certificates
- **RADIUS server**: Authenticate against on-prem RADIUS/NPS
- **Azure AD (Entra ID)**: Users authenticate with their corporate Entra credentials — ideal for this scenario

Protocol options: OpenVPN (supports Azure AD auth), IKEv2, SSTP

**Site-to-Site (S2S) VPN**: Connects an entire on-premises network to Azure using a VPN device. Not suitable for individual user connections.

**Setup steps for P2S with Azure AD:**

1. Create VPN Gateway (Generation 2, VpnGw1 or higher)
2. Configure P2S with OpenVPN protocol + Azure AD tenant ID
3. Download VPN client configuration file
4. Users install Azure VPN Client and authenticate with Entra credentials

---

### Question 16

You need to configure Azure DNS to automatically resolve VM hostnames within a VNet without manually creating DNS records. What should you enable?

- A. Azure Public DNS zone
- B. Azure Private DNS zone with auto-registration
- C. Custom DNS server with forwarding
- D. Azure DNS alias record

**Answer: B**
**Explanation:**
**Azure Private DNS zones** provide name resolution within VNets. When you link a VNet to a private DNS zone with **auto-registration enabled**, Azure automatically creates DNS A records for each VM deployed in that VNet.

Setup:

1. Create Private DNS zone (e.g., `internal.contoso.com`)
2. Link VNet to the zone with registration enabled
3. VMs automatically get records like `vm1.internal.contoso.com → 10.0.0.4`

Key facts:

- Auto-registration only for VNets with the registration link (max 1 VNet per zone for auto-registration)
- Multiple VNets can be **linked** for resolution only (no auto-registration)
- VMs use Azure-provided DNS (168.63.129.16) to query private zones

**Azure Public DNS zones**: Used for internet-facing domain resolution — not for private VNet resolution.

---

### Question 17

You want to route traffic from Azure to on-premises with a custom BGP community for identifying specific traffic classes over ExpressRoute. You also want Azure VMs to route specific traffic to a firewall NVA before leaving the VNet. What Azure feature enables this?

- A. NSG outbound rules
- B. User-Defined Routes (UDR) in a route table
- C. Azure Firewall policy
- D. Application Security Groups

**Answer: B**
**Explanation:**
**User-Defined Routes (UDR)** override Azure's default system routes, allowing you to force traffic through a Network Virtual Appliance (NVA), Azure Firewall, or VPN Gateway.

Common UDR scenarios:

- Force internet-bound traffic from a subnet through Azure Firewall (0.0.0.0/0 → Azure Firewall private IP)
- Route spoke VNet traffic through a hub NVA in a hub-spoke topology
- Override default routes learned from BGP

UDR configuration:

1. Create a route table
2. Add routes (address prefix → next hop type: Virtual Appliance, VNet Gateway, Internet, None)
3. Associate the route table with a subnet

**Exam trap**: NSG rules allow/deny traffic but do NOT route traffic to different next hops. Only UDR controls routing.

---

### Question 18

You need to capture all inbound and outbound traffic flows at the NSG level for security analysis and compliance. Which feature provides this flow data?

- A. Azure Monitor activity log
- B. NSG Flow Logs
- C. Azure Firewall diagnostics
- D. VM boot diagnostics

**Answer: B**
**Explanation:**
**NSG Flow Logs** capture information about IP traffic flowing through NSGs:

- 5-tuple information: source IP, source port, destination IP, destination port, protocol
- Flow direction and allow/deny decision
- Stored as JSON in an Azure Storage account

**Traffic Analytics** (built on NSG Flow Logs):

- Requires Log Analytics workspace + Storage account
- Provides visualization of traffic patterns, top talkers, threat intelligence, geo-maps
- Aggregates flow data every 10 or 60 minutes

Enable flow logs: NSG → Monitoring → Flow logs → Enable → Select storage account + retention period.

**Version 2 flow logs** include flow-state (new/existing/ended) and byte/packet counts.

---

## 5) Load Balancing and Global Traffic

### Question 19

You have a web application deployed in three Azure regions. You need users to always be directed to the closest region based on network latency. Which Azure service provides this?

- A. Azure Load Balancer
- B. Azure Application Gateway
- C. Traffic Manager with Performance routing
- D. Azure Front Door

**Answer: C**
**Explanation:**
**Azure Traffic Manager** is a DNS-based global traffic distribution service. Routing methods:

| Method          | How it works                                               |
| --------------- | ---------------------------------------------------------- |
| **Priority**    | Send all traffic to primary; failover to secondary         |
| **Weighted**    | Distribute traffic by assigned weights (e.g., 70/30 split) |
| **Performance** | Route to the endpoint with lowest latency for the user     |
| **Geographic**  | Route based on user's geographic location                  |
| **Multivalue**  | Return multiple healthy endpoints (UDP/TCP apps)           |
| **Subnet**      | Route based on user's source IP subnet                     |

**Performance routing** uses the Traffic Manager latency table to determine which endpoint is closest to the user's DNS resolver.

**Exam trap**: Traffic Manager works at DNS level (layer 7) but does NOT inspect HTTP headers or paths. For HTTP-level load balancing with routing rules, use Application Gateway or Azure Front Door.

---

### Question 20

You want to deploy a global HTTP/S load balancer that provides Web Application Firewall (WAF), SSL offload, URL-path-based routing, and instant failover between Azure regions. Which service meets all these requirements?

- A. Traffic Manager
- B. Azure Load Balancer (Standard, Global)
- C. Azure Front Door
- D. Application Gateway

**Answer: C**
**Explanation:**
**Azure Front Door** is a global, anycast-based HTTP/S load balancer with:

- **WAF** (Web Application Firewall) — OWASP rulesets, bot protection
- **SSL/TLS offload** at the edge
- **URL path-based routing** to different backends
- **Caching** of static content at PoPs
- **Health probes** with instant failover (milliseconds, not DNS TTL-based)
- **Custom domain + managed certificates**

**Front Door vs Traffic Manager:**

| Feature        | Azure Front Door | Traffic Manager |
| -------------- | ---------------- | --------------- |
| Protocol       | HTTP/S only      | Any protocol    |
| Load balancing | Layer 7 (HTTP)   | DNS-based       |
| WAF            | Yes              | No              |
| Caching        | Yes              | No              |
| Failover speed | Near-instant     | DNS TTL (60s+)  |

**Application Gateway**: Regional HTTP/S load balancer — operates within one region. Use Front Door for global multi-region.

---

## 6) Monitoring and Maintenance

### Question 21

Your team wants to query Azure Monitor Logs to find the top 10 VMs by CPU usage over the last hour. Which query language should you use?

- A. SQL
- B. KQL (Kusto Query Language)
- C. SPARQL
- D. Gremlin

**Answer: B**
**Explanation:**
Azure Monitor Logs (Log Analytics) uses **KQL (Kusto Query Language)**. It is optimized for time-series telemetry and log analytics.

Common KQL patterns:

```kql
// Top 10 VMs by average CPU over last 1 hour
Perf
| where TimeGenerated > ago(1h)
| where CounterName == "% Processor Time"
| summarize AvgCPU = avg(CounterValue) by Computer
| top 10 by AvgCPU desc

// Count failed login attempts by user
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by Account
| order by FailedAttempts desc
```

Key KQL operators: `where`, `summarize`, `project`, `extend`, `join`, `render`, `top`, `limit`, `order by`, `ago()`, `bin()`.

---

### Question 22

You need to send VM diagnostic logs to three destinations simultaneously: long-term archive, real-time streaming for SIEM, and query/analysis. What is the correct Azure configuration?

- A. Create three separate VMs with different monitoring configurations
- B. Configure a Diagnostic Setting with three destinations
- C. Enable only Log Analytics workspace; other destinations require separate settings
- D. Use Azure Backup for log archiving

**Answer: B**
**Explanation:**
**Diagnostic Settings** can be configured per resource to route logs/metrics to up to **four destinations** simultaneously:

| Destination                 | Use case                                       |
| --------------------------- | ---------------------------------------------- |
| **Log Analytics workspace** | Interactive query (KQL), dashboards, alerts    |
| **Storage Account**         | Long-term archive, compliance retention        |
| **Event Hub**               | Real-time streaming to SIEM (Splunk, Sentinel) |
| **Partner solution**        | Third-party monitoring integration             |

Configure via: Resource → Monitoring → Diagnostic settings → Add diagnostic setting → Select categories → Select destinations.

**Exam trap**: You can send to all three (Storage + Event Hub + Log Analytics) in a **single** diagnostic setting — you do NOT need multiple settings.

Resources with diagnostic settings: VMs (guest OS metrics/logs), Storage accounts (operations), Key Vault (audit events), NSGs (flow logs use separate config), etc.

---

### Question 23

Azure Monitor generates hundreds of alerts daily during a scheduled 4-hour maintenance window. You want to suppress these alerts (prevent notifications) only during the maintenance window without deleting the alert rules. What should you configure?

- A. Delete alert rules before maintenance and recreate after
- B. Disable the alert rules manually each time
- C. Create an Alert Processing Rule with the Suppress Notifications action
- D. Reduce alert severity to Informational

**Answer: C**
**Explanation:**
**Alert Processing Rules** (formerly Action Rules) allow you to manage what happens when alerts fire — without modifying the underlying alert rules.

Actions available:

- **Suppress notifications**: No notifications sent for matched alerts during specified window
- **Apply action group**: Add extra notifications to matching alerts

Configuration:

- Scope: Specific resources, resource groups, subscriptions
- Filter: By alert severity, alert rule name, resource type, etc.
- Schedule: One-time or recurring (e.g., every Sunday 22:00–02:00)

Portal path: Azure Monitor → Alerts → Alert processing rules → Create.

**Exam distinction**: Alert processing rules affect what happens after an alert fires (notification suppression). Alert rules define WHEN an alert fires (threshold conditions). These are separate objects.

---

### Question 24

You want to analyze VM performance trends (CPU, memory, disk) through a pre-built workbook with visual charts, without writing custom KQL queries. Which Azure Monitor feature provides this out-of-the-box?

- A. Azure Advisor
- B. VM Insights
- C. Application Insights
- D. Network Watcher Connection Monitor

**Answer: B**
**Explanation:**
**Azure Monitor VM Insights** provides pre-built dashboards for monitoring VM and VM Scale Set performance and dependencies:

- **Performance tab**: CPU, memory, disk, network — historical charts, percentile views
- **Map tab**: Shows live dependencies (processes, connections, external endpoints) using Dependency Agent
- **Get Started tab**: Lists unmonitored VMs for easy onboarding

**Prerequisites**: Log Analytics workspace + Azure Monitor Agent (or legacy MMA + Dependency Agent for Map feature).

**Other Monitor Insights:**

| Insight            | Target                                                  |
| ------------------ | ------------------------------------------------------- |
| VM Insights        | Virtual Machines and VMSS                               |
| Container Insights | AKS and Arc-enabled clusters                            |
| Network Insights   | VNets, Load Balancers, Application Gateway, Connections |
| Storage Insights   | Azure Storage accounts                                  |

**Azure Advisor**: Provides recommendations (cost, security, reliability, performance, operational excellence) but does NOT show performance metrics or charts. Different purpose.

---

### Question 25

The Azure Advisor shows a recommendation to "Right-size or shutdown underutilized virtual machines." Which Advisor category does this recommendation fall under?

- A. Security
- B. Reliability
- C. Cost
- D. Operational Excellence

**Answer: C**
**Explanation:**
**Azure Advisor** analyzes your Azure usage and provides personalized recommendations across five categories:

| Category                   | Examples                                                                   |
| -------------------------- | -------------------------------------------------------------------------- |
| **Cost**                   | Right-size VMs, delete unattached disks, reserved instance recommendations |
| **Security**               | Enable MFA, encrypt disks, Security Center recommendations                 |
| **Reliability**            | Enable backup, zone redundancy, availability sets                          |
| **Performance**            | Improve SQL DTU, upgrade VM size for performance                           |
| **Operational Excellence** | Follow deployment best practices, service health subscriptions             |

Advisor uses up to 7 days of usage data for cost recommendations. Recommendations can be:

- Accepted and implemented
- Postponed
- Dismissed

Access: Azure portal → Advisor, or programmatically via Azure Advisor REST API.

---

## 7) Backup and Recovery (Advanced)

### Question 26

You need to back up individual files and folders from an on-premises Windows Server to Azure without setting up a Recovery Services Vault agent on Azure VMs. Which tool provides this?

- A. Azure Backup Server (MABS)
- B. Microsoft Azure Recovery Services (MARS) agent
- C. Azure Site Recovery
- D. Azure File Sync

**Answer: B**
**Explanation:**
**MARS (Microsoft Azure Recovery Services) agent** is a lightweight agent installed directly on Windows servers (on-premises or Azure VMs) to back up:

- Files and folders
- System state / bare metal recovery
- Application data (with additional agents)

Backup targets: Recovery Services Vault only.

**Backup solutions comparison:**

| Tool                        | What it backs up                   | Where installed          |
| --------------------------- | ---------------------------------- | ------------------------ |
| MARS agent                  | Files/folders/system state         | Windows server OS        |
| Azure Backup (VM extension) | Full VM (disk snapshots)           | Azure VM platform        |
| MABS (Azure Backup Server)  | Applications (SQL, Exchange, etc.) | On-prem dedicated server |
| DPM                         | Enterprise workloads               | Dedicated DPM server     |

MARS backup frequency: Up to 3 times per day. Retention: daily (up to 180 days), weekly, monthly, yearly policies.

---

### Question 27

You have both Azure VMs and Azure file shares to protect. You also need immutable backup storage (Trusted Launch VM support). Which vault type should you use?

- A. Recovery Services Vault only
- B. Backup Vault only
- C. Recovery Services Vault for VMs; Backup Vault for file shares
- D. Backup Vault for both

**Answer: B**
**Explanation:**
**Backup Vault** is the newer vault type that supports:

- Azure Blobs
- Azure Disks (Managed Disks backup)
- **Azure Database for PostgreSQL**
- **Trusted Launch VMs** (newer Azure VMs with vTPM and Secure Boot)
- Immutable vault support (prevents deleting backup data for a specified period)

**Recovery Services Vault** supports:

- Azure VMs (classic and standard)
- Azure Files (file shares)
- SQL Server in Azure VMs
- SAP HANA in Azure VMs
- On-prem servers via MARS/MABS
- Azure Site Recovery

**Exam note (2026 scope)**: Questions may ask you to distinguish which vault type supports which workloads. When in doubt: Recovery Services Vault = VMs + files + on-prem; Backup Vault = newer services (Blobs, Disks, PostgreSQL, Trusted Launch VMs).

---

## 8) Azure Migrate

### Question 28

Your company wants to migrate 200 on-premises VMware VMs to Azure. The first step is to understand the total cost and compatibility before committing. What tool should you use?

- A. Azure Site Recovery (ASR)
- B. Azure Migrate: Discovery and Assessment
- C. Azure Database Migration Service
- D. Azure Data Box

**Answer: B**
**Explanation:**
**Azure Migrate** is the central hub for Azure migration projects. The workflow has two main phases:

**Phase 1 — Discovery and Assessment:**

1. Create Azure Migrate project
2. Deploy **Azure Migrate Appliance** (lightweight VM) on-premises
3. Appliance discovers VMware VMs, Hyper-V VMs, or physical servers
4. Assessment generates:
   - **Azure VM assessment**: Recommended VM sizes, estimated monthly cost, migration readiness
   - **AVS assessment**: For migrating to Azure VMware Solution
   - **SQL assessment**: For SQL Server migration

**Phase 2 — Migration:**

- Use **Azure Migrate: Server Migration** tool (powered by Azure Site Recovery for agent-based migration)
- Agentless migration: Directly replicate VMware VMs without installing agents on each VM
- Agent-based migration: Install Mobility service agent for more complex scenarios

**Azure Site Recovery (ASR)**: Used for ongoing disaster recovery replication (not initial migration assessment). Can be used for the actual migration step, but Azure Migrate is the recommended end-to-end hub.

---

### Question 29

After running Azure Migrate assessment, some VMs are marked "Conditionally ready." What does this status mean?

- A. The VM cannot be migrated to Azure
- B. The VM can be migrated but requires attention to specific issues (e.g., OS support, boot type)
- C. The VM is already running in Azure
- D. The VM has failed the performance assessment

**Answer: B**
**Explanation:**
Azure Migrate assessment readiness statuses:

| Status                  | Meaning                                              |
| ----------------------- | ---------------------------------------------------- |
| **Ready**               | VM can be migrated as-is                             |
| **Conditionally ready** | Can migrate, but specific issues need attention      |
| **Not ready**           | Cannot migrate with current settings; major blockers |
| **Readiness unknown**   | Assessment lacks sufficient data                     |

Common "Conditionally ready" reasons:

- **Boot type**: BIOS boot detected (Generation 1 VM required); UEFI may need conversion
- **OS support**: Operating system is supported but may need updates
- **Disk size**: Individual disk exceeds Azure limits (32 TB)
- **NIC count**: VM has more NICs than Azure target size supports

These are warnings, not blockers — you can proceed with migration but should address the issues.

---

## 9) Key Vault

### Question 30

You want to ensure that even if someone with Owner access deletes a Key Vault, the vault and its secrets remain recoverable for 90 days. Which two Key Vault features must be enabled?

- A. Soft delete and Purge Protection
- B. Soft delete and Access policies
- C. Private Endpoint and RBAC
- D. Firewall rules and Trusted Services

**Answer: A**
**Explanation:**
**Soft delete**: When enabled, deleted Key Vaults and their objects (keys, secrets, certificates) are retained for a configurable retention period (7–90 days) in a "deleted" state. They can be recovered during this period.

**Purge Protection**: When enabled, prevents **permanent deletion** (purging) of soft-deleted items during the retention period — even by users with Owner or Key Vault Contributor roles. After the retention period expires, items are automatically purged.

**Without purge protection**: An Owner could delete the vault AND purge it immediately, losing all data.
**With soft delete + purge protection**: Delete → enters soft-deleted state → cannot be purged for 90 days → recoverable.

**Note**: As of February 2025, soft delete is enabled by default on all new Key Vaults and cannot be disabled.

---

### Question 31

A web application uses Key Vault to retrieve a connection string. The app is deployed as an Azure App Service. Which access method is most secure and avoids credential management?

- A. Store the Key Vault access key in app settings
- B. Use a system-assigned managed identity for the App Service and grant it Key Vault Secrets User RBAC role
- C. Use a shared access signature to access Key Vault
- D. Embed the Key Vault client ID and secret in code

**Answer: B**
**Explanation:**
Key Vault RBAC roles for data plane access:

| Role                          | Permissions                           |
| ----------------------------- | ------------------------------------- |
| **Key Vault Secrets User**    | Read secret values                    |
| **Key Vault Secrets Officer** | Create/update/delete secrets          |
| **Key Vault Reader**          | View metadata only (no secret values) |
| **Key Vault Administrator**   | Full control                          |

**Best practice workflow:**

1. Enable system-assigned managed identity on the App Service
2. Assign `Key Vault Secrets User` role to the managed identity on the Key Vault
3. In app code, use `DefaultAzureCredential` (Azure SDK) — automatically uses the managed identity token
4. In App Service Configuration, use Key Vault references: `@Microsoft.KeyVault(VaultName=myvault;SecretName=mySecret)` — secret retrieved at runtime

**Key Vault access models:**

- **Access policies** (legacy): Vault-level; grants permissions per principal
- **Azure RBAC** (recommended): Standard Azure role assignments; supports inheritance from resource group

---

## 10) Azure Policy (Advanced)

### Question 32

After assigning a "DeployIfNotExists" policy to enable diagnostic settings on all Storage accounts, existing Storage accounts are still non-compliant. What must you do to bring them into compliance?

- A. Delete and recreate the storage accounts
- B. Create a remediation task for the policy assignment
- C. Assign the policy again with a different scope
- D. Wait 24 hours for automatic remediation

**Answer: B**
**Explanation:**
**DeployIfNotExists (DINE)** and **Modify** policies only apply automatically to **new or updated** resources created after policy assignment. For **existing non-compliant resources**, you must trigger a **remediation task**.

Remediation task:

1. Azure Policy → Remediation → Create remediation task
2. Select the policy assignment
3. Choose scope (subscription, resource group)
4. Azure triggers the DINE action (deploys diagnostic settings) on all non-compliant resources

**Required permissions for remediation**: The policy assignment's managed identity must have contributor rights to the resources being remediated. This is configured during assignment.

**Audit vs Deny vs DINE:**

- Audit: Marks non-compliant, does nothing
- Deny: Prevents creation of non-compliant resources
- DINE: Deploys a related resource if missing (requires remediation for existing)

---

### Question 33

You want to group multiple related Azure Policy definitions together and assign them as a single unit to enforce a set of security standards. What Azure Policy construct should you use?

- A. Policy Set (Initiative)
- B. Management group
- C. Policy effect
- D. Compliance report

**Answer: A**
**Explanation:**
A **Policy Initiative** (also called a Policy Set Definition) groups multiple policy definitions and assigns them together.

Example use case: "CIS Microsoft Azure Foundations Benchmark" initiative — contains 100+ policies for security compliance. Assign the initiative once instead of assigning 100+ individual policies.

Key concepts:

- **Policy definition**: Single rule with one effect
- **Initiative definition**: Collection of policy definitions
- **Assignment**: Apply a definition or initiative to a scope (management group, subscription, resource group)
- **Compliance report**: Shows compliant/non-compliant resources per policy

**Built-in initiatives** include: Azure Security Benchmark, HIPAA, ISO 27001, CIS Benchmark, PCI DSS.

**Management groups**: Organize subscriptions hierarchically for governance — but they are the SCOPE for policy assignments, not a grouping mechanism for policies themselves.

---

## 11) Networking (Additional)

### Question 34

You have a hub VNet with Azure Firewall and two spoke VNets (Spoke-A and Spoke-B) peered to the hub. You want all internet-bound traffic from both spokes to go through Azure Firewall. What must you configure in each spoke VNet's subnets?

- A. NSG outbound deny rule
- B. UDR with default route 0.0.0.0/0 pointing to Azure Firewall private IP
- C. Service Endpoint for Azure Firewall
- D. ExpressRoute connection from each spoke

**Answer: B**
**Explanation:**
In a **hub-spoke topology** with Azure Firewall as the central security control:

1. Azure Firewall is deployed in a subnet named `AzureFirewallSubnet` in the hub VNet
2. To force spoke subnets to use the Firewall for all internet traffic:
   - Create a route table with route: `0.0.0.0/0` → Next hop: Virtual Appliance → Azure Firewall private IP
   - Associate this route table with each spoke subnet

**Without UDR**: VNet peering does not automatically route traffic through the hub's Firewall — traffic goes directly to the internet.

**Azure Firewall specifics:**

- Requires a dedicated `AzureFirewallSubnet` (/26 or larger)
- Has its own public IP for SNAT of outbound traffic
- Supports FQDN filtering, threat intelligence, network rules, application rules
- Premium SKU adds TLS inspection and IDPS

---

### Question 35

You have a VPN Gateway in a VNet. The `GatewaySubnet` already exists. A colleague wants to add an NSG to the GatewaySubnet to restrict traffic. What is the expected outcome?

- A. The NSG works exactly the same as on any other subnet
- B. The NSG will block all VPN traffic because NSGs are not supported on GatewaySubnet
- C. NSGs on GatewaySubnet are not recommended and may break gateway functionality
- D. NSGs must be added to GatewaySubnet for security compliance

**Answer: C**
**Explanation:**
Microsoft does **not recommend** adding NSGs to the `GatewaySubnet`. The subnet is used by the VPN/ExpressRoute Gateway which needs to communicate with Azure's infrastructure for:

- BGP peer connectivity
- IKE handshake for VPN tunnels
- Health probe traffic

An NSG with overly restrictive rules can break these internal communications. If you must add an NSG, you need to carefully allow all required gateway management ports.

**Exam rule**: `GatewaySubnet` — no NSG (or very careful NSG that allows all gateway management traffic). The question is testing awareness of this constraint.

**AzureFirewallSubnet**: Similarly, NSGs are not supported on the dedicated Azure Firewall subnet.

---

## 12) Mixed Topics

### Question 36

You want to monitor the health of Azure services and receive notifications when Azure incidents might affect your subscriptions. Which Azure service provides this?

- A. Azure Monitor Alert rules
- B. Azure Service Health
- C. Azure Resource Health
- D. Azure Status page (azure.status.microsoft.com)

**Answer: B**
**Explanation:**
Azure health services comparison:

| Service                   | What it monitors                                                                                      | Scope               |
| ------------------------- | ----------------------------------------------------------------------------------------------------- | ------------------- |
| **Azure Service Health**  | Azure incidents, planned maintenance, health advisories affecting YOUR subscriptions/regions/services | Personalized        |
| **Azure Resource Health** | Health of a specific resource (is THIS VM running?)                                                   | Single resource     |
| **Azure Status page**     | Broad global Azure outages — public, not personalized                                                 | All Azure customers |
| **Azure Monitor Alerts**  | Custom metric/log threshold violations                                                                | Your resources      |

**Azure Service Health** allows you to create **Service Health Alerts** (powered by Activity Log alerts) to receive notifications for: Service issues, Planned maintenance, Health advisories, Security advisories.

---

### Question 37

A storage account has a shared access policy named `readpolicy` associated with a stored access policy. The stored access policy sets expiration to 2 hours. If you need to immediately revoke access for all SAS tokens referencing this policy, what is the fastest action?

- A. Rotate the storage account key
- B. Delete or modify the stored access policy
- C. Change the storage account firewall rules
- D. Delete the entire storage account

**Answer: B**
**Explanation:**
**Stored access policies** are a feature of Service SAS that allows you to define permissions and expiry on the server side. All SAS tokens that reference a stored access policy can be centrally revoked by:

- **Deleting the stored access policy** — immediately invalidates all SAS tokens that reference it
- **Modifying the policy** (e.g., setting expiry to a past date) — effectively revokes access

This is a key advantage over ad-hoc SAS tokens (without stored access policy): ad-hoc tokens cannot be revoked before their expiry — the only option is to **rotate the storage account key**, which invalidates ALL SAS tokens on the account.

**Comparison:**

| SAS type                      | Revocation method                                     |
| ----------------------------- | ----------------------------------------------------- |
| Ad-hoc SAS (no stored policy) | Rotate account key (affects ALL tokens)               |
| SAS with stored access policy | Delete/modify the stored policy (surgical revocation) |
| User Delegation SAS           | Revoke the Entra credential / token                   |

---

### Question 38

You need to implement a governance structure where multiple Azure subscriptions share the same Azure Policy and RBAC assignments, and where budgets can be managed hierarchically. Which Azure feature enables this?

- A. Resource groups
- B. Management groups
- C. Azure Blueprints
- D. Subscriptions linked by VNet peering

**Answer: B**
**Explanation:**
**Management Groups** provide a hierarchical governance structure above subscriptions:

```
Root Management Group
  ├── Corp Management Group
  │   ├── Prod Subscription
  │   └── Dev Subscription
  └── External Management Group
      └── Partner Subscription
```

Key features:

- Policy and RBAC assignments applied at management group level inherit to all child subscriptions
- Up to 6 levels of hierarchy (excluding root and subscription levels)
- Root management group contains all subscriptions
- Global Administrator can elevate to manage all subscriptions via root management group

**Use case**: Apply "Require HTTPS" policy to all production subscriptions by assigning to the Corp Management Group instead of each subscription.

---

### Question 39

Your VM in Azure is not reachable on port 80. You check the NSG and see a rule allowing TCP 80 inbound. However, there is also an Application Security Group (ASG) involved. Troubleshooting reveals the VM's NIC is not in the ASG referenced by the allow rule. What is the most likely cause?

- A. NSGs cannot use ASGs
- B. The ASG rule allows traffic only to NICs assigned to that ASG; the VM's NIC must be added
- C. ASGs override NSGs
- D. The VM requires a public IP to receive traffic

**Answer: B**
**Explanation:**
**Application Security Groups (ASG)** allow you to group VM NICs logically and reference the group in NSG rules — instead of individual IP addresses.

NSG rule with ASG:

- Destination: `asg-webservers`
- Protocol: TCP, Port: 80
- Action: Allow

This rule **only applies to VM NICs that are members of `asg-webservers`**. If a VM's NIC is not assigned to this ASG, the rule does not match, and the default NSG deny rule applies.

**Fix**: In the VM's NIC settings → Application Security Groups → Add → select `asg-webservers`.

**ASG benefits:**

- No need to update NSG rules when IP addresses change
- Logical grouping: "all web servers," "all database servers"
- Works within a VNet (cannot span VNets)

---

### Question 40

You are reviewing Azure costs and want automated recommendations to purchase Reserved Instances for VM workloads that run 24/7. Which Azure service provides these specific recommendations?

- A. Azure Cost Management + Billing
- B. Azure Advisor (Cost category)
- C. Azure Policy
- D. Microsoft Defender for Cloud

**Answer: B**
**Explanation:**
**Azure Advisor** provides reservation purchase recommendations in the Cost category based on your VM usage over the past 7, 30, or 60 days. Recommendations include:

- Which VM families and sizes to reserve
- Estimated annual savings
- 1-year vs 3-year commitment comparison

**Azure Cost Management + Billing**: Provides cost analysis, budgets, cost alerts, and export — but the recommendation engine is Advisor.

**Reserved Instances vs Savings Plans:**

- Reserved Instances: Commit to specific VM size + region + OS — up to 72% savings
- Azure Savings Plans: Flexible commitment by spend amount — applies to any VM size/region — up to 65% savings

Both require commitment (1 or 3 years) and are billed upfront, partial upfront, or monthly.

**Advisor access**: Azure portal → Advisor → Cost → Reservations recommendations. Or programmatically via Azure Advisor REST API / Azure CLI: `az advisor recommendation list --category Cost`.

---

_End of Set 3 — 40 Questions_

**Topic coverage summary:**

- Q1-Q2: Managed Identities (system-assigned vs user-assigned)
- Q3-Q4: Entra Connect (delta sync, PHS vs PTA)
- Q5: Bicep/ARM templates
- Q6: Azure Policy (Modify effect)
- Q7-Q9: Storage advanced (Data Box, object replication, Azure Files identity-based access)
- Q10: VM Encryption at Host + CMK
- Q11: AKS upgrade sequence
- Q12: App Service VNet Integration vs Private Endpoint
- Q13: NAT Gateway
- Q14: ExpressRoute peering types
- Q15: VPN Point-to-Site with Azure AD
- Q16: Private DNS auto-registration
- Q17: UDR (User-Defined Routes)
- Q18: NSG Flow Logs + Traffic Analytics
- Q19: Traffic Manager routing methods
- Q20: Azure Front Door vs Application Gateway
- Q21: KQL basics
- Q22: Diagnostic Settings (3 destinations)
- Q23: Alert Processing Rules (suppression)
- Q24: VM Insights
- Q25: Azure Advisor categories
- Q26: MARS agent (on-prem backup)
- Q27: Recovery Services Vault vs Backup Vault
- Q28-Q29: Azure Migrate workflow and readiness statuses
- Q30-Q31: Key Vault (soft delete + purge protection, managed identity access)
- Q32-Q33: Azure Policy advanced (remediation tasks, initiatives)
- Q34: Hub-spoke UDR with Azure Firewall
- Q35: GatewaySubnet NSG constraints
- Q36: Azure Service Health vs Resource Health
- Q37: Stored access policy revocation
- Q38: Management Groups
- Q39: ASG membership and NSG rules
- Q40: Azure Advisor reservation recommendations
