# AZ-104 Practice Questions (English) - Set 2

> Original exam-style practice set aligned to AZ-104 skills measured (Apr 17, 2026)
> 40 questions with concise explanations

---

## 1) Identities and Governance

### Q1
You need to allow a helpdesk team to reset passwords for users in only one department.
What should you use?

- A. Management Group
- B. Administrative Unit
- C. Resource Lock
- D. Azure Policy

**Answer: B**
**Explanation:**
**Administrative Units (AUs)** are an Entra ID concept — they are **not** related to Azure subscription roles or RBAC at all.
- AUs contain a subset of Entra users, groups, or devices
- An admin role (e.g., Password Administrator) scoped to an AU can only act on members of that AU
- Example: HR Admin can reset passwords only for HR users, not Engineering users
- Management Groups organize subscriptions; Resource Locks prevent deletion; Policy enforces compliance — none of these scope helpdesk identity admin actions
- Portal path: Entra ID → Administrative Units → Create AU → add members → assign role

### Q2
A user has Reader at subscription scope and Contributor at one resource group.
What can the user do in that resource group?

- A. Read only
- B. Create/update/delete resources in that resource group
- C. Assign roles
- D. Delete subscription

**Answer: B**
**Explanation:** Lower-scope Contributor grants write actions there; role assignment still requires Owner or User Access Administrator.

### Q3
You must enforce that all new resource groups contain the tag `CostCenter`.
Best approach?

- A. Resource Lock
- B. Azure Policy with Deny effect
- C. NSG
- D. Management Group rename

**Answer: B**
**Explanation:** Azure Policy can deny create/update when required tags are missing.

### Q4
Which statement about resource locks is correct?

- A. ReadOnly lock allows write via portal but not CLI
- B. Delete lock blocks all updates
- C. Locks apply to control plane operations
- D. Locks are not inherited

**Answer: C**
**Explanation:**
Azure has two planes:
- **Control plane (management plane)**: ARM API operations — creating, deleting, or updating a resource configuration (e.g., changing a VM's size, adding a disk, deleting a storage account)
- **Data plane**: actual data operations — reading/writing files inside a storage account, running queries in a database, sending messages to a queue

Resource Locks apply **only to control plane operations**. Examples:
- A **ReadOnly lock** on a storage account prevents changing its settings (control plane), but apps can still read/write blob data (data plane)
- A **Delete lock** on a Key Vault prevents its deletion (control plane), but secrets can still be read (data plane)

This is a common exam trap: a locked resource is not necessarily data-protected.

### Q5
You need to centrally manage budgets and spending alerts per subscription.
Where should you configure this?

- A. Azure DNS
- B. Cost Management + Billing
- C. Network Watcher
- D. App Service Plan

**Answer: B**
**Explanation:** Budgets, alerts, and cost analysis are in Cost Management.

### Q6
A custom role is created in one subscription.
Can it be assigned in another subscription by default?

- A. Yes
- B. No
- C. Only with Owner role
- D. Only if using Policy initiative

**Answer: B**
**Explanation:** Custom roles are scoped where created unless explicitly created at higher scope.

### Q7
You want MFA only when users sign in from risky locations.
Best method?

- A. Per-user MFA
- B. Conditional Access policy
- C. SSPR
- D. PIM only

**Answer: B**
**Explanation:**
**Conditional Access (CA)** is an Entra ID feature that acts as an if-then policy engine:
- *If* conditions are met (user, location, device, app, sign-in risk) → *Then* enforce grant controls or session controls

**Grant controls (what you can require):**
- Require MFA (most common)
- Require compliant device (Intune-enrolled)
- Require Hybrid Azure AD joined device
- Require approved client app
- Require app protection policy
- Require password change (for risky sign-ins)

**Per-user MFA** is the legacy method — it always prompts for MFA regardless of location/risk. CA is far more flexible and is the recommended approach.

### Q8
You need to prevent accidental deletion of a production resource group, while allowing updates.

- A. ReadOnly lock
- B. Delete lock
- C. Deny assignment
- D. NSG rule

**Answer: B**
**Explanation:** Delete lock blocks deletes and still allows modifications.

---

## 2) Storage

### Q9
You need temporary read access to one blob container without exposing account keys.
Best choice?

- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Shared key auth only

**Answer: C**
**Explanation:**
All three SAS types can grant access to a single container — so why is Service SAS (B) wrong here?

| SAS Type | Signed with | Key used? | Notes |
|----------|------------|-----------|-------|
| Account SAS | Account key | Yes | Broadest scope; requires key |
| Service SAS | Account key | Yes | Single service scope; still uses account key |
| User Delegation SAS | Entra credentials | No | Signed by AAD token; no account key needed |

The question says **"without exposing account keys"**. Both Account SAS and Service SAS are signed using the storage account key — generating them requires access to that key. **User Delegation SAS** is the only type that does not touch the account key at all; it uses an Entra OAuth token instead. This makes it the most secure option and is Microsoft's recommended approach for Blob access delegation.

### Q10
You must revoke multiple SAS tokens quickly without rotating account keys.
What helps most?

- A. NSG
- B. Stored access policy
- C. ZRS
- D. Blob versioning

**Answer: B**
**Explanation:** Stored access policy enables centralized control/revocation behavior for SAS.

### Q11
Which redundancy provides zone-level durability in one region only?

- A. LRS
- B. ZRS
- C. GRS
- D. RA-GRS

**Answer: B**
**Explanation:** ZRS replicates across availability zones within a single region.

### Q12
You need read access to secondary region during regional outage.
Which option?

- A. GRS
- B. ZRS
- C. RA-GRS
- D. LRS

**Answer: C**
**Explanation:** RA-GRS adds read access to secondary region.

### Q13
Which service is best for decoupling app components with reliable messages?

- A. Azure Files
- B. Blob Archive
- C. Queue Storage
- D. Table Storage

**Answer: C**
**Explanation:** Queue Storage is designed for asynchronous messaging.

### Q14
Table Storage primary key pattern is:

- A. TableName + Index
- B. PartitionKey + RowKey
- C. RowId + GUID
- D. ResourceId + Timestamp

**Answer: B**
**Explanation:** Table entities are addressed by PartitionKey and RowKey.

### Q15
You need to keep deleted blobs recoverable for 14 days.
Configure:

- A. Blob soft delete
- B. File snapshot only
- C. LRS
- D. Queue TTL

**Answer: A**
**Explanation:** Blob soft delete retains deleted blobs for configured retention days.

### Q16
You need automatic tier transition from Hot to Cool based on age.
Use:

- A. Azure Policy initiative
- B. Blob lifecycle management
- C. NSG outbound rule
- D. UDR

**Answer: B**
**Explanation:** Lifecycle management moves blobs between tiers using rules.

---

## 3) Compute, Containers, and App Service

### Q17
You need to move a VM to another resource group in same subscription.
What is supported?

- A. Not supported
- B. Move operation is supported for many VM resources with dependencies
- C. Must recreate VM always
- D. Only if VM is stopped in guest OS

**Answer: B**
**Explanation:**
Azure does support moving many resources between resource groups or subscriptions using the Move operation, but it is **not always seamless**. Key points for VMs:

**What moves together:** When moving a VM, you typically must also move its dependent resources:
- The managed disk(s) associated with the VM
- The NIC(s)
- The public IP address (if any)
- The NSG (if NIC-attached)
- The VNet must either exist in the target RG or be moved separately

**Constraints:**
- The VM does not need to be stopped to initiate the move (though it's safer to do so)
- You cannot move resources between Azure AD tenants with this method
- Validation runs before the move; Azure will list blockers
- Portal path: Resource Group → select resources → Move → Move to another resource group

**Common exam trap:** "Not supported" is wrong — moves are broadly supported. "Must recreate" is also wrong.

### Q18
For customer-managed key encryption of managed disks, you need:

- A. Disk Encryption Set
- B. Route table
- C. Application Gateway
- D. Log Analytics workspace

**Answer: A**
**Explanation:** Disk Encryption Set bridges managed disks and Key Vault key.

### Q19
A VM Scale Set default scale-in policy removes:

- A. Oldest instance
- B. Newest instance
- C. Highest instance ID
- D. Lowest CPU VM

**Answer: C**
**Explanation:** Default behavior removes highest instance ID unless policy changed.

### Q20
You need quick serverless container execution for a one-off batch job.
Best fit:

- A. Azure Kubernetes Service
- B. Azure Container Instances
- C. VM Scale Sets
- D. App Service Web App

**Answer: B**
**Explanation:** ACI is optimized for fast, simple serverless container runs.

### Q21
You need managed microservices with revisions and event-driven scale.
Best fit:

- A. Azure Container Apps
- B. Azure Files
- C. Traffic Manager
- D. Bastion

**Answer: A**
**Explanation:** Container Apps offers managed runtime, scale, and revisions.

### Q22
Private image storage for containers should use:

- A. Azure DNS
- B. Azure Container Registry
- C. Blob public container
- D. Queue Storage

**Answer: B**
**Explanation:** ACR is Azure's managed private container registry.

### Q23
You need zero-downtime deployment validation before production swap.
Which App Service feature?

- A. Deployment slots
- B. NSG
- C. Backup vault
- D. Availability set

**Answer: A**
**Explanation:**
**Azure App Service** is Microsoft's fully managed platform for hosting web applications, REST APIs, and mobile backends — you deploy your code; Azure manages the underlying OS, patching, scaling, and load balancing. You pay for the App Service Plan (compute tier), not individual servers.

**Deployment Slots** are separate live environments within the same App Service:
- Default slot = **production**
- You can add additional named slots: `staging`, `dev`, `test`, etc.
- Each slot has its own URL (e.g., `myapp-staging.azurewebsites.net`)
- You deploy to staging → run integration tests → **swap** to production with near-zero downtime
- During a swap, Azure routes traffic from the old slot to the new one (a brief routing switch, not a redeployment)
- Some settings are **slot-specific** (sticky): connection strings, app settings marked sticky — they do not follow the swap
- You can also configure **Traffic splitting** (e.g., 10% of users to staging for A/B testing)

This is why deployment slots are the correct tool for zero-downtime validated releases.

### Q24
For private inbound access to App Service from vNet, choose:

- A. VNet integration only
- B. Private Endpoint
- C. Service Endpoint
- D. Route-based VPN

**Answer: B**
**Explanation:** Private Endpoint provides private inbound IP-based access.

---

## 4) Virtual Networking

### Q25
Requirement: Keep Storage public endpoint, but allow only selected subnet traffic over Microsoft backbone.
Choose:

- A. Private Endpoint
- B. Service Endpoint
- C. VNet peering
- D. NAT Gateway

**Answer: B**
**Explanation:** Service Endpoint secures traffic path and service firewall by subnet without private IP on service.

### Q26
Requirement: Disable public access and access Storage only through private IP in vNet.
Choose:

- A. Service Endpoint
- B. Private Endpoint
- C. NSG only
- D. Public Load Balancer

**Answer: B**
**Explanation:** Private Endpoint maps service to private IP in your subnet.

### Q27
What is correct about Azure Firewall vs NSG?

- A. They are the same resource type
- B. NSG replaces Azure Firewall in all enterprise scenarios
- C. Azure Firewall and NSG are complementary
- D. Azure Firewall can only do inbound filtering

**Answer: C**
**Explanation:** NSG does subnet/NIC L3-L4 filtering; Azure Firewall provides centralized advanced controls.

### Q28
Gateway subnet in Azure is used for:

- A. VPN gateway only
- B. ExpressRoute gateway only
- C. Virtual network gateway resources (VPN/ExpressRoute scenarios)
- D. Azure Bastion only

**Answer: C**
**Explanation:** GatewaySubnet hosts virtual network gateway resources for gateway scenarios.

### Q29
Which subnet name is required for VPN/ER gateway deployment?

- A. VNetGatewaySubnet
- B. GatewaySubnet
- C. AzureGateway
- D. Any name

**Answer: B**
**Explanation:** Exact name must be GatewaySubnet.

### Q30
You need simpler NSG rules based on application role instead of IP addresses.
Use:

- A. UDR
- B. ASG
- C. Azure Policy
- D. Load Balancer rule

**Answer: B**
**Explanation:** Application Security Groups group NICs for role-based NSG references.

### Q31
NSGs can be applied to:

- A. vNet only
- B. NIC only
- C. Subnet and NIC
- D. Storage account only

**Answer: C**
**Explanation:** NSG scope supports both subnet and NIC.

### Q32
To troubleshoot why outbound traffic is blocked, you should inspect:

- A. Effective routes and effective security rules
- B. DNS zone only
- C. Blob index tags
- D. Backup reports

**Answer: A**
**Explanation:** Combined route and NSG results determine effective flow behavior.

---

## 5) Load Balancing, DNS, Monitoring, Backup

### Q33
What does zone-redundant frontend on Standard Load Balancer mean?

- A. Backend VMs are automatically replicated across zones
- B. Frontend IP remains available during a zone failure
- C. Only internal load balancers support it
- D. Requires Basic SKU

**Answer: B**
**Explanation:** Frontend survives zone failure, but backend design must also be resilient.

### Q34
Standard Load Balancer backend VM has Basic public IP.
Result?

- A. Fully supported
- B. Unsupported combination
- C. Works only in one zone
- D. Works after reboot

**Answer: B**
**Explanation:** Standard LB requires Standard public IP or no public IP.

### Q35
You need internet DNS delegation for contoso.com to Azure DNS.
Required action:

- A. Create TXT record only
- B. Update registrar NS records to Azure DNS name servers
- C. Add UDR
- D. Enable Bastion

**Answer: B**
**Explanation:** Registrar must delegate the zone to Azure DNS authoritative name servers.

### Q36
Need secure RDP/SSH to VMs without public IPs.
Best service:

- A. NAT Gateway
- B. Azure Bastion
- C. Front Door
- D. ExpressRoute

**Answer: B**
**Explanation:** Bastion provides browser-based secure management without VM public IP.

### Q37
Which tool checks if a specific flow is allowed/denied by NSG?

- A. Packet Capture
- B. Connection Monitor
- C. IP Flow Verify
- D. Topology

**Answer: C**
**Explanation:** IP Flow Verify returns allow/deny and matching NSG rule.

### Q38
You need scheduled backups and restore points for Azure VMs.
Primary service:

- A. Azure Policy
- B. Recovery Services vault
- C. Route table
- D. DNS private zone

**Answer: B**
**Explanation:** Azure Backup uses Recovery Services vault with backup policies.

### Q39
ASR test failover should be used because it:

- A. Permanently deletes source VM
- B. Validates DR plan without impacting production
- C. Requires no target network
- D. Disables replication

**Answer: B**
**Explanation:** Test failover validates recovery workflow safely.

### Q40
You need alert notifications to email and webhook for CPU threshold breaches.
Configure:

- A. Action Group in Azure Monitor alerts
- B. NSG flow logs only
- C. Storage lifecycle rule
- D. Azure DNS record set

**Answer: A**
**Explanation:** Alert rules trigger reusable action groups (email/SMS/webhook/etc.).

---

Use this set together with Set 1 and review incorrect answers by objective domain.