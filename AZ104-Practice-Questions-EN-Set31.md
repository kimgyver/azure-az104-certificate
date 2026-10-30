# AZ-104 Practice Questions (Set 31) — English

Gap set against the **17 April 2026** skills guide. Covers official items that were missing or thin in Sets 11–23. Does not include Identity/Policy, Azure Firewall, Application Gateway, AKS, or PIM.

| Area | Questions |
|---|---|
| Compute (VM, disks, VMSS, containers) | 1–13 |
| Storage (AzCopy, object replication, encryption) | 14–20 |
| Networking (NSG, DNS, Public IP, endpoints, Watcher) | 21–30 |
| Monitor and backup | 31–40 |

---

## 1) Compute: VMs, disks, VMSS, containers

### Q1
You must encrypt a VM’s **temp disk** and **OS/data disk caches** on the host. You do not want BitLocker inside the guest OS. What should you use?

- A. Azure Disk Encryption (ADE)
- B. Encryption at host
- C. Storage Service Encryption (SSE) with PMK only
- D. HTTPS-only on the storage account

**Answer: B**
**Explanation:** Encryption at host encrypts data on the compute host, including temp disks, disk caches, and ephemeral OS disks. ADE runs inside the guest (BitLocker/DM-Crypt). SSE encrypts managed disks at rest in storage but does not cover host cache or temp disks.

---

### Q2
You want to enable encryption at host on a running VM. What is the correct sequence?

- A. Change the VM property while it is running
- B. Stop (deallocate) the VM, enable encryption at host, then start it
- C. Install ADE first, then enable encryption at host
- D. Snapshot the OS disk and move only the encryption key to Key Vault

**Answer: B**
**Explanation:** Enable encryption at host at **create time**, or after **deallocate**. You cannot turn it on while the VM is running. ADE is not a prerequisite.

---

### Q3
You need to move VM1 to another resource group in the **same subscription and region**. Which feature should you use?

- A. Azure Site Recovery
- B. Azure Resource Mover
- C. Azure resource Move
- D. Restore from a Recovery Services vault

**Answer: C**
**Explanation:** Same-region moves to another resource group (or another subscription in the same tenant) use **Move**. Resource Mover and ASR are for **cross-region** moves. Backup restore creates a new VM; it does not move the existing resource.

---

### Q4
You need to move VM1 from East US to West US. Can you use resource Move?

- A. Yes. Move supports cross-region relocation
- B. No. Use Azure Resource Mover, ASR, or backup/restore
- C. Yes, if you deallocate the VM first
- D. No. Cross-region moves are not supported at all

**Answer: B**
**Explanation:** The Move API does **not** change region. Cross-region options are Resource Mover, Site Recovery, or restore a backup in the target region. “Not supported at all” is wrong; there are supported methods.

---

### Q5
You want to move VM1 to another subscription. Which condition is required?

- A. Both subscriptions must be in the same Microsoft Entra tenant
- B. Same management group is enough even if the directories differ
- C. Owner on the target is enough even if the directory is different
- D. A public IP on the VM always blocks subscription moves

**Answer: A**
**Explanation:** Cross-subscription moves require the **same Entra tenant**. A public IP may add constraints, but it does not always block the move. Move the NIC, disks, and other dependencies together.

---

### Q6
You create a Virtual Machine Scale Set. Instances must be manageable with the standard VM API, and you may mix VM sizes later. Which orchestration mode should you choose?

- A. Uniform
- B. Flexible
- C. Availability Set
- D. Proximity Placement Group

**Answer: B**
**Explanation:** **Flexible** is the recommended mode. It uses `Microsoft.Compute/virtualMachines`, supports instance mix, and can mix Spot and regular VMs. Uniform manages identical scale-set VMs through the VMSS VM API.

---

### Q7
Which statement about Uniform orchestration is correct?

- A. A scaling profile (VM template) is optional
- B. A scaling profile is **required**; new capacity is created from that template
- C. You can freely add existing standalone VMs into the set
- D. Linux and Windows can mix in one Uniform set

**Answer: B**
**Explanation:** Uniform **requires** a scaling profile (image, NIC, disks, credentials). No-profile sets, attaching existing VMs, and mixed OS are Flexible features.

---

### Q8
VMSS autoscale needs weekday rules, weekend scale-in, and a one-day Black Friday scale-out. How should you combine autoscale profile types?

- A. Three Default profiles
- B. Default (normal) + Recurrence (weekend) + Fixed-date (Black Friday)
- C. Three Recurrence profiles
- D. Fixed-date only, and delete Default

**Answer: B**
**Explanation:** The three types are **Default** (always present, cannot delete), **Recurrence** (days of week), and **Fixed-date** (a specific window). One Default per setting.

---

### Q9
In which order does autoscale pick a profile?

- A. Default → Recurrence → Fixed-date
- B. Recurrence → Fixed-date → Default
- C. Fixed-date → Recurrence → Default
- D. Lowest numeric priority on the profile

**Answer: C**
**Explanation:** Evaluation order is **Fixed-date → Recurrence → Default**. The first match wins. A Black Friday Fixed-date profile suppresses Recurrence/Default that day.

---

### Q10
Autoscale Default capacity is 3, minimum 2, maximum 10. Metrics cannot be read, and the set currently has 2 instances. What does autoscale do?

- A. Scale immediately to the maximum of 10
- B. Scale out to Default (3), because current is below Default
- C. Do nothing
- D. Scale in to the minimum of 2

**Answer: B**
**Explanation:** If metrics are unavailable and **current < Default**, autoscale scales out to Default for availability. It does not scale in if current is already at or above Default.

---

### Q11
You want to raise a Premium SSD data disk from performance tier P15 to P20. Must you stop the VM?

- A. Yes. Changing tier requires deallocate, like increasing disk size
- B. No. You can change the performance tier while the VM is running
- C. Yes. You must detach the disk first
- D. No, but only after converting the disk to Ultra Disk

**Answer: B**
**Explanation:** Premium SSD **performance tiers** can change with the VM running. Increasing **disk size** is a different operation.

---

### Q12
A Windows Server failover cluster needs several VMs to use the **same managed disk** at once. What should you use?

- A. Azure Files snapshots
- B. Azure Shared Disk
- C. Copy the same VHD to each VM
- D. Mount an Azure Files share as the OS disk

**Answer: B**
**Explanation:** **Azure shared disks** use SCSI persistent reservations so clustered VMs can share one managed disk. Supported on specific SKUs such as Premium SSD and Ultra. Copied VHDs and Azure Files are not clustered shared disks.

---

### Q13
You need containers without managing a cluster, scale to zero when there is no HTTP traffic, plus ingress and revisions. What should you choose?

- A. Azure Container Instances
- B. Azure Container Apps
- C. Azure Kubernetes Service
- D. Azure Batch only

**Answer: B**
**Explanation:** **Container Apps** is the serverless container app platform (scale to zero, HTTP ingress, revisions). ACI is a building block for short-lived containers. AKS is for Kubernetes API control.

---

## 2) Storage: AzCopy, object replication, encryption

### Q14
You must copy hundreds of GB of blobs from account A to account B **without** pulling data through your PC, and you must script it. What should you use?

- A. Drag-and-drop in Azure Storage Explorer
- B. AzCopy (server-to-server copy)
- C. Download in the portal, then upload
- D. Robocopy

**Answer: B**
**Explanation:** **AzCopy** can do **server-to-server** copies when both ends are Azure, so data stays on the Azure backbone. Storage Explorer is better for browsing and small jobs.

---

### Q15
You will use AzCopy and do not want account keys in the script. How do you authenticate with Microsoft Entra ID?

- A. Run `azcopy login` and ensure a data-plane RBAC role (for example Storage Blob Data Contributor)
- B. Put the account key in an environment variable only
- C. Append an account SAS to the URL
- D. Enable anonymous public access

**Answer: A**
**Explanation:** Entra ID auth is `azcopy login` (or a managed identity/service principal) plus a data-plane role. Keys and SAS work but conflict with “no keys in the script.”

---

### Q16
You want periodic **one-way sync** from source to destination, including deleting destination blobs that were removed at the source. Which AzCopy command?

- A. `azcopy copy`
- B. `azcopy sync`
- C. `azcopy make`
- D. `azcopy jobs`

**Answer: B**
**Explanation:** `azcopy sync` is one-way sync. `--delete-destination` removes orphans on the target. `copy` does not sync deletions.

---

### Q17
You are configuring blob **object replication**. Which two features must be enabled on the **source** account?

- A. Hierarchical namespace and NFS 3.0
- B. Blob versioning and change feed
- C. Static website and Azure CDN
- D. Customer-managed keys and an immutable policy only

**Answer: B**
**Explanation:** Object replication requires **blob versioning** and **change feed** on the source, and **versioning** on the destination. The policy is created on the source account.

---

### Q18
Which statement about object replication is correct?

- A. It is synchronous, so RPO is zero
- B. It asynchronously replicates block blobs; the destination can lag
- C. It is only for page blobs (disk VHDs)
- D. It cannot be used with LRS accounts

**Answer: B**
**Explanation:** Object replication is **asynchronous** and for **block blobs**. It is not GRS. GRS is Microsoft’s paired-region copy; object replication copies to **accounts/containers you choose**.

---

### Q19
You want **customer-managed keys (CMK)** for storage account encryption. Where must the key live?

- A. Next to the storage account access keys
- B. Azure Key Vault or Managed HSM
- C. The VM certificate store
- D. A Recovery Services vault

**Answer: B**
**Explanation:** SSE with CMK uses a key in **Key Vault** or **Managed HSM**. Account access keys are for authorization, not SSE CMK.

---

### Q20
A blob was **overwritten**. Soft delete is on; versioning is off. Can you restore the previous content?

- A. Yes. Soft delete keeps overwrite history
- B. No. Overwrites need versioning; soft delete is for deleted blobs/versions
- C. Yes. Lifecycle management keeps the old tier
- D. No. Only snapshots help, and versioning is unrelated

**Answer: B**
**Explanation:** **Soft delete** retains deleted blobs (and versions). **Overwrites** need **versioning** (or a manual snapshot). Lifecycle management only moves/deletes by policy.

---

## 3) Networking

### Q21
A subnet NSG and a NIC NSG are both associated. What do **Effective security rules** on the NIC show?

- A. The NIC NSG only
- B. The subnet NSG only
- C. The **combined** result of subnet NSG and NIC NSG
- D. Azure Firewall rules only

**Answer: C**
**Explanation:** Traffic must pass **both** NSGs. A deny in either blocks the packet. Effective security rules show that combined view for troubleshooting.

---

### Q22
One NSG has inbound priority 100 Allow TCP 443 and priority 200 Deny TCP 443. What happens to 443 traffic?

- A. Deny is evaluated last, so traffic is denied
- B. Lower priority number wins first, so Allow applies
- C. When both match, Deny always wins
- D. Rules are ignored unless an ASG is used

**Answer: B**
**Explanation:** Within one NSG, **lower priority number is evaluated first**, and the first match wins. “Deny always wins” confuses this with combining two different NSGs.

---

### Q23
You want to allow 80/443 to a logical set of “web servers” without repeating IPs in NSG rules. What should you use?

- A. Application Security Group (ASG)
- B. Private DNS zone
- C. NAT Gateway
- D. Service endpoints

**Answer: A**
**Explanation:** **ASGs** group NICs so NSG source/destination can target the group instead of IP lists.

---

### Q24
VMs in a VNet must resolve each other as `vm1.corp.internal`. Azure-provided DNS alone is not enough. What is the minimum configuration?

- A. Create A records in a public Azure DNS zone
- B. Create a Private DNS zone `corp.internal`, link the VNet, and use auto-registration
- C. Edit each VM hosts file only
- D. Create a Traffic Manager profile

**Answer: B**
**Explanation:** **Azure Private DNS** linked to the VNet with **auto-registration** creates VM A records. Auto-registration can be enabled for only one zone per VNet. Public DNS is for internet names.

---

### Q25
You created a **private endpoint** on a storage account. VMs in the VNet must resolve the blob FQDN to the **private IP**. What do you need?

- A. A Microsoft.Storage service endpoint only
- B. A Private DNS zone for `privatelink.blob.core.windows.net` linked to the VNet
- C. Allow 0.0.0.0/0 on the storage firewall
- D. An NSG outbound Deny on 443

**Answer: B**
**Explanation:** The private endpoint has a private IP. Name resolution uses **Private DNS** (`privatelink.blob.core.windows.net`) so the public FQDN maps to that IP. Service endpoints do not assign a private IP.

---

### Q26
Which difference between service endpoints and private endpoints is correct?

- A. Both assign a private IP to the PaaS service
- B. Service endpoints keep the PaaS **public** endpoint but send subnet traffic over the Azure backbone; private endpoints expose PaaS on a **private IP** in your VNet
- C. Private endpoints are internet-only
- D. Service endpoints work only from on-premises

**Answer: B**
**Explanation:** Service endpoints lock the PaaS firewall to the subnet and use the backbone; the destination is still a public VIP. Private endpoints (Private Link) put a **VNet private IP** in front of the service.

---

### Q27
Which public IP SKU must you use with a Standard Load Balancer? (Basic public IPs were retired in 2025.)

- A. Basic only
- B. Standard
- C. Gateway SKU
- D. Premium Front Door SKU

**Answer: B**
**Explanation:** Standard LB requires a **Standard public IP**. Standard IPs are static, closed by default (use NSGs), and can be zone-redundant.

---

### Q28
Connectivity between two VMs fails intermittently. You need **continuous** RTT and failure-rate measurement with alerts. Which Network Watcher tool?

- A. IP flow verify
- B. Next hop
- C. Connection Monitor
- D. A one-time packet capture

**Answer: C**
**Explanation:** **Connection Monitor** watches endpoint pairs continuously. IP flow verify is a **one-shot** NSG check. Next hop is a single route lookup.

---

### Q29
You want to know **right now** whether an NSG allows a specific source IP to a destination IP:port. Which tool?

- A. Connection Monitor
- B. IP flow verify
- C. Azure Advisor
- D. Service Health

**Answer: B**
**Explanation:** That is **IP flow verify**. Connection Monitor is ongoing monitoring.

---

### Q30
A UDR sends `0.0.0.0/0` to an NVA. For the same prefix, what is the route priority?

- A. System routes > BGP > UDR
- B. BGP > UDR > system routes
- C. UDR > BGP > system routes
- D. The route created last

**Answer: C**
**Explanation:** For equal prefixes, **UDR > BGP > system**. A longer (more specific) prefix still wins over a shorter one.

---

## 4) Monitoring and backup

### Q31
You will back up **Azure managed disks** only. Which vault do you create?

- A. Recovery Services vault
- B. Backup vault
- C. Key Vault
- D. Disk Backup never uses a vault

**Answer: B**
**Explanation:** A **Backup vault** holds Azure Disks, Blobs, PostgreSQL, AKS, and similar newer workloads. A **Recovery Services vault** holds Azure VMs, Azure Files, SQL/HANA on VMs, and MARS.

---

### Q32
You will back up an Azure **VM**. Which vault?

- A. Backup vault
- B. Recovery Services vault
- C. Either; they are interchangeable
- D. A Log Analytics workspace

**Answer: B**
**Explanation:** IaaS VM backup uses a **Recovery Services vault**.

---

### Q33
You need **minutes-level RPO** and a failover/commit/reprotect workflow into a secondary region. Is backup Cross Region Restore enough?

- A. Yes. CRR replaces ASR
- B. No. That is Azure Site Recovery. CRR restores a backup point in the paired region
- C. Yes. A GRS vault fails over automatically
- D. No. Resource Mover is the only option

**Answer: B**
**Explanation:** **ASR** is replication plus failover, commit, and reprotect. **CRR** restores Azure Backup data in the paired region and can have an RPO of many hours. GRS alone does not auto-failover.

---

### Q34
To use Cross Region Restore on a Recovery Services vault, what do you select?

- A. Storage replication = RA-GRS in the dropdown
- B. Storage replication = **GRS**, then **enable Cross Region Restore**
- C. LRS plus CRR
- D. ZRS plus RA-GZRS

**Answer: B**
**Explanation:** Vault choices are **LRS / ZRS / GRS**. There is no RA-GRS dropdown. Enabling CRR on GRS upgrades storage to RA-GRS behind the scenes.

---

### Q35
You want Azure Backup **reports** for success/failure trends. What do you typically do first?

- A. Install only the Network Watcher agent on each VM
- B. Send vault diagnostics to a Log Analytics workspace and use Backup Reports
- C. Open the vault in Storage Explorer
- D. Create a Cost Management budget only

**Answer: B**
**Explanation:** Backup reports are built on **Log Analytics** (diagnostics / report settings) and the Backup Reports workbook or Backup center.

---

### Q36
During Saturday 02:00–06:00 maintenance you want to stop CPU alert **emails**, but keep the alert rule and still see fired alerts in the portal. What should you use?

- A. Delete and recreate the alert rule
- B. An **alert processing rule** that suppresses notifications on that schedule
- C. Delete the metric
- D. Block metrics with an NSG

**Answer: B**
**Explanation:** **Alert processing rules** suppress or add notifications. Alerts still fire and remain visible. Do not delete the alert rule.

---

### Q37
When a metric alert fires, you want email and a webhook. Which resource defines those **destinations**?

- A. An alert processing rule only
- B. An action group
- C. Azure Policy
- D. Budget contact roles only

**Answer: B**
**Explanation:** An **action group** is who/how to notify. The alert rule is the condition; processing rules suppress or add actions.

---

### Q38
In Log Analytics you want computers with a Heartbeat in the last hour. Which KQL is correct?

- A. `SELECT * FROM Heartbeat WHERE TimeGenerated > NOW()-1h`
- B. `Heartbeat | where TimeGenerated > ago(1h) | summarize by Computer`
- C. `Get-Heartbeat -LastHours 1`
- D. `az monitor heartbeat list --hours 1`

**Answer: B**
**Explanation:** Azure Monitor logs use **KQL**, not SQL. `ago(1h)` is the last hour.

---

### Q39
You configure diagnostic settings on a storage account. Which destination is **not** supported?

- A. Log Analytics workspace
- B. Storage account
- C. Event Hubs
- D. An Azure Front Door profile

**Answer: D**
**Explanation:** Typical diagnostic destinations are **Log Analytics, a storage account, and Event Hubs** (plus some partners). Front Door is not a diagnostic sink.

---

### Q40
You committed an ASR failover to the secondary region. To return to the original region later, what must you do after failover?

- A. Change the vault to LRS
- B. **Reprotect** to reverse replication, then fail back
- C. Undo the commit
- D. Recreate Connection Monitor only

**Answer: B**
**Explanation:** **Commit** finalizes failover and deletes prior recovery points; it cannot be undone. Failback requires **reprotect** (reverse replication) then failback.

---

## Answer key

| Q | Ans | Q | Ans |
|---|---|---|---|
| 1 | B | 21 | C |
| 2 | B | 22 | B |
| 3 | C | 23 | A |
| 4 | B | 24 | B |
| 5 | A | 25 | B |
| 6 | B | 26 | B |
| 7 | B | 27 | B |
| 8 | B | 28 | C |
| 9 | C | 29 | B |
| 10 | B | 30 | C |
| 11 | B | 31 | B |
| 12 | B | 32 | B |
| 13 | B | 33 | B |
| 14 | B | 34 | B |
| 15 | A | 35 | B |
| 16 | B | 36 | B |
| 17 | B | 37 | B |
| 18 | B | 38 | B |
| 19 | B | 39 | D |
| 20 | B | 40 | B |
