# AZ-104 Practice Questions (Set 2) - English

50 new practice questions with answers and short explanations.

---

## 1) Identity and Governance

### Q1
You need to allow a team to manage VMs but prevent them from granting access to others.
- A. Owner
- B. Contributor
- C. User Access Administrator
- D. Reader

**Answer: B**
**Explanation:** Contributor can manage resources but cannot assign RBAC roles.

### Q2
You want to enforce that all resources must have the tag `Environment`.
- A. RBAC
- B. Resource Lock
- C. Azure Policy
- D. Management Group

**Answer: C**
**Explanation:** Azure Policy enforces governance rules like required tags.

### Q3
Which role can assign RBAC permissions without full resource management rights?
- A. Contributor
- B. Reader
- C. User Access Administrator
- D. Virtual Machine Contributor

**Answer: C**
**Explanation:** User Access Administrator is scoped for access management.

### Q4
A lock should prevent deletion but still allow modifications.
- A. ReadOnly lock
- B. Delete lock
- C. CanNotModify lock
- D. Audit lock

**Answer: B**
**Explanation:** Delete lock blocks deletion only.

### Q5
Where can you apply Azure Policy assignments?
- A. Tenant only
- B. Subscription only
- C. Resource group only
- D. Management group, subscription, resource group, resource

**Answer: D**
**Explanation:** Policy supports multiple scopes.

### Q6
You need to organize multiple subscriptions under one hierarchy for centralized governance.
- A. Resource Groups
- B. Management Groups
- C. Tags
- D. Availability Sets

**Answer: B**
**Explanation:** Management groups are built for subscription hierarchy.

### Q7
SSPR password writeback to on-prem AD requires:
- A. Azure AD Free
- B. Azure AD Premium P1/P2
- C. Office 365 E1 only
- D. No license

**Answer: B**
**Explanation:** Password writeback requires Premium P1 or P2.

### Q8
Which identity is best for Azure VM to access Key Vault without secrets in code?
- A. Service principal with client secret in script
- B. Managed identity
- C. Local admin account
- D. Shared account key

**Answer: B**
**Explanation:** Managed identity avoids embedded credentials.

### Q9
You need least-privilege read-only access to all resources in a subscription.
- A. Reader at subscription scope
- B. Contributor at resource group scope
- C. Owner at subscription scope
- D. User Access Administrator

**Answer: A**
**Explanation:** Reader grants view-only permissions.

### Q10
What is the maximum management group depth (excluding root)?
- A. 4
- B. 6
- C. 8
- D. 10

**Answer: B**
**Explanation:** Azure supports up to 6 levels below root.

---

## 2) Storage

### Q11
You need read access to secondary region before failover.
- A. LRS
- B. GRS
- C. RA-GRS
- D. ZRS

**Answer: C**
**Explanation:** RA-GRS enables read access to secondary endpoint.

### Q12
Which SAS type is generally most secure?
- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. All equal

**Answer: C**
**Explanation:** User delegation SAS uses Microsoft Entra credentials.

### Q13
You need to revoke container URL access centrally without rotating account keys.
- A. Ad-hoc SAS
- B. Stored access policy + service SAS
- C. Public access
- D. Storage account key sharing

**Answer: B**
**Explanation:** Stored access policy allows centralized SAS control.

### Q14
Data is rarely accessed and can tolerate rehydration delay.
- A. Hot tier
- B. Cool tier
- C. Archive tier
- D. Premium

**Answer: C**
**Explanation:** Archive is lowest cost for long-term cold data.

### Q15
Cool blob tier minimum retention is:
- A. 7 days
- B. 30 days
- C. 90 days
- D. 180 days

**Answer: B**
**Explanation:** Cool tier has 30-day minimum retention.

### Q16
Archive blob tier minimum retention is:
- A. 30 days
- B. 90 days
- C. 120 days
- D. 180 days

**Answer: D**
**Explanation:** Archive tier minimum is 180 days.

### Q17
You must migrate 70+ TB from on-prem with limited bandwidth.
- A. AzCopy
- B. Azure File Sync
- C. Azure Data Box
- D. FTP

**Answer: C**
**Explanation:** Data Box is optimized for large offline transfer.

### Q18
Best default storage account kind for modern workloads:
- A. Storage (GPv1)
- B. BlobStorage
- C. StorageV2 (GPv2)
- D. Premium BlockBlob only

**Answer: C**
**Explanation:** GPv2 is recommended for most scenarios.

### Q19
You want SMB shared files in Azure.
- A. Blob container
- B. Azure Files
- C. Managed disk
- D. Queue storage

**Answer: B**
**Explanation:** Azure Files provides SMB/NFS file shares.

### Q20
How many access keys does a storage account have by default?
- A. 1
- B. 2
- C. 3
- D. 4

**Answer: B**
**Explanation:** Two keys support key rotation.

---

## 3) Compute

### Q21
VM must stay available during planned maintenance across two instances.
- A. Availability Set
- B. Backup vault
- C. Azure Site Recovery only
- D. Auto-shutdown

**Answer: A**
**Explanation:** Availability sets spread VMs across update/fault domains.

### Q22
To add a NIC to an existing VM, first:
- A. Resize VM
- B. Stop/deallocate VM
- C. Reboot guest OS
- D. Enable boot diagnostics

**Answer: B**
**Explanation:** NIC attach/detach requires deallocated state.

### Q23
Customer-managed disk encryption needs Key Vault plus:
- A. NSG
- B. Disk Encryption Set
- C. Recovery vault
- D. Route table

**Answer: B**
**Explanation:** DES connects managed disks to Key Vault key.

### Q24
Which tool is best for repeatable IaC deployments in Azure?
- A. ARM/Bicep
- B. Portal click-only
- C. RDP scripts only
- D. Manual runbooks only

**Answer: A**
**Explanation:** ARM/Bicep is declarative infrastructure as code.

### Q25
Custom Script Extension is mainly used to:
- A. Scale VM automatically
- B. Run post-deployment scripts on VM
- C. Encrypt VM disks
- D. Replace VM OS

**Answer: B**
**Explanation:** It executes setup scripts on VMs.

### Q26
Default VM Scale Set scale-in policy removes:
- A. Oldest instance
- B. Newest instance
- C. Highest instance ID
- D. Lowest CPU instance

**Answer: C**
**Explanation:** Default behavior targets highest instance ID.

### Q27
Availability Set SLA with 2+ VMs is:
- A. 99.9%
- B. 99.95%
- C. 99.99%
- D. 99.999%

**Answer: B**
**Explanation:** Availability Set SLA is 99.95%.

### Q28
Availability Zone SLA with 2+ zones is:
- A. 99.95%
- B. 99.97%
- C. 99.99%
- D. No SLA

**Answer: C**
**Explanation:** Zone-redundant VM setup can reach 99.99%.

### Q29
You need automatic scale based on CPU metrics.
- A. Availability Set
- B. VM Scale Set
- C. Recovery Services Vault
- D. Azure Policy

**Answer: B**
**Explanation:** VMSS provides autoscaling capabilities.

### Q30
Best way to create a VM from an existing VM image state:
- A. Snapshot -> managed disk -> create VM
- B. Export NIC
- C. Clone NSG
- D. Restart source VM

**Answer: A**
**Explanation:** Snapshot and managed disk workflow is standard.

---

## 4) Networking

### Q31
Gateway subnet must be named:
- A. AzureGateway
- B. VNetGatewaySubnet
- C. GatewaySubnet
- D. Any name

**Answer: C**
**Explanation:** Gateway subnet name is fixed.

### Q32
Gateway subnet recommended size is:
- A. /30
- B. /29
- C. /27 or /28
- D. /32

**Answer: C**
**Explanation:** /27 or /28 is recommended for VPN gateway growth.

### Q33
Which gateway type supports multiple S2S and P2S?
- A. Policy-based
- B. Route-based
- C. Basic only
- D. ExpressRoute only

**Answer: B**
**Explanation:** Route-based VPN supports broader scenarios.

### Q34
Resource representing on-prem VPN device info:
- A. Virtual Network Gateway
- B. Local Network Gateway
- C. Application Gateway
- D. Traffic Manager

**Answer: B**
**Explanation:** LNG stores remote public IP and address space.

### Q35
vNet peering between two vNets requires:
- A. One-way peering only
- B. Bi-directional configuration
- C. VPN gateway mandatory
- D. Overlapping CIDR

**Answer: B**
**Explanation:** Configure peering from both sides.

### Q36
NSG rule priority behavior:
- A. Higher number wins
- B. Lower number wins
- C. Last created wins
- D. Deny always wins regardless

**Answer: B**
**Explanation:** Lower number means higher priority.

### Q37
Tool to verify whether NSG allows specific flow:
- A. Packet capture
- B. Connection monitor
- C. IP Flow Verify
- D. Topology

**Answer: C**
**Explanation:** IP Flow Verify returns allow/deny + matching rule.

### Q38
Tool to identify route path to destination:
- A. Effective security rules
- B. Next Hop
- C. NSG flow log
- D. DDoS dashboard

**Answer: B**
**Explanation:** Next Hop checks effective routing decision.

### Q39
Private access to Azure PaaS without private IP on target service:
- A. Private Endpoint
- B. Service Endpoint
- C. vNet peering
- D. VPN Gateway

**Answer: B**
**Explanation:** Service endpoint keeps traffic on Microsoft backbone.

### Q40
A VM cannot access internet; subnet NSG looks fine. Next checks should include:
- A. Public IP/NAT path
- B. NIC NSG
- C. Route table (UDR)
- D. All of the above

**Answer: D**
**Explanation:** Connectivity depends on NSG + routing + egress path.

---

## 5) Load Balancing, DNS, DR, Monitoring

### Q41
Standard Load Balancer backend VM has Basic public IP. Result?
- A. Works normally
- B. Unsupported/incompatible
- C. Works if same subnet only
- D. Works after reboot

**Answer: B**
**Explanation:** Standard LB requires Standard PIP or no PIP.

### Q42
Standard Load Balancer SLA is:
- A. No SLA
- B. 99.9%
- C. 99.95%
- D. 99.99%

**Answer: D**
**Explanation:** Standard LB provides 99.99% SLA.

### Q43
Load balancer health probe source IP commonly referenced in exam:
- A. 10.0.0.1
- B. 168.63.129.16
- C. 169.254.169.254
- D. 8.8.8.8

**Answer: B**
**Explanation:** NSG must allow probe traffic where required.

### Q44
Need SSL termination and URL path routing.
- A. Standard Load Balancer
- B. Application Gateway
- C. Basic Load Balancer
- D. VPN Gateway

**Answer: B**
**Explanation:** Application Gateway is Layer 7 HTTP/HTTPS.

### Q45
To make Azure DNS authoritative for `contoso.com`:
- A. Add A record only
- B. Update registrar NS records to Azure DNS name servers
- C. Enable ASR
- D. Create route table

**Answer: B**
**Explanation:** Registrar must delegate zone to Azure DNS NS.

### Q46
Azure VMs must resolve on-prem `.local` names in hybrid setup.
- A. Use default Azure DNS only
- B. Set vNet DNS servers to AD DNS/DC IPs
- C. Enable DDoS Standard
- D. Add public CNAME records

**Answer: B**
**Explanation:** Custom DNS points clients to AD-aware DNS servers.

### Q47
Azure Backup vault region for VM backup should be:
- A. Same as source VM region
- B. Any region
- C. Target DR region only
- D. Must be paired region only

**Answer: A**
**Explanation:** Backup vault must be in same region as VM.

### Q48
ASR vault region for East US -> West US replication:
- A. East US
- B. West US
- C. Any region
- D. Not required

**Answer: B**
**Explanation:** ASR uses vault in recovery(target) region.

### Q49
For ASR test failover, network should be:
- A. Production recovery network only
- B. Isolated test network in target region
- C. Source region production network
- D. No network needed

**Answer: B**
**Explanation:** Isolated test network prevents production impact.

### Q50
For CPU > 80% for 5 minutes email alert, configure:
- A. Diagnostic setting only
- B. Metric alert + action group
- C. NSG flow logs only
- D. Policy assignment only

**Answer: B**
**Explanation:** Alerts trigger notifications through action groups.

---

## Quick Numbers
- Management group depth: 6 (excluding root)
- Availability Set SLA: 99.95%
- Availability Zone SLA: 99.99%
- Standard Load Balancer SLA: 99.99%
- Cool tier minimum: 30 days
- Archive tier minimum: 180 days
- Gateway name: GatewaySubnet

Last updated: 2026-05-03
