# AZ-104 Practice Questions (Set 11) — English

---

## 1) Networking and Firewall

### Q1
You are configuring an Azure Standard Load Balancer for a Network Virtual Appliance (NVA) that must process all TCP and UDP traffic on all ports. What Protocol and Port values should you configure in the load balancing rule?

- A. Protocol: TCP; Port: 80
- B. Protocol: All; Port: 0
- C. Protocol: UDP; Port: 0
- D. Protocol: TCP; Port: 443

**Answer: B**
**Explanation:** For NVA scenarios that must handle all TCP and UDP traffic on all ports, Azure Load Balancer uses an HA Ports rule configured with Protocol: All and Port: 0. Port 0 is Azure's representation of "all ports." This is the standard HA Ports pattern used when forwarding traffic to firewalls, IDS/IPS, or other NVAs that need full packet visibility.

---

### Q2
A virtual machine in the East US region cannot access a storage account named storage1, even though both resources are in the same region. The storage account has a firewall configured and the public IP of an on-premises server has been added. What should you do to grant the VM access?

- A. Add an NSG inbound rule to allow port 443 on the VM's NIC
- B. Add the VM's virtual network subnet as a VNet rule on the storage account firewall
- C. Assign the Storage Blob Data Reader role to the VM's managed identity
- D. Enable a Service Endpoint for Microsoft.Compute on the subnet

**Answer: B**
**Explanation:** Storage account firewall rules control which networks can access the storage service. NSG rules filter traffic at the VM/subnet level but do not grant access to the storage account's data plane. To allow a VM to access a storage account through its firewall, you must add the VM's VNet/subnet as a VNet rule on the storage account. The Service Endpoint for Microsoft.Storage on the subnet should also be enabled to ensure traffic routes optimally.

---

### Q3
You configure an NSG rule that allows inbound TCP traffic on port 443 to a VM. A client sends a request to the VM. Does the NSG automatically allow the return traffic from the VM back to the client?

- A. No — you must add an explicit outbound allow rule for the return traffic
- B. Yes — Azure NSGs are stateful; return traffic for established connections is automatically permitted
- C. No — you must enable Service Endpoint to allow return traffic
- D. Yes — but only if the subnet NSG and the NIC NSG have identical rules

**Answer: B**
**Explanation:** Azure NSGs are stateful. When an inbound connection is permitted, the corresponding return (response) traffic is automatically allowed without requiring an explicit outbound rule. This stateful behavior applies to both TCP and UDP connections and mirrors the behavior of AWS Security Groups, which are also stateful.

---

### Q4
Azure VM extensions on a Windows VM fail to execute after an ARM template deployment. The VM Agent is confirmed to be running. The VM subnet has an NSG with a custom deny-all outbound rule. What specific destination must be reachable for VM extensions to function?

- A. 10.0.0.1 (Azure DNS)
- B. 8.8.8.8 (Google DNS)
- C. 168.63.129.16 (Azure platform IP)
- D. 169.254.169.254 (instance metadata)

**Answer: C**
**Explanation:** Azure VM extensions require outbound access to 168.63.129.16, a special virtual public IP used exclusively by Azure platform services. This IP handles VM Agent communication, extension download and management, DNS resolution, and health probe responses. A deny-all outbound NSG rule will block this communication, causing extension provisioning to fail even when the VM Agent itself is running.

---

### Q5
You are configuring Azure Firewall application rules to allow outbound traffic to Windows Update endpoints. You want a simple solution that does not require listing individual FQDNs or IP addresses, because Microsoft updates its endpoint list frequently. What should you use?

- A. Service Tags in a network rule
- B. FQDN Tags in an application rule
- C. IP Groups with the Microsoft IP ranges
- D. Application Security Groups (ASGs)

**Answer: B**
**Explanation:** FQDN Tags are pre-defined, Microsoft-managed collections of well-known Azure service FQDNs. The "WindowsUpdate" FQDN Tag in an Azure Firewall application rule automatically covers all Microsoft Update endpoints without requiring manual maintenance. Service Tags work in network rules and are IP-based, not FQDN-based.

---

### Q6
You deploy Azure Firewall Premium. Some outbound HTTPS requests to different websites resolve to the same IP address (common on CDN-backed services). You need to allow traffic to site-a.example.com but block traffic to site-b.example.com, even though both FQDNs resolve to the same IP. What should you configure?

- A. A network rule with the destination IP address
- B. A NAT rule with FQDN filtering
- C. An application rule with TLS inspection enabled
- D. A network rule with the FQDN as the destination

**Answer: C**
**Explanation:** Network rules (L4) operate on IP and port, so they cannot differentiate between two FQDNs that resolve to the same IP. Azure Firewall Premium application rules with TLS inspection decrypt HTTPS traffic and inspect the HTTP Host header and TLS SNI field, enabling FQDN-level differentiation even when the underlying IPs are identical. TLS inspection is an Azure Firewall Premium feature.

---

### Q7
You configure Azure Firewall with Threat Intelligence enabled. The next day, you notice that known malicious IP addresses appear in firewall logs, but the traffic is still reaching backend servers. What is the most likely cause?

- A. Threat Intelligence is completely disabled
- B. Threat Intelligence is set to Alert only mode and is not blocking traffic
- C. The NSG is bypassing the firewall for that traffic
- D. Network rules take precedence over Threat Intelligence settings

**Answer: B**
**Explanation:** Azure Firewall's default Threat Intelligence mode is "Alert only." In this mode, matches with the threat intelligence feed are logged but traffic is not blocked. To actively block traffic from known malicious IPs and domains, the Threat Intelligence mode must be explicitly changed to "Alert and deny."

---

### Q8
You have an Azure Firewall Policy with Threat Intelligence set to "Alert and deny" (the most restrictive setting). A child policy is configured to inherit from this parent policy. What Threat Intelligence mode options are available for the child policy?

- A. Off, Alert, or Alert and deny (any can be selected)
- B. Off or Alert only
- C. Alert and deny only
- D. Alert only

**Answer: C**
**Explanation:** Azure Firewall Policy inheritance enforces a security-only direction: child policies cannot reduce the security posture inherited from the parent. When the parent policy is set to "Alert and deny" (the most restrictive Threat Intelligence mode), the child policy is restricted to "Alert and deny" only. It cannot be loosened to Alert or Off.

---

### Q9
A subnet uses a Service Endpoint for Microsoft.Storage. You need to ensure that resources in the subnet can only reach two specific storage accounts and are blocked from accessing all other storage accounts (to prevent data exfiltration). What should you configure?

- A. Add VNet rules on each of the two allowed storage accounts
- B. Create an NSG outbound rule that blocks all storage traffic except the two accounts
- C. Create a Service Endpoint Policy on the subnet that lists only the two allowed storage accounts
- D. Use Private Endpoints for the two storage accounts and disable public access

**Answer: C**
**Explanation:** A Service Endpoint alone opens subnet-to-Azure-Storage access broadly (all storage accounts in the region), which creates a data exfiltration risk. A Service Endpoint Policy is applied directly to the subnet and restricts outbound traffic to only the specified storage accounts. Storage account VNet rules restrict inbound access to the storage account side but do not prevent the subnet from reaching other storage accounts.

---

### Q10
An App Service (Linux) uses deployment slots including a staging slot. You want the staging slot to automatically swap to production after each deployment without any manual steps. You check the slot configuration and find Auto Swap is unavailable. Why is Auto Swap not available?

- A. Auto Swap requires the Standard SKU App Service Plan
- B. Auto Swap is not supported for Linux App Service
- C. Auto Swap requires a custom domain to be configured first
- D. Auto Swap is only available for Function Apps

**Answer: B**
**Explanation:** Auto Swap is a deployment slot feature available only for Windows App Service. It is not supported for Linux App Service slots. For Linux, slot swaps must be performed as an explicit step in a CI/CD pipeline (Azure Pipelines or GitHub Actions) using `az webapp deployment slot swap`.

---

### Q11
You have an App Service (Linux) with a staging slot. After each deployment to the staging slot, you want to automatically swap staging to production. Auto Swap is not supported on Linux. What is the recommended approach?

- A. Use a Logic App with a timer to call the swap API
- B. Add a slot swap step to the CI/CD pipeline after the deployment step
- C. Configure a Health Check probe to trigger the swap automatically
- D. Enable Traffic Manager to gradually shift traffic from staging to production

**Answer: B**
**Explanation:** For Linux App Service where Auto Swap is unavailable, the recommended approach is to add an explicit swap step to the CI/CD pipeline after the deployment is validated. The pipeline executes `az webapp deployment slot swap --name AppName --resource-group RG --slot staging --target-slot production` after deployment succeeds.

---

### Q12
You are comparing Azure NSG and AWS Security Group behavior. A colleague says AWS Security Groups are stateless. Is this correct?

- A. Yes — AWS Security Groups are stateless and require both inbound and outbound rules
- B. No — AWS Security Groups are stateful, just like Azure NSGs
- C. Yes — all cloud firewalls at the subnet level are stateless
- D. No — AWS Security Groups are stateful but Azure NSGs are stateless

**Answer: B**
**Explanation:** AWS Security Groups are stateful, the same as Azure NSGs. In both services, when an inbound connection is permitted, return traffic is automatically allowed without an explicit outbound rule. The key difference between Azure NSGs and AWS Security Groups is that Azure NSGs support explicit deny rules while AWS Security Groups only support allow rules (deny is implicit for anything not allowed).

---

## 2) Storage, SAS, and Lifecycle

### Q13
You need to provide time-limited blob container access to external partners. After the access period ends, you must be able to revoke access immediately without regenerating the storage account keys. Which SAS type should you use?

- A. Account SAS with an expiry time
- B. User Delegation SAS
- C. Service SAS backed by a stored access policy
- D. Service SAS with a fixed expiry time only

**Answer: C**
**Explanation:** A Service SAS backed by a stored access policy can be revoked by deleting or modifying the stored access policy, which immediately invalidates all SAS tokens linked to it — without regenerating the storage account key. A Service SAS with only a fixed expiry cannot be revoked early without key regeneration. User Delegation SAS uses Entra ID credentials but revocation requires waiting for expiry or revoking the user's token.

---

### Q14
Your organization requires that Azure Storage accounts use only Microsoft Entra ID-based authentication. Access using storage account access keys must be completely blocked. User Delegation SAS must continue to work. What should you configure?

- A. Remove all RBAC role assignments from the storage account
- B. Regenerate both access keys and do not share them
- C. Disable shared key access (Allow shared key access = Disabled)
- D. Place a ReadOnly lock on the storage account

**Answer: C**
**Explanation:** Setting "Allow shared key access" to Disabled blocks all Shared Key authentication, including Service SAS and Account SAS (which are signed with the account key). User Delegation SAS is signed with Entra ID credentials rather than the account key, so it remains available after disabling shared key access. This enforces Entra ID as the only authentication path while preserving User Delegation SAS functionality.

---

### Q15
You are configuring a key expiration policy on a storage account in the Azure portal. The "Set rotation reminder" button is grayed out. What is the most likely reason?

- A. The storage account uses geo-redundant replication (GRS)
- B. The storage account firewall is blocking portal management access
- C. The storage access keys have no creation timestamp recorded
- D. The storage account is in a different region than the portal user

**Answer: C**
**Explanation:** The key expiration policy requires a keyCreationTime property on each access key to calculate when the key expires. Keys created before this feature was introduced have no creation timestamp. The rotation reminder can only be configured after the keys are regenerated, which causes Azure to record the creation timestamp on the new keys.

---

### Q16
The "Set rotation reminder" button for a storage account key expiration policy is grayed out in the Azure portal. What action should you take to enable this configuration?

- A. Enable blob soft delete on the storage account
- B. Regenerate the storage account access keys
- C. Assign the Key Vault Administrator role to the storage account
- D. Enable blob versioning on the container

**Answer: B**
**Explanation:** Regenerating the storage account access keys causes Azure to assign a keyCreationTime property to the new keys. The key expiration policy uses this timestamp to determine when rotation reminders should be triggered. After regeneration, the "Set rotation reminder" button becomes active.

---

### Q17
You have blobs stored in an Azure Storage account's Archive tier. Due to an urgent business requirement, you need to access these blobs as quickly as possible. Each blob is smaller than 10 GB. What rehydration option should you choose?

- A. Standard priority rehydration (may take up to 15 hours)
- B. High priority rehydration
- C. Blob Copy with online tier change
- D. Lifecycle management rule with tier change action

**Answer: B**
**Explanation:** Archive tier blobs must be rehydrated to Hot or Cool tier before they can be read. High priority rehydration is processed before standard-priority requests and typically completes in under 1 hour for objects smaller than 10 GB. When urgency is the primary requirement, High priority rehydration is the correct choice, even though it costs more than Standard priority.

---

### Q18
You are configuring diagnostic settings on an Azure Storage account to send resource logs to a Log Analytics workspace. You want to use category groups to simplify log collection without selecting individual log categories. Which two category groups are available?

- A. allLogs and diagnosticLogs
- B. allLogs and auditLogs
- C. resourceLogs and securityLogs
- D. auditLogs and performanceLogs

**Answer: B**
**Explanation:** Azure Diagnostic Settings support two predefined category groups available for storage accounts: "allLogs" (collects all resource log categories) and "auditLogs" (collects only security and access audit-related logs). Using category groups eliminates the need to manually select individual log categories and automatically includes new categories as they become available.

---

### Q19
You are creating a lifecycle management policy to move blobs from Cool tier to Cold tier. The blobs were recently transitioned from Hot to Cool. What is the minimum number of days a blob must stay in the Cool tier before being moved to Cold tier to avoid early deletion fees?

- A. 7 days
- B. 14 days
- C. 30 days
- D. 60 days

**Answer: C**
**Explanation:** The Cool tier has a minimum storage duration of 30 days. If a blob is moved from Cool tier to Cold tier before it has been in Cool tier for 30 days, Azure charges a prorated early deletion fee for the remaining days. Lifecycle management rules that target the Cool-to-Cold transition must account for this 30-day minimum.

---

### Q20
You need to configure a lifecycle management policy that moves blobs based on when they were last accessed rather than when they were last modified. What prerequisite must be enabled before you can use the `daysAfterLastAccessTimeGreaterThan` condition?

- A. Blob versioning must be enabled
- B. Blob soft delete must be enabled
- C. Last access time tracking must be enabled
- D. Change feed must be enabled

**Answer: C**
**Explanation:** Last access time tracking is a storage account feature that must be explicitly enabled before the `daysAfterLastAccessTimeGreaterThan` condition can be used in lifecycle management policies. Without it, Azure does not record the last access time for blobs, so the condition has no data to evaluate. Enabling last access time tracking may incur a small performance overhead.

---

## 3) Compute and App Service

### Q21
You need to deploy an Azure Container Instances (ACI) group with two containers: a main application container and a sidecar logging container that run simultaneously and share the same network namespace. What OS type must you select for the container group?

- A. Windows
- B. Either Windows or Linux
- C. Linux
- D. Windows Server Core only

**Answer: C**
**Explanation:** ACI multi-container groups (where multiple containers share the same lifecycle, network, and storage resources — required for sidecar patterns) are fully supported only on Linux. Windows ACI container groups have very limited multi-container support and are not recommended for sidecar scenarios. Always use Linux when deploying multi-container ACI groups.

---

### Q22
You have a Windows VM running in Azure. You need to ensure all data at rest is encrypted, including temporary disks and disk caches, without consuming VM compute resources for encryption. Which encryption solution should you use?

- A. Azure Disk Encryption (ADE) with BitLocker
- B. Encryption at host
- C. Storage Service Encryption (SSE) with customer-managed keys
- D. BitLocker Drive Encryption (standalone)

**Answer: B**
**Explanation:** Encryption at host performs encryption at the hypervisor layer, before data is written to or read from physical disks, and covers temporary disks and disk caches — areas Azure Disk Encryption (BitLocker) may not fully cover. Crucially, encryption at host does not consume VM CPU resources because the encryption process occurs on the physical host hardware, not inside the VM.

---

### Q23
You deploy a Windows VM using an ARM template that includes a Custom Script Extension. After deployment, the extension status shows "Failed." The VM Agent status is healthy. The VM subnet NSG has a custom deny-all outbound rule. What is the most likely cause?

- A. The script storage account is in a different Azure region
- B. The VM cannot reach the Azure platform IP 168.63.129.16
- C. The VM Agent version is outdated
- D. Custom Script Extension requires a public IP on the VM

**Answer: B**
**Explanation:** Azure VM extensions communicate with Azure's management plane through the platform IP 168.63.129.16. A deny-all outbound NSG rule blocks this communication, preventing the extension handler from downloading the script and reporting execution status back to Azure. The VM Agent appearing healthy indicates the agent process is running, but this does not guarantee the extension can reach Azure's control plane.

---

### Q24
A Windows VM's Custom Script Extension executed a deployment script but failed midway. You need to review the detailed execution log including error output. Where should you look?

- A. Azure Monitor Logs in the Log Analytics workspace
- B. Windows Event Viewer (Application log)
- C. C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\
- D. C:\Program Files\Microsoft Azure\Logs\

**Answer: C**
**Explanation:** Custom Script Extension execution logs on Windows VMs are stored at `C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\`. This directory contains detailed logs of download attempts, script execution output, and error messages. The downloaded script files themselves are stored under `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\`.

---

### Q25
A web application runs on Azure App Service with multiple backend instances. Users must be consistently routed to the same backend instance throughout their session (session persistence). The frontend uses Application Gateway. What feature should you enable?

- A. URL path-based routing on Application Gateway
- B. Cookie-based affinity (session persistence) on Application Gateway
- C. Source IP affinity on Azure Load Balancer
- D. WebSocket support on Application Gateway

**Answer: B**
**Explanation:** Application Gateway's cookie-based affinity ensures requests from the same client session are always routed to the same backend instance. On the first response, the gateway sets an ARRAffinity cookie; subsequent requests carrying this cookie are directed to the same server. Source IP affinity is an Azure Load Balancer (L4) concept that should not be confused with Application Gateway's L7 session persistence.

---

### Q26
You have a web application currently deployed to an App Service in the Free (F1) tier. The application requires a custom domain mapping and a TLS/SSL certificate binding. What is the minimum App Service Plan tier you need?

- A. Shared (D1)
- B. Basic (B1)
- C. Standard (S1)
- D. Premium (P1v2)

**Answer: B**
**Explanation:** The Free (F1) and Shared (D1) tiers do not support custom domain mapping or SSL/TLS certificate bindings. The Basic (B1) tier is the minimum tier that supports both features. Standard and Premium tiers also support these features but cost more, so Basic is the correct answer when the requirement is to minimize cost while enabling custom domain and SSL.

---

### Q27
An Azure SQL Database (vCore, General Purpose tier) has critical data accidentally deleted by a user. You need to restore the database to exactly 10 minutes before the deletion. The solution must minimize administrative effort. What should you do?

- A. Restore from the most recent manual backup
- B. Use geo-restore to a new database in another region
- C. Use point-in-time restore (PITR) to the target timestamp
- D. Export a BACPAC and re-import with a filtered dataset

**Answer: C**
**Explanation:** Azure SQL Database automatically creates full, differential, and transaction log backups and supports point-in-time restore (PITR) to any point within the backup retention period. PITR requires only selecting the target timestamp and initiating the restore — no manual backup management is needed. It is the minimum-effort solution for accidental data deletion recovery.

---

### Q28
You create an Azure metric alert rule on a VM with two conditions joined by AND: Percentage CPU greater than 90% AND Available Memory Bytes less than 1 GB. CPU peaks at 10:01 AM and normalizes by 10:02 AM. Memory drops below 1 GB at 10:04 AM. Will the alert fire?

- A. Yes — both conditions were true at some point within the evaluation period
- B. No — both conditions must be simultaneously true at the same evaluation time slice
- C. Yes — metric alerts with AND conditions use OR logic at evaluation time
- D. No — metric alerts do not support memory as a condition

**Answer: B**
**Explanation:** Azure metric alert rules with multiple AND conditions require all conditions to be simultaneously true at the same evaluation time slice. If CPU spikes at 10:01 but recovers before memory drops at 10:04, there is no single moment when both conditions are simultaneously true, so the alert will not fire. To capture "each condition met at any point within the evaluation period," use aggregations like Max (for CPU) and Min (for memory) over the evaluation window.

---

## 4) ARM Templates and Bicep

### Q29
You have an ARM template that deploys multiple resources. Before running the deployment, you want to check only the template syntax and parameter validity without creating any resources. What command should you run?

- A. az deployment group what-if
- B. az deployment group create --dry-run
- C. az deployment group validate
- D. az deployment group check

**Answer: C**
**Explanation:** `az deployment group validate` performs syntax validation, parameter validation, and basic deployment feasibility checks without creating any resources. `az deployment group what-if` also does not create resources, but its primary purpose is to preview which changes would occur — it goes beyond pure syntax validation.

---

### Q30
You plan to deploy an ARM template to a resource group. Before deployment, you want a detailed preview showing exactly which resources will be created, modified, or deleted — without applying any changes. What should you use?

- A. az deployment group validate
- B. az deployment group create --mode Incremental
- C. az deployment group what-if
- D. az deployment group create --no-wait

**Answer: C**
**Explanation:** The ARM What-if operation (`az deployment group what-if`) simulates the deployment and reports all resource changes with symbols: `+` for create, `~` for modify, `-` for delete. No actual changes are applied to the environment. `validate` checks syntax only and does not report resource impact.

---

### Q31
You have an ARM template with linked templates for modular deployments. What deployment mode does a linked template support?

- A. Complete mode only
- B. Incremental mode only
- C. Both Incremental and Complete modes
- D. The same mode as the parent template

**Answer: B**
**Explanation:** ARM linked templates (invoked via `Microsoft.Resources/deployments` with `templateLink`) support only Incremental deployment mode. Complete mode — which deletes resources in the resource group not present in the template — is not supported for linked templates. The parent template may use either mode, but linked templates are always Incremental.

---

### Q32
You are writing a Bicep template. You need to deploy a storage account resource only when a boolean parameter `deployStorage` is `true`. What is the correct Bicep syntax?

- A. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = condition deployStorage {...}`
- B. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) {...}`
- C. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' when (deployStorage) {...}`
- D. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = [if (deployStorage) then {...}]`

**Answer: B**
**Explanation:** In Bicep, conditional resource deployment uses the `= if (condition) { ... }` syntax. The equivalent in ARM JSON uses a `"condition"` property set to an ARM expression. Bicep's syntax is more readable. If the condition is false, the resource is not deployed and references to it in outputs must use a null-safe ternary expression.

---

### Q33
In an ARM JSON template, you define a conditional resource using the `"condition"` property. What is the ARM JSON equivalent of the Bicep expression `if (deployStorage)`?

- A. `"condition": "deployStorage"`
- B. `"condition": "[parameters('deployStorage')]"`
- C. `"condition": "[equals(parameters('deployStorage'), true())]"`
- D. Both B and C are valid

**Answer: D**
**Explanation:** In ARM JSON, boolean parameters can be used directly in condition expressions. `[parameters('deployStorage')]` works when the parameter is a boolean type that evaluates directly. `[equals(parameters('deployStorage'), true())]` is the more explicit form. Both forms are valid and functionally equivalent when `deployStorage` is a bool parameter.

---

### Q34
You are creating a new Azure custom RBAC role using a JSON role definition file. Which property must be set to `null` in the JSON?

- A. AssignableScopes
- B. IsCustom
- C. Id
- D. Description

**Answer: C**
**Explanation:** When creating a new custom RBAC role, the `Id` property must be set to `null` in the JSON definition. Azure will automatically generate a unique GUID for the role. `AssignableScopes` must be explicitly provided with at least one valid scope. `IsCustom` must be `true`. Setting a non-null `Id` would indicate an update to an existing role definition.

---

### Q35
You want to invoke an external ARM template (stored in a storage account) from a parent ARM template for modular deployment. What resource type do you declare in the parent template?

- A. Microsoft.Resources/linkedDeployments
- B. Microsoft.Template/deployments
- C. Microsoft.Resources/deployments
- D. Microsoft.ARM/templateLinks

**Answer: C**
**Explanation:** Linked ARM templates are invoked using the `Microsoft.Resources/deployments` resource type in the parent template, with a `templateLink` property specifying the URI of the external template file. This is the standard mechanism for modular ARM deployments. The linked template runs with Incremental mode only.

---

## 5) Identity, RBAC, and Governance

### Q36
User1 has two role assignments on a storage account: (1) a custom role with `Microsoft.Storage/*` in Actions but `Microsoft.Storage/storageAccounts/delete` in NotActions, and (2) the built-in "Storage Account Contributor" role which includes delete permissions. Can User1 delete storage accounts?

- A. No — NotActions in one role override all other role assignments
- B. Yes — Azure RBAC is additive; permissions from all roles are combined and NotActions only restrict the specific role they belong to
- C. No — custom role NotActions always take precedence over built-in roles
- D. Yes — but only if User1 also has an Owner assignment at the subscription level

**Answer: B**
**Explanation:** Azure RBAC uses an additive model: effective permissions are the union of all role assignments. NotActions only exclude a permission within the specific role definition they appear in — they do not cancel permissions granted by other roles. Since the Storage Account Contributor role grants delete permissions separately, User1 can delete storage accounts despite the custom role's NotActions exclusion.

---

### Q37
What is the key difference between `NotActions` in an Azure RBAC role definition and a Deny Assignment?

- A. They are functionally identical — both block the specified permission across all roles
- B. NotActions is a role-level exclusion overridable by another role; Deny Assignment is a system-level override that blocks the permission regardless of any role assignments
- C. Deny Assignment is deprecated; NotActions is the current recommended approach for blocking permissions
- D. NotActions applies to the subscription scope; Deny Assignment applies to the resource group scope only

**Answer: B**
**Explanation:** `NotActions` removes a permission only within the role definition it appears in. If another role assignment grants the same permission, that grant takes effect (RBAC is additive). A Deny Assignment explicitly blocks a permission at a system level and overrides all role assignments, including Owner — even if multiple roles grant the blocked permission, the Deny Assignment prevents it.

---

### Q38
A Microsoft Entra tenant has SSPR configured with: "Number of methods required: 2" and available methods: Mobile app notification, Mobile app code, Email, Mobile phone. User1 has registered only one SSPR authentication method. User1 forgets their password and tries to reset it via SSPR. What happens?

- A. SSPR allows the reset using the single registered method
- B. User1 is automatically redirected to the help desk
- C. User1 cannot complete the password reset because fewer methods are registered than required
- D. SSPR sends a temporary password to the registered method

**Answer: C**
**Explanation:** SSPR enforces the "number of methods required" policy at reset time. If a user has registered fewer methods than the required number, they cannot complete the SSPR flow and will see an error telling them to contact their administrator. The policy check happens at the time of the reset attempt.

---

### Q39
You are bulk-creating users in Microsoft Entra ID using a CSV file upload in the Entra admin center. Which four columns are required in the CSV template?

- A. Name, User principal name, Department, Job title
- B. Name, User principal name, Initial password, Block sign in
- C. Name, Email, Password, Manager
- D. User principal name, First name, Last name, Department

**Answer: B**
**Explanation:** The Entra admin center CSV template for bulk user creation has four mandatory columns: Name (display name), User principal name (UPN/login ID), Initial password, and Block sign in (Yes/No). The Initial password is required because CSV upload creates fully provisioned accounts. Block sign in determines whether the account is enabled or disabled after creation.

---

### Q40
You need to ensure that all newly created Azure subscriptions are automatically placed in a management group named MG-Onboarding instead of the root management group. What built-in role grants the minimum required permissions to change the default management group setting?

- A. Owner at the root management group
- B. Global Administrator in Microsoft Entra ID
- C. Hierarchy Settings Administrator
- D. Management Group Contributor

**Answer: C**
**Explanation:** The "Hierarchy Settings Administrator" is the built-in role that grants the `Microsoft.Management/managementgroups/settings/write` permission required to change the default management group for new subscriptions. This role is more narrowly scoped than Owner or Global Administrator, following the principle of least privilege for management group hierarchy configuration.

---

### Q41
Which statement about the Azure root management group is correct?

- A. Each subscription can have its own root management group
- B. The root management group can be deleted once all child resources are removed
- C. All Azure subscriptions and management groups in a tenant ultimately reside under the root management group
- D. The root management group must be manually created for each tenant

**Answer: C**
**Explanation:** The root management group is automatically created for every Azure AD tenant and represents the top of the management group hierarchy. Every subscription and every nested management group ultimately exists under the root management group. The root management group cannot be deleted or moved, and there is exactly one per tenant.

---

### Q42
You are building an Azure custom RBAC role. The role must allow reading storage account metadata (management plane) and reading/writing blob data (data plane). Which properties in the role definition JSON should define these permissions?

- A. Both permissions should be in Actions
- B. Storage account metadata in Actions; blob read/write in DataActions
- C. Both permissions should be in DataActions
- D. Storage metadata in NotActions; blob access in DataActions

**Answer: B**
**Explanation:** Azure RBAC separates management plane and data plane permissions. `Actions` covers ARM management plane operations (such as reading storage account properties, configurations, and ARM metadata). `DataActions` covers direct data-plane operations (such as reading or writing blob data). Mixing them or using `NotActions` in place of `Actions`/`DataActions` would result in incorrect role behavior.

---

### Q43
You are configuring an Azure Cost Management budget and need it to reset automatically every three months. What Time Grain value should you select?

- A. P1M (Monthly)
- B. P3M (Quarterly)
- C. P1Y (Annually)
- D. P90D (90-day period)

**Answer: B**
**Explanation:** Azure Cost Management budgets use ISO 8601 period duration notation: P1M = 1 month (monthly), P3M = 3 months (quarterly), P1Y = 1 year (annually). For a budget that resets every three months, the correct Time Grain value is P3M (Quarterly).

---

### Q44
You need to create a Microsoft Entra ID group where membership is automatically maintained based on user attributes (such as department = "Engineering"). What license is required for this capability?

- A. Microsoft 365 E3
- B. Entra ID Premium P1
- C. Entra ID Premium P2
- D. No additional license is needed

**Answer: B**
**Explanation:** Dynamic membership groups, where members are automatically added or removed based on attribute rules (such as department, job title, or location), require at least Entra ID Premium P1. This feature is not available in the Entra ID Free tier. P2 also satisfies the requirement but is not the minimum license needed.

---

## 6) Backup, Monitoring, and Operations

### Q45
You configure Azure Backup for a VM using an Enhanced backup policy with Consistency type set to "Only crash consistent snapshot." What does this backup configuration guarantee?

- A. All in-flight transactions are committed to disk before the snapshot
- B. The disk state is captured at a precise moment, equivalent to a sudden power loss, with no application quiescing
- C. All open files are flushed and the file system is in a consistent state
- D. Application-level consistency via VSS is enforced

**Answer: B**
**Explanation:** A crash-consistent snapshot captures the disk state at the exact moment of capture without quiescing applications, flushing memory, or committing in-flight transactions. It is equivalent to the state after a sudden power loss. This approach is fast and has minimal overhead but may require application-level recovery (such as SQL transaction log replay) upon restore.

---

### Q46
You are selecting Azure Backup consistency type for a SQL Server VM. The database requires that all transactions are fully committed and recorded in the backup to avoid any data loss on restore. What consistency type must you use?

- A. Crash-consistent
- B. File-consistent
- C. Application-consistent
- D. Disk-consistent

**Answer: C**
**Explanation:** Application-consistent backups use VSS (Volume Shadow Copy Service) on Windows or pre/post scripts on Linux to quiesce the application, flush in-flight I/O, and commit pending transactions before taking the snapshot. This ensures that databases can be restored to a fully committed state without needing to replay transaction logs or perform crash recovery.

---

### Q47
You have an Azure VM with VM Insights enabled. A monitoring team needs to query performance counter data (CPU, memory, disk) and dependency map data. In which Azure service is this data stored?

- A. Azure Storage account (table storage)
- B. Azure Monitor Metrics
- C. Log Analytics workspace
- D. Application Insights

**Answer: C**
**Explanation:** VM Insights stores all collected data — performance counters and process/dependency data — in a Log Analytics workspace in KQL-queryable tables (such as `InsightsMetrics` and `VMConnection`). This allows the monitoring team to use KQL queries, create alerts, and build Azure Monitor Workbooks based on this data.

---

### Q48
You are building an Azure Monitor Workbook that needs to display a cross-subscription inventory of VMs including their tags, locations, and resource types. What data source should you use for the workbook query?

- A. Log Analytics
- B. Azure Resource Graph
- C. Application Insights
- D. Azure Monitor Metrics

**Answer: B**
**Explanation:** Azure Resource Graph is designed for cross-subscription, cross-resource-group resource metadata queries at scale. In Azure Monitor Workbooks, selecting Azure Resource Graph as the data source enables KQL queries against resource properties, tags, locations, subscription associations, and other ARM metadata. Log Analytics is used for log and performance data, not resource inventory metadata.

---

### Q49
An App Service web app is still serving an old secret value 2 hours after the secret was rotated in Key Vault. The App Service uses Key Vault references without specifying a version (so it should always fetch the latest). What is the most likely reason the old value is still being served?

- A. The Key Vault reference is pointing to a deleted version
- B. The App Service caches resolved Key Vault secret values for up to 24 hours
- C. The managed identity no longer has access to Key Vault after secret rotation
- D. Key Vault references without a version always pin to the initial version

**Answer: B**
**Explanation:** App Service Key Vault references cache the resolved secret value to reduce API calls and avoid Key Vault throttling. This cache has a maximum lifetime of 24 hours. If the secret was rotated 2 hours ago, the App Service may still be serving the cached old value. Restarting the App Service forces an immediate refresh of all cached Key Vault reference values.

---

### Q50
A security audit finds that an Azure Firewall configured with Threat Intelligence set to "Alert and deny" has log entries showing alerts for known malicious IPs, but the traffic was not blocked during the first week of operation. The current Threat Intelligence setting is confirmed to be "Alert and deny." What is the most likely explanation?

- A. Threat Intelligence does not block traffic to private IP ranges
- B. The Azure Firewall Threat Intelligence feed was not yet updated during the first week
- C. Azure Firewall's default Threat Intelligence mode is "Alert only"; the logs from the first week reflect the default setting before it was changed to "Alert and deny"
- D. Alert and deny requires a separate Network rule to be present before blocking takes effect

**Answer: C**
**Explanation:** Azure Firewall's default Threat Intelligence mode is "Alert only," which logs matches without blocking traffic. If the firewall was deployed and operated under the default setting before being changed to "Alert and deny," the log entries from that initial period would show alerts without corresponding blocks. The current "Alert and deny" setting correctly blocks traffic going forward.

---

*Document generated from AZ-104 course materials. Last updated: 2026-05-22*
