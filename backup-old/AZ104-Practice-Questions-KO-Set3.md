# AZ-104 연습 문제 — 한국어 Set 3

**커버리지**: Set 1·2에서 부족했던 주제 — 관리 ID, Key Vault, Entra Connect, Bicep/ARM, AKS, App Service 심화, NAT Gateway, ExpressRoute, VPN P2S, Traffic Manager, Azure Front Door, Private DNS, NSG Flow Logs, 백업 심화, VM 호스트 암호화, 스토리지 복제, AzCopy, Azure Policy, Azure Advisor, Monitor Insights, KQL, 진단 설정, 경고 처리 규칙, Azure Migrate.

총 40문항 | 합격 목표: 700/1000

---

## 1) 신원 및 거버넌스 (심화)

### 문제 1

VM이 코드나 설정 어디에도 자격 증명을 저장하지 않고 Azure Key Vault에 액세스해야 합니다. 어떤 기능이 이를 가능하게 합니까?

- A. Service SAS
- B. 시스템 할당 관리 ID
- C. B2B 게스트 계정
- D. 계정 키

**정답: B**
**해설:**
**관리 ID(Managed Identity)**는 Azure가 자동으로 관리하는 Entra ID ID입니다 — 개발자가 자격 증명을 교체하거나 저장할 필요가 없습니다.

- **시스템 할당(System-assigned)**: 특정 리소스(VM, App Service 등)의 수명에 연결됨; 리소스 삭제 시 함께 삭제
- **사용자 할당(User-assigned)**: 독립적으로 생성; 여러 리소스에서 공유 가능; 독립적으로 유지

**Key Vault 접근 워크플로:**

1. VM에 시스템 할당 ID 활성화
2. Key Vault → 액세스 정책: VM의 관리 ID에 Get/List 시크릿 권한 추가 (또는 RBAC: Key Vault Secrets User 역할)
3. 앱 코드에서 Azure 인스턴스 메타데이터 서비스(IMDS) 엔드포인트로 토큰 획득 — 코드에 자격 증명 없음

**시험 함정**: 클라이언트 시크릿을 가진 서비스 주체도 가능하지만 자격 증명 관리가 필요합니다. 관리 ID가 자격 증명 없는 접근의 권장 방식입니다.

---

### 문제 2

사용자 할당 관리 ID `app-identity`를 만들었습니다. `vm-web`과 `vm-api` 모두 이 ID를 사용하게 하려면 어떻게 해야 합니까?

- A. 각 VM에 별도의 시스템 할당 ID 만들기
- B. `app-identity`를 `vm-web`과 `vm-api` 모두에 연결
- C. 보안을 위해 VM당 새 관리 ID 만들기
- D. 인증서가 있는 서비스 주체를 두 VM에 사용

**정답: B**
**해설:**
**사용자 할당 관리 ID**는 여러 리소스에서 하나의 ID를 공유해야 할 때 올바른 선택입니다. 각 리소스에 독립적으로 연결하면 됩니다. 이렇게 하면 두 VM이 자동으로 동일한 역할 할당을 가집니다.

**시스템 할당 vs 사용자 할당 비교:**

| 기능      | 시스템 할당        | 사용자 할당      |
| --------- | ------------------ | ---------------- |
| 수명 주기 | 리소스에 연결      | 독립적           |
| 공유      | 단일 리소스만      | 여러 리소스      |
| 삭제      | 리소스와 자동 삭제 | 수동 삭제        |
| 사용 사례 | 단일 리소스        | 공유/재사용 가능 |

---

### 문제 3

Entra Connect 동기화에서 온프레미스 Active Directory 특성 변경이 30분 후에도 Azure AD에 반영되지 않았습니다. 동기화를 강제 실행하는 가장 빠른 방법은?

- A. Entra Connect 서버 재시작
- B. `Start-ADSyncSyncCycle -PolicyType Delta` 실행
- C. Azure AD에서 사용자 삭제 후 재생성
- D. 24시간 대기 (전체 동기화 주기)

**정답: B**
**해설:**
Entra Connect(이전 Azure AD Connect)는 두 가지 동기화 주기를 실행합니다:

- **델타 동기화(Delta sync)**: 마지막 동기화 이후 변경 사항만 동기화 (기본 간격: 30분). 빠름 — 즉시 업데이트에 사용
- **전체 동기화(Full sync)**: 모든 개체를 재처리. 느림 — 스키마 변경이나 커넥터 재구성 후에만 필요

Entra Connect 서버에서 PowerShell로 실행:

```
Start-ADSyncSyncCycle -PolicyType Delta    # 최근 변경 동기화
Start-ADSyncSyncCycle -PolicyType Initial  # 전체 재동기화
```

**기타 Entra Connect 모드**: 스테이징 모드(두 번째 서버 설치, Azure AD에서 읽기만 하고 쓰지 않음 — 재해 복구 및 구성 변경 테스트에 사용).

---

### 문제 4

조직에서 Entra Connect를 사용합니다. 보안 팀은 온프레미스 AD의 사용자 암호를 Azure AD에 동기화하기를 원합니다(해시의 해시). 어떤 인증 방법이 이를 제공합니까?

- A. 통과 인증(PTA)
- B. AD FS와 페더레이션
- C. 암호 해시 동기화(PHS)
- D. 인증서 기반 인증

**정답: C**
**해설:**
Entra Connect 인증 방법:

| 방법                  | 인증 처리 위치 | 클라우드에 암호? | 온프레미스 인프라 필요? |
| --------------------- | -------------- | ---------------- | ----------------------- |
| 암호 해시 동기화(PHS) | Azure AD       | 예(해시의 해시)  | Entra Connect만         |
| 통과 인증(PTA)        | 온프레미스 AD  | 아니오           | 온프레미스 PTA 에이전트 |
| 페더레이션(AD FS)     | AD FS 서버     | 아니오           | AD FS + WAP 서버        |

PHS는 암호 해시의 솔트된 해시 버전을 Azure AD로 전송합니다. Microsoft는 원본 암호를 역공학으로 추출할 수 없습니다. 온프레미스 AD가 다운되어도 클라우드 복원력을 제공합니다.

PTA는 인증 요청을 온프레미스 에이전트로 보냅니다 — 모든 로그인에 온프레미스 연결이 필요합니다.

---

### 문제 5

JSON ARM 템플릿보다 더 간결하고 모듈 및 타입 검사를 기본 지원하는 템플릿 언어로 Azure 인프라를 배포해야 합니다. 무엇을 사용해야 합니까?

- A. PowerShell DSC
- B. Azure Bicep
- C. Azure CLI 스크립트
- D. Ansible

**정답: B**
**해설:**
**Azure Bicep**은 ARM JSON 템플릿으로 컴파일되는 도메인 특화 언어(DSL)입니다. Azure IaC(Infrastructure as Code)에 Microsoft가 권장하는 방식입니다.

Bicep vs ARM JSON 비교:

| 기능      | ARM JSON                  | Bicep              |
| --------- | ------------------------- | ------------------ |
| 구문      | 장황한 JSON               | 간결하고 읽기 쉬움 |
| 모듈      | 연결된 템플릿(복잡)       | `module` 키워드    |
| 타입 검사 | 제한적                    | 일류(first-class)  |
| 상태 파일 | 없음(Azure가 진실의 근원) | 없음               |
| 변환      | `az bicep decompile`      | `az bicep build`   |

기존 ARM 템플릿을 Bicep으로 변환:

```
az bicep decompile --file template.json
```

배포:

```
az deployment group create --resource-group rg1 --template-file main.bicep
```

---

### 문제 6

태그가 없는 모든 새 리소스 그룹에 `CostCenter` 태그를 자동으로 추가하는 정책을 시행하려고 합니다. 어떤 Azure Policy 효과를 사용해야 합니까?

- A. Deny
- B. Audit
- C. Modify
- D. DeployIfNotExists

**정답: C**
**해설:**
Azure Policy 효과:

| 효과              | 역할                                            |
| ----------------- | ----------------------------------------------- |
| Deny              | 비규격 리소스 생성/업데이트 차단                |
| Audit             | 리소스 허용하지만 비규격으로 표시               |
| Modify            | 리소스에 태그나 속성 추가/교체                  |
| DeployIfNotExists | 관련 리소스가 없으면 배포(예: 진단 설정 활성화) |
| AuditIfNotExists  | 관련 리소스가 없으면 감사                       |
| Append            | 기존 값을 교체하지 않고 리소스에 필드 추가      |

**Modify**는 기존 또는 새로 생성된 리소스의 태그 작업에 특별히 사용됩니다. Modify 정책 할당 후, 이미 존재하는 비규격 리소스를 수정하려면 **수정 작업(remediation task)**을 실행해야 합니다.

---

## 2) 스토리지 (심화)

### 문제 7

온프레미스 데이터 10TB를 Azure Blob Storage에 복사해야 합니다. 네트워크 대역폭이 제한적입니다(50Mbps). 인터넷 사용을 최소화하면서 가장 빠른 전송 방법은?

- A. AzCopy
- B. Azure Data Box
- C. Storage Explorer
- D. Azure File Sync

**정답: B**
**해설:**
50Mbps에서 10TB를 업로드하면 약 **18일 이상**의 연속 전송이 필요합니다. **Azure Data Box**는 물리적 하드웨어를 귀사로 배송합니다; 로컬에서 데이터를 로드하고 Microsoft 데이터센터로 반송하면 가져오기가 처리됩니다.

**도구 선택 기준:**

| 도구                 | 적합한 경우                          | 네트워크 필요?   |
| -------------------- | ------------------------------------ | ---------------- |
| AzCopy               | 커맨드라인 대량 복사, 소~중형 데이터 | 예               |
| Storage Explorer     | GUI 기반 파일 관리                   | 예               |
| Azure Data Box(80TB) | 대규모 데이터 오프라인 전송          | 아니오(오프라인) |
| Azure Data Box Disk  | 최대 35TB(소규모 배송)               | 아니오(오프라인) |
| Azure File Sync      | 하이브리드 파일 서버 캐싱            | 예(지속 동기화)  |

**AzCopy 팁**: `azcopy copy 'source' 'destination?SAS-token' --recursive` 명령으로 블롭 컨테이너 복사. 서버 간(Server-to-Server) 복사 지원(로컬 중간 저장 불필요).

---

### 문제 8

두 Blob Storage 계정 간에 개체 복제를 구성합니다. 어떤 조건을 충족해야 합니까?

- A. 두 계정 모두 같은 지역에 있어야 함
- B. 원본과 대상 모두 Blob 버전 관리가 활성화되어야 함
- C. 두 계정 모두 LRS 복제를 선택해야 함
- D. 계정이 같은 구독에 있어야 함

**정답: B**
**해설:**
**Azure Blob 개체 복제**는 블롭을 원본 컨테이너에서 대상 컨테이너로 비동기적으로 복사합니다(지역 간, 구독 간 가능).

사전 요구 사항:

- 원본 AND 대상 스토리지 계정 모두 **Blob 버전 관리** 활성화 필수
- 원본 계정에 **변경 피드(Change Feed)** 활성화 필수
- 계정은 다른 지역이나 구독에 있어도 됨

사용 사례: 기본 제공 복제를 넘어선 지역 중복성, 다른 지역의 규정 준수 사본, 더 가까운 사본에서 읽기 지연 감소.

**개체 복제 vs 기본 제공 중복성:**

- GRS/GZRS: Microsoft 관리, 장애 조치 없이는 보조 스토리지에 직접 접근 불가
- 개체 복제: 원본/대상 직접 제어, 접두사/날짜로 필터링, 직접 접근 가능

---

### 문제 9

개발자가 스토리지 계정 키 없이 Entra ID 자격 증명으로 Azure Files 공유에 접근하려 합니다. VM이 Entra ID에 조인되어 있습니다. 무엇을 구성해야 합니까?

- A. "Blob 공개 액세스 허용" 활성화
- B. 사용자에게 "Storage File Data SMB Share Contributor" RBAC 역할 할당
- C. 목록 권한이 있는 SAS 토큰 생성
- D. Blob 일시 삭제 활성화

**정답: B**
**해설:**
**Azure Files의 ID 기반 액세스**(SMB를 통한 Kerberos)는 스토리지 계정 키를 공유할 필요를 없애줍니다.

설정 단계:

1. 스토리지 계정에서 Entra ID Kerberos 인증 활성화(또는 온프레미스 조인의 경우 AD DS 통합)
2. 파일 공유 수준에서 적절한 RBAC 역할 할당:
   - **Storage File Data SMB Share Reader** — 읽기 전용
   - **Storage File Data SMB Share Contributor** — 읽기/쓰기
   - **Storage File Data SMB Share Elevated Contributor** — ACL 수정 포함

**시험 함정**: Azure Blob Storage는 다른 역할(Storage Blob Data Reader/Contributor)을 통해 Entra ID 인증을 사용합니다. Azure Files는 별도의 "SMB Share" 역할을 사용합니다. 서로 호환되지 않습니다.

---

## 3) 컴퓨팅 (심화)

### 문제 10

Microsoft 데이터센터 직원이 물리적으로 접근하더라도 VM 디스크를 무단 접근으로부터 보호하려 합니다. 조직만이 제어하는 키를 사용하여 암호화하는 기능은?

- A. Azure Disk Encryption (ADE) with BitLocker
- B. 고객 관리 키(CMK)를 사용한 호스트 암호화
- C. 플랫폼 관리 키(PMK)를 사용한 SSE
- D. Azure 기밀 컴퓨팅

**정답: B**
**해설:**
Azure는 여러 디스크 암호화 계층을 제공합니다:

| 옵션                                  | 암호화 위치                                     | 키 제어                  |
| ------------------------------------- | ----------------------------------------------- | ------------------------ |
| PMK를 사용한 SSE                      | 스토리지 서비스(Azure 관리 키)                  | Microsoft                |
| CMK를 사용한 SSE(Disk Encryption Set) | 스토리지 서비스                                 | 사용자(Key Vault를 통해) |
| ADE(BitLocker/DM-Crypt)               | 게스트 OS                                       | 사용자(Key Vault를 통해) |
| **호스트 암호화**                     | 데이터가 스토리지에 도달하기 전 호스트 서버에서 | 사용자(PMK 또는 CMK)     |

**호스트 암호화**는 임시 디스크 데이터(OS 디스크 캐시, 임시 디스크)가 Azure 호스트 계층에서 노출되는 것을 방지합니다. 먼저 구독 수준에서 활성화해야 합니다:

```
az feature register --name EncryptionAtHost --namespace Microsoft.Compute
```

그런 다음 VM에 설정: `--encryption-at-host true`

**Disk Encryption Set**은 CMK(Key Vault의 고객 관리 키)를 관리 디스크에 연결하는 데 필요합니다.

---

### 문제 11

AKS 클러스터에서 Kubernetes 1.28이 실행 중입니다. 1.30으로 업그레이드해야 합니다. 올바른 업그레이드 순서는?

- A. 컨트롤 플레인을 1.28에서 1.30으로 직접 업그레이드(1.29 건너뜀)
- B. 컨트롤 플레인을 1.29로 먼저 업그레이드, 그 후 노드 풀, 그 후 1.30으로
- C. 버전 1.30으로 클러스터를 삭제하고 재생성
- D. 노드 풀을 먼저 업그레이드, 그 후 컨트롤 플레인

**정답: B**
**해설:**
AKS Kubernetes 업그레이드 규칙:

1. **업그레이드는 순차적** — 부 버전을 건너뛸 수 없음. 반드시 1.28 → 1.29 → 1.30 순서
2. **컨트롤 플레인을 먼저 업그레이드**, 그 후 노드 풀(컨트롤 플레인이 항상 노드 풀 버전 이상이어야 함)
3. AKS는 컨트롤 플레인보다 하나 낮은 부 버전의 노드 풀 실행을 지원

업그레이드 명령:

```bash
# 1단계: 컨트롤 플레인만 업그레이드
az aks upgrade --resource-group rg1 --name aks1 --kubernetes-version 1.29.x --control-plane-only

# 2단계: 노드 풀 업그레이드
az aks nodepool upgrade --resource-group rg1 --cluster-name aks1 --name nodepool1 --kubernetes-version 1.29.x
```

**노드 풀**: AKS는 서로 다른 VM 크기의 여러 노드 풀을 지원합니다(시스템 풀: 시스템 파드용, 사용자 풀: 앱 워크로드용). 노드 풀을 독립적으로 추가/제거할 수 있습니다.

---

### 문제 12

App Service 웹 앱이 West US 지역에 배포되어 있습니다. 동일 지역의 개인 VNet에 있는 SQL Database에 앱이 접근하게 하려 합니다. 어떤 기능이 앱의 아웃바운드 트래픽을 VNet으로 라우팅합니까?

- A. App Service의 Private Endpoint
- B. VNet Integration
- C. Access Restrictions
- D. App Service의 Service Endpoint

**정답: B**
**해설:**

- **VNet Integration**: App Service에서 VNet으로 **아웃바운드** 트래픽을 라우팅합니다. 앱이 개인 IP를 사용하여 VNet 내 리소스(예: VM, 개인 DB 엔드포인트)에 접근할 수 있습니다. 전용 서브넷 필요(`Microsoft.Web/serverFarms`로 위임).
- **App Service의 Private Endpoint**: App Service에 VNet 내부의 개인 IP를 부여합니다 — **인바운드** 접근 제어(VNet 클라이언트만 앱에 접근 가능). 아웃바운드 라우팅이 아닙니다.

**일반적인 패턴:**

- VNet의 Private Endpoint가 있는 SQL DB → VNet Integration이 있는 App Service → 앱이 개인 IP로 SQL에 접근
- 외부 사용자 → App Service의 Private Endpoint(인바운드 개인) — 앱이 인터넷에 노출되지 않음

**Access Restrictions**: 인바운드 HTTP 요청에 대한 IP 허용/거부 목록. 라우팅에는 영향 없음.

---

## 4) 가상 네트워킹 (심화)

### 문제 13

여러 Azure VM이 개별 공용 IP 없이 인터넷으로 아웃바운드 연결을 시작해야 합니다. 확장 가능한 관리형 아웃바운드 NAT 솔루션이 필요합니다. 무엇을 배포해야 합니까?

- A. 아웃바운드 규칙이 있는 Azure Load Balancer(공용)
- B. NAT Gateway
- C. NVA를 통해 인터넷으로 가는 경로 테이블
- D. 각 VM에 개별 공용 IP

**정답: B**
**해설:**
**Azure NAT Gateway**는 서브넷의 VM에 관리형 아웃바운드 SNAT(Source Network Address Translation)를 제공합니다.

주요 특성:

- 하나 이상의 공용 IP 주소 또는 공용 IP 접두사 연결
- 관련 서브넷의 모든 VM이 아웃바운드 연결에 NAT Gateway의 IP를 공유
- 공용 IP당 최대 **64,000개의 동시 연결** 지원(더 많은 규모를 위해 여러 IP)
- 완전 관리형 — Azure Load Balancer의 일반적인 SNAT 포트 고갈 문제 없음
- **인바운드** 트래픽 처리 불가 — 인바운드는 별도 공용 IP 또는 Load Balancer 필요

**구성**: VNet 설정에서 NAT Gateway를 서브넷에 할당. 해당 서브넷의 모든 VM이 자동으로 사용.

**시험 함정**: 아웃바운드 규칙이 있는 Azure Load Balancer도 아웃바운드 SNAT를 제공하지만, 전용 아웃바운드 시나리오에는 NAT Gateway가 권장됩니다(더 단순하고, 확장 가능하며, 인바운드/아웃바운드 포트 충돌 없음).

---

### 문제 14

회사에 온프레미스를 Azure에 연결하는 ExpressRoute 회로가 있습니다. 공용 인터넷을 거치지 않고 Azure PaaS 서비스(Azure Storage, SQL Database 등)에 접근할 수 있는 피어링 유형은?

- A. 개인 피어링
- B. Microsoft 피어링
- C. 공용 피어링(사용 중단됨)
- D. VNet 피어링

**정답: B**
**해설:**
ExpressRoute 피어링 유형:

| 피어링 유형          | 연결 대상                                  | 사용 가능? |
| -------------------- | ------------------------------------------ | ---------- |
| **개인 피어링**      | 가상 네트워크 게이트웨이를 통한 Azure VNet | 예         |
| **Microsoft 피어링** | Azure PaaS 서비스(Storage, SQL, M365)      | 예         |
| 공용 피어링          | Azure 공용 IP                              | 사용 중단  |

- **개인 피어링**: 온프레미스 → Azure VNet 연결 — IaaS(VM, 내부 서비스)용
- **Microsoft 피어링**: 온프레미스 → 공용 인터넷을 우회하여 개인 회로를 통해 Azure PaaS 서비스 및 Microsoft 365 연결

**ExpressRoute vs VPN Gateway:**

| 기능      | ExpressRoute          | VPN Gateway         |
| --------- | --------------------- | ------------------- |
| 연결      | 전용 개인 회로        | 인터넷을 통한 IPSec |
| 대역폭    | 최대 100Gbps          | 최대 ~10Gbps        |
| SLA       | 99.95%                | 99.9%               |
| 설정 시간 | 몇 주(ISP 프로비저닝) | 몇 분               |

---

### 문제 15

재택근무 사용자가 회사 자격 증명(Azure AD/Entra ID)으로 Azure VM에 연결해야 합니다. 온프레미스 VPN 장치는 없습니다. 적합한 Azure VPN Gateway 구성은?

- A. 사이트-투-사이트 VPN
- B. ExpressRoute
- C. Azure AD 인증을 사용한 포인트-투-사이트(P2S) VPN
- D. VNet 피어링

**정답: C**
**해설:**
**포인트-투-사이트(P2S) VPN**은 사용자 장치에서 Azure VNet까지 개별 암호화 터널을 만듭니다 — 온프레미스 VPN 장치 불필요.

P2S 인증 옵션:

- **Azure 인증서**: 자체 서명 또는 CA 발급 인증서
- **RADIUS 서버**: 온프레미스 RADIUS/NPS에 대해 인증
- **Azure AD(Entra ID)**: 사용자가 회사 Entra 자격 증명으로 인증 — 이 시나리오에 이상적

프로토콜 옵션: OpenVPN(Azure AD 인증 지원), IKEv2, SSTP

**사이트-투-사이트(S2S) VPN**: 전체 온프레미스 네트워크를 VPN 장치를 사용하여 Azure에 연결합니다. 개별 사용자 연결에는 적합하지 않습니다.

---

### 문제 16

VNet 내 VM 호스트 이름을 DNS 레코드를 수동으로 생성하지 않고 자동으로 확인하도록 Azure DNS를 구성해야 합니다. 무엇을 활성화해야 합니까?

- A. Azure 공용 DNS 영역
- B. 자동 등록이 있는 Azure 개인 DNS 영역
- C. 전달이 있는 커스텀 DNS 서버
- D. Azure DNS 별칭 레코드

**정답: B**
**해설:**
**Azure 개인 DNS 영역**은 VNet 내에서 이름 확인을 제공합니다. VNet을 **자동 등록이 활성화된** 개인 DNS 영역에 연결하면 Azure가 해당 VNet에 배포된 각 VM에 대해 DNS A 레코드를 자동으로 생성합니다.

설정:

1. 개인 DNS 영역 생성 (예: `internal.contoso.com`)
2. 등록이 활성화된 상태로 VNet을 영역에 연결
3. VM이 자동으로 레코드를 받음: `vm1.internal.contoso.com → 10.0.0.4`

주요 사실:

- 자동 등록은 등록 링크가 있는 VNet에만 해당(영역당 자동 등록용 VNet 최대 1개)
- 여러 VNet을 확인 전용으로 **연결** 가능(자동 등록 없음)
- VM은 Azure 제공 DNS(168.63.129.16)를 사용하여 개인 영역 쿼리

**Azure 공용 DNS 영역**: 인터넷 연결 도메인 확인에 사용 — 개인 VNet 확인이 아님.

---

### 문제 17

Azure에서 온프레미스로의 트래픽을 라우팅하고, 특정 트래픽이 VNet을 떠나기 전에 방화벽 NVA를 통과하게 하려 합니다. 이를 가능하게 하는 Azure 기능은?

- A. NSG 아웃바운드 규칙
- B. 경로 테이블의 사용자 정의 경로(UDR)
- C. Azure Firewall 정책
- D. 애플리케이션 보안 그룹

**정답: B**
**해설:**
**사용자 정의 경로(UDR)**는 Azure의 기본 시스템 경로를 재정의하여 트래픽을 NVA(Network Virtual Appliance), Azure Firewall 또는 VPN Gateway를 통해 강제로 보낼 수 있습니다.

일반적인 UDR 시나리오:

- 서브넷에서 인터넷 바운드 트래픽을 Azure Firewall을 통해 강제 실행(0.0.0.0/0 → Azure Firewall 개인 IP)
- 허브-스포크 토폴로지에서 스포크 VNet 트래픽을 허브 NVA를 통해 라우팅
- BGP에서 학습한 기본 경로 재정의

UDR 구성:

1. 경로 테이블 생성
2. 경로 추가(주소 접두사 → 다음 홉 유형: 가상 어플라이언스, VNet 게이트웨이, 인터넷, 없음)
3. 서브넷에 경로 테이블 연결

**시험 함정**: NSG 규칙은 트래픽을 허용/거부하지만 다른 다음 홉으로 라우팅하지는 않습니다. 라우팅 제어는 UDR만 가능합니다.

---

### 문제 18

보안 분석 및 규정 준수를 위해 NSG 수준에서 모든 인바운드 및 아웃바운드 트래픽 흐름을 캡처해야 합니다. 어떤 기능이 이 흐름 데이터를 제공합니까?

- A. Azure Monitor 활동 로그
- B. NSG 흐름 로그
- C. Azure Firewall 진단
- D. VM 부팅 진단

**정답: B**
**해설:**
**NSG 흐름 로그**는 NSG를 통과하는 IP 트래픽 정보를 캡처합니다:

- 5-튜플 정보: 원본 IP, 원본 포트, 대상 IP, 대상 포트, 프로토콜
- 흐름 방향 및 허용/거부 결정
- Azure Storage 계정에 JSON으로 저장

**Traffic Analytics**(NSG 흐름 로그 기반):

- Log Analytics 작업 영역 + Storage 계정 필요
- 트래픽 패턴, 상위 트래픽 유발자, 위협 인텔리전스, 지리 맵의 시각화 제공
- 10분 또는 60분마다 흐름 데이터 집계

흐름 로그 활성화: NSG → 모니터링 → 흐름 로그 → 활성화 → 스토리지 계정 + 보존 기간 선택.

**버전 2 흐름 로그**: 흐름 상태(신규/기존/종료) 및 바이트/패킷 수 포함.

---

## 5) 부하 분산 및 글로벌 트래픽

### 문제 19

세 개의 Azure 지역에 배포된 웹 애플리케이션이 있습니다. 사용자를 네트워크 지연 시간을 기준으로 가장 가까운 지역으로 항상 연결하려 합니다. 어떤 Azure 서비스가 이를 제공합니까?

- A. Azure Load Balancer
- B. Azure Application Gateway
- C. 성능 라우팅이 있는 Traffic Manager
- D. Azure Front Door

**정답: C**
**해설:**
**Azure Traffic Manager**는 DNS 기반 전역 트래픽 배포 서비스입니다. 라우팅 방법:

| 방법                   | 작동 방식                                                 |
| ---------------------- | --------------------------------------------------------- |
| **우선순위(Priority)** | 모든 트래픽을 기본으로; 기본이 실패하면 보조로            |
| **가중치(Weighted)**   | 할당된 가중치에 따라 트래픽 분산(예: 70/30 분할)          |
| **성능(Performance)**  | 사용자에게 가장 낮은 지연 시간을 가진 엔드포인트로 라우팅 |
| **지리(Geographic)**   | 사용자의 지리적 위치에 따라 라우팅                        |
| **다중값(Multivalue)** | 여러 정상 엔드포인트 반환(UDP/TCP 앱)                     |
| **서브넷(Subnet)**     | 사용자의 원본 IP 서브넷에 따라 라우팅                     |

**성능 라우팅**은 Traffic Manager 지연 시간 테이블을 사용하여 사용자의 DNS 해석기에 가장 가까운 엔드포인트를 결정합니다.

**시험 함정**: Traffic Manager는 DNS 수준(레이어 7)에서 작동하지만 HTTP 헤더나 경로는 검사하지 않습니다. HTTP 수준 부하 분산과 라우팅 규칙을 위해서는 Application Gateway 또는 Azure Front Door를 사용하세요.

---

### 문제 20

WAF(웹 애플리케이션 방화벽), SSL 오프로드, URL 경로 기반 라우팅, Azure 지역 간 즉각적인 장애 조치를 제공하는 글로벌 HTTP/S 부하 분산기를 배포하려 합니다. 모든 요구 사항을 충족하는 서비스는?

- A. Traffic Manager
- B. Azure Load Balancer(표준, 글로벌)
- C. Azure Front Door
- D. Application Gateway

**정답: C**
**해설:**
**Azure Front Door**는 다음을 제공하는 글로벌 애니캐스트 기반 HTTP/S 부하 분산기입니다:

- **WAF** — OWASP 규칙 집합, 봇 보호
- 엣지에서 **SSL/TLS 오프로드**
- 다른 백엔드로의 **URL 경로 기반 라우팅**
- PoP에서 정적 콘텐츠 **캐싱**
- 즉각적인 장애 조치가 있는 **상태 프로브**(DNS TTL 기반 아님, 밀리초 단위)
- **사용자 지정 도메인 + 관리 인증서**

**Front Door vs Traffic Manager 비교:**

| 기능           | Azure Front Door | Traffic Manager |
| -------------- | ---------------- | --------------- |
| 프로토콜       | HTTP/S만         | 모든 프로토콜   |
| 부하 분산      | 레이어 7(HTTP)   | DNS 기반        |
| WAF            | 예               | 아니오          |
| 캐싱           | 예               | 아니오          |
| 장애 조치 속도 | 거의 즉각적      | DNS TTL(60초+)  |

**Application Gateway**: 지역 HTTP/S 부하 분산기 — 단일 지역 내에서만 작동. 글로벌 다중 지역에는 Front Door를 사용하세요.

---

## 6) 모니터링 및 유지 관리

### 문제 21

팀에서 지난 한 시간 동안 CPU 사용량 기준 상위 10개 VM을 찾는 Azure Monitor 로그 쿼리를 원합니다. 어떤 쿼리 언어를 사용해야 합니까?

- A. SQL
- B. KQL (Kusto Query Language)
- C. SPARQL
- D. Gremlin

**정답: B**
**해설:**
Azure Monitor 로그(Log Analytics)는 **KQL(Kusto Query Language)**을 사용합니다. 시계열 원격 측정 및 로그 분석에 최적화되어 있습니다.

일반적인 KQL 패턴:

```kql
// 지난 1시간 동안 평균 CPU 기준 상위 10개 VM
Perf
| where TimeGenerated > ago(1h)
| where CounterName == "% Processor Time"
| summarize AvgCPU = avg(CounterValue) by Computer
| top 10 by AvgCPU desc

// 사용자별 로그인 실패 횟수
SecurityEvent
| where EventID == 4625
| summarize FailedAttempts = count() by Account
| order by FailedAttempts desc
```

주요 KQL 연산자: `where`, `summarize`, `project`, `extend`, `join`, `render`, `top`, `limit`, `order by`, `ago()`, `bin()`.

---

### 문제 22

VM 진단 로그를 동시에 세 가지 대상으로 전송해야 합니다: 장기 보관, 실시간 SIEM 스트리밍, 쿼리/분석. 올바른 Azure 구성은?

- A. 서로 다른 모니터링 구성으로 세 개의 별도 VM 생성
- B. 세 가지 대상이 있는 진단 설정 구성
- C. Log Analytics 작업 영역만 활성화; 다른 대상은 별도 설정 필요
- D. 로그 보관에 Azure Backup 사용

**정답: B**
**해설:**
**진단 설정**은 리소스당 구성하여 로그/메트릭을 동시에 최대 **네 가지 대상**으로 라우팅할 수 있습니다:

| 대상                        | 사용 사례                                    |
| --------------------------- | -------------------------------------------- |
| **Log Analytics 작업 영역** | 대화형 쿼리(KQL), 대시보드, 경고             |
| **스토리지 계정**           | 장기 보관, 규정 준수 보존                    |
| **이벤트 허브**             | SIEM으로의 실시간 스트리밍(Splunk, Sentinel) |
| **파트너 솔루션**           | 서드파티 모니터링 통합                       |

구성 방법: 리소스 → 모니터링 → 진단 설정 → 진단 설정 추가 → 범주 선택 → 대상 선택.

**시험 함정**: 스토리지 + 이벤트 허브 + Log Analytics 세 곳 모두에 **단일** 진단 설정으로 전송 가능 — 여러 설정이 필요하지 않습니다.

---

### 문제 23

Azure Monitor가 예약된 4시간 유지 관리 기간 동안 매일 수백 개의 경고를 생성합니다. 경고 규칙을 삭제하지 않고 유지 관리 기간 동안만 알림을 억제하려 합니다. 무엇을 구성해야 합니까?

- A. 유지 관리 전 경고 규칙을 삭제하고 후에 재생성
- B. 매번 수동으로 경고 규칙 비활성화
- C. 알림 억제 작업이 있는 경고 처리 규칙 생성
- D. 경고 심각도를 정보로 줄이기

**정답: C**
**해설:**
**경고 처리 규칙**(이전 작업 규칙)을 사용하면 기본 경고 규칙을 수정하지 않고 경고가 발생했을 때 어떤 일이 일어날지 관리할 수 있습니다.

사용 가능한 작업:

- **알림 억제**: 지정된 시간 동안 일치하는 경고에 알림 미전송
- **작업 그룹 적용**: 일치하는 경고에 추가 알림 추가

구성:

- 범위: 특정 리소스, 리소스 그룹, 구독
- 필터: 경고 심각도, 경고 규칙 이름, 리소스 유형 등
- 일정: 일회성 또는 반복(예: 매주 일요일 22:00–02:00)

포털 경로: Azure Monitor → 경고 → 경고 처리 규칙 → 만들기.

**시험 구분**: 경고 처리 규칙은 경고 발생 후 처리(알림 억제)에 영향을 미칩니다. 경고 규칙은 경고가 발생하는 시기(임계값 조건)를 정의합니다. 이 두 가지는 별도의 개체입니다.

---

### 문제 24

사용자 지정 KQL 쿼리를 작성하지 않고 시각적 차트를 통해 VM 성능 추세(CPU, 메모리, 디스크)를 분석하려 합니다. 어떤 Azure Monitor 기능이 이를 기본 제공합니까?

- A. Azure Advisor
- B. VM Insights
- C. Application Insights
- D. Network Watcher 연결 모니터

**정답: B**
**해설:**
**Azure Monitor VM Insights**는 VM 및 VM Scale Set 성능과 종속성 모니터링을 위한 사전 빌드된 대시보드를 제공합니다:

- **성능 탭**: CPU, 메모리, 디스크, 네트워크 — 히스토리 차트, 백분위수 보기
- **맵 탭**: 종속성 에이전트를 사용하여 라이브 종속성(프로세스, 연결, 외부 엔드포인트) 표시
- **시작 탭**: 쉬운 온보딩을 위한 모니터링되지 않은 VM 목록

**필수 조건**: Log Analytics 작업 영역 + Azure Monitor Agent(또는 맵 기능을 위한 레거시 MMA + 종속성 에이전트).

**기타 Monitor Insights:**

| Insight            | 대상                                         |
| ------------------ | -------------------------------------------- |
| VM Insights        | 가상 머신 및 VMSS                            |
| Container Insights | AKS 및 Arc 사용 클러스터                     |
| Network Insights   | VNet, 부하 분산기, Application Gateway, 연결 |
| Storage Insights   | Azure 스토리지 계정                          |

**Azure Advisor**: 권장 사항(비용, 보안, 안정성, 성능, 운영 우수성)을 제공하지만 성능 메트릭이나 차트는 표시하지 않습니다. 목적이 다릅니다.

---

### 문제 25

Azure Advisor에서 "사용량이 적은 가상 머신 크기 조정 또는 종료" 권장 사항이 표시됩니다. 이 권장 사항은 어떤 Advisor 범주에 해당합니까?

- A. 보안
- B. 안정성
- C. 비용
- D. 운영 우수성

**정답: C**
**해설:**
**Azure Advisor**는 Azure 사용을 분석하여 5개 범주에 걸쳐 개인화된 권장 사항을 제공합니다:

| 범주            | 예시                                                        |
| --------------- | ----------------------------------------------------------- |
| **비용**        | VM 크기 조정, 연결되지 않은 디스크 삭제, 예약 인스턴스 권장 |
| **보안**        | MFA 활성화, 디스크 암호화, Security Center 권장 사항        |
| **안정성**      | 백업 활성화, 영역 중복성, 가용성 집합                       |
| **성능**        | SQL DTU 개선, 성능을 위한 VM 크기 업그레이드                |
| **운영 우수성** | 배포 모범 사례 준수, 서비스 상태 구독                       |

Advisor는 비용 권장 사항에 최대 7일간의 사용 데이터를 사용합니다. 권장 사항은:

- 수락하고 구현
- 연기
- 해제

접근: Azure 포털 → Advisor, 또는 Azure Advisor REST API를 통해 프로그래밍 방식으로 접근.

---

## 7) 백업 및 복구 (심화)

### 문제 26

Azure VM에 에이전트를 설정하지 않고 온프레미스 Windows Server에서 개별 파일과 폴더를 Azure에 백업해야 합니다. 어떤 도구가 이를 제공합니까?

- A. Azure Backup Server (MABS)
- B. Microsoft Azure Recovery Services (MARS) 에이전트
- C. Azure Site Recovery
- D. Azure File Sync

**정답: B**
**해설:**
**MARS(Microsoft Azure Recovery Services) 에이전트**는 Windows 서버(온프레미스 또는 Azure VM)에 직접 설치하여 다음을 백업하는 경량 에이전트입니다:

- 파일 및 폴더
- 시스템 상태 / 운영 체제 복구
- 애플리케이션 데이터(추가 에이전트로)

백업 대상: Recovery Services Vault만.

**백업 솔루션 비교:**

| 도구                      | 백업 대상                      | 설치 위치            |
| ------------------------- | ------------------------------ | -------------------- |
| MARS 에이전트             | 파일/폴더/시스템 상태          | Windows 서버 OS      |
| Azure Backup(VM 확장)     | 전체 VM(디스크 스냅샷)         | Azure VM 플랫폼      |
| MABS(Azure Backup Server) | 애플리케이션(SQL, Exchange 등) | 온프레미스 전용 서버 |
| DPM                       | 엔터프라이즈 워크로드          | 전용 DPM 서버        |

MARS 백업 빈도: 하루 최대 3회. 보존: 일별(최대 180일), 주별, 월별, 연별 정책.

---

### 문제 27

Azure VM과 Azure 파일 공유 모두 보호해야 합니다. 또한 변경 불가능한 백업 스토리지(Trusted Launch VM 지원)가 필요합니다. 어떤 Vault 유형을 사용해야 합니까?

- A. Recovery Services Vault만
- B. Backup Vault만
- C. VM에는 Recovery Services Vault; 파일 공유에는 Backup Vault
- D. 둘 다 Backup Vault

**정답: B**
**해설:**
**Backup Vault**는 다음을 지원하는 최신 Vault 유형입니다:

- Azure Blobs
- Azure 디스크(관리 디스크 백업)
- **Azure Database for PostgreSQL**
- **Trusted Launch VM**(vTPM 및 보안 부팅이 있는 최신 Azure VM)
- 변경 불가능한 Vault 지원(지정된 기간 동안 백업 데이터 삭제 방지)

**Recovery Services Vault**는 다음을 지원합니다:

- Azure VM(클래식 및 표준)
- Azure Files(파일 공유)
- Azure VM의 SQL Server
- Azure VM의 SAP HANA
- MARS/MABS를 통한 온프레미스 서버
- Azure Site Recovery

**시험 참고(2026 범위)**: 어떤 Vault 유형이 어떤 워크로드를 지원하는지 구분하는 문제가 출제될 수 있습니다. 기억법: Recovery Services Vault = VM + 파일 + 온프레미스; Backup Vault = 최신 서비스(Blobs, 디스크, PostgreSQL, Trusted Launch VM).

---

## 8) Azure Migrate

### 문제 28

회사에서 온프레미스 VMware VM 200개를 Azure로 마이그레이션하려 합니다. 첫 번째 단계는 비용과 호환성을 이해하는 것입니다. 어떤 도구를 사용해야 합니까?

- A. Azure Site Recovery (ASR)
- B. Azure Migrate: 검색 및 평가
- C. Azure Database Migration Service
- D. Azure Data Box

**정답: B**
**해설:**
**Azure Migrate**는 Azure 마이그레이션 프로젝트의 중앙 허브입니다. 워크플로에는 두 가지 주요 단계가 있습니다:

**1단계 — 검색 및 평가:**

1. Azure Migrate 프로젝트 생성
2. 온프레미스에 **Azure Migrate 어플라이언스**(경량 VM) 배포
3. 어플라이언스가 VMware VM, Hyper-V VM 또는 물리적 서버를 검색
4. 평가 생성:
   - **Azure VM 평가**: 권장 VM 크기, 예상 월별 비용, 마이그레이션 준비성
   - **AVS 평가**: Azure VMware Solution으로 마이그레이션용
   - **SQL 평가**: SQL Server 마이그레이션용

**2단계 — 마이그레이션:**

- **Azure Migrate: 서버 마이그레이션** 도구 사용(에이전트 기반 마이그레이션에는 Azure Site Recovery 활용)
- 에이전트 없는 마이그레이션: 각 VM에 에이전트 설치 없이 VMware VM 직접 복제
- 에이전트 기반 마이그레이션: 더 복잡한 시나리오에 Mobility 서비스 에이전트 설치

**Azure Site Recovery (ASR)**: 지속적인 재해 복구 복제에 사용(초기 마이그레이션 평가 아님).

---

### 문제 29

Azure Migrate 평가를 실행한 후 일부 VM이 "조건부 준비" 상태로 표시되었습니다. 이 상태는 무엇을 의미합니까?

- A. VM을 Azure로 마이그레이션할 수 없음
- B. 특정 문제(예: OS 지원, 부팅 유형)에 주의가 필요하지만 마이그레이션 가능
- C. VM이 이미 Azure에서 실행 중
- D. 성능 평가에 실패

**정답: B**
**해설:**
Azure Migrate 평가 준비성 상태:

| 상태                  | 의미                                            |
| --------------------- | ----------------------------------------------- |
| **준비됨**            | VM을 현재 상태로 마이그레이션 가능              |
| **조건부 준비**       | 마이그레이션 가능하지만 특정 문제 주의 필요     |
| **준비되지 않음**     | 현재 설정으로 마이그레이션 불가; 주요 차단 요소 |
| **알 수 없는 준비성** | 평가에 충분한 데이터 없음                       |

일반적인 "조건부 준비" 이유:

- **부팅 유형**: BIOS 부팅 감지됨(1세대 VM 필요); UEFI는 변환 필요할 수 있음
- **OS 지원**: 지원되지만 업데이트 필요할 수 있음
- **디스크 크기**: 개별 디스크가 Azure 한도 초과(32TB)
- **NIC 수**: VM의 NIC 수가 Azure 대상 크기 지원 이상

경고이지, 차단 요소가 아닙니다 — 마이그레이션은 진행할 수 있지만 문제를 해결해야 합니다.

---

## 9) Key Vault

### 문제 30

Owner 액세스 권한이 있는 사람이 Key Vault를 삭제해도 Vault과 시크릿이 90일 동안 복구 가능하게 유지되기를 원합니다. 어떤 두 가지 Key Vault 기능을 활성화해야 합니까?

- A. 일시 삭제 및 제거 보호
- B. 일시 삭제 및 액세스 정책
- C. Private Endpoint 및 RBAC
- D. 방화벽 규칙 및 신뢰할 수 있는 서비스

**정답: A**
**해설:**
**일시 삭제(Soft delete)**: 활성화하면 삭제된 Key Vault 및 해당 개체(키, 시크릿, 인증서)가 구성 가능한 보존 기간(7–90일) 동안 "삭제됨" 상태로 유지됩니다. 이 기간 동안 복구할 수 있습니다.

**제거 보호(Purge Protection)**: 활성화하면 보존 기간 동안 소프트 삭제된 항목의 **영구 삭제(제거)**를 방지합니다 — Owner 또는 Key Vault Contributor 역할을 가진 사용자도 포함합니다. 보존 기간이 만료된 후 항목이 자동으로 제거됩니다.

**제거 보호 없이**: Owner가 Vault을 삭제하고 즉시 제거하여 모든 데이터를 잃을 수 있습니다.
**일시 삭제 + 제거 보호 포함**: 삭제 → 소프트 삭제 상태 진입 → 90일 동안 제거 불가 → 복구 가능.

**참고**: 2025년 2월부터 모든 새 Key Vault에서 일시 삭제가 기본으로 활성화되며 비활성화할 수 없습니다.

---

### 문제 31

웹 애플리케이션이 연결 문자열을 검색하기 위해 Key Vault를 사용합니다. 앱이 Azure App Service로 배포되어 있습니다. 자격 증명 관리를 피하는 가장 안전한 액세스 방법은?

- A. 앱 설정에 Key Vault 액세스 키 저장
- B. App Service에 시스템 할당 관리 ID를 사용하고 Key Vault Secrets User RBAC 역할 부여
- C. 공유 액세스 서명을 사용하여 Key Vault 액세스
- D. 코드에 Key Vault 클라이언트 ID와 시크릿 포함

**정답: B**
**해설:**
데이터 플레인 액세스를 위한 Key Vault RBAC 역할:

| 역할                          | 권한                              |
| ----------------------------- | --------------------------------- |
| **Key Vault Secrets User**    | 시크릿 값 읽기                    |
| **Key Vault Secrets Officer** | 시크릿 생성/업데이트/삭제         |
| **Key Vault Reader**          | 메타데이터만 보기(시크릿 값 없음) |
| **Key Vault Administrator**   | 완전한 제어                       |

**모범 사례 워크플로:**

1. App Service에서 시스템 할당 관리 ID 활성화
2. Key Vault에서 관리 ID에 `Key Vault Secrets User` 역할 할당
3. 앱 코드에서 `DefaultAzureCredential`(Azure SDK) 사용 — 자동으로 관리 ID 토큰 사용
4. App Service 구성에서 Key Vault 참조 사용: `@Microsoft.KeyVault(VaultName=myvault;SecretName=mySecret)` — 런타임에 시크릿 검색

**Key Vault 액세스 모델:**

- **액세스 정책**(레거시): Vault 수준; 주체별 권한 부여
- **Azure RBAC**(권장): 표준 Azure 역할 할당; 리소스 그룹에서의 상속 지원

---

## 10) Azure Policy (심화)

### 문제 32

모든 스토리지 계정에 진단 설정을 활성화하는 "DeployIfNotExists" 정책을 할당한 후, 기존 스토리지 계정이 여전히 비규격입니다. 규정 준수 상태로 만들려면 어떻게 해야 합니까?

- A. 스토리지 계정을 삭제하고 재생성
- B. 정책 할당에 대한 수정 작업 생성
- C. 다른 범위로 정책을 다시 할당
- D. 자동 수정을 위해 24시간 대기

**정답: B**
**해설:**
**DeployIfNotExists(DINE)** 및 **Modify** 정책은 정책 할당 후 생성되거나 업데이트된 **새 리소스**에만 자동으로 적용됩니다. **기존 비규격 리소스**에 대해서는 **수정 작업(remediation task)**을 트리거해야 합니다.

수정 작업:

1. Azure Policy → 수정 → 수정 작업 만들기
2. 정책 할당 선택
3. 범위 선택(구독, 리소스 그룹)
4. Azure가 모든 비규격 리소스에 DINE 작업(진단 설정 배포)을 트리거

**수정에 필요한 권한**: 정책 할당의 관리 ID에 수정되는 리소스에 대한 기여자 권한이 있어야 합니다. 할당 중에 구성됩니다.

---

### 문제 33

여러 관련 Azure Policy 정의를 그룹화하여 보안 표준 집합을 단일 단위로 할당하려 합니다. 어떤 Azure Policy 구성을 사용해야 합니까?

- A. 정책 세트(이니셔티브)
- B. 관리 그룹
- C. 정책 효과
- D. 규정 준수 보고서

**정답: A**
**해설:**
**정책 이니셔티브**(정책 세트 정의라고도 함)는 여러 정책 정의를 그룹화하여 함께 할당합니다.

사용 사례 예시: "CIS Microsoft Azure Foundations Benchmark" 이니셔티브 — 보안 규정 준수를 위한 100개 이상의 정책 포함. 100개 이상의 개별 정책을 할당하는 대신 이니셔티브를 한 번 할당.

주요 개념:

- **정책 정의**: 하나의 효과를 가진 단일 규칙
- **이니셔티브 정의**: 정책 정의 모음
- **할당**: 정의 또는 이니셔티브를 범위(관리 그룹, 구독, 리소스 그룹)에 적용
- **규정 준수 보고서**: 정책별 규격/비규격 리소스 표시

**기본 제공 이니셔티브**: Azure Security Benchmark, HIPAA, ISO 27001, CIS 벤치마크, PCI DSS.

**관리 그룹**: 거버넌스를 위해 구독을 계층적으로 구성 — 하지만 정책 할당의 범위이지, 정책 자체의 그룹화 메커니즘이 아닙니다.

---

## 11) 네트워킹 (추가)

### 문제 34

허브 VNet에 Azure Firewall이 있고 두 스포크 VNet(Spoke-A, Spoke-B)이 허브에 피어링되어 있습니다. 두 스포크 모두의 인터넷 바운드 트래픽이 Azure Firewall을 통과하게 하려면 각 스포크 VNet의 서브넷에 무엇을 구성해야 합니까?

- A. NSG 아웃바운드 거부 규칙
- B. Azure Firewall 개인 IP를 가리키는 기본 경로 0.0.0.0/0이 있는 UDR
- C. Azure Firewall용 서비스 엔드포인트
- D. 각 스포크에서 ExpressRoute 연결

**정답: B**
**해설:**
Azure Firewall을 중앙 보안 제어로 사용하는 **허브-스포크 토폴로지**에서:

1. Azure Firewall이 허브 VNet의 `AzureFirewallSubnet` 서브넷에 배포됨
2. 스포크 서브넷이 모든 인터넷 트래픽에 Firewall을 사용하도록 강제하려면:
   - 경로가 있는 경로 테이블 생성: `0.0.0.0/0` → 다음 홉: 가상 어플라이언스 → Azure Firewall 개인 IP
   - 각 스포크 서브넷에 이 경로 테이블 연결

**UDR 없이**: VNet 피어링은 허브의 Firewall을 통해 트래픽을 자동으로 라우팅하지 않음 — 트래픽이 인터넷으로 직접 이동합니다.

**Azure Firewall 특성:**

- 전용 `AzureFirewallSubnet`(/26 이상) 필요
- 아웃바운드 트래픽의 SNAT를 위한 자체 공용 IP 보유
- FQDN 필터링, 위협 인텔리전스, 네트워크 규칙, 애플리케이션 규칙 지원
- Premium SKU: TLS 검사 및 IDPS 추가

---

### 문제 35

VNet에 VPN Gateway가 있습니다. `GatewaySubnet`이 이미 있습니다. 동료가 트래픽을 제한하기 위해 GatewaySubnet에 NSG를 추가하려 합니다. 예상되는 결과는?

- A. NSG가 다른 서브넷과 완전히 동일하게 작동함
- B. NSG가 VPN 트래픽을 모두 차단함(GatewaySubnet에서 NSG 미지원)
- C. GatewaySubnet의 NSG는 권장되지 않으며 게이트웨이 기능을 손상시킬 수 있음
- D. 보안 규정 준수를 위해 GatewaySubnet에 NSG를 추가해야 함

**정답: C**
**해설:**
Microsoft는 `GatewaySubnet`에 NSG를 추가하는 것을 **권장하지 않습니다**. 서브넷은 다음을 위해 Azure 인프라와 통신해야 하는 VPN/ExpressRoute 게이트웨이가 사용합니다:

- BGP 피어 연결
- VPN 터널용 IKE 핸드셰이크
- 상태 프로브 트래픽

과도하게 제한적인 규칙이 있는 NSG는 이러한 내부 통신을 손상시킬 수 있습니다. NSG를 추가해야 한다면 모든 필요한 게이트웨이 관리 포트를 신중하게 허용해야 합니다.

**시험 규칙**: `GatewaySubnet` — NSG 없음(또는 모든 게이트웨이 관리 트래픽을 허용하는 매우 신중한 NSG). 이 질문은 이 제약 조건에 대한 인식을 테스트합니다.

**AzureFirewallSubnet**: 마찬가지로, 전용 Azure Firewall 서브넷에서는 NSG가 지원되지 않습니다.

---

## 12) 혼합 주제

### 문제 36

Azure 서비스 상태를 모니터링하고 Azure 인시던트가 구독에 영향을 미칠 수 있을 때 알림을 받으려 합니다. 어떤 Azure 서비스가 이를 제공합니까?

- A. Azure Monitor 경고 규칙
- B. Azure Service Health
- C. Azure Resource Health
- D. Azure 상태 페이지(azure.status.microsoft.com)

**정답: B**
**해설:**
Azure 상태 서비스 비교:

| 서비스                    | 모니터링 대상                                                                         | 범위            |
| ------------------------- | ------------------------------------------------------------------------------------- | --------------- |
| **Azure Service Health**  | 사용자의 구독/지역/서비스에 영향을 미치는 Azure 인시던트, 계획된 유지 관리, 상태 권고 | 개인화됨        |
| **Azure Resource Health** | 특정 리소스의 상태(이 VM이 실행 중인가?)                                              | 단일 리소스     |
| **Azure 상태 페이지**     | 광범위한 글로벌 Azure 중단 — 공개, 개인화 없음                                        | 모든 Azure 고객 |
| **Azure Monitor 경고**    | 사용자 지정 메트릭/로그 임계값 위반                                                   | 사용자의 리소스 |

**Azure Service Health**를 사용하면 다음에 대한 알림을 받기 위한 **Service Health 경고**(활동 로그 경고 기반)를 만들 수 있습니다: 서비스 문제, 계획된 유지 관리, 상태 권고, 보안 권고.

---

### 문제 37

스토리지 계정에 `readpolicy`라는 이름의 공유 액세스 정책이 저장된 액세스 정책과 연결되어 있습니다. 이 정책을 참조하는 모든 SAS 토큰에 대한 액세스를 즉시 취소하는 가장 빠른 방법은?

- A. 스토리지 계정 키 교체
- B. 저장된 액세스 정책 삭제 또는 수정
- C. 스토리지 계정 방화벽 규칙 변경
- D. 전체 스토리지 계정 삭제

**정답: B**
**해설:**
**저장된 액세스 정책**은 Service SAS의 기능으로 서버 측에서 권한과 만료를 정의할 수 있습니다. 저장된 액세스 정책을 참조하는 모든 SAS 토큰은 다음 방법으로 중앙에서 취소할 수 있습니다:

- **저장된 액세스 정책 삭제** — 이를 참조하는 모든 SAS 토큰을 즉시 무효화
- **정책 수정**(예: 만료를 과거 날짜로 설정) — 액세스를 효과적으로 취소

이것은 임시 SAS 토큰(저장된 액세스 정책 없음)에 비해 핵심 장점입니다: 임시 토큰은 만료 전에 취소할 수 없음 — 유일한 옵션은 **스토리지 계정 키 교체**로, 계정의 모든 SAS 토큰이 무효화됩니다.

**비교:**

| SAS 유형                      | 취소 방법                      |
| ----------------------------- | ------------------------------ |
| 임시 SAS(저장된 정책 없음)    | 계정 키 교체(모든 토큰에 영향) |
| 저장된 액세스 정책이 있는 SAS | 정책 삭제/수정(수술적 취소)    |
| User Delegation SAS           | Entra 자격 증명/토큰 취소      |

---

### 문제 38

여러 Azure 구독이 동일한 Azure Policy 및 RBAC 할당을 공유하고 예산을 계층적으로 관리해야 합니다. 어떤 Azure 기능이 이를 가능하게 합니까?

- A. 리소스 그룹
- B. 관리 그룹
- C. Azure Blueprints
- D. VNet 피어링으로 연결된 구독

**정답: B**
**해설:**
**관리 그룹**은 구독 위에 계층적 거버넌스 구조를 제공합니다:

```
루트 관리 그룹
  ├── Corp 관리 그룹
  │   ├── 프로덕션 구독
  │   └── 개발 구독
  └── External 관리 그룹
      └── 파트너 구독
```

주요 기능:

- 관리 그룹 수준에서 할당된 정책 및 RBAC가 모든 자식 구독으로 상속
- 최대 6단계 계층(루트 및 구독 수준 제외)
- 루트 관리 그룹이 모든 구독을 포함
- 전역 관리자는 루트 관리 그룹을 통해 모든 구독을 관리할 수 있도록 권한 상승 가능

**사용 사례**: 각 구독에 개별적으로 할당하는 대신 Corp 관리 그룹에 할당하여 모든 프로덕션 구독에 "HTTPS 필요" 정책을 적용.

---

### 문제 39

Azure의 VM이 포트 80에서 연결되지 않습니다. NSG를 확인하면 TCP 80 인바운드를 허용하는 규칙이 있습니다. 그러나 ASG(Application Security Group)도 관련되어 있습니다. 문제 해결 결과 VM의 NIC가 허용 규칙에서 참조하는 ASG에 없음을 발견했습니다. 가장 가능성 있는 원인은?

- A. NSG는 ASG를 사용할 수 없음
- B. ASG 규칙은 해당 ASG에 할당된 NIC에만 트래픽을 허용; VM의 NIC를 추가해야 함
- C. ASG가 NSG를 재정의함
- D. VM이 트래픽을 받으려면 공용 IP가 필요함

**정답: B**
**해설:**
**ASG(Application Security Group)**를 사용하면 VM NIC를 논리적으로 그룹화하고 NSG 규칙에서 그룹을 참조할 수 있습니다 — 개별 IP 주소 대신.

ASG가 있는 NSG 규칙:

- 대상: `asg-webservers`
- 프로토콜: TCP, 포트: 80
- 작업: 허용

이 규칙은 **`asg-webservers`의 구성원인 VM NIC에만 적용됩니다**. VM의 NIC가 이 ASG에 할당되지 않으면 규칙이 일치하지 않고 기본 NSG 거부 규칙이 적용됩니다.

**수정**: VM의 NIC 설정 → 애플리케이션 보안 그룹 → 추가 → `asg-webservers` 선택.

**ASG 장점:**

- IP 주소가 변경될 때 NSG 규칙을 업데이트할 필요 없음
- 논리적 그룹화: "모든 웹 서버," "모든 데이터베이스 서버"
- VNet 내에서 작동(VNet 간 불가)

---

### 문제 40

Azure 비용을 검토하고 24/7로 실행되는 VM 워크로드에 대한 예약 인스턴스 구매 자동화 권장 사항을 원합니다. 어떤 Azure 서비스가 이러한 특정 권장 사항을 제공합니까?

- A. Azure Cost Management + 청구
- B. Azure Advisor(비용 범주)
- C. Azure Policy
- D. Microsoft Defender for Cloud

**정답: B**
**해설:**
**Azure Advisor**는 지난 7일, 30일 또는 60일 동안의 VM 사용 기반으로 비용 범주에서 예약 구매 권장 사항을 제공합니다. 권장 사항에는 다음이 포함됩니다:

- 예약할 VM 제품군 및 크기
- 예상 연간 절감액
- 1년 vs 3년 약정 비교

**Azure Cost Management + 청구**: 비용 분석, 예산, 비용 경고 및 내보내기를 제공하지만 권장 엔진은 Advisor입니다.

**예약 인스턴스 vs 절감 계획:**

- 예약 인스턴스: 특정 VM 크기 + 지역 + OS 약정 — 최대 72% 절감
- Azure 절감 계획: 지출 금액별 유연한 약정 — 모든 VM 크기/지역에 적용 — 최대 65% 절감

두 방법 모두 약정(1년 또는 3년)이 필요하며 선불, 일부 선불 또는 월별로 청구됩니다.

**Advisor 액세스**: Azure 포털 → Advisor → 비용 → 예약 권장 사항. 또는 Azure Advisor REST API/Azure CLI로 프로그래밍 방식: `az advisor recommendation list --category Cost`.

---

_Set 3 종료 — 40문항_

**주제 커버리지 요약:**

- Q1-Q2: 관리 ID(시스템 할당 vs 사용자 할당)
- Q3-Q4: Entra Connect(델타 동기화, PHS vs PTA)
- Q5: Bicep/ARM 템플릿
- Q6: Azure Policy(Modify 효과)
- Q7-Q9: 스토리지 심화(Data Box, 개체 복제, Azure Files ID 기반 액세스)
- Q10: VM 호스트 암호화 + CMK
- Q11: AKS 업그레이드 순서
- Q12: App Service VNet Integration vs Private Endpoint
- Q13: NAT Gateway
- Q14: ExpressRoute 피어링 유형
- Q15: Azure AD를 사용한 VPN 포인트-투-사이트
- Q16: Private DNS 자동 등록
- Q17: UDR(사용자 정의 경로)
- Q18: NSG 흐름 로그 + Traffic Analytics
- Q19: Traffic Manager 라우팅 방법
- Q20: Azure Front Door vs Application Gateway
- Q21: KQL 기초
- Q22: 진단 설정(3가지 대상)
- Q23: 경고 처리 규칙(억제)
- Q24: VM Insights
- Q25: Azure Advisor 범주
- Q26: MARS 에이전트(온프레미스 백업)
- Q27: Recovery Services Vault vs Backup Vault
- Q28-Q29: Azure Migrate 워크플로 및 준비성 상태
- Q30-Q31: Key Vault(일시 삭제 + 제거 보호, 관리 ID 액세스)
- Q32-Q33: Azure Policy 심화(수정 작업, 이니셔티브)
- Q34: Azure Firewall이 있는 허브-스포크 UDR
- Q35: GatewaySubnet NSG 제약
- Q36: Azure Service Health vs Resource Health
- Q37: 저장된 액세스 정책 취소
- Q38: 관리 그룹
- Q39: ASG 멤버십 및 NSG 규칙
- Q40: Azure Advisor 예약 권장 사항
