# AZ-104 Practice Questions (Set 8) — English

Source: `expected_questions/4.txt` — all question prompts, traps, and verified answer explanations reviewed.

---

## 1) Compute — Custom Script Extension

### Q1
You have an Azure virtual machine named VM1 that runs Windows Server. You plan to deploy the Custom Script Extension to VM1 using Azure PowerShell. You need to identify the required parameters to configure the extension. Which parameter identifies the extension source as Microsoft?

- A. `-ExtensionName`
- B. `-Location`
- C. `-Publisher`
- D. `-TypeHandlerVersion`

**Answer: C**
**Explanation:** The `-Publisher` parameter specifies who provides the VM extension. For Custom Script Extension, Windows uses `Microsoft.Compute` and Linux uses `Microsoft.Azure.Extensions`. The question tests whether you know which parameter identifies the extension provider, not how the script itself runs.

### Q2
You have an Azure virtual machine named VM1 that runs Windows Server. You use the Custom Script Extension to run a PowerShell script that configures IIS. You need to ensure the same script runs automatically every time the VM starts. What should you do?

- A. Re-enable the Custom Script Extension on each restart
- B. Configure Azure Automation with a startup runbook
- C. Use Azure Run Command on a schedule
- D. Create a scheduled task in Windows Task Scheduler triggered at system startup

**Answer: D**
**Explanation:** Custom Script Extension is designed for one-time deployment-time execution — it does not re-execute on VM restart. For recurring boot-time scripts on Windows, use Windows Task Scheduler with a startup trigger. Azure Automation is technically possible but over-engineered for what this exam question is testing.

### Q3
You have an Azure virtual machine named VM1 that runs Ubuntu 22.04. You need to run a Bash script once every time the VM boots. The script is stored on the VM locally. What is the correct OS-level mechanism to use?

- A. Custom Script Extension
- B. Windows Task Scheduler
- C. A systemd service unit with `WantedBy=multi-user.target`
- D. Azure Automation State Configuration

**Answer: C**
**Explanation:** On Linux VMs, a systemd service unit configured to run at boot (`WantedBy=multi-user.target`) is the correct mechanism for recurring startup scripts. Custom Script Extension runs once at provisioning time. Windows Task Scheduler is Windows-only. Azure Automation State Configuration enforces configuration state, not arbitrary script execution at boot.

### Q4
You have deployed a Custom Script Extension on a Windows Azure VM to configure IIS settings. A junior administrator asks whether the CSE script will re-run if the VM is deallocated and then restarted. What is the correct answer?

- A. Yes, the CSE re-runs on every VM start by default
- B. Yes, if the `forceUpdateTag` property is set
- C. No, CSE runs only once at the time it is first applied and does not re-run on VM restart
- D. No, unless Azure Automation is configured as a trigger

**Answer: C**
**Explanation:** Custom Script Extension runs exactly once when it is first deployed. It does not re-execute on VM restart, deallocation, or redeployment unless the extension is explicitly removed and re-added with a new `forceUpdateTag`. This is a critical behavioral difference from Windows Task Scheduler or systemd, which do execute at each boot.

### Q5
You have an Azure virtual machine that runs Windows Server. You need to automate the execution of a PowerShell script when the VM starts. The solution must work without additional Azure management services and must not require modification of the VM extension configuration. What should you configure?

- A. Azure Automation runbook linked to a Start VM alert
- B. Custom Script Extension with a startup trigger parameter
- C. Windows Task Scheduler with a trigger set to "At system startup"
- D. Azure VM Run Command scheduled via Azure Monitor action group

**Answer: C**
**Explanation:** Windows Task Scheduler is the native OS-level mechanism for recurring startup scripts on Windows VMs. It requires no additional Azure services and does not touch the VM extension configuration. Custom Script Extension does not support recurring execution. Azure Automation and Run Command require additional Azure service configuration.

### Q6
A developer is configuring a Custom Script Extension for a Linux VM and needs to identify the correct publisher name for the extension. What is the correct value for the `-Publisher` parameter for the Linux Custom Script Extension?

- A. `Microsoft.Compute`
- B. `Microsoft.OSTCExtensions`
- C. `Microsoft.Azure.Extensions`
- D. `Microsoft.Automation`

**Answer: C**
**Explanation:** For Linux VMs, the Custom Script Extension publisher is `Microsoft.Azure.Extensions`. For Windows VMs, it is `Microsoft.Compute`. `Microsoft.OSTCExtensions` was used for older Linux CSE versions and is now deprecated. `Microsoft.Automation` is not a VM extension publisher.

---

## 2) Identity, Governance, and RBAC

### Q7
You need to create a dynamic user group in Microsoft Entra ID that includes only guest users in your organization. Which membership rule should you use?

- A. `(user.companyName -eq "Contoso")`
- B. `(user.objectType -eq "Guest")`
- C. `(user.mail -contains "contoso")`
- D. `(user.userType -eq "Guest")`

**Answer: D**
**Explanation:** `user.userType` is the correct built-in attribute to distinguish guest users (`"Guest"`) from member users (`"Member"`) in Entra dynamic group rules. `companyName` may not be set for all guests and is unreliable. `mail`-based patterns are not exact. `user.objectType` is not a valid dynamic group rule attribute.

### Q8
You are creating a dynamic group membership rule in Microsoft Entra ID to include all internal (member) users only — excluding guests. Which rule is correct?

- A. `(user.userType -eq "Member")`
- B. `(user.userType -ne "Guest")`
- C. `(user.accountEnabled -eq true)`
- D. `(user.externalUserState -eq "PendingAcceptance")`

**Answer: B**
**Explanation:** `(user.userType -ne "Guest")` matches all users whose `userType` is not "Guest", effectively including only internal member users. While `(user.userType -eq "Member")` seems equivalent, `user.userType` can also be null for some accounts, so the `-ne "Guest"` form is more robust. `accountEnabled` and `externalUserState` are unrelated to the Member/Guest distinction.

### Q9
You have two custom RBAC roles assigned to a user at subscription scope. Role1 grants all blob data actions and excludes delete via `NotDataActions`. Role2 explicitly grants blob delete via `DataActions`. Which blob data actions can the user perform?

- A. All blob actions except delete, because `NotDataActions` overrides Role2
- B. Read and write only
- C. All blob actions including delete, because Azure RBAC is additive
- D. No blob actions, because the conflicting roles cancel each other

**Answer: C**
**Explanation:** Azure RBAC is an additive (union) permission model — permissions from all assigned roles are combined. `NotDataActions` only subtracts permissions within the same role definition; it is NOT a global deny and does not override other roles. Because Role2 explicitly grants blob delete as a DataAction, the effective union includes delete. This differs from AWS IAM, where explicit Deny always overrides Allow.

### Q10
A security engineer explains that `NotDataActions` in Azure RBAC "explicitly denies" a data action for the user. Is this correct?

- A. Yes, `NotDataActions` creates a global deny that no other role can override
- B. Yes, but only at the resource group scope and above
- C. No, `NotDataActions` only subtracts permissions within the same role definition and has no effect across other role assignments
- D. No, `NotDataActions` is applied only to management plane operations, not data plane

**Answer: C**
**Explanation:** `NotDataActions` is NOT a deny rule. It only removes specific data actions from the permissions granted by the same role definition. If another role assigned to the same user grants that same data action, the user retains access. Azure RBAC is fundamentally additive — there is no built-in deny override between roles (unlike Azure Policy, which does have a deny effect).

### Q11
You need to assign the minimum RBAC role that allows a user to view Azure Cost Management billing data and budgets but not modify any resources. Which role should you assign?

- A. Contributor
- B. Reader
- C. Cost Management Reader
- D. Billing Account Reader

**Answer: C**
**Explanation:** Cost Management Reader grants read-only access to cost management data, budgets, and spending reports without granting access to modify any resources. The general Reader role also provides read access but is broader. Contributor allows resource modifications and is over-privileged. Billing Account Reader is an EA/MCA billing scope role, not an Azure RBAC subscription-scope role.

### Q12
You create a dynamic group in Microsoft Entra ID using the rule `(user.department -eq "Finance")`. A user in the Finance department has an `accountEnabled` value of `false`. Will this user be included in the group?

- A. No, disabled accounts are excluded from dynamic groups automatically
- B. Yes, dynamic group membership is based on the attribute rule only, regardless of account state
- C. No, only enabled accounts are evaluated for dynamic group rules
- D. Yes, but the user will be listed as pending until the account is re-enabled

**Answer: B**
**Explanation:** Entra ID dynamic group membership is determined solely by whether the user's attributes match the membership rule. `accountEnabled` has no effect on group membership evaluation unless it is explicitly included in the rule. A disabled account that matches `(user.department -eq "Finance")` will still be a member of the group.

### Q13
You need to allow a third-party application service principal to list and read secrets from an Azure Key Vault, but not create or delete them. Which Azure built-in RBAC role should you assign at the Key Vault scope?

- A. Key Vault Contributor
- B. Key Vault Administrator
- C. Key Vault Secrets Officer
- D. Key Vault Secrets User

**Answer: D**
**Explanation:** Key Vault Secrets User grants `get` and `list` permissions on secrets — exactly what a read-only application needs. Key Vault Secrets Officer adds create and delete permissions (over-privileged). Key Vault Administrator grants full Key Vault management. Key Vault Contributor allows resource-level management (ARM plane) but not secret read access in RBAC mode.

---

## 3) Monitoring — VM Insights

### Q14
You have an Azure subscription. You plan to enable VM Insights on all virtual machines. Where does VM Insights store the monitoring data it collects?

- A. Azure Storage account
- B. Application Insights resource
- C. Azure Monitor workspace
- D. Log Analytics workspace

**Answer: D**
**Explanation:** VM Insights stores performance metrics, process data, and dependency data in a Log Analytics workspace. Application Insights is for application performance monitoring (APM), not VM guest-OS data. Azure Monitor workspace is used for Prometheus metrics, not VM Insights.

### Q15
You have an Azure subscription that contains 50 virtual machines. You plan to enable VM Insights on all virtual machines by using Azure Policy. Before you can assign the policy initiative, you need to create a prerequisite resource. Which resource must you create first?

- A. A Log Analytics workspace
- B. An Application Insights resource
- C. An Azure Monitor workspace
- D. A VM Insights data collection rule (DCR)

**Answer: D**
**Explanation:** When enabling VM Insights via Azure Policy, the required prerequisite is a VM Insights Data Collection Rule (DCR). The DCR defines what data to collect and how the data stream is configured. While a Log Analytics workspace is also needed, the specific policy-assignment prerequisite that must exist first is the DCR. Application Insights and Azure Monitor workspace (Prometheus) are not part of this flow.

### Q16
A monitoring engineer asks what a Data Collection Rule (DCR) defines when used with VM Insights. Which of the following best describes the purpose of a DCR?

- A. It defines the backup retention schedule for VM diagnostic logs
- B. It specifies which Log Analytics workspace to bill for VM data ingestion
- C. It defines what performance data to collect from VMs and how that data stream is configured
- D. It configures the alert rules that fire when VM metrics exceed thresholds

**Answer: C**
**Explanation:** A Data Collection Rule (DCR) defines what data should be collected from agents (such as performance counters and log streams) and how the data is processed and delivered. For VM Insights via Azure Policy, the DCR must exist before the policy initiative can be assigned because it tells the agents what to collect. It does not control alerting, billing, or backup.

### Q17
You want to monitor application-level performance data — such as request rates, exception counts, and dependency call durations — for a .NET application running on an Azure VM. Which Azure monitoring resource should you use?

- A. VM Insights with a Log Analytics workspace
- B. Application Insights
- C. Azure Monitor workspace (Prometheus)
- D. Azure Diagnostics extension with a Storage account

**Answer: B**
**Explanation:** Application Insights (Azure Monitor APM) is designed for application-level telemetry: request rates, response times, exceptions, and dependency tracking. VM Insights monitors the guest OS — CPU, memory, processes, and network connections — not application code. Azure Monitor workspace with Prometheus collects infrastructure metrics. Azure Diagnostics extension sends OS-level logs/metrics to Storage.

### Q18
You configure VM Insights for a virtual machine through the Azure portal using the default settings. Which data does VM Insights collect by default in this basic portal onboarding scenario?

- A. Application traces and exception logs
- B. Network Security Group flow logs
- C. Guest performance metrics only (CPU, memory, disk, network)
- D. Full dependency map data and process inventory

**Answer: C**
**Explanation:** When onboarding VM Insights through the portal with default settings, only guest performance metrics (CPU, memory, disk, network) are collected. The full dependency map (Service Map) and process inventory require the Dependency Agent in addition to the Azure Monitor Agent. These are optional components that must be explicitly enabled.

### Q19
Which Azure Monitor workspace type is used specifically for collecting Prometheus-style metrics from Azure Kubernetes Service (AKS) and other containerized workloads?

- A. Log Analytics workspace
- B. Application Insights resource
- C. Azure Monitor workspace
- D. Metrics namespace in Azure Monitor

**Answer: C**
**Explanation:** Azure Monitor workspace is the store specifically designed for Prometheus-compatible metrics, such as those scraped from AKS and containerized workloads. Log Analytics workspace stores logs and VM Insights data. Application Insights is for APM telemetry. The built-in Azure Monitor Metrics namespace stores platform metrics but does not support Prometheus format natively.

---

## 4) Storage and Files — Azure Files AD DS

### Q20
You have an Azure subscription that contains a storage account with an Azure file share. You have an on-premises Active Directory Domain Services (AD DS) environment synchronized to Microsoft Entra ID. You need to enable AD DS authentication for the Azure file share. Which type of object does the `Join-AzStorageAccount` cmdlet create in your AD DS by default?

- A. A user account
- B. A service logon account
- C. A managed service account
- D. A computer account

**Answer: D**
**Explanation:** By default, `Join-AzStorageAccount` creates a computer account in AD DS to represent the storage account as a server-style network resource supporting Kerberos/SMB authentication. A service logon account can be created instead by specifying `-DomainAccountType ServiceLogonAccount`. Group managed service accounts (gMSAs) are not supported in this scenario.

### Q21
You have run `Join-AzStorageAccount` to configure AD DS authentication for an Azure file share. Your security policy requires using a service logon account instead of the default object type. Which parameter do you add to the cmdlet?

- A. `-DomainAccountType ComputerAccount`
- B. `-DomainAccountType ServiceLogonAccount`
- C. `-DomainAccountType ManagedServiceAccount`
- D. `-UseServiceAccount $true`

**Answer: B**
**Explanation:** The `-DomainAccountType ServiceLogonAccount` parameter instructs `Join-AzStorageAccount` to create a service logon account instead of the default computer account. `ComputerAccount` is the default and does not need to be specified. `ManagedServiceAccount` and `-UseServiceAccount` are not valid parameter values for this cmdlet.

### Q22
You are configuring Azure Files with AD DS authentication. A colleague asks whether a group managed service account (gMSA) can be used as the AD DS object. What is the correct answer?

- A. Yes, gMSAs are the recommended account type for Azure Files AD DS authentication
- B. Yes, but only for premium tier file shares
- C. No, gMSAs are not supported; only computer accounts or service logon accounts can be used
- D. No, gMSAs require Azure Arc and are not supported for storage accounts

**Answer: C**
**Explanation:** `Join-AzStorageAccount` supports two AD DS object types: computer accounts (default) and service logon accounts (via `-DomainAccountType ServiceLogonAccount`). Group managed service accounts (gMSAs) are not supported for Azure Files AD DS authentication, regardless of the file share tier.

### Q23
You have successfully configured AD DS authentication for an Azure file share. Users report that they can browse file shares but cannot mount them from non-domain-joined machines. What is the most likely cause?

- A. The Recovery Services vault is not in the same region as the storage account
- B. The storage account firewall is blocking SMB port 445
- C. The AD DS sync to Entra ID has not completed
- D. The `Join-AzStorageAccount` cmdlet created the wrong object type

**Answer: B**
**Explanation:** Azure Files AD DS authentication uses SMB (port 445). Many corporate networks and ISPs block outbound port 445. Non-domain-joined machines that try to mount Azure File Shares using AD DS credentials will fail if port 445 is blocked. The AD DS sync, vault region, and account type are unrelated to this connectivity issue.

### Q24
You need to configure Azure Files with Active Directory Domain Services (AD DS) authentication. Before running `Join-AzStorageAccount`, what must be true about the machine where you run the PowerShell cmdlet?

- A. The machine must be an Azure VM with a managed identity
- B. The machine must be domain-joined to the same AD DS domain you want to use
- C. The machine must be running Windows Server 2022
- D. The machine must have the AzureAD PowerShell module installed

**Answer: B**
**Explanation:** `Join-AzStorageAccount` must be run from a machine that is domain-joined to the target AD DS domain. This is because the cmdlet performs an offline domain-join on behalf of the storage account and requires access to a domain controller. The machine does not need to be an Azure VM — an on-premises domain-joined machine works. The AzureAD module is not required; the Az.Storage module is needed.

---

## 5) Networking and DNS

### Q25
You have an Azure subscription that contains a public DNS zone named `contoso.com`. Three web servers have IP addresses 10.1.1.10, 10.1.1.11, and 10.1.1.12. You want all three IP addresses to be returned when clients query for `www.contoso.com`. What should you do?

- A. Create three separate A records, one per IP address
- B. Create a CNAME record pointing to a load-balanced alias
- C. Deploy an Azure Load Balancer and point a single A record to it
- D. Create a single A record set for `www` with all three IP addresses as values

**Answer: D**
**Explanation:** Azure DNS supports multiple IP values in a single A record set. When queried, all IPs are returned for basic client-side round-robin selection. This is DNS-level distribution only — there are no health checks, failover, or session affinity. For production-grade traffic management, use Azure Load Balancer or Traffic Manager. A CNAME cannot return multiple IPs directly.

### Q26
You have configured an Azure DNS A record set for `www.contoso.com` with three IP addresses for basic load distribution. A user asks whether this setup provides automatic failover if one of the web servers becomes unavailable. What is the correct answer?

- A. Yes, Azure DNS removes unhealthy IP addresses automatically
- B. Yes, but only if the DNS zone has Azure Traffic Manager integration enabled
- C. No, Azure DNS A records perform no health checks — all IPs are always returned
- D. No, but Azure DNS will remove IPs that are unreachable after a 30-second probe

**Answer: C**
**Explanation:** Azure DNS performs no health monitoring on A record values. All IPs in the record set are always returned regardless of server availability. Clients choose one IP (typically the first), which means unhealthy servers may still receive traffic. For health-aware routing and automatic failover, use Azure Traffic Manager (DNS-based) or Azure Load Balancer (network-level).

### Q27
You need to configure DNS-based geographic load balancing so that users in Europe are directed to European servers and users in North America are directed to US servers. Which Azure service should you use?

- A. Azure DNS A record with multiple IP values
- B. Azure Load Balancer
- C. Azure Application Gateway
- D. Azure Traffic Manager with Geographic routing profile

**Answer: D**
**Explanation:** Azure Traffic Manager with the Geographic routing method directs DNS queries based on the geographic location of the client's DNS resolver. Azure DNS A records with multiple IPs perform round-robin only with no geographic awareness. Azure Load Balancer is an L4 network load balancer with no geographic routing. Application Gateway is a regional L7 load balancer.

### Q28
You create a CNAME record in an Azure DNS zone for `www.contoso.com` pointing to `backend.contoso.com`. A colleague tries to create a second A record for `www.contoso.com` in the same zone. What happens?

- A. The second record is created and both coexist
- B. Azure DNS automatically converts the CNAME to an A record
- C. The A record creation fails because a CNAME cannot coexist with another record of the same name
- D. The A record overwrites the CNAME record

**Answer: C**
**Explanation:** DNS standards prohibit a CNAME record from coexisting with any other record type for the same name. In Azure DNS, attempting to add an A record for a name that already has a CNAME (or vice versa) returns an error. This is a standard DNS constraint, not specific to Azure. The only way to have both is to use different record names.

### Q29
You have an Azure public DNS zone. You need to delegate a subdomain `dev.contoso.com` to a separate DNS zone managed by a development team. What type of DNS record should you create in the parent zone?

- A. A record pointing to the new zone's IP
- B. CNAME record pointing to the new zone
- C. NS (Name Server) records for `dev.contoso.com` pointing to the delegated zone's name servers
- D. SOA record update in the parent zone

**Answer: C**
**Explanation:** DNS subdomain delegation is accomplished by creating NS (Name Server) records in the parent zone that point to the authoritative name servers of the child zone. This tells resolvers to query the delegated zone for any records under `dev.contoso.com`. A records, CNAME records, and SOA records do not perform delegation.

---

## 6) Backup, Recovery, and SQL

### Q30
You have a storage account named storage1 located in the East US region. Storage1 contains an Azure file share named fileshare1. You need to configure Azure Backup to protect fileshare1. You plan to create a Recovery Services vault. In which region should you create the Recovery Services vault?

- A. Any region
- B. West US (the paired region)
- C. A central hub region
- D. East US (the same region as the storage account)

**Answer: D**
**Explanation:** Azure Backup requires the Recovery Services vault to be in the same region as the protected resource. For Azure File Share backup, the vault must be co-located with the storage account. This is distinct from Azure Site Recovery, which uses cross-region replication for disaster recovery. The same vault family name (Recovery Services vault) is used by both services, which makes the region rules easy to confuse.

### Q31
You have an Azure subscription that contains a Recovery Services vault named Vault1. You create a Standard backup policy for Azure virtual machines with a daily backup schedule. You need to configure the instant restore snapshot retention period. What is the valid range for the retention period?

- A. 1 to 7 days
- B. 7 to 35 days
- C. 1 to 30 days
- D. 1 to 5 days

**Answer: D**
**Explanation:** Azure VM Instant Restore snapshot retention is configurable from 1 to 5 days. These snapshots are stored locally near the VM for faster restore operations before falling back to vault storage. Do not confuse this with vault-tier retention (years), SQL PITR (up to 35 days), or SQL LTR (up to 10 years).

### Q32
You have an Azure SQL Database in the Basic service tier using the DTU purchasing model. You need to configure the maximum supported point-in-time restore (PITR) backup retention period. What is the maximum number of days you can configure?

- A. 35 days
- B. 14 days
- C. 10 years
- D. 7 days

**Answer: D**
**Explanation:** Azure SQL Database Basic tier (DTU model) supports a maximum PITR retention of 7 days. Standard and Premium DTU tiers, and General Purpose and Business Critical vCore tiers, support up to 35 days. The 10-year option refers to Long-term retention (LTR), which is a separate feature. The "35 days" rule does not apply universally — the Basic tier exception is a frequent exam trap.

### Q33
You have an Azure SQL Database in the Standard service tier using the DTU purchasing model. What is the maximum PITR backup retention period you can configure?

- A. 7 days
- B. 14 days
- C. 35 days
- D. 90 days

**Answer: C**
**Explanation:** Azure SQL Database Standard, Premium (DTU), and General Purpose, Business Critical (vCore) tiers all support PITR retention up to 35 days. Only the Basic DTU tier is limited to 7 days. The 90-day and longer options are only available through Long-term retention (LTR), which is a separate, manually configured feature.

### Q34
You need to retain Azure SQL Database backups for 5 years to meet compliance requirements. Point-in-time restore (PITR) retention has already been set to its maximum value. What feature should you configure?

- A. Increase the PITR retention beyond 35 days using the Azure portal advanced settings
- B. Configure Long-term retention (LTR) backups with a yearly retention policy
- C. Create an Azure Backup policy targeting the SQL Database
- D. Enable geo-redundant backup storage and set retention to 5 years

**Answer: B**
**Explanation:** Long-term retention (LTR) allows weekly, monthly, or yearly backup copies of Azure SQL Database to be retained for up to 10 years. PITR cannot be extended beyond 35 days regardless of portal settings. Azure Backup (Recovery Services vault) integrates with Azure SQL Database but the LTR feature within the SQL Database service is the primary mechanism for multi-year compliance retention.

### Q35
A three-tier application runs on Azure VMs and is protected by Azure Site Recovery. You create a recovery plan. You need to ensure that the SQL Server VM starts first, followed by the App Server, and finally the Web Server. What should you configure in the recovery plan?

- A. Set a priority level for each virtual machine
- B. Configure dependencies between virtual machines
- C. Place the virtual machines in different Availability Sets
- D. Add the virtual machines to separate groups within the recovery plan

**Answer: D**
**Explanation:** Azure Site Recovery Recovery Plans use Groups to control VM startup order during failover. Lower-numbered groups start first; VMs within the same group start in parallel. For this scenario: Group 1 = SQL Server, Group 2 = App Server, Group 3 = Web Server. "Priority", "dependency", and "Availability Sets" are common distractors — none of these control startup order in ASR Recovery Plans.

### Q36
You have an Azure Site Recovery recovery plan for a three-tier application. All three VMs are added to the same Group 1. A failover is initiated. In what order do the VMs start?

- A. They start in the order they were added to the group
- B. They start alphabetically by VM name
- C. They start in parallel simultaneously
- D. They start based on their VM size (smaller VMs first)

**Answer: C**
**Explanation:** In Azure Site Recovery recovery plans, all VMs within the same group start in parallel simultaneously. To enforce sequential ordering, VMs must be placed in separate groups. Only the group numbering determines order — lower group numbers start first. Within a group, there is no ordering mechanism.

### Q37
You need to protect an Azure VM workload from accidental deletion and provide the ability to restore the VM to a state from 20 days ago. Which service should you use, and where should the vault be located?

- A. Azure Site Recovery vault in the paired region
- B. Recovery Services vault in the same region as the VM, with a backup policy retaining daily backups for at least 20 days
- C. Recovery Services vault in any region, with cross-region restore enabled
- D. Azure Backup with geo-redundant storage in a secondary region

**Answer: B**
**Explanation:** Azure Backup (using a Recovery Services vault) protects against accidental deletion and supports point-in-time VM restore. The vault must be in the same region as the VM. The backup policy retention must be set to at least 20 days to enable restoring to a state 20 days ago. Azure Site Recovery is for disaster recovery (DR) — it provides failover but is not designed for accidental deletion recovery.

---

## 7) AKS and Containers

### Q38
You create a Spot node pool in an Azure Kubernetes Service (AKS) cluster and set the eviction policy to Deallocate. After Azure evicts the Spot nodes, the cluster is unable to scale properly and encounters quota issues. What is the cause of this behavior?

- A. The `spot-max-price` setting conflicts with the Deallocate eviction policy
- B. The cluster autoscaler cannot detect evicted Spot nodes when using Deallocate policy
- C. The Deallocate eviction policy requires manual intervention to release resources
- D. Nodes in the stopped-deallocated state count against your compute quota

**Answer: D**
**Explanation:** When eviction policy is set to Deallocate, evicted Spot VMs transition to the `Stopped (deallocated)` state. Even though they are not running, they continue to count against the subscription's compute quota. This prevents the AKS autoscaler from creating new nodes. The Delete eviction policy is generally preferred for Spot node pools because it fully releases quota when nodes are evicted.

### Q39
You are configuring an AKS Spot node pool and want to ensure that evicted nodes do not consume compute quota so that the cluster autoscaler can create replacement nodes. Which eviction policy should you configure?

- A. Deallocate
- B. Stop
- C. Hibernate
- D. Delete

**Answer: D**
**Explanation:** The Delete eviction policy removes Spot VMs entirely from the scale set when they are evicted by Azure. This releases the quota associated with those VMs, allowing the AKS autoscaler to create new replacement Spot nodes. The Deallocate policy places nodes in the stopped-deallocated state, which retains quota and blocks autoscaling.

### Q40
You have an AKS cluster with a Spot node pool using the Delete eviction policy. Azure evicts a Spot node. What happens to the evicted VM?

- A. The VM is moved to the Stopped (deallocated) state and quota is retained
- B. The VM is deleted from the underlying scale set and the quota is released
- C. The VM is suspended and restart is attempted after 5 minutes
- D. The VM is placed in a quarantine state pending administrator review

**Answer: B**
**Explanation:** With the Delete eviction policy, when Azure reclaims a Spot VM, it is fully deleted from the underlying Virtual Machine Scale Set (VMSS). The compute quota associated with that VM is released immediately, allowing the AKS cluster autoscaler to provision new nodes. This is the recommended policy for AKS Spot node pools that use the cluster autoscaler.

### Q41
You are designing an AKS workload for a Spot node pool. Which workload type is most appropriate for Spot nodes?

- A. A stateful database application with persistent storage requirements
- B. An API gateway that handles authentication for all other services
- C. A batch image processing job that can be interrupted and restarted
- D. A service mesh control plane that must maintain continuous connections

**Answer: C**
**Explanation:** Spot VMs can be evicted at any time by Azure when capacity is needed. Spot node pools are best suited for stateless, interruptible workloads such as batch jobs, CI/CD pipelines, and parallel data processing. Stateful databases, API gateways, and service mesh control planes require continuous availability and are not appropriate for Spot nodes.

### Q42
You have an Azure Container Registry named ACR1. You need to grant a CI/CD pipeline service principal the ability to push images to and pull images from ACR1 while following the principle of least privilege. The registry uses the default RBAC Registry Permissions mode. Which Azure built-in role should you assign to the service principal?

- A. AcrPull
- B. Reader
- C. Contributor
- D. AcrPush

**Answer: D**
**Explanation:** AcrPush includes both pull (`Microsoft.ContainerRegistry/registries/pull/read`) and push (`Microsoft.ContainerRegistry/registries/push/write`) permissions for container images, making it the least-privilege role for a CI/CD pipeline. AcrPull is insufficient because it only allows image pulls. Contributor is over-privileged because it grants registry management permissions beyond what a pipeline needs.

### Q43
You need to assign a role to a deployment pipeline so it can only pull container images from an Azure Container Registry — it must not be able to push or manage the registry. Which role should you assign?

- A. AcrPush
- B. AcrPull
- C. Reader
- D. AcrDelete

**Answer: B**
**Explanation:** AcrPull grants only image pull (read) permissions and is the correct least-privilege role for a deployment pipeline that needs to retrieve images but never push. AcrPush includes both push and pull and is over-privileged for read-only scenarios. Reader grants read access to the registry resource in Azure but does not grant data-plane permissions to pull images. AcrDelete is not a standard built-in ACR role.

### Q44
A security review identifies that a service principal assigned to an Azure Container Registry has the Contributor role. The principal is used only for pulling container images in a production deployment pipeline. What is the recommended remediation?

- A. Replace Contributor with Owner to add more explicit control
- B. Replace Contributor with AcrPull to follow the principle of least privilege
- C. Replace Contributor with AcrPush to allow both push and pull
- D. Keep Contributor but add a conditional access policy to restrict push operations

**Answer: B**
**Explanation:** Contributor grants full resource management permissions on the registry (create, update, delete registry settings), which far exceeds what a read-only pull pipeline needs. The principle of least privilege requires using AcrPull, which grants only image pull permissions. AcrPush is also over-privileged for this use case. Conditional access policies do not control RBAC data-plane permissions for ACR.

---

## 8) Infrastructure as Code — Bicep

### Q45
You have a Bicep file deployed at subscription scope. The file includes a module that deploys a storage account. You need to deploy the storage account module to a specific resource group named StorageRG. Which scope should you set on the module declaration?

- A. `scope: subscription()`
- B. `scope: tenant()`
- C. `scope: managementGroup()`
- D. `scope: resourceGroup('StorageRG')`

**Answer: D**
**Explanation:** In a subscription-scoped Bicep file, modules inherit the subscription scope by default. To deploy a module to a specific resource group, you must override the scope using `resourceGroup('StorageRG')`. The `resourceGroup()` function returns a scope object targeting the specified resource group within the current subscription.

### Q46
In a Bicep module declaration, what does the `scope: resourceGroup('StorageRG')` expression do?

- A. It creates a new resource group named StorageRG if it does not exist
- B. It restricts the module to read-only access within StorageRG
- C. It returns a scope object that targets the specified resource group for module deployment
- D. It assigns the module's output to the StorageRG resource group context

**Answer: C**
**Explanation:** The `resourceGroup('name')` function in Bicep returns a scope object that references the specified resource group. When used in a module's `scope` property, it directs all resources deployed by that module into the specified resource group, regardless of the parent Bicep file's scope. It does not create the resource group or change access permissions.

### Q47
You have a subscription-scoped Bicep file. A module within the file does not have a `scope` property defined. Where will the module's resources be deployed?

- A. To the tenant root management group
- B. To the subscription scope (same as the parent Bicep file)
- C. To the default resource group configured in Azure CLI context
- D. The deployment will fail because modules require an explicit scope

**Answer: B**
**Explanation:** When a module does not have an explicit `scope` property, it inherits the scope of the parent Bicep file. If the parent file targets a subscription, the module also targets the subscription. This means resources that require a resource group scope (like storage accounts and VMs) cannot be deployed directly by such a module without an explicit `scope: resourceGroup(...)` override.

### Q48
You are writing a Bicep file that is deployed at the subscription level. You want to deploy a resource group and a storage account into it in the same Bicep deployment. What is the correct approach?

- A. Deploy both the resource group and storage account in the main Bicep file with no scope changes
- B. Deploy the resource group at subscription scope in the main file, then use a module with `scope: resourceGroup('rgName')` for the storage account
- C. Deploy both resources in a single module with `scope: subscription()`
- D. Use two separate deployments — resource groups and storage accounts cannot be in the same Bicep file

**Answer: B**
**Explanation:** In a subscription-scoped Bicep file, a resource group resource can be deployed directly (it is a subscription-level resource). A storage account is a resource group-level resource and cannot be deployed at the subscription scope. The correct pattern is to deploy the resource group at subscription scope, then use a Bicep module with `scope: resourceGroup('rgName')` to deploy the storage account into that resource group.

### Q49
You need to deploy a Management Group-level Azure Policy definition using Bicep. What is the correct `targetScope` to declare at the top of the Bicep file?

- A. `targetScope = 'subscription'`
- B. `targetScope = 'resourceGroup'`
- C. `targetScope = 'tenant'`
- D. `targetScope = 'managementGroup'`

**Answer: D**
**Explanation:** To deploy resources at the Management Group scope (such as Management Group-level Policy definitions or role assignments), the Bicep file must declare `targetScope = 'managementGroup'`. Subscription scope targets a single subscription. Resource group scope is the default and targets a resource group. Tenant scope is for root-level deployments such as creating management groups.

### Q50
You have a Bicep module file named `storage.bicep` that deploys a storage account. The main Bicep file targets the subscription scope. You reference the module with `scope: resourceGroup('RG1')`. The storage account name is passed as a parameter. After deployment, how do you reference the storage account's primary endpoint output from the module in the parent Bicep file?

- A. `storageModule.outputs.primaryEndpoint`
- B. `reference(storageModule).primaryEndpoint`
- C. `listKeys(storageModule, '2021-01-01').primaryEndpoint`
- D. `storageModule.primaryEndpoint`

**Answer: A**
**Explanation:** In Bicep, a module's outputs are accessed via `<moduleName>.outputs.<outputName>`. If the module `storageModule` defines an output named `primaryEndpoint`, the parent file accesses it as `storageModule.outputs.primaryEndpoint`. The `reference()` function is used in ARM templates, not in Bicep. `listKeys()` retrieves access keys, not endpoints. Direct property access (`storageModule.primaryEndpoint`) is not valid syntax.
