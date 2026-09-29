# AZ-104 Practice Questions (Set 10) — English

## 1) Identity, RBAC, and Microsoft Graph

### Q1
An Azure subscription currently has **3,500** role assignments. An administrator assigns the **Virtual Machine Contributor** role to a security group that contains 100 users. How many total role assignments does the subscription have after this action?

- A. 3,600 (one new assignment per user in the group)
- B. 3,501 (one new assignment created for the group principal)
- C. 3,500 (no new assignment; group membership is tracked separately)
- D. 3,601 (one per user plus one for the group itself)

**Answer: B**

**Explanation:** When an RBAC role is assigned to a security group, a single role assignment object is created for the group—regardless of how many members it contains. Azure supports up to 4,000 role assignments per subscription, so assigning to groups rather than individuals is a best practice for minimising assignment consumption.

---

### Q2
You are creating a custom RBAC role that allows a managed identity to **read blob data** from a storage account but must not be able to perform any management-plane operations on the account. Which property in the role definition should contain the blob-read permission?

- A. Actions
- B. NotActions
- C. DataActions
- D. NotDataActions

**Answer: C**

**Explanation:** `Actions` and `NotActions` govern management-plane (ARM) operations. Accessing blob content is a data-plane operation and must be placed in `DataActions`. For example: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`.

---

### Q3
You are writing a PowerShell script using the Microsoft Graph PowerShell SDK. The script must **create and update** Microsoft Entra ID user accounts. Which permission scope should be requested with `Connect-MgGraph` to follow the principle of least privilege?

- A. User.Read
- B. User.Read.All
- C. User.ReadWrite.All
- D. Directory.ReadWrite.All

**Answer: C**

**Explanation:** `User.ReadWrite.All` grants read and write access to all user objects. `User.Read` and `User.Read.All` are read-only. `Directory.ReadWrite.All` grants broad tenant-wide write access, which exceeds what is needed for user management.

---

### Q4
A user must be able to **manage all resources** in an Azure subscription (create, update, delete VMs, storage accounts, networks, etc.) but must **not** be able to assign roles to other principals. Which built-in role satisfies both requirements?

- A. Owner
- B. Contributor
- C. User Access Administrator
- D. Reader

**Answer: B**

**Explanation:** `Contributor` grants `Actions: *` but includes `NotActions` that explicitly exclude `Microsoft.Authorization/*/Write` and `Microsoft.Authorization/*/Delete`, preventing role assignments. `Owner` includes role assignment rights.

---

### Q5
A third-party application needs **time-limited, read-only** access to a blob container. The solution must not expose the storage account key and must follow the principle of least privilege. Which SAS type provides the highest level of security?

- A. Account SAS (signed with account key)
- B. Service SAS (signed with account key)
- C. User delegation SAS (signed with Entra ID credentials)
- D. Service SAS with stored access policy

**Answer: C**

**Explanation:** A user delegation SAS is signed with Microsoft Entra ID credentials, not the storage account key. This eliminates key exposure risk and is Microsoft's recommended approach for maximum blob access security.

---

### Q6
WebApp1 uses a **system-assigned managed identity** to access key vault **KV1** via Key Vault references in application settings. KV1 uses the **Azure RBAC permission model**. Following the principle of least privilege, which role should be assigned to WebApp1's managed identity on KV1?

- A. Key Vault Contributor
- B. Key Vault Reader
- C. Key Vault Secrets User
- D. Key Vault Administrator

**Answer: C**

**Explanation:** `Key Vault Secrets User` grants permission to read secret values—exactly what Key Vault references require. `Key Vault Reader` allows reading metadata only, not secret values. `Contributor` and `Administrator` grant management capabilities beyond what is needed.

---

### Q7
An administrator applies a **CanNotDelete** lock to resource group **RG1**, which contains 10 virtual machines. What effect does this lock have on the VMs?

- A. The lock applies only to the resource group container; the VMs are unaffected
- B. The VMs inherit the CanNotDelete lock and cannot be deleted individually
- C. The VMs can be deleted but the resource group itself cannot
- D. The lock must be applied separately to each VM to take effect

**Answer: B**

**Explanation:** Azure resource locks are **inherited hierarchically**. A lock applied to a resource group automatically propagates to all resources within that group. Each VM in RG1 cannot be deleted while the RG-level lock is active.

---

### Q8
An organization enables **Combined Security Information Registration** together with SSPR (requiring 2 authentication methods). A new user signs in for the first time. Which statement correctly describes what the user experiences?

- A. The user must complete separate registration wizards for MFA and SSPR
- B. The user registers authentication methods once, and those methods apply to both MFA and SSPR
- C. Only MFA registration is required at first sign-in
- D. The user is directed to the legacy MFA registration page at aka.ms/mfasetup

**Answer: B**

**Explanation:** Combined registration consolidates the experience. Users register methods once in a unified flow, and those methods become available for both MFA challenges and SSPR. The previous requirement to register separately for each feature no longer applies.

---

### Q9
User1 needs to execute **KQL queries** against a Log Analytics workspace to investigate events. User1 must **not** be able to modify workspace settings, view access keys, or change data collection rules. Which role satisfies this requirement with least privilege?

- A. Reader (subscription scope)
- B. Log Analytics Contributor
- C. Log Analytics Reader
- D. Monitoring Reader

**Answer: C**

**Explanation:** `Log Analytics Reader` allows viewing and querying log data without any write permissions. Subscription-level `Reader` does not include the permissions required to query log analytics data. `Monitoring Reader` does not grant log query access.

---

## 2) Governance

### Q10
What is the **maximum number of nested management group levels** supported in Azure, **excluding** the root management group and subscriptions?

- A. 4
- B. 5
- C. 6
- D. 10

**Answer: C**

**Explanation:** Azure supports a maximum of **6 levels** of management group nesting. The hierarchy is: Root → Level 1 → Level 2 → Level 3 → Level 4 → Level 5 → Level 6 → Subscriptions. The root and subscriptions are not counted in the depth.

---

### Q11
An organisation wants all **newly created Azure subscriptions** to be automatically placed in the **Onboarding** management group rather than the root. What should be configured?

- A. An Azure Policy at root management group scope
- B. The **Default management group** setting in Management Group settings
- C. An RBAC assignment at the Onboarding management group
- D. An Azure Blueprint applied at the root

**Answer: B**

**Explanation:** The **Default management group** setting (Management Groups → Settings) controls where newly created subscriptions are automatically placed. Changing it to the Onboarding management group ensures all new subscriptions land there instead of at the root.

---

### Q12
An organisation uses **Microsoft Entra ID Free**. An administrator wants to create a security group with automatic membership based on the `user.department` attribute. What must be done first?

- A. Create a Microsoft 365 group with dynamic user membership rules
- B. Use a scheduled PowerShell script to manage group membership
- C. Upgrade the tenant to Microsoft Entra ID Premium P1
- D. Enable B2B collaboration in the tenant settings

**Answer: C**

**Explanation:** Dynamic membership groups require **Microsoft Entra ID Premium P1** (or P2). The Free tier does not support dynamic group membership rules. An upgrade is required before the group can be created.

---

## 3) Storage, SAS, and Lifecycle

### Q13
An Azure file share is protected with **vault-tier backup** (vaulted backup). A ransomware attack requires restoring the file share. Which restore operation is supported from vault-tier recovery points?

- A. File-level recovery to the original file share
- B. Full share recovery to the original location (original location restore)
- C. Full share recovery to an **alternate** storage account only
- D. Incremental recovery to any location

**Answer: C**

**Explanation:** Vaulted backup for Azure Files currently supports only **full share recovery to an alternate location**. Original location restore (OLR) and file-level recovery are not supported from vault-tier recovery points.

---

### Q14
A storage account for Azure Files currently has **Microsoft Entra Domain Services** authentication enabled. An administrator wants to switch to **on-premises Active Directory Domain Services** authentication. What must be done before enabling AD DS?

- A. Enable both methods simultaneously, then disable the old one
- B. **Disable** the Microsoft Entra Domain Services authentication on the storage account first
- C. Create a new storage account and configure AD DS from scratch
- D. Add the on-premises domain controllers as DNS servers in the VNet

**Answer: B**

**Explanation:** Azure Files supports **only one identity-based authentication method per storage account** at a time. The current method (Entra Domain Services) must be disabled before enabling a new one (on-premises AD DS).

---

### Q15
An Azure Files share has been successfully backed up by Azure Backup for several months. Backups suddenly begin failing. The file share and storage account both exist and are accessible. What is the most likely cause?

- A. The Recovery Services vault has been soft-deleted
- B. The maximum number of snapshots per file share (**200**) has been reached
- C. The Azure Backup agent on the file server has expired
- D. The storage account's access keys were rotated after backup was configured

**Answer: B**

**Explanation:** Azure Backup for Azure Files uses share snapshots internally. Each file share supports a maximum of **200 snapshots**. When this limit is reached, new backup jobs cannot create additional snapshots and therefore fail.

---

### Q16
A lifecycle management rule is configured with the following actions on base blobs: **tierToCool after 30 days**, **tierToArchive after 30 days**, and **delete after 30 days** since last modification. All three conditions evaluate to `true` for the same blob simultaneously. Which action does Azure apply?

- A. Tier to cool (first action in the list)
- B. Tier to archive (coldest non-deleted tier)
- C. Delete (least expensive storage operation)
- D. All three actions are applied in sequence

**Answer: C**

**Explanation:** When multiple lifecycle actions match the same blob simultaneously, Azure selects the **least expensive action**. The cost ranking is: Delete < Archive < Cool. Therefore the delete action takes precedence over the tier transitions.

---

### Q17
You want to configure a lifecycle policy that moves blobs to the cool tier after **30 days without access** and automatically returns them to the hot tier when accessed. What must be enabled on the storage account **before** creating this policy?

- A. Blob versioning
- B. Soft delete for blobs
- C. **Blob last access time tracking**
- D. Immutable storage policies

**Answer: C**

**Explanation:** The `daysAfterLastAccessTimeGreaterThan` condition and the `enableAutoTierToHotFromCool` property both require **last access time tracking** to be enabled first. Without it, Azure has no `LastAccessTime` property on blobs to evaluate.

---

### Q18
You plan to create a lifecycle management policy that moves block blobs from the **Cool tier** to the **Cold tier**. To avoid early deletion fees, what is the **minimum number of days** blobs must remain in the Cool tier before being moved?

- A. 7 days
- B. 14 days
- C. 30 days
- D. 90 days

**Answer: C**

**Explanation:** The Cool tier has a minimum retention period of **30 days**. Moving or deleting a blob before 30 days results in a prorated early deletion charge for the remaining days in that tier.

---

### Q19
A storage account has **blob soft delete** enabled with a **30-day retention period**. Blob versioning is NOT enabled. A user accidentally deleted `file1.txt` five days ago. How should `file1.txt` be recovered?

- A. Restore a previous blob version from the version history
- B. Call the **Undelete Blob** operation on the soft-deleted blob
- C. Restore the blob from a blob snapshot
- D. Contact Azure support—soft-deleted blobs without versioning cannot be recovered

**Answer: B**

**Explanation:** With only soft delete enabled (no versioning), deleted blobs enter a soft-deleted state and can be recovered using the **Undelete Blob** operation within the retention period. Versioning is not required; the undelete operation restores the blob to its state before deletion.

---

### Q20
**RG1** contains a storage account and a virtual network. An ARM template defining **only a virtual machine** is deployed to RG1 using **Complete** deployment mode. What happens to the existing resources in RG1?

- A. Only the VM is created; the storage account and virtual network are preserved
- B. The storage account and virtual network are deleted; only the VM remains
- C. The deployment fails because the template does not reference the existing resources
- D. Complete mode is not allowed when unrelated resources already exist in the group

**Answer: B**

**Explanation:** ARM **Complete mode** synchronises the resource group to match the template exactly. Any resources in the resource group that are **not defined in the template are deleted**. Because the storage account and virtual network are not in the VM-only template, they are removed.

---

### Q21
A Windows Server has three volumes: **C: (system drive, NTFS)**, **D: (NTFS data drive)**, and **E: (ReFS data drive)**. Azure File Sync with cloud tiering enabled is being configured. Which volume(s) are eligible to host server endpoints with cloud tiering?

- A. C:, D:, and E:
- B. D: and E: (any non-system volume)
- C. D: only (NTFS, non-system volume)
- D. C: and D: (NTFS volumes only)

**Answer: C**

**Explanation:** Cloud tiering requires **NTFS** volumes—ReFS is not supported. Additionally, **system volumes (such as C:)** cannot host server endpoints with cloud tiering, even if NTFS. Volume D: is the only eligible candidate.

---

### Q22
Three server endpoints share the same NTFS volume. Their volume free space policies are: **SE1 = 15%**, **SE2 = 20%**, **SE3 = 30%**. What is the effective volume free space policy that Azure File Sync applies to the volume?

- A. 15% (lowest — most aggressive tiering)
- B. 21.7% (average of all three)
- C. 20% (median)
- D. 30% (highest — most conservative)

**Answer: D**

**Explanation:** When multiple server endpoints share a volume, Azure File Sync applies the **most restrictive (highest) free space percentage**. This ensures the strictest policy is enforced across all endpoints to avoid the volume running low on space.

---

## 4) Networking

### Q23
An **Azure Container Instances** container group is deployed inside a virtual network subnet. The containers need outbound access to the internet. There is no public IP or load balancer on the subnet. What should be configured?

- A. Assign a public IP address directly to the container group
- B. Deploy an Azure Standard Load Balancer with outbound rules
- C. Attach a **NAT Gateway** to the subnet
- D. Deploy an Azure Application Gateway in front of the container group

**Answer: C**

**Explanation:** For VNet-integrated ACI container groups, outbound internet access requires a **NAT Gateway** on the subnet. NAT Gateway provides SNAT for outbound flows. Direct public IP assignment to ACI in a VNet is not supported for general outbound internet access.

---

### Q24
A VM's NIC has **NSG-NIC** attached to it. The VM's subnet has **NSG-Subnet** attached. Internet traffic arrives on port 443 targeting the VM. In what order are the NSGs evaluated for this **inbound** traffic?

- A. NSG-NIC first, then NSG-Subnet
- B. NSG-Subnet first, then NSG-NIC
- C. Only NSG-Subnet is evaluated for inbound traffic
- D. Rules across both NSGs are merged and evaluated together by priority

**Answer: B**

**Explanation:** For **inbound** traffic, Azure evaluates the **subnet NSG first**, then the **NIC NSG**. Both NSGs must permit the traffic for it to reach the VM. This evaluation order is fixed and cannot be changed.

---

### Q25
The same VM from the previous scenario initiates an **outbound** connection to the internet on port 80. NSG-Subnet is on the subnet and NSG-NIC is on the NIC. In what order are the NSGs evaluated?

- A. NSG-Subnet first, then NSG-NIC
- B. NSG-NIC first, then NSG-Subnet
- C. Only NSG-NIC is evaluated for outbound traffic
- D. Priority numbers across both NSGs determine the order

**Answer: B**

**Explanation:** For **outbound** traffic, the evaluation order is reversed: **NIC NSG first**, then **subnet NSG**. The pattern is: inbound = Subnet → NIC; outbound = NIC → Subnet.

---

### Q26
A subnet is **delegated** to `Microsoft.Web/serverFarms` for App Service VNet integration. A developer wants to deploy a **private endpoint** for an Azure SQL Database into the same delegated subnet. What will happen?

- A. The private endpoint can coexist with the delegated service
- B. Private endpoints **cannot** be created in a subnet delegated to another service
- C. The private endpoint is allowed if it uses a different NSG
- D. The private endpoint can be created if the subnet has at least 5 available IP addresses

**Answer: B**

**Explanation:** A **delegated subnet is exclusive to the service it is delegated to**. Private endpoints cannot be deployed in a delegated subnet. A separate, non-delegated subnet is required for the private endpoint.

---

### Q27
A virtual network is connected to on-premises via a **VPN gateway using BGP**. A route table is associated with **Subnet1**. VMs in Subnet1 must **not** learn any routes advertised from on-premises via BGP. What should be configured on the route table?

- A. Add a default route (0.0.0.0/0) pointing to a virtual appliance
- B. Disable **virtual network gateway route propagation** on the route table
- C. Remove the BGP session from the VPN gateway
- D. Add explicit deny routes for all on-premises CIDR blocks

**Answer: B**

**Explanation:** Disabling **virtual network gateway route propagation** on a route table prevents the associated subnet from receiving routes propagated by the gateway (including BGP-learned on-premises routes). The VPN connection and BGP session remain active for all other subnets.

---

### Q28
**ASG-Web** exists in **VNet1** and contains the NIC of **VM1** (in Subnet1 of VNet1). You want to add **VM2's NIC** to ASG-Web. VM2 is in **Subnet2** of VNet1. What is required?

- A. VM2 must be moved to Subnet1 to match VM1
- B. A VNet peering must be created between Subnet1 and Subnet2
- C. VM2 must be in the same virtual network (VNet1) as VM1
- D. ASG-Web must be deleted and recreated to support cross-subnet members

**Answer: C**

**Explanation:** Application Security Groups allow NICs across **different subnets within the same VNet**. The only requirement is that all NIC members belong to the **same virtual network**. Cross-subnet ASG membership within one VNet is supported.

---

### Q29
The DNS zone **contoso.com** is hosted in Azure DNS. You need **contoso.com** (the zone apex) to resolve to a **Traffic Manager** profile. Creating a CNAME record at contoso.com fails. What should you use instead?

- A. An A record with a static IP address for the Traffic Manager profile
- B. An AAAA record pointing to the Traffic Manager endpoint
- C. An Azure DNS **ALIAS A record** pointing to the Traffic Manager profile
- D. A CNAME record with a shorter TTL to work around the restriction

**Answer: C**

**Explanation:** CNAME records are **not allowed at the DNS zone apex** because the apex must host SOA and NS records, and CNAME cannot coexist with other record types on the same name. An **Azure DNS ALIAS record** solves this—it allows the apex to point to an Azure resource (like Traffic Manager) without requiring a static IP.

---

### Q30
Azure Firewall has **Threat Intelligence** filtering enabled. An administrator observes that traffic from known malicious IPs is being **logged** but **not blocked**. What change is required to both log AND block this traffic?

- A. Enable Azure Firewall Premium IDPS in Alert mode
- B. Change the Threat Intelligence mode to **Alert and deny**
- C. Create explicit deny network rules for the malicious CIDRs
- D. Upgrade to the Premium SKU to enable blocking

**Answer: B**

**Explanation:** The **default** Threat Intelligence mode is **Alert only** — it generates logs but allows the traffic through. Changing the mode to **Alert and deny** instructs the firewall to both log and **block** traffic from known malicious IP addresses and domains.

---

### Q31
A Private Endpoint is created for a storage account. VMs in **VNet1** must resolve the storage account's private FQDN to the **private IP**. The Private DNS Zone `privatelink.blob.core.windows.net` exists with the correct A record. What additional resource must be created?

- A. A DNS forwarder VM in VNet1
- B. A custom CNAME record in the Private DNS Zone
- C. A **Virtual Network Link** between the Private DNS Zone and VNet1
- D. An Azure Firewall DNS proxy rule

**Answer: C**

**Explanation:** A Private DNS Zone is not automatically used by any VNet. A **Virtual Network Link** must be created to associate the zone with VNet1. Without this link, VMs in VNet1 cannot use the Private DNS Zone to resolve private endpoint DNS records.

---

## 5) Compute, AKS, and App Service

### Q32
You need to deploy virtual machines that must be protected against **datacenter-level failures** within a single Azure region and must achieve the **99.99% compute SLA**. Which deployment option satisfies both requirements?

- A. Deploy VMs in an Availability Set
- B. Deploy a single VM with Premium SSD managed disks
- C. Deploy VMs across **two or more Availability Zones** in the same region
- D. Deploy a VM Scale Set without specifying zones

**Answer: C**

**Explanation:** Availability Sets protect against rack-level failures within a single datacenter but do not provide datacenter-level fault isolation. **Availability Zones** are physically separate facilities within a region. Deploying VMs across 2+ zones achieves the **99.99% SLA** and guards against full datacenter failures.

---

### Q33
An App Service web app uses a **Key Vault reference without specifying a secret version**. An administrator rotates the secret. The app has not been restarted. What is the **maximum time** before the app begins using the new secret value?

- A. Immediately after the rotation
- B. Up to 1 hour
- C. Up to **24 hours**
- D. Only after the next deployment slot swap

**Answer: C**

**Explanation:** App Service caches Key Vault reference values to prevent per-request Key Vault calls (which would add latency and risk throttling). The cache is refreshed periodically with a maximum interval of **24 hours**. Restarting the app forces an immediate refresh.

---

### Q34
**AKS1** uses a Linux system node pool. You plan to add a **Windows container user node pool**. What must be true about the cluster before the Windows node pool can be added?

- A. The cluster must have at least 3 availability zones configured
- B. The cluster must use the **Standard SKU load balancer**
- C. The cluster must have a Premium SSD storage class configured
- D. The cluster must run Kubernetes version 1.16 or earlier

**Answer: B**

**Explanation:** Multiple node pools in AKS require the **Standard SKU load balancer**. The Basic SKU does not support multiple node pools. The Standard SKU must be selected at cluster creation time and cannot be changed later.

---

### Q35
A CI/CD pipeline runs as a service principal and must both **push** container images to Registry1 **and pull** images during deployments. Following the principle of least privilege, which role should be assigned?

- A. AcrPull
- B. **AcrPush**
- C. Contributor
- D. Owner

**Answer: B**

**Explanation:** `AcrPush` includes both **push and pull** permissions on the registry. `AcrPull` only permits pulling. `Contributor` grants registry management operations (configuration, firewall rules, etc.) that the CI/CD service principal does not need.

---

### Q36
Users must connect to virtual machines through Azure Bastion using **custom port values** instead of the default RDP port 3389 and SSH port 22. What is the **minimum Azure Bastion SKU** that supports custom ports?

- A. Basic
- B. **Standard**
- C. Developer
- D. Custom ports are available in all SKUs

**Answer: B**

**Explanation:** Custom port mapping is a feature available starting with the Azure Bastion **Standard SKU**. The Basic SKU supports only the default RDP (3389) and SSH (22) ports.

---

### Q37
A container is configured on **Kubernetes** with `cpu request: 1` and `cpu limit: 2`. The same container workload is deployed on **ACI** with `cpu: 1` (the request value; no separate limit). Which statement correctly describes the behavioural difference?

- A. Kubernetes guarantees 2 cores; ACI guarantees 1 core minimum
- B. On Kubernetes the container may use up to 2 cores (the limit); on ACI the request of 1 is the effective maximum
- C. ACI allows bursting beyond the request value; Kubernetes does not
- D. Both platforms treat the request as both the minimum and the maximum

**Answer: B**

**Explanation:** In **Kubernetes**, `request` is the guaranteed minimum (used for scheduling) and `limit` is the hard cap. In **ACI**, there is no concept of a separate limit—the `request` value is the **effective maximum** for the container.

---

### Q38
When an **Azure subscription budget threshold** is exceeded, you need to automatically send a Slack notification and create an ITSM ticket without writing any custom application code. Which Azure service should handle the automation workflow?

- A. Azure Function with HTTP trigger
- B. **Azure Logic App** triggered by the budget alert action
- C. Azure Automation PowerShell runbook
- D. Azure Event Grid custom topic

**Answer: B**

**Explanation:** **Azure Logic App** is a no-code/low-code workflow automation service with built-in connectors for Slack, ITSM platforms, and many other services. Azure Budget alerts can trigger a Logic App directly via its HTTP/webhook trigger, enabling automated multi-step workflows without writing code.

---

## 6) Infrastructure as Code

### Q39
A main ARM template must **invoke a linked template** stored in Azure Blob Storage to deploy an App Service Plan. Which resource type should be defined in the main template to reference the linked template?

- A. `Microsoft.Resources/templateSpecs`
- B. `Microsoft.Compute/virtualMachines`
- C. `Microsoft.Resources/deployments`
- D. `Microsoft.Resources/links`

**Answer: C**

**Explanation:** Linked templates are called using the **`Microsoft.Resources/deployments`** resource type with a `templateLink` property pointing to the external template URL. This creates a nested deployment that executes the linked template as a child deployment.

---

### Q40
A Bicep file conditionally deploys a storage account when the `deployStorage` parameter is `true`. The output must return the blob endpoint when deployed, or an **empty string** when not deployed. Which output syntax is correct?

- A. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`
- B. `output blobEndpoint string = if (deployStorage) storageAccount.properties.primaryEndpoints.blob`
- C. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- D. `output blobEndpoint string = when deployStorage storageAccount.properties.primaryEndpoints.blob else ''`

**Answer: A**

**Explanation:** Bicep uses the **ternary operator** (`condition ? trueValue : falseValue`) for conditional expressions. This is the correct syntax to return the blob endpoint when deployed or an empty string otherwise.

---

### Q41
You are building an Azure Workbook to display a list of **all virtual machines across multiple subscriptions**, including their tags and locations. Which data source should be used?

- A. Azure Monitor Metrics
- B. Azure Monitor Logs (Log Analytics workspace)
- C. **Azure Resource Graph**
- D. Azure Activity Log

**Answer: C**

**Explanation:** **Azure Resource Graph** is designed for querying Azure resource metadata (types, tags, locations, properties) efficiently across multiple subscriptions. Azure Monitor Metrics stores time-series performance data; Monitor Logs stores telemetry and events; Activity Log records management-plane operations.

---

### Q42
User1 has the **Monitoring Reader** role and needs to **edit and save** changes to a shared Azure Monitor workbook. Which additional role must be assigned to User1?

- A. Monitoring Contributor
- B. Log Analytics Contributor
- C. **Workbook Contributor**
- D. Contributor (subscription scope)

**Answer: C**

**Explanation:** **Workbook Contributor** provides the `microsoft.insights/workbooks/write` permission required to create, edit, and save shared workbooks. While Monitoring Contributor also grants workbook write access, it provides broader monitoring permissions beyond what is needed—making Workbook Contributor the more precisely scoped choice following least privilege.

---

## 7) Monitoring, Operations, and File Sync

### Q43
You enable **VM Insights** for a fleet of virtual machines. Where does VM Insights store the collected performance metrics and process dependency data?

- A. A designated Azure Storage account
- B. **Log Analytics workspace**
- C. Azure Monitor Metrics store (platform metrics)
- D. Application Insights resource

**Answer: B**

**Explanation:** VM Insights stores all collected data—CPU, memory, disk performance, process information, and network dependency maps—in a **Log Analytics workspace**. The data is written to KQL-queryable tables and can be explored through Azure Monitor dashboards and Workbooks.

---

### Q44
Azure File Sync is configured on a Windows Server with **cloud tiering enabled**. A file (`report.xlsx`) has been tiered to Azure and a local **placeholder stub** remains on the server. A user opens `report.xlsx` from Windows Explorer. What happens?

- A. The user receives a "file not found" error because the content is not local
- B. The file is permanently restored to local storage after access
- C. The file content is **automatically recalled from Azure** and made available to the user
- D. The user must manually download the file from Azure Storage Explorer

**Answer: C**

**Explanation:** Cloud tiering is **transparent to users**. Accessing a tiered file (placeholder) triggers an automatic recall of the full file content from the Azure File Share. The experience is seamless; the user may observe a brief delay while the content downloads, but no special action is required.

---

### Q45
An App Service app uses Key Vault references. After a secret is rotated, the app continues using the **old secret value** for some time. Why does the new value not take effect immediately?

- A. App Service requires a manual deployment to load updated Key Vault references
- B. App Service **caches the secret value** to avoid calling Key Vault on every HTTP request
- C. Key Vault requires an approval workflow before new secret versions are visible
- D. The rotation applies only to new app instances created after the rotation time

**Answer: B**

**Explanation:** App Service caches Key Vault reference values locally. Calling Key Vault on every incoming request would introduce network latency and risk hitting Key Vault throttling limits. The cache is refreshed periodically (up to 24 hours), after which the new secret version is automatically picked up.

---

### Q46
An organisation has **Combined Security Information Registration** enabled alongside MFA and SSPR. Which statement best describes the benefit for end users regarding authentication method registration?

- A. Users must register authentication methods twice—once for MFA and once for SSPR
- B. Users register authentication methods **once**, and those methods work for both MFA and SSPR
- C. SSPR registration is completed automatically; users only need to register for MFA
- D. Combined registration replaces both MFA and SSPR with a single authentication challenge

**Answer: B**

**Explanation:** Combined registration provides a **unified registration flow**. Users register their authentication methods one time, and those methods are then available for both MFA challenge scenarios and SSPR self-service password recovery—eliminating the previous need to register the same methods in separate portals.

---

### Q47
Azure Firewall has **Threat Intelligence filtering enabled** with the default configuration. An administrator reviews logs and sees alerts about traffic from known malicious IPs, but the traffic from those IPs was **not blocked**. Why?

- A. The Azure Firewall SKU does not support Threat Intelligence blocking
- B. The **default TI mode is Alert only**, which logs but does not block traffic
- C. Threat Intelligence requires IDPS to be enabled before blocking can function
- D. Application rules override Threat Intelligence decisions

**Answer: B**

**Explanation:** The **default Azure Firewall Threat Intelligence mode is Alert only**. In this mode the firewall generates alert logs when it detects malicious traffic but permits the traffic through. To both alert and block, the mode must be explicitly changed to **Alert and deny**.

---

### Q48
An App Service references a Key Vault secret using the URI `https://vault.vault.azure.net/secrets/mySecret/` **without specifying a version**. Which secret version does App Service use?

- A. The version that was current at the time of the last app restart
- B. App Service **always fetches the latest active version** of the secret
- C. The first version (version 1) of the secret by default
- D. A specific version must always be provided; omitting the version is not supported

**Answer: B**

**Explanation:** When no version is specified in a Key Vault reference URI, App Service resolves the reference to the **latest active version** of the secret. This enables seamless secret rotation—the app automatically picks up new versions within the 24-hour cache refresh cycle without any configuration changes.

---

### Q49
A container is deployed in **Azure Container Instances** with `cpu: 2` specified as the request. No separate CPU limit is defined. What is the maximum amount of CPU this container can use?

- A. Up to 4 cores (ACI allows a 2× burst above the request)
- B. Exactly **2 cores** (the request is the effective maximum in ACI)
- C. Between 1 and 2 cores (the request is the minimum guaranteed allocation)
- D. Unlimited, subject only to host availability

**Answer: B**

**Explanation:** In ACI, there is no concept of a separate CPU limit. The **request value is the effective maximum** resource allocation for the container. This contrasts with Kubernetes, where `request` is the guaranteed minimum and `limit` is the hard cap.

---

### Q50
An Azure subscription currently has **3,999 role assignments**. The subscription limit is 4,000. An administrator attempts to assign the **Reader role** to a new security group. What happens?

- A. The assignment fails because the 4,000 limit would be exceeded
- B. The assignment **succeeds**, bringing the total to exactly 4,000
- C. The oldest role assignment is automatically removed to accommodate the new one
- D. The limit applies only to user principals, not security groups

**Answer: B**

**Explanation:** The Azure subscription role assignment limit is **4,000**. At 3,999 current assignments, adding one more reaches exactly 4,000—the limit itself, not above it. The assignment succeeds. A 4,001st assignment attempt would fail with a limit-exceeded error.

---

*Question count: 50*
*Last updated: 2026-05-20*
