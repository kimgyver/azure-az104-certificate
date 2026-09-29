# AZ-104 예상 문제 (세트 11) — 한국어

---

## 1) 네트워킹 및 방화벽

### 문제 1
Azure Standard Load Balancer를 NVA(네트워크 가상 어플라이언스)에 대해 모든 TCP 및 UDP 트래픽을 모든 포트에서 처리하도록 구성하고 있습니다. 부하 분산 규칙에서 Protocol과 Port 값을 어떻게 설정해야 합니까?

- A. Protocol: TCP; Port: 80
- B. Protocol: All; Port: 0
- C. Protocol: UDP; Port: 0
- D. Protocol: TCP; Port: 443

**정답: B**
**해설:** 모든 TCP 및 UDP 트래픽을 모든 포트에서 처리해야 하는 NVA 시나리오에서는 Protocol: All, Port: 0으로 구성된 HA Ports 규칙을 사용합니다. Port 0은 Azure에서 "모든 포트"를 의미합니다. 이는 방화벽, IDS/IPS 등 전체 패킷 가시성이 필요한 NVA로 트래픽을 전달할 때 사용하는 표준 HA Ports 패턴입니다.

---

### 문제 2
East US 리전의 가상 머신이 동일 리전에 있는 스토리지 계정 storage1에 접근하지 못합니다. 스토리지 계정에는 방화벽이 구성되어 있고 온프레미스 서버의 공개 IP만 추가되어 있습니다. VM 접근을 허용하려면 무엇을 해야 합니까?

- A. VM NIC에 포트 443 인바운드를 허용하는 NSG 규칙 추가
- B. 스토리지 계정 방화벽에 VM의 가상 네트워크 서브넷을 VNet 규칙으로 추가
- C. VM의 관리 ID에 Storage Blob Data Reader 역할 할당
- D. 서브넷에 Microsoft.Compute용 Service Endpoint 활성화

**정답: B**
**해설:** 스토리지 계정 방화벽 규칙은 스토리지 서비스에 접근할 수 있는 네트워크를 제어합니다. NSG는 VM/서브넷 수준의 트래픽을 필터링하지만 스토리지 계정 데이터 플레인 접근을 허용하지 않습니다. 방화벽을 통해 VM의 스토리지 접근을 허용하려면 VM의 VNet/서브넷을 스토리지 계정의 VNet 규칙으로 추가해야 합니다.

---

### 문제 3
VM에 대한 포트 443 인바운드 TCP 트래픽을 허용하는 NSG 규칙을 구성했습니다. 클라이언트가 요청을 보낸 후, VM이 클라이언트에게 반환하는 응답 트래픽이 NSG에 의해 자동으로 허용됩니까?

- A. 아니요 — 반환 트래픽에 대한 명시적 아웃바운드 허용 규칙을 추가해야 합니다
- B. 예 — Azure NSG는 Stateful입니다. 허용된 연결에 대한 반환 트래픽은 자동으로 허용됩니다
- C. 아니요 — 반환 트래픽을 허용하려면 Service Endpoint를 활성화해야 합니다
- D. 예 — 단, 서브넷 NSG와 NIC NSG에 동일한 규칙이 있을 때만 해당됩니다

**정답: B**
**해설:** Azure NSG는 Stateful입니다. 인바운드 연결이 허용되면 해당 반환(응답) 트래픽은 명시적 아웃바운드 규칙 없이도 자동으로 허용됩니다. 이 Stateful 동작은 TCP 및 UDP 연결 모두에 적용되며, 역시 Stateful인 AWS Security Group과 동일한 동작입니다.

---

### 문제 4
ARM 템플릿 배포 후 Windows VM의 확장(extension)이 실행에 실패합니다. VM Agent 상태는 정상입니다. VM 서브넷 NSG에는 모든 아웃바운드를 차단하는 사용자 지정 규칙이 있습니다. VM 확장이 정상 작동하려면 어떤 대상 IP에 접근할 수 있어야 합니까?

- A. 10.0.0.1 (Azure DNS)
- B. 8.8.8.8 (Google DNS)
- C. 168.63.129.16 (Azure 플랫폼 IP)
- D. 169.254.169.254 (인스턴스 메타데이터)

**정답: C**
**해설:** Azure VM 확장은 Azure 플랫폼 서비스 전용 가상 공개 IP인 168.63.129.16에 대한 아웃바운드 접근이 필요합니다. 이 IP는 VM Agent 통신, 확장 다운로드 및 관리, DNS 확인, 상태 프로브에 사용됩니다. 모든 아웃바운드를 차단하는 NSG 규칙은 이 통신을 막아 VM Agent가 정상이더라도 확장 프로비저닝이 실패하게 됩니다.

---

### 문제 5
Azure Firewall 애플리케이션 규칙으로 Windows Update 트래픽을 허용하려 합니다. Microsoft가 엔드포인트 목록을 자주 변경하므로 개별 FQDN이나 IP를 관리하지 않는 간단한 솔루션이 필요합니다. 무엇을 사용해야 합니까?

- A. 네트워크 규칙의 Service Tags
- B. 애플리케이션 규칙의 FQDN Tags
- C. Microsoft IP 범위가 포함된 IP Groups
- D. Application Security Groups (ASGs)

**정답: B**
**해설:** FQDN Tags는 잘 알려진 Azure 서비스 FQDN의 Microsoft 관리 컬렉션입니다. Azure Firewall 애플리케이션 규칙에서 "WindowsUpdate" FQDN Tag를 사용하면 개별 FQDN이나 IP를 수동으로 관리하지 않고도 모든 Microsoft Update 엔드포인트를 자동으로 커버합니다. Service Tags는 네트워크 규칙(IP 기반)에 사용하며 FQDN 기반이 아닙니다.

---

### 문제 6
Azure Firewall Premium을 배포했습니다. 여러 웹사이트로의 아웃바운드 HTTPS 요청이 동일한 IP 주소로 확인됩니다(CDN 기반 서비스에서 흔히 발생). site-a.example.com 트래픽은 허용하고 site-b.example.com 트래픽은 차단해야 하지만 두 FQDN 모두 같은 IP를 사용합니다. 무엇을 구성해야 합니까?

- A. 대상 IP 주소를 사용하는 네트워크 규칙
- B. FQDN 필터링이 있는 NAT 규칙
- C. TLS 검사가 활성화된 애플리케이션 규칙
- D. FQDN을 대상으로 하는 네트워크 규칙

**정답: C**
**해설:** 네트워크 규칙(L4)은 IP와 포트를 기반으로 작동하므로 같은 IP로 확인되는 두 FQDN을 구분할 수 없습니다. Azure Firewall Premium 애플리케이션 규칙에 TLS 검사를 활성화하면 HTTPS 트래픽을 복호화하여 HTTP Host 헤더와 TLS SNI 필드를 검사함으로써, 기반 IP가 동일해도 FQDN 수준의 필터링이 가능합니다. TLS 검사는 Azure Firewall Premium 기능입니다.

---

### 문제 7
Azure Firewall에 Threat Intelligence를 활성화했습니다. 다음 날, 방화벽 로그에 알려진 악성 IP가 기록되지만 트래픽이 여전히 백엔드 서버에 도달하고 있습니다. 가장 가능성 높은 원인은 무엇입니까?

- A. Threat Intelligence가 완전히 비활성화되어 있습니다
- B. Threat Intelligence가 경고 전용(Alert only) 모드로 설정되어 트래픽을 차단하지 않습니다
- C. NSG가 해당 트래픽에 대해 방화벽을 우회하고 있습니다
- D. 네트워크 규칙이 Threat Intelligence 설정보다 우선순위가 높습니다

**정답: B**
**해설:** Azure Firewall의 기본 Threat Intelligence 모드는 "경고 전용(Alert only)"입니다. 이 모드에서는 위협 인텔리전스 피드와 일치하는 항목이 기록되지만 트래픽은 차단되지 않습니다. 알려진 악성 IP 및 도메인으로부터의 트래픽을 실제로 차단하려면 Threat Intelligence 모드를 명시적으로 "경고 및 거부(Alert and deny)"로 변경해야 합니다.

---

### 문제 8
Threat Intelligence가 "경고 및 거부(Alert and deny)"로 설정된 Azure Firewall Policy(부모 정책)가 있습니다. 자식 정책이 이 부모 정책을 상속합니다. 자식 정책에서 사용 가능한 Threat Intelligence 모드는 무엇입니까?

- A. 끄기(Off), 경고(Alert), 경고 및 거부(Alert and deny) 중 선택 가능
- B. 끄기(Off) 또는 경고(Alert)만 선택 가능
- C. 경고 및 거부(Alert and deny)만 선택 가능
- D. 경고(Alert)만 선택 가능

**정답: C**
**해설:** Azure Firewall Policy 상속은 보안 강화 방향만 적용합니다. 자식 정책은 부모로부터 상속된 보안 수준을 낮출 수 없습니다. 부모 정책이 가장 제한적인 Threat Intelligence 모드인 "경고 및 거부"로 설정된 경우, 자식 정책은 "경고 및 거부"만 사용할 수 있습니다. 경고 또는 끄기로 낮출 수 없습니다.

---

### 문제 9
서브넷이 Microsoft.Storage용 Service Endpoint를 사용하고 있습니다. 데이터 유출을 방지하기 위해 서브넷의 리소스가 두 개의 특정 스토리지 계정에만 접근하고 다른 모든 스토리지 계정에는 차단되어야 합니다. 무엇을 구성해야 합니까?

- A. 허용된 두 스토리지 계정 각각에 VNet 규칙 추가
- B. 두 계정을 제외한 모든 스토리지 트래픽을 차단하는 NSG 아웃바운드 규칙 생성
- C. 허용된 두 스토리지 계정만 나열하는 Service Endpoint Policy를 서브넷에 생성
- D. 두 스토리지 계정에 Private Endpoint를 사용하고 공개 접근 비활성화

**정답: C**
**해설:** Service Endpoint만으로는 리전 내 모든 Azure Storage에 대한 서브넷 접근이 열려 데이터 유출 위험이 발생합니다. Service Endpoint Policy는 서브넷에 직접 적용되어 아웃바운드 트래픽을 지정된 스토리지 계정만으로 제한합니다. 스토리지 계정 VNet 규칙은 스토리지 계정 측 인바운드 접근을 제한하지만, 서브넷이 다른 스토리지 계정에 접근하는 것을 막지는 않습니다.

---

### 문제 10
App Service(Linux)에 배포 슬롯(스테이징 슬롯 포함)이 있습니다. 각 배포 후 스테이징 슬롯을 프로덕션으로 자동으로 교체하려 합니다. 슬롯 구성을 확인하니 Auto Swap을 사용할 수 없습니다. 왜 Auto Swap을 사용할 수 없습니까?

- A. Auto Swap은 Standard SKU App Service Plan이 필요합니다
- B. Auto Swap은 Linux App Service에서 지원되지 않습니다
- C. Auto Swap은 먼저 사용자 지정 도메인이 구성되어야 합니다
- D. Auto Swap은 Function App에서만 사용 가능합니다

**정답: B**
**해설:** Auto Swap은 Windows App Service 배포 슬롯에서만 사용 가능한 기능입니다. Linux App Service 슬롯에서는 지원되지 않습니다. Linux의 경우, CI/CD 파이프라인(Azure Pipelines 또는 GitHub Actions)에서 `az webapp deployment slot swap` 명령을 사용하여 슬롯 교체를 명시적 단계로 수행해야 합니다.

---

### 문제 11
App Service(Linux)에 스테이징 슬롯이 있습니다. 스테이징에 각 배포가 성공한 후 자동으로 프로덕션으로 교체하려 합니다. Auto Swap이 지원되지 않는 경우 권장 방법은 무엇입니까?

- A. 타이머 Logic App을 사용하여 swap API 호출
- B. 배포 단계 이후 CI/CD 파이프라인에 슬롯 교체 단계 추가
- C. 상태 확인(Health Check) 프로브를 구성하여 자동으로 교체 트리거
- D. Traffic Manager를 구성하여 스테이징에서 프로덕션으로 트래픽을 점진적으로 이동

**정답: B**
**해설:** Auto Swap을 사용할 수 없는 Linux App Service의 경우, CI/CD 파이프라인(Azure Pipelines 또는 GitHub Actions)에 배포가 완료된 후 슬롯 교체 단계를 추가하는 것이 권장 방법입니다. 파이프라인은 배포 성공 후 `az webapp deployment slot swap --name AppName --resource-group RG --slot staging --target-slot production` 명령을 실행합니다.

---

### 문제 12
Azure NSG와 AWS Security Group의 동작을 비교하고 있습니다. 동료가 "AWS Security Group은 Stateless"라고 합니다. 이것이 맞습니까?

- A. 예 — AWS Security Group은 Stateless이며 인바운드와 아웃바운드 규칙이 모두 필요합니다
- B. 아니요 — AWS Security Group은 Azure NSG와 마찬가지로 Stateful입니다
- C. 예 — 서브넷 수준의 모든 클라우드 방화벽은 Stateless입니다
- D. 아니요 — AWS Security Group은 Stateful이지만 Azure NSG는 Stateless입니다

**정답: B**
**해설:** AWS Security Group은 Azure NSG와 마찬가지로 Stateful입니다. 두 서비스 모두 인바운드 연결이 허용되면 명시적 아웃바운드 규칙 없이 반환 트래픽이 자동으로 허용됩니다. Azure NSG와 AWS Security Group의 핵심 차이는, Azure NSG는 명시적 거부 규칙을 지원하지만 AWS Security Group은 허용 규칙만 지원(명시되지 않은 모든 것은 묵시적 거부)한다는 점입니다.

---

## 2) 스토리지, SAS 및 수명 주기 관리

### 문제 13
외부 파트너에게 블로브 컨테이너에 대한 시간 제한 접근을 제공해야 합니다. 접근 기간이 종료되면 스토리지 계정 키를 재생성하지 않고 즉시 접근을 취소할 수 있어야 합니다. 어떤 SAS 유형을 사용해야 합니까?

- A. 만료 시간이 있는 Account SAS
- B. User Delegation SAS
- C. 저장된 액세스 정책(Stored Access Policy)이 있는 Service SAS
- D. 고정 만료 시간만 있는 Service SAS

**정답: C**
**해설:** 저장된 액세스 정책이 있는 Service SAS는 스토리지 계정 키를 재생성하지 않고도 해당 정책을 삭제하거나 수정함으로써 즉시 취소할 수 있습니다. 고정 만료 시간만 있는 Service SAS는 키를 재생성하지 않고는 조기 취소가 불가능합니다. User Delegation SAS는 Entra ID 자격 증명을 사용하지만 만료 전 취소에는 사용자의 토큰을 취소해야 합니다.

---

### 문제 14
조직에서 Azure Storage 계정이 Microsoft Entra ID 기반 인증만 사용해야 합니다. 스토리지 계정 액세스 키 사용은 완전히 차단해야 합니다. User Delegation SAS는 계속 작동해야 합니다. 무엇을 구성해야 합니까?

- A. 스토리지 계정의 모든 RBAC 역할 할당 제거
- B. 두 액세스 키를 재생성하고 공유하지 않음
- C. 공유 키 액세스 비활성화 (Allow shared key access = Disabled)
- D. 스토리지 계정에 ReadOnly 잠금 설정

**정답: C**
**해설:** "공유 키 액세스 허용"을 비활성화하면 Service SAS 및 Account SAS를 포함한 모든 공유 키 인증이 차단됩니다. User Delegation SAS는 계정 키가 아닌 Entra ID 자격 증명으로 서명되므로 공유 키 액세스를 비활성화해도 계속 사용할 수 있습니다. 이로써 Entra ID가 유일한 인증 경로로 적용되면서 User Delegation SAS 기능은 유지됩니다.

---

### 문제 15
Azure 포털에서 스토리지 계정에 키 만료 정책을 구성하려 합니다. "Set rotation reminder" 버튼이 회색으로 비활성화되어 있습니다. 가장 가능성 높은 이유는 무엇입니까?

- A. 스토리지 계정이 지역 중복 복제(GRS)를 사용하고 있습니다
- B. 스토리지 계정 방화벽이 포털 관리 접근을 차단하고 있습니다
- C. 스토리지 액세스 키에 생성 타임스탬프가 기록되어 있지 않습니다
- D. 스토리지 계정이 포털 사용자와 다른 리전에 있습니다

**정답: C**
**해설:** 키 만료 정책은 각 액세스 키의 keyCreationTime 속성을 사용하여 만료 시기를 계산합니다. 이 기능이 도입되기 전에 생성된 키에는 생성 타임스탬프가 없습니다. 키를 재생성하면 Azure가 새 키에 생성 타임스탬프를 기록하여 교체 알림 기능이 활성화됩니다.

---

### 문제 16
스토리지 계정의 키 만료 정책에 대한 "Set rotation reminder" 버튼이 Azure 포털에서 회색으로 비활성화되어 있습니다. 이 구성을 활성화하려면 어떤 조치를 취해야 합니까?

- A. 스토리지 계정에서 블로브 일시 삭제 활성화
- B. 스토리지 계정 액세스 키 재생성
- C. 스토리지 계정에 Key Vault 관리자 역할 할당
- D. 컨테이너에서 블로브 버전 관리 활성화

**정답: B**
**해설:** 스토리지 계정 액세스 키를 재생성하면 Azure가 새 키에 keyCreationTime 속성을 할당합니다. 키 만료 정책은 이 타임스탬프를 사용하여 교체 알림을 트리거하는 시기를 결정합니다. 재생성 후 "Set rotation reminder" 버튼이 활성화됩니다.

---

### 문제 17
Azure Storage 계정의 Archive 계층에 블로브가 저장되어 있습니다. 긴급한 비즈니스 요구 사항으로 인해 이 블로브에 최대한 빨리 접근해야 합니다. 각 블로브의 크기는 10GB 미만입니다. 어떤 리하이드레이션 옵션을 선택해야 합니까?

- A. 표준 우선순위 리하이드레이션 (최대 15시간 소요될 수 있음)
- B. 높은 우선순위 리하이드레이션
- C. 온라인 계층 변경을 통한 블로브 복사
- D. 계층 변경 작업이 있는 수명 주기 관리 규칙

**정답: B**
**해설:** Archive 계층의 블로브는 읽기 전에 Hot 또는 Cool 계층으로 리하이드레이션해야 합니다. 높은 우선순위 리하이드레이션은 표준 우선순위 요청보다 먼저 처리되며, 10GB 미만의 개체에 대해 일반적으로 1시간 이내에 완료됩니다. 속도가 최우선 요건인 경우 비용이 더 높더라도 높은 우선순위 리하이드레이션이 올바른 선택입니다.

---

### 문제 18
Azure Storage 계정의 진단 설정을 구성하여 리소스 로그를 Log Analytics 작업 영역으로 전송하려 합니다. 개별 로그 범주를 선택하지 않고 범주 그룹을 사용하여 수집을 단순화하려 합니다. 사용 가능한 두 가지 범주 그룹은 무엇입니까?

- A. allLogs와 diagnosticLogs
- B. allLogs와 auditLogs
- C. resourceLogs와 securityLogs
- D. auditLogs와 performanceLogs

**정답: B**
**해설:** Azure 진단 설정은 스토리지 계정에 대해 두 가지 사전 정의된 범주 그룹을 지원합니다. "allLogs"(모든 리소스 로그 범주 수집)와 "auditLogs"(보안 및 접근 감사 관련 로그만 수집)입니다. 범주 그룹을 사용하면 개별 로그 범주를 수동으로 선택할 필요가 없으며 새 범주가 추가될 때 자동으로 포함됩니다.

---

### 문제 19
블로브를 Cool 계층에서 Cold 계층으로 이동하는 수명 주기 관리 정책을 만들고 있습니다. 블로브가 최근 Hot에서 Cool로 전환되었습니다. 조기 삭제 요금 없이 Cold 계층으로 이동하기 전에 블로브가 Cool 계층에 최소 몇 일 있어야 합니까?

- A. 7일
- B. 14일
- C. 30일
- D. 60일

**정답: C**
**해설:** Cool 계층의 최소 스토리지 기간은 30일입니다. 블로브를 Cool 계층에서 30일 이전에 Cold 계층으로 이동하면 나머지 기간에 대해 조기 삭제 요금이 부과됩니다. Cool에서 Cold로의 전환을 대상으로 하는 수명 주기 관리 규칙은 이 30일 최소 기간을 고려해야 합니다.

---

### 문제 20
마지막 수정 시간 대신 마지막 접근 시간을 기준으로 블로브를 이동하는 수명 주기 관리 정책을 구성해야 합니다. `daysAfterLastAccessTimeGreaterThan` 조건을 사용하기 전에 활성화해야 하는 전제 조건은 무엇입니까?

- A. 블로브 버전 관리 활성화
- B. 블로브 일시 삭제 활성화
- C. 마지막 접근 시간 추적 활성화
- D. 변경 피드 활성화

**정답: C**
**해설:** 마지막 접근 시간 추적은 수명 주기 관리 정책에서 `daysAfterLastAccessTimeGreaterThan` 조건을 사용하기 전에 명시적으로 활성화해야 하는 스토리지 계정 기능입니다. 이 기능 없이는 Azure가 블로브의 마지막 접근 시간을 기록하지 않아 조건이 평가할 데이터가 없습니다. 마지막 접근 시간 추적을 활성화하면 약간의 성능 오버헤드가 발생할 수 있습니다.

---

## 3) 컴퓨팅 및 App Service

### 문제 21
두 개의 컨테이너(주 애플리케이션 컨테이너와 사이드카 로깅 컨테이너)가 동시에 실행되며 동일한 네트워크 네임스페이스를 공유하는 ACI(Azure Container Instances) 그룹을 배포해야 합니다. 컨테이너 그룹에서 어떤 OS 유형을 선택해야 합니까?

- A. Windows
- B. Windows 또는 Linux 모두 가능
- C. Linux
- D. Windows Server Core만 가능

**정답: C**
**해설:** ACI 다중 컨테이너 그룹(여러 컨테이너가 동일한 수명 주기, 네트워크 및 스토리지 리소스를 공유하는 사이드카 패턴에 필요)은 Linux에서만 완전히 지원됩니다. Windows ACI 컨테이너 그룹은 다중 컨테이너 지원이 매우 제한적이며 사이드카 시나리오에는 권장되지 않습니다. 다중 컨테이너 ACI 그룹을 배포할 때는 항상 Linux를 사용하십시오.

---

### 문제 22
Azure에서 실행 중인 Windows VM이 있습니다. 임시 디스크와 디스크 캐시를 포함한 모든 미사용 데이터를 암호화해야 하며, VM 컴퓨팅 리소스를 암호화 프로세스에 사용하지 않아야 합니다. 어떤 암호화 솔루션을 사용해야 합니까?

- A. BitLocker를 사용하는 Azure Disk Encryption (ADE)
- B. 호스트에서의 암호화 (Encryption at host)
- C. 고객 관리 키를 사용하는 Storage Service Encryption (SSE)
- D. 독립 실행형 BitLocker 드라이브 암호화

**정답: B**
**해설:** 호스트에서의 암호화는 하이퍼바이저 계층에서 암호화를 수행하며, Azure Disk Encryption(BitLocker 기반)이 일관되게 커버하지 못할 수 있는 임시 디스크와 디스크 캐시를 포함합니다. 암호화가 VM 내부가 아닌 물리적 호스트 하드웨어에서 이루어지므로 VM CPU 리소스를 소비하지 않습니다.

---

### 문제 23
Custom Script Extension이 포함된 ARM 템플릿을 사용하여 Windows VM을 배포했습니다. 배포 후 확장 상태가 "실패"를 표시합니다. VM Agent 상태는 정상입니다. VM 서브넷 NSG에는 모든 아웃바운드를 차단하는 사용자 지정 규칙이 있습니다. 실패의 가장 가능성 높은 원인은 무엇입니까?

- A. 스크립트 스토리지 계정이 다른 Azure 리전에 있습니다
- B. VM이 Azure 플랫폼 IP 168.63.129.16에 접근할 수 없습니다
- C. VM Agent 버전이 오래되었습니다
- D. Custom Script Extension은 ARM 템플릿과 함께 지원되지 않습니다

**정답: B**
**해설:** Azure VM 확장은 플랫폼 IP 168.63.129.16을 통해 Azure의 관리 플레인과 통신합니다. 모든 아웃바운드를 차단하는 NSG 규칙은 이 통신을 막아 확장 핸들러가 스크립트를 다운로드하고 Azure에 실행 상태를 보고하는 것을 방지합니다. VM Agent가 정상으로 표시되는 것은 에이전트 프로세스가 실행 중임을 나타내지만, 이것이 확장의 Azure 제어 플레인 접근을 보장하지는 않습니다.

---

### 문제 24
Windows VM의 Custom Script Extension이 배포 스크립트를 실행하다가 중간에 실패했습니다. 오류 출력을 포함한 자세한 실행 로그를 확인해야 합니다. 어디에서 찾아야 합니까?

- A. Log Analytics 작업 영역의 Azure Monitor 로그
- B. Windows 이벤트 뷰어 (응용 프로그램 로그)
- C. C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\
- D. C:\Program Files\Microsoft Azure\Logs\

**정답: C**
**해설:** Windows VM의 Custom Script Extension 실행 로그는 `C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\` 경로에 저장됩니다. 이 디렉터리에는 다운로드 시도, 스크립트 실행 출력 및 오류 메시지에 대한 자세한 로그가 포함되어 있습니다. 다운로드된 스크립트 파일 자체는 `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\` 아래에 저장됩니다.

---

### 문제 25
여러 백엔드 인스턴스가 있는 App Service에서 웹 애플리케이션이 실행되고 있습니다. 사용자가 세션 내내 동일한 백엔드 인스턴스로 라우팅되어야 합니다(세션 지속성). 프론트엔드는 Application Gateway를 사용합니다. 어떤 기능을 활성화해야 합니까?

- A. Application Gateway의 URL 경로 기반 라우팅
- B. Application Gateway의 쿠키 기반 선호도 (세션 지속성)
- C. Azure Load Balancer의 원본 IP 선호도
- D. Application Gateway의 WebSocket 지원

**정답: B**
**해설:** Application Gateway의 쿠키 기반 선호도는 동일한 클라이언트 세션의 요청이 항상 동일한 백엔드 인스턴스로 라우팅되도록 합니다. 첫 번째 응답에서 게이트웨이가 ARRAffinity 쿠키를 설정하고, 이 쿠키를 가진 후속 요청은 동일한 서버로 전달됩니다. 원본 IP 선호도는 Azure Load Balancer(L4) 개념으로 Application Gateway와 혼동해서는 안 됩니다.

---

### 문제 26
현재 Free(F1) 계층 App Service에서 웹 애플리케이션이 배포되어 있습니다. 애플리케이션에 사용자 지정 도메인 매핑과 TLS/SSL 인증서 바인딩이 필요합니다. 업그레이드해야 하는 최소 App Service Plan 계층은 무엇입니까?

- A. Shared (D1)
- B. Basic (B1)
- C. Standard (S1)
- D. Premium (P1v2)

**정답: B**
**해설:** Free(F1) 및 Shared(D1) 계층은 사용자 지정 도메인 매핑이나 SSL/TLS 인증서 바인딩을 지원하지 않습니다. Basic(B1) 계층은 두 기능 모두를 지원하는 최소 계층입니다. Standard 및 Premium 계층도 이러한 기능을 지원하지만 비용이 더 높으므로, 비용 최소화가 요건일 때 Basic이 올바른 답입니다.

---

### 문제 27
Azure SQL Database(vCore, 범용 계층)에서 사용자가 중요한 데이터를 실수로 삭제했습니다. 삭제 10분 전 시점으로 데이터베이스를 복원해야 합니다. 관리 작업을 최소화해야 합니다. 무엇을 해야 합니까?

- A. 가장 최근의 수동 백업에서 복원
- B. 다른 리전의 새 데이터베이스에 지역 복원 사용
- C. 목표 타임스탬프로 특정 시점 복원(PITR) 사용
- D. BACPAC 내보내기 후 필터링된 데이터 세트로 재가져오기

**정답: C**
**해설:** Azure SQL Database는 전체, 차등, 트랜잭션 로그 백업을 자동으로 생성하고 백업 보존 기간 내의 모든 시점으로의 특정 시점 복원(PITR)을 지원합니다. PITR은 목표 타임스탬프를 선택하고 복원을 시작하기만 하면 됩니다. 수동 백업 관리가 필요 없으므로 실수로 삭제된 데이터 복구를 위한 최소 노력 솔루션입니다.

---

### 문제 28
VM에 두 가지 조건이 AND로 결합된 메트릭 경고 규칙을 만들었습니다: CPU 사용률 90% 초과 AND 사용 가능한 메모리 바이트 1GB 미만. CPU가 오전 10시 01분에 급등하고 10시 02분에 정상화됩니다. 메모리는 10시 04분에 1GB 아래로 떨어집니다. 경고가 발생합니까?

- A. 예 — 두 조건 모두 평가 기간 내 어느 시점에서든 참이었습니다
- B. 아니요 — 두 조건이 동일한 평가 시간 슬라이스에서 동시에 참이어야 합니다
- C. 예 — AND 조건이 있는 메트릭 경고는 평가 시간에 OR 로직을 사용합니다
- D. 아니요 — 메트릭 경고는 메모리를 조건으로 지원하지 않습니다

**정답: B**
**해설:** 여러 AND 조건이 있는 Azure 메트릭 경고 규칙은 모든 조건이 동일한 평가 시간 슬라이스에서 동시에 참이어야 합니다. CPU가 10:01에 급등하지만 메모리가 10:04에 떨어지기 전에 회복되면, 두 조건이 동시에 참인 순간이 없으므로 경고가 발생하지 않습니다. "평가 기간 내 각 조건이 한 번이라도 충족"하려면 평가 창에 Max(CPU용), Min(메모리용)과 같은 집계 유형을 사용해야 합니다.

---

## 4) ARM 템플릿 및 Bicep

### 문제 29
여러 리소스를 배포하는 ARM 템플릿이 있습니다. 배포를 실행하기 전에 리소스를 생성하지 않고 템플릿 구문과 매개변수 유효성만 확인하려 합니다. 어떤 명령을 실행해야 합니까?

- A. az deployment group what-if
- B. az deployment group create --dry-run
- C. az deployment group validate
- D. az deployment group check

**정답: C**
**해설:** `az deployment group validate`는 리소스를 생성하지 않고 ARM 템플릿 구문, 매개변수 유효성 및 기본 배포 가능성 검사를 수행합니다. `az deployment group what-if`도 리소스를 생성하지 않지만 주된 목적은 변경 사항 미리 보기이며 단순 구문 유효성 검사를 넘어서는 것입니다.

---

### 문제 30
리소스 그룹에 ARM 템플릿을 배포할 계획입니다. 배포 전에 생성, 수정 또는 삭제될 리소스에 대한 자세한 미리 보기가 필요합니다. 실제 변경은 적용되지 않아야 합니다. 무엇을 사용해야 합니까?

- A. az deployment group validate
- B. az deployment group create --mode Incremental
- C. az deployment group what-if
- D. az deployment group create --no-wait

**정답: C**
**해설:** ARM What-if 작업(`az deployment group what-if`)은 배포를 시뮬레이션하고 모든 리소스 변경 사항을 보여줍니다: `+`는 생성, `~`는 수정, `-`는 삭제입니다. 실제 변경은 환경에 적용되지 않습니다. `validate`는 구문만 확인하고 리소스 영향을 보고하지 않습니다.

---

### 문제 31
모듈식 배포를 위한 연결된 템플릿이 있는 ARM 템플릿이 있습니다. 연결된 템플릿이 지원하는 배포 모드는 무엇입니까?

- A. Complete 모드만
- B. Incremental 모드만
- C. Incremental 및 Complete 모드 모두
- D. 부모 템플릿과 동일한 모드

**정답: B**
**해설:** ARM 연결 템플릿(`templateLink`가 있는 `Microsoft.Resources/deployments`를 통해 호출)은 Incremental 배포 모드만 지원합니다. 템플릿에 없는 리소스 그룹의 리소스를 삭제하는 Complete 모드는 연결 템플릿에서 지원되지 않습니다. 부모 템플릿은 어느 모드든 사용할 수 있지만, 그 안의 연결 템플릿은 항상 Incremental입니다.

---

### 문제 32
Bicep 템플릿을 작성하고 있습니다. 부울 매개변수 `deployStorage`가 `true`일 때만 스토리지 계정 리소스를 배포해야 합니다. 올바른 Bicep 구문은 무엇입니까?

- A. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = condition deployStorage {...}`
- B. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = if (deployStorage) {...}`
- C. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' when (deployStorage) {...}`
- D. `resource sa 'Microsoft.Storage/storageAccounts@2023-01-01' = [if (deployStorage) then {...}]`

**정답: B**
**해설:** Bicep에서 조건부 리소스 배포는 `= if (조건) { ... }` 구문을 사용합니다. ARM JSON 템플릿에서의 동등한 표현은 ARM 표현식으로 설정된 `"condition"` 속성을 사용하는 것입니다. Bicep의 구문이 ARM JSON보다 더 읽기 쉽습니다. 조건이 false이면 리소스가 배포되지 않으며, 출력에서 이를 참조할 때는 null 안전 삼항 표현식을 사용해야 합니다.

---

### 문제 33
ARM JSON 템플릿에서 `"condition"` 속성을 사용하여 조건부 리소스를 정의합니다. Bicep 표현식 `if (deployStorage)`의 ARM JSON 동등 표현은 무엇입니까?

- A. `"condition": "deployStorage"`
- B. `"condition": "[parameters('deployStorage')]"`
- C. `"condition": "[equals(parameters('deployStorage'), true())]"`
- D. B와 C 모두 유효합니다

**정답: D**
**해설:** ARM JSON에서는 조건 표현식에 부울 매개변수를 직접 사용할 수 있습니다. `[parameters('deployStorage')]`는 매개변수가 직접 평가되는 부울 유형일 때 작동합니다. `[equals(parameters('deployStorage'), true())]`는 더 명시적인 형태입니다. `deployStorage`가 부울 매개변수일 때 두 형태 모두 유효하고 기능적으로 동일합니다.

---

### 문제 34
JSON 역할 정의 파일을 사용하여 새 Azure 사용자 지정 RBAC 역할을 만들고 있습니다. JSON에서 `null`로 설정해야 하는 속성은 무엇입니까?

- A. AssignableScopes
- B. IsCustom
- C. Id
- D. Description

**정답: C**
**해설:** 새 사용자 지정 RBAC 역할을 만들 때 `Id` 속성은 JSON 정의에서 `null`로 설정해야 합니다. Azure가 역할에 대한 고유 GUID를 자동으로 생성합니다. `AssignableScopes`는 최소 하나의 유효한 범위와 함께 명시적으로 제공해야 합니다. `IsCustom`은 `true`여야 합니다. null이 아닌 값으로 설정하면 기존 역할 정의의 업데이트를 나타냅니다.

---

### 문제 35
스토리지 계정에 저장된 외부 ARM 템플릿을 부모 ARM 템플릿에서 모듈식 배포로 호출하려 합니다. 부모 템플릿에서 어떤 리소스 유형을 선언해야 합니까?

- A. Microsoft.Resources/linkedDeployments
- B. Microsoft.Template/deployments
- C. Microsoft.Resources/deployments
- D. Microsoft.ARM/templateLinks

**정답: C**
**해설:** 연결 ARM 템플릿은 부모 템플릿에서 `Microsoft.Resources/deployments` 리소스 유형을 사용하고, 외부 템플릿 파일의 URI를 지정하는 `templateLink` 속성을 통해 호출합니다. 이것이 모듈식 ARM 배포의 표준 메커니즘입니다. 연결 템플릿은 Incremental 모드로만 실행됩니다.

---

## 5) ID, RBAC 및 거버넌스

### 문제 36
User1에게 스토리지 계정에 두 개의 역할이 할당되어 있습니다: (1) Actions에 `Microsoft.Storage/*`가 있지만 NotActions에 `Microsoft.Storage/storageAccounts/delete`가 있는 사용자 지정 역할, (2) 삭제 권한이 포함된 기본 제공 "Storage Account Contributor" 역할. User1이 스토리지 계정을 삭제할 수 있습니까?

- A. 아니요 — 한 역할의 NotActions가 다른 모든 역할 할당을 재정의합니다
- B. 예 — Azure RBAC는 가산적(Additive)입니다. 모든 역할의 권한이 결합되며 NotActions는 속한 특정 역할에만 영향을 미칩니다
- C. 아니요 — 사용자 지정 역할 NotActions는 항상 기본 제공 역할보다 우선합니다
- D. 예 — 단, User1이 구독 수준에서 Owner 할당도 있을 때만 해당됩니다

**정답: B**
**해설:** Azure RBAC는 가산적 모델을 사용합니다. 유효 권한은 모든 역할 할당의 합집합입니다. NotActions는 해당 역할 정의에서만 권한을 제외하며, 다른 역할이 부여한 권한을 취소하거나 무효화하지 않습니다. Storage Account Contributor 역할이 삭제 권한을 별도로 부여하므로, User1은 사용자 지정 역할의 NotActions 제외에도 불구하고 스토리지 계정을 삭제할 수 있습니다.

---

### 문제 37
Azure RBAC 역할 정의의 `NotActions`와 Deny Assignment의 주요 차이점은 무엇입니까?

- A. 기능적으로 동일합니다 — 둘 다 모든 역할에서 지정된 권한을 차단합니다
- B. NotActions는 다른 역할에 의해 재정의될 수 있는 역할 수준 제외입니다. Deny Assignment는 역할 할당에 관계없이 권한을 차단하는 시스템 수준 재정의입니다
- C. Deny Assignment는 더 이상 사용되지 않으며 NotActions가 현재 권장 방법입니다
- D. NotActions는 구독 범위에 적용되고 Deny Assignment는 리소스 그룹 범위에만 적용됩니다

**정답: B**
**해설:** `NotActions`는 해당 역할 정의가 있는 역할 내에서만 권한을 제거합니다. 다른 역할 할당이 동일한 권한을 부여하면 그 권한이 적용됩니다(RBAC는 가산적). Deny Assignment는 시스템 수준에서 권한을 명시적으로 차단하며 모든 역할 할당을 재정의합니다. 여러 역할이 차단된 권한을 부여하더라도 Deny Assignment가 모두 막습니다.

---

### 문제 38
Microsoft Entra 테넌트에서 SSPR이 구성되어 있습니다: 필요한 방법 수 2개, 사용 가능한 방법: 모바일 앱 알림, 모바일 앱 코드, 이메일, 휴대폰. User1은 SSPR 인증 방법 하나만 등록했습니다. User1이 SSPR을 통해 암호를 재설정하려 하면 어떻게 됩니까?

- A. SSPR이 등록된 하나의 방법을 사용하여 재설정을 허용합니다
- B. User1은 자동으로 헬프 데스크로 리디렉션됩니다
- C. User1은 필요한 것보다 적은 방법이 등록되어 있어 암호 재설정을 완료할 수 없습니다
- D. SSPR이 등록된 방법으로 임시 암호를 전송합니다

**정답: C**
**해설:** SSPR은 재설정 시 정책에 지정된 방법 수(이 경우 2개)를 사용하여 사용자 신원을 확인합니다. 사용자가 필요한 수보다 적은 방법을 등록한 경우, SSPR 흐름을 완료할 수 없으며 관리자에게 문의하라는 오류가 표시됩니다. 정책 확인은 재설정 시도 시에 발생합니다.

---

### 문제 39
Entra 관리 센터에서 CSV 파일을 사용하여 Microsoft Entra ID에서 사용자를 대량 생성하고 있습니다. CSV 템플릿에서 필수 열 4개는 무엇입니까?

- A. 이름, 사용자 계정 이름, 부서, 직책
- B. 이름, 사용자 계정 이름, 초기 암호, 로그인 차단
- C. 이름, 이메일, 암호, 관리자
- D. 사용자 계정 이름, 이름, 성, 부서

**정답: B**
**해설:** Entra 관리 센터 CSV 템플릿에서 사용자 대량 생성에는 4개의 필수 열이 있습니다: 이름(표시 이름), 사용자 계정 이름(UPN/로그인 ID), 초기 암호, 로그인 차단(예/아니요). 초기 암호가 필요한 이유는 CSV 업로드가 완전히 프로비저닝된 계정을 생성하기 때문입니다. 로그인 차단은 생성 후 계정의 활성화 여부를 결정합니다.

---

### 문제 40
새로 생성된 모든 Azure 구독이 루트 관리 그룹 대신 MG-Onboarding이라는 관리 그룹에 자동으로 배치되도록 구성해야 합니다. 기본 관리 그룹 설정을 변경하는 데 필요한 최소 권한을 부여하는 기본 제공 역할은 무엇입니까?

- A. 루트 관리 그룹의 Owner
- B. Microsoft Entra ID의 전역 관리자
- C. Hierarchy Settings Administrator
- D. Management Group Contributor

**정답: C**
**해설:** "Hierarchy Settings Administrator"는 새 구독의 기본 관리 그룹을 변경하는 데 필요한 `Microsoft.Management/managementgroups/settings/write` 권한을 부여하는 기본 제공 역할입니다. 이 역할은 Owner 또는 전역 관리자보다 더 좁은 범위이며, 계층 구조 구성 작업에 대한 최소 권한 원칙을 따릅니다.

---

### 문제 41
Azure 루트 관리 그룹에 관해 올바른 설명은 무엇입니까?

- A. 각 구독은 자체 루트 관리 그룹을 가질 수 있습니다
- B. 루트 관리 그룹은 모든 자식 리소스가 제거되면 삭제할 수 있습니다
- C. 테넌트의 모든 Azure 구독과 관리 그룹은 궁극적으로 루트 관리 그룹 아래에 있습니다
- D. 루트 관리 그룹은 각 테넌트에 대해 수동으로 생성해야 합니다

**정답: C**
**해설:** 루트 관리 그룹은 모든 Azure AD 테넌트에 대해 자동으로 생성되며 관리 그룹 계층 구조의 최상위를 나타냅니다. 모든 구독과 중첩된 관리 그룹은 궁극적으로 루트 관리 그룹 아래에 있습니다. 루트 관리 그룹은 삭제하거나 이동할 수 없으며, 테넌트당 정확히 하나만 있습니다.

---

### 문제 42
Azure 사용자 지정 RBAC 역할을 구축하고 있습니다. 역할은 스토리지 계정 메타데이터 읽기(관리 플레인)와 블로브 데이터 읽기/쓰기(데이터 플레인)를 허용해야 합니다. 역할 정의 JSON에서 이 권한을 정의하는 데 어떤 속성을 사용해야 합니까?

- A. 두 권한 모두 Actions에 포함
- B. Actions에 스토리지 계정 메타데이터; DataActions에 블로브 읽기/쓰기
- C. 두 권한 모두 DataActions에 포함
- D. NotActions에 스토리지 메타데이터; DataActions에 블로브 액세스

**정답: B**
**해설:** Azure RBAC는 관리 플레인과 데이터 플레인 권한을 분리합니다. `Actions`는 ARM 관리 플레인 작업(스토리지 계정 속성, 구성, ARM 메타데이터 읽기 등)을 커버합니다. `DataActions`는 직접 데이터 플레인 작업(블로브 데이터 읽기 또는 쓰기 등)을 커버합니다. 이들을 혼합하거나 `Actions`/`DataActions` 대신 `NotActions`를 사용하면 잘못된 역할 동작이 발생합니다.

---

### 문제 43
Azure Cost Management 예산을 구성하고 있으며 예산이 매 3개월마다 자동으로 초기화되어야 합니다. 어떤 시간 단위(Time Grain) 값을 선택해야 합니까?

- A. P1M (월별)
- B. P3M (분기별)
- C. P1Y (연간)
- D. P90D (90일 기간)

**정답: B**
**해설:** Azure Cost Management 예산은 ISO 8601 기간 표기법을 사용합니다: P1M = 1개월(월별), P3M = 3개월(분기별), P1Y = 1년(연간). 매 3개월마다 초기화되는 예산의 경우 올바른 시간 단위 값은 P3M(분기별)입니다.

---

### 문제 44
사용자 특성(예: 부서 = "엔지니어링")을 기반으로 멤버십이 자동으로 유지되는 Microsoft Entra ID 그룹을 만들어야 합니다. 이 기능에 필요한 라이선스는 무엇입니까?

- A. Microsoft 365 E3
- B. Entra ID Premium P1
- C. Entra ID Premium P2
- D. 추가 라이선스 불필요

**정답: B**
**해설:** 속성 규칙(부서, 직책, 위치 등)을 기반으로 멤버가 자동으로 추가 또는 제거되는 동적 멤버십 그룹은 최소 Entra ID Premium P1이 필요합니다. Entra ID Free 계층에서는 이 기능을 사용할 수 없습니다. P2도 요구 사항을 충족하지만 필요한 최소 라이선스는 아닙니다.

---

## 6) 백업, 모니터링 및 운영

### 문제 45
일관성 유형이 "충돌 일관성 스냅샷만(Only crash consistent snapshot)"으로 설정된 향상된 백업 정책을 사용하여 VM에 대한 Azure Backup을 구성했습니다. 이 백업 구성은 무엇을 보장합니까?

- A. 스냅샷 전에 모든 진행 중인 트랜잭션이 디스크에 커밋됩니다
- B. 정확한 순간의 디스크 상태가 캡처됩니다. 이는 갑작스러운 전원 차단과 동일하며 애플리케이션 정지(Quiescing)가 없습니다
- C. 모든 열린 파일이 플러시되고 파일 시스템이 일관된 상태입니다
- D. VSS를 통한 애플리케이션 수준 일관성이 적용됩니다

**정답: B**
**해설:** 충돌 일관성 스냅샷은 애플리케이션 정지, 메모리 플러시, 진행 중인 트랜잭션 커밋 없이 정확한 캡처 시점의 디스크 상태를 기록합니다. 이는 갑작스러운 전원 차단 후의 상태와 동일합니다. 이 방식은 빠르고 오버헤드가 적지만 복원 시 애플리케이션 수준 복구(예: SQL 트랜잭션 로그 재생)가 필요할 수 있습니다.

---

### 문제 46
SQL Server VM에 대한 Azure Backup 일관성 유형을 선택하고 있습니다. 데이터베이스는 복원 시 데이터 손실을 방지하기 위해 모든 트랜잭션이 완전히 커밋되고 백업에 기록될 것을 요구합니다. 어떤 일관성 유형을 사용해야 합니까?

- A. 충돌 일관성(Crash-consistent)
- B. 파일 일관성(File-consistent)
- C. 애플리케이션 일관성(Application-consistent)
- D. 디스크 일관성(Disk-consistent)

**정답: C**
**해설:** 애플리케이션 일관성 백업은 Windows에서 VSS(볼륨 섀도 복사 서비스)를, Linux에서 사전/사후 스크립트를 사용하여 애플리케이션을 정지시키고, 진행 중인 I/O를 플러시하고, 보류 중인 트랜잭션을 커밋한 후 스냅샷을 만듭니다. 이를 통해 트랜잭션 로그 재생이나 충돌 복구 없이 데이터베이스를 완전히 커밋된 상태로 복원할 수 있습니다.

---

### 문제 47
VM Insights가 활성화된 Azure VM이 있습니다. 모니터링 팀이 성능 카운터 데이터(CPU, 메모리, 디스크)와 종속성 맵 데이터를 쿼리해야 합니다. 이 데이터는 어떤 Azure 서비스에 저장됩니까?

- A. Azure Storage 계정 (테이블 스토리지)
- B. Azure Monitor 메트릭
- C. Log Analytics 작업 영역
- D. Application Insights

**정답: C**
**해설:** VM Insights는 수집된 모든 데이터(성능 카운터 및 프로세스/종속성 데이터)를 Log Analytics 작업 영역의 KQL 쿼리 가능한 테이블(`InsightsMetrics`, `VMConnection` 등)에 저장합니다. 이를 통해 모니터링 팀은 KQL 쿼리를 사용하고, 경고를 만들고, 이 데이터를 기반으로 Azure Monitor Workbooks를 구축할 수 있습니다.

---

### 문제 48
VM의 태그, 위치 및 리소스 유형을 포함한 여러 구독에 걸친 VM 인벤토리를 표시하는 Azure Monitor Workbook을 구축하고 있습니다. Workbook 쿼리에서 어떤 데이터 소스를 사용해야 합니까?

- A. Log Analytics
- B. Azure Resource Graph
- C. Application Insights
- D. Azure Monitor 메트릭

**정답: B**
**해설:** Azure Resource Graph는 대규모로 여러 구독, 여러 리소스 그룹에 걸친 리소스 메타데이터 쿼리를 위해 설계되었습니다. Azure Monitor Workbooks에서 Azure Resource Graph를 데이터 소스로 선택하면 리소스 속성, 태그, 위치, 구독 연결 및 기타 ARM 메타데이터에 대한 KQL 쿼리가 가능합니다. Log Analytics는 리소스 인벤토리 메타데이터가 아닌 로그 및 성능 데이터에 사용됩니다.

---

### 문제 49
App Service 웹앱이 Key Vault에서 비밀을 교체한 지 2시간이 지났음에도 이전 비밀 값을 계속 제공하고 있습니다. App Service는 버전을 지정하지 않은 Key Vault 참조를 사용합니다(항상 최신 버전을 가져와야 함). 이전 값이 계속 제공되는 가장 가능성 높은 이유는 무엇입니까?

- A. Key Vault 참조가 삭제된 버전을 가리키고 있습니다
- B. App Service는 Key Vault 비밀 값을 최대 24시간 동안 캐시합니다
- C. 비밀 교체 후 관리 ID가 Key Vault에 더 이상 접근할 수 없습니다
- D. 버전이 없는 Key Vault 참조는 항상 초기 버전에 고정됩니다

**정답: B**
**해설:** App Service Key Vault 참조는 API 호출을 줄이고 Key Vault 제한을 방지하기 위해 확인된 비밀 값을 캐시합니다. 이 캐시의 최대 수명은 24시간입니다. 비밀이 2시간 전에 교체되었다면 App Service는 여전히 캐시된 이전 값을 제공할 수 있습니다. App Service를 재시작하면 캐시된 모든 Key Vault 참조 값이 즉시 새로 고쳐집니다.

---

### 문제 50
Threat Intelligence가 "경고 및 거부(Alert and deny)"로 설정된 Azure Firewall에 대한 보안 감사에서 알려진 악성 IP에 대한 경고 로그 항목이 발견되었지만 첫 번째 운영 주간 동안 트래픽이 차단되지 않았습니다. 현재 Threat Intelligence 설정은 "경고 및 거부"로 확인되었습니다. 가장 가능성 높은 설명은 무엇입니까?

- A. Threat Intelligence는 사설 IP 범위로의 트래픽을 차단하지 않습니다
- B. 첫 번째 주 동안 Azure Firewall Threat Intelligence 피드가 아직 업데이트되지 않았습니다
- C. Azure Firewall의 기본 Threat Intelligence 모드는 "경고 전용(Alert only)"이며, 첫 주의 로그는 설정이 "경고 및 거부"로 변경되기 전의 기본 설정을 반영합니다
- D. 경고 및 거부는 차단이 효력을 발휘하기 전에 별도의 네트워크 규칙이 필요합니다

**정답: C**
**해설:** Azure Firewall의 기본 Threat Intelligence 모드는 "경고 전용(Alert only)"으로, 트래픽을 차단하지 않고 일치 항목을 기록합니다. 방화벽이 기본 설정 하에 배포되어 운영되다가 "경고 및 거부"로 변경된 경우, 그 초기 기간의 로그 항목은 차단 없이 경고만 표시됩니다. 현재 "경고 및 거부" 설정은 이후 트래픽을 올바르게 차단합니다.

---

*Document generated from AZ-104 course materials. Last updated: 2026-05-22*
