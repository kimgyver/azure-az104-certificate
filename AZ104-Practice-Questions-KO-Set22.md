# AZ-104 예상 문제 (세트 22) — 한국어

## 1) Application Gateway, 라우팅, 방화벽

### 문제 1

Azure Application Gateway v2에 경로 기반 라우팅 규칙이 있습니다. URL 경로 맵에 `/*`, `/images/*`, `/api/*` 순서로 세 가지 규칙이 있습니다. `/api/v1/users`에 대한 요청이 API 백엔드 풀 대신 기본 백엔드 풀로 라우팅됩니다. 이 문제를 어떻게 해결해야 합니까?

- A. Application Gateway SKU를 v2에서 v1으로 변경
- B. 각 경로 규칙에 우선순위 번호 추가
- C. `/*`가 마지막에 오도록 경로 규칙 순서를 재배열
- D. 각 경로에 대해 별도의 HTTP 리스너 생성

**정답: C**

**해설:** Application Gateway 경로 규칙은 목록 순서대로 위에서 아래로 평가됩니다 — 처음 일치하는 규칙이 적용됩니다. `/*`는 모든 경로와 일치하기 때문에 `/api/*`와 같은 더 구체적인 규칙이 평가되기 전에 모든 요청을 캡처합니다. `/*`를 마지막에 배치하도록 규칙을 재정렬하면(기본/폴백으로 사용) `/api/*` 및 `/images/*`와 같은 특정 경로가 먼저 평가됩니다. NSG 규칙과 달리 Application Gateway 경로 규칙은 우선순위 번호를 사용하지 않습니다 — 목록 순서가 평가 순서입니다.

---

### 문제 2

`/checkout`에 대한 요청이 Payments 백엔드 풀로 가고 다른 모든 요청은 기본 백엔드 풀로 가도록 Azure Application Gateway에 경로 기반 라우팅을 구성해야 합니다. Application Gateway는 경로 규칙을 어떻게 평가합니까?

- A. 각 규칙에 할당된 우선순위 번호 순서로 (낮은 번호 = 높은 우선순위)
- B. 생성 날짜 순서로 (가장 오래된 규칙이 먼저 평가됨)
- C. URL 경로 맵에 나열된 순서대로 (위에서 아래로)
- D. 규칙 이름의 알파벳 순서

**정답: C**

**해설:** Azure Application Gateway는 URL 경로 맵 규칙을 목록에 나타나는 순서대로 엄격하게 평가합니다. 첫 번째로 일치하는 경로 규칙이 적용되고 이후 규칙은 평가되지 않습니다. 이는 우선순위 번호를 사용하는 NSG 규칙과 다릅니다. Application Gateway 경로 기반 라우팅의 경우 가장 구체적인 규칙부터 덜 구체적인 규칙 순서로 정렬해야 하며, `/*`와 같은 포괄적인 패턴은 목록 맨 아래에 배치하여 기본값으로 사용해야 합니다.

---

### 문제 3

Parent-Policy라는 상위 Azure Firewall Policy가 위협 인텔리전스 모드를 "Alert"로 설정했습니다. Parent-Policy를 상속하는 Child-Policy라는 하위 Firewall Policy를 생성합니다. Child-Policy에서 사용 가능한 위협 인텔리전스 모드 옵션은 무엇입니까?

- A. Off, Alert, Alert and Deny
- B. Alert 및 Alert and Deny만
- C. Off만
- D. Alert만

**정답: B**

**해설:** Azure Firewall Policy 상속에서 하위 정책은 위협 인텔리전스 모드를 상위 정책과 같거나 더 엄격하게만 구성할 수 있습니다. 상위가 "Alert"로 설정된 경우 하위는 "Alert"(동일) 또는 "Alert and Deny"(더 엄격)로만 설정할 수 있습니다. 하위는 상위보다 덜 제한적인 "Off"로 설정할 수 없습니다. 이를 통해 정책 계층 구조의 하위 수준에서 보안 설정이 약화되지 않도록 합니다.

---

### 문제 4

Azure Firewall이 아웃바운드 인터넷 트래픽을 허용하도록 구성되어 있습니다. VM이 Microsoft의 자주 변경되는 IP 주소를 하드코딩하지 않고 Windows Update 서비스에 접근할 수 있도록 해야 합니다. 어떤 유형의 Azure Firewall 규칙을 사용해야 합니까?

- A. 서비스 태그가 있는 네트워크 규칙
- B. FQDN 태그가 있는 애플리케이션 규칙
- C. 아웃바운드 트래픽을 위한 DNAT 규칙
- D. IP 그룹이 있는 네트워크 규칙

**정답: B**

**해설:** Azure Firewall 애플리케이션 규칙은 FQDN 태그를 지원합니다. FQDN 태그는 잘 알려진 서비스와 관련된 Microsoft가 관리하는 사전 정의된 FQDN 집합을 나타냅니다. "WindowsUpdate" FQDN 태그에는 Windows Update에 필요한 모든 도메인이 포함됩니다. FQDN 태그를 사용하면 Microsoft가 관련 FQDN 변경 시 태그를 자동으로 업데이트하므로 IP 주소나 도메인 이름 목록을 수동으로 유지 관리할 필요가 없습니다. WindowsUpdate FQDN 태그로 HTTPS 트래픽을 허용하는 애플리케이션 규칙을 방화벽 정책에 생성합니다.

---

### 문제 5

VNet의 가상 머신에 0.0.0.0/0 프리픽스에 대한 시스템 경로가 있습니다. 관리자가 NVA를 통해 0.0.0.0/0을 라우팅하는 UDR(User Defined Route)을 추가합니다. 관리자는 또한 ExpressRoute를 통해 BGP로 광고된 동일한 0.0.0.0/0 프리픽스 경로를 추가합니다. 아웃바운드 인터넷 트래픽에 어떤 경로가 사용됩니까?

- A. 시스템 경로 (Azure에서 자동으로 추가됨)
- B. BGP 경로 (ExpressRoute에서)
- C. UDR (User Defined Route)
- D. 가장 구체적인 프리픽스가 있는 경로가 적용됨

**정답: C**

**해설:** 동일한 프리픽스에 여러 경로가 존재할 때의 Azure 경로 우선순위는 UDR(User Defined Route) > BGP 경로(ExpressRoute 또는 BGP가 있는 VPN에서) > 시스템 경로 순입니다. UDR은 동일한 프리픽스에 대해 BGP 학습 경로와 시스템 기본 경로를 항상 재정의합니다. 이를 통해 관리자가 BGP에서 대안 경로를 광고하더라도 트래픽을 NVA를 통해 강제로 라우팅할 수 있습니다. UDR이 없으면 BGP 경로가 시스템 경로보다 선호됩니다.

---

### 문제 6

조직에서 모든 새 Azure 구독을 자동으로 MG-Compliance라는 특정 관리 그룹 아래에 배치하려 합니다. 이 기본 관리 그룹 설정을 어디에서 구성해야 합니까?

- A. 구독의 속성 블레이드에서
- B. 테넌트 루트 그룹의 정책 할당에서
- C. Azure Portal의 관리 그룹 설정에서 (설정 > 기본 관리 그룹)
- D. 외부 ID 아래의 Azure Active Directory 관리 센터에서

**정답: C**

**해설:** 새 구독에 대한 기본 관리 그룹은 Azure Portal에서 관리 그룹 > 설정 > 기본 관리 그룹에서 구성합니다. 새 구독이 생성될 때 Azure는 기본적으로 테넌트 루트 그룹 아래에 배치합니다. 기본 관리 그룹 설정을 변경하면 모든 새 구독이 자동으로 특정 관리 그룹(예: MG-Compliance) 아래에 배치됩니다. 이를 통해 해당 그룹의 정책 및 RBAC 할당이 새 구독에 즉시 적용됩니다.

---

### 문제 7

팀에게 Azure RBAC와 Azure Policy의 차이를 설명해야 합니다. User1은 Azure 구독에서 가상 머신 생성이 차단되었지만 Contributor 역할을 보유하고 있습니다. 가장 가능성 있는 원인은 무엇입니까?

- A. User1의 Contributor 역할이 구독 범위에 상속되지 않음
- B. Deny 효과가 있는 Azure Policy가 VM 생성을 차단함
- C. Contributor 역할에는 VM 생성 권한이 포함되지 않음
- D. User1에게는 특별히 Virtual Machine Contributor 역할이 필요함

**정답: B**

**해설:** Azure RBAC는 누가 작업을 수행할 수 있는지(예: VM 생성)를 제어하고, Azure Policy는 권한에 관계없이 무엇이 허용되는지(예: VM을 특정 지역에서만 생성하거나 특정 SKU만 사용)를 제어합니다. Contributor 역할을 가진 사용자는 VM을 생성할 RBAC 권한이 있지만, Deny 효과가 있는 Azure Policy는 사용자가 충분한 권한을 가지고 있더라도 해당 작업을 차단할 수 있습니다. RBAC 권한과 Policy 효과는 독립적으로 평가됩니다. Policy Deny는 RBAC Allow를 재정의할 수 있습니다.

---

## 2) 관리 ID 및 액세스 제어

### 문제 8

동일한 Azure Storage 계정에 관리 ID를 사용하여 접근하는 5개의 Azure 웹 애플리케이션을 구성해야 합니다. 역할 할당은 웹 애플리케이션이 배포되기 전에 구성해야 합니다. 어떤 유형의 관리 ID가 적합합니까?

- A. 각 웹 앱에 대해 생성된 시스템 할당 관리 ID
- B. 생성 후 5개 앱 모두에 할당된 사용자 할당 관리 ID
- C. 인증서 기반 인증이 있는 서비스 주체
- D. Key Vault에 저장된 스토리지 계정 공유 액세스 서명 (SAS)

**정답: B**

**해설:** 사용자 할당 관리 ID는 독립적인 Azure 리소스로 생성, 역할 권한 할당 후 여러 Azure 리소스에 공유할 수 있습니다. 단일 사용자 할당 관리 ID를 생성하고, 스토리지 계정에 필요한 RBAC 역할을 할당한 다음, 5개의 웹 앱 모두에 할당하면 별도의 자격 증명 없이 일관된 접근을 보장합니다. 시스템 할당 관리 ID는 독립적으로 사전 구성할 수 없으며 리소스 간에 공유할 수 없습니다. 여러 리소스가 ID를 공유하고 배포 전 RBAC 구성이 필요한 시나리오에는 사용자 할당 관리 ID가 올바른 선택입니다.

---

### 문제 9

단일 Linux 시스템 노드 풀이 있는 AKS 클러스터가 있습니다. Windows 노드 풀을 추가해야 합니다. Standard SKU Load Balancer 요구 외에 추가적인 네트워킹 요구 사항은 무엇입니까?

- A. 클러스터가 kubenet 네트워킹을 사용해야 함
- B. 클러스터가 Azure CNI 네트워킹을 사용해야 함
- C. 클러스터에 Windows 노드를 위한 전용 서브넷이 있어야 함
- D. 클러스터가 Azure CNI Overlay 네트워킹을 사용해야 함

**정답: B**

**해설:** AKS의 Windows 노드 풀에는 Azure CNI(Container Network Interface) 네트워킹이 필요합니다. kubenet 네트워킹은 Windows 노드 풀에서 지원되지 않습니다. Azure CNI는 각 Pod에 VNet 서브넷의 IP 주소를 할당하는 반면, kubenet은 VNet IP에 NAT되는 개인 IP 주소를 사용합니다. Windows 컨테이너는 적절한 네트워크 통신을 위해 Azure CNI가 필요합니다. Windows 노드 풀을 포함하는 AKS 클러스터를 생성할 때 클러스터 생성 시 Azure CNI를 선택해야 합니다. 배포 후에는 네트워크 플러그인을 변경할 수 없습니다.

---

### 문제 10

Microsoft.Web/serverFarms에 대한 위임이 있는 Azure App Service의 서브넷이 있습니다. 동일한 서브넷에 Azure Storage 계정에 연결하기 위한 Private Endpoint를 생성하려 합니다. Private Endpoint를 배포하면 어떻게 됩니까?

- A. Private Endpoint가 App Service와 함께 성공적으로 배포됨
- B. 위임된 서브넷에는 Private Endpoint를 배포할 수 없으므로 배포 실패
- C. Private Endpoint가 배포되지만 추가 NSG 규칙이 필요함
- D. Private Endpoint를 추가하기 전에 서브넷 크기를 조정해야 함

**정답: B**

**해설:** 서비스 위임이 있는 서브넷은 위임된 서비스 유형에 전용됩니다. Private Endpoint는 위임된 서브넷에 배포할 수 없습니다. 위임된 서브넷은 위임된 특정 서비스를 위해 예약되므로 Private Endpoint를 포함한 다른 리소스 유형을 혼용하는 것이 허용되지 않습니다. 스토리지 계정의 Private Endpoint를 생성하려면 동일한 VNet 내의 위임되지 않은 다른 서브넷을 사용해야 합니다.

---

### 문제 11

SMB 접근을 위해 Azure Files가 구성된 Azure Storage 계정이 있습니다. 스토리지 계정에는 현재 Azure Files에 대한 Microsoft Entra Domain Services(Entra DS) 인증이 활성화되어 있습니다. 온-프레미스 Active Directory Domain Services(AD DS) 인증으로 전환해야 합니다. 가장 먼저 무엇을 해야 합니까?

- A. AD DS 인증을 활성화합니다 — 두 방법이 공존할 수 있음
- B. 먼저 Microsoft Entra Domain Services 인증을 비활성화한 다음 AD DS를 활성화
- C. AD DS 인증을 위해 새 스토리지 계정을 생성
- D. 스토리지 계정을 Premium FileStorage 계층으로 업그레이드

**정답: B**

**해설:** Azure Files는 한 번에 하나의 ID 기반 인증 방법만 지원합니다. Microsoft Entra Domain Services와 온-프레미스 Active Directory Domain Services 인증을 동일한 스토리지 계정에서 동시에 활성화할 수 없습니다. AD DS 인증을 활성화하기 전에 기존 Entra DS 인증을 먼저 비활성화해야 합니다. Entra DS를 비활성화한 후 온-프레미스 AD에 스토리지 계정을 가입하고 컴퓨터 계정을 등록하는 온-프레미스 AD DS 인증을 구성할 수 있습니다.

---

---

### 문제 12

조직에서 JIT(Just-In-Time) 역할 할당을 위해 Privileged Identity Management(PIM)를 구현해야 합니다. 어떤 Microsoft Entra ID 라이선스 계층이 필요합니까?

- A. Microsoft Entra ID Free
- B. Microsoft Entra ID Premium P1
- C. Microsoft Entra ID Premium P2
- D. Microsoft 365 E3

**정답: C**

**해설:** Microsoft Entra ID PIM(Privileged Identity Management)에는 Microsoft Entra ID Premium P2 라이선스가 필요합니다. PIM은 Free 또는 Premium P1 계층에서 사용할 수 없습니다. P2는 모든 P1 기능과 함께 PIM, ID 보호 위험 기반 조건부 액세스, 권한 있는 역할에 대한 액세스 검토를 포함합니다. P1 라이선스만 있는 경우 조건부 액세스를 통해 시간 제한 역할 할당을 사용할 수 있지만, 활성화 워크플로우와 감사 로그가 있는 전체 PIM 기능을 사용할 수 없습니다.

---

### 문제 13

User1은 구독 범위에서 Contributor 역할이 할당되어 있습니다. User1은 동일한 구독 내의 리소스 그룹(RG1)에 Reader 역할도 할당되어 있습니다. RG1 내의 가상 머신에 대한 User1의 유효 권한 수준은 무엇입니까?

- A. Reader 권한 (가장 제한적인 할당이 적용됨)
- B. 접근 없음 (충돌하는 역할 할당이 서로 상쇄됨)
- C. Contributor 권한 (구독 범위에서 상속됨)
- D. 가장 최근에 할당된 역할이 적용됨

**정답: C**

**해설:** Azure RBAC는 가산적 모델을 사용합니다 — 유효 권한은 적용 가능한 모든 범위의 모든 역할 할당의 합집합입니다. 구독 범위의 Contributor 역할은 모든 리소스 그룹 및 그 내부의 리소스를 포함하는 구독 내 모든 것에 Contributor 접근 권한을 부여합니다. RG1의 Reader 역할은 구독에서 상속된 Contributor를 재정의하지 않습니다. 유효 권한은 두 역할을 포함합니다(합집합 = Contributor). 표준 RBAC 역할 할당에는 "재정의" 또는 "충돌 해결"이 없습니다 — 권한은 추가만 되고 제거되지 않습니다(거부 할당은 별도로 작동합니다).

---

### 문제 14

활성 세션 중에 리프레시 토큰이 여전히 유효하더라도 사용자가 8시간마다 다시 인증하도록 요구하는 조건부 액세스 정책을 구성해야 합니다. 어떤 조건부 액세스 조건 또는 컨트롤을 구성해야 합니까?

- A. 중간 위험 임계값이 있는 로그인 위험 조건
- B. 로그인 빈도 세션 컨트롤 (8시간)
- C. "절대 지속"으로 설정된 영구 브라우저 세션 컨트롤
- D. 장치 규정 준수 조건

**정답: B**

**해설:** 조건부 액세스 로그인 빈도를 사용하면 활성 세션 중에 사용자가 재인증해야 하는 빈도를 지정할 수 있습니다. 8시간으로 설정하면 리프레시 토큰이 여전히 유효하더라도 8시간의 활동 후 사용자는 다시 자격 증명을 제공해야 합니다. 이는 보안에 민감한 시나리오에 주기적 재인증을 적용하는 데 일반적으로 사용됩니다. 영구 브라우저 세션 컨트롤은 브라우저가 재시작 후 세션 쿠키를 유지하는지 여부를 제어하지만 재인증 빈도를 제어하지 않습니다.

---

## 3) 사용자 지정 역할, 스토리지, 데이터 접근

### 문제 15

사용자가 Azure Blob Storage 컨테이너에서 데이터를 읽을 수 있도록(Blob 목록 조회 및 다운로드) 하되, 스토리지 계정 생성이나 삭제와 같은 관리 플레인 작업은 허용하지 않는 사용자 지정 RBAC 역할을 생성해야 합니다. 역할 정의의 어떤 섹션에 Blob 읽기 권한을 포함해야 합니까?

- A. Actions
- B. DataActions
- C. NotActions
- D. NotDataActions

**정답: B**

**해설:** Azure RBAC 역할 정의는 관리 플레인 권한(Actions / NotActions)과 데이터 플레인 권한(DataActions / NotDataActions)을 분리합니다. Blob 데이터 읽기(Blob 목록 조회, 콘텐츠 다운로드)는 데이터 플레인 작업으로 DataActions 섹션에 정의해야 합니다. 예: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`. 스토리지 계정 생성과 같은 관리 플레인 작업은 Actions 섹션을 사용합니다. 이를 잘못 혼용하면 데이터나 관리 플레인에 대한 접근이 없거나 과도한 접근이 발생합니다.

---

### 문제 16

Azure Storage Blob에 대한 접근을 허용하는 서비스 SAS 토큰을 생성합니다. SAS 토큰이 조직의 기업 네트워크에서만 사용될 수 있도록 제한하려 합니다. 기업 네트워크는 공용 IP 주소(203.0.113.0/24)와 내부 개인 IP 주소(10.0.0.0/24)를 모두 사용합니다. SAS 토큰에서 IP 제한을 구성할 때의 제한 사항은 무엇입니까?

- A. SAS 토큰은 범위가 아닌 단일 IP 주소만 제한 지원
- B. SAS 토큰은 공용 IP 주소만 제한할 수 있으며 개인 IP 주소는 지원 안 함
- C. SAS 토큰은 공용 및 개인 IP 범위를 모두 지원
- D. SAS 토큰의 IP 제한에는 Azure Private Link가 필요함

**정답: B**

**해설:** SAS 토큰 IP 제한은 공용 IP 주소만 지원합니다. Azure Storage는 요청의 소스 개인 IP를 확인할 수 없으므로 개인 IP 주소(10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16과 같은 RFC 1918 범위)는 SAS 토큰의 IP 제한에서 지원되지 않습니다. 기업 네트워크가 개인 IP를 사용하는 경우, 해당 개인 IP 범위의 클라이언트는 개인 IP 제한이 있는 SAS 토큰을 사용할 수 없습니다. IP 제한에는 공개적으로 라우팅 가능한 IP 주소만 포함해야 합니다. 개인 네트워크 접근의 경우 Private Endpoint를 사용하세요.

---

### 문제 17

Azure File Sync 서버 엔드포인트에서 클라우드 계층화를 활성화합니다. 계층화 정책 임계값이 충족된 후에도 작은 구성 파일(4 KiB)이 클라우드로 계층화되지 않는다고 사용자가 보고합니다. Azure File Sync 클라우드 계층화가 계층화할 수 있는 최소 파일 크기는 얼마입니까?

- A. 1 KiB
- B. 4 KiB
- C. 8 KiB
- D. 64 KiB

**정답: C**

**해설:** Azure File Sync 클라우드 계층화의 최소 파일 크기는 8 KiB입니다. 8 KiB보다 작은 파일은 계층화 정책 구성에 관계없이 클라우드로 계층화되지 않습니다. 이 최솟값은 클러스터 크기의 2배(최솟값 8 KiB)로 계산됩니다. 계층화된 파일이 NTFS 볼륨에서 재파싱 포인트로 저장되고, 재파싱 포인트 자체가 최소 하나의 클러스터를 차지하기 때문입니다. 구성 파일 및 8 KiB 미만의 매우 작은 파일은 항상 로컬에 유지됩니다.

---

### 문제 18

Cool 액세스 계층에 있는 Blob이 10일 전에 업로드되었습니다. 계층을 Archive로 변경합니다. 조기 삭제 페널티는 무엇입니까?

- A. 페널티 없음; 계층 변경은 항상 무료
- B. Cool 계층의 30일 최소 기간 중 나머지 20일에 대한 요금 청구
- C. Archive 계층의 180일 최소 기간 중 나머지 150일에 대한 요금 청구
- D. 월 스토리지 비용과 동일한 일회성 삭제 요금 지불

**정답: B**

**해설:** Cool 액세스 계층에는 30일의 최소 저장 기간이 있습니다. 30일이 경과하기 전에 Blob을 삭제하거나 다른 계층으로 이동하면 나머지 날에 대한 조기 삭제 페널티가 청구됩니다. Blob이 10일 동안 Cool에 있었고 최솟값이 30일이므로 10일 후 Archive로 이동하면 Cool 최소 기간의 나머지 20일에 대한 요금이 청구됩니다. Archive 계층은 자체 최솟값 180일이 있습니다. Archive에서 조기 삭제하면 나머지 Archive 기간에 대한 요금이 청구됩니다.

---

### 문제 19

GPv1(범용 v1) 유형의 Azure Storage 계정이 있습니다. VNet에서 스토리지 계정에 안전하게 접근하기 위해 Private Endpoint를 생성해야 합니다. Private Endpoint를 생성하기 전에 무엇을 해야 합니까?

- A. 스토리지 계정에서 계층 구조 네임스페이스 활성화
- B. 스토리지 계정을 GPv1에서 GPv2로 업그레이드
- C. 스토리지 계정 방화벽 허용 네트워크에 VNet 추가
- D. 스토리지 계정에서 일시 삭제 활성화

**정답: B**

**해설:** Azure Storage의 Private Endpoint는 스토리지 계정이 General Purpose v2(GPv2) 또는 특수화된 스토리지 유형(Blob Storage, Data Lake Storage Gen2)이어야 합니다. GPv1 스토리지 계정은 Private Endpoint를 지원하지 않습니다. Private Endpoint를 생성하기 전에 GPv1 계정을 GPv2로 업그레이드해야 합니다. GPv1에서 GPv2로의 업그레이드는 서비스 중단이나 데이터 손실 없이 수행할 수 있는 단방향 작업이지만 되돌릴 수 없습니다.

---

### 문제 20

Block Blob, Append Blob, Page Blob을 포함하는 스토리지 계정이 있습니다. 마지막 접근 후 90일이 지난 Blob을 Archive 계층으로 이동하는 계층화 규칙이 있는 수명 주기 관리 정책을 구성합니다. 어떤 Blob 유형이 이 정책에 의해 Archive 계층으로 이동되지 않습니까?

- A. Block Blob만
- B. Append Blob과 Page Blob
- C. Page Blob만
- D. 모든 Blob 유형이 이동됨

**정답: B**

**해설:** Azure Blob Storage 수명 주기 관리 계층화 규칙(Archive 계층으로 이동 포함)은 Block Blob에만 적용됩니다. Append Blob과 Page Blob은 수명 주기 관리 정책을 통한 액세스 계층 변경을 지원하지 않습니다. Append Blob은 순차 추가 작업을 위해 설계되었으며, Page Blob(VM 디스크 이미지에 사용)은 계층화할 수 없습니다. Block Blob만 수명 주기 관리 정책을 통해 Hot, Cool, Cold, Archive 계층 간에 전환할 수 있습니다.

---

### 문제 21

지역 중복 스토리지(GRS) 복제가 있는 Azure Storage 계정이 있습니다. GRS와 읽기 액세스 지역 중복 스토리지(RA-GRS)의 차이점은 무엇입니까?

- A. RA-GRS는 기본 지역에 세 개의 복사본을 저장하고, GRS는 하나만 저장함
- B. GRS는 보조 지역에 대한 읽기 접근을 제공하고, RA-GRS는 제공하지 않음
- C. RA-GRS는 보조 지역에 대한 읽기 접근을 허용하고, GRS는 허용하지 않음
- D. GRS는 기본 지역에서 영역 중복 스토리지를 지원하고, RA-GRS는 지원하지 않음

**정답: C**

**해설:** GRS와 RA-GRS 모두 재해 복구를 위해 보조 Azure 지역에 데이터를 복제합니다. 핵심 차이점은 RA-GRS(읽기 액세스 지역 중복 스토리지)가 기본 지역이 사용 가능한 경우에도 보조 지역 엔드포인트에 대한 읽기 전용 접근을 추가로 제공한다는 것입니다. GRS는 Microsoft가 기본 지역에서 보조 지역으로의 장애 조치를 시작한 후에만 보조 지역에서 읽을 수 있습니다. RA-GRS를 사용하면 애플리케이션이 부하 분산 또는 재해 대비 테스트를 위해 보조 엔드포인트에서 사전에 읽을 수 있습니다.

---

### 문제 22

규정 준수 기간 동안 7년 동안 모든 Blob 수정 및 삭제를 방지하도록 Azure Blob Storage를 구성해야 합니다. 사용자는 이 보존 기간을 재정의하거나 단축할 수 없어야 합니다. 어떤 스토리지 기능을 구성해야 합니까?

- A. 7년 보존 기간의 Blob 일시 삭제
- B. 시간 기반 보존 정책과 잠금이 활성화된 불변 Blob Storage
- C. Blob 스토리지를 위한 Azure Backup
- D. 스토리지 계정 리소스 잠금 (CanNotDelete)

**정답: B**

**해설:** Azure 불변 Blob Storage는 시간 기반 보존 정책을 사용하여 WORM(Write Once, Read Many) 보호를 제공합니다. 보존 정책이 생성되고 잠기면 관리자나 계정 소유자를 포함하여 누구도 보존 기간이 만료될 때까지 Blob을 삭제하거나 수정할 수 없습니다. 정책을 잠그면 보존 기간을 단축하는 것을 방지합니다. 이는 규정 준수 요구 사항(예: SEC 17a-4, CFTC)을 충족합니다. 일시 삭제는 복구를 허용하지만 비활성화할 수 있습니다. 리소스 잠금은 삭제를 방지하지만 쓰기 불변성을 적용하지 않습니다.

---

## 4) 거버넌스, 정책, Bicep

### 문제 23

추가 정책 할당을 생성하지 않고 정책 할당에서 특정 Azure 리소스를 영구적으로 제외해야 합니다. 제외가 원래 정책 할당에 직접 연결되도록 해야 합니다. 어떤 접근 방식을 사용해야 합니까?

- A. Audit 효과를 사용하여 리소스 범위에서 새 정책 할당 생성
- B. 리소스를 정책 할당의 notScopes 속성에 추가
- C. 특정 리소스를 대상으로 하는 Policy Exemption 리소스 생성
- D. 새 정책 정의에서 Disabled 효과 사용

**정답: B**

**해설:** Azure Policy 할당의 notScopes 속성을 사용하면 정책 평가에서 제외할 개별 리소스, 리소스 그룹 또는 구독을 지정할 수 있습니다. 이러한 제외는 추가 정책 할당이나 별도 예외 리소스 없이 기존 정책 할당에 직접 포함됩니다. 리소스를 notScopes에 추가하는 것이 특정 범위를 영구적으로 제외하는 가장 간단한 방법입니다. Policy Exemption(옵션 C)은 또한 작동하지만 별도의 예외 개체를 생성하고 관리해야 합니다.

---

### 문제 24

사용자 지정 도메인이 있는 Azure App Service 웹 앱에 무료 관리형 TLS/SSL 인증서를 사용하려 합니다. Azure App Service Managed Certificate를 사용하기로 합니다. App Service Managed Certificate에 대한 올바른 설명은 무엇입니까?

- A. Managed Certificate는 와일드카드 도메인(예: \*.contoso.com)을 지원함
- B. Managed Certificate는 Azure Key Vault에 저장해야 함
- C. Managed Certificate는 와일드카드 도메인을 지원하지 않으며 SNI 기반 SSL만 지원함
- D. Managed Certificate를 내보내어 다른 서비스에서 사용할 수 있음

**정답: C**

**해설:** Azure App Service Managed Certificate에는 몇 가지 중요한 제한 사항이 있습니다: (1) 와일드카드 인증서는 지원되지 않습니다 — 각 인증서는 특정 호스트 이름에 연결됩니다. (2) Managed Certificate는 IP 기반 SSL 바인딩이 아닌 SNI(Server Name Indication) SSL 바인딩만 지원합니다. (3) Managed Certificate는 내보낼 수 없습니다 — App Service에 의해 완전히 관리됩니다. (4) Managed Certificate는 수동 개입 없이 자동으로 갱신됩니다. 와일드카드 도메인, IP 기반 SSL 또는 인증서 재사용이 필요한 경우 Key Vault에 업로드하거나 저장된 사용자 지정 인증서를 사용해야 합니다.

---

### 문제 25

boolean 매개변수를 기반으로 Azure Storage 계정을 조건부로 배포하는 Bicep 파일을 작성합니다. Bicep에서 조건부 배포를 올바르게 구현하는 구문은 무엇입니까?

- A. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = if (deployStorage) { ... }`
- B. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = condition(deployStorage) { ... }`
- C. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = when (deployStorage == true) { ... }`
- D. `resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = [if deployStorage] { ... }`

**정답: A**

**해설:** Bicep은 조건부 리소스 배포에 `if` 키워드를 사용합니다. 구문은 `resource <이름> '<유형>@<API버전>' = if (<조건>) { <리소스 본문> }`입니다. 조건이 true이면 리소스가 배포되고, false이면 배포되지 않습니다. 반면 ARM 템플릿은 리소스 개체 내의 `"condition"` 속성을 사용합니다. Bicep의 `if` 구문은 동등한 ARM JSON 방식보다 더 깔끔하고 읽기 쉽습니다.

---

### 문제 26

스토리지 계정을 배포하는 `stgModule`이라는 모듈이 있는 Bicep 파일이 있습니다. 스토리지 모듈에는 리소스 ID를 반환하는 `storageId`라는 출력이 있습니다. 메인 Bicep 파일에서 이 출력을 어떻게 참조합니까?

- A. `storageId`
- B. `stgModule.storageId`
- C. `stgModule.outputs.storageId`
- D. `outputs.stgModule.storageId`

**정답: C**

**해설:** Bicep에서 모듈 출력에 접근하는 구문은 `<모듈이름>.outputs.<출력이름>`입니다. 모듈 이름이 `stgModule`이고 `storageId`라는 출력이 있으면 `stgModule.outputs.storageId`로 참조합니다. `.outputs.` 접근자 없이 모듈 출력에 직접 접근할 수 없습니다. 이 구문은 모듈의 출력 선언에서 정의된 모듈의 내보낸 출력 값에 접근하고 있음을 명확히 합니다.

---

### 문제 27

`environment` 매개변수를 기반으로 스토리지 계정 SKU를 조건부로 설정하기 위해 Bicep 3항 연산자를 사용하려 합니다. `environment`가 'prod'이면 'Standard_GRS'를, 그렇지 않으면 'Standard_LRS'를 사용합니다. 올바른 Bicep 표현식은 무엇입니까?

- A. `sku: (environment == 'prod') ? 'Standard_GRS' : 'Standard_LRS'`
- B. `sku: if(environment == 'prod', 'Standard_GRS', 'Standard_LRS')`
- C. `sku: environment == 'prod' select 'Standard_GRS' else 'Standard_LRS'`
- D. `sku: environment.equals('prod') ? 'Standard_GRS' : 'Standard_LRS'`

**정답: A**

**해설:** Bicep은 `조건 ? 참일때값 : 거짓일때값` 구문을 사용하는 3항 연산자를 지원합니다. 조건 주위의 괄호는 선택 사항이지만 가독성을 향상시킵니다. 표현식 `(environment == 'prod') ? 'Standard_GRS' : 'Standard_LRS'`는 environment 매개변수가 'prod'일 때 'Standard_GRS'로, 그렇지 않으면 'Standard_LRS'로 평가됩니다. 이는 조건부 표현식과 동등하며 Bicep 템플릿에서 환경별 구성에 일반적으로 사용됩니다.

---

### 문제 28

Azure 규정 준수 표준을 집합적으로 적용하는 정책 집합이 있습니다(지역 제한, 필수 태그, 허용된 VM SKU, 필수 진단 설정). 각 정책을 여러 범위에 개별적으로 할당하는 대신 관리를 단순화하려 합니다. 무엇을 사용해야 합니까?

- A. 중첩 정책 정의
- B. 정책 집합 정의 (이니셔티브)
- C. 여러 조건이 있는 관리 그룹 수준 정책
- D. 여러 규칙 조건이 있는 단일 정책

**정답: B**

**해설:** Azure Policy 이니셔티브(정책 집합 정의라고도 함)는 여러 정책 정의를 단일 단위로 그룹화하여 하나의 이니셔티브로 할당할 수 있습니다. 5개의 별도 정책을 여러 관리 그룹과 구독에 할당하는 대신, 5개의 정책을 모두 포함하는 하나의 이니셔티브를 생성하고 한 번에 이니셔티브를 할당합니다. 이를 통해 규정 준수 모니터링(이니셔티브에 대한 하나의 규정 준수 상태)과 관리(이니셔티브에서 중앙 집중식으로 정책 업데이트)가 단순화됩니다.

---

### 문제 29

Azure 구독을 구성하기 위한 관리 그룹 계층 구조를 생성하려 합니다. 테넌트 루트 관리 그룹 아래에 만들 수 있는 관리 그룹의 최대 수준은 몇 개입니까?

- A. 3개 수준
- B. 4개 수준
- C. 5개 수준
- D. 6개 수준

**정답: D**

**해설:** Azure 관리 그룹은 테넌트 루트 관리 그룹 아래에 최대 6개 수준의 관리 그룹을 지원합니다. 테넌트 루트 그룹 자체는 이 제한에 포함되지 않으며, 구독도 수준으로 계산되지 않습니다. 이는 루트 아래에 6개 수준의 관리 그룹으로 구성된 계층 구조를 가질 수 있음을 의미합니다. Microsoft는 관리 용이성을 위해 계층 구조를 단순하게(3-4 수준) 유지하도록 권장하지만, 플랫폼에서 지원하는 최대 제한은 루트 아래 6개 수준입니다.

---

### 문제 30

ARM 템플릿, 정책 할당, RBAC 역할 할당, 리소스 그룹을 포함하는 청사진에 따라 Azure 인프라를 배포해야 합니다. 이 모든 것이 버전 관리되고 하나의 단위로 함께 배포되어야 합니다. 어떤 Azure 서비스를 사용해야 합니까?

- A. Azure Policy 이니셔티브
- B. Azure Resource Manager 템플릿 사양
- C. Azure Blueprints
- D. Azure Deployment Stacks

**정답: C**

**해설:** Azure Blueprints를 사용하면 ARM 템플릿, 정책 할당, RBAC 역할 할당, 리소스 그룹을 포함한 아티팩트로 구성된 버전 관리된 청사진을 정의할 수 있습니다. 청사진은 구독에 할당되어 모든 아티팩트를 조율된 방식으로 배포합니다. ARM 템플릿과 달리 Blueprints는 청사진과 배포된 리소스(잠금) 간의 관계를 유지하고 변경 추적을 위한 버전 관리를 지원합니다. 참고: Azure Blueprints는 새 배포를 위해 Azure Deployment Stacks 및 Template Specs를 지원하는 방향으로 더 이상 사용되지 않을 예정입니다.

---

## 5) 네트워킹 심화

### 문제 31

Azure Private Link와 Azure Service Endpoint를 비교해야 합니다. 주요 차이점을 정확하게 설명하는 것은 무엇입니까?

- A. Service Endpoint는 VNet에 개인 IP 주소를 제공하고, Private Link는 공용 IP를 사용함
- B. Private Link는 VNet에 개인 IP 주소(Private Endpoint)를 제공하고, Service Endpoint는 Azure 백본을 통해 트래픽을 라우팅하지만 서비스는 공용 엔드포인트를 유지함
- C. Service Endpoint는 교차 지역 연결을 지원하고, Private Link는 지원하지 않음
- D. Private Link와 Service Endpoint는 동일한 DNS 구성을 사용함

**정답: B**

**해설:** 핵심 차이점은 IP 주소 지정입니다. Private Endpoint(Private Link)는 VNet 내에 개인 IP 주소가 있는 NIC를 생성하여 서비스를 내부 리소스처럼 표시합니다. 서비스의 공용 엔드포인트는 완전히 비활성화할 수 있습니다. Service Endpoint는 VNet 서브넷의 트래픽을 Azure 백본을 통해 Azure 서비스로 라우팅하지만, 서비스는 여전히 공용 IP 엔드포인트를 사용합니다 — 트래픽이 인터넷을 통하지 않을 뿐입니다. 최고 보안을 위해 Private Link가 공용 노출을 완전히 제거하므로 선호됩니다.

---

### 문제 32

storage1이라는 스토리지 계정에서 Azure Files에 대한 Private Endpoint를 생성하고 Private DNS 영역 통합을 활성화합니다. Azure Files Private Endpoint에 대해 어떤 Private DNS 영역이 생성됩니까?

- A. privatelink.file.core.windows.net
- B. privatelink.files.core.windows.net
- C. privatelink.storage.core.windows.net
- D. privatelink.smb.core.windows.net

**정답: A**

**해설:** Private DNS 영역 통합을 사용하여 Azure Files(`file` 하위 리소스)에 대한 Private Endpoint를 생성하면 privatelink.file.core.windows.net 영역이 자동으로 생성됩니다. storage1.file.core.windows.net을 Private Endpoint의 개인 IP 주소에 매핑하는 A 레코드가 추가됩니다. 이를 통해 VNet 내에서 접근할 때 DNS 확인이 공용 IP 대신 개인 IP를 반환합니다. 각 스토리지 하위 리소스에는 전용 영역이 있습니다: file → privatelink.file.core.windows.net, blob → privatelink.blob.core.windows.net.

---

### 문제 33

VNet1과 VNet2가 VNet 피어링으로 연결됩니다. VNet1에는 온-프레미스 연결이 있는 VPN Gateway가 있습니다. VNet2의 리소스가 VNet1의 게이트웨이를 사용하여 온-프레미스 네트워크에 도달해야 합니다. VNet1의 피어링에서 "게이트웨이 전송 허용"을 활성화합니다. VNet2의 피어링에서 추가로 무엇을 구성해야 합니까?

- A. "전달된 트래픽 허용"
- B. "원격 게이트웨이 사용"
- C. "가상 네트워크 접근 허용"
- D. "게이트웨이 전송 허용"

**정답: B**

**해설:** 게이트웨이 전송은 피어링 양쪽 모두에서 구성해야 합니다. VNet1의 피어링 설정(VNet2 방향)에서: "게이트웨이 전송 허용" 활성화 — VNet1이 게이트웨이를 공유할 수 있도록 합니다. VNet2의 피어링 설정(VNet1 방향)에서: "원격 게이트웨이 사용" 활성화 — VNet2가 VNet1의 게이트웨이를 통해 온-프레미스로 트래픽을 라우팅하도록 지시합니다. 두 설정 모두 게이트웨이 전송이 작동하려면 활성화되어야 합니다. "전달된 트래픽 허용"은 피어링된 VNet이 아닌 다른 네트워크에서 전달된 트래픽(원래 피어링된 VNet에서 시작하지 않은)이 허용되는지 여부를 제어합니다.

---

### 문제 34

Azure ExpressRoute 회로가 있습니다. Azure 가상 머신이 ExpressRoute 회로를 통해 Office 365 서비스 및 기타 Microsoft 클라우드 서비스에 직접 연결할 수 있도록 해야 합니다. 이를 지원하는 ExpressRoute 피어링 유형은 무엇입니까?

- A. 개인 피어링
- B. Microsoft 피어링
- C. 공용 피어링 (더 이상 사용되지 않음)
- D. 인터넷 피어링

**정답: B**

**해설:** ExpressRoute Microsoft 피어링은 Office 365, Dynamics 365, Azure 공용 서비스(Storage, SQL Database) 및 기타 Microsoft SaaS 서비스를 포함한 Microsoft 클라우드 서비스에 대한 연결을 제공합니다. 트래픽은 온-프레미스에서 이러한 서비스로 Microsoft 네트워크를 통해 직접 흐릅니다. ExpressRoute 개인 피어링은 Azure 개인 IP(VNet 리소스)에 대한 연결을 제공합니다. 공용 피어링은 더 이상 사용되지 않으며 Microsoft 피어링에 통합되었습니다. ExpressRoute를 통해 Office 365에 연결하려면 Microsoft 피어링이 필요합니다.

---

### 문제 35

Azure Bastion Standard SKU가 배포되어 있습니다. 브라우저 기반 클라이언트 대신 워크스테이션의 네이티브 클라이언트를 사용하는 네이티브 RDP/SSH 클라이언트 연결을 활성화하려 합니다. 네이티브 클라이언트 연결을 지원하는 Azure Bastion SKU는 무엇입니까?

- A. Azure Bastion Basic SKU
- B. Azure Bastion Standard SKU만
- C. Basic 및 Standard SKU 모두
- D. Azure Bastion Developer SKU

**정답: B**

**해설:** Azure Bastion의 네이티브 클라이언트 지원(az network bastion ssh 또는 az network bastion rdp 명령 또는 네이티브 Windows RDP 클라이언트 사용)은 Standard SKU에서만 사용할 수 있습니다. Basic SKU는 Azure 포털을 통한 브라우저 기반 RDP/SSH 세션만 제공합니다. Standard SKU에는 사용자 지정 포트 지원, 파일 전송(업로드/다운로드), 공유 가능한 링크, 피어링된 VNet의 VM에 연결하는 기능 등의 추가 기능도 포함됩니다.

---

---

---

### 문제 36

보안 및 규정 준수 감사를 위해 VNet의 VM에 대한 네트워크 트래픽 흐름을 캡처하고 분석해야 합니다. 어떤 NSG 규칙이 네트워크 트래픽을 허용하거나 거부하는지 기록하려 합니다. 어떤 Azure 서비스를 구성해야 합니까?

- A. Azure Network Watcher - Connection Monitor
- B. Azure Network Watcher - NSG 흐름 로그
- C. Azure Monitor - Network Insights
- D. Azure Firewall 진단 로그

**정답: B**

**해설:** NSG 흐름 로그는 네트워크 보안 그룹을 통해 흐르는 IP 트래픽에 대한 정보를 캡처하여 각 규칙에 의해 허용되거나 거부된 트래픽을 기록합니다. 흐름 로그 데이터에는 소스/대상 IP, 포트, 프로토콜, 적용된 NSG 규칙, 트래픽 허용 또는 거부 여부가 포함됩니다. 이 데이터는 Traffic Analytics를 사용한 분석을 위해 스토리지 계정 및/또는 Log Analytics 작업 영역으로 전송될 수 있습니다. NSG 흐름 로그는 NSG 규칙 효과의 네트워크 트래픽 감사 및 보안 규정 준수 검토에 적합한 도구입니다.

---

### 문제 37

contoso.internal이라는 Private DNS 영역이 VNet에 연결되어 있습니다. 이 VNet에 등록된 Azure VM이 개인 IP 주소를 받을 때 영역에 DNS 레코드가 자동으로 생성되도록 하려 합니다. 어떤 DNS 영역 설정을 활성화해야 합니까?

- A. 쿼리 확인
- B. 영역 전송
- C. 자동 등록 (가상 네트워크 등록)
- D. 재귀 확인

**정답: C**

**해설:** Azure Private DNS 영역은 자동 등록을 지원합니다. 자동 등록은 연결된 가상 네트워크의 VM이 개인 IP 주소를 얻을 때 DNS A 레코드를 자동으로 생성합니다. 이는 Private DNS 영역에 VNet을 연결할 때 VNet 링크에서 "자동 등록 활성화" 옵션을 선택하여 활성화됩니다. 연결된 VNet에 VM이 생성되면 호스트 이름과 개인 IP가 Private DNS 영역에 자동으로 등록됩니다. Private DNS 영역당 하나의 VNet만 자동 등록을 활성화할 수 있습니다.

---

### 문제 38

Azure Front Door가 웹 애플리케이션의 전역 진입점으로 구성되어 있습니다. 일반적인 웹 익스플로잇으로부터 보호하는 WAF(Web Application Firewall)를 활성화해야 합니다. WAF 정책이 "방지(Prevention) 모드"에서 "감지(Detection) 모드"와 다르게 하는 것은 무엇입니까?

- A. 방지 모드는 감지된 위협만 기록하고, 감지 모드는 차단함
- B. 감지 모드는 규칙과 일치하는 모든 트래픽을 차단하고, 방지 모드는 감사만 함
- C. 방지 모드는 WAF 규칙과 일치하는 요청을 차단하고, 감지 모드는 기록만 함
- D. 두 모드 모두 동일하게 동작하지만 방지 모드는 추가 경고를 전송함

**정답: C**

**해설:** Azure WAF 정책에는 두 가지 모드가 있습니다. 감지(Detection) 모드는 감지된 위협을 기록하고(모든 트래픽을 통과시키면서 위반을 기록) 방지 모드는 WAF 규칙과 일치하는 요청을 적극적으로 차단합니다. 감지 모드에서는 트래픽이 차단되지 않으며, 차단을 활성화하기 전에 영향을 받는 트래픽 패턴을 이해하는 데 사용됩니다. 방지 모드에서는 관리 규칙 집합(예: OWASP Core Rule Set) 또는 사용자 지정 규칙과 일치하는 요청이 차단되고 응답 코드(일반적으로 403)가 클라이언트에 반환됩니다. 권장 접근 방식은 감지 모드에서 시작하여 규칙을 튜닝한 다음 방지 모드로 전환하는 것입니다.

---

## 6) 컴퓨팅 및 컨테이너

### 문제 39

myRegistry라는 Azure Container Registry(ACR)가 있습니다. Azure Kubernetes Service(AKS) 클러스터가 자격 증명을 저장하지 않고 myRegistry에서 이미지를 가져올 수 있도록 해야 합니다. 권장 접근 방식은 무엇입니까?

- A. ACR 관리자 사용자를 활성화하고 자격 증명을 Kubernetes 비밀에 저장
- B. AKS 클러스터의 관리 ID에 ACR의 AcrPull 역할 할당
- C. ACR을 공개적으로 접근 가능하게 하고 모든 IP 주소 허용
- D. ACR에 대한 SAS 토큰을 생성하고 Kubernetes 풀 비밀 생성

**정답: B**

**해설:** AKS가 ACR에서 가져올 때 권장 접근 방식은 AKS 클러스터의 kubelet 관리 ID에 ACR 리소스의 AcrPull 역할을 할당하는 것입니다. 이는 자격 증명을 저장하지 않고 관리 ID 인증을 사용합니다. ACR 관리자 사용자(옵션 A) 사용은 수동으로 교체해야 하는 자격 증명이 있는 Kubernetes 비밀을 생성해야 합니다. 관리 ID를 사용하면 더 안전하고, 자격 증명 교체를 자동으로 처리하며, 구성에 비밀이 없다는 원칙을 따릅니다. `az aks update --attach-acr`을 사용하여 이를 자동으로 구성할 수도 있습니다.

---

### 문제 40

컨테이너화된 워크로드 배포를 위해 Azure Container Apps, Azure Container Instances(ACI), Azure Kubernetes Service(AKS) 중에서 선택하고 있습니다. 어떤 시나리오가 Azure Container Instances에 가장 적합합니까?

- A. 영구 상태와 고가용성이 있는 복잡한 마이크로서비스 애플리케이션 실행
- B. 온디맨드로 시작하고 완료 시 종료되는 배치 처리 작업 실행
- C. 자동 스케일링이 있는 전 세계적으로 분산된 웹 애플리케이션 호스팅
- D. Active Directory 통합이 필요한 Windows 컨테이너 실행

**정답: B**

**해설:** Azure Container Instances(ACI)는 배치 처리 작업, 빌드 프로세스, CI/CD 러너, 이벤트 기반 작업과 같은 온디맨드 단기 컨테이너 워크로드에 가장 적합합니다. ACI는 초당 청구 및 클러스터 관리 오버헤드 없이 빠른 시작 시간을 제공합니다. 영구 상태의 복잡한 마이크로서비스에는 AKS가 더 적합합니다. 자동 스케일링이 있는 서버리스 이벤트 기반 애플리케이션에는 Azure Container Apps가 설계되었습니다. ACI는 인프라 관리 없이 컨테이너를 실행하는 가장 간단한 옵션으로, 배치 및 작업 워크로드에 이상적입니다.

---

### 문제 41

웹 애플리케이션을 위한 VMSS(Virtual Machine Scale Set)가 있습니다. VMSS는 기본 인스턴스 수가 3인 자동 크기 조정 프로필을 사용합니다. 평일 오전 8시부터 오후 6시까지 VMSS가 10개 인스턴스로 확장되고, 다른 시간에는 3개 인스턴스로 돌아오도록 해야 합니다. 업무 시간 확장에 어떤 자동 크기 조정 프로필 유형을 구성해야 합니까?

- A. 메트릭 기반 규칙이 있는 기본 프로필
- B. 특정 시간 범위에 대한 되풀이(일정 기반) 프로필
- C. 특정 날짜에 대한 고정 날짜 프로필
- D. 사용자 지정 메트릭 경고 프로필

**정답: B**

**해설:** VMSS 자동 크기 조정은 세 가지 프로필 유형을 지원합니다: (1) 기본 프로필 — 다른 프로필이 일치하지 않을 때 적용되며, 메트릭 기반 규칙 사용; (2) 되풀이 프로필 — 되풀이 일정(예: 매 평일 8:00-18:00)에 적용되며, 기본값을 재정의; (3) 고정 날짜 프로필 — 비반복적인 특정 날짜/시간 범위에 적용됩니다. 평일에 정기적으로 반복되는 업무 시간 확장의 경우, 요일과 시간 창을 지정하고 원하는 인스턴스 수인 10개를 설정하는 되풀이 프로필을 구성합니다. 오프 아워의 경우 기본 프로필을 3개로 유지합니다.

---

### 문제 42

중단을 허용하는 단기 개발 및 테스트 워크로드에 비용 효율적인 VM을 배포해야 합니다. 어떤 VM 가격 옵션을 사용해야 합니까?

- A. 예약 VM 인스턴스 (1년 약정)
- B. Azure Spot VM
- C. 버스트 가능 VM (B 시리즈)
- D. Dedicated Host

**정답: B**

**해설:** Azure Spot VM은 미사용 Azure 용량에 크게 할인된 가격(종량제 대비 최대 90% 절감)으로 접근할 수 있습니다. Spot VM은 다른 워크로드를 위한 용량이 필요하거나 현물 가격이 최대 가격을 초과할 때 Azure에 의해 제거될 수 있습니다. 개발/테스트 환경, 배치 처리, 상태 비저장 애플리케이션과 같은 장애 허용, 중단 가능한 워크로드에 이상적입니다. 예약 인스턴스는 1-3년 약정이 필요합니다. 버스트 가능 VM(B 시리즈)은 CPU 변동이 있는 워크로드를 위한 것이지만 중단 가능하지 않습니다.

---

### 문제 43

상태 비저장 HTTP API 호스팅을 위해 Azure Container Apps를 평가하고 있습니다. Azure Container Apps 스케일링에 대한 올바른 설명은 무엇입니까?

- A. Container Apps는 트래픽이 없을 때 비용이 없어지도록 인스턴스를 0개로 줄일 수 있음
- B. Container Apps는 항상 최소 하나의 실행 중인 인스턴스가 필요함
- C. Container Apps는 CPU 및 메모리 메트릭에만 기반하여 확장됨
- D. Container Apps는 런타임을 위한 전용 AKS 클러스터가 필요함

**정답: A**

**해설:** Azure Container Apps는 0개 인스턴스로 축소를 지원합니다 — 들어오는 트래픽이 없으면 Container Apps는 복제본 수를 0개로 줄이고 컴퓨팅 비용이 발생하지 않습니다. 트래픽이 도착하면 Container Apps가 자동으로 다시 확장됩니다. Container Apps는 내부적으로 KEDA(Kubernetes 기반 이벤트 기반 자동 스케일링)를 사용하며 HTTP 트래픽, Azure Queue 길이, 사용자 지정 메트릭 등을 기반으로 스케일링을 지원합니다. 이 0으로 축소 기능은 Container Apps를 변동 또는 예측 불가능한 트래픽 패턴이 있는 API 및 서비스에 특히 비용 효율적으로 만듭니다.

---

### 문제 44

프로덕션, 스테이징, QA라는 세 개의 배포 슬롯이 있는 Azure App Service가 있습니다. 세 개의 배포 슬롯을 지원하는 데 필요한 최소 App Service 플랜 계층은 무엇입니까?

- A. Basic (B1)
- B. Standard (S1)
- C. Premium (P1v2 또는 P2v3)
- D. Free (F1)

**정답: B**

**해설:** 사용 가능한 배포 슬롯 수는 App Service 플랜 계층에 따라 다릅니다. Basic 계층: 배포 슬롯 없음. Standard 계층: 최대 5개 배포 슬롯. Premium 계층: 최대 20개 배포 슬롯. 세 개의 배포 슬롯(프로덕션 + 스테이징 + QA)의 경우 Standard 계층(S1)이 최대 5개의 슬롯을 지원하므로 충분합니다. Standard S1은 최대 5개의 배포 슬롯을 지원합니다. 5개를 초과하는 슬롯이나 추가 기능이 필요한 경우 Premium이 필요합니다.

---

## 7) 모니터링, DR, 보안

### 문제 45

Recovery Services vault에 VM을 보호하는 Azure Backup이 있습니다. 테스트 목적으로 VM을 다른 Azure 지역으로 복원해야 합니다. vault에는 지역 중복 스토리지(GRS) 복제가 구성되어 있습니다. 교차 지역 복원을 지원하려면 vault에서 무엇을 활성화해야 합니까?

- A. vault의 일시 삭제
- B. vault의 교차 지역 복원(Cross Region Restore) 기능
- C. vault의 영역 중복 스토리지
- D. 향상된 복원 정책

**정답: B**

**해설:** 교차 지역 복원(CRR)은 Recovery Services vault에서 명시적으로 활성화해야 하는 기능입니다. GRS vault에서 활성화되면 테스트 또는 DR 시나리오를 위해 보조 페어링 지역에 백업 데이터를 복원할 수 있습니다. CRR이 활성화되지 않으면 GRS vault은 Microsoft의 자동 장애 조치를 위해서만 보조 지역을 사용합니다 — 보조 지역에 수동으로 복원할 수 없습니다. CRR은 GRS 복제를 사용하는 vault의 Azure VM 및 Azure VM의 SQL Server/SAP HANA에서 사용 가능합니다.

---

### 문제 46

가상 머신을 복제하도록 Azure Site Recovery를 구성합니다. 복구 지점이 생성되는 빈도와 보존 기간을 제어하려 합니다. 이러한 설정을 어디에서 구성해야 합니까?

- A. Recovery Services vault의 백업 정책에서
- B. 복제된 항목과 연결된 복제 정책에서
- C. VM의 Azure Monitor 진단 설정에서
- D. Recovery Plan 구성에서

**정답: B**

**해설:** Azure Site Recovery 복제 정책은 충돌 일관성 및 앱 일관성 복구 지점 스냅샷 빈도와 복구 지점의 보존 기간을 정의합니다. 이는 Azure Backup 정책과 별개입니다. 복제 정책은 Recovery Services vault에서 생성되고 복제 설정 중 보호된 항목과 연결됩니다. 충돌 일관성 복구 지점 빈도(5분마다), 앱 일관성 스냅샷 빈도(매시간), 복구 지점 보존(대부분의 시나리오에서 최대 72시간) 등의 설정을 구성합니다.

---

### 문제 47

Azure에서 조직의 전반적인 보안 상태를 이해하고 Azure Defender for Cloud의 보안 권장 사항을 확인하려 합니다. Defender for Cloud의 어떤 메트릭이 보안 상태의 집계 측정값을 제공합니까?

- A. 규정 준수 점수
- B. 보안 점수
- C. 위험 점수
- D. 위협 점수

**정답: B**

**해설:** Microsoft Defender for Cloud는 보안 점수를 사용하여 전반적인 보안 상태를 측정합니다. 구현된 보안 컨트롤 및 권장 사항 대 아직 보류 중인 것의 비율을 기반으로 백분율을 계산합니다. 각 보안 컨트롤에는 최대 점수 값이 있습니다. 컨트롤의 모든 권장 사항을 구현하면 해당 컨트롤의 전체 점수를 얻습니다. 더 높은 보안 점수는 더 안전한 상태를 나타냅니다. 보안 점수는 Defender for Cloud 범위에 포함된 모든 구독에서 계산됩니다.

---

### 문제 48

웹 애플리케이션에 대한 Azure Monitor Application Insights가 대량의 원격 분석을 생성합니다. 분석을 위한 대표적인 데이터를 유지하면서 비용을 절감하기 위해 샘플링을 구성합니다. 어떤 샘플링 유형이 원격 분석 볼륨에 따라 샘플링 비율을 자동으로 조정합니까?

- A. 고정 비율 샘플링
- B. 수집 샘플링
- C. 적응형 샘플링
- D. 수동 샘플링

**정답: C**

**해설:** Application Insights의 적응형 샘플링은 원격 분석 볼륨을 목표 데이터 속도 내에서 유지하기 위해 샘플링 백분율을 자동으로 조정합니다. 생성되는 원격 분석 볼륨을 모니터링하고 볼륨이 높을 때 샘플링 비율을 줄이고, 볼륨이 낮을 때 증가시킵니다. 이는 Application Insights 리소스에 대한 대략적으로 일정한 원격 분석 처리량을 유지합니다. 고정 비율 샘플링은 일정한 샘플링 백분율을 적용합니다. 수집 샘플링은 Application Insights에 도달한 후 데이터를 삭제합니다(낭비적). 적응형 샘플링은 ASP.NET SDK의 기본값입니다.

---

### 문제 49

단일 서비스에서 Azure 비용 절감, 보안 개선 및 리소스 사용 최적화에 대한 권장 사항을 여러 구독에 걸쳐 받고 싶습니다. 어떤 Azure 서비스가 이러한 다중 범주 권장 사항을 제공합니까?

- A. Azure Cost Management
- B. Microsoft Defender for Cloud
- C. Azure Advisor
- D. Azure Monitor

**정답: C**

**해설:** Azure Advisor는 다섯 가지 범주에 걸쳐 개인화된 권장 사항을 제공합니다: 비용(지출 절감), 보안(보안 상태 개선), 신뢰성(가용성 보장), 운영 우수성(모범 사례), 성능(속도/응답성 향상). Advisor는 리소스 구성 및 사용량을 분석하고 실행 가능한 권장 사항을 제공합니다. Azure Cost Management는 비용 분석 및 예산 책정에 중점을 둡니다. Defender for Cloud는 보안에 중점을 둡니다. Azure Monitor는 모니터링 및 알림에 중점을 둡니다. Advisor는 포괄적인 다중 도메인 권장 사항 서비스입니다.

---

### 문제 50

Azure VM 및 서비스에서 중앙 집중식 데이터 수집 파이프라인으로 성능 메트릭과 로그를 수집해야 합니다. Log Analytics Agent(MMA)와 Diagnostics Extension을 모두 대체하는 최신 에이전트 기반 데이터 수집 방식을 사용하려 합니다. 어떤 Azure Monitor 기능을 구성해야 합니까?

- A. Log Analytics Agent (MMA/OMS 에이전트)
- B. Azure Diagnostics Extension (WAD/LAD)
- C. 데이터 수집 규칙(DCR)이 있는 Azure Monitor Agent(AMA)
- D. OMS Gateway

**정답: C**

**해설:** Azure Monitor Agent(AMA)는 레거시 Log Analytics Agent(MMA/OMS) 및 Azure Diagnostics Extension을 대체하는 최신 통합 에이전트입니다. AMA는 데이터 수집 규칙(DCR)을 사용하여 수집할 데이터, 수집 원본 및 전송 위치를 정의합니다. DCR은 여러 VM 및 서비스에 걸쳐 데이터 수집을 위한 중앙 집중식 버전 관리 구성을 제공합니다. AMA는 Windows 및 Linux를 지원하고, 향상된 보안(관리 ID), 향상된 성능, 멀티 홈(여러 대상으로 데이터 전송)을 제공합니다. Microsoft는 레거시 에이전트를 AMA로 대체하기 위해 단계적으로 지원 중단하고 있습니다.

---

_AZ-104 강의 자료를 기반으로 생성된 문서입니다. 최종 업데이트: 2026-05-24_
