# AZ-104 예상 문제 (세트 9) — 한국어

## 1) ID, 거버넌스, RBAC

### 문제 1

Azure 구독에 사용자가 있습니다. 이 사용자는 구독 범위에서 Contributor 역할을, 구독 내 특정 스토리지 계정에서 Reader 역할을 가지고 있습니다. 해당 스토리지 계정에 대한 유효 권한은 무엇입니까?

- A. Reader — 좁은 범위에 있는 더 제한적인 역할이 우선하기 때문
- B. Contributor — RBAC 권한은 합산되므로 상위 범위 권한이 하위 범위 할당으로 줄어들지 않기 때문
- C. 액세스 없음 — 충돌하는 역할 할당이 서로 상쇄되기 때문
- D. Reader — 리소스에 더 가까운 역할 할당이 항상 우선하기 때문

**정답: B**

**해설:** Azure RBAC 권한은 합산됩니다. 구독 범위에서 Contributor, 리소스 범위에서 Reader를 가진 경우 두 권한 집합이 모두 적용됩니다. 유효 권한은 모든 할당의 합집합이므로 해당 스토리지 계정에서 Contributor 수준의 접근 권한이 유지됩니다. 하위 범위의 Reader 할당은 상위 범위의 Contributor를 줄이지 않습니다.

---

### 문제 2

관리 ID(managed identity)가 스토리지 계정에서 Blob 데이터를 읽을 수 있지만 스토리지 계정 리소스 자체를 관리하지 못하도록 하는 사용자 지정 RBAC 역할을 만들려고 합니다. 역할 정의에서 어떤 권한 속성을 채워야 합니까?

- A. Actions
- B. NotActions
- C. DataActions
- D. NotDataActions

**정답: C**

**해설:** Blob 데이터 읽기는 데이터 플레인 작업입니다. Azure RBAC는 컨트롤 플레인 권한(Actions/NotActions)과 데이터 플레인 권한(DataActions/NotDataActions)을 분리합니다. 스토리지 계정 관리 권한 없이 Blob 읽기 접근 권한만 부여하려면 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`를 `DataActions`에 지정해야 합니다.

---

### 문제 3

다음 정의의 사용자 지정 RBAC 역할이 있습니다:

```json
{
  "Actions": ["Microsoft.CostManagement/*"],
  "NotActions": ["Microsoft.CostManagement/exports/delete"]
}
```

이 역할만 구독 범위에서 할당된 사용자에게 어떤 설명이 맞습니까?

- A. 와일드카드 Actions가 NotActions를 재정의하므로 내보내기 삭제를 포함한 모든 Cost Management 작업 가능
- B. NotActions는 항상 Actions를 재정의하므로 내보내기 삭제를 제외한 모든 Cost Management 작업 가능
- C. NotActions가 전체 네임스페이스를 차단하므로 어떤 Cost Management 작업도 불가능
- D. Actions의 와일드카드가 NotActions보다 더 구체적이므로 내보내기 삭제 가능

**정답: B**

**해설:** Azure RBAC에서 `NotActions`는 와일드카드를 사용하더라도 항상 `Actions`를 재정의합니다. `Microsoft.CostManagement/*` 와일드카드는 모든 Cost Management 권한을 부여하지만, `NotActions: Microsoft.CostManagement/exports/delete`가 해당 작업 하나를 명시적으로 제거합니다. 결과는 내보내기 삭제를 제외한 전체 Cost Management 접근 권한입니다.

---

### 문제 4

Microsoft Entra 테넌트에 Windows 11을 실행하는 모든 디바이스를 멤버로 자동 포함하는 그룹을 만들어야 합니다. 어떤 유형의 그룹을 만들어야 합니까?

- A. 동적 사용자 멤버십을 사용하는 Microsoft 365 그룹
- B. 할당된 멤버십을 사용하는 보안 그룹
- C. 동적 디바이스 멤버십을 사용하는 보안 그룹
- D. 동적 디바이스 멤버십을 사용하는 Microsoft 365 그룹

**정답: C**

**해설:** 동적 디바이스 멤버십은 보안 그룹에서만 지원되며, Microsoft 365 그룹에서는 지원되지 않습니다. Microsoft 365 그룹은 사용자만 멤버로 포함할 수 있습니다. OS 조건을 기반으로 디바이스를 자동 포함하려면 보안 그룹을 만들고 동적 디바이스 멤버십 규칙(예: `device.deviceOSType -eq "Windows"`)을 설정해야 합니다.

---

### 문제 5

Microsoft Entra 테넌트에서 동적 멤버십 그룹을 만들 계획입니다. 동적 그룹에는 2,000명의 고유 사용자가 포함될 예정입니다. 라이선스 요구 사항은 무엇입니까?

- A. 모든 사용자에게 Microsoft Entra ID Free 라이선스
- B. 그룹을 관리하는 관리자에게만 Microsoft Entra ID P1 라이선스
- C. 동적 그룹의 멤버인 2,000명 모두에게 Microsoft Entra ID P1 라이선스
- D. 2,000명 모두에게 Microsoft Entra ID P2 라이선스

**정답: C**

**해설:** 동적 멤버십 그룹을 사용하려면 하나 이상의 동적 그룹에 속한 각 고유 사용자에게 Microsoft Entra ID Premium P1 라이선스가 필요합니다. 2,000명의 멤버가 있는 경우 최소 2,000개의 P1 라이선스가 필요합니다. 라이선스가 필요한 것은 관리자가 아니라 멤버 사용자입니다. 동적 그룹의 디바이스에는 라이선스가 필요하지 않습니다.

---

### 문제 6

Sales, Marketing, Finance 부서 사용자를 포함하는 동적 멤버십 규칙을 만들려고 합니다. 효율적인 평가를 위해 규칙을 최적화하고 싶습니다. 어떤 연산자를 사용해야 합니까?

- A. `-match`
- B. `-like`
- C. `-contains`
- D. `-in`

**정답: D**

**해설:** `-in` 연산자는 동일한 속성에 대한 여러 값을 단일 조건으로 지정할 수 있으며 가장 효율적인 접근 방식입니다. `-eq`를 사용한 여러 `-or` 문은 처리 시간이 늘어나고 규칙이 복잡해집니다. Microsoft는 여러 OR 조건 대신 `-in`을 명시적으로 권장합니다: `user.department -in ["Sales", "Marketing", "Finance"]`. `-match`와 `-contains` 연산자는 패턴 매칭을 사용하므로 정확한 값 비교에는 더 비효율적입니다.

---

### 문제 7

귀하는 구독 Sub1에 대해 Contributor 역할을 가진 Azure 관리자입니다. Sub1은 현재 관리 그룹 MG-Finance 아래에 있으며, 이 그룹에서 구독에 대한 상속된 Owner 역할을 가지고 있습니다. Sub1을 명시적 Owner 역할을 가진 MG-IT로 이동해야 합니다. 이 이동을 수행할 수 있습니까?

- A. 아니요 — Sub1에 대한 Owner 역할이 상속된 것이지 명시적이지 않기 때문
- B. 아니요 — 대상 관리 그룹에 Contributor 역할이 필요하기 때문
- C. 예 — MG-IT에 Owner 권한이 있고 원본 그룹에 충분한 권한이 있기 때문
- D. 아니요 — 글로벌 관리자만 구독을 관리 그룹 간에 이동할 수 있기 때문

**정답: C**

**해설:** 구독을 관리 그룹 간에 이동하려면 대상 관리 그룹에 Owner 또는 동등한 권한이 필요합니다. MG-IT에 명시적 Owner 역할이 있으므로 대상 요구 사항이 충족됩니다. MG-Finance에서 Sub1에 대한 상속된 Owner는 충분한 원본 접근 권한을 제공합니다. 상속된 역할은 명시적으로 할당된 역할과 동일하게 유효한 Azure RBAC 역할입니다.

---

## 2) Azure Policy 및 거버넌스

### 문제 8

구독 범위의 Azure Policy가 West US 지역으로만 리소스 배포를 제한합니다. 추가 정책 할당을 만들지 않고 TestRG 리소스 그룹을 이 정책 평가에서 영구적으로 제외해야 합니다. 무엇을 구성해야 합니까?

- A. TestRG에 대한 Policy Exemption
- B. TestRG를 포함하도록 정책 할당의 `notScopes` 속성 구성
- C. DoNotEnforce 모드로 TestRG 범위에 새 정책 할당 생성
- D. 정책 할당의 `resourceSelectors` 필터 설정

**정답: B**

**해설:** 정책 할당의 `notScopes` 속성은 특정 리소스 컨테이너를 평가에서 영구적으로 제외합니다. TestRG의 리소스 ID를 `notScopes`에 추가하면 추가 정책 할당 없이 해당 리소스 그룹이 제외됩니다. Policy Exemption은 별도의 리소스 개체를 생성합니다. `resourceSelectors`는 리소스 속성에 따른 조건부 평가에 사용되며 영구 범위 제외 목적이 아닙니다.

---

### 문제 9

특정 태그가 있는지 여부를 기준으로 리소스 준수 여부를 평가하는 사용자 지정 Azure Policy 정의를 만들 계획입니다. 태그를 지원하지 않는 리소스가 평가에서 자동으로 제외되도록 해야 합니다. 어떤 `mode`를 구성해야 합니까?

- A. All
- B. Indexed
- C. Microsoft.Kubernetes.Data
- D. Microsoft.Network.Data

**정답: B**

**해설:** `mode`를 `Indexed`로 설정하면 태그와 위치를 지원하는 리소스 유형만 평가됩니다. 이러한 속성을 지원하지 않는 리소스는 평가에서 자동으로 제외됩니다. `All` 모드로 설정하면 모든 리소스 유형이 평가되어 태그를 지원하지 않는 리소스에 대한 오류나 노이즈가 발생할 수 있습니다.

---

### 문제 10

테넌트의 모든 구독에서 12개의 Azure Policy 정의를 함께 적용하고 그룹 내 여러 정책에 동일한 매개변수 값을 전달하려고 합니다. 무엇을 만들어야 합니까?

- A. 포함된 하위 정책이 있는 사용자 지정 정책 정의
- B. 여러 정책 ID가 있는 Azure Policy 할당
- C. Azure Policy 이니셔티브 정의(정책 집합)
- D. 기본 제공 정책이 있는 관리 그룹

**정답: C**

**해설:** Azure Policy 이니셔티브(정책 집합 정의)는 여러 정책 정의를 함께 번들로 묶고 단일 할당으로 모두 적용할 수 있습니다. 또한 집합 내 여러 정책 정의에 전달할 수 있는 공유 매개변수를 지원하여 관리를 단순화합니다. 이는 관련된 준수 요구 사항 그룹을 함께 적용하는 권장 방법입니다.

---

### 문제 11

테넌트의 모든 구독에 걸쳐 리소스 그룹에 할당할 수 있는 사용자 지정 Azure Policy 정의를 저장해야 합니다. 정책 정의를 어디에 저장해야 합니까?

- A. 구독 범위
- B. 루트 관리 그룹 범위
- C. 리소스 그룹 범위
- D. 테넌트 내 개별 구독

**정답: B**

**해설:** 관리 그룹 범위에 저장된 정책 정의는 모든 구독 및 리소스 그룹을 포함한 모든 하위 범위에 할당할 수 있습니다. 루트 관리 그룹에 저장하면 정의를 테넌트 전체에서 사용할 수 있습니다. 구독 범위에 저장된 정의는 해당 구독 내에서만 사용 가능합니다.

---

## 3) 스토리지, SAS, 수명 주기 관리

### 문제 12

storage1이라는 GPv1(General Purpose v1) 스토리지 계정이 있습니다. 가상 네트워크에서 storage1에 대한 프라이빗 엔드포인트를 만들어야 합니다. 먼저 무엇을 해야 합니까?

- A. storage1에 대해 Azure Defender for Storage 활성화
- B. storage1을 GPv1에서 GPv2로 업그레이드
- C. 먼저 서브넷에 서비스 엔드포인트 만들기
- D. 프라이빗 엔드포인트 생성을 허용하는 방화벽 규칙 구성

**정답: B**

**해설:** 스토리지 계정의 Azure Private Endpoint는 GPv2(General Purpose v2) 또는 Blob Storage 계정이 필요합니다. GPv1 계정은 Private Endpoint 생성을 지원하지 않습니다. 첫 번째 단계는 storage1을 GPv2로 업그레이드하는 것입니다. 업그레이드는 비파괴적이며 기존 데이터를 모두 보존합니다.

---

### 문제 13

특정 IP 주소 범위에서의 요청으로 접근을 제한하는 SAS 토큰을 만들어야 합니다. SAS 토큰의 `allowedIPAddresses` 매개변수를 구성합니다. 이 제한에 지원되는 IP 주소 유형은 무엇입니까?

- A. 가상 네트워크의 프라이빗 IP 주소
- B. 공용 및 프라이빗 IP 주소 모두
- C. 공용 IP 주소만
- D. Azure 백본 네트워크 내 IP 주소만

**정답: C**

**해설:** SAS 토큰의 IP 주소 제한은 공용 인터넷 라우팅 가능한 IP 주소만 지원합니다. 프라이빗 IP 주소(10.x.x.x, 172.16.x.x, 192.168.x.x)는 사용할 수 없습니다. SAS IP 제한은 공용 엔드포인트에서 평가되기 때문입니다. Azure Storage 서비스는 공용 엔드포인트에 도착하는 요청을 수신하고 클라이언트 IP를 비교하며, 이 IP는 공용 IP여야 합니다.

---

### 문제 14

90일 후에 Blob을 Archive 계층으로 이동하는 수명 주기 관리 정책이 있는 스토리지 계정이 있습니다. 스토리지 계정에는 블록 Blob과 페이지 Blob이 모두 포함되어 있습니다. 어떤 Blob이 정책에 의해 Archive 계층으로 전환됩니까?

- A. 블록 Blob과 페이지 Blob 모두
- B. 페이지 Blob만
- C. 블록 Blob만
- D. 추가 Blob만

**정답: C**

**해설:** Azure 수명 주기 관리 정책은 블록 Blob만 Archive 계층으로 전환할 수 있습니다. 페이지 Blob(VM 디스크 및 임의 읽기/쓰기 워크로드에 사용됨)과 추가 Blob은 Archive 계층을 지원하지 않습니다. 블록 Blob만 전체 계층화 수명 주기를 지원합니다: Hot → Cool → Cold → Archive.

---

### 문제 15

Cool 계층에서 15일만 지낸 Blob이 Archive 계층으로 이동했습니다. 이 조기 이동의 결과는 무엇입니까?

- A. 이동이 허용되지 않음 — Blob은 Archive로 전환하기 전에 Cool에 30일 있어야 함
- B. Cool 계층 최소 보존 기간의 남은 15일에 대한 조기 삭제 페널티 적용
- C. 페널티 없음 — Cool 계층에 최소 보존 요구 사항이 없음
- D. Archive로 전환하기 전에 Blob이 자동으로 Hot 계층으로 반환됨

**정답: B**

**해설:** Cool 계층의 권장 최소 보존 기간은 30일입니다. 30일 전에 Blob을 삭제하거나 다른 계층으로 이동하면 남은 일수에 대한 조기 삭제 페널티가 적용됩니다. 이 경우 Blob이 15일 후에 이동했으므로 남은 15일에 대한 조기 삭제 요금이 적용됩니다. Archive 계층도 180일의 최소 보존 기간과 동일한 페널티 구조를 가집니다.

---

### 문제 16

Azure Portal에서 스토리지 계정의 `액세스 키` 블레이드를 열었는데 `순환 미리 알림 설정` 버튼이 회색으로 표시되어 있습니다. 키 만료 정책을 만들기 전에 무엇을 해야 합니까?

- A. Microsoft Defender for Storage 활성화
- B. 스토리지 계정을 Premium 성능 계층으로 업그레이드
- C. 각 계정 액세스 키를 한 번 이상 순환
- D. Azure Key Vault 통합 활성화

**정답: C**

**해설:** 키 만료 정책을 만들기 전에 두 액세스 키 모두에 대해 `keyCreationTime` 속성이 채워져 있어야 합니다. 스토리지 계정을 처음 만들 때 키는 존재하지만 한 번도 순환되지 않아 `keyCreationTime`이 null입니다. 각 키를 한 번 이상 순환하여 생성 타임스탬프를 설정해야 합니다. 그러면 키 만료 정책 기능을 사용할 수 있게 됩니다.

---

### 문제 17

AD DS 인증(SMB)을 사용하는 Azure 파일 공유에서 사용자에게 파일을 읽고, 쓰고, 삭제하는 기능이 필요합니다. 사용자는 파일 또는 디렉터리 권한을 수정할 수 없어야 합니다. 파일 공유 범위에서 어떤 기본 제공 역할을 할당해야 합니까?

- A. Storage File Data SMB Share Reader
- B. Storage File Data SMB Share Contributor
- C. Storage File Data SMB Share Elevated Contributor
- D. Storage Blob Data Contributor

**정답: B**

**해설:** `Storage File Data SMB Share Contributor` 역할은 SMB 파일 공유 데이터에 대한 읽기, 쓰기, 삭제 권한을 부여하지만 ACL 변경이나 권한 수정 기능은 포함하지 않습니다. `Elevated Contributor` 역할은 권한 관리 기능을 추가하여 요구 사항을 초과합니다. `Reader` 역할은 읽기 전용이며, `Storage Blob Data Contributor`는 파일 공유가 아닌 Blob 스토리지에 적용됩니다.

---

## 4) 네트워킹: NSG, ASG, Load Balancer, 라우팅

### 문제 18

Azure NSG에 다음 인바운드 규칙이 있습니다:
- Rule1: 우선순위 200, 원본: 10.0.0.0/24, 포트: 22, 작업: Allow
- Rule2: 우선순위 100, 원본: Any, 포트: 22, 작업: Deny

IP 주소 10.0.0.5의 관리자가 SSH를 통해 VM에 연결하려고 합니다. 결과는 무엇입니까?

- A. Rule1(Allow)이 10.0.0.5와 일치하므로 연결 허용
- B. Rule2(우선순위 100, 모든 소스 Deny)가 먼저 평가되므로 연결 거부
- C. Allow 규칙이 항상 Deny를 재정의하므로 연결 허용
- D. 10.0.0.5에 대한 특정 규칙이 없으므로 연결 거부

**정답: B**

**해설:** NSG 규칙은 오름차순 우선순위 순서(낮은 숫자 = 높은 우선순위)로 평가됩니다. Rule2는 우선순위 100을 가지며 Any 소스에서 포트 22의 모든 트래픽을 거부합니다. Rule1은 우선순위 200을 가지며 10.0.0.5를 허용하지만 Rule2 이후에 평가됩니다. Rule2가 먼저 일치하고 작업이 Deny이므로 연결이 차단됩니다. 관리자 IP 10.0.0.5는 Rule2의 "Any" 소스 범위에 포함됩니다.

---

### 문제 19

다른 모든 사용자 지정 규칙보다 먼저 평가되도록 새 인바운드 NSG 규칙에 가능한 가장 높은 우선순위 값을 할당하려고 합니다. 어떤 우선순위 값을 사용해야 합니까?

- A. 1
- B. 50
- C. 100
- D. 4096

**정답: C**

**해설:** NSG 사용자 지정 규칙 우선순위 값은 100에서 4096 사이여야 합니다. 낮은 숫자는 높은 우선순위를 나타냅니다(먼저 평가). 따라서 100은 허용되는 최솟값이며 사용자 지정 규칙의 가능한 가장 높은 우선순위를 나타냅니다. 100 미만의 값은 사용자 지정 규칙에 유효하지 않습니다. 65500 같은 값은 Azure의 기본 시스템 규칙에 예약되어 있습니다.

---

### 문제 20

Subnet1과 Subnet2 두 개의 서브넷이 있는 Azure 가상 네트워크가 있습니다. ASG-Web이라는 Application Security Group(ASG)에는 이미 Subnet1의 VM에서 온 네트워크 인터페이스가 포함되어 있습니다. Subnet2에서 VM NIC를 ASG-Web에 추가할 계획입니다. 이것이 작동합니까?

- A. 아니요 — ASG 멤버는 모두 같은 서브넷에 있어야 하므로
- B. 예 — 모든 NIC가 같은 가상 네트워크에 있으면 됩니다
- C. 아니요 — ASG는 5개 이상의 네트워크 인터페이스를 포함할 수 없으므로
- D. 예 — 단, Subnet2에 공용 IP가 할당된 경우에만

**정답: B**

**해설:** Application Security Group은 서브넷에 구애받지 않습니다. 같은 가상 네트워크 내의 모든 NIC는 속한 서브넷에 관계없이 ASG에 추가할 수 있습니다. 유일한 요구 사항은 ASG의 모든 NIC가 같은 가상 네트워크에 있어야 한다는 것입니다. ASG는 서로 다른 가상 네트워크에 걸쳐 사용할 수 없습니다. ASG당 NIC 수는 5개로 제한되지 않습니다.

---

### 문제 21

포트 80에 대한 부하 분산 규칙이 있는 Azure Standard Load Balancer가 있습니다. 여러 규칙이 같은 백엔드 포트를 사용하도록 허용하고 백엔드 인스턴스로 전송되는 트래픽에서 원래 대상 IP 주소를 보존해야 합니다. 부하 분산 규칙에서 무엇을 활성화해야 합니까?

- A. 세션 지속성(소스 IP 선호도)
- B. HA 포트
- C. 부동 IP(Direct Server Return)
- D. 상태 프로브 프로토콜 TCP

**정답: C**

**해설:** 부동 IP(Direct Server Return 또는 DSR라고도 함) 모드는 Load Balancer가 트래픽을 전달하는 방식을 변경합니다. 부동 IP가 활성화되면 패킷의 대상 IP가 백엔드 IP로 NAT되지 않아 원래 프런트엔드 IP가 보존됩니다. 또한 여러 부하 분산 규칙이 같은 백엔드 포트를 공유할 수 있습니다. SQL Server Always On, NVA 등의 시나리오에서 일반적으로 사용됩니다.

---

### 문제 22

서브넷에 다음 경로가 있는 가상 네트워크가 있습니다:
- 시스템 경로: 10.0.0.0/16, 다음 홉: Virtual network
- 사용자 정의 경로(UDR): 10.0.0.0/24, 다음 홉: Virtual appliance
- BGP 경로: 10.0.0.0/24, 다음 홉: Virtual network gateway

IP 주소 10.0.0.50으로 트래픽이 전송됩니다. 어떤 다음 홉이 사용됩니까?

- A. Virtual network(시스템 경로, /16)
- B. Virtual network gateway(BGP 경로, /24)
- C. Virtual appliance(UDR, /24)
- D. 여러 /24 경로가 충돌할 때 Azure가 무작위로 경로를 선택함

**정답: C**

**해설:** Azure 경로 선택은 동일한 접두사 길이를 가진 여러 경로가 있을 때 엄격한 우선순위 순서를 따릅니다. /24 경로의 경우 사용자 정의 경로(UDR)는 BGP 경로 및 시스템 경로보다 우선합니다. 같은 /24 접두사를 가진 BGP 경로보다 가상 어플라이언스를 가리키는 UDR이 이깁니다. 서로 다른 접두사 길이(/16 대 /24)를 가진 경로 중에서는 경로 유형에 관계없이 더 구체적인 접두사(/24)가 선호됩니다.

---

### 문제 23

서브넷에 두 개의 NSG가 연결된 VM이 있습니다: NSG1은 서브넷에, NSG2는 VM의 네트워크 인터페이스에 연결되어 있습니다. 인터넷 사용자가 TCP 포트 443으로 VM에 연결하려고 합니다. 연결이 성공하려면 어떤 조건이 충족되어야 합니까?

- A. NSG1 또는 NSG2 중 하나만 트래픽을 허용하면 됨(더 관대한 쪽)
- B. NSG1과 NSG2 모두 인터넷에서 TCP 443을 허용하는 인바운드 규칙이 있어야 함
- C. NIC 수준의 NSG2만 트래픽을 허용하면 됨
- D. 서브넷 수준의 NSG1만 트래픽을 허용하면 됨

**정답: B**

**해설:** 서브넷 수준 NSG와 NIC 수준 NSG가 모두 적용되면 인바운드 트래픽은 두 필터를 모두 통과해야 합니다. Azure는 "가장 관대한 것이 이긴다"는 로직을 사용하지 않습니다. 두 NSG는 독립적으로 평가됩니다. 인바운드 트래픽의 경우 먼저 서브넷 NSG(NSG1)를 통과한 다음 NIC NSG(NSG2)를 통과합니다. 어느 하나라도 거부 규칙이 있거나 허용 규칙이 없으면 연결이 차단됩니다.

---

## 5) Compute: VM, App Service, 컨테이너

### 문제 24

Basic 계층 App Service 플랜에서 실행되는 Azure App Service 웹앱이 있습니다. 배포 슬롯을 사용하여 블루-그린 배포 전략을 구현하고 싶습니다. 먼저 무엇을 해야 합니까?

- A. Basic 계층 플랜에서 직접 staging이라는 새 배포 슬롯 만들기
- B. App Service 설정에서 Always On 활성화
- C. App Service 플랜을 Standard 이상으로 스케일 업
- D. 트래픽을 분할하기 위한 Traffic Manager 프로필 구성

**정답: C**

**해설:** 배포 슬롯은 Free 또는 Basic App Service 플랜 계층에서 사용할 수 없습니다. Standard 계층은 최대 5개의 배포 슬롯을 지원합니다. 블루-그린 전략을 구현하려면 먼저 Standard 이상으로 스케일 업해야 합니다. 스케일 업 후 스테이징 슬롯을 만들고, 거기에 배포한 다음 슬롯 교환을 수행하여 프로덕션으로 승격할 수 있습니다.

---

### 문제 25

Free(F1) 가격 계층의 Azure App Service 웹앱이 있습니다. 사용자 지정 도메인과 SSL/TLS 바인딩을 구성해야 합니다. 먼저 무엇을 해야 합니까?

- A. App Service 관리 인증서 구입
- B. App Service 플랜을 Basic 이상으로 스케일 업
- C. DNS 공급자에서 CNAME 레코드 구성
- D. TLS/SSL 설정에서 HTTPS만 활성화

**정답: B**

**해설:** Free(F1) 계층은 사용자 지정 도메인 또는 SSL/TLS 바인딩을 지원하지 않습니다. 두 기능 모두 최소한 Basic 계층이 필요합니다. 올바른 순서는 (1) 플랜 스케일 업, (2) 사용자 지정 도메인 추가, (3) SSL 바인딩 구성입니다. 스케일 업 전에 인증서를 구입하거나 DNS를 구성해도 계층 제한이 해결되지 않습니다.

---

### 문제 26

무료 App Service 관리 인증서를 사용하여 Azure App Service의 사용자 지정 도메인을 보호할 계획입니다. 이 인증서 유형에 어떤 제한이 적용됩니까?

- A. 인증서를 바인딩하기 전에 Azure Key Vault에 저장해야 함
- B. 인증서는 90일 동안만 유효하며 수동 갱신이 필요함
- C. 와일드카드 인증서는 지원되지 않음
- D. 인증서는 IP 기반 SSL 바인딩에서만 사용할 수 있음

**정답: C**

**해설:** App Service 관리 인증서(무료)는 와일드카드 인증서(예: `*.contoso.com`)를 지원하지 않습니다. 특정 호스트 이름 바인딩만 지원합니다. 플랫폼이 자동으로 갱신하므로 수동 갱신이 필요하지 않습니다. SNI SSL 바인딩(IP 기반뿐만 아니라)에서 작동하며, Key Vault에 저장할 필요가 없습니다. 플랫폼이 완전히 관리합니다.

---

### 문제 27

Azure App Service에서 단일 IP 주소를 사용하여 여러 사용자 지정 도메인을 구성하고 각각 자체 TLS/SSL 인증서로 보호해야 합니다. 어떤 SSL 바인딩 유형을 사용해야 합니까?

- A. IP 기반 SSL
- B. SNI SSL
- C. 상호 TLS
- D. 와일드카드 인증서 바인딩

**정답: B**

**해설:** SNI SSL(Server Name Indication)은 TLS 핸드셰이크에서 제공된 호스트 이름을 사용하여 올바른 인증서를 선택함으로써 동일한 IP 주소에서 여러 SSL 인증서를 제공할 수 있습니다. IP 기반 SSL은 인증서당 전용 공용 IP가 필요하여 여러 도메인이 IP를 공유할 때 비실용적입니다. SNI SSL은 Azure App Service의 기본 권장 바인딩 유형입니다.

---

### 문제 28

Basic SKU의 ACR1이라는 Azure Container Registry가 있습니다. 지역 복제를 활성화해야 합니다. 무엇을 실행해야 합니까?

- A. `az acr replication create --registry ACR1 --location westus`
- B. `az acr update --name ACR1 --sku Standard`
- C. `az acr update --name ACR1 --sku Premium`
- D. `az acr create --name ACR1 --sku Premium --location westus`

**정답: C**

**해설:** 지역 복제는 Azure Container Registry의 Premium SKU에서만 사용할 수 있습니다. 기존 레지스트리를 Basic에서 Premium으로 업그레이드하려면 `az acr update --name ACR1 --sku Premium`을 사용합니다. `az acr create` 명령은 기존 레지스트리를 수정하는 것이 아니라 새 레지스트리를 만듭니다. 복제 명령은 SKU가 이미 Premium인 경우에만 사용할 수 있습니다.

---

### 문제 29

Azure Container Instances 컨테이너 그룹을 VNet 서브넷에 배포하여 컨테이너가 가상 네트워크의 프라이빗 리소스와 통신할 수 있도록 해야 합니다. 컨테이너 그룹을 배포하기 전에 무엇을 구성해야 합니까?

- A. 서브넷에서 서비스 엔드포인트 활성화
- B. 서브넷을 `Microsoft.ContainerInstance/containerGroups`에 위임
- C. 컨테이너 그룹용 프라이빗 DNS 영역 만들기
- D. 배포 전에 서브넷에 NSG 연결

**정답: B**

**해설:** Azure Container Instances(ACI)는 컨테이너 그룹을 배포하기 전에 서브넷이 `Microsoft.ContainerInstance/containerGroups`에 위임되어 있어야 합니다. 서브넷 위임은 이 서브넷이 ACI용으로 예약되어 있음을 Azure에 신호하여 컨테이너의 네트워킹을 관리할 수 있게 합니다. 위임 없이는 배포가 실패합니다. 서비스 엔드포인트와 NSG만으로는 충분하지 않습니다.

---

## 6) Azure Site Recovery 및 Backup

### 문제 30

Azure Site Recovery(ASR)에서 가상 머신의 장애 조치를 수행하고 VM이 올바르게 작동하는지 확인한 후 장애 조치를 커밋합니다. 장애 조치 커밋의 주요 결과는 무엇입니까?

- A. 기본 지역에서 보조 지역으로의 복제가 계속됨
- B. Site Recovery에서 VM에 사용 가능한 모든 복구 지점이 삭제됨
- C. VM이 자동으로 기본 지역으로 반환됨
- D. 새 복구 지점이 만들어지고 90일 동안 저장됨

**정답: B**

**해설:** ASR에서 장애 조치를 커밋하면 복구 지점 선택이 완료됩니다. 커밋 후 해당 VM에 대한 Site Recovery의 모든 복구 지점이 삭제됩니다. "복구 지점 변경" 옵션을 더 이상 사용할 수 없습니다. VM은 이제 Azure(또는 보조 지역)에서 완전히 실행되며, 커밋 작업은 이 상태를 영구적으로 수락했음을 신호합니다. VM을 다시 보호하려면 재보호(reprotect)를 해야 합니다.

---

### 문제 31

지역 중단 후 Azure Site Recovery를 사용하여 장애 조치를 수행했습니다. VM이 이제 보조 지역에서 실행되고 있습니다. 기본 지역으로 다시 복제하기 위한 재보호 프로세스를 시작하기 전에 VM 상태는 어떠해야 합니까?

- A. 장애 조치 진행 중
- B. 장애 조치 커밋됨
- C. 복제 정상
- D. 복구 지점 선택됨

**정답: B**

**해설:** Azure Site Recovery에서 재보호(역방향 복제)를 시작하기 전에 VM 상태가 `Failover committed`여야 합니다. 이 상태는 장애 조치가 완료되었고 VM의 현재 상태가 수락되었음을 나타냅니다. 장애 조치가 커밋되지 않은 경우 재보호 옵션을 사용할 수 없습니다. 재보호가 시작된 후 VM은 원래 기본 지역으로 다시 복제를 시작합니다.

---

### 문제 32

Azure Backup으로 보호되는 Azure 파일 공유가 있습니다. 하루에 여러 번 백업을 예약하여 데이터 손실을 최소화하는 백업 정책을 구성해야 합니다. Azure 파일 공유에 대해 하루에 예약할 수 있는 최대 백업 횟수는 몇 번입니까?

- A. 24회(매시간)
- B. 12회
- C. 6회
- D. 3회

**정답: C**

**해설:** Azure Files에 대한 Azure Backup은 하루에 최대 6회 예약 백업 스냅샷을 지원하며, 최소 4시간 간격으로 실행됩니다. 파일 공유 백업 정책에는 시간별 백업을 사용할 수 없습니다. 하루 6개의 스냅샷이 충분하지 않은 경우 다른 백업 방법과 함께 Azure File Sync 사용을 고려하십시오.

---

### 문제 33

Azure Site Recovery를 사용하여 보조 지역에 복제된 Azure VM이 있습니다. 장애 조치 후 VM이 보조 지역에서 실행됩니다. 재보호를 시작하기 전에 어떤 조건을 확인해야 합니까?

- A. 보조 지역에 새 Recovery Services vault을 만들어야 함
- B. 캐시 스토리지 계정을 삭제하고 다시 만들어야 함
- C. 장애 조치된 VM에 모바일 에이전트를 수동으로 다시 설치해야 함
- D. VM 상태가 Failover committed여야 함

**정답: D**

**해설:** 재보호 옵션은 장애 조치가 커밋된(`Failover committed` 상태) 후에만 사용할 수 있습니다. 모바일 에이전트는 자동으로 등록되므로 수동 재설치가 필요 없습니다. 새 Recovery Services vault이 필요하지 않습니다. 기존 vault이 두 방향의 복제를 모두 관리합니다. ASR은 재보호 프로세스의 일부로 대상 지역(원래 기본 지역)에 새 캐시 스토리지 계정을 자동으로 만듭니다.

---

## 7) Azure Monitor, Workbook, Service Health

### 문제 34

프로덕션 가상 머신에 대한 Azure Monitor 메트릭 경고를 만들고 있습니다. 즉각적인 주의가 필요한 심각한 문제를 나타내기 위해 가장 높은 심각도 수준을 할당해야 합니다. 어떤 심각도 값을 선택해야 합니까?

- A. 4
- B. 2
- C. 1
- D. 0

**정답: D**

**해설:** Azure Monitor 경고에서 심각도는 0(Critical)에서 4(Verbose)까지입니다. 낮은 숫자는 더 높은 심각도를 나타냅니다. 심각도 0은 가장 중요한 수준이며 즉각적인 주의가 필요한 문제에 적합합니다. 척도: 0 = Critical, 1 = Error, 2 = Warning, 3 = Informational, 4 = Verbose.

---

### 문제 35

리소스 인벤토리 데이터와 성능 메트릭을 결합하는 Azure Workbook을 설계하고 있습니다. 사용자 지정 보고서 범위를 구축하기 위해 리소스와 메타데이터를 쿼리해야 합니다. 어떤 데이터 소스를 사용해야 합니까?

- A. Azure Monitor Logs
- B. Azure Monitor Metrics
- C. Azure Resource Graph
- D. JSON

**정답: C**

**해설:** Azure Resource Graph는 Azure Workbook에서 리소스 인벤토리와 메타데이터를 쿼리하는 올바른 데이터 소스입니다. 유형, 위치, 태그, SKU 등의 리소스 속성에 대한 KQL 쿼리를 지원합니다. Azure Monitor Logs는 이벤트 및 원격 분석 데이터용이고, Metrics는 숫자 시계열 성능 데이터용이며, JSON은 동적 리소스 쿼리가 아닌 정적 드롭다운 값용입니다.

---

### 문제 36

리소스에 영향을 미친 과거 Azure 서비스 중단을 검토하고 있습니다. Azure Portal의 Azure Service Health에서 Health History 섹션을 사용합니다. 비활성 Service Health 이벤트는 Health History 섹션에 몇 일 동안 보관됩니까?

- A. 30일
- B. 60일
- C. 90일
- D. 180일

**정답: C**

**해설:** Azure Service Health는 비활성(해결된) 이벤트를 Health History 섹션에 90일 동안 보관합니다. 90일 후에는 기록 이벤트가 포털에서 더 이상 표시되지 않습니다. 장기 감사 요구 사항의 경우 Azure Monitor 진단 설정을 사용하여 Service Health 경고를 구성하고 이벤트를 Log Analytics 작업 영역, 스토리지 계정 또는 이벤트 허브로 내보내야 합니다.

---

### 문제 37

Premium SSD 관리 디스크가 있는 프로덕션 가상 머신이 있습니다. VM에서 SQL Server 데이터베이스를 호스팅합니다. 하나의 디스크에는 데이터베이스 파일이, 다른 하나에는 트랜잭션 로그 파일이 저장되어 있습니다. 트랜잭션 로그 디스크의 최적 호스트 캐싱 구성은 무엇입니까?

- A. ReadOnly 캐싱
- B. ReadWrite 캐싱
- C. 없음(캐싱 없음)
- D. 워크로드 기반 자동 캐싱

**정답: C**

**해설:** 트랜잭션 로그 디스크는 `None`(캐싱 없음)으로 구성해야 합니다. 트랜잭션 로그는 순차적 쓰기 작업이기 때문입니다. 읽기 캐싱은 쓰기 중심의 순차적 워크로드에 이점을 제공하지 않습니다. ReadWrite 캐싱은 캐시가 내구성이 없기 때문에 VM 실패 시 데이터 손실 위험이 있습니다. 데이터베이스 파일 디스크(데이터 파일용)는 예측 가능한 읽기 패턴이 있으므로 ReadOnly 캐싱의 혜택을 받습니다.

---

## 8) IaC, 네트워킹, 기타

### 문제 38

같은 리소스 그룹에 `storage1`이라는 기존 스토리지 계정이 배포되어 있습니다. 새 Bicep 템플릿을 작성하면서 재생성 없이 `storage1`을 참조하여 속성을 가져와야 합니다. 어떤 구문을 사용해야 합니까?

- A. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' = { name: 'storage1' }`
- B. `resource storage 'Microsoft.Storage/storageAccounts@2023-01-01' existing = { name: 'storage1' }`
- C. `var storage = resourceId('Microsoft.Storage/storageAccounts', 'storage1')`
- D. `import storage from 'storage1.bicep'`

**정답: B**

**해설:** Bicep의 `existing` 키워드는 재생성 없이 이미 배포된 리소스를 참조할 때 사용합니다. `existing = { name: 'storage1' }`를 사용하면 기존 스토리지 계정에 대한 참조를 얻어 속성(예: `storage.properties.primaryEndpoints.blob`)에 접근할 수 있습니다. `existing` 없이는 Bicep이 해당 이름으로 새 리소스를 만들려고 시도하여 충돌이 발생할 수 있습니다.

---

### 문제 39

Azure Blob Storage의 연결된 템플릿을 참조하는 기본 ARM 템플릿을 사용하여 App Service 플랜을 배포하고 있습니다. 연결된 템플릿 배포 리소스의 배포 모드를 구성해야 합니다. 어떤 배포 모드를 사용할 수 있습니까?

- A. Complete만
- B. Complete 또는 Incremental 모두
- C. Incremental만
- D. 연결된 템플릿에는 모드 설정이 필요 없음

**정답: C**

**해설:** ARM의 연결된 템플릿(`templateLink`로 참조됨)은 `Incremental` 배포 모드만 사용할 수 있습니다. 템플릿에 정의되지 않은 리소스를 삭제하는 `Complete` 모드는 연결된 템플릿에서 지원되지 않습니다. 이 제한은 연결된 템플릿이 부모 배포의 범위 내에서 작동하며 해당 범위의 모든 리소스를 완전히 표현하는 것으로 간주되지 않기 때문입니다.

---

### 문제 40

`stgModule`이라는 Bicep 모듈이 스토리지 계정을 배포하고 스토리지 엔드포인트를 출력합니다. 부모 템플릿에서 이 출력을 참조해야 합니다. 어떤 구문이 올바릅니까?

- A. `stgModule.outputs['storageEndpoint']`
- B. `stgModule.storageEndpoint`
- C. `output(stgModule, 'storageEndpoint')`
- D. `stgModule.outputs.storageEndpoint`

**정답: D**

**해설:** Bicep에서 모듈 출력을 참조하려면 `모듈이름.outputs.출력속성이름` 구문을 사용합니다. 이 경우 `stgModule.outputs.storageEndpoint`가 `stgModule` 모듈에서 `storageEndpoint` 출력을 가져옵니다. 이는 모듈 출력에 대한 표준 Bicep 점 표기법 접근 패턴을 따릅니다.

---

### 문제 41

Bicep 템플릿에서 스토리지 계정을 조건부로 배포하려고 합니다. 어떤 구문이 올바르게 조건부 리소스 배포를 구현합니까?

- A. `resource storage '...' when (deployStorage) = { ... }`
- B. `if (deployStorage) { resource storage '...' = { ... } }`
- C. `resource storage '...' = if (deployStorage) { ... }`
- D. `resource storage '...' = deployStorage ? { ... } : null`

**정답: C**

**해설:** Bicep에서 조건부 리소스 배포는 `=` 할당 연산자 뒤에 `if` 표현식을 배치합니다: `resource storage '...' = if (deployStorage) { ... }`. 이는 다른 언어의 명령형 스타일 `if` 블록과 다릅니다. 조건은 불리언으로 평가되어야 합니다. 조건이 거짓이면 리소스가 배포되지 않습니다.

---

### 문제 42

기본 라우팅 규칙이 있는 Application Gateway가 있습니다. URL 경로를 기반으로 다른 백엔드 풀로 트래픽을 라우팅(예: `/images/*`를 별도의 백엔드로)해야 합니다. 무엇을 구성해야 합니까?

- A. URL 경로 맵을 사용하는 경로 기반 라우팅 규칙으로 기본 라우팅 규칙 교체
- B. `/images/` 경로에 대한 새 수신기 추가
- C. 기존 백엔드 풀에 세션 선호도 구성
- D. `/images/` 트래픽을 리디렉션하는 재작성 규칙 추가

**정답: A**

**해설:** URL 경로를 기반으로 트래픽을 라우팅하려면 URL 경로 맵이 필요한 Azure Application Gateway의 경로 기반 라우팅 규칙을 사용해야 합니다. URL 경로 맵은 경로 패턴(예: `/images/*`)을 정의하고 각각을 특정 백엔드 풀 및 HTTP 설정에 매핑합니다. 기본 라우팅 규칙은 URL 경로에 관계없이 모든 트래픽을 단일 백엔드 풀로 전송합니다. URL 경로 기반 라우팅에는 별도의 수신기가 필요하지 않습니다.

---

### 문제 43

App Service VNet 통합을 구성하여 앱이 프라이빗 리소스에 접근할 수 있도록 해야 합니다. 또한 같은 VNet에서 Azure SQL Database에 대한 프라이빗 엔드포인트를 만들 계획입니다. 두 가지가 같은 서브넷에 있을 수 없다는 것을 알게 되었습니다. 이유는 무엇입니까?

- A. App Service VNet 통합에 NSG가 필요한데 프라이빗 엔드포인트 정책과 충돌하기 때문
- B. 프라이빗 엔드포인트에 App Service가 사용할 수 없는 /28 이상의 전용 서브넷이 필요하기 때문
- C. App Service VNet 통합에 위임된 서브넷이 필요하고 위임된 서브넷에는 프라이빗 엔드포인트를 만들 수 없기 때문
- D. Azure는 각 서브넷을 단일 Azure 서비스 유형으로 제한하며 둘 다 별도의 서비스로 계산되기 때문

**정답: C**

**해설:** App Service VNet 통합에는 `Microsoft.Web/serverFarms`에 대한 서브넷 위임이 필요합니다. 서브넷이 위임되면 동일한 서브넷에 프라이빗 엔드포인트를 만들 수 없습니다. 서브넷 위임과 프라이빗 엔드포인트는 상호 배타적입니다. 해결 방법은 VNet 통합을 위한 위임된 서브넷을 유지하면서 프라이빗 엔드포인트를 위한 별도의 비위임 서브넷을 만드는 것입니다.

---

### 문제 44

Azure 구독에 대한 Service Health 경고를 이메일 및 SMS 알림으로 구성해야 합니다. Service Health 경고를 위한 작업 그룹을 구성할 때 작업 그룹에 어떤 지역을 선택해야 합니까?

- A. East US(기본 Azure 지역)
- B. 가장 중요한 리소스와 같은 지역
- C. 전역(Global)
- D. West US 2

**정답: C**

**해설:** Azure Service Health 경고에 사용되는 작업 그룹은 `Global` 지역에 만들어야 합니다. Service Health는 글로벌 서비스이며 특정 지역에 연결되지 않습니다. Service Health 경고 규칙에 대한 작업 그룹을 만들거나 선택할 때 지역을 Global로 설정해야 합니다. 특정 지역의 작업 그룹은 리소스별 메트릭 및 로그 경고에 사용할 수 있습니다.

---

### 문제 45

14일 보존 기간으로 컨테이너 일시 삭제가 활성화된 스토리지 계정이 있습니다. `container1`을 삭제한 다음 같은 이름으로 새 컨테이너를 만들었습니다. 원래 `container1`을 복원해야 합니다. 무엇을 해야 합니까?

- A. 일시 삭제된 컨테이너를 직접 복원 — 새 컨테이너를 자동으로 덮어씀
- B. 새 `container1`을 삭제한 다음 일시 삭제된 원래 `container1`을 복원
- C. Azure Backup을 사용하여 컨테이너를 이전 상태로 복원
- D. 지원 티켓 제출 — 동일한 이름의 컨테이너 복원은 지원되지 않음

**정답: B**

**해설:** Azure Blob 컨테이너 일시 삭제 복원은 동일한 이름의 기존 활성 컨테이너를 덮어쓸 수 없습니다. 복원 작업은 삭제된 컨테이너 개체의 재생성이며 이름이 이미 사용 중이면 실패합니다. 올바른 절차는 먼저 새 `container1`을 삭제하여 이름을 해제한 다음 일시 삭제된 원래 컨테이너를 복원하는 것입니다. 복원된 컨테이너는 원래 모든 Blob을 보유합니다.

---

### 문제 46

Microsoft Entra 테넌트에 외부 사용자(B2B 게스트) User1을 초대했습니다. User1이 아직 초대를 승인하지 않았습니다. 프로그래밍 방식으로 User1의 초대 상태를 확인해야 합니다. 어떤 Microsoft Graph API 사용자 속성을 쿼리해야 합니까?

- A. `userType`
- B. `externalUserState`
- C. `accountEnabled`
- D. `identities`

**정답: B**

**해설:** Microsoft Graph의 사용자 개체에 있는 `externalUserState` 속성은 B2B 게스트 초대 상태를 나타냅니다. 가능한 값으로는 `PendingAcceptance`(초대가 발송되었지만 승인되지 않음) 및 `Accepted`(초대가 승인됨)가 있습니다. `userType` 속성은 `Guest`와 `Member`를 구별하지만 초대 승인 상태를 반영하지는 않습니다.

---

### 문제 47

East US 지역에 가용성 옵션 없이 배포된 VM1이라는 Azure 가상 머신이 있습니다. 데이터 센터 수준 장애로부터 VM1을 보호하기 위해 가용성 영역으로 이동해야 합니다. 관리 노력을 최소화하는 솔루션이어야 합니다. 무엇을 해야 합니까?

- A. VM1을 삭제하고 동일한 관리 디스크로 가용성 집합에 다시 만들기
- B. Azure Portal의 가용성 및 확장 블레이드를 사용하여 VM1을 가용성 영역으로 이동
- C. Azure Resource Mover를 사용하여 VM1을 가용성 영역을 지원하는 다른 지역으로 이동
- D. Azure Backup을 사용하여 VM1을 가용성 영역 구성으로 복원

**정답: B**

**해설:** Azure Portal은 `가용성 및 확장` 블레이드에서 단일 인스턴스 VM을 동일 지역 내의 지역(비영역) 구성에서 영역(가용성 영역) 구성으로 이동하는 안내 환경을 제공합니다. 이 접근 방식은 수동 VM 삭제 및 재생성에 비해 관리 노력을 최소화합니다. 가용성 집합은 데이터 센터 수준 장애가 아닌 랙 수준 장애로부터 보호하므로 올바른 선택이 아닙니다.

---

### 문제 48

사용자에게 스토리지 계정에서 `Storage Blob Data Contributor` 역할이 할당되어 있습니다. 사용자는 API를 통해 Blob 데이터에 접근할 수 있지만 Azure Portal에서 Blob 컨테이너를 탐색할 수 없다고 보고합니다. 이 문제를 해결하려면 어떻게 해야 합니까?

- A. Contributor 대신 `Storage Blob Data Owner` 역할 할당
- B. 스토리지 계정 범위에서 `Reader` 역할 할당
- C. 스토리지 계정에 Azure RBAC 활성화
- D. 사용자의 공용 IP를 허용하도록 방화벽 구성

**정답: B**

**해설:** `Storage Blob Data Contributor` 역할은 데이터 플레인 접근(Blob 읽기, 쓰기, 삭제)을 부여하지만 Azure Portal에서 스토리지 계정 리소스를 볼 수 있는 관리 플레인 접근은 부여하지 않습니다. 포털에서 컨테이너를 탐색하려면 사용자에게 스토리지 계정 범위에서 `Reader` 역할(또는 유사한 ARM 읽기 역할)도 필요합니다. Reader 역할은 포털이 스토리지 계정 리소스 트리를 표시할 수 있게 해주며, 이는 UI를 통해 Blob 데이터에 접근하기 전에 필요합니다.

---

### 문제 49

조직의 사용자를 위해 SSPR(셀프 서비스 암호 재설정)을 구성하고 있습니다. User1은 전역 관리자 역할을 가지고 있습니다. User1이 SSPR에 사용할 수 없는 인증 방법은 무엇입니까?

- A. Microsoft Authenticator 앱 알림
- B. 휴대폰(SMS)
- C. 이메일 확인
- D. 보안 질문

**정답: D**

**해설:** Microsoft는 전역 관리자(및 다른 권한 있는 관리자 역할)가 SSPR 인증 방법으로 보안 질문을 사용하는 것을 금지합니다. 이 제한은 보안 설계 결정입니다. 권한 있는 계정에는 더 강력한 인증 방법이 필요합니다. 전역 관리자는 Authenticator 앱, 전화 또는 이메일과 같은 최소 두 가지 강력한 방법을 사용해야 합니다. 보안 질문은 더 약한 인증 요소로 간주되어 관리자 계정에서 비활성화됩니다.

---

### 문제 50

NTFS 볼륨에서 기본 4 KiB 클러스터 크기를 사용하는 Azure File Sync 서버 엔드포인트가 있습니다. 클라우드 계층화를 활성화합니다. 클라우드 계층화에 적합한 최소 파일 크기는 얼마입니까?

- A. 4 KiB
- B. 8 KiB
- C. 16 KiB
- D. 64 KiB

**정답: B**

**해설:** Azure File Sync에서 클라우드 계층화에 적합한 최소 파일 크기는 `max(2 × NTFS 클러스터 크기, 8 KiB)`로 계산됩니다. 4 KiB 클러스터 크기의 경우: 2 × 4 KiB = 8 KiB로, 8 KiB 절대 최솟값과 같습니다. NTFS 마스터 파일 테이블(MFT)에 직접 저장된 파일(매우 작은 파일)은 정책 설정에 관계없이 계층화에 적합하지 않습니다.

---
