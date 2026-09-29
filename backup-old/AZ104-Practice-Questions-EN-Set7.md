# AZ-104 Practice Questions (Set 7) - English

This set was built by reviewing all actual question prompts in `expected_questiions/3-1.txt` and `expected_questiions/3-2.txt`, then removing duplicates while keeping the final correct answer for each unique item.

---

## 1) Governance, Identity, and RBAC

### Q1
You have an Azure Policy assignment that uses the `modify` effect to automatically add a required tag to storage accounts. After policy evaluation, several existing storage accounts are marked non-compliant. What must you configure to update the existing non-compliant storage accounts with the required tag?
- A. Reassign the policy and wait 24 hours
- B. Create a remediation task for the policy assignment
- C. Change the policy effect to `deny`
- D. Redeploy the storage accounts

**Answer: B**
**Explanation:** Existing resources are not automatically fixed by `modify`; you need a remediation task.

### Q2
You are creating a custom Azure Policy definition. The policy must check whether a related extension resource exists for each virtual machine. If the extension does not exist, the policy should mark the virtual machine as non-compliant without blocking deployments. Which effect should you use?
- A. Deny
- B. Modify
- C. AuditIfNotExists
- D. DeployIfNotExists

**Answer: C**
**Explanation:** `AuditIfNotExists` checks for a related resource and marks the resource non-compliant without blocking deployment.

### Q3
You have a Microsoft Entra tenant. You need to create a group that can be assigned Microsoft Entra administrator roles. The group must have members added and removed automatically based on the users' department attribute. Is this possible?
- A. Yes, by using a dynamic Security group
- B. Yes, by using a dynamic Microsoft 365 group
- C. No, because role-assignable groups must use Assigned membership
- D. No, because Entra roles cannot be assigned to groups

**Answer: C**
**Explanation:** Role-assignable groups cannot use dynamic membership.

### Q4
You have a shared Azure dashboard. You need to share it with users who can view it and make local edits, but cannot publish changes back to the server. Which Azure RBAC role should you assign?
- A. Contributor
- B. Reader
- C. Owner
- D. Monitoring Contributor

**Answer: B**
**Explanation:** Reader allows viewing and personal/local edits without allowing changes to the shared dashboard.

### Q5
You need to configure external collaboration settings so that only users with the User Administrator or Guest Inviter roles can invite guest users. Which Guest invite settings option should you select?
- A. Everyone including guests can invite
- B. Member users and users assigned to specific admin roles can invite guests
- C. Only users assigned to specific admin roles can invite guests
- D. No one can invite guests

**Answer: C**
**Explanation:** This option limits guest invitations to specific admin roles only.

### Q6
You need to create new users and manage their properties in Microsoft Entra ID using Azure PowerShell. You must use the least privileged Microsoft Entra role. Which role should you request?
- A. Global Administrator
- B. Privileged Role Administrator
- C. User Administrator
- D. Directory Writer

**Answer: C**
**Explanation:** User Administrator is the least-privileged role for creating users and managing user properties.

### Q7
A colleague needs to create new user accounts, reset user passwords, and manage user licenses in Microsoft Entra ID. What is the minimum required role?
- A. License Administrator
- B. Helpdesk Administrator
- C. User Administrator
- D. Global Administrator

**Answer: C**
**Explanation:** User Administrator covers all three tasks with least privilege.

### Q8
User1 has the Contributor role at RG1 scope and needs to create a CanNotDelete lock on RG1. What should you do?
- A. Remove Contributor first
- B. Use elevated permissions in the portal
- C. Assign Virtual Machine Contributor at subscription scope
- D. Assign User Access Administrator at RG1 scope

**Answer: D**
**Explanation:** Creating locks requires `Microsoft.Authorization/locks/*`; User Access Administrator is the least-privileged built-in role that includes it.

### Q9
User1 is assigned Reader at management group scope and Contributor at resource group scope. What is User1's effective permission level on resources in that resource group?
- A. Reader
- B. Contributor
- C. Owner
- D. No access

**Answer: B**
**Explanation:** Azure RBAC is additive; the Contributor assignment applies at the narrower scope.

### Q10
You are preparing a CSV file to bulk create users in Microsoft Entra ID. Which columns are required?
- A. User principal name, Initial password, Department, and Job title
- B. Name, User principal name, Initial password, Usage location
- C. Name, User principal name, Display name, and Initial password
- D. Name, User principal name, Initial password, and Block sign in

**Answer: D**
**Explanation:** Those four columns are the required ones in the bulk-create template.

### Q11
You need to prevent accidental deletion of resources in both a management group and a resource group. Which statement about resource locks is correct?
- A. You can lock the management group and the lock will be inherited
- B. You can lock the management group but not the resource group
- C. You must use Azure Policy instead of locks for both
- D. You can lock the resource group but not the management group

**Answer: D**
**Explanation:** Locks are supported at subscription, resource group, and resource scope, but not at management group scope.

---

## 2) Storage, Files, and Backup

### Q12
Microsoft recommends which type of SAS when you need delegated access to blob resources with the highest security?
- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Stored access SAS

**Answer: C**
**Explanation:** User delegation SAS is the most secure because it uses Microsoft Entra credentials.

### Q13
A partner application needs temporary read and write access to blobs in `container1`, and you must be able to revoke access quickly without regenerating account keys. Which approach should you use?
- A. Create an ad hoc service SAS
- B. Create a service SAS associated with a stored access policy
- C. Create an account SAS associated with a stored access policy
- D. Create a user delegation SAS associated with a stored access policy

**Answer: B**
**Explanation:** Stored access policies are supported only with service SAS and allow quick revocation without regenerating account keys.

### Q14
A third-party application needs temporary read access to specific blobs for 30 days. You want the most secure SAS-based delegation method. Which type of SAS should you use?
- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Shared key authentication

**Answer: C**
**Explanation:** User delegation SAS is the most secure SAS option.

### Q15
What type of object does AzFilesHybrid create in AD DS by default when joining a storage account for Azure Files AD DS authentication?
- A. User object
- B. Group object
- C. Computer object
- D. Service principal object

**Answer: C**
**Explanation:** It creates a computer object by default.

### Q16
Before enabling Azure Disk Encryption on a VM, which Key Vault access policy setting must you enable?
- A. Azure Resource Manager for template deployment
- B. Azure Disk Encryption for volume encryption
- C. Azure Backup for deployment
- D. Purge protection only

**Answer: B**
**Explanation:** Azure Disk Encryption requires that specific Key Vault setting.

### Q17
You need read and write SMB access for a security group to an Azure file share authenticated by Microsoft Entra Domain Services. Which built-in role should you assign at the file share scope?
- A. Storage File Data SMB Share Reader
- B. Storage File Data SMB Share Contributor
- C. Storage File Data SMB Share Elevated Contributor
- D. Reader

**Answer: B**
**Explanation:** That role provides read/write SMB access.

### Q18
An app running as LocalSystem on domain-joined VMs needs access to an Azure file share with AD DS authentication. What should you configure for share-level permissions with minimum effort?
- A. Assign Storage File Data SMB Share Contributor directly to each computer account
- B. Use storage account keys instead of identity-based auth
- C. Configure a default share-level permission for all authenticated identities
- D. Assign Reader at the storage account scope

**Answer: C**
**Explanation:** Computer accounts can't be directly granted Azure RBAC share permissions; default share-level permission is the minimum-effort solution.

### Q19
The backup policy for a protected Azure file share uses the Snapshot tier only. A user deletes several files and you must restore only those files to the original location. What restore type should you select?
- A. File Recovery with Original Location as the recovery destination
- B. Full Share Recovery with Alternate Location
- C. File Recovery with Alternate Location
- D. Full Share Recovery with Original Location

**Answer: A**
**Explanation:** Snapshot-based Azure Files backup supports item-level restore with File Recovery.

### Q20
You need cost-effective point-in-time backups of managed disks that store only changes since the previous backup. Which type of snapshot should you create?
- A. Full snapshot
- B. Incremental snapshot
- C. Page blob snapshot
- D. Differential snapshot

**Answer: B**
**Explanation:** Incremental snapshots store only changed blocks.

### Q21
What is the maximum number of records you can add to a single Azure DNS record set?
- A. 10
- B. 20
- C. 50
- D. 100

**Answer: B**
**Explanation:** Azure DNS supports up to 20 records per record set.

### Q22
User1 needs read access to blobs in `container1` only, and no access to other containers. At which scope should you assign Storage Blob Data Reader?
- A. Subscription scope
- B. Storage account scope
- C. `container1` scope
- D. Blob scope only

**Answer: C**
**Explanation:** Container scope provides least-privilege access to just that container.

### Q23
Blob soft delete and blob versioning are both enabled. A developer overwrites an existing blob. What happens to the previous content?
- A. A soft-deleted snapshot is created
- B. It is permanently removed
- C. A previous version is automatically created
- D. It remains unchanged for 14 days only

**Answer: C**
**Explanation:** With versioning enabled, overwrite creates a previous version.

### Q24
Which Azure file shares can be backed up by Azure Backup when some shares use SMB and others use NFS?
- A. SMB and NFS
- B. SMB only
- C. NFS only
- D. Premium only

**Answer: B**
**Explanation:** Azure Backup for Azure Files supports SMB shares only.

### Q25
A private endpoint is created from a different subscription without the required permissions on the target storage account. What is its initial connection state?
- A. Approved
- B. Connected
- C. Pending
- D. Rejected

**Answer: C**
**Explanation:** It starts in Pending until the target resource owner approves it.

### Q26
An on-demand backup recovery point was created with 60-day retention. Later, the backup policy retention was changed to 14 days. What happens to the on-demand recovery point?
- A. It changes to 14 days
- B. It resets to 30 days
- C. It remains for 60 days
- D. It is deleted on the next scheduled backup

**Answer: C**
**Explanation:** On-demand backup retention is independent of later policy changes.

### Q27
User1 has Storage Blob Data Contributor at storage account scope but still cannot browse containers in the Azure portal. What should you do with least privilege?
- A. Add Storage Blob Data Reader
- B. Add Reader at the storage account scope
- C. Replace Contributor with Reader
- D. Add Owner

**Answer: B**
**Explanation:** Portal navigation requires ARM Reader access in addition to the data-plane role.

### Q28
You want to automatically rehydrate archived blobs to Hot using lifecycle management. What should you do?
- A. Use `enableAutoTierToHotFromCool`
- B. Use `tierToHot`
- C. Use `tierToCool` then `tierToHot`
- D. Use another method because lifecycle management can't rehydrate Archive blobs

**Answer: D**
**Explanation:** Lifecycle management does not support Archive-to-Hot/Cool rehydration.

---

## 3) Networking, DNS, and Access

### Q29
When Application Gateway fronts an App Service backend, what should you configure so the App Service receives the correct host name?
- A. Configure the backend pool with the custom domain only
- B. Enable `Pick host name from backend target` in backend HTTP settings
- C. Replace the frontend certificate only
- D. Assign a public IP to the backend

**Answer: B**
**Explanation:** App Service is host-header-based, so Application Gateway must send the correct backend host header.

### Q30
Users can't RDP to VM1 from the internet and you want to quickly determine whether NSG rules allow or deny the traffic and which rule is responsible. What should you use?
- A. NSG flow logs
- B. Connection troubleshoot
- C. IP flow verify
- D. Traffic analytics

**Answer: C**
**Explanation:** IP flow verify immediately identifies whether traffic is allowed or denied and which NSG rule applies.

### Q31
Which limitation applies to service endpoint policies?
- A. They support only storage accounts in the same region as the VNet
- B. They do not apply to traffic from on-premises networks
- C. They require a `/24` minimum subnet size
- D. They allow only one storage account per policy

**Answer: B**
**Explanation:** Service endpoint policies apply only to traffic originating from Azure subnets, not on-premises networks.

### Q32
You want to force traffic from a subnet through an NVA before reaching on-premises resources learned via BGP. What should you configure on the route table to stop on-premises routes from being added automatically?
- A. Enable gateway route propagation
- B. Disable BGP route propagation
- C. Add a default route to Internet
- D. Use virtual network gateway as next hop

**Answer: B**
**Explanation:** Disabling BGP route propagation prevents automatically learned on-premises routes from being applied to the subnet.

### Q33
If ASG-Web already contains NICs from VNet1 and you want to use ASG-Web and ASG-DB in the same NSG rule, which virtual machines can be members of ASG-DB?
- A. Any VM in East US
- B. Only VMs in VNet1
- C. Only VM3
- D. Any VM in the subscription

**Answer: B**
**Explanation:** NICs in source and destination ASGs used in the same NSG rule must be in the same virtual network.

### Q34
What record type should you create so the zone apex `contoso.com` resolves to an Azure Traffic Manager profile?
- A. CNAME
- B. TXT
- C. Alias record
- D. PTR

**Answer: C**
**Explanation:** Azure DNS apex records must use an Alias record, not CNAME.

### Q35
Azure Bastion Standard is deployed and a contractor without an Azure portal account must securely connect to VM1. What should you configure?
- A. Just-in-time VM access
- B. Azure Bastion Shareable Link
- C. A public IP and NSG rule for RDP
- D. Point-to-Site VPN

**Answer: B**
**Explanation:** Shareable Link allows secure Bastion access without requiring an Azure portal account.

### Q36
You need Azure Bastion with session recording and support for private-only deployment without a public IP. Which SKU should you deploy?
- A. Developer
- B. Basic
- C. Standard
- D. Premium

**Answer: D**
**Explanation:** Premium supports session recording and private-only deployment.

---

## 4) Compute, App Service, ACR, and AKS

### Q37
Which operating system supports multi-container groups in Azure Container Instances?
- A. Windows
- B. Linux
- C. Both Linux and Windows
- D. Nano Server only

**Answer: B**
**Explanation:** ACI multi-container groups are supported only on Linux.

### Q38
You need to validate a staging App Service slot with production settings before completing a swap and cancel if validation fails. What should you use?
- A. Regular swap
- B. Swap with preview
- C. Auto swap
- D. Traffic routing only

**Answer: B**
**Explanation:** Swap with preview lets you validate before the final swap.

### Q39
If a user is routed to a staging slot through App Service traffic routing, how long does the session stay pinned to that slot?
- A. Until the browser is closed
- B. 30 minutes
- C. One hour or until cookies are deleted
- D. Until the next slot swap

**Answer: C**
**Explanation:** App Service slot pinning is controlled by the `x-ms-routing-name` cookie for one hour or until cookies are deleted.

### Q40
WebApp1 is in a Basic App Service plan and you need a staging deployment slot. What must you do first?
- A. Redeploy the web app
- B. Scale up the plan to Standard or higher
- C. Create a new resource group
- D. Move to PremiumV3 only

**Answer: B**
**Explanation:** Deployment slots require Standard tier or higher.

### Q41
You need a TLS/SSL binding for multiple custom domains on the same App Service IP while minimizing cost. Which binding type should you use?
- A. IP-based SSL
- B. SNI SSL
- C. Mutual TLS
- D. Basic SSL

**Answer: B**
**Explanation:** SNI SSL supports multiple domains on a shared IP at lower cost.

### Q42
Which taint should you apply to an AKS system node pool to block application pods while allowing system pods?
- A. `node-role.kubernetes.io/system:NoExecute`
- B. `kubernetes.azure.com/mode=system:NoSchedule`
- C. `SystemOnly=true:PreferNoSchedule`
- D. `CriticalAddonsOnly=true:NoSchedule`

**Answer: D**
**Explanation:** That is the standard AKS taint for system node pools.

### Q43
ACR1 is Standard tier and you need low-latency image pulls for AKS clusters in multiple regions. What should you do first?
- A. Enable geo-replication immediately
- B. Upgrade ACR1 to Premium tier
- C. Create a separate registry per region
- D. Export images to storage accounts

**Answer: B**
**Explanation:** Geo-replication is a Premium-only ACR feature.

### Q44
An availability set was created with default settings. How many fault domains and update domains does it support?
- A. 2 FD, 5 UD
- B. 3 FD, 5 UD
- C. 3 FD, 20 UD
- D. 2 FD, 20 UD

**Answer: B**
**Explanation:** The default availability set supports 3 fault domains and 5 update domains.

---

## 5) Bicep, ARM, Cost, and Monitoring

### Q45
What syntax should you use in Bicep to access a module output named `storageEndpoint`?
- A. `storageEndpoint`
- B. `module.storageEndpoint`
- C. `<moduleName>.outputs.<outputName>`
- D. `reference(moduleName).storageEndpoint`

**Answer: C**
**Explanation:** Bicep module outputs are referenced with `moduleName.outputs.outputName`.

### Q46
You need a custom role that grants all VM permissions except delete. Which role definition properties should you use?
- A. `Actions` only
- B. `NotActions` only
- C. `Actions` for all VM operations plus `NotActions` for delete
- D. `DataActions` for delete

**Answer: C**
**Explanation:** Custom roles use `Actions` and `NotActions` together for this pattern.

### Q47
How do you conditionally deploy a storage account in Bicep when `deployStorage` is true?
- A. `condition: deployStorage`
- B. `= if (deployStorage) { ... }`
- C. `when deployStorage { ... }`
- D. Use only a ternary operator

**Answer: B**
**Explanation:** Conditional resource deployment in Bicep uses `= if (condition) { }`.

### Q48
What Bicep output declaration returns the blob endpoint only when the storage account is deployed, otherwise an empty string?
- A. `output blobEndpoint string = if (deployStorage) ...`
- B. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- C. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`
- D. `output blobEndpoint string = when (deployStorage) ... else ''`

**Answer: C**
**Explanation:** Conditional output values use the ternary operator in Bicep.

### Q49
What ARM expression should you use to reference a linked template output named `planId`?
- A. `[resourceId('planId')]`
- B. `[deployment().outputs.planId]`
- C. `[reference('linkedTemplateDeployment').outputs.planId.value]`
- D. `[variables('planId')]`

**Answer: C**
**Explanation:** Linked template outputs are referenced through `reference('<deploymentName>').outputs.<name>.value`.

### Q50
User1 has Cost Management Reader at subscription scope. What can User1 do with Budget1?
- A. Create and edit it
- B. Delete it
- C. View it only
- D. Change thresholds

**Answer: C**
**Explanation:** Cost Management Reader provides read-only access to budgets.

### Q51
What should you expect when creating a management group scope budget if you want to use budget thresholds and automated actions?
- A. Budgets can't be created at management group scope
- B. Action groups work the same as at subscription scope
- C. Threshold alerts are unsupported
- D. Action groups can't be configured at management group scope

**Answer: D**
**Explanation:** Management group budgets support email notifications, but not action groups.

### Q52
When you enable VM Insights from the Azure portal and accept the default data collection rule, what data is collected?
- A. Guest performance metrics, processes, and dependencies
- B. Guest performance metrics and Windows event logs
- C. Host metrics and guest metrics
- D. Guest performance metrics only

**Answer: D**
**Explanation:** The default VM Insights configuration collects guest performance metrics only.

### Q53
Which operator should you add at the end of a KQL query to render a time-based line chart?
- A. `render linechart`
- B. `render timechart`
- C. `visualize time`
- D. `chart time`

**Answer: B**
**Explanation:** `render timechart` is the correct operator for time-based line charts in Log Analytics.

---

*End of Set 7 — 53 Questions*

**Topic coverage summary:**
- Q1-Q11: Azure Policy, Entra ID, RBAC, locks, dashboards, guest invite settings, user roles
- Q12-Q28: SAS, Azure Files, AD DS, disk encryption, backup, blob protection, DNS limits, archive rehydration
- Q29-Q36: Application Gateway, NSG diagnostics, service endpoint policy, BGP propagation, ASGs, DNS, Bastion
- Q37-Q44: ACI, App Service slots, AKS, ACR, availability sets
- Q45-Q53: Bicep/ARM outputs, conditional deployment, budgets, VM Insights, KQL visualization
