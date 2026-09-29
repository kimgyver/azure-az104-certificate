# AZ-104 예상 문제 (세트 13) — 한국어

---

## 1) 거버넌스, 정책, 리소스 관리

### 문제 1

Azure Policy 할당이 **modify** 효과를 사용해 Storage Account에 필수 태그를 자동으로 추가합니다. 할당 후 여러 기존 Storage Account가 비준수 상태로 평가됩니다. 기존의 비준수 Storage Account에 태그를 적용하려면 무엇을 구성해야 합니까?

- A. 정책 효과를 deployIfNotExists로 변경한다
- B. 정책 할당에 대한 수정 작업(remediation task)을 만든다
- C. 더 넓은 범위에 정책을 재할당한다
- D. 비준수 Storage Account를 삭제하고 다시 만든다

**정답: B**

**해설:** modify 효과는 새로 만들거나 업데이트되는 리소스에는 자동으로 태그를 추가하지만, 기존의 비준수 리소스에는 소급 적용되지 않습니다. 기존 리소스를 업데이트하려면 정책 할당에 대한 **수정 작업(remediation task)** 을 별도로 만들어야 합니다. Azure는 할당의 관리 ID를 사용해 모든 비준수 Storage Account에 필수 태그를 적용합니다.

---

### 문제 2

각 가상 머신에 특정 확장 리소스(extension)가 있는지 확인하는 Azure Policy 정의를 만들고 있습니다. 확장이 없으면 VM을 비준수로 표시하되, 배포를 차단해서는 안 됩니다. 어떤 정책 효과를 사용해야 합니까?

- A. Deny
- B. Audit
- C. AuditIfNotExists
- D. DeployIfNotExists

**정답: C**

**해설:** **AuditIfNotExists**는 기본 리소스(VM)에 대해 관련 리소스(확장)가 존재하는지 평가합니다. 확장이 없으면 VM이 비준수로 표시됩니다. 이 효과는 배포를 차단하거나 리소스를 수정하지 않고, 위반 사항만 표시합니다. Deny는 배포를 차단하고, DeployIfNotExists는 확장을 자동으로 배포합니다.

---

### 문제 3

RG1이라는 리소스 그룹에서 리소스가 실수로 삭제되는 것을 방지해야 합니다. 또한 MG1이라는 관리 그룹에도 유사한 보호를 적용해야 합니다. 어느 설명이 맞습니까?

- A. MG1에 CanNotDelete 잠금을 적용하면 RG1에 상속된다
- B. RG1에는 CanNotDelete 잠금을 적용할 수 있지만 MG1에는 적용할 수 없다
- C. MG1에는 CanNotDelete 잠금을 적용할 수 있지만 RG1에는 적용할 수 없다
- D. 리소스 그룹에는 리소스 잠금을 적용할 수 없으므로 Azure Policy를 사용해야 한다

**정답: B**

**해설:** Azure 리소스 잠금은 구독, 리소스 그룹 또는 리소스 수준에만 적용할 수 있습니다. 관리 그룹은 명시적으로 잠금 범위에서 제외됩니다. 잠금이 관리 그룹에서 구독으로 상속되지도 않습니다. MG1 아래의 리소스를 보호하려면 각 구독 또는 리소스 그룹 수준에서 별도로 잠금을 적용해야 합니다.

---

### 문제 4

리소스 그룹 RG1에 **ReadOnly** 잠금이 적용되어 있습니다. RG1에 있는 Azure Automation 계정의 런북이 "scope locked" 오류로 실패합니다. 런북이 실행될 수 있도록 하면서도 RG1 리소스의 실수 삭제를 방지해야 합니다. 어떻게 해야 합니까?

- A. ReadOnly 잠금을 제거하고 audit 효과의 Azure Policy를 추가한다
- B. ReadOnly 잠금을 Delete 잠금으로 변경한다
- C. Automation 계정을 다른 리소스 그룹으로 이동한다
- D. Automation 계정의 관리 ID에 RG1에 대한 Owner 역할을 부여한다

**정답: B**

**해설:** ReadOnly 잠금은 모든 쓰기 작업을 차단하며, Azure Automation이 작업을 시작할 때 내부적으로 수행하는 상태 변경도 포함됩니다. **Delete 잠금**은 리소스 삭제만 방지하고 읽기 및 쓰기 작업은 허용하므로 런북이 정상적으로 실행됩니다. 잠금 유형을 ReadOnly에서 Delete로 변경하면 두 요구 사항을 모두 충족합니다.

---

### 문제 5

여러 구독의 비용을 모니터링하기 위해 관리 그룹 범위에서 Azure 예산을 만들었습니다. 임계값 초과 시 자동화된 워크플로를 트리거하는 Action Group을 구성하려 합니다. 어떤 결과를 예상해야 합니까?

- A. Action Group은 구독 범위와 동일하게 관리 그룹 범위에서 완전히 지원된다
- B. Action Group은 관리 그룹 범위에서 이메일 및 웹훅에 한해 지원된다
- C. 관리 그룹 범위 예산에는 Action Group을 구성할 수 없다
- D. 관리 그룹 예산은 어떤 알림도 보낼 수 없다

**정답: C**

**해설:** 관리 그룹 범위의 Azure 비용 관리 예산은 이메일 알림 및 임계값 경고를 지원하지만 **Action Group은 지원하지 않습니다**. Action Group(Logic Apps, Azure Functions, 웹훅 등 트리거 가능)은 구독 또는 리소스 그룹 범위의 예산에서만 사용할 수 있습니다. 여러 구독에서 자동 대응이 필요하다면 각 구독에 별도의 예산과 Action Group을 만들어야 합니다.

---

### 문제 6

User1에게 관리 그룹(MG1)에서 **Reader** 역할이 할당되었고, 해당 관리 그룹 내 리소스 그룹(RG1)에서 **Contributor** 역할이 할당되어 있습니다. RG1 내 리소스에 대한 User1의 유효 권한은 무엇입니까?

- A. Reader — 관리 그룹 범위가 리소스 그룹 범위보다 우선되기 때문
- B. Contributor — Azure RBAC는 누적 모델을 사용하며, Contributor에는 이미 모든 Reader 권한이 포함되어 있기 때문
- C. 관리 작업에는 Reader, 데이터 작업에는 Contributor가 혼합 적용된다
- D. 역할 충돌로 인해 액세스가 거부된다

**정답: B**

**해설:** Azure RBAC는 **누적(additive) 모델**을 사용합니다. 모든 역할 할당의 권한이 합산됩니다. User1은 MG1에서 상속된 Reader 권한과 RG1에서 직접 할당된 Contributor 권한을 모두 가집니다. Contributor에는 모든 Reader 권한과 추가 쓰기 기능이 포함되어 있으므로 RG1에서의 유효 권한은 Contributor입니다.

---

### 문제 7

User1이 리소스 그룹 RG1에 **CanNotDelete** 잠금을 만들 수 있도록 해야 합니다. User1은 현재 RG1에 **Contributor** 역할을 가지고 있습니다. 필요한 최소 추가 역할은 무엇입니까?

- A. 구독 범위에서 Virtual Machine Contributor 역할 할당
- B. RG1 범위에서 Owner 역할 할당
- C. RG1 범위에서 User Access Administrator 역할 할당
- D. 추가 역할 불필요 — Contributor가 잠금을 만들 수 있다

**정답: C**

**해설:** 관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/locks/*` 권한이 필요합니다. 기본 제공 역할 중 이 권한을 포함하는 것은 **Owner**와 **User Access Administrator**뿐입니다. Contributor에는 이 권한이 없습니다. **User Access Administrator**를 RG1 범위에서 할당하는 것이 최소 권한 원칙을 적용한 올바른 방법입니다.

---

## 2) ID, Entra ID 및 RBAC

### 문제 8

Microsoft Entra 그룹을 만들어 관리자 역할을 할당하고, 사용자의 **department** 속성을 기반으로 구성원을 자동으로 관리하려 합니다. 이것이 가능합니까?

- A. 가능 — 동적 사용자 구성원 자격이 있는 보안 그룹을 만들고 역할 할당을 활성화한다
- B. 가능 — 동적 사용자 구성원 자격이 있는 Microsoft 365 그룹을 만들고 역할 할당을 활성화한다
- C. 불가능 — 역할 할당 가능 그룹은 반드시 Assigned 구성원 자격 유형을 사용해야 한다
- D. 불가능 — Microsoft Entra 역할은 개별 사용자에게만 할당할 수 있다

**정답: C**

**해설:** Microsoft Entra 관리자 역할을 할당할 수 있는 그룹(`isAssignableToRole = true`)은 반드시 **Assigned** 구성원 자격 유형을 사용해야 합니다. 동적 구성원 자격은 역할 할당 가능 그룹에 명시적으로 금지됩니다. 예를 들어 department 속성이 잘못 동기화되면 의도치 않게 관리자 권한이 부여될 수 있는 보안 위험 때문입니다.

---

### 문제 9

외부 공동 작업 설정을 구성해 **User Administrator** 또는 **Guest Inviter** 역할을 가진 사용자만 외부 게스트 사용자를 초대할 수 있도록 해야 합니다. 어떤 게스트 초대 설정을 선택해야 합니까?

- A. 게스트를 포함한 모든 사용자가 초대할 수 있음
- B. 구성원 사용자 및 특정 관리자 역할에 할당된 사용자가 게스트를 초대할 수 있음
- C. 특정 관리자 역할에 할당된 사용자만 게스트를 초대할 수 있음
- D. 아무도 게스트를 초대할 수 없음

**정답: C**

**해설:** "특정 관리자 역할에 할당된 사용자만 게스트를 초대할 수 있음"을 선택하면 초대 권한이 User Administrator 또는 Guest Inviter 같은 지정된 관리자 역할을 보유한 사용자로만 제한됩니다. 옵션 B는 일반 구성원 사용자도 초대를 보낼 수 있어 요구 사항보다 더 넓은 범위입니다.

---

### 문제 10

동료가 Microsoft Entra ID에서 새 사용자 계정 만들기, 사용자 암호 재설정, 사용자 라이선스 관리 세 가지 작업을 수행해야 합니다. 최소 권한 원칙에 따라 어떤 역할을 할당해야 합니까?

- A. Helpdesk Administrator
- B. License Administrator
- C. User Administrator
- D. Global Administrator

**정답: C**

**해설:** **User Administrator**는 세 가지 작업(사용자 만들기, 암호 재설정, 라이선스 관리)을 모두 수행하는 최소 역할입니다. Helpdesk Administrator는 비관리자의 암호 재설정만 가능합니다. License Administrator는 라이선스만 관리합니다. Global Administrator는 모든 권한을 가지지만 최소 권한 원칙에 위배됩니다.

---

### 문제 11

Microsoft Entra ID에서 사용자를 대량 만들기 위해 CSV 파일을 준비하고 있습니다. 대량 만들기 CSV 템플릿에서 **필수** 열 4개는 무엇입니까?

- A. Name, 사용자 계정 이름, 초기 암호, Department
- B. Name, 사용자 계정 이름, 초기 암호, 로그인 차단
- C. Name, 사용자 계정 이름, 표시 이름, 초기 암호
- D. Name, 사용자 계정 이름, 초기 암호, 사용 위치

**정답: B**

**해설:** Microsoft의 대량 사용자 만들기 템플릿에서 필수 열은 정확히 4개입니다: **Name**(표시 이름 역할), **사용자 계정 이름**(User principal name, 로그인 ID), **초기 암호**(Initial password), **로그인 차단**(Block sign in, Yes/No). Department, 표시 이름, 사용 위치는 모두 선택 사항입니다.

---

### 문제 12

가상 머신에 대한 모든 권한을 부여하되 삭제 권한만 제외하는 Azure RBAC 사용자 지정 역할을 만들어야 합니다. 역할 정의에서 어떤 속성 조합을 구성해야 합니까?

- A. Actions에 `Microsoft.Compute/virtualMachines/*`만 사용하고, 삭제에 대한 Deny 할당을 사용한다
- B. Actions에 `Microsoft.Compute/virtualMachines/*`, NotActions에 `Microsoft.Compute/virtualMachines/delete`를 구성한다
- C. 삭제를 제외한 모든 VM 작업을 Actions에 개별적으로 나열한다
- D. DataActions에 `Microsoft.Compute/virtualMachines/*`, NotDataActions에 `Microsoft.Compute/virtualMachines/delete`를 구성한다

**정답: B**

**해설:** 사용자 지정 역할 정의에서 `Actions`는 허용할 작업을 정의하고, `NotActions`는 해당 역할에서 특정 작업을 빼냅니다. Actions를 `Microsoft.Compute/virtualMachines/*`로 설정하고 NotActions를 `Microsoft.Compute/virtualMachines/delete`로 설정하면 삭제를 제외한 모든 VM 작업이 허용됩니다. Deny 할당은 역할 정의가 아닌 별도 메커니즘입니다.

---

### 문제 13

User1에게 **container1**이라는 컨테이너의 Blob에 대한 읽기 액세스만 부여해야 합니다. 동일한 Storage Account의 다른 컨테이너에는 액세스 없어야 합니다. **Storage Blob Data Reader** 역할을 어느 범위에서 할당해야 합니까?

- A. Storage Account 범위에서
- B. 리소스 그룹 범위에서
- C. container1(컨테이너) 범위에서
- D. 구독 범위에서

**정답: C**

**해설:** Azure Storage는 Storage Account, 컨테이너, Blob 범위에서 역할 할당을 지원합니다. **컨테이너 범위**에서 Storage Blob Data Reader를 할당하면 해당 컨테이너 내의 Blob에만 액세스가 제한됩니다. Storage Account 범위에서 할당하면 모든 컨테이너에 대한 읽기 액세스가 부여되어 최소 권한 원칙에 위배됩니다.

---

### 문제 14

User1에게 Storage Account 범위에서 **Storage Blob Data Contributor** 역할이 할당되었지만 Azure Portal에서 Blob 컨테이너를 볼 수 없다고 합니다. 최소 권한을 유지하면서 이 문제를 해결하려면 어떻게 해야 합니까?

- A. 컨테이너 범위에서 Storage Blob Data Reader 역할을 할당한다
- B. Storage Account 범위에서 Reader 역할을 할당한다
- C. Storage Account 범위에서 Contributor 역할을 할당한다
- D. User1에게 Azure Portal 접근 권한을 직접 부여한다

**정답: B**

**해설:** Storage Blob Data Contributor는 **데이터 플레인** 역할로, Blob 데이터 읽기 및 쓰기는 허용하지만 Azure Resource Manager의 Storage Account 관리 리소스에 대한 액세스는 제공하지 않습니다. Azure Portal은 컨테이너를 표시하기 위해 ARM 수준의 액세스가 필요합니다. **Reader**(ARM) 역할을 Storage Account 범위에서 할당하면 User1이 컨테이너를 탐색할 수 있게 됩니다.

---

## 3) 네트워킹

### 문제 15

VNet1에 있는 VM1에 인터넷에서 RDP로 접근이 안 됩니다. 특정 NSG 규칙이 RDP 트래픽을 허용 또는 거부하는지 빠르게 확인해야 합니다. 어떤 도구를 사용해야 합니까?

- A. Azure Network Watcher의 NSG 흐름 로그
- B. Azure Network Watcher의 연결 문제 해결
- C. Azure Monitor의 트래픽 분석
- D. Azure Network Watcher의 IP 흐름 확인

**정답: D**

**해설:** **IP 흐름 확인**은 특정 트래픽 흐름(소스 IP, 대상 IP, 포트, 프로토콜)을 모든 NSG 규칙에 대해 시뮬레이션하고, 트래픽 허용 또는 거부 여부와 해당 NSG 규칙 이름을 즉시 반환합니다. NSG 흐름 로그는 과거 트래픽을 기록하지만 로그 분석이 필요합니다. 연결 문제 해결은 종단 간 연결을 테스트하지만 특정 규칙을 지정하지 않습니다. 트래픽 분석은 장기 트렌드 분석용입니다.

---

### 문제 16

NVA(네트워크 가상 어플라이언스)가 배포된 VNet이 있습니다. NVA를 가리키는 UDR이 포함된 경로 테이블을 만들어 서브넷에 연결했습니다. BGP를 사용하는 VPN 게이트웨이가 온-프레미스에서 경로를 학습하고 있으며, BGP에서 학습한 경로가 서브넷에 나타나 일부 접두사에서 UDR을 재정의하고 있습니다. 온-프레미스 BGP 경로가 서브넷에 자동으로 전파되지 않도록 하려면 경로 테이블에서 무엇을 구성해야 합니까?

- A. 경로 테이블에서 BGP 경로에 대한 거부 규칙을 추가한다
- B. 경로 테이블에서 BGP 경로 전파를 비활성화한다
- C. VPN 게이트웨이를 정적 라우팅으로 변경한다
- D. VPN 게이트웨이에서 BGP 구성을 제거한다

**정답: B**

**해설:** 경로 테이블에는 **BGP 경로 전파 비활성화** 설정이 있습니다. 활성화되면 게이트웨이가 BGP에서 학습한 온-프레미스 경로를 연결된 서브넷에 전파하지 않습니다. 서브넷은 그 후 UDR과 시스템 경로만 사용합니다. 이것은 모든 트래픽을 NVA를 통해 강제로 라우팅하고 BGP의 간섭을 방지하는 표준 접근 방식입니다.

---

### 문제 17

East US에 있는 VNet1에 VM1, VM2, VM3이 있습니다. East US에 ASG-Web이라는 애플리케이션 보안 그룹을 만들고 VM1, VM2를 구성원으로 추가했습니다. ASG-Web에서 ASG-DB로 TCP 443 인바운드를 허용하는 NSG 규칙을 만들려고 합니다. ASG-DB의 구성원이 될 수 있는 가상 머신은 무엇입니까?

- A. VM3만 — VM1과 VM2는 이미 ASG-Web에 있기 때문
- B. East US 지역의 모든 가상 머신
- C. VNet1의 가상 머신만
- D. Azure 구독의 모든 가상 머신

**정답: C**

**해설:** ASG에 할당된 모든 네트워크 인터페이스는 해당 그룹에 처음 할당된 인터페이스와 **동일한 가상 네트워크**에 있어야 합니다. ASG-Web이 VNet1의 인터페이스를 포함하므로 ASG-DB도 VNet1의 인터페이스만 포함할 수 있습니다. 제약은 VNet 기반이며, 지역이나 구독 기반이 아닙니다. 또한 VM은 여러 ASG에 동시에 속할 수 있으므로 VM1과 VM2도 ASG-DB의 구성원이 될 수 있습니다.

---

### 문제 18

Application Gateway를 배포해 기본 도메인이 `webapp1.azurewebsites.net`인 Azure App Service 백엔드로 트래픽을 라우팅합니다. 사용자는 사용자 지정 도메인을 통해 앱에 액세스합니다. 테스트 시 App Service가 들어오는 호스트 헤더를 인식하지 못해 오류가 발생합니다. Application Gateway 백엔드 설정에서 무엇을 구성해야 합니까?

- A. 사용자 지정 도메인 이름으로 백엔드 풀을 구성한다
- B. 백엔드 HTTP 설정에서 "백엔드 대상에서 호스트 이름 선택(Pick host name from backend target)"을 사용하도록 설정한다
- C. 사용자 지정 도메인을 호스트 헤더로 사용하는 사용자 지정 프로브를 만든다
- D. 백엔드로 전달하기 전에 호스트 헤더를 제거하는 재작성 규칙을 추가한다

**정답: B**

**해설:** App Service는 HTTP Host 헤더의 호스트 이름을 사용해 요청을 올바른 앱으로 라우팅하는 멀티 테넌트 서비스입니다. 클라이언트 측에서 사용자 지정 도메인을 사용하는 경우 Host 헤더에 사용자 지정 도메인이 포함되어 App Service가 인식하지 못합니다. 백엔드 HTTP 설정에서 **"백엔드 대상에서 호스트 이름 선택"** 을 활성화하면 Application Gateway가 Host 헤더를 백엔드 풀 대상의 FQDN(예: `webapp1.azurewebsites.net`)으로 자동 교체합니다.

---

### 문제 19

서브넷에 Microsoft.Storage 서비스 엔드포인트가 사용하도록 설정되어 있습니다. 서비스 엔드포인트 정책을 사용해 서브넷이 특정 Azure Storage Account에만 도달할 수 있도록 제한하려 합니다. Azure Storage의 서비스 엔드포인트 정책에 어떤 제한이 적용됩니까?

- A. 서비스 엔드포인트 정책은 VNet과 동일한 지역의 Storage Account만 대상으로 할 수 있다
- B. 서비스 엔드포인트 정책은 온-프레미스 네트워크의 트래픽에는 적용되지 않는다
- C. 서비스 엔드포인트 정책은 최소 /24 서브넷 크기가 필요하다
- D. 서비스 엔드포인트 정책은 정책당 하나의 Storage Account만 허용할 수 있다

**정답: B**

**해설:** 서비스 엔드포인트 정책은 **Azure VNet 서브넷에서** Azure Storage로의 아웃바운드 트래픽만 필터링합니다. 온-프레미스 네트워크(VPN, ExpressRoute 등)에서 발생한 트래픽은 서비스 엔드포인트 정책을 완전히 우회합니다. 옵션 A에 대해: 서비스 엔드포인트 정책이 적용되면 범위가 글로벌로 업그레이드되어 모든 지역의 Storage Account를 대상으로 할 수 있습니다.

---

### 문제 20

`fabrikam.com`의 공개 Azure DNS 영역이 있습니다. DNS 라운드 로빈 부하 분산을 위해 서로 다른 IP 주소를 가리키는 여러 A 레코드가 포함된 레코드 집합을 만들려 합니다. Azure DNS의 단일 A 레코드 집합에 추가할 수 있는 최대 레코드 수는 몇 개입니까?

- A. 10개
- B. 20개
- C. 50개
- D. 100개

**정답: B**

**해설:** Azure DNS는 A, AAAA, MX, TXT 같이 여러 값을 허용하는 레코드 유형에 대해 레코드 집합당 최대 **20개의 레코드**를 지원합니다. 레코드 집합에는 동일한 레코드가 두 개 포함될 수 없습니다. 이 제한은 여러 IP 주소에 걸친 라운드 로빈 DNS 부하 분산과 같은 시나리오를 지원합니다.

---

### 문제 21

`contoso.com`에 대한 Azure DNS 영역이 있습니다. **영역 루트**(zone apex, `contoso.com`)를 Azure Traffic Manager 프로필로 확인되도록 구성해야 합니다. 어떤 유형의 DNS 레코드를 만들어야 합니까?

- A. CNAME 레코드
- B. 고정 IP가 포함된 A 레코드
- C. Traffic Manager 프로필을 가리키는 Alias(A/AAAA) 레코드
- D. Traffic Manager를 가리키는 NS 레코드

**정답: C**

**해설:** DNS 표준에서는 영역 루트(루트 도메인 또는 네이키드 도메인)에 CNAME 레코드를 허용하지 않습니다. Azure DNS는 루트에 배치하고 Traffic Manager 프로필, Azure CDN, Azure Front Door 같은 Azure 리소스를 직접 가리킬 수 있는 특수 레코드 유형인 **Alias 레코드**를 지원합니다. 레코드는 대상 Azure 리소스의 현재 IP로 동적으로 확인됩니다.

---

### 문제 22

트래픽을 검사하는 NVA 역할을 할 VM을 배포했습니다. NVA의 개인 IP를 가리키는 UDR이 포함된 경로 테이블을 만들었습니다. NVA를 통해야 하는 트래픽이 삭제됩니다. 가장 가능성 높은 원인은 무엇입니까?

- A. 경로 테이블이 올바른 서브넷에 연결되어 있지 않다
- B. Azure에서 NVA의 네트워크 인터페이스에 IP 전달이 활성화되지 않았다
- C. NVA VM이 너무 작아 트래픽 부하를 처리할 수 없다
- D. NVA 서브넷의 NSG 규칙이 전달된 트래픽을 차단하고 있다

**정답: B**

**해설:** Azure는 다른 대상 IP로 주소가 지정된 패킷을 VM의 NIC로 전송할 때, 해당 NIC에 명시적으로 **IP 전달**이 활성화되지 않으면 패킷을 삭제합니다. 기본적으로 Azure의 가상화 계층은 NIC가 자신의 IP로 주소가 지정된 트래픽만 허용하도록 강제합니다. 다른 대상으로 트래픽을 전달해야 하는 NVA의 경우 Azure NIC 리소스에서 IP 전달을 활성화해야 합니다(VM 내부 OS 수준의 IP 전달 구성도 필요).

---

## 4) Storage, SAS 및 Blob

### 문제 23

파트너 애플리케이션에 컨테이너의 Blob에 대한 임시 읽기/쓰기 액세스가 필요합니다. **Storage Account 키를 재생성하지 않고** 액세스를 빠르게 취소할 수 있어야 합니다. 어떤 접근 방식을 사용해야 합니까?

- A. 짧은 만료 시간의 임시 Service SAS를 만든다
- B. 저장된 액세스 정책(stored access policy)과 연결된 Service SAS를 만든다
- C. 저장된 액세스 정책과 연결된 Account SAS를 만든다
- D. 저장된 액세스 정책과 함께 User Delegation SAS를 만든다

**정답: B**

**해설:** **저장된 액세스 정책과 연결된 Service SAS**는 Storage Account 키를 재생성하지 않고도 취소할 수 있는 유일한 SAS 유형입니다. 저장된 액세스 정책을 삭제하거나 수정하면 연결된 모든 SAS 토큰이 즉시 무효화됩니다. 임시 SAS 토큰(저장된 정책 없음)은 계정 키를 순환해야만 무효화할 수 있습니다. Account SAS와 User Delegation SAS는 저장된 액세스 정책을 지원하지 않습니다.

---

### 문제 24

가장 높은 보안으로 Blob Storage 리소스에 대한 위임된 액세스를 제공해야 합니다. 이 시나리오에 대해 Microsoft에서 권장하는 SAS 유형은 무엇입니까?

- A. 기본 Storage Account 키를 사용하는 Account SAS
- B. 저장된 액세스 정책이 있는 Service SAS
- C. User Delegation SAS
- D. 고객 관리 키를 사용하는 Account SAS

**정답: C**

**해설:** Microsoft는 Blob 작업에 가장 안전한 SAS 유형으로 **User Delegation SAS**를 권장합니다. Service SAS 및 Account SAS(Storage Account 키로 서명)와 달리 User Delegation SAS는 Microsoft Entra 자격 증명으로 서명됩니다. 계정 키를 노출하지 않고 RBAC와 통합되며 침해 시 피해 범위를 제한합니다. 단, User Delegation SAS는 저장된 액세스 정책을 지원하지 않습니다.

---

### 문제 25

Storage Account에 **Blob 버전 관리**와 **Blob 일시 삭제**(14일 보존)가 모두 활성화되어 있습니다. 개발자가 `report.pdf`라는 기존 Blob을 새 콘텐츠로 덮어씁니다. 이전 콘텐츠에는 어떤 일이 발생합니까?

- A. 이전 콘텐츠가 영구적으로 삭제된다
- B. 이전 콘텐츠의 일시 삭제된 스냅샷이 만들어진다
- C. 원본 콘텐츠를 포함하는 새 이전 버전이 자동으로 만들어진다
- D. Blob이 14일 동안 잠기고 다시 덮어쓸 수 없다

**정답: C**

**해설:** Blob 버전 관리가 활성화된 경우, Blob을 덮어쓰면 원본 콘텐츠를 보존하는 **이전 버전**이 자동으로 만들어집니다. 일시 삭제된 스냅샷은 버전 관리가 **비활성화**된 경우에만 덮어쓰기 시 만들어집니다. 버전 관리가 활성화된 경우 버전 관리 메커니즘이 덮어쓰기에 우선합니다. 일시 삭제는 Blob을 삭제할 때는 여전히 적용되지만, 버전 관리가 활성 상태일 때 덮어쓰기에는 적용되지 않습니다.

---

### 문제 26

Storage Account에 Subnet1에서 Microsoft.Storage 서비스 엔드포인트가 활성화되어 있습니다. Storage Account 방화벽에 Subnet1에 대한 VNet 규칙을 추가했습니다. 구성 후에도 Subnet1의 VM1이 Storage Account에 액세스할 수 없습니다. 가장 가능성 높은 누락 단계는 무엇입니까?

- A. VM의 관리 ID에 Storage Blob Data Contributor 역할을 할당한다
- B. Subnet1에서 Microsoft.Storage 서비스 엔드포인트를 활성화한다
- C. Storage Account에 대한 Private Endpoint를 만든다
- D. Storage Account 방화벽에 VM의 공용 IP를 허용한다

**정답: B**

**해설:** Storage Account 방화벽에 VNet 규칙을 추가하려면 해당 서브넷에서 **Microsoft.Storage 서비스 엔드포인트**가 활성화되어 있어야 합니다. 서브넷에 서비스 엔드포인트가 활성화되지 않으면 VNet 규칙이 효과가 없고 서브넷의 트래픽은 여전히 공용 IP에서 오는 것으로 처리됩니다. 서비스 엔드포인트는 방화벽 규칙이 적용되기 전에 서브넷에서 명시적으로 활성화해야 합니다.

---

### 문제 27

조직이 AD DS 인증과 함께 Azure Files를 사용합니다. 도메인에 연결된 VM에서 **LocalSystem** 컨텍스트로 실행되는 애플리케이션이 Azure 파일 공유에 액세스하지 못합니다. 최소 구성으로 액세스를 활성화해야 합니다. 공유 수준 권한은 어떻게 구성해야 합니까?

- A. 각 컴퓨터 계정에 직접 Storage File Data SMB Share Contributor 역할을 할당한다
- B. 모든 인증된 ID에 대한 기본 공유 수준 권한을 구성한다
- C. 컴퓨터 계정이 포함된 보안 그룹을 만들고 Reader 역할을 할당한다
- D. ID 기반 인증을 비활성화하고 Storage Account 키 인증을 사용한다

**정답: B**

**해설:** LocalSystem 컨텍스트로 실행되는 애플리케이션은 VM의 **컴퓨터 계정**(DOMAIN\MACHINENAME$)을 사용해 Azure Files에 인증합니다. 컴퓨터 계정은 Microsoft Entra ID에 동기화할 수 없으므로 Azure RBAC 역할을 직접 할당할 수 없습니다. 올바른 접근 방식은 모든 인증된 ID에 적용되는 **기본 공유 수준 권한**을 구성하는 것입니다. 이는 최소 구성으로 컴퓨터 계정을 포함합니다.

---

### 문제 28

Azure Storage Account의 **Archive** 계층에 블록 Blob이 있습니다. 수명 주기 관리 정책을 사용해 일정 기간 후 Blob을 Hot 계층으로 자동 리하이드레이션하려 합니다. 어떻게 해야 합니까?

- A. `daysAfterModificationGreaterThan`이 포함된 `tierToHot` 작업을 구성한다
- B. 먼저 `tierToCool`을 구성한 다음 `tierToHot` 규칙을 추가한다
- C. Storage Account에서 `enableAutoTierToHotFromCool` 속성을 활성화한다
- D. 다른 방법을 사용한다 — 수명 주기 관리 정책은 Archive 계층에서 Blob을 리하이드레이션할 수 없다

**정답: D**

**해설:** Azure Storage 수명 주기 관리 정책은 Blob을 **더 차가운** 저장소 계층으로만 전환할 수 있습니다(예: Hot → Cool → Archive). Archive에서 온라인 계층으로 리하이드레이션(웜업)하는 것은 지원하지 않습니다. Archive된 Blob을 리하이드레이션하려면 **Set Blob Tier API**, **Copy Blob** 작업, 또는 Portal에서 수동으로 계층을 변경해야 합니다.

---

## 5) 컴퓨팅 및 App Service

### 문제 29

메트릭 경고 규칙이 **상태 저장(stateful)** 으로 구성되어 있습니다. CPU 사용량이 임계값을 초과하고 이 상태가 3시간 동안 지속된 후 정상으로 돌아옵니다. 이 기간 동안 Action Group이 몇 번 트리거됩니까?

- A. 평가 간격마다 한 번(예: 5분마다)
- B. 3번 — 매시간 한 번씩
- C. 한 번 — 경고가 발생하여 Fired 상태로 전환될 때
- D. 두 번 — 발생 시 한 번, 해결 시 한 번

**정답: C**

**해설:** **상태 저장(stateful)** 메트릭 경고는 현재 경고 상태(Fired 또는 Resolved)를 추적합니다. 임계값이 처음 초과되면 경고가 발생하고 Action Group이 한 번 트리거됩니다. CPU가 임계값을 초과하는 동안에는 상태가 변경되지 않으므로 후속 평가에서 Action Group이 다시 트리거되지 않습니다. 두 번째 Action Group 트리거는 경고가 해결될 때(CPU가 임계값 아래로 떨어질 때)만 발생합니다. 상태 비저장 경고는 반대로 조건이 충족되는 모든 평가 간격에서 Action Group을 트리거합니다.

---

### 문제 30

Windows 가상 머신 VM1에 **Azure Disk Encryption**을 활성화하려 합니다. 동일한 지역에 KeyVault1이라는 Azure Key Vault를 만들었습니다. Azure Disk Encryption을 구성하기 전에 활성화해야 하는 Key Vault 액세스 정책 설정은 무엇입니까?

- A. 템플릿 배포를 위해 Azure Resource Manager에 대한 액세스 활성화
- B. 볼륨 암호화를 위해 Azure Disk Encryption에 대한 액세스 활성화
- C. 일시 삭제 및 제거 보호 활성화
- D. Azure Disk Encryption 인증서 권한 활성화

**정답: B**

**해설:** Azure Disk Encryption을 사용하려면 Key Vault에 **"볼륨 암호화를 위한 Azure Disk Encryption(Azure Disk Encryption for volume encryption)"** 액세스 정책 옵션이 활성화되어 있어야 합니다. 이 설정은 VM 부팅 및 디스크 복호화 중에 암호화 키를 검색할 수 있도록 Azure 플랫폼에 Key Vault 키 및 비밀 액세스 권한을 부여합니다. 이 설정이 없으면 ADE가 Key Vault에 액세스할 수 없어 암호화 구성이 실패합니다.

---

### 문제 31

WebApp1이라는 App Service 웹앱이 **Basic** 가격 책정 계층에 있습니다. WebApp1에 대한 스테이징 배포 슬롯을 만들어야 합니다. 먼저 무엇을 해야 합니까?

- A. App Service 구성에서 배포 슬롯을 활성화한다
- B. Azure Portal에서 새 배포 슬롯을 추가한다
- C. App Service 플랜을 Standard 계층 이상으로 스케일 업한다
- D. Standard 계층에서 새 App Service 플랜을 만들고 WebApp1을 이동한다

**정답: C**

**해설:** 배포 슬롯은 Free 또는 Basic 가격 책정 계층에서는 사용할 수 없습니다. **Standard 계층 이상**(Standard, Premium 또는 Isolated)으로 업그레이드해야 배포 슬롯 기능을 사용할 수 있습니다. 플랜을 스케일 업한 후 Portal에서 직접 스테이징 슬롯을 추가할 수 있습니다.

---

### 문제 32

스테이징 배포 슬롯이 있는 웹앱이 있습니다. 스테이징 슬롯을 프로덕션으로 교체하려 합니다. 교체가 완료되기 전에 **프로덕션 설정**으로 스테이징 애플리케이션을 검증하고, 검증이 실패하면 취소할 수 있어야 합니다. 무엇을 사용해야 합니까?

- A. Traffic Manager를 사용한 Blue-Green 배포
- B. 미리 보기로 교체(Swap with preview, 다단계 교체)
- C. 롤백이 포함된 표준 슬롯 교체
- D. 트래픽 라우팅 비율을 사용한 A/B 테스트

**정답: B**

**해설:** **미리 보기로 교체(Swap with preview)** 는 먼저 프로덕션 설정(슬롯 설정으로 표시된 연결 문자열, 앱 설정)을 스테이징 슬롯에 적용하고, 프로덕션 트래픽을 라우팅하지 않습니다. 이를 통해 교체를 커밋하기 전에 스테이징 앱이 프로덕션 구성에서 올바르게 작동하는지 테스트할 수 있습니다. 검증이 실패하면 취소하면 되고 스테이징 슬롯은 원래 설정으로 돌아갑니다.

---

### 문제 33

App Service 트래픽 라우팅을 구성해 프로덕션 트래픽의 **10%** 를 스테이징 슬롯으로 보냅니다. 사용자의 첫 번째 요청이 스테이징 슬롯으로 라우팅됩니다. 이 사용자의 세션은 얼마 동안 스테이징 슬롯에 고정됩니까?

- A. 브라우저 세션이 유지되는 동안
- B. 브라우저를 닫을 때까지
- C. 1시간 또는 라우팅 쿠키가 삭제될 때까지
- D. 수동으로 재설정할 때까지 영구적으로

**정답: C**

**해설:** 사용자가 트래픽 비율 라우팅을 통해 배포 슬롯으로 라우팅된 후 App Service는 HTTP 응답에 `x-ms-routing-name` 쿠키를 설정합니다. 이 쿠키는 **1시간 후** 만료됩니다. 쿠키가 존재하고 유효한 동안 해당 사용자의 후속 요청은 동일한 슬롯으로 라우팅됩니다. 브라우저가 쿠키를 삭제하거나 쿠키가 만료되면 다음 요청에서 라우팅 결정이 새로 이루어집니다.

---

### 문제 34

App Service 웹앱에 사용자 지정 도메인 `www.contoso.com`을 TLS/SSL과 함께 구성해야 합니다. **동일한 공유 IP 주소에서 여러 사용자 지정 도메인**을 지원하고 비용을 최소화해야 합니다. 어떤 TLS/SSL 바인딩 유형을 사용해야 합니까?

- A. IP 기반 SSL
- B. SNI SSL
- C. 와일드카드 인증서 바인딩
- D. Azure Front Door 관리형 인증서

**정답: B**

**해설:** **SNI SSL**(서버 이름 표시)은 단일 공유 IP 주소에서 여러 TLS 인증서를 호스팅할 수 있게 합니다. 클라이언트는 TLS 핸드셰이크 중에 대상 호스트 이름을 표시하므로 서버가 올바른 인증서를 선택할 수 있습니다. 이는 여러 사용자 지정 도메인에 대한 표준적이고 비용 효율적인 접근 방식입니다. **IP 기반 SSL**은 인증서당 전용 IP 주소가 필요해 비용이 더 많이 들고 확장성이 제한됩니다.

---

### 문제 35

Azure Container Instances를 배포하고 단일 컨테이너 그룹에서 여러 컨테이너를 실행해야 합니다(사이드카 패턴). Azure Container Instances에서 다중 컨테이너 그룹을 지원하는 운영 체제는 무엇입니까?

- A. Windows만
- B. Linux만
- C. Linux와 Windows 모두
- D. Windows Server 2019만

**정답: B**

**해설:** 다중 컨테이너 그룹(두 개 이상의 컨테이너가 있는 컨테이너 그룹)은 Azure Container Instances에서 **Linux**에서만 지원됩니다. ACI의 Windows 컨테이너는 단일 컨테이너 그룹으로 제한됩니다. Linux 컨테이너 그룹은 사이드카 패턴을 사용해 동일한 네트워크와 저장소를 공유하는 기본 애플리케이션 컨테이너 옆에 보조 컨테이너(로깅 에이전트, 프록시 등)를 실행할 수 있습니다.

---

### 문제 36

**Standard** 계층의 Azure Container Registry ACR1이 있습니다. 여러 Azure 지역의 AKS 클러스터에 컨테이너 이미지를 배포하려 합니다. 모든 클러스터로의 이미지 풀 지연 시간을 최소화해야 합니다. 먼저 무엇을 해야 합니까?

- A. 각 지역에 별도의 ACR 레지스트리를 만든다
- B. ACR1에서 지역 복제를 활성화한다
- C. ACR1을 Premium 계층으로 업그레이드한다
- D. Container Registry를 위한 CDN 배포를 구성한다

**정답: C**

**해설:** 지역 복제(여러 지역 복제본에 레지스트리 콘텐츠를 배포해 짧은 지연 시간의 풀을 가능하게 하는 기능)는 Azure Container Registry의 **Premium 계층에서만 사용할 수 있는** 기능입니다. Standard 계층은 지역 복제를 지원하지 않습니다. 따라서 지역 복제를 구성하기 전에 먼저 ACR1을 Premium 계층으로 업그레이드해야 합니다.

---

## 6) Azure Bastion 및 연결

### 문제 37

계약업체가 VNet의 VM1에 연결해야 하지만 Azure Portal 계정이 없습니다. **Standard SKU**의 Azure Bastion이 배포되어 있습니다. 안전한 브라우저 기반 액세스를 제공하려면 어떻게 해야 합니까?

- A. 계약업체를 위한 새 Azure AD 게스트 계정을 만든다
- B. JIT VM 액세스를 활성화하고 RDP 파일을 공유한다
- C. VM1에 대한 Azure Bastion 공유 링크(Shareable Link)를 구성한다
- D. 계약업체를 위한 Point-to-Site VPN 연결을 만든다

**정답: C**

**해설:** Azure Bastion Standard SKU는 **공유 링크(Shareable Link)** 기능을 지원합니다. 이 기능은 사용자가 Azure Portal 계정 없이 브라우저를 통해 VM에 연결할 수 있는 고유한 URL을 생성합니다. 연결은 여전히 Bastion을 통해 이루어지므로(VM에 공용 IP 불필요) 안전한 RDP/SSH 액세스를 제공합니다. 공유 링크는 Basic SKU에서는 사용할 수 없습니다.

---

### 문제 38

규정 준수 요구 사항에 따라 가상 머신에 대한 모든 RDP 및 SSH 연결의 **세션 녹화**가 필요하며, 녹화는 고객이 지정한 Storage Account에 저장되어야 합니다. 어떤 Azure Bastion SKU를 배포해야 합니까?

- A. Basic
- B. Developer
- C. Standard
- D. Premium

**정답: D**

**해설:** 세션 녹화는 **Azure Bastion Premium SKU에서만** 사용할 수 있습니다. 활성화하면 Bastion이 모든 RDP 및 SSH 세션을 그래픽으로 녹화하고 고객이 지정한 Storage Account(SAS URL을 통해)에 녹화를 저장합니다. Standard SKU는 네이티브 클라이언트 지원, 호스트 크기 조정, 공유 링크 같은 고급 기능을 포함하지만 세션 녹화는 포함하지 않습니다.

---

### 문제 39

**세션 녹화**와 Bastion 리소스 자체에 공용 IP 주소 없이 사용자가 연결할 수 있는 기능(프라이빗 전용 배포)을 모두 지원하는 Azure Bastion을 배포해야 합니다. 어떤 SKU를 배포해야 합니까?

- A. 프라이빗 배포가 활성화된 Basic SKU
- B. 프라이빗 전용 구성의 Standard SKU
- C. Premium SKU
- D. 모든 SKU — 모두 프라이빗 배포와 세션 녹화를 지원한다

**정답: C**

**해설:** 세션 녹화와 프라이빗 전용 배포(Bastion 호스트에 공용 IP 없음) 모두 **Premium SKU에서만** 사용할 수 있는 기능입니다. 두 기능을 동시에 지원하는 다른 SKU는 없습니다. Standard SKU는 공유 링크와 네이티브 클라이언트를 지원하지만 세션 녹화나 완전한 프라이빗 배포는 지원하지 않습니다.

---

## 7) Kubernetes(AKS)

### 문제 40

시스템 노드 풀이 있는 AKS 클러스터가 있습니다. 시스템 노드 풀에 애플리케이션 Pod가 스케줄링되지 않도록 방지하면서, 시스템 Pod는 계속 실행되어야 합니다. 시스템 노드 풀에 어떤 테인트를 적용해야 합니까?

- A. `node-role.kubernetes.io/system:NoExecute`
- B. `kubernetes.azure.com/mode=system:NoSchedule`
- C. `SystemOnly=true:PreferNoSchedule`
- D. `CriticalAddonsOnly=true:NoSchedule`

**정답: D**

**해설:** `CriticalAddonsOnly=true:NoSchedule`은 시스템 노드 풀에 대한 AKS의 권장 테인트입니다. 시스템 Pod(coredns, kube-proxy, 기타 중요한 추가 기능 등)는 시스템 노드 풀에서 계속 실행될 수 있는 매칭 톨러레이션이 있습니다. 일반 애플리케이션 Pod에는 이 톨러레이션이 없으므로 시스템 노드에 스케줄링되지 않습니다. `NoExecute`는 기존 Pod도 추가로 퇴거시켜 더 공격적입니다.

---

## 8) Bicep 및 ARM 템플릿

### 문제 41

`storage.bicep`이라는 Bicep 모듈이 Storage Account를 배포하고 `storageEndpoint`라는 출력을 가집니다. 메인 Bicep 파일에서 모듈을 `stgModule`로 호출한 후 이 출력에 어떻게 액세스해야 합니까?

- A. `stgModule.storageEndpoint`
- B. `stgModule.outputs.storageEndpoint`
- C. `reference(stgModule).outputs.storageEndpoint.value`
- D. `stgModule.outputs.storageEndpoint.value`

**정답: B**

**해설:** Bicep에서 모듈 출력은 `moduleName.outputs.outputName` 패턴을 사용해 액세스합니다. `.value` 접미사는 필요하지 않습니다. Bicep은 형식화된 값을 직접 제공합니다. 이는 연결된 배포 출력에 명시적인 `.value` 접미사가 포함된 `reference('deploymentName').outputs.outputName.value`가 필요한 ARM 템플릿과 다릅니다.

---

### 문제 42

메인 ARM 템플릿이 `appplan`이라는 연결된 템플릿을 사용해 App Service 플랜을 배포합니다. 연결된 템플릿에는 `planId`라는 출력이 있습니다. App Service의 `serverFarmId` 속성을 설정하기 위해 이 출력을 참조하려면 어떤 식을 사용해야 합니까?

- A. `"[parameters('planId')]"`
- B. `"[variables('appplan').outputs.planId]"`
- C. `"[reference('appplan').outputs.planId.value]"`
- D. `"[outputs('appplan').planId]"`

**정답: C**

**해설:** ARM 템플릿에서 연결된 배포의 출력에 액세스하려면 `reference('<deploymentName>').outputs.<outputName>.value`를 사용합니다. `.value` 접미사가 필요한데, ARM JSON에서 출력은 `value` 속성이 있는 객체이기 때문입니다. Bicep에서는 동등한 표현이 `.value` 없이 `appplan.outputs.planId`입니다.

---

### 문제 43

`param deployStorage bool = false`가 있는 Bicep 파일이 있습니다. `deployStorage`가 true이면 Storage Account의 Blob 엔드포인트를 반환하고, false이면 빈 문자열을 반환하는 출력을 작성해야 합니다. 올바른 출력 선언은 무엇입니까?

- A. `output blobEndpoint string = if (deployStorage) storageAccount.properties.primaryEndpoints.blob`
- B. `output blobEndpoint string = when (deployStorage) storageAccount.properties.primaryEndpoints.blob else ''`
- C. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- D. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`

**정답: D**

**해설:** Bicep에서 `if (...)` 키워드는 조건부 **리소스 및 모듈 배포**(예: `resource x = if (condition) {...}`)에만 사용됩니다. 조건부 **출력 값**에는 **삼항 연산자**(`condition ? trueValue : falseValue`)를 사용해야 합니다. null 합치기 연산자 `??`는 리소스가 배포되지 않은 경우 `storageAccount.properties`에 액세스하면 연산자가 평가되기 전에 런타임 오류가 발생하므로 적합하지 않습니다.

---

### 문제 44

Bicep을 사용해 리소스 그룹을 배포한 후 동일한 Bicep 파일에서 해당 리소스 그룹에 Storage Account를 배포하려 합니다. 파일의 대상 범위는 **구독**입니다. 어떻게 구조화해야 합니까?

- A. `targetScope = 'resourceGroup'`으로 설정하고 리소스 그룹과 Storage Account를 동일한 파일에 포함한다
- B. `targetScope = 'subscription'`으로 설정하고, 리소스 그룹을 최상위 리소스로 만들고, Storage Account에 대해 `scope: resourceGroup('RGName')`이 포함된 모듈을 사용한다
- C. 두 개의 별도 파일을 만든다 — 리소스 그룹에는 구독 범위, Storage Account에는 리소스 그룹 범위
- D. Bicep이 혼합 범위를 지원하지 않으므로 ARM 템플릿 중첩을 사용한다

**정답: B**

**해설:** Bicep 파일이 구독 범위를 대상으로 할 때(`targetScope = 'subscription'`), 리소스 그룹을 최상위 리소스로 만들 수 있습니다. 동일한 파일 내에서 리소스 그룹 범위에서 리소스를 배포하려면 `scope: resourceGroup('RGName')`이 포함된 **모듈**을 사용합니다. 부모 파일은 구독 범위에 유지되면서 모듈 배포는 명명된 리소스 그룹으로 범위가 지정됩니다.

---

## 9) 모니터링 및 Azure Monitor

### 문제 45

Azure Portal에서 기본 데이터 컬렉션 규칙 설정을 적용해 VM에 대한 **VM Insights**를 활성화했습니다. VM에서 어떤 데이터가 수집됩니까?

- A. 게스트 성능 메트릭, 프로세스 및 종속성
- B. 호스트 메트릭, 게스트 성능 메트릭 및 Windows 이벤트 로그
- C. 게스트 성능 메트릭만
- D. 게스트 성능 메트릭 및 Windows 이벤트 로그

**정답: C**

**해설:** 기본 DCR(데이터 컬렉션 규칙)로 VM Insights를 활성화하면 **게스트 OS 성능 메트릭**(CPU, 메모리, 디스크, 네트워크)만 수집됩니다. 프로세스 및 종속성 기능(VM Insights 맵 보기를 지원)은 데이터 수집 비용을 줄이기 위해 **기본적으로 비활성화**되어 있습니다. 활성화하려면 새 DCR을 만들거나 기존 DCR을 수정해야 합니다. 이벤트 로그도 기본 VM Insights DCR에서 수집되지 않습니다.

---

### 문제 46

Azure Log Analytics에서 이벤트 수를 시간별로 그룹화하는 KQL 쿼리를 실행 중입니다. 결과를 **시간 기반 꺾은선형 차트**로 시각화해야 합니다. 쿼리 끝에 어떤 연산자를 추가해야 합니까?

- A. `| render barchart`
- B. `| render table`
- C. `| render piechart`
- D. `| render timechart`

**정답: D**

**해설:** KQL의 `render timechart` 연산자는 쿼리 결과를 시간 계열 꺾은선형 차트로 렌더링합니다. 시간별 집계의 일반적인 패턴은 `| summarize count() by bin(TimeGenerated, 1h) | render timechart`입니다. 다른 렌더 옵션으로는 다양한 시각화 유형을 위한 `barchart`, `piechart`, `columnchart`, `table`이 있습니다.

---

## 10) Azure Files, 백업 및 Recovery Services

### 문제 47

AD DS에 연결된 Windows Server가 있습니다. AzFilesHybrid 모듈을 사용해 `storage1`이라는 Storage Account에서 AD DS 인증을 위한 Azure Files를 활성화할 계획입니다. `Join-AzStorageAccountForAuth` 명령을 실행하면 AD DS에 기본적으로 어떤 유형의 개체가 만들어집니까?

- A. 사용자 계정
- B. 서비스 주체
- C. 컴퓨터 계정
- D. 관리 서비스 계정

**정답: C**

**해설:** AzFilesHybrid 모듈의 `Join-AzStorageAccountForAuth` 명령은 Storage Account를 나타내기 위해 AD DS에 기본적으로 **컴퓨터 계정**을 만듭니다. 이는 Storage Account를 도메인에 가입된 컴퓨터 리소스로 모델링합니다. 컴퓨터 계정은 Kerberos 인증에 사용되므로 AD 가입 클라이언트가 도메인 자격 증명을 사용해 Azure 파일 공유에 인증할 수 있습니다.

---

### 문제 48

**스냅샷 계층** 정책을 사용해 Azure Backup으로 보호된 Azure 파일 공유가 있습니다. 사용자가 실수로 여러 개별 파일을 삭제했습니다. 삭제된 파일만 원래 위치로 복원해야 합니다. 어떤 복원 유형과 복구 대상을 선택해야 합니까?

- A. 전체 공유 복구 + 원래 위치
- B. 전체 공유 복구 + 대체 위치
- C. 파일 복구 + 대체 위치
- D. 파일 복구 + 원래 위치

**정답: D**

**해설:** 스냅샷 기반 Azure Files 백업에서는 항목 수준(개별 파일) 복원이 지원됩니다. 전체 공유가 아닌 삭제된 파일만 복원하려면 **파일 복구(File Recovery)** 를 선택합니다. 데이터를 다른 곳으로 이동하지 않고 동일한 위치로 복원하려면 **원래 위치(Original Location)** 를 선택합니다. 파일 복구 + 원래 위치 조합은 선택한 파일만 원래 있던 위치로 복원합니다.

---

### 문제 49

Azure Storage Account에 파일 공유가 있습니다. 일부는 **SMB** 프로토콜을 사용하고 일부는 **NFS** 프로토콜을 사용합니다. Azure Backup을 구성해야 합니다. 어떤 파일 공유를 백업할 수 있습니까?

- A. NFS 파일 공유만
- B. SMB 파일 공유만
- C. SMB 및 NFS 파일 공유 모두
- D. 모두 불가 — Azure Backup은 Azure Files를 지원하지 않는다

**정답: B**

**해설:** Azure Files에 대한 Azure Backup은 **SMB 파일 공유만** 지원합니다. NFS 파일 공유는 Azure Backup에서 지원되지 않습니다. Azure Files의 백업 메커니즘은 SMB 스냅샷 기능을 사용합니다. NFS 공유에 대한 데이터 보호가 필요한 경우 Azure Backup 외의 대안적인 방법을 사용해야 합니다.

---

### 문제 50

가상 머신의 on-demand backup을 트리거하고 보존 기간을 **60일**로 지정했습니다. 현재 백업 정책의 보존은 30일입니다. 1주일 후 정책을 수정해 보존을 **14일**로 줄였습니다. on-demand backup 복구 지점은 어떻게 됩니까?

- A. on-demand recovery point이 새 정책에 맞게 14일로 업데이트된다
- B. on-demand recovery point은 원래 지정된 대로 60일 동안 보존된다
- C. 정책이 변경되면 on-demand recovery point이 즉시 삭제된다
- D. on-demand recovery point이 소급하여 정책 보존을 상속한다

**정답: B**

**해설:** on-demand backup 복구 지점은 정책 기반 백업과 독립적인 수명 주기를 가집니다. on-demand backup 시 지정된 보존 기간(60일)은 해당 복구 지점에 대해 고정됩니다. 이후 백업 정책 변경(보존을 14일로 줄이기)은 정책이 만든 향후 **예약된** 복구 지점에만 영향을 미칩니다. 기존 on-demand recovery point을 소급하여 수정하거나 삭제하지 않습니다.

---

_AZ-104 강의 자료를 기반으로 생성된 문서입니다. 최종 업데이트: 2026-05-22_
