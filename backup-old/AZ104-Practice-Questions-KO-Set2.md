# AZ-104 예상 문제 (한국어) - Set 2

> AZ-104 최신 스킬(2026-04-17) 기준 오리지널 문제 세트
> 40문제 + 간결 해설

---

## 1) ID 및 거버넌스

### 문제 1
한 부서 사용자에 대해서만 헬프데스크가 비밀번호를 재설정하게 해야 합니다.
무엇을 사용해야 합니까?

- A. 관리 그룹
- B. 관리 단위(Administrative Unit)
- C. 리소스 잠금
- D. Azure Policy

**정답: B**
**해설:**
**관리 단위(Administrative Unit, AU)**는 Entra ID 개념으로, Azure 구독 RBAC/리소스와는 완전히 별개입니다.
- AU는 Entra 사용자·그룹·디바이스의 하위 집합을 담는 컨테이너
- AU에 Entra 관리 역할(예: Password Administrator)을 할당하면, 그 관리자는 해당 AU 안의 사용자에게만 권한을 행사할 수 있음
- 예: 인사팀 AU에 Password Admin을 배정하면, 그 관리자는 인사팀 사용자의 비밀번호만 재설정 가능 (엔지니어링팀 불가)
- 구독 RBAC와는 관계없음 — VM 생성/삭제 같은 리소스 접근 제어는 별도의 RBAC 역할로 관리
- 포털 경로: Entra ID → Administrative Units → 만들기 → 멤버 추가 → 역할 할당

### 문제 2
사용자가 구독 범위에서 Reader, 특정 리소스 그룹에서 Contributor 권한을 갖습니다.
해당 리소스 그룹에서 가능한 작업은?

- A. 읽기만 가능
- B. 해당 리소스 그룹의 리소스 생성/수정/삭제 가능
- C. 역할 할당 가능
- D. 구독 삭제 가능

**정답: B**
**해설:** 하위 범위 Contributor가 해당 범위의 쓰기 작업을 허용합니다. 역할 할당은 Owner/User Access Administrator 필요.

### 문제 3
새 리소스 그룹에 `CostCenter` 태그가 반드시 있어야 합니다.
가장 적절한 방법은?

- A. 리소스 잠금
- B. Deny 효과의 Azure Policy
- C. NSG
- D. 관리 그룹 이름 변경

**정답: B**
**해설:** Azure Policy로 태그 미포함 생성/수정을 거부할 수 있습니다.

### 문제 4
리소스 잠금에 대한 올바른 설명은?

- A. ReadOnly 잠금은 포털에서는 수정 가능
- B. Delete 잠금은 모든 업데이트 차단
- C. 잠금은 제어 평면 작업에 적용
- D. 잠금은 상속되지 않음

**정답: C**
**해설:**
Azure 작업에는 두 개의 평면이 있습니다:

- **제어 평면(Control plane / Management plane)**: ARM API를 통한 리소스 관리 작업 — 리소스 생성, 삭제, 설정 변경 (예: VM 크기 변경, 스토리지 계정 삭제, Key Vault 접근 정책 수정)
- **데이터 평면(Data plane)**: 리소스 내부의 실제 데이터 접근 작업 — 블롭 읽기/쓰기, DB 쿼리, 큐 메시지 전송

리소스 잠금은 **제어 평면에만 적용**됩니다. 예시:
- 스토리지 계정에 **ReadOnly 잠금** → 계정 설정 변경 불가(제어 평면 차단)이지만 블롭 데이터 읽기/쓰기는 여전히 가능(데이터 평면 미차단)
- Key Vault에 **Delete 잠금** → Key Vault 삭제 불가이지만 시크릿 조회는 가능

시험 함정: "잠금이 걸려 있으면 데이터 접근도 안 된다"는 오해를 노리는 문제가 자주 출제됩니다.

### 문제 5
구독별 예산과 지출 경고를 중앙에서 관리하려면?

- A. Azure DNS
- B. Cost Management + Billing
- C. Network Watcher
- D. App Service Plan

**정답: B**
**해설:** 예산, 경고, 비용 분석은 Cost Management에서 설정합니다.

### 문제 6
한 구독에서 만든 사용자 지정 역할을 다른 구독에 기본적으로 바로 할당할 수 있습니까?

- A. 예
- B. 아니요
- C. Owner면 항상 가능
- D. Initiative면 가능

**정답: B**
**해설:** 사용자 지정 역할은 생성 스코프에 종속됩니다(상위 스코프로 만들지 않는 한 자동 공유되지 않음).

### 문제 7
위험 위치에서 로그인할 때만 MFA를 요구하려고 합니다.
가장 적합한 방법은?

- A. 사용자별 MFA
- B. 조건부 액세스
- C. SSPR
- D. PIM만 사용

**정답: B**
**해설:**
**조건부 액세스(Conditional Access)**는 Entra ID의 if-then 정책 엔진입니다:
- *만약* 조건이 충족되면(사용자, 위치, 디바이스, 앱, 로그인 위험도) → *그러면* 부여 제어 또는 세션 제어를 적용

**부여 제어(Grant controls) — 어떤 조건을 충족해야 접근 허용:**
- MFA 필요 (가장 일반적)
- 규격 준수 디바이스 필요 (Intune 등록)
- 하이브리드 Azure AD 조인 디바이스 필요
- 승인된 클라이언트 앱 필요
- 앱 보호 정책 필요
- 비밀번호 변경 필요 (위험 로그인)

**사용자별 MFA(Per-user MFA)**는 레거시 방식으로, 조건 없이 항상 MFA를 요구합니다. 조건부 액세스가 훨씬 유연하여 Microsoft의 권장 방식입니다.

### 문제 8
운영 리소스 그룹의 실수 삭제를 막되 수정은 허용해야 합니다.

- A. ReadOnly 잠금
- B. Delete 잠금
- C. Deny assignment
- D. NSG 규칙

**정답: B**
**해설:** Delete 잠금은 삭제만 차단하고 수정은 허용합니다.

---

## 2) 스토리지

### 문제 9
계정 키 노출 없이 단일 Blob 컨테이너에 임시 읽기 권한을 주려면?

- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Shared key만 사용

**정답: C**
**해설:**
SAS 세 가지 유형 모두 단일 컨테이너 접근이 가능한데 왜 Service SAS(B)가 틀릴까요?

| SAS 유형 | 서명 방법 | 계정 키 사용? | 비고 |
|---------|---------|------------|-----|
| Account SAS | 계정 키 | 예 | 가장 넓은 범위; 키 필요 |
| Service SAS | 계정 키 | 예 | 단일 서비스 범위; 키 필요 |
| User Delegation SAS | Entra 자격증명 | **아니요** | AAD 토큰 서명; 키 불필요 |

문제의 핵심 조건은 **"계정 키 노출 없이"**입니다. Account SAS와 Service SAS 모두 계정 키로 서명되어 생성 시 반드시 키가 사용됩니다. **User Delegation SAS**만이 Entra OAuth 토큰으로 서명하므로 계정 키에 전혀 접근하지 않아도 됩니다. Microsoft의 권장 방식입니다.

### 문제 10
계정 키 교체 없이 여러 SAS를 빠르게 통제/철회하려면?

- A. NSG
- B. Stored Access Policy
- C. ZRS
- D. Blob versioning

**정답: B**
**해설:** Stored Access Policy로 SAS의 만료/권한을 중앙 관리할 수 있습니다.

### 문제 11
단일 리전 내에서 영역 수준 내구성을 제공하는 복제는?

- A. LRS
- B. ZRS
- C. GRS
- D. RA-GRS

**정답: B**
**해설:** ZRS는 하나의 리전 내 여러 가용성 영역에 복제합니다.

### 문제 12
리전 장애 시 보조 리전 읽기가 필요합니다.
어떤 옵션을 선택해야 합니까?

- A. GRS
- B. ZRS
- C. RA-GRS
- D. LRS

**정답: C**
**해설:** RA-GRS는 보조 리전 읽기 액세스를 제공합니다.

### 문제 13
앱 구성요소를 비동기 메시지로 분리(디커플링)하는 데 가장 적절한 서비스는?

- A. Azure Files
- B. Blob Archive
- C. Queue Storage
- D. Table Storage

**정답: C**
**해설:** Queue Storage는 비동기 메시징에 최적화된 서비스입니다.

### 문제 14
Table Storage의 기본 키 조합은?

- A. TableName + Index
- B. PartitionKey + RowKey
- C. RowId + GUID
- D. ResourceId + Timestamp

**정답: B**
**해설:** 엔터티 식별/조회는 PartitionKey와 RowKey 조합을 사용합니다.

### 문제 15
삭제된 Blob을 14일 동안 복구 가능하게 유지하려면?

- A. Blob soft delete
- B. File snapshot만
- C. LRS
- D. Queue TTL

**정답: A**
**해설:** Blob soft delete 보존 기간을 설정하면 삭제 후 복구 가능합니다.

### 문제 16
오래된 Blob을 Hot에서 Cool로 자동 전환하려면?

- A. Azure Policy initiative
- B. Blob lifecycle management
- C. NSG 아웃바운드 규칙
- D. UDR

**정답: B**
**해설:** Lifecycle 규칙으로 계층 자동 전환이 가능합니다.

---

## 3) 컴퓨팅, 컨테이너, App Service

### 문제 17
같은 구독 내에서 VM을 다른 리소스 그룹으로 이동해야 합니다.
올바른 설명은?

- A. 지원되지 않음
- B. 종속 리소스 조건 충족 시 이동 지원
- C. 항상 재배포 필요
- D. 게스트 OS 종료만 하면 됨

**정답: B**
**해설:**
VM 이동은 지원되지만 **종속 리소스를 함께 이동**해야 합니다.

**VM 이동 시 함께 이동해야 할 리소스:**
- 관리형 디스크(OS 디스크 + 데이터 디스크)
- NIC
- 공용 IP (있는 경우)
- NIC에 연결된 NSG
- VNet은 이동하거나, 대상 RG에 이미 존재해야 함

**이동 제약:**
- VM을 반드시 중지할 필요는 없지만 중지 후 이동이 안전함
- 다른 Azure AD 테넌트로는 이 방법으로 이동 불가
- 이동 전 유효성 검사 실행 — 블로커가 있으면 목록 표시
- 포털 경로: 리소스 그룹 → 리소스 선택 → 이동 → 다른 리소스 그룹으로 이동

**시험 함정:** "지원되지 않음"과 "항상 재배포" 모두 오답입니다.

### 문제 18
관리형 디스크를 고객 관리 키(CMK)로 암호화하려면 추가로 필요한 것은?

- A. Disk Encryption Set
- B. Route table
- C. Application Gateway
- D. Log Analytics workspace

**정답: A**
**해설:** Disk Encryption Set이 Key Vault 키와 디스크를 연결합니다.

### 문제 19
VM Scale Set의 기본 스케일 인 정책은 어떤 인스턴스를 제거합니까?

- A. 가장 오래된 인스턴스
- B. 가장 최신 인스턴스
- C. 인스턴스 ID가 가장 높은 인스턴스
- D. CPU가 가장 낮은 인스턴스

**정답: C**
**해설:** 정책을 바꾸지 않으면 기본은 highest instance ID 제거입니다.

### 문제 20
일회성 배치 작업을 빠르게 서버리스 컨테이너로 실행하려면?

- A. AKS
- B. ACI
- C. VM Scale Sets
- D. App Service Web App

**정답: B**
**해설:** ACI는 단순/신속 컨테이너 실행에 적합합니다.

### 문제 21
리비전과 이벤트 기반 스케일이 필요한 관리형 마이크로서비스 플랫폼은?

- A. Azure Container Apps
- B. Azure Files
- C. Traffic Manager
- D. Bastion

**정답: A**
**해설:** Container Apps는 관리형 실행 환경, 스케일, 리비전을 제공합니다.

### 문제 22
컨테이너 이미지의 프라이빗 저장소로 적합한 서비스는?

- A. Azure DNS
- B. Azure Container Registry
- C. Blob public container
- D. Queue Storage

**정답: B**
**해설:** ACR은 Azure의 관리형 프라이빗 컨테이너 레지스트리입니다.

### 문제 23
프로덕션 스왑 전 무중단 검증 배포를 하려면 App Service의 어떤 기능을 사용합니까?

- A. Deployment slots
- B. NSG
- C. Backup vault
- D. Availability set

**정답: A**
**해설:**
**Azure App Service**는 Microsoft의 완전 관리형 PaaS 플랫폼으로, 웹 앱·REST API·모바일 백엔드를 호스팅합니다. OS 패치, 스케일링, 로드 밸런싱을 Azure가 대신 처리하며, 개발자는 코드만 배포하면 됩니다.

**Deployment Slots(배포 슬롯)**는 같은 App Service 내의 별도 실행 환경입니다:
- 기본 슬롯 = **production** (`myapp.azurewebsites.net`)
- 추가 슬롯 예: `staging` → `myapp-staging.azurewebsites.net`
- 흐름: staging 슬롯에 배포 → 통합 테스트 → **swap** 실행 → production 전환 (재배포 아님, 라우팅 전환)
- **슬롯 고정 설정(Sticky settings)**: 연결 문자열, 특정 앱 설정은 swap해도 슬롯에 유지 (DB 연결이 staging용 DB를 계속 가리키게 하는 용도)
- **트래픽 분할**: 예) 10%를 staging 슬롯으로 보내어 A/B 테스트 가능

무중단 프로덕션 전환이 핵심 목적입니다.

### 문제 24
vNet에서 App Service로 인바운드 사설 접근을 원할 때 선택은?

- A. VNet integration만
- B. Private Endpoint
- C. Service Endpoint
- D. Route-based VPN

**정답: B**
**해설:** 인바운드 사설 IP 접근은 Private Endpoint가 핵심입니다.

---

## 4) 가상 네트워킹

### 문제 25
스토리지의 퍼블릭 엔드포인트는 유지하되, 특정 서브넷 트래픽만 Microsoft 백본으로 허용하려면?

- A. Private Endpoint
- B. Service Endpoint
- C. vNet peering
- D. NAT Gateway

**정답: B**
**해설:** Service Endpoint는 서비스의 퍼블릭 엔드포인트를 유지하면서 서브넷 기반 접근 제한이 가능합니다.

### 문제 26
스토리지 퍼블릭 접근을 끄고 vNet의 사설 IP로만 접근하려면?

- A. Service Endpoint
- B. Private Endpoint
- C. NSG만 구성
- D. Public Load Balancer

**정답: B**
**해설:** Private Endpoint가 서비스에 사설 IP를 할당합니다.

### 문제 27
Azure Firewall과 NSG의 관계로 맞는 것은?

- A. 동일 리소스 유형
- B. NSG가 항상 Firewall을 완전히 대체
- C. 상호 보완 관계
- D. Firewall은 인바운드만 가능

**정답: C**
**해설:** NSG는 분산형 L3/L4 필터, Firewall은 중앙 고급 정책 제어 역할입니다.

### 문제 28
Gateway subnet의 올바른 용도는?

- A. VPN 전용
- B. ExpressRoute 전용
- C. 가상 네트워크 게이트웨이 리소스용(VPN/ER)
- D. Bastion 전용

**정답: C**
**해설:** GatewaySubnet은 게이트웨이 리소스용 전용 서브넷입니다.

### 문제 29
VPN/ER 게이트웨이 배포를 위해 필수인 서브넷 이름은?

- A. VNetGatewaySubnet
- B. GatewaySubnet
- C. AzureGateway
- D. 임의 이름 가능

**정답: B**
**해설:** 이름은 정확히 GatewaySubnet이어야 합니다.

### 문제 30
IP 기반이 아닌 애플리케이션 역할 기준으로 NSG 규칙을 단순화하려면?

- A. UDR
- B. ASG
- C. Azure Policy
- D. Load Balancer 규칙

**정답: B**
**해설:** ASG는 NIC를 논리 그룹으로 묶어 NSG 참조를 단순화합니다.

### 문제 31
NSG 적용 가능 범위는?

- A. vNet만
- B. NIC만
- C. Subnet과 NIC
- D. Storage account만

**정답: C**
**해설:** NSG는 서브넷/ NIC 둘 다에 연결할 수 있습니다.

### 문제 32
아웃바운드 트래픽 차단 원인을 분석할 때 우선 확인할 것은?

- A. Effective routes + effective security rules
- B. DNS zone만
- C. Blob index tags
- D. Backup reports

**정답: A**
**해설:** 실제 흐름은 라우팅 결과와 NSG 유효 규칙의 결합으로 결정됩니다.

---

## 5) 로드밸런서, DNS, 모니터링, 백업

### 문제 33
Standard Load Balancer의 zone-redundant frontend 의미는?

- A. 백엔드 VM이 자동으로 다중 영역 복제됨
- B. 영역 장애 시에도 프런트엔드 IP 접근성이 유지됨
- C. 내부 LB만 지원
- D. Basic SKU 필요

**정답: B**
**해설:** 프런트엔드 가용성이 높아지지만 백엔드도 별도 중복 설계가 필요합니다.

### 문제 34
Standard LB 백엔드 VM에 Basic 공용 IP를 붙였습니다. 결과는?

- A. 정상 지원
- B. 비호환
- C. 단일 영역에서만 동작
- D. 재부팅 후 동작

**정답: B**
**해설:** Standard LB는 Standard 공용 IP(또는 공용 IP 없음) 조합을 요구합니다.

### 문제 35
인터넷 도메인 contoso.com을 Azure DNS로 위임하려면?

- A. TXT 레코드만 추가
- B. 레지스트라 NS 레코드를 Azure DNS 네임서버로 변경
- C. UDR 추가
- D. Bastion 활성화

**정답: B**
**해설:** 권한 DNS 위임은 레지스트라의 NS 변경이 핵심입니다.

### 문제 36
VM 공용 IP 없이 안전한 RDP/SSH 관리 접속이 필요합니다.
가장 적합한 서비스는?

- A. NAT Gateway
- B. Azure Bastion
- C. Front Door
- D. ExpressRoute

**정답: B**
**해설:** Bastion은 브라우저 기반 보안 관리 접속을 제공합니다.

### 문제 37
특정 트래픽이 NSG에서 허용/거부되는지 확인하는 도구는?

- A. Packet Capture
- B. Connection Monitor
- C. IP Flow Verify
- D. Topology

**정답: C**
**해설:** IP Flow Verify는 매칭된 NSG 규칙과 허용/거부 결과를 보여줍니다.

### 문제 38
VM 정기 백업 정책과 복원 지점을 운영하려면 기본적으로 어떤 서비스를 사용합니까?

- A. Azure Policy
- B. Recovery Services Vault
- C. Route table
- D. DNS private zone

**정답: B**
**해설:** Azure Backup은 Recovery Services Vault 기반으로 구성합니다.

### 문제 39
ASR 테스트 장애 조치(Test failover)의 목적은?

- A. 원본 VM 영구 삭제
- B. 운영 영향 최소화 상태에서 DR 절차 검증
- C. 대상 네트워크 없이 수행
- D. 복제 비활성화

**정답: B**
**해설:** Test failover는 운영 영향 없이 복구 계획을 검증합니다.

### 문제 40
CPU 임계치 경보 발생 시 이메일과 웹훅을 보내려면 무엇을 구성해야 합니까?

- A. Azure Monitor Alert의 Action Group
- B. NSG Flow Logs만
- C. Storage Lifecycle 규칙
- D. Azure DNS 레코드

**정답: A**
**해설:** 경보 규칙은 Action Group(이메일/SMS/웹훅 등)을 트리거합니다.

---

Set 1과 함께 오답을 도메인별로 분류해 재학습하면 효율이 높습니다.