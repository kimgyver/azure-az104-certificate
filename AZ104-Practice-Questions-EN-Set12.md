# AZ-104 Practice Questions (Set 12) — English

---

## 1) Networking and Private Connectivity

### Q1
An NSG named NSG1 is associated with Subnet1 and has the following inbound rules:
- Rule1 (Priority 100): Allow TCP port 443 from 10.0.0.0/8
- Rule2 (Priority 200): Deny TCP port 443 from Any
- Rule3 (Priority 300): Allow TCP port 443 from Internet

A client at IP address 52.168.1.50 tries to reach a VM in Subnet1 on port 443. What is the result?

- A. Traffic is allowed by Rule1
- B. Traffic is allowed by Rule3
- C. Traffic is denied because Rule3 has a lower priority number
- D. Traffic is denied because Rule2 matches first and denies the connection

**Answer: D**

**Explanation:** NSG rules are evaluated in ascending priority order (lowest number first). Rule1 does not match — 52.168.1.50 is not in 10.0.0.0/8. Rule2 matches the "Any" source and denies port 443 traffic. Rules are evaluated in order and processing stops at the first match, so Rule3 is never reached.

---

### Q2
A Standard SKU internal load balancer has three backend VMs. Health probes are failing even though all VMs respond normally when accessed directly. The VMs have NSGs that deny all inbound traffic except specified sources. Which source IP address must be explicitly allowed inbound on the health probe port?

- A. 10.0.0.1
- B. 0.0.0.0
- C. 168.63.129.16
- D. 169.254.169.254

**Answer: C**

**Explanation:** Azure Load Balancer health probes originate from the Azure infrastructure virtual IP 168.63.129.16. If an NSG blocks inbound traffic from this address on the probe port, the probe fails and the backend is marked unhealthy even if the application is running correctly. This rule must be added explicitly.

---

### Q3
A network administrator in a different Azure subscription creates a private endpoint targeting your storage account storage1. The connection appears in storage1 with a state of **Pending**. What action must you perform?

- A. Add the external subscription to storage1's firewall allowed virtual networks
- B. Approve the private endpoint connection on storage1
- C. Enable the Private Link service on storage1
- D. Create a DNS A record for the private endpoint in the private DNS zone

**Answer: B**

**Explanation:** When a private endpoint is created from a different subscription, the target resource owner must manually approve the connection. The Pending state means the connection is awaiting approval. Navigate to storage1 → Networking → Private endpoint connections and select Approve.

---

### Q4
You create a private endpoint named pe-blob targeting the blob subresource of storage account storage1. During creation you select "Integrate with private DNS zone." Which private DNS zone is created?

- A. privatelink.core.windows.net
- B. privatelink.storage.azure.com
- C. privatelink.blob.core.windows.net
- D. blob.privatelink.azure.net

**Answer: C**

**Explanation:** Azure automatically creates the zone `privatelink.blob.core.windows.net` when integrating a blob private endpoint with a private DNS zone. This zone maps the storage account's public FQDN to the private endpoint IP address. Equivalent zones exist for other subresources: `.file`, `.queue`, `.table`.

---

### Q5
You need to configure Azure Firewall to allow outbound HTTPS traffic to all Windows Update endpoints. You want the configuration to require minimal ongoing maintenance. Which option should you choose?

- A. A network rule using the WindowsUpdate service tag
- B. An application rule with the WindowsUpdate FQDN tag
- C. A NAT rule forwarding port 443 to Windows Update IP addresses
- D. An application rule listing every Windows Update FQDN manually

**Answer: B**

**Explanation:** Azure Firewall FQDN tags represent Microsoft-managed groups of FQDNs for well-known services. The WindowsUpdate FQDN tag in an application rule automatically includes all Windows Update endpoints and is updated by Microsoft whenever endpoints change — eliminating manual maintenance.

---

### Q6
VNet1 is deployed in the East US region and contains Subnet1. RouteTable1 was created in the West US region. You need to associate RouteTable1 with Subnet1. What should you do first?

- A. Move VNet1 to the West US region using Azure Resource Mover
- B. Use the Azure CLI to force the route table association across regions
- C. Delete RouteTable1 and create a new route table in East US
- D. Change the region property of RouteTable1 in the Azure portal

**Answer: C**

**Explanation:** Route tables must be in the same Azure region as the virtual network subnet they are associated with. Azure does not support changing a route table's region after creation. The only solution is to delete RouteTable1 and create a new route table in East US, then associate it with Subnet1.

---

### Q7
A private DNS zone named private.contoso.com exists. Virtual machines deployed to VNet1 must have their A records automatically registered in the zone. VMs in VNet2 must be able to resolve records in the zone but must not register any records. What should you configure?

- A. Create a VNet link for VNet1 with autoregistration enabled, and a VNet link for VNet2 with autoregistration disabled
- B. Create VNet links for both VNet1 and VNet2 with autoregistration enabled
- C. Create a VNet link for VNet1 only; VNet2 does not need a link for resolution
- D. Configure a conditional forwarder in VNet2's DNS settings

**Answer: A**

**Explanation:** Azure Private DNS VNet links control both resolution and registration per VNet. Enabling autoregistration on the VNet1 link causes Azure to automatically create and delete A records when VMs are deployed or removed. Disabling autoregistration on the VNet2 link allows VMs in VNet2 to resolve records in the zone without being able to register new ones.

---

### Q8
A container group in Azure Container Instances contains Container1 (listens on port 80, publicly exposed) and Container2 (listens on port 8080, not externally exposed). How should Container1 send requests to Container2?

- A. Via localhost:8080 — all containers in the same group share a network namespace
- B. Via Container2's private IP address in the VNet
- C. Via the container group's public IP address on port 8080
- D. Via an Azure internal load balancer fronting both containers

**Answer: A**

**Explanation:** Containers within the same ACI container group share a network namespace, including the loopback interface. Container1 can reach Container2 using localhost:8080 (or 127.0.0.1:8080). No external routing is required. Only port 80 needs to be exposed externally; port 8080 remains internal.

---

### Q9
You deploy an Azure Container Instances container group into a virtual network subnet. The containers need to make outbound calls to a public REST API on the internet. What must you configure to enable internet access?

- A. Assign a public IP address to the container group
- B. Add an inbound NSG rule allowing port 443
- C. Attach a NAT gateway to the subnet or add a UDR with a default route pointing to the internet
- D. Enable the Microsoft.ContainerRegistry service endpoint on the subnet

**Answer: C**

**Explanation:** When ACI container groups are injected into a VNet, they lose automatic outbound internet connectivity. Internet access must be provided at the subnet level through a NAT gateway (for managed SNAT with a stable public IP) or a user-defined route with a next hop of Internet (0.0.0.0/0). Assigning a public IP to the container group is not supported in VNet-injected deployments.

---

### Q10
An Application Gateway named AppGW1 shows all backend pool members as unhealthy. Accessing the backend servers directly works correctly. You create a custom health probe with the correct URL path and port. Where must you associate the custom health probe for it to take effect?

- A. The listener
- B. The backend HTTP settings
- C. The routing rule
- D. The backend pool

**Answer: B**

**Explanation:** In Azure Application Gateway, health probes are associated with backend HTTP settings. The backend HTTP settings define the protocol, port, host header, and the health probe to use when evaluating backend server health. Associating a probe at any other level (listener, rule, or pool) has no effect.

---

## 2) Storage, SAS, and Lifecycle Management

### Q11
A blob container has a Service SAS linked to stored access policy named policy1. You need to immediately revoke all access granted by SAS tokens that reference policy1 without regenerating the storage account's access keys. What should you do?

- A. Delete the SAS token string from the application configuration
- B. Rotate the secondary access key on the storage account
- C. Change the container's public access level to Private
- D. Delete stored access policy policy1 from the container

**Answer: D**

**Explanation:** Deleting the stored access policy immediately invalidates all SAS tokens that reference it. Azure cannot validate the SAS signature without the backing policy, so access is denied instantly. Regenerating keys is not needed (and would break all other SAS tokens and applications using those keys).

---

### Q12
An Azure file share has been protected by Azure Backup using snapshot-based backups for several months. An on-demand backup fails with an error indicating the snapshot limit has been reached. What is the maximum number of snapshots per Azure file share?

- A. 100
- B. 500
- C. 200
- D. 1,000

**Answer: C**

**Explanation:** Azure file shares support a maximum of 200 snapshots per share. Both scheduled and on-demand backup snapshots count toward this limit. When the limit is reached, no new snapshots can be created. Old snapshots must be deleted, or the retention policy must be modified to stay within the limit.

---

### Q13
A storage account contains block blobs, append blobs, and page blobs. You configure a lifecycle management policy with tier-to-cool and tier-to-archive actions. Which blob types are eligible for these tiering actions?

- A. Block blobs and append blobs
- B. Block blobs only
- C. Append blobs only
- D. Page blobs only

**Answer: B**

**Explanation:** Azure Storage lifecycle management tiering actions (hot → cool → cold → archive) apply only to block blobs. Append blobs and page blobs are not supported for lifecycle tier transitions. Lifecycle management can still delete append blobs, but cannot tier them.

---

### Q14
Users upload files directly to an Azure file share via the Azure portal. The on-premises server registered with Azure File Sync is not receiving these changes for over 24 hours. What is the most direct way to trigger immediate synchronization?

- A. Restart the Storage Sync Agent service on the server
- B. Delete and recreate the server endpoint
- C. Increase the bandwidth allowance in the sync group
- D. Run the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet

**Answer: D**

**Explanation:** Azure File Sync detects changes made directly to the Azure file share (bypassing server endpoints) using a background change detection job that runs every 24 hours by default. Running `Invoke-AzStorageSyncChangeDetection` forces an immediate scan of the cloud endpoint and propagates changes to all registered server endpoints without waiting for the automatic cycle.

---

### Q15
A lifecycle management policy has two actions configured for block blobs with a last-modified age over 30 days: `tierToCool` and `delete`. Both conditions are met simultaneously for a blob. Which action does the lifecycle engine apply?

- A. tierToCool — tiering is always preferred before deletion
- B. Both actions are applied in parallel
- C. delete — lifecycle management applies the least expensive qualifying action
- D. The action defined first in the policy JSON takes precedence

**Answer: C**

**Explanation:** When multiple lifecycle actions qualify for the same blob simultaneously, Azure applies only the least expensive action. The cost priority order is: delete (least expensive) < tierToArchive < tierToCool < tierToCold. In this scenario, delete wins because eliminating storage costs entirely is cheaper than tiering.

---

### Q16
You manage a storage account where access keys must be rotated manually every 90 days. You need the Azure portal to display a reminder when a key exceeds this age without being rotated. What should you configure?

- A. An Azure Monitor alert rule on the key's last-used metric
- B. A key expiration policy on the storage account
- C. A lifecycle management policy on the storage account
- D. An action group targeting the storage account owner

**Answer: B**

**Explanation:** A key expiration policy (configured under the storage account's Access keys blade → Set rotation reminder) defines a maximum key age. When a key exceeds the specified number of days without rotation, Azure displays a reminder banner in the portal. This feature is purpose-built for manual key rotation workflows.

---

### Q17
An Azure file share is currently configured for Microsoft Entra Domain Services authentication. You need to switch to on-premises Active Directory Domain Services authentication. What must you do first?

- A. Disable Microsoft Entra Domain Services authentication on the storage account
- B. Enable on-premises AD DS authentication on the storage account
- C. Rejoin the storage account to the on-premises domain
- D. Regenerate the storage account access keys before making changes

**Answer: A**

**Explanation:** Azure Files supports only one active identity-based authentication source at a time. Attempting to enable on-premises AD DS authentication while Entra Domain Services is still configured will fail. The existing Entra Domain Services authentication must be disabled first before the new source can be enabled.

---

### Q18
A subnet has the Microsoft.Storage service endpoint enabled. VMs in the subnet can currently reach any Azure Storage account. You need to restrict outbound Storage service endpoint traffic to a single specific storage account to prevent data exfiltration. What should you configure?

- A. Add VNet firewall rules only on the allowed storage account
- B. Create an outbound NSG rule blocking the Storage service tag
- C. Apply a Service Endpoint Policy to the subnet that lists only the allowed storage account
- D. Replace the service endpoint with a private endpoint for the allowed storage account

**Answer: C**

**Explanation:** Service Endpoint Policies are applied at the subnet level and filter service endpoint traffic to only the Azure resource identifiers listed in the policy. VMs in the subnet are blocked from reaching any storage account not listed — directly addressing the data exfiltration scenario without replacing the existing service endpoint configuration.

---

### Q19
You need to create stored access policies for blob container container1. What is the maximum number of stored access policies you can configure for a single blob container?

- A. 10
- B. 5
- C. 20
- D. 25

**Answer: B**

**Explanation:** Azure Blob Storage supports a maximum of 5 stored access policies per container. Each policy can be referenced by multiple SAS tokens. If you need more than 5 policies, an existing policy must be deleted before a new one can be added.

---

### Q20
User1 has the Storage Account Contributor role at the storage account scope. User1 signs in to the Azure portal and opens a blob container to view blob data. Which authentication method does the Azure portal use to access the blob data?

- A. User Delegation SAS generated from User1's Entra ID token
- B. Microsoft Entra ID OAuth 2.0 token using User1's role assignment
- C. Shared Access Signature linked to a stored access policy
- D. Account access key via Shared Key authorization

**Answer: D**

**Explanation:** Storage Account Contributor includes `Microsoft.Storage/storageAccounts/listKeys/action`. When the Azure portal detects this permission, it retrieves the account access key and uses Shared Key authorization to access blob data — it does not use the user's Entra ID credentials. Microsoft Entra ID authentication is only used by the portal when the user lacks listKeys permission.

---

## 3) Compute and App Service

### Q21
A Linux VM has a Custom Script Extension that shows a failed status. You need to review the extension's execution logs to diagnose the failure. Which log file should you check?

- A. /var/log/syslog
- B. /var/log/azure/custom-script-extension/handler.log
- C. /var/log/waagent.log (waagent.log only — no other files needed)
- D. /etc/azure/extension-error.log

**Answer: B**

**Explanation:** The Custom Script Extension on Linux VMs writes detailed execution logs to `/var/log/azure/custom-script-extension/handler.log`. This file contains script download attempts, execution output, and error messages specific to the extension. While `/var/log/waagent.log` provides Azure VM Agent information, `handler.log` is the primary file for Custom Script Extension diagnostics.

---

### Q22
VM Insights is enabled on a Windows virtual machine. You navigate to the VM Insights Map tab but it shows no data. The Performance tab shows data correctly. Which additional component must be installed to enable the Map feature?

- A. Log Analytics Gateway
- B. Network Watcher Agent
- C. Dependency agent
- D. Azure Diagnostics extension

**Answer: C**

**Explanation:** The VM Insights Map feature requires the Dependency agent in addition to the Azure Monitor Agent. The Dependency agent collects process-level data and network connection information that powers the dependency map. Without it, performance metrics are collected correctly but the Map tab cannot display process and connection data.

---

### Q23
You plan to enable VM Insights on a virtual machine for performance monitoring (CPU, memory, disk, network). Which two components are the minimum requirements?

- A. Log Analytics workspace and Azure Monitor Agent
- B. Dependency agent and Log Analytics workspace
- C. Azure Monitor Agent and Diagnostics extension
- D. Log Analytics workspace and Network Watcher Agent

**Answer: A**

**Explanation:** VM Insights requires a Log Analytics workspace (to store InsightsMetrics and performance data) and the Azure Monitor Agent (to collect and deliver data to the workspace). The Dependency agent is required additionally only for the Map feature. For performance monitoring alone, Log Analytics workspace + Azure Monitor Agent is the minimum configuration.

---

### Q24
Availability set AS1 has 2 fault domains and 5 update domains. It contains 10 virtual machines, distributed evenly across all update domains. Microsoft performs planned maintenance requiring VM restarts. What is the maximum number of VMs in AS1 that can be unavailable simultaneously during the planned maintenance window?

- A. 5
- B. 2
- C. 10
- D. 1

**Answer: B**

**Explanation:** During planned maintenance, Azure restarts VMs one update domain at a time. With 10 VMs distributed evenly across 5 update domains, each update domain contains 2 VMs. Since only one update domain is restarted at a time, a maximum of 2 VMs can be unavailable simultaneously.

---

### Q25
You are writing an ARM template to deploy an availability set that will contain virtual machines using managed disks. Which property must you set to ensure the availability set aligns compute fault domains with managed disk storage fault domains?

- A. properties.managed = true
- B. properties.faultDomainCount = 3
- C. sku.name = Aligned
- D. sku.name = Classic

**Answer: C**

**Explanation:** For availability sets hosting VMs with managed disks, the `sku.name` property must be set to `Aligned`. This ensures that Azure aligns the compute fault domain with the underlying managed disk storage fault domain, providing proper fault isolation. `Classic` is the legacy value for availability sets using unmanaged disks.

---

### Q26
You create a new Azure VM backup policy using the Enhanced sub-type with a backup frequency of every 6 hours. What is the default retention period for instant recovery snapshots under this policy?

- A. 1 day
- B. 7 days
- C. 30 days
- D. 14 days

**Answer: B**

**Explanation:** With the Enhanced backup policy, the default instant recovery snapshot retention is 7 days regardless of backup frequency (2/4/6/8/12/24 hours). Instant recovery snapshots are stored locally to enable fast restores before data is transferred to the Recovery Services vault for long-term retention.

---

### Q27
You need to enable Azure Disk Encryption on a Windows VM named VM1 located in the East US region. Which resource must be in the same Azure region as VM1?

- A. The Recovery Services vault
- B. The Log Analytics workspace
- C. The Azure Key Vault that stores the encryption keys
- D. The storage account used for boot diagnostics

**Answer: C**

**Explanation:** Azure Disk Encryption requires the VM and the Azure Key Vault storing the encryption keys (BitLocker key for Windows) to be co-located in the same region and subscription. This requirement ensures data sovereignty (encryption keys do not cross regional boundaries) and minimizes latency for encryption operations.

---

### Q28
WebApp1 has a staging deployment slot. The staging slot's application setting named DatabaseConnection points to the staging database. When the staging slot is swapped to production, DatabaseConnection must stay with the staging slot and not move to production. What must you configure?

- A. Enable Auto Swap on the staging slot targeting production
- B. Store DatabaseConnection as an encrypted application setting
- C. Store DatabaseConnection in Azure Key Vault and reference it
- D. Mark DatabaseConnection as a deployment slot setting

**Answer: D**

**Explanation:** Deployment slot settings (sticky settings) remain bound to the specific slot they are configured on during swap operations. Marking DatabaseConnection as a deployment slot setting means the staging slot always uses its own DatabaseConnection value and production keeps its own separate value — neither changes during a swap.

---

### Q29
You want code deployed to the staging deployment slot to automatically replace the production slot after the application warms up successfully. What should you enable?

- A. Auto Swap on the staging slot targeting production
- B. Health check probes on the staging slot
- C. Continuous deployment from a source control repository
- D. Traffic routing percentage on the staging slot

**Answer: A**

**Explanation:** Auto Swap automates the swap process: when new code is deployed to the staging slot, Azure waits for the staging slot to warm up (begin responding to HTTP requests) and then automatically swaps it into production. Auto Swap is configured on the staging slot and is supported only on Windows App Service plans.

---

### Q30
A web app has multiple custom domains configured. You need to bind separate TLS/SSL certificates to each domain while sharing the same public IP address. Which SSL binding type should you use?

- A. IP-based SSL
- B. SNI SSL
- C. Wildcard SSL
- D. Mutual TLS authentication

**Answer: B**

**Explanation:** SNI SSL (Server Name Indication) allows multiple custom domains to share the same IP address with separate certificates. During the TLS handshake, the client sends the requested hostname, and the server selects the correct certificate. IP-based SSL binds one certificate to a dedicated IP address — requiring a separate IP per domain and incurring additional cost.

---

## 4) Identity, RBAC, and Governance

### Q31
You need to update the usage location to France (FR) for all users in the Accounting department using Microsoft Graph PowerShell. Which cmdlet combination should you use?

- A. Get-AzureADUser + Set-AzureADUser
- B. Get-MgGroup + Update-MgGroup
- C. Get-MgUser + Update-MgUser
- D. Get-MgUser + Set-MgUserUsageLocation

**Answer: C**

**Explanation:** Microsoft Graph PowerShell uses `Get-MgUser` with a `-Filter` for the department property to retrieve users, and `Update-MgUser` with the `-UsageLocation` parameter to update the property. The older AzureAD module cmdlets (Get-AzureADUser, Set-AzureADUser) are deprecated and replaced by the Microsoft Graph PowerShell SDK.

---

### Q32
A tenant has a Microsoft Entra ID Free license. You plan to create a dynamic membership group that automatically adds users based on the department attribute. What must you do before creating the group?

- A. Enable Entra ID B2C on the tenant
- B. Upgrade the tenant to Microsoft Entra ID Premium P1 or P2
- C. Create a static security group as a prerequisite
- D. Assign the Group Administrator role to the user creating the group

**Answer: B**

**Explanation:** Dynamic membership groups — where membership is automatically managed based on user attribute rules — require Microsoft Entra ID Premium P1 or P2. The Free tier supports only static (manually managed) group membership. The license must be provisioned before dynamic groups can be created.

---

### Q33
User1 has the Contributor role at the subscription scope and the Reader role on resource group RG1. What are User1's effective permissions on resources in RG1?

- A. Contributor — RBAC is additive; Contributor already includes all Reader permissions
- B. Reader — the narrowest scope (RG1) overrides the subscription-level assignment
- C. Reader — explicit resource group assignments always override inherited roles
- D. Owner — combining Contributor and Reader at different scopes escalates to Owner

**Answer: A**

**Explanation:** Azure RBAC uses an additive model: effective permissions are the union of all role assignments across all applicable scopes. Contributor at subscription scope inherits down to RG1 and already includes every permission that Reader provides. Adding Reader at RG1 has no incremental effect. The result is Contributor-level access at RG1.

---

### Q34
A CanNotDelete lock is applied to resource group RG1. A user with the Owner role at the subscription scope tries to delete a virtual machine inside RG1. What is the outcome?

- A. The delete succeeds because the Owner role overrides management locks
- B. The delete succeeds and generates a compliance alert
- C. The delete fails, but the user can remove the lock first and then delete
- D. The delete fails because management locks take precedence over all RBAC roles including Owner

**Answer: D**

**Explanation:** Azure management locks take precedence over RBAC permissions regardless of the role held. Even an Owner cannot delete a resource protected by a CanNotDelete lock while the lock is in place. However, the Owner role includes `Microsoft.Authorization/locks/*` permission, so the user can first remove the lock and then perform the deletion. Answer C also describes a valid sequence, but D correctly identifies the immediate outcome.

---

### Q35
Admin1 has the Contributor role on resource group RG1. Admin1 needs to apply a CanNotDelete lock to VM1 within RG1. What should you do?

- A. Assign the Security Admin role to Admin1
- B. Assign the User Access Administrator role to Admin1 at the VM1 scope
- C. Assign the Owner role to Admin1 at the RG1 scope
- D. Assign the Resource Policy Contributor role to Admin1

**Answer: C**

**Explanation:** Creating and deleting management locks requires `Microsoft.Authorization/locks/*` permission. The Contributor role does not include this permission. The built-in roles that include it are Owner and User Access Administrator. The exam-standard answer is to assign the Owner role at the RG1 scope.

---

### Q36
Management group MG1 contains subscriptions Sub1 and Sub2. User1 needs read access to all resources in both subscriptions. What is the minimum number of role assignments required?

- A. Assign Reader to User1 at the MG1 management group scope — one assignment
- B. Assign Reader to User1 separately on Sub1 and Sub2 — two assignments
- C. Assign Reader to User1 on each resource group in both subscriptions
- D. Assign the Global Reader role to User1 in Microsoft Entra ID

**Answer: A**

**Explanation:** Azure RBAC role assignments at a management group scope are inherited by all child subscriptions, resource groups, and resources. A single Reader assignment at MG1 covers both Sub1 and Sub2 with one assignment — the minimum possible. Assigning at the subscription level would require two separate assignments.

---

### Q37
You need to configure an Azure Policy that automatically adds a missing tag to resources at deployment time and can also remediate existing non-compliant resources through a remediation task. Which policy effect should you use?

- A. append
- B. modify
- C. deny
- D. deployIfNotExists

**Answer: B**

**Explanation:** The `modify` effect adds, updates, or removes resource properties (including tags) during resource creation or update, and supports creating remediation tasks to fix existing non-compliant resources. The `append` effect can add properties during creation but does not support remediation tasks. `deployIfNotExists` deploys additional resources, not tag modifications.

---

### Q38
You need to prevent users from deploying virtual machines that do not meet organizational compliance requirements. Non-compliant deployments must be blocked with an error. Which Azure Policy effect should you configure?

- A. audit
- B. auditIfNotExists
- C. deny
- D. modify

**Answer: C**

**Explanation:** The `deny` effect blocks resource creation and update requests that do not meet policy conditions. The deployment fails immediately with an error message. `audit` and `auditIfNotExists` only log non-compliance — they do not block deployments.

---

### Q39
A resource group has an `Environment = Production` tag. You configure an Azure Policy to add this tag to all resources in the group. Resources that already have an `Environment` tag with a different value (e.g., `Development`) must not be overwritten. Which modify operation type should you use?

- A. add
- B. addOrReplace
- C. remove
- D. copy

**Answer: A**

**Explanation:** The `add` modify operation adds the tag only when the tag is absent on the resource. If a resource already has the `Environment` tag with any value, the operation leaves it unchanged. `addOrReplace` would overwrite the existing tag value, violating the requirement not to override existing values.

---

### Q40
A subscription has a policy assignment with the `deny` effect. You want to evaluate which existing resources would be marked non-compliant without blocking any new deployments during the assessment period. What should you configure on the policy assignment?

- A. Temporarily change the effect from deny to audit
- B. Remove the policy assignment during testing
- C. Set a non-compliance message on the policy assignment
- D. Set the policy assignment's enforcement mode to Disabled

**Answer: D**

**Explanation:** Setting the enforcement mode to Disabled stops the policy effect (deny) from being applied to resource create/update operations while still evaluating and reporting compliance state. This is the correct approach for assessing impact before enforcement. Changing the effect (option A) modifies the policy definition itself, not just assignment behavior.

---

## 5) ARM Templates and Bicep

### Q41
You are writing a Bicep file that should deploy storage accounts only when parameter `createStorage` is `true`, using a loop for multiple instances. Which syntax is correct?

- A. `resource sa '...' = if(createStorage) [for i in range(0, count): { ... }]`
- B. `resource sa '...' = [for i in range(0, count): if(createStorage) { ... }]`
- C. `resource sa '...' = [for i in range(0, count)] if(createStorage) { ... }`
- D. `resource sa '...' = if(createStorage) { for i in range(0, count) { ... } }`

**Answer: B**

**Explanation:** In Bicep, when combining a loop with a condition, the `for` expression must come before the `if` condition: `[for ... : if(condition) { }]`. The reverse order (if before for) is a syntax error. When `createStorage` is false, no instances of the resource are deployed; when true, `count` instances are created.

---

### Q42
A main Bicep file calls a module named storageModule.bicep that declares an output named `storageEndpoint`. How do you reference this output value in the main file after the module declaration?

- A. storageModule.outputs.storageEndpoint
- B. storageModule.output('storageEndpoint')
- C. outputs['storageModule'].storageEndpoint
- D. reference(storageModule).storageEndpoint

**Answer: A**

**Explanation:** In Bicep, module outputs are referenced using the syntax `<moduleSymbolicName>.outputs.<outputName>`. The module symbolic name is the alias given in the module declaration (e.g., `module storageModule 'storageModule.bicep' = { ... }`). This is the standard pattern for consuming values produced by nested modules.

---

### Q43
A Bicep template has `param createNewStorage bool = true` and `param storageCount int = 3`. You want to deploy a storage account for each index only when `createNewStorage` is true. Which code correctly implements this?

- A. `resource sa '...' = if(createNewStorage) [for i in range(0, storageCount): { ... }]`
- B. `resource sa '...' = [for i in range(0, storageCount): if(createNewStorage) { ... }]`
- C. `resource sa '...' = [for i in range(0, storageCount): { condition: createNewStorage, ... }]`
- D. Options A and B are both valid syntax

**Answer: B**

**Explanation:** The correct Bicep syntax for a conditional loop places `for` before `if`: `[for i in range(0, storageCount): if(createNewStorage) { ... }]`. Placing `if` outside the `for` expression (option A) is invalid Bicep syntax. When `createNewStorage` is `false`, zero instances are deployed; when `true`, `storageCount` instances are deployed.

---

### Q44
You have a policy assignment with a deny effect in a production subscription. Before enforcing it, you set the enforcement mode to Disabled to assess impact. Which statement about the Disabled enforcement mode is correct?

- A. No compliance data is collected while enforcement is disabled
- B. The policy blocks new deployments but does not evaluate existing resources
- C. Compliance is evaluated and reported but resource operations are not blocked
- D. The Disabled mode makes the assignment completely inactive

**Answer: C**

**Explanation:** When enforcement mode is set to Disabled, the policy effect (e.g., deny, modify) is not applied to incoming resource create/update operations. However, Azure Policy continues to evaluate resources for compliance and surfaces the results in the Compliance dashboard. This allows teams to understand the impact of a policy before switching enforcement on.

---

## 6) Site Recovery, Monitoring, and AKS

### Q45
You configure an Azure Monitor action group to send email alerts to security@contoso.com. During a large alert storm, the security team reports that they stopped receiving notifications after the first several emails. What is the maximum number of email notifications Azure Monitor can send to a single email address per hour?

- A. 10
- B. 100
- C. 500
- D. 1,000

**Answer: B**

**Explanation:** Azure Monitor action groups apply rate limiting of 100 emails per hour per recipient address. When this limit is reached, subsequent emails are suppressed until the next hour. A rate-limiting notification is sent to the recipient informing them that the limit was exceeded. This is a known Azure-side constraint and is not configurable.

---

### Q46
Azure Monitor alerts are firing correctly. The security team reports that they occasionally receive no email notifications despite alerts being active. The action group configuration appears correct and alerts are confirmed in the portal. What is the most likely cause?

- A. The action group has exceeded the maximum supported number of email recipients
- B. An SMTP filter at the recipient's mail server is blocking Azure Monitor emails
- C. Azure Monitor has applied rate limiting to email notifications
- D. The alert rule evaluation frequency is too low to detect all events

**Answer: C**

**Explanation:** The pattern of intermittent notification failures during periods of high alert volume is a characteristic symptom of Azure Monitor's email rate limiting (100 emails/hour per recipient). When the limit is reached in a burst, subsequent notifications in that hour are dropped. The issue resolves automatically after the hour window resets.

---

### Q47
Virtual machines were failed over to a secondary Azure region using Azure Site Recovery. The failover has been committed. The primary region is now available and you want to begin replicating the VMs back. What should you do first?

- A. Perform the Re-protect operation on the VMs in the secondary region
- B. Create a new replication policy in the Recovery Services vault
- C. Initiate a planned failover from the secondary region back to the primary
- D. Restore the original VM configuration in the primary region manually

**Answer: A**

**Explanation:** After a failover is committed, replication in the reverse direction (secondary → primary) is not automatically configured. The Re-protect operation reconfigures Site Recovery to replicate from the secondary region back to the primary, establishing the reverse replication relationship. Re-protect must be completed before a failback (planned failover back to primary) can be initiated.

---

### Q48
A virtual machine was failed over to the secondary region and tested successfully. You commit the failover. What happens to the existing recovery points after the commit?

- A. Recovery points are retained for 30 days in the primary region
- B. Recovery points are moved to the secondary region automatically
- C. Recovery points remain intact and available for rollback
- D. All existing recovery points are deleted

**Answer: D**

**Explanation:** Committing a failover finalizes the operation and designates the secondary region as the permanent active location. Azure Site Recovery deletes all existing recovery points after commit because there is no longer a source to replicate from and rollback is not possible. This action is irreversible — once committed, recovery points are gone.

---

### Q49
An App Service web app has a system-assigned managed identity. The app uses Key Vault references to read secrets from an Azure Key Vault that is configured with the Azure RBAC permission model. What is the minimum role to assign to the managed identity?

- A. Key Vault Reader
- B. Key Vault Contributor
- C. Key Vault Secrets User
- D. Key Vault Administrator

**Answer: C**

**Explanation:** Key Vault Secrets User provides the `Microsoft.KeyVault/vaults/secrets/getSecret/action` permission — the minimum required to retrieve (read) secret values. Key Vault Reader provides only metadata access and cannot read secret contents. Key Vault Contributor is an ARM management-plane role and cannot access Key Vault data plane (secrets, keys, certificates).

---

### Q50
You have an AKS cluster with a single system node pool. You want to add a Spot node pool to run batch processing workloads that can tolerate interruptions. Which node pool mode must you use for the Spot node pool?

- A. System
- B. User
- C. Either system or user — Spot VMs can be used in both
- D. Spot mode is automatically assigned when you select Spot pricing

**Answer: B**

**Explanation:** AKS Spot node pools can only be created as user node pools. System node pools host critical cluster components (CoreDNS, metrics-server) that require high availability — Spot VMs (which can be preempted by Azure at any time) are incompatible with this stability requirement. Spot node pools must be secondary user pools, with at least one system node pool maintained for cluster stability.

---

*Document generated from AZ-104 course transcript. Last updated: 2026-05-22*
