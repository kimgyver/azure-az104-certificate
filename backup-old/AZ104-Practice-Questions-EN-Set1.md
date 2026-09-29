# AZ-104 Microsoft Azure Administrator — Practice Questions (English)

> **50 Practice Questions with Detailed Explanations**
> Topics: Entra ID, RBAC, Governance, Storage, VMs, Networking, VPN, DNS, Load Balancer, Backup, ASR, Monitor

---

## Section 1: Manage Azure Identities and Governance

---

### Question 1

Your company has several departments. Each department has a number of virtual machines (VMs). All VMs are located in the same resource group named RG1. You want to associate each VM with its respective department for billing and management purposes.

**What should you do?**

- A. Create Azure Management Groups for each department
- B. Create a separate resource group for each department
- C. Assign tags to the virtual machines
- D. Modify the settings of the virtual machines

**✅ Correct Answer: C**

**Explanation:**
Tags are name-value pairs you attach to resources for organization, billing, and management. They are the correct tool for associating resources with specific departments without restructuring resource groups. Management Groups are for organizing subscriptions, not individual resources. Moving VMs to separate resource groups would require effort and doesn't scale well for simple categorization.

---

### Question 2

You want to implement a Conditional Access policy that requires members of the Global Administrators group to use Multi-Factor Authentication and an Azure AD-joined device when they connect from untrusted locations.

**Solution: You access the multi-factor authentication page to alter the user settings. Does this solution meet the goal?**

- A. Yes
- B. No

**✅ Correct Answer: B**

**Explanation:**
The per-user MFA settings page does not support conditional access logic such as location-based conditions or device compliance requirements. To require MFA only from untrusted locations and also enforce an Azure AD-joined device, you must use a **Conditional Access Policy** with the grant controls "Require MFA" and "Require Azure AD joined device."

---

### Question 3

You want to implement a Conditional Access policy that requires Global Admins to use MFA and an Azure AD-joined device from untrusted locations.

**Solution: You access the Azure portal to alter the Grant Control of the Azure AD Conditional Access policy. Does this solution meet the goal?**

- A. Yes
- B. No

**✅ Correct Answer: A**

**Explanation:**
The **Grant Control** in a Conditional Access policy is where you specify what must be satisfied before access is granted. You can require "Require multi-factor authentication" and "Require device to be marked as compliant" or "Require Hybrid Azure AD joined device" here. This is the correct location to configure both MFA and device requirements simultaneously.

---

### Question 4

Your company has an Azure AD tenant configured for hybrid coexistence with on-premises Active Directory. A DirSync server (Azure AD Connect) is running. You create a new user in the on-premises AD and need to replicate the user to Azure AD immediately.

**Solution: You run `Start-ADSyncSyncCycle -PolicyType Delta`. Does this meet the goal?**

- A. Yes
- B. No

**✅ Correct Answer: A**

**Explanation:**
`Start-ADSyncSyncCycle -PolicyType Delta` triggers an immediate delta synchronization cycle in Azure AD Connect, which picks up recent changes (including new users) from the on-premises AD and pushes them to Azure AD. A Delta sync is faster than an Initial sync because it only processes changes since the last sync, not all objects. Using `-PolicyType Initial` would also work but is slower and unnecessary here.

---

### Question 5

You have an Azure subscription with a resource group that contains multiple resources. You want to prevent anyone from accidentally deleting any resources in the resource group, but you still want users to be able to modify resource configurations.

**What type of resource lock should you apply?**

- A. ReadOnly lock on each resource
- B. Delete lock on the resource group
- C. ReadOnly lock on the resource group
- D. Delete lock on each resource individually

**✅ Correct Answer: B**

**Explanation:**
A **Delete lock** on the resource group prevents deletion of the resource group and all resources within it (locks inherit from parent to child), while still allowing modifications (updates to configurations). A ReadOnly lock would prevent both deletion AND modification, which is too restrictive. Applying locks to each resource individually is unnecessary since a lock on the parent resource group is inherited.

---

### Question 6

You need to ensure that all resources in your Azure subscription are tagged with a "CostCenter" tag. Some resources don't have this tag.

**What should you do?**

- A. Apply a resource lock to each resource
- B. Create an Azure Policy with the "Require a tag on resources" definition and assign it to the subscription
- C. Add the tag manually to each non-compliant resource
- D. Create a Management Group and apply the tag there

**✅ Correct Answer: B**

**Explanation:**
An **Azure Policy** with the "Require a tag on resources" built-in definition, when assigned at the subscription scope, will flag or deny (depending on the effect: Audit vs. Deny) any resource that doesn't have the required tag. This provides automated governance at scale. Management Groups don't apply tags directly. Manual tagging doesn't enforce future compliance. Resource locks are unrelated to tags.

---

### Question 7

Your organization has multiple Azure subscriptions. You need to manage access and policy at scale across all subscriptions.

**What Azure feature should you use?**

- A. Resource Groups
- B. Azure Policy
- C. Management Groups
- D. Azure Blueprints

**✅ Correct Answer: C**

**Explanation:**
**Management Groups** are containers for organizing multiple subscriptions. Policies, RBAC role assignments, and initiatives applied at the management group level are inherited by all subscriptions within it. Management Groups can be nested up to 6 levels deep (excluding the root). This is the correct tool for cross-subscription governance.

---

### Question 8

You are assigned the **Contributor** role at the subscription level. A colleague needs to assign you the **Owner** role on a specific resource group.

**Can your colleague assign you the Owner role on that resource group if they only have the Contributor role?**

- A. Yes, because Contributor has full access to resources
- B. No, because Contributor cannot assign roles — they need User Access Administrator or Owner
- C. Yes, because role assignments inherit from the subscription
- D. No, because the Owner role cannot be assigned at the resource group level

**✅ Correct Answer: B**

**Explanation:**
The **Contributor** role grants full read/write access to Azure resources but explicitly **cannot** manage access (role assignments). To assign roles, you need the **Owner** role or the **User Access Administrator** role. This is a key distinction in Azure RBAC.

---

### Question 9

A user in your organization needs to be able to manage virtual machines, including starting, stopping, and resizing them. They should NOT have access to the virtual network or storage accounts.

**Which built-in role should you assign?**

- A. Contributor
- B. Owner
- C. Virtual Machine Contributor
- D. Reader

**✅ Correct Answer: C**

**Explanation:**
The **Virtual Machine Contributor** built-in role grants permissions to manage VMs (create, start, stop, resize, delete) but does NOT grant access to the virtual networks or storage accounts that the VMs may use. The Contributor role would give too broad access (to all resources in scope). The Owner role grants even more access including role assignment.

---

### Question 10

You are configuring Self-Service Password Reset (SSPR) for your organization. Users are complaining that they reset their Azure AD password but their on-premises Active Directory password remains unchanged, causing issues.

**What is required to fix this?**

- A. Configure Azure AD Connect delta sync
- B. Enable Password Writeback in SSPR, which requires Azure AD Premium P1 or P2
- C. Enable Password Writeback, which requires only Azure AD Free
- D. Install an additional Azure AD Connect agent on every domain controller

**✅ Correct Answer: B**

**Explanation:**
**Password Writeback** is the feature that synchronizes password changes made in Azure AD back to the on-premises Active Directory. It requires:
1. Azure AD Connect to be configured to enable the writeback feature
2. **Azure AD Premium P1 or P2** license (it is not available in the Free tier)

Without Password Writeback, SSPR only changes the cloud password — the on-premises password remains unchanged, causing authentication failures when users log in on-premises.

---

## Section 2: Implement and Manage Storage

---

### Question 11

You need to store large amounts of infrequently accessed data (accessed maybe once a month) in Azure Blob Storage. Cost optimization is a priority.

**Which access tier should you use?**

- A. Hot
- B. Cool
- C. Archive
- D. Premium

**✅ Correct Answer: B**

**Explanation:**
The **Cool** tier is designed for infrequently accessed data (not accessed for at least 30 days). It has lower storage costs than Hot but higher access costs. The Archive tier is for data that's rarely accessed (months to years) and has a minimum retention of 180 days — it also requires rehydration (hours) before the data can be read. If accessed "once a month," Cool is more appropriate than Archive due to the access frequency and instant availability.

---

### Question 12

A developer needs to provide a third-party vendor temporary read access to a specific blob container for the next 48 hours. The access should be revocable immediately if needed without changing the storage account keys.

**What is the best solution?**

- A. Share the storage account access key with the vendor
- B. Create a Service SAS token with a 48-hour expiry linked to a Stored Access Policy
- C. Create a User Delegation SAS token with a 48-hour expiry directly (no stored access policy)
- D. Grant the vendor the Storage Blob Data Reader role on the container

**✅ Correct Answer: B**

**Explanation:**
The key requirement is **immediate revocation without rotating account keys**. The best fit is a **Service SAS linked to a Stored Access Policy**:
- You can revoke or change access by editing/deleting the stored access policy
- You can centrally control start time, expiry time, and permissions
- Option C (ad-hoc User Delegation SAS) is secure, but it is not policy-linked for centralized revocation in this pattern
- Option D (RBAC) can work in B2B scenarios, but this question describes temporary token-style delegated URL access

---

### Question 13

Your organization uses Azure Blob Storage for backups. You need data to be replicated to a secondary region AND you also want to be able to read from the secondary region during an outage.

**Which replication option should you choose?**

- A. LRS (Locally Redundant Storage)
- B. ZRS (Zone Redundant Storage)
- C. GRS (Geo-Redundant Storage)
- D. RA-GRS (Read-Access Geo-Redundant Storage)

**✅ Correct Answer: D**

**Explanation:**
- **LRS**: 3 copies in one datacenter. No geo-replication.
- **ZRS**: 3 copies across 3 zones in one region. No geo-replication.
- **GRS**: Replicates to a secondary region but the secondary is NOT readable during normal operation.
- **RA-GRS**: Same as GRS but also provides a **readable secondary endpoint** — you can read from the secondary region at any time (great for disaster recovery read scenarios).

---

### Question 14

You need to transfer 75 TB of on-premises data to Azure Storage. Your internet connection is limited to 100 Mbps and uploading would take months.

**What should you use?**

- A. AzCopy
- B. Azure Import/Export Service (ship your own HDDs)
- C. Azure Data Box
- D. Azure File Sync

**✅ Correct Answer: C**

**Explanation:**
**Azure Data Box** is a physical device with 80 TB usable capacity that Microsoft ships to you. You copy data to it offline, ship it back, and Microsoft uploads it to your Azure Storage. For 75 TB, this is far faster than a 100 Mbps internet link and is the recommended solution. Azure Import/Export requires you to ship your own HDDs (up to 8 TB per disk, requiring multiple drives). AzCopy and Azure File Sync are internet-based solutions not suited for this volume.

---

### Question 15

You have a storage account with the following access configuration:
- Shared access key authentication is enabled
- Public access is allowed on a container

An auditor asks you: "What is the most secure type of SAS token?"

**What is your answer?**

- A. Account SAS — because it has the broadest permissions
- B. Service SAS — because it is limited to one service
- C. User Delegation SAS — because it uses Azure AD credentials and doesn't require the account key
- D. All SAS types are equally secure

**✅ Correct Answer: C**

**Explanation:**
**User Delegation SAS** is the most secure SAS type because it is signed with **Azure AD user or managed identity credentials** rather than the storage account key. The storage account key is a long-lived secret that grants full access — if it leaks, the entire account is compromised. A User Delegation SAS is time-limited and can be individually revoked by revoking the Azure AD credentials. This is the recommended SAS type per Microsoft security guidance.

---

## Section 3: Deploy and Manage Azure Compute Resources

---

### Question 16

You want to deploy an Azure Virtual Machine and ensure that if there is a planned maintenance event (such as a host OS update), your VM remains available.

**What should you use?**

- A. Deploy the VM in an Availability Zone
- B. Deploy the VM in an Availability Set
- C. Enable Azure Backup
- D. Configure auto-shutdown

**✅ Correct Answer: C**

**Explanation:**
**Availability Sets** use Update Domains (UDs) to ensure that Azure only reboots VMs in one update domain at a time during planned maintenance. With 2+ VMs in an availability set, at least one VM will always be available during planned maintenance (SLA: 99.95%). Availability Zones protect against datacenter failure (unplanned outages). Azure Backup is for data recovery, not availability. Auto-shutdown is the opposite of what you want.

---

### Question 17

Your company deploys 3 VMs for a web application. The VMs need to be protected against both planned maintenance AND unplanned hardware failures at the datacenter level (power outage in one building).

**What should you use?**

- A. Deploy all 3 VMs in the same Availability Set
- B. Deploy each VM in a separate Availability Zone
- C. Enable Azure Site Recovery on all 3 VMs
- D. Deploy the VMs with Premium SSD disks

**✅ Correct Answer: B**

**Explanation:**
**Availability Zones** are physically separate buildings within an Azure region with independent power, network, and cooling. Deploying VMs across zones protects against datacenter-level failures. With 2+ VMs across zones, the SLA is **99.99%**. Availability Sets protect against rack-level failures within a single datacenter (SLA: 99.95%) but NOT against datacenter-level failures. Azure Site Recovery is a DR tool, not a high-availability solution for production workloads.

---

### Question 18

You need to add a second NIC to an existing Azure virtual machine that is currently running.

**What must you do first?**

- A. Nothing — you can add a NIC to a running VM
- B. Stop and deallocate the VM
- C. Restart the VM after adding the NIC
- D. Remove all existing NICs first

**✅ Correct Answer: B**

**Explanation:**
To add or remove NICs from an Azure VM, the VM must be in a **stopped (deallocated)** state. It is not sufficient to simply restart the VM. "Stopped" from within the OS (without deallocating) still incurs charges and does NOT allow NIC modification. Only the "Stop (Deallocate)" action in the Azure portal/CLI puts the VM in the required state.

---

### Question 19

You need to create multiple identical VMs with a consistent configuration. You want to define the infrastructure as code so that the deployment is repeatable and version-controlled.

**What Azure tool should you use?**

- A. Azure Portal deployment wizard
- B. Azure Resource Manager (ARM) templates
- C. VM Scale Sets
- D. Azure Blueprints

**✅ Correct Answer: B**

**Explanation:**
**ARM Templates** (JSON files) or Bicep files define infrastructure as code. They are repeatable, version-controllable, and can be stored in source control. VM Scale Sets are for autoscaling identical VMs but don't address the IaC/repeatability aspect for arbitrary VM deployments. Azure Blueprints are for governance/compliance (not VM configuration). The portal wizard is manual and not repeatable.

---

### Question 20

You deployed a Windows Server VM and want to install IIS Web Server on it automatically without RDP-ing into the machine.

**Which feature allows you to run a PowerShell script on a VM post-deployment?**

- A. Azure Automation Runbooks
- B. Custom Script Extension
- C. Azure Policy Guest Configuration
- D. Azure VM Run Command only

**✅ Correct Answer: B**

**Explanation:**
The **Custom Script Extension** allows you to download and execute scripts (PowerShell, Bash, etc.) on Azure VMs. It can be configured at VM deployment time via the portal or ARM templates. For example, to install IIS: `Install-WindowsFeature -name Web-Server -IncludeManagementTools`. The Azure VM Run Command also executes scripts but is more suited for one-off ad-hoc commands. Custom Script Extension is the standard method for post-deployment configuration.

---

### Question 21

You have a VM Scale Set with autoscaling configured. The scale-out rule is: "Add 1 VM when average CPU > 75% for 10 minutes." After a scale-out event, which scale-in policy will Azure use by default to determine which VM to remove?

- A. Remove the VM with the highest instance ID
- B. Remove the oldest VM
- C. Remove the newest VM
- D. Remove the VM with the lowest CPU usage

**✅ Correct Answer: A**

**Explanation:**
The **default scale-in policy** for VM Scale Sets is to remove the instance with the **highest instance ID**. Azure also offers "Oldest VM" and "Newest VM" policies. You cannot configure "lowest CPU" as a built-in scale-in policy. Understanding the default behavior is important for exam questions about scale set operations.

---

### Question 22

You need to encrypt the OS disk of an Azure VM using a customer-managed key. You have already created an Azure Key Vault.

**What additional resource is required?**

- A. A Storage Account for key storage
- B. A Disk Encryption Set (DES)
- C. An Azure AD App Registration
- D. An Azure Policy assignment

**✅ Correct Answer: B**

**Explanation:**
Customer-managed key encryption for Azure managed disks requires a **Disk Encryption Set (DES)**, which acts as the intermediary between the Key Vault (where the key is stored) and the managed disk. The Key Vault and Disk Encryption Set must be in the **same region and same subscription** as the VM. The DES has a managed identity that is granted access to the Key Vault key.

---

## Section 4: Implement and Manage Virtual Networking

---

### Question 23

You need to allow HTTPS traffic (port 443) inbound to a VM from the internet while blocking all other inbound internet traffic.

**What should you configure?**

- A. Create a route table with a default route to the internet
- B. Add a Network Security Group (NSG) inbound rule allowing TCP port 443 from Any source with a lower priority number than the default "Deny All" rule
- C. Enable Azure DDoS Standard
- D. Configure the VM's Windows Firewall only

**✅ Correct Answer: B**

**Explanation:**
NSG rules work by priority — **lower priority numbers are evaluated first**. The default "DenyAllInBound" rule has priority 65500. To allow HTTPS, you add a custom inbound rule with:
- Priority: a number lower than 65500 (e.g., 100)
- Source: Any (or specific IP)
- Destination port: 443
- Protocol: TCP
- Action: Allow

This custom rule is evaluated before the deny-all rule, so HTTPS traffic is permitted while all other internet traffic hits the deny rule. NSGs are not sufficient alone if the VM's host firewall also blocks traffic, but NSGs are the Azure-native first layer.

---

### Question 24

You have two virtual networks: VNet-A (10.0.0.0/16) in East US and VNet-B (10.1.0.0/16) in West US. You configured vNet Peering from VNet-A to VNet-B. Users in VNet-A still cannot reach VMs in VNet-B.

**What is the most likely cause?**

- A. The address spaces overlap
- B. vNet Peering across regions is not supported
- C. The peering was only configured from VNet-A to VNet-B but not from VNet-B to VNet-A
- D. A VPN Gateway is required for cross-region peering

**✅ Correct Answer: C**

**Explanation:**
**vNet Peering must be configured bidirectionally** — you must create a peering connection on BOTH sides. Creating peering only from VNet-A → VNet-B establishes a one-way incomplete connection. You must also create the peering from VNet-B → VNet-A. Global (cross-region) vNet peering IS supported. The address spaces (10.0.x.x and 10.1.x.x) do not overlap, so that's not the issue. No VPN gateway is needed for vNet peering.

---

### Question 25

You are configuring a Site-to-Site VPN between your on-premises network and Azure. You have created the Virtual Network Gateway. What resource represents the on-premises VPN device in Azure?

- A. Virtual Network Gateway
- B. Local Network Gateway
- C. VPN Connection
- D. Gateway Subnet

**✅ Correct Answer: B**

**Explanation:**
The **Local Network Gateway** is the Azure resource that represents the remote end (on-premises VPN device). It stores:
- The public IP address of the on-premises VPN appliance
- The address space(s) of the on-premises network

The Virtual Network Gateway is the Azure-side VPN endpoint. The VPN Connection links the two gateways with a shared key. The Gateway Subnet is where the gateway VMs reside.

---

### Question 26

You want to create a Site-to-Site VPN that supports multiple connections (multiple on-premises sites connecting to the same Azure VNet) and also allows Point-to-Site (P2S) connections for remote users.

**What type of Virtual Network Gateway should you deploy?**

- A. Policy-based
- B. Route-based
- C. ExpressRoute Gateway
- D. Basic SKU gateway

**✅ Correct Answer: B**

**Explanation:**
**Route-based VPN gateways** support:
- Multiple site-to-site connections
- Point-to-site connections
- vNet-to-vNet connections

**Policy-based VPN gateways** only support a single site-to-site connection and do not support P2S. ExpressRoute gateways are for private circuit connections, not internet-based VPNs.

---

### Question 27

You are setting up the gateway subnet for a VPN gateway in Azure. Which name must the subnet have?

- A. VPNGatewaySubnet
- B. GatewaySubnet
- C. AzureGatewaySubnet
- D. Any name you choose

**✅ Correct Answer: B**

**Explanation:**
The gateway subnet **must** be named exactly **"GatewaySubnet"** — this is a hard requirement in Azure. The name cannot be changed or customized. Azure reserves this name for the VPN gateway's two hidden virtual machines. The recommended size is /27 or /28.

---

### Question 28

Your Azure VM cannot reach the internet. You check the NSG on the subnet and see only the default rules. What should you check next to determine why internet traffic is being blocked?

- A. Check if the VM has a public IP assigned
- B. Check the NSG applied at the NIC level
- C. Check the routing table for a route to the internet
- D. All of the above

**✅ Correct Answer: D**

**Explanation:**
Internet connectivity for an Azure VM depends on multiple factors:
1. **Public IP**: The VM or its load balancer needs a public IP for direct internet access (or NAT gateway)
2. **NIC-level NSG**: NSGs can be applied at both the NIC and subnet levels; both are evaluated
3. **Route table**: A User Defined Route (UDR) might redirect internet traffic through a firewall or Network Virtual Appliance, which may be blocking it

The effective security rules and effective routes tools in Network Watcher help diagnose these scenarios.

---

### Question 29

You need to allow Azure VMs in a subnet to access an Azure Storage account over the private Microsoft network rather than over the public internet, without assigning a private IP to the storage account.

**What feature should you use?**

- A. vNet Peering
- B. Private Endpoint
- C. Service Endpoint
- D. Azure Firewall

**✅ Correct Answer: C**

**Explanation:**
A **Service Endpoint** extends the vNet's private address space to Azure services (like Storage, SQL) so traffic flows over the Microsoft backbone instead of the public internet. The storage account still has a public IP but you can configure it to only accept connections from the specific vNet/subnet. A **Private Endpoint** assigns a private IP to the storage account (stronger isolation) but is more complex to set up. The question specifies "without assigning a private IP" — that points to Service Endpoint.

---

### Question 30

You need to test whether NSG rules are allowing or blocking a specific traffic flow to an Azure VM.

**Which Azure Network Watcher tool should you use?**

- A. Connection Monitor
- B. Packet Capture
- C. IP Flow Verify
- D. Next Hop

**✅ Correct Answer: C**

**Explanation:**
**IP Flow Verify** lets you specify a direction (inbound/outbound), protocol, source IP, source port, destination IP, and destination port, and it tells you whether the traffic would be allowed or denied by NSG rules — and which specific rule is responsible. Connection Monitor tests actual connectivity over time. Packet Capture captures actual packets. Next Hop identifies routing decisions, not NSG rules.

---

## Section 5: Load Balancers and High Availability

---

### Question 31

You are configuring a Standard Azure Load Balancer. You have two VMs in the backend pool. One VM has a Basic SKU Public IP and the other has no public IP. Will this configuration work?

- A. Yes, both configurations are supported with Standard Load Balancer
- B. No — the VM with a Basic SKU Public IP is incompatible with Standard Load Balancer
- C. No — both VMs must have Standard Public IPs
- D. Yes, but the VM with Basic Public IP must be in a different backend pool

**✅ Correct Answer: B**

**Explanation:**
**Standard Load Balancer requires all backend pool VMs to either have a Standard Public IP or NO public IP.** A VM with a Basic SKU Public IP is incompatible with Standard Load Balancer. This is a common gotcha on the exam. Basic Load Balancer is compatible with Basic Public IPs, but Standard LB is not backward compatible.

---

### Question 32

You configure an Azure Load Balancer health probe on TCP port 80. After a few minutes, you notice one VM is being marked as unhealthy and is removed from the load balancer rotation, even though the application is running.

**What is the most likely cause?**

- A. The VM's NSG is blocking TCP port 80 from the Azure Load Balancer IP (168.63.129.16)
- B. The VM is in a different availability zone than the load balancer
- C. The load balancer SKU is wrong
- D. The health probe interval is too long

**✅ Correct Answer: A**

**Explanation:**
Azure Load Balancer health probes originate from the IP address **168.63.129.16** (a special Azure platform IP). If the NSG on the VM's subnet or NIC blocks this IP on the probe port (TCP 80 in this case), the load balancer cannot receive a healthy response and marks the VM as unhealthy. You must ensure NSG rules allow inbound traffic from 168.63.129.16 on the health probe port.

---

### Question 33

You want to distribute HTTPS traffic across multiple VMs in Azure, with SSL termination, cookie-based session persistence, and URL-based routing (different paths to different backend pools).

**What Azure service should you use?**

- A. Azure Standard Load Balancer
- B. Azure Application Gateway
- C. Azure Traffic Manager
- D. Azure Front Door

**✅ Correct Answer: B**

**Explanation:**
**Azure Application Gateway** is a Layer 7 (HTTP/HTTPS) load balancer that supports:
- SSL/TLS termination
- Cookie-based session persistence
- URL path-based routing (e.g., /images to one pool, /api to another)
- WAF (Web Application Firewall)

Azure Load Balancer is Layer 4 (TCP/UDP) and doesn't support HTTP-level features. Traffic Manager is DNS-based, not a proxy. Azure Front Door is a global CDN/WAF but is more complex than needed for this scenario.

---

### Question 34

You have a Standard Public Load Balancer with two VMs in the backend pool. What SLA does Microsoft guarantee?

- A. 99.9%
- B. 99.95%
- C. 99.99%
- D. No SLA is provided for Basic Load Balancer

**✅ Correct Answer: C**

**Explanation:**
**Standard Azure Load Balancer** comes with a **99.99% SLA**. Basic Load Balancer has no SLA. This is a key differentiator and exam topic. Note: The VM SLA also contributes to the overall application SLA, so you need 2+ VMs in an Availability Set (99.95%) or Availability Zones (99.99%) to achieve the full benefit.

---

## Section 6: Azure DNS

---

### Question 35

You want to host the DNS zone for your domain `contoso.com` in Azure DNS. After creating the zone, what must you do to make Azure DNS authoritative for your domain?

- A. Create an A record pointing to Azure's IP
- B. Update your domain registrar's name server (NS) records to point to the Azure DNS name servers
- C. Enable DNSSEC on the Azure DNS zone
- D. Create a CNAME record in your existing DNS server

**✅ Correct Answer: B**

**Explanation:**
When you create an Azure DNS zone, Azure assigns **4 name servers** to that zone. To make Azure DNS authoritative, you must log into your domain registrar (e.g., GoDaddy, Namecheap) and replace their default name servers with the 4 Azure DNS name servers. Until you do this, DNS queries for your domain will still be answered by your registrar's name servers.

---

### Question 36

Your on-premises Active Directory domain is `corp.contoso.local`. After extending to Azure with a hybrid setup, Azure VMs cannot resolve on-premises computer names using the `.local` domain.

**What should you configure?**

- A. Create an Azure Private DNS Zone for `corp.contoso.local`
- B. Configure the vNet's DNS Servers setting to point to the on-premises Active Directory domain controllers
- C. Enable Azure DNS resolver
- D. Create CNAME records in Azure DNS for each on-premises server

**✅ Correct Answer: B**

**Explanation:**
By default, Azure VMs use **Azure-provided DNS** (168.63.129.16), which cannot resolve on-premises Active Directory domains. To resolve on-premises DNS names, you must configure the **vNet's DNS Servers** setting to use the IP addresses of your on-premises (or Azure-hosted) Active Directory Domain Controllers. The DCs run the DNS service and can resolve the `.local` domain.

---

## Section 7: Azure Backup and Site Recovery

---

### Question 37

You are creating a Recovery Services Vault to back up Azure VMs in the East US region. Where must the vault be located?

- A. In any Azure region
- B. In a different region from the VMs for geo-redundancy
- C. In the same region as the VMs (East US)
- D. In the paired region (West US)

**✅ Correct Answer: C**

**Explanation:**
An Azure Backup Recovery Services Vault must be in the **same region** as the VMs it backs up. This is a fundamental requirement. The vault itself stores backup data using geo-redundant storage (GRS) by default, which replicates to a paired region — but the vault resource must be in the same region as the source VMs.

---

### Question 38

You have an Azure VM backed up with Azure Backup. The VM's OS disk is corrupted. You want to recover the VM to its last backup state as quickly as possible, replacing the corrupted disks.

**Which restore option should you use?**

- A. Create New VM
- B. Restore Disks
- C. Replace Existing Disks
- D. File Recovery

**✅ Correct Answer: C**

**Explanation:**
**Replace Existing** (also called "Replace disks on existing VM") is the fastest option when the VM still exists but its disks are corrupted. Azure Backup automatically takes a snapshot of the current (corrupted) state before replacing, so you can roll back if needed. "Create New VM" creates a brand-new VM from the backup. "Restore Disks" gives you a managed disk that you then attach manually. "File Recovery" is for recovering individual files, not an OS disk.

---

### Question 39

You are setting up Azure Site Recovery (ASR) to replicate Azure VMs from East US to West US for disaster recovery. Where should you create the Recovery Services Vault?

- A. In East US (same as source VMs)
- B. In West US (target region)
- C. In any region — vault location doesn't matter for ASR
- D. In a management region separate from both

**✅ Correct Answer: B**

**Explanation:**
For Azure Site Recovery, the Recovery Services Vault must be in the **target (recovery) region** — different from the source VMs. This is the opposite of Azure Backup (which requires the vault in the same region as the VMs). The vault in the recovery region orchestrates the replication and stores the replica information. This is a common exam trap to confuse ASR vault placement with Backup vault placement.

---

### Question 40

After failing over VMs from Region A to Region B using Azure Site Recovery, you want to test whether the DR plan works without affecting the production workload in Region B.

**What ASR feature allows this?**

- A. Planned Failover
- B. Unplanned Failover
- C. Test Failover
- D. Failback

**✅ Correct Answer: C**

**Explanation:**
**Test Failover** in ASR creates a test copy of the VM in the target region using a separate isolated network. This allows you to validate the DR process (app starts, connectivity works, RTO/RPO met) without disrupting the actively replicated production configuration. After testing, you run "Cleanup test failover" to remove the test VM. Failback is the process of returning VMs to the original region after a real failover.

---

## Section 8: Azure Monitor and Diagnostics

---

### Question 41

You want to receive an email notification when the average CPU utilization of a specific VM exceeds 80% for more than 5 minutes.

**What should you configure in Azure Monitor?**

- A. A Log Analytics query and workspace
- B. An Alert Rule with a metric condition and an Action Group
- C. Diagnostic settings to a storage account
- D. Azure Advisor recommendations

**✅ Correct Answer: B**

**Explanation:**
The solution requires:
1. **Alert Rule**: Set the condition to "metric = CPU Percentage > 80% for 5 minutes" on the specific VM
2. **Action Group**: Contains the email address(es) to notify when the alert fires

Diagnostic settings send telemetry data to storage/Event Hub/Log Analytics but don't trigger notifications. Log Analytics queries require more setup and are for historical analysis. Azure Advisor provides proactive recommendations, not real-time alerts.

---

### Question 42

You want to monitor network traffic flowing through NSGs to track which IP addresses are accessing your Azure resources and identify unusual patterns.

**What should you enable?**

- A. Azure Security Center (Microsoft Defender for Cloud)
- B. NSG Flow Logs
- C. IP Flow Verify
- D. Connection Monitor

**✅ Correct Answer: B**

**Explanation:**
**NSG Flow Logs** capture information about IP traffic flowing through NSGs, including: source/destination IP, source/destination port, protocol, direction (inbound/outbound), and whether traffic was allowed or denied. This data is stored in a storage account and can be analyzed with Traffic Analytics in Azure Monitor for pattern visualization. IP Flow Verify tests specific flows on-demand. Connection Monitor measures latency/reachability. Security Center provides threat intelligence but not raw flow data.

---

### Question 43

You need to view the topology (visual diagram) of your virtual network and all connected resources.

**Which Network Watcher feature should you use?**

- A. IP Flow Verify
- B. Connection Troubleshoot
- C. Topology
- D. Network Performance Monitor

**✅ Correct Answer: C**

**Explanation:**
The **Topology** feature in Azure Network Watcher generates a visual diagram of all resources within a virtual network, including subnets, VMs, NICs, public IPs, NSGs, and their connections. This is useful for understanding network architecture at a glance. IP Flow Verify tests NSG rules, Connection Troubleshoot tests connectivity, Network Performance Monitor monitors hybrid network health over time.

---

### Question 44

You want to configure Diagnostic Settings for an Azure VM to collect and store OS-level performance metrics (like memory usage, disk queue length) which are not available as platform metrics.

**What must you install on the VM?**

- A. Azure Monitor Logs agent
- B. Azure Diagnostics extension (AMA — Azure Monitor Agent)
- C. Network Watcher extension
- D. Custom Script Extension

**✅ Correct Answer: B**

**Explanation:**
Platform metrics (CPU%, Disk bytes, Network bytes) are collected automatically from the Azure hypervisor layer without any agent. However, **guest OS metrics** (memory usage, disk queue length, page file usage, etc.) are NOT available as platform metrics — they require an **agent** running inside the VM. The **Azure Monitor Agent (AMA)** collects these guest OS metrics (and logs) and sends them to a Log Analytics workspace or Azure Monitor Metrics.

---

### Question 45

You receive a critical alert at 2 AM due to planned maintenance work your team scheduled. You want to suppress all alerts for a 4-hour maintenance window without deleting the alert rules.

**What should you use?**

- A. Delete the alert rules temporarily
- B. Disable the alert rules
- C. Create an Action Rule to suppress notifications during the maintenance window
- D. Remove the action groups from the alert rules

**✅ Correct Answer: C**

**Explanation:**
**Action Rules** (now called "Alert Processing Rules" in the Azure portal) allow you to suppress notifications from action groups during a defined time window. The underlying alert rules continue to evaluate conditions — you just suppress the notifications. This is exactly the right tool for planned maintenance suppression. Deleting or disabling rules would prevent alert evaluation entirely, which is risky (you'd miss real issues).

---

## Section 9: Mixed Topics (Advanced)

---

### Question 46

You need to restrict who can create resources in a specific Azure subscription to only the West Europe and North Europe regions.

**What is the best approach?**

- A. Create a custom RBAC role that restricts access to those regions
- B. Assign an Azure Policy with the "Allowed Locations" built-in definition to the subscription
- C. Apply a resource lock to the subscription
- D. Create Management Groups for each region

**✅ Correct Answer: B**

**Explanation:**
The **"Allowed Locations"** built-in Azure Policy definition, when assigned to a subscription, ensures that resources can only be deployed to the specified regions (West Europe, North Europe). Any deployment to another region is denied. RBAC controls what actions users can perform, not where resources are deployed. Resource locks prevent deletion/modification but don't restrict deployment locations. Management Groups organize subscriptions, not restrict deployment regions.

---

### Question 47

Your company's Azure subscription has reached its limit for a certain resource type. You need to request an increase.

**Where do you go?**

- A. Azure Active Directory → Subscriptions
- B. Azure Portal → Help + Support → New Support Request (Technical) → Service Limit Increase
- C. Azure Cost Management
- D. Azure Policy → Compliance

**✅ Correct Answer: B**

**Explanation:**
Azure resource limits (also called **quotas**) are soft limits that can be increased by submitting a **support request** in the Azure portal. Go to Help + Support → New Support Request → Issue Type: "Service and subscription limits (quotas)." Some limits can also be increased via the "Quotas" page under the subscription.

---

### Question 48

You have a VM that was deployed with a Basic SKU Public IP. You want to associate it with a Standard Load Balancer.

**What must you do?**

- A. Upgrade the Basic Public IP to Standard SKU
- B. Remove the Basic Public IP from the VM (Standard LB only works with VMs that have Standard or no public IP)
- C. Both A and B are valid approaches
- D. This is not possible — you must create a new VM

**✅ Correct Answer: C**

**Explanation:**
Two valid approaches:
1. **Upgrade the Basic Public IP to Standard SKU** (can be done on an unattached IP; a running VM's IP can be migrated with some steps)
2. **Dissociate (remove) the Basic Public IP** from the VM, then add the VM to the Standard Load Balancer backend pool — the VM doesn't need its own public IP to be load balanced

Both approaches are valid. You do NOT need to recreate the VM.

---

### Question 49

You want to give a user the ability to assign roles to other users within a specific resource group, but not outside that resource group, and without giving them full Owner access at the subscription level.

**What should you do?**

- A. Assign the Contributor role at the resource group level
- B. Assign the Owner role at the resource group level
- C. Assign the User Access Administrator role at the resource group level
- D. Assign the User Access Administrator role at the subscription level

**✅ Correct Answer: B**

**Explanation:**
The **Owner** role at the resource group level grants full resource management rights PLUS the ability to assign roles — but only within that resource group's scope. The **User Access Administrator** role grants the ability to manage access (assign roles) but provides no resource management capabilities. Since the question says "assign roles to other users," either Owner or UAA could work, but Owner at the RG level is the scoped answer that also gives resource management. Actually, for **only** role assignment without other resource management, **User Access Administrator at the resource group level (Option C)** is the most minimal/least privilege approach. However, if they need both resource management AND role assignment, Owner at RG level is correct. The question says "assign roles" as the primary need — so **C** is the most privileged-minimal answer.

> **Note:** In many exam contexts, the answer is C (User Access Administrator at RG level) for least privilege. If combined resource management + role assignment is needed, B is correct.

---

### Question 50

You are preparing for an Azure DR test. Your production VMs are in East US. The ASR replication is configured to West US. During the test failover, which network should you use?

- A. The production network in West US (same as where production VMs would fail over to)
- B. An isolated test network in West US so production replication is not affected
- C. The production network in East US (source)
- D. Any network — test failover doesn't use a real network

**✅ Correct Answer: B**

**Explanation:**
During **Test Failover**, ASR starts a copy of the VM in the target region. You should use an **isolated test network** (not the production network in West US). If you use the production recovery network, your test VMs might interfere with production traffic or conflict with the actual recovery environment. After the test, you clean up the test VMs. ASR documentation explicitly recommends creating a separate isolated vNet for test failover validation.

---

## Summary: Key Numbers to Remember

| Fact | Value |
|------|-------|
| Management Group max depth | 6 levels (excluding root) |
| Availability Set SLA | 99.95% (2+ VMs) |
| Availability Zone SLA | 99.99% (2+ zones) |
| Standard Load Balancer SLA | 99.99% |
| VPN Gateway deployment time | ~45 minutes |
| Azure Data Box capacity | 80 TB |
| Blob Cool tier minimum retention | 30 days |
| Blob Archive tier minimum retention | 180 days |
| Max tags per resource | 50 |
| Trusted IPs (MFA bypass) | IPv4 only, intranet only |
| SSPR Password Writeback license | Azure AD Premium P1 or P2 |
| Gateway subnet name | Must be exactly "GatewaySubnet" |
| Policy-based VPN max S2S connections | 1 |
| Route-based VPN | Multiple S2S, P2S, vNet-to-vNet |
| Backup vault region | Same as source VMs |
| ASR vault region | Different from source VMs (recovery region) |

---

*Practice questions compiled from ExamTopics, Microsoft Learn, and AZ-104 course materials. Last updated: 2025*
