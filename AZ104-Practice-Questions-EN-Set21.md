# AZ-104 Practice Questions (Set 21) — English

## 1) Load Balancer, Health Probes, and Network Traffic

### Q1
You are deploying a Network Virtual Appliance (NVA) behind an Azure Standard internal load balancer. The NVA must inspect all types of traffic on all ports simultaneously. Which load balancing rule configuration achieves this?

- A. Protocol: TCP, Port: 80
- B. Protocol: All, Port: 0
- C. Protocol: UDP, Port: 0
- D. Protocol: TCP, Port: 443

**Answer: B**

**Explanation:** To allow an NVA to inspect all traffic types on all ports, configure an HA Ports rule using Protocol: All and Port: 0 on an Azure Standard Load Balancer. This creates a High Availability Ports rule that load-balances all TCP and UDP flows across every port simultaneously. HA Ports rules are only available on the Standard SKU and are specifically designed for NVA deployments where all traffic must pass through the appliance.

---

### Q2
You have three VMs in the backend pool of a Standard internal load balancer. Users report intermittent connectivity issues. You discover that the load balancer's health probes are failing. You check the NSG on the subnet and find inbound rules that restrict traffic. Which IP address must be allowed inbound in the NSG for health probes to succeed?

- A. 10.0.0.1
- B. 169.254.169.254
- C. 168.63.129.16
- D. 0.0.0.0/0

**Answer: C**

**Explanation:** Azure Load Balancer health probes originate from the special Azure platform IP 168.63.129.16. This IP is used for health probe traffic, VM agent communication, and DNS services. If the NSG blocks inbound traffic from 168.63.129.16, health probes will fail and VMs will be removed from the active backend pool, causing connectivity issues. You must add an NSG inbound rule allowing traffic from 168.63.129.16 on the health probe port.

---

### Q3
You deploy a VM using an ARM template that includes a Custom Script Extension. The VM agent is running and healthy. The NSG restricts all outbound traffic except port 443 to specific IP ranges, and 168.63.129.16 is not in the allowed list. The extension consistently fails to execute. What is the most likely cause?

- A. The VM agent service is not running
- B. Outbound port 80 is blocked by the NSG
- C. Outbound access to 168.63.129.16 is blocked by the NSG
- D. The custom script URL has an invalid certificate

**Answer: C**

**Explanation:** Azure VM Extensions are provisioned through the Azure fabric controller, which communicates through the special Azure platform IP 168.63.129.16. Extensions require outbound access to this IP to download extension packages, send status updates, and report execution results. If an NSG blocks outbound traffic to 168.63.129.16, the extension will fail even if the VM agent is running. Add an outbound NSG rule allowing traffic to 168.63.129.16 (or use the AzurePlatformDNS service tag).

---

### Q4
You have a subnet with the Microsoft.Storage service endpoint enabled. Multiple workloads in the subnet need access to a company-approved storage account named storage-corp. You need to prevent any VM in the subnet from accessing unauthorized storage accounts and block potential data exfiltration to external storage accounts. What should you configure?

- A. Add a storage account VNet rule on each storage account to allow only the subnet
- B. Add an NSG outbound deny rule for the Azure Storage service tag
- C. Create a Service Endpoint Policy on the subnet specifying storage-corp as the allowed resource
- D. Configure a Private Endpoint for storage-corp

**Answer: C**

**Explanation:** Service Endpoint Policies allow you to filter outbound traffic from a subnet to only specific Azure Storage accounts. By creating a Service Endpoint Policy that references storage-corp, all traffic from the subnet to Azure Storage is restricted to that specific account — preventing access to any other storage account, including those in other tenants. Storage account VNet rules (option A) control which subnets can access a specific account but do not prevent the subnet from accessing other accounts. Service Endpoint Policies provide the granular, subnet-side filtering needed to prevent data exfiltration.

---

### Q5
You have an Azure Application Gateway v2 in front of a stateful web application. Users' session state is stored on specific backend servers. After a failover event, some users are redirected to different backend servers and lose their session data. You need to implement session persistence so users always reach the same backend server. Which Application Gateway feature should you enable?

- A. Multi-site listener with host-based routing
- B. Cookie-based affinity (ARRAffinity cookie)
- C. Connection draining
- D. URL path-based routing

**Answer: B**

**Explanation:** Application Gateway supports cookie-based session affinity using the ARRAffinity cookie. When enabled, the gateway inserts an ARRAffinity cookie into the first HTTP response. All subsequent requests from the same client include this cookie, and the gateway uses its value to route requests consistently to the same backend server. This ensures session persistence for stateful applications without requiring changes to the application code.

---

### Q6
You have an Azure Standard SKU Load Balancer with a frontend IP address. You need to ensure that the frontend IP address remains available even if one Azure Availability Zone fails. What must you configure for the frontend IP?

- A. Assign a Basic SKU public IP address
- B. Configure a zone-redundant frontend IP address
- C. Deploy a separate load balancer per availability zone
- D. Enable floating IP on the load balancing rules

**Answer: B**

**Explanation:** Standard SKU Azure Load Balancers support zone-redundant frontend IP addresses that are replicated across all availability zones in a region. If one zone fails, traffic continues to be served through the surviving zones. A zone-redundant frontend IP is configured by selecting "Zone-redundant" (no specific zone pinned) when assigning the frontend IP. Basic SKU Load Balancers do not support availability zones. This is the recommended approach for high-availability load balancer deployments.

---

### Q7
A network administrator in a different Azure subscription creates a private endpoint targeting your storage account named storage1. The private endpoint connection appears in storage1 with a status of Pending. What must you do to authorize the connection?

- A. Create a DNS record mapping the private endpoint's IP
- B. Grant the network administrator Contributor access to storage1
- C. Approve the private endpoint connection in the storage1 networking settings
- D. Recreate the private endpoint from within your own subscription

**Answer: C**

**Explanation:** When a private endpoint is created from a different subscription, the target resource owner must explicitly approve the connection. Navigate to storage1 > Networking > Private endpoint connections, select the pending connection, and click Approve. Cross-subscription private endpoint connections do not auto-approve because the creator does not have ownership over the target resource. Auto-approval only occurs when the creator has Owner or Contributor access to the target resource in the same subscription.

---

### Q8
You create a private endpoint for the blob service of a storage account named storage1 and enable the "Integrate with private DNS zone" option during creation. Which private DNS zone name is automatically created?

- A. privatelink.storage.core.windows.net
- B. privatelink.blob.core.windows.net
- C. storage1.blob.privatelink.net
- D. blob.privatelink.azure.com

**Answer: B**

**Explanation:** When you create a private endpoint for Azure Blob Storage and enable private DNS zone integration, Azure automatically creates the zone privatelink.blob.core.windows.net. An A record is added mapping storage1.blob.core.windows.net to the private endpoint's private IP address. Each storage service has a dedicated DNS zone: blob → privatelink.blob.core.windows.net, file → privatelink.file.core.windows.net, queue → privatelink.queue.core.windows.net, table → privatelink.table.core.windows.net.

---

## 2) ARM Templates, Bicep, and Deployments

### Q9
You have an ARM template that will be deployed to a production subscription. Before deploying, you need to see exactly which resources will be created, modified, or deleted — without making any actual changes to the environment. Which Azure CLI command should you use?

- A. `az deployment group validate`
- B. `az deployment group create --mode Complete`
- C. `az deployment group what-if`
- D. `az deployment group export`

**Answer: C**

**Explanation:** The `az deployment group what-if` command simulates a deployment and shows the predicted impact without applying any changes. It displays resources that will be created (+), modified (~), or deleted (-). The `az deployment group validate` command only validates template syntax and parameter values — it does not show the impact on existing resources. The what-if operation is specifically designed for pre-deployment impact analysis.

---

### Q10
You have an ARM template that uses a linked template to deploy networking resources. The linked template is stored in Azure Blob Storage. Which deployment mode is supported for the linked template?

- A. Complete mode only
- B. Incremental mode only
- C. Both Complete and Incremental modes
- D. Validate mode only

**Answer: B**

**Explanation:** ARM linked templates (and nested templates) only support Incremental deployment mode. Complete mode, which deletes resources not present in the template, is not supported for linked or nested deployments. In Incremental mode, the deployment adds or updates resources defined in the template without deleting resources that are not in the template. Using linked templates is a common pattern for modular ARM template design, and all such deployments must use Incremental mode.

---

### Q11
You have multiple blobs stored in the Archive access tier. Due to an urgent business requirement, you need to restore access to blobs smaller than 5 GB as quickly as possible. What should you configure?

- A. Standard priority rehydration, moving blobs to Cool tier
- B. High priority rehydration, moving blobs to Hot tier
- C. Copy the blobs to a new storage account with Hot tier
- D. Use AzCopy to stream the archived blobs directly

**Answer: B**

**Explanation:** To restore access to archived blobs as quickly as possible, use High Priority rehydration. For blobs smaller than 10 GB, High Priority rehydration typically completes within 1 hour. Standard Priority rehydration can take 1-15 hours. Archived blobs cannot be read directly — they must first be rehydrated (moved) to either Hot or Cool tier. High Priority rehydration costs more than Standard Priority but provides the fastest access restoration.

---

### Q12
An Azure availability set named AS1 has 2 fault domains and 5 update domains, and contains 10 VMs distributed evenly across update domains. Microsoft initiates a planned maintenance operation that requires VM restarts. What is the maximum number of VMs that can be unavailable simultaneously?

- A. 1
- B. 2
- C. 5
- D. 10

**Answer: B**

**Explanation:** During planned maintenance, Azure processes one update domain at a time. With 10 VMs spread evenly across 5 update domains, each update domain contains 2 VMs. Only one update domain is taken offline at any given time, so at most 2 VMs will be simultaneously unavailable. Fault domains are used to isolate VMs from physical hardware failures and do not affect planned maintenance scheduling. This is a key distinction on the AZ-104 exam.

---

### Q13
You have an Azure App Service web app named WebApp1 running on the Free (F1) tier. You need to configure a custom domain name and bind a TLS/SSL certificate to it. You want to minimize costs. Which App Service plan tier should you upgrade to?

- A. Shared (D1)
- B. Basic (B1)
- C. Standard (S1)
- D. Premium (P1v2)

**Answer: B**

**Explanation:** Custom domain binding and SSL/TLS certificate binding require at minimum the Basic (B1) tier App Service plan. The Free (F1) and Shared (D1) tiers do not support custom domains or SSL certificates. Basic (B1) supports both features at the lowest cost. Standard (S1) also supports these features but includes additional capabilities (auto-scaling, staging slots) that come at a higher cost and are not required for this scenario. Basic is the minimum tier needed and the cost-minimizing choice.

---

### Q14
You have a storage account containing block blobs and append blobs. You create a lifecycle management policy to transition blobs from Hot to Cool tier after 30 days of no modification. Which blob type will be affected by the tiering action?

- A. Both block blobs and append blobs
- B. Append blobs only
- C. Block blobs only
- D. Page blobs only

**Answer: C**

**Explanation:** Azure Blob Storage lifecycle management tiering rules (Hot → Cool, Cool → Archive) apply only to block blobs. Append blobs and page blobs do not support tier changes via lifecycle management policies. Append blobs are designed for sequential write operations such as logging and do not have access tier support. Lifecycle management delete rules can apply to all blob types, but tiering rules are exclusive to block blobs.

---

## 3) Identity, Entra ID, and Access Management

### Q15
You need to create a Microsoft Entra ID group where membership is automatically maintained based on a user's job title attribute. Your tenant currently has Microsoft Entra ID Free license. What must you do first to enable dynamic group membership?

- A. Create the group with dynamic membership rules and define conditions
- B. Upgrade to Microsoft Entra ID Premium P1 or P2
- C. Enable Azure AD Connect hybrid synchronization
- D. Assign the Groups Administrator role to the group creator

**Answer: B**

**Explanation:** Dynamic group membership in Microsoft Entra ID requires at minimum the Microsoft Entra ID Premium P1 (or P2) license. The Free license does not include the dynamic membership feature. You must upgrade the tenant license before you can create dynamic groups with attribute-based membership rules such as `(user.jobTitle -eq "Manager")`. Without the Premium license, the dynamic membership option will not appear when creating a group.

---

### Q16
You configure SSPR (Self-Service Password Reset) with the following settings: Number of methods required to reset: 2. Available methods: Mobile app notification, Mobile app code, Email, Mobile phone. A user registers only one authentication method and then forgets their password. What happens when the user attempts to reset their password using SSPR?

- A. The user can reset their password using the single registered method
- B. The user is prompted to register an additional method before proceeding
- C. The user cannot reset their password and must contact an administrator
- D. An SSPR bypass link is sent to the user's email

**Answer: C**

**Explanation:** When SSPR is configured to require 2 authentication methods, a user who has registered only 1 method cannot complete the password reset. SSPR will not proceed because the minimum registration requirement is not satisfied. The user must contact an administrator to have their password reset manually, or they must register an additional authentication method through a temporary access pass while their password is known. This prevents unauthorized password resets with insufficient verification.

---

### Q17
You need to update the Department property for 500 users in Microsoft Entra ID using PowerShell. The users' UPNs are in a CSV file. Which PowerShell commands from the current recommended module should you use?

- A. Get-AzureADUser and Set-AzureADUser
- B. Get-MgUser and Update-MgUser
- C. Get-AzADUser and Update-AzADUser
- D. Get-EntraIDUser and Set-EntraIDUser

**Answer: B**

**Explanation:** The Microsoft Graph PowerShell SDK is the current recommended module for managing Microsoft Entra ID. Use `Get-MgUser` to retrieve user objects and `Update-MgUser` to modify user properties such as the Department attribute. The AzureAD PowerShell module (using Get-AzureADUser / Set-AzureADUser) is deprecated and being retired. The Az module's Entra ID cmdlets provide limited functionality. Microsoft Graph PowerShell (Get-MgUser / Update-MgUser) is the supported replacement.

---

### Q18
A storage account named storage1 has a CanNotDelete resource lock. A user needs to remove this lock to delete an outdated resource. What is the minimum RBAC role required to delete a resource lock?

- A. Contributor
- B. Reader
- C. Storage Account Contributor
- D. Owner

**Answer: D**

**Explanation:** To create or delete resource locks, a user must have the Microsoft.Authorization/locks/* permission. This permission is included in the Owner role. The Contributor role does NOT include the ability to manage locks — Contributors can manage resources but cannot create or remove locks. User Access Administrator also has this permission, but Owner is the standard exam answer. This is an important distinction: a Contributor can manage all resources in a scope but cannot bypass locks or manage RBAC assignments.

---

### Q19
You have an Azure subscription with multiple resource groups. A developer needs to create, configure, and delete virtual machines only in RG2. You need to grant the minimum required permissions following the principle of least privilege. What should you do?

- A. Assign the Contributor role at the subscription scope
- B. Assign the Owner role at the RG2 scope
- C. Assign the Virtual Machine Contributor role at the RG2 scope
- D. Assign the Contributor role at the RG2 scope

**Answer: C**

**Explanation:** The Virtual Machine Contributor role grants the permissions required to create, manage, and delete virtual machines without granting access to virtual networks or storage accounts they depend on (or other resource types in RG2). Assigning this role specifically at the RG2 scope ensures the developer can only manage VMs within RG2 — satisfying the principle of least privilege. Contributor at RG2 scope would grant broader rights to manage all resource types in RG2, which exceeds the stated requirement.

---

### Q20
User1 is assigned the Contributor role at the subscription scope. User1 is also separately assigned the Reader role on a resource group named RG1 within that subscription. What are User1's effective permissions on RG1?

- A. Reader permissions only, since the Reader role overrides at the resource group scope
- B. No permissions, because conflicting roles cancel each other out
- C. Contributor permissions inherited from the subscription scope
- D. The most recently assigned role applies

**Answer: C**

**Explanation:** Azure RBAC is additive — permissions from all applicable roles across all scopes are combined. A role assigned at a higher scope (subscription) is inherited by all child scopes (resource groups, resources). A more restrictive role (Reader) at a child scope (RG1) does not override a broader role at the parent scope (subscription). There is no concept of a "deny" in standard RBAC role assignments (only Azure Policy deny effects and explicit deny assignments work differently). User1's effective permission on RG1 is Contributor, inherited from the subscription assignment.

---

### Q21
You need multiple Azure web applications to access the same Azure Storage account using managed identity. You also need to pre-configure the RBAC role assignment before the web apps are deployed. Which type of managed identity should you use?

- A. System-assigned managed identity
- B. User-assigned managed identity
- C. Service principal with client secret
- D. Service principal with certificate

**Answer: B**

**Explanation:** User-assigned managed identity is a standalone Azure resource that exists independently from any specific Azure service. It can be created before the web apps are deployed, pre-configured with RBAC role assignments (e.g., Storage Blob Data Contributor on the storage account), and then assigned to multiple web apps at deployment time. System-assigned managed identity is created with a specific resource, cannot be pre-configured independently, and cannot be shared across multiple resources. For multi-resource and pre-deployment scenarios, user-assigned is the correct choice.

---

### Q22
Your organization requires that administrative role activations are time-limited, require approval, and generate an audit trail. Users should have "eligible" assignments that must be explicitly activated. Which Microsoft Entra ID feature provides this?

- A. Conditional Access policies
- B. Privileged Identity Management (PIM)
- C. Identity Protection risk policies
- D. Access Reviews

**Answer: B**

**Explanation:** Microsoft Entra ID Privileged Identity Management (PIM) provides just-in-time (JIT) privileged access. With PIM, users can be assigned "eligible" roles that require explicit activation with a time limit, justification, and optional approval workflow. All activations are logged for audit purposes. This reduces the attack surface by eliminating persistent privileged access. PIM requires Microsoft Entra ID Premium P2 license.

---

## 4) Governance, Policy, and Recovery

### Q23
You create a monthly budget for a subscription. When the budget reaches the alert threshold, you need email notifications sent to all users who currently hold the Owner or Contributor roles on the subscription. Which notification option in the budget alert configuration should you use?

- A. Action groups with individual email addresses
- B. Contact emails with a specific distribution list
- C. Contact roles: Owner and Contributor
- D. Azure Monitor metric alert rules

**Answer: C**

**Explanation:** Azure Cost Management budgets support "Contact roles" as a notification target, which dynamically sends budget alerts to all users in the current scope who hold the specified RBAC roles (e.g., Owner, Contributor, Reader). This is automatically updated as role assignments change — you don't need to maintain static email lists. Action groups require specifying individual email addresses (static) and are better for custom notification routing or automated actions.

---

### Q24
An action group sends email alerts to a security team distribution list at sec-alerts@contoso.com. The team occasionally misses critical alerts even though all alert rules are confirmed to be firing. The alert rules and action group configuration appear correct. What is the most likely explanation?

- A. The action group has reached the maximum of 10 email recipients
- B. Azure Monitor rate-limits email to 100 emails per hour per email address
- C. Email notifications require SMTP relay configuration in Azure
- D. The distribution list is rejecting emails from Azure Monitor

**Answer: B**

**Explanation:** Azure Monitor action groups enforce a rate limit of 100 email notifications per hour per email address. If more than 100 alerts fire within an hour targeting the same recipient, subsequent emails are suppressed until the rate limit window resets, and a rate-limiting notification is sent instead. This is a common cause of intermittent missed email notifications when alerts are confirmed to be firing correctly. To mitigate this, use more specific alert scopes or consider using different notification channels (e.g., webhook or ITSM connector) for high-volume alert scenarios.

---

### Q25
You have Azure Site Recovery protecting a three-tier application (SQL, Application, and Web servers). You are configuring a Recovery Plan to ensure SQL servers come online first, followed by Application servers, then Web servers. How should you configure the startup groups?

- A. Add all VMs to Group 1 and add manual steps between each tier
- B. SQL in Group 1, Application servers in Group 2, Web servers in Group 3
- C. Web servers in Group 1, Application in Group 2, SQL in Group 3
- D. Add all VMs to the same group with boot delay settings

**Answer: B**

**Explanation:** Azure Site Recovery Recovery Plans execute startup groups sequentially: Group 1 starts and completes before Group 2 begins, and so on. All VMs within a single group start in parallel. For a three-tier application that requires SQL → App → Web startup order, place SQL servers in Group 1, Application servers in Group 2, and Web servers in Group 3. This ensures the database layer is available before the application layer starts, and the application layer is ready before the web layer, maintaining application dependency order.

---

### Q26
Virtual machines replicate from the East US primary region to West US secondary region using Azure Site Recovery. A major outage occurs in East US. You fail over the VMs to West US and commit the failover. The East US region recovers. What must you do FIRST to prepare the VMs in West US to replicate back to East US?

- A. Create a new Recovery Services vault in East US
- B. Delete the VMs in West US and recreate them in East US
- C. Perform a Re-protect operation on the VMs in West US
- D. Configure a new replication policy targeting East US

**Answer: C**

**Explanation:** After a failover and commit, the original replication relationship is broken — there is no active replication. To enable the VMs (now running in West US) to replicate back to East US, you must perform a Re-protect operation. Re-protect reverses the replication direction: it configures West US as the new source and East US as the new target. Only after Re-protect completes can you perform a Failback to move the VMs back to East US. No new vault or replication policy creation is needed — the existing infrastructure is reused.

---

### Q27
You need to ensure all newly created Azure resources automatically receive a specific cost center tag. You also need existing resources without the tag to be remediated automatically via a remediation task. Which Azure Policy effect should you use?

- A. Audit
- B. Deny
- C. Append
- D. Modify

**Answer: D**

**Explanation:** The Modify effect in Azure Policy can add, update, or remove tags and resource properties. For new resources, Modify prevents deployment unless the tag condition is met (or adds the tag automatically, depending on configuration). Crucially, Modify supports remediation tasks that can be triggered to update existing non-compliant resources. The Append effect only adds to properties and cannot update existing values. Audit only flags non-compliance without taking action. Modify with a remediation task is the correct approach for auto-tagging both new and existing resources.

---

### Q28
You configure diagnostic settings for Azure Storage accounts to send resource logs to a Log Analytics workspace. You want to use category groups to simplify configuration. Which two category groups are available for selection?

- A. allLogs and operationalLogs
- B. allLogs and auditLogs
- C. resourceLogs and securityLogs
- D. diagnosticLogs and complianceLogs

**Answer: B**

**Explanation:** Azure diagnostic settings support two built-in category groups: allLogs (collects all available resource log categories for the service) and auditLogs (collects audit and security-related logs). Using category groups simplifies configuration and ensures new log categories added by Microsoft in the future are automatically included in allLogs without requiring manual updates. Individual log categories can also still be selected separately if more granular control is needed.

---

### Q29
You have an Azure Key Vault that contains encryption keys. You attempt to configure a rotation reminder for a key but the rotation reminder option appears grayed out. What is the most likely reason?

- A. The key does not have an expiration date set
- B. The key's creation time (keyCreationTime) attribute is not set
- C. The Key Vault does not have soft delete enabled
- D. The user does not have Key Vault Contributor role

**Answer: B**

**Explanation:** Azure Key Vault key rotation reminders rely on the key's creation time (keyCreationTime attribute) to calculate relative rotation intervals. If the keyCreationTime property is not set on the key, the rotation reminder configuration option will be unavailable (grayed out) in the portal. Setting the keyCreationTime attribute on the key allows the rotation policy to be configured with reminder notifications based on elapsed time since key creation.

---

### Q30
You have a management group hierarchy with a parent management group MG-Root containing child management groups MG-Prod and MG-Dev. You assign a policy at MG-Root to restrict resource creation to approved Azure regions. Which scopes will the policy apply to?

- A. Only the MG-Root management group itself
- B. All subscriptions directly assigned to MG-Root only
- C. All management groups, subscriptions, resource groups, and resources within the MG-Root hierarchy
- D. Only the subscriptions in MG-Prod and MG-Dev, not MG-Root itself

**Answer: C**

**Explanation:** Azure Policy assignments are inherited downward through the entire management group hierarchy. A policy assigned at MG-Root applies to MG-Root, all child management groups (MG-Prod, MG-Dev), all subscriptions under those management groups, all resource groups within those subscriptions, and all resources within those resource groups. This hierarchical inheritance is a fundamental property of management groups — policies assigned at higher scopes are automatically applied to all descendant scopes.

---

## 5) Compute, App Service, and AKS

### Q31
You deploy a Custom Script Extension to a Linux VM using an ARM template. The extension fails to execute and you need to review the extension logs. What is the correct log file path on the Linux VM?

- A. /var/log/waagent.log
- B. /var/log/azure/custom-script/handler.log
- C. /var/log/azure/custom-script-extension/handler.log
- D. /tmp/azure/customscript.log

**Answer: C**

**Explanation:** The Custom Script Extension for Linux writes handler logs to `/var/log/azure/custom-script-extension/handler.log`. This log file contains detailed information about extension initialization, script download attempts, and execution status. The stdout and stderr output from the executed script are also captured in separate log files within the same directory path. This is the primary location for troubleshooting Custom Script Extension failures on Linux VMs.

---

### Q32
You enable VM Insights on virtual machines in your environment. After enabling VM Insights and waiting for data collection, you notice that the Map tab shows no data or dependencies. What additional component must be installed on the VMs to populate the Map feature?

- A. Log Analytics Agent (MMA)
- B. Azure Monitor Agent (AMA) with performance counters
- C. Dependency Agent
- D. Network Watcher Agent extension

**Answer: C**

**Explanation:** The VM Insights Map feature requires the Dependency Agent to be installed on virtual machines. The Dependency Agent collects detailed data about running processes, active network connections, and port mappings between services. While Azure Monitor Agent (or Log Analytics Agent) is required for VM Insights performance data collection, the Map feature specifically depends on the Dependency Agent for dependency visualization. Without the Dependency Agent, the Map tab will be empty even after VM Insights is enabled.

---

### Q33
You have an Azure App Service web app named WebApp1 with a staging deployment slot. The staging slot has an application setting named DatabaseConnection that points to the staging database. When you swap staging to production, you need the DatabaseConnection in the staging slot to always retain its staging database value (not follow the code to production). What should you configure?

- A. Store DatabaseConnection as a Key Vault secret reference
- B. Enable auto swap on the staging slot
- C. Mark DatabaseConnection as a deployment slot setting (sticky setting)
- D. Configure the setting at the App Service plan level

**Answer: C**

**Explanation:** Deployment slot settings (sticky settings) are bound to the specific slot and do not move during a slot swap. If DatabaseConnection is marked as a deployment slot setting in the staging slot, its value stays in staging after the swap — it does not follow the code to production. Conversely, the production slot has its own value for DatabaseConnection that also stays in production. Non-sticky settings travel with the deployed code during the swap. Sticky settings allow different configurations per slot for environment-specific values.

---

### Q34
You have an Azure App Service web app with staging and production deployment slots. You need code deployed to the staging slot to automatically swap into production after the application has successfully warmed up, without requiring manual operator intervention. What should you configure?

- A. Configure a GitHub Actions workflow to trigger the swap after deployment
- B. Enable auto swap targeting the production slot on the staging slot
- C. Configure continuous deployment from the main branch directly to production
- D. Set up a deployment center with the staging slot as the primary target

**Answer: B**

**Explanation:** Auto swap is an App Service feature that automatically triggers a slot swap after code is deployed to a slot and the application has completed its warm-up (startup) process. You configure auto swap on the staging slot, specifying the production slot as the target. After code is deployed to staging, App Service runs the warm-up sequence, and upon successful startup, automatically swaps staging into production. This eliminates manual swap steps in CI/CD pipelines. Auto swap requires the Standard tier or higher.

---

### Q35
You need to deploy a container workload that uses two containers: a main application container and a sidecar logging container. Both containers must run in the same Azure Container Instances container group and share a network namespace. Which operating system must the container group use?

- A. Windows Server 2022
- B. Windows or Linux (both are supported)
- C. Linux
- D. Windows Server Core

**Answer: C**

**Explanation:** Multi-container groups in Azure Container Instances — including sidecar patterns where containers share a network and storage — are only supported on Linux. Windows-based ACI container groups do not support multi-container deployments. If you need to run a main container and a sidecar container (logging, monitoring, etc.) in the same container group with a shared network namespace, you must select Linux as the operating system.

---

### Q36
You have an AKS cluster with a single Linux system node pool. You need to add a Windows node pool to run Windows Server container workloads. Before adding the Windows node pool, you verify a critical prerequisite about the load balancer SKU. Which statement is correct?

- A. The AKS cluster must use the Basic SKU load balancer
- B. The AKS cluster must use the Standard SKU load balancer
- C. No load balancer is required for Windows node pools
- D. A separate load balancer must be created for the Windows node pool

**Answer: B**

**Explanation:** To add Windows node pools to an AKS cluster, the cluster must use the Standard SKU load balancer. Basic SKU load balancers do not support multiple node pools, which is a prerequisite for Windows node pool support. Additionally, Windows node pools require Azure CNI (Container Network Interface) networking — kubenet is not supported for Windows node pools. The Standard SKU load balancer requirement is specifically tested on the AZ-104 exam as a prerequisite for Windows AKS node pools.

---

### Q37
You create an Azure Monitor metric alert rule for a virtual machine with two conditions using AND logic: CPU Percentage > 90% AND Available Memory Bytes < 1 GB. The evaluation frequency is 5 minutes. You observe that the alert fires only occasionally even during heavy load. What is the correct behavior of AND-condition alerts?

- A. Conditions are evaluated independently; either condition alone can trigger the alert
- B. All conditions must be true within the same evaluation window for the alert to fire
- C. Conditions are evaluated sequentially; Condition 1 must be sustained for 10 minutes before Condition 2 is checked
- D. The alert fires when the combined weighted average of all conditions exceeds a threshold

**Answer: B**

**Explanation:** When multiple metric conditions are combined with AND logic in an Azure Monitor alert rule, all conditions must be simultaneously true within the same evaluation window for the alert to trigger. Both CPU > 90% AND Available Memory < 1 GB must occur together in the same 5-minute evaluation period. This stateful, time-slice-based evaluation helps reduce alert noise from transient individual metric spikes. If either condition returns to normal during the evaluation window, the alert does not fire.

---

### Q38
You configure Azure Backup for a Windows VM and enable application-consistent backups. What is the primary advantage of application-consistent backup over crash-consistent backup for database workloads?

- A. Application-consistent backup is faster to complete
- B. Application-consistent backup requires no VM agent
- C. Application-consistent backup uses VSS to flush and quiesce in-flight transactions before the snapshot
- D. Application-consistent backup stores data in lower-cost archive storage

**Answer: C**

**Explanation:** Application-consistent backup uses the Windows Volume Shadow Copy Service (VSS) on Windows (or pre/post-freeze scripts on Linux) to coordinate with applications. VSS signals running applications (like SQL Server) to flush and complete all in-flight transactions, clear write caches, and reach a consistent state before the snapshot is taken. This ensures that databases can be restored to a clean, consistent state without needing transaction log replay or crash recovery. Crash-consistent backup captures disk state as-is and may result in partial transactions that require recovery after restore.

---

## 6) Monitoring and Alerts

### Q39
You need to create an Azure Monitor alert that triggers when a specific virtual machine is deleted from a resource group. Which type of alert rule should you create?

- A. Metric alert
- B. Log query alert
- C. Activity log alert
- D. Smart detection alert

**Answer: C**

**Explanation:** Activity log alerts fire when specific events are recorded in the Azure Activity Log. The Activity Log captures all Azure Resource Manager control-plane operations, including resource creation, modification, and deletion. To alert when a VM is deleted, create an Activity Log alert rule scoped to the subscription or resource group, filtering for the "Delete Virtual Machine" operation. Metric alerts monitor numeric performance data. Log query alerts analyze data in Log Analytics. Activity log alert is the appropriate type for control-plane events.

---

### Q40
You have a Log Analytics workspace. You need to grant a security analyst the ability to run log queries and view query results without allowing them to modify workspace configuration, create alert rules, or manage data sources. Which role should you assign?

- A. Log Analytics Contributor
- B. Monitoring Contributor
- C. Log Analytics Reader
- D. Monitoring Reader

**Answer: C**

**Explanation:** The Log Analytics Reader role grants read-only access to a Log Analytics workspace. Users with this role can view workspace settings, run log queries, view saved searches, and access query results. They cannot create or modify workspace settings, alert rules, data sources, or automation rules. This is the minimum role needed for query-only access. Log Analytics Contributor grants write permissions and is appropriate for users who need to create queries, alerts, and configure the workspace.

---

### Q41
You need to configure a Conditional Access policy to block sign-ins from IP addresses outside your organization's office networks. Your office network uses five different public IP ranges. What should you create in Microsoft Entra ID to use in the Conditional Access policy?

- A. A user risk policy with network location exclusions
- B. A Named Location with your office IP ranges, then exclude it in the policy
- C. An Identity Protection sign-in risk policy
- D. A Conditional Access policy with device compliance as the condition

**Answer: B**

**Explanation:** Conditional Access Named Locations allow you to define sets of IP ranges or geographic locations. By creating a Named Location containing your five office IP ranges and then configuring a Conditional Access policy to block access when the sign-in is NOT coming from that Named Location, you effectively block all sign-ins from outside your office networks. This is a common pattern for restricting access to organizational IP ranges. Named Locations can also be marked as trusted for use in risk calculations.

---

### Q42
Your organization has a hybrid identity environment using Azure AD Connect for synchronization. SSPR is enabled, but users report that after resetting their password via the Azure portal, they cannot log into their on-premises workstations with the new password. What feature of Azure AD Connect must be enabled to fix this?

- A. Pass-through authentication
- B. Password hash synchronization
- C. Password writeback
- D. Seamless single sign-on

**Answer: C**

**Explanation:** Password writeback is an Azure AD Connect feature that synchronizes password changes made in Microsoft Entra ID (including SSPR) back to the on-premises Active Directory. Without password writeback, a user can reset their cloud password but their on-premises AD password remains unchanged, preventing on-premises login. Password writeback requires Microsoft Entra ID Premium P1 or P2 and must be configured in the Azure AD Connect wizard under optional features.

---

### Q43
Your organization wants to provide external business partners from partner-company.com with access to your internal Azure applications without requiring them to have a Microsoft Entra ID account in your tenant. Which feature should you use?

- A. Microsoft Entra External ID B2C (Business-to-Consumer)
- B. Microsoft Entra ID B2B (Business-to-Business) collaboration
- C. Azure AD Application Proxy
- D. Microsoft Entra ID External Identities with social login

**Answer: B**

**Explanation:** Microsoft Entra ID B2B collaboration allows you to invite external users as guests to your tenant. Guest users authenticate with their own organization's identity provider and can access the resources you share with them. B2B does not require creating new user accounts in your tenant — guests use their existing work accounts. Microsoft Entra External ID B2C is designed for consumer-facing applications where end users sign in with consumer identities. B2B is the correct feature for partner organization access scenarios.

---

### Q44
You have an Azure Key Vault storing sensitive encryption keys. You want to ensure that if a key is accidentally deleted, it remains recoverable for 90 days. You also want to prevent anyone — including administrators — from permanently deleting the key during the recovery period. Which two features should you enable?

- A. Soft delete with 90-day retention and purge protection
- B. Key rotation policy and versioning
- C. Purge protection and RBAC authorization only
- D. Backup and export with 90-day retention

**Answer: A**

**Explanation:** Soft delete retains deleted Key Vault objects (keys, secrets, certificates) for a configurable retention period (7-90 days), allowing recovery. Purge protection prevents permanent deletion (purge) of soft-deleted objects during the retention period, even by users with Key Vault Administrator roles. Together, these two features ensure a deleted key can always be recovered within the retention window. Note: Once purge protection is enabled on a Key Vault, it cannot be disabled, and the vault becomes permanently bound to the retention period.

---

## 7) Networking and Security

### Q45
Your organization requires protection against large-scale volumetric DDoS attacks targeting Azure resources deployed in virtual networks, including automatic traffic scrubbing, attack telemetry, and post-attack analysis. Basic DDoS protection is already in place. Which service should you deploy?

- A. Azure Firewall Premium with intrusion detection
- B. Azure DDoS Protection Standard (Network Protection)
- C. NSG rules with rate limiting
- D. Azure Application Gateway with WAF v2

**Answer: B**

**Explanation:** Azure DDoS Protection Standard (now called Azure DDoS Network Protection) provides advanced protection against volumetric, protocol, and resource layer DDoS attacks. It includes automatic traffic scrubbing tuned to your specific resources, real-time metrics and diagnostics, post-attack analysis reports, and DDoS Rapid Response support. Azure DDoS Basic Protection is automatically included for all Azure services but provides only platform-level protection without resource-specific tuning or analytics. Standard protection is the appropriate upgrade for comprehensive DDoS defense.

---

### Q46
You have an Azure Traffic Manager profile configured with three endpoints across three Azure regions. You need all traffic to be directed to the endpoint in Region A as long as it's healthy. If Region A becomes unavailable, traffic should automatically fail over to Region B. If Region B is also unavailable, traffic should go to Region C. Which routing method should you configure?

- A. Weighted routing with weights 100, 50, and 10
- B. Performance routing based on lowest latency
- C. Priority routing with Priority 1 (Region A), Priority 2 (Region B), Priority 3 (Region C)
- D. Geographic routing based on user location

**Answer: C**

**Explanation:** Traffic Manager Priority routing directs all traffic to the endpoint with the lowest priority number (highest priority). Traffic only fails over to the next endpoint when the current primary is detected as unhealthy by the Traffic Manager health probe. By assigning Region A = Priority 1, Region B = Priority 2, Region C = Priority 3, you achieve the desired failover cascade. Weighted routing distributes traffic as a percentage. Performance routing selects the lowest-latency endpoint regardless of failure state.

---

### Q47
You have VNet1 and VNet2 connected via VNet peering. VNet1 contains a Virtual Network Gateway connecting to an on-premises network via VPN. You need resources in VNet2 to access the on-premises network through VNet1's gateway. What must you configure?

- A. Create a second VPN gateway in VNet2 and configure BGP between VNet1 and VNet2
- B. Enable "Allow gateway transit" in VNet1's peering settings and "Use remote gateways" in VNet2's peering settings
- C. Configure a route table in VNet2 with UDRs pointing to VNet1's gateway IP
- D. Enable global VNet peering and configure the VPN gateway for multi-VNet support

**Answer: B**

**Explanation:** Gateway transit allows resources in a peered VNet to use the VPN gateway in another VNet. To configure this: in VNet1's peering settings (facing VNet2), enable "Allow gateway transit" (allowing VNet1 to share its gateway). In VNet2's peering settings (facing VNet1), enable "Use remote gateways" (directing VNet2 traffic to use VNet1's gateway). This allows VNet2 resources to reach the on-premises network through VNet1's VPN gateway without deploying a separate gateway in VNet2. VNet1 must have a VPN or ExpressRoute gateway deployed for gateway transit to work.

---

### Q48
You need to monitor network connectivity, latency, and packet loss between Azure VMs in East US and a web server endpoint in West Europe continuously over time. Which Azure Network Watcher tool should you use?

- A. IP Flow Verify
- B. Packet Capture
- C. Connection Monitor
- D. Next Hop

**Answer: C**

**Explanation:** Azure Network Watcher Connection Monitor provides continuous, automated monitoring of network connectivity between sources (Azure VMs, on-premises machines with the agent) and destinations (Azure endpoints, external URLs, IP addresses). It measures round-trip time, packet loss percentage, and reachability at configured intervals, generating alerts when connectivity degrades. IP Flow Verify tests whether a specific packet would be allowed by NSG rules (one-time check). Packet Capture records raw network traffic for analysis. Next Hop shows the routing next hop for a specific destination.

---

### Q49
You need to configure Azure Firewall to allow VMs to reach Windows Update services. Microsoft's update IP addresses change periodically and are managed by Microsoft. You want a solution that does not require manual IP list maintenance. What should you configure in Azure Firewall?

- A. Network rules with Microsoft's ASN IP ranges
- B. Application rules using the WindowsUpdate FQDN tag
- C. DNAT rules forwarding update traffic to a proxy server
- D. Enable threat intelligence mode to allow update traffic

**Answer: B**

**Explanation:** Azure Firewall supports FQDN Tags in application rules, which represent pre-defined, Microsoft-maintained sets of fully qualified domain names associated with known services. The WindowsUpdate FQDN tag covers all domains required for Windows Update without requiring manual maintenance. Microsoft automatically updates the tag when the associated FQDNs change. Create an application rule in Azure Firewall allowing outbound HTTPS traffic to the WindowsUpdate FQDN tag, and the firewall will resolve and permit all necessary update traffic.

---

### Q50
You have an Azure ExpressRoute circuit connecting your on-premises datacenter to Azure. For a high-performance workload, you need network traffic from on-premises to bypass the Virtual Network Gateway and flow directly to the NICs of Azure VMs to reduce latency. Which ExpressRoute feature enables this?

- A. ExpressRoute Global Reach
- B. ExpressRoute FastPath
- C. ExpressRoute Direct
- D. ExpressRoute Premium add-on

**Answer: B**

**Explanation:** ExpressRoute FastPath improves data path performance by sending network traffic directly to Azure VM NICs, bypassing the Virtual Network Gateway in the data path. The gateway still handles routing control plane updates but is no longer in the data forwarding path, reducing latency and increasing throughput for latency-sensitive workloads. Without FastPath, all traffic flows through the gateway before reaching VMs. FastPath is supported on Ultra Performance and ErGw3AZ gateway SKUs and requires the ExpressRoute Direct port type or ExpressRoute premium.

---

*Document generated from AZ-104 course transcript. Last updated: 2026-05-24*
