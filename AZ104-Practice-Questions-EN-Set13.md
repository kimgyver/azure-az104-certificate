# AZ-104 Practice Questions (Set 13) — English

---

## 1) Governance, Policy, and Resource Management

### Q1
You have an Azure Policy assignment that uses the **modify** effect to automatically add a required tag to storage accounts. After assignment, several existing storage accounts are evaluated and marked as non-compliant. What must you configure to apply the tag to the existing non-compliant storage accounts?

- A. Change the policy effect to deployIfNotExists
- B. Create a remediation task for the policy assignment
- C. Re-assign the policy to a broader scope
- D. Delete and recreate the non-compliant storage accounts

**Answer: B**

**Explanation:** The modify effect can automatically add tags during create or update operations, but it does not retroactively fix existing non-compliant resources. To update existing resources, you must create a remediation task on the policy assignment. Azure then uses the managed identity of the assignment to apply the required tag to all non-compliant storage accounts.

---

### Q2
You are creating a custom Azure Policy definition that checks whether a specific extension resource exists for each virtual machine. If the extension is missing, the VM should be marked non-compliant, but deployments must not be blocked. Which policy effect should you use?

- A. Deny
- B. Audit
- C. AuditIfNotExists
- D. DeployIfNotExists

**Answer: C**

**Explanation:** AuditIfNotExists evaluates whether a related resource (the extension) exists for the primary resource (the VM). If the extension is absent, the VM is marked non-compliant. Crucially, this effect does not block the deployment or modify any resource — it only flags the violation. Deny blocks deployments, and DeployIfNotExists would auto-create the extension.

---

### Q3
You need to prevent the accidental deletion of all resources in a resource group named RG1. You also need to apply similar protection to a management group named MG1. Which statement is correct?

- A. You can apply a CanNotDelete lock to MG1 and it will be inherited by RG1
- B. You can apply a CanNotDelete lock to RG1 but not to MG1
- C. You can apply a CanNotDelete lock to MG1 but not to RG1
- D. Resource locks cannot be applied to resource groups; use Azure Policy instead

**Answer: B**

**Explanation:** Azure resource locks can be applied at the subscription, resource group, or resource level. Management groups are explicitly excluded from lock scope — you cannot add a lock to a management group, and there is no inheritance from MG to subscriptions. To protect resources under MG1, you must apply locks individually at the subscription or resource group level within each subscription.

---

### Q4
A resource group named RG1 has a **ReadOnly** lock applied. An Azure Automation account in RG1 has runbooks that fail with a "scope locked" error. You need to allow the runbooks to execute while still preventing accidental deletion of RG1's resources. What should you do?

- A. Remove the ReadOnly lock and add an additional Azure Policy with the audit effect
- B. Change the ReadOnly lock to a Delete lock
- C. Move the Automation account to a different resource group
- D. Grant the Automation account's managed identity the Owner role on RG1

**Answer: B**

**Explanation:** A ReadOnly lock blocks all write operations, including internal state changes that Azure Automation makes when starting a job (creating a job object, updating job status). A Delete lock only prevents deletion of resources — it allows reads and writes, so runbooks can execute normally. Changing the lock type from ReadOnly to Delete satisfies both requirements.

---

### Q5
You create an Azure budget at the management group scope to monitor costs across multiple subscriptions. You want to configure action groups to trigger automated workflows when thresholds are exceeded. What should you expect?

- A. Action groups are fully supported at the management group scope, the same as subscription scope
- B. Action groups are supported at management group scope for email and webhook only
- C. Action groups cannot be configured for management group scope budgets
- D. Management group budgets cannot send any alerts

**Answer: C**

**Explanation:** Azure Cost Management budgets at the management group scope support email notifications and threshold alerts, but do NOT support action groups. Action groups (which can trigger Logic Apps, Azure Functions, webhooks, etc.) are only available for budgets scoped to subscriptions or resource groups. To automate responses at scale, you must create separate subscription-scoped budgets with action groups for each subscription.

---

### Q6
A user named User1 is assigned the **Reader** role at a management group (MG1) and the **Contributor** role at a resource group (RG1) within that management group. What is User1's effective permission on resources inside RG1?

- A. Reader, because the management group scope overrides the resource group scope
- B. Contributor, because Azure RBAC uses an additive model and Contributor already includes all Reader permissions
- C. A mix of Reader for management tasks and Contributor for data operations
- D. Denied access because conflicting roles require explicit resolution

**Answer: B**

**Explanation:** Azure RBAC uses an additive model — permissions from all role assignments are combined. User1 has Reader (from MG1, inherited down) and Contributor (assigned at RG1). Since Contributor already includes all Reader permissions plus write capabilities, the effective permission at RG1 is Contributor. Neither scope overrides the other; they accumulate.

---

### Q7
You need to assign a resource lock so that a user can create a **CanNotDelete** lock on resource group RG1. The user currently has the **Contributor** role on RG1. What is the minimum additional role required?

- A. Assign the Virtual Machine Contributor role at subscription scope
- B. Assign the Owner role at RG1 scope
- C. Assign the User Access Administrator role at RG1 scope
- D. No additional role is needed; Contributor can create locks

**Answer: C**

**Explanation:** Creating or deleting resource management locks requires the `Microsoft.Authorization/locks/*` permission. Of the built-in roles, only **Owner** and **User Access Administrator** include this permission. Contributor does not. Assigning User Access Administrator at RG1 scope is the least-privilege option because it grants only authorization management permissions, rather than the full resource management access that Owner provides.

---

## 2) Identity, Entra ID, and RBAC

### Q8
You need to create a Microsoft Entra group that can be assigned administrator roles and whose members are automatically managed based on the users' **department** attribute. Is this possible?

- A. Yes, by creating a Security group with Dynamic User membership and enabling role assignment
- B. Yes, by creating a Microsoft 365 group with Dynamic User membership and enabling role assignment
- C. No, because role-assignable groups must use Assigned membership type
- D. No, because Microsoft Entra roles can only be assigned to individual users

**Answer: C**

**Explanation:** Groups that can be assigned Microsoft Entra administrator roles must have `isAssignableToRole = true` and must use **Assigned** (not dynamic) membership. Dynamic membership is explicitly prohibited for role-assignable groups to prevent automated membership changes from inadvertently granting administrator privileges — for example, if a department attribute is modified or synced incorrectly.

---

### Q9
You need to configure external collaboration settings so that only users with the **User Administrator** or **Guest Inviter** roles can invite external guest users. Which Guest invite settings option should you select?

- A. Everyone including guests can invite
- B. Member users and users assigned to specific admin roles can invite guests
- C. Only users assigned to specific admin roles can invite guests
- D. No one can invite guests

**Answer: C**

**Explanation:** "Only users assigned to specific admin roles can invite guests" restricts invitations exclusively to users holding a designated admin role such as User Administrator or Guest Inviter. Option B also allows regular Member users to send invitations, which is broader than required.

---

### Q10
A colleague needs to perform the following tasks in Microsoft Entra ID: create new user accounts, reset user passwords, and manage user licenses. You must assign the minimum required role. Which role should you assign?

- A. Helpdesk Administrator
- B. License Administrator
- C. User Administrator
- D. Global Administrator

**Answer: C**

**Explanation:** User Administrator is the minimum role that covers all three tasks: creating users, resetting passwords, and managing licenses. Helpdesk Administrator can only reset passwords for non-admin users. License Administrator can only manage licenses. Global Administrator has all permissions but violates the principle of least privilege.

---

### Q11
You are preparing a CSV file to bulk-create users in Microsoft Entra ID using the admin center. Which four columns are **required** in the bulk create CSV template?

- A. Name, User principal name, Initial password, Department
- B. Name, User principal name, Initial password, Block sign in
- C. Name, User principal name, Display name, Initial password
- D. Name, User principal name, Initial password, Usage location

**Answer: B**

**Explanation:** Microsoft's bulk user creation template requires exactly four columns: **Name** (serves as display name), **User principal name** (login ID), **Initial password**, and **Block sign in** (Yes/No). Department, Display name, and Usage location are all optional properties that can be populated after creation.

---

### Q12
You need to create a custom Azure RBAC role that grants all permissions for virtual machines **except** the ability to delete them. Which combination of role definition properties should you configure?

- A. Actions: `Microsoft.Compute/virtualMachines/*` only, and use Deny assignment for delete
- B. Actions: `Microsoft.Compute/virtualMachines/*` and NotActions: `Microsoft.Compute/virtualMachines/delete`
- C. Actions: list every VM action except delete individually
- D. DataActions: `Microsoft.Compute/virtualMachines/*` and NotDataActions: `Microsoft.Compute/virtualMachines/delete`

**Answer: B**

**Explanation:** In a custom role definition, `Actions` defines what is permitted and `NotActions` subtracts specific operations from those permissions within the same role. Setting Actions to `Microsoft.Compute/virtualMachines/*` and NotActions to `Microsoft.Compute/virtualMachines/delete` grants all VM operations while excluding deletion. Deny assignments are a separate mechanism and are not defined within a role definition itself.

---

### Q13
You need to grant User1 read access to blobs in a container named **container1** only, with no access to blobs in any other container in the same storage account. At which scope should you assign the **Storage Blob Data Reader** role?

- A. At the storage account scope
- B. At the resource group scope
- C. At the container1 (container) scope
- D. At the subscription scope

**Answer: C**

**Explanation:** Azure Storage supports role assignments at the storage account, container, and blob scope. Assigning the Storage Blob Data Reader role at the container scope limits access to blobs within that container only. Assigning at the storage account scope would grant read access to all containers, violating least privilege.

---

### Q14
User1 is assigned the **Storage Blob Data Contributor** role at the storage account scope but reports that they cannot see the blob containers in the Azure portal. You need to fix this while maintaining least privilege. What should you do?

- A. Assign the Storage Blob Data Reader role at the container scope
- B. Assign the Reader role at the storage account scope
- C. Assign the Contributor role at the storage account scope
- D. Grant User1 access to the Azure portal directly

**Answer: B**

**Explanation:** Storage Blob Data Contributor is a **data plane** role — it allows reading and writing blob data but does not provide access to the storage account's management resources in Azure Resource Manager. The Azure portal requires ARM-level access to enumerate and display container resources. Assigning the **Reader** (ARM) role at the storage account scope gives User1 the visibility to browse containers without granting extra write or management permissions.

---

## 3) Networking

### Q15
A virtual machine named VM1 in VNet1 cannot be reached via RDP from the internet. You need to quickly determine which specific NSG rule is allowing or denying the RDP traffic. Which tool should you use?

- A. NSG flow logs in Azure Network Watcher
- B. Connection troubleshoot in Azure Network Watcher
- C. Traffic analytics in Azure Monitor
- D. IP flow verify in Azure Network Watcher

**Answer: D**

**Explanation:** IP flow verify simulates a specific traffic flow (source IP, destination IP, port, protocol) against all NSG rules and returns immediately: whether the traffic is allowed or denied, and the exact name of the NSG rule responsible. NSG flow logs record past traffic but require log analysis. Connection troubleshoot tests end-to-end connectivity but doesn't pinpoint a specific rule. Traffic analytics is for long-term trend analysis.

---

### Q16
You have a virtual network named VNet1 with an NVA (network virtual appliance) deployed. You create a route table with a UDR pointing to the NVA and associate it with a subnet. You also have a VPN gateway using BGP that learns routes from on-premises. BGP-learned routes are appearing in the subnet and overriding the UDR for some prefixes. What should you configure on the route table to prevent on-premises BGP routes from being automatically propagated to the subnet?

- A. Add a deny rule in the route table for BGP routes
- B. Disable BGP route propagation on the route table
- C. Change the VPN gateway to use static routing
- D. Remove the BGP configuration from the VPN gateway

**Answer: B**

**Explanation:** Route tables have a **Disable BGP route propagation** setting. When enabled (disabled = true), the gateway does not propagate BGP-learned on-premises routes to the associated subnet. The subnet then only uses UDRs and system routes. This is the standard approach when forcing all traffic through an NVA and preventing BGP from interfering.

---

### Q17
You have a VNet1 in East US containing three VMs: VM1, VM2, and VM3. You create an application security group (ASG) named ASG-Web in the East US region and add VM1 and VM2 as members. You plan to create an NSG rule that allows inbound TCP 443 from ASG-Web to ASG-DB. Which virtual machines can be members of ASG-DB?

- A. Only VM3, because VM1 and VM2 are already in ASG-Web
- B. Any virtual machine in the East US region
- C. Only virtual machines in VNet1
- D. Any virtual machine in the Azure subscription

**Answer: C**

**Explanation:** All network interfaces assigned to an ASG must exist in the same virtual network as the first network interface assigned to that group. Since ASG-Web contains interfaces from VNet1, ASG-DB must also contain interfaces from VNet1 only. The constraint is VNet-based, not region or subscription-based. Note that VM1 and VM2 can belong to multiple ASGs simultaneously — a VM is not excluded from ASG-DB just because it is already in ASG-Web.

---

### Q18
You deploy an Azure Application Gateway to route traffic to an Azure App Service backend with the default domain `webapp1.azurewebsites.net`. Users access the app via a custom domain. When you test, the App Service returns errors because it does not recognize the incoming host header. What should you configure in the Application Gateway backend settings?

- A. Configure the backend pool with the custom domain name
- B. Enable "Pick host name from backend target" in the backend HTTP settings
- C. Create a custom probe that uses the custom domain as the host header
- D. Add a rewrite rule to remove the host header before forwarding to the backend

**Answer: B**

**Explanation:** App Service is a multi-tenant service that uses the hostname in the HTTP Host header to route requests to the correct app. When a custom domain is used on the client side, the Host header contains the custom domain, which App Service does not recognize. Enabling **"Pick host name from backend target"** in the backend HTTP settings causes Application Gateway to automatically replace the Host header with the FQDN of the backend pool target (e.g., `webapp1.azurewebsites.net`), which App Service can process correctly.

---

### Q19
You have a subnet with a Microsoft.Storage service endpoint enabled. You want to create a service endpoint policy to restrict which specific Azure Storage accounts the subnet can reach. Which limitation applies to service endpoint policies for Azure Storage?

- A. Service endpoint policies can only target storage accounts in the same region as the VNet
- B. Service endpoint policies do not apply to traffic from on-premises networks
- C. Service endpoint policies require a minimum subnet size of /24
- D. Service endpoint policies can only allow access to one storage account per policy

**Answer: B**

**Explanation:** Service endpoint policies only filter outbound traffic **from Azure VNet subnets** to Azure Storage. Traffic that originates from on-premises networks (via VPN, ExpressRoute, etc.) bypasses service endpoint policies entirely and must be controlled using firewalls or NVAs. Regarding option A: when service endpoint policies are applied, the scope is actually upgraded to global — policies can target storage accounts in any region.

---

### Q20
You have a public Azure DNS zone for `fabrikam.com`. You want to create a record set with multiple A records pointing to different IP addresses for DNS round-robin load distribution. What is the maximum number of records you can add to a single A record set in Azure DNS?

- A. 10
- B. 20
- C. 50
- D. 100

**Answer: B**

**Explanation:** Azure DNS supports up to **20 records per record set** for record types that allow multiple values, such as A, AAAA, MX, and TXT. A record set cannot contain two identical records. This limit supports scenarios like round-robin DNS load balancing across multiple IP addresses.

---

### Q21
You have an Azure DNS zone for `contoso.com`. You need to configure the **zone apex** (`contoso.com`) to resolve to an Azure Traffic Manager profile. What type of DNS record should you create?

- A. CNAME record
- B. A record with a static IP
- C. Alias (A/AAAA) record pointing to the Traffic Manager profile
- D. NS record pointing to Traffic Manager

**Answer: C**

**Explanation:** DNS standards do not allow a CNAME record at the zone apex (also called the root domain or naked domain) because a CNAME cannot coexist with other record types at the same label. Azure DNS solves this with **Alias records** — a special record type that can be placed at the apex and pointed directly to Azure resources such as Traffic Manager profiles, Azure CDN, or Azure Front Door. The record resolves dynamically to the current IPs of the target Azure resource.

---

### Q22
You deploy a VM that will act as a network virtual appliance (NVA) to inspect traffic between subnets. You create a route table with UDRs pointing to the NVA's private IP. Traffic that should pass through the NVA is being dropped. What is the most likely cause?

- A. The route table is not associated with the correct subnet
- B. IP forwarding is not enabled on the NVA's network interface in Azure
- C. The NVA VM is too small to handle the traffic load
- D. NSG rules on the NVA subnet are blocking forwarded traffic

**Answer: B**

**Explanation:** Azure drops packets sent to a VM's NIC that are addressed to a different destination IP unless **IP forwarding** is explicitly enabled on that NIC in Azure. By default, Azure's virtualization layer enforces that a NIC only accepts traffic addressed to its own IP. For an NVA that must forward traffic to other destinations, IP forwarding must be enabled on the Azure NIC resource (in addition to configuring IP forwarding at the OS level inside the VM).

---

## 4) Storage, SAS, and Blob

### Q23
A partner application needs temporary read and write access to blobs in a container. You must be able to revoke access quickly **without regenerating storage account keys**. Which approach should you use?

- A. Create an ad hoc Service SAS with a short expiry time
- B. Create a Service SAS associated with a stored access policy
- C. Create an Account SAS associated with a stored access policy
- D. Create a User delegation SAS with a stored access policy

**Answer: B**

**Explanation:** A **Service SAS linked to a stored access policy** is the only SAS type that can be revoked without regenerating the storage account key. Deleting or modifying the stored access policy immediately invalidates all SAS tokens associated with it. Ad hoc SAS tokens (with no stored policy) can only be invalidated by rotating the account key. Account SAS and User delegation SAS do not support stored access policies.

---

### Q24
You need to provide delegated access to blob storage resources with the highest security. Microsoft recommends which type of SAS for this scenario?

- A. Account SAS using the primary storage account key
- B. Service SAS with a stored access policy
- C. User delegation SAS
- D. Account SAS using a customer-managed key

**Answer: C**

**Explanation:** Microsoft recommends **User delegation SAS** as the most secure SAS type for blob operations. Unlike Service SAS and Account SAS (which are signed with the storage account key), a User delegation SAS is signed with Microsoft Entra credentials. It does not expose the account key, integrates with RBAC, and limits blast radius if compromised. Note: User delegation SAS does not support stored access policies — revocation is handled through Entra ID identity management.

---

### Q25
A storage account has both **blob versioning** and **blob soft delete** (14-day retention) enabled. A developer overwrites an existing blob named `report.pdf` with new content. What happens to the previous content?

- A. The previous content is permanently deleted
- B. A soft-deleted snapshot of the previous content is created
- C. A new previous version is automatically created containing the original content
- D. The blob is locked for 14 days and cannot be overwritten again

**Answer: C**

**Explanation:** When blob versioning is enabled, overwriting a blob automatically creates a **previous version** that preserves the original content. Soft-deleted snapshots are created on overwrites only when versioning is **disabled**. With versioning enabled, the versioning mechanism takes precedence for overwrites. Soft delete still applies when a blob is deleted, but not for overwrites when versioning is active.

---

### Q26
You have a storage account named `storage1` with the Microsoft.Storage service endpoint enabled on Subnet1. You add a VNet rule for Subnet1 in the storage account firewall. After configuring this, VM1 on Subnet1 still cannot access the storage account. What is the most likely missing step?

- A. Assign the Storage Blob Data Contributor role to the VM's managed identity
- B. Enable the Microsoft.Storage service endpoint on Subnet1
- C. Create a private endpoint for the storage account
- D. Allow the VM's public IP in the storage account firewall

**Answer: B**

**Explanation:** Adding a VNet rule to the storage account firewall requires the **Microsoft.Storage service endpoint** to be enabled on the specific subnet. Without the service endpoint enabled on the subnet, the VNet rule has no effect and traffic from the subnet is still treated as coming from a public IP. The service endpoint must be explicitly enabled on the subnet before the firewall rule takes effect.

---

### Q27
Your organization uses Azure Files with AD DS authentication. A developer reports that an application running under the **LocalSystem** context on a domain-joined VM cannot access an Azure file share. You need to enable access with minimum configuration effort. What should you configure for share-level permissions?

- A. Assign the Storage File Data SMB Share Contributor role directly to each computer account
- B. Configure a default share-level permission for all authenticated identities
- C. Create a security group containing the computer accounts and assign the Reader role
- D. Disable identity-based authentication and use storage account key authentication

**Answer: B**

**Explanation:** Applications running under the LocalSystem context authenticate to Azure Files using the VM's **computer account** (DOMAIN\MACHINENAME$). Computer accounts cannot be synced to Microsoft Entra ID, which means they cannot be directly assigned Azure RBAC roles. The correct approach is to configure a **default share-level permission** that applies to all authenticated identities — this covers computer accounts with minimum configuration overhead.

---

### Q28
You have an Azure storage account containing block blobs in the **Archive** tier. You want to use a lifecycle management policy to automatically rehydrate the blobs to the Hot tier after a certain period. What should you do?

- A. Configure the `tierToHot` action with `daysAfterModificationGreaterThan`
- B. Configure `tierToCool` first, then add a second rule for `tierToHot`
- C. Enable the `enableAutoTierToHotFromCool` property on the storage account
- D. Use a different method — lifecycle management policies cannot rehydrate blobs from the Archive tier

**Answer: D**

**Explanation:** Azure Storage lifecycle management policies can only transition blobs to **cooler** storage tiers (e.g., Hot → Cool → Archive). They cannot rehydrate (warm up) blobs from Archive to an online tier. To rehydrate archived blobs, you must use the **Set Blob Tier API**, the **Copy Blob** operation, or manually change the tier in the portal. There is no lifecycle policy action for Archive → Hot or Archive → Cool transitions.

---

## 5) Compute and App Service

### Q29
You have an Azure subscription that contains a metric alert rule configured as **stateful**. The CPU usage exceeds the threshold. The elevated CPU usage persists for three hours before returning to normal. How many times will the action group be triggered during this period?

- A. Once per evaluation interval (e.g., every 5 minutes)
- B. Three times — once per hour
- C. Once — when the alert fires and transitions to the Fired state
- D. Twice — once when it fires and once when it resolves

**Answer: C**

**Explanation:** A **stateful** metric alert tracks the current alert state (Fired or Resolved). When the threshold is first crossed, the alert fires and the action group is triggered once. While the alert remains in the Fired state (CPU stays above threshold), subsequent evaluations do not re-trigger the action group because the state has not changed. A second action group trigger occurs only when the alert resolves (CPU drops below threshold). A stateless alert, by contrast, would trigger the action group on every evaluation interval where the condition is met.

---

### Q30
You have a Windows virtual machine named VM1. You want to enable **Azure Disk Encryption** on VM1. You created an Azure Key Vault named KeyVault1 in the same region. Which Key Vault access policy setting must be enabled before you can configure Azure Disk Encryption?

- A. Enable access to Azure Resource Manager for template deployment
- B. Enable access to Azure Disk Encryption for volume encryption
- C. Enable soft delete and purge protection
- D. Enable the Azure Disk Encryption certificate permission

**Answer: B**

**Explanation:** Azure Disk Encryption requires the Key Vault to have the **"Azure Disk Encryption for volume encryption"** access policy option enabled. This setting grants the Azure platform access to the Key Vault's keys and secrets so that the encryption keys can be retrieved during VM boot and disk decryption. Without this setting, ADE cannot access the Key Vault and the encryption configuration will fail.

---

### Q31
You have an App Service web app named WebApp1 in the **Basic** pricing tier. You need to create a staging deployment slot. What should you do first?

- A. Enable deployment slots in the App Service configuration
- B. Add a new deployment slot from the Azure portal
- C. Scale up the App Service plan to the Standard tier or higher
- D. Create a new App Service plan in the Standard tier and move WebApp1

**Answer: C**

**Explanation:** Deployment slots are not available in the Free or Basic pricing tiers. You must upgrade to **Standard tier or higher** (Standard, Premium, or Isolated) before the deployment slots feature becomes available. After scaling up the plan, you can then add staging (and other) slots directly from the portal.

---

### Q32
You have a web app with a staging deployment slot. You plan to swap the staging slot into production. Before the swap completes, you want to validate the staging application running with **production settings**, with the option to cancel if validation fails. What should you use?

- A. Blue-Green deployment using Traffic Manager
- B. Swap with preview (multi-phase swap)
- C. Standard slot swap with rollback
- D. A/B testing with traffic routing percentages

**Answer: B**

**Explanation:** **Swap with preview** is a multi-phase swap that first applies production settings (connection strings, app settings marked as "slot setting") to the staging slot without routing any production traffic. This allows you to test whether the staging app functions correctly with production configuration before the swap is committed. If validation fails, you can cancel, and the staging slot reverts to its original settings with no disruption to production.

---

### Q33
You configure Azure App Service traffic routing to send **10% of production traffic** to the staging slot. A user is routed to the staging slot on their first request. How long will their session remain pinned to the staging slot?

- A. For the duration of the browser session
- B. Until the browser is closed
- C. One hour or until the routing cookie is deleted
- D. Permanently until manually reset

**Answer: C**

**Explanation:** After a user is routed to a deployment slot through traffic percentage routing, App Service sets the `x-ms-routing-name` cookie in the HTTP response. This cookie expires after **one hour**. While the cookie is present and valid, subsequent requests from that user will be routed to the same slot. If the browser deletes cookies or the cookie expires, the routing decision is made fresh on the next request.

---

### Q34
You need to configure a custom domain `www.contoso.com` with TLS/SSL on an App Service web app. The solution must support **multiple custom domains on the same shared IP address** and minimize costs. Which TLS/SSL binding type should you use?

- A. IP-based SSL
- B. SNI SSL
- C. Wildcard certificate binding
- D. Azure Front Door managed certificate

**Answer: B**

**Explanation:** **SNI SSL** (Server Name Indication) allows multiple TLS certificates to be hosted on a single shared IP address. The client indicates the target hostname during the TLS handshake, enabling the server to select the correct certificate. This is the standard and cost-effective approach for multiple custom domains. **IP-based SSL** requires a dedicated IP address per certificate, which is more expensive and limits scalability.

---

### Q35
You are deploying Azure Container Instances and need to run multiple containers in a single container group (sidecar pattern). Which operating system supports multi-container groups in Azure Container Instances?

- A. Windows only
- B. Linux only
- C. Both Linux and Windows
- D. Windows Server 2019 only

**Answer: B**

**Explanation:** Multi-container groups (container groups with more than one container) are supported **only on Linux** in Azure Container Instances. Windows containers in ACI are limited to single-container groups. Linux container groups can use the sidecar pattern to run a primary application container alongside helper containers (logging agents, proxies, etc.) that share the same network and storage.

---

### Q36
You have an Azure Container Registry named ACR1 in the **Standard** tier. You plan to deploy container images to AKS clusters in multiple Azure regions. You need to minimize image pull latency to all clusters. What should you do first?

- A. Create a separate ACR registry in each region
- B. Enable geo-replication on ACR1
- C. Upgrade ACR1 to the Premium tier
- D. Configure CDN distribution for the container registry

**Answer: C**

**Explanation:** Geo-replication (which distributes registry content to multiple regional replicas for low-latency pulls) is a feature available **exclusively on the Premium tier** of Azure Container Registry. Standard tier does not support geo-replication. Therefore, before you can configure geo-replication, you must first upgrade ACR1 to Premium tier.

---

## 6) Azure Bastion and Connectivity

### Q37
A contractor needs to connect to a virtual machine named VM1 in your VNet but does not have an Azure portal account. Azure Bastion is deployed using the **Standard SKU**. What should you configure to provide secure browser-based access?

- A. Create a new Azure AD guest account for the contractor
- B. Enable JIT VM access and share the RDP file
- C. Configure Azure Bastion Shareable Link for VM1
- D. Create a point-to-site VPN connection for the contractor

**Answer: C**

**Explanation:** Azure Bastion Standard SKU supports the **Shareable Link** feature, which generates a unique URL that allows a user to connect to a VM through a browser without needing an Azure portal account. The connection still goes through Bastion (no public IP on the VM is required), providing secure RDP/SSH access. Shareable Link is not available on the Basic SKU.

---

### Q38
Your compliance requirements mandate **session recording** for all RDP and SSH connections to virtual machines, with recordings stored in a customer-designated storage account. Which Azure Bastion SKU should you deploy?

- A. Basic
- B. Developer
- C. Standard
- D. Premium

**Answer: D**

**Explanation:** Session recording is available **only on the Azure Bastion Premium SKU**. When enabled, Bastion graphically records all RDP and SSH sessions and stores the recordings in a storage account designated by the customer (via SAS URL). The Standard SKU includes advanced features such as native client support, host scaling, and shareable links, but does NOT include session recording.

---

### Q39
You need to deploy Azure Bastion that supports **session recording** AND allows users to connect without a public IP address on the Bastion resource itself. Which SKU should you deploy?

- A. Basic SKU with private deployment enabled
- B. Standard SKU with private-only configuration
- C. Premium SKU
- D. Any SKU — all support private deployment and session recording

**Answer: C**

**Explanation:** Both session recording and private-only deployment (without a public IP on the Bastion host) are features exclusively available in the **Premium SKU**. No other SKU supports both capabilities simultaneously. Standard SKU supports shareable links and native client but not session recording or fully private deployment.

---

## 7) Kubernetes (AKS)

### Q40
You have an AKS cluster with a system node pool. You want to prevent application pods from being scheduled on the system node pool while allowing system pods to continue running there. Which taint should you apply to the system node pool?

- A. `node-role.kubernetes.io/system:NoExecute`
- B. `kubernetes.azure.com/mode=system:NoSchedule`
- C. `SystemOnly=true:PreferNoSchedule`
- D. `CriticalAddonsOnly=true:NoSchedule`

**Answer: D**

**Explanation:** `CriticalAddonsOnly=true:NoSchedule` is AKS's recommended taint for system node pools. System pods (such as coredns, kube-proxy, and other critical add-ons) are deployed with a matching toleration so they can still run on the system node pool. Regular application pods do not have this toleration, so they are prevented from being scheduled on the node. `NoExecute` would additionally evict existing pods, which is more aggressive than needed.

---

## 8) Bicep and ARM Templates

### Q41
You are creating a Bicep module named `storage.bicep` that deploys a storage account and has an output named `storageEndpoint`. In your main Bicep file, how should you access this output after calling the module as `stgModule`?

- A. `stgModule.storageEndpoint`
- B. `stgModule.outputs.storageEndpoint`
- C. `reference(stgModule).outputs.storageEndpoint.value`
- D. `stgModule.outputs.storageEndpoint.value`

**Answer: B**

**Explanation:** In Bicep, module outputs are accessed using the pattern `moduleName.outputs.outputName`. There is no `.value` suffix required — Bicep provides the typed value directly. This is different from ARM templates, where linked deployment outputs require `reference('deploymentName').outputs.outputName.value` with an explicit `.value` suffix.

---

### Q42
You have a main ARM template that deploys an App Service plan using a linked template named `appplan`. The linked template has an output named `planId`. You need to reference this output to set the `serverFarmId` property of an App Service. Which expression should you use?

- A. `"[parameters('planId')]"`
- B. `"[variables('appplan').outputs.planId]"`
- C. `"[reference('appplan').outputs.planId.value]"`
- D. `"[outputs('appplan').planId]"`

**Answer: C**

**Explanation:** To access an output from a linked deployment in an ARM template, use `reference('<deploymentName>').outputs.<outputName>.value`. The `.value` suffix is required in ARM JSON because outputs are objects with a `value` property. In Bicep, the equivalent would be `appplan.outputs.planId` without `.value`.

---

### Q43
You have a Bicep file with `param deployStorage bool = false`. You need to write an output that returns the storage account's blob endpoint when `deployStorage` is true, or an empty string when it is false. Which output declaration is correct?

- A. `output blobEndpoint string = if (deployStorage) storageAccount.properties.primaryEndpoints.blob`
- B. `output blobEndpoint string = when (deployStorage) storageAccount.properties.primaryEndpoints.blob else ''`
- C. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- D. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`

**Answer: D**

**Explanation:** In Bicep, the `if (...)` keyword is only used for conditional **resource and module deployments** (e.g., `resource x = if (condition) {...}`). For conditional **output values**, you must use the **ternary operator** (`condition ? trueValue : falseValue`). The null-coalescing operator `??` would not prevent a runtime error because accessing `storageAccount.properties` when the resource was not deployed would fail before the operator is evaluated.

---

### Q44
You need to deploy a resource group using Bicep and then deploy a storage account into that same resource group in a single Bicep file that targets the **subscription** scope. How should you structure the deployment?

- A. Set `targetScope = 'resourceGroup'` and include both the resource group and storage account in the same file
- B. Set `targetScope = 'subscription'`, create the resource group as a top-level resource, and use a module with `scope: resourceGroup('RGName')` for the storage account
- C. Create two separate files — one subscription-scoped for the resource group and one resource group-scoped for the storage account
- D. Use ARM template nesting instead of Bicep since Bicep does not support mixed scopes

**Answer: B**

**Explanation:** When a Bicep file targets the subscription scope (`targetScope = 'subscription'`), you can create a resource group as a top-level resource. To deploy resources at the resource group scope within the same file, use a **module** with `scope: resourceGroup('RGName')`. This scopes the module deployment to the named resource group while the parent file remains at the subscription scope. Both can coexist in a single deployment.

---

## 9) Monitoring and Azure Monitor

### Q45
You enable **VM Insights** for a virtual machine from the Azure portal using the default data collection rule settings. Which data is collected from the VM?

- A. Guest performance metrics, processes, and dependencies
- B. Host metrics, guest performance metrics, and Windows event logs
- C. Guest performance metrics only
- D. Guest performance metrics and Windows event logs

**Answer: C**

**Explanation:** When VM Insights is enabled with the default DCR (Data Collection Rule), only **guest OS performance metrics** (CPU, memory, disk, network) are collected. The processes and dependencies features (which power the VM Insights Map view) are **disabled by default** to reduce data ingestion costs. To enable them, you must create a new DCR or modify the existing one. Event logs are also not collected by the default VM Insights DCR.

---

### Q46
You are running a KQL query in Azure Log Analytics that calculates the count of events grouped by hour. You need to visualize the results as a **time-based line chart**. Which operator should you add at the end of the query?

- A. `| render barchart`
- B. `| render table`
- C. `| render piechart`
- D. `| render timechart`

**Answer: D**

**Explanation:** The `render timechart` operator in KQL renders query results as a time-series line chart, ideal for time-based aggregations. The typical pattern for hourly aggregation is: `| summarize count() by bin(TimeGenerated, 1h) | render timechart`. Other render options include `barchart`, `piechart`, `columnchart`, and `table` for different visualization types.

---

## 10) Azure Files, Backup, and Recovery

### Q47
You have a Windows Server joined to AD DS. You plan to enable AD DS authentication for Azure Files on a storage account named `storage1`. When you run the `Join-AzStorageAccountForAuth` command from the AzFilesHybrid module, what type of object is created in AD DS by default?

- A. User account
- B. Service principal
- C. Computer account
- D. Managed service account

**Answer: C**

**Explanation:** The AzFilesHybrid module's `Join-AzStorageAccountForAuth` command creates a **computer account** in AD DS by default to represent the storage account. This models the storage account as a domain-joined computer resource in AD. The computer account is used by Kerberos authentication so that AD-joined clients can authenticate to Azure file shares using their domain credentials.

---

### Q48
You have an Azure file share protected by Azure Backup using a **Snapshot tier** policy. A user accidentally deletes several individual files. You need to restore only the deleted files to the original location. What restore type and recovery destination should you select?

- A. Full Share Recovery with Original Location
- B. Full Share Recovery with Alternate Location
- C. File Recovery with Alternate Location
- D. File Recovery with Original Location

**Answer: D**

**Explanation:** For snapshot-based Azure Files backups, item-level (individual file) restore is supported. To restore only the deleted files (not the entire share), select **File Recovery**. To restore to the same location without moving data elsewhere, select **Original Location**. The combination of File Recovery + Original Location restores only the selected files to where they originally existed.

---

### Q49
You have an Azure storage account containing file shares. Some file shares use **SMB** and others use **NFS**. You need to configure Azure Backup for the file shares. Which file shares can be backed up?

- A. Only NFS file shares
- B. Only SMB file shares
- C. Both SMB and NFS file shares
- D. Neither — Azure Backup does not support Azure Files

**Answer: B**

**Explanation:** Azure Backup for Azure Files supports **SMB file shares only**. NFS file shares are not supported by Azure Backup. The backup mechanism for Azure Files uses SMB snapshot capabilities. If you need data protection for NFS shares, you must use alternative methods outside of Azure Backup.

---

### Q50
You trigger an on-demand backup of a virtual machine and specify a retention period of **60 days**. The current backup policy has a 30-day retention. One week later, you modify the policy to reduce retention to **14 days**. What happens to the on-demand backup recovery point?

- A. The on-demand recovery point is updated to 14 days to match the new policy
- B. The on-demand recovery point is retained for 60 days as originally specified
- C. The on-demand recovery point is immediately deleted when the policy changes
- D. The on-demand recovery point inherits the policy retention retroactively

**Answer: B**

**Explanation:** On-demand backup recovery points have an independent lifecycle from policy-based backups. The retention period specified at the time of the on-demand backup (60 days) is fixed for that recovery point. Subsequent changes to the backup policy (reducing retention to 14 days) only affect future **scheduled** recovery points created by the policy — they do not retroactively modify or delete existing on-demand recovery points.
