# AZ-104 Practice Questions (Set 14) — English

---

## 1) Site Recovery, Backup, and Compute

### Q1
A three-tier application consists of a SQL Server VM, an application server VM, and a web server VM. You create an Azure Site Recovery recovery plan. You need to ensure that during failover the SQL Server starts first, then the application server, and finally the web server. How should you configure the startup groups?

- A. Place all three VMs in Group 1 with an ordered list
- B. Place the SQL Server in Group 1, the application server in Group 2, and the web server in Group 3 (one VM per group)
- C. Place the SQL and application servers in Group 1 and the web server in Group 2
- D. Place all three VMs in Group 2 with a startup delay between each

**Answer: B**

**Explanation:** In a Site Recovery recovery plan, startup groups run sequentially — Group 1 runs first, then Group 2, then Group 3. Placing each tier in a dedicated group (SQL in Group 1, App in Group 2, Web in Group 3) guarantees the dependency order of DB → App → Web during failover.

---

### Q2
When configuring an Azure VM backup policy, what is the valid range for the Instant Restore snapshot retention period?

- A. 1 to 30 days
- B. 7 to 14 days
- C. 1 to 5 days
- D. 1 to 7 days

**Answer: C**

**Explanation:** The Instant Restore snapshot retention period is configurable from 1 to 5 days. These snapshots are stored locally close to the VM for fast recovery. After the retention period, data is moved to or referenced only from the Recovery Services vault.

---

### Q3
You have an Azure file share hosted in the East US region. You need to configure Azure Backup for the file share. In which region must you create the Recovery Services vault?

- A. West US, for geographic disaster recovery separation
- B. Any Azure region — Recovery Services vaults are globally accessible
- C. East US — same region as the protected resource
- D. The Azure region nearest to your on-premises data center

**Answer: C**

**Explanation:** Azure Backup requires the Recovery Services vault to be in the same region as the protected resource. Backup snapshots are region-bound; a vault in a different region cannot protect the file share and a cross-region vault is not supported for this scenario.

---

### Q4
You configure an AKS cluster with a Spot node pool. The eviction policy is set to **Deallocate**. During a high-demand Azure period, Spot nodes are evicted. The cluster autoscaler attempts to add new nodes but consistently fails. What is the most likely cause?

- A. The Deallocate eviction policy prevents the autoscaler from replacing evicted nodes
- B. Spot VMs in the region are temporarily unavailable
- C. Deallocated Spot VMs remain in Stopped (deallocated) state and continue consuming compute quota, preventing new node provisioning
- D. The system node pool is occupying all available quota in the subscription

**Answer: C**

**Explanation:** With the Deallocate eviction policy, evicted Spot VMs are placed in Stopped (deallocated) state rather than deleted. These VMs continue to count against the subscription's vCPU quota. When the autoscaler tries to create new nodes, the quota is still occupied by the deallocated VMs, causing provisioning failures. Using the Delete eviction policy removes the VMs entirely, releasing quota immediately.

---

### Q5
A storage account contains 500 GB of blob data in the Archive access tier. The data was moved to Archive 45 days ago. You need to make the data available for immediate read access at minimum cost. Which rehydration approach should you use?

- A. Copy the blob to a Cool tier storage account using standard priority
- B. Set the blob tier to Cool using standard priority
- C. Set the blob tier to Hot using standard priority
- D. Set the blob tier to Hot using high priority

**Answer: D**

**Explanation:** High priority rehydration to Hot tier is the fastest method — objects under 10 GB typically complete within 1 hour. Standard priority can take up to 15 hours. Hot tier enables immediate read access once rehydration completes. While early deletion fees apply (45 days < 180-day minimum), the need for immediate access makes high priority the correct choice.

---

### Q6
You have an Azure SQL Database in the Basic service tier using the DTU purchasing model. What is the maximum number of days you can configure for the point-in-time restore (PITR) backup retention period?

- A. 7 days
- B. 14 days
- C. 90 days
- D. 35 days

**Answer: D**

**Explanation:** Azure SQL Database PITR backup retention can be configured from 1 to 35 days for all DTU-based service tiers (Basic, Standard, Premium). 90 or 180 days apply to long-term retention (LTR) policies or Azure SQL Managed Instance — not standard PITR on DTU tiers.

---

### Q7
You need to enable on-premises Active Directory Domain Services (AD DS) authentication for an Azure file share. You run the **Join-AzStorageAccount** PowerShell cmdlet. What type of object does this cmdlet create in AD DS by default?

- A. A user account
- B. A group Managed Service Account (gMSA)
- C. A service logon account
- D. A computer account

**Answer: D**

**Explanation:** By default, `Join-AzStorageAccount` creates a computer account in AD DS to represent the storage account. The storage account authenticates as this computer object, enabling Kerberos-based SMB authentication from domain-joined machines. You can optionally create a service logon account instead by specifying `-DomainAccountType ServiceLogonAccount`.

---

### Q8
An Azure virtual machine displays a status of **Stopped (deallocated)** in the Azure portal. Which statement correctly describes this state?

- A. The VM OS is shut down, but Azure still reserves the compute host and charges compute costs
- B. The VM and its attached disks are deleted from the subscription
- C. The Azure compute host resources are released, compute charges stop, but the VM object and attached disks continue to exist
- D. The VM is billed for compute but not for storage

**Answer: C**

**Explanation:** Stopped (deallocated) means Azure has released the underlying compute host. Compute charges stop in this state. However, the VM definition and attached managed disks still exist in Azure, and disk storage charges continue. This differs from the Stopped state (OS shutdown only), where Azure still reserves the compute host and compute billing continues.

---

## 2) Identity and RBAC

### Q9
A Microsoft Entra tenant has both SSPR and MFA enabled. Users report they must register authentication methods separately for each feature. You need a solution that lets users register for both SSPR and MFA in a single session. What should you configure?

- A. Enable Password Writeback for Microsoft Entra Connect
- B. Configure a Conditional Access policy requiring MFA registration
- C. Enable combined security information registration in Microsoft Entra ID
- D. Create a Named Location that covers both registration portals

**Answer: C**

**Explanation:** Microsoft Entra combined security information registration provides a unified experience where users register authentication methods once and those methods apply to both SSPR and MFA. Without it, users are directed to separate registration portals for each feature.

---

### Q10
Your tenant uses Microsoft Entra ID Free. An administrator tries to create a dynamic user group that automatically includes users with the job title "Engineer." The option to configure a dynamic membership rule is unavailable. What must be done first?

- A. Enable Microsoft Entra ID B2B collaboration
- B. Assign the Groups Administrator role to the administrator
- C. Create the group as a static group, then convert it to dynamic
- D. Upgrade to Microsoft Entra ID Premium P1 or P2

**Answer: D**

**Explanation:** Dynamic membership groups — where members are automatically added or removed based on attribute rules — require Microsoft Entra ID Premium P1 or P2. The Free tier supports only static (manually assigned) group membership. Once the Premium license is provisioned, the dynamic rule editor becomes available.

---

### Q11
You need to create a dynamic user group in Microsoft Entra ID that automatically includes all external guest users in the tenant. Which membership rule should you use?

- A. `(user.companyName -eq "Guest")`
- B. `(user.mail -contains "@guest")`
- C. `(user.objectType -eq "Guest")`
- D. `(user.userType -eq "Guest")`

**Answer: D**

**Explanation:** The `userType` attribute is set to `Member` for internal users and `Guest` for external B2B guest users. The rule `(user.userType -eq "Guest")` correctly matches all guest accounts in the tenant regardless of domain or company name. `companyName` is not a reliable identifier, and `objectType` is not a valid dynamic group attribute.

---

### Q12
A new administrator needs to create and manage user accounts in Microsoft Entra ID using the admin center. The solution must follow the principle of least privilege. Which Microsoft Entra role should be assigned?

- A. Global Administrator
- B. Billing Administrator
- C. Password Administrator
- D. User Administrator

**Answer: D**

**Explanation:** User Administrator provides the minimum permissions needed to create users, manage user properties, reset passwords, and manage non-privileged groups. Password Administrator can only reset passwords — not create users. Global Administrator is far more privileged than required and violates the least privilege principle.

---

### Q13
An Azure subscription contains 15 resource groups. User1 is assigned the **Contributor** role at the subscription scope and the **Reader** role on resource group RG1. What are User1's effective permissions on RG1?

- A. Reader — the narrower RG1 scope overrides the subscription-level assignment
- B. Owner — combining Contributor and Reader permissions escalates to Owner
- C. Reader — explicit resource group assignments always take precedence over inherited roles
- D. Contributor — Azure RBAC is additive; subscription-level Contributor is inherited down to RG1

**Answer: D**

**Explanation:** Azure RBAC uses an additive (union) model. Effective permissions are the union of all role assignments at all applicable scopes. Contributor at the subscription scope is inherited by all child resource groups including RG1. The Reader role on RG1 adds no new rights since Contributor already includes all Reader permissions. The effective role is Contributor.

---

### Q14
User1 is assigned two roles on a storage account. **Role1** allows all blob DataActions but lists blob delete in **NotDataActions**. **Role2** explicitly grants blob delete in DataActions. What is User1's effective permission for blob delete operations?

- A. Blob delete is denied — NotDataActions always overrides DataActions across all role assignments
- B. Blob delete is denied — NotDataActions in Role1 wins globally and cannot be overridden
- C. The result is undefined when roles conflict on the same data action
- D. Blob delete is allowed — Azure RBAC is additive; Role2's DataActions grant blob delete independently

**Answer: D**

**Explanation:** `NotDataActions` only subtracts permissions within the same role definition — it does not create a deny rule that blocks other role assignments. Azure RBAC uses an additive union model: effective permissions = union of all DataActions from all assigned roles, minus NotDataActions from within the same role. Role2 independently grants blob delete in its DataActions, so User1 can delete blobs.

---

### Q15
User1 has the **Reader** role at the subscription scope. You need User1 to be able to create and manage virtual machines in resource group RG2 only. The solution must use the principle of least privilege. What should you do?

- A. Assign the Contributor role to User1 at the subscription scope
- B. Assign the Owner role to User1 at the RG2 scope
- C. Assign the Contributor role to User1 at the RG2 scope
- D. Assign the Virtual Machine Contributor role to User1 at the RG2 scope

**Answer: D**

**Explanation:** Virtual Machine Contributor grants VM-specific create and management permissions and is scoped to RG2 only. Contributor at RG2 would allow modifying all resource types in RG2 (storage, networking, etc.) — not just VMs — violating the least privilege principle. Subscription-level Contributor is even more permissive.

---

### Q16
A junior administrator asks about the primary purpose of a Microsoft Entra ID Security Group. Which statement is most accurate?

- A. Security Groups are mailing lists used to send email to multiple users simultaneously
- B. Security Groups are policy objects that enforce compliance rules on Azure resources
- C. Security Groups are units for assigning RBAC roles and access permissions to multiple users in bulk
- D. Security Groups control VM network traffic filtering, similar to network security groups

**Answer: C**

**Explanation:** The primary purpose of a Microsoft Entra ID Security Group is access management — grouping users (and other objects) so that RBAC roles, application assignments, and licensing can be applied to the group as a single unit rather than individually per user. While some groups can have email enabled (Mail-enabled Security Group), this is not their primary purpose.

---

## 3) Governance, Policy, and Routing

### Q17
You need to assign a policy with a Deny effect to a subscription. Before fully enforcing it, you want to evaluate compliance on existing resources without blocking any new deployments during the testing period. What should you configure on the policy assignment?

- A. Change the policy effect from Deny to Audit temporarily
- B. Remove the policy assignment entirely until testing is complete
- C. Set the assignment scope to exclude all resource groups
- D. Set the enforcement mode to DoNotEnforce

**Answer: D**

**Explanation:** Setting enforcement mode to `DoNotEnforce` disables the policy effect (no blocking occurs) while still evaluating resources and reporting compliance results. This allows you to assess impact on existing resources before switching to full enforcement. Setting the effect to Disabled would stop all evaluation — you would see no compliance data at all.

---

### Q18
You need to prevent users from deploying virtual machines that do not comply with organizational tagging and size standards. Non-compliant deployment attempts must fail with an error immediately. Which Azure Policy effect achieves this?

- A. audit
- B. append
- C. auditIfNotExists
- D. deny

**Answer: D**

**Explanation:** The `deny` effect blocks the resource creation or update request when the policy conditions are not met, returning an error to the requester. `audit` and `auditIfNotExists` only log non-compliance without blocking the operation. `append` adds required fields but does not block deployments.

---

### Q19
An organization needs to ensure that Azure resources can only be deployed to approved regions (West Europe and North Europe). Which Azure feature should be used to enforce this?

- A. Azure RBAC — assign a custom role that limits the locations users can select
- B. Azure Firewall — create network rules that block traffic to non-approved regions
- C. Management Group locks — apply a ReadOnly lock to prevent resource creation outside approved regions
- D. Azure Policy — assign a policy with the Deny effect that restricts allowed locations

**Answer: D**

**Explanation:** Azure Policy governs *what is allowed* — including resource location constraints. The built-in "Allowed locations" policy initiative blocks deployments outside approved regions using the Deny effect. Azure RBAC governs *who can do what* (permissions/identity) — it cannot restrict deployment regions. Locks prevent changes but cannot enforce location rules.

---

### Q20
Your Azure environment has a virtual network named VNet1 with a subnet (Subnet2) containing multiple VMs. Traffic from Subnet2 must route through an on-premises network via ExpressRoute using BGP-advertised routes. You also create a User-Defined Route (UDR) with the same address prefix as the BGP-advertised route, pointing to a network virtual appliance. Which route takes precedence?

- A. The BGP route takes precedence because it is learned dynamically from on-premises
- B. The system route takes precedence because it was created first
- C. The UDR and BGP route are combined using ECMP load balancing
- D. The User-Defined Route takes precedence over the BGP route

**Answer: D**

**Explanation:** When multiple routes share the same address prefix, Azure selects based on the following priority: User-Defined Route > BGP Route > System Route. UDRs always override BGP routes when address prefixes match, allowing administrators to control traffic flow even when dynamic routes are advertised from on-premises.

---

### Q21
You have an Azure subscription with a budget configured. You need to automatically notify all users who have the Contributor role whenever the budget reaches 80% of its threshold. No individual email address configuration should be needed. What should you configure in the budget alert?

- A. An Action Group that sends email to a distribution list
- B. A Logic App that queries RBAC assignments and sends notifications
- C. Contact Roles — specify the Contributor role as an alert recipient
- D. An alert rule in Azure Monitor targeting the Contributor role

**Answer: C**

**Explanation:** Azure Cost Management budget alerts support Contact Roles, which automatically target all users who hold a specified RBAC role (e.g., Contributor) on the subscription. This eliminates the need to manage individual email addresses. Action Groups can send notifications but cannot dynamically target users based on RBAC roles.

---

### Q22
You have an Azure Firewall policy (ParentPolicy) with threat intelligence mode set to **Alert**. You create a child firewall policy (ChildPolicy) that inherits from ParentPolicy. Which threat intelligence mode values are valid for ChildPolicy?

- A. Off, Alert, or Alert and deny — all three are available on child policies
- B. Off only — child policies must disable inherited settings
- C. Alert and deny only
- D. Alert only

**Answer: C**

**Explanation:** When a child policy inherits from a parent policy, the threat intelligence mode must be configured with the same or a stricter value than the parent. Since the parent is set to Alert, the child can be set to Alert (same) or Alert and deny (stricter). Setting it to Off (less restrictive than Alert) is not allowed on a child policy.

---

### Q23
You configure Azure Application Gateway v2 with path-based routing. The URL path rules are listed in the following order: `/*`, `/images/*`, `/api/*`. You notice that requests to `/api/v1/users` are routed to the default backend pool instead of the API backend pool. What should you do to resolve this?

- A. Upgrade to Application Gateway v2 Premium SKU to enable priority-based routing
- B. Convert path-based rules to header-based routing rules
- C. Move `/*` to the end of the path rule list so more specific paths are evaluated first
- D. Assign a lower numeric priority value to `/api/*` so it is processed first

**Answer: C**

**Explanation:** Azure Application Gateway evaluates path rules in top-down list order (first match wins). The wildcard `/*` at the top of the list matches all requests first, including `/api/v1/users`, before the specific `/api/*` rule is reached. Moving `/*` to the end ensures specific paths (`/api/*`, `/images/*`) are evaluated first. Unlike NSG rules, Application Gateway path rules have no numeric priority — order is determined by list position.

---

### Q24
An Azure Firewall has a firewall policy with three Rule Collection Groups: RCG1 (priority 200), RCG2 (priority 150), and RCG3 (priority 300). Each group contains both network rules and application rules. Traffic arrives that matches a network rule in RCG2. In which overall order does the firewall process rule types across all rule collection groups?

- A. Network rules in RCG2 (lowest priority) → Application rules in RCG2 → all other groups
- B. All DNAT rules → all Network rules → all Application rules (across all groups)
- C. All rules in RCG2 (lowest priority number) → all rules in RCG1 → all rules in RCG3
- D. Application rules → Network rules → DNAT rules

**Answer: B**

**Explanation:** Azure Firewall processes rules by rule type first, then by Rule Collection Group (RCG) priority within each type. The processing order is: DNAT rules (all RCGs) → Network rules (all RCGs, ordered by RCG priority) → Application rules (all RCGs, ordered by RCG priority). The firewall does not process all rules in one RCG before moving to the next.

---

## 4) Networking

### Q25
You have an Azure Standard Load Balancer (LB1) with a backend pool of three VMs. The health probe is configured for HTTP on port 80. All backend VMs are marked unhealthy, although the web applications are running correctly and accessible directly via private IP. Which IP address must be allowed in the NSG rules on the backend VMs to resolve this issue?

- A. The public IP address of LB1
- B. The private IP addresses of all other backend VMs
- C. The Azure Virtual Network gateway IP address
- D. 168.63.129.16

**Answer: D**

**Explanation:** Azure Load Balancer health probes originate from the Azure platform's fixed special IP address 168.63.129.16. If the NSG on the backend VMs does not permit inbound traffic from this IP on the probe port (80), the probe requests are dropped and VMs are marked unhealthy. Adding an inbound NSG rule allowing TCP port 80 from 168.63.129.16 resolves the issue.

---

### Q26
You configure a User-Defined Route (UDR) on Subnet2 with the next hop type set to Virtual appliance, pointing to the private IP of VM1 in Subnet1. VM1 is intended to act as a network virtual appliance (NVA). After applying the UDR, traffic from Subnet2 still does not flow through VM1. What must be configured to resolve this?

- A. A peering connection between Subnet1 and Subnet2
- B. IP forwarding enabled on VM1's network interface
- C. A route table applied to Subnet1 as well
- D. A new NSG rule on Subnet2 allowing all outbound traffic

**Answer: B**

**Explanation:** By default, Azure VMs drop network packets that are not destined for their own IP address. For VM1 to act as an NVA and forward traffic between subnets, IP forwarding must be enabled on VM1's network interface (NIC) in Azure. Without IP forwarding, packets arriving at VM1 from Subnet2 are discarded rather than forwarded to their destination.

---

### Q27
Your organization requires that access to an Azure SQL Database be restricted to traffic from within a specific virtual network, with no traffic traversing the public internet. Which Azure feature provides private IP-based connectivity to the Azure SQL Database without internet exposure?

- A. Service Endpoint — restricts access to the VNet and prevents public access
- B. Azure Private Link with a Private Endpoint — assigns a private IP from the VNet to the SQL Database
- C. Virtual Network Gateway — routes all SQL traffic through an IPSec VPN
- D. Azure Firewall — blocks all public access to the SQL Database

**Answer: B**

**Explanation:** Azure Private Link with a Private Endpoint creates a network interface with a private IP address in your VNet that maps to the Azure SQL Database. Traffic flows entirely within the Azure backbone with no public internet exposure. A Service Endpoint keeps the service's public IP but restricts access to the VNet; Private Endpoint eliminates the public endpoint entirely for that connection.

---

### Q28
A developer attempts to access an Azure Storage Account from a newly deployed virtual machine. No additional configuration has been made beyond creating the storage account. Which statement describes the default network access behavior?

- A. Azure Storage Accounts are private by default and require Private Endpoint configuration
- B. Azure Storage Accounts require a service endpoint before any VM can access them
- C. Azure Storage Accounts are publicly accessible by default — all networks can reach the public endpoint
- D. Azure Storage Accounts block all access until explicitly opened via the firewall

**Answer: C**

**Explanation:** Azure PaaS services including Storage Accounts are publicly accessible by default. The storage account's public endpoint (`storageaccount.blob.core.windows.net`) is open to all networks unless explicitly restricted using storage firewall rules, service endpoints, or Private Endpoints. Organizations must actively configure network restrictions to limit access.

---

### Q29
You add VNet1/Subnet1 to the firewall configuration of a storage account (Firewalls and virtual networks → Selected networks). VM1 in Subnet1 still cannot access the storage account. What is the most likely cause?

- A. The storage account firewall requires a Private Endpoint, not a virtual network rule
- B. The VM must have a public IP address to use storage firewall virtual network rules
- C. The storage account redundancy setting (LRS vs GRS) affects network access
- D. The Microsoft.Storage service endpoint is not enabled on Subnet1

**Answer: D**

**Explanation:** Adding a VNet/subnet to the storage account firewall is only effective if the **Microsoft.Storage** service endpoint is also enabled on that subnet. The service endpoint establishes a secure, optimized path from the subnet to Azure Storage. Without the service endpoint enabled on Subnet1, the storage firewall cannot recognize the source as the permitted subnet, and access is denied.

---

### Q30
You manage a public Azure DNS zone for contoso.com. Three web server VMs have private IP addresses 10.1.1.10, 10.1.1.11, and 10.1.1.12. You need DNS queries for `www.contoso.com` to return all three IP addresses so clients can distribute connections. What should you configure in Azure DNS?

- A. Three separate A records, each with a different IP and a different name (www1, www2, www3)
- B. One CNAME record pointing to an Azure Traffic Manager profile
- C. One A record set for `www` containing all three IP addresses (10.1.1.10, 10.1.1.11, 10.1.1.12)
- D. An AAAA record set with three IPv6 addresses mapped to the IPv4 addresses

**Answer: C**

**Explanation:** In Azure DNS, an A record set can contain multiple IP values under the same name. A query for `www.contoso.com` returns all three IP addresses in the response. DNS clients typically attempt them in order or randomly, providing basic round-robin distribution. This is a lightweight approach to multi-IP DNS response without requiring a load balancer.

---

### Q31
You have an Azure DNS zone for contoso.com. A CNAME record currently exists for `www.contoso.com` pointing to `backend.contoso.com`. A colleague attempts to create an A record for `www.contoso.com` pointing to a new IP address. What will happen?

- A. The A record is created and takes precedence over the existing CNAME
- B. Both records are created and Azure DNS returns both in responses
- C. The CNAME is automatically removed to make room for the A record
- D. The A record creation fails because a CNAME record cannot coexist with any other record type on the same name

**Answer: D**

**Explanation:** DNS RFC and Azure DNS both enforce that a CNAME record cannot coexist with any other record type (A, AAAA, MX, TXT, etc.) on the same DNS name. This applies specifically to the exact same name (`www.contoso.com`). The A record creation will fail with a conflict error.

---

### Q32
You have an Azure App Service (WebApp1) with a deployment slot named **staging**. Traffic routing is configured to send 20% of production traffic to the staging slot. A user browses to WebApp1 and is routed to the staging slot. The user closes the browser and returns 30 minutes later. To which slot will the user be routed?

- A. Production slot — the 80/20 routing ratio is re-evaluated on every new browser session
- B. Staging slot — App Service pins users to the same slot for 1 hour using a cookie
- C. A random slot — Azure randomly selects staging or production based on the 20% weight each time
- D. Production slot — closing the browser always resets the routing assignment

**Answer: B**

**Explanation:** Azure App Service traffic routing uses a cookie named `x-ms-routing-name` to pin users to the slot they were initially assigned to. By default, this pinning lasts for 1 hour. Since the user returns within 30 minutes (before the 1-hour expiry), the cookie is still valid and the user is routed back to the staging slot.

---

## 5) Storage, SAS, and App Service

### Q33
You manage WebApp1 with a staging deployment slot. The staging slot contains a new version of the application. You need to validate that the application behaves correctly with production configuration settings before completing the swap. You also need the ability to cancel the operation if issues are found. What should you do?

- A. Perform a standard slot swap and monitor logs immediately after
- B. Use deployment slot auto-swap to apply production settings before the swap
- C. Clone the production slot to a test slot and test there separately
- D. Use Swap with preview (multi-phase swap) to apply production settings to staging for validation before committing

**Answer: D**

**Explanation:** Swap with preview (multi-phase swap) is a two-phase process: Phase 1 applies production slot settings to the staging slot so you can validate the app under production configuration. Phase 2 either commits the swap (traffic switches to the new version) or cancels it (staging reverts to its original settings). Standard swap switches traffic immediately with no pre-validation phase.

---

### Q34
You need to add a custom root domain (contoso.com) to an Azure App Service. As part of domain ownership verification, you must add a DNS record. Which DNS record is required for root domain ownership verification in Azure App Service?

- A. A CNAME record named `verify` pointing to `<app>.azurewebsites.net`
- B. A TXT record named `asuid` containing the Azure-provided domain verification ID
- C. An MX record pointing to the App Service endpoint
- D. A CNAME record named `asuid` pointing to `<app>.azurewebsites.net`

**Answer: B**

**Explanation:** Azure App Service uses a TXT record named `asuid` (with the value set to the App Service domain verification ID) to verify root domain ownership. Root domains (apex domains like contoso.com) cannot use CNAME records because CNAME on an apex domain conflicts with SOA/NS records per DNS standards. The `asuid` TXT record is the required verification method.

---

### Q35
You have an Azure Storage account with several blob containers. Multiple shared access signatures (SAS) have been created for different applications. You need a way to revoke SAS tokens without waiting for them to expire. Which SAS type supports stored access policies that enable revocation?

- A. Account SAS — supports stored access policies at the account level
- B. User delegation SAS — supports stored access policies through Entra ID
- C. Service SAS — supports stored access policies defined on the container or blob
- D. All SAS types support stored access policies equally

**Answer: C**

**Explanation:** Only Service SAS supports stored access policies. A stored access policy is defined on the resource (container, queue, table, or file share) and the SAS token references it. To revoke, you modify or delete the stored access policy, instantly invalidating all SAS tokens linked to it. Account SAS and User delegation SAS do not support stored access policies and cannot be revoked before expiry.

---

### Q36
You have an Azure file share in a storage account. Users access the share from Microsoft Entra-joined devices. The environment is cloud-only with no on-premises domain controller and no connectivity to AD DS. You need to enable identity-based authentication for the Azure file share. Which identity source should you configure?

- A. On-premises AD DS — configure VPN access for domain controller connectivity
- B. Microsoft Entra Domain Services — deploy a managed domain for Kerberos
- C. Storage account access key — use key-based authentication for all clients
- D. Microsoft Entra Kerberos — enables SMB access from Entra-joined devices without a domain controller

**Answer: D**

**Explanation:** Microsoft Entra Kerberos allows Entra-joined devices to authenticate to Azure Files using Kerberos tickets issued by Microsoft Entra ID (cloud-based) — no on-premises domain controller is required. Azure AD DS requires deploying a managed domain and domain-joining VMs. On-premises AD DS requires domain controller connectivity. Access keys are not identity-based authentication.

---

### Q37
You install the Azure File Sync agent on a Windows Server. Which two components are installed as part of the agent?

- A. FileSyncSvc.exe — background service for sync sessions; and StorageSync.sys — file system filter driver for cloud tiering
- B. AzureFileSyncAgent.exe — sync engine; and AzBackupAgent.dll — backup integration component
- C. FileSyncSvc.exe — sync service; and FileSyncSnapService — Volume Shadow Copy integration
- D. StorageExplorer.exe — file browsing tool; and StorageSync.sys — tiering filter driver

**Answer: A**

**Explanation:** The Azure File Sync agent installs two main components: **FileSyncSvc.exe**, the background Windows service that monitors changes on server endpoints and initiates sync sessions with Azure; and **StorageSync.sys**, a file system filter driver that enables cloud tiering (stubs tiered files locally) and supports fast disaster recovery by recalling files on demand.

---

### Q38
You have an Azure VM (VM1) running with a Premium SSD data disk. The disk size is 256 GiB with performance tier P15. You need to increase the performance to P20 without resizing the disk and without stopping the VM. What should you do?

- A. Detach the disk, change the tier, then re-attach it to the running VM
- B. Stop (deallocate) VM1, change the disk performance tier to P20, then restart
- C. Change the disk performance tier to P20 directly on the running disk without resizing or stopping the VM
- D. Create a new P20 disk, copy data from the P15 disk, and attach it to VM1

**Answer: C**

**Explanation:** Azure Premium SSD supports in-place performance tier changes without requiring the VM to be stopped or the disk to be resized. You can upgrade the performance tier (e.g., P15 → P20) from the disk properties in the portal or via CLI while the VM is running. The tier change is separate from disk size changes and incurs no downtime.

---

### Q39
You create an Azure Monitor Action Group to send notifications when an alert fires. Which of the following are valid **notification** types in an Action Group? (Select three)

- A. Webhook
- B. Email
- C. Azure Function
- D. SMS
- E. Push notification (Azure mobile app)
- F. Logic App

**Answer: B, D, E**

**Explanation:** Action Group notification types that directly alert people are: **Email**, **SMS**, and **Push notification** (via the Azure mobile app). Webhook, Azure Function, and Logic App are *action* types — they trigger automated processes but do not directly notify a person. Understanding this distinction is important for exam questions about alerting users vs. automating responses.

---

### Q40
You have three Azure web apps that all need to access the same Azure Storage account. The role assignment for the storage account must be configured before the web apps are deployed. You need to configure a managed identity. Which type of managed identity should you use?

- A. System-assigned managed identity — one per app, automatically created with the app
- B. User-assigned managed identity — a standalone identity resource that can be shared across apps and pre-created
- C. Service principal with a certificate — allows pre-deployment configuration
- D. Managed identity is not supported across multiple App Service instances

**Answer: B**

**Explanation:** A user-assigned managed identity is an independent Azure resource that can be created before the web apps are deployed. RBAC role assignments can be configured on the storage account ahead of time. The same user-assigned identity can then be attached to all three web apps, giving them identical storage access without managing separate credentials. System-assigned identities are tied to a single resource's lifecycle and cannot be pre-created or shared.

---

## 6) ARM Templates, Bicep, Containers, and Monitoring

### Q41
You have an ARM template deployment named **aspDeployment** that deploys an App Service Plan and outputs its resource ID as `planId`. In the main template, you need to reference this output value as the `serverFarmId` for an App Service resource. What is the correct expression?

- A. `[outputs('aspDeployment').planId]`
- B. `[aspDeployment.outputs.planId]`
- C. `[reference('aspDeployment').outputs.planId.value]`
- D. `[resourceId('Microsoft.Web/serverfarms', 'aspDeployment')]`

**Answer: C**

**Explanation:** In ARM templates, linked deployment outputs are accessed using the `reference()` function: `reference('<deploymentName>').outputs.<outputName>.value`. The `.value` suffix is required to extract the actual value from the output object. Bicep uses different syntax (`moduleName.outputs.outputName` without `.value`).

---

### Q42
You have a Bicep file that defines a module named **stgModule** which deploys a storage account. The module has an output named `storageId`. How do you reference this output in the parent Bicep file?

- A. `storageId`
- B. `module.storageId`
- C. `stgModule.outputs.storageId`
- D. `reference('stgModule').outputs.storageId.value`

**Answer: C**

**Explanation:** In Bicep, module outputs are accessed using the syntax `<moduleName>.outputs.<outputName>`. Unlike ARM templates, Bicep does not require the `.value` suffix — the output is directly accessible as a typed value. The `reference()` function syntax with `.value` is ARM template syntax and is not used in Bicep.

---

### Q43
You are writing a Bicep file with `targetScope = 'subscription'`. You need to deploy a storage account into a specific resource group named **StorageRG** as part of the same deployment. How should you deploy the storage account?

- A. Deploy the storage account directly in the subscription-scoped Bicep file alongside the resource group resource
- B. Use a separate Bicep deployment file; cross-scope resources cannot be in the same deployment
- C. Use a module with `scope: resourceGroup('StorageRG')` to deploy the storage account at resource group scope
- D. Use `scope: subscription()` on the storage account resource to override the file's default scope

**Answer: C**

**Explanation:** In Bicep, individual modules can target a different scope from the parent file using the `scope` property. Deploying from a subscription-scoped file, you create the resource group at subscription scope and then use `scope: resourceGroup('StorageRG')` on a module to deploy the storage account at resource group scope. Storage accounts are resource-group-scoped resources and cannot be deployed directly at subscription scope.

---

### Q44
You create a new Bicep file without specifying a `targetScope` declaration. Which deployment scope is used by default?

- A. subscription
- B. tenant
- C. managementGroup
- D. resourceGroup

**Answer: D**

**Explanation:** If `targetScope` is omitted in a Bicep file, the default deployment scope is `resourceGroup`. To deploy at subscription, management group, or tenant scope, you must explicitly declare `targetScope = 'subscription'` (or the appropriate level). Modules without an explicit `scope` property inherit the parent file's scope.

---

### Q45
You write a subscription-scoped Bicep file that creates a resource group named **AppRG** and needs to deploy a storage account into it in the same deployment. Which approach is correct?

- A. Deploy both the resource group and storage account at subscription scope in the same file without modules
- B. Deploy the resource group at subscription scope in the main file, then use a module with `scope: resourceGroup('AppRG')` for the storage account
- C. Use `scope: subscription()` on the storage account resource to place it in the new resource group
- D. Deploy the resource group and storage account in separate deployments — they cannot share the same Bicep file

**Answer: B**

**Explanation:** Resource groups are deployed at subscription scope. Storage accounts are resource-group-scoped resources — they cannot be deployed at subscription scope directly. The correct pattern in a subscription-scoped Bicep file is to create the resource group first (`Microsoft.Resources/resourceGroups`), then use a module scoped to that resource group (`scope: resourceGroup('AppRG')`) to deploy the storage account. Both can coexist in the same deployment.

---

### Q46
You plan to use Azure Policy to automatically enable VM Insights on 50 virtual machines across a subscription. Before assigning the policy initiative, you need to create a prerequisite resource. Which resource must you create first?

- A. An Application Insights workspace for VM application monitoring
- B. An Azure Monitor workspace for Prometheus-based metrics
- C. A Log Analytics workspace to store the collected data
- D. A VM Insights data collection rule (DCR) that defines what data to collect

**Answer: D**

**Explanation:** When enabling VM Insights via Azure Policy (the modern DCR-based approach), the policy initiative requires a **Data Collection Rule (DCR)** as the prerequisite. The DCR defines which performance metrics, process data, and logs to collect from VMs. While a Log Analytics workspace is also needed to store data, the DCR is the specific resource the policy initiative references and must be created first.

---

### Q47
A team wants to standardize VM monitoring dashboards across multiple Azure subscriptions. They need a reusable definition that can be deployed to create identical monitoring views without rebuilding queries and layouts each time. Which Azure Monitor feature meets this requirement?

- A. Azure Monitor Alerts — reusable rule definitions that generate consistent alert dashboards
- B. Log Analytics saved queries — query definitions that can be shared across workspaces
- C. Azure Dashboard JSON export — export and import an existing dashboard configuration
- D. Azure Workbook templates — reusable blueprints for Workbooks containing pre-defined queries, charts, and layouts

**Answer: D**

**Explanation:** Azure Workbook templates are reusable blueprints that contain pre-built KQL queries, metrics queries, visualizations, and parameter definitions. Templates can be deployed to create Workbook instances across environments without rebuilding from scratch. They support standardization and can be gallery-published for organization-wide use. Workbook templates are distinct from Workbooks (the running instances).

---

### Q48
You have an Azure Container Registry. You need to assign a role to a CI/CD pipeline service principal so it can push new images and pull base images from the registry. The solution must use the principle of least privilege. Which built-in role should you assign?

- A. AcrPull — for pulling images only
- B. Contributor — full management of the container registry
- C. AcrPush — allows both push and pull operations on the registry
- D. Owner — required to configure registry settings during CI/CD pipeline runs

**Answer: C**

**Explanation:** The **AcrPush** built-in role includes both `Microsoft.ContainerRegistry/registries/pull/read` and `Microsoft.ContainerRegistry/registries/push/write` permissions. This is exactly what a CI/CD pipeline needs: push newly built images and pull base images. **AcrPull** allows pull only. **Contributor** grants full resource management rights (including registry deletion) — far more than the pipeline requires.

---

### Q49
You have multiple Azure web apps that all require access to the same Azure Storage account. You need to choose between system-assigned and user-assigned managed identity. Which statement correctly describes a key difference?

- A. System-assigned managed identity can be shared across multiple resources; user-assigned cannot
- B. User-assigned managed identity is deleted when the web app is deleted; system-assigned persists independently
- C. System-assigned managed identity must be pre-created before resource deployment; user-assigned is created automatically
- D. User-assigned managed identity is an independent resource that can be created before deployment and attached to multiple resources; system-assigned is tied to a single resource's lifecycle

**Answer: D**

**Explanation:** A user-assigned managed identity is a standalone Azure resource with its own lifecycle — it can be created before any web app exists, RBAC assignments can be configured in advance, and the same identity can be attached to multiple web apps. A system-assigned identity is automatically created when the resource is created, cannot be shared across resources, and is deleted when the resource is deleted.

---

### Q50
You have an Azure Firewall policy (ParentPolicy) with threat intelligence mode set to **Alert**. A second firewall policy (ChildPolicy) inherits from ParentPolicy and is assigned to a different Azure Firewall instance. An administrator tries to set ChildPolicy's threat intelligence mode to **Off** to troubleshoot a connectivity issue. What is the result?

- A. The setting is applied because child policies can override any parent setting
- B. The configuration fails — a child policy's threat intelligence mode cannot be less restrictive than the parent's mode
- C. The change is allowed but the parent policy's Alert mode overrides it at runtime
- D. The Off mode is permitted only for child policies assigned to test firewalls

**Answer: B**

**Explanation:** In Azure Firewall Manager, child policies must have a threat intelligence mode that is equal to or stricter than the parent policy. The strictness order is: Off < Alert < Alert and deny. Since the parent is set to Alert, the child may only use Alert or Alert and deny. Setting the child to Off (less restrictive than Alert) is invalid and the configuration will be rejected.

---

*Document generated from AZ-104 course transcript. Last updated: 2026-05-22*
