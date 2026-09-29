# AZ-104 Microsoft Azure Administrator — Course Summary
# AZ-104 Microsoft Azure Administrator — 강의 요약

---

## Table of Contents / 목차

1. [Microsoft Entra ID (Azure AD)](#1-microsoft-entra-id-azure-ad)
2. [SSPR (Self-Service Password Reset)](#2-sspr-self-service-password-reset)
3. [MFA (Multi-Factor Authentication)](#3-mfa-multi-factor-authentication)
4. [RBAC (Role-Based Access Control)](#4-rbac-role-based-access-control)
5. [Azure Governance](#5-azure-governance)
6. [Azure Storage](#6-azure-storage)
7. [Virtual Machines (VMs)](#7-virtual-machines-vms)
8. [ARM Templates & Custom Script Extensions](#8-arm-templates--custom-script-extensions)
9. [Virtual Networking](#9-virtual-networking)
10. [Site-to-Site VPN](#10-site-to-site-vpn)
11. [Azure DNS](#11-azure-dns)
12. [Availability Sets & Zones](#12-availability-sets--availability-zones)
13. [Azure Load Balancers](#13-azure-load-balancers)
14. [Network Watcher](#14-network-watcher)
15. [Azure Backup & Recovery Services](#15-azure-backup--recovery-services)
16. [Azure Site Recovery (ASR)](#16-azure-site-recovery-asr)
17. [Azure Monitor & Diagnostics](#17-azure-monitor--diagnostics)
20. [2026 Gap Fill: Set6 Topics](#section-20--2026-gap-fill-set6-topics)

21. [2026 Gap Fill: Expected Questions Set 7 (3-1 and 3-2)](#section-21--2026-gap-fill-expected-questions-3-1-and-3-2)
22. [2026 Gap Fill: Expected Questions Set 8](#section-22--2026-gap-fill-expected-questions-set-8)
23. [2026 Gap Fill: Expected Questions Set 9](#section-23--2026-gap-fill-expected-questions-set-9)
24. [2026 Gap Fill: Expected Questions Set 10](#section-24--2026-gap-fill-expected-questions-set-10)
25. [2026 Gap Fill: Expected Questions Set 11](#section-25--2026-gap-fill-expected-questions-set-11)
26. [2026 Gap Fill: Expected Questions Set 12](#section-26--2026-gap-fill-expected-questions-set-12)
27. [2026 Gap Fill: Expected Questions Set 14](#section-27--2026-gap-fill-expected-questions-set-14)
28. [2026 Gap Fill: Expected Questions Set 13](#section-28--2026-gap-fill-expected-questions-set-13)

---

## 1. Microsoft Entra ID (Azure AD)

### EN
- **User Types**: Cloud-only, Synced (from on-prem AD), Guest (B2B)
- **Group Types**: Security Groups, Microsoft 365 Groups
- **Assignment Types**: Assigned (manual), Dynamic User, Dynamic Device
- **Bulk Operations**: CSV-based bulk create/invite/delete
- **B2B (Business-to-Business)**: Invite external users as guests
- **Admin Units**: Scope Entra ID admin permissions to a subset of users, groups, or devices (NOT related to subscription RBAC)
  - Example: HR admin can only reset HR users’ passwords; cannot manage Engineering accounts
  - An admin role (e.g., Password Admin, User Admin) is assigned **within** an AU
  - Separate concept from Azure RBAC — RBAC controls resource access; AU controls Entra admin scope
- **Device Identities**: Azure AD Registered, Azure AD Joined, Hybrid Azure AD Joined
- **Device Settings**: Configure max device limit, require MFA for join

### 한국어
- **사용자 유형**: 클라우드 전용, 동기화(온프레미스 AD에서), 게스트(B2B)
- **그룹 유형**: 보안 그룹, Microsoft 365 그룹
- **할당 유형**: Assigned(수동), Dynamic User, Dynamic Device
- **일괄 작업**: CSV 기반 대량 생성/초대/삭제
- **B2B**: 외부 사용자를 게스트로 초대
- **관리 단위(Admin Units)**: Entra ID 관리 권한을 특정 사용자/그룹/디바이스 하위 범위로 제한 (Azure 구독 RBAC와는 싴각다른 개념)
  - 예: 인사팀 관리자가 인사팀 사용자의 비밀번호만 재설정 가능, 엔지니어링팀로는 불가
  - AU 내에서 역할(Password Admin, User Admin 등)을 할당
  - Azure RBAC는 리소스 접근 제어, Admin Units는 Entra 관리 작업 범위 제어
- **장치 ID**: Azure AD 등록, Azure AD 조인, 하이브리드 Azure AD 조인
- **장치 설정**: 최대 장치 수 제한, 조인 시 MFA 요구 설정

---

## 2. SSPR (Self-Service Password Reset)

### EN
- **Authentication Methods** (choose up to 6):
  - Mobile app notification
  - Mobile app code (TOTP)
  - Email
  - Mobile phone (SMS)
  - Office phone
  - Security questions
- **Minimum required**: 2 methods by default; admin accounts always require 2
- **Password Writeback**: Requires Azure AD Premium P1 or P2 license; syncs new password back to on-prem AD
- **Scope**: Can be enabled for All users or a selected group (None by default)

### 한국어
- **인증 방법** (최대 6개 중 선택):
  - 모바일 앱 알림
  - 모바일 앱 코드(TOTP)
  - 이메일
  - 모바일 전화(SMS)
  - 회사 전화
  - 보안 질문
- **최소 요구 메서드**: 기본값 2개; 관리자 계정은 항상 2개 필요
- **비밀번호 쓰기 저장(Password Writeback)**: Azure AD Premium P1 또는 P2 라이선스 필요; 새 비밀번호를 온프레미스 AD로 다시 동기화
- **적용 범위**: 모든 사용자 또는 선택된 그룹(기본값: 없음)

---

## 3. MFA (Multi-Factor Authentication)

### EN
- **Enable methods**:
  - Per-user (legacy): Enables MFA per individual account regardless of conditions
  - Conditional Access Policy (recommended): Apply MFA based on configurable conditions
- **Conditional Access — Grant Controls (what the user must satisfy to get access)**:
  - Require MFA
  - Require compliant device (Intune-enrolled)
  - Require Hybrid Azure AD joined device
  - Require approved client app
  - Require app protection policy
  - Require password change
- **Conditional Access — Conditions (when the policy fires)**:
  - User/group/role, location (named location or country), device platform, client app, sign-in risk, user risk
- **Verification Methods**: Phone call, SMS, Authenticator app notification, TOTP code
- **Fraud Alert**: Users can report suspicious MFA prompts; admins can block accounts
- **One-Time Bypass**: Available only with MFA Server (on-prem); bypasses MFA for a set time
- **Trusted IPs**: IPv4 only; skips MFA for requests from corporate intranet IP ranges
- **Remember MFA**: Trust a device for N days so MFA isn't re-prompted

### 한국어
- **활성화 방법**:
  - 사용자별(레거시): 조건 없이 개별 계정에 항상 MFA 설정
  - 조건부 액세스 정책(권장): 조건에 따라 MFA 적용
- **조건부 액세스 — 부여 제어(Grant Controls, 접근 허용 조건)**:
  - MFA 필요
  - 규격 준수 디바이스 필요 (Intune 등록)
  - 하이브리드 Azure AD 조인 장치 필요
  - 승인된 클라이언트 앱 필요
  - 앱 보호 정책 필요
  - 비밀번호 변경 필요
- **조건부 액세스 — 조건(언제 정책이 발동되는가)**:
  - 사용자/그룹/역할, 위치(명명된 위치 또는 국가), 디바이스 플랫폼, 클라이언트 앱, 로그인 위험도, 사용자 위험도
- **인증 방법**: 전화 통화, SMS, Authenticator 앱 알림, TOTP 코드
- **사기 경보(Fraud Alert)**: 사용자가 의심스러운 MFA 프롬프트 신고 가능; 관리자가 계정 차단 가능
- **일회성 우회**: MFA Server(온프레미스)에서만 사용 가능; 설정 시간 동안 MFA 우회
- **신뢰할 수 있는 IP**: IPv4만 지원; 회사 인트라넷 IP 범위에서의 요청에 대해 MFA 생략
- **MFA 기억**: 특정 장치를 N일간 신뢰하여 MFA 재요청 방지

---

## 4. RBAC (Role-Based Access Control)

### EN
- **Built-in Roles** (key ones):
  - Owner: Full access + assign roles
  - Contributor: Full access, cannot assign roles
  - Reader: Read-only access
  - User Access Administrator: Manage user access only
  - VM Contributor: Manage VMs but not the network/storage they use
- **Custom Roles**: Define custom action/notAction permissions in JSON; scoped per subscription
- **Assignment**: Role → Security Principal → Scope (management group / subscription / resource group / resource)
- **Deny Assignments**: Block specific actions even if a role allows them (set by Azure Blueprints)
- **Multiple Directories**: A subscription can only trust one Azure AD directory; admin can switch directories

### 한국어
- **기본 제공 역할** (주요 역할):
  - Owner: 전체 액세스 + 역할 할당 가능
  - Contributor: 전체 액세스, 역할 할당 불가
  - Reader: 읽기 전용 액세스
  - User Access Administrator: 사용자 액세스만 관리
  - VM Contributor: VM 관리 가능, 연결된 네트워크/스토리지는 관리 불가
- **사용자 지정 역할**: JSON으로 actions/notActions 권한 정의; 구독별 범위 지정
- **할당**: 역할 → 보안 주체 → 범위(관리 그룹/구독/리소스 그룹/리소스)
- **거부 할당**: 역할이 허용해도 특정 작업을 차단(Azure Blueprints에서 설정)
- **다중 디렉터리**: 구독은 하나의 Azure AD 디렉터리만 신뢰 가능; 관리자가 디렉터리 전환 가능

---

## 5. Azure Governance

### EN

#### Subscriptions
- Billing boundary and access control boundary
- Multiple subscriptions can exist under one account

#### Management Groups
- Up to 6 levels deep (excluding root)
- Root Management Group contains all subscriptions
- Policies and RBAC set at higher level are inherited downward

#### Azure Policy
- **Policy Definition**: A rule that evaluates compliance (e.g., "VMs must use managed disks")
- **Initiative Definition**: A collection of policy definitions (policy set)
- **Assignment**: Attach policy/initiative to a scope; non-compliant resources are flagged
- Policies do not enforce retroactively (only audit/deny new deployments by default)

#### Resource Locks
- **Delete lock**: Prevents deletion; modifications are allowed
- **ReadOnly lock**: Prevents deletion AND modification
- **Control plane vs data plane**:
  - Control plane = ARM management operations (create/delete/update resource settings)
  - Data plane = actual data operations (read/write blob content, query DB, send queue messages)
  - Locks apply to control plane only — a ReadOnly-locked storage account still allows blob data writes
  - Exam trap: "Locked" does not mean "data is also read-only"
- Inheritance: Locks on parent resource group apply to all children
- Who can create/delete locks: Owner and User Access Administrator roles

#### Resource Tagging
- Name + value pairs applied to resources
- NOT automatically inherited by child resources
- Used for cost tracking, automation, governance
- Max 50 tags per resource

#### Azure Cost Management
- **Cost Analysis**: Visual spending breakdowns by resource, tag, time period
- **Budgets**: Set spending alerts at subscription or resource group level
- **Recommendations**: Azure Advisor cost optimization suggestions
- **Cost Savings**: Reserved Instances (up to 72% off), Azure Hybrid Benefit, Azure Credits

### 한국어

#### 구독
- 청구 경계 및 액세스 제어 경계
- 하나의 계정 아래 여러 구독 가능

#### 관리 그룹
- 루트 제외 최대 6단계 깊이
- 루트 관리 그룹이 모든 구독 포함
- 상위 레벨의 정책 및 RBAC는 하위로 상속됨

#### Azure 정책(Policy)
- **정책 정의**: 준수 여부를 평가하는 규칙 (예: "VM은 관리형 디스크를 사용해야 함")
- **이니셔티브 정의**: 정책 정의 모음(정책 집합)
- **할당**: 범위에 정책/이니셔티브 연결; 미준수 리소스는 표시됨
- 기본적으로 기존 리소스에 소급 적용되지 않음(새 배포에 대해 감사/거부만)

#### 리소스 잠금
- **삭제 잠금**: 삭제 방지; 수정은 허용
- **읽기 전용 잠금**: 삭제 및 수정 모두 방지
- **제어 평면 vs 데이터 평면**:
  - 제어 평면 = ARM 관리 작업(리소스 생성/삭제/설정 변경)
  - 데이터 평면 = 실제 데이터 접근(블록 읽기/쓰기, DB 쿼리, 큐 메시지 전송)
  - 잠금은 제어 평면에만 적용 — ReadOnly 잠금된 스토리지 계정도 블록 데이터 쓰기는 가능
  - 시험 함정: "잠금" ≠ "데이터도 읽기 전용"
- 상속: 상위 리소스 그룹의 잠금은 모든 하위 리소스에 적용
- 잠금 생성/삭제 권한: Owner 및 User Access Administrator 역할

#### 리소스 태그
- 리소스에 적용되는 이름+값 쌍
- 하위 리소스에 자동으로 상속되지 않음
- 비용 추적, 자동화, 거버넌스에 활용
- 리소스당 최대 50개 태그

#### Azure Cost Management
- **비용 분석**: 리소스, 태그, 기간별 지출 시각화
- **예산**: 구독 또는 리소스 그룹 수준에서 지출 경보 설정
- **권장 사항**: Azure Advisor 비용 최적화 제안
- **비용 절감**: Reserved Instances(최대 72% 할인), Azure 하이브리드 혜택, Azure 크레딧

---

## 6. Azure Storage

### EN

#### Storage Account Types
- **StorageV2 (General Purpose v2)**: Recommended; supports all storage services
- **Performance Tiers**: Standard (HDD) and Premium (SSD)

#### Replication Options
| Option | Description |
|--------|-------------|
| LRS | Locally Redundant Storage – 3 copies in 1 datacenter |
| ZRS | Zone Redundant Storage – 3 copies across 3 availability zones |
| GRS | Geo-Redundant Storage – LRS + 3 copies in secondary region |
| GZRS | Geo-Zone Redundant Storage – ZRS + GRS |
| RA-GRS | GRS + read access to secondary region |
| RA-GZRS | GZRS + read access to secondary region |

#### Shared Access Signatures (SAS)
- **User Delegation SAS**: Signed with Azure AD credentials (most secure)
- **Service SAS**: Access one storage service only
- **Account SAS**: Access multiple services; signed with account key
- SAS tokens define: allowed resources, permissions, expiry, allowed IPs, protocol

#### Access Keys
- 2 keys per storage account (for zero-downtime rotation)
- Grants full access to the storage account — treat like passwords

#### Stored Access Policies
- Server-side policy attached to a container/queue/table/share
- Can be used to revoke SAS tokens without changing the key

#### Azure Blob Storage Tiers
- **Hot**: Frequently accessed data; lower access cost, higher storage cost
- **Cool**: Infrequently accessed; minimum 30-day storage
- **Archive**: Rarely accessed; minimum 180-day storage; must rehydrate before reading

#### Azure Files
- SMB (445) or NFS (2049) file shares in the cloud
- Mount as a network drive on Windows/Linux/macOS
- Azure File Sync: Sync on-prem Windows Server file shares with Azure Files
- For **snapshot-based Azure Files backups**, you can do **item-level restore** with **File Recovery**
- Exam pattern: "restore only deleted files to the original location" → **File Recovery + Original Location**

#### Queue Storage and Table Storage (Exam Scope)
- **Queue Storage**: Durable message queue for asynchronous workloads
  - Message size up to 64 KB; supports visibility timeout and poison-message handling patterns
  - Typical use: decouple producer/consumer components
- **Table Storage**: NoSQL key-value store for semi-structured data
  - Uses `PartitionKey` + `RowKey` for scalability and lookup
  - Not relational; no joins/foreign keys
- Exam tip: AZ-104 focuses more on account/network/security/access patterns, but you should still know Queue/Table service purpose and basic access model

#### Data Import/Export
- **Azure Import/Export Service**: Ship HDDs to/from Azure datacenter
- **Azure Data Box**: 80 TB device; Microsoft ships it to you; offline transfer

### 한국어

#### 스토리지 계정 유형
- **StorageV2(범용 v2)**: 권장; 모든 스토리지 서비스 지원
- **성능 계층**: 표준(HDD), 프리미엄(SSD)

#### 복제 옵션
| 옵션 | 설명 |
|------|------|
| LRS | 로컬 중복 스토리지 – 1개 데이터 센터에 3개 복사본 |
| ZRS | 영역 중복 스토리지 – 3개 가용성 영역에 복사본 분산 |
| GRS | 지역 중복 스토리지 – LRS + 보조 지역에 3개 복사본 |
| GZRS | 지역-영역 중복 스토리지 – ZRS + GRS |
| RA-GRS | GRS + 보조 지역 읽기 액세스 |
| RA-GZRS | GZRS + 보조 지역 읽기 액세스 |

#### SAS(공유 액세스 서명)
- **사용자 위임 SAS**: Azure AD 자격 증명으로 서명(가장 안전)
- **서비스 SAS**: 하나의 스토리지 서비스만 액세스
- **계정 SAS**: 여러 서비스 액세스; 계정 키로 서명
- SAS 토큰: 허용된 리소스, 권한, 만료, 허용 IP, 프로토콜 정의

#### 액세스 키
- 스토리지 계정당 2개 키(무중단 순환을 위해)
- 스토리지 계정에 전체 액세스 부여 — 비밀번호처럼 취급

#### 저장된 액세스 정책
- 컨테이너/큐/테이블/공유에 연결된 서버 측 정책
- 키를 변경하지 않고 SAS 토큰 취소 가능
- 시험 관점: 중앙 정책으로 수명/권한 제어가 필요하면 서비스 SAS + 저장된 액세스 정책 패턴을 우선 검토

#### Azure Blob Storage 계층
- **핫(Hot)**: 자주 접근하는 데이터; 낮은 액세스 비용, 높은 스토리지 비용
- **쿨(Cool)**: 비정기 접근; 최소 30일 스토리지
- **아카이브(Archive)**: 드물게 접근; 최소 180일 스토리지; 읽기 전 리하이드레이션 필요

#### Azure Files
- 클라우드의 SMB(445) 또는 NFS(2049) 파일 공유
- Windows/Linux/macOS에서 네트워크 드라이브로 마운트
- Azure File Sync: 온프레미스 Windows Server 파일 공유를 Azure Files와 동기화
- **스냅샷 기반 Azure Files 백업**은 **파일 단위(item-level) 복원** 지원
- 시험 패턴: "삭제된 파일만 원래 위치로 복원" → **File Recovery + Original Location**

#### Queue Storage / Table Storage (시험 범위)
- **Queue Storage**: 비동기 워크로드용 내구성 메시지 큐
  - 메시지 크기 최대 64KB, visibility timeout 및 poison message 처리 패턴 지원
  - 대표 용도: 생산자/소비자 컴포넌트 분리(디커플링)
- **Table Storage**: 반정형 데이터용 NoSQL Key-Value 저장소
  - `PartitionKey` + `RowKey` 조합으로 확장성과 조회 성능 확보
  - 관계형 DB가 아니므로 조인/외래키 개념 없음
- 시험 팁: AZ-104는 계정/네트워크/보안/접근 제어 비중이 더 크지만, Queue/Table의 목적과 기본 접근 모델은 알아두는 것이 안전

#### 데이터 가져오기/내보내기
- **Azure Import/Export 서비스**: Azure 데이터 센터로/에서 HDD 발송
- **Azure Data Box**: 80TB 장치; Microsoft가 배송; 오프라인 전송

---

## 7. Virtual Machines (VMs)

### EN

#### Deployment Options
- Availability Sets, Availability Zones, or no redundancy
- Disk types: Premium SSD, Standard SSD, Standard HDD, Ultra Disk
- NSG can be assigned at the NIC level or subnet level at deployment time

#### Key Post-Deployment Tasks
- **Add Data Disk**: Attach managed disks; can be Standard or Premium
- **Disk Encryption**:
  - Platform-managed keys (default, no config required)
  - Customer-managed keys: Requires Azure Key Vault + Disk Encryption Set in same region & subscription
  - Double encryption: Platform + Customer keys combined
- **Set Static IP**:
  - Private IP: Go to NIC → IP Configurations → change from Dynamic to Static
  - Public IP: Go to Public IP resource → Configuration → Static (may reboot VM)
- **Add NIC**: VM must be stopped/deallocated first; NIC must be in same region
- **DNS Label**: Add to Public IP resource for a user-friendly hostname

#### VM Scale Sets
- Pool of identical VMs behind a load balancer
- Autoscaling based on CPU/memory/network metrics
- Scale Out: Add VMs when CPU threshold exceeded for a duration
- Scale In Policies: Default (highest instance ID), Newest VM, Oldest VM

#### VHD/Snapshot-Based Deployment
1. Take snapshot of existing VM's OS disk
2. Create managed disk from snapshot
3. Create new VM using "Create VM" from that managed disk

### 한국어

#### 배포 옵션
- 가용성 집합, 가용성 영역 또는 중복 없음
- 디스크 유형: 프리미엄 SSD, 표준 SSD, 표준 HDD, Ultra Disk
- NSG는 배포 시 NIC 수준 또는 서브넷 수준에 할당 가능

#### 주요 배포 후 작업
- **데이터 디스크 추가**: 관리형 디스크 연결; 표준 또는 프리미엄
- **디스크 암호화**:
  - 플랫폼 관리 키(기본값, 별도 구성 불필요)
  - 고객 관리 키: 동일 지역 및 구독에 Azure Key Vault + 디스크 암호화 세트 필요
  - 이중 암호화: 플랫폼 + 고객 키 결합
- **정적 IP 설정**:
  - 프라이빗 IP: NIC → IP 구성 → 동적에서 정적으로 변경
  - 공용 IP: 공용 IP 리소스 → 구성 → 정적(VM 재시작될 수 있음)
- **NIC 추가**: VM을 먼저 중지/할당 해제해야 함; NIC는 동일 지역에 있어야 함
- **DNS 레이블**: 공용 IP 리소스에 추가하여 친숙한 호스트 이름 사용 가능

#### VM 확장 집합(Scale Sets)
- 로드 밸런서 뒤의 동일한 VM 풀
- CPU/메모리/네트워크 메트릭을 기반으로 자동 크기 조정
- 스케일 아웃: CPU 임계값이 일정 시간 초과 시 VM 추가
- 스케일 인 정책: 기본값(가장 높은 인스턴스 ID), 최신 VM, 가장 오래된 VM

#### VHD/스냅샷 기반 배포
1. 기존 VM의 OS 디스크 스냅샷 촬영
2. 스냅샷에서 관리형 디스크 생성
3. 해당 관리형 디스크에서 "VM 만들기"로 새 VM 생성

---

## 8. ARM Templates & Custom Script Extensions

### EN

#### ARM Templates
- JSON files defining Azure resource deployments
- Two files: `template.json` (resource schema) + `parameters.json` (values)
- Deploy via: Azure Portal (Template deployment) → "Build your own template in editor" → Load file
- Templates downloadable during VM deployment from the portal

#### Custom Script Extensions
- Download and execute scripts on Azure VMs post-deployment
- Script source: Azure Blob Storage, GitHub, or uploaded at runtime
- Common use: Install software, configure settings (e.g., install IIS via PowerShell)
- PowerShell example: `Install-WindowsFeature -name Web-Server -IncludeManagementTools`
- Can be triggered from: Portal (VM → Extensions → Custom Script Extension), ARM templates, CLI, PowerShell

### 한국어

#### ARM 템플릿
- Azure 리소스 배포를 정의하는 JSON 파일
- 두 파일: `template.json`(리소스 스키마) + `parameters.json`(값)
- 배포 방법: Azure Portal(템플릿 배포) → "편집기에서 직접 빌드" → 파일 로드
- VM 배포 중 포털에서 템플릿 다운로드 가능

#### 사용자 지정 스크립트 확장
- 배포 후 Azure VM에서 스크립트를 다운로드하고 실행
- 스크립트 소스: Azure Blob Storage, GitHub 또는 런타임에 업로드
- 일반적 용도: 소프트웨어 설치, 설정 구성 (예: PowerShell로 IIS 설치)
- PowerShell 예시: `Install-WindowsFeature -name Web-Server -IncludeManagementTools`
- 트리거 방법: 포털(VM → 확장 → 사용자 지정 스크립트 확장), ARM 템플릿, CLI, PowerShell

---

## 9. Virtual Networking

### EN

#### Virtual Networks (vNets)
- CIDR address space (e.g., `192.168.0.0/16`)
- Must create at least one subnet; subnet address range must fall within vNet address space
- **DDoS Protection**: Basic (free, built-in) or Standard (paid, enhanced)
- **Service Endpoints**: Extends vNet private address to Azure services (e.g., Storage, SQL)
- **Azure Firewall**: Cloud-based network security service (alternative to NSG for advanced scenarios)

#### Service Endpoint vs Private Endpoint
| Item | Service Endpoint | Private Endpoint |
|------|------------------|------------------|
| Target service IP | Public endpoint remains | Private IP in your subnet |
| Data path | Microsoft backbone | Private Link (private IP path) |
| DNS requirement | Usually minimal change | Private DNS integration usually required |
| Isolation level | Service firewall limited to subnet/vNet | Service can be exposed privately only |
| Typical exam clue | "No private IP assignment" | "Must use private IP / disable public access" |

#### Azure Firewall vs NSG (Relationship)
- **NSG**: L3/L4 allow/deny rules on subnet/NIC; distributed filtering, low cost, no centralized threat intel
- **Azure Firewall**: Managed, centralized firewall service with DNAT/SNAT, FQDN filtering, threat intelligence modes, logging integration
- They are **complementary**, not mutually exclusive:
  - NSG for workload-level segmentation
  - Azure Firewall for centralized egress/ingress control and advanced policy

#### NAT Gateway vs Azure Firewall vs Load Balancer
| Service | Layer | Primary purpose | Traffic direction | Exam clue |
|---------|-------|-----------------|-------------------|-----------|
| NAT Gateway | SNAT (egress only) | Stable outbound public IP for private resources | Outbound only | "Private subnet needs fixed outbound internet IP" |
| Azure Firewall | L3-L7 managed firewall | Centralized filtering, DNAT/SNAT, threat intel, FQDN rules | Inbound + outbound + east-west | "Need centralized policy and logging" |
| Azure Load Balancer | L4 (TCP/UDP) | Distribute inbound/outbound flows across backend instances | Primarily inbound distribution; outbound via outbound rules | "Port-level TCP/UDP balancing" |

- Quick rule:
  - Outbound internet identity only -> NAT Gateway
  - Security inspection/policy enforcement -> Azure Firewall
  - High-scale L4 traffic distribution -> Azure Load Balancer

#### Subnets
- Subdivisions of a vNet
- **Gateway Subnet**: Dedicated subnet for virtual network gateway resources
  - Used by VPN Gateway and ExpressRoute Gateway scenarios (not VPN-only)
  - Name must be exactly `GatewaySubnet`; recommend /27 or /28 for growth
  - Do not place regular workloads in this subnet

#### Subnet vs NIC Boundaries (Exam Trap)
- Subnet is network segment boundary (address range, route table association, subnet-level NSG)
- NIC is per-VM interface boundary (IP configuration, NIC-level NSG, effective routes/rules view)
- If NSG exists on both subnet and NIC, traffic is evaluated against both effective rule sets

#### ASG (Application Security Group) Concept
- Logical grouping of VM NICs for NSG rule simplification
- Instead of IP-based NSG rules, you can reference source/destination ASGs (e.g., `Web-ASG -> App-ASG on 443`)
- Helps reduce rule sprawl and improves readability

#### Network Security Groups (NSGs)
- Applied to subnets or NICs (or both)
- **Default inbound rules**:
  - Allow VNet inbound (any ↔ any within vNet)
  - Allow Azure Load Balancer inbound
  - Deny All inbound
- **Default outbound rules**:
  - Allow VNet outbound
  - Allow Internet outbound
  - Deny All outbound
- **Custom rules**: Source IP, destination IP, port, protocol, allow/deny, priority (lower = higher priority)
- **Effective Security Rules**: View the combined result of all NSG rules on a NIC via NSG → Support + troubleshooting

#### IP Addresses
- **Private IP**: Assigned from subnet; Dynamic (DHCP-like) or Static
- **Public IP**: Basic or Standard SKU; Dynamic or Static; Standard is required for Standard Load Balancer
- Set static in: NIC → IP Configurations (private) or Public IP resource → Configuration (public)

#### vNet Peering
- Connect two vNets so they appear as one (traffic stays on Microsoft backbone)
- **Regional Peering**: Same Azure region
- **Global Peering**: Different Azure regions
- Peering must be configured on **both** vNets (bidirectional configuration required)
- No downtime required; no gateways needed

### 한국어

#### 가상 네트워크(vNet)
- CIDR 주소 공간 (예: `192.168.0.0/16`)
- 최소 하나의 서브넷 생성 필요; 서브넷 주소 범위는 vNet 주소 공간 내에 있어야 함
- **DDoS 보호**: 기본(무료, 기본 제공) 또는 표준(유료, 강화)
- **서비스 엔드포인트**: vNet 개인 주소를 Azure 서비스(예: Storage, SQL)로 확장
- **Azure Firewall**: 클라우드 기반 네트워크 보안 서비스(고급 시나리오에서 NSG 대안)

#### Service Endpoint vs Private Endpoint
| 항목 | Service Endpoint | Private Endpoint |
|------|------------------|------------------|
| 대상 서비스 IP | 퍼블릭 엔드포인트 유지 | 서브넷 내 프라이빗 IP 할당 |
| 데이터 경로 | Microsoft 백본 | Private Link(사설 IP 경로) |
| DNS 요구 | 변경이 상대적으로 적음 | Private DNS 연계가 보통 필요 |
| 격리 수준 | 서비스 방화벽을 vNet/서브넷으로 제한 | 서비스 자체를 사설로만 노출 가능 |
| 시험 단서 | "프라이빗 IP 할당 없이" | "반드시 사설 IP/퍼블릭 차단" |

#### Azure Firewall vs NSG 관계
- **NSG**: 서브넷/NIC에 적용하는 L3/L4 허용/거부 규칙
- **Azure Firewall**: 중앙집중형 관리형 방화벽(DNAT/SNAT, FQDN 필터링, 위협 인텔리전스 모드, 로그 통합)
- 둘은 대체재가 아니라 **상호 보완** 관계:
  - NSG: 워크로드 단위 세분화
  - Azure Firewall: 중앙 egress/ingress 통제와 고급 정책

#### NAT Gateway, Azure Firewall, Azure Load Balancer 비교
| 서비스 | 계층 | 주요 역할 | 트래픽 방향 | 시험 단서 |
|--------|------|-----------|-------------|-----------|
| NAT Gateway | SNAT(아웃바운드 전용) | 프라이빗 리소스의 고정 아웃바운드 공인 IP 제공 | 아웃바운드 전용 | "프라이빗 서브넷에서 고정 아웃바운드 IP 필요" |
| Azure Firewall | L3-L7 관리형 방화벽 | 중앙 정책 기반 필터링, DNAT/SNAT, 위협 인텔리전스, FQDN 규칙 | 인바운드 + 아웃바운드 + 동서(East-West) | "중앙 보안 정책/로깅 필요" |
| Azure Load Balancer | L4(TCP/UDP) | 백엔드 인스턴스에 트래픽 분산 | 주로 인바운드 분산, 아웃바운드는 outbound rule 활용 | "포트 단위 TCP/UDP 분산" |

- 빠른 선택 기준:
  - 아웃바운드 IP 고정만 필요 -> NAT Gateway
  - 보안 검사/정책 강제 필요 -> Azure Firewall
  - 대규모 L4 트래픽 분산 필요 -> Azure Load Balancer

#### 서브넷
- vNet의 하위 구분
- **게이트웨이 서브넷**: 가상 네트워크 게이트웨이 전용 서브넷
  - VPN Gateway와 ExpressRoute Gateway 시나리오에서 사용(VPN 전용 아님)
  - 이름은 반드시 `GatewaySubnet`; 확장 고려 시 /27 또는 /28 권장
  - 일반 VM/워크로드 배치 금지

#### Subnet vs NIC 경계(시험 함정)
- Subnet: 주소 범위, 라우트 테이블 연결, 서브넷 수준 NSG가 적용되는 경계
- NIC: VM별 인터페이스 경계(IP 구성, NIC 수준 NSG, 유효한 규칙/경로 확인)
- 서브넷 NSG와 NIC NSG가 동시에 있으면 둘 다 평가된 유효 규칙 결과가 적용됨

#### ASG(Application Security Group) 개념
- VM NIC를 논리 그룹으로 묶어 NSG 규칙을 단순화
- IP 직접 지정 대신 ASG를 원본/대상으로 참조 가능(예: `Web-ASG -> App-ASG : 443`)
- 규칙 수 증가/가독성 저하를 줄이는 데 유리

#### 네트워크 보안 그룹(NSG)
- 서브넷 또는 NIC(또는 둘 다)에 적용
- **기본 인바운드 규칙**:
  - VNet 인바운드 허용(vNet 내 any ↔ any)
  - Azure Load Balancer 인바운드 허용
  - 모든 인바운드 거부
- **기본 아웃바운드 규칙**:
  - VNet 아웃바운드 허용
  - 인터넷 아웃바운드 허용
  - 모든 아웃바운드 거부
- **사용자 지정 규칙**: 원본 IP, 대상 IP, 포트, 프로토콜, 허용/거부, 우선순위(낮을수록 높은 우선순위)
- **유효한 보안 규칙**: NSG → 지원 + 문제 해결에서 NIC에 적용된 모든 NSG 규칙의 결합 결과 확인

#### IP 주소
- **프라이빗 IP**: 서브넷에서 할당; 동적(DHCP 방식) 또는 정적
- **공용 IP**: 기본 또는 표준 SKU; 동적 또는 정적; 표준 SKU는 표준 Load Balancer에 필요
- 정적 설정: NIC → IP 구성(프라이빗) 또는 공용 IP 리소스 → 구성(공용)

#### vNet 피어링
- 두 vNet을 연결하여 하나처럼 표시(트래픽은 Microsoft 백본에서 유지)
- **지역 피어링**: 동일한 Azure 지역
- **글로벌 피어링**: 다른 Azure 지역
- 피어링은 **양쪽** vNet에 모두 설정해야 함(양방향 구성 필요)
- 가동 중지 불필요; 게이트웨이 불필요

---

## 10. Site-to-Site VPN

### EN

#### Components
| Component | Description |
|-----------|-------------|
| On-prem VPN device | Hardware/software VPN appliance at on-prem site |
| Gateway Subnet | Special /27 or /28 subnet in Azure vNet for VPN gateway VMs |
| Virtual Network Gateway | Azure-side VPN endpoint; route-based or policy-based; takes ~45 min to deploy |
| Local Network Gateway | Represents the remote side; holds the public IP and address space of the other end |
| VPN Connection | IPSec connection joining two VPN gateways using a shared key |

#### Setup Steps
1. Create **Gateway Subnet** in each vNet
2. Deploy **Virtual Network Gateway** in each vNet (route-based for vNet-to-vNet)
3. Create **Local Network Gateway** in each vNet (references the other side's gateway public IP + address space)
4. Create **VPN Connection** resource on each side with the same shared key
5. Verify status shows "Connected"

#### VPN Types
- **Route-based**: Supports multiple site-to-site connections, point-to-site, inter-vNet
- **Policy-based**: Supports only 1 site-to-site connection

#### ExpressRoute
- Private connection through a connectivity provider (not over public internet)
- Higher reliability, speed, lower latency, higher cost

### 한국어

#### 구성 요소
| 구성 요소 | 설명 |
|----------|------|
| 온프레미스 VPN 장치 | 온프레미스 사이트의 하드웨어/소프트웨어 VPN 어플라이언스 |
| 게이트웨이 서브넷 | VPN 게이트웨이 VM용 Azure vNet의 특수 /27 또는 /28 서브넷 |
| 가상 네트워크 게이트웨이 | Azure 측 VPN 엔드포인트; 경로 기반 또는 정책 기반; 배포에 약 45분 소요 |
| 로컬 네트워크 게이트웨이 | 원격 측을 나타냄; 상대방 게이트웨이의 공용 IP 및 주소 공간 보유 |
| VPN 연결 | 공유 키를 사용하여 두 VPN 게이트웨이를 연결하는 IPSec 연결 |

#### 설정 단계
1. 각 vNet에 **게이트웨이 서브넷** 생성
2. 각 vNet에 **가상 네트워크 게이트웨이** 배포(vNet 간 연결에는 경로 기반 사용)
3. 각 vNet에 **로컬 네트워크 게이트웨이** 생성(상대방 게이트웨이 공용 IP + 주소 공간 참조)
4. 동일한 공유 키로 각 측에 **VPN 연결** 리소스 생성
5. 상태가 "연결됨"으로 표시되는지 확인

#### VPN 유형
- **경로 기반**: 여러 사이트 간 연결, 지점 간 연결, vNet 간 연결 지원
- **정책 기반**: 사이트 간 연결 1개만 지원

#### ExpressRoute
- 연결 공급자를 통한 전용 연결(공용 인터넷 미사용)
- 높은 신뢰성, 속도, 낮은 대기 시간, 높은 비용

---

## 11. Azure DNS

### EN
- **Public DNS Zone**: Host DNS for an internet-facing domain in Azure
  - Create DNS zone → get 4 Azure name servers → update registrar (e.g., GoDaddy) to point to Azure NS
- **Private DNS Zone**: Name resolution within a vNet (no internet exposure)
- **Custom DNS on vNet**: Set in vNet → DNS Servers → Custom; point to on-prem DC for AD resolution
- DNS label on Public IP: Creates an A record like `<label>.<region>.cloudapp.azure.com`

### 한국어
- **공용 DNS 영역**: Azure에서 인터넷 도메인의 DNS 호스팅
  - DNS 영역 생성 → 4개의 Azure 네임서버 확인 → 레지스트라(예: GoDaddy)를 Azure NS로 업데이트
- **프라이빗 DNS 영역**: vNet 내 이름 확인(인터넷 노출 없음)
- **vNet의 사용자 지정 DNS**: vNet → DNS 서버 → 사용자 지정으로 설정; AD 확인을 위해 온프레미스 DC 지정
- 공용 IP의 DNS 레이블: `<레이블>.<지역>.cloudapp.azure.com`과 같은 A 레코드 생성

---

## 12. Availability Sets & Availability Zones

### EN

#### Availability Sets
- Logical grouping within a single datacenter
- **Fault Domains (FDs)**: VMs spread across different physical racks (different power/network)
- **Update Domains (UDs)**: VMs spread so Azure only reboots one UD at a time during maintenance
- SLA: 99.95% when 2+ VMs in an availability set
- No cost for the availability set itself

#### Availability Zones
- Physically separate zones within an Azure region
- 3 zones per supported region
- Each zone has its own power, network, and cooling
- SLA: 99.99% for VMs across 2+ zones
- Provides protection against datacenter failure (not just rack failure)

### 한국어

#### 가용성 집합
- 단일 데이터 센터 내의 논리적 그룹화
- **장애 도메인(FD)**: VM이 서로 다른 물리적 랙에 분산(전원/네트워크 분리)
- **업데이트 도메인(UD)**: Azure가 유지 관리 중 한 번에 하나의 UD만 재시작하도록 VM 분산
- SLA: 가용성 집합에 VM 2개 이상 시 99.95%
- 가용성 집합 자체는 무료

#### 가용성 영역
- Azure 지역 내의 물리적으로 분리된 영역
- 지원 지역당 3개 영역
- 각 영역은 자체 전원, 네트워크, 냉각 시스템 보유
- SLA: 2개 이상의 영역에 VM 배포 시 99.99%
- 데이터 센터 장애 방지(랙 장애뿐만 아니라)

---

## 13. Azure Load Balancers

### EN

#### SKUs
| Feature | Basic | Standard |
|---------|-------|----------|
| Backend pool size | Up to 300 | Up to 1000 |
| Health probes | HTTP, TCP | HTTP, HTTPS, TCP |
| Availability Zones | No | Yes |
| SLA | None | 99.99% |

#### Types
- **Public Load Balancer**: Maps public IP:port to VM private IP:port; internet-facing
- **Internal Load Balancer**: Routes traffic only within vNet or via VPN; no public IP

#### Configuration Components
1. **Frontend IP Configuration**: Public or private IP address clients connect to
2. **Backend Pool**: The VMs being load balanced (availability set, scale set, or individual VMs)
3. **Health Probe**: HTTP/HTTPS/TCP probe to check VM health; remove unhealthy VMs from rotation
4. **Load Balancing Rules**: Map frontend port to backend port; session persistence options
5. **Inbound NAT Rules**: Forward specific ports to specific VMs

#### Key Concepts
- **Session Persistence (Affinity)**: None (default), Client IP, Client IP + Protocol
- **Floating IP (Direct Server Return)**: Recommended only for SQL Always On
- **Zone Redundant**: Front-end IP survives zone failure
- Standard LB requires VMs to have either Standard Public IP or no public IP (not Basic)
- **Zone Redundant detail**:
  - The Standard Load Balancer frontend is distributed across multiple availability zones
  - If one zone fails, the frontend public IP remains reachable from healthy zones
  - This does not guarantee backend VM health; backend must also be zone-aware/redundant

#### Azure Traffic Distribution Services (Global vs Regional)
| Service | Operating model | Primary role | Scope |
|---------|------------------|--------------|-------|
| Traffic Manager | DNS-based routing | Distribute traffic across regions and endpoints | Global |
| Azure Front Door | HTTP/S (L7) anycast edge | Global web entry, acceleration, WAF, URL/path-based routing | Global |
| Application Gateway | HTTP/S (L7) regional reverse proxy | Regional web load balancing, WAF, host/path routing | Regional |
| Azure Load Balancer | Network (L4) packet-based | Regional TCP/UDP load balancing | Regional |

- Selection shorthand:
  - Cross-region endpoint selection -> Traffic Manager
  - Global web front door + edge/WAF -> Azure Front Door
  - Regional app-layer routing/WAF -> Application Gateway
  - Regional L4 balancing -> Azure Load Balancer

### 한국어

#### SKU
| 기능 | 기본 | 표준 |
|------|------|------|
| 백엔드 풀 크기 | 최대 300 | 최대 1000 |
| 상태 프로브 | HTTP, TCP | HTTP, HTTPS, TCP |
| 가용성 영역 | 없음 | 있음 |
| SLA | 없음 | 99.99% |

#### 유형
- **공용 Load Balancer**: 공용 IP:포트를 VM 프라이빗 IP:포트에 매핑; 인터넷 연결
- **내부 Load Balancer**: vNet 내 또는 VPN을 통해서만 트래픽 라우팅; 공용 IP 없음

#### 구성 구성 요소
1. **프런트엔드 IP 구성**: 클라이언트가 연결하는 공용 또는 프라이빗 IP 주소
2. **백엔드 풀**: 로드 밸런싱되는 VM(가용성 집합, 확장 집합 또는 개별 VM)
3. **상태 프로브**: VM 상태 확인을 위한 HTTP/HTTPS/TCP 프로브; 비정상 VM을 순환에서 제거
4. **부하 분산 규칙**: 프런트엔드 포트를 백엔드 포트에 매핑; 세션 지속성 옵션
5. **인바운드 NAT 규칙**: 특정 포트를 특정 VM으로 전달

#### 핵심 개념
- **세션 지속성(선호도)**: 없음(기본값), 클라이언트 IP, 클라이언트 IP + 프로토콜
- **부동 IP(직접 서버 반환)**: SQL Always On에만 권장
- **영역 중복**: 프런트엔드 IP가 영역 장애에도 유지됨
- 표준 LB는 VM에 표준 공용 IP 또는 공용 IP 없음 필요(기본 IP 불가)
- **Zone Redundant 의미 상세**:
  - Standard Load Balancer의 프런트엔드가 여러 가용성 영역에 분산 배치됨
  - 특정 영역 장애 시에도 정상 영역을 통해 동일 프런트엔드 공용 IP로 계속 진입 가능
  - 다만 백엔드 VM 자체가 단일 영역에만 있으면 서비스 전체 가용성은 제한될 수 있음

#### Azure 트래픽 분산 서비스 비교 (Global vs Regional)
| 서비스 이름 | 작동 방식 | 주요 역할 | 서비스 범위 |
|------------|-----------|-----------|-------------|
| Traffic Manager | DNS 기반 | 전 세계 Region 간 트래픽 분산 | 글로벌(Global) |
| Azure Front Door | HTTP/S (L7) 애니캐스트 | 전 세계 웹 보안 및 URL 기반 분산 | 글로벌(Global) |
| Application Gateway | HTTP/S (L7) 프록시 | 단일 지역 내 VM/컨테이너 분산, WAF | 지역(Regional) |
| Azure Load Balancer | 네트워크 (L4) 패킷 기반 | 단일 지역 내 TCP/UDP 포트 단위 분산 | 지역(Regional) |

- 선택 요약:
  - 리전 간 분산(DNS) -> Traffic Manager
  - 글로벌 웹 진입점/엣지 WAF -> Azure Front Door
  - 지역 내 L7 라우팅/WAF -> Application Gateway
  - 지역 내 L4 포트 분산 -> Azure Load Balancer

---

## 14. Network Watcher

### EN

#### Monitoring Tools
- **Topology**: Visual diagram of vNet resources and relationships
- **Connection Monitor**: Continuously monitors reachability, latency, topology changes between a source VM and an endpoint
- **Network Performance Monitor**: Hybrid network monitoring solution

#### Diagnostic Tools
- **IP Flow Verify**: Test if traffic is allowed/denied (direction, protocol, source/dest IP, port) by NSG
- **Next Hop**: Identify routing path for traffic from a source IP to a destination
- **Effective Security Rules**: View combined NSG rules applied to a NIC
- **VPN Troubleshoot**: Diagnose VPN gateway and connection health; requires a storage account for logs
- **Packet Capture**: Capture network traffic with time/size limits; store to Azure Storage or VM disk
- **Connection Troubleshoot**: Point-in-time TCP connectivity test between VM and another VM/FQDN/IP

#### Logs
- **NSG Flow Logs**: Log source/dest IP, port, protocol, and allow/deny per NSG rule
- **Diagnostic Logs**: Enable/disable diagnostic logs for network resources
- **Traffic Analytics**: Rich visualizations from NSG flow logs

#### Enabling Network Watcher
- Enabled per region; goes into a resource group called "NetworkWatcherRG"
- Monitor → Insights → Networks → Enable per region

### 한국어

#### 모니터링 도구
- **토폴로지**: vNet 리소스 및 관계의 시각적 다이어그램
- **연결 모니터**: 원본 VM과 엔드포인트 간의 도달 가능성, 대기 시간, 토폴로지 변경 지속 모니터링
- **네트워크 성능 모니터**: 하이브리드 네트워크 모니터링 솔루션

#### 진단 도구
- **IP 흐름 확인**: NSG에 의한 트래픽 허용/거부 테스트(방향, 프로토콜, 원본/대상 IP, 포트)
- **다음 홉**: 원본 IP에서 대상까지의 라우팅 경로 식별
- **유효한 보안 규칙**: NIC에 적용된 NSG 규칙 결합 확인
- **VPN 문제 해결**: VPN 게이트웨이 및 연결 상태 진단; 로그용 스토리지 계정 필요
- **패킷 캡처**: 시간/크기 제한으로 네트워크 트래픽 캡처; Azure Storage 또는 VM 디스크에 저장
- **연결 문제 해결**: VM과 다른 VM/FQDN/IP 간의 지점 간 TCP 연결 테스트

#### 로그
- **NSG 흐름 로그**: NSG 규칙별 원본/대상 IP, 포트, 프로토콜, 허용/거부 기록
- **진단 로그**: 네트워크 리소스의 진단 로그 활성화/비활성화
- **트래픽 분석**: NSG 흐름 로그의 풍부한 시각화

#### Network Watcher 활성화
- 지역별로 활성화; "NetworkWatcherRG"라는 리소스 그룹에 배치
- Monitor → Insights → Networks → 지역별 활성화

---

## 15. Azure Backup & Recovery Services

### EN

#### Recovery Services Vault
- Container for backup data and backup policies
- Must be in the **same region** as the VMs being backed up
- Geo-redundant storage by default (GRS)

#### Backup Policies
- Define: frequency (daily/weekly), time, timezone, retention period
- **Instant Restore**: Retains snapshots locally for 1-5 days for faster recovery
- Snapshot → transferred to vault (two-phase process)
- Supports disks up to 4 TB; Standard SSD and Premium SSD

#### Manual Backup
- From VM blade → Backup → select vault → Enable Backup
- Or from vault → Backup Items → trigger "Backup Now"

#### Restore Options
1. **Create New VM**: Restore from backup as a brand-new VM
2. **Restore Disks**: Restore disk only; use it to create a custom VM
3. **Replace Existing**: Replace disks on existing VM (VM must exist); Azure takes a pre-restore snapshot automatically

### 한국어

#### Recovery Services Vault (백업용)
- 백업 데이터 및 백업 정책의 컨테이너
- 백업할 VM과 **동일한 지역**에 있어야 함
- 기본적으로 지역 중복 스토리지(GRS) 사용

#### 백업 정책
- 정의: 빈도(매일/매주), 시간, 시간대, 보존 기간
- **즉시 복원**: 더 빠른 복구를 위해 스냅샷을 1-5일간 로컬 보관
- 스냅샷 → Vault으로 전송(2단계 프로세스)
- 최대 4TB 디스크 지원; 표준 SSD 및 프리미엄 SSD

#### 수동 백업
- VM 블레이드 → 백업 → Recovery Services Vault 선택 → 백업 활성화
- 또는 Recovery Services Vault → 백업 항목 → "지금 백업" 트리거

#### 복원 옵션
1. **새 VM 만들기**: 완전히 새로운 VM으로 백업에서 복원
2. **디스크 복원**: 디스크만 복원; 사용자 지정 VM 생성에 사용
3. **기존 항목 교체**: 기존 VM의 디스크 교체(VM이 존재해야 함); Azure가 복원 전 스냅샷 자동 촬영

---

## 16. Azure Site Recovery (ASR)

### EN
- Replicate VMs from one Azure region to another (or on-prem to Azure)
- **Recovery Services Vault**: Must be in a **different region** than the source VMs
- **Replication Policy**: Recovery point retention (default 24h); app-consistent snapshot frequency (default 4h)
- **Multi-VM Consistency**: Group VMs to replicate together (for multi-tier apps)
- **Test Failover**: Test the failover without affecting production; clean up afterward
- **Failover**: Shuts down source VM → syncs latest data → starts VM in target region
- **Failback**: After failover, can replicate back to original region

### 한국어
- Azure 지역 간 VM 복제 또는 온프레미스에서 Azure로 복제
- **Recovery Services Vault (ASR용)**: 원본 VM과 **다른 지역**에 있어야 함
- **복제 정책**: 복구 지점 보존(기본값 24시간); 앱 일관 스냅샷 빈도(기본값 4시간)
- **다중 VM 일관성**: 다중 계층 앱을 위해 VM을 함께 복제하도록 그룹화
- **테스트 장애 조치**: 프로덕션에 영향 없이 장애 조치 테스트; 이후 정리
- **장애 조치**: 원본 VM 종료 → 최신 데이터 동기화 → 대상 지역에서 VM 시작
- **장애 복구**: 장애 조치 후 원래 지역으로 다시 복제 가능

#### 용어/지역 규칙 주의
- Azure Backup: Recovery Services Vault는 **원본 VM과 같은 지역**
- Azure Site Recovery: Recovery Services Vault는 **복구(대상) 지역**
- 시험 함정: Backup 규칙과 ASR 규칙을 서로 바꿔 고르는 실수 주의

---

## 17. Azure Monitor & Diagnostics

### EN

#### Azure Monitor Overview
- Collect, analyze, and act on telemetry from Azure + on-prem
- Data sources: Application, Guest OS, Azure Resources, Subscription, Tenant

#### Metrics
- Explore Metrics: Azure Monitor → Explore Metrics → select resource, metric, aggregation, time range
- Pin charts to dashboard
- Metric types: CPU%, Disk Read/Write bytes, Network bytes sent/received, etc.

#### Alerts
- Create Alert Rule: Azure Monitor → Alerts → Create → select resource → add condition → add action group → name rule
- **Signal Types**: Platform (metrics) or Administration (Activity Log events)
- Alert fired → action group triggered → email/SMS/webhook/etc. sent
- **Action Groups**: Reusable notification configurations; max 2000 per subscription
  - Action Types: Email, SMS, Voice, Azure Function, Logic App, Webhook, ITSM, Automation Runbook
- **Action Rules**: Suppress alerts during planned maintenance windows

#### Diagnostic Settings
- Enable on individual resources via: Azure Monitor → Diagnostic settings → filter → Add diagnostic setting
- Destination: Archive to Storage Account, Stream to Event Hub, Send to Log Analytics
- Configure metrics and retention period (0 = infinite)

#### Log Analytics
- Query logs using KQL (Kusto Query Language)
- Workspace-based; resources send logs to workspace

### 한국어

#### Azure Monitor 개요
- Azure + 온프레미스의 원격 분석 데이터 수집, 분석 및 조치
- 데이터 소스: 애플리케이션, 게스트 OS, Azure 리소스, 구독, 테넌트

#### 메트릭
- 메트릭 탐색: Azure Monitor → 메트릭 탐색 → 리소스, 메트릭, 집계, 시간 범위 선택
- 차트를 대시보드에 고정
- 메트릭 유형: CPU%, 디스크 읽기/쓰기 바이트, 네트워크 송수신 바이트 등

#### 경보(Alerts)
- 경보 규칙 만들기: Azure Monitor → 경보 → 만들기 → 리소스 선택 → 조건 추가 → 작업 그룹 추가 → 규칙 이름 지정
- **신호 유형**: 플랫폼(메트릭) 또는 관리(활동 로그 이벤트)
- 경보 발생 → 작업 그룹 트리거 → 이메일/SMS/웹훅 등 전송
- **작업 그룹**: 재사용 가능한 알림 구성; 구독당 최대 2000개
  - 작업 유형: 이메일, SMS, 음성, Azure Function, Logic App, 웹훅, ITSM, Automation Runbook
- **작업 규칙**: 계획된 유지 관리 창 동안 경보 억제

#### 진단 설정
- 개별 리소스에서 활성화: Azure Monitor → 진단 설정 → 필터 → 진단 설정 추가
- 대상: 스토리지 계정에 보관, 이벤트 허브로 스트리밍, Log Analytics로 전송
- 메트릭 및 보존 기간 구성(0 = 무한)

#### Log Analytics
- KQL(Kusto Query Language)을 사용하여 로그 쿼리
- 워크스페이스 기반; 리소스에서 워크스페이스로 로그 전송

---

## Quick Reference: AZ-104 Exam Domains

| Domain | Weight | Key Topics |
|--------|--------|------------|
| Manage Azure identities and governance | ~20-25% | Entra ID, RBAC, Policy, Locks, Subscriptions |
| Implement and manage storage | ~15-20% | Storage accounts, Blob, Files, SAS, Import/Export |
| Deploy and manage Azure compute resources | ~20-25% | VMs, Scale Sets, ARM Templates, Containers |
| Configure and manage virtual networking | ~15-20% | vNet, NSG/ASG, Endpoints, Load Balancer, VPN, DNS, Peering |
| Monitor and maintain Azure resources | ~10-15% | Azure Monitor, Backup, Site Recovery, Alerts |

---

## Screenshot-Based Clarifications

### EN
- Policy Definition vs Initiative: A policy definition is one rule; an initiative groups multiple policy definitions and is assigned by scope (management group/subscription/resource group).
- Stored Access Policy: Useful when you need centralized control for SAS start/expiry/permissions and quick revocation behavior.
- Storage Redundancy: GRS/GZRS secondary is not read-enabled by default; read access requires RA-GRS or RA-GZRS.
- Storage Account Choice: Standard general-purpose v2 is the recommended default for Blob/Files/Queue/Table scenarios.

### 한국어
- Policy Definition과 Initiative 구분: Definition은 단일 규칙, Initiative는 여러 규칙 묶음이며 스코프(관리 그룹/구독/리소스 그룹)에 할당해 적용합니다.
- Stored Access Policy: SAS의 시작/만료/권한을 중앙에서 바꾸고 빠르게 통제해야 할 때 유용합니다.
- 스토리지 복제: GRS/GZRS는 보조 지역 읽기가 기본이 아니며, 읽기까지 필요하면 RA-GRS 또는 RA-GZRS를 사용합니다.
- 스토리지 계정 선택: 일반적으로 Standard GPv2를 기본 선택으로 보고, 워크로드에 따라 계층/복제를 조정합니다.

---

## 18. 2026 Scope Addendum (Gap Fill)

### EN

#### Containers in Azure Portal
- **Azure Container Registry (ACR)**: Private registry for container images
- **Azure Container Instances (ACI)**: Fast serverless container execution (per-second billing)
- **Azure Container Apps (ACA)**: Managed environment for microservices/jobs with scaling and revisions
- Exam focus: When to choose ACI vs ACA, and how ACR integrates with both

#### Azure App Service
**What is it?** App Service is Azure's fully managed PaaS platform for hosting web applications, REST APIs, and mobile backends. You deploy your code (or containers); Azure handles OS patching, scaling, load balancing, and TLS. Cost is based on the App Service Plan tier, not individual VM instances.

- **App Service Plan**: Defines the compute region, OS, pricing tier (Free/Shared/Basic/Standard/Premium), and scaling limits
  - Multiple apps can share one plan
  - Higher tiers unlock: custom domains, TLS, deployment slots, VNet integration
- **Deployment Slots**: Named staging environments within the same App Service
  - Each slot has its own URL (e.g., `myapp-staging.azurewebsites.net`)
  - Deploy to staging → test → **swap** to production with near-zero downtime
  - Slot-specific settings (sticky): certain app settings and connection strings stay with the slot even after swap
- **Custom Domains**: Map own domain; requires CNAME or A record + domain verification TXT record
- **TLS Certificates**: Upload or use App Service Managed Certificate (free)
- **Networking**:
  - Access restrictions: IP-based allow/deny list for inbound
  - VNet Integration: routes outbound traffic through a vNet (e.g., to reach on-prem or private resources)
  - Private Endpoint: gives App Service a private IP — accessible only inside vNet (inbound private access)
- **Backup/Restore**: Scheduled full backup of app code + config + DB to a Storage Account

#### Azure Bastion (Secure VM Admin Access)
- Browser-based RDP/SSH without exposing VM public IP
- Deployed into dedicated `AzureBastionSubnet` (name requirement)
- Exam clue: "Secure management without public IP on VM" usually points to Bastion

#### UDR (User-Defined Routes)
- Route table can force traffic to Virtual Appliance, VPN gateway, or internet
- Always validate with Effective Routes in Network Watcher when troubleshooting

#### Backup Vault vs Recovery Services Vault
- Recovery Services Vault is used for Azure Backup and Site Recovery scenarios in AZ-104 context
- Questions may test vault type selection and region constraints by scenario

### 한국어

#### 컨테이너(포털 중심)
- **ACR**: 컨테이너 이미지용 프라이빗 레지스트리
- **ACI**: 초고속 서버리스 컨테이너 실행(초 단위 과금)
- **ACA**: 마이크로서비스/잡 실행을 위한 관리형 플랫폼(스케일링/리비전)
- 시험 포인트: ACI vs ACA 선택 기준, ACR 연계 방식

#### Azure App Service
**정의**: Azure App Service는 웹 애플리케이션, REST API, 모바일 백엔드를 호스팅하는 완전 관리형 PaaS 플랫폼입니다. 코드(또는 컨테이너)만 배포하면 OS 패치, 스케일링, 로드 밸런싱, TLS를 Azure가 처리합니다. 비용은 App Service Plan 계층 기준입니다.

- **App Service Plan**: 컴퓨팅 지역, OS, 가격 계층(Free/Shared/Basic/Standard/Premium), 스케일 한도 정의
  - 여러 앱이 하나의 Plan을 공유 가능
  - 계층이 올라갈수록: 사용자 지정 도메인, TLS, 배포 슬롯, VNet 통합 사용 가능
- **Deployment Slots**: 같은 App Service 내 이름이 붙은 별도 실행 환경
  - 각 슬롯 고유 URL: `myapp-staging.azurewebsites.net`
  - staging 배포 → 테스트 → **swap** 실행 → 무중단 프로덕션 전환
  - Sticky 설정: 특정 앱 설정·연결 문자열은 swap 후에도 슬롯에 고정
- **사용자 지정 도메인**: CNAME/A 레코드 + 도메인 소유 확인 TXT 레코드 필요
- **TLS 인증서**: 업로드 또는 App Service Managed Certificate(무료) 사용
- **네트워킹**:
  - Access Restrictions: IP 기반 인바운드 허용/거부
  - VNet Integration: 아웃바운드 트래픽을 vNet 통해 라우팅
  - Private Endpoint: App Service에 사설 IP 부여 (vNet 내부에서만 인바운드 접근)
- **백업/복원**: 앱 코드+설정+DB를 Storage Account에 정기 전체 백업

#### Azure Bastion (VM 안전 접속)
- VM 공용 IP 없이 브라우저 기반 RDP/SSH 제공
- 전용 `AzureBastionSubnet`에 배포(이름 요구)
- 시험 단서: "공용 IP 없이 안전한 관리 접속"이면 Bastion 가능성이 높음

#### UDR(User-Defined Route)
- 라우트 테이블로 트래픽을 NVA/VPN 게이트웨이/인터넷으로 강제 라우팅 가능
- 트러블슈팅 시 Network Watcher의 Effective Routes로 최종 경로 확인

#### Backup Vault vs Recovery Services Vault
- AZ-104 맥락에서는 Azure Backup/ASR 시나리오에서 Recovery Services Vault 이해가 핵심
- 문제는 시나리오별 Vault 선택/지역 제약을 섞어 물어보는 경우가 많음

---

## Section 19 — 2026 Gap Fill: Set4 Topics

### English

#### LB HA Ports Rule
- A single load balancing rule with `Protocol: All` and `Port: 0` — forwards **all** TCP/UDP ports to backend
- Only available on **Standard Load Balancer** with an internal frontend
- Use case: NVAs (Network Virtual Appliances) where you cannot predict which ports will be used
- Removes need for separate rules per port

#### Azure Firewall FQDN Tags
- Pre-built FQDN groupings maintained by Microsoft (e.g., `WindowsUpdate`, `AzureKubernetesService`)
- Use in **Application rules** to allow traffic to a complex set of FQDNs without listing each one
- Microsoft keeps the FQDN list up-to-date — no manual maintenance needed
- Exam clue: "allow Windows Update traffic through Azure Firewall" → use FQDN Tag `WindowsUpdate`

#### Azure File Sync — Cloud Tiering
- Files not recently accessed are **tiered** (replaced by a pointer stub on the server) to Azure Files
- Frees local disk space while keeping the namespace visible on-premises
- Tiering policy: by **volume free space** (keep X% free) or **date** (files not accessed in N days)
- Recalled automatically when a user opens the stub file
- **Cloud tiering is per server endpoint**, not per sync group

#### VM Extension — Azure Platform IP (168.63.129.16)
- `168.63.129.16` is Azure's non-routable virtual IP used by the VM Agent and platform services
- Used for: VM Agent heartbeat, extension status reporting, custom script execution, health probes
- NSG rules must allow outbound to `168.63.129.16` (or tag `AzurePlatform`) for extensions to work correctly
- It is a **host-level IP** — the VM communicates with the Azure host via this address

#### SQL Database — Point-in-Time Restore (PITR)
- Azure SQL Database automatically retains transaction log backups
- PITR retention: 1–35 days (default 7 days; configurable per database)
- Restores to a **new database** — does not overwrite the source database
- RPO: approximately 5–10 minutes (frequency of log backups)
- Available in all service tiers (General Purpose, Business Critical, Hyperscale)

#### ARM Template Deployment Modes
| Mode | Behavior |
|------|---------|
| **Incremental** (default) | Adds/updates resources in template; leaves existing resources not in template **untouched** |
| **Complete** | Resources in template are deployed; resources in RG **not** in template are **deleted** |
- Linked templates: a master template that calls child templates via URL; each child runs in its own deployment scope
- `templateLink` + `contentVersion` used to reference child templates

#### ARM Template — validate and what-if
- **`az deployment group validate`**: Checks template syntax and ARM API constraints without deploying
- **`az deployment group what-if`**: Shows a preview diff of what would be created/modified/deleted — does NOT deploy
- Both are pre-deployment safety checks; `what-if` is richer (shows resource-level change summary)

#### Bicep — Conditional Deployment (`if`)
- Deploy a resource only when a condition is true:
  ```bicep
  resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) {
    ...
  }
  ```
- Condition must evaluate to a `bool` at deployment time (parameter, variable, or expression)
- If condition is `false`, the resource block is skipped entirely — no resource created

#### Application Gateway — Session Affinity (Cookie-Based)
- Application Gateway inserts a cookie (`ApplicationGatewayAffinity`) on the first response
- Subsequent requests with the cookie are routed to the **same backend instance**
- Enabled per HTTP setting (listener → routing rule → HTTP settings → Cookie-based affinity: Enabled)
- Use case: stateful web apps where session state is stored on the server

#### Service Endpoint Policy
- Allows you to filter traffic from a VNet subnet to **specific Azure Storage accounts** (not just all Storage)
- Without policy: Service Endpoint allows access to all Storage in a region
- With policy: only listed storage accounts are permitted through the endpoint
- Scope: only Azure Storage is supported today for Service Endpoint Policies
- Important limitation: applies only to traffic originating from the associated **Azure subnets**, **not** from **on-premises networks**
- Exam trap: **not** restricted to storage accounts in the same region; the more relevant limitation is the **traffic origin scope**

#### Backup — Crash-Consistent vs Application-Consistent
| Type | How captured | Data state |
|------|-------------|-----------|
| **Crash-consistent** | Snapshot of disk at a point in time (no OS notification) | Like pulling power — in-flight writes may be lost |
| **Application-consistent** | Uses VSS (Windows) / pre/post scripts (Linux) to quiesce I/O | All transactions complete before snapshot |
- Azure Backup for VMs takes **application-consistent** by default when VSS is available
- Crash-consistent is the fallback (e.g., if VSS fails or for Linux without pre/post scripts)
- Exam clue: "minimal data loss, no in-flight transactions lost" → application-consistent

#### Blob Archive — Rehydration Priority
- Archive tier blobs must be **rehydrated** (moved to Hot or Cool) before reading
- **Standard priority**: completes within 15 hours
- **High priority**: for urgent scenarios; completes within **1 hour** for objects < 10 GB
- Rehydration method: Change tier (copy to Hot/Cool) or `Set Blob Tier` with priority parameter

#### Diagnostic Settings — Category Groups (allLogs + audit)
- Diagnostic settings can send logs to: Log Analytics, Storage Account, Event Hub, or partner solution
- **Log categories**:
  - `allLogs`: every log category the resource supports (most comprehensive)
  - `audit`: only audit/control-plane logs (who did what to the resource)
- You can select individual categories **or** use category groups (`allLogs`, `audit`)
- Metrics are a separate checkbox alongside log categories

#### Metric Alert — AND Logic for Multiple Conditions
- A single Metric Alert rule can have **multiple conditions** (signals)
- Default logic: **AND** — all conditions must breach simultaneously to fire the alert
- Example: alert when CPU > 80% **AND** Available Memory < 1 GB at the same time
- This reduces alert noise compared to separate single-signal alerts

---

### 한국어

#### LB HA Ports 규칙
- `프로토콜: 전체`, `포트: 0`으로 설정한 단일 부하 분산 규칙 — **모든** TCP/UDP 포트를 백엔드에 전달
- **Standard Load Balancer**의 내부 프런트엔드에서만 사용 가능
- 사용 사례: 어떤 포트가 사용될지 예측할 수 없는 NVA(네트워크 가상 어플라이언스)
- 포트별 별도 규칙 작성 불필요

#### Azure Firewall FQDN 태그
- Microsoft가 관리하는 사전 빌드 FQDN 그룹 (예: `WindowsUpdate`, `AzureKubernetesService`)
- **애플리케이션 규칙**에서 사용해 복잡한 FQDN 집합을 일일이 나열하지 않고 허용
- Microsoft가 FQDN 목록을 최신 상태로 유지 — 수동 관리 불필요
- 시험 단서: "Azure Firewall을 통해 Windows Update 트래픽 허용" → FQDN 태그 `WindowsUpdate` 사용

#### Azure File Sync — Cloud Tiering
- 최근 액세스되지 않은 파일을 Azure Files로 **Cloud Tiering**(서버에서 포인터 스텁으로 대체)
- 로컬 디스크 공간을 확보하면서 온프레미스에서 네임스페이스 계속 유지
- Cloud Tiering 정책: **볼륨 여유 공간** 기준(X% 유지) 또는 **날짜** 기준(N일간 미접근 파일)
- 사용자가 스텁 파일 열 때 자동 호출(recall)
- **Cloud Tiering은 서버 엔드포인트 단위**로 설정 (동기화 그룹 단위 아님)

#### VM 확장 — Azure 플랫폼 IP (168.63.129.16)
- `168.63.129.16`은 VM 에이전트와 플랫폼 서비스가 사용하는 Azure의 비라우팅 가상 IP
- 사용 용도: VM 에이전트 하트비트, 확장 상태 보고, 사용자 지정 스크립트 실행, 상태 프로브
- NSG 규칙에서 `168.63.129.16`(또는 태그 `AzurePlatform`)으로의 아웃바운드를 허용해야 확장이 정상 작동
- **호스트 레벨 IP** — VM이 이 주소를 통해 Azure 호스트와 통신

#### SQL Database — 특정 시점 복원 (PITR)
- Azure SQL Database가 트랜잭션 로그 백업을 자동 보관
- PITR 보존 기간: 1~35일 (기본 7일, 데이터베이스별 구성 가능)
- **새 데이터베이스**로 복원 — 원본 데이터베이스 덮어쓰기 불가
- RPO: 약 5~10분 (로그 백업 빈도)
- 모든 서비스 계층에서 사용 가능 (General Purpose, Business Critical, Hyperscale)

#### ARM 템플릿 배포 모드
| 모드 | 동작 |
|------|------|
| **증분(Incremental)** (기본값) | 템플릿의 리소스 추가/업데이트; 템플릿에 없는 기존 리소스 **그대로 유지** |
| **완전(Complete)** | 템플릿의 리소스 배포; RG에서 템플릿에 없는 리소스 **삭제** |
- 연결 템플릿(Linked templates): 마스터 템플릿이 URL로 하위 템플릿 호출; 각 하위 템플릿은 자체 배포 범위에서 실행
- `templateLink` + `contentVersion`으로 하위 템플릿 참조

#### ARM 템플릿 — validate와 what-if
- **`az deployment group validate`**: 배포 없이 템플릿 문법과 ARM API 제약 확인
- **`az deployment group what-if`**: 생성/수정/삭제될 내용의 미리 보기 diff 표시 — 배포 미실행
- 둘 다 배포 전 안전성 검사; `what-if`가 더 풍부한 정보 (리소스 수준 변경 요약)

#### Bicep — 조건부 배포 (`if`)
- 조건이 참일 때만 리소스 배포:
  ```bicep
  resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) {
    ...
  }
  ```
- 조건은 배포 시점에 `bool`로 평가 (파라미터, 변수 또는 표현식)
- 조건이 `false`이면 리소스 블록 전체 스킵 — 리소스 미생성

#### Application Gateway — 세션 선호도 (쿠키 기반)
- Application Gateway가 첫 응답에 쿠키(`ApplicationGatewayAffinity`) 삽입
- 이후 요청은 해당 쿠키로 **동일한 백엔드 인스턴스**로 라우팅
- HTTP 설정별로 활성화 (리스너 → 라우팅 규칙 → HTTP 설정 → 쿠키 기반 선호도: 사용)
- 사용 사례: 세션 상태가 서버에 저장되는 스테이트풀 웹 앱

#### Service Endpoint Policy
- VNet 서브넷에서 **특정 Azure 스토리지 계정**으로만 트래픽 필터링 가능 (전체 Storage가 아닌)
- 정책 없음: Service Endpoint로 지역 내 모든 Storage에 접근 가능
- 정책 있음: 나열된 스토리지 계정만 엔드포인트 통과 허용
- 범위: 현재 Azure Storage에만 Service Endpoint Policy 지원
- 중요한 제한: 연결된 **Azure 서브넷에서 시작된 트래픽**에만 적용되며, **온프레미스 네트워크 트래픽에는 적용되지 않음**
- 시험 함정: **같은 지역 Storage만 가능**이 핵심 제약이 아니라, **트래픽 출발지 범위 제한**이 핵심 포인트

#### 백업 — 크래시 일관성 vs 애플리케이션 일관성
| 유형 | 캡처 방법 | 데이터 상태 |
|------|---------|-----------|
| **크래시 일관성** | OS 알림 없이 시점 디스크 스냅샷 | 전원 차단처럼 — 전송 중 쓰기 손실 가능 |
| **애플리케이션 일관성** | VSS(Windows)/사전·사후 스크립트(Linux)로 I/O 정지 | 스냅샷 전 모든 트랜잭션 완료 |
- Azure VM Backup은 VSS 사용 가능 시 기본적으로 **애플리케이션 일관성** 백업 수행
- 크래시 일관성은 폴백 (예: VSS 실패 또는 사전·사후 스크립트 없는 Linux)
- 시험 단서: "데이터 손실 최소화, 진행 중 트랜잭션 손실 없음" → 애플리케이션 일관성

#### Blob 아카이브 — 리하이드레이션 우선순위
- 아카이브 계층 Blob은 읽기 전 반드시 **리하이드레이션** 필요 (Hot 또는 Cool로 이동)
- **표준 우선순위**: 최대 15시간 내 완료
- **높은 우선순위**: 긴급 시나리오; 10GB 미만 객체는 **1시간** 내 완료
- 리하이드레이션 방법: 계층 변경(Hot/Cool으로 복사) 또는 우선순위 파라미터와 함께 `Set Blob Tier`

#### Diagnostic Settings — 카테고리 그룹 (allLogs + audit)
- Diagnostic settings 전송 대상: Log Analytics, Storage Account, Event Hub, 파트너 솔루션
- **로그 카테고리**:
  - `allLogs`: 리소스가 지원하는 모든 로그 카테고리 (가장 포괄적)
  - `audit`: 감사/컨트롤 플레인 로그만 (리소스에 누가 무엇을 했는지)
- 개별 카테고리 선택 **또는** 카테고리 그룹(`allLogs`, `audit`) 사용 가능
- 메트릭은 로그 카테고리와 별도 체크박스

#### 메트릭 경고 — 여러 조건에 AND 논리
- 단일 메트릭 경고 규칙에 **여러 조건** (신호) 추가 가능
- 기본 논리: **AND** — 모든 조건이 동시에 위반될 때 경고 발생
- 예시: CPU > 80% **AND** 사용 가능 메모리 < 1GB 동시 충족 시 경고
- 단일 신호 경고 대비 알림 노이즈 감소

---

## Section 20 — 2026 Gap Fill: Set5 Topics

### English

#### Load Balancer Floating IP / Direct Server Return (DSR)
- **What it does**: Preserves the Load Balancer's frontend IP as the destination IP in packets delivered to backend VMs — the IP is **not** translated (no DNAT)
- **OS requirement**: VM must have a loopback adapter with the LB frontend IP configured so the OS accepts packets destined for that IP
- **Use cases**: SQL Server Always On listeners, Windows/Linux Failover Clusters, NVAs requiring symmetric routing, port-reuse across rules on the same VM
- **Traffic flow**: Client → LB → VM (dest = LB frontend IP, untranslated); return traffic goes directly from VM to client, bypassing LB

#### RBAC: NotActions vs Deny (Additive Model)
- **RBAC is additive**: Effective permissions = union of all role assignments at that scope
- **NotActions ≠ Deny**: `NotActions` only subtracts from `Actions` within the **same role definition**. It has no effect on permissions granted by a different role assignment
- **To truly block**: Use an Azure Policy `Deny` effect or a resource lock — not `NotActions`
- **Exam trap**: A custom role with `NotActions: ['...delete']` + Storage Account Contributor assigned = user **can** delete (Contributor grants it additively)

#### Custom Role Actions vs DataActions
- `Actions` — management plane (ARM) operations (e.g., read storage account properties)
- `DataActions` — data plane operations (e.g., read/write blob data)
- Assign both when a role needs both management **and** data plane access
- `NotActions` and `NotDataActions` subtract within their respective role, never deny cross-role

#### Custom Role: Id Must Be Null on Creation
- When creating a new custom role via JSON, set `"Id": null` — Azure auto-generates a GUID
- `AssignableScopes` must **not** be null — specify at least one valid scope
- Setting a non-null `Id` attempts to update an existing role with that ID

#### Azure Firewall Child Policy: Threat Intelligence Inheritance
- Threat intelligence mode strictness: Off < Alert < Alert and deny
- **Child policy rule**: child mode ≥ parent mode. A child policy cannot be less strict than its parent
- If parent = "Alert and deny" (strictest), child can only be "Alert and deny"
- Design intent: parent enforces org-wide baselines; child can tighten but not loosen inherited settings

#### Azure Firewall Premium: Application Rules and SNI
- Application rules use a transparent proxy that reads the **SNI TLS extension** (for HTTPS) and the HTTP `Host` header
- This enables filtering by FQDN even when multiple FQDNs share the same IP address
- Network rules filter by IP + port only — cannot distinguish same-IP FQDNs

#### Encryption at Host
- Encrypts **temporary disk + disk cache + OS and data disks** at the Azure storage host level
- Encryption happens **before** data reaches Azure Storage — not inside the VM
- **No VM CPU overhead** (contrast with ADE/BitLocker which runs inside the VM)
- Covers what standard SSE alone does not (temp disk + caches)
- Exam keyword combo: "temp disk and cache" + "no VM CPU" → Encryption at Host

#### AKS Spot Node Pool
- Uses Azure Spot VMs — can be evicted by Azure with 30-second notice
- **Pool mode must be User** (cannot be System); cluster must already have a System node pool
- Suitable for: fault-tolerant batch jobs, CI/CD agents, stateless interruptible workloads
- Not suitable for: critical system pods, production web serving

#### Service Health: Inactive Events Retention
- Azure Service Health **Health History** retains inactive (resolved) events for **90 days**
- After 90 days, events are purged from the Health History view
- Active incidents remain visible until resolved

#### Budget Time Grain
- **Monthly**: resets every calendar month
- **Quarterly**: resets every 3 months
- **Annual**: resets every 12 months
- Exam trigger: "reset every three months" → Quarterly

#### Hierarchy Settings Administrator Role
- Grants `Microsoft.Management/managementGroups/settings/write` and `read`
- Required to configure the **default management group** for new subscriptions
- Least-privilege alternative to Owner on the root management group

#### Root Management Group Facts
- One root management group per Entra tenant (ID = tenant ID)
- Cannot be moved or deleted
- All subscriptions and management groups roll up to the root
- Global Admins must explicitly elevate to User Access Administrator at `/` scope to gain access

---

### 한국어

#### Load Balancer Floating IP / Direct Server Return (DSR)
- **기능**: Load Balancer 프런트엔드 IP가 백엔드 VM에 전달되는 패킷의 목적지 IP로 유지됨(DNAT 미발생)
- **OS 요구사항**: VM 루프백 어댑터에 LB 프런트엔드 IP 구성 필요 — 그래야 OS가 해당 IP 목적지 패킷 수락
- **사용 사례**: SQL Server Always On 리스너, Windows/Linux 장애 조치 클러스터, NVA 대칭 라우팅, 동일 VM에서 동일 포트 재사용
- **트래픽 흐름**: 클라이언트 → LB → VM(목적지 = LB 프런트엔드 IP, 변환 없음); 반환 트래픽은 VM에서 클라이언트로 직접, LB 우회

#### RBAC: NotActions ≠ 거부 (가산 모델)
- **RBAC는 가산 모델**: 유효 권한 = 해당 범위의 모든 역할 할당 합집합
- **NotActions ≠ 거부**: `NotActions`는 **같은 역할 정의** 내 `Actions`에서만 뺌. 다른 역할이 부여한 권한에 영향 없음
- **진짜 차단하려면**: Azure Policy `Deny` 효과 또는 Resource Lock 사용
- **시험 함정**: `NotActions: ['...delete']`가 있는 커스텀 역할 + Storage Account Contributor 동시 할당 = 사용자가 **삭제 가능** (Contributor가 가산적으로 부여)

#### 사용자 지정 역할: Actions vs DataActions
- `Actions` — 관리 플레인(ARM) 작업 (예: 스토리지 계정 속성 읽기)
- `DataActions` — 데이터 플레인 작업 (예: Blob 데이터 읽기/쓰기)
- 관리 플레인과 데이터 플레인 모두 필요한 역할에 둘 다 지정
- `NotActions` / `NotDataActions`는 해당 역할 정의 내에서만 차감

#### 사용자 지정 역할: 생성 시 Id는 반드시 null
- JSON으로 새 역할 생성 시 `"Id": null`로 설정 → Azure가 GUID 자동 생성
- `AssignableScopes`는 null이면 안 됨 — 유효한 범위 최소 1개 지정 필수
- non-null Id 지정 시 해당 ID의 기존 역할 업데이트 시도 (또는 충돌 오류)

#### Azure Firewall 하위 정책: 위협 인텔리전스 상속
- 엄격도 순서: 끄기 < 경고 < 경고 및 거부
- **하위 정책 규칙**: 하위 모드 ≥ 부모 모드. 하위 정책이 부모보다 덜 엄격할 수 없음
- 부모 = "경고 및 거부"(최고 엄격도)이면 하위는 "경고 및 거부"만 가능
- 설계 의도: 부모가 조직 기준 적용, 하위는 강화만 가능, 완화 불가

#### Azure Firewall Premium: 애플리케이션 규칙과 SNI
- 애플리케이션 규칙은 투명 프록시로 **SNI TLS 확장**(HTTPS) 및 HTTP `Host` 헤더 검사
- 여러 FQDN이 같은 IP를 공유해도 FQDN별 필터링 가능
- 네트워크 규칙은 IP + 포트만 필터링 — 같은 IP FQDN 구별 불가

#### 호스트 암호화 (Encryption at Host)
- Azure 스토리지 호스트 수준에서 **임시 디스크 + 디스크 캐시 + OS/데이터 디스크** 암호화
- VM에 도달하기 전에 암호화 — VM 내부가 아닌 하드웨어 레벨
- **VM CPU 오버헤드 없음** (ADE/BitLocker는 VM 내부 실행으로 CPU 소비)
- 표준 SSE가 커버하지 못하는 영역(임시 디스크 + 캐시) 포함
- 시험 키워드: "임시 디스크와 캐시" + "VM CPU 없음" → 호스트 암호화

#### AKS Spot 노드 풀
- Azure Spot VM 사용 — 30초 통보 후 Azure에 의해 퇴출 가능
- **풀 모드는 반드시 User** (System 불가); 클러스터에 이미 System 노드 풀 있어야 함
- 적합 워크로드: 내결함성 배치 잡, CI/CD 에이전트, 상태 비저장 중단 허용 워크로드
- 부적합: 중요 시스템 Pod, 프로덕션 웹 서비스

#### Service Health: 비활성 이벤트 보존 기간
- Azure Service Health 상태 기록(Health History)에서 비활성(해결됨) 이벤트를 **90일** 보존
- 90일 후 이벤트가 상태 기록 보기에서 제거됨
- 활성 인시던트는 해결될 때까지 계속 표시

#### 예산 시간 단위 (Budget Time Grain)
- **월별**: 매 달력 월 재설정
- **분기별**: 3개월마다 재설정
- **연간**: 12개월마다 재설정
- 시험 트리거: "3개월마다 재설정" → 분기별

#### 계층 구조 설정 관리자 역할
- `Microsoft.Management/managementGroups/settings/write` 및 `read` 권한 부여
- 새 구독의 **기본 관리 그룹** 구성에 필요
- 루트 관리 그룹 소유자 대비 최소 권한 대안

#### 루트 관리 그룹 특징
- Entra 테넌트당 루트 관리 그룹 1개 (ID = 테넌트 ID)
- 이동하거나 삭제 불가
- 모든 구독과 관리 그룹이 루트로 귀결
- 전역 관리자는 `/` 범위에서 사용자 액세스 관리자로 명시적 권한 상승 필요

---

## Section 20 — 2026 Gap Fill: Set6 Topics

### English

#### Custom Script Extension Logs — Linux VM
- Extension-specific logs stored at: `/var/log/azure/<ExtensionName>/`
- For Custom Script Extension: `/var/log/azure/custom-script-extension/handler.log`
- `handler.log` contains script execution output, error details, and status
- Broader Azure Linux Agent logs: `/var/log/waagent.log`
- Extension binaries, settings, scripts: `/var/lib/waagent/`
- Exam tip: "Linux VM Custom Script Extension failed" → start at `handler.log`

#### Microsoft Graph PowerShell — Managing Entra Users
- Current recommended module for Entra ID (Azure AD) management: **Microsoft Graph PowerShell SDK**
- `Get-MgUser`: retrieve users; supports `-Filter` with OData syntax (e.g., `department eq 'Accounting'`)
- `Update-MgUser`: update user properties including `UsageLocation`, `Department`, etc.
- Deprecated modules (do NOT use for new scripts): `AzureAD` (`Get-AzureADUser`), `MSOnline` (`Get-MsolUser`)
- `Az` module (`Get-AzADUser`) is for Azure resource management, not Entra user attribute management

#### Azure Policy — `modify` Effect
- **`modify`** effect: adds, updates, or removes resource properties (tags, SKU, location) at deployment time
- Supports **Remediation Tasks** for existing non-compliant resources — requires managed identity on assignment
- Existing resources can still be reported as **non-compliant** until a remediation task is run; evaluation and remediation are separate steps
- Comparison:
  - `append`: adds array values; limited remediation support
  - `deployIfNotExists`: deploys a missing companion resource (e.g., diagnostic settings on a VM)
  - `audit`/`deny`: evaluate compliance only — no changes made
- Exam pattern: "auto-add tag + remediate existing resources" → `modify`

#### Resource Locks — Who Can Manage Them
- Required permission: `Microsoft.Authorization/locks/*`
- Built-in roles with this permission: **Owner** and **User Access Administrator**
- **Contributor cannot manage locks** — this is a common exam trap
- Exam answer pattern:
  - "Minimum privilege to manage locks only" → User Access Administrator
  - "Enable a Contributor user to apply a lock" → assign **Owner** role
- Owner = full resource management + RBAC assignment + lock/policy management

#### VM Insights — Map Feature and Dependency Agent
- VM Insights has two components: Azure Monitor Agent (metrics/logs) + **Dependency Agent** (process/network data)
- **Map feature** requires the Dependency Agent — without it, only the Performance tab works
- Dependency Agent collects: running processes, TCP connections, inbound/outbound network connections
- Must be installed on the VM alongside Azure Monitor Agent (or Log Analytics Agent)
- Exam tip: "Map feature", "running processes", "dependencies" → add **Dependency Agent**

#### NSG Rule Priority Evaluation
- Rules evaluated in ascending priority order (lower number = higher priority)
- Processing **stops at the first matching rule** — subsequent rules are not evaluated
- `Any` as source/destination matches all traffic, including internet
- Common exam trap: An "Allow Internet" rule at priority 300 is blocked if a "Deny Any" rule exists at priority 200
- Fix: assign the Allow rule a lower priority number than the Deny rule

#### Load Balancer Health Probe — Source IP
- Azure Load Balancer health probes originate from: **`168.63.129.16`**
- This is Azure's non-routable virtual IP used by platform services
- If NSG or OS firewall blocks inbound from `168.63.129.16`, health probes fail → VM marked unhealthy → removed from pool
- NSG fix: allow inbound from `168.63.129.16` **or** service tag `AzureLoadBalancer` on the probe port
- Same IP also used by: VM Agent heartbeat, Custom Script Extension status reporting, DHCP/DNS

---

### 한국어

#### Custom Script Extension 로그 — Linux VM
- 확장별 로그 경로: `/var/log/azure/<확장이름>/`
- Custom Script Extension: `/var/log/azure/custom-script-extension/handler.log`
- `handler.log`: 스크립트 실행 출력, 오류 세부 정보, 상태 정보 포함
- 광범위한 Azure Linux 에이전트 로그: `/var/log/waagent.log`
- 확장 바이너리, 설정, 스크립트: `/var/lib/waagent/`
- 시험 팁: "Linux VM Custom Script Extension 실패" → `handler.log`에서 시작

#### Microsoft Graph PowerShell — Entra 사용자 관리
- Entra ID(Azure AD) 관리 현행 권장 모듈: **Microsoft Graph PowerShell SDK**
- `Get-MgUser`: 사용자 검색; OData 구문 `-Filter` 지원 (예: `department eq 'Accounting'`)
- `Update-MgUser`: `UsageLocation`, `Department` 등 사용자 속성 업데이트
- 더 이상 사용하지 않는 모듈: `AzureAD`(`Get-AzureADUser`), `MSOnline`(`Get-MsolUser`) — 사용 중단 예고
- `Az` 모듈(`Get-AzADUser`)은 Azure 리소스 관리용; Entra 사용자 속성 관리 불가

#### Azure Policy — `modify` 효과
- **`modify`** 효과: 배포 시점에 리소스 속성(태그, SKU, 위치) 추가·업데이트·제거
- 기존 비준수 리소스에 **Remediation Task** 지원 — 정책 할당에 관리 ID 필요
- 기존 리소스는 정책 평가 후에도 **비준수로만 표시**될 수 있으며, 실제 수정은 **Remediation Task** 실행 시 반영됨
- 비교:
  - `append`: 배열 값 추가; 수정 제한적
  - `deployIfNotExists`: 누락된 동반 리소스 배포 (예: VM에 진단 설정 추가)
  - `audit`/`deny`: 준수 여부 평가만 — 변경 없음
- 시험 패턴: "태그 자동 추가 + 기존 리소스 수정" → `modify`

#### 리소스 잠금 — 관리 가능한 역할
- 필요 권한: `Microsoft.Authorization/locks/*`
- 이 권한을 가진 기본 역할: **Owner**와 **User Access Administrator**
- **Contributor는 잠금 관리 불가** — 자주 나오는 시험 함정
- 시험 답변 패턴:
  - "잠금만 관리하는 최소 권한" → User Access Administrator
  - "Contributor 사용자가 잠금 적용하도록 설정" → **Owner** 역할 할당
- Owner = 전체 리소스 관리 + RBAC 할당 + 잠금/정책 관리

#### VM Insights — Map 기능과 Dependency Agent
- VM Insights 두 구성 요소: Azure Monitor Agent(메트릭/로그) + **Dependency Agent**(프로세스/네트워크)
- **Map 기능은 Dependency Agent 필수** — 없으면 Performance 탭만 작동
- Dependency Agent 수집 정보: 실행 중인 프로세스, TCP 연결, 인바운드/아웃바운드 네트워크 연결
- Azure Monitor Agent(또는 Log Analytics Agent)와 함께 VM에 설치 필요
- 시험 팁: "Map 기능", "실행 중인 프로세스", "종속성" → **Dependency Agent** 추가

#### NSG 규칙 우선순위 평가
- 규칙은 우선순위 오름차순(숫자가 낮을수록 우선순위 높음)으로 평가
- **첫 번째 일치 규칙에서 처리 중단** — 이후 규칙은 평가되지 않음
- 원본/대상 `모두`(Any)는 인터넷 포함 모든 트래픽에 일치
- 자주 나오는 시험 함정: 우선순위 200의 "모두 거부" 규칙이 있으면 우선순위 300의 "인터넷 허용" 규칙은 도달 불가
- 수정 방법: 허용 규칙에 거부 규칙보다 낮은 우선순위 번호 부여

#### Load Balancer 상태 프로브 — 원본 IP
- Azure Load Balancer 상태 프로브 원본 IP: **`168.63.129.16`**
- Azure 플랫폼 서비스가 사용하는 비라우팅 가상 IP
- NSG 또는 OS 방화벽이 `168.63.129.16`의 인바운드 차단 시 → 프로브 실패 → VM 비정상 → 풀에서 제거
- NSG 수정: 프로브 포트에 `168.63.129.16` **또는** 서비스 태그 `AzureLoadBalancer` 인바운드 허용
- 동일 IP의 기타 용도: VM 에이전트 하트비트, Custom Script Extension 상태 보고, DHCP/DNS

#### 가용성 집합 — 계획된 유지 관리 (업데이트 도메인)
- 계획된 유지 관리는 **업데이트 도메인(UD)** 단위로 순차 적용; 결함 도메인과 무관
- UD당 최대 비가용 VM = 총 VM ÷ UD 수 (예: 10 VM ÷ 5 UD = 2대 동시 비가용)
- 시험 팁: "계획된 유지 관리" → 업데이트 도메인 집중; 결함 도메인은 하드웨어 장애용

#### 가용성 집합 — 관리형 디스크 ARM 템플릿 (`sku.name`)
- 관리형 디스크 VM을 포함하는 가용성 집합 → `sku.name = Aligned` 필수
- `Classic`은 레거시 비관리형 디스크용
- 시험 팁: 가용성 집합 + 관리형 디스크 ARM 템플릿 → `sku.name: Aligned`

#### App Service 배포 슬롯 — 슬롯 설정 (스티키)
- 기본 앱 설정은 슬롯 교환 시 코드와 함께 **이동**
- **슬롯 설정(스티키)**으로 표시된 설정은 구성된 슬롯에 **고정** (교환 후에도 해당 슬롯에 유지)
- 시험 팁: "교환 후에도 스테이징에 유지되어야 하는 설정" → 슬롯 설정으로 표시

#### App Service 자동 교환 (Auto Swap)
- 스테이징 슬롯에서 활성화 시 코드 배포 후 웜업 완료되면 프로덕션으로 자동 교환
- 수동 개입 불필요; CI/CD 파이프라인에 이상적
- 시험 팁: "웜업 후 자동으로 프로덕션 교환" → **자동 교환** 활성화

#### App Service TLS/SSL 바인딩 유형
- **SNI SSL**: 여러 도메인이 동일 IP 공유 가능 (현행 표준, 권장)
- **IP 기반 SSL**: 도메인당 전용 IP 필요 (레거시, 비용 증가)
- 시험 팁: "동일 IP에 여러 도메인 + TLS" → **SNI SSL**

#### Azure Site Recovery — 장애 조치 후 재보호
- 장애 조치 + 커밋 후 원래 복제 관계가 끊어짐
- 기본 지역으로 다시 복제하려면 **재보호(Re-protect)** 필수 (복제 방향 역전)
- 순서: 장애 조치 → 커밋 → 재보호 → (역방향 복제) → 장애 복구
- 시험 팁: "커밋 후 기본으로 복제" → **재보호** 먼저

#### ASR 커밋 — 복구 지점 삭제
- 커밋 후 기존 복구 지점 모두 **삭제**; 롤백 불가
- 커밋 후 복구 지점은 재보호 후에만 다시 생성 가능
- 시험 팁: ASR 커밋 → 복구 지점 **삭제**, 장애 복구 불가

#### Azure VM Enhanced 백업 정책 — 즉시 복구 스냅샷
- Enhanced 정책 기본 즉시 복구 스냅샷 보존 기간: **7일**
- 스냅샷 계층 (VM 근처 디스크 스토리지) + Vault 계층 (장기 보존) 두 계층 저장
- 시험 팁: Enhanced 정책 + 즉시 복구 스냅샷 → 기본 **7일**

#### Azure Policy — deny 효과
- `deny` 효과: 비준수 리소스 생성/업데이트 시 즉시 **차단**
- `audit`: 로그만 기록, 차단 안 함; `modify`: 속성 수정, 차단 안 함
- 시험 팁: "배포 방지/차단" → **`deny`**

#### Azure Policy — modify 작업 유형
- `add`: 태그/속성이 **없을 때만** 추가 (기존 값 보존)
- `addOrReplace`: 없으면 추가, 있으면 **덮어씀**
- `remove`: 태그/속성 삭제
- `copy`: 한 필드에서 다른 필드로 값 복사
- 시험 팁: "기존 값 보존하며 상속" → `add`; "항상 적용" → `addOrReplace`

#### Azure Policy — 적용 모드 (Enforcement Mode)
- `Disabled` 모드: 준수 평가 유지하되 **차단 동작 없음** (테스트용)
- `Enabled` (기본값): 평가 + 차단 모두 적용
- 시험 팁: "차단 없이 정책 테스트" → 적용 모드 **Disabled**

#### Bicep 루프 + 조건 구문
- `[for ... : if(...) { }]` 순서 — **`for` 먼저, `if` 다음**
- `if(...) [for ... : { }]`는 잘못된 구문 (컴파일 오류)
- 시험 팁: Bicep 루프 + 조건 조합 → `for`가 항상 `if` 앞

#### Bicep 모듈 출력 참조
- `moduleName.outputs.outputName` (복수형 `outputs` 사용)
- `moduleName.output.outputName`은 잘못된 구문
- 시험 팁: Bicep 모듈 출력 → `모듈이름.outputs.출력이름`

#### 관리 그룹 RBAC 범위 상속
- 관리 그룹에서 할당된 역할은 **모든 하위 구독과 리소스 그룹에 상속**
- 여러 구독에 걸쳐 최소 역할 할당 → 관리 그룹 범위에서 1회 할당
- 시험 팁: "여러 구독 최소 역할 할당" → **관리 그룹 범위**

#### 동적 멤버십 그룹 — Premium P1/P2 필요
- 동적 그룹(속성 기반 자동 멤버십) = **Microsoft Entra ID Premium P1 또는 P2** 필수
- Entra ID Free 라이선스에서는 동적 그룹 사용 불가
- 시험 팁: "동적 그룹 만들기" → 먼저 **Premium P1/P2 업그레이드** 확인

#### 리소스 잠금 상속 + RBAC 재정의
- 리소스 그룹의 잠금은 모든 하위 리소스에 **상속**
- `CanNotDelete` 잠금은 **Owner 역할도 삭제 불가**; 잠금이 RBAC보다 우선
- 시험 팁: "CanNotDelete + Owner 삭제 시도" → **삭제 실패**

#### Azure Storage 수명 주기 관리 — 블록 Blob 전용
- 계층화(핫 → 쿨 → 보관) 작업: **블록 Blob만** 지원; 추가·페이지 Blob 미지원
- 여러 작업 동시 일치 시 **가장 저렴한 작업 적용** (delete < archive < cool)
- 시험 팁: 수명 주기 계층화 → 블록 Blob; 같은 Blob에 여러 작업 → 가장 저렴한 것

#### Azure 파일 공유 스냅샷 한도
- 파일 공유당 최대 스냅샷 수: **200개**
- 한도 도달 시 새 스냅샷(백업 포함) 생성 불가; 오래된 스냅샷 삭제 필요
- 시험 팁: 파일 공유 스냅샷 한도 = **200**

#### 서비스 SAS 취소 — 저장된 액세스 정책 삭제
- 저장된 액세스 정책과 연결된 서비스 SAS → **정책 삭제로 즉시 취소 가능**
- 계정 키 재생성 없이도 SAS 즉시 무효화 가능
- 시험 팁: "키 재생성 없이 SAS 즉시 취소" → **저장된 액세스 정책 삭제**

#### Blob 컨테이너 저장된 액세스 정책 한도
- 컨테이너당 최대 저장된 액세스 정책 수: **5개**
- 시험 팁: 컨테이너 저장된 액세스 정책 한도 = **5**

#### Azure Files ID 기반 인증 — 한 번에 하나의 원본
- Azure Files는 한 번에 **하나의 ID 원본**만 지원 (Entra DS, 온프레미스 AD DS 중 하나)
- ID 원본 변경 시 **기존 원본 먼저 비활성화** 필수
- 시험 팁: Azure Files ID 인증 전환 → 기존 원본 **먼저 비활성화**

#### Azure File Sync 클라우드 변경 감지
- Azure 파일 공유에 직접 변경 시 서버에 자동 전파는 **24시간 주기**로 실행
- 즉시 동기화 트리거: `Invoke-AzStorageSyncChangeDetection` cmdlet 실행
- 시험 팁: "Azure 파일 공유 직접 변경이 서버에 느리게 전파" → **`Invoke-AzStorageSyncChangeDetection`**

#### Azure File Sync 서버 엔드포인트
- 유효한 **로컬 NTFS 경로**이면 어디든 서버 엔드포인트 가능
- 다른 서버와 경로가 같을 필요 없음; UNC 또는 클라우드 경로 불가
- 시험 팁: File Sync 서버 엔드포인트 = 로컬 **NTFS 경로** (경로명은 자유)

#### 스토리지 계정 키 순환 알림 (키 만료 정책)
- **키 만료 정책**: 순환 기간 경과 시 포털에 알림 표시 (자동 순환 아님)
- Key Vault 순환 정책과 다름 — 이것은 스토리지 계정 자체 설정
- 시험 팁: "키 순환 시기 알림" → **키 만료 정책**

#### Storage Account Contributor + Blob 포털 액세스 → Shared Key
- Portal에서 Blob 데이터 액세스 시 `listKeys` 권한 있으면 **Shared Key(계정 액세스 키)** 우선 사용
- Storage Account Contributor는 `listKeys` 포함 → Portal이 Azure AD 아닌 Shared Key로 인증
- Storage Blob Data Reader/Contributor는 `listKeys` 없음 → Azure AD 인증 사용
- 시험 팁: Storage Account Contributor + Portal → **Shared Key** (RBAC 관련임에도 Azure AD 아님)

#### Blob Private Endpoint DNS 영역
- Blob 하위 리소스 프라이빗 엔드포인트 + DNS 통합 → `privatelink.blob.core.windows.net`
- 다른 하위 리소스: file → `privatelink.file`, queue → `privatelink.queue`, table → `privatelink.table`
- 시험 팁: Blob 프라이빗 엔드포인트 DNS 영역 → **`privatelink.blob.core.windows.net`**

#### 프라이빗 엔드포인트 승인 (구독 간)
- 다른 구독에서 만든 프라이빗 엔드포인트 → 상태 `보류 중` → 대상 리소스 소유자가 **승인** 필요
- 승인 경로: 대상 리소스 → 네트워킹 → 프라이빗 엔드포인트 연결 → 승인
- 시험 팁: 교차 구독 프라이빗 엔드포인트 `보류 중` → **승인** 클릭

#### 프라이빗 DNS VNet 링크 자동 등록
- 자동 등록 활성화: VM A 레코드 자동 생성 (VNet당 설정)
- 자동 등록 비활성화: DNS 확인 가능하지만 레코드 자동 등록 없음
- 두 VNet 모두 확인하려면 VNet 링크 **필수**
- 시험 팁: "자동 등록 + 확인 전용" → 각각 별도 VNet 링크; 둘 다 링크 필요

#### 서비스 엔드포인트 정책
- 서비스 엔드포인트 단독 활성화 시 → 해당 서비스의 모든 리소스에 접근 가능
- **서비스 엔드포인트 정책** 추가 시 → 지정된 특정 리소스만 허용
- 시험 팁: "서비스 엔드포인트이지만 특정 스토리지 계정만 허용" → **서비스 엔드포인트 정책**

#### Application Gateway 상태 프로브 → 백엔드 HTTP 설정
- Application Gateway 사용자 지정 상태 프로브는 **백엔드 HTTP 설정**에 연결
- 구성 요소 흐름: 수신기 → 라우팅 규칙 → 백엔드 HTTP 설정(프로브 여기) → 백엔드 풀
- 시험 팁: Application Gateway 상태 프로브 → **백엔드 HTTP 설정**에 연결

#### Application Gateway 다중 사이트 수신기
- 동일 IP와 포트에서 여러 호스트명(도메인) 라우팅 → **다중 사이트 수신기**
- Host 헤더 기반 라우팅; 각 도메인마다 별도 다중 사이트 수신기 생성
- 기본 수신기: IP+포트만; 하나의 사이트만 지원
- 시험 팁: "동일 Application Gateway에 여러 도메인" → **다중 사이트 수신기**

#### Azure Firewall 애플리케이션 규칙 + FQDN 태그 (WindowsUpdate)
- FQDN 태그: Microsoft가 자동 관리하는 잘 알려진 서비스 FQDN 그룹
- `WindowsUpdate` FQDN 태그: 모든 Windows Update 엔드포인트 포함; 수동 업데이트 불필요
- 애플리케이션 규칙(레이어 7) + FQDN 태그 사용 = 최소 유지 관리
- 시험 팁: "최소 유지 관리로 Windows Update 허용" → 애플리케이션 규칙 + **`WindowsUpdate` FQDN 태그**

#### Route Table 지역 제약
- 경로 테이블은 연결할 서브넷의 VNet과 **동일한 지역**에 있어야 함
- 경로 테이블 지역 변경 불가 → **삭제 후 올바른 지역에 재생성** 필요
- 시험 팁: 경로 테이블과 VNet 지역 불일치 → 삭제 + 올바른 지역에 **재생성**

#### Azure Monitor 작업 그룹 이메일 속도 제한
- 이메일 알림 속도 제한: 수신자당 **시간당 최대 100개**
- 초과 시 속도 제한 기간 만료까지 추가 이메일 전송 안 됨
- 시험 팁: 작업 그룹 이메일 한도 = **100개/시간** (1,000개가 아님)

#### VM Insights 최소 구성 요소
- 기본 VM Insights(성능 모니터링): **Log Analytics 작업 영역 + Azure Monitor Agent**
- Map 기능(프로세스/종속성): 추가로 **Dependency Agent** 필요
- 시험 팁: VM Insights 최소 = **작업 영역 + Azure Monitor Agent**; Map 추가 = Dependency Agent

#### Key Vault Secrets User 역할 (App Service 비밀 읽기 최소 권한)
- RBAC 권한 모델 Key Vault에서 App Service(관리 ID)가 비밀 읽기: **Key Vault Secrets User** 최소 필요
- Key Vault Reader: 메타데이터만, 비밀 값 읽기 불가
- Key Vault Contributor: 제어 플레인 관리; 데이터 플레인(비밀 읽기) 권한 없음
- 시험 팁: 관리 ID가 Key Vault 비밀 읽기 → **Key Vault Secrets User**

---

### English (New Topics — Q8-Q44)

#### Availability Set — Planned Maintenance (Update Domains)
- Planned maintenance applied one **Update Domain (UD)** at a time; Fault Domains are for hardware failure
- Max unavailable VMs = Total VMs ÷ UD count (e.g., 10 VMs ÷ 5 UDs = 2 simultaneously unavailable)
- Exam tip: "Planned maintenance + Availability Set" → focus on **Update Domains**, not Fault Domains

#### Availability Set + Managed Disk ARM Template (`sku.name`)
- Availability sets containing managed disk VMs must set `sku.name = Aligned`
- `Classic` is for legacy unmanaged disks
- Exam tip: Availability Set + managed disk ARM template → `sku.name: Aligned`

#### App Service Deployment Slot Settings (Sticky)
- Default app settings move with the code during swap
- **Slot settings (sticky)**: pinned to the specific slot; do not move during swap
- Exam tip: "Setting must remain in staging slot after swap" → mark as **Slot setting**

#### App Service Auto Swap
- When enabled on staging slot: automatically swaps to production after warm-up
- No manual intervention; ideal for CI/CD pipelines
- Exam tip: "Automatically swapped to production after warming up" → enable **Auto swap**

#### App Service TLS/SSL Binding Types
- **SNI SSL**: multiple domains share the same IP (current standard, recommended)
- **IP-based SSL**: dedicated IP per domain (legacy, higher cost)
- Exam tip: "Multiple domains on same IP + TLS" → **SNI SSL**

#### Azure Site Recovery — Re-protect After Failover
- After failover + commit, original replication relationship is broken
- To replicate back to primary: **Re-protect** is mandatory (reverses replication direction)
- Workflow: Failover → Commit → Re-protect → (reverse replication) → Failback
- Exam tip: "Replicate back after commit" → **Re-protect** first

#### ASR Commit — Recovery Points Deleted
- After commit, all existing recovery points are **deleted**; rollback not possible
- Recovery points can only be recreated after performing Re-protect
- Exam tip: ASR commit → recovery points **deleted**, failback not possible

#### Azure VM Enhanced Backup — Instant Recovery Snapshot
- Enhanced policy default instant recovery snapshot retention: **7 days**
- Two tiers: snapshot (near VM) + vault (long-term)
- Exam tip: Enhanced policy + instant recovery snapshot → default **7 days**

#### Azure Policy — deny Effect
- `deny` effect: **blocks** non-compliant resource creation/update immediately
- `audit`: logs only, does not block; `modify`: modifies properties, does not block
- Exam tip: "Prevent deployment/block non-compliant resources" → **`deny`**

#### Azure Policy — modify Operation Types
- `add`: adds tag/property **only if not already present** (preserves existing values)
- `addOrReplace`: adds if missing, **overwrites** if exists
- `remove`: deletes the tag/property
- `copy`: copies value from one field to another
- Exam tip: "Inherit tag but never overwrite existing" → `add`; "always apply value" → `addOrReplace`

#### Azure Policy — Enforcement Mode
- `Disabled` mode: compliance evaluation runs but **no blocking behavior** (testing mode)
- `Enabled` (default): evaluation + blocking both applied
- Exam tip: "Test policy without blocking" → set enforcement mode to **Disabled**

#### Bicep Loop + Condition Syntax
- `[for ... : if(...) { }]` — **`for` must come before `if`**
- `if(...) [for ... : { }]` is invalid syntax (compile error)
- Exam tip: Bicep loop + condition → `for` always precedes `if`

#### Bicep Module Output Reference
- `moduleName.outputs.outputName` (plural `outputs`)
- `moduleName.output.outputName` is invalid syntax
- Exam tip: Bicep module output → `moduleName.outputs.outputName`

#### Management Group RBAC Scope Inheritance
- Role assigned at management group scope **inherits to all child subscriptions and resource groups**
- Minimum role assignments across multiple subscriptions → assign once at management group scope
- Exam tip: "Minimum assignments across multiple subscriptions" → **management group scope**

#### Dynamic Membership Groups — Premium P1/P2 Required
- Dynamic groups (attribute-based auto membership) require **Microsoft Entra ID Premium P1 or P2**
- Not available with Entra ID Free license
- Exam tip: "Create dynamic group" → first verify **Premium P1/P2** license

#### Resource Lock Inheritance + RBAC Override
- Locks on a resource group are **inherited by all child resources**
- `CanNotDelete` lock prevents deletion even for **Owner role** — lock overrides RBAC
- Exam tip: "CanNotDelete + Owner attempts delete" → **delete fails**

#### Azure Storage Lifecycle Management — Block Blobs Only
- Tiering actions (Hot → Cool → Archive): **block blobs only**; append and page blobs not supported
- When multiple actions match the same blob: **least expensive action wins** (delete < archive < cool)
- Exam tip: Lifecycle tiering → block blobs; multiple actions on same blob → cheapest wins

#### Azure File Share Snapshot Limit
- Maximum snapshots per file share: **200**
- When limit reached: new snapshots (including backups) cannot be created; delete old snapshots
- Exam tip: File share snapshot limit = **200**

#### Service SAS Revocation — Stored Access Policy Deletion
- Service SAS linked to stored access policy → **delete the policy** to immediately revoke
- No key regeneration required; only affects SAS tokens referencing that specific policy
- Exam tip: "Revoke SAS without regenerating keys" → **delete the stored access policy**

#### Blob Container Stored Access Policy Limit
- Maximum stored access policies per container: **5**
- Exam tip: Container stored access policy limit = **5**

#### Azure Files Identity Auth — One Source at a Time
- Azure Files supports only **one identity source** at a time (Entra DS or on-prem AD DS)
- To switch identity sources: **disable the existing source first**, then enable the new one
- Exam tip: Azure Files identity auth switch → **disable existing source first**

#### Azure File Sync — Cloud Change Detection
- Changes made directly to Azure file share propagate to servers on a **24-hour automatic cycle**
- To trigger immediately: run `Invoke-AzStorageSyncChangeDetection` cmdlet
- Exam tip: "Direct changes to Azure file share slow to sync" → **`Invoke-AzStorageSyncChangeDetection`**

#### Azure File Sync — Server Endpoints
- Any valid **local NTFS path** is a valid server endpoint
- No need to match other servers' paths; UNC or cloud paths are not valid
- Exam tip: File Sync server endpoint = local **NTFS path** (path name is flexible)

#### Storage Account Key Rotation Reminder (Key Expiration Policy)
- **Key expiration policy**: displays a reminder in portal when rotation interval elapses (not automatic rotation)
- Different from Key Vault rotation policy — this is a storage account native setting
- Exam tip: "Reminder when time to rotate keys" → **key expiration policy**

#### Storage Account Contributor + Blob Portal Access → Shared Key
- Portal blob access priority: if user has `listKeys` permission → **Shared Key (account access key)** used
- Storage Account Contributor includes `listKeys` → portal uses Shared Key, not Azure AD
- Storage Blob Data Reader/Contributor: no `listKeys` → Azure AD authentication
- Exam tip: Storage Account Contributor + portal blob access → **Shared Key** (not Azure AD)

#### Blob Private Endpoint DNS Zone
- Blob subresource private endpoint + DNS integration → `privatelink.blob.core.windows.net`
- Other subresources: file → `privatelink.file`, queue → `privatelink.queue`, table → `privatelink.table`
- Exam tip: Blob private endpoint DNS zone → **`privatelink.blob.core.windows.net`**

#### Private Endpoint Approval (Cross-Subscription)
- Private endpoint created from a different subscription → status `Pending` → target resource owner must **Approve**
- Approval path: target resource → Networking → Private endpoint connections → Approve
- Exam tip: Cross-subscription private endpoint `Pending` → **Approve**

#### Private DNS VNet Link Auto-Registration
- Auto-registration enabled: VM A records created automatically (per VNet setting)
- Auto-registration disabled: DNS resolution works but no auto registration
- Both VNets require a VNet link to resolve records in the zone
- Exam tip: "Auto-register + resolve-only" → separate VNet links each; **both need a link**

#### Service Endpoint Policy
- Service endpoint alone: allows access to **all** resources of that service type
- **Service endpoint policy**: restricts to only the specified resources
- Exam tip: "Service endpoint but restrict to one storage account" → **service endpoint policy**

#### Application Gateway Health Probe → Backend HTTP Settings
- Custom health probe is associated with **Backend HTTP settings** (not listener or backend pool)
- Flow: Listener → Routing Rule → **Backend HTTP Settings (probe here)** → Backend Pool
- Exam tip: Application Gateway health probe → associate with **Backend HTTP settings**

#### Application Gateway Multi-Site Listener
- Multiple hostnames on same Application Gateway IP/port → **Multi-site listener** for each hostname
- Routes based on HTTP Host header; each domain gets its own listener
- Basic listener: IP+port only; supports one site
- Exam tip: "Multiple domains on same Application Gateway" → **multi-site listener**

#### Azure Firewall Application Rule + FQDN Tag (WindowsUpdate)
- FQDN tags: auto-maintained groups of FQDNs for well-known Microsoft services
- `WindowsUpdate` FQDN tag: covers all Windows Update endpoints; Microsoft maintains the list
- Application rule (Layer 7) + FQDN tag = minimum maintenance approach
- Exam tip: "Allow Windows Update with minimal maintenance" → Application rule + **`WindowsUpdate` FQDN tag**

#### Route Table Region Constraint
- Route table must be in the **same region** as the VNet's subnets it will be associated with
- Cannot move route table between regions → must **delete and recreate** in the correct region
- Exam tip: Route table region mismatch → delete + **recreate in correct region**

#### Azure Monitor Action Group Email Rate Limit
- Email notifications: maximum **100 per hour per recipient address**
- Throttling notification sent when rate limit is applied
- Exam tip: Action Group email limit = **100/hour** (not 1,000)

#### VM Insights Minimum Components
- Basic VM Insights (performance): **Log Analytics workspace + Azure Monitor Agent**
- Map feature (processes/dependencies): additionally requires **Dependency Agent**
- Exam tip: VM Insights minimum = **workspace + Azure Monitor Agent**; Map = add Dependency Agent

#### Key Vault Secrets User Role (Minimum for App Service to Read Secrets)
- App Service (managed identity) reading Key Vault secrets via RBAC: **Key Vault Secrets User** minimum
- Key Vault Reader: metadata only, cannot read secret values
- Key Vault Contributor: control plane management; no data plane (secret read) permission
- Exam tip: Managed identity reads Key Vault secrets → **Key Vault Secrets User**

---

## Section 21 — 2026 Gap Fill: Expected Questions 3-1 and 3-2

### English

#### Governance and Identity
- `modify` handles create/update changes, but **existing** non-compliant resources still need a **remediation task**
- Role-assignable Entra groups must use **Assigned** membership; **dynamic membership isn't supported**
- Guest invitation restriction for User Administrator / Guest Inviter only: **Only users assigned to specific admin roles can invite guests**
- Least-privilege Entra role for creating users and managing user properties: **User Administrator**
- Least-privilege Entra role for creating users, resetting passwords, and managing licenses: **User Administrator**
- Bulk user create required CSV columns: **Name, User principal name, Initial password, Block sign in**

#### RBAC and Locks
- Azure RBAC is **additive**, not override-based; Contributor at a narrower scope still results in **Contributor** where it applies
- Creating resource locks requires `Microsoft.Authorization/locks/*`; least-privilege built-in role: **User Access Administrator**
- Resource locks apply to **subscriptions, resource groups, and resources**, but **not** to **management groups**
- Shared Azure dashboard requirement of view + local edits only + no publish back: assign **Reader**

#### Storage Access and SAS
- Most secure delegated Blob access SAS: **User delegation SAS**
- If rapid revocation without regenerating storage account keys is required, use **Service SAS + Stored Access Policy**
- Portal browsing for Blob containers often needs **ARM Reader** in addition to data-plane roles
- Container-only Blob read access should use **container scope** for `Storage Blob Data Reader`

#### Azure Files and Backup
- `AzFilesHybrid` joins a storage account to on-prem AD DS by creating a **computer object**
- For Azure Disk Encryption, Key Vault must allow **Azure Disk Encryption for volume encryption**
- Azure Files with AD DS + LocalSystem computer account access: use **default share-level permission for all authenticated identities**
- Azure Files SMB read/write role at share scope: **Storage File Data SMB Share Contributor**
- Snapshot-tier Azure Files backup supports **item-level restore**; deleted files back to original path → **File Recovery with Original Location**
- Azure Backup for Azure Files supports **SMB only**, not NFS
- On-demand VM backup retention is **independent** of later policy changes

#### Blob Protection and Lifecycle
- Blob overwrite with **versioning enabled** creates a **previous version**
- Lifecycle management can move blobs to **cooler** tiers, but **cannot rehydrate Archive blobs** back to Hot/Cool
- Incremental snapshots store only changed blocks since the previous snapshot

#### Networking and DNS
- **Stateful** metric alerts trigger the action group **once** while the alert remains fired
- Application Gateway + App Service backend host header issue: configure **Pick host name from backend target**
- To identify whether NSG rules allow/deny traffic and which rule applies, use **IP flow verify**
- Service endpoint policies filter traffic from **Azure subnets only**, not from **on-premises networks**
- To stop BGP-learned on-prem routes from being added automatically to a subnet, **disable BGP route propagation**
- Azure DNS supports up to **20 records per record set**
- Application Security Groups used together in an NSG rule must reference NICs from the **same VNet**
- Zone apex pointing to Azure Traffic Manager requires an **Alias** record, not CNAME
- Private endpoint created without target permissions starts in **Pending** state

#### Bastion, App Service, ACR, AKS
- Azure Bastion Standard feature for external users without portal accounts: **Shareable Link**
- Azure Bastion **Premium** is required for **session recording** and **private-only deployment**
- App Service slot validation before final swap: **Swap with preview**
- App Service traffic routing to slots stays pinned for **one hour or until cookies are deleted**
- Deployment slots require **Standard tier or higher**
- Multi-domain TLS on the same App Service IP with lower cost: **SNI SSL**
- AKS system node pool isolation taint: **`CriticalAddonsOnly=true:NoSchedule`**
- ACI multi-container groups are supported on **Linux only**
- ACR geo-replication requires **Premium tier** first
- Default availability set values: **3 fault domains, 5 update domains**

#### IaC and Monitoring
- Bicep module output syntax: **`moduleName.outputs.outputName`**
- Conditional resource deployment in Bicep uses **`if (condition)`** on the resource/module declaration
- Conditional Bicep outputs should use the **ternary operator** `condition ? trueValue : falseValue`
- ARM linked template output reference syntax: **`reference('<deploymentName>').outputs.<name>.value`**
- Custom role pattern for allow-all-except-delete: use **`Actions` + `NotActions`**
- Cost Management Reader can **view budgets only**
- Management group scope budgets do **not** support **action groups**
- VM Insights default portal onboarding collects **guest performance metrics only**
- KQL time-based line chart rendering uses **`render timechart`**

### 한국어

#### 거버넌스와 ID
- `modify`는 생성/업데이트 시점에는 변경 가능하지만, **기존** 비준수 리소스는 별도의 **Remediation Task**가 필요
- Entra 역할 할당 가능 그룹은 **Assigned** 멤버십만 사용 가능하며 **동적 멤버십은 지원되지 않음**
- User Administrator / Guest Inviter만 게스트 초대를 허용하려면 **Only users assigned to specific admin roles can invite guests** 선택
- 사용자 생성과 속성 관리의 최소 권한 역할: **User Administrator**
- 사용자 생성, 암호 재설정, 라이선스 관리까지 포함한 최소 권한 역할도 **User Administrator**
- 사용자 일괄 생성 CSV 필수 열: **Name, User principal name, Initial password, Block sign in**

#### RBAC와 잠금
- Azure RBAC는 **누적(additive)** 모델이며 override 모델이 아님; 더 좁은 범위의 Contributor는 그 범위에서 그대로 **Contributor** 유효 권한이 됨
- 잠금 생성에는 `Microsoft.Authorization/locks/*` 권한이 필요하며 최소 권한 기본 제공 역할은 **User Access Administrator**
- 리소스 잠금은 **구독, 리소스 그룹, 리소스**에는 가능하지만 **관리 그룹에는 불가**
- 공유 대시보드를 보고 로컬 편집만 가능하고 원본 게시가 불가해야 하면 **Reader** 역할 사용

#### 스토리지 액세스와 SAS
- 가장 안전한 Blob 위임 SAS: **User delegation SAS**
- 저장소 계정 키 재생성 없이 빠른 취소가 필요하면 **Service SAS + Stored Access Policy** 사용
- Azure Portal에서 Blob 컨테이너 탐색에는 데이터 평면 역할 외에 **ARM Reader**가 추가로 필요할 수 있음
- 특정 컨테이너만 읽게 하려면 `Storage Blob Data Reader`를 **컨테이너 범위**에 할당

#### Azure Files와 백업
- `AzFilesHybrid`는 스토리지 계정을 온프레미스 AD DS에 조인할 때 기본적으로 **컴퓨터 객체**를 생성
- Azure Disk Encryption 전제 조건: Key Vault에서 **Azure Disk Encryption for volume encryption** 허용
- Azure Files + AD DS + LocalSystem 컴퓨터 계정 접근의 최소 설정 해법: **모든 인증된 ID에 대한 기본 share-level permission**
- Azure Files SMB 읽기/쓰기 역할: **Storage File Data SMB Share Contributor**
- Snapshot tier Azure Files 백업은 **파일 단위 복원** 지원; 삭제 파일을 원래 위치로 되돌리려면 **File Recovery with Original Location**
- Azure Files 백업은 **SMB만** 지원하고 NFS는 지원하지 않음
- VM의 on-demand 백업 보존 기간은 이후 정책 변경과 **독립적**임

#### Blob 보호와 수명 주기
- Blob versioning이 켜진 상태에서 덮어쓰기를 하면 기존 내용은 **previous version**으로 저장됨
- Lifecycle management는 더 차가운 계층으로 이동만 지원하며 **Archive에서 Hot/Cool로 rehydrate는 지원하지 않음**
- Incremental snapshot은 이전 스냅샷 이후 **변경된 블록만** 저장

#### 네트워킹과 DNS
- **Stateful** 메트릭 경보는 경보 상태가 유지되는 동안 action group을 **한 번만** 실행
- Application Gateway 뒤 App Service의 호스트 헤더 문제는 **Pick host name from backend target**으로 해결
- NSG가 특정 트래픽을 허용/거부하는지와 어떤 규칙이 적용되는지 바로 보려면 **IP flow verify** 사용
- Service endpoint policy는 **Azure 서브넷에서 시작한 트래픽만** 필터링하며 **온프레미스 트래픽에는 적용되지 않음**
- BGP로 학습한 온프레미스 경로가 자동으로 들어오지 않게 하려면 **BGP route propagation 비활성화**
- Azure DNS 레코드 집합 최대 레코드 수는 **20개**
- 같은 NSG 규칙에서 함께 쓰는 ASG의 NIC는 **같은 VNet**에 있어야 함
- Zone apex에서 Azure Traffic Manager를 가리키려면 **Alias 레코드** 사용
- 권한 없이 생성된 private endpoint 연결의 초기 상태는 **Pending**

#### Bastion, App Service, ACR, AKS
- 포털 계정 없는 외부 사용자에게 Bastion으로 안전하게 접속시키려면 **Shareable Link** 사용
- **Session recording**과 **private-only deployment**는 Azure Bastion **Premium** 필요
- App Service 슬롯의 사전 검증 교체는 **Swap with preview** 사용
- App Service 슬롯 트래픽 고정은 **1시간 또는 쿠키 삭제 시까지** 유지
- Deployment slot은 **Standard 이상** App Service plan 필요
- 같은 IP에서 여러 사용자 지정 도메인의 TLS를 저비용으로 처리하려면 **SNI SSL** 사용
- AKS 시스템 노드 풀 격리 taint는 **`CriticalAddonsOnly=true:NoSchedule`**
- ACI multi-container group은 **Linux만** 지원
- ACR geo-replication을 쓰려면 먼저 **Premium tier**로 업그레이드해야 함
- 기본 Availability Set 값: **Fault domains 3, Update domains 5**

#### IaC와 모니터링
- Bicep 모듈 출력 참조 구문: **`moduleName.outputs.outputName`**
- Bicep 조건부 리소스 배포는 리소스/모듈 선언에서 **`if (condition)`** 사용
- Bicep 조건부 출력은 **삼항 연산자** `condition ? trueValue : falseValue` 사용
- ARM linked template 출력 참조 구문: **`reference('<deploymentName>').outputs.<name>.value`**
- 사용자 지정 역할에서 전체 허용 후 delete만 제외하는 패턴은 **`Actions` + `NotActions`**
- Cost Management Reader는 **예산 조회만 가능**
- 관리 그룹 범위 예산은 **action group 미지원**
- VM Insights 기본 포털 온보딩은 **guest performance metrics only** 수집
- KQL 시간 기반 선형 차트 시각화는 **`render timechart`** 사용

## Section 22 — 2026 Gap Fill: Expected Questions Set 8

### English

#### Custom Script Extension — Publisher Parameter
- **`-Publisher`** parameter identifies the VM extension provider (e.g., Windows: `Microsoft.Compute`, Linux: `Microsoft.Azure.Extensions`)
- Exam focus: "which parameter identifies the extension source" → **`-Publisher`** (not `-ExtensionName`, not `-Location`)

#### Custom Script Extension — Recurring Startup Scripts
- Custom Script Extension runs **once at deployment time** — does NOT re-execute on VM restart
- For scripts that must run on **every VM boot** (Windows): use **Windows Task Scheduler** with a startup trigger
- Azure Automation is technically possible but over-engineered for this specific scenario

#### Azure Site Recovery — Recovery Plan Startup Order
- Use **Groups** in a Recovery Plan to control VM startup order during failover
- Lower group number starts first; VMs within the same group start in **parallel**
- Common distractors: "Priority", "Dependency", "Availability Set" — none of these control startup order in ASR

#### Entra ID Dynamic Groups — Guest Filter
- Correct rule for guest-only dynamic group: `(user.userType -eq "Guest")`
- Key attribute: **`user.userType`** — built-in, reliable
- Wrong answers: `user.companyName` (unreliable), `user.mail` (not exact), `user.objectType` (not a valid attribute)

#### VM Insights — Prerequisite (Two Versions to Know)
- **General scenario**: VM Insights stores data in a **Log Analytics workspace**
- **Azure Policy scenario**: prerequisite before assigning the policy initiative is a **VM Insights Data Collection Rule (DCR)**, not the workspace
- DCR defines what data to collect and how the data stream is configured
- Distractors: Application Insights (APM only), Storage account, Azure Monitor workspace (Prometheus)

#### Azure Files — AD DS Authentication
- **`Join-AzStorageAccount`** creates a **computer account** in AD DS by default
- Rationale: storage account is treated as a server-style network resource for Kerberos/SMB
- Optional: specify `-DomainAccountType ServiceLogonAccount` to create a service logon account instead
- Not supported: group managed service accounts (gMSAs)

#### Azure SQL Database — PITR Retention by Tier
- **Basic tier (DTU model): maximum 7 days** — this is the key exception to memorize
- Standard, Premium (DTU); General Purpose, Business Critical (vCore): maximum **35 days**
- 10 years = Long-term retention (LTR), a separate feature — not PITR
- Never apply the 35-day rule universally; always check for Basic tier exceptions

#### Azure DNS — Multi-Value A Record
- Create a **single A record set** for `www` with multiple IP values to return all IPs for one hostname
- This is basic DNS round-robin, not true load balancing — no health checks, failover, or session affinity
- CNAME cannot return multiple IPs directly
- For production-grade distribution: use Azure Load Balancer or Traffic Manager

#### Azure Backup — Recovery Services Vault Region
- Azure Backup requires the Recovery Services vault to be in the **same region** as the protected resource
- Azure File Share backup: vault must be in the same region as the storage account
- Contrast: **Azure Site Recovery** uses **cross-region** replication — different rule, but the same vault family name

#### Azure VM Backup — Instant Restore Snapshot Retention
- Valid range: **1 to 5 days**
- Local snapshots stored near the VM for faster restore operations
- Do not confuse with: vault retention (years), SQL PITR (up to 35 days), SQL LTR (up to 10 years)

#### AKS Spot Node Pool — Deallocate vs Delete Eviction Policy
- **Deallocate** policy: evicted VMs go to `Stopped (deallocated)` state — they **still consume compute quota**
- This prevents AKS autoscaler from creating new nodes → cluster scaling fails
- **Delete** policy: evicted VMs are removed completely → quota is released → autoscaling works normally
- Prefer **Delete** for Spot node pools unless preserving the stopped VM resource is specifically required

#### Azure Container Registry — Least Privilege for CI/CD
- **AcrPush** = pull + push → correct least-privilege role for CI/CD pipelines
- AcrPull = pull only (insufficient for push operations)
- Contributor = full registry management (over-privileged)

#### Azure RBAC — NotDataActions and Additive Model
- Azure RBAC is an **additive (union) model** — permissions from all roles are combined
- **`NotDataActions`** only subtracts permissions **within the same role definition** — it is NOT a global deny
- If a second role grants a permission excluded by `NotDataActions` in another role, the user CAN perform that action
- This differs from AWS IAM, where explicit Deny always overrides Allow

#### Bicep Module Scope
- Modules in a subscription-scoped Bicep file inherit subscription scope by default
- To deploy a module to a specific resource group: use **`scope: resourceGroup('ResourceGroupName')`**
- The `resourceGroup()` function returns a scope object for the specified RG

---

### 한국어

#### Custom Script Extension — Publisher 파라미터
- **`-Publisher`**: VM 확장 게시자 식별 파라미터 (Windows: `Microsoft.Compute`, Linux: `Microsoft.Azure.Extensions`)
- 시험 포인트: "확장 소스를 식별하는 파라미터" → **`-Publisher`** (`-ExtensionName`, `-Location` 아님)

#### Custom Script Extension — 반복 시작 스크립트
- Custom Script Extension은 **배포 시점에 한 번만** 실행 — VM 재시작 시 재실행 안 됨
- **매 부팅 시마다 실행**이 필요한 경우(Windows): 시작 트리거가 있는 **Windows Task Scheduler** 사용
- Azure Automation도 가능하지만 이 시나리오에서는 과도한 솔루션

#### Azure Site Recovery — Recovery Plan 실행 순서
- 장애 조치 시 VM 시작 순서 제어: Recovery Plan의 **그룹(Group)** 사용
- 낮은 그룹 번호가 먼저 시작; 같은 그룹의 VM은 **병렬** 시작
- 자주 나오는 함정: "Priority", "Dependency", "Availability Set" — ASR 실행 순서 제어와 무관

#### Entra ID 동적 그룹 — 게스트 필터
- 게스트 전용 동적 그룹의 올바른 규칙: `(user.userType -eq "Guest")`
- 핵심 속성: **`user.userType`** — 기본 제공, 신뢰할 수 있음
- 오답: `user.companyName` (신뢰 불가), `user.mail` (정확하지 않음), `user.objectType` (유효하지 않은 속성)

#### VM Insights — 선행 조건 (두 가지 버전 구분 필수)
- **일반 시나리오**: VM Insights 데이터 저장 위치 → **Log Analytics workspace**
- **Azure Policy 시나리오**: 정책 이니셔티브 할당 전 선행 조건 → **VM Insights DCR (데이터 수집 규칙)** (workspace 아님)
- DCR: 수집할 데이터와 데이터 스트림 구성 방법 정의
- 함정: Application Insights (APM용), Storage account, Azure Monitor workspace (Prometheus용)

#### Azure Files — AD DS 인증
- **`Join-AzStorageAccount`**는 기본적으로 AD DS에 **컴퓨터 계정** 생성
- 이유: 스토리지 계정이 Kerberos/SMB용 서버형 네트워크 리소스로 취급됨
- 선택 사항: `-DomainAccountType ServiceLogonAccount` 지정 시 서비스 로그온 계정 생성 가능
- 지원 안 됨: 그룹 관리 서비스 계정(gMSA)

#### Azure SQL Database — Tier별 PITR 보존 기간
- **Basic tier (DTU 모델): 최대 7일** — 반드시 암기할 예외 규칙
- Standard, Premium (DTU); General Purpose, Business Critical (vCore): 최대 **35일**
- 10년 = LTR(장기 보존), 별도 기능 — PITR 아님
- "35일" 규칙을 모든 tier에 적용하지 말 것; 항상 Basic tier 예외 확인

#### Azure DNS — 다중 값 A 레코드
- `www`에 대한 **단일 A 레코드 세트**에 여러 IP를 추가하면 하나의 호스트명에서 모든 IP 반환 가능
- 기본 DNS 라운드 로빈이며 실제 부하 분산이 아님 — 상태 확인, 장애 조치, 세션 선호도 없음
- CNAME은 여러 IP를 직접 반환 불가
- 운영 수준 분산: Azure Load Balancer 또는 Traffic Manager 사용

#### Azure Backup — Recovery Services Vault 지역
- Azure Backup은 Recovery Services vault가 보호 대상 리소스와 **같은 지역**에 있어야 함
- Azure File Share 백업: vault는 스토리지 계정과 같은 지역
- 비교: **Azure Site Recovery**는 **교차 지역** 복제 사용 — 규칙이 다르지만 vault 이름이 같아서 혼동 주의

#### Azure VM 백업 — 즉시 복원 스냅샷 보존 기간
- 유효 범위: **1일~5일**
- VM 근처에 로컬로 저장된 스냅샷으로 더 빠른 복원 작업 지원
- 혼동 금지: vault 보존 기간(수년), SQL PITR(최대 35일), SQL LTR(최대 10년)

#### AKS Spot 노드 풀 — Deallocate 대 Delete 제거 정책
- **Deallocate** 정책: 제거된 VM이 `Stopped (deallocated)` 상태로 유지 — **컴퓨팅 할당량 계속 소모**
- AKS 자동 크기 조정기가 새 노드를 만들 수 없음 → 클러스터 스케일링 실패
- **Delete** 정책: 제거된 VM 완전 삭제 → 할당량 반환 → 자동 크기 조정 정상 작동
- Spot 노드 풀에는 **Delete** 권장 (중지된 VM 리소스 보존이 특별히 필요한 경우 제외)

#### Azure Container Registry — CI/CD 최소 권한
- **AcrPush** = pull + push → CI/CD 파이프라인의 올바른 최소 권한 역할
- AcrPull = pull만 가능 (push 작업에 부족)
- Contributor = 전체 레지스트리 관리 (과권한)

#### Azure RBAC — NotDataActions와 누적 모델
- Azure RBAC는 **누적(합집합) 모델** — 모든 역할의 권한이 합산됨
- **`NotDataActions`**는 **동일 역할 정의 내에서만** 권한 제외 — 전역 거부가 아님
- 다른 역할이 `NotDataActions`로 제외된 권한을 부여하면 사용자는 해당 작업 수행 가능
- AWS IAM에서 명시적 거부가 항상 허용을 재정의하는 것과 다름

#### Bicep 모듈 범위
- 구독 범위 Bicep 파일의 모듈은 기본적으로 구독 범위 상속
- 특정 리소스 그룹에 모듈 배포: **`scope: resourceGroup('리소스그룹이름')`** 사용
- `resourceGroup()` 함수는 지정된 RG에 대한 범위 객체를 반환

*Document generated from AZ-104 course transcript. Last updated: 2026-05-17*

---

## Section 23 — 2026 Gap Fill: Expected Questions Set 9

### English

#### Identity, Governance, and RBAC
- **RBAC Additive Model**: permissions are the union of all role assignments; a more restrictive role at a lower scope does not reduce a broader permission from a higher scope
- **DataActions vs Actions**: blob/file data access uses `DataActions`; storage account management uses `Actions`
- **NotActions override Actions**: even when a wildcard `Actions` is assigned, `NotActions` always removes the listed operations
- **Dynamic Device Group**: only Security groups support Dynamic Device membership — not Microsoft 365 groups
- **Dynamic Group Licensing**: every unique member of a dynamic group requires Microsoft Entra ID Premium P1
- **Dynamic Rule `-in` Operator**: most efficient for multiple OR conditions on the same attribute (e.g., `user.department -in ["Sales","Marketing"]`)
- **Management Group Subscription Move**: inherited Owner on a subscription counts; you need Owner on the target management group to move

#### Azure Policy and Governance
- **`notScopes` property**: permanently excludes a specific scope from policy assignment evaluation without creating a separate resource (unlike Policy Exemption)
- **Policy Mode `Indexed`**: evaluates only resource types that support tags and location; non-taggable resources are automatically excluded
- **Policy Initiative (Policy Set)**: bundles multiple policy definitions; supports shared parameters; applied via a single assignment
- **Policy Definition Scope**: save at management group scope for tenant-wide availability across all subscriptions

#### Storage, SAS, and Lifecycle Management
- **GPv1 → GPv2 required for Private Endpoint**: GPv1 accounts do not support Private Endpoints; upgrade first
- **SAS Token IP Restriction**: supports only publicly routable IP addresses; private IPs (RFC 1918) are not valid
- **Archive Tier Lifecycle**: only block blobs can be tiered to Archive; page blobs and append blobs are not supported
- **Cool Tier Early Deletion Penalty**: moving/deleting a blob from Cool before 30 days triggers a prorated penalty for remaining days
- **Key Rotation Reminder greyed out**: `keyCreationTime` is null until each key is rotated at least once; rotate both keys first
- **Azure Files SMB RBAC**: `Storage File Data SMB Share Contributor` = read/write/delete; `Elevated Contributor` adds permission management

#### Networking: NSG, ASG, Load Balancer, and Routing
- **NSG Priority Evaluation**: lower number = higher priority; first-match wins; a Deny at priority 100 blocks traffic before an Allow at priority 200
- **NSG Priority Range**: custom rules use 100–4096; 100 is the maximum-priority (lowest number) allowed for user-defined rules
- **ASG Cross-Subnet**: an ASG can span subnets within the same VNet; it cannot span different VNets
- **Load Balancer Floating IP (DSR)**: enables multiple rules to share the same backend port; preserves the original destination IP on packets reaching backend instances
- **UDR Priority**: UDR > BGP > System route for the same prefix; for different prefix lengths, more specific prefix always wins regardless of route type
- **Dual NSG (Subnet + NIC)**: inbound traffic must pass both NSGs; neither "most permissive wins" nor "NIC only" — both must allow

#### Compute: VMs, App Service, and Containers
- **App Service Deployment Slots**: not available on Free or Basic tiers; scale up to Standard (min 5 slots) first
- **App Service Free Tier**: no custom domain, no SSL/TLS; scale up to Basic before configuring either
- **App Service Managed Certificate**: no wildcard support; auto-renewed by platform; no Key Vault required
- **App Service SNI SSL**: allows multiple certificates on a single IP; IP-based SSL requires one dedicated IP per certificate
- **ACR SKU Upgrade for Geo-replication**: `az acr update --sku Premium` — geo-replication is Premium-only
- **ACI Subnet Delegation**: subnet must be delegated to `Microsoft.ContainerInstance/containerGroups` before deploying a container group into a VNet

#### Azure Site Recovery and Backup
- **ASR Commit Failover**: permanently deletes all recovery points for that VM; cannot revert; reprotect is required to resume replication
- **ASR Reprotection Prerequisite**: VM status must be `Failover committed` before the Reprotect option becomes available
- **Azure Backup File Share**: maximum 6 scheduled snapshots per day (minimum 4-hour interval between each)

#### Azure Monitor, Workbooks, and Service Health
- **Azure Monitor Alert Severity**: 0 = Critical (highest severity); scale is 0–4 (lower number = more severe)
- **Azure Workbooks Data Source**: use **Azure Resource Graph** for resource inventory/metadata; use **Metrics** for numerical performance time-series
- **Service Health Health History**: inactive (resolved) events are retained for 90 days
- **VM Disk Host Caching**: transaction log disk → `None`; data files disk → `ReadOnly`

#### Infrastructure as Code, Networking, and Miscellaneous
- **Bicep `existing` keyword**: reference a deployed resource without re-creating it; syntax: `resource x '...' existing = { name: '...' }`
- **ARM Linked Template Mode**: linked templates support `Incremental` only; `Complete` mode is not supported
- **Bicep Module Output**: referenced as `moduleName.outputs.outputName`
- **Bicep Conditional Resource**: `resource x '...' = if (condition) { ... }`
- **Application Gateway Path-Based Routing**: uses a URL Path Map within a single routing rule; no separate listener needed per path
- **App Service VNet Integration + Private Endpoints**: delegated subnet (`Microsoft.Web/serverFarms`) cannot contain Private Endpoints — use separate subnets
- **Service Health Action Group Region**: must be set to **Global**
- **Container Soft Delete Restore**: an active container with the same name blocks restore; delete the new container first, then restore the soft-deleted one
- **B2B Invitation Status**: check `externalUserState` property in Microsoft Graph (not `userType`, which only distinguishes Guest vs Member)
- **VM Availability Zone Migration**: use the **Availability + scaling** blade in Azure Portal (not Availability Set)
- **Storage Blob Portal Access**: data-plane role alone is insufficient; `Reader` (ARM management plane) is also needed for portal navigation
- **SSPR Admin Restriction**: Global Admins cannot use security questions as an SSPR authentication method
- **Azure File Sync Cloud Tiering Minimum File Size**: `max(2 × cluster size, 8 KiB)`; with 4 KiB cluster → 8 KiB minimum
- **Azure Firewall Rule Processing Order**: DNAT rules → Network rules → Application rules (type precedence overrides priority numbers within a type)

---

### 한국어

#### ID, 거버넌스, RBAC
- **RBAC 누적 모델**: 권한은 모든 역할 할당의 합집합; 하위 범위의 더 제한적인 역할은 상위 범위의 더 넓은 권한을 줄이지 않음
- **DataActions vs Actions**: Blob/파일 데이터 접근은 `DataActions`, 스토리지 관리는 `Actions`
- **NotActions는 Actions를 재정의**: 와일드카드 `Actions`가 있어도 `NotActions`는 항상 해당 작업을 제거
- **동적 디바이스 그룹**: 동적 디바이스 멤버십은 보안 그룹만 지원 — Microsoft 365 그룹은 불가
- **동적 그룹 라이선스**: 동적 그룹의 각 고유 멤버에게 Microsoft Entra ID Premium P1 필요
- **동적 규칙 `-in` 연산자**: 동일 속성의 여러 OR 조건에 가장 효율적 (예: `user.department -in ["Sales","Marketing"]`)
- **관리 그룹 구독 이동**: 상속된 Owner도 유효한 권한; 대상 관리 그룹에 Owner 역할 필요

#### Azure Policy 및 거버넌스
- **`notScopes` 속성**: 별도 리소스 없이 특정 범위를 정책 평가에서 영구 제외 (Policy Exemption과 달리)
- **정책 모드 `Indexed`**: 태그와 위치를 지원하는 리소스 유형만 평가; 태그 미지원 리소스 자동 제외
- **정책 이니셔티브(정책 집합)**: 여러 정책 번들; 공유 매개변수 지원; 단일 할당으로 적용
- **정책 정의 범위**: 테넌트 전체 가용성을 위해 관리 그룹 범위에 저장

#### 스토리지, SAS, 수명 주기 관리
- **Private Endpoint에 GPv2 필요**: GPv1은 Private Endpoint 미지원; 먼저 업그레이드 필요
- **SAS 토큰 IP 제한**: 공용 라우팅 가능 IP만 지원; 프라이빗 IP(RFC 1918)는 사용 불가
- **Archive 계층 수명 주기**: 블록 Blob만 Archive로 전환 가능; 페이지 Blob, 추가 Blob 미지원
- **Cool 계층 조기 삭제 페널티**: 30일 이전 삭제/이동 시 남은 일수에 대한 비례 요금 발생
- **키 순환 미리 알림 회색 표시**: `keyCreationTime`이 null인 경우 발생; 각 키를 최소 한 번 순환해야 함
- **Azure Files SMB RBAC**: `SMB 공유 참여자` = 읽기/쓰기/삭제; `Elevated 참여자`는 권한 관리 추가

#### 네트워킹: NSG, ASG, Load Balancer, 라우팅
- **NSG 우선순위 평가**: 낮은 숫자 = 높은 우선순위; 첫 번째 일치 규칙 적용; 우선순위 100의 Deny가 200의 Allow보다 먼저 평가
- **NSG 우선순위 범위**: 사용자 정의 규칙은 100–4096; 100이 가장 높은 우선순위
- **ASG 교차 서브넷**: 동일 VNet 내 서브넷 간 ASG 사용 가능; 다른 VNet 간은 불가
- **Load Balancer 부동 IP(DSR)**: 여러 규칙이 동일한 백엔드 포트 공유 가능; 백엔드 인스턴스에서 원래 대상 IP 보존
- **UDR 우선순위**: 동일 접두사에서 UDR > BGP > 시스템 경로; 다른 접두사 길이 간에는 더 구체적인 접두사가 항상 우선
- **이중 NSG(서브넷 + NIC)**: 인바운드 트래픽이 두 NSG를 모두 통과해야 함; '더 관대한 것 우선' 논리 없음

#### Compute: VM, App Service, 컨테이너
- **App Service 배포 슬롯**: Free 또는 Basic 계층에서 사용 불가; Standard 이상으로 스케일 업 필요
- **App Service Free 계층**: 사용자 지정 도메인, SSL/TLS 미지원; Basic으로 스케일 업 필요
- **App Service 관리 인증서**: 와일드카드 미지원; 플랫폼이 자동 갱신; Key Vault 불필요
- **App Service SNI SSL**: 단일 IP에 여러 인증서 허용; IP 기반 SSL은 인증서당 전용 IP 필요
- **ACR 지역 복제 SKU**: `az acr update --sku Premium`; 지역 복제는 Premium 전용
- **ACI 서브넷 위임**: 컨테이너 그룹 배포 전 `Microsoft.ContainerInstance/containerGroups`에 위임 필요

#### Azure Site Recovery 및 Backup
- **ASR 장애 조치 커밋**: VM의 모든 복구 지점 영구 삭제; 되돌릴 수 없음; 재보호 필요
- **ASR 재보호 선행 조건**: VM 상태가 `Failover committed`여야 재보호 옵션 활성화
- **Azure Backup 파일 공유**: 하루 최대 6회 예약 스냅샷(최소 4시간 간격)

#### Azure Monitor, Workbook, Service Health
- **Azure Monitor 경고 심각도**: 0 = Critical(가장 높은 심각도); 범위 0–4(낮은 숫자 = 더 심각)
- **Azure Workbook 데이터 소스**: 리소스 인벤토리/메타데이터 → **Azure Resource Graph**; 수치 성능 시계열 → **Metrics**
- **Service Health 기록 보존**: 비활성(해결된) 이벤트 90일 보관
- **VM 디스크 호스트 캐싱**: 트랜잭션 로그 디스크 → `없음`; 데이터 파일 디스크 → `읽기 전용`

#### IaC, 네트워킹, 기타
- **Bicep `existing` 키워드**: 재생성 없이 배포된 리소스 참조; 구문: `resource x '...' existing = { name: '...' }`
- **ARM 연결된 템플릿 모드**: `Incremental`만 지원; `Complete` 모드 미지원
- **Bicep 모듈 출력 참조**: `모듈이름.outputs.출력속성이름`
- **Bicep 조건부 리소스**: `resource x '...' = if (조건) { ... }`
- **Application Gateway 경로 기반 라우팅**: 단일 라우팅 규칙 내 URL 경로 맵 사용; 경로별 별도 수신기 불필요
- **App Service VNet 통합 + Private Endpoint**: 위임된 서브넷(`Microsoft.Web/serverFarms`)에는 Private Endpoint 불가 — 별도 서브넷 사용
- **Service Health 작업 그룹 지역**: **Global**로 설정 필요
- **컨테이너 일시 삭제 복원**: 동일 이름의 활성 컨테이너가 복원을 차단; 새 컨테이너 먼저 삭제 후 복원
- **B2B 초대 상태**: Microsoft Graph의 `externalUserState` 속성 확인 (`userType`은 Guest/Member 구분만)
- **VM 가용성 영역 마이그레이션**: Azure Portal의 **가용성 및 확장** 블레이드 사용 (가용성 집합 아님)
- **스토리지 Blob 포털 접근**: 데이터 플레인 역할만으로는 부족; 포털 탐색을 위해 `Reader`(ARM 관리 플레인) 역할도 필요
- **SSPR 관리자 제한**: 전역 관리자는 SSPR 인증 방법으로 보안 질문 사용 불가
- **Azure File Sync 클라우드 계층화 최소 파일 크기**: `max(2 × 클러스터 크기, 8 KiB)`; 4 KiB 클러스터 → 최소 8 KiB
- **Azure Firewall 규칙 처리 순서**: DNAT 규칙 → 네트워크 규칙 → 애플리케이션 규칙 (유형 우선순위가 동일 유형 내 숫자 우선순위를 재정의)


## Section 24 — 2026 Gap Fill: Expected Questions Set 10

### English

#### Identity, RBAC, and Microsoft Graph
- **RBAC group assignment count**: assigning a role to a security group of 100 users creates **1** assignment (not 100); total increases by 1
- **DataActions vs Actions**: blob data read = `DataActions`; storage account management = `Actions`
- **Microsoft Graph permission scope**: `User.ReadWrite.All` for creating and updating Entra ID users; `Directory.ReadWrite.All` is too broad
- **Contributor role**: `Actions: *` with `NotActions` excluding `Microsoft.Authorization/*/Write` — manages all resources but cannot assign roles
- **User delegation SAS**: signed with Entra ID credentials (not account key); most secure SAS type for blob access
- **Key Vault Secrets User**: minimum role for App Service managed identity to read secrets via Key Vault references; `Key Vault Reader` only gives metadata access
- **CanNotDelete lock inheritance**: lock applied to a resource group is automatically inherited by all resources within the group
- **SSPR + MFA combined registration**: users register authentication methods **once**; those methods work for both MFA challenges and SSPR recovery

#### Governance
- **Management Group maximum depth**: **6 levels** nested below root (root management group and subscriptions are excluded from the count)
- **Default Management Group setting**: configure in Management Groups → Settings to auto-place new subscriptions in a target MG instead of root
- **Dynamic group license requirement**: dynamic membership groups require **Entra ID Premium P1**; not available in Free tier

#### Storage, SAS, and Lifecycle
- **Azure Files vaulted backup**: supports **full share recovery to alternate location only**; original location restore (OLR) and file-level recovery are not supported from vault-tier recovery points
- **Azure Files authentication — one method at a time**: only one identity-based auth source per storage account; must disable current method before enabling a new one
- **Azure Files backup — 200 snapshot limit**: Azure Backup for Files uses share snapshots; max 200 snapshots per share; reaching this limit causes backup job failure
- **Lifecycle management — least expensive action wins**: when delete + archive + cool conditions all apply simultaneously, Azure selects **delete** (least expensive); cost ranking: delete < archive < cool
- **Last access time tracking prerequisite**: must be enabled before using `daysAfterLastAccessTimeGreaterThan` condition or `enableAutoTierToHotFromCool` property in lifecycle policies
- **Cool → Cold tier minimum**: blobs must remain in Cool tier for at least **30 days** before moving to Cold to avoid early deletion fees
- **Blob soft delete recovery**: use the **Undelete Blob** operation to recover soft-deleted blobs within retention period (versioning not required)
- **ARM Complete mode**: deploys template and **deletes all resources in the resource group not defined in the template**; entire RG is synchronised to match the template

#### Azure File Sync
- **Eligible volumes for cloud tiering**: only **NTFS non-system volumes**; ReFS is not supported; system volume (C:) is not eligible
- **Multiple server endpoints — highest free space wins**: when multiple server endpoints share a volume, the **most restrictive (highest) free space policy** is applied across all endpoints
- **Cloud tiering recall mechanism**: tiered files are replaced locally with placeholder stubs; when accessed, the file is **automatically recalled from Azure** transparently

#### Networking
- **NAT Gateway for ACI in VNet**: VNet-deployed container groups require a **NAT Gateway** on the subnet for outbound internet access
- **NSG inbound evaluation order**: subnet NSG first → NIC NSG
- **NSG outbound evaluation order**: NIC NSG first → subnet NSG
- **Subnet delegation — no private endpoints**: a delegated subnet is exclusive to the delegated service; private endpoints cannot be deployed in a delegated subnet
- **Disable BGP route propagation**: configure on the route table to prevent a subnet from learning BGP-advertised on-premises routes (VPN connection and BGP session remain active for other subnets)
- **ASG cross-subnet**: ASG members can be in different subnets within the **same VNet**; cross-VNet ASG is not supported
- **Azure DNS ALIAS record at zone apex**: CNAME not allowed at zone apex (conflicts with SOA/NS); use Azure DNS **ALIAS A record** to point apex to Traffic Manager or other Azure resources without a static IP
- **Azure Firewall Threat Intelligence default mode**: default is **Alert only** (logs but does not block); change to **Alert and deny** to both log and block malicious traffic
- **Private DNS Zone — Virtual Network Link**: required to associate a Private DNS Zone with a VNet so resources in the VNet can resolve private endpoint DNS records

#### Compute, AKS, App Service
- **Availability Zones — 99.99% SLA**: deploying VMs across 2+ Availability Zones provides datacenter-level fault isolation and qualifies for 99.99% compute SLA
- **Log Analytics Reader**: read-only access to query log data; does not allow modifying workspace settings
- **App Service Key Vault reference refresh**: App Service caches secret values; maximum refresh interval is **24 hours**; restart forces immediate refresh
- **AKS Windows node pool**: requires **Standard SKU load balancer** (Basic SKU does not support multiple node pools)
- **ACR AcrPush role**: includes both push and pull permissions; minimum privilege for CI/CD pipelines
- **Azure Bastion Standard SKU**: minimum SKU required for **custom port** support (Basic only supports default RDP 3389 and SSH 22)
- **Kubernetes request/limit vs ACI**: Kubernetes → request = guaranteed minimum, limit = hard cap; ACI → request = effective maximum (no separate limit)
- **Logic App for budget automation**: Logic App with built-in connectors (Slack, ITSM, etc.) triggered by Budget alert actions; no custom code required

#### Infrastructure as Code
- **ARM linked template resource type**: `Microsoft.Resources/deployments` with `templateLink` property
- **Bicep conditional output (ternary)**: `output x string = condition ? resource.property : ''`
- **Azure Resource Graph in Workbooks**: correct data source for cross-subscription VM inventory queries (tags, locations, resource types)
- **Workbook Contributor role**: provides `microsoft.insights/workbooks/write` permission; more scoped than Monitoring Contributor for workbook-only edit access

#### Monitoring and Operations
- **VM Insights data storage**: all performance and dependency data stored in **Log Analytics workspace**
- **Key Vault reference — no version specified**: App Service fetches **latest active version** automatically; enables seamless secret rotation
- **SSPR combined registration benefit**: register once → methods available for both MFA and SSPR
- **Azure Firewall TI mode — Alert and deny**: explicitly required to block (not just alert) traffic from known malicious IPs/domains
- **ACI CPU request = maximum**: in ACI, `request` is the effective maximum; no separate limit concept
- **RBAC subscription limit**: maximum **4,000** role assignments per subscription; assigning to groups conserves assignment quota

---

### 한국어

#### ID, RBAC, Microsoft Graph
- **RBAC 그룹 할당 수**: 100명 포함 보안 그룹에 역할 할당 시 **1개** 역할 할당 생성 (100개 아님)
- **DataActions vs Actions**: 블로브 데이터 읽기 = `DataActions`; 스토리지 계정 관리 = `Actions`
- **Microsoft Graph 권한 범위**: 사용자 생성·업데이트 = `User.ReadWrite.All`; `Directory.ReadWrite.All`은 범위 과다
- **Contributor 역할**: `Actions: *` + `NotActions`에 `Microsoft.Authorization/*/Write` 제외 — 리소스 관리 O, 역할 할당 X
- **사용자 위임 SAS**: Entra ID 자격 증명으로 서명 (계정 키 불필요); 가장 안전한 블로브 SAS 유형
- **Key Vault Secrets User**: 관리 ID가 App Service에서 Key Vault 참조로 비밀을 읽는 최소 권한 역할; `Key Vault Reader`는 메타데이터만
- **CanNotDelete 잠금 상속**: 리소스 그룹에 적용된 잠금은 내부 모든 리소스에 자동 상속
- **SSPR + MFA 통합 등록**: 인증 방법 **한 번 등록** → MFA와 SSPR 모두 사용 가능

#### 거버넌스
- **관리 그룹 최대 깊이**: 루트 아래 **6단계** (루트 관리 그룹과 구독은 깊이 계산 제외)
- **기본 관리 그룹 설정**: 관리 그룹 → 설정에서 구성; 새 구독이 루트 대신 지정 MG에 자동 배치
- **동적 그룹 라이선스 요구사항**: 동적 멤버십 그룹은 **Entra ID Premium P1** 필요; Free 티어 미지원

#### 스토리지, SAS, 수명 주기 관리
- **Azure Files 볼트 백업**: **대체 위치로의 전체 공유 복구만** 지원; OLR 및 파일 수준 복구 미지원
- **Azure Files 인증 — 한 번에 하나**: 스토리지 계정당 하나의 ID 기반 인증 방법만 지원; 변경 전 기존 방법 비활성화 필요
- **Azure Files 백업 — 스냅샷 200개 한도**: Azure Backup이 공유 스냅샷 사용; 최대 200개 도달 시 백업 실패
- **수명 주기 관리 — 가장 저렴한 작업 우선**: 삭제·보관·쿨 조건이 동시에 true → **삭제** 선택 (비용 순위: 삭제 < 보관 < 쿨)
- **마지막 접근 시간 추적 전제조건**: `daysAfterLastAccessTimeGreaterThan` 및 `enableAutoTierToHotFromCool` 사용 전 활성화 필수
- **쿨→콜드 계층 최소 기간**: 조기 삭제 요금 방지를 위해 최소 **30일** 쿨 계층 유지 필요
- **블로브 일시 삭제 복구**: 보존 기간 내 **Undelete Blob 작업** 호출 (버전 관리 불필요)
- **ARM Complete 모드**: 리소스 그룹을 템플릿과 완전 동기화; **템플릿에 없는 리소스는 모두 삭제**

#### Azure File Sync
- **클라우드 계층화 가능 볼륨**: **NTFS 비시스템 볼륨만** 지원; ReFS 미지원; 시스템 볼륨(C:) 불가
- **여러 서버 엔드포인트 — 가장 높은 여유 공간 정책 적용**: 동일 볼륨 공유 시 **가장 제한적(높은)** 여유 공간 정책 적용
- **클라우드 계층화 회수 메커니즘**: 계층화된 파일은 로컬에 플레이스홀더로 유지; 접근 시 **Azure에서 자동 회수** (사용자에게 투명)

#### 네트워킹
- **VNet 내 ACI용 NAT Gateway**: VNet 통합 컨테이너 그룹의 아웃바운드 인터넷 접근에 **NAT Gateway** 필요
- **NSG 인바운드 평가 순서**: 서브넷 NSG → NIC NSG
- **NSG 아웃바운드 평가 순서**: NIC NSG → 서브넷 NSG
- **서브넷 위임 — 프라이빗 엔드포인트 불가**: 위임 서브넷은 위임 서비스 전용; 프라이빗 엔드포인트 배포 불가
- **BGP 경로 전파 비활성화**: 라우팅 테이블에서 설정; 해당 서브넷만 온프레미스 BGP 경로 학습 차단 (VPN·BGP 연결 유지)
- **ASG 크로스 서브넷**: 동일 **VNet** 내 다른 서브넷의 NIC를 포함 가능; VNet 간 ASG 불지원
- **DNS 영역 apex에서 ALIAS 레코드**: apex에서 CNAME 불가 (SOA/NS와 충돌); **Azure DNS ALIAS A 레코드**로 Traffic Manager 등 Azure 리소스 연결
- **Azure Firewall TI 기본 모드**: 기본값 **경고 전용** (로깅만, 차단 없음); **경고 및 거부**로 변경 시 로깅 + 차단
- **Private DNS Zone — Virtual Network Link**: VNet의 리소스가 프라이빗 엔드포인트 DNS 레코드 확인하려면 링크 필요

#### 컴퓨팅, AKS, App Service
- **가용성 영역 — 99.99% SLA**: 2개 이상 가용성 영역에 VM 배포 시 데이터 센터 수준 장애 격리 및 99.99% SLA 달성
- **Log Analytics Reader**: 로그 데이터 쿼리 읽기 전용 접근; 작업 영역 설정 수정 불가
- **App Service Key Vault 참조 새로 고침**: 비밀 값 캐시; 최대 새로 고침 주기 **24시간**; 재시작 시 즉시 새로 고침
- **AKS Windows 노드 풀**: **Standard SKU 부하 분산 장치** 필요 (Basic은 여러 노드 풀 미지원)
- **ACR AcrPush 역할**: 푸시 + 풀 권한 포함; CI/CD 파이프라인의 최소 권한 역할
- **Azure Bastion Standard SKU**: **사용자 지정 포트** 지원 최소 SKU (Basic은 기본 RDP 3389·SSH 22만 지원)
- **Kubernetes 요청/제한 vs ACI**: K8s → request = 최솟값 보장, limit = 하드 캡; ACI → request = 실질적 최댓값 (별도 limit 없음)
- **Logic App + 예산 자동화**: 예산 경고 작업으로 Logic App 트리거; 기본 제공 커넥터(Slack, ITSM 등) 활용; 사용자 지정 코드 불필요

#### Infrastructure as Code
- **ARM 연결 템플릿 리소스 유형**: `Microsoft.Resources/deployments` + `templateLink` 속성
- **Bicep 조건부 출력 (삼항)**: `output x string = 조건 ? 리소스.속성 : ''`
- **Workbook의 Azure Resource Graph**: 여러 구독의 VM 인벤토리 쿼리용 올바른 데이터 소스 (태그, 위치, 리소스 유형)
- **Workbook Contributor 역할**: `microsoft.insights/workbooks/write` 권한 제공; Monitoring Contributor보다 범위가 좁은 워크북 전용 역할

#### 모니터링 및 운영
- **VM Insights 데이터 저장소**: 성능 및 종속성 데이터 모두 **Log Analytics 작업 영역**에 저장
- **버전 미지정 Key Vault 참조**: App Service는 항상 **최신 활성 버전** 가져옴; 비밀 교체 자동 반영
- **SSPR 통합 등록 이점**: 한 번 등록 → MFA와 SSPR 모두 사용 가능
- **Azure Firewall TI 경고 및 거부 모드**: 악성 트래픽 **차단**(경고만 아닌) 위해 명시적 설정 필요
- **ACI CPU request = 최댓값**: ACI에서 `request`가 실질적 최대치; 별도 limit 개념 없음
- **RBAC 구독 한도**: 구독당 최대 **4,000개** 역할 할당; 그룹 할당으로 할당 할당량 절약


## Section 25 — 2026 Gap Fill: Expected Questions Set 11

### English

#### Networking and Firewall
- **HA Ports rule**: Protocol: All + Port: 0 — forwards all TCP/UDP traffic to NVA; standard Standard Load Balancer pattern for firewalls and IDS/IPS
- **Storage firewall VNet rules**: NSG filters VM traffic; VNet rules on storage account control which Azure network the storage service accepts traffic from — these are separate concerns
- **NSG stateful behavior**: Azure NSGs are stateful; return traffic for established connections is automatically allowed without explicit outbound rules
- **168.63.129.16**: Azure platform IP — must be reachable from VM subnet; required for VM Agent, extension download/management, DNS, and health probes; blocked by deny-all outbound NSG
- **FQDN Tags in Azure Firewall**: pre-defined Microsoft-managed FQDN collections (e.g., WindowsUpdate) used in application rules to avoid manual FQDN/IP management
- **Azure Firewall Premium + TLS inspection**: required to differentiate FQDNs that resolve to the same IP — decrypts HTTPS and inspects Host header/TLS SNI
- **Threat Intelligence default mode**: "Alert only" — logs but does not block; must explicitly set to "Alert and deny" to block malicious traffic
- **Firewall Policy TI inheritance**: child policy cannot reduce security below parent; if parent is "Alert and deny", child must also be "Alert and deny"
- **Service Endpoint Policy**: applied to subnet to restrict outbound Service Endpoint traffic to specific storage accounts only — prevents data exfiltration; different from storage VNet rules (which control inbound to storage)
- **Linux App Service Auto Swap**: not supported; must use CI/CD pipeline step (`az webapp deployment slot swap`) after deployment
- **NSG vs AWS Security Group**: both stateful; key difference: Azure NSG supports explicit deny rules; AWS SG supports allow only (implicit deny)

#### Storage, SAS, and Lifecycle
- **Service SAS + stored access policy**: enables revocation without key regeneration — delete the stored access policy to immediately invalidate all linked SAS tokens
- **Disable shared key access**: blocks Service SAS and Account SAS (key-based); User Delegation SAS (Entra ID-based) remains available — enforces Entra ID-only auth
- **Key rotation reminder disabled**: caused by missing keyCreationTime on access keys; fix by regenerating keys, which assigns the creation timestamp
- **High priority rehydration**: fastest option to move blobs from Archive tier to Hot/Cool; typically under 1 hour for objects < 10 GB; costs more than Standard priority
- **Diagnostic settings category groups**: two groups for storage accounts: `allLogs` (all resource logs) and `auditLogs` (security/audit logs only)
- **Cool tier minimum 30 days**: moving blob from Cool to Cold before 30 days incurs prorated early deletion fee; lifecycle rules must account for this
- **Last access time tracking prerequisite**: must be enabled before using `daysAfterLastAccessTimeGreaterThan` or `enableAutoTierToHotFromCool` in lifecycle policies

#### Compute and App Service
- **ACI multi-container = Linux only**: sidecar patterns and multi-container groups fully supported only on Linux; Windows ACI has very limited multi-container support
- **Encryption at host**: hypervisor-level encryption covering temp disks and disk caches; does not consume VM CPU — unlike ADE (BitLocker) which runs inside the VM
- **Custom Script Extension log path (Windows)**: `C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\`; downloaded scripts under `C:\Packages\Plugins\...`
- **Application Gateway cookie-based affinity**: sets ARRAffinity cookie for session persistence; routes subsequent requests to same backend instance; distinct from Azure LB source IP affinity (L4)
- **App Service Basic (B1) minimum**: minimum tier for custom domain + SSL certificate binding; Free and Shared tiers do not support these features
- **Azure SQL PITR**: automatic backup + point-in-time restore to any point within retention; minimum administrative effort for accidental data deletion
- **Metric alert AND logic**: requires both conditions simultaneously true at same time slice; use Max/Min aggregation over window to capture "met at any point in period" behavior

#### ARM Templates and Bicep
- **`az deployment group validate`**: syntax + parameter validation only; no resource changes; does not show resource impact
- **ARM What-if operation**: previews all resource changes (create/modify/delete) without applying them; `az deployment group what-if`
- **ARM linked template mode**: only Incremental; Complete mode not supported for linked templates
- **Bicep conditional resource**: `resource x '...' = if (condition) { ... }`
- **ARM conditional resource**: `"condition": "[parameters('deployStorage')]"` — bool parameter used directly, or with `equals()`
- **Custom RBAC role Id = null**: must be null when creating new role; Azure auto-generates GUID; AssignableScopes must be explicitly set
- **Linked template resource type**: `Microsoft.Resources/deployments` with `templateLink` property

#### Identity, RBAC, and Governance
- **RBAC additive model + NotActions**: effective permissions = union of all role assignments; NotActions excludes permission only within that specific role; another role can still grant the permission
- **NotActions vs Deny Assignment**: NotActions = role-level soft exclusion (overridable); Deny Assignment = system-level hard block (overrides all roles including Owner)
- **SSPR insufficient methods**: user cannot complete SSPR if registered methods < required number; sees error and must contact administrator
- **Bulk CSV required fields**: Name, User principal name, Initial password, Block sign in (4 mandatory columns in Entra admin center CSV)
- **Hierarchy Settings Administrator**: grants `Microsoft.Management/managementgroups/settings/write`; required to set default management group for new subscriptions
- **Root management group**: single per tenant, auto-created, cannot be deleted; all subscriptions and management groups ultimately reside under it
- **Actions vs DataActions**: Actions = ARM management plane; DataActions = data plane operations; NotActions/NotDataActions exclude permissions, do not grant them
- **Azure Budget P3M**: quarterly reset; P1M = monthly, P3M = quarterly, P1Y = yearly (ISO 8601)
- **Dynamic group license**: requires Entra ID Premium P1; not available in Free tier

#### Backup, Monitoring, and Operations
- **Crash-consistent vs App-consistent**: crash-consistent = disk state at power-loss moment, no quiescing, fast; app-consistent = VSS/scripts to flush transactions and quiesce, safe for DB recovery
- **VM Insights storage**: all performance and dependency data stored in Log Analytics workspace (`InsightsMetrics`, `VMConnection` tables)
- **Azure Resource Graph in Workbooks**: cross-subscription resource inventory queries; resource properties, tags, locations, ARM metadata
- **App Service Key Vault reference cache**: caches secret values up to 24 hours; restart forces immediate refresh; designed to reduce Key Vault API calls and avoid throttling
- **Azure Firewall TI default + audit finding**: default is "Alert only"; logs showing alerts without blocks may reflect the pre-change period when default was still active

---

### 한국어

#### 네트워킹 및 방화벽
- **HA Ports 규칙**: Protocol: All + Port: 0 — NVA로 모든 TCP/UDP 트래픽 전달; Standard Load Balancer에서 방화벽·IDS/IPS용 표준 패턴
- **스토리지 방화벽 VNet 규칙**: NSG는 VM 트래픽 필터링; 스토리지 계정의 VNet 규칙은 스토리지 서비스가 허용하는 Azure 네트워크를 제어 — 별개의 개념
- **NSG Stateful 동작**: Azure NSG는 Stateful; 허용된 연결의 반환 트래픽은 명시적 아웃바운드 규칙 없이 자동 허용
- **168.63.129.16**: Azure 플랫폼 IP — VM 서브넷에서 접근 가능해야 함; VM Agent, 확장 다운로드/관리, DNS, 상태 프로브에 필요; 모든 아웃바운드 차단 NSG에 의해 블록됨
- **Azure Firewall FQDN Tags**: Microsoft 관리 FQDN 컬렉션(예: WindowsUpdate); 수동 FQDN/IP 관리 없이 애플리케이션 규칙에서 사용
- **Azure Firewall Premium + TLS 검사**: 동일 IP로 확인되는 FQDN을 구별하기 위해 필요 — HTTPS 복호화 후 Host 헤더/TLS SNI 검사
- **Threat Intelligence 기본 모드**: "경고 전용(Alert only)" — 기록만 하고 차단 안 함; 악성 트래픽 차단하려면 "경고 및 거부(Alert and deny)"로 명시 변경
- **Firewall Policy TI 상속**: 자식 정책은 부모보다 보안을 낮출 수 없음; 부모가 "경고 및 거부"이면 자식도 반드시 "경고 및 거부"
- **Service Endpoint Policy**: 서브넷에 적용하여 Service Endpoint 아웃바운드 트래픽을 특정 스토리지 계정으로만 제한 — 데이터 유출 방지; 스토리지 VNet 규칙(인바운드 제어)과 다름
- **Linux App Service Auto Swap**: 지원되지 않음; 배포 후 CI/CD 파이프라인 단계(`az webapp deployment slot swap`)를 사용해야 함
- **NSG vs AWS Security Group**: 둘 다 Stateful; 차이: Azure NSG는 명시적 거부 규칙 지원; AWS SG는 허용만 지원(묵시적 거부)

#### 스토리지, SAS 및 수명 주기 관리
- **Service SAS + 저장된 액세스 정책**: 키 재생성 없이 취소 가능 — 저장된 액세스 정책 삭제 시 연결된 모든 SAS 토큰 즉시 무효화
- **공유 키 액세스 비활성화**: Service SAS 및 Account SAS(키 기반) 차단; User Delegation SAS(Entra ID 기반)는 유지 — Entra ID 전용 인증 강제
- **키 교체 알림 비활성화**: 액세스 키의 keyCreationTime 누락이 원인; 키 재생성으로 생성 타임스탬프 할당하여 해결
- **높은 우선순위 리하이드레이션**: Archive 계층에서 Hot/Cool로 블로브 이동 시 가장 빠른 옵션; 10GB 미만 개체는 일반적으로 1시간 이내; 표준 우선순위보다 비용 높음
- **진단 설정 범주 그룹**: 스토리지 계정에 두 그룹: `allLogs`(모든 리소스 로그) 및 `auditLogs`(보안/감사 로그만)
- **Cool 계층 최소 30일**: 30일 이전에 Cool에서 Cold로 이동 시 나머지 기간에 대한 조기 삭제 요금 부과; 수명 주기 규칙 설계 시 고려 필요
- **마지막 접근 시간 추적 전제 조건**: 수명 주기 정책에서 `daysAfterLastAccessTimeGreaterThan` 또는 `enableAutoTierToHotFromCool` 사용 전 활성화 필수

#### 컴퓨팅 및 App Service
- **ACI 다중 컨테이너 = Linux만**: 사이드카 패턴 및 다중 컨테이너 그룹은 Linux에서만 완전 지원; Windows ACI는 다중 컨테이너 지원 매우 제한적
- **호스트에서의 암호화**: 하이퍼바이저 수준 암호화; 임시 디스크 및 디스크 캐시 포함; VM CPU 사용 없음 — VM 내부에서 실행되는 ADE(BitLocker)와 다름
- **Custom Script Extension 로그 경로(Windows)**: `C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\`; 다운로드된 스크립트는 `C:\Packages\Plugins\...` 아래
- **Application Gateway 쿠키 기반 선호도**: ARRAffinity 쿠키로 세션 지속성; 이후 요청을 동일한 백엔드 인스턴스로 라우팅; Azure LB 원본 IP 선호도(L4)와 구별
- **App Service Basic(B1) 최소**: 사용자 지정 도메인 + SSL 인증서 바인딩을 위한 최소 계층; Free 및 Shared 계층 미지원
- **Azure SQL PITR**: 자동 백업 + 보존 기간 내 임의 시점 복원; 실수로 데이터 삭제 시 최소 관리 노력으로 복구
- **메트릭 경고 AND 로직**: 두 조건이 동일 시간 슬라이스에서 동시에 참이어야 함; "기간 내 한 번이라도 충족" 동작을 위해 창에 대한 Max/Min 집계 사용

#### ARM 템플릿 및 Bicep
- **`az deployment group validate`**: 구문 + 매개변수 유효성 검사만; 리소스 변경 없음; 리소스 영향 표시 안 함
- **ARM What-if 작업**: 적용 없이 모든 리소스 변경 사항(생성/수정/삭제) 미리 보기; `az deployment group what-if`
- **ARM 연결 템플릿 모드**: Incremental만; Complete 모드 연결 템플릿 미지원
- **Bicep 조건부 리소스**: `resource x '...' = if (조건) { ... }`
- **ARM 조건부 리소스**: `"condition": "[parameters('deployStorage')]"` — 부울 매개변수 직접 사용 또는 `equals()` 사용
- **사용자 지정 RBAC 역할 Id = null**: 새 역할 생성 시 반드시 null; Azure가 GUID 자동 생성; AssignableScopes는 명시적으로 설정 필요
- **연결 템플릿 리소스 유형**: `templateLink` 속성이 있는 `Microsoft.Resources/deployments`

#### ID, RBAC 및 거버넌스
- **RBAC 가산적 모델 + NotActions**: 유효 권한 = 모든 역할 할당의 합집합; NotActions는 해당 역할 내에서만 권한 제외; 다른 역할이 여전히 권한을 부여할 수 있음
- **NotActions vs Deny Assignment**: NotActions = 역할 수준 소프트 제외(재정의 가능); Deny Assignment = 시스템 수준 하드 차단(Owner 포함 모든 역할 재정의)
- **SSPR 불충분한 방법**: 등록된 방법 수 < 필요 수이면 SSPR 완료 불가; 오류 표시 후 관리자에게 문의해야 함
- **CSV 대량 생성 필수 필드**: 이름, 사용자 계정 이름, 초기 암호, 로그인 차단 (Entra 관리 센터 CSV의 4개 필수 열)
- **Hierarchy Settings Administrator**: `Microsoft.Management/managementgroups/settings/write` 권한 부여; 새 구독의 기본 관리 그룹 설정에 필요
- **루트 관리 그룹**: 테넌트당 하나, 자동 생성, 삭제 불가; 모든 구독과 관리 그룹이 궁극적으로 그 아래에 있음
- **Actions vs DataActions**: Actions = ARM 관리 플레인; DataActions = 데이터 플레인 작업; NotActions/NotDataActions는 권한 제외, 부여가 아님
- **Azure Budget P3M**: 분기별 초기화; P1M = 월별, P3M = 분기별, P1Y = 연간 (ISO 8601)
- **동적 그룹 라이선스**: Entra ID Premium P1 필요; Free 계층 미지원

#### 백업, 모니터링 및 운영
- **충돌 일관성 vs 애플리케이션 일관성**: 충돌 일관성 = 전원 차단 순간의 디스크 상태, 정지 없음, 빠름; 애플리케이션 일관성 = VSS/스크립트로 트랜잭션 플러시 및 정지, DB 복구에 안전
- **VM Insights 스토리지**: 모든 성능 및 종속성 데이터가 Log Analytics 작업 영역에 저장(`InsightsMetrics`, `VMConnection` 테이블)
- **Workbook의 Azure Resource Graph**: 여러 구독 리소스 인벤토리 쿼리; 리소스 속성, 태그, 위치, ARM 메타데이터
- **App Service Key Vault 참조 캐시**: 비밀 값 최대 24시간 캐시; 재시작 시 즉시 새로 고침; Key Vault API 호출 감소 및 제한 방지 목적
- **Azure Firewall TI 기본값 + 감사 발견**: 기본값은 "경고 전용"; 차단 없는 경고를 보여주는 로그는 기본 설정이 여전히 활성화되어 있던 변경 이전 기간을 반영할 수 있음


## Section 26 — 2026 Gap Fill: Expected Questions Set 12

### English

#### Networking and Private Connectivity
- **NSG priority first-match**: Rules evaluated lowest priority number first; first matching rule terminates processing — Rule2 (P200 Deny Any) blocks traffic before Rule3 (P300 Allow Internet) is evaluated
- **LB health probe source IP**: 168.63.129.16 — NSG rules must explicitly allow inbound from this Azure infrastructure IP on the probe port
- **Private Endpoint approval (cross-subscription)**: Connection stays Pending until the target resource owner approves it from the resource's Private endpoint connections blade
- **Private Endpoint DNS zone (blob)**: `privatelink.blob.core.windows.net` — created automatically when integrating blob Private Endpoint with a private DNS zone
- **Azure Firewall FQDN tag**: Application rule + WindowsUpdate FQDN tag = minimal-maintenance Windows Update allow rule; Microsoft manages the FQDN list automatically
- **Route table region requirement**: Route table and VNet subnet must be in the same region — changing region is not supported; delete and recreate in the correct region
- **Private DNS VNet link autoregistration**: Enabled (VNet1) = automatic A record registration; Disabled (VNet2) = resolution only, no registration
- **ACI container group shared network namespace**: Containers in the same group communicate via localhost:port (loopback interface shared)
- **ACI in VNet — internet access**: VNet-injected container groups require NAT gateway or UDR with 0.0.0.0/0 → Internet for outbound internet access
- **Application Gateway health probe**: Custom probe must be associated with Backend HTTP settings — not listener, routing rule, or backend pool

#### Storage, SAS, and Lifecycle
- **Stored access policy revocation**: Deleting the stored access policy immediately invalidates all SAS tokens referencing it — no key regeneration needed
- **Azure File Share snapshot limit**: Maximum 200 snapshots per file share — exceeding this limit causes backup failures
- **Lifecycle tiering blob types**: Tier-to-cool / tier-to-archive apply to block blobs only — append blobs and page blobs excluded from tiering
- **Azure File Sync cloud change detection**: Portal uploads sync after 24h by default — run `Invoke-AzStorageSyncChangeDetection` for immediate detection
- **Lifecycle action priority**: When multiple actions qualify for the same blob simultaneously, least expensive wins — delete < tierToArchive < tierToCool
- **Key expiration policy**: Configures a rotation reminder in the portal when a storage account key exceeds the specified age without rotation
- **Azure Files auth source exclusivity**: Only one identity authentication source active at a time — disable Entra Domain Services before enabling on-premises AD DS
- **Service Endpoint Policy**: Applied at subnet level to restrict service endpoint traffic to specific storage account resource IDs — prevents data exfiltration
- **Blob container stored access policy limit**: Maximum 5 stored access policies per container
- **Storage Account Contributor + portal blob access**: Role includes `listKeys/action` → portal uses Account access key (Shared Key), not Entra ID credentials

#### Compute and App Service
- **Linux CSE log path**: Primary log at `/var/log/azure/custom-script-extension/handler.log`; `waagent.log` is secondary
- **VM Insights Map — Dependency agent**: Dependency agent required for the Map feature (process and network dependency visualization); AMA alone only supports performance monitoring
- **VM Insights minimum requirements**: Log Analytics workspace + Azure Monitor Agent — sufficient for performance monitoring; Dependency agent is additional (Map only)
- **Availability Set Update Domain math**: 10 VMs / 5 UDs = 2 VMs per UD; planned maintenance affects 1 UD at a time → max 2 VMs unavailable simultaneously
- **ARM template Availability Set managed disks**: `sku.name = Aligned` aligns compute and storage fault domains — not `properties.managed = true`
- **Enhanced backup policy instant snapshot retention**: Default 7 days for all backup frequencies (4/6/12 hours)
- **Azure Disk Encryption + Key Vault co-location**: VM and Key Vault must be in the same region and subscription for Disk Encryption to work
- **App Service slot setting (sticky)**: Mark setting as deployment slot setting to keep it bound to the slot during swaps — e.g., DatabaseConnection stays with staging
- **App Service Auto Swap**: Enable on staging slot targeting production for automatic warm-up + swap; Windows App Service plans only
- **App Service SNI SSL**: Allows multiple custom domains to share the same IP address with separate certificates; IP-based SSL requires dedicated IP per domain

#### Identity, RBAC, and Governance
- **PowerShell usage location update**: `Get-MgUser -Filter "department eq 'Accounting'"` + `Update-MgUser -UsageLocation "FR"` — Microsoft Graph PowerShell SDK
- **Dynamic group Premium P1 requirement**: Dynamic membership rules require Entra ID Premium P1 or P2 — not available in Free tier
- **Dynamic group scope**: Only group membership (join/leave) is automated; user account creation and property setting remain manual
- **RBAC additive model**: Effective permissions = union of all role assignments across scopes — Contributor at subscription + Reader at RG1 = Contributor at RG1
- **CanNotDelete lock precedence**: Management locks override all RBAC roles including Owner — lock must be removed before deletion can proceed
- **Lock management permission**: `Microsoft.Authorization/locks/*` required — Contributor lacks this; Owner (or User Access Administrator) needed; exam answer = Owner
- **Management Group RBAC inheritance**: Single Reader assignment at MG scope covers all child subscriptions — minimum assignments for multi-subscription access
- **Policy modify effect**: Auto-adds/updates tags at creation time and supports remediation tasks for existing non-compliant resources — `append` does not support remediation
- **Policy deny effect**: Blocks resource creation/update requests that fail policy conditions — `audit` only logs non-compliance
- **Policy modify operation — `add` vs `addOrReplace`**: `add` sets tag only if absent (preserves existing values); `addOrReplace` overwrites existing tag values
- **Policy enforcement mode Disabled**: Evaluates and reports compliance but does NOT apply the policy effect to resource operations — for impact assessment before enforcement

#### ARM Templates and Bicep
- **Bicep for + if order**: Loop + condition must be `[for ... : if(condition) { }]` — `for` before `if`; reverse order is invalid syntax
- **Bicep module output reference**: `moduleName.outputs.outputName` — module symbolic name followed by `.outputs.`
- **Policy enforcement Disabled mode**: Compliance data still collected; resource operations not blocked — distinct from removing or disabling the assignment

#### Site Recovery, Monitoring, and AKS
- **Action Group email rate limit**: 100 emails/hour per recipient address — intermittent notification gaps during alert storms indicate rate limiting (not misconfiguration)
- **Site Recovery Re-protect**: After failover + commit, must Re-protect to establish reverse replication (secondary → primary) before failback can proceed
- **Site Recovery commit**: Finalizes failover; all existing recovery points are deleted; rollback no longer possible — irreversible operation
- **Key Vault Secrets User**: Minimum role for managed identity to read secret values when Key Vault uses RBAC model — Key Vault Reader cannot access secret data
- **AKS Spot node pool mode**: Spot node pools must be User node pools — system node pools require high availability incompatible with Spot (preemptible) VMs

---

### 한국어

#### 네트워킹 및 프라이빗 연결
- **NSG 우선순위 첫 번째 일치**: 가장 낮은 우선순위 번호부터 평가되며 첫 번째 일치 규칙에서 처리 종료 — Rule2(P200 Deny Any)가 Rule3(P300 Allow Internet)보다 먼저 트래픽 차단
- **LB 상태 프로브 소스 IP**: 168.63.129.16 — NSG 규칙이 프로브 포트에서 이 Azure 인프라 IP의 인바운드를 명시적으로 허용해야 함
- **Private Endpoint 승인(구독 간)**: 대상 리소스 소유자가 리소스의 프라이빗 엔드포인트 연결 블레이드에서 승인할 때까지 연결은 보류 중 상태 유지
- **Private Endpoint DNS 영역(blob)**: `privatelink.blob.core.windows.net` — Blob Private Endpoint를 프라이빗 DNS 영역과 통합할 때 자동으로 생성
- **Azure Firewall FQDN 태그**: 애플리케이션 규칙 + WindowsUpdate FQDN 태그 = 최소 유지 관리로 Windows Update 허용; Microsoft가 FQDN 목록 자동 관리
- **경로 테이블 지역 요구 사항**: 경로 테이블과 VNet 서브넷은 동일 지역에 있어야 함 — 지역 변경 불가; 올바른 지역에서 삭제 후 재생성
- **프라이빗 DNS VNet 링크 자동 등록**: 활성화(VNet1) = 자동 A 레코드 등록; 비활성화(VNet2) = 확인 전용, 등록 없음
- **ACI 컨테이너 그룹 공유 네트워크 네임스페이스**: 같은 그룹의 컨테이너는 localhost:포트를 통해 통신(루프백 인터페이스 공유)
- **VNet의 ACI — 인터넷 액세스**: VNet 주입된 컨테이너 그룹은 아웃바운드 인터넷 액세스를 위해 NAT 게이트웨이 또는 0.0.0.0/0 → 인터넷 UDR이 필요
- **Application Gateway 상태 프로브**: 사용자 지정 프로브는 백엔드 HTTP 설정에 연결해야 함 — 수신기, 라우팅 규칙, 백엔드 풀에 연결 불가

#### 스토리지, SAS 및 수명 주기
- **저장된 액세스 정책 취소**: 저장된 액세스 정책을 삭제하면 해당 정책을 참조하는 모든 SAS 토큰이 즉시 무효화 — 키 재생성 불필요
- **Azure 파일 공유 스냅샷 한도**: 파일 공유당 최대 200개의 스냅샷 — 한도 초과 시 백업 실패
- **수명 주기 계층화 Blob 유형**: 쿨/아카이브 계층 전환은 블록 Blob에만 적용 — 추가 Blob 및 페이지 Blob 제외
- **Azure File Sync 클라우드 변경 감지**: Portal 업로드는 기본적으로 24시간 후 동기화 — 즉각적인 감지를 위해 `Invoke-AzStorageSyncChangeDetection` 실행
- **수명 주기 작업 우선순위**: 동일한 Blob에 여러 작업이 해당될 때 가장 저렴한 작업 선택 — delete < tierToArchive < tierToCool
- **키 만료 정책**: 스토리지 계정 키가 지정된 기간을 초과하여 교체되지 않으면 Portal에 교체 미리 알림 표시
- **Azure Files 인증 소스 독점성**: 한 번에 하나의 ID 인증 소스만 활성화 — 온-프레미스 AD DS 활성화 전 Entra Domain Services 비활성화 필요
- **서비스 엔드포인트 정책**: 특정 스토리지 계정 리소스 ID로 서비스 엔드포인트 트래픽을 제한하기 위해 서브넷 수준에 적용 — 데이터 유출 방지
- **Blob 컨테이너 저장된 액세스 정책 한도**: 컨테이너당 최대 5개
- **Storage Account Contributor + Portal Blob 액세스**: 역할에 `listKeys/action` 포함 → Portal이 Entra ID 자격 증명 대신 계정 액세스 키(Shared Key) 사용

#### 컴퓨팅 및 App Service
- **Linux CSE 로그 경로**: 기본 로그는 `/var/log/azure/custom-script-extension/handler.log`; `waagent.log`는 보조
- **VM Insights 맵 — Dependency agent**: Dependency agent는 맵 기능(프로세스 및 네트워크 종속성 시각화)에 필요; AMA 단독으로는 성능 모니터링만 지원
- **VM Insights 최소 요구 사항**: Log Analytics 작업 영역 + Azure Monitor Agent — 성능 모니터링에 충분; Dependency agent는 추가(맵 전용)
- **가용성 집합 업데이트 도메인 계산**: 10개 VM / 5개 UD = UD당 2개 VM; 계획된 유지 관리는 한 번에 1개 UD에 영향 → 동시에 최대 2개 VM 사용 불가
- **ARM 템플릿 가용성 집합 관리 디스크**: `sku.name = Aligned`로 컴퓨팅과 스토리지 장애 도메인 정렬 — `properties.managed = true` 아님
- **향상된 백업 정책 즉시 스냅샷 보존**: 모든 백업 빈도(4/6/12시간)에 대해 기본 7일
- **Azure Disk Encryption + Key Vault 같은 위치**: VM과 Key Vault는 Disk Encryption 작동을 위해 동일 지역 및 구독에 있어야 함
- **App Service 슬롯 설정(고정)**: 설정을 배포 슬롯 설정으로 표시하면 교체 중에도 슬롯에 바인딩된 채 유지 — 예: DatabaseConnection은 스테이징에 유지
- **App Service Auto Swap**: 스테이징 슬롯에서 프로덕션을 대상으로 활성화 시 자동 예열 + 교체; Windows App Service 계획만 지원
- **App Service SNI SSL**: 여러 사용자 지정 도메인이 각각의 인증서를 가지면서 동일한 IP 주소 공유 가능; IP 기반 SSL은 도메인당 전용 IP 필요

#### ID, RBAC 및 거버넌스
- **PowerShell 사용 위치 업데이트**: `Get-MgUser -Filter "department eq 'Accounting'"` + `Update-MgUser -UsageLocation "FR"` — Microsoft Graph PowerShell SDK
- **동적 그룹 Premium P1 요구 사항**: 동적 구성원 자격 규칙은 Entra ID Premium P1 또는 P2 필요 — Free 계층에서 사용 불가
- **동적 그룹 범위**: 그룹 구성원 자격(가입/탈퇴)만 자동화; 사용자 계정 생성 및 속성 설정은 수동
- **RBAC 가산적 모델**: 유효 권한 = 모든 범위에 걸친 역할 할당의 합집합 — 구독의 Contributor + RG1의 Reader = RG1에서 Contributor
- **CanNotDelete 잠금 우선순위**: 관리 잠금은 Owner를 포함한 모든 RBAC 역할보다 우선 — 삭제를 진행하려면 잠금을 먼저 제거해야 함
- **잠금 관리 권한**: `Microsoft.Authorization/locks/*` 필요 — Contributor 없음; Owner(또는 User Access Administrator) 필요; 시험 답변 = Owner
- **관리 그룹 RBAC 상속**: MG 범위에서 단일 Reader 할당이 모든 자식 구독을 포함 — 다중 구독 액세스를 위한 최소 할당 수
- **Policy modify 효과**: 생성 시 태그 자동 추가/업데이트 + 기존 비준수 리소스에 대한 수정 작업 지원 — `append`는 수정 미지원
- **Policy deny 효과**: 정책 조건을 충족하지 않는 리소스 생성/업데이트 요청 차단 — `audit`은 비준수만 기록
- **Policy modify 작업 — `add` vs `addOrReplace`**: `add`는 태그가 없을 때만 설정(기존 값 보존); `addOrReplace`는 기존 태그 값 덮어쓰기
- **Policy 적용 모드 Disabled**: 규정 준수 데이터는 수집되지만 리소스 작업에 정책 효과 미적용 — 적용 전 영향 평가용

#### ARM 템플릿 및 Bicep
- **Bicep for + if 순서**: 루프 + 조건은 반드시 `[for ... : if(condition) { }]` — `for` 다음에 `if`; 역순은 구문 오류
- **Bicep 모듈 출력 참조**: `moduleName.outputs.outputName` — 모듈 기호 이름 다음에 `.outputs.`
- **Policy 적용 Disabled 모드**: 규정 준수 데이터는 계속 수집; 리소스 작업은 차단되지 않음 — 할당 제거 또는 비활성화와 구별

#### Site Recovery, 모니터링 및 AKS
- **Action Group 이메일 속도 제한**: 수신자 주소당 시간당 100개 이메일 — 경고 폭발 중 간헐적 알림 부재는 속도 제한(잘못된 구성 아님) 나타냄
- **Site Recovery 다시 보호**: 장애 조치 + 커밋 후 역방향 복제 설정을 위해 반드시 다시 보호 수행(보조 → 기본) — 장애 복구 전 필수 단계
- **Site Recovery 커밋**: 장애 조치 확정; 기존 복구 지점 모두 삭제; 롤백 불가 — 되돌릴 수 없는 작업
- **Key Vault Secrets User**: Key Vault RBAC 모델에서 관리 ID가 비밀 값을 읽기 위한 최소 역할 — Key Vault Reader는 비밀 데이터 액세스 불가
- **AKS Spot 노드 풀 모드**: Spot 노드 풀은 반드시 User 노드 풀이어야 함 — 시스템 노드 풀은 Spot(선점 가능) VM과 호환되지 않는 고가용성 요구



---

## Section 27 — 2026 Gap Fill: Expected Questions Set 14

### English

#### Site Recovery and Backup
- **Recovery Plan startup groups**: Startup groups run sequentially (Group 1 → 2 → 3); place SQL in Group 1, App in Group 2, Web in Group 3 to guarantee DB → App → Web dependency order during failover
- **VM Instant Restore snapshot retention**: Configurable 1–5 days; stored locally near the VM for fast restore before vault transfer
- **Recovery Services vault region**: Must be in the same region as the protected resource; backup snapshots are region-bound
- **AKS Spot Deallocate eviction**: Evicted Spot VMs in Stopped (deallocated) state continue consuming vCPU quota — prevents autoscaler from provisioning new nodes; use Delete policy to release quota immediately
- **Archive blob rehydration**: For immediate access, use High Priority + Hot tier (< 1 hour for objects under 10 GB); Standard Priority = up to 15 hours
- **Azure SQL PITR retention**: Max 35 days for all DTU-based tiers (Basic, Standard, Premium); long-term retention (LTR) is a separate feature
- **Join-AzStorageAccount AD DS object**: Creates a computer account in AD DS by default to represent the storage account; enables Kerberos SMB auth
- **VM power states**: Stopped = OS shutdown, compute host still reserved, billing continues; Stopped (deallocated) = compute host released, billing stops, disk charges continue

#### Identity and RBAC
- **Combined security information registration**: Single Entra ID registration experience for both SSPR and MFA authentication methods; eliminates separate registration portals
- **Dynamic group license**: Requires Microsoft Entra ID Premium P1 or P2; Free tier supports static membership only
- **Dynamic guest user rule**: `(user.userType -eq "Guest")` — userType attribute is Guest for B2B external users, Member for internal users
- **User Administrator role**: Least-privilege Entra ID role for creating and managing users; Password Administrator cannot create users
- **RBAC additive model**: Azure RBAC uses a union (additive) model — effective permissions = union of all role assignments across all scopes; broader assignments are inherited downward
- **NotDataActions scope**: NotDataActions only subtracts permissions within the same role definition; does NOT create deny rules across separate role assignments
- **Virtual Machine Contributor**: Least-privilege role for VM create/manage scoped to a specific resource group; Contributor at RG level is too broad (covers all resources)
- **Security Group purpose**: Primary purpose is RBAC access management — assign roles and app access to groups rather than individuals; not a mailing list

#### Governance, Policy, and Routing
- **Policy DoNotEnforce mode**: Evaluates resources and reports compliance without applying the policy effect; allows impact assessment before full enforcement
- **Policy deny effect**: Blocks resource create/update and returns an error; use for immediate enforcement; audit/auditIfNotExists only log violations
- **Azure Policy vs RBAC**: Policy controls *what is allowed* (compliance rules, location, tags, SKUs); RBAC controls *who can do what* (permissions/identity)
- **Route priority**: User-Defined Route (UDR) > BGP Route > System Route when address prefixes are identical; UDR always overrides BGP-learned routes
- **Budget alert Contact Roles**: Automatically notifies all users with a specified RBAC role (e.g., Contributor) without managing individual email addresses; different from Action Group
- **Firewall policy child inheritance**: Child policy threat intelligence mode must be same or stricter than parent; Off < Alert < Alert and deny; cannot set child to less restrictive
- **Application Gateway path rule order**: Rules evaluated top-down (first match wins); wildcard `/*` must be at the bottom of the list; no numeric priority like NSG rules
- **Azure Firewall rule type processing**: Rule type evaluated first (DNAT → Network → Application across all RCGs), then RCG priority within each type; not per-RCG sequentially

#### Networking
- **Load Balancer health probe IP**: Health probes always originate from 168.63.129.16 (Azure platform IP); must be allowed in backend VM NSGs on the probe port
- **NVA IP forwarding**: A VM acting as an NVA (network virtual appliance) requires IP forwarding enabled on its NIC in Azure; disabled by default — packets are dropped without it
- **Azure Private Link**: Assigns a private IP from your VNet to an Azure PaaS service via Private Endpoint; traffic stays on the Azure backbone with no internet exposure
- **Azure PaaS public by default**: All Azure PaaS services (Storage, SQL, etc.) have public endpoints open by default; firewalls, service endpoints, or Private Endpoints must be configured to restrict access
- **Storage firewall service endpoint**: Adding a VNet to the storage account firewall requires the Microsoft.Storage service endpoint to be enabled on that subnet
- **Azure DNS multi-value A record**: A single A record set can contain multiple IP values; DNS returns all IPs for round-robin distribution across web servers
- **DNS CNAME exclusivity**: A CNAME record cannot coexist with any other record type on the same DNS name; creation of conflicting records will fail
- **App Service slot sticky routing**: Traffic routing uses `x-ms-routing-name` cookie to pin users to a slot for 1 hour; returning within 1 hour routes to the same slot

#### Storage, SAS, and App Service
- **App Service Swap with preview**: Multi-phase swap applies production settings to staging first for validation; can commit or cancel before traffic switches; standard swap has no validation phase
- **App Service root domain verification**: Uses a TXT record named `asuid` with the Azure-provided verification ID; root domains (apex) cannot use CNAME records
- **Service SAS stored access policies**: Only Service SAS supports stored access policies for revocation; Account SAS and User delegation SAS cannot be revoked before expiry
- **Azure Files Entra Kerberos**: Enables SMB access to Azure Files from Entra-joined devices without any domain controller; cloud-issued Kerberos tickets; cloud-only identity environment
- **Azure File Sync agent components**: FileSyncSvc.exe (background sync service) + StorageSync.sys (file system filter driver for cloud tiering and fast disaster recovery)
- **Premium SSD performance tier change**: Can change performance tier (e.g., P15 → P20) while the VM is running, without resizing the disk or stopping the VM; zero downtime
- **Action Group notification types**: Email, SMS, Push (Azure mobile app) = notification types; Webhook, Azure Function, Logic App = action (automation) types
- **User-assigned managed identity**: Standalone Azure resource, pre-creatable, sharable across multiple resources, RBAC configurable before deployment; system-assigned is tied to one resource's lifecycle

#### ARM Templates, Bicep, and Monitoring
- **ARM linked template output**: Access linked deployment outputs using `reference('<deploymentName>').outputs.<outputName>.value` — `.value` suffix required in ARM JSON
- **Bicep module output**: Access using `<moduleName>.outputs.<outputName>` — no `.value` suffix needed in Bicep (typed value directly accessible)
- **Bicep module cross-scope**: Use `scope: resourceGroup('RGName')` on a module to deploy resources to a different resource group from a subscription-scoped parent file
- **Bicep default targetScope**: If `targetScope` is omitted, defaults to `resourceGroup`; subscription/tenant/managementGroup scopes must be explicitly declared
- **Bicep subscription + RG deployment**: Create resource group at subscription scope, then use module with `scope: resourceGroup('Name')` for resource-group-level resources — both can coexist in one deployment
- **VM Insights DCR prerequisite**: Azure Policy-based VM Insights enablement requires a Data Collection Rule (DCR) as the prerequisite; DCR defines what data to collect from VMs
- **Azure Workbook templates**: Reusable blueprints containing pre-built KQL queries, metrics, charts, and parameters; deploy to create Workbook instances without rebuilding from scratch
- **AcrPush role**: Least-privilege ACR role for CI/CD pipelines — includes both push/write and pull/read permissions; AcrPull is pull-only; Contributor is too broad
- **User-assigned vs system-assigned identity**: User-assigned = independent resource, pre-creatable, shareable across multiple resources; system-assigned = auto-created with resource, single-resource lifecycle, cannot be shared

---

### 한국어

#### Site Recovery 및 백업
- **복구 계획 시작 그룹**: 시작 그룹은 순서대로 실행(Group 1 → 2 → 3); SQL을 Group 1, App을 Group 2, Web을 Group 3에 배치하여 장애 조치 시 DB → App → Web 의존성 순서 보장
- **VM 인스턴트 복원 스냅샷 보존**: 1~5일 구성 가능; VM 근처에 로컬 저장으로 빠른 복원 지원
- **Recovery Services vault 지역**: 보호 대상 리소스와 동일한 지역에 있어야 함; 백업 스냅샷은 지역에 종속
- **AKS Spot Deallocate 제거**: 제거된 Spot VM이 Stopped (deallocated) 상태로 vCPU 할당량을 계속 점유 — 자동 크기 조정기의 새 노드 프로비저닝 방해; Delete 정책 사용 시 즉시 할당량 반환
- **Archive Blob 리하이드레이션**: 즉시 접근에는 높은 우선순위 + Hot 계층 사용(10GB 미만 객체 1시간 이내); 표준 우선순위는 최대 15시간 소요
- **Azure SQL PITR 보존**: 모든 DTU 기반 계층(Basic, Standard, Premium)에서 최대 35일; LTR(장기 보존)은 별도 기능
- **Join-AzStorageAccount AD DS 개체**: 기본적으로 AD DS에 컴퓨터 계정 생성으로 스토리지 계정을 표현; Kerberos SMB 인증 활성화
- **VM 전원 상태**: Stopped = OS 종료, 컴퓨팅 호스트 예약 유지, 과금 계속; Stopped (deallocated) = 컴퓨팅 호스트 해제, 컴퓨팅 요금 중단, 디스크 요금 계속

#### ID 및 RBAC
- **통합 보안 정보 등록**: SSPR과 MFA 인증 방법을 한 번의 Entra ID 등록 경험으로 처리; 별도 등록 포털 불필요
- **동적 그룹 라이선스**: Microsoft Entra ID Premium P1 또는 P2 필요; Free 계층은 정적 멤버십만 지원
- **동적 게스트 사용자 규칙**: `(user.userType -eq "Guest")` — userType 특성은 외부 B2B 사용자에게 Guest, 내부 사용자에게 Member로 설정
- **사용자 관리자 역할**: 사용자 생성 및 관리를 위한 최소 권한 Entra ID 역할; 암호 관리자는 사용자를 만들 수 없음
- **RBAC 가산적 모델**: Azure RBAC는 합집합(가산적) 모델 — 유효 권한 = 모든 범위의 모든 역할 할당 합집합; 상위 범위 할당이 하위로 상속
- **NotDataActions 범위**: NotDataActions는 동일한 역할 정의 내에서만 권한 차감; 별도 역할 할당 간에 거부 규칙 생성하지 않음
- **Virtual Machine Contributor**: 특정 리소스 그룹으로 범위를 지정한 VM 생성/관리를 위한 최소 권한 역할; RG 수준의 Contributor는 모든 리소스를 포함하므로 과도한 권한
- **Security Group 목적**: 주요 목적은 RBAC 액세스 관리 — 개인이 아닌 그룹 단위로 역할 및 앱 액세스 할당; 메일링 리스트가 아님

#### 거버넌스, 정책, 라우팅
- **Policy DoNotEnforce 모드**: 정책 효과 적용 없이 리소스를 평가하고 규정 준수를 보고; 완전 적용 전 영향 평가 가능
- **Policy deny 효과**: 리소스 생성/업데이트를 차단하고 오류 반환; audit/auditIfNotExists는 위반 사항만 기록
- **Azure Policy vs RBAC**: Policy = *허용되는 것* 제어(규정 준수 규칙, 위치, 태그, SKU); RBAC = *누가 무엇을 할 수 있는지* 제어(권한/ID)
- **경로 우선순위**: 주소 접두사가 동일할 때 사용자 정의 경로(UDR) > BGP 경로 > 시스템 경로; UDR은 항상 BGP 학습 경로를 재정의
- **예산 경고 Contact Roles**: 개별 이메일 주소 관리 없이 지정된 RBAC 역할(예: Contributor)을 가진 모든 사용자에게 자동 알림; Action Group과 다름
- **방화벽 정책 하위 상속**: 하위 정책의 위협 인텔리전스 모드는 상위와 동일하거나 더 엄격해야 함; Off < Alert < Alert and deny; 덜 제한적으로 설정 불가
- **Application Gateway 경로 규칙 순서**: 위에서 아래로 평가(첫 번째 일치가 이김); 와일드카드 `/*`는 목록 맨 아래에 있어야 함; NSG 규칙과 달리 숫자 우선순위 없음
- **Azure Firewall 규칙 유형 처리**: 규칙 유형별로 먼저 처리(모든 RCG에 걸쳐 DNAT → 네트워크 → 애플리케이션), 그 다음 각 유형 내에서 RCG 우선순위; RCG별 순차적 처리 아님

#### 네트워킹
- **부하 분산 장치 상태 프로브 IP**: 상태 프로브는 항상 168.63.129.16(Azure 플랫폼 IP)에서 발생; 백엔드 VM NSG에서 프로브 포트에 허용 필요
- **NVA IP 전달**: NVA 역할을 하는 VM은 Azure에서 NIC에 IP 전달이 활성화되어야 함; 기본적으로 비활성화 — 없으면 패킷 삭제
- **Azure Private Link**: VNet의 개인 IP를 Private Endpoint를 통해 Azure PaaS 서비스에 할당; 트래픽이 Azure 백본에서 유지되며 인터넷 노출 없음
- **Azure PaaS 기본 공개 접근**: 모든 Azure PaaS 서비스는 기본적으로 공용 엔드포인트가 열려 있음; 접근 제한을 위해 방화벽, 서비스 엔드포인트 또는 Private Endpoint 구성 필요
- **스토리지 방화벽 서비스 엔드포인트**: 스토리지 계정 방화벽에 VNet 추가 시 해당 서브넷에서 Microsoft.Storage 서비스 엔드포인트 활성화 필요
- **Azure DNS 다중 값 A 레코드**: 단일 A 레코드 집합에 여러 IP 값 포함 가능; DNS가 모든 IP를 반환하여 웹 서버 간 라운드 로빈 분산
- **DNS CNAME 배타성**: CNAME 레코드는 동일한 DNS 이름에 다른 레코드 유형과 공존 불가; 충돌하는 레코드 생성 시도는 실패
- **App Service 슬롯 스티키 라우팅**: 트래픽 라우팅은 `x-ms-routing-name` 쿠키를 사용하여 1시간 동안 사용자를 슬롯에 고정; 1시간 내 재접속 시 동일 슬롯으로 라우팅

#### 스토리지, SAS, App Service
- **App Service 미리 보기로 스왑**: 다단계 스왑으로 스테이징에 프로덕션 설정을 먼저 적용하여 검증; 트래픽 전환 전 커밋 또는 취소 가능; 표준 스왑은 검증 단계 없음
- **App Service 루트 도메인 확인**: Azure 제공 확인 ID가 포함된 `asuid`라는 이름의 TXT 레코드 사용; 루트(apex) 도메인은 CNAME 레코드 사용 불가
- **서비스 SAS 저장된 액세스 정책**: 서비스 SAS만 저장된 액세스 정책 지원으로 취소 가능; 계정 SAS와 사용자 위임 SAS는 만료 전 취소 불가
- **Azure Files Entra Kerberos**: 도메인 컨트롤러 없이 Entra에 가입된 디바이스에서 Azure Files SMB 접근 활성화; 클라우드 발급 Kerberos 티켓; 클라우드 전용 ID 환경
- **Azure File Sync 에이전트 구성 요소**: FileSyncSvc.exe(백그라운드 동기화 서비스) + StorageSync.sys(클라우드 계층화 및 빠른 재해 복구를 위한 파일 시스템 필터 드라이버)
- **Premium SSD 성능 계층 변경**: VM 실행 중에 디스크 크기 조정이나 VM 중지 없이 성능 계층 변경 가능(예: P15 → P20); 가동 중지 시간 없음
- **Action Group 알림 유형**: 이메일, SMS, 푸시(Azure 모바일 앱) = 알림 유형; 웹훅, Azure Function, Logic App = 작업(자동화) 유형
- **사용자 할당 관리 ID**: 독립적인 Azure 리소스로 사전 생성 가능, 여러 리소스에서 공유 가능, 배포 전 RBAC 구성 가능; 시스템 할당은 단일 리소스 수명 주기에 종속

#### ARM 템플릿, Bicep, 모니터링
- **ARM 연결 템플릿 출력**: `reference('<deploymentName>').outputs.<outputName>.value`로 연결된 배포 출력에 접근 — ARM JSON에서 `.value` 접미사 필수
- **Bicep 모듈 출력**: `<moduleName>.outputs.<outputName>`으로 접근 — Bicep에서는 `.value` 접미사 불필요(형식화된 값으로 직접 접근)
- **Bicep 모듈 교차 범위**: 구독 범위 부모 파일에서 다른 리소스 그룹에 리소스를 배포하기 위해 모듈에서 `scope: resourceGroup('RGName')` 사용
- **Bicep 기본 targetScope**: `targetScope` 생략 시 기본값은 `resourceGroup`; 구독/테넌트/관리 그룹 범위는 명시적으로 선언 필요
- **Bicep 구독 + RG 배포**: 구독 범위에서 리소스 그룹을 만들고, `scope: resourceGroup('Name')`이 있는 모듈로 리소스 그룹 수준 리소스 배포 — 하나의 배포에 공존 가능
- **VM Insights DCR 필수 구성 요소**: Azure Policy 기반 VM Insights 활성화에는 데이터 수집 규칙(DCR)이 필수; DCR은 VM에서 수집할 데이터를 정의
- **Azure Workbook 템플릿**: 미리 빌드된 KQL 쿼리, 메트릭, 차트, 매개변수가 포함된 재사용 가능한 청사진; 처음부터 다시 만들지 않고 Workbook 인스턴스 배포
- **AcrPush 역할**: CI/CD 파이프라인을 위한 최소 권한 ACR 역할 — 푸시/쓰기 및 가져오기/읽기 권한 모두 포함; AcrPull은 가져오기 전용; Contributor는 과도한 권한
- **사용자 할당 vs 시스템 할당 ID**: 사용자 할당 = 독립적 리소스, 사전 생성 가능, 여러 리소스에서 공유 가능; 시스템 할당 = 리소스와 함께 자동 생성, 단일 리소스 수명 주기, 공유 불가


---

## Section 28 — 2026 Gap Fill: Expected Questions Set 13

### English

#### Governance, Policy, and Resource Management
- **modify effect + Remediation Task**: Policy modify effect auto-tags new resources on create/update; existing non-compliant resources require a separate **remediation task** to apply retroactively
- **AuditIfNotExists**: checks whether a related resource (e.g., VM extension) exists for the primary resource — marks non-compliant but does NOT block deployments (unlike Deny)
- **Resource Lock scope**: locks can be applied to subscriptions, resource groups, and individual resources — **cannot be applied to management groups**; no inheritance from MG to subscription
- **ReadOnly vs Delete lock**: ReadOnly blocks all write operations including Automation job creation; changing to **Delete lock** allows writes/executes while still preventing resource deletion
- **Budget at Management Group scope**: supports email threshold alerts but **cannot use Action Groups** — Action Groups are only available at subscription and resource group scope
- **RBAC additive model**: Azure RBAC permissions accumulate — MG Reader + RG Contributor = Contributor at RG (Contributor already includes Reader); no override, just union
- **Locks require Owner or User Access Administrator**: `Microsoft.Authorization/locks/*` permission needed; Contributor does NOT have this; **User Access Administrator** is the least-privilege option

#### Identity, Entra ID, and RBAC
- **Role-assignable groups must use Assigned membership**: dynamic membership is explicitly prohibited for groups with `isAssignableToRole = true` to prevent automated elevation of privileges
- **Guest invite settings**: "Only users assigned to specific admin roles can invite guests" restricts invitations to designated admin roles; "Members and specific admin roles" is broader
- **User Administrator role**: minimum role for creating users, resetting passwords, and managing licenses simultaneously
- **Bulk create CSV required columns**: Name + User principal name + Initial password + Block sign in (4 required; Department, Usage location, Display name are optional)
- **Custom role with NotActions**: `Actions: virtualMachines/*` + `NotActions: virtualMachines/delete` = all VM permissions except delete; NotActions subtract from Actions within the same role
- **Container-scope RBAC for least privilege**: assigning Storage Blob Data Reader at the **container** scope limits access to that single container; storage-account scope grants access to all containers
- **Storage Blob Data Contributor + Reader ARM role**: Data Contributor alone cannot browse containers in the Azure portal; the **Reader (ARM) role** at storage account scope is needed for portal visibility

#### Networking
- **IP flow verify**: instantly identifies the specific NSG rule allowing or denying a simulated traffic flow (source IP, dest IP, port, protocol)
- **Disable BGP route propagation**: prevents on-premises BGP-learned routes from appearing in a subnet route table, forcing all routing through UDRs; essential when using an NVA
- **ASG membership constraint = same VNet**: all NICs in an ASG must exist in the same VNet; the constraint is VNet-based (not region or subscription); a VM can belong to multiple ASGs simultaneously
- **Application Gateway + App Service**: enable **"Pick host name from backend target"** in backend HTTP settings so the Host header is replaced with the App Service FQDN
- **Service endpoint policy limitation**: policies only filter traffic from Azure VNet subnets; **on-premises traffic bypasses service endpoint policies entirely**
- **Azure DNS record set max**: 20 records per record set (applies to A, AAAA, MX, TXT)
- **Zone apex alias record**: CNAME cannot be used at the zone apex; use an **Alias record** pointing to Traffic Manager, Azure CDN, or Front Door
- **IP forwarding on NVA NIC**: must be explicitly enabled in Azure on the NIC resource; without it, Azure drops packets addressed to other destinations

#### Storage, SAS, and Blob
- **Service SAS + Stored Access Policy**: only SAS type that supports stored access policies; deleting the policy revokes all linked SAS tokens **without rotating the storage account key**
- **User delegation SAS**: most secure SAS type (signed with Entra ID credentials, not account key); does NOT support stored access policies
- **Blob versioning on overwrite**: when versioning is enabled, overwriting creates a **previous version** (not a soft-deleted snapshot); soft delete only applies to deletions when versioning is active
- **Lifecycle management cannot rehydrate from Archive**: lifecycle policies only transition to cooler tiers (Hot→Cool→Archive); rehydration requires Set Blob Tier API, Copy Blob, or manual change
- **Service endpoint on subnet required first**: adding a VNet rule to a storage account firewall requires the Microsoft.Storage service endpoint to be enabled on the subnet first

#### Compute and App Service
- **Stateful metric alert fires once**: a stateful alert tracks state (Fired/Resolved); the action group is triggered once when it fires and once when it resolves — not on every evaluation interval
- **Azure Disk Encryption Key Vault access policy**: must enable **"Azure Disk Encryption for volume encryption"** access policy on the Key Vault before configuring ADE on a VM
- **Deployment slots require Standard tier or higher**: Basic and Free tiers do not support deployment slots; must scale up to Standard or Premium first
- **Swap with preview**: multi-phase swap that applies production settings to staging before routing traffic; allows validation before commit with option to cancel
- **App Service slot pinning**: `x-ms-routing-name` cookie pins users to a slot for **1 hour** (not browser session duration); deleted cookies or expiry triggers re-evaluation
- **SNI SSL for multiple domains**: SNI SSL uses the hostname in the TLS handshake to serve different certs on a single shared IP; IP-based SSL requires a dedicated IP per cert
- **ACI multi-container groups = Linux only**: Windows ACI containers are single-container only; Linux supports multi-container (sidecar) groups
- **ACR geo-replication requires Premium tier**: geo-replication is a Premium-only feature; Standard tier does not support it; must upgrade before enabling

#### Azure Bastion and Connectivity
- **Bastion Shareable Link (Standard SKU)**: generates a URL allowing access without an Azure portal account; not available on Basic SKU
- **Bastion Premium for session recording**: session recording + storage in customer-designated account is **Premium-only**; Standard has advanced features but no recording
- **Bastion Premium for private deployment**: no public IP on the Bastion host is also a Premium feature; Standard and Basic require public IP

#### Kubernetes (AKS)
- **System node pool taint**: `CriticalAddonsOnly=true:NoSchedule` is AKS's standard taint for system node pools; system pods have a matching toleration; application pods are blocked from scheduling there

#### Bicep and ARM Templates
- **Bicep module output syntax**: `moduleName.outputs.outputName` (no `.value` suffix); contrast with ARM linked deployments: `reference('deploymentName').outputs.outputName.value`
- **Bicep conditional output = ternary operator**: `output x = condition ? trueValue : falseValue`; `if(...)` is only for conditional resource/module deployment, not output values
- **Mixed scope in Bicep**: set `targetScope = 'subscription'`; create resource group as a top-level resource; use a module with `scope: resourceGroup('name')` for resources within that group

#### Monitoring and Azure Monitor
- **VM Insights default DCR = performance only**: enabling VM Insights with the default DCR collects guest OS performance metrics; processes/dependencies and event logs are disabled by default to reduce cost
- **KQL render timechart**: `| render timechart` renders time-series aggregation as a line chart; typical pattern: `summarize count() by bin(TimeGenerated, 1h) | render timechart`

#### Azure Files, Backup, and Recovery
- **AzFilesHybrid creates Computer account**: `Join-AzStorageAccountForAuth` creates a **computer account** (not user/service) in AD DS by default
- **Azure Files AD DS + computer account (LocalSystem)**: computer accounts cannot be synced to Entra ID → RBAC assignment not possible → use **default share-level permission** for all authenticated identities
- **File Recovery + Original Location**: for snapshot-based Azure Files backup, restoring individual deleted files uses File Recovery with Original Location
- **Azure Files Backup = SMB only**: Azure Backup for Azure Files supports SMB file shares; NFS file shares are not supported
- **On-demand backup retention is independent**: on-demand recovery point retention is fixed at creation time; subsequent policy retention changes do NOT affect existing on-demand recovery points

---

### 한국어

#### 거버넌스, 정책, 리소스 관리
- **modify 효과 + 수정 작업(Remediation Task)**: modify 정책 효과는 새로 만들거나 업데이트되는 리소스에 자동 태그 적용; 기존 비준수 리소스에 소급 적용하려면 **수정 작업(remediation task)** 이 별도로 필요
- **AuditIfNotExists**: 기본 리소스에 대한 관련 리소스(예: VM 확장)가 존재하는지 확인 — 비준수로 표시하지만 배포를 차단하지 않음(Deny와 달리)
- **리소스 잠금 범위**: 구독, 리소스 그룹, 리소스에 적용 가능 — **관리 그룹에는 적용 불가**; 관리 그룹에서 구독으로 상속 없음
- **ReadOnly vs Delete 잠금**: ReadOnly는 Automation 작업 생성 포함 모든 쓰기 차단; **Delete 잠금**으로 변경하면 쓰기/실행은 허용하면서 삭제만 방지
- **관리 그룹 범위 예산**: 이메일 임계값 알림 지원하지만 **Action Group 사용 불가** — Action Group은 구독 및 리소스 그룹 범위에서만 가능
- **RBAC 누적(additive) 모델**: 권한이 합산됨 — MG Reader + RG Contributor = RG에서 Contributor(Contributor에 Reader 포함); 재정의가 아닌 합집합
- **잠금에는 Owner 또는 User Access Administrator 필요**: `Microsoft.Authorization/locks/*` 권한 필요; Contributor에는 없음; **User Access Administrator**가 최소 권한 옵션

#### ID, Entra ID 및 RBAC
- **역할 할당 가능 그룹은 Assigned 구성원 자격 필수**: `isAssignableToRole = true` 그룹에는 동적 구성원 자격 명시적으로 금지 — 의도치 않은 권한 상승 방지
- **게스트 초대 설정**: "특정 관리자 역할에 할당된 사용자만 게스트 초대 가능"이 가장 제한적; "구성원 및 특정 관리자 역할"은 더 넓은 범위
- **User Administrator 역할**: 사용자 만들기, 암호 재설정, 라이선스 관리를 동시에 수행하는 최소 역할
- **대량 만들기 CSV 필수 열**: Name + UPN + 초기 암호 + 로그인 차단(4개 필수; Department, 사용 위치, 표시 이름은 선택)
- **NotActions가 있는 사용자 지정 역할**: `Actions: virtualMachines/*` + `NotActions: virtualMachines/delete` = 삭제 제외 모든 VM 권한; NotActions는 동일 역할 내 Actions에서 빼냄
- **최소 권한을 위한 컨테이너 범위 RBAC**: 컨테이너 범위에서 Storage Blob Data Reader 할당 시 해당 컨테이너만 제한 접근; Storage Account 범위는 모든 컨테이너 접근 부여
- **Storage Blob Data Contributor + Reader(ARM) 역할**: Data Contributor만으로는 Azure Portal에서 컨테이너를 탐색할 수 없음; 포털 가시성을 위해 Storage Account 범위의 **Reader(ARM) 역할**이 필요

#### 네트워킹
- **IP 흐름 확인**: 시뮬레이션된 트래픽 흐름(소스 IP, 대상 IP, 포트, 프로토콜)을 허용 또는 거부하는 특정 NSG 규칙을 즉시 식별
- **BGP 경로 전파 비활성화**: 온-프레미스 BGP 학습 경로가 서브넷 경로 테이블에 나타나지 않도록 방지, 모든 라우팅을 UDR을 통해 강제; NVA 사용 시 필수
- **ASG 구성원 제약 = 동일 VNet**: ASG의 모든 NIC는 동일한 VNet에 있어야 함; 지역이나 구독이 아닌 VNet 기반 제약; VM은 여러 ASG에 동시에 속할 수 있음
- **Application Gateway + App Service**: 백엔드 HTTP 설정에서 **"백엔드 대상에서 호스트 이름 선택"** 활성화로 Host 헤더를 App Service FQDN으로 교체
- **서비스 엔드포인트 정책 제한**: Azure VNet 서브넷의 트래픽만 필터링; **온-프레미스 트래픽은 서비스 엔드포인트 정책을 완전히 우회**
- **Azure DNS 레코드 집합 최대값**: 레코드 집합당 최대 20개 레코드(A, AAAA, MX, TXT에 적용)
- **영역 루트 Alias 레코드**: 영역 루트에 CNAME 사용 불가; Traffic Manager, Azure CDN, Front Door를 가리키는 **Alias 레코드** 사용
- **NVA NIC의 IP 전달**: Azure NIC 리소스에서 명시적으로 활성화해야 함; 없으면 Azure가 다른 대상으로 주소가 지정된 패킷을 삭제

#### Storage, SAS 및 Blob
- **Service SAS + 저장된 액세스 정책**: 저장된 액세스 정책을 지원하는 유일한 SAS 유형; 정책 삭제 시 연결된 모든 SAS 토큰 즉시 취소 — **Storage Account 키 순환 불필요**
- **User Delegation SAS**: 가장 안전한 SAS 유형(계정 키가 아닌 Entra ID 자격 증명으로 서명); 저장된 액세스 정책 지원 안 함
- **덮어쓰기 시 Blob 버전 관리**: 버전 관리 활성화 시 덮어쓰면 **이전 버전** 생성(일시 삭제 스냅샷 아님); 버전 관리 활성 상태에서 일시 삭제는 삭제에만 적용
- **수명 주기 관리로 Archive에서 리하이드레이션 불가**: 수명 주기 정책은 더 차가운 계층으로만 전환(Hot→Cool→Archive); 리하이드레이션은 Set Blob Tier API, Copy Blob 또는 수동으로 해야 함
- **먼저 서브넷에 서비스 엔드포인트 필요**: Storage Account 방화벽에 VNet 규칙 추가 전에 해당 서브넷에 Microsoft.Storage 서비스 엔드포인트 먼저 활성화 필요

#### 컴퓨팅 및 App Service
- **상태 저장 메트릭 경고는 한 번만 발생**: 상태 저장 경고는 상태(Fired/Resolved)를 추적; 발생 시 한 번, 해결 시 한 번 Action Group 트리거 — 모든 평가 간격마다 아님
- **Azure Disk Encryption Key Vault 액세스 정책**: VM에 ADE 구성 전에 Key Vault에서 **"볼륨 암호화를 위한 Azure Disk Encryption"** 액세스 정책 활성화 필요
- **배포 슬롯은 Standard 계층 이상 필요**: Basic 및 Free 계층은 배포 슬롯 지원 안 함; 먼저 Standard 또는 Premium으로 스케일 업 필요
- **미리 보기로 교체(Swap with preview)**: 트래픽 라우팅 전에 프로덕션 설정을 스테이징에 적용하는 다단계 교체; 커밋 전 검증 및 취소 옵션 제공
- **App Service 슬롯 고정**: `x-ms-routing-name` 쿠키가 사용자를 슬롯에 **1시간** 동안 고정(브라우저 세션 기간 아님); 쿠키 삭제 또는 만료 시 재평가
- **여러 도메인을 위한 SNI SSL**: SNI SSL은 TLS 핸드셰이크의 호스트 이름을 사용해 단일 공유 IP에서 다른 인증서 제공; IP 기반 SSL은 인증서당 전용 IP 필요
- **ACI 다중 컨테이너 그룹 = Linux만**: Windows ACI 컨테이너는 단일 컨테이너만 가능; Linux는 다중 컨테이너(사이드카) 그룹 지원
- **ACR 지역 복제는 Premium 계층 필요**: 지역 복제는 Premium 전용 기능; Standard 계층은 지원 안 함; 활성화 전에 업그레이드 필요

#### Azure Bastion 및 연결
- **Bastion 공유 링크(Standard SKU)**: Azure Portal 계정 없이 액세스 가능한 URL 생성; Basic SKU에서는 사용 불가
- **세션 녹화를 위한 Bastion Premium**: 고객 지정 Storage Account에 녹화 저장은 **Premium 전용**; Standard는 고급 기능 있지만 녹화 없음
- **프라이빗 배포를 위한 Bastion Premium**: Bastion 호스트에 공용 IP 없는 것도 Premium 기능; Standard와 Basic은 공용 IP 필요

#### Kubernetes(AKS)
- **시스템 노드 풀 테인트**: `CriticalAddonsOnly=true:NoSchedule`이 시스템 노드 풀의 AKS 표준 테인트; 시스템 Pod는 매칭 톨러레이션 보유; 애플리케이션 Pod는 스케줄링 차단

#### Bicep 및 ARM 템플릿
- **Bicep 모듈 출력 구문**: `moduleName.outputs.outputName`(`.value` 접미사 없음); ARM 연결 배포의 `reference('deploymentName').outputs.outputName.value`와 대조
- **Bicep 조건부 출력 = 삼항 연산자**: `output x = condition ? trueValue : falseValue`; `if(...)`는 조건부 리소스/모듈 배포에만 사용, 출력 값에는 미사용
- **Bicep 혼합 범위**: `targetScope = 'subscription'` 설정; 리소스 그룹을 최상위 리소스로 만들기; 해당 그룹 내 리소스에는 `scope: resourceGroup('name')`이 포함된 모듈 사용

#### 모니터링 및 Azure Monitor
- **VM Insights 기본 DCR = 성능만**: 기본 DCR로 VM Insights 활성화 시 게스트 OS 성능 메트릭만 수집; 프로세스/종속성 및 이벤트 로그는 비용 절감을 위해 기본적으로 비활성화
- **KQL render timechart**: `| render timechart`는 시간 계열 집계를 꺾은선형 차트로 렌더링; 일반적인 패턴: `summarize count() by bin(TimeGenerated, 1h) | render timechart`

#### Azure Files, 백업 및 Recovery Services
- **AzFilesHybrid는 컴퓨터 계정 만들기**: `Join-AzStorageAccountForAuth`는 AD DS에 기본적으로 **컴퓨터 계정**(사용자/서비스 아님) 만들기
- **Azure Files AD DS + 컴퓨터 계정(LocalSystem)**: 컴퓨터 계정은 Entra ID에 동기화 불가 → RBAC 할당 불가 → 모든 인증된 ID에 **기본 공유 수준 권한** 사용
- **파일 복구 + 원래 위치**: 스냅샷 기반 Azure Files 백업에서 삭제된 개별 파일 복원에는 원래 위치를 사용한 파일 복구 사용
- **Azure Files 백업 = SMB만**: Azure Files의 Azure Backup은 SMB 파일 공유 지원; NFS 파일 공유는 미지원
- **on-demand backup 보존은 독립적**: on-demand recovery point 보존은 만들 때 고정; 이후 정책 보존 변경이 기존 on-demand recovery point에 영향을 미치지 않음

*Document generated from AZ-104 course transcript. Last updated: 2026-05-22*

---

## Section 27 — 2026 Gap Fill: PIM, Key Vault RBAC, App Insights, Service Health

### 한국어

#### Microsoft Entra PIM (Privileged Identity Management)
- 목적: 상시 관리자 권한 제거, 적격(Eligible) 할당 + JIT(Just-In-Time) 활성화.
- 활성화 정책: MFA, 업무 사유 입력, 승인자 승인, 활성화 시간 제한.
- 거버넌스: 활성화 이력 감사(Audit), 경고(Alerts), Access Review.
- 적용 대상: Entra 디렉터리 역할, Azure 리소스 역할(관리 그룹/구독/RG/리소스), 지원되는 그룹 멤버십/소유권.
- 라이선스: Microsoft Entra ID P2 또는 Entra ID Governance.

#### Key Vault 권한 모델 (RBAC vs Access Policy)
- 세분화된 권한(개별 비밀/키/인증서) + PIM 운영 요구 시 정답은 Azure RBAC 모델.
- Access Policy 모델은 레거시 방식이며 PIM 기반 운영 요구와 맞지 않음.
- 시험 패턴: "비밀 단위 권한 + PIM" -> Azure RBAC.

#### Key Vault 오브젝트 단위 Scope 예시
- Secret: /.../vaults/{kv}/secrets/{secret}
- Key: /.../vaults/{kv}/keys/{key}
- Certificate: /.../vaults/{kv}/certificates/{cert}
- 핵심: az role assignment로 해당 scope에 역할을 주면 오브젝트 단위 최소 권한 가능.

#### Application Insights (APM) 핵심
- 성능 모니터링(응답 시간, 의존성 호출), 예외 로깅, 사용자 분석, Live Metrics, Application Map.
- 마이크로서비스 상호 연결의 본체는 분산 추적(Distributed Tracing): operation_Id + W3C(traceparent/tracestate).
- Application Map은 시각화 계층이며, 실제 연결 데이터는 분산 추적에서 생성.

#### Application Insights 샘플링 정리
- 요구가 "예측 가능한 50% 절감 + 상호연결 로그 보존"이면 SDK 고정 비율 샘플링(Fixed-rate) 패턴이 정답.
- Adaptive Sampling은 트래픽 기반으로 비율이 동적으로 변해 자동 제어에는 유리하지만 고정 목표치에는 덜 결정적.
- Ingestion Sampling은 수집 후 드롭이라 E2E 추적 보존 관점에서 상대적으로 불리할 수 있음.

#### Service Health 사전 알림
- 서비스 중단/계획된 유지 관리 사전 알림 서비스: Azure Service Health.
- 구성: Service Health Alert Rule + Action Group(이메일/SMS/Webhook).

#### Traffic Manager 우선순위 라우팅 동작
- Priority 모드: 가장 높은 우선순위의 정상 엔드포인트로 DNS 응답.
- Priority 1 장애 시 Priority 2로 자동 장애 조치.
- DNS 기반 특성상 사용자 전환 속도는 TTL/캐시 만료의 영향.
- Priority 1 복구 시 주 엔드포인트로 failback 가능.

#### 단일 전역 HTTP/S 서비스로 WAF + 가속 + 에지 SSL + 분할 라우팅
- 정답: Azure Front Door.
- 근거: 글로벌 Anycast 에지 가속 + WAF + 에지 TLS 종료 + 가중치/규칙 기반 분할 라우팅(A/B, 카나리).

#### KQL 예시: 지난 24시간 컴퓨터별 Error 집계
```kusto
Event
| where TimeGenerated > ago(24h)
| where EventLevelName == "Error"
| summarize ErrorCount = count() by Computer
| sort by ErrorCount desc
```

*Document generated from AZ-104 course transcript. Last updated: 2026-06-08*
