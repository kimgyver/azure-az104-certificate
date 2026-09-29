# AZ-104 예상 문제 (세트 12) — 한국어

---

## 1) 네트워킹 및 프라이빗 연결

### 문제 1

NSG1이 Subnet1에 연결되어 있으며 다음 인바운드 규칙이 있습니다:

- Rule1 (우선순위 100): 10.0.0.0/8에서 TCP 443 허용
- Rule2 (우선순위 200): 모든 소스에서 TCP 443 거부
- Rule3 (우선순위 300): 인터넷에서 TCP 443 허용

IP 주소 52.168.1.50의 클라이언트가 Subnet1에 있는 VM의 포트 443으로 연결을 시도합니다. 결과는 무엇입니까?

- A. Rule1에 의해 허용됨
- B. Rule3에 의해 허용됨
- C. Rule3이 더 낮은 우선순위 번호를 가지므로 거부됨
- D. Rule2가 먼저 일치하여 연결을 거부함

**정답: D**

**해설:** NSG 규칙은 우선순위 오름차순(낮은 번호 먼저)으로 평가됩니다. Rule1은 일치하지 않습니다(52.168.1.50은 10.0.0.0/8에 속하지 않음). Rule2는 "Any" 소스와 일치하여 포트 443 트래픽을 즉시 거부합니다. 첫 번째 일치 규칙에서 처리가 중단되므로 Rule3은 평가되지 않습니다.

---

### 문제 2

Standard SKU 내부 부하 분산 장치에 백엔드 VM 세 대가 있습니다. VM이 정상 실행 중임에도 상태 프로브가 실패합니다. VM의 NSG는 특정 소스를 제외한 모든 인바운드 트래픽을 차단합니다. 상태 프로브 포트에서 인바운드를 허용해야 하는 소스 IP 주소는 무엇입니까?

- A. 10.0.0.1
- B. 0.0.0.0
- C. 168.63.129.16
- D. 169.254.169.254

**정답: C**

**해설:** Azure Load Balancer 상태 프로브는 Azure 인프라 가상 IP인 168.63.129.16에서 발신됩니다. NSG가 이 주소에서 프로브 포트로의 인바운드 트래픽을 차단하면 애플리케이션이 정상이더라도 프로브가 실패하고 백엔드가 비정상으로 표시됩니다.

---

### 문제 3

다른 Azure 구독의 네트워크 관리자가 스토리지 계정 storage1을 대상으로 프라이빗 엔드포인트를 생성했습니다. 연결이 **보류 중** 상태로 나타납니다. 무엇을 해야 합니까?

- A. storage1의 방화벽 허용 가상 네트워크에 외부 구독을 추가
- B. storage1에서 프라이빗 엔드포인트 연결 승인
- C. storage1에서 Private Link 서비스 활성화
- D. 프라이빗 DNS 영역에 프라이빗 엔드포인트의 A 레코드 생성

**정답: B**

**해설:** 다른 구독에서 프라이빗 엔드포인트가 생성되면 대상 리소스 소유자가 수동으로 연결을 승인해야 합니다. 보류 중 상태는 승인 대기 중을 의미합니다. storage1 → 네트워킹 → 프라이빗 엔드포인트 연결로 이동하여 승인을 선택합니다.

---

### 문제 4

스토리지 계정 storage1의 Blob 하위 리소스를 대상으로 프라이빗 엔드포인트 pe-blob을 생성하면서 "프라이빗 DNS 영역과 통합"을 선택했습니다. 어떤 프라이빗 DNS 영역이 생성됩니까?

- A. privatelink.core.windows.net
- B. privatelink.storage.azure.com
- C. privatelink.blob.core.windows.net
- D. blob.privatelink.azure.net

**정답: C**

**해설:** Azure는 Blob 프라이빗 엔드포인트를 프라이빗 DNS 영역과 통합할 때 `privatelink.blob.core.windows.net` 영역을 자동으로 생성합니다. 이 영역은 스토리지 계정의 공개 FQDN을 프라이빗 엔드포인트 IP 주소로 매핑합니다. 다른 하위 리소스에는 `.file`, `.queue`, `.table` 등의 영역이 사용됩니다.

---

### 문제 5

Azure Firewall에서 모든 Windows Update 엔드포인트로의 아웃바운드 HTTPS 트래픽을 허용하되, 최소한의 유지 관리가 필요한 구성을 원합니다. 어떤 옵션을 선택해야 합니까?

- A. WindowsUpdate 서비스 태그를 사용하는 네트워크 규칙
- B. WindowsUpdate FQDN 태그를 사용하는 애플리케이션 규칙
- C. Windows Update IP 주소로 포트 443을 전달하는 NAT 규칙
- D. 모든 Windows Update FQDN을 수동으로 나열하는 애플리케이션 규칙

**정답: B**

**해설:** Azure Firewall FQDN 태그는 잘 알려진 서비스에 대한 Microsoft 관리형 FQDN 그룹을 나타냅니다. WindowsUpdate FQDN 태그를 애플리케이션 규칙에 사용하면 모든 Windows Update 엔드포인트가 자동으로 포함되고 엔드포인트가 변경될 때 Microsoft가 자동으로 업데이트하므로 수동 유지 관리가 필요 없습니다.

---

### 문제 6

VNet1은 East US 지역에 배포되어 Subnet1을 포함합니다. RouteTable1은 West US 지역에 생성되었습니다. RouteTable1을 Subnet1에 연결해야 합니다. 먼저 무엇을 해야 합니까?

- A. Azure Resource Mover를 사용하여 VNet1을 West US 지역으로 이동
- B. Azure CLI를 사용하여 지역 간 경로 테이블 연결 강제 적용
- C. RouteTable1을 삭제하고 East US에 새 경로 테이블 생성
- D. Azure Portal에서 RouteTable1의 지역 속성 변경

**정답: C**

**해설:** 경로 테이블은 연결하려는 가상 네트워크 서브넷과 동일한 Azure 지역에 있어야 합니다. Azure는 경로 테이블의 지역을 생성 후 변경하는 것을 지원하지 않습니다. 유일한 해결책은 RouteTable1을 삭제하고 East US에 새 경로 테이블을 생성한 다음 Subnet1에 연결하는 것입니다.

---

### 문제 7

프라이빗 DNS 영역 private.contoso.com이 있습니다. VNet1에 배포된 VM은 해당 영역에 A 레코드가 자동으로 등록되어야 합니다. VNet2의 VM은 영역의 레코드를 확인할 수 있어야 하지만 새 레코드를 등록할 수 없어야 합니다. 무엇을 구성해야 합니까?

- A. 자동 등록을 활성화하여 VNet1의 VNet 링크를 생성하고, 자동 등록을 비활성화하여 VNet2의 VNet 링크를 생성
- B. 자동 등록을 활성화하여 VNet1과 VNet2 모두의 VNet 링크 생성
- C. VNet1의 VNet 링크만 생성; VNet2는 확인을 위한 링크가 필요 없음
- D. VNet2의 DNS 설정에서 조건부 전달자 구성

**정답: A**

**해설:** Azure 프라이빗 DNS VNet 링크는 VNet별로 확인 및 등록을 독립적으로 제어합니다. VNet1 링크에서 자동 등록을 활성화하면 VM 배포 시 A 레코드가 자동으로 생성 및 삭제됩니다. VNet2 링크에서 자동 등록을 비활성화하면 VNet2의 VM이 영역의 레코드를 확인할 수 있지만 새 레코드를 등록하지 않습니다.

---

### 문제 8

Azure Container Instances 컨테이너 그룹에 Container1(포트 80, 외부 노출)과 Container2(포트 8080, 외부 미노출)가 있습니다. Container1은 Container2에 어떻게 요청을 보내야 합니까?

- A. localhost:8080 — 같은 그룹의 컨테이너는 네트워크 네임스페이스를 공유함
- B. VNet의 Container2 프라이빗 IP 주소를 통해
- C. 포트 8080의 컨테이너 그룹 공용 IP 주소를 통해
- D. 두 컨테이너 앞에 있는 Azure 내부 부하 분산 장치를 통해

**정답: A**

**해설:** 동일한 ACI 컨테이너 그룹 내의 컨테이너는 루프백 인터페이스를 포함한 네트워크 네임스페이스를 공유합니다. Container1은 외부 라우팅 없이 localhost:8080(또는 127.0.0.1:8080)을 사용하여 Container2에 도달할 수 있습니다. 포트 80만 외부에 노출되고 포트 8080은 내부용으로 유지됩니다.

---

### 문제 9

Azure Container Instances 컨테이너 그룹을 가상 네트워크 서브넷에 배포했습니다. 컨테이너는 인터넷의 공개 REST API를 호출해야 합니다. 인터넷 액세스를 활성화하려면 무엇을 구성해야 합니까?

- A. 컨테이너 그룹에 공용 IP 주소 할당
- B. 포트 443을 허용하는 인바운드 NSG 규칙 추가
- C. 서브넷에 NAT 게이트웨이를 연결하거나 인터넷을 가리키는 기본 경로가 있는 UDR 추가
- D. 서브넷에서 Microsoft.ContainerRegistry 서비스 엔드포인트 활성화

**정답: C**

**해설:** ACI 컨테이너 그룹이 VNet에 주입되면 자동 아웃바운드 인터넷 연결이 없어집니다. NAT 게이트웨이(안정적인 공용 IP를 가진 관리형 SNAT) 또는 인터넷을 다음 홉으로 지정하는 사용자 정의 경로(0.0.0.0/0 → 인터넷)를 통해 서브넷 수준에서 인터넷 액세스를 구성해야 합니다.

---

### 문제 10

AppGW1이라는 Application Gateway에서 모든 백엔드 풀 멤버가 비정상으로 표시됩니다. 백엔드 서버에 직접 액세스하면 올바르게 작동합니다. 올바른 URL 경로와 포트로 사용자 지정 상태 프로브를 생성했습니다. 사용자 지정 프로브를 어디에 연결해야 효력이 발생합니까?

- A. 수신기
- B. 백엔드 HTTP 설정
- C. 라우팅 규칙
- D. 백엔드 풀

**정답: B**

**해설:** Azure Application Gateway에서 상태 프로브는 백엔드 HTTP 설정에 연결됩니다. 백엔드 HTTP 설정은 백엔드 서버 상태를 평가할 때 사용할 프로토콜, 포트, 호스트 헤더 및 프로브를 정의합니다. 다른 수준(수신기, 규칙 또는 풀)에서 프로브를 연결해도 효력이 없습니다.

---

## 2) 스토리지, SAS 및 수명 주기 관리

### 문제 11

Blob 컨테이너에 저장된 액세스 정책 policy1에 연결된 서비스 SAS가 있습니다. 스토리지 계정의 액세스 키를 재생성하지 않고 policy1을 참조하는 모든 SAS 토큰의 액세스를 즉시 취소해야 합니다. 무엇을 해야 합니까?

- A. 애플리케이션 구성에서 SAS 토큰 문자열 삭제
- B. 스토리지 계정의 보조 액세스 키 교체
- C. 컨테이너의 공용 액세스 수준을 Private으로 변경
- D. 컨테이너에서 저장된 액세스 정책 policy1 삭제

**정답: D**

**해설:** 저장된 액세스 정책을 삭제하면 해당 정책을 참조하는 모든 SAS 토큰이 즉시 무효화됩니다. Azure는 정책이 없으면 SAS 서명을 검증할 수 없으므로 액세스가 즉시 거부됩니다. 키 재생성은 필요하지 않으며, 키를 재생성하면 해당 키를 사용하는 다른 모든 SAS 토큰과 애플리케이션이 중단됩니다.

---

### 문제 12

Azure 파일 공유가 수개월간 스냅샷 기반 Azure Backup으로 보호되고 있습니다. 주문형 백업이 스냅샷 한도 초과 오류로 실패합니다. Azure 파일 공유당 최대 스냅샷 수는 얼마입니까?

- A. 100
- B. 500
- C. 200
- D. 1,000

**정답: C**

**해설:** Azure 파일 공유는 공유당 최대 200개의 스냅샷을 지원합니다. 예약 및 주문형 백업 스냅샷 모두 이 한도에 포함됩니다. 한도에 도달하면 새 스냅샷을 생성할 수 없습니다. 이전 스냅샷을 삭제하거나 백업 보존 정책을 조정하여 한도 내로 유지해야 합니다.

---

### 문제 13

스토리지 계정에는 블록 Blob, 추가 Blob, 페이지 Blob이 포함되어 있습니다. 쿨 계층 전환 및 아카이브 계층 전환 작업으로 수명 주기 관리 정책을 구성합니다. 이러한 계층 전환 작업에 해당하는 Blob 유형은 무엇입니까?

- A. 블록 Blob과 추가 Blob 모두
- B. 블록 Blob만
- C. 추가 Blob만
- D. 페이지 Blob만

**정답: B**

**해설:** Azure Storage 수명 주기 관리 계층 전환 작업(핫 → 쿨 → 콜드 → 아카이브)은 블록 Blob에만 적용됩니다. 추가 Blob과 페이지 Blob은 수명 주기 관리 정책에서 계층 전환을 지원하지 않습니다. 수명 주기 관리는 추가 Blob을 삭제할 수는 있지만 계층을 변경할 수는 없습니다.

---

### 문제 14

사용자가 Azure Portal을 통해 Azure 파일 공유에 직접 파일을 업로드합니다. Azure File Sync에 등록된 온-프레미스 서버에 이 변경 사항이 24시간 이상 동기화되지 않습니다. 즉각적인 동기화를 트리거하는 가장 직접적인 방법은 무엇입니까?

- A. 서버에서 Storage Sync Agent 서비스 재시작
- B. 서버 엔드포인트 삭제 후 재생성
- C. 동기화 그룹의 대역폭 허용량 증가
- D. Invoke-AzStorageSyncChangeDetection PowerShell cmdlet 실행

**정답: D**

**해설:** Azure File Sync는 Azure 파일 공유에 직접 변경된 내용(서버 엔드포인트를 우회)을 기본적으로 24시간마다 실행되는 백그라운드 변경 감지 작업으로 감지합니다. `Invoke-AzStorageSyncChangeDetection`을 실행하면 클라우드 엔드포인트를 즉시 스캔하고 자동 주기를 기다리지 않고 모든 등록된 서버 엔드포인트에 변경 사항을 전파합니다.

---

### 문제 15

수명 주기 관리 정책에 30일 이상 경과된 블록 Blob에 대한 두 가지 작업이 구성되어 있습니다: `tierToCool`과 `delete`. Blob이 두 조건을 동시에 충족합니다. 수명 주기 엔진은 어떤 작업을 적용합니까?

- A. tierToCool — 계층 전환이 항상 삭제보다 우선함
- B. 두 작업이 병렬로 실행됨
- C. delete — 수명 주기 관리는 여러 작업이 해당될 때 가장 비용이 저렴한 작업을 적용함
- D. 정책 JSON에서 먼저 정의된 작업이 우선함

**정답: C**

**해설:** 여러 수명 주기 작업이 동일한 Blob에 동시에 해당될 때 Azure는 가장 비용이 저렴한 작업만 적용합니다. 비용 우선순위 순서는: delete(가장 저렴) < tierToArchive < tierToCool < tierToCold입니다. 이 시나리오에서는 스토리지 비용을 완전히 없애는 delete가 가장 저렴하므로 delete가 선택됩니다.

---

### 문제 16

스토리지 계정의 액세스 키를 90일마다 수동으로 교체해야 합니다. 키가 이 기간 내에 교체되지 않으면 Azure Portal에 미리 알림이 표시되길 원합니다. 무엇을 구성해야 합니까?

- A. 키의 마지막 사용 시간 메트릭에 대한 Azure Monitor 경고 규칙
- B. 스토리지 계정의 키 만료 정책
- C. 스토리지 계정의 수명 주기 관리 정책
- D. 스토리지 계정 소유자를 대상으로 하는 작업 그룹

**정답: B**

**해설:** 키 만료 정책(스토리지 계정의 액세스 키 블레이드 → 교체 미리 알림 설정)은 최대 키 사용 기간을 정의합니다. 키가 지정된 일수를 초과하여 교체되지 않으면 Azure가 Portal에 미리 알림 배너를 표시합니다. 이 기능은 수동 키 교체 워크플로를 위해 특별히 설계되었습니다.

---

### 문제 17

Azure 파일 공유가 현재 Microsoft Entra Domain Services 인증으로 구성되어 있습니다. 온-프레미스 Active Directory Domain Services 인증으로 전환해야 합니다. 먼저 무엇을 해야 합니까?

- A. 스토리지 계정에서 Microsoft Entra Domain Services 인증 비활성화
- B. 스토리지 계정에서 온-프레미스 AD DS 인증 활성화
- C. 스토리지 계정을 온-프레미스 도메인에 재가입
- D. 변경하기 전에 스토리지 계정 액세스 키 재생성

**정답: A**

**해설:** Azure Files는 한 번에 하나의 ID 기반 인증 소스만 지원합니다. Entra Domain Services가 아직 구성되어 있는 동안 온-프레미스 AD DS 인증을 활성화하려고 하면 실패합니다. 새 소스를 활성화하기 전에 기존 Entra Domain Services 구성을 먼저 비활성화해야 합니다.

---

### 문제 18

서브넷에 Microsoft.Storage 서비스 엔드포인트가 활성화되어 있습니다. 서브넷의 VM은 현재 모든 Azure Storage 계정에 액세스할 수 있습니다. 데이터 유출을 방지하기 위해 아웃바운드 스토리지 서비스 엔드포인트 트래픽을 특정 단일 스토리지 계정으로만 제한해야 합니다. 무엇을 구성해야 합니까?

- A. 허용된 스토리지 계정에만 VNet 방화벽 규칙 추가
- B. Storage 서비스 태그를 차단하는 아웃바운드 NSG 규칙 생성
- C. 허용된 스토리지 계정만 나열하는 서비스 엔드포인트 정책을 서브넷에 적용
- D. 허용된 스토리지 계정의 프라이빗 엔드포인트로 서비스 엔드포인트 교체

**정답: C**

**해설:** 서비스 엔드포인트 정책은 서브넷 수준에서 적용되며 정책에 나열된 Azure 리소스 식별자로만 서비스 엔드포인트 트래픽을 필터링합니다. 서브넷의 VM은 나열되지 않은 스토리지 계정에 액세스할 수 없어 데이터 유출 시나리오를 직접 해결합니다.

---

### 문제 19

Blob 컨테이너 container1에 대한 저장된 액세스 정책을 생성해야 합니다. 단일 Blob 컨테이너에 구성할 수 있는 저장된 액세스 정책의 최대 수는 얼마입니까?

- A. 10
- B. 5
- C. 20
- D. 25

**정답: B**

**해설:** Azure Blob Storage는 컨테이너당 최대 5개의 저장된 액세스 정책을 지원합니다. 각 정책은 하나 이상의 SAS 토큰에서 참조될 수 있습니다. 한도를 초과하는 새 정책을 추가하려면 기존 정책을 먼저 삭제해야 합니다.

---

### 문제 20

User1은 스토리지 계정 범위에서 Storage Account Contributor 역할이 있습니다. User1이 Azure Portal에 로그인하여 Blob 데이터를 보기 위해 Blob 컨테이너를 엽니다. Azure Portal은 어떤 인증 방법을 사용하여 Blob 데이터에 액세스합니까?

- A. User1의 Entra ID 토큰을 사용하여 생성된 사용자 위임 SAS
- B. User1의 역할 할당을 사용하는 Microsoft Entra ID OAuth 2.0 토큰
- C. 저장된 액세스 정책에 연결된 공유 액세스 서명
- D. Shared Key 인증을 통한 계정 액세스 키

**정답: D**

**해설:** Storage Account Contributor 역할에는 `Microsoft.Storage/storageAccounts/listKeys/action`이 포함됩니다. Azure Portal이 이 권한을 감지하면 계정 액세스 키를 검색하고 Shared Key 인증을 사용하여 Blob 데이터에 액세스합니다. 사용자의 Entra ID 자격 증명을 사용하지 않습니다. Entra ID 인증은 사용자에게 listKeys 권한이 없는 경우에만 Portal에서 사용됩니다.

---

## 3) 컴퓨팅 및 App Service

### 문제 21

Linux VM에 사용자 지정 스크립트 확장이 실패 상태로 표시됩니다. 실패 원인을 진단하기 위해 확장의 실행 로그를 검토해야 합니다. 어떤 로그 파일을 확인해야 합니까?

- A. /var/log/syslog
- B. /var/log/azure/custom-script-extension/handler.log
- C. /var/log/waagent.log(waagent.log만 — 다른 파일 불필요)
- D. /etc/azure/extension-error.log

**정답: B**

**해설:** Linux VM에서 사용자 지정 스크립트 확장은 `/var/log/azure/custom-script-extension/handler.log`에 상세한 실행 로그를 기록합니다. 이 파일에는 스크립트 다운로드 시도, 실행 출력 및 확장별 오류 메시지가 포함됩니다. `/var/log/waagent.log`는 Azure VM Agent 정보를 제공하지만 `handler.log`가 사용자 지정 스크립트 확장 진단의 기본 파일입니다.

---

### 문제 22

Windows 가상 머신에서 VM Insights가 활성화되어 있습니다. VM Insights 맵 탭에 데이터가 표시되지 않습니다. 성능 탭은 정상적으로 데이터를 표시합니다. 맵 기능을 활성화하려면 추가로 설치해야 하는 구성 요소는 무엇입니까?

- A. Log Analytics 게이트웨이
- B. Network Watcher Agent
- C. Dependency agent
- D. Azure 진단 확장

**정답: C**

**해설:** VM Insights 맵 기능을 사용하려면 Azure Monitor Agent 외에 Dependency agent가 필요합니다. Dependency agent는 종속성 맵을 구동하는 프로세스 수준 데이터와 네트워크 연결 정보를 수집합니다. Dependency agent 없이는 성능 메트릭은 올바르게 수집되지만 맵 탭에 프로세스 및 연결 데이터가 표시되지 않습니다.

---

### 문제 23

성능 모니터링(CPU, 메모리, 디스크, 네트워크)을 위해 가상 머신에서 VM Insights를 활성화할 계획입니다. 최소 요구 사항이 되는 두 가지 구성 요소는 무엇입니까?

- A. Log Analytics 작업 영역과 Azure Monitor Agent
- B. Dependency agent와 Log Analytics 작업 영역
- C. Azure Monitor Agent와 진단 확장
- D. Log Analytics 작업 영역과 Network Watcher Agent

**정답: A**

**해설:** VM Insights를 사용하려면 Log Analytics 작업 영역(InsightsMetrics 및 성능 데이터 저장)과 Azure Monitor Agent(데이터 수집 및 작업 영역 전달)가 필요합니다. Dependency agent는 맵 기능에만 추가로 필요합니다. 기본 성능 모니터링에는 Log Analytics 작업 영역 + Azure Monitor Agent가 최소 구성입니다.

---

### 문제 24

가용성 집합 AS1에는 2개의 장애 도메인과 5개의 업데이트 도메인이 있습니다. 모든 업데이트 도메인에 균등하게 분산된 10개의 가상 머신이 포함되어 있습니다. Microsoft가 VM 재시작이 필요한 계획된 유지 관리를 수행합니다. 계획된 유지 관리 기간 동안 동시에 사용할 수 없는 VM의 최대 수는 얼마입니까?

- A. 5
- B. 2
- C. 10
- D. 1

**정답: B**

**해설:** 계획된 유지 관리 중에 Azure는 한 번에 하나의 업데이트 도메인씩 VM을 재시작합니다. 10개의 VM이 5개의 업데이트 도메인에 균등하게 분산되면 각 업데이트 도메인에 2개의 VM이 있습니다. 한 번에 하나의 업데이트 도메인만 재시작되므로 동시에 최대 2개의 VM이 사용 불가능합니다.

---

### 문제 25

관리 디스크를 사용하는 가상 머신을 포함할 가용성 집합을 배포하는 ARM 템플릿을 작성하고 있습니다. 컴퓨팅 장애 도메인과 관리 디스크 스토리지 장애 도메인을 정렬하려면 어떤 속성을 설정해야 합니까?

- A. properties.managed = true
- B. properties.faultDomainCount = 3
- C. sku.name = Aligned
- D. sku.name = Classic

**정답: C**

**해설:** 관리 디스크를 사용하는 VM이 있는 가용성 집합의 경우 `sku.name` 속성을 `Aligned`로 설정해야 합니다. 이렇게 하면 Azure가 컴퓨팅 장애 도메인을 관리 디스크 스토리지 장애 도메인과 정렬하여 적절한 장애 격리를 제공합니다. `Classic`은 비관리 디스크를 사용하는 가용성 집합의 레거시 값입니다.

---

### 문제 26

향상된 하위 유형으로 6시간마다 백업 빈도를 사용하는 새 Azure VM 백업 정책을 생성합니다. 이 정책의 즉시 복구 스냅샷 기본 보존 기간은 얼마입니까?

- A. 1일
- B. 7일
- C. 30일
- D. 14일

**정답: B**

**해설:** 향상된 백업 정책에서 즉시 복구 스냅샷의 기본 보존 기간은 백업 빈도(2/4/6/8/12/24시간)에 관계없이 7일입니다. 즉시 복구 스냅샷은 Recovery Services vault에 데이터가 전송되기 전에 빠른 복원 작업을 위해 로컬에 저장됩니다.

---

### 문제 27

East US 지역에 있는 Windows VM VM1에서 Azure Disk Encryption을 활성화해야 합니다. VM과 동일한 Azure 지역에 있어야 하는 리소스는 무엇입니까?

- A. Recovery Services vault
- B. Log Analytics 작업 영역
- C. 암호화 키를 저장하는 Azure Key Vault
- D. 부트 진단에 사용되는 스토리지 계정

**정답: C**

**해설:** Azure Disk Encryption을 사용하려면 암호화 키(Windows의 BitLocker 키)를 저장하는 VM과 Azure Key Vault가 동일한 지역 및 구독에 있어야 합니다. 이 요구 사항은 데이터 주권(암호화 키가 지역 경계를 넘지 않도록 보장)을 확보하고 암호화 작업의 대기 시간을 최소화합니다.

---

### 문제 28

WebApp1에 스테이징 배포 슬롯이 있습니다. 스테이징 슬롯의 애플리케이션 설정 DatabaseConnection은 스테이징 데이터베이스를 가리킵니다. 스테이징 슬롯이 프로덕션으로 교체될 때 DatabaseConnection은 스테이징 슬롯에 남아 있어야 하고 프로덕션으로 이동해서는 안 됩니다. 무엇을 구성해야 합니까?

- A. 프로덕션을 대상으로 스테이징 슬롯에서 Auto Swap 활성화
- B. DatabaseConnection을 암호화된 애플리케이션 설정으로 저장
- C. DatabaseConnection을 Azure Key Vault에 저장하고 참조
- D. DatabaseConnection을 배포 슬롯 설정으로 표시(mark)

**정답: D**

**해설:** 배포 슬롯 설정(고정 설정)은 교체 작업 중에 구성된 특정 슬롯에 바인딩된 채로 유지됩니다. DatabaseConnection을 슬롯 설정으로 표시하면 교체 작업에 관계없이 스테이징 슬롯은 항상 자체 DatabaseConnection 값을 사용하고 프로덕션은 자체 값을 유지합니다.

---

### 문제 29

스테이징 배포 슬롯에 배포된 코드가 애플리케이션이 성공적으로 예열된 후 자동으로 프로덕션 슬롯을 교체하기를 원합니다. 무엇을 활성화해야 합니까?

- A. 프로덕션을 대상으로 스테이징 슬롯에서 Auto Swap
- B. 스테이징 슬롯의 상태 확인 프로브
- C. 소스 제어 리포지토리에서 지속적 배포
- D. 스테이징 슬롯의 트래픽 라우팅 비율

**정답: A**

**해설:** Auto Swap은 배포 슬롯 교체 프로세스를 자동화합니다. 스테이징 슬롯에서 프로덕션을 대상으로 활성화하면 스테이징 슬롯에 새 코드가 배포될 때 Azure가 슬롯이 예열(HTTP 요청에 응답 시작)될 때까지 기다렸다가 자동으로 프로덕션으로 교체합니다. Auto Swap은 Windows App Service 계획에서만 지원됩니다.

---

### 문제 30

웹 앱에 여러 사용자 지정 도메인이 구성되어 있습니다. 동일한 공용 IP 주소를 공유하면서 각 도메인에 별도의 TLS/SSL 인증서를 바인딩해야 합니다. 어떤 SSL 바인딩 유형을 사용해야 합니까?

- A. IP 기반 SSL
- B. SNI SSL
- C. 와일드카드 SSL
- D. 상호 TLS 인증

**정답: B**

**해설:** SNI SSL(서버 이름 표시)을 사용하면 여러 사용자 지정 도메인이 각각의 인증서를 가지면서 동일한 IP 주소를 공유할 수 있습니다. TLS 핸드셰이크 중에 클라이언트가 요청한 호스트 이름을 전송하면 서버가 올바른 인증서를 선택합니다. IP 기반 SSL은 도메인당 전용 IP 주소를 바인딩하므로 추가 비용이 발생합니다.

---

## 4) ID, RBAC 및 거버넌스

### 문제 31

Microsoft Graph PowerShell을 사용하여 Accounting 부서의 모든 사용자에 대한 사용 위치를 France(FR)로 업데이트해야 합니다. 어떤 cmdlet 조합을 사용해야 합니까?

- A. Get-AzureADUser + Set-AzureADUser
- B. Get-MgGroup + Update-MgGroup
- C. Get-MgUser + Update-MgUser
- D. Get-MgUser + Set-MgUserUsageLocation

**정답: C**

**해설:** Microsoft Graph PowerShell은 `Get-MgUser`에 부서 속성에 대한 `-Filter`를 사용하여 사용자를 검색하고 `Update-MgUser`에 `-UsageLocation` 매개변수를 사용하여 속성을 업데이트합니다. 이전 AzureAD 모듈 cmdlet(Get-AzureADUser, Set-AzureADUser)은 더 이상 사용되지 않으며 Microsoft Graph PowerShell SDK로 대체되었습니다.

---

### 문제 32

테넌트에 Microsoft Entra ID Free 라이선스가 있습니다. 부서 특성을 기반으로 사용자를 자동으로 추가하는 동적 구성원 자격 그룹을 생성할 계획입니다. 그룹을 생성하기 전에 먼저 무엇을 해야 합니까?

- A. 테넌트에서 Entra ID B2C 활성화
- B. 테넌트를 Microsoft Entra ID Premium P1 또는 P2로 업그레이드
- C. 동적 규칙의 필수 조건으로 정적 보안 그룹 생성
- D. 그룹을 생성하는 사용자에게 그룹 관리자 역할 할당

**정답: B**

**해설:** 사용자 특성 규칙을 기반으로 구성원 자격이 자동으로 관리되는 동적 구성원 자격 그룹은 Microsoft Entra ID Premium P1 또는 P2가 필요합니다. Free 계층은 정적(수동으로 관리되는) 그룹 구성원 자격만 지원합니다. 동적 그룹을 생성하기 전에 라이선스를 프로비전해야 합니다.

---

### 문제 33

User1은 구독 범위에서 Contributor 역할과 리소스 그룹 RG1에서 Reader 역할을 가지고 있습니다. RG1의 리소스에 대한 User1의 유효 권한은 무엇입니까?

- A. Contributor — RBAC는 가산적이며 Contributor는 이미 모든 Reader 권한을 포함함
- B. Reader — 가장 좁은 범위(RG1)가 구독 수준 할당을 재정의함
- C. Reader — 명시적 리소스 그룹 할당은 항상 상속된 역할을 재정의함
- D. Owner — 다른 범위에서 Contributor와 Reader를 결합하면 Owner로 에스컬레이션됨

**정답: A**

**해설:** Azure RBAC는 가산적 모델을 사용합니다. 유효 권한은 모든 적용 가능한 범위에서 모든 역할 할당의 합집합입니다. 구독 범위의 Contributor는 RG1로 상속되며 Reader가 제공하는 모든 권한을 이미 포함합니다. RG1에 Reader를 추가해도 증분 효과가 없습니다. 결과는 RG1에서 Contributor 수준 액세스입니다.

---

### 문제 34

리소스 그룹 RG1에 CanNotDelete 잠금이 적용되어 있습니다. 구독 범위에서 Owner 역할이 있는 사용자가 RG1 내의 가상 머신을 삭제하려고 합니다. 결과는 무엇입니까?

- A. Owner 역할이 관리 잠금을 재정의하므로 삭제 성공
- B. 삭제가 성공하고 규정 준수 경고가 생성됨
- C. 삭제가 실패하지만 사용자가 먼저 잠금을 제거하면 삭제 가능
- D. 관리 잠금이 Owner를 포함한 모든 RBAC 역할보다 우선하므로 삭제 실패

**정답: D**

**해설:** Azure 관리 잠금은 보유 역할에 관계없이 RBAC 권한보다 우선합니다. Owner조차도 잠금이 적용된 동안에는 CanNotDelete 잠금으로 보호된 리소스를 삭제할 수 없습니다. 그러나 Owner 역할에는 `Microsoft.Authorization/locks/*` 권한이 포함되어 있으므로 사용자는 먼저 잠금을 제거한 다음 삭제를 진행할 수 있습니다.

---

### 문제 35

Admin1은 리소스 그룹 RG1에서 Contributor 역할을 가지고 있습니다. Admin1은 RG1 내의 VM1에 CanNotDelete 잠금을 적용해야 합니다. 무엇을 해야 합니까?

- A. Admin1에게 Security Admin 역할 할당
- B. VM1 범위에서 Admin1에게 User Access Administrator 역할 할당
- C. RG1 범위에서 Admin1에게 Owner 역할 할당
- D. Admin1에게 Resource Policy Contributor 역할 할당

**정답: C**

**해설:** 관리 잠금을 생성하고 삭제하려면 `Microsoft.Authorization/locks/*` 권한이 필요합니다. Contributor 역할에는 이 권한이 포함되지 않습니다. 이 권한을 포함하는 기본 제공 역할은 Owner와 User Access Administrator입니다. 시험 표준 답변은 RG1 범위에서 Owner 역할을 할당하는 것입니다.

---

### 문제 36

관리 그룹 MG1에는 구독 Sub1과 Sub2가 포함되어 있습니다. User1은 두 구독의 모든 리소스에 대한 읽기 액세스가 필요합니다. 필요한 최소 역할 할당 수는 몇 개입니까?

- A. MG1 관리 그룹 범위에서 User1에게 Reader 할당 — 할당 1개
- B. Sub1과 Sub2에 각각 User1에게 Reader 할당 — 할당 2개
- C. 두 구독의 각 리소스 그룹에 User1에게 Reader 할당
- D. Microsoft Entra ID에서 User1에게 전역 Reader 역할 할당

**정답: A**

**해설:** 관리 그룹 범위에서의 Azure RBAC 역할 할당은 모든 자식 구독, 리소스 그룹 및 리소스에 상속됩니다. MG1에서의 단일 Reader 할당은 Sub1과 Sub2를 모두 포함하여 최소 할당 수로 요구 사항을 충족합니다. 구독 수준에서 할당하면 두 개의 별도 할당이 필요합니다.

---

### 문제 37

배포 시 리소스에 누락된 태그를 자동으로 추가하고 수정 작업을 통해 기존 비준수 리소스를 수정할 수 있는 Azure Policy를 구성해야 합니다. 어떤 정책 효과를 사용해야 합니까?

- A. append
- B. modify
- C. deny
- D. deployIfNotExists

**정답: B**

**해설:** `modify` 효과는 리소스 생성 또는 업데이트 중에 리소스 속성(태그 포함)을 추가, 업데이트 또는 제거하며 기존 비준수 리소스를 수정하기 위한 수정 작업 생성을 지원합니다. `append` 효과는 생성 중에 속성을 추가할 수 있지만 수정 작업을 지원하지 않습니다. `deployIfNotExists`는 태그 수정이 아닌 추가 리소스를 배포합니다.

---

### 문제 38

조직의 규정 준수 요구 사항을 충족하지 않는 가상 머신을 사용자가 배포하지 못하도록 해야 합니다. 비준수 배포는 오류와 함께 즉시 차단되어야 합니다. 어떤 Azure Policy 효과를 구성해야 합니까?

- A. audit
- B. auditIfNotExists
- C. deny
- D. modify

**정답: C**

**해설:** `deny` 효과는 정책 조건을 충족하지 않는 리소스 생성 및 업데이트 요청을 차단합니다. 오류 메시지와 함께 배포가 즉시 실패합니다. `audit`과 `auditIfNotExists`는 비준수를 기록할 뿐이며 배포를 차단하지 않습니다.

---

### 문제 39

리소스 그룹에 `Environment = Production` 태그가 있습니다. 그룹의 모든 리소스가 이 태그를 상속하도록 Azure Policy를 구성합니다. 이미 다른 값(예: `Development`)의 `Environment` 태그가 있는 리소스는 덮어쓰지 않아야 합니다. 어떤 modify 작업 유형을 사용해야 합니까?

- A. add
- B. addOrReplace
- C. remove
- D. copy

**정답: A**

**해설:** `add` modify 작업은 태그가 리소스에 없는 경우에만 태그를 추가합니다. 리소스에 이미 어떤 값이든 `Environment` 태그가 있으면 변경하지 않습니다. `addOrReplace`는 기존 태그 값을 덮어써서 "기존 값을 재정의하지 않는" 요구 사항을 위반합니다.

---

### 문제 40

구독에 `deny` 효과가 있는 정책 할당이 있습니다. 평가 기간 동안 새 배포를 차단하지 않고 기존 비준수 리소스가 어느 것인지 평가하려고 합니다. 정책 할당에 무엇을 구성해야 합니까?

- A. 일시적으로 효과를 deny에서 audit으로 변경
- B. 테스트 중 정책 할당 제거
- C. 정책 할당에 비준수 메시지 설정
- D. 정책 할당의 적용 모드를 Disabled로 설정

**정답: D**

**해설:** 적용 모드를 Disabled로 설정하면 리소스 생성/업데이트 작업에 정책 효과(deny)가 적용되지 않으며 동시에 리소스 규정 준수를 평가하고 상태를 보고합니다. 이는 적용 전에 영향을 평가하는 올바른 방법입니다. 효과를 변경하는 방법(옵션 A)은 할당 동작이 아닌 정책 정의 자체를 수정합니다.

---

## 5) ARM 템플릿 및 Bicep

### 문제 41

`createStorage` 매개변수가 `true`일 때만 여러 인스턴스에 대한 루프를 사용하여 스토리지 계정을 배포하는 Bicep 파일을 작성하고 있습니다. 올바른 구문은 무엇입니까?

- A. `resource sa '...' = if(createStorage) [for i in range(0, count): { ... }]`
- B. `resource sa '...' = [for i in range(0, count): if(createStorage) { ... }]`
- C. `resource sa '...' = [for i in range(0, count)] if(createStorage) { ... }`
- D. `resource sa '...' = if(createStorage) { for i in range(0, count) { ... } }`

**정답: B**

**해설:** Bicep에서 루프와 조건을 결합할 때 `for` 표현식이 `if` 조건 앞에 와야 합니다: `[for ... : if(condition) { }]`. 역순(if before for)은 구문 오류입니다. `createStorage`가 false이면 리소스 인스턴스가 배포되지 않고, true이면 `count`개의 인스턴스가 생성됩니다.

---

### 문제 42

메인 Bicep 파일이 `storageEndpoint`라는 출력을 선언하는 storageModule.bicep 모듈을 호출합니다. 메인 파일에서 이 출력 값을 어떻게 참조합니까?

- A. storageModule.outputs.storageEndpoint
- B. storageModule.output('storageEndpoint')
- C. outputs['storageModule'].storageEndpoint
- D. reference(storageModule).storageEndpoint

**정답: A**

**해설:** Bicep에서 모듈 출력은 `<모듈기호이름>.outputs.<출력이름>` 구문을 사용하여 참조합니다. 모듈 기호 이름은 모듈 선언에서 사용된 별칭입니다(예: `module storageModule 'storageModule.bicep' = { ... }`). 이것은 중첩 모듈에서 생성된 값을 사용하는 표준 Bicep 패턴입니다.

---

### 문제 43

Bicep 템플릿에 `param createNewStorage bool = true`와 `param storageCount int = 3`이 있습니다. `createNewStorage`가 true일 때만 각 인덱스에 대해 스토리지 계정을 배포하려고 합니다. 이를 올바르게 구현하는 코드는 무엇입니까?

- A. `resource sa '...' = if(createNewStorage) [for i in range(0, storageCount): { ... }]`
- B. `resource sa '...' = [for i in range(0, storageCount): if(createNewStorage) { ... }]`
- C. `resource sa '...' = [for i in range(0, storageCount): { condition: createNewStorage, ... }]`
- D. 옵션 A와 B 모두 유효한 구문

**정답: B**

**해설:** 조건부 루프의 올바른 Bicep 구문은 `for`를 `if` 앞에 배치합니다: `[for i in range(0, storageCount): if(createNewStorage) { ... }]`. `if` 표현식을 `for` 표현식 외부에 배치하는 옵션 A는 잘못된 Bicep 구문입니다. `createNewStorage`가 `false`이면 인스턴스가 배포되지 않고, `true`이면 `storageCount`개의 인스턴스가 배포됩니다.

---

### 문제 44

프로덕션 구독에 deny 효과가 있는 정책 할당이 있습니다. 적용 전에 적용 모드를 Disabled로 설정하여 어떤 기존 리소스가 영향을 받는지 평가하려고 합니다. Disabled 적용 모드에 대한 올바른 설명은 무엇입니까?

- A. 적용이 비활성화되면 규정 준수 데이터가 수집되지 않음
- B. 정책이 새 배포를 차단하지만 기존 리소스를 감사하지 않음
- C. 규정 준수가 평가 및 보고되지만 리소스 작업이 차단되지 않음
- D. Disabled 모드는 정책 할당을 완전히 비활성화함

**정답: C**

**해설:** 적용 모드를 Disabled로 설정하면 리소스 생성/업데이트 작업에 정책 효과(예: deny, modify)가 적용되지 않습니다. 그러나 Azure Policy는 계속해서 리소스를 평가하고 규정 준수 결과를 규정 준수 대시보드에 표시합니다. 이를 통해 팀이 적용을 켜기 전에 정책의 영향을 파악할 수 있습니다.

---

## 6) Site Recovery, 모니터링 및 AKS

### 문제 45

Azure Monitor 작업 그룹을 구성하여 security@contoso.com으로 경고 이메일을 보냅니다. 대규모 경고 폭발 기간 동안 보안 팀이 처음 몇 개의 이메일 이후에 알림을 받지 못한다고 보고합니다. Azure Monitor가 시간당 단일 이메일 주소로 보낼 수 있는 최대 이메일 알림 수는 얼마입니까?

- A. 10
- B. 100
- C. 500
- D. 1,000

**정답: B**

**해설:** Azure Monitor 작업 그룹은 수신자 이메일 주소당 시간당 100개의 이메일로 속도 제한을 적용합니다. 이 한도에 도달하면 후속 이메일이 다음 시간까지 억제됩니다. 수신자에게 한도가 초과되었다는 속도 제한 알림이 전송됩니다. 이는 알려진 Azure 측 제약으로 구성할 수 없습니다.

---

### 문제 46

Azure Monitor 경고가 올바르게 발생하고 있습니다. 보안 팀은 경고가 활성화되어 있음에도 불구하고 가끔 이메일 알림을 받지 못한다고 보고합니다. 작업 그룹 구성이 올바르고 경고가 Portal에서 확인됩니다. 가장 가능한 원인은 무엇입니까?

- A. 작업 그룹이 지원되는 최대 이메일 수신자 수를 초과함
- B. 수신자의 메일 서버의 SMTP 필터가 Azure Monitor 이메일을 차단함
- C. Azure Monitor가 이메일 알림에 속도 제한을 적용함
- D. 경고 규칙 평가 빈도가 모든 이벤트를 감지하기에 너무 낮음

**정답: C**

**해설:** 대규모 경고 볼륨 기간 동안 간헐적인 알림 실패 패턴은 Azure Monitor의 이메일 속도 제한(수신자당 시간당 100개)의 특징적인 증상입니다. 한 시간 내에 버스트로 한도에 도달하면 후속 알림이 해당 시간 동안 삭제됩니다. 시간 창이 재설정된 후 문제가 자동으로 해결됩니다.

---

### 문제 47

Azure Site Recovery를 사용하여 가상 머신이 보조 Azure 지역으로 장애 조치되었습니다. 장애 조치가 커밋되었습니다. 이제 기본 지역을 다시 사용할 수 있으며 VM을 다시 복제하려고 합니다. 먼저 무엇을 해야 합니까?

- A. 보조 지역의 VM에서 다시 보호(Re-protect) 작업 수행
- B. Recovery Services vault에서 새 복제 정책 생성
- C. 보조 지역에서 기본 지역으로 계획된 장애 조치 시작
- D. 기본 지역에서 원래 VM 구성 수동 복원

**정답: A**

**해설:** 장애 조치가 커밋된 후 보조 지역에서 기본 지역으로의 복제는 자동으로 구성되지 않습니다. 다시 보호(Re-protect) 작업은 보조 지역에서 기본 지역으로(역방향) 복제하도록 Site Recovery를 재구성합니다. 장애 복구(기본 지역으로의 계획된 장애 조치)가 시작되기 전에 반드시 다시 보호 작업을 완료해야 합니다.

---

### 문제 48

가상 머신이 보조 지역으로 장애 조치되어 성공적으로 작동이 확인되었습니다. 장애 조치를 커밋합니다. 커밋 후 기존 복구 지점에 어떤 일이 발생합니까?

- A. 복구 지점이 30일 동안 기본 지역에 보존됨
- B. 복구 지점이 보조 지역으로 자동 이동됨
- C. 복구 지점이 롤백을 위해 그대로 유지됨
- D. 기존 복구 지점이 모두 삭제됨

**정답: D**

**해설:** 장애 조치를 커밋하면 작업이 완료되어 보조 지역이 영구 활성 위치로 지정됩니다. Azure Site Recovery는 커밋 후 롤백이 더 이상 불가능하므로 기존 복구 지점을 모두 삭제합니다. 이 작업은 복구 지점 관점에서 되돌릴 수 없습니다 — 커밋된 후에는 복구 지점이 사라집니다.

---

### 문제 49

App Service 웹 앱에 시스템 할당 관리 ID가 있습니다. 앱은 Azure RBAC 권한 모델로 구성된 Azure Key Vault에서 Key Vault 참조를 사용하여 비밀을 읽습니다. 관리 ID에 할당해야 하는 최소 역할은 무엇입니까?

- A. Key Vault Reader
- B. Key Vault Contributor
- C. Key Vault Secrets User
- D. Key Vault Administrator

**정답: C**

**해설:** Key Vault Secrets User는 비밀 값을 검색(읽기)하는 데 필요한 최소 권한인 `Microsoft.KeyVault/vaults/secrets/getSecret/action`을 제공합니다. Key Vault Reader는 메타데이터 액세스만 제공하며 비밀 내용을 읽을 수 없습니다. Key Vault Contributor는 ARM 관리 평면 역할로 Key Vault 데이터 평면(비밀, 키, 인증서)에 액세스할 수 없습니다.

---

### 문제 50

AKS 클러스터에 단일 시스템 노드 풀이 있습니다. 중단을 허용할 수 있는 배치 처리 워크로드를 실행하기 위한 Spot 노드 풀을 추가하려고 합니다. Spot 노드 풀에 선택해야 하는 노드 풀 모드는 무엇입니까?

- A. System
- B. User
- C. 시스템 또는 사용자 — Spot VM은 모두에서 사용 가능
- D. Spot 가격을 선택하면 Spot 모드가 자동으로 할당됨

**정답: B**

**해설:** AKS Spot 노드 풀은 사용자 노드 풀로만 생성할 수 있습니다. 시스템 노드 풀은 고가용성이 필요한 중요한 클러스터 구성 요소(CoreDNS, metrics-server)를 호스팅합니다 — 언제든지 선점될 수 있는 Spot VM은 이 요구 사항과 호환되지 않습니다. Spot 노드 풀은 클러스터 안정성을 위해 최소 하나의 시스템 노드 풀을 유지하면서 보조 사용자 풀이어야 합니다.

---

_AZ-104 과정 내용에서 생성된 문서. 마지막 업데이트: 2026-05-22_
