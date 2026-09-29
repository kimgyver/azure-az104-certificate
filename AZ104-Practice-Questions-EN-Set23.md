# AZ-104 Practice Questions (Set 23) — English

## 1) Management Groups, Governance, and Logic Apps

### Q1
You are designing an Azure management group hierarchy. Your organization needs multiple levels of management groups below the tenant root group. What is the maximum number of management group levels you can have below the tenant root management group?

- A. 3 levels
- B. 4 levels
- C. 5 levels
- D. 6 levels

**Answer: D**

**Explanation:** Azure Management Groups support a maximum hierarchy depth of 6 levels below the tenant root management group. The root group itself is not counted, nor are subscriptions. This means an object can be at most 7 levels deep: root + 6 management group levels + subscription. Microsoft recommends keeping hierarchies at 3-4 levels for manageability, but the hard platform limit is 6 levels below root.

---

### Q2
Your organization wants all newly created Azure subscriptions to automatically be placed under the MG-Corp management group. Where in the Azure Portal do you configure the default management group for new subscriptions?

- A. Subscriptions > Manage subscriptions > Default group
- B. Microsoft Entra ID > Properties > Default management group
- C. Management Groups > Settings > Default management group
- D. Cost Management > Billing > Subscription settings

**Answer: C**

**Explanation:** The default management group for new subscriptions is configured in the Azure Portal under Management Groups > Settings > Default management group. Once changed from the Tenant Root Group, all new subscriptions created in the tenant are automatically moved to the specified management group. This is particularly useful for immediately applying governance policies and RBAC assignments to new subscriptions without manual intervention after creation.

---

### Q3
Your organization uses Azure Cost Management budgets to control spending. When a budget threshold is reached, you want to automatically trigger a workflow that sends a notification to a Slack channel and creates an incident ticket in ServiceNow. Which Azure service should you integrate with the budget action group to accomplish this?

- A. Azure Automation Runbook
- B. Azure Function
- C. Logic Apps
- D. Event Grid

**Answer: C**

**Explanation:** Azure Cost Management budgets support action groups that can trigger webhooks. Azure Logic Apps can receive HTTP webhook triggers and provide built-in connectors for hundreds of services including Slack, ServiceNow, Teams, Jira, and more. By creating a Logic App with an HTTP trigger and configuring the budget alert's action group to call the Logic App webhook URL, you can automate complex multi-step workflows across various services when a cost threshold is reached.

---

### Q4
You need to create an Azure Cost Management budget. Which scopes are supported for budget creation? (Choose all that apply)

- A. Subscription
- B. Resource group
- C. Management group
- D. Individual resources

**Answer: A, B, C**

**Explanation:** Azure Cost Management budgets can be scoped to subscriptions, resource groups, and management groups. This allows cost governance at any level of the Azure hierarchy. Budgets cannot be created for individual resources. When scoped to a management group, the budget covers all subscriptions and resource groups within that management group. Budget alerts can notify via email to contact roles/emails and trigger action groups for automated responses.

---

### Q5
You want to onboard on-premises Windows and Linux servers to Azure management capabilities (Azure Policy, Azure Monitor, Defender for Cloud) without migrating them to Azure. Which Azure service and agent should you use?

- A. Azure Site Recovery and the Mobility Service agent
- B. Azure Monitor and the Log Analytics agent
- C. Azure Arc and the Connected Machine agent
- D. Azure Migrate and the assessment agent

**Answer: C**

**Explanation:** Azure Arc for servers allows you to onboard physical and virtual machines running on-premises or in other clouds to Azure management. The Connected Machine agent (Azure Connected Machine agent) is installed on each server to register it with Azure Arc. Once onboarded, the server appears as an Azure resource and can be managed with Azure Policy for compliance, Azure Monitor for telemetry, Microsoft Defender for Cloud for security posture, and more — without migrating to Azure.

---

### Q6
You are a Managed Service Provider (MSP) and need to manage Azure resources across multiple customer tenants without requiring individual guest accounts in each tenant. Which Azure service enables cross-tenant resource management at scale?

- A. Azure B2B collaboration
- B. Azure Lighthouse
- C. Azure Advisor
- D. Cross-tenant subscription transfer

**Answer: B**

**Explanation:** Azure Lighthouse enables delegated resource management across tenants at scale for MSPs and partners. Through Azure Lighthouse, customers can delegate management of their Azure subscriptions or resource groups to the service provider's tenant. The MSP can manage resources across all customer tenants from a single control plane (their own tenant) without guest accounts in each customer tenant. Delegation can be scoped with RBAC permissions, and all operations are recorded in the customer's activity log.

---

### Q7
You configure an Azure Cost Management budget alert with a threshold of 80% of the monthly budget. The alert is configured with two conditions: Actual cost > 80% and Forecasted cost > 100%. What is the difference between these two alert condition types?

- A. Actual alert fires immediately when spend crosses 80%; Forecast alert fires when AI predicts you will exceed 100% by end of period
- B. Actual alert is for subscriptions only; Forecast alert is for resource groups only
- C. Actual alert requires approval to fire; Forecast alert fires automatically
- D. Both alert types behave identically but track different currencies

**Answer: A**

**Explanation:** Azure budget alerts support two types of conditions: (1) Actual cost alerts — fire when the actual accumulated spending crosses the defined threshold percentage during the budget period. (2) Forecast alerts — fire when Azure's spending projection (based on current consumption patterns) predicts that the total cost will exceed the threshold percentage by the end of the budget period. Forecast alerts provide proactive warning before the actual spend reaches the threshold, allowing cost control actions in advance.

---

### Q8
You want to estimate the cost of running a specific Azure architecture before deploying it. Which Azure tool should you use?

- A. Azure Advisor
- B. Azure Cost Management
- C. Azure Pricing Calculator
- D. Total Cost of Ownership (TCO) Calculator

**Answer: C**

**Explanation:** The Azure Pricing Calculator (calculator.azure.com) allows you to estimate the cost of specific Azure products and configurations before deployment. You can add services, configure their settings (region, SKU, hours, etc.), and see an estimated monthly cost. The TCO Calculator is for comparing the cost of on-premises infrastructure vs. Azure (not for estimating specific Azure deployments). Azure Cost Management shows actual incurred costs. Azure Advisor provides recommendations but does not perform pre-deployment cost estimation.

---

## 2) Networking: NAT Gateway, VNet, and Availability

### Q9
You deploy Azure Container Instances (ACI) with VNet integration (containers deployed into a subnet). The containers need to make outbound calls to external APIs on the internet. After deployment, the containers cannot reach the internet. What should you deploy to provide outbound internet connectivity?

- A. A public IP address attached directly to each container
- B. An Azure Firewall in the same subnet
- C. A NAT Gateway associated with the ACI subnet
- D. A VPN Gateway for internet access

**Answer: C**

**Explanation:** When ACI containers are deployed with VNet integration, they are placed in a private subnet and do not have default outbound internet connectivity. Azure NAT Gateway provides outbound internet connectivity for resources in a subnet without requiring public IP addresses on each resource. Associate a NAT Gateway with the subnet where ACI containers are deployed, and the containers will use the NAT Gateway's public IP for outbound internet traffic. NAT Gateway provides a consistent outbound public IP, preventing port exhaustion common with other NAT methods.

---

### Q10
You need to design a high-availability architecture for a critical web application. You consider using Availability Zones versus Availability Sets. Which statement CORRECTLY describes the difference?

- A. Availability Sets protect against datacenter-level failures; Availability Zones protect against rack/power failures within a datacenter
- B. Availability Zones protect against datacenter-level failures with 99.99% SLA; Availability Sets protect against rack and power domain failures within a single datacenter with 99.95% SLA
- C. Availability Zones are only available for storage accounts; Availability Sets are for VMs
- D. Availability Sets support automatic failover to a secondary region; Availability Zones do not

**Answer: B**

**Explanation:** Availability Zones are physically separate datacenters within the same Azure region, each with independent power, cooling, and networking. Deploying VMs across AZs protects against entire datacenter failures and provides 99.99% SLA. Availability Sets group VMs across Fault Domains (separate physical racks/power/network) and Update Domains within a single datacenter, protecting against hardware failures and planned maintenance in that datacenter with 99.95% SLA. Availability Zones provide a higher level of physical isolation than Availability Sets.

---

### Q11
You plan to create a subnet with CIDR block 10.0.0.0/29. How many IP addresses are available for Azure resources (VMs, NICs) in this subnet?

- A. 8 addresses
- B. 6 addresses
- C. 5 addresses
- D. 3 addresses

**Answer: D**

**Explanation:** A /29 CIDR block provides 8 total IP addresses. Azure reserves 5 IP addresses in every subnet: .0 (network address), .1 (default gateway), .2 and .3 (Azure DNS), .7 (broadcast address). This leaves 8 - 5 = 3 usable IP addresses for Azure resources. This reservation applies to all Azure subnets regardless of CIDR size. When planning subnet sizes, always account for the 5 reserved Azure addresses.

---

### Q12
You have two VNets (VNet-A: 10.1.0.0/16 and VNet-B: 10.1.0.0/16). You need VMs in both VNets to communicate directly. What is the problem with VNet peering in this scenario?

- A. VNet peering requires matching address spaces
- B. VNet peering is not possible because the address spaces overlap
- C. VNet peering requires a VPN Gateway in each VNet
- D. VNet peering cannot be established between VNets in the same subscription

**Answer: B**

**Explanation:** VNet peering requires that the address spaces of the peered VNets do NOT overlap. Both VNet-A and VNet-B use 10.1.0.0/16, making them completely overlapping. Azure cannot route traffic between peered VNets with overlapping address spaces because it cannot determine which VNet owns a given IP address. To enable peering, you would need to redesign one VNet with a different address space (e.g., 10.2.0.0/16). VNet peering is also non-transitive by default.

---

### Q13
You have an Azure private DNS zone named corp.internal and an on-premises DNS domain with the same name. Azure VMs use Azure DNS (168.63.129.16) and cannot resolve on-premises corp.internal records. What should you configure?

- A. Add the on-premises DNS server as a custom DNS server for the VNet
- B. Configure a conditional forwarder on the on-premises DNS server to forward corp.internal queries to Azure DNS
- C. Create a conditional forwarder DNS zone in Azure DNS pointing corp.internal to the on-premises DNS server
- D. Enable DNS zone transfer between Azure private DNS and on-premises DNS

**Answer: A**

**Explanation:** When Azure VMs need to resolve on-premises DNS records, configure the Azure VNet to use custom DNS servers (the on-premises DNS server IP addresses). This directs all DNS queries from VMs to the on-premises DNS server, which can resolve both on-premises corp.internal records and can forward Azure DNS queries to 168.63.129.16 for Azure resources. Alternatively, you can deploy a DNS forwarder VM in Azure that conditionally forwards queries to on-premises for corp.internal. The key is to use the on-premises DNS server for Azure VNet DNS resolution when on-premises record resolution is required.

---

### Q14
You have an Azure public DNS zone for contoso.com. You add a Traffic Manager profile as an endpoint. You need the apex domain (contoso.com without www) to point to the Traffic Manager profile without CNAME flattening issues. Which record type should you use in Azure DNS?

- A. CNAME record
- B. A record with Traffic Manager IP
- C. Alias record set pointing to the Traffic Manager profile
- D. NS record pointing to Traffic Manager

**Answer: C**

**Explanation:** Alias record sets in Azure DNS can point to Azure resources including Traffic Manager profiles, Azure CDN endpoints, and public IP addresses. Unlike CNAME records, alias records can be used at the zone apex (root domain, e.g., contoso.com). When the Traffic Manager profile's IP address changes, the alias record automatically reflects the new IP. Traditional CNAME records cannot be created at the zone apex (RFC limitation). Azure DNS alias record sets resolve this limitation while maintaining automatic IP updates when the target resource changes.

---

### Q15
You have an Azure Application Gateway (WAF v2 SKU) protecting a web application. You configure a WAF policy. What does WAF Detection mode do?

- A. Blocks all requests matching OWASP rules
- B. Logs requests matching WAF rules but does not block any traffic
- C. Blocks traffic only from known malicious IP addresses
- D. Enforces TLS 1.2 and disables weaker cipher suites

**Answer: B**

**Explanation:** Application Gateway WAF Detection mode monitors and logs requests that match WAF rules but does not block any traffic — all requests pass through regardless of rule matches. Detection mode is used to understand what traffic would be blocked before switching to Prevention mode. You analyze the WAF logs to identify false positives, tune exclusions, and customize rules. Once satisfied with the rule configuration, you switch to Prevention mode which actively blocks matching requests. Always start in Detection mode when first deploying WAF.

---

### Q16
You have a Standard Load Balancer with a frontend IP and multiple backend VMs. You need to configure the backend VMs to receive traffic with the original frontend IP preserved in the destination IP field (Direct Server Return scenario). What Load Balancer feature should you enable?

- A. Connection Draining
- B. High Availability Ports
- C. Floating IP (Direct Server Return)
- D. Session persistence (sticky sessions)

**Answer: C**

**Explanation:** Floating IP (also known as Direct Server Return or DSR) is a Load Balancer configuration where the destination IP in the load-balanced traffic is set to the frontend IP address rather than the VM's IP address. This is required for scenarios where the backend servers need to see the original frontend VIP in the destination IP, such as SQL Always On Availability Groups, NVA configurations, and other high-availability application patterns. The backend VM must be configured to respond on the frontend IP (e.g., via a loopback adapter) for DSR to work correctly.

---

## 3) Storage and Backup

### Q17
You use Azure Backup for Azure Files. You configure a vaulted backup policy (vault-standard) for a file share. A user accidentally deletes an entire folder and needs the data restored. Which restore options are supported for vaulted Azure Files backup?

- A. Original location restore, alternate location restore, and item-level restore
- B. Full share restore to alternate location only
- C. Item-level restore (individual file/folder) only
- D. Full share restore to original location or alternate location

**Answer: B**

**Explanation:** Azure Files vaulted backup has important restore limitations compared to operational backup. Vaulted backup only supports full share restore to an alternate location — you cannot restore to the original location (no Original Location Restore), and you cannot restore individual files or folders (no item-level restore). If item-level restore or original location restore is needed, use Azure Files operational backup (snapshot-based) instead, which supports all restore options including individual file/folder restoration.

---

### Q18
You manage Azure file shares. You are concerned about the maximum number of snapshots you can take per file share. What is the maximum number of snapshots allowed per Azure file share?

- A. 100 snapshots
- B. 200 snapshots
- C. 500 snapshots
- D. 1000 snapshots

**Answer: B**

**Explanation:** Azure file shares support a maximum of 200 snapshots per file share. This limit applies regardless of whether snapshots are created manually or by Azure Backup. When 200 snapshots exist, you must delete older snapshots before new ones can be created. Azure Backup policies manage this automatically by deleting the oldest snapshots when the limit is approached. Planning snapshot retention policies to stay well within this limit is important for operational continuity.

---

### Q19
You use Azure File Sync to tier infrequently accessed files to the cloud. A user reports that files smaller than 1 KiB are not being tiered. Is this expected behavior?

- A. No, File Sync should tier all files regardless of size
- B. Yes, File Sync has a minimum tiering size of 64 KiB
- C. Yes, File Sync has a minimum tiering size of 8 KiB — files smaller than 8 KiB are never tiered
- D. No, the minimum tiering size is 1 KiB

**Answer: C**

**Explanation:** Azure File Sync cloud tiering has a minimum file size of 8 KiB. Files smaller than 8 KiB are never tiered to the cloud, regardless of the tiering policy configuration (free space percentage or date policy). This minimum exists because tiered files are represented as reparse points on the local volume, and the reparse point itself consumes disk space. For very small files, tiering them would not meaningfully free disk space while adding I/O overhead for recall. Files at or above 8 KiB are eligible for tiering per the configured policy.

---

### Q20
You configure Azure File Sync with multiple server endpoints across different servers. What is the constraint for the cloud endpoint in a sync group?

- A. A sync group can have multiple cloud endpoints from different storage accounts
- B. A sync group can have exactly one cloud endpoint — one Azure file share in one storage account
- C. A storage account's file share can be the cloud endpoint for multiple sync groups simultaneously
- D. Cloud endpoints can be in different Azure regions from the storage sync service

**Answer: B**

**Explanation:** Each Azure File Sync sync group can have exactly one cloud endpoint, which corresponds to exactly one Azure file share in one storage account. The cloud endpoint is the authoritative copy in Azure. Additionally, a single Azure file share can only be part of one sync group — it cannot be shared across multiple sync groups. Multiple server endpoints (local file system paths on Windows servers) can be part of the same sync group, all syncing to the single cloud endpoint.

---

### Q21
You need to enable SFTP access to an Azure Storage account for a legacy application. What prerequisite must be met for the storage account?

- A. The storage account must be of type GPv2 (General Purpose v2)
- B. The storage account must have the hierarchical namespace (Azure Data Lake Storage Gen2) enabled
- C. The storage account must use Premium performance tier
- D. The storage account must be in a region with SFTP preview

**Answer: B**

**Explanation:** SFTP support for Azure Blob Storage requires the hierarchical namespace feature to be enabled, which converts the storage account to Azure Data Lake Storage Gen2 (ADLS Gen2). SFTP is not supported on standard flat-namespace blob storage accounts. Enabling hierarchical namespace is a one-way operation that cannot be reversed. Once enabled, the storage account supports SFTP users (local users) that can be configured with password or SSH key authentication. SFTP is useful for legacy workloads that require the SFTP protocol for file transfer.

---

### Q22
You review your storage account's diagnostic settings. You see two category groups available for log collection. What are the two standard category groups for storage account diagnostic logs?

- A. allLogs and operationalLogs
- B. allLogs and auditLogs
- C. resourceLogs and securityLogs
- D. activityLogs and applicationLogs

**Answer: B**

**Explanation:** Azure diagnostic settings for storage accounts (and many other Azure services) provide two standard log category groups: (1) allLogs — collects all available resource log categories for the service, ensuring no log categories are missed even when new categories are added by Microsoft. (2) auditLogs — collects security and audit-related logs only (data plane authentication and authorization logs). Using category groups simplifies configuration compared to selecting individual log categories, especially for allLogs which auto-includes newly added categories.

---

### Q23
You accidentally delete a blob from a container. You restore it using Azure Blob Storage soft delete. How long can deleted blobs be retained and restored using soft delete?

- A. Fixed 7-day retention, non-configurable
- B. Configurable 1 to 365 days retention
- C. Configurable 1 to 90 days retention
- D. Fixed 30-day retention, non-configurable

**Answer: B**

**Explanation:** Azure Blob Storage soft delete can be configured with a retention period of 1 to 365 days. During this period, deleted blobs are retained in a soft-deleted state and can be restored using the Azure Portal, Azure CLI, PowerShell, or REST API. After the retention period expires, soft-deleted blobs are permanently deleted. Blob soft delete is separate from container soft delete (which protects entire containers) and blob versioning. Microsoft recommends enabling soft delete for all storage accounts to protect against accidental deletion.

---

### Q24
You need to configure Azure Backup for Azure Blobs. Your requirement is near-continuous backup with the ability to restore to any point-in-time within the last 30 days, and you want to minimize backup storage costs. Which backup type should you configure?

- A. Vaulted backup (vault-standard) for blobs
- B. Operational backup for blobs
- C. Azure Backup for VMs with file-level recovery
- D. Azure Blob Storage versioning

**Answer: B**

**Explanation:** Azure Blob operational backup is a managed, continuous data protection solution that uses blob change tracking to enable point-in-time restore to any moment within the configured retention period (up to 360 days). Data is stored in the customer's own storage account (not in the vault), so there are no vault storage costs for the backup data — you only pay for the storage actually used. Vaulted backup for blobs stores recovery points in the vault (incurring vault storage costs) and is used when cross-region restore is needed. Operational backup is the cost-effective choice for near-continuous point-in-time recovery within the same region.

---

## 4) Compute, ACI, and Kubernetes

### Q25
You deploy both Azure Container Instances and Kubernetes (AKS) for different workloads. What is a fundamental difference in how ACI and Kubernetes handle container resource requests and limits?

- A. ACI supports resource limits separate from requests; Kubernetes does not
- B. In ACI, resource request equals the maximum (no separate limit); in Kubernetes, request (guaranteed minimum) and limit (maximum) are separate values
- C. Kubernetes assigns the same amount of resources to all containers; ACI supports variable resources
- D. ACI does not support resource requests; Kubernetes requires them

**Answer: B**

**Explanation:** ACI and Kubernetes have fundamentally different resource models. In ACI, when you specify a CPU/memory request, it is also the maximum allocated — ACI allocates exactly the requested amount and there is no separate "limit" above the request. In Kubernetes, resource requests define the guaranteed minimum that the scheduler uses for placement decisions, and resource limits define the maximum cap — pods can use resources up to the limit, and the scheduler guarantees at least the request amount. This distinction is important when right-sizing containerized applications.

---

### Q26
You deploy ACI containers into a VNet subnet for private network access. The containers need to download updates from the internet. You do not want to assign public IPs to each container. What should you deploy?

- A. Azure VPN Gateway
- B. Azure NAT Gateway on the ACI subnet
- C. Azure Application Gateway
- D. Azure ExpressRoute circuit

**Answer: B**

**Explanation:** ACI containers deployed in a VNet subnet have private IP addresses and no direct internet connectivity by default. Azure NAT Gateway provides outbound internet connectivity for all resources in a subnet through a shared, scalable outbound IP address pool. By associating a NAT Gateway with the ACI subnet, all containers can make outbound connections to the internet without individual public IPs. NAT Gateway handles SNAT (Source Network Address Translation) and prevents port exhaustion that can occur with other NAT approaches.

---

### Q27
You need to upgrade an AKS cluster's node pools to a new Kubernetes version. What is the recommended order for upgrading node pools in an AKS cluster that has both a system node pool and a user node pool?

- A. Upgrade user node pools first, then the system node pool
- B. Upgrade the control plane first, then the system node pool, then user node pools
- C. Upgrade all node pools simultaneously using the --all flag
- D. Upgrade user node pools only; the system node pool upgrades automatically

**Answer: B**

**Explanation:** The recommended AKS upgrade order is: (1) Upgrade the control plane (API server) first. (2) Upgrade the system node pool. (3) Upgrade user node pools. During node pool upgrades, nodes are cordoned (no new pods scheduled), drained (existing pods evicted and rescheduled on other nodes), then the node is upgraded, and finally uncordoned. The system node pool hosts critical system pods (kube-dns, kube-proxy, etc.) and must be upgraded before user node pools to maintain cluster functionality.

---

### Q28
You have a VMSS with autoscaling configured. When scale-in occurs, which VMs are deleted by default?

- A. VMs with the lowest CPU utilization
- B. VMs that have been running the longest (oldest)
- C. The newest VMs are deleted first
- D. VMs are deleted randomly

**Answer: C**

**Explanation:** Azure VMSS default scale-in policy deletes the newest virtual machines first (last created). This approach preserves VMs that may have longer warm-up times or cached data. The default "NewestVM" policy can be customized. Other available policies include "OldestVM" (delete longest-running VMs first — useful for patching) and "Default" (balanced approach prioritizing zones and fault domains for distribution). The scale-in policy can be configured in the VMSS properties.

---

### Q29
You provision an Azure Dedicated Host for your organization's VMs. How is an Azure Dedicated Host billed?

- A. Per VM running on the host
- B. Per CPU core used by VMs on the host
- C. Per host (regardless of how many VMs run on it)
- D. Per hour of active VM compute on the host

**Answer: C**

**Explanation:** Azure Dedicated Hosts are billed per host instance, not per VM. You pay for the entire host regardless of how many VMs are running on it (even zero VMs). The host type determines the SKU family and number of cores/VMs the host can support. Dedicated Hosts allow you to have dedicated physical hardware isolated from other customers, meeting compliance requirements, and enabling control of Azure maintenance events. Despite paying for the whole host, licensing benefits (Azure Hybrid Benefit, Reserved Instances for hosts) can significantly reduce costs.

---

### Q30
You create an Azure Spot VM with an eviction policy. What is the difference between the Deallocate and Delete eviction policies?

- A. Deallocate stops the VM but charges for the stopped VM; Delete removes everything and stops billing
- B. Deallocate stops the VM and preserves disks; Delete removes the VM and all managed disks
- C. Delete removes only the VM; Deallocate removes both the VM and its associated storage
- D. Deallocate sends a 30-second warning; Delete sends no warning

**Answer: B**

**Explanation:** When an Azure Spot VM is evicted, the behavior depends on the eviction policy: (1) Deallocate policy — the VM is stopped and moved to a deallocated state. Managed disks are preserved. You stop paying for compute (vCPU/memory), but you continue paying for disk storage. The VM can be restarted manually when capacity is available. (2) Delete policy — the VM and all associated managed disks are deleted. No storage costs after eviction. Both policies provide a 30-second eviction notice via Azure Scheduled Events. Choose Delete for truly stateless workloads, Deallocate if you need to recover disk data after eviction.

---

### Q31
You need to provide a user read-only access to logs in a Log Analytics workspace. The user should be able to run queries and view results but must not be able to modify workspace settings, create alert rules, or manage data connectors. Which role provides the minimum required access?

- A. Log Analytics Contributor
- B. Monitoring Contributor
- C. Monitoring Reader
- D. Log Analytics Reader

**Answer: D**

**Explanation:** Log Analytics Reader provides read-only access to a Log Analytics workspace. Users with this role can view all monitoring data and settings, search and view monitoring data, run log queries, view saved searches, and view workspace analytics. They cannot modify workspace settings, create or edit alert rules, manage data connectors, or change workspace access control. This is the minimum role required for query-only access. Monitoring Reader is even more restrictive and doesn't include query execution in some scenarios.

---

### Q32
Your organization uses a Log Analytics workspace. Some users should only see logs from resources they have access to via RBAC (resource-level access), while others should see all logs in the workspace (workspace-level access). Which Azure Monitor feature controls this?

- A. Log Analytics workspace pricing tier
- B. Azure Monitor resource log category filters
- C. Log Analytics workspace access control mode (Resource-context vs Workspace-context)
- D. Azure Monitor alert rule scope

**Answer: C**

**Explanation:** Log Analytics workspace access control mode determines how logs are accessible: (1) Workspace-context mode — users with workspace RBAC permissions (Log Analytics Reader/Contributor) can see all logs in the workspace regardless of resource permissions. (2) Resource-context mode — users only see logs from resources they have at least Reader access to via Azure RBAC, even if they don't have explicit workspace permissions. This allows fine-grained access control without granting workspace-level permissions to all users. The mode is configured in the workspace's Properties > Access control mode.

---

## 5) Identity, Security, and MFA

### Q33
You need to configure Microsoft Entra ID PIM for a user who should have the Global Administrator role available on-demand but not permanently active. What assignment type should you use?

- A. Active assignment with time limit
- B. Eligible assignment
- C. Permanent active assignment
- D. Delegated assignment

**Answer: B**

**Explanation:** Eligible assignments in PIM require users to explicitly activate the role when they need it. Users with an eligible role assignment must go through the PIM activation process (which can require MFA, justification, and approval) to become active in the role for a limited time. After the activation period expires, the role deactivates automatically. This is the core JIT (Just-In-Time) access pattern of PIM. Active assignments (option A) make the role permanently available without activation. Eligible assignments are the recommended approach for privileged roles.

---

### Q34
You need to assign PIM-managed roles to users in your organization. Which Azure role is required to manage PIM role assignments?

- A. Global Reader
- B. Privileged Role Administrator
- C. User Access Administrator
- D. Security Administrator

**Answer: B**

**Explanation:** The Privileged Role Administrator role in Microsoft Entra ID is required to manage PIM role assignments, including creating and managing eligible and active assignments, configuring role settings (activation duration, approval requirements, MFA requirements), and reviewing PIM audit logs. Global Administrator can also perform these tasks. Security Administrator can view PIM but cannot manage role assignments. User Access Administrator manages Azure RBAC assignments (not Entra ID roles in PIM).

---

### Q35
You configure Microsoft Entra ID Identity Protection. A user's account shows a high sign-in risk during a login attempt. You configure a risk-based Conditional Access policy to require MFA for medium and high risk sign-ins. The user does not have MFA registered. What happens?

- A. The login proceeds without MFA because the user hasn't registered it
- B. The login is blocked because the user cannot satisfy the MFA requirement
- C. The risk is automatically remediated and the user logs in normally
- D. The user is sent an email to register MFA before proceeding

**Answer: B**

**Explanation:** When a Conditional Access policy requires MFA for risky sign-ins and the user hasn't registered MFA methods, the login is blocked because the user cannot satisfy the grant control requirement. The conditional access policy evaluation fails when the user cannot complete the required control. This is why it's important to ensure all users complete MFA registration before enabling risk-based Conditional Access policies. You can use an MFA registration policy in Identity Protection or a separate Conditional Access policy to enforce MFA registration for all users proactively.

---

### Q36
Your organization requires users to use strong authentication methods. Which authentication method provides phishing-resistant authentication in Microsoft Entra ID?

- A. SMS verification code
- B. Voice call
- C. FIDO2 security key
- D. Time-based one-time password (TOTP)

**Answer: C**

**Explanation:** FIDO2 security keys (hardware security keys like YubiKey) provide phishing-resistant authentication because they use public key cryptography tied to the specific origin (domain). Even if a user is tricked into entering their credentials on a phishing site, the FIDO2 key will not authenticate because the origin doesn't match. SMS codes, voice calls, and TOTP are vulnerable to phishing and real-time attacks (man-in-the-middle, SIM swapping). Microsoft's passwordless authentication methods — FIDO2 keys, Windows Hello for Business, and the Microsoft Authenticator passkey (preview) — are considered phishing-resistant.

---

### Q37
You configure SSPR (Self-Service Password Reset) with the following settings: Required authentication methods: 2; Available methods: mobile app notification, mobile app code, email, mobile phone. A user has registered only their mobile phone (SMS). When they try to reset their password, what happens?

- A. They can use SMS as the single factor since mobile phone is available
- B. They are prompted to register an additional method before resetting
- C. The reset fails — they cannot proceed with only one registered method when two are required
- D. The system automatically sends a reset link to their registered email

**Answer: C**

**Explanation:** When SSPR requires 2 authentication methods and a user only has 1 method registered, the password reset cannot be completed. Azure SSPR validates that the minimum number of required authentication methods are registered before allowing the reset flow to proceed. The user is blocked from resetting their password. The solution is for the user to register additional methods while they still know their current password, or to have an administrator reset the password manually. This is why SSPR rollout should include a registration campaign.

---

### Q38
You have a customer-facing web application. Customers should be able to sign in using their Google, Facebook, or Microsoft personal accounts. Which Microsoft Entra feature supports this consumer identity scenario?

- A. Microsoft Entra ID B2B collaboration
- B. Microsoft Entra External ID (formerly B2C)
- C. Microsoft Entra Domain Services
- D. Application Proxy

**Answer: B**

**Explanation:** Microsoft Entra External ID (formerly Azure AD B2C) is designed for consumer-facing (customer identity and access management - CIAM) scenarios. It allows customers to sign in using social accounts (Google, Facebook, Apple, Twitter), local accounts (email + password), or federated enterprise accounts. It provides customizable sign-in/sign-up flows, customer profile management, and scales to millions of users. B2B collaboration (option A) is for external business partners using their work accounts to access your internal apps, not for consumer-facing applications.

---

### Q39
You have an Azure App Service web application with a custom domain. You need to configure HTTPS with a dedicated IP address for SSL because your users have older devices that don't support Server Name Indication (SNI). Which SSL binding type should you use?

- A. SNI SSL
- B. IP-based SSL
- C. Mutual TLS (mTLS)
- D. Free App Service Managed Certificate

**Answer: B**

**Explanation:** IP-based SSL binds an SSL certificate to a dedicated public IP address. This is required for older clients (browsers, devices) that do not support SNI (Server Name Indication). With IP-based SSL, the web app gets a dedicated IP address assigned, and all HTTPS traffic to that IP uses the associated certificate. SNI SSL is the default and modern approach — it allows multiple certificates on a single IP address using the SNI TLS extension. SNI is supported by all modern browsers and devices. IP-based SSL incurs additional charges for the dedicated IP address.

---

### Q40
You need to configure Azure Key Vault access for an application. Your organization wants to use fine-grained permissions (e.g., secret-level RBAC) and wants PIM integration for privileged Key Vault operations. Which Key Vault permission model should you use?

- A. Vault access policies
- B. Azure RBAC permission model
- C. Shared Access Signatures
- D. Managed identity with no explicit permissions

**Answer: B**

**Explanation:** Azure Key Vault supports two permission models: (1) Vault access policies — grant permissions at the vault level (all secrets, keys, or certificates); cannot grant per-secret permissions. Limited integration with PIM. (2) Azure RBAC permission model — uses Azure role assignments (built-in roles like Key Vault Secrets User, Key Vault Administrator) that can be scoped to the vault level or to individual secrets/keys/certificates. Works natively with PIM for just-in-time access to privileged Key Vault roles. For fine-grained, PIM-integrated access, Azure RBAC is the recommended model.

---

## 6) Monitoring, Alerts, and Application Insights

### Q41
You have a metric alert rule configured to fire when CPU > 90%. The rule fires and creates an alert. The CPU then drops below 90% and remains low. What is the behavior of a Stateful alert vs a Stateless alert?

- A. Stateful: fires every 5 minutes; Stateless: fires once and auto-resolves
- B. Stateful: fires once and auto-resolves when condition clears; Stateless: fires every evaluation period the condition is true
- C. Both behave identically but Stateful sends email; Stateless creates ITSM tickets
- D. Stateful: requires manual acknowledgment; Stateless: auto-resolves after 24 hours

**Answer: B**

**Explanation:** Azure Monitor metric alerts support two alert processing modes: (1) Stateful — the alert fires once when the condition is first met and is considered "active." When the condition clears (CPU drops below 90%), the alert automatically resolves (transitions to "Resolved" state). You receive one notification for firing and one for resolution. (2) Stateless — the alert fires every evaluation period that the condition is true, regardless of previous state. There is no automatic resolution notification. Stateful is the default and recommended mode for most scenarios as it reduces notification noise.

---

### Q42
You have a microservices application with Application Insights. A user reports that their request shows as slow but you cannot trace it across multiple services. Which Application Insights feature correlates requests across multiple microservices?

- A. Application Map
- B. Live Metrics
- C. Distributed tracing (using operation_Id / W3C trace context)
- D. Availability tests

**Answer: C**

**Explanation:** Application Insights distributed tracing automatically correlates telemetry (requests, dependencies, exceptions) across multiple services using the W3C Trace Context standard (traceparent header). A unique operation_Id is assigned when a request first enters the application and is propagated to all downstream calls. You can search for an operation_Id in Application Insights to see all telemetry from all services participating in that request chain. Application Map shows service topology and dependency health but does not show individual request traces. The End-to-End Transaction Details view shows all correlated telemetry.

---

### Q43
Your Application Insights resource receives 1 million telemetry events per day. You want to reduce costs to approximately 500,000 events per day while maintaining representative sampling across all sessions. Which sampling approach preserves complete sessions?

- A. Adaptive sampling
- B. Fixed-rate sampling
- C. Ingestion sampling
- D. Manual filtering

**Answer: B**

**Explanation:** Fixed-rate sampling in Application Insights uses the same sampling rate for both the SDK (client-side) and the server-side, and crucially, it samples by user session — all telemetry for a sampled session is included, ensuring complete end-to-end traces. This session-aware sampling preserves correlated request chains. Adaptive sampling automatically adjusts rates but does not guarantee session completeness. Ingestion sampling drops data after it reaches the AI endpoint (post-collection), which can break correlated traces. For predictable volume reduction with session integrity, fixed-rate sampling at the SDK is recommended.

---

### Q44
You write a KQL query to analyze Azure Monitor logs. You need to count the number of errors per computer for the last 24 hours from the Event table. Which KQL query is correct?

- A. `Event | where TimeGenerated > ago(24h) | count by Computer`
- B. `Event | where TimeGenerated > ago(24h) and EventLevelName == "Error" | summarize ErrorCount=count() by Computer`
- C. `Event | filter TimeGenerated > ago(24h) | group by Computer | count`
- D. `Event | select Computer, count(*) where TimeGenerated > ago(24h)`

**Answer: B**

**Explanation:** The correct KQL syntax uses `where` for filtering, `and` for multiple conditions, and `summarize` for aggregation. The query: (1) Starts with the `Event` table, (2) Filters to the last 24 hours using `where TimeGenerated > ago(24h)`, (3) Filters to errors using `and EventLevelName == "Error"`, (4) Aggregates with `summarize ErrorCount=count() by Computer` to count errors grouped by computer. KQL uses `summarize` (not `group by`) for aggregation, `where` (not `filter`) for filtering rows, and `project` for column selection.

---

### Q45
You want to receive proactive notifications about Azure service outages and planned maintenance for services you use in your subscriptions. Which Azure service provides this?

- A. Azure Monitor activity log alerts
- B. Azure Service Health alerts
- C. Microsoft Defender for Cloud recommendations
- D. Azure Advisor notifications

**Answer: B**

**Explanation:** Azure Service Health provides personalized notifications about Azure service issues that affect your subscriptions and regions. It covers three types of events: (1) Service Issues — current outages or degradations affecting Azure services in your regions, (2) Planned Maintenance — upcoming maintenance events that could affect availability, (3) Health Advisories — changes requiring action (retirements, breaking changes, security advisories). Service Health alerts can be configured in Azure Monitor to send notifications via action groups when relevant events occur.

---

### Q46
You want to retain Log Analytics workspace data for 2 years for compliance purposes. The default retention period is 30 days. What is the maximum retention period configurable for a Log Analytics workspace?

- A. 90 days
- B. 1 year (365 days)
- C. 2 years (730 days)
- D. 7 years

**Answer: C**

**Explanation:** Log Analytics workspace interactive data retention can be configured from 30 to 730 days (2 years). Data retained beyond 30 days incurs additional charges. For data retention beyond 2 years, you can use the Archive tier, which extends retention up to 12 years at a lower cost but data is not queryable in real-time (archived data must be restored to the hot tier for analysis). The 730-day maximum for interactive/hot retention is important to remember for the AZ-104 exam.

---

### Q47
You create an Azure Monitor Workbook to visualize performance data across multiple subscriptions. A manager wants a quick read-only view of key metrics on their dashboard. What is the difference between Azure Monitor Workbooks and Azure Dashboards?

- A. Workbooks are for Azure DevOps only; Dashboards are for Azure Portal
- B. Workbooks are interactive reports with parameters, tabs, and dynamic visualizations; Dashboards are static pinned tiles from multiple Azure services
- C. Dashboards require Azure Monitor; Workbooks work with any Azure service
- D. Workbooks are public; Dashboards are private

**Answer: B**

**Explanation:** Azure Monitor Workbooks provide rich, interactive reports and analytics. They support parameters (date ranges, resource filters), multiple tabs, conditional visualizations, text blocks with markdown, and dynamic queries. They are ideal for detailed analysis, investigations, and sharing complex reports. Azure Dashboards are collections of static tiles pinned from various Azure Portal views (metrics charts, resource tiles, Query results, etc.) — they provide a quick overview but have limited interactivity. Dashboards refresh automatically but don't support the advanced parameterization of Workbooks.

---

## 7) Advanced Topics

### Q48
You have an Azure Traffic Manager profile using the Priority routing method. You have three endpoints: West US (Priority 1), East US (Priority 2), and South Central US (Priority 3). The West US endpoint health probe starts failing. What happens?

- A. Traffic is distributed equally between East US and South Central US
- B. Traffic is routed to East US (Priority 2) only until West US recovers
- C. Traffic is distributed proportionally based on remaining endpoint weights
- D. Traffic Manager stops routing until West US recovers

**Answer: B**

**Explanation:** Traffic Manager Priority routing always sends all traffic to the healthy endpoint with the lowest priority number. When West US (Priority 1) becomes unhealthy, Traffic Manager automatically fails over to the next healthy priority — East US (Priority 2). 100% of traffic goes to East US. South Central US (Priority 3) is the tertiary backup and only receives traffic if both West US and East US are unhealthy. When West US recovers and passes health probes, Traffic Manager automatically routes traffic back to West US.

---

### Q49
You need a global HTTP/HTTPS load balancer that also provides WAF capabilities, CDN-based content acceleration, SSL termination at the edge, and intelligent routing with split traffic (A/B testing). Which Azure service provides all these capabilities in a single service?

- A. Azure Load Balancer Standard
- B. Azure Application Gateway v2
- C. Azure Front Door
- D. Azure Traffic Manager with Application Gateway

**Answer: C**

**Explanation:** Azure Front Door is Microsoft's global, scalable entry point that provides all the listed capabilities: (1) Global HTTP/HTTPS load balancing with anycast routing, (2) Integrated WAF (Web Application Firewall) at the edge, (3) CDN for static content caching and acceleration, (4) SSL/TLS termination at the edge (reducing latency), (5) Intelligent routing including weighted traffic splitting (A/B testing, canary deployments), latency-based routing, and session affinity. Front Door operates at the application layer (Layer 7) and uses Microsoft's global network for acceleration.

---

### Q50
You have an Azure Container Registry (ACR) with Premium SKU. Teams in multiple Azure regions need to pull container images with low latency. Which ACR feature should you enable?

- A. Content trust (image signing)
- B. ACR Tasks for automated builds
- C. Geo-replication
- D. Private endpoint for ACR

**Answer: C**

**Explanation:** Azure Container Registry geo-replication replicates the registry to multiple Azure regions, creating a local registry copy in each configured region. When teams or AKS clusters pull images, they pull from the nearest replica, reducing latency and egress costs. Geo-replication is only available with the Premium ACR SKU. All replica registries are synchronized automatically when images are pushed to the primary registry. Geo-replication is also resilient — if one region's registry is unavailable, pulls can fail over to another regional replica.

---

*Document generated from AZ-104 course transcript. Last updated: 2026-05-24*
