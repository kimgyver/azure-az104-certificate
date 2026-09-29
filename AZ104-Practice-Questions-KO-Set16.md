# AZ-104 예상 문제 (세트 10) — 한국어

## 1) ID, RBAC, Microsoft Graph

### 문제 1

Azure 구독에 현재 **3,500개**의 역할 할당이 있습니다. 관리자가 **100명**의 사용자가 포함된 보안 그룹에 **Virtual Machine Contributor** 역할을 할당했습니다. 이 작업 후 구독의 총 역할 할당 수는?

- A. 3,600개 (그룹 내 사용자 1명당 1개)
- B. 3,501개 (그룹 보안 주체에 1개 생성)
- C. 3,500개 (새 할당 없음; 그룹 멤버십은 별도 추적)
- D. 3,601개 (사용자별 1개 + 그룹 1개)

**정답: B**

**해설:** RBAC 역할을 보안 그룹에 할당하면 그룹 구성원 수에 관계없이 **그룹 보안 주체**에 대한 **단일 역할 할당 개체**가 생성됩니다. Azure 구독당 최대 4,000개의 역할 할당이 지원되므로, 개별 사용자 대신 그룹에 할당하는 것이 모범 사례입니다.

---

### 문제 2

관리 ID가 스토리지 계정의 **블로브 데이터를 읽기만** 할 수 있고 계정 자체는 관리할 수 없도록 커스텀 RBAC 역할을 만들려 합니다. 블로브 읽기 권한은 역할 정의의 어느 속성에 넣어야 합니까?

- A. Actions
- B. NotActions
- C. DataActions
- D. NotDataActions

**정답: C**

**해설:** `Actions`와 `NotActions`는 관리 플레인(ARM) 작업을 제어합니다. 블로브 콘텐츠 접근은 **데이터 플레인 작업**이므로 `DataActions`에 지정해야 합니다. 예: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`.

---

### 문제 3

Microsoft Graph PowerShell SDK를 사용하는 스크립트가 `Connect-MgGraph`로 연결하여 Microsoft Entra ID 사용자를 **생성하고 업데이트**해야 합니다. 최소 권한 원칙에 따라 요청해야 하는 권한 범위는?

- A. User.Read
- B. User.Read.All
- C. User.ReadWrite.All
- D. Directory.ReadWrite.All

**정답: C**

**해설:** `User.ReadWrite.All`은 모든 사용자 개체에 대한 읽기 및 쓰기 권한을 부여합니다. `User.Read` 및 `User.Read.All`은 읽기 전용입니다. `Directory.ReadWrite.All`은 사용자 관리에 필요한 것보다 훨씬 광범위한 테넌트 전체 쓰기 권한을 부여합니다.

---

### 문제 4

사용자가 Azure 구독의 **모든 리소스를 관리**(VM, 스토리지, 네트워크 생성·수정·삭제 등)할 수 있어야 하지만, **다른 보안 주체에 역할을 할당해서는 안 됩니다**. 두 요구사항을 모두 충족하는 기본 제공 역할은?

- A. Owner
- B. Contributor
- C. User Access Administrator
- D. Reader

**정답: B**

**해설:** `Contributor`는 `Actions: *`를 부여하지만, `NotActions`에 `Microsoft.Authorization/*/Write`와 `Microsoft.Authorization/*/Delete`가 명시적으로 포함되어 역할 할당을 차단합니다. `Owner`는 역할 할당 권한이 포함됩니다.

---

### 문제 5

타사 애플리케이션에 블로브 컨테이너에 대한 **시간 제한 읽기 전용** 접근 권한을 부여해야 합니다. 스토리지 계정 키를 노출하지 않고 최소 권한을 따르는 SAS 유형은?

- A. Account SAS (계정 키로 서명)
- B. Service SAS (계정 키로 서명)
- C. 사용자 위임 SAS (Entra ID 자격 증명으로 서명)
- D. 저장된 액세스 정책이 있는 Service SAS

**정답: C**

**해설:** 사용자 위임 SAS는 스토리지 계정 키가 아닌 **Microsoft Entra ID 자격 증명**으로 서명됩니다. 이는 계정 키 노출 위험을 없애며, Microsoft가 블로브 접근에 권장하는 가장 안전한 방식입니다.

---

### 문제 6

WebApp1은 **시스템 할당 관리 ID**를 사용하여 **Azure RBAC 권한 모델**을 사용하는 KV1에서 Key Vault 참조로 비밀을 읽습니다. 최소 권한에 따라 WebApp1 관리 ID에 KV1에서 어떤 역할을 할당해야 합니까?

- A. Key Vault Contributor
- B. Key Vault Reader
- C. Key Vault Secrets User
- D. Key Vault Administrator

**정답: C**

**해설:** `Key Vault Secrets User`는 비밀 값을 읽을 수 있는 권한을 부여합니다. `Key Vault Reader`는 메타데이터만 읽고 실제 비밀 값에는 접근할 수 없습니다. `Contributor`와 `Administrator`는 필요 이상의 관리 권한을 부여합니다.

---

### 문제 7

관리자가 **RG1** 리소스 그룹에 **CanNotDelete** 잠금을 적용했습니다. RG1에는 10개의 가상 머신이 있습니다. 이 잠금이 VM에 미치는 영향은?

- A. 잠금은 리소스 그룹 컨테이너에만 적용되며 VM은 영향 없음
- B. VM이 CanNotDelete 잠금을 상속받아 개별적으로 삭제할 수 없음
- C. VM은 삭제할 수 있지만 리소스 그룹 자체는 삭제 불가
- D. 잠금이 적용되려면 각 VM에 개별적으로 설정해야 함

**정답: B**

**해설:** Azure 리소스 잠금은 **계층적으로 상속**됩니다. 리소스 그룹에 적용된 잠금은 해당 그룹 내 모든 리소스에 자동으로 전파됩니다. RG1의 각 VM은 잠금이 활성화된 동안 삭제할 수 없습니다.

---

### 문제 8

조직이 SSPR(2가지 인증 방법 필요)과 함께 **통합 보안 정보 등록(Combined Security Information Registration)**을 활성화했습니다. 신규 사용자가 처음 로그인할 때 경험하는 내용으로 옳은 것은?

- A. MFA와 SSPR에 대한 별도 등록 마법사를 완료해야 함
- B. 인증 방법을 한 번 등록하면 MFA와 SSPR 모두에 적용됨
- C. 첫 로그인 시 MFA 등록만 필요함
- D. 레거시 MFA 등록 페이지(aka.ms/mfasetup)로 이동함

**정답: B**

**해설:** 통합 등록은 경험을 통합합니다. 사용자는 통합된 흐름에서 인증 방법을 **한 번만 등록**하면 되고, 그 방법이 MFA 챌린지와 SSPR 복구 모두에 사용됩니다. 이전에 두 포털에서 별도로 등록해야 했던 요구사항이 더 이상 적용되지 않습니다.

---

### 문제 9

User1은 Log Analytics 작업 영역에서 **KQL 쿼리를 실행**하여 이벤트를 조사해야 합니다. User1은 작업 영역 설정 수정, 액세스 키 조회, 데이터 수집 규칙 변경이 **불가**해야 합니다. 최소 권한으로 이 요구사항을 충족하는 역할은?

- A. Reader (구독 범위)
- B. Log Analytics Contributor
- C. Log Analytics Reader
- D. Monitoring Reader

**정답: C**

**해설:** `Log Analytics Reader`는 로그 데이터를 조회하고 쿼리할 수 있는 권한을 부여하지만 쓰기 작업은 허용하지 않습니다. 구독 수준의 `Reader`는 로그 쿼리에 필요한 권한이 포함되지 않습니다. `Monitoring Reader`는 로그 쿼리 접근 권한이 없습니다.

---

## 2) 거버넌스

### 문제 10

Azure에서 **루트 관리 그룹과 구독을 제외하고** 지원되는 **최대 중첩 관리 그룹 레벨 수**는?

- A. 4
- B. 5
- C. 6
- D. 10

**정답: C**

**해설:** Azure는 최대 **6단계**의 관리 그룹 중첩을 지원합니다. 계층 구조: 루트 → 레벨 1 → 레벨 2 → 레벨 3 → 레벨 4 → 레벨 5 → 레벨 6 → 구독. 루트와 구독은 이 깊이 계산에 포함되지 않습니다.

---

### 문제 11

조직은 **새로 생성되는 모든 Azure 구독**이 루트 관리 그룹이 아닌 **Onboarding** 관리 그룹에 자동으로 배치되기를 원합니다. 무엇을 구성해야 합니까?

- A. 루트 관리 그룹 범위에서 Azure Policy 적용
- B. 관리 그룹 설정의 **기본 관리 그룹(Default management group)** 설정 변경
- C. Onboarding 관리 그룹에 RBAC 할당
- D. 루트 범위에 Azure Blueprint 적용

**정답: B**

**해설:** **기본 관리 그룹** 설정(관리 그룹 → 설정)은 새로 생성되는 구독이 자동으로 배치되는 위치를 제어합니다. Onboarding 관리 그룹으로 변경하면 모든 새 구독이 루트 대신 해당 그룹에 배치됩니다.

---

### 문제 12

조직이 **Microsoft Entra ID Free**를 사용 중입니다. 관리자가 `user.department` 특성을 기반으로 자동 멤버십을 가진 보안 그룹을 만들려 합니다. 먼저 무엇을 해야 합니까?

- A. 동적 사용자 멤버십 규칙이 있는 Microsoft 365 그룹 만들기
- B. 예약된 PowerShell 스크립트로 그룹 멤버십 관리
- C. 테넌트를 **Microsoft Entra ID Premium P1**으로 업그레이드
- D. 테넌트 설정에서 B2B 협업 활성화

**정답: C**

**해설:** 동적 멤버십 그룹은 **Microsoft Entra ID Premium P1**(또는 P2)이 필요합니다. Free 티어는 동적 그룹 멤버십 규칙을 지원하지 않으므로, 그룹을 만들기 전에 업그레이드가 필요합니다.

---

## 3) 스토리지, SAS, 수명 주기 관리

### 문제 13

Azure 파일 공유가 **볼트 계층 백업(vaulted backup)**으로 보호되고 있습니다. 랜섬웨어 공격 후 파일 공유 전체를 복원해야 합니다. 볼트 계층 복구 지점에서 지원되는 복원 작업은?

- A. 원본 파일 공유로의 파일 수준 복구
- B. 원본 위치로의 전체 공유 복구 (OLR)
- C. **대체 스토리지 계정으로의 전체 공유 복구만** 지원
- D. 모든 위치로의 증분 복구

**정답: C**

**해설:** Azure Files 볼트 백업은 현재 **대체 위치로의 전체 공유 복구**만 지원합니다. 원본 위치 복구(OLR)와 파일 수준 복구는 볼트 계층 복구 지점에서 지원되지 않습니다.

---

### 문제 14

Azure Files 스토리지 계정에 현재 **Microsoft Entra Domain Services** 인증이 활성화되어 있습니다. 관리자가 **온프레미스 Active Directory Domain Services** 인증으로 전환하려 합니다. AD DS를 활성화하기 전에 무엇을 해야 합니까?

- A. 두 방법을 동시에 활성화한 후 이전 방법 비활성화
- B. 스토리지 계정에서 **Microsoft Entra Domain Services 인증을 먼저 비활성화**
- C. 새 스토리지 계정을 만들고 처음부터 AD DS 구성
- D. VNet에 온프레미스 도메인 컨트롤러를 DNS 서버로 추가

**정답: B**

**해설:** Azure Files는 스토리지 계정당 **한 번에 하나의 ID 기반 인증 방법**만 지원합니다. 새 방법(온프레미스 AD DS)을 활성화하기 전에 현재 방법(Entra Domain Services)을 비활성화해야 합니다.

---

### 문제 15

Azure Files 공유가 Azure Backup으로 몇 달간 성공적으로 백업되다가 갑자기 실패하기 시작했습니다. 파일 공유와 스토리지 계정은 모두 존재하고 접근 가능합니다. 가장 가능성이 높은 원인은?

- A. Recovery Services 볼트가 일시 삭제됨
- B. 파일 공유당 최대 스냅샷 수(**200개**)에 도달함
- C. 파일 서버의 Azure Backup 에이전트가 만료됨
- D. 백업 구성 후 스토리지 계정 액세스 키가 교체됨

**정답: B**

**해설:** Azure Backup for Azure Files는 내부적으로 공유 스냅샷을 사용합니다. 각 파일 공유는 최대 **200개의 스냅샷**을 지원합니다. 이 한도에 도달하면 새 백업 작업이 추가 스냅샷을 만들 수 없어 실패합니다.

---

### 문제 16

수명 주기 관리 규칙에 기본 블로브에 대한 다음 세 가지 작업이 구성되어 있습니다: **수정 후 30일 경과 시 쿨 계층으로 이동**, **수정 후 30일 경과 시 보관 계층으로 이동**, **수정 후 30일 경과 시 삭제**. 특정 블로브에 대해 세 조건이 동시에 true로 평가됩니다. Azure가 적용하는 작업은?

- A. 쿨 계층으로 이동 (목록의 첫 번째 작업)
- B. 보관 계층으로 이동 (삭제 외 가장 차가운 계층)
- C. 삭제 (가장 저렴한 스토리지 작업)
- D. 세 작업이 순서대로 모두 적용됨

**정답: C**

**해설:** 여러 수명 주기 작업이 동시에 동일한 블로브와 일치하면 Azure는 **가장 저렴한 작업**을 선택합니다. 비용 순위: 삭제 < 보관 < 쿨. 따라서 삭제 작업이 계층 전환보다 우선합니다.

---

### 문제 17

**30일 동안 접근이 없는 블로브**를 쿨 계층으로 이동하고, 접근 시 자동으로 핫 계층으로 복귀하는 수명 주기 정책을 구성하려 합니다. 이 정책을 만들기 **전에** 스토리지 계정에서 무엇을 활성화해야 합니까?

- A. 블로브 버전 관리
- B. 블로브에 대한 일시 삭제
- C. **블로브 마지막 접근 시간 추적(Last access time tracking)**
- D. 변경 불가 스토리지 정책

**정답: C**

**해설:** `daysAfterLastAccessTimeGreaterThan` 조건과 `enableAutoTierToHotFromCool` 속성 모두 **마지막 접근 시간 추적**이 먼저 활성화되어야 합니다. 이 기능 없이는 Azure가 블로브의 `LastAccessTime` 속성을 평가할 수 없습니다.

---

### 문제 18

블로브를 **쿨 계층에서 콜드 계층으로** 이동하는 수명 주기 정책을 만들려 합니다. 조기 삭제 요금을 피하려면 블로브가 쿨 계층에 최소 **며칠** 이상 있어야 합니까?

- A. 7일
- B. 14일
- C. 30일
- D. 90일

**정답: C**

**해설:** 쿨 계층은 최소 보존 기간이 **30일**입니다. 30일 전에 블로브를 이동하거나 삭제하면 남은 일수에 대한 조기 삭제 요금이 일할 계산으로 부과됩니다.

---

### 문제 19

스토리지 계정에 **30일 보존 기간**의 **블로브 일시 삭제**가 활성화되어 있습니다. 블로브 버전 관리는 활성화되지 않았습니다. 사용자가 5일 전에 `file1.txt`를 실수로 삭제했습니다. `file1.txt`를 어떻게 복구해야 합니까?

- A. 버전 기록에서 이전 블로브 버전 복원
- B. 일시 삭제된 블로브에 대해 **Undelete Blob 작업 호출**
- C. 블로브 스냅샷에서 복원
- D. Azure 지원에 문의 — 버전 관리 없는 일시 삭제 블로브는 복구 불가

**정답: B**

**해설:** 일시 삭제만 활성화된 경우(버전 관리 없음), 삭제된 블로브는 일시 삭제 상태가 되며 보존 기간 내에 **Undelete Blob 작업**으로 복구할 수 있습니다. 버전 관리는 필요하지 않으며 Undelete는 삭제 전 상태로 블로브를 복원합니다.

---

### 문제 20

**RG1**에는 스토리지 계정과 가상 네트워크가 있습니다. **가상 머신만** 정의하는 ARM 템플릿이 **Complete** 배포 모드로 RG1에 배포됩니다. RG1의 기존 리소스에 어떤 일이 발생합니까?

- A. VM만 생성되고 스토리지 계정과 가상 네트워크는 유지됨
- B. 스토리지 계정과 가상 네트워크가 삭제되고 VM만 남음
- C. 템플릿이 기존 리소스를 참조하지 않아 배포가 실패함
- D. 관련 없는 리소스가 이미 존재하면 Complete 모드는 허용되지 않음

**정답: B**

**해설:** ARM **Complete 모드**는 리소스 그룹을 템플릿과 정확히 일치하도록 동기화합니다. **템플릿에 정의되지 않은 리소스 그룹 내의 모든 리소스는 삭제됩니다**. 스토리지 계정과 가상 네트워크가 VM 전용 템플릿에 없으므로 제거됩니다.

---

### 문제 21

Windows Server에 세 개의 볼륨이 있습니다: **C: (시스템 드라이브, NTFS)**, **D: (NTFS 데이터 드라이브)**, **E: (ReFS 데이터 드라이브)**. cloud tiering가 활성화된 Azure File Sync를 구성합니다. cloud tiering가 있는 서버 엔드포인트를 호스팅할 수 있는 볼륨은?

- A. C:, D:, E: 모두
- B. D:와 E: (시스템 드라이브가 아닌 볼륨)
- C. D: 만 (NTFS 비시스템 볼륨)
- D. C:와 D: (NTFS 볼륨만)

**정답: C**

**해설:** cloud tiering는 **NTFS** 볼륨만 지원하며 ReFS는 지원되지 않습니다. 또한 NTFS여도 **시스템 볼륨(C:)** 은 cloud tiering가 있는 서버 엔드포인트를 호스팅할 수 없습니다. D: 만이 유일한 적합한 볼륨입니다.

---

### 문제 22

같은 NTFS 볼륨에 세 개의 서버 엔드포인트가 있습니다. 볼륨 여유 공간 정책: **SE1 = 15%**, **SE2 = 20%**, **SE3 = 30%**. Azure File Sync가 이 볼륨에 적용하는 유효 여유 공간 정책은?

- A. 15% (가장 낮음 — 가장 적극적인 Cloud Tiering)
- B. 21.7% (세 값의 평균)
- C. 20% (중간값)
- D. 30% (가장 높음 — 가장 보수적)

**정답: D**

**해설:** 여러 서버 엔드포인트가 볼륨을 공유할 때 Azure File Sync는 **가장 제한적인(가장 높은) 여유 공간 비율**을 적용합니다. 이는 모든 엔드포인트에서 가장 엄격한 정책이 적용되어 볼륨 공간 부족을 방지합니다.

---

## 4) 네트워킹

### 문제 23

**Azure Container Instances** 컨테이너 그룹이 가상 네트워크 서브넷 내에 배포되어 있습니다. 컨테이너에 아웃바운드 인터넷 접근이 필요합니다. 서브넷에는 공용 IP나 부하 분산 장치가 없습니다. 무엇을 구성해야 합니까?

- A. 컨테이너 그룹에 직접 공용 IP 주소 할당
- B. 아웃바운드 규칙이 있는 Azure Standard Load Balancer 배포
- C. 서브넷에 **NAT Gateway** 연결
- D. 컨테이너 그룹 앞에 Azure Application Gateway 배포

**정답: C**

**해설:** VNet 통합 ACI 컨테이너 그룹의 아웃바운드 인터넷 접근에는 서브넷에 **NAT Gateway**가 필요합니다. NAT Gateway는 아웃바운드 트래픽에 대한 SNAT를 제공합니다. VNet 내 ACI에 직접 공용 IP 할당은 일반 아웃바운드 인터넷 접근에 지원되지 않습니다.

---

### 문제 24

VM의 NIC에는 **NSG-NIC**가, VM이 속한 서브넷에는 **NSG-Subnet**이 연결되어 있습니다. 포트 443의 인터넷 트래픽이 VM을 대상으로 들어옵니다. 이 **인바운드** 트래픽에 대해 NSG가 평가되는 순서는?

- A. NSG-NIC 먼저, 그 다음 NSG-Subnet
- B. NSG-Subnet 먼저, 그 다음 NSG-NIC
- C. 인바운드 트래픽에는 NSG-Subnet만 평가됨
- D. 두 NSG의 규칙이 병합되어 우선순위 번호 순으로 평가됨

**정답: B**

**해설:** **인바운드** 트래픽의 경우 Azure는 **서브넷 NSG를 먼저** 평가하고 그 다음 **NIC NSG**를 평가합니다. 트래픽이 VM에 도달하려면 두 NSG 모두 허용해야 합니다. 이 평가 순서는 고정되어 있으며 변경할 수 없습니다.

---

### 문제 25

동일한 VM이 포트 80에서 인터넷으로 **아웃바운드** 연결을 시작합니다. 서브넷에는 NSG-Subnet, NIC에는 NSG-NIC가 있습니다. NSG가 평가되는 순서는?

- A. NSG-Subnet 먼저, 그 다음 NSG-NIC
- B. NSG-NIC 먼저, 그 다음 NSG-Subnet
- C. 아웃바운드 트래픽에는 NSG-NIC만 평가됨
- D. 두 NSG의 우선순위 번호가 평가 순서를 결정함

**정답: B**

**해설:** **아웃바운드** 트래픽의 경우 평가 순서가 반전됩니다: **NIC NSG 먼저**, 그 다음 **서브넷 NSG**. 패턴: 인바운드 = 서브넷 → NIC; 아웃바운드 = NIC → 서브넷.

---

### 문제 26

서브넷이 App Service VNet 통합을 위해 `Microsoft.Web/serverFarms`에 **위임**되어 있습니다. 개발자가 Azure SQL Database의 **프라이빗 엔드포인트**를 동일한 위임 서브넷에 배포하려 합니다. 어떻게 됩니까?

- A. 프라이빗 엔드포인트는 위임된 서비스와 공존할 수 있음
- B. 다른 서비스에 위임된 서브넷에는 **프라이빗 엔드포인트를 만들 수 없음**
- C. 다른 NSG를 사용하면 프라이빗 엔드포인트 허용
- D. 서브넷에 사용 가능한 IP 주소가 5개 이상이면 가능

**정답: B**

**해설:** **위임 서브넷은 위임된 서비스 전용**입니다. 프라이빗 엔드포인트는 위임 서브넷에 배포할 수 없습니다. 프라이빗 엔드포인트를 위한 별도의 위임되지 않은 서브넷이 필요합니다.

---

### 문제 27

가상 네트워크가 **BGP를 사용하는 VPN 게이트웨이**를 통해 온프레미스에 연결되어 있습니다. 라우팅 테이블이 **Subnet1**에 연결되어 있습니다. Subnet1의 VM이 BGP를 통해 온프레미스 경로를 **학습하지 않아야** 합니다. 라우팅 테이블에서 무엇을 구성해야 합니까?

- A. 가상 어플라이언스를 가리키는 기본 경로(0.0.0.0/0) 추가
- B. 라우팅 테이블에서 **가상 네트워크 게이트웨이 경로 전파 비활성화**
- C. VPN 게이트웨이에서 BGP 세션 제거
- D. 모든 온프레미스 CIDR 블록에 대한 명시적 거부 경로 추가

**정답: B**

**해설:** 라우팅 테이블에서 **가상 네트워크 게이트웨이 경로 전파를 비활성화**하면 해당 서브넷이 게이트웨이에서 전파된 경로(BGP 학습 온프레미스 경로 포함)를 수신하지 않습니다. VPN 연결과 BGP 세션은 다른 서브넷에 대해 계속 활성화됩니다.

---

### 문제 28

**ASG-Web**이 **VNet1**에 있으며 VM1(Subnet1)의 NIC가 포함되어 있습니다. **VM2의 NIC**를 ASG-Web에 추가하려 합니다. VM2는 VNet1의 **Subnet2**에 있습니다. 이 작업에 필요한 요구사항은?

- A. VM2를 VM1과 같은 Subnet1으로 이동해야 함
- B. Subnet1과 Subnet2 간에 서브넷 피어링을 만들어야 함
- C. VM2가 VM1과 동일한 가상 네트워크(VNet1)에 있어야 함
- D. ASG-Web을 삭제하고 다시 만들어야 함

**정답: C**

**해설:** 애플리케이션 보안 그룹(ASG)은 **동일한 VNet 내 다른 서브넷**의 NIC를 지원합니다. 유일한 요구사항은 모든 NIC 구성원이 **동일한 가상 네트워크**에 속해야 한다는 것입니다. 서로 다른 VNet에 걸친 ASG는 지원되지 않습니다.

---

### 문제 29

DNS 영역 **contoso.com**이 Azure DNS에 호스팅되어 있습니다. **contoso.com**(영역 apex)이 **Traffic Manager** 프로필로 확인되어야 합니다. contoso.com에 CNAME 레코드를 만들려는 시도가 실패합니다. 무엇을 사용해야 합니까?

- A. Traffic Manager 프로필의 고정 IP 주소를 가리키는 A 레코드
- B. Traffic Manager 엔드포인트를 가리키는 AAAA 레코드
- C. Traffic Manager 프로필을 가리키는 **Azure DNS ALIAS A 레코드**
- D. 짧은 TTL을 가진 CNAME 레코드로 제한을 우회

**정답: C**

**해설:** CNAME 레코드는 **DNS 영역 apex에서 허용되지 않습니다**. apex에는 이미 SOA 및 NS 레코드가 있고 CNAME은 동일한 이름의 다른 레코드 유형과 공존할 수 없기 때문입니다. **Azure DNS ALIAS 레코드**는 고정 IP 없이 apex에서 Azure 리소스(Traffic Manager 등)를 가리킬 수 있게 합니다.

---

### 문제 30

Azure Firewall에 **위협 인텔리전스(Threat Intelligence)** 필터링이 기본 구성으로 활성화되어 있습니다. 관리자가 알려진 악성 IP의 트래픽이 **로깅되지만 차단되지 않는 것**을 확인합니다. 트래픽을 로깅하고 차단하려면 어떤 변경이 필요합니까?

- A. Azure Firewall Premium IDPS를 경고 모드로 활성화
- B. 위협 인텔리전스 모드를 **경고 및 거부(Alert and deny)**로 변경
- C. 악성 CIDR에 대한 명시적 거부 네트워크 규칙 만들기
- D. 차단 기능을 위해 Premium SKU로 업그레이드

**정답: B**

**해설:** **기본** 위협 인텔리전스 모드는 **경고 전용(Alert only)**입니다. 이 모드는 로그를 생성하지만 트래픽을 허용합니다. **경고 및 거부** 모드로 변경하면 알려진 악성 IP 및 도메인의 트래픽을 로깅하고 **차단**합니다.

---

### 문제 31

스토리지 계정에 대한 프라이빗 엔드포인트가 만들어졌습니다. **VNet1**의 VM이 스토리지 계정의 프라이빗 FQDN을 **프라이빗 IP**로 확인해야 합니다. 올바른 A 레코드가 있는 Private DNS Zone `privatelink.blob.core.windows.net`이 이미 만들어졌습니다. 추가로 만들어야 하는 리소스는?

- A. VNet1에 DNS 전달 가상 머신
- B. Private DNS Zone에 사용자 지정 CNAME 레코드
- C. Private DNS Zone과 VNet1 간의 **Virtual Network Link**
- D. Azure Firewall DNS 프록시 규칙

**정답: C**

**해설:** Private DNS Zone은 자동으로 어떤 VNet에도 사용되지 않습니다. VNet1에서 Zone을 사용하려면 **Virtual Network Link**를 만들어야 합니다. 이 링크가 없으면 VNet1의 VM은 Private DNS Zone을 사용하여 프라이빗 엔드포인트 DNS 레코드를 확인할 수 없습니다.

---

## 5) 컴퓨팅, AKS, App Service

### 문제 32

단일 Azure 지역 내에서 **데이터 센터 수준의 장애**로부터 보호받고 **99.99% SLA**를 달성해야 하는 가상 머신을 배포해야 합니다. 두 요구사항을 모두 충족하는 배포 옵션은?

- A. 가용성 집합(Availability Set)에 VM 배포
- B. Premium SSD 관리 디스크가 있는 단일 VM 배포
- C. 동일 지역의 **둘 이상의 가용성 영역**에 VM 배포
- D. 영역을 지정하지 않고 VM Scale Set 배포

**정답: C**

**해설:** 가용성 집합은 단일 데이터 센터 내 랙 수준 장애를 방지하지만 데이터 센터 수준 격리를 제공하지 않습니다. **가용성 영역**은 지역 내 물리적으로 분리된 시설입니다. 2개 이상의 영역에 배포하면 **99.99% SLA**를 달성하고 전체 데이터 센터 장애를 방지합니다.

---

### 문제 33

App Service 웹앱이 **버전을 지정하지 않는 Key Vault 참조**를 사용합니다. 관리자가 비밀을 교체했습니다. 앱은 재시작되지 않았습니다. 앱이 새 비밀 값을 사용하기 시작하는 **최대 시간**은?

- A. 교체 직후 즉시
- B. 최대 1시간
- C. 최대 **24시간**
- D. 다음 배포 슬롯 교체 후에만

**정답: C**

**해설:** App Service는 각 요청마다 Key Vault를 호출하는 것을 방지하기 위해(지연 및 제한 위험) Key Vault 참조 값을 캐시합니다. 캐시는 주기적으로 최대 **24시간** 간격으로 새로 고쳐집니다. 앱을 재시작하면 즉시 새로 고침이 강제됩니다.

---

### 문제 34

**AKS1**에는 Linux 시스템 노드 풀이 있습니다. **Windows 컨테이너 사용자 노드 풀**을 추가할 계획입니다. Windows 노드 풀을 추가하기 전에 클러스터에 필요한 조건은?

- A. 클러스터에 최소 3개의 가용성 영역이 구성되어 있어야 함
- B. 클러스터가 **Standard SKU 부하 분산 장치**를 사용해야 함
- C. 클러스터에 Premium SSD 스토리지 클래스가 구성되어 있어야 함
- D. 클러스터가 Kubernetes 버전 1.16 이하를 실행해야 함

**정답: B**

**해설:** AKS의 여러 노드 풀에는 **Standard SKU 부하 분산 장치**가 필요합니다. Basic SKU 부하 분산 장치는 여러 노드 풀을 지원하지 않습니다. Standard SKU는 클러스터 생성 시 선택해야 하며 나중에 변경할 수 없습니다.

---

### 문제 35

CI/CD 파이프라인이 서비스 주체로 실행되며 Registry1에 컨테이너 이미지를 **푸시**하고 배포 중에 이미지를 **풀**해야 합니다. 최소 권한 원칙에 따라 할당해야 하는 역할은?

- A. AcrPull
- B. **AcrPush**
- C. Contributor
- D. Owner

**정답: B**

**해설:** `AcrPush`에는 **푸시와 풀** 권한이 모두 포함됩니다. `AcrPull`은 풀만 허용합니다. `Contributor`는 CI/CD 서비스 주체에 필요하지 않은 레지스트리 관리 작업(방화벽 규칙, SKU 변경 등)을 부여합니다.

---

### 문제 36

사용자가 기본 RDP 포트 3389와 SSH 포트 22 대신 **사용자 지정 포트**를 사용하여 Azure Bastion을 통해 VM에 연결해야 합니다. 사용자 지정 포트를 지원하는 **최소 Azure Bastion SKU**는?

- A. Basic
- B. **Standard**
- C. Developer
- D. 사용자 지정 포트는 모든 SKU에서 사용 가능

**정답: B**

**해설:** Azure Bastion의 사용자 지정 포트 지원은 **Standard SKU**부터 제공됩니다. Basic SKU는 기본 RDP(3389)와 SSH(22) 포트만 지원합니다.

---

### 문제 37

**Kubernetes**에서 컨테이너의 CPU 요청이 1코어, 제한이 2코어로 설정되어 있습니다. 동일한 컨테이너 워크로드가 **ACI**에서 CPU `1`(요청값, 제한 없음)로 배포됩니다. 두 플랫폼의 차이를 올바르게 설명하는 것은?

- A. Kubernetes는 2코어를 보장하고, ACI는 1코어를 최소로 보장
- B. Kubernetes에서는 컨테이너가 최대 2코어(제한)까지 사용 가능하고, ACI에서는 요청값 1이 실질적인 최대값
- C. ACI는 요청값을 초과하여 버스트할 수 있고, Kubernetes는 불가
- D. 두 플랫폼 모두 요청값을 최솟값이자 최댓값으로 취급

**정답: B**

**해설:** **Kubernetes**에서 `request`는 보장된 최솟값(스케줄링에 사용)이고 `limit`가 하드 캡입니다. **ACI**에서는 별도의 제한 개념이 없으며 `request` 값이 컨테이너의 **실질적인 최대** 리소스 할당입니다.

---

### 문제 38

**Azure 구독 예산 임계값**이 초과될 때 자동으로 Slack 알림을 보내고 ITSM 티켓을 생성해야 합니다. 사용자 지정 애플리케이션 코드 없이 이 자동화 워크플로를 처리하는 Azure 서비스는?

- A. HTTP 트리거가 있는 Azure Function
- B. 예산 경고 작업으로 트리거되는 **Azure Logic App**
- C. Azure Automation PowerShell Runbook
- D. Azure Event Grid 사용자 지정 토픽

**정답: B**

**해설:** **Azure Logic App**은 Slack, ITSM 플랫폼 등 수백 개의 서비스에 대한 기본 제공 커넥터가 있는 노코드/로우코드 워크플로 자동화 서비스입니다. Azure Budget 경고는 HTTP/웹훅 트리거를 통해 Logic App을 직접 트리거할 수 있어 코드 없이 자동화된 다단계 워크플로를 구현합니다.

---

## 6) Infrastructure as Code

### 문제 39

기본 ARM 템플릿이 Azure Blob Storage에 저장된 **연결 템플릿(Linked Template)**을 호출하여 App Service Plan을 배포해야 합니다. 기본 템플릿에서 연결 템플릿을 참조하는 데 사용해야 하는 리소스 유형은?

- A. `Microsoft.Resources/templateSpecs`
- B. `Microsoft.Compute/virtualMachines`
- C. `Microsoft.Resources/deployments`
- D. `Microsoft.Resources/links`

**정답: C**

**해설:** 연결 템플릿은 외부 템플릿 URL을 가리키는 `templateLink` 속성이 있는 **`Microsoft.Resources/deployments`** 리소스 유형으로 호출됩니다. 이렇게 하면 연결 템플릿을 자식 배포로 실행하는 중첩 배포가 생성됩니다.

---

### 문제 40

Bicep 파일이 `deployStorage` 매개변수가 `true`일 때만 스토리지 계정을 조건부로 배포합니다. 배포 시 블로브 엔드포인트를, 미배포 시 **빈 문자열**을 반환하는 올바른 출력 구문은?

- A. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`
- B. `output blobEndpoint string = if (deployStorage) storageAccount.properties.primaryEndpoints.blob`
- C. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- D. `output blobEndpoint string = when deployStorage storageAccount.properties.primaryEndpoints.blob else ''`

**정답: A**

**해설:** Bicep은 조건부 값 선택에 **삼항 연산자**(`조건 ? 참값 : 거짓값`)를 사용합니다. 이것이 배포 시 블로브 엔드포인트를, 미배포 시 빈 문자열을 반환하는 올바른 구문입니다.

---

### 문제 41

**여러 구독 전체의 모든 가상 머신** 목록(태그 및 위치 포함)을 표시하는 Azure Workbook을 만들고 있습니다. 어떤 데이터 소스를 사용해야 합니까?

- A. Azure Monitor 메트릭
- B. Azure Monitor 로그 (Log Analytics 작업 영역)
- C. **Azure Resource Graph**
- D. Azure 활동 로그

**정답: C**

**해설:** **Azure Resource Graph**는 여러 구독 전체에서 Azure 리소스 메타데이터(유형, 태그, 위치, SKU)를 효율적으로 쿼리하도록 설계되었습니다. Azure Monitor 메트릭은 시계열 성능 데이터, Monitor 로그는 원격 분석 및 이벤트, 활동 로그는 관리 플레인 이벤트를 저장합니다.

---

### 문제 42

User1은 **Monitoring Reader** 역할을 가지고 있으며 공유 Azure Monitor 워크북을 **편집하고 저장**해야 합니다. User1에게 추가로 할당해야 하는 역할은?

- A. Monitoring Contributor
- B. Log Analytics Contributor
- C. **Workbook Contributor**
- D. Contributor (구독 범위)

**정답: C**

**해설:** **Workbook Contributor**는 공유 워크북을 만들고 편집하고 저장하는 데 필요한 `microsoft.insights/workbooks/write` 권한을 제공합니다. Monitoring Contributor도 워크북 쓰기 권한을 부여하지만 필요한 것보다 광범위한 모니터링 권한을 제공합니다.

---

## 7) 모니터링, 운영, 기타

### 문제 43

여러 가상 머신에 대해 **VM Insights**를 활성화했습니다. VM Insights가 수집된 성능 메트릭과 프로세스 종속성 데이터를 저장하는 위치는?

- A. 지정된 Azure Storage 계정
- B. **Log Analytics 작업 영역**
- C. Azure Monitor 메트릭 저장소 (플랫폼 메트릭)
- D. Application Insights 리소스

**정답: B**

**해설:** VM Insights는 CPU, 메모리, 디스크 성능, 프로세스 정보, 네트워크 종속성 맵을 포함한 모든 수집 데이터를 **Log Analytics 작업 영역**에 저장합니다. 데이터는 KQL 쿼리 가능한 테이블에 기록됩니다.

---

### 문제 44

Cloud Tiering이 활성화된 상태로 Windows Server에 Azure File Sync가 구성되어 있습니다. 파일(`report.xlsx`)이 Azure로 Cloud Tiering되어 로컬에는 **플레이스홀더 스텁**만 남아 있습니다. 사용자가 Windows 탐색기에서 `report.xlsx`를 엽니다. 어떻게 됩니까?

- A. 콘텐츠가 로컬에 없어 "파일을 찾을 수 없음" 오류 발생
- B. 파일이 접근 후 로컬 스토리지에 영구적으로 복원됨
- C. 파일 콘텐츠가 **Azure에서 자동으로 회수되어** 사용자에게 제공됨
- D. 사용자가 Azure Storage Explorer에서 수동으로 파일을 다운로드해야 함

**정답: C**

**해설:** Cloud Tiering은 **사용자에게 투명하게 작동**합니다. Cloud Tiering된 파일(플레이스홀더)에 접근하면 Azure File Sync가 Azure File Share에서 전체 파일 콘텐츠를 자동으로 회수합니다. 콘텐츠 다운로드 중 약간의 지연이 있을 수 있지만 특별한 작업은 필요 없습니다.

---

### 문제 45

App Service 앱이 Key Vault 참조를 사용합니다. 비밀이 교체된 후에도 앱이 **일정 시간 동안 이전 비밀 값을 계속 사용**합니다. 새 값이 즉시 적용되지 않는 이유는?

- A. App Service는 업데이트된 Key Vault 참조를 로드하기 위해 수동 재배포가 필요
- B. App Service는 모든 HTTP 요청마다 Key Vault를 호출하지 않기 위해 **비밀 값을 캐시**함
- C. Key Vault는 새 비밀 버전이 표시되기 전에 승인 워크플로 필요
- D. 교체는 교체 시간 이후에 만들어진 새 인스턴스에만 적용됨

**정답: B**

**해설:** App Service는 네트워크 지연과 Key Vault 제한 위험을 방지하기 위해 Key Vault 참조 값을 로컬에 캐시합니다. 캐시는 주기적으로 새로 고쳐지며 최대 24시간 간격입니다. 앱을 재시작하면 즉시 새로 고침이 강제됩니다.

---

### 문제 46

조직이 MFA 및 SSPR과 함께 **통합 보안 정보 등록**을 활성화했습니다. 사용자의 인증 방법 등록 경험에 대해 가장 잘 설명하는 것은?

- A. 사용자는 MFA와 SSPR에 대해 각각 인증 방법을 두 번 등록해야 함
- B. 사용자는 인증 방법을 **한 번 등록**하면 MFA와 SSPR 모두에 사용됨
- C. SSPR 등록은 자동으로 완료되며 사용자는 MFA만 등록하면 됨
- D. 통합 등록은 MFA와 SSPR을 단일 인증 챌린지로 대체함

**정답: B**

**해설:** 통합 등록은 **통합된 등록 흐름**을 제공합니다. 사용자는 인증 방법을 한 번 등록하면 그 방법이 MFA 챌린지와 SSPR 자가 서비스 암호 복구 모두에 사용됩니다. 이전에 별도 포털에서 동일한 방법을 등록해야 했던 요구사항이 더 이상 필요하지 않습니다.

---

### 문제 47

Azure Firewall에 **위협 인텔리전스 필터링**이 기본 구성으로 활성화되어 있습니다. 관리자가 로그를 검토하니 알려진 악성 IP의 트래픽에 대한 경고가 있지만 해당 IP의 트래픽이 **차단되지 않았습니다**. 이유는?

- A. Azure Firewall SKU가 위협 인텔리전스 차단을 지원하지 않음
- B. **기본 TI 모드는 경고 전용**으로, 로깅만 하고 트래픽을 차단하지 않음
- C. 위협 인텔리전스의 차단 기능은 IDPS 활성화가 필요함
- D. 애플리케이션 규칙이 위협 인텔리전스 결정을 재정의함

**정답: B**

**해설:** **기본 Azure Firewall 위협 인텔리전스 모드는 경고 전용**입니다. 이 모드에서 방화벽은 악성 트래픽 감지 시 경고 로그를 생성하지만 트래픽은 허용합니다. 경고와 차단을 모두 하려면 모드를 **경고 및 거부(Alert and deny)**로 명시적으로 변경해야 합니다.

---

### 문제 48

App Service가 **버전을 지정하지 않은** URI `https://vault.vault.azure.net/secrets/mySecret/`으로 Key Vault 비밀을 참조합니다. App Service는 어떤 비밀 버전을 사용합니까?

- A. 마지막 앱 재시작 시 현재였던 버전
- B. App Service는 항상 비밀의 **최신 활성 버전**을 가져옴
- C. 기본적으로 첫 번째 버전(버전 1)
- D. 버전을 항상 명시해야 하며 버전 없는 참조는 지원되지 않음

**정답: B**

**해설:** Key Vault 참조 URI에 버전이 지정되지 않으면 App Service는 비밀의 **최신 활성 버전**으로 참조를 확인합니다. 이를 통해 원활한 비밀 교체가 가능합니다. 앱은 24시간 캐시 새로 고침 주기 내에서 구성 변경 없이 자동으로 새 버전을 가져옵니다.

---

### 문제 49

**Azure Container Instances**에 CPU가 `2`(요청값)로 지정된 컨테이너가 배포됩니다. 별도의 CPU 제한이 정의되지 않았습니다. 이 컨테이너가 사용할 수 있는 최대 CPU 양은?

- A. 최대 4코어 (ACI는 요청값의 2배 버스트 허용)
- B. 정확히 **2코어** (ACI에서 요청값이 실질적인 최댓값)
- C. 1~2코어 (요청값이 최솟값 보장)
- D. 무제한 (호스트 가용성에 따라)

**정답: B**

**해설:** ACI에는 별도의 CPU 제한 개념이 없습니다. **요청값이 컨테이너의 실질적인 최대** 리소스 할당입니다. 이는 Kubernetes에서 `request`가 최솟값 보장이고 `limit`가 하드 캡인 것과 대조됩니다.

---

### 문제 50

Azure 구독에 현재 **3,999개**의 역할 할당이 있습니다. 구독 한도는 4,000개입니다. 관리자가 새 보안 그룹에 **Reader 역할**을 할당하려 합니다. 어떻게 됩니까?

- A. 4,000 한도를 초과하므로 할당 실패
- B. 할당이 **성공**하여 총 4,000개(정확히 한도)가 됨
- C. 가장 오래된 역할 할당이 자동으로 제거되어 새 항목 수용
- D. 역할 할당 한도는 사용자 보안 주체에만 적용되며 보안 그룹에는 적용 안 됨

**정답: B**

**해설:** 구독당 최대 허용 역할 할당은 **4,000개**입니다. 현재 3,999개 할당에 1개를 추가하면 정확히 4,000개가 됩니다. 이는 한도 초과가 아닌 한도 도달이므로 할당이 성공합니다. 4,001번째 할당 시도는 한도 초과 오류와 함께 실패합니다.

---

_문제 수: 50_
_최종 수정일: 2026-05-20_
