# AZ-104 Practice Questions (Set 9) — English

## 1) Identity, Governance, and RBAC

### Q1

You have an Azure subscription. A user has the Contributor role at the subscription scope and the Reader role on a specific storage account within that subscription. What is the user's effective permission on that storage account?

- A. Reader, because the more restrictive role at the lower scope takes precedence
- B. Contributor, because RBAC permissions are additive and higher-scope permissions are not reduced by lower-scope assignments
- C. No access, because conflicting role assignments cancel each other
- D. Reader, because role assignments closer to the resource always win

**Answer: B**

**Explanation:** Azure RBAC permissions are additive. When a user has Contributor at the subscription scope and Reader at the resource scope, both sets of permissions apply. The effective permission is the union of all assignments, so the user retains Contributor-level access on that storage account. A lower-scope Reader assignment does not reduce a higher-scope Contributor.

---

### Q2

You need to create a custom RBAC role that allows a managed identity to read blob data from a storage account but prevents it from managing the storage account resource itself. Which permission property should you populate in the role definition?

- A. Actions
- B. NotActions
- C. DataActions
- D. NotDataActions

**Answer: C**

**Explanation:** Reading blob data is a data-plane operation. Azure RBAC separates control-plane permissions (Actions/NotActions) from data-plane permissions (DataActions/NotDataActions). To grant blob read access without granting storage account management rights, you must use `DataActions` with `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`.

---

### Q3

You have a custom RBAC role with the following definition:

```json
{
  "Actions": ["Microsoft.CostManagement/*"],
  "NotActions": ["Microsoft.CostManagement/exports/delete"]
}
```

A user is assigned only this role at the subscription scope. Which statement is correct?

- A. The user can perform all Cost Management actions including deleting exports, because wildcard Actions override NotActions
- B. The user can perform all Cost Management actions except deleting exports, because NotActions always override Actions
- C. The user cannot perform any Cost Management actions because NotActions blocks the entire namespace
- D. The user can delete exports because the wildcard in Actions is more specific than NotActions

**Answer: B**

**Explanation:** In Azure RBAC, `NotActions` always override `Actions`, even when a wildcard is used. The wildcard `Microsoft.CostManagement/*` grants all Cost Management permissions, but `NotActions: Microsoft.CostManagement/exports/delete` explicitly removes that one action. The result is full Cost Management access minus the ability to delete exports.

---

### Q4

You have a Microsoft Entra tenant. You need to create a group that automatically includes all devices running Windows 11 as members. Which type of group should you create?

- A. Microsoft 365 group with Dynamic User membership
- B. Security group with Assigned membership
- C. Security group with Dynamic Device membership
- D. Microsoft 365 group with Dynamic Device membership

**Answer: C**

**Explanation:** Dynamic Device membership is only supported by Security groups, not Microsoft 365 groups. Microsoft 365 groups can only contain users as members. To automatically include devices based on OS conditions, you must create a Security group and configure Dynamic Device membership with a rule such as `(device.deviceOSType -eq "Windows") -and (device.osVersion -startsWith "10.0.22")`.

---

### Q5

You plan to create dynamic membership groups in your Microsoft Entra tenant. The dynamic groups will include 2,000 unique users. What is the licensing requirement?

- A. Microsoft Entra ID Free licenses for all users
- B. Microsoft Entra ID P1 licenses for the administrators who manage the groups
- C. Microsoft Entra ID P1 licenses for all 2,000 unique users who are members of the dynamic groups
- D. Microsoft Entra ID P2 licenses for all 2,000 users

**Answer: C**

**Explanation:** Dynamic membership groups require a Microsoft Entra ID Premium P1 license for each unique user that is a member of one or more dynamic groups. With 2,000 users as members, you need at least 2,000 P1 licenses. It is the members who require licensing, not just the administrators. Devices in dynamic groups do not require licenses.

---

### Q6

You are creating a dynamic membership rule to include users from the Sales, Marketing, and Finance departments. You want to optimize the rule for efficient evaluation. Which operator should you use?

- A. `-match`
- B. `-like`
- C. `-contains`
- D. `-in`

**Answer: D**

**Explanation:** The `-in` operator allows you to specify multiple values for the same property in a single criterion and is the most efficient approach. Using multiple `-or` statements with `-eq` increases processing time and rule complexity. Microsoft explicitly recommends `-in` over multiple OR conditions: `user.department -in ["Sales", "Marketing", "Finance"]`. The `-match` and `-contains` operators use pattern matching and are less efficient for exact value comparisons.

---

### Q7

You are an Azure administrator with the Contributor role on subscription Sub1. Sub1 is under management group MG-Finance, where you have an inherited Owner role on the subscription. You need to move Sub1 to MG-IT, where you have an explicit Owner role. Can you perform this move?

- A. No, because your Owner role on Sub1 is inherited, not explicit
- B. No, because you need the Contributor role on the target management group
- C. Yes, because you have Owner on MG-IT and sufficient rights on the source group
- D. No, because only Global Administrators can move subscriptions between management groups

**Answer: C**

**Explanation:** To move a subscription between management groups, you need Owner or equivalent permissions on the target management group. You have an explicit Owner role on MG-IT, which satisfies the target requirement. The inherited Owner on Sub1 from MG-Finance provides sufficient source access. Inherited roles are valid Azure RBAC roles and count the same as explicitly assigned roles for this purpose.

---

## 2) Azure Policy and Governance

### Q8

You have a subscription-scoped Azure Policy that restricts resource deployments to the West US region. You need to permanently exclude a resource group named TestRG from this policy evaluation without creating additional policy assignments. What should you configure?

- A. Policy Exemption on TestRG
- B. `notScopes` on the policy assignment to include TestRG
- C. A new policy assignment at the TestRG scope with DoNotEnforce mode
- D. `resourceSelectors` filter on the policy assignment

**Answer: B**

**Explanation:** The `notScopes` property on a policy assignment permanently excludes specific resource containers from evaluation. By adding TestRG's resource ID to `notScopes`, that resource group is excluded without requiring any additional policy assignments. A Policy Exemption would create a separate resource object. `resourceSelectors` is used for conditional evaluation based on resource properties, not for permanent scope exclusion.

---

### Q9

You plan to create a custom Azure Policy definition that evaluates compliance based on whether resources have a specific tag. You need to ensure that resources not supporting tags are automatically excluded from evaluation. Which `mode` should you configure?

- A. All
- B. Indexed
- C. Microsoft.Kubernetes.Data
- D. Microsoft.Network.Data

**Answer: B**

**Explanation:** When `mode` is set to `Indexed`, the policy only evaluates resource types that support tags and location. Resources that do not support these properties are automatically excluded from evaluation. When `mode` is set to `All`, every resource type is evaluated, which could produce errors or noise for non-taggable resources.

---

### Q10

You want to apply 12 Azure Policy definitions together across all subscriptions in your tenant and pass the same parameter values to multiple policies in the group. What should you create?

- A. A custom policy definition with embedded sub-policies
- B. An Azure Policy assignment with multiple policy IDs
- C. An Azure Policy initiative definition (policy set)
- D. A management group with a built-in policy

**Answer: C**

**Explanation:** An Azure Policy initiative (also called a policy set definition) bundles multiple policy definitions together and allows a single assignment to apply all of them. It also supports shared parameters that can be passed to multiple policy definitions within the set, simplifying management. This is the recommended approach for enforcing a group of related compliance requirements together.

---

### Q11

You need to save a custom Azure Policy definition so that it can be assigned to resource groups across all subscriptions in your tenant. Where should you save the policy definition?

- A. At the subscription scope
- B. At the root management group scope
- C. At a resource group scope
- D. At any individual subscription within the tenant

**Answer: B**

**Explanation:** Policy definitions saved at the management group scope are available for assignment to any child scope, including all subscriptions and resource groups in the hierarchy. Saving at the root management group ensures the definition is accessible tenant-wide. Definitions saved at the subscription scope are only available within that subscription.

---

## 3) Storage, SAS, and Lifecycle Management

### Q12

You have a General Purpose v1 (GPv1) storage account named storage1. You need to create a private endpoint for storage1 in a virtual network. What should you do first?

- A. Enable Azure Defender for Storage on storage1
- B. Upgrade storage1 from GPv1 to GPv2
- C. Create a service endpoint on the subnet first
- D. Configure a firewall rule to allow private endpoint creation

**Answer: B**

**Explanation:** Azure Private Endpoints for storage accounts require a General Purpose v2 (GPv2) or Blob Storage account. GPv1 accounts do not support Private Endpoint creation. The first step is to upgrade storage1 to GPv2. The upgrade is non-destructive and preserves all existing data.

---

### Q13

You need to create a SAS token that restricts access to requests from a specific range of IP addresses. You configure the `allowedIPAddresses` parameter on the SAS token. Which IP address type is supported for this restriction?

- A. Private IP addresses from the virtual network
- B. Both public and private IP addresses
- C. Public IP addresses only
- D. IP addresses within the Azure backbone network only

**Answer: C**

**Explanation:** The IP address restriction on a SAS token only supports public, internet-routable IP addresses. Private IP addresses (10.x.x.x, 172.16.x.x, 192.168.x.x) cannot be used because the SAS IP restriction is evaluated at the public endpoint. The Azure Storage service sees requests arriving at its public endpoint and compares the client IP, which must be a public IP.

---

### Q14

You have a storage account with a lifecycle management policy that moves blobs to the archive tier after 90 days. The storage account contains both block blobs and page blobs. Which blobs will be transitioned to the archive tier by the policy?

- A. Both block blobs and page blobs
- B. Page blobs only
- C. Block blobs only
- D. Append blobs only

**Answer: C**

**Explanation:** Azure Lifecycle Management policies can only transition block blobs to the archive tier. Page blobs (used for VM disks and random read/write workloads) and append blobs do not support the archive tier. Only block blobs support the full tiering lifecycle: Hot → Cool → Cold → Archive.

---

### Q15

A blob was moved from the Cool tier to the Archive tier after only 15 days in the Cool tier. What is the consequence of this early move?

- A. The move is not allowed; blobs must stay in Cool for 30 days before archiving
- B. An early deletion penalty applies for the remaining 15 days of the Cool tier minimum retention
- C. No penalty applies; there is no minimum retention requirement in the Cool tier
- D. The blob is automatically returned to the Hot tier before archiving

**Answer: B**

**Explanation:** The Cool tier has a minimum recommended retention period of 30 days. If a blob is deleted or moved to another tier before 30 days, an early deletion penalty applies for the remaining days. In this case, the blob was moved after 15 days, so an early deletion charge applies for the remaining 15 days. The Archive tier also has a minimum retention of 180 days with the same penalty structure.

---

### Q16

You have a storage account with the `Access keys` blade open in the Azure portal. The `Set rotation reminder` button is grayed out. What must you do before you can create a key expiration policy?

- A. Enable Microsoft Defender for Storage
- B. Upgrade the storage account to the Premium performance tier
- C. Rotate each account access key at least once
- D. Enable Azure Key Vault integration

**Answer: C**

**Explanation:** Before a key expiration policy can be created, the `keyCreationTime` property must be populated for both access keys. When a storage account is first created, the keys exist but have never been rotated, so `keyCreationTime` is null. You must rotate each key at least once to establish the creation timestamp. After that, the key expiration policy feature becomes available.

---

### Q17

You need to grant a user the ability to read, write, and delete files in an Azure file share that uses AD DS authentication (SMB). The user must not be able to modify file or directory permissions. Which built-in role should you assign at the file share scope?

- A. Storage File Data SMB Share Reader
- B. Storage File Data SMB Share Contributor
- C. Storage File Data SMB Share Elevated Contributor
- D. Storage Blob Data Contributor

**Answer: B**

**Explanation:** The `Storage File Data SMB Share Contributor` role grants read, write, and delete permissions on SMB file share data but does not include the ability to change ACLs or modify permissions. The `Elevated Contributor` role adds permission management capabilities, which exceeds the requirements here. The `Reader` role is read-only, and `Storage Blob Data Contributor` applies to Blob storage, not File shares.

---

## 4) Networking: NSG, ASG, Load Balancer, and Routing

### Q18

An Azure NSG has the following inbound rules:
- Rule1: Priority 200, Source: 10.0.0.0/24, Port: 22, Action: Allow
- Rule2: Priority 100, Source: Any, Port: 22, Action: Deny

An administrator at IP address 10.0.0.5 attempts to connect to the VM over SSH. What is the result?

- A. Connection is allowed because 10.0.0.5 matches Rule1 (Allow)
- B. Connection is denied because Rule2 (Priority 100, Deny Any) is evaluated first
- C. Connection is allowed because the Allow rule always overrides Deny
- D. Connection is denied because there is no rule specifically for 10.0.0.5

**Answer: B**

**Explanation:** NSG rules are evaluated in ascending priority order (lower number = higher priority). Rule2 has priority 100 and denies all traffic on port 22 from Any source. Rule1 has priority 200 and allows 10.0.0.5, but it is evaluated after Rule2. Since Rule2 is matched first and the action is Deny, the connection is blocked. The administrator's IP 10.0.0.5 falls within the "Any" source of Rule2.

---

### Q19

You want to assign a new inbound NSG rule the highest possible priority value so it is evaluated before all other custom rules. Which priority value should you use?

- A. 1
- B. 50
- C. 100
- D. 4096

**Answer: C**

**Explanation:** NSG custom rule priority values must be between 100 and 4096. Lower numbers indicate higher priority (evaluated first). Therefore, 100 is the minimum allowed value and represents the highest possible priority for a custom rule. Values below 100 are not valid for custom rules. Values like 65500 are reserved for Azure's default system rules.

---

### Q20

You have an Azure virtual network with two subnets: Subnet1 and Subnet2. An Application Security Group (ASG) named ASG-Web already contains network interfaces from VMs in Subnet1. You plan to add a VM NIC from Subnet2 to ASG-Web. Will this work?

- A. No, because ASG members must all be in the same subnet
- B. Yes, as long as all NICs are in the same virtual network
- C. No, because ASGs cannot span more than 5 network interfaces
- D. Yes, but only if Subnet2 has a public IP assigned

**Answer: B**

**Explanation:** Application Security Groups are subnet-agnostic. Any NIC within the same virtual network can be added to an ASG, regardless of which subnet it belongs to. The only hard requirement is that all NICs in an ASG must reside in the same virtual network. ASGs cannot span across different virtual networks. There is no per-ASG NIC count limit of 5.

---

### Q21

You have an Azure Standard Load Balancer with a load balancing rule for port 80. You need to allow multiple rules to use the same backend port and preserve the original destination IP address for traffic sent to backend instances. What should you enable on the load balancing rule?

- A. Session persistence (source IP affinity)
- B. HA Ports
- C. Floating IP (Direct Server Return)
- D. Health probe protocol TCP

**Answer: C**

**Explanation:** Floating IP (also known as Direct Server Return or DSR) mode changes how the load balancer delivers traffic. When Floating IP is enabled, the destination IP in packets is not NAT'd to the backend IP — the original frontend IP is preserved. This also enables multiple load balancing rules to share the same backend port. It is commonly used for SQL Server Always On, NVAs, and similar scenarios.

---

### Q22

You have a virtual network with a subnet that has the following routes:
- System route: 10.0.0.0/16, Next hop: Virtual network
- User-defined route (UDR): 10.0.0.0/24, Next hop: Virtual appliance
- BGP route: 10.0.0.0/24, Next hop: Virtual network gateway

Traffic is sent to IP address 10.0.0.50. Which next hop is used?

- A. Virtual network (system route, /16)
- B. Virtual network gateway (BGP route, /24)
- C. Virtual appliance (UDR, /24)
- D. Azure selects the route randomly when multiple /24 routes conflict

**Answer: C**

**Explanation:** Azure route selection follows a strict priority order when multiple routes have the same prefix length. For /24 routes, User-Defined Routes (UDRs) take precedence over BGP routes and system routes. The UDR pointing to the virtual appliance wins over the BGP route with the same /24 prefix. Among routes with different prefix lengths (/16 vs /24), the more specific prefix (/24) is preferred regardless of route type.

---

### Q23

You have a virtual machine connected to a subnet with two NSGs: NSG1 is associated with the subnet, and NSG2 is associated with the VM's network interface. A user from the internet tries to connect to the VM on TCP port 443. Which condition must be met for the connection to succeed?

- A. Either NSG1 or NSG2 must allow the traffic (whichever is more permissive)
- B. Both NSG1 and NSG2 must have inbound rules allowing TCP 443 from the internet
- C. Only NSG2 (NIC level) needs to allow the traffic
- D. Only NSG1 (subnet level) needs to allow the traffic

**Answer: B**

**Explanation:** When both a subnet-level NSG and a NIC-level NSG are applied, inbound traffic must pass both filters. Azure does not use "most permissive wins" logic — both NSGs are evaluated independently. For inbound traffic, it first hits the subnet NSG (NSG1), then the NIC NSG (NSG2). If either one has a deny rule or lacks an allow rule, the connection is blocked.

---

## 5) Compute: VMs, App Service, and Containers

### Q24

You have an Azure App Service web app running on a Basic tier App Service plan. You want to implement a blue-green deployment strategy using deployment slots. What should you do first?

- A. Create a new deployment slot named staging directly in the Basic tier plan
- B. Enable Always On in the App Service settings
- C. Scale up the App Service plan to Standard or higher
- D. Configure a Traffic Manager profile to split traffic

**Answer: C**

**Explanation:** Deployment slots are not available on the Free or Basic App Service plan tiers. The Standard tier supports up to 5 deployment slots. To implement a blue-green strategy, you must first scale up to Standard (or higher). After scaling, you can create a staging slot, deploy there, and then perform a slot swap to promote to production.

---

### Q25

You have an Azure App Service web app on the Free (F1) pricing tier. You need to configure a custom domain and an SSL/TLS binding. What should you do first?

- A. Purchase an App Service managed certificate
- B. Scale up the App Service plan to Basic or higher
- C. Configure a CNAME record in your DNS provider
- D. Enable HTTPS Only in the TLS/SSL settings

**Answer: B**

**Explanation:** The Free (F1) tier does not support custom domains or SSL/TLS bindings. Both features require at minimum the Basic tier. The correct order is: (1) scale up the plan, (2) add the custom domain, (3) configure SSL binding. Purchasing a certificate or configuring DNS before scaling up will not resolve the tier limitation.

---

### Q26

You plan to secure a custom domain for an Azure App Service using the free App Service managed certificate. Which limitation applies to this certificate type?

- A. The certificate must be stored in Azure Key Vault before binding
- B. The certificate is valid for only 90 days and requires manual renewal
- C. Wildcard certificates are not supported
- D. The certificate can only be used with IP-based SSL bindings

**Answer: C**

**Explanation:** App Service managed certificates (free) do not support wildcard certificates (e.g., `*.contoso.com`). They only support specific hostname bindings. They are automatically renewed by the platform, so manual renewal is not required. They work with SNI SSL bindings (not just IP-based), and they do not need to be stored in Key Vault — the platform manages them entirely.

---

### Q27

You need to configure multiple custom domains on an Azure App Service and protect each with its own TLS/SSL certificate using a single IP address. Which SSL binding type should you use?

- A. IP-based SSL
- B. SNI SSL
- C. Mutual TLS
- D. Wildcard certificate binding

**Answer: B**

**Explanation:** SNI SSL (Server Name Indication) allows multiple SSL certificates to be served from the same IP address by using the hostname provided in the TLS handshake to select the correct certificate. IP-based SSL requires a dedicated public IP per certificate, making it impractical when multiple domains share an IP. SNI SSL is the default and recommended binding type in Azure App Service.

---

### Q28

You have an Azure Container Registry named ACR1 on the Basic SKU. You need to enable geo-replication. What should you run?

- A. `az acr replication create --registry ACR1 --location westus`
- B. `az acr update --name ACR1 --sku Standard`
- C. `az acr update --name ACR1 --sku Premium`
- D. `az acr create --name ACR1 --sku Premium --location westus`

**Answer: C**

**Explanation:** Geo-replication is only available in the Premium SKU for Azure Container Registry. To upgrade an existing registry from Basic to Premium, use `az acr update --name ACR1 --sku Premium`. The `az acr create` command creates a new registry rather than modifying an existing one. Replication commands can only be used after the SKU is already Premium.

---

### Q29

You need to deploy an Azure Container Instances container group to a VNet subnet so that containers can communicate with private resources in the virtual network. What must you configure before deploying the container group?

- A. Enable service endpoints on the subnet
- B. Delegate the subnet to `Microsoft.ContainerInstance/containerGroups`
- C. Create a private DNS zone for the container group
- D. Associate an NSG with the subnet before deployment

**Answer: B**

**Explanation:** Azure Container Instances (ACI) requires a subnet that is delegated to `Microsoft.ContainerInstance/containerGroups` before a container group can be deployed into it. Subnet delegation signals to Azure that this subnet is reserved for ACI, allowing Azure to manage networking for the containers. Without delegation, the deployment will fail. Service endpoints and NSGs alone are not sufficient.

---

## 6) Azure Site Recovery and Backup

### Q30

You perform a failover of a virtual machine in Azure Site Recovery (ASR) and validate the VM is working correctly. You then commit the failover. What is the primary outcome of committing the failover?

- A. Replication continues from the primary to the secondary region
- B. All recovery points available for the VM in Site Recovery are deleted
- C. The VM is automatically returned to the primary region
- D. A new recovery point is created and stored for 90 days

**Answer: B**

**Explanation:** Committing a failover in ASR finalizes the recovery point selection. After commit, all recovery points for that VM are deleted from Site Recovery. You can no longer use the "Change recovery point" option. The VM is now fully running in Azure (or the secondary region), and the commit action signals that you have accepted this state permanently. If you want to protect the VM again, you must re-protect it.

---

### Q31

After a regional outage, you perform a failover using Azure Site Recovery. The VMs are now running in the secondary region. Before you can start the reprotection process to replicate back to the primary region, what must the VM status be?

- A. Failover in progress
- B. Failover committed
- C. Replication healthy
- D. Recovery point selected

**Answer: B**

**Explanation:** Before you can initiate reprotection (reverse replication) in Azure Site Recovery, the VM's status must be `Failover committed`. This status indicates that the failover has been finalized and the current state of the VM has been accepted. If the failover has not been committed, the reprotect option is unavailable. After reprotection begins, the VM starts replicating back to the original primary region.

---

### Q32

You have an Azure file share protected by Azure Backup. You need to configure the backup policy to minimize data loss by scheduling backups multiple times per day. What is the maximum number of scheduled backups per day for an Azure file share?

- A. 24 (one per hour)
- B. 12
- C. 6
- D. 3

**Answer: C**

**Explanation:** Azure Backup for Azure Files supports a maximum of 6 scheduled backup snapshots per day, taken at intervals of no less than 4 hours each. Hourly backups are not available for file share backup policies. If 6 snapshots per day is insufficient, consider using Azure File Sync with a different backup approach.

---

### Q33

You have Azure virtual machines replicated to a secondary region using Azure Site Recovery. You perform a failover and the VMs run in the secondary region. To replicate back to the primary region, which condition must be verified before starting reprotection?

- A. A new Recovery Services vault must be created in the secondary region
- B. The cache storage account must be deleted and recreated
- C. The mobility agent must be manually reinstalled on the failed-over VMs
- D. The VM status must be Failover committed

**Answer: D**

**Explanation:** The reprotect option becomes available only after the failover has been committed (`Failover committed` status). The mobility agent auto-registers and does not require manual reinstallation. You do not need a new Recovery Services vault — the existing vault manages replication in both directions. ASR creates a new cache storage account in the target region automatically as part of the reprotection process.

---

## 7) Azure Monitor, Workbooks, and Service Health

### Q34

You are creating an Azure Monitor metric alert for a production virtual machine. You need to assign the highest severity level to indicate a critical issue requiring immediate attention. Which severity value should you select?

- A. 4
- B. 2
- C. 1
- D. 0

**Answer: D**

**Explanation:** In Azure Monitor alerts, severity ranges from 0 (Critical) to 4 (Verbose). Lower numbers represent higher severity. Severity 0 is the most critical level and is appropriate for issues requiring immediate attention. The scale is: 0 = Critical, 1 = Error, 2 = Warning, 3 = Informational, 4 = Verbose.

---

### Q35

You are designing an Azure Workbook that combines resource inventory data with performance metrics. You need to query resources and their metadata to build a custom report scope. Which data source should you use?

- A. Azure Monitor Logs
- B. Azure Monitor Metrics
- C. Azure Resource Graph
- D. JSON

**Answer: C**

**Explanation:** Azure Resource Graph is the correct data source for querying resource inventory and metadata in Azure Workbooks. It supports KQL queries against resource properties such as type, location, tags, and SKU. Azure Monitor Logs is for event and telemetry data, Metrics is for numeric time-series performance data, and JSON is for static dropdown values, not dynamic resource queries.

---

### Q36

You are reviewing past Azure service outages that affected your resources. You use the Health History section of Azure Service Health in the portal. How many days are inactive Service Health events retained in the Health History section?

- A. 30 days
- B. 60 days
- C. 90 days
- D. 180 days

**Answer: C**

**Explanation:** Azure Service Health retains inactive (resolved) events in the Health History section for 90 days. After 90 days, historical events are no longer visible in the portal. For longer-term audit requirements, you should configure Service Health alerts and export events to a Log Analytics workspace, Storage account, or Event Hub using Azure Monitor diagnostic settings.

---

### Q37

You have a production virtual machine with Premium SSD managed disks. The VM hosts a SQL Server database. One disk stores database files, and another stores transaction log files. Which host caching configuration is optimal for the transaction log disk?

- A. ReadOnly caching
- B. ReadWrite caching
- C. None (no caching)
- D. Automatic caching based on workload

**Answer: C**

**Explanation:** Transaction log disks should be configured with `None` (no caching) because transaction logs are sequential write operations. Read caching provides no benefit for write-heavy sequential workloads. ReadWrite caching can risk data loss in the event of a VM failure because the cache is not durable. Database file disks (for data files) benefit from ReadOnly caching since they have predictable read patterns.

---

## 8) Infrastructure as Code, Networking, and Miscellaneous

### Q38

You have an existing storage account named `storage1` deployed in the same resource group. You are writing a new Bicep template and need to reference `storage1` to retrieve its properties without recreating it. Which syntax should you use?

- A. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = { name: 'storage1' }`
- B. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' existing = { name: 'storage1' }`
- C. `var storage = resourceId('Microsoft.Storage/storageAccounts', 'storage1')`
- D. `import storage from 'storage1.bicep'`

**Answer: B**

**Explanation:** The `existing` keyword in Bicep is used to reference a resource that is already deployed without recreating it. Using `existing = { name: 'storage1' }` gives you a reference to the existing storage account so you can access its properties (e.g., `storage.properties.primaryEndpoints.blob`). Without `existing`, Bicep would attempt to create a new resource with that name, potentially causing a conflict.

---

### Q39

You are deploying an App Service Plan using a main ARM template that references a linked template in Azure Blob Storage. You need to configure the deployment mode for the linked template deployment resource. Which deployment mode can you use?

- A. Complete only
- B. Either Complete or Incremental
- C. Incremental only
- D. No mode setting is required for linked templates

**Answer: C**

**Explanation:** Linked templates in ARM (referenced with `templateLink`) can only use `Incremental` deployment mode. The `Complete` mode, which deletes resources not defined in the template, is not supported for linked templates. This restriction exists because linked templates operate within the scope of a parent deployment and are not considered complete representations of all resources in that scope.

---

### Q40

You have a Bicep module named `stgModule` that deploys a storage account and outputs the storage endpoint. You need to reference this output in the parent template. Which syntax is correct?

- A. `stgModule.outputs['storageEndpoint']`
- B. `stgModule.storageEndpoint`
- C. `output(stgModule, 'storageEndpoint')`
- D. `stgModule.outputs.storageEndpoint`

**Answer: D**

**Explanation:** To reference a module output in Bicep, use the syntax `moduleName.outputs.outputPropertyName`. In this case, `stgModule.outputs.storageEndpoint` retrieves the `storageEndpoint` output from the `stgModule` module. This follows the standard Bicep dot-notation access pattern for module outputs.

---

### Q41

You have a Bicep template that conditionally deploys a storage account. Which syntax correctly implements conditional resource deployment?

- A. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' when (deployStorage) = { ... }`
- B. `if (deployStorage) { resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = { ... } }`
- C. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) { ... }`
- D. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = deployStorage ? { ... } : null`

**Answer: C**

**Explanation:** In Bicep, conditional resource deployment uses the `if` expression placed after the `=` assignment operator: `resource storage '...' = if (deployStorage) { ... }`. This is different from imperative-style `if` blocks in other languages. The condition must evaluate to a boolean. When the condition is false, the resource is not deployed.

---

### Q42

You have an Application Gateway configured with a basic routing rule. You need to route traffic to different backend pools based on the URL path (e.g., `/images/*` to a separate backend). What must you configure?

- A. Replace the basic routing rule with a path-based routing rule using a URL path map
- B. Add a new listener for the `/images/` path
- C. Configure session affinity on the existing backend pool
- D. Add a rewrite rule to redirect `/images/` traffic

**Answer: A**

**Explanation:** To route traffic based on URL paths, you must use a path-based routing rule in Azure Application Gateway, which requires a URL Path Map. The URL Path Map defines path patterns (e.g., `/images/*`) and maps each to a specific backend pool and HTTP settings. A basic routing rule sends all traffic to a single backend pool regardless of URL path. A separate listener is not needed for URL-path-based routing.

---

### Q43

You need to configure Azure App Service VNet Integration so the app can reach private resources. You also plan to create a private endpoint for Azure SQL Database in the same VNet. You discover that both cannot be in the same subnet. Why?

- A. App Service VNet Integration requires an NSG, which conflicts with private endpoint policies
- B. Private endpoints require a dedicated subnet with a /28 or larger prefix that App Service cannot use
- C. App Service VNet Integration requires a delegated subnet, and private endpoints cannot be created in delegated subnets
- D. Azure limits each subnet to a single Azure service type and both count as separate services

**Answer: C**

**Explanation:** App Service VNet Integration requires subnet delegation to `Microsoft.Web/serverFarms`. Once a subnet is delegated, private endpoints cannot be created in that same subnet — subnet delegation and private endpoints are mutually exclusive. The solution is to create a separate non-delegated subnet for the private endpoint while keeping the delegated subnet for VNet Integration.

---

### Q44

You need to configure Service Health alerts for your Azure subscription with email and SMS notifications. When configuring the action group for Service Health alerts, which region must you select for the action group?

- A. East US (default Azure region)
- B. The same region as your most critical resources
- C. Global
- D. West US 2

**Answer: C**

**Explanation:** Action groups used for Azure Service Health alerts must be created in the `Global` region. Service Health is a global service and is not tied to any specific region. When creating or selecting an action group for Service Health alert rules, the region must be set to Global. Action groups in specific regions can be used for resource-specific metric and log alerts.

---

### Q45

You have a storage account with container soft delete enabled (14-day retention). You deleted `container1` and then created a new container with the same name. You need to restore the original `container1`. What should you do?

- A. Restore the soft-deleted container directly — it automatically overwrites the new container
- B. Delete the new `container1`, then restore the soft-deleted original `container1`
- C. Use Azure Backup to restore the container to the previous state
- D. Create a support ticket — same-name container restore is not supported

**Answer: B**

**Explanation:** Azure Blob container soft delete restore cannot overwrite an existing active container with the same name. The restore operation is a recreation of the deleted container object and will fail if the name is already in use. The correct procedure is to first delete the new `container1` (freeing the name), and then restore the soft-deleted original container. The restored container retains all original blobs.

---

### Q46

You have a Microsoft Entra tenant. You invite an external user (B2B guest) named User1. User1 has not yet redeemed the invitation. You need to check User1's invitation status programmatically. Which Microsoft Graph API user property should you query?

- A. `userType`
- B. `externalUserState`
- C. `accountEnabled`
- D. `identities`

**Answer: B**

**Explanation:** The `externalUserState` property on a user object in Microsoft Graph indicates the status of a B2B guest invitation. Possible values include `PendingAcceptance` (invitation sent but not redeemed) and `Accepted` (invitation redeemed). The `userType` property distinguishes `Guest` from `Member` but does not reflect invitation redemption status.

---

### Q47

You have an Azure Virtual Machine (VM1) in the East US region deployed without any availability options. You need to protect VM1 from datacenter-level failures by moving it to an availability zone. The solution must minimize administrative effort. What should you do?

- A. Delete VM1 and recreate it with the same managed disk in an availability set
- B. Use the Availability + scaling blade in the Azure portal to move VM1 to an availability zone
- C. Use Azure Resource Mover to move VM1 to a different region that supports availability zones
- D. Use Azure Backup to restore VM1 into an availability zone configuration

**Answer: B**

**Explanation:** Azure portal provides a guided experience in the `Availability + scaling` blade that allows you to move a single-instance VM from a regional (non-zonal) configuration to a zonal (availability zone) configuration within the same region. This approach minimizes administrative effort compared to manual VM deletion and recreation. Availability sets protect against rack-level failures within a datacenter, not datacenter-level failures, so they are not the correct choice.

---

### Q48

A user has the `Storage Blob Data Contributor` role assigned on a storage account. The user reports they can access blob data via API but cannot navigate to blob containers in the Azure portal. What should you do to fix this?

- A. Assign the `Storage Blob Data Owner` role instead of Contributor
- B. Assign the `Reader` role at the storage account scope
- C. Enable Azure RBAC for the storage account
- D. Configure the firewall to allow the user's public IP

**Answer: B**

**Explanation:** The `Storage Blob Data Contributor` role grants data-plane access (read, write, delete blobs) but does not grant management-plane access to view the storage account resource in the Azure portal. To navigate to containers in the portal, the user also needs the `Reader` role (or a similar ARM read role) at the storage account scope. The Reader role allows the portal to display the storage account resource tree, which is required before accessing blob data through the UI.

---

### Q49

You are configuring Self-Service Password Reset (SSPR) for users in your organization. User1 has the Global Administrator role. Which authentication method is User1 prohibited from using for SSPR?

- A. Microsoft Authenticator app notification
- B. Mobile phone (SMS)
- C. Email verification
- D. Security questions

**Answer: D**

**Explanation:** Microsoft prohibits Global Administrators (and other privileged administrator roles) from using security questions as an SSPR authentication method. This restriction is a security design decision: privileged accounts require stronger authentication methods. Global Admins must use at least two strong methods such as Authenticator app, phone, or email. Security questions are considered a weaker factor and are disabled for admin accounts.

---

### Q50

You have an Azure File Sync server endpoint where the NTFS volume uses the default 4 KiB cluster size. You enable cloud tiering. What is the minimum file size eligible for cloud tiering?

- A. 4 KiB
- B. 8 KiB
- C. 16 KiB
- D. 64 KiB

**Answer: B**

**Explanation:** The minimum file size eligible for cloud tiering in Azure File Sync is calculated as `max(2 × NTFS cluster size, 8 KiB)`. With a 4 KiB cluster size: 2 × 4 KiB = 8 KiB, which equals the 8 KiB absolute minimum floor. Files stored directly in the NTFS Master File Table (very small files) are not eligible for tiering regardless of policy settings.

---
