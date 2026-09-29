# AZ-104 Practice Questions (Set 22) — English

## 1) Application Gateway, Routing, and Firewall

### Q1
You have an Azure Application Gateway v2 with a path-based routing rule. The URL path map contains three rules in this order: `/*`, `/images/*`, `/api/*`. Requests to `/api/v1/users` are being routed to the default backend pool instead of the API backend pool. What should you do to fix this?

- A. Change the Application Gateway SKU from v2 to v1
- B. Add a priority number to each path rule
- C. Reorder the path rules so that `/*` is listed last
- D. Create separate HTTP listeners for each path

**Answer: C**

**Explanation:** Application Gateway path rules are evaluated top-down in list order — the first matching rule wins. Since `/*` matches all paths, it captures every request before more specific rules like `/api/*` can be evaluated. Reordering the rules to place `/*` last (as a default/fallback) ensures specific paths like `/api/*` and `/images/*` are evaluated first. Unlike NSG rules, Application Gateway path rules do NOT use priority numbers — list order is the evaluation order.

---

### Q2
You need to configure path-based routing on an Azure Application Gateway so that requests to `/checkout` go to the Payments backend pool and all other requests go to the default backend pool. How does Application Gateway evaluate path rules?

- A. By priority number assigned to each rule (lower number = higher priority)
- B. By creation date (oldest rule evaluated first)
- C. In the order the rules are listed in the URL path map (top to bottom)
- D. Alphabetically by rule name

**Answer: C**

**Explanation:** Azure Application Gateway evaluates URL path map rules strictly in the order they appear in the list. The first matching path rule is applied and subsequent rules are not evaluated. This is different from NSG rules, which use priority numbers. For Application Gateway path-based routing, you must order rules from most specific to least specific, placing catch-all patterns like `/*` at the bottom of the list to act as a default.

---

### Q3
You have a parent Azure Firewall Policy named Parent-Policy with the threat intelligence mode set to "Alert." You create a child Firewall Policy named Child-Policy that inherits from Parent-Policy. Which threat intelligence mode options are available for Child-Policy?

- A. Off, Alert, and Alert and Deny
- B. Alert and Alert and Deny only
- C. Off only
- D. Alert only

**Answer: B**

**Explanation:** In Azure Firewall Policy inheritance, child policies can only configure threat intelligence mode to be equal to or stricter than the parent policy. If the parent is set to "Alert," the child can only be set to "Alert" (same) or "Alert and Deny" (stricter). The child cannot be set to "Off" because that would be less restrictive than the parent. This ensures that security settings cannot be weakened at lower levels in the policy hierarchy.

---

### Q4
You have an Azure Firewall configured to allow outbound internet traffic. You need to allow VMs to access Windows Update services without hardcoding Microsoft's frequently-changing IP addresses. What type of Azure Firewall rule should you use?

- A. Network rule with service tags
- B. Application rule with FQDN tags
- C. DNAT rule for outbound traffic
- D. Network rule with IP group

**Answer: B**

**Explanation:** Azure Firewall application rules support FQDN tags, which are Microsoft-managed collections of FQDNs for well-known services. The "WindowsUpdate" FQDN tag contains all domains required for Windows Update. Using FQDN tags eliminates the need to manually maintain lists of IP addresses or domain names since Microsoft automatically updates the tag content. Create an application rule in the firewall policy allowing HTTPS traffic to the WindowsUpdate FQDN tag.

---

### Q5
A virtual machine in a VNet has a system route to the internet for the prefix 0.0.0.0/0. An administrator adds a UDR (User Defined Route) to route 0.0.0.0/0 through an NVA. The admin also adds a BGP route advertised via ExpressRoute for the same prefix 0.0.0.0/0. Which route will be preferred for outbound internet traffic?

- A. The system route (automatically added by Azure)
- B. The BGP route (from ExpressRoute)
- C. The UDR (User Defined Route)
- D. The route with the most specific prefix wins

**Answer: C**

**Explanation:** Azure route priority when multiple routes exist for the same prefix is: UDR (User Defined Route) > BGP routes (from ExpressRoute or VPN with BGP) > System routes. A UDR always overrides both BGP-learned routes and system default routes for the same prefix. This allows administrators to force traffic through an NVA even when BGP advertises an alternative route. If there were no UDR, the BGP route would be preferred over the system route.

---

### Q6
Your organization wants all new Azure subscriptions to be automatically placed under a specific management group named MG-Compliance. Where should you configure this default management group setting?

- A. In the subscription's Properties blade
- B. In the Tenant Root Group's policy assignment
- C. In the Management Groups settings in the Azure Portal (Settings > Default management group)
- D. In the Azure Active Directory admin center under External Identities

**Answer: C**

**Explanation:** The default management group for new subscriptions is configured in the Azure Portal under Management Groups > Settings > Default management group. When a new subscription is created, Azure automatically places it under the tenant root group by default. By changing the default management group setting, you can ensure all new subscriptions are automatically placed under a specific management group (such as MG-Compliance), allowing policies and RBAC assignments on that group to immediately apply to new subscriptions.

---

### Q7
You need to explain the difference between Azure RBAC and Azure Policy to your team. User1 has been blocked from creating virtual machines in an Azure subscription, but User1 has the Contributor role. What is the most likely cause?

- A. User1's Contributor role is not inherited to the subscription scope
- B. An Azure Policy with the Deny effect is blocking VM creation
- C. The Contributor role does not include VM creation permissions
- D. User1 needs the Virtual Machine Contributor role specifically

**Answer: B**

**Explanation:** Azure RBAC controls WHO can perform actions (e.g., create VMs), while Azure Policy controls WHAT is allowed regardless of permissions (e.g., VMs can only be created in certain regions or with specific SKUs). A user with the Contributor role has the permission to create VMs (RBAC), but an Azure Policy with Deny effect can block the action even if the user has sufficient permissions. RBAC permissions and Policy effects are evaluated independently; Policy Deny can override RBAC Allow.

---

## 2) Managed Identities and Access Control

### Q8
You need to configure five Azure web applications to access the same Azure Storage account using managed identity. The role assignments must be configured before the web applications are deployed. Which type of managed identity is appropriate?

- A. System-assigned managed identity created for each web app
- B. User-assigned managed identity created and assigned to all five apps
- C. Service principal with certificate-based authentication
- D. Storage account shared access signature (SAS) stored in Key Vault

**Answer: B**

**Explanation:** User-assigned managed identity is an independent Azure resource that can be created, assigned role permissions, and then shared across multiple Azure resources. Creating a single user-assigned managed identity, assigning it the necessary RBAC roles on the storage account, and then assigning it to all five web apps ensures consistent access without managing separate credentials. System-assigned managed identity cannot be pre-configured independently and cannot be shared across resources. For scenarios requiring multiple resources to share identity and pre-deployment RBAC configuration, user-assigned managed identity is the correct choice.

---

### Q9
You have an AKS cluster that uses a single Linux system node pool. You need to add a Windows node pool. Beyond requiring the Standard SKU load balancer, which additional networking requirement must be met?

- A. The cluster must use kubenet networking
- B. The cluster must use Azure CNI networking
- C. The cluster must have a dedicated subnet for Windows nodes
- D. The cluster must use Azure CNI Overlay networking

**Answer: B**

**Explanation:** Windows node pools in AKS require Azure CNI (Container Network Interface) networking. Kubenet networking is not supported for Windows node pools. Azure CNI assigns each pod an IP address from the VNet subnet, while kubenet uses private IP addresses that are NATed to VNet IPs. Windows containers require Azure CNI for proper network communication. When creating an AKS cluster that will include Windows node pools, you must select Azure CNI at cluster creation time, as you cannot change the network plugin after deployment.

---

### Q10
You have a subnet with delegation to Microsoft.Web/serverFarms for Azure App Service. You want to create a Private Endpoint in the same subnet to connect to an Azure Storage account. What happens when you try to deploy the Private Endpoint?

- A. The Private Endpoint deploys successfully alongside App Service
- B. The Private Endpoint deployment fails because delegated subnets cannot have Private Endpoints
- C. The Private Endpoint deploys but requires an additional NSG rule
- D. The subnet must be resized before adding a Private Endpoint

**Answer: B**

**Explanation:** A subnet with a service delegation is exclusive to the delegated service type. Private Endpoints cannot be deployed into a delegated subnet. This is a fundamental constraint: delegated subnets are reserved for the specific service they are delegated to, and mixing other resource types (including Private Endpoints) is not permitted. To create a Private Endpoint for the storage account, you would need to use a different, non-delegated subnet within the same VNet.

---

### Q11
You have an Azure Storage account with Azure Files configured for SMB access. The storage account currently has Microsoft Entra Domain Services (Entra DS) authentication enabled for Azure Files. You need to switch to on-premises Active Directory Domain Services (AD DS) authentication. What must you do first?

- A. Enable AD DS authentication — both methods can coexist
- B. Disable the Microsoft Entra Domain Services authentication first, then enable AD DS
- C. Create a new storage account specifically for AD DS authentication
- D. Upgrade the storage account to Premium FileStorage tier

**Answer: B**

**Explanation:** Azure Files supports only one identity-based authentication method at a time. You cannot have both Microsoft Entra Domain Services and on-premises Active Directory Domain Services authentication active simultaneously on the same storage account. Before enabling AD DS authentication, you must first disable the existing Entra DS authentication. After disabling Entra DS, you can configure on-premises AD DS authentication, which requires joining the storage account to your on-premises AD domain and registering the computer account.

---

### Q12
Your organization needs to implement Privileged Identity Management (PIM) for just-in-time role assignments. Which Microsoft Entra ID license tier is required?

- A. Microsoft Entra ID Free
- B. Microsoft Entra ID Premium P1
- C. Microsoft Entra ID Premium P2
- D. Microsoft 365 E3

**Answer: C**

**Explanation:** Microsoft Entra ID Privileged Identity Management (PIM) requires Microsoft Entra ID Premium P2 license. PIM is not available in the Free or Premium P1 tiers. P2 includes all P1 features plus PIM, Identity Protection risk-based Conditional Access, and Access Reviews for privileged roles. If your organization only has P1 licenses, you can use time-limited role assignments via Conditional Access but not the full PIM feature set with activation workflows and audit logs.

---

### Q13
User1 is assigned the Contributor role at the subscription scope. User1 is also assigned the Reader role at a resource group (RG1) within the same subscription. What is User1's effective permission level on a virtual machine inside RG1?

- A. Reader permissions (the most restrictive assignment applies)
- B. No access (conflicting role assignments cancel each other)
- C. Contributor permissions (inherited from the subscription scope)
- D. The most recently assigned role applies

**Answer: C**

**Explanation:** Azure RBAC uses an additive model — the effective permissions are the union of all role assignments across all applicable scopes. The Contributor role at the subscription scope grants Contributor access to everything in the subscription, including all resource groups and resources within them. A Reader role at RG1 does not override the inherited Contributor from the subscription; instead, the effective permissions include both (union = Contributor). There is no "override" or "conflict resolution" in standard RBAC — permissions only add, never subtract (except deny assignments).

---

### Q14
You need to configure a Conditional Access policy that requires users to re-authenticate every 8 hours during an active session, even if their refresh token is still valid. Which Conditional Access condition or control should you configure?

- A. Sign-in risk condition with medium risk threshold
- B. Sign-in frequency session control (8 hours)
- C. Persistent browser session control set to "Never persistent"
- D. Device compliance condition

**Answer: B**

**Explanation:** Conditional Access Sign-in frequency allows you to specify how often users must re-authenticate during an active session. Setting it to 8 hours means that after 8 hours of activity, the user must provide their credentials again, regardless of whether their refresh token is still valid. This is commonly used to enforce periodic re-authentication for security-sensitive scenarios. Persistent browser session controls whether the user's browser keeps the session cookie across restarts but does not control re-authentication frequency.

---

## 3) Custom Roles, Storage, and Data Access

### Q15
You need to create a custom RBAC role that allows users to read data from Azure Blob Storage containers (listing and downloading blobs) but does not allow any management plane operations like creating or deleting storage accounts. Which section of the role definition should contain the blob read permissions?

- A. Actions
- B. DataActions
- C. NotActions
- D. NotDataActions

**Answer: B**

**Explanation:** Azure RBAC role definitions separate management plane permissions (Actions / NotActions) from data plane permissions (DataActions / NotDataActions). Reading blob data (listing blobs, downloading content) is a data plane operation and must be defined in the DataActions section. For example: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`. Management plane operations like creating storage accounts use the Actions section. Mixing them incorrectly results in either no access or excessive access to the data or management plane.

---

### Q16
You generate a Service SAS token to grant access to an Azure Storage blob. You want to restrict access so that the SAS token can only be used from your organization's corporate network. Your corporate network uses both public IP addresses (203.0.113.0/24) and private IP addresses (10.0.0.0/24) internally. What is the limitation when configuring IP restrictions on the SAS token?

- A. SAS tokens only support restricting to a single IP address, not ranges
- B. SAS tokens can only restrict to public IP addresses, not private IP addresses
- C. SAS tokens support both public and private IP ranges
- D. IP restrictions on SAS tokens require Azure Private Link

**Answer: B**

**Explanation:** SAS token IP restrictions only support public IP addresses. Private IP addresses (RFC 1918 ranges like 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16) are not supported for IP restriction in SAS tokens because Azure Storage cannot verify the source private IP of a request. If your corporate network uses private IPs, clients on those private IP ranges will not be able to use SAS tokens with private IP restrictions. The IP restriction should only include publicly routable IP addresses. For private network access, use Private Endpoints instead.

---

### Q17
You enable cloud tiering on an Azure File Sync server endpoint. A user reports that small configuration files (4 KiB) are not being tiered to the cloud even after the tiering policy threshold is met. What is the minimum file size that Azure File Sync cloud tiering will tier?

- A. 1 KiB
- B. 4 KiB
- C. 8 KiB
- D. 64 KiB

**Answer: C**

**Explanation:** Azure File Sync cloud tiering has a minimum file size of 8 KiB. Files smaller than 8 KiB will never be tiered to the cloud, regardless of how the tiering policy is configured. This minimum is calculated as 2 times the cluster size (with a floor of 8 KiB) because tiered files are stored as reparse points on the NTFS volume, and the reparse point itself occupies at least one cluster. Configuration files and other very small files below 8 KiB will always remain local.

---

### Q18
You have a blob in the Cool access tier that was uploaded 10 days ago. You change its tier to Archive. What is the early deletion penalty?

- A. No penalty; tier changes are always free
- B. You are charged for the remaining 20 days of the 30-day minimum Cool tier duration
- C. You are charged for the remaining 150 days of the 180-day Archive tier duration
- D. You pay a one-time deletion fee equal to the monthly storage cost

**Answer: B**

**Explanation:** The Cool access tier has a minimum storage duration of 30 days. If a blob is deleted or moved to another tier before 30 days have elapsed, an early deletion penalty is charged for the remaining days. Since the blob has been in Cool for 10 days and the minimum is 30 days, moving it to Archive after 10 days results in a charge for the remaining 20 days of the Cool minimum duration. The Archive tier has its own minimum of 180 days; early deletion from Archive results in a charge for the remaining Archive duration.

---

### Q19
You have an Azure Storage account of type GPv1 (General Purpose v1). You need to create a Private Endpoint to securely access the storage account from a VNet. What must you do before creating the Private Endpoint?

- A. Enable the hierarchical namespace on the storage account
- B. Upgrade the storage account from GPv1 to GPv2
- C. Add the VNet to the storage account firewall allowed networks
- D. Enable soft delete on the storage account

**Answer: B**

**Explanation:** Private Endpoints for Azure Storage require the storage account to be General Purpose v2 (GPv2) or a specialized storage type (Blob Storage, Data Lake Storage Gen2). GPv1 storage accounts do not support Private Endpoints. You must upgrade the GPv1 account to GPv2 before creating a Private Endpoint. The upgrade from GPv1 to GPv2 is a one-way operation that does not interrupt service or cause data loss, but it cannot be reverted.

---

### Q20
You have a storage account containing block blobs, append blobs, and page blobs. You configure a lifecycle management policy with a tiering rule to move blobs to the Archive tier after 90 days of no access. Which blob types will NOT be moved to the Archive tier by this policy?

- A. Block blobs only
- B. Append blobs and page blobs
- C. Page blobs only
- D. All blob types are moved

**Answer: B**

**Explanation:** Azure Blob Storage lifecycle management tiering rules (including moving to Archive tier) only apply to block blobs. Append blobs and page blobs do not support access tier changes through lifecycle management policies. Append blobs are designed for sequential append operations, and page blobs (used for VM disk images) cannot be tiered. Only block blobs can be transitioned between Hot, Cool, Cold, and Archive tiers via lifecycle management policies.

---

### Q21
You have an Azure Storage account with Geo-Redundant Storage (GRS) replication. What is the difference between GRS and Read-Access Geo-Redundant Storage (RA-GRS)?

- A. RA-GRS stores three copies in the primary region; GRS stores only one
- B. GRS provides read access to the secondary region; RA-GRS does not
- C. RA-GRS allows read access to the secondary region; GRS does not
- D. GRS supports zone-redundant storage in the primary region; RA-GRS does not

**Answer: C**

**Explanation:** Both GRS and RA-GRS replicate data to a secondary Azure region for disaster recovery. The key difference is that RA-GRS (Read-Access Geo-Redundant Storage) additionally provides read-only access to the secondary region endpoint, even when the primary region is available. GRS only allows you to read from the secondary region after Microsoft initiates a failover from the primary to the secondary region. RA-GRS enables applications to read from the secondary endpoint proactively for load distribution or disaster preparedness testing.

---

### Q22
You need to configure Azure Blob Storage to prevent all modifications and deletions to blobs for a regulatory compliance period of 7 years. Users should not be able to override or shorten this retention period. Which storage feature should you configure?

- A. Blob soft delete with 7-year retention
- B. Immutable Blob Storage with a time-based retention policy and locking enabled
- C. Azure Backup for blob storage
- D. Storage account resource lock (CanNotDelete)

**Answer: B**

**Explanation:** Azure Immutable Blob Storage provides WORM (Write Once, Read Many) protection using time-based retention policies. When a retention policy is created and locked, blobs cannot be deleted or modified until the retention period expires — even by administrators or account owners. Locking the policy makes it immutable and prevents shortening the retention period. This meets regulatory compliance requirements (e.g., SEC 17a-4, CFTC). Soft delete allows recovery but can be disabled. Resource locks prevent deletion but don't enforce write immutability.

---

## 4) Governance, Policy, and Bicep

### Q23
You need to permanently exclude a specific Azure resource from a policy assignment without creating additional policy assignments. You want the exclusion to be directly tied to the original policy assignment. Which approach should you use?

- A. Create a new policy assignment at the resource scope with the Audit effect
- B. Add the resource to the policy assignment's notScopes property
- C. Create a Policy Exemption resource targeting the specific resource
- D. Use the Disabled effect in a new policy definition

**Answer: B**

**Explanation:** The notScopes property on an Azure Policy assignment allows you to specify individual resources, resource groups, or subscriptions to be excluded from the policy evaluation. These exclusions are directly embedded in the existing policy assignment without requiring additional policy assignments or separate exemption resources. Adding a resource to notScopes is the simplest way to permanently exclude specific scopes. Policy Exemptions (option C) are separate Azure resources that also work but require creating and managing a separate exemption object.

---

### Q24
You want to create a free managed TLS/SSL certificate for an Azure App Service web app to use with a custom domain. You decide to use Azure App Service Managed Certificates. Which statement about App Service Managed Certificates is TRUE?

- A. Managed certificates support wildcard domains (e.g., *.contoso.com)
- B. Managed certificates must be stored in Azure Key Vault
- C. Managed certificates do not support wildcard domains and only support SNI-based SSL
- D. Managed certificates can be exported and used in other services

**Answer: C**

**Explanation:** Azure App Service Managed Certificates have several important limitations: (1) Wildcard certificates are NOT supported — each certificate is tied to a specific hostname. (2) Managed certificates only support SNI (Server Name Indication) SSL bindings, not IP-based SSL bindings. (3) Managed certificates cannot be exported — they are managed entirely by App Service. (4) Managed certificates auto-renew automatically without manual intervention. For wildcard domains, IP-based SSL, or certificate reuse, you must use a custom certificate uploaded to or stored in Key Vault.

---

### Q25
You write a Bicep file that should conditionally deploy an Azure Storage account based on a boolean parameter. Which syntax correctly implements conditional deployment in Bicep?

- A. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = if (deployStorage) { ... }`
- B. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = condition(deployStorage) { ... }`
- C. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = when (deployStorage == true) { ... }`
- D. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = [if deployStorage] { ... }`

**Answer: A**

**Explanation:** Bicep uses the `if` keyword for conditional resource deployment. The syntax is `resource <name> '<type>@<apiVersion>' = if (<condition>) { <resource body> }`. The condition is a boolean expression; if the condition is true, the resource is deployed; if false, the resource is not deployed. In contrast, ARM templates use a `"condition"` property within the resource object. Bicep's `if` syntax is cleaner and more readable than the equivalent ARM JSON approach.

---

### Q26
You have a Bicep file with a module named `stgModule` that deploys a storage account. The storage module has an output named `storageId` that returns the resource ID. How do you reference this output in the main Bicep file?

- A. `storageId`
- B. `stgModule.storageId`
- C. `stgModule.outputs.storageId`
- D. `outputs.stgModule.storageId`

**Answer: C**

**Explanation:** In Bicep, you access module outputs using the syntax `<moduleName>.outputs.<outputName>`. If the module is named `stgModule` and it has an output named `storageId`, you reference it as `stgModule.outputs.storageId`. You cannot access module outputs directly without the `.outputs.` accessor. This syntax makes it clear that you are accessing the module's exported output values, which are defined in the module's output declarations.

---

### Q27
You want to use a Bicep ternary operator to conditionally set the storage account SKU based on an `environment` parameter. If `environment` is 'prod', use 'Standard_GRS'; otherwise, use 'Standard_LRS'. Which Bicep expression is correct?

- A. `sku: (environment == 'prod') ? 'Standard_GRS' : 'Standard_LRS'`
- B. `sku: if(environment == 'prod', 'Standard_GRS', 'Standard_LRS')`
- C. `sku: environment == 'prod' select 'Standard_GRS' else 'Standard_LRS'`
- D. `sku: environment.equals('prod') ? 'Standard_GRS' : 'Standard_LRS'`

**Answer: A**

**Explanation:** Bicep supports the ternary operator using the syntax `condition ? trueValue : falseValue`. The parentheses around the condition are optional but improve readability. The expression `(environment == 'prod') ? 'Standard_GRS' : 'Standard_LRS'` evaluates to 'Standard_GRS' when the environment parameter is 'prod' and 'Standard_LRS' otherwise. This is equivalent to a conditional expression and is commonly used for environment-specific configurations in Bicep templates.

---

### Q28
You have a set of Azure policies that collectively enforce your organization's compliance standards (region restriction, mandatory tags, allowed VM SKUs, required diagnostic settings). Instead of assigning each policy individually to multiple scopes, what should you use to simplify management?

- A. A nested policy definition
- B. A policy set definition (initiative)
- C. A management group-level policy with multiple conditions
- D. A single policy with multiple rule conditions

**Answer: B**

**Explanation:** An Azure Policy initiative (also called a policy set definition) groups multiple policy definitions into a single unit that can be assigned as one initiative. Instead of assigning five separate policies to multiple management groups and subscriptions, you create one initiative containing all five policies and assign the initiative once. This simplifies compliance monitoring (one compliance state for the initiative) and management (update policies in the initiative centrally). Initiatives are the recommended approach for managing related policies as a collection.

---

### Q29
You plan to create a management group hierarchy to organize your Azure subscriptions. What is the maximum number of levels of management groups you can create below the tenant root management group?

- A. 3 levels
- B. 4 levels
- C. 5 levels
- D. 6 levels

**Answer: D**

**Explanation:** Azure Management Groups support a maximum depth of 6 levels of management groups below the tenant root management group. The tenant root group itself is not counted in this limit, and subscriptions are also not counted as levels. This means you can have 6 levels of management groups organized in a hierarchy beneath the root. Microsoft recommends keeping hierarchies flat (3-4 levels) for manageability, but the hard limit supported by the platform is 6 levels below root.

---

### Q30
You need to deploy Azure infrastructure according to a blueprint that includes specific RBAC role assignments, policy assignments, ARM templates, and resource groups — all versioned and deployed together as a unit. Which Azure service should you use?

- A. Azure Policy initiatives
- B. Azure Resource Manager template specs
- C. Azure Blueprints
- D. Azure Deployment Stacks

**Answer: C**

**Explanation:** Azure Blueprints allows you to define a repeatable set of Azure resources and configurations (called artifacts) including ARM templates, policy assignments, RBAC role assignments, and resource groups — all packaged together as a versioned blueprint. Blueprints can be assigned to subscriptions, deploying all artifacts in a coordinated manner. Unlike ARM templates, Blueprints maintain a relationship between the blueprint and the deployed resources (locking) and support versioning for change tracking. Note: Azure Blueprints is being deprecated in favor of Azure Deployment Stacks and Template Specs for new deployments.

---

## 5) Networking Deep Dive

### Q31
You need to compare Azure Private Link and Azure Service Endpoints. Which statement accurately describes a key difference?

- A. Service Endpoints provide a private IP address in the VNet; Private Link uses public IPs
- B. Private Link provides a private IP address in the VNet (Private Endpoint); Service Endpoints route traffic through Azure backbone but the service retains its public endpoint
- C. Service Endpoints support cross-region connectivity; Private Link does not
- D. Both Private Link and Service Endpoints use the same DNS configuration

**Answer: B**

**Explanation:** The key difference is IP addressing: Private Endpoints (Private Link) create a NIC with a private IP address inside your VNet, making the service appear as an internal resource. The service's public endpoint can be disabled entirely. Service Endpoints route traffic from VNet subnets to Azure services over the Azure backbone, but the service still uses its public IP endpoint — the traffic just doesn't go over the internet. For highest security, Private Link is preferred because it eliminates public exposure entirely.

---

### Q32
You create a Private Endpoint for Azure Files in a storage account named storage1. You enable private DNS zone integration. Which private DNS zone is created for the Azure Files private endpoint?

- A. privatelink.file.core.windows.net
- B. privatelink.files.core.windows.net
- C. privatelink.storage.core.windows.net
- D. privatelink.smb.core.windows.net

**Answer: A**

**Explanation:** When creating a Private Endpoint for Azure Files (the `file` subresource) with private DNS zone integration, the zone privatelink.file.core.windows.net is automatically created. An A record is added mapping storage1.file.core.windows.net to the private endpoint's private IP address. This enables DNS resolution to return the private IP instead of the public IP when accessed from within the VNet. Each storage subresource has a dedicated zone: file → privatelink.file.core.windows.net, blob → privatelink.blob.core.windows.net.

---

### Q33
VNet1 and VNet2 are connected via VNet peering. VNet1 has a VPN Gateway with on-premises connectivity. You need resources in VNet2 to reach the on-premises network using VNet1's gateway. You enable "Allow gateway transit" on the VNet1 peering. What must you also configure on the VNet2 peering?

- A. "Allow forwarded traffic"
- B. "Use remote gateways"
- C. "Allow virtual network access"
- D. "Allow gateway transit"

**Answer: B**

**Explanation:** Gateway transit requires configuration on both sides of the peering. On VNet1's peering settings (pointing to VNet2): enable "Allow gateway transit" — this allows VNet1 to share its gateway. On VNet2's peering settings (pointing to VNet1): enable "Use remote gateways" — this tells VNet2 to use VNet1's gateway for routing traffic to on-premises. Both settings must be enabled together for gateway transit to work. "Allow forwarded traffic" controls whether traffic forwarded from other networks (not originally from the peered VNet) is accepted.

---

### Q34
You have an Azure ExpressRoute circuit. You need to allow Azure virtual machines to connect directly to Office 365 services and other Microsoft cloud services over the ExpressRoute circuit. Which ExpressRoute peering type supports this?

- A. Private peering
- B. Microsoft peering
- C. Public peering (deprecated)
- D. Internet peering

**Answer: B**

**Explanation:** ExpressRoute Microsoft peering provides connectivity to Microsoft cloud services including Office 365, Dynamics 365, Azure public services (Storage, SQL Database), and other Microsoft SaaS services. Traffic flows through Microsoft's network directly from on-premises to these services. ExpressRoute Private peering provides connectivity to Azure private IPs (VNet resources). Public peering was deprecated and merged into Microsoft peering. For connecting to Office 365 over ExpressRoute, Microsoft peering is required.

---

### Q35
You have Azure Bastion Standard SKU deployed. You want to enable native RDP/SSH client connectivity (using the native client on your workstation instead of the browser-based client). Which Azure Bastion SKU supports native client connectivity?

- A. Azure Bastion Basic SKU
- B. Azure Bastion Standard SKU only
- C. Both Basic and Standard SKUs
- D. Azure Bastion Developer SKU

**Answer: B**

**Explanation:** Native client support in Azure Bastion (using az network bastion ssh or az network bastion rdp commands, or the native Windows RDP client) is available only on the Standard SKU. The Basic SKU only provides browser-based RDP/SSH sessions through the Azure portal. The Standard SKU also includes additional features like custom port support, file transfer (upload/download), shareable links, and the ability to connect to VMs in peered VNets.

---

### Q36
You need to capture and analyze network traffic flows for VMs in a VNet for security and compliance auditing. You want to log which NSG rules are allowing or denying network traffic. Which Azure service should you configure?

- A. Azure Network Watcher - Connection Monitor
- B. Azure Network Watcher - NSG Flow Logs
- C. Azure Monitor - Network Insights
- D. Azure Firewall diagnostic logs

**Answer: B**

**Explanation:** NSG Flow Logs capture information about IP traffic flowing through Network Security Groups, recording which traffic was allowed or denied by each rule. Flow log data includes source/destination IPs, ports, protocols, the NSG rule applied, and whether traffic was allowed or denied. This data can be sent to a storage account and/or a Log Analytics workspace for analysis using Traffic Analytics. NSG Flow Logs are the appropriate tool for network traffic auditing and security compliance review of NSG rule effects.

---

### Q37
You have an Azure VNet with a private DNS zone named contoso.internal linked to it. You want Azure VMs registered in this VNet to automatically have DNS records created in the zone when they receive a private IP address. Which DNS zone setting must you enable?

- A. Query resolution
- B. Zone transfer
- C. Auto-registration (virtual network registration)
- D. Recursive resolution

**Answer: C**

**Explanation:** Azure Private DNS zones support auto-registration, which automatically creates DNS A records for VMs in a linked virtual network when they obtain a private IP address. This is enabled when linking a VNet to a private DNS zone by checking the "Enable auto-registration" option on the VNet link. When a VM is created in the linked VNet, its hostname and private IP are automatically registered in the private DNS zone. Only one VNet can have auto-registration enabled per private DNS zone.

---

### Q38
You have Azure Front Door configured as a global entry point for your web application. You need to enable a Web Application Firewall (WAF) to protect against common web exploits. What does a WAF policy in "Prevention mode" do differently from "Detection mode"?

- A. Prevention mode only logs detected threats; Detection mode blocks them
- B. Detection mode blocks all traffic matching rules; Prevention mode only audits
- C. Prevention mode blocks requests matching WAF rules; Detection mode only logs them
- D. Both modes behave identically but Prevention mode sends additional alerts

**Answer: C**

**Explanation:** Azure WAF policies have two modes: Detection mode logs detected threats (allows all traffic through while recording violations) and Prevention mode actively blocks requests that match WAF rules. In Detection mode, no traffic is blocked — it is used to understand what traffic patterns would be affected before enabling blocking. In Prevention mode, requests matching managed rule sets (such as OWASP Core Rule Set) or custom rules are blocked and a response code (usually 403) is returned to the client. The recommended approach is to start in Detection mode to tune rules, then switch to Prevention.

---

## 6) Compute and Containers

### Q39
You have an Azure Container Registry (ACR) named myRegistry. You need to allow an Azure Kubernetes Service (AKS) cluster to pull images from myRegistry without storing credentials. What is the recommended approach?

- A. Enable the ACR admin user and store the credentials in a Kubernetes secret
- B. Assign the AcrPull role to the AKS cluster's managed identity on the ACR
- C. Make the ACR publicly accessible and allow all IP addresses
- D. Generate a SAS token for the ACR and create a Kubernetes pull secret

**Answer: B**

**Explanation:** The recommended approach for AKS to pull from ACR is to assign the AcrPull role to the AKS cluster's kubelet managed identity on the ACR resource. This uses managed identity authentication without storing any credentials. Using the ACR admin user (option A) requires creating Kubernetes secrets with credentials that must be rotated manually. Using managed identity is more secure, automatically handles credential rotation, and follows the principle of no secrets in configuration. You can also use `az aks update --attach-acr` to configure this automatically.

---

### Q40
You are choosing between Azure Container Apps, Azure Container Instances (ACI), and Azure Kubernetes Service (AKS) for deploying containerized workloads. Which scenario is BEST suited for Azure Container Instances?

- A. Running a complex microservices application with persistent state and high availability
- B. Running a batch processing job that starts on-demand and terminates when complete
- C. Hosting a globally distributed web application with automatic scaling
- D. Running Windows containers that require Active Directory integration

**Answer: B**

**Explanation:** Azure Container Instances (ACI) is best suited for on-demand, short-lived container workloads such as batch processing jobs, build processes, CI/CD runners, or event-driven tasks. ACI provides fast startup times with per-second billing and no cluster management overhead. For complex microservices with persistent state, AKS is more appropriate. For serverless, event-driven applications with automatic scaling, Azure Container Apps is designed. ACI is the simplest option for running containers without managing infrastructure, ideal for batch and task workloads.

---

### Q41
You have a Virtual Machine Scale Set (VMSS) for a web application. The VMSS uses an autoscale profile with a default instance count of 3. You need to ensure that the VMSS scales out to 10 instances every weekday from 8:00 AM to 6:00 PM, and returns to 3 instances at other times. Which autoscale profile type should you configure for the business hours scaling?

- A. Default profile with metric-based rules
- B. Recurrence (schedule-based) profile for specific time windows
- C. Fixed date profile for specific dates
- D. Custom metric alert profile

**Answer: B**

**Explanation:** VMSS autoscale supports three profile types: (1) Default profile — applies when no other profile matches, uses metric-based rules; (2) Recurrence profile — applies on a recurring schedule (e.g., every weekday 8:00-18:00), overrides the default; (3) Fixed date profile — applies for a specific non-repeating date/time range. For regular business-hours scaling that repeats daily on weekdays, configure a Recurrence profile specifying the days of the week and time window with the desired instance count of 10, and keep the default profile at 3 for off-hours.

---

### Q42
You need to deploy a short-lived, cost-effective VM for development and testing workloads that can tolerate interruptions. Which VM pricing option should you use?

- A. Reserved VM Instances (1-year commitment)
- B. Azure Spot VMs
- C. Burstable VMs (B-series)
- D. Dedicated Hosts

**Answer: B**

**Explanation:** Azure Spot VMs provide access to unused Azure capacity at significantly reduced prices (up to 90% discount compared to pay-as-you-go). Spot VMs can be evicted by Azure when the capacity is needed for other workloads or when the spot price exceeds your maximum price. They are ideal for fault-tolerant, interruptible workloads such as development/test environments, batch processing, and stateless applications. Reserved Instances require a 1-3 year commitment. Burstable VMs (B-series) are for workloads with variable CPU but are not interruptible.

---

### Q43
You are evaluating Azure Container Apps for hosting a stateless HTTP API. Which statement about Azure Container Apps scaling is correct?

- A. Container Apps can scale to zero instances, eliminating costs when there is no traffic
- B. Container Apps require a minimum of one running instance at all times
- C. Container Apps only scale based on CPU and memory metrics
- D. Container Apps require a dedicated AKS cluster for the runtime

**Answer: A**

**Explanation:** Azure Container Apps support scaling to zero — when there is no incoming traffic, Container Apps can reduce the number of replicas to zero, incurring no compute costs. When traffic arrives, Container Apps scales back up automatically. Container Apps uses KEDA (Kubernetes-based Event Driven Autoscaling) under the hood and supports scaling based on HTTP traffic, Azure Queue length, custom metrics, and more. This scale-to-zero capability makes Container Apps particularly cost-effective for APIs and services with variable or unpredictable traffic patterns.

---

### Q44
You have an Azure App Service with three deployment slots: production, staging, and QA. Which App Service plan tier is required to support THREE deployment slots?

- A. Basic (B1)
- B. Standard (S1)
- C. Premium (P1v2 or P2v3)
- D. Free (F1)

**Answer: C**

**Explanation:** The number of deployment slots available depends on the App Service plan tier. Basic tier: no deployment slots. Standard tier: up to 5 deployment slots. Premium tier: up to 20 deployment slots. For three deployment slots (production + staging + QA), Standard tier (S1) would be sufficient since it supports up to 5 slots. However, if the question specifies that you need more than 5 slots or additional features, Premium is required. Standard S1 supports up to 5 deployment slots. The answer depends on the exact count in the question; for 3 slots, Standard is the minimum required.

**Note:** The correct answer is Standard (S1) as it supports up to 5 deployment slots. The Premium tier is only required when you need more than 5 slots.

---

## 7) Monitoring, DR, and Security

### Q45
You have Azure Backup protecting VMs to a Recovery Services vault. You need to restore a VM to a different Azure region for testing purposes. The vault has Geo-Redundant Storage (GRS) replication configured. What must be enabled on the vault to support cross-region restore?

- A. Soft Delete on the vault
- B. Cross Region Restore feature on the vault
- C. Zone-Redundant Storage on the vault
- D. Enhanced Restore Policy

**Answer: B**

**Explanation:** Cross Region Restore (CRR) is a feature that must be explicitly enabled on a Recovery Services vault. When enabled on a GRS vault, it allows you to restore backed-up data to the secondary paired region for testing or DR scenarios. Without CRR enabled, GRS vaults only use the secondary region for automatic failover by Microsoft — you cannot manually restore to the secondary region. CRR is available for Azure VMs and SQL Server/SAP HANA in Azure VMs when the vault uses GRS replication.

---

### Q46
You configure Azure Site Recovery to replicate virtual machines. You want to control how often recovery points are created and how long they are retained. Where should you configure these settings?

- A. In the Recovery Services vault's backup policies
- B. In the replication policy associated with the replicated items
- C. In the VM's Azure Monitor diagnostic settings
- D. In the Recovery Plan configuration

**Answer: B**

**Explanation:** Azure Site Recovery replication policies define the frequency of crash-consistent and app-consistent recovery point snapshots and the retention period for recovery points. These are separate from Azure Backup policies. The replication policy is created in the Recovery Services vault and associated with the protected items during replication setup. You configure settings such as: crash-consistent recovery point frequency (every 5 minutes), app-consistent snapshot frequency (every hour), and recovery point retention (up to 72 hours for most scenarios).

---

### Q47
You want to understand your organization's overall security posture in Azure, including security recommendations from Azure Defender for Cloud. Which metric in Defender for Cloud provides an aggregated measure of your security posture?

- A. Compliance score
- B. Secure score
- C. Risk score
- D. Threat score

**Answer: B**

**Explanation:** Microsoft Defender for Cloud uses the Secure Score to measure your overall security posture. It calculates a percentage based on the security controls and recommendations that have been implemented vs. those that are still pending. Each security control has a maximum score value; implementing all recommendations in a control earns the full score for that control. A higher secure score indicates a more secure posture. The secure score is calculated across all subscriptions included in the Defender for Cloud scope.

---

### Q48
Azure Monitor Application Insights for your web application generates a large volume of telemetry. To reduce costs while retaining representative data for analysis, you configure sampling. Which sampling type automatically adjusts the sampling rate based on the volume of telemetry?

- A. Fixed-rate sampling
- B. Ingestion sampling
- C. Adaptive sampling
- D. Manual sampling

**Answer: C**

**Explanation:** Adaptive sampling in Application Insights automatically adjusts the sampling percentage to keep the telemetry volume within a target data rate. It monitors the volume of telemetry being generated and reduces the sampling rate when volume is high, and increases it when volume is low. This maintains a roughly constant telemetry throughput to the Application Insights resource. Fixed-rate sampling applies a constant sampling percentage. Ingestion sampling discards data after it reaches Application Insights (wasteful). Adaptive sampling is the default for the ASP.NET SDK.

---

### Q49
You want to receive recommendations for reducing Azure costs, improving security, and optimizing resource usage across your subscriptions in a single service. Which Azure service provides these multi-category recommendations?

- A. Azure Cost Management
- B. Microsoft Defender for Cloud
- C. Azure Advisor
- D. Azure Monitor

**Answer: C**

**Explanation:** Azure Advisor provides personalized recommendations across five categories: Cost (reduce spending), Security (improve security posture), Reliability (ensure availability), Operational Excellence (best practices), and Performance (improve speed/responsiveness). Advisor analyzes your resource configuration and usage, then provides actionable recommendations. Azure Cost Management focuses specifically on cost analysis and budgeting. Defender for Cloud focuses on security. Azure Monitor focuses on monitoring and alerting. Advisor is the comprehensive multi-domain recommendation service.

---

### Q50
You need to collect performance metrics and logs from Azure VMs and services into a centralized data collection pipeline. You want to use the latest agent-based data collection approach that replaces both the Log Analytics Agent (MMA) and the Diagnostics Extension. Which Azure Monitor feature should you configure?

- A. Log Analytics Agent (MMA/OMS agent)
- B. Azure Diagnostics Extension (WAD/LAD)
- C. Azure Monitor Agent (AMA) with Data Collection Rules (DCRs)
- D. OMS Gateway

**Answer: C**

**Explanation:** Azure Monitor Agent (AMA) is the latest, unified agent that replaces the legacy Log Analytics Agent (MMA/OMS) and Azure Diagnostics Extension. AMA uses Data Collection Rules (DCRs) to define what data to collect, from which sources, and where to send it. DCRs provide a centralized, version-controlled configuration for data collection across multiple VMs and services. AMA supports Windows and Linux, provides better security (managed identity), improved performance, and enables multi-homing (sending data to multiple destinations). Microsoft is retiring the legacy agents in favor of AMA.

---

*Document generated from AZ-104 course transcript. Last updated: 2026-05-24*
