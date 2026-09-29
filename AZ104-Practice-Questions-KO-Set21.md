# AZ-104 예상 문제 (세트 21) — 한국어

## 1) Load Balancer, 헬스 프로브, 네트워크 트래픽

### 문제 1

NVA(Network Virtual Appliance)를 Azure Standard 내부 Load Balancer 뒤에 배포하려 합니다. NVA가 모든 포트에서 모든 유형의 트래픽을 동시에 검사해야 합니다. 로드 밸런싱 규칙을 어떻게 구성해야 합니까?

- A. Protocol: TCP, Port: 80
- B. Protocol: All, Port: 0
- C. Protocol: UDP, Port: 0
- D. Protocol: TCP, Port: 443

**정답: B**

**해설:** NVA가 모든 포트에서 모든 유형의 트래픽을 검사하려면 Azure Standard Load Balancer에서 Protocol: All, Port: 0으로 HA Ports 규칙을 구성해야 합니다. 이렇게 하면 모든 TCP 및 UDP 흐름을 모든 포트에서 동시에 부하 분산하는 High Availability Ports 규칙이 생성됩니다. HA Ports 규칙은 Standard SKU에서만 사용 가능하며 NVA 배포에 권장되는 구성입니다.

---

### 문제 2

세 개의 VM이 Standard 내부 Load Balancer의 백엔드 풀에 있습니다. 사용자들이 간헐적인 연결 문제를 보고합니다. 조사 결과 헬스 프로브가 실패하고 있음을 확인했습니다. NSG가 서브넷에 적용되어 있을 때, 헬스 프로브가 성공하려면 어느 IP 주소로부터의 인바운드 트래픽을 허용해야 합니까?

- A. 10.0.0.1
- B. 169.254.169.254
- C. 168.63.129.16
- D. 0.0.0.0/0

**정답: C**

**해설:** Azure Load Balancer 헬스 프로브는 Azure 플랫폼 특수 IP 주소인 168.63.129.16에서 발송됩니다. 이 IP는 헬스 프로브, VM Agent 통신, DNS 서비스 등 Azure 플랫폼 서비스에 사용됩니다. NSG가 168.63.129.16으로부터의 인바운드 트래픽을 차단하면 헬스 프로브가 실패하고, 해당 VM은 Load Balancer의 활성 백엔드 풀에서 제거됩니다. NSG 인바운드 규칙에 168.63.129.16에서의 트래픽을 허용하도록 추가해야 합니다.

---

### 문제 3

Custom Script Extension을 포함한 ARM 템플릿으로 VM을 배포합니다. VM Agent는 정상 실행 중입니다. NSG가 특정 IP 범위의 포트 443만 허용하고, 168.63.129.16은 허용 목록에 없습니다. Extension 실행이 계속 실패합니다. 가장 가능성 있는 원인은 무엇입니까?

- A. VM Agent 서비스가 실행되지 않음
- B. NSG가 아웃바운드 포트 80을 차단함
- C. NSG가 168.63.129.16으로의 아웃바운드 접근을 차단함
- D. Custom Script URL에 유효하지 않은 인증서가 있음

**정답: C**

**해설:** Azure VM Extension은 Azure 패브릭 컨트롤러를 통해 관리되며, 이 과정에서 플랫폼 IP 168.63.129.16과 통신합니다. Extension은 이 IP에 대한 아웃바운드 접근이 필요해야 Extension 패키지를 다운로드하고, 상태 업데이트를 전송하고, 실행 결과를 보고할 수 있습니다. VM Agent가 정상 실행 중이더라도 NSG가 168.63.129.16으로의 아웃바운드 트래픽을 차단하면 Extension 프로비저닝이 실패합니다. 168.63.129.16으로의 아웃바운드 트래픽을 허용하는 NSG 규칙을 추가해야 합니다.

---

### 문제 4

Microsoft.Storage Service Endpoint가 활성화된 서브넷이 있습니다. 서브넷의 VM들이 회사 승인된 storage-corp라는 스토리지 계정에만 접근하도록 제한하고, 다른 모든 Azure Storage 계정으로의 접근(데이터 유출 포함)을 차단해야 합니다. 무엇을 구성해야 합니까?

- A. 각 스토리지 계정에서 해당 서브넷을 허용하는 VNet 규칙 추가
- B. Azure Storage 서비스 태그에 대한 NSG 아웃바운드 거부 규칙 추가
- C. storage-corp를 허용 리소스로 지정하는 Service Endpoint Policy를 서브넷에 생성
- D. storage-corp에 대한 Private Endpoint 구성

**정답: C**

**해설:** Service Endpoint Policy를 사용하면 서브넷에서 Azure Storage로의 아웃바운드 트래픽을 특정 스토리지 계정으로만 제한할 수 있습니다. storage-corp를 참조하는 Service Endpoint Policy를 생성하면, 서브넷에서 Azure Storage로의 모든 트래픽이 해당 계정으로만 제한되어 다른 테넌트의 계정을 포함한 모든 다른 스토리지 계정으로의 접근이 차단됩니다. 스토리지 계정 VNet 규칙(옵션 A)은 특정 계정에 접근할 수 있는 서브넷을 제어하지만, 해당 서브넷에서 다른 계정으로 접근하는 것을 방지하지 않습니다.

---

### 문제 5

Azure Application Gateway v2 뒤에 상태 유지(stateful) 웹 애플리케이션이 있습니다. 사용자의 세션 상태가 특정 백엔드 서버에 저장됩니다. 장애 발생 후 일부 사용자가 다른 백엔드 서버로 리디렉션되어 세션 데이터를 잃습니다. 사용자가 항상 동일한 백엔드 서버에 도달하도록 세션 지속성을 구현하려면 어떤 기능을 활성화해야 합니까?

- A. 호스트 기반 라우팅으로 멀티사이트 리스너
- B. Cookie 기반 선호도 (ARRAffinity 쿠키)
- C. Connection Draining
- D. URL 경로 기반 라우팅

**정답: B**

**해설:** Application Gateway는 ARRAffinity 쿠키를 사용한 Cookie 기반 세션 선호도를 지원합니다. 이 기능이 활성화되면 게이트웨이는 첫 번째 HTTP 응답에 ARRAffinity 쿠키를 삽입합니다. 동일한 클라이언트의 이후 요청은 이 쿠키를 포함하고, 게이트웨이는 이 값을 사용하여 동일한 백엔드 서버로 요청을 일관되게 라우팅합니다. 이를 통해 애플리케이션 코드 변경 없이 상태 유지 웹 애플리케이션의 세션 지속성을 보장합니다.

---

### 문제 6

Azure Standard SKU Load Balancer의 프런트엔드 IP 주소가 있습니다. 하나의 Azure Availability Zone이 실패하더라도 프런트엔드 IP 주소가 계속 사용 가능하도록 해야 합니다. 프런트엔드 IP에 대해 무엇을 구성해야 합니까?

- A. Basic SKU 공용 IP 주소 할당
- B. Zone-redundant 프런트엔드 IP 주소 구성
- C. Availability Zone별로 별도의 Load Balancer 배포
- D. 로드 밸런싱 규칙에서 Floating IP 활성화

**정답: B**

**해설:** Standard SKU Azure Load Balancer는 지역의 모든 Availability Zone에서 자동으로 복제되는 Zone-redundant 프런트엔드 IP 주소를 지원합니다. 한 Zone이 실패해도 나머지 Zone을 통해 트래픽이 계속 제공됩니다. Zone-redundant 프런트엔드 IP는 프런트엔드 IP 할당 시 특정 Zone을 지정하지 않고 "Zone-redundant"를 선택하여 구성합니다. Basic SKU Load Balancer는 Availability Zone을 지원하지 않습니다.

---

### 문제 7

다른 Azure 구독의 네트워크 관리자가 storage1이라는 스토리지 계정을 대상으로 Private Endpoint를 생성했습니다. storage1의 Private Endpoint 연결 상태가 "Pending"으로 표시됩니다. 연결을 허용하려면 어떻게 해야 합니까?

- A. Private Endpoint의 IP에 대한 DNS 레코드 생성
- B. 네트워크 관리자에게 storage1에 대한 Contributor 권한 부여
- C. storage1의 네트워킹 설정에서 Private Endpoint 연결 승인
- D. 자체 구독에서 Private Endpoint를 다시 생성

**정답: C**

**해설:** 다른 구독에서 Private Endpoint를 생성한 경우 대상 리소스 소유자가 연결을 명시적으로 승인해야 합니다. storage1 > 네트워킹 > Private Endpoint 연결로 이동하여 대기 중인 연결을 선택하고 승인을 클릭합니다. 교차 구독 Private Endpoint 연결은 생성자가 대상 리소스에 대한 소유권이 없기 때문에 자동으로 승인되지 않습니다. 동일한 구독 내에서 생성자가 대상 리소스에 대해 Owner 또는 Contributor 권한을 가진 경우에만 자동 승인됩니다.

---

### 문제 8

storage1이라는 스토리지 계정의 Blob 서비스에 대한 Private Endpoint를 생성하고 "Private DNS 영역과 통합" 옵션을 활성화합니다. 어떤 Private DNS 영역 이름이 자동으로 생성됩니까?

- A. privatelink.storage.core.windows.net
- B. privatelink.blob.core.windows.net
- C. storage1.blob.privatelink.net
- D. blob.privatelink.azure.com

**정답: B**

**해설:** Azure Blob Storage에 대한 Private Endpoint를 생성하고 Private DNS 영역 통합을 활성화하면 Azure는 privatelink.blob.core.windows.net 영역을 자동으로 생성합니다. 이 영역에는 storage1.blob.core.windows.net을 Private Endpoint의 개인 IP 주소에 매핑하는 A 레코드가 추가됩니다. 각 스토리지 서비스에는 전용 DNS 영역이 있습니다: Blob → privatelink.blob.core.windows.net, File → privatelink.file.core.windows.net, Queue → privatelink.queue.core.windows.net.

---

## 2) ARM 템플릿, Bicep, 배포

### 문제 9

운영 구독에 배포할 ARM 템플릿이 있습니다. 실제 배포 전에 어떤 리소스가 생성, 수정, 삭제될지 미리 확인하되 실제 변경은 하지 않으려 합니다. 어떤 Azure CLI 명령을 사용해야 합니까?

- A. `az deployment group validate`
- B. `az deployment group create --mode Complete`
- C. `az deployment group what-if`
- D. `az deployment group export`

**정답: C**

**해설:** `az deployment group what-if` 명령은 실제 변경을 적용하지 않고 배포를 시뮬레이션하여 예상 영향을 보여줍니다. 생성될 리소스(+), 수정될 리소스(~), 삭제될 리소스(-)를 표시합니다. `az deployment group validate`는 템플릿 구문 및 매개변수 값만 검증하며 기존 리소스에 대한 영향을 보여주지 않습니다. what-if 작업은 사전 배포 영향 분석을 위해 특별히 설계되었습니다.

---

### 문제 10

Azure Blob Storage에 저장된 별도 템플릿 파일에서 네트워킹 리소스를 배포하는 Linked Template을 사용하는 ARM 템플릿이 있습니다. Linked Template에서 지원되는 배포 모드는 무엇입니까?

- A. Complete 모드만
- B. Incremental 모드만
- C. Complete 및 Incremental 모두
- D. Validate 모드만

**정답: B**

**해설:** ARM Linked Template(중첩 템플릿 포함)은 Incremental 배포 모드만 지원합니다. 템플릿에 없는 리소스를 삭제하는 Complete 모드는 Linked/중첩 배포에서 지원되지 않습니다. Incremental 모드에서 배포는 템플릿에 정의된 리소스를 추가하거나 업데이트하고, 템플릿에 없는 기존 리소스는 그대로 유지합니다. Linked Template 사용은 모듈식 ARM 템플릿 설계의 일반적인 패턴이며, 이러한 모든 배포는 Incremental 모드를 사용해야 합니다.

---

### 문제 11

Archive 액세스 계층에 여러 Blob이 저장되어 있습니다. 긴급한 비즈니스 요구 사항으로 5GB 미만의 Blob에 최대한 빠르게 접근해야 합니다. 무엇을 구성해야 합니까?

- A. Standard 우선순위로 Cool 계층으로 리하이드레이션
- B. High 우선순위로 Hot 계층으로 리하이드레이션
- C. Blob을 Hot 계층의 새 스토리지 계정으로 복사
- D. AzCopy를 사용하여 보관된 Blob을 직접 스트리밍

**정답: B**

**해설:** 보관된 Blob에 최대한 빠르게 접근을 복원하려면 High Priority 리하이드레이션을 사용하세요. 10GB 미만의 Blob의 경우 High Priority 리하이드레이션은 일반적으로 1시간 이내에 완료됩니다. Standard Priority 리하이드레이션은 1-15시간이 소요될 수 있습니다. 보관된 Blob은 직접 읽을 수 없으며 Hot 또는 Cool 계층으로 리하이드레이션 후에만 접근 가능합니다. High Priority 리하이드레이션은 Standard보다 비용이 높지만 가장 빠른 접근 복원을 제공합니다.

---

### 문제 12

Azure Availability Set AS1은 2개의 장애 도메인과 5개의 업데이트 도메인으로 구성되어 있으며, 업데이트 도메인에 고르게 분산된 10개의 VM을 포함합니다. Microsoft가 VM 재시작이 필요한 계획된 유지 관리를 시작합니다. 동시에 사용 불가능한 최대 VM 수는 얼마입니까?

- A. 1개
- B. 2개
- C. 5개
- D. 10개

**정답: B**

**해설:** 계획된 유지 관리 중에 Azure는 한 번에 하나의 업데이트 도메인을 처리합니다. 10개의 VM이 5개의 업데이트 도메인에 고르게 분산되면 각 업데이트 도메인에는 2개의 VM이 있습니다. 한 번에 하나의 업데이트 도메인만 오프라인이 되므로 최대 2개의 VM이 동시에 사용 불가능합니다. 장애 도메인은 물리적 하드웨어 오류 격리에 사용되며 계획된 유지 관리 일정에는 영향을 주지 않습니다.

---

### 문제 13

WebApp1이라는 Azure App Service 웹 앱이 Free (F1) 계층에서 실행 중입니다. 사용자 지정 도메인을 구성하고 TLS/SSL 인증서를 바인딩해야 합니다. 비용을 최소화하려 합니다. 어느 App Service 플랜 계층으로 업그레이드해야 합니까?

- A. Shared (D1)
- B. Basic (B1)
- C. Standard (S1)
- D. Premium (P1v2)

**정답: B**

**해설:** 사용자 지정 도메인 바인딩과 SSL/TLS 인증서 바인딩을 사용하려면 최소 Basic (B1) 계층 App Service 플랜이 필요합니다. Free (F1) 및 Shared (D1) 계층은 사용자 지정 도메인 또는 SSL 인증서를 지원하지 않습니다. Basic (B1)은 두 기능을 모두 가장 저렴한 비용으로 지원합니다. Standard (S1)도 이러한 기능을 지원하지만 자동 스케일링, Deployment Slot 등 추가 기능을 포함하여 비용이 더 높고 이 시나리오에서는 필요하지 않습니다.

---

### 문제 14

Block Blob과 Append Blob이 포함된 스토리지 계정이 있습니다. 수정된 이후 30일이 경과한 Blob을 Hot에서 Cool 계층으로 자동 전환하는 Lifecycle Management 정책을 생성합니다. 계층 구분 작업에서 어떤 Blob 유형이 영향을 받습니까?

- A. Block Blob과 Append Blob 모두
- B. Append Blob만
- C. Block Blob만
- D. Page Blob만

**정답: C**

**해설:** Azure Blob Storage Lifecycle Management 계층 규칙(Hot → Cool, Cool → Archive 등)은 Block Blob에만 적용됩니다. Append Blob과 Page Blob은 Lifecycle Management 정책의 계층 작업을 지원하지 않습니다. Append Blob은 로깅과 같은 순차적 쓰기 작업을 위해 설계되었으며 액세스 계층 지원이 없습니다. Lifecycle Management 삭제 규칙은 모든 Blob 유형에 적용될 수 있지만, 계층 규칙은 Block Blob에 한정됩니다.

---

## 3) ID, Entra ID, 액세스 관리

### 문제 15

사용자의 직함(job title) 속성을 기반으로 멤버십이 자동으로 관리되는 Microsoft Entra ID 그룹을 만들어야 합니다. 현재 테넌트에는 Microsoft Entra ID Free 라이선스가 있습니다. 동적 그룹 멤버십을 활성화하려면 가장 먼저 무엇을 해야 합니까?

- A. 동적 멤버십 규칙을 사용하여 그룹을 생성하고 조건을 정의
- B. Microsoft Entra ID Premium P1 또는 P2로 업그레이드
- C. Azure AD Connect 하이브리드 동기화 활성화
- D. 그룹 생성자에게 그룹 관리자 역할 할당

**정답: B**

**해설:** Microsoft Entra ID의 동적 그룹 멤버십에는 최소 Microsoft Entra ID Premium P1(또는 P2) 라이선스가 필요합니다. Free 라이선스는 동적 멤버십 기능을 포함하지 않습니다. `(user.jobTitle -eq "Manager")`와 같은 속성 기반 멤버십 규칙을 사용하는 동적 그룹을 생성하려면 먼저 테넌트 라이선스를 업그레이드해야 합니다. Premium 라이선스 없이는 그룹 생성 시 동적 멤버십 옵션이 표시되지 않습니다.

---

### 문제 16

SSPR(셀프 서비스 암호 재설정)을 다음 설정으로 구성합니다: 재설정에 필요한 방법 수: 2, 사용 가능한 방법: 모바일 앱 알림, 모바일 앱 코드, 이메일, 휴대폰. 사용자가 인증 방법을 하나만 등록한 상태에서 암호를 잊어버립니다. 사용자가 SSPR을 사용하여 암호를 재설정하려 할 때 어떻게 됩니까?

- A. 등록된 하나의 방법으로 암호를 재설정할 수 있음
- B. 계속 진행하기 전에 추가 방법을 등록하라는 메시지가 표시됨
- C. 암호를 재설정할 수 없으며 관리자에게 연락해야 함
- D. 사용자의 이메일로 SSPR 우회 링크가 전송됨

**정답: C**

**해설:** SSPR이 2개의 인증 방법을 요구하도록 구성된 경우, 1개의 방법만 등록한 사용자는 암호 재설정을 완료할 수 없습니다. 최소 등록 요구 사항이 충족되지 않기 때문에 SSPR이 진행되지 않습니다. 사용자는 암호를 수동으로 재설정하도록 관리자에게 연락하거나, 암호를 알고 있는 상태에서 임시 액세스 코드를 통해 추가 인증 방법을 먼저 등록해야 합니다.

---

### 문제 17

PowerShell을 사용하여 Microsoft Entra ID에서 500명의 사용자의 Department 속성을 업데이트해야 합니다. 현재 권장되는 모듈의 어떤 PowerShell 명령을 사용해야 합니까?

- A. Get-AzureADUser 및 Set-AzureADUser
- B. Get-MgUser 및 Update-MgUser
- C. Get-AzADUser 및 Update-AzADUser
- D. Get-EntraIDUser 및 Set-EntraIDUser

**정답: B**

**해설:** Microsoft Graph PowerShell SDK는 Microsoft Entra ID 관리에 현재 권장되는 모듈입니다. `Get-MgUser`를 사용하여 사용자 개체를 검색하고 `Update-MgUser`를 사용하여 Department와 같은 사용자 속성을 수정합니다. AzureAD PowerShell 모듈(Get-AzureADUser / Set-AzureADUser)은 더 이상 사용되지 않으며 중단될 예정입니다. Az 모듈의 Entra ID cmdlet은 제한된 기능을 제공합니다. Microsoft Graph PowerShell(Get-MgUser / Update-MgUser)이 지원되는 최신 대안입니다.

---

### 문제 18

storage1이라는 스토리지 계정에 CanNotDelete 리소스 잠금이 설정되어 있습니다. 사용자가 이 잠금을 제거해야 합니다. 리소스 잠금을 삭제하는 데 필요한 최소 RBAC 역할은 무엇입니까?

- A. Contributor
- B. Reader
- C. Storage Account Contributor
- D. Owner

**정답: D**

**해설:** 리소스 잠금을 생성하거나 삭제하려면 사용자에게 Microsoft.Authorization/locks/\* 권한이 있어야 합니다. 이 권한은 Owner 역할에 포함되어 있습니다. Contributor 역할에는 잠금 관리 권한이 포함되어 있지 않습니다. Contributor는 리소스를 관리할 수 있지만 잠금을 생성하거나 제거하거나 RBAC 할당을 관리할 수 없습니다. User Access Administrator도 이 권한을 가지지만, Owner가 AZ-104 시험의 표준 정답입니다.

---

### 문제 19

여러 리소스 그룹이 있는 Azure 구독이 있습니다. 개발자가 RG2에서만 가상 머신을 생성, 구성, 삭제해야 합니다. 최소 권한 원칙에 따라 필요한 최소 권한을 부여해야 합니다. 어떻게 해야 합니까?

- A. 구독 범위에서 Contributor 역할 할당
- B. RG2 범위에서 Owner 역할 할당
- C. RG2 범위에서 Virtual Machine Contributor 역할 할당
- D. RG2 범위에서 Contributor 역할 할당

**정답: C**

**해설:** Virtual Machine Contributor 역할은 VM을 생성, 관리, 삭제하는 데 필요한 권한을 부여하며, VM이 의존하는 가상 네트워크나 스토리지 계정(또는 RG2의 다른 리소스 유형)에 대한 접근을 부여하지 않습니다. 특별히 RG2 범위에 이 역할을 할당하면 개발자가 RG2 내의 VM만 관리할 수 있어 최소 권한 원칙을 충족합니다. RG2 범위의 Contributor는 RG2의 모든 리소스 유형을 관리하는 더 광범위한 권한을 부여하여 요구 사항을 초과합니다.

---

### 문제 20

User1은 구독 범위에서 Contributor 역할이 할당되어 있습니다. User1은 동일 구독 내의 리소스 그룹 RG1에 Reader 역할도 별도로 할당되어 있습니다. RG1에서 User1의 유효 권한은 무엇입니까?

- A. 리소스 그룹 범위에서 Reader 역할이 재정의되므로 Reader 권한만
- B. 충돌하는 역할이 서로 상쇄되므로 권한 없음
- C. 구독 범위에서 상속된 Contributor 권한
- D. 가장 최근에 할당된 역할이 적용됨

**정답: C**

**해설:** Azure RBAC는 가산적 방식으로 적용됩니다. 모든 범위에서 적용 가능한 모든 역할의 권한이 결합됩니다. 높은 범위(구독)에서 할당된 역할은 모든 하위 범위(리소스 그룹, 리소스)에 상속됩니다. 하위 범위(RG1)에서의 더 제한적인 역할(Reader)은 상위 범위의 광범위한 역할을 재정의하지 않습니다. 표준 RBAC 역할 할당에는 "거부" 개념이 없습니다(Azure Policy 거부 효과와 명시적 거부 할당은 다르게 작동합니다). User1의 RG1에서의 유효 권한은 구독 할당에서 상속된 Contributor입니다.

---

### 문제 21

여러 Azure 웹 애플리케이션이 동일한 Azure Storage 계정에 관리 ID를 사용하여 접근해야 합니다. 웹 앱이 배포되기 전에 RBAC 역할 할당을 사전 구성할 수도 있어야 합니다. 어떤 유형의 관리 ID를 사용해야 합니까?

- A. 시스템 할당 관리 ID
- B. 사용자 할당 관리 ID
- C. 클라이언트 비밀이 있는 서비스 주체
- D. 인증서가 있는 서비스 주체

**정답: B**

**해설:** 사용자 할당 관리 ID는 특정 Azure 서비스와 독립적으로 존재하는 독립 Azure 리소스입니다. 웹 앱이 배포되기 전에 생성할 수 있고, RBAC 역할 할당(예: 스토리지 계정에 Storage Blob Data Contributor)을 사전 구성한 후 배포 시 여러 웹 앱에 할당할 수 있습니다. 시스템 할당 관리 ID는 특정 리소스와 함께 생성되고 해당 리소스에 종속되며, 독립적으로 사전 생성하거나 여러 리소스에서 공유할 수 없습니다. 여러 리소스 공유 및 사전 배포 시나리오에는 사용자 할당이 올바른 선택입니다.

---

### 문제 22

조직에서 관리 역할 활성화가 시간 제한적이고, 승인이 필요하며, 감사 추적이 생성되어야 합니다. 사용자는 명시적으로 활성화해야 하는 "적격(eligible)" 할당을 가져야 합니다. 이 기능을 제공하는 Microsoft Entra ID 기능은 무엇입니까?

- A. 조건부 액세스 정책
- B. Privileged Identity Management (PIM)
- C. ID 보호 위험 정책
- D. 액세스 검토

**정답: B**

**해설:** Microsoft Entra ID PIM(Privileged Identity Management)은 JIT(Just-In-Time) 권한 있는 액세스를 제공합니다. PIM을 사용하면 사용자에게 시간 제한, 정당성 및 선택적 승인 워크플로우와 함께 명시적으로 활성화해야 하는 "적격" 역할 할당을 부여할 수 있습니다. 모든 활성화는 감사 목적으로 기록됩니다. 이를 통해 지속적인 권한 있는 접근을 제거하고 손상된 계정의 위험을 줄입니다. PIM에는 Microsoft Entra ID Premium P2 라이선스가 필요합니다.

---

## 4) 거버넌스, 정책, 복구

### 문제 23

구독에 대한 월별 예산을 생성합니다. 예산 경고 임계값에 도달하면 구독에서 Owner 또는 Contributor 역할을 보유한 모든 사용자에게 이메일 알림을 보내야 합니다. 예산 경고 구성에서 어떤 알림 옵션을 사용해야 합니까?

- A. 개별 이메일 주소를 포함한 작업 그룹
- B. 특정 배포 목록을 포함한 연락처 이메일
- C. 연락처 역할: Owner 및 Contributor
- D. Azure Monitor 메트릭 경고 규칙

**정답: C**

**해설:** Azure Cost Management 예산은 "연락처 역할"을 알림 대상으로 지원하며, 이는 현재 범위에서 지정된 RBAC 역할(예: Owner, Contributor, Reader)을 보유한 모든 사용자에게 예산 경고를 동적으로 전송합니다. 역할 할당이 변경되면 자동으로 업데이트됩니다. 작업 그룹(옵션 A)은 개별 이메일 주소(정적)를 지정해야 하며 특정 개인에게 알림을 보내거나 자동화된 작업을 트리거할 때 더 적합합니다.

---

### 문제 24

작업 그룹이 sec-alerts@contoso.com의 보안 팀 배포 목록에 이메일 경고를 보냅니다. 모든 경고 규칙이 정상적으로 발생하고 있음을 확인했지만 팀이 가끔 중요한 경고를 놓칩니다. 가장 가능성 있는 원인은 무엇입니까?

- A. 작업 그룹이 최대 10명의 이메일 수신자 제한에 도달함
- B. Azure Monitor가 이메일 주소당 시간당 100개의 이메일로 속도를 제한함
- C. Azure Monitor에서 이메일 알림에 SMTP 릴레이 구성이 필요함
- D. 배포 목록이 Azure Monitor의 이메일을 거부함

**정답: B**

**해설:** Azure Monitor 작업 그룹은 이메일 주소당 시간당 100개의 이메일 알림으로 속도를 제한합니다. 동일한 수신자를 대상으로 한 시간에 100개 이상의 경고가 발생하면 이후 이메일이 억제되고 속도 제한 알림이 대신 전송됩니다. 이는 경고가 정상적으로 발생하는 것이 확인된 경우 이메일 알림이 간헐적으로 누락되는 가장 일반적인 원인입니다. 고볼륨 경고 시나리오의 경우 더 구체적인 경고 범위를 사용하거나 웹훅 또는 ITSM 커넥터와 같은 다른 알림 채널을 사용하는 것을 고려하세요.

---

### 문제 25

Azure Site Recovery로 3계층 애플리케이션(SQL, Application, Web 서버)을 보호합니다. SQL 서버가 먼저 온라인 상태가 되고 그 다음 Application 서버, 마지막으로 Web 서버가 되도록 Recovery Plan을 구성해야 합니다. 시작 그룹을 어떻게 구성해야 합니까?

- A. 모든 VM을 그룹 1에 추가하고 각 계층 사이에 수동 단계 추가
- B. SQL을 그룹 1에, Application 서버를 그룹 2에, Web 서버를 그룹 3에 배치
- C. Web 서버를 그룹 1에, Application을 그룹 2에, SQL을 그룹 3에 배치
- D. 모든 VM을 동일한 그룹에 추가하고 부팅 지연 설정

**정답: B**

**해설:** Azure Site Recovery Recovery Plan은 시작 그룹을 순차적으로 실행합니다: 그룹 1이 완료된 후 그룹 2가 시작되고, 그 다음 그룹 3이 시작됩니다. 단일 그룹 내의 모든 VM은 병렬로 시작됩니다. SQL → App → Web 시작 순서가 필요한 3계층 애플리케이션의 경우, SQL 서버를 그룹 1에, Application 서버를 그룹 2에, Web 서버를 그룹 3에 배치합니다. 이렇게 하면 데이터베이스 계층이 Application 계층 시작 전에 사용 가능하고, Application 계층이 Web 계층 전에 준비되는 애플리케이션 종속성 순서가 유지됩니다.

---

### 문제 26

VM이 East US 기본 지역에서 West US 보조 지역으로 Azure Site Recovery를 통해 복제됩니다. East US에서 대규모 장애가 발생합니다. VM을 West US로 장애 조치하고 장애 조치를 커밋합니다. East US 지역이 복구됩니다. West US의 VM이 East US로 다시 복제하도록 준비하려면 가장 먼저 무엇을 해야 합니까?

- A. East US에 새 Recovery Services vault 생성
- B. West US의 VM을 삭제하고 East US에 다시 생성
- C. West US의 VM에서 재보호(Re-protect) 작업 수행
- D. East US를 대상으로 하는 새 복제 정책 구성

**정답: C**

**해설:** 장애 조치 및 커밋 후에는 원래 복제 관계가 끊어집니다. 현재 West US에서 실행 중인 VM이 East US로 다시 복제하도록 하려면 재보호(Re-protect) 작업을 수행해야 합니다. 재보호는 복제 방향을 반전시킵니다: West US를 새 원본으로, East US를 새 대상으로 구성합니다. 재보호가 완료된 후에만 VM을 East US로 다시 이동하는 장애 복구(Failback)를 수행할 수 있습니다. 새 vault이나 복제 정책을 생성할 필요가 없으며 기존 인프라가 재사용됩니다.

---

### 문제 27

모든 새로 생성된 Azure 리소스에 특정 코스트 센터 태그가 자동으로 적용되도록 해야 합니다. 또한 태그가 없는 기존 리소스도 수정 작업(remediation task)을 통해 자동으로 업데이트해야 합니다. 어떤 Azure Policy 효과를 사용해야 합니까?

- A. Audit
- B. Deny
- C. Append
- D. Modify

**정답: D**

**해설:** Azure Policy의 Modify 효과는 리소스에서 태그와 속성을 추가, 업데이트 또는 제거할 수 있습니다. 새 리소스의 경우 태그 조건이 충족되지 않으면 배포를 방지하거나(구성에 따라) 태그를 자동으로 추가합니다. 중요한 점은 Modify가 기존의 비준수 리소스를 업데이트하는 수정 작업(remediation task) 생성을 지원한다는 것입니다. Append 효과는 속성에만 추가하고 기존 값을 수정할 수 없습니다. Audit은 조치를 취하지 않고 비준수만 표시합니다. 새 리소스와 기존 리소스 모두에 자동 태그 지정을 적용하려면 Modify와 수정 작업이 올바른 접근 방식입니다.

---

### 문제 28

리소스 로그를 Log Analytics 작업 영역으로 전송하도록 여러 Azure 리소스의 진단 설정을 구성합니다. 개별 로그 범주를 선택하지 않고 범주 그룹을 사용하여 구성을 단순화하려 합니다. 진단 설정에서 선택할 수 있는 두 가지 범주 그룹은 무엇입니까?

- A. allLogs 및 operationalLogs
- B. allLogs 및 auditLogs
- C. resourceLogs 및 securityLogs
- D. diagnosticLogs 및 complianceLogs

**정답: B**

**해설:** Azure 진단 설정은 두 가지 기본 제공 범주 그룹을 지원합니다: allLogs(서비스에 사용 가능한 모든 리소스 로그 범주 수집) 및 auditLogs(보안 및 감사 관련 로그 수집). 범주 그룹을 사용하면 구성이 단순화되고 Microsoft가 향후 새 로그 범주를 추가해도 allLogs에 수동 업데이트 없이 자동으로 포함됩니다. 더 세밀한 제어가 필요한 경우 개별 로그 범주를 별도로 선택할 수도 있습니다.

---

### 문제 29

Azure Key Vault에 암호화 키가 포함되어 있습니다. 키에 대한 교체 알림(rotation reminder)을 구성하려 하지만 포털에서 해당 옵션이 회색으로 표시됩니다. 가장 가능성 있는 원인은 무엇입니까?

- A. 키에 만료 날짜가 설정되지 않음
- B. 키의 생성 시간(keyCreationTime) 속성이 설정되지 않음
- C. Key Vault에 일시 삭제가 활성화되지 않음
- D. 사용자에게 Key Vault Contributor 역할이 없음

**정답: B**

**해설:** Azure Key Vault 키 교체 알림은 키의 생성 시간(keyCreationTime 속성)을 사용하여 상대적 교체 간격을 계산합니다. 키에 keyCreationTime 속성이 설정되지 않은 경우 포털에서 교체 알림 구성 옵션을 사용할 수 없습니다(회색으로 표시). 키에 keyCreationTime 속성을 설정하면 생성 이후 경과 시간을 기반으로 한 알림 기능이 있는 교체 정책 구성이 가능합니다.

---

### 문제 30

MG-Root 상위 관리 그룹과 하위 관리 그룹 MG-Prod 및 MG-Dev를 포함하는 관리 그룹 계층이 있습니다. MG-Root에 승인된 Azure 지역 외부에서의 리소스 생성을 거부하는 정책을 할당합니다. 이 정책은 어떤 범위에 적용됩니까?

- A. MG-Root 관리 그룹 자체에만
- B. MG-Root에 직접 할당된 구독에만
- C. MG-Root 계층 내의 모든 관리 그룹, 구독, 리소스 그룹 및 리소스
- D. MG-Root 자체는 제외하고 MG-Prod 및 MG-Dev의 구독에만

**정답: C**

**해설:** Azure Policy 할당은 관리 그룹 계층 전체에서 아래쪽으로 상속됩니다. MG-Root에 할당된 정책은 MG-Root, 모든 하위 관리 그룹(MG-Prod, MG-Dev), 해당 관리 그룹 아래의 모든 구독, 해당 구독의 모든 리소스 그룹 및 해당 리소스 그룹 내의 모든 리소스에 자동으로 적용됩니다. 이러한 계층적 상속은 관리 그룹의 근본적인 특성이며, 높은 범위에서 할당된 정책은 모든 하위 범위에 자동으로 적용됩니다.

---

## 5) 컴퓨팅, App Service, AKS

### 문제 31

ARM 템플릿으로 Linux VM에 Custom Script Extension을 배포했습니다. Extension이 실행에 실패하여 로그를 검토해야 합니다. Linux VM에서 Custom Script Extension의 기본 로그 파일 경로는 무엇입니까?

- A. /var/log/waagent.log
- B. /var/log/azure/custom-script/handler.log
- C. /var/log/azure/custom-script-extension/handler.log
- D. /tmp/azure/customscript.log

**정답: C**

**해설:** Linux용 Custom Script Extension은 핸들러 로그를 `/var/log/azure/custom-script-extension/handler.log`에 기록합니다. 이 로그 파일에는 Extension 초기화, 스크립트 다운로드 시도 및 실행 상태에 대한 세부 정보가 포함됩니다. 실행된 스크립트의 stdout 및 stderr 출력도 동일한 디렉터리 경로 내의 별도 로그 파일에 기록됩니다. 이 경로가 Linux VM의 Custom Script Extension 실패 문제를 해결하는 기본 위치입니다.

---

### 문제 32

환경의 가상 머신에서 VM Insights를 활성화합니다. VM Insights 활성화 후 Map 탭에 데이터나 종속성이 표시되지 않습니다. Map 기능을 채우려면 VM에 어떤 추가 구성 요소를 설치해야 합니까?

- A. Log Analytics Agent (MMA)
- B. 성능 카운터가 있는 Azure Monitor Agent (AMA)
- C. Dependency Agent
- D. Network Watcher Agent Extension

**정답: C**

**해설:** VM Insights Map 기능은 가상 머신에 Dependency Agent가 설치되어 있어야 합니다. Dependency Agent는 실행 중인 프로세스, 활성 네트워크 연결 및 서비스 간 포트 매핑에 대한 자세한 데이터를 수집합니다. Azure Monitor Agent(또는 Log Analytics Agent)가 VM Insights 성능 데이터 수집에 필요하지만, Map 기능은 특히 종속성 시각화를 위해 Dependency Agent에 의존합니다. Dependency Agent 없이는 VM Insights가 활성화된 후에도 Map 탭이 비어 있습니다.

---

### 문제 33

WebApp1이라는 Azure App Service 웹 앱에 스테이징 배포 슬롯이 있습니다. 스테이징 슬롯에는 스테이징 데이터베이스를 가리키는 DatabaseConnection이라는 애플리케이션 설정이 있습니다. 스테이징을 프로덕션으로 교환(swap)할 때 스테이징 슬롯의 DatabaseConnection이 항상 스테이징 데이터베이스 값을 유지하도록(프로덕션으로 이동하지 않도록) 해야 합니다. 무엇을 구성해야 합니까?

- A. DatabaseConnection을 Key Vault 비밀 참조로 저장
- B. 스테이징 슬롯에서 자동 교환 활성화
- C. DatabaseConnection을 배포 슬롯 설정(스티키 설정)으로 표시
- D. App Service 플랜 수준에서 설정 구성

**정답: C**

**해설:** 배포 슬롯 설정(스티키 설정)은 특정 슬롯에 바인딩되어 슬롯 교환 중에 이동하지 않습니다. DatabaseConnection이 스테이징 슬롯에서 배포 슬롯 설정으로 표시되면 교환 후에도 해당 값이 스테이징에 유지됩니다. 일반(스티키하지 않은) 애플리케이션 설정은 교환 중에 배포된 코드와 함께 이동합니다. 스티키 설정은 환경별 값에 대해 슬롯별로 다른 구성을 허용합니다.

---

### 문제 34

스테이징 및 프로덕션 배포 슬롯이 있는 Azure App Service 웹 앱이 있습니다. 스테이징 슬롯에 배포된 코드가 애플리케이션이 성공적으로 워밍업된 후 운영자의 수동 개입 없이 자동으로 프로덕션으로 교환되도록 해야 합니다. 무엇을 구성해야 합니까?

- A. 배포 후 교환을 트리거하도록 GitHub Actions 워크플로 구성
- B. 스테이징 슬롯에서 프로덕션 슬롯을 대상으로 자동 교환(auto swap) 활성화
- C. 주 브랜치에서 프로덕션으로 직접 지속적 배포 구성
- D. 스테이징 슬롯을 기본 대상으로 하는 배포 센터 설정

**정답: B**

**해설:** 자동 교환은 코드가 슬롯에 배포되고 애플리케이션이 워밍업(시작) 프로세스를 완료한 후 자동으로 슬롯 교환을 트리거하는 App Service 기능입니다. 스테이징 슬롯에 자동 교환을 구성하고 프로덕션 슬롯을 대상으로 지정합니다. 스테이징에 코드가 배포되면 App Service는 워밍업 시퀀스를 실행하고 성공적인 시작 시 스테이징을 프로덕션으로 자동 교환합니다. CI/CD 파이프라인에서 수동 교환 단계를 없애줍니다. 자동 교환은 Standard 계층 이상이 필요합니다.

---

### 문제 35

메인 애플리케이션 컨테이너와 사이드카 로깅 컨테이너를 사용하는 컨테이너 워크로드를 배포해야 합니다. 두 컨테이너는 동일한 Azure Container Instances 컨테이너 그룹에서 실행되고 네트워크 네임스페이스를 공유해야 합니다. 컨테이너 그룹에 어떤 운영 체제를 사용해야 합니까?

- A. Windows Server 2022
- B. Windows 또는 Linux (둘 다 지원됨)
- C. Linux
- D. Windows Server Core

**정답: C**

**해설:** Azure Container Instances의 다중 컨테이너 그룹(사이드카 컨테이너가 네트워크 및 스토리지를 공유하는 경우 포함)은 Linux에서만 지원됩니다. Windows 기반 ACI 컨테이너 그룹은 다중 컨테이너 배포를 지원하지 않습니다. 동일한 ACI 컨테이너 그룹에서 메인 컨테이너와 사이드카 컨테이너(로깅, 모니터링 등)를 실행해야 하는 경우 운영 체제로 Linux를 선택해야 합니다.

---

### 문제 36

단일 Linux 시스템 노드 풀이 있는 AKS 클러스터가 있습니다. Windows Server 컨테이너 워크로드를 실행하기 위해 Windows 노드 풀을 추가해야 합니다. Load Balancer SKU에 대한 중요한 사전 요구 사항을 확인했습니다. 어떤 설명이 맞습니까?

- A. AKS 클러스터는 Basic SKU Load Balancer를 사용해야 함
- B. AKS 클러스터는 Standard SKU Load Balancer를 사용해야 함
- C. Windows 노드 풀에는 Load Balancer가 필요하지 않음
- D. Windows 노드 풀을 위해 별도의 Load Balancer를 생성해야 함

**정답: B**

**해설:** AKS 클러스터에 Windows 노드 풀을 추가하려면 클러스터가 Standard SKU Load Balancer를 사용해야 합니다. Basic SKU Load Balancer는 Windows 노드 풀 지원에 필요한 다중 노드 풀을 지원하지 않습니다. 또한 Windows 노드 풀은 Azure CNI(Container Network Interface) 네트워킹이 필요합니다. kubenet은 Windows 노드 풀에서 지원되지 않습니다. Standard SKU Load Balancer 요구 사항은 AZ-104 시험에서 Windows AKS 노드 풀의 사전 요구 사항으로 특별히 테스트됩니다.

---

### 문제 37

가상 머신에 대해 AND 논리로 두 가지 조건이 있는 Azure Monitor 메트릭 경고 규칙을 생성합니다: CPU 백분율 > 90% AND 사용 가능한 메모리 바이트 < 1GB. 평가 빈도는 5분입니다. AND 조건 경고의 올바른 동작은 무엇입니까?

- A. 조건이 독립적으로 평가되어 둘 중 하나가 경고를 트리거할 수 있음
- B. 경고가 발생하려면 동일한 평가 창 내에 모든 조건이 true여야 함
- C. 조건이 순차적으로 평가됩니다. 조건 1이 10분 동안 지속된 후 조건 2를 확인함
- D. 경고는 모든 조건의 가중 평균이 임계값을 초과할 때 발생함

**정답: B**

**해설:** AND 논리를 사용하는 Azure Monitor 메트릭 경고 규칙에서 여러 메트릭 조건이 결합되면 경고가 트리거되려면 모든 조건이 동일한 평가 창 내에서 동시에 true여야 합니다. CPU > 90% AND 사용 가능한 메모리 < 1GB 모두 동일한 5분 평가 기간 내에 동시에 발생해야 합니다. 이 상태 기반, 시간 슬라이스 기반 평가는 일시적인 개별 메트릭 급등으로 인한 경고 노이즈를 줄이는 데 도움이 됩니다. 평가 창 동안 어느 한 조건이 정상으로 돌아오면 경고가 발생하지 않습니다.

---

### 문제 38

Windows VM에 대해 Azure Backup을 구성하고 애플리케이션 일관성 백업을 활성화합니다. 데이터베이스 워크로드에 대해 애플리케이션 일관성 백업이 충돌 일관성 백업보다 제공하는 주요 이점은 무엇입니까?

- A. 애플리케이션 일관성 백업이 완료 속도가 더 빠름
- B. 애플리케이션 일관성 백업은 VM 에이전트가 필요하지 않음
- C. 애플리케이션 일관성 백업은 VSS를 사용하여 스냅샷 전에 진행 중인 트랜잭션을 플러시하고 정지함
- D. 애플리케이션 일관성 백업은 저비용 아카이브 스토리지에 데이터를 저장함

**정답: C**

**해설:** 애플리케이션 일관성 백업은 Windows의 VSS(볼륨 섀도 복사 서비스)(Linux의 경우 사전/사후 고정 스크립트)를 사용하여 애플리케이션과 조율합니다. VSS는 실행 중인 애플리케이션(예: SQL Server)에 진행 중인 모든 트랜잭션을 플러시하고 완료하고, 쓰기 캐시를 지우고, 스냅샷 전에 일관된 상태에 도달하도록 신호를 보냅니다. 이를 통해 데이터베이스를 트랜잭션 로그 재생이나 충돌 복구 없이 깨끗하고 일관된 상태로 복원할 수 있습니다. 충돌 일관성 백업은 디스크 상태를 있는 그대로 캡처하여 복원 후 복구가 필요한 부분 트랜잭션이 발생할 수 있습니다.

---

## 6) 모니터링 및 경고

### 문제 39

리소스 그룹에서 특정 가상 머신이 삭제될 때 트리거되는 Azure Monitor 경고를 생성해야 합니다. 어떤 유형의 경고 규칙을 생성해야 합니까?

- A. 메트릭 경고
- B. 로그 쿼리 경고
- C. Activity Log 경고
- D. 스마트 감지 경고

**정답: C**

**해설:** Activity Log 경고는 Azure Activity Log에 기록된 특정 이벤트가 발생할 때 실행됩니다. Activity Log는 리소스 생성, 수정, 삭제 등 모든 Azure Resource Manager 컨트롤 플레인 작업을 캡처합니다. VM 삭제 시 알림을 받으려면 구독 또는 리소스 그룹으로 범위가 지정된 Activity Log 경고 규칙을 생성하고 "가상 머신 삭제" 작업을 필터링합니다. 메트릭 경고는 숫자 성능 데이터를 모니터링합니다. 로그 쿼리 경고는 Log Analytics의 데이터를 분석합니다. Activity Log 경고가 컨트롤 플레인 이벤트에 적합한 유형입니다.

---

### 문제 40

Log Analytics 작업 영역이 있습니다. 보안 분석가가 작업 영역 구성 수정, 경고 규칙 생성, 데이터 원본 관리를 허용하지 않으면서 로그 쿼리를 실행하고 쿼리 결과를 볼 수 있도록 해야 합니다. 어떤 역할을 할당해야 합니까?

- A. Log Analytics Contributor
- B. Monitoring Contributor
- C. Log Analytics Reader
- D. Monitoring Reader

**정답: C**

**해설:** Log Analytics Reader 역할은 Log Analytics 작업 영역에 대한 읽기 전용 접근 권한을 부여합니다. 이 역할을 가진 사용자는 작업 영역 설정을 보고, 로그 쿼리를 실행하고, 저장된 검색을 보고, 쿼리 결과에 접근할 수 있습니다. 작업 영역 설정, 경고 규칙, 데이터 원본 또는 자동화 규칙을 생성하거나 수정할 수 없습니다. 이것이 쿼리 전용 접근에 필요한 최소 역할입니다. Log Analytics Contributor는 작업 영역 구성에 대한 쓰기 권한을 부여합니다.

---

### 문제 41

조직의 사무실 네트워크 외부 IP 주소에서의 로그인을 차단하는 조건부 액세스 정책을 구성해야 합니다. 사무실 네트워크는 5개의 다른 공용 IP 범위를 사용합니다. Microsoft Entra ID에서 조건부 액세스 정책에 사용할 무엇을 생성해야 합니까?

- A. 네트워크 위치 제외가 있는 사용자 위험 정책
- B. 사무실 IP 범위가 포함된 명명된 위치를 생성한 다음 정책에서 제외
- C. ID 보호 로그인 위험 정책
- D. 장치 준수를 조건으로 하는 조건부 액세스 정책

**정답: B**

**해설:** 조건부 액세스 명명된 위치(Named Locations)를 사용하면 IP 범위 또는 지리적 위치 집합을 정의할 수 있습니다. 5개의 사무실 IP 범위가 포함된 명명된 위치를 생성하고, 로그인이 해당 명명된 위치에서 오지 않을 때 접근을 차단하는 조건부 액세스 정책을 구성함으로써 사무실 네트워크 외부의 모든 로그인을 효과적으로 차단합니다. 명명된 위치는 위험 계산에서 신뢰할 수 있는 것으로 표시할 수도 있습니다.

---

### 문제 42

조직은 Azure AD Connect를 사용하는 하이브리드 ID 환경이 있습니다. SSPR이 활성화되어 있지만, 사용자들이 Azure 포털에서 암호를 재설정한 후 새 암호로 온-프레미스 워크스테이션에 로그인할 수 없다고 보고합니다. 이 문제를 해결하려면 Azure AD Connect의 어떤 기능을 활성화해야 합니까?

- A. 통과 인증
- B. 암호 해시 동기화
- C. 비밀번호 쓰기 저장(Password writeback)
- D. 원활한 SSO

**정답: C**

**해설:** 비밀번호 쓰기 저장(Password writeback)은 Microsoft Entra ID에서 변경된 암호(SSPR 포함)를 온-프레미스 Active Directory로 동기화하는 Azure AD Connect 기능입니다. 비밀번호 쓰기 저장이 없으면 사용자는 클라우드 암호를 재설정할 수 있지만 온-프레미스 AD 암호는 변경되지 않아 온-프레미스 로그인이 방지됩니다. 비밀번호 쓰기 저장은 Microsoft Entra ID Premium P1 또는 P2와 Azure AD Connect 마법사의 선택적 기능에서 구성이 필요합니다.

---

### 문제 43

조직에서 partner-company.com의 외부 비즈니스 파트너가 내부 Azure 애플리케이션에 접근할 수 있도록 하되, 파트너를 위한 새 계정을 만들 필요가 없도록 하려 합니다. 어떤 기능을 사용해야 합니까?

- A. Microsoft Entra External ID B2C (소비자용)
- B. Microsoft Entra ID B2B (기업 간) 협업
- C. Azure AD Application Proxy
- D. 소셜 로그인이 있는 Microsoft Entra ID External Identities

**정답: B**

**해설:** Microsoft Entra ID B2B 협업을 사용하면 외부 사용자를 테넌트에 게스트로 초대할 수 있습니다. 게스트 사용자는 자신의 조직 ID 공급자로 인증하고 공유한 리소스에 접근할 수 있습니다. B2B는 테넌트에서 새 사용자 계정을 생성할 필요가 없습니다. 게스트는 기존 회사 계정을 사용합니다. Microsoft Entra External ID B2C는 최종 사용자가 소비자 ID로 로그인하는 소비자용 애플리케이션을 위해 설계되었습니다. B2B가 파트너 조직 접근 시나리오에 올바른 기능입니다.

---

### 문제 44

Azure Key Vault에 중요한 암호화 키가 저장되어 있습니다. 키가 실수로 삭제되더라도 90일 동안 복구 가능하도록 해야 합니다. 또한 복구 기간 동안 관리자를 포함한 누구도 키를 영구적으로 삭제하지 못하도록 방지해야 합니다. 어떤 두 가지 기능을 활성화해야 합니까?

- A. 90일 보존 기간의 일시 삭제와 제거 보호
- B. 키 교체 정책 및 버전 관리
- C. 제거 보호 및 RBAC 인증만
- D. 90일 보존 기간의 백업 및 내보내기

**정답: A**

**해설:** 일시 삭제(Soft delete)는 삭제된 Key Vault 개체(키, 비밀, 인증서)를 구성 가능한 보존 기간(7-90일) 동안 유지하여 복구를 허용합니다. 제거 보호(Purge protection)는 보존 기간 동안 Key Vault 관리자 역할을 가진 사용자를 포함하여 누구에게도 일시 삭제된 개체의 영구 삭제(제거)를 방지합니다. 이 두 기능을 함께 사용하면 보존 창 내에서 항상 삭제된 키를 복구할 수 있습니다. 참고: Key Vault에 제거 보호가 활성화되면 비활성화할 수 없으며 보존 기간이 만료될 때까지 vault 또는 개체를 영구적으로 삭제할 수 없습니다.

---

## 7) 네트워킹 및 보안

### 문제 45

조직은 가상 네트워크에 배포된 Azure 리소스를 대상으로 하는 대규모 대용량 DDoS 공격에 대한 보호가 필요합니다. 자동 트래픽 스크러빙, 공격 텔레메트리 및 사후 분석이 포함되어야 합니다. Basic DDoS 보호는 이미 있습니다. 어떤 서비스를 배포해야 합니까?

- A. 침입 탐지가 있는 Azure Firewall Premium
- B. Azure DDoS Protection Standard (Network Protection)
- C. 속도 제한이 있는 NSG 규칙
- D. WAF v2가 있는 Azure Application Gateway

**정답: B**

**해설:** Azure DDoS Protection Standard(현재 Azure DDoS Network Protection이라고도 함)는 대용량, 프로토콜, 리소스 계층 DDoS 공격에 대한 고급 보호를 제공합니다. 특정 리소스에 맞춘 자동 트래픽 스크러빙, 실시간 메트릭 및 진단, 사후 분석 보고서, DDoS 신속 대응 지원이 포함됩니다. Azure DDoS Basic Protection은 모든 Azure 서비스에 자동으로 포함되지만 리소스별 튜닝이나 분석 없이 플랫폼 수준 보호만 제공합니다. Standard 보호는 포괄적인 DDoS 방어를 위한 적절한 업그레이드입니다.

---

### 문제 46

세 Azure 지역에 걸쳐 세 개의 엔드포인트가 있는 Azure Traffic Manager 프로필이 있습니다. 지역 A의 엔드포인트가 정상인 한 모든 트래픽이 지역 A로 라우팅되어야 합니다. 지역 A를 사용할 수 없게 되면 트래픽이 자동으로 지역 B로 장애 조치되어야 합니다. 어떤 라우팅 방법을 구성해야 합니까?

- A. 가중치 100, 50, 10으로 가중치 라우팅
- B. 최저 대기 시간 기반 성능 라우팅
- C. 우선순위 1(지역 A), 우선순위 2(지역 B), 우선순위 3(지역 C)으로 우선순위 라우팅
- D. 사용자 위치 기반 지리적 라우팅

**정답: C**

**해설:** Traffic Manager 우선순위 라우팅은 우선순위 번호가 가장 낮은(가장 높은 우선순위) 엔드포인트로 모든 트래픽을 보냅니다. 현재 기본 엔드포인트가 Traffic Manager 상태 프로브에 의해 비정상으로 감지될 때만 다음 엔드포인트로 트래픽이 장애 조치됩니다. 지역 A = 우선순위 1, 지역 B = 우선순위 2, 지역 C = 우선순위 3을 할당하면 원하는 장애 조치 연쇄가 달성됩니다. 가중치 라우팅은 백분율 기반으로 트래픽을 분산합니다. 성능 라우팅은 장애 상태에 관계없이 최저 대기 시간 엔드포인트를 선택합니다.

---

### 문제 47

VNet1과 VNet2가 VNet Peering으로 연결되어 있습니다. VNet1은 VPN을 통해 온-프레미스 네트워크에 연결하는 Virtual Network Gateway를 포함합니다. VNet2의 리소스가 VNet1의 게이트웨이를 통해 온-프레미스 네트워크에 접근해야 합니다. 무엇을 구성해야 합니까?

- A. VNet2에 두 번째 VPN 게이트웨이를 생성하고 VNet1과 VNet2 사이에 BGP 구성
- B. VNet1의 피어링 설정에서 "게이트웨이 전송 허용"을 활성화하고 VNet2의 피어링 설정에서 "원격 게이트웨이 사용"을 활성화
- C. VNet1의 게이트웨이 IP를 가리키는 UDR이 있는 VNet2의 라우팅 테이블 구성
- D. 전역 VNet Peering을 활성화하고 다중 VNet 지원을 위해 VPN 게이트웨이 구성

**정답: B**

**해설:** 게이트웨이 전송(Gateway transit)을 통해 피어링된 VNet의 리소스가 다른 VNet의 VPN 게이트웨이를 사용할 수 있습니다. 이를 구성하려면: VNet1의 피어링 설정(VNet2 방향)에서 "게이트웨이 전송 허용"을 활성화합니다(VNet1이 게이트웨이를 공유 허용). VNet2의 피어링 설정(VNet1 방향)에서 "원격 게이트웨이 사용"을 활성화합니다(VNet2 트래픽이 VNet1의 게이트웨이를 사용하도록 지시). 이를 통해 VNet2 리소스가 VNet2에 별도 게이트웨이를 배포하지 않고 VNet1의 VPN 게이트웨이를 통해 온-프레미스 네트워크에 도달할 수 있습니다. 게이트웨이 전송이 작동하려면 VNet1에 VPN 또는 ExpressRoute 게이트웨이가 배포되어 있어야 합니다.

---

### 문제 48

Azure VM과 특정 엔드포인트 간의 네트워크 연결, 대기 시간 및 패킷 손실을 시간 경과에 따라 지속적으로 모니터링해야 합니다. 어떤 Azure Network Watcher 도구를 사용해야 합니까?

- A. IP 흐름 확인
- B. 패킷 캡처
- C. Connection Monitor
- D. 다음 홉

**정답: C**

**해설:** Azure Network Watcher Connection Monitor는 소스(Azure VM, 에이전트가 있는 온-프레미스 머신)와 대상(Azure 엔드포인트, 외부 URL, IP 주소) 간의 네트워크 연결을 구성된 간격으로 지속적으로 자동 모니터링합니다. 연결이 저하될 때 경고를 생성하면서 왕복 시간, 패킷 손실 백분율 및 연결 가능성을 측정합니다. IP 흐름 확인은 특정 패킷이 NSG 규칙에 의해 허용 또는 거부되는지 테스트합니다(일회성 확인). 패킷 캡처는 분석을 위해 원시 네트워크 트래픽을 기록합니다. Connection Monitor가 지속적인 연결 및 성능 모니터링에 올바른 도구입니다.

---

### 문제 49

VM이 Windows Update 서비스에 접근할 수 있도록 Azure Firewall을 구성해야 합니다. Microsoft의 업데이트 IP 주소는 주기적으로 변경되며 Microsoft가 관리합니다. 수동 IP 목록 유지 관리가 필요 없는 솔루션이 필요합니다. Azure Firewall에 무엇을 구성해야 합니까?

- A. Microsoft ASN IP 범위가 있는 네트워크 규칙
- B. WindowsUpdate FQDN 태그를 사용한 애플리케이션 규칙
- C. 업데이트 트래픽을 프록시 서버로 전달하는 DNAT 규칙
- D. 업데이트 트래픽을 허용하도록 위협 인텔리전스 모드 활성화

**정답: B**

**해설:** Azure Firewall은 애플리케이션 규칙에서 FQDN 태그를 지원하며, 이는 알려진 서비스와 관련된 Microsoft가 관리하는 사전 정의된 FQDN 집합을 나타냅니다. WindowsUpdate FQDN 태그는 수동 유지 관리 없이 Windows Update에 필요한 모든 도메인을 포함합니다. Microsoft는 관련 FQDN이 변경될 때 태그를 자동으로 업데이트합니다. Azure Firewall에서 WindowsUpdate FQDN 태그로의 아웃바운드 HTTPS 트래픽을 허용하는 애플리케이션 규칙을 생성하면 방화벽이 필요한 모든 업데이트 트래픽을 확인하고 허용합니다.

---

### 문제 50

온-프레미스 데이터센터를 Azure에 연결하는 Azure ExpressRoute 회로가 있습니다. 고성능 워크로드의 경우 온-프레미스에서 오는 네트워크 트래픽이 Virtual Network Gateway를 우회하여 Azure VM NIC에 직접 흐르도록 대기 시간을 줄여야 합니다. 이를 가능하게 하는 ExpressRoute 기능은 무엇입니까?

- A. ExpressRoute Global Reach
- B. ExpressRoute FastPath
- C. ExpressRoute Direct
- D. ExpressRoute Premium 추가 기능

**정답: B**

**해설:** ExpressRoute FastPath는 네트워크 트래픽을 Azure VM NIC에 직접 전송하여 데이터 경로 성능을 향상시키고 데이터 경로에서 Virtual Network Gateway를 우회합니다. 게이트웨이는 여전히 라우팅 컨트롤 플레인 업데이트를 처리하지만 더 이상 데이터 포워딩 경로에 있지 않아 대기 시간 민감 워크로드의 대기 시간을 줄이고 처리량을 높입니다. FastPath 없이는 모든 트래픽이 VM에 도달하기 전에 게이트웨이를 통해 흐릅니다. FastPath는 Ultra Performance 및 ErGw3AZ 게이트웨이 SKU와 ExpressRoute Direct 포트 유형 또는 ExpressRoute Premium에서 지원됩니다.

---

_AZ-104 강의 자료를 기반으로 생성된 문서입니다. 최종 업데이트: 2026-05-24_
