# AZ-104 연습 문제 — 한국어 Set 6
**커버리지**: 2.txt 채팅 리뷰 기반 — Linux VM Custom Script Extension 로그, Microsoft Graph PowerShell(Get-MgUser/Update-MgUser), Azure Policy 효과 및 수정 작업, VM Insights, NSG 규칙 우선순위, 리소스 잠금, Load Balancer 상태 프로브, ASR Re-protect/Commit, 가용성 집합, App Service 슬롯/Auto Swap/SSL, Azure Storage 수명 주기/SAS/File Sync/인증, Private Endpoint/DNS, 네트워킹(Application Gateway, Azure Firewall, Route Table), Azure Monitor 경고, Bicep 템플릿, Key Vault, 동적 그룹.

총 44문항 | 합격 목표: 700/1000

---

## 1) 컴퓨팅

### 문제 1
Custom Script Extension이 설치된 Azure Linux 가상 머신이 있습니다. 확장이 실패 상태를 표시하고 있으며, 확장 로그를 검토하여 문제를 해결해야 합니다. 가상 머신에서 확장 로그를 어디서 찾아야 합니까?

- A. `/var/log/waagent.log`
- B. `/var/log/azure/custom-script-extension/handler.log`
- C. `/var/lib/waagent/custom-script/work/`
- D. `/var/log/messages`

**정답: B**
**설명:**
Azure Linux VM에서 확장별 로그는 Azure 에이전트의 확장 디렉터리에 저장됩니다:

```
/var/log/azure/<확장이름>/
```

Custom Script Extension의 경우:
```
/var/log/azure/custom-script-extension/handler.log
```

`handler.log`에는 스크립트 실행 출력, 오류 세부 정보, 상태 정보가 포함됩니다. Custom Script Extension이 실패 상태를 표시할 때 가장 먼저 확인해야 할 파일입니다.

**관련 경로:**
| 경로 | 용도 |
|------|------|
| `/var/log/azure/custom-script-extension/handler.log` | 확장 실행 세부 정보 및 오류 ✅ |
| `/var/log/waagent.log` | Azure Linux 에이전트 (플랫폼 수준의 광범위한 문제 해결) |
| `/var/lib/waagent/` | 확장 바이너리, 설정, 다운로드된 스크립트 |

**Windows VM의 해당 경로:**
```
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension\<버전>\
```

**시험 팁**: "Linux VM Custom Script Extension 실패" → `/var/log/azure/custom-script-extension/handler.log`에서 시작.

---

## 2) ID 및 거버넌스

### 문제 2
여러 사용자 계정이 있는 Microsoft Entra 테넌트가 있습니다. Accounting 부서의 모든 사용자에 대해 사용 위치를 프랑스로 변경하기 위해 PowerShell을 사용해야 합니다. 어떤 Microsoft Graph PowerShell cmdlet 조합을 사용해야 합니까?

- A. `Get-AzureADUser` 및 `Set-AzureADUser`
- B. `Get-MgUser` 및 `Update-MgUser`
- C. `Get-MsolUser` 및 `Set-MsolUser`
- D. `Get-AzADUser` 및 `Update-AzADUser`

**정답: B**
**설명:**
**Microsoft Graph PowerShell SDK**는 Microsoft Entra ID(구 Azure AD) 관리에 현재 권장되는 모듈입니다. 필요한 두 cmdlet은 다음과 같습니다:

| Cmdlet | 용도 |
|--------|------|
| **`Get-MgUser`** | 사용자 검색; OData 쿼리를 위한 `-Filter` 지원 |
| **`Update-MgUser`** | `UsageLocation`을 포함한 사용자 속성 업데이트 |

**사용 예시:**
```powershell
# 1단계: Accounting 부서의 모든 사용자 가져오기
$users = Get-MgUser -Filter "department eq 'Accounting'" -All

# 2단계: 각 사용자의 사용 위치 업데이트
foreach ($user in $users) {
    Update-MgUser -UserId $user.Id -UsageLocation "FR"
}
```

**다른 옵션이 정답이 아닌 이유:**
| 모듈 | 상태 |
|------|------|
| `AzureAD` (`Get-AzureADUser`) | 사용 중단 예고됨 |
| `MSOnline` (`Get-MsolUser`) | 사용 중단 예고됨 |
| `Az` 모듈 (`Get-AzADUser`) | Azure 리소스 관리용, Entra 사용자 속성 관리 불가 |

**시험 팁**: Entra ID 사용자를 PowerShell로 관리하는 현행/권장 방법 → **Microsoft Graph PowerShell** (`Get-MgUser` / `Update-MgUser`).

---

### 문제 3
태그가 없을 때 리소스에 자동으로 태그를 추가하는 사용자 지정 Azure Policy 정의를 만들고 있습니다. 리소스 생성 시 태그를 추가하고 기존 비준수 리소스의 수정(remediation)도 허용하도록 정책을 구성해야 합니다. 정책 정의에서 어떤 효과(effect)를 사용해야 합니까?

- A. `append`
- B. `deployIfNotExists`
- C. `modify`
- D. `audit`

**정답: C**
**설명:**
**`modify`** 효과는 태그를 포함한 리소스 속성 추가, 업데이트, 제거를 위해 특별히 설계되었으며, 배포 시점 적용과 기존 리소스 수정을 모두 지원합니다.

**태그 관리 시 효과 비교:**
| 효과 | 생성 시 태그 추가? | 기존 리소스 수정? | 사용 사례 |
|------|-----------------|-----------------|---------|
| `append` | ✅ (추가 전용) | ❌ 제한적 | 배열 값 추가; 기존 태그 수정 불가 |
| **`modify`** | ✅ | ✅ **Remediation Task 통해** | **태그 또는 속성 추가/업데이트/제거** |
| `deployIfNotExists` | 관련 리소스 배포 | ✅ | 누락된 동반 리소스 배포 (예: 진단 설정) |
| `audit` / `deny` | ❌ | ❌ | 준수 여부 평가만 / 배포 차단 |

**`modify`의 주요 특징:**
- 배포 시점에 적용 (생성/업데이트 작업 가로챔)
- 기존 비준수 리소스에 대해 실행 가능한 **Remediation Task** 지원
- 수정 권한 부여를 위해 정책 할당에 **관리 ID** 필요
- 태그, 위치, SKU 및 기타 리소스 속성 지원

**샘플 정책 규칙 (태그 없으면 추가):**
```json
{
  "if": {
    "field": "tags['Environment']",
    "exists": "false"
  },
  "then": {
    "effect": "modify",
    "details": {
      "roleDefinitionIds": [
        "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
      ],
      "operations": [{
        "operation": "add",
        "field": "tags['Environment']",
        "value": "Production"
      }]
    }
  }
}
```

---

### 문제 4
RG1이라는 리소스 그룹이 포함된 Azure 구독이 있습니다. RG1에는 여러 가상 머신과 스토리지 계정이 있습니다. Admin1이라는 사용자가 RG1에 대한 Contributor 역할을 가지고 있습니다. Admin1은 RG1 내의 VM1이라는 가상 머신에 CanNotDelete 잠금을 적용해야 합니다. Admin1이 잠금을 적용할 수 있도록 하려면 어떻게 해야 합니까?

- A. RG1 범위에서 Admin1에게 User Access Administrator 역할 할당
- B. RG1 범위에서 Admin1에게 Owner 역할 할당
- C. VM1 범위에서 Admin1에게 Resource Policy Contributor 역할 할당
- D. 추가 작업 불필요; Contributor는 잠금 관리 가능

**정답: B**
**설명:**
리소스 잠금 관리에는 `Microsoft.Authorization/locks/*` 권한이 필요합니다. 기본 제공 역할 중 이 권한을 포함하는 역할은 **Owner**와 **User Access Administrator** 두 가지뿐입니다.

**역할별 권한 비교:**
| 역할 | 리소스 관리 | RBAC 역할 할당 | 잠금 관리 |
|------|------------|--------------|---------|
| Reader | ❌ | ❌ | ❌ |
| Contributor | ✅ | ❌ | ❌ |
| User Access Administrator | ❌ (리소스) | ✅ | ✅ |
| **Owner** | ✅ | ✅ | ✅ |

**Owner가 정답인 이유 (User Access Administrator가 아닌):**
기술적으로는 두 역할 모두 잠금 권한을 부여하지만, Microsoft 공식 시험 자료에서는 이 시나리오에 대해 **Owner**를 정답으로 명시합니다. Owner는 Contributor가 이미 보유한 리소스 관리 권한과 잠금에 필요한 추가 인증 권한을 모두 포함하기 때문입니다.

**참고**: `Microsoft.Authorization/*` — 기본 제공 역할 중 Owner와 User Access Administrator만 이 작업 권한을 가집니다.

**시험 패턴:**
- "잠금만 관리하는 최소 권한" → User Access Administrator
- "Admin1(이미 Contributor)이 잠금을 적용할 수 있도록 설정" → **Owner**

---

## 3) 모니터링

### 문제 5
VM Insights가 활성화된 VM1이라는 가상 머신이 있는 Azure 구독이 있습니다. VM Insights의 **Map 기능**을 사용하여 VM1에서 실행 중인 프로세스와 해당 종속성을 보려고 합니다. Map 기능을 활성화하기 위해 VM1에 추가로 설치해야 하는 구성 요소는 무엇입니까?

- A. Log Analytics Agent만 설치
- B. Azure Monitor Agent만 설치
- C. Dependency Agent(종속성 에이전트)
- D. Azure Diagnostics Extension

**정답: C**
**설명:**
VM Insights는 서로 다른 역할을 수행하는 두 가지 구성 요소로 구성됩니다:

| 구성 요소 | 역할 | Map 기능에 필요한가? |
|---------|------|------------------|
| **Azure Monitor Agent** (또는 Log Analytics Agent) | 성능 메트릭 및 로그 수집 | 부분적 (Performance 뷰 활성화) |
| **Dependency Agent** | 프로세스 수준 및 네트워크 연결 데이터 수집 | ✅ **Map 기능에 필수** |

**Map 기능이 시각화하는 정보:**
- VM에서 실행 중인 프로세스
- 프로세스 간 TCP 연결
- 인바운드 및 아웃바운드 네트워크 연결
- 외부/내부 서비스 종속성

Map 기능에는 Dependency Agent만 수집할 수 있는 **프로세스 수준 데이터**가 필요합니다. Azure Monitor Agent 단독으로는 CPU/메모리/디스크 메트릭만 제공하며, 프로세스를 열거하거나 네트워크 연결을 매핑할 수 없습니다.

**설치 방법**: Dependency Agent는 VM에서 서비스로 실행되며 Log Analytics 워크스페이스로 데이터를 전송합니다. Azure Monitor Agent(또는 Log Analytics Agent)가 먼저 설치되어 있어야 합니다.

**시험 팁**: "Map 기능", "실행 중인 프로세스", "종속성" → **Dependency Agent**가 필요한 추가 구성 요소.

---

## 4) 네트워킹

### 문제 6
Azure 구독에 VM1이라는 가상 머신이 있습니다. VM1에는 Subnet1에 연결된 NIC1이라는 네트워크 인터페이스가 있습니다. NSG1이라는 NSG가 Subnet1과 연결되어 있습니다. NSG1에는 다음과 같은 사용자 지정 인바운드 규칙이 있습니다:

| 규칙 | 우선순위 | 작업 | 프로토콜 | 포트 | 원본 |
|------|---------|-----|---------|------|------|
| Rule1 | 100 | 허용 | TCP | 443 | 10.0.0.0/8 |
| Rule2 | 200 | 거부 | TCP | 443 | 모두 |
| Rule3 | 300 | 허용 | TCP | 443 | 인터넷 |

인터넷 사용자가 공용 IP 주소를 사용하여 TCP 포트 443에서 VM1에 연결을 시도합니다. 연결 시도의 결과는 무엇입니까?

- A. Rule3에 의해 연결 허용됨
- B. 인터넷이 10.0.0.0/8 범위에 포함되므로 Rule1에 의해 연결 허용됨
- C. Rule2에 의해 연결 거부됨
- D. 기본 Deny All 규칙에 의해 연결 거부됨

**정답: C**
**설명:**
NSG 규칙은 **우선순위 순서**로 평가됩니다 — 숫자가 낮을수록 우선순위가 높습니다. 처리는 **첫 번째로 일치하는 규칙**에서 중단됩니다.

**인터넷 원본 IP (예: 52.168.1.50)가 TCP 443으로 접근 시 평가:**

| 규칙 | 우선순위 | 원본 일치 여부 | 결과 |
|------|---------|-------------|------|
| Rule1 | 100 | ❌ `52.168.1.50`은 `10.0.0.0/8`에 해당 안 됨 | 건너뜀 |
| **Rule2** | **200** | ✅ **`모두`는 모든 원본과 일치** | **🚫 거부 — 여기서 종료** |
| Rule3 | 300 | 도달하지 못함 | — |

**핵심 개념:**
- Rule2의 `모두`(Any)가 Rule3보다 먼저 인터넷 원본과 일치함
- Rule3 (인터넷 허용, 우선순위 300)은 Rule2 (모두 거부, 우선순위 200)가 먼저 일치하기 때문에 평가되지 않음
- **NSG 평가는 첫 번째 일치에서 중단됨** — 낮은 우선순위 규칙은 일치 후 검토되지 않음

**자주 나오는 시험 함정**: Rule3이 인터넷 트래픽을 허용하는 것처럼 보이지만, 우선순위 번호가 낮아 먼저 평가되는 Rule2가 `모두`(Any)를 사용하므로 인터넷 트래픽은 Rule3에 도달하기 전에 차단됩니다.

**수정 방법**: Rule3을 Rule2 위로 이동(예: 우선순위 150 할당)하여 인터넷 트래픽이 모두 거부 규칙에 걸리기 전에 허용되도록 합니다.

---

### 문제 7
LB1이라는 Standard SKU 내부 Load Balancer가 있는 Azure 구독이 있습니다. LB1에는 세 개의 가상 머신이 포함된 백엔드 풀이 있습니다. 사용자들이 간헐적인 연결 문제를 보고합니다. 상태 프로브가 실패하고 있다는 것을 발견했습니다. 상태 프로브가 성공하려면 가상 머신에서 어떤 IP 주소의 인바운드 트래픽을 허용해야 합니까?

- A. `10.0.0.1` (Azure 기본 게이트웨이)
- B. `169.254.169.254` (Azure 인스턴스 메타데이터 서비스)
- C. `168.63.129.16`
- D. `172.16.0.1` (Load Balancer 내부 IP)

**정답: C**
**설명:**
Azure Load Balancer 상태 프로브는 Azure 인프라 주소 **`168.63.129.16`**에서 시작됩니다. 이는 Azure 플랫폼 서비스에서만 사용하는 특수한 비라우팅 가상 IP입니다.

**`168.63.129.16`을 사용하는 서비스:**
| 서비스 | 용도 |
|-------|------|
| **Load Balancer 상태 프로브** | VM 상태 확인을 위한 프로브 |
| VM 에이전트 하트비트 | Azure 패브릭에 VM 상태 보고 |
| Custom Script Extension | 실행 중 상태 보고 |
| DHCP / DNS 확인 | Azure 내부 DNS 응답 |

**상태 프로브가 실패하는 이유:**
NSG 규칙 또는 OS 방화벽(Windows 방화벽 / iptables)이 `168.63.129.16`에서의 인바운드 트래픽을 차단하면 Load Balancer가 VM의 상태 프로브 포트에 연결하지 못하고 VM을 **비정상**으로 표시하여 백엔드 풀에서 제거합니다.

**해결 방법 — 추가해야 할 NSG 규칙:**
```
방향: 인바운드
원본: 168.63.129.16 (또는 서비스 태그 AzureLoadBalancer)
대상: 모두
포트: <상태 프로브 포트>
프로토콜: TCP
작업: 허용
우선순위: (거부 규칙보다 낮은 숫자여야 함)
```

**참고**: NSG 규칙에서 서비스 태그 `AzureLoadBalancer`는 `168.63.129.16`을 포함합니다 — 둘 중 하나를 사용해도 됩니다.

**시험 팁**: "Standard Load Balancer에서 상태 프로브 실패" → NSG에서 `168.63.129.16` / `AzureLoadBalancer`의 인바운드 허용 규칙 확인.

---

### 문제 8
Azure 구독에 가용성 집합 AS1이 있습니다. AS1은 결함 도메인 2개, 업데이트 도메인 5개로 구성되어 있으며 가상 머신 10대를 포함하고 있습니다. Microsoft가 VM 재시작이 필요한 계획된 유지 관리를 수행합니다. 계획된 유지 관리 중 동시에 사용할 수 없는 VM의 최대 수는 몇 대입니까?

- A. 1
- B. 2
- C. 5
- D. 10

**정답: B**
**설명:**
계획된 유지 관리는 **업데이트 도메인(UD)** 단위로 한 번에 하나씩 적용됩니다. UD 5개에 VM 10대가 있으므로 각 UD에 VM이 2대씩 분배됩니다.

| 업데이트 도메인 | VM |
|--------------|-----|
| UD1 | VM1, VM2 |
| UD2 | VM3, VM4 |
| UD3 | VM5, VM6 |
| UD4 | VM7, VM8 |
| UD5 | VM9, VM10 |

Microsoft가 한 번에 UD 하나를 재시작 → 동시에 최대 **2대** 사용 불가.

**핵심 규칙:**
- 결함 도메인은 하드웨어 장애 격리용 — 계획된 유지 관리 일정과 **무관**
- 업데이트 도메인이 계획된 유지 관리 재시작의 **순차 배치**를 결정
- 계산식: UD당 VM = 총 VM ÷ UD 수 = 10 ÷ 5 = **2**

**시험 팁**: "계획된 유지 관리 + 가용성 집합" → **결함 도메인이 아닌 업데이트 도메인**에 집중.

---

### 문제 9
관리형 디스크를 사용하는 가상 머신용 가용성 집합을 배포하는 Azure Resource Manager 템플릿을 만들고 있습니다. 가용성 집합이 관리형 디스크를 지원하도록 해야 합니다. 가용성 집합 리소스 정의에서 어떤 속성을 구성해야 합니까?

- A. `properties.managed`를 `true`로 설정
- B. `sku.name`을 `Aligned`로 설정
- C. `properties.platformFaultDomainCount`를 3으로 설정
- D. `sku.name`을 `Classic`으로 설정

**정답: B**
**설명:**
관리형 디스크를 사용하는 VM이 포함된 가용성 집합에는 반드시 `sku.name`을 **`Aligned`**로 설정해야 합니다. 이를 통해 스토리지 결함 도메인이 컴퓨팅 결함 도메인과 정렬되어 올바른 격리가 보장됩니다.

**ARM 템플릿 예시:**
```json
{
  "type": "Microsoft.Compute/availabilitySets",
  "sku": {
    "name": "Aligned"
  },
  "properties": {
    "platformFaultDomainCount": 2,
    "platformUpdateDomainCount": 5
  }
}
```

| SKU 이름 | 사용 사례 |
|---------|---------|
| **`Aligned`** | 관리형 디스크 (현행 표준) |
| `Classic` | 비관리형 디스크 (레거시) |

**자주 나오는 함정**: `managed: true`는 이 목적에 유효한 속성이 아님 — 올바른 설정은 `sku.name = Aligned`.

**시험 팁**: 가용성 집합 + 관리형 디스크 → `sku.name`을 `Aligned`로 설정.

---

### 문제 10
Standard 티어 App Service 플랜에서 실행 중인 WebApp1이라는 Azure App Service 웹앱이 있습니다. WebApp1에는 스테이징 배포 슬롯이 있습니다. 스테이징 슬롯에는 여러 애플리케이션 설정이 있으며, 스테이징 슬롯을 프로덕션으로 교환할 때 **DatabaseConnection**이라는 애플리케이션 설정이 스테이징 슬롯에 남아 있도록 해야 합니다. DatabaseConnection 설정을 어떻게 구성해야 합니까?

- A. 연결 문자열로 구성
- B. **슬롯 설정(배포 슬롯 설정)**으로 표시
- C. 배포 파이프라인에서 환경 변수로 설정
- D. 추가 구성 필요 없음; 설정은 항상 슬롯에 유지됨

**정답: B**
**설명:**
기본적으로 애플리케이션 설정은 슬롯 교환 시 **코드와 함께 이동**합니다. 설정을 **특정 슬롯에 고정**하려면 **슬롯 설정**(스티키 설정이라고도 함)으로 표시해야 합니다.

| 설정 유형 | 교환 시 동작 |
|---------|------------|
| 일반 앱 설정 | 슬롯 콘텐츠와 함께 대상 슬롯으로 이동 |
| **슬롯 설정 (스티키)** | **구성된 슬롯에 유지됨** |

**DatabaseConnection을 슬롯 설정으로 구성한 후:**
- 스테이징 슬롯은 교환 후에도 자체 `DatabaseConnection` 값을 유지
- 프로덕션 슬롯은 자체 `DatabaseConnection` 값을 유지

**시험 팁**: "교환 후에도 스테이징 슬롯에 남아야 하는 설정" → **슬롯 설정**.

---

### 문제 11
WebApp1이라는 Azure App Service 웹앱이 있습니다. 배포 슬롯을 사용하여 프로덕션에 자동 배포를 구성할 계획입니다. 코드가 스테이징 슬롯에 배포될 때 웜업 후 자동으로 프로덕션으로 교환되도록 스테이징 배포 슬롯을 구성해야 합니다. 스테이징 슬롯에서 어떤 구성을 활성화해야 합니까?

- A. 슬롯 설정
- B. 지속적인 배포
- C. 자동 교환 (Auto swap)
- D. 블루-그린 배포

**정답: C**
**설명:**
**자동 교환(Auto swap)**은 앱이 웜업(시작/상태 확인)을 완료한 후 스테이징 슬롯을 자동으로 프로덕션으로 교환합니다. 수동 개입이 필요하지 않습니다.

**자동 교환 워크플로우:**
1. 코드가 스테이징 슬롯에 배포됨
2. 앱이 스테이징에서 웜업됨(상태 확인 통과)
3. Azure가 자동으로 스테이징 → 프로덕션 교환

| 기능 | 목적 |
|------|------|
| 슬롯 설정 | 특정 슬롯에 설정 고정 |
| **자동 교환** | **웜업 후 자동으로 프로덕션 교환** |
| 지속적인 배포 | 소스 컨트롤에서 배포 트리거 |

**시험 팁**: "웜업 후 자동으로 프로덕션으로 교환" → 스테이징 슬롯에서 **자동 교환** 활성화.

---

### 문제 12
사용자 지정 도메인이 구성된 Azure App Service 웹앱이 있습니다. TLS/SSL 인증서로 사용자 지정 도메인을 보호해야 합니다. 솔루션은 여러 도메인이 동일한 IP 주소를 공유할 수 있어야 합니다. 어떤 TLS/SSL 바인딩 유형을 사용해야 합니까?

- A. IP 기반 SSL
- B. SNI SSL
- C. 공유 SSL
- D. 와일드카드 SSL

**정답: B**
**설명:**
**SNI SSL(서버 이름 표시)**은 TLS 핸드셰이크 중 대상 도메인 이름을 전송하여 여러 도메인이 **동일한 IP 주소**를 공유할 수 있게 합니다.

| 바인딩 유형 | IP 사용 | 동일 IP에 여러 도메인 |
|-----------|---------|-------------------|
| IP 기반 SSL | 도메인당 전용 IP | ❌ |
| **SNI SSL** | **공유 IP** | ✅ |

**SNI가 작동하는 이유:**
TLS 핸드셰이크 시 클라이언트가 대상 호스트명(서버 이름 표시)을 전송 → 서버가 일치하는 인증서를 선택 → 전용 IP 없이도 인증서 제공.

**시험 팁**: "동일한 IP에 여러 도메인" → **SNI SSL**.

---

### 문제 13
VM1이라는 Azure 가상 머신이 Azure Site Recovery를 사용하여 보조 지역으로 복제되고 있습니다. 기본 지역에 재해가 발생합니다. VM1을 보조 지역으로 성공적으로 장애 조치하고 장애 조치를 커밋합니다. 기본 지역이 다시 사용 가능해졌습니다. 보조 지역의 VM1이 기본 지역으로 다시 복제할 수 있도록 보호를 활성화해야 합니다. 먼저 무엇을 해야 합니까?

- A. 새 복제 정책 생성
- B. 재보호(Re-protect) 작업 수행
- C. 테스트 장애 조치 실행
- D. 보조 지역의 VM을 삭제하고 재배포

**정답: B**
**설명:**
장애 조치 + 커밋 후에는 원래 복제 관계가 끊어집니다. 보조 지역에서 기본 지역으로 다시 복제하려면 먼저 **재보호(Re-protect)**를 수행해야 합니다. 재보호는 복제 방향을 반대로 설정합니다.

**장애 조치 후 워크플로우:**
```
장애 조치 → 커밋 → [기본 지역 복구] → 재보호 → (다시 복제) → 장애 복구
```

| 단계 | 작업 |
|------|------|
| 장애 조치 | 보조 지역으로 전환 |
| 커밋 | 확정 — 복구 지점 삭제 |
| **재보호** | **복제 방향 반대로 설정 (보조 → 기본)** |
| 장애 복구 | 기본 지역으로 전환 |

**핵심**: 재보호는 장애 복구 **전에 반드시** 필요 — 재보호 없이는 장애 복구 불가.

**시험 팁**: "장애 조치 + 커밋 후 기본으로 다시 복제" → **재보호(Re-protect)**.

---

### 문제 14
Recovery Services Vault에서 Azure 가상 머신에 대한 새 백업 정책을 만들고 있습니다. Enhanced 정책 하위 유형을 사용할 계획입니다. 6시간마다 백업을 수행하도록 정책을 구성합니다. 이 구성에서 즉시 복구 스냅샷의 기본 보존 기간은 얼마입니까?

- A. 1일
- B. 2일
- C. 7일
- D. 30일

**정답: C**
**설명:**
Azure VM 백업(Enhanced 정책)은 2단계 저장 구조로 백업을 저장합니다:

| 계층 | 위치 | 기본 보존 기간 |
|-----|------|-------------|
| **즉시 복구 스냅샷** | VM 근처 (디스크 스토리지) | **7일** |
| Vault 백업 | Recovery Services Vault | 보존 정책에 따름 |

즉시 복구 스냅샷은 빠른 복원을 가능하게 합니다(Vault 다운로드 없이 로컬 복원). 6시간 빈도의 Enhanced 정책에서 스냅샷 기본 보존 기간은 **7일**입니다.

**시험 팁**: Enhanced 정책 + 즉시 복구 스냅샷 → 기본 보존 기간 = **7일**.

---

## 2) ID 및 거버넌스

### 문제 15
사용자 지정 Azure Policy 정의를 만들고 있습니다. 조직의 요구 사항을 충족하지 않는 리소스 배포를 사용자가 방지하도록 정책을 구성해야 합니다. 정책 규칙에서 어떤 효과를 구성해야 합니까?

- A. `audit`
- B. `append`
- C. `modify`
- D. `deny`

**정답: D**
**설명:**
**`deny`** 효과는 생성/업데이트 시 비준수 리소스 배포를 차단합니다.

| 효과 | 동작 |
|-----|------|
| `audit` | 비준수 기록; 차단 **안 함** |
| `append` | 속성 추가; 차단 안 함 |
| `modify` | 속성 추가/업데이트; 생성 차단 안 함 |
| **`deny`** | **비준수 리소스 배포 차단** |

**시험 팁**: "배포 방지" / "비준수 리소스 차단" → **`deny`**.

---

### 문제 16
RG1이라는 리소스 그룹이 있으며 `Production` 값이 있는 `Environment` 태그가 있습니다. RG1에 배포된 모든 리소스가 상위 리소스 그룹의 `Environment` 태그와 값을 자동으로 상속하도록 해야 합니다. 솔루션은 리소스의 기존 `Environment` 태그 값을 재정의해서는 안 됩니다. Azure Policy 정의에서 어떤 `modify` 작업을 사용해야 합니까?

- A. `addOrReplace`
- B. `remove`
- C. `add`
- D. `copy`

**정답: C**
**설명:**
`modify` 효과는 태그 관리를 위한 여러 작업을 지원합니다:

| 작업 | 동작 |
|-----|------|
| **`add`** | **태그가 없을 때만 추가** |
| `addOrReplace` | 추가 또는 덮어쓰기 — 기존 값 무시 |
| `remove` | 태그 삭제 |
| `copy` | 한 필드에서 다른 필드로 값 복사 |

요구 사항이 **"기존 값을 재정의하면 안 됨"**이므로, 없을 때만 추가하고 있을 때는 유지하는 `add`만 두 조건을 모두 충족합니다.

**시험 팁**: "태그 상속하되 기존 값 덮어쓰지 않음" → `modify` 효과에서 **`add`** 작업 사용.

---

### 문제 17
deny 효과를 사용하는 정책 할당이 있는 Azure 구독이 있습니다. 리소스 생성 또는 업데이트 요청을 차단하지 않고 기존 리소스에 대해 정책을 테스트해야 합니다. 솔루션은 계속해서 준수 여부를 평가해야 합니다. 정책 할당에서 무엇을 구성해야 합니까?

- A. 효과를 `deny`에서 `audit`으로 변경
- B. 적용 모드를 **사용 안 함(Disabled)**으로 설정
- C. 정책 할당을 삭제하고 `audit`으로 재생성
- D. 모든 리소스 그룹에 예외 추가

**정답: B**
**설명:**
정책 할당에서 **적용 모드를 사용 안 함(Disabled)**으로 설정하면 준수 평가는 유지하되 차단 동작이 제거됩니다. 리소스는 준수/비준수로 평가되지만 배포는 차단되지 않습니다.

| 적용 모드 | 준수 평가 여부 | 배포 차단 여부 |
|----------|-------------|-------------|
| 사용(기본값) | ✅ | ✅ |
| **사용 안 함** | **✅** | **❌** |

이는 기존 운영에 영향 없이 `deny` 정책을 **테스트**하는 데 이상적입니다.

**시험 팁**: "차단 없이 정책 테스트" → 적용 모드를 **사용 안 함**으로 설정.

---

### 문제 18
여러 스토리지 계정을 배포하는 Bicep 파일을 만들고 있습니다. `createStorage`라는 매개변수가 `true`로 설정된 경우에만 스토리지 계정이 배포되어야 합니다. 루프와 조건부 배포를 결합해야 합니다. 어떤 구문을 사용해야 합니까?

- A. `if(createStorage) [for i in range(0, storageCount): { ... }]`
- B. `[for i in range(0, storageCount): if(createStorage) { ... }]`
- C. `for i in range(0, storageCount): { condition: createStorage, ... }`
- D. `[for i in range(0, storageCount): { condition: createStorage }]`

**정답: B**
**설명:**
Bicep에서 루프와 조건을 결합할 때는 **`for` 표현식이 먼저** 오고 그 다음에 **`if` 표현식**이 와야 합니다:

```bicep
resource storageAccounts 'Microsoft.Storage/storageAccounts@2023-05-01' = [
  for i in range(0, storageCount): if(createStorage) {
    name: '${i}storage${uniqueString(resourceGroup().id)}'
    location: location
    sku: { name: 'Standard_LRS' }
    kind: 'StorageV2'
  }
]
```

**규칙**: `[for ... : if(...) { }]` — `if(...) [for ... : { }]`는 잘못된 구문.

| 구문 순서 | 유효 여부 |
|---------|---------|
| `for` → `if` → 본문 | ✅ |
| `if` → `for` → 본문 | ❌ |

**시험 팁**: Bicep 루프 + 조건 → **`for`는 항상 `if` 앞에**.

---

### 문제 19
`storageModule.bicep`이라는 모듈을 호출하여 스토리지 계정을 배포하는 기본 Bicep 파일이 있습니다. 모듈에는 기본 Blob 엔드포인트를 반환하는 `storageEndpoint`라는 출력이 있습니다. 기본 Bicep 파일에서 이 출력 값을 참조해야 합니다. 어떤 구문을 사용해야 합니까?

- A. `storageModule.output.storageEndpoint`
- B. `storageModule.outputs.storageEndpoint`
- C. `outputs.storageModule.storageEndpoint`
- D. `storageModule['storageEndpoint']`

**정답: B**
**설명:**
Bicep 모듈 출력은 `<모듈별칭>.outputs.<출력이름>` 패턴으로 접근합니다:

```bicep
module storageModule './storageModule.bicep' = {
  name: 'storageDeploy'
  params: { ... }
}

var endpoint = storageModule.outputs.storageEndpoint
```

**핵심 규칙**: 키워드는 `output`(단수)이 아닌 **`outputs`(복수)**.

**시험 팁**: Bicep 모듈 출력 참조 → `모듈이름.outputs.출력이름`.

---

### 문제 20
MG1이라는 Azure 관리 그룹에 Sub1과 Sub2라는 두 개의 구독이 있습니다. User1이라는 사용자는 두 구독의 모든 리소스를 읽을 수 있어야 합니다. 최소한의 역할 할당으로 User1에게 액세스 권한을 부여해야 합니다. 어떻게 해야 합니까?

- A. Sub1 범위와 Sub2 범위에서 User1에게 각각 Reader 역할 할당
- B. MG1 관리 그룹 범위에서 User1에게 Reader 역할 할당
- C. MG1 범위에서 User1에게 Contributor 역할 할당
- D. 테넌트 루트 그룹 범위에서 Reader 역할 할당

**정답: B**
**설명:**
RBAC 역할 할당은 범위 계층 구조를 통해 **아래로 상속**됩니다:

```
관리 그룹 (MG1)
   └── Sub1 (MG1 할당 상속)
   └── Sub2 (MG1 할당 상속)
```

**MG1**에서 Reader를 할당하면 **단 1개의 역할 할당**으로 Sub1과 Sub2의 모든 리소스에 대한 읽기 액세스가 제공됩니다.

| 방법 | 필요한 역할 할당 수 |
|-----|----------------|
| Sub1 + Sub2 개별 할당 | 2 |
| **MG1 (관리 그룹)** | **1** |

**시험 팁**: "여러 구독에 걸쳐 최소 역할 할당" → **관리 그룹 범위**에서 할당.

---

### 문제 21
Microsoft Entra ID Free 라이선스가 있는 Microsoft Entra 테넌트가 있습니다. 사용자의 부서 특성을 기반으로 자동으로 사용자를 추가하는 동적 멤버십 그룹을 만들 계획입니다. 동적 그룹을 만들기 전에 먼저 무엇을 해야 합니까?

- A. 보안 기본값 활성화
- B. Microsoft Entra ID Premium P1 또는 P2로 업그레이드
- C. 모든 장치에 대해 Microsoft Entra ID 조인 활성화
- D. 셀프 서비스 비밀번호 재설정 구성

**정답: B**
**설명:**
**동적 멤버십 그룹**(사용자/장치 특성을 기반으로 멤버십이 자동으로 관리되는 그룹)은 **Microsoft Entra ID Premium P1 또는 P2**가 필요합니다. Free 티어에서는 이 기능을 사용할 수 없습니다.

| 라이선스 티어 | 동적 그룹 |
|------------|---------|
| Free | ❌ |
| Microsoft 365 Apps | ❌ |
| **Premium P1** | **✅** |
| **Premium P2** | **✅** |

**동적 그룹 작동 방식:**
- 규칙 정의 (예: `department -eq "IT"`)
- Azure가 규칙과 일치하는 특성을 가진 사용자를 자동으로 추가/제거
- 수동 그룹 관리 불필요

**시험 팁**: "동적 멤버십 그룹" → 먼저 **Premium P1 또는 P2** 필요.

---

### 문제 22
RG1이라는 리소스 그룹이 포함된 Azure 구독이 있습니다. RG1에는 가상 머신 5대가 있습니다. RG1 범위에 CanNotDelete 잠금을 적용합니다. 구독 범위에서 Owner 역할을 가진 사용자가 가상 머신 중 하나를 삭제하려고 시도합니다. 예상되는 결과는 무엇입니까?

- A. Owner는 전체 권한을 가지고 있으므로 삭제 성공
- B. 잠금이 RBAC 권한을 재정의하므로 삭제 실패
- C. 잠금이 VM 수준이 아닌 RG1 수준에 있으므로 삭제 성공
- D. Owner 역할에 삭제 권한이 포함되지 않으므로 삭제 실패

**정답: B**
**설명:**
관리 잠금은 **RBAC를 재정의**합니다 — Owner 역할조차도 `CanNotDelete` 잠금으로 보호된 리소스를 삭제할 수 없습니다.

**핵심 규칙:**
- 상위 범위(리소스 그룹)에 적용된 잠금은 **모든 하위 리소스에 상속**됨
- `CanNotDelete` 잠금은 사용자의 역할에 관계없이 삭제를 방지
- Owner, Contributor 및 모든 다른 역할은 잠금 적용을 받음

| 역할 | 잠금 없이 삭제 가능? | CanNotDelete 잠금 있을 때 삭제 가능? |
|-----|-----------------|--------------------------|
| Owner | ✅ | ❌ |
| Contributor | ✅ | ❌ |
| 삭제 권한이 있는 사용자 지정 역할 | ✅ | ❌ |

**시험 팁**: "CanNotDelete 잠금 + Owner가 삭제 시도" → **삭제 실패**. 잠금은 항상 RBAC를 이깁니다.

---

## 3) 스토리지

### 문제 23
블록 Blob과 추가 Blob이 포함된 Azure Storage 계정이 있습니다. Blob을 더 차가운 계층으로 자동 전환하는 수명 주기 관리 정책을 만듭니다. 수명 주기 관리 정책의 계층화 작업에 의해 전환되는 Blob 유형은 무엇입니까?

- A. 블록 Blob과 추가 Blob 모두
- B. 추가 Blob만
- C. 블록 Blob만
- D. 페이지 Blob만

**정답: C**
**설명:**
Azure Storage 수명 주기 관리 계층화(핫 → 쿨 → 보관)는 **블록 Blob에만** 적용됩니다.

| Blob 유형 | 수명 주기 계층화 지원 여부 |
|---------|---------------------|
| **블록 Blob** | ✅ |
| 추가 Blob | ❌ |
| 페이지 Blob | ❌ |

**추가 Blob이 제외되는 이유:**
추가 Blob은 순차 추가 작업을 위해 설계되었습니다(예: 로깅). 계층 변경을 지원하지 않습니다.

**시험 팁**: 수명 주기 관리 계층화 → **블록 Blob만** 해당.

---

### 문제 24
블록 Blob이 포함된 Azure Storage 계정에 대한 수명 주기 관리 정책을 구성하고 있습니다. 동일한 Blob 유형에 대해 30일 후 `tierToCool` 작업과 30일 후 `delete` 작업을 모두 포함하는 규칙을 정의합니다. 두 조건을 모두 충족하는 Blob에 수명 주기 관리가 적용하는 작업은 무엇입니까?

- A. `tierToCool` — 계층화가 항상 우선
- B. `delete` — 가장 저렴한 작업이 적용됨
- C. 두 작업이 동시에 적용됨
- D. 두 작업 모두 적용 안 됨 — 충돌하는 규칙은 무시됨

**정답: B**
**설명:**
동일한 Blob에 여러 수명 주기 작업이 동시에 일치하면 Azure는 **가장 저렴한 작업**을 적용합니다:

```
delete  <  tierToArchive  <  tierToCool
(가장 저렴)                  (가장 비쌈)
```

`delete`는 `tierToCool`보다 저렴하므로(삭제된 데이터는 저장 비용이 없음) `delete`가 이깁니다.

**시험 팁**: 동일한 Blob에 여러 수명 주기 작업 → **가장 저렴한 작업이 이깁니다** (보통 `delete`).

---

### 문제 25
storage1이라는 스토리지 계정에 share1이라는 Azure 파일 공유가 있습니다. share1은 스냅샷 기반 백업 정책을 사용하는 Azure Backup으로 보호되고 있습니다. 주문형 백업을 만들려고 하지만 최대 스냅샷 한도에 도달했다는 오류가 발생합니다. 파일 공유당 허용되는 최대 스냅샷 수는 얼마입니까?

- A. 50
- B. 100
- C. 200
- D. 500

**정답: C**
**설명:**
Azure 파일 공유는 파일 공유당 최대 **200개의 스냅샷**을 지원합니다. 이 한도에 도달하면 이전 스냅샷이 삭제될 때까지 새 스냅샷(주문형 백업 포함)을 만들 수 없습니다.

**한도 도달 시 해결 방법:**
1. 이전/만료된 스냅샷 수동 삭제
2. 스냅샷 누적을 줄이도록 백업 보존 정책 조정

**시험 팁**: Azure 파일 공유 스냅샷 한도 = **200개**.

---

### 문제 26
storage1이라는 Azure 스토리지 계정에 container1이라는 Blob 컨테이너가 있습니다. policy1이라는 저장된 액세스 정책과 연결된 서비스 SAS가 있습니다. 스토리지 계정 키를 재생성하지 않고 SAS에 대한 액세스를 즉시 취소해야 합니다. 어떻게 해야 합니까?

- A. Azure Key Vault에서 SAS 토큰 삭제
- B. 스토리지 계정 액세스 키 재생성
- C. policy1이라는 저장된 액세스 정책 삭제
- D. 사용자 위임 키 순환

**정답: C**
**설명:**
저장된 액세스 정책과 연결된 서비스 SAS는 **저장된 액세스 정책을 삭제**하면 즉시 취소할 수 있습니다. SAS에는 정책에 대한 참조가 포함되어 있으며, 정책이 삭제되면 SAS를 더 이상 유효성 검사할 수 없습니다.

| 취소 방법 | 효과 | 다른 SAS에 영향? |
|---------|------|---------------|
| SAS 토큰 삭제 | 서버 측에 저장되지 않으므로 작동 안 함 | 없음 |
| **저장된 액세스 정책 삭제** | **해당 정책을 참조하는 모든 SAS 토큰 즉시 무효화** | 해당 정책 참조 SAS만 |
| 스토리지 계정 키 재생성 | 모든 SAS 토큰(계정 키 기반) 무효화 | 모든 SAS 토큰 |

**시험 팁**: "키 재생성 없이 SAS 취소" → **저장된 액세스 정책 삭제**.

---

### 문제 27
스토리지 계정의 container1이라는 Blob 컨테이너가 있습니다. 컨테이너에 저장된 액세스 정책을 만들어야 합니다. container1에 설정할 수 있는 최대 저장된 액세스 정책 수는 얼마입니까?

- A. 3
- B. 5
- C. 10
- D. 25

**정답: B**
**설명:**
Azure Blob 컨테이너는 컨테이너당 최대 **5개의 저장된 액세스 정책**을 지원합니다.

**시험 팁**: 컨테이너당 저장된 액세스 정책 한도 = **5개**.

---

### 문제 28
Azure 파일 공유가 있는 Azure 스토리지 계정이 있습니다. 스토리지 계정은 현재 Microsoft Entra Domain Services 인증으로 구성되어 있습니다. 온프레미스 AD DS를 사용하도록 인증을 변경해야 합니다. 먼저 무엇을 해야 합니까?

- A. Entra Domain Services 인증을 비활성화하기 전에 AD DS 인증 활성화
- B. AD DS 인증이 활성화된 새 스토리지 계정 만들기
- C. 스토리지 계정에서 Microsoft Entra Domain Services 인증 비활성화
- D. 스토리지 계정을 온프레미스 도메인에 조인

**정답: C**
**설명:**
Azure Files는 ID 기반 인증을 위해 한 번에 **하나의 ID 원본**만 지원합니다. 첫 번째 ID 원본이 활성화된 상태에서는 두 번째 ID 원본을 활성화할 수 없습니다.

**필요한 순서:**
1. **Microsoft Entra Domain Services 인증 비활성화** ← 먼저 수행
2. 온프레미스 AD DS 인증 활성화

| 단계 | 작업 |
|-----|------|
| **1단계** | **Entra Domain Services 인증 비활성화** |
| 2단계 | 온프레미스 AD DS 환경 준비 |
| 3단계 | 스토리지 계정에서 AD DS 인증 활성화 |

**시험 팁**: Azure Files ID 기반 인증 → 한 번에 하나의 ID 원본만 지원; 항상 **기존 원본 먼저 비활성화**.

---

### 문제 29
Azure File Sync 배포가 있습니다. 사용자들이 Azure Portal을 통해 Azure 파일 공유에 직접 파일을 업로드합니다. Azure 파일 공유에 직접 적용된 변경 사항이 온프레미스 서버 엔드포인트에 표시되는 데 24시간 이상 걸린다는 것을 발견했습니다. 동기화 지연을 줄이려면 어떻게 해야 합니까?

- A. Azure File Sync 에이전트 버전 업그레이드
- B. Storage Sync Service 재시작
- C. `Invoke-AzStorageSyncChangeDetection` cmdlet 실행
- D. 클라우드 계층화 여유 공간 비율 감소

**정답: C**
**설명:**
Azure 파일 공유에서 파일이 직접 변경되면(Portal, REST API 또는 다른 클라이언트의 SMB를 통해), **클라우드 변경 감지 작업**이 해당 변경 사항을 발견해야 서버 엔드포인트와 동기화될 수 있습니다. 이 작업은 **24시간마다 한 번씩만 자동 실행**됩니다.

24시간을 기다리지 않고 즉시 변경 감지를 트리거하려면 다음을 실행합니다:

```powershell
Invoke-AzStorageSyncChangeDetection `
  -ResourceGroupName "RG1" `
  -StorageSyncServiceName "SyncService1" `
  -SyncGroupName "SyncGroup1" `
  -CloudEndpointName "CloudEndpoint1" `
  -Path "/"
```

**시험 팁**: "Azure 파일 공유에 직접 적용된 변경 사항이 동기화되는 데 너무 오래 걸림" → **`Invoke-AzStorageSyncChangeDetection`** 실행.

---

### 문제 30
Azure File Sync 배포에 Server1과 Server2라는 두 개의 Windows 파일 서버가 있습니다. 두 서버 모두 Storage Sync Service에 등록되어 있습니다. SyncGroup1이라는 동기화 그룹에는 Azure 파일 공유를 가리키는 클라우드 엔드포인트가 있습니다. Server1은 서버 엔드포인트 경로 `D:\Data`로 SyncGroup1에 추가됩니다. 동일한 데이터를 동기화하기 위해 Server2를 SyncGroup1에 추가해야 합니다. Server2에 유효한 서버 엔드포인트 구성은 무엇입니까?

- A. 서버 엔드포인트는 Server1과 동일한 경로 `D:\Data`여야 함
- B. Server2의 유효한 로컬 NTFS 경로(예: `E:\SharedData`)
- C. 서버 엔드포인트는 UNC 경로여야 함
- D. 서버 엔드포인트는 Azure 파일 공유 경로여야 함

**정답: B**
**설명:**
동기화 그룹의 각 서버 엔드포인트는 등록된 서버의 **로컬 NTFS 볼륨 경로**여야 합니다. 경로는 Server1의 경로와 **일치할 필요가 없습니다** — 유효한 로컬 폴더이면 됩니다.

**유효한 서버 엔드포인트 요구 사항:**
- NTFS 볼륨(ReFS 불가)이어야 함
- 로컬 경로(UNC 불가, Azure 파일 공유 경로 불가)
- 서버에 Azure File Sync 에이전트 설치 필요
- 서버가 Storage Sync Service에 등록되어 있어야 함

| 경로 예시 | 유효 여부 |
|---------|---------|
| `D:\Data` | ✅ |
| `E:\SharedData` | ✅ |
| `\\server\share` (UNC) | ❌ |
| Azure 파일 공유 URL | ❌ |
| `C:\Windows` (시스템 폴더) | ❌ |

**시험 팁**: Azure File Sync 서버 엔드포인트 = 유효한 **로컬 NTFS 경로**; 동기화 그룹의 다른 서버와 경로가 같을 필요 없음.

---

### 문제 31
storage1이라는 Azure 스토리지 계정이 있는 Azure 구독이 있습니다. storage1의 액세스 키를 주기적으로 수동으로 순환할 계획입니다. 키를 순환할 시기가 되면 알림을 받아야 합니다. storage1에 무엇을 구성해야 합니까?

- A. Key Vault 순환 정책
- B. Azure Monitor 경고
- C. 키 만료 정책
- D. 관리 ID 순환 일정

**정답: C**
**설명:**
**키 만료 정책**을 사용하면 스토리지 계정 액세스 키 순환에 대한 알림을 구성할 수 있습니다. 지정된 간격이 경과하고 키가 아직 순환되지 않은 경우 Azure에서 알림을 표시합니다.

**Azure Portal에서의 구성 경로:**
`스토리지 계정 → 액세스 키 → 순환 알림 설정`

**주요 사실:**
- 이것은 **알림 전용** — 키를 자동으로 순환하지 않음
- 액세스 키는 자동으로 만료되지 않음; 수동 순환 필요
- 간격이 경과하면 포털에 알림 표시

| 기능 | 목적 |
|-----|------|
| 키 재생성 | 실제로 키 순환 |
| **키 만료 정책** | **순환 필요 시 알림 전송** |
| Key Vault 순환 | Key Vault를 통한 순환 자동화 (다른 서비스) |

**시험 팁**: "키를 순환할 시기가 되면 알림" → **키 만료 정책**.

---

### 문제 32
Blob 컨테이너가 포함된 storage1이라는 Azure 스토리지 계정이 있습니다. User1이라는 사용자는 스토리지 계정 범위에서 **Storage Account Contributor** 역할이 할당되어 있습니다. User1은 Azure Portal을 통해 Blob 데이터에 액세스합니다. User1이 Blob 데이터에 액세스하는 데 사용하는 인증 방법은 무엇입니까?

- A. Microsoft Entra ID (Azure RBAC)
- B. 계정 액세스 키 (Shared Key 권한 부여)
- C. SAS 토큰
- D. 익명 공개 액세스

**정답: B**
**설명:**
이것은 자주 나오는 시험 함정입니다. Azure Portal은 우선순위 규칙에 따라 인증 방법을 결정합니다:

**Blob 액세스를 위한 Portal 인증 우선순위:**
1. 사용자에게 `Microsoft.Storage/storageAccounts/listKeys/action` 권한이 있는 경우 → **Shared Key (계정 액세스 키)**
2. 사용자에게 listKeys 권한이 없는 경우 → **Microsoft Entra ID (Azure AD)**

**Storage Account Contributor** 역할은 `listKeys/action`을 **포함**하므로 Portal은 **Shared Key 권한 부여**를 기본값으로 사용합니다.

| 역할 | listKeys 포함 여부 | Portal 인증 방법 |
|-----|-----------------|---------------|
| Storage Blob Data Reader | ❌ | Entra ID (Azure AD) |
| **Storage Account Contributor** | **✅** | **Shared Key (액세스 키)** |
| Owner | ✅ | Shared Key (액세스 키) |

**시험 팁**: Portal을 통해 Blob 데이터에 액세스하는 Storage Account Contributor → **Shared Key** (RBAC가 관련되어 있어도 Azure AD 아님).

---

### 문제 33
VNet1이라는 Azure 가상 네트워크와 storage1이라는 스토리지 계정이 있는 Azure 구독이 있습니다. VNet1에서 storage1의 Blob 하위 리소스에 대한 `pe-blob`이라는 프라이빗 엔드포인트를 만듭니다. 프라이빗 엔드포인트 생성 중 프라이빗 DNS 영역과 통합 옵션을 선택합니다. 어떤 프라이빗 DNS 영역이 생성됩니까?

- A. `privatelink.storage.core.windows.net`
- B. `privatelink.blob.core.windows.net`
- C. `privatelink.azure.blob.net`
- D. `blob.privatelink.core.windows.net`

**정답: B**
**설명:**
프라이빗 DNS 영역 통합을 사용하여 Azure Storage에 대한 프라이빗 엔드포인트를 만들 때 DNS 영역 이름은 **하위 리소스 유형**에 따라 다릅니다:

| 스토리지 하위 리소스 | 프라이빗 DNS 영역 |
|-----------------|---------------|
| **blob** | **`privatelink.blob.core.windows.net`** |
| file | `privatelink.file.core.windows.net` |
| queue | `privatelink.queue.core.windows.net` |
| table | `privatelink.table.core.windows.net` |

**프라이빗 DNS 영역의 목적:**
`storage1.blob.core.windows.net`을 공용 IP 대신 프라이빗 엔드포인트의 개인 IP로 확인.

**시험 팁**: Blob 프라이빗 엔드포인트 + DNS 통합 → **`privatelink.blob.core.windows.net`**.

---

## 4) 네트워킹

### 문제 34
storage1이라는 스토리지 계정이 있는 Azure 구독이 있습니다. 다른 구독의 네트워크 관리자가 storage1을 대상으로 하는 프라이빗 엔드포인트를 만듭니다. 프라이빗 엔드포인트 연결이 **보류 중** 상태로 storage1에 표시됩니다. 프라이빗 엔드포인트가 storage1에 연결되도록 허용해야 합니다. 어떻게 해야 합니까?

- A. storage1 쪽에서 새 프라이빗 엔드포인트 만들기
- B. storage1에서 공용 네트워크 액세스 활성화
- C. storage1에서 프라이빗 엔드포인트 연결 승인
- D. VNet에 서비스 엔드포인트 구성

**정답: C**
**설명:**
**다른 구독**에서 프라이빗 엔드포인트를 만들면 대상 리소스 소유자가 수동으로 연결을 **승인**해야 합니다. `보류 중` 상태는 승인 대기 중임을 나타냅니다.

**승인 경로:**
`storage1 → 네트워킹 → 프라이빗 엔드포인트 연결 → [연결 선택] → 승인`

| 연결 상태 | 의미 |
|---------|------|
| 보류 중 | 리소스 소유자의 승인 대기 |
| **승인됨** | 연결 활성화 |
| 거부됨 | 연결 거부 |
| 연결 끊김 | 이전에 승인되었지만 현재 연결 끊김 |

**참고**: 동일 구독 내에서 적절한 권한을 가진 사용자가 프라이빗 엔드포인트를 만들면 자동 승인될 수 있습니다.

**시험 팁**: 다른 구독의 프라이빗 엔드포인트 → `보류 중` 상태 → 리소스 소유자가 **승인** 필요.

---

### 문제 35
`private.contoso.com`이라는 Azure 프라이빗 DNS 영역이 있습니다. VNet1이라는 가상 네트워크에 배포된 가상 머신의 DNS 레코드를 자동으로 등록하도록 구성해야 합니다. 또한 VNet2의 가상 머신은 자동 등록 없이 프라이빗 DNS 영역의 레코드를 확인할 수 있어야 합니다. 어떻게 구성해야 합니까?

- A. 자동 등록이 활성화된 VNet1에만 단일 VNet 링크 만들기; VNet2는 링크 불필요
- B. 자동 등록이 활성화된 VNet1에 VNet 링크를 만들고, 자동 등록이 비활성화된 VNet2에 VNet 링크 만들기
- C. 두 VNet 모두에 자동 등록이 활성화된 VNet 링크 만들기
- D. VNet2에만 VNet 링크 만들기; VNet1은 레코드를 자동으로 등록

**정답: B**
**설명:**
Azure 프라이빗 DNS VNet 링크는 두 가지 모드를 지원합니다:

| 링크 모드 | DNS 등록 | DNS 확인 |
|---------|---------|---------|
| **자동 등록 활성화** | ✅ VM A 레코드 자동 생성 | ✅ |
| **자동 등록 비활성화** | ❌ 자동 레코드 없음 | ✅ 확인 전용 |

**구성:**
- VNet1 → **자동 등록 = 활성화**된 링크
- VNet2 → **자동 등록 = 비활성화**된 링크

**중요**: VNet2도 프라이빗 DNS 영역의 레코드를 확인하려면 **VNet 링크가 있어야** 합니다.

**시험 팁**: "DNS 자동 등록" → 활성화; "확인 전용" → 비활성화. 두 VNet 모두 확인을 위한 링크 필요.

---

### 문제 36
VNet1이라는 가상 네트워크가 있는 Azure 구독이 있습니다. VNet1에는 `Microsoft.Storage`에 대해 서비스 엔드포인트가 활성화된 서브넷이 있습니다. 서브넷의 여러 가상 머신이 다른 구독의 다양한 Azure 스토리지 계정에 액세스합니다. 가상 머신이 **특정 단일** 스토리지 계정에만 액세스할 수 있도록 해야 합니다. 무엇을 구성해야 합니까?

- A. 네트워크 보안 그룹 규칙
- B. 프라이빗 엔드포인트
- C. 서비스 엔드포인트 정책
- D. 스토리지 계정의 방화벽 규칙

**정답: C**
**설명:**
**서비스 엔드포인트 정책**은 서비스 엔드포인트를 통해 특정 Azure 리소스로의 VNet 트래픽을 필터링합니다. 정책 없이 서브넷에서 `Microsoft.Storage` 서비스 엔드포인트를 활성화하면 전 세계 **모든** Azure 스토리지 계정에 액세스할 수 있습니다. 서비스 엔드포인트 정책은 나열된 계정만으로 액세스를 제한합니다.

| 메커니즘 | 특정 스토리지 계정으로 제한 가능? |
|---------|--------------------------|
| 서비스 엔드포인트(단독) | ❌ 모든 Azure 스토리지 허용 |
| NSG 규칙 | ❌ IP 기반, 리소스 기반 아님 |
| **서비스 엔드포인트 정책** | **✅ 지정된 계정으로 제한** |
| 프라이빗 엔드포인트 | ✅ 하지만 더 복잡한 메커니즘 |

**시험 팁**: "서비스 엔드포인트이지만 단일 스토리지 계정으로 제한" → **서비스 엔드포인트 정책**.

---

### 문제 37
AppGW1이라는 Azure Application Gateway가 있습니다. 백엔드 상태 확인에서 백엔드 풀의 모든 서버가 비정상인 것으로 표시됩니다. 서버는 직접 액세스할 때 정상적으로 실행되고 응답합니다. 이 문제를 해결하기 위해 사용자 지정 상태 프로브를 구성해야 합니다. 사용자 지정 상태 프로브를 어디에 연결해야 합니까?

- A. 수신기
- B. 라우팅 규칙
- C. 백엔드 풀
- D. 백엔드 HTTP 설정

**정답: D**
**설명:**
Azure Application Gateway에서 **상태 프로브는 백엔드 HTTP 설정**에 연결됩니다.

**Application Gateway 구성 요소 흐름:**
```
수신기 → 라우팅 규칙 → 백엔드 HTTP 설정 → 백엔드 풀
                              ↑
                        상태 프로브는 여기에
```

| 구성 요소 | 역할 |
|---------|------|
| 수신기 | 들어오는 요청 수신 |
| 라우팅 규칙 | 수신기를 백엔드에 매핑 |
| **백엔드 HTTP 설정** | **통신 + 상태 프로브 정의** |
| 백엔드 풀 | 대상 VM/IP 포함 |

**시험 팁**: "Application Gateway 사용자 지정 상태 프로브" → **백엔드 HTTP 설정**에 연결.

---

### 문제 38
`www.contoso.com`, `www.fabrikam.com`, `www.adatum.com` 세 개의 웹사이트를 호스팅하는 Azure Application Gateway를 구성하고 있습니다. 세 웹사이트 모두 HTTPS로 포트 443을 사용합니다. 각 웹사이트는 자체 백엔드 풀로 라우팅되어야 합니다. 각 웹사이트에 대해 어떤 유형의 수신기를 만들어야 합니까?

- A. 기본 수신기
- B. 다중 사이트 수신기
- C. 경로 기반 수신기
- D. 리디렉션 수신기

**정답: B**
**설명:**
**다중 사이트 수신기**는 **HTTP Host 헤더(도메인 이름)**를 기반으로 트래픽을 라우팅하여 여러 웹사이트가 동일한 IP 주소와 포트를 공유할 수 있게 합니다.

| 수신기 유형 | 라우팅 기준 | 동일 IP에 여러 도메인? |
|-----------|-----------|-------------------|
| 기본 | IP + 포트만 | ❌ 하나의 사이트만 |
| **다중 사이트** | **Host 헤더 (도메인 이름)** | **✅ 여러 사이트** |
| 경로 기반 | URL 경로 (`/api`, `/images`) | 다른 개념 (URL 라우팅) |

**다중 사이트 수신기를 사용한 트래픽 흐름:**
```
HTTPS :443
  ├─ Host: www.contoso.com  → 백엔드 풀 1
  ├─ Host: www.fabrikam.com → 백엔드 풀 2
  └─ Host: www.adatum.com   → 백엔드 풀 3
```

**시험 팁**: 동일한 Application Gateway에 여러 호스트명 → 각 호스트명에 대한 **다중 사이트 수신기**.

---

### 문제 39
Windows Update 엔드포인트에 대한 아웃바운드 액세스를 허용하도록 Azure Firewall 정책을 구성하고 있습니다. 최소한의 유지 관리가 필요한 가장 간단한 방법을 사용하려고 합니다. 어떤 규칙 유형과 구성을 사용해야 합니까?

- A. 모든 IP에 TCP 포트 80 및 443을 허용하는 네트워크 규칙
- B. 개별 Windows Update FQDN이 나열된 애플리케이션 규칙
- C. `WindowsUpdate` FQDN 태그를 사용한 애플리케이션 규칙
- D. Windows Update 트래픽에 대한 DNAT 규칙

**정답: C**
**설명:**
Azure Firewall의 **FQDN 태그**는 잘 알려진 Microsoft 서비스의 FQDN 그룹을 나타냅니다. `WindowsUpdate` FQDN 태그는 모든 Windows Update 엔드포인트를 포함하며 **Microsoft가 자동으로 유지 관리**합니다.

| 규칙 유형 | 최적 용도 |
|---------|---------|
| 네트워크 규칙 | IP/포트 기반 (레이어 3/4) |
| **애플리케이션 규칙 + FQDN 태그** | **자동 관리 FQDN을 사용한 도메인 기반 (레이어 7)** |
| 애플리케이션 규칙 + 개별 FQDN | 도메인 기반이지만 수동 유지 관리 필요 |

**FQDN 태그가 최적인 이유:**
- Windows Update는 동적으로 변경되는 많은 엔드포인트를 사용
- FQDN 태그는 Microsoft가 자동으로 업데이트
- 최소한의 유지 관리 필요

**시험 팁**: "최소 유지 관리로 Windows Update 허용" → 애플리케이션 규칙 + **`WindowsUpdate` FQDN 태그**.

---

### 문제 40
**미국 동부** 지역에 VNet1이라는 가상 네트워크가 있는 Azure 구독이 있습니다. VNet1에는 Subnet1이 있습니다. **미국 서부** 지역에 RouteTable1이라는 경로 테이블을 만듭니다. RouteTable1을 Subnet1과 연결해야 합니다. 먼저 무엇을 해야 합니까?

- A. Azure Resource Mover를 사용하여 VNet1을 미국 서부 지역으로 이동
- B. Azure Resource Mover를 사용하여 RouteTable1을 미국 동부 지역으로 이동
- C. RouteTable1을 삭제하고 미국 동부 지역에 새 경로 테이블 만들기
- D. 미국 동부와 미국 서부 간의 VNet 피어링을 만들어 연결 활성화

**정답: C**
**설명:**
경로 테이블은 **동일한 Azure 지역 및 구독**에 있는 가상 네트워크의 서브넷에만 연결할 수 있습니다. Azure는 경로 테이블의 지역 이동 또는 변경을 지원하지 않습니다 — 올바른 지역에 **삭제하고 다시 만들어야** 합니다.

| 옵션 | 유효 여부 |
|-----|---------|
| VNet1을 미국 서부로 이동 | ❌ 파괴적; 방향이 잘못됨 |
| RouteTable1을 미국 동부로 이동 | ❌ 경로 테이블은 지역 간 이동 불가 |
| **RouteTable1 삭제 후 미국 동부에 새로 만들기** | **✅ 유일하게 유효한 솔루션** |
| VNet 피어링 | ❌ 경로 테이블 지역 제약과 무관 |

**시험 팁**: 경로 테이블과 서브넷은 **동일한 지역**에 있어야 합니다. 그렇지 않은 경우 경로 테이블을 삭제하고 올바른 지역에 새로 만드세요.

---

## 5) 모니터링

### 문제 41
여러 리소스가 포함된 Azure 구독이 있습니다. `admin@contoso.com`에 이메일 알림을 포함하는 AG1이라는 작업 그룹을 만듭니다. Azure Monitor 서비스 제한을 초과하지 않고 AG1에서 `admin@contoso.com`으로 이메일 알림을 보낼 수 있도록 해야 합니다. 시간당 `admin@contoso.com`으로 보낼 수 있는 최대 이메일 수는 얼마입니까?

- A. 10
- B. 100
- C. 1,000
- D. 제한 없음

**정답: B**
**설명:**
Azure Monitor 작업 그룹 이메일 알림은 이메일 주소당 **시간당 최대 100개**로 속도가 제한됩니다. 이 임계값을 초과하면 속도 제한 기간이 만료될 때까지 추가 이메일이 전송되지 않습니다.

| 알림 유형 | 속도 제한 |
|---------|---------|
| **이메일** | **수신자당 시간당 100개** |
| SMS | 번호당 5분마다 1개 |
| 음성 | 번호당 5분마다 1개 |

**시험 팁**: Azure Monitor 작업 그룹 이메일 한도 = 수신자당 **시간당 100개**. 자주 나오는 함정: 1,000개는 틀린 답.

---

### 문제 42
VM1이라는 가상 머신이 있는 Azure 구독이 있습니다. VM1에서 성능 모니터링을 위해 VM Insights를 활성화할 계획입니다. VM Insights를 활성화하기 전에 구성해야 하는 최소 필수 구성 요소를 식별해야 합니다. 필요한 두 가지 구성 요소는 무엇입니까?

- A. Log Analytics 작업 영역 및 Dependency Agent
- B. **Log Analytics 작업 영역 및 Azure Monitor Agent**
- C. Azure 진단 확장 및 Log Analytics 작업 영역
- D. Azure Monitor Agent 및 Network Watcher

**정답: B**
**설명:**
VM Insights에 필요한 최소 구성 요소는 다음과 같습니다:

| 구성 요소 | 역할 |
|---------|------|
| **Log Analytics 작업 영역** | 수집된 성능 데이터 및 로그 저장 |
| **Azure Monitor Agent (AMA)** | VM에서 데이터를 수집하여 작업 영역으로 전송 |

**Dependency Agent 참고:**
Dependency Agent는 특히 **Map 기능**(프로세스/네트워크 시각화)에 필요하지만 기본 VM Insights 성능 모니터링에는 **필요하지 않습니다**. 최소 활성화를 위해서는 AMA + 작업 영역만 필요합니다.

| 구성 요소 | VM Insights (기본) 필요 여부 | Map 기능 필요 여부 |
|---------|------------------------|---------------|
| Log Analytics 작업 영역 | ✅ | ✅ |
| Azure Monitor Agent | ✅ | ✅ |
| Dependency Agent | ❌ (선택 사항) | ✅ |

**시험 팁**: VM Insights 최소 = **Log Analytics 작업 영역 + Azure Monitor Agent**.

---

## 6) 백업 및 복구

### 문제 43
Azure Site Recovery를 사용하여 VM1이라는 Azure 가상 머신이 보조 지역으로 복제되고 있습니다. VM1을 보조 지역으로 장애 조치하고 VM이 올바르게 실행되고 있는지 확인합니다. 그런 다음 장애 조치를 커밋합니다. 커밋 작업 후 복구 지점은 어떻게 됩니까?

- A. 복구 지점은 구성된 보존 기간 동안 유지됨
- B. 복구 지점이 보조 지역 Vault으로 이동됨
- C. 복구 지점이 삭제됨
- D. 복구 지점이 콜드 스토리지에 보관됨

**정답: C**
**설명:**
Azure Site Recovery의 **커밋** 작업 후 기존 복구 지점은 모두 **삭제**됩니다. 커밋은 장애 조치를 확정합니다 — 보조 지역 VM이 이제 프로덕션 VM임을 확인하여 롤백을 불가능하게 만듭니다.

**ASR 장애 조치 수명 주기:**

| 단계 | 복구 지점 | 롤백 가능? |
|-----|---------|---------|
| 장애 조치 (커밋 전) | 유지됨 | ✅ 장애 복구 가능 |
| **커밋** | **삭제됨** | **❌ 롤백 불가** |
| 커밋 후 | 없음 | ❌ 먼저 재보호 필요 |

**커밋 후** 기본 지역으로 다시 복제하려면 **재보호(Re-protect)** 작업을 수행해야 합니다(문제 13 참조).

**시험 팁**: ASR 커밋 → 복구 지점 **삭제**, 롤백 불가.

---

## 7) 보안

### 문제 44
시스템 할당 관리 ID가 활성화된 WebApp1이라는 Azure App Service 웹앱이 있습니다. Azure 역할 기반 액세스 제어 권한 모델을 사용하는 Vault1이라는 Azure Key Vault가 있습니다. WebApp1은 애플리케이션 설정의 Key Vault 참조를 사용하여 Vault1에서 비밀을 읽어야 합니다. 최소한으로 관리 ID에 어떤 Azure 역할을 할당해야 합니까?

- A. Key Vault Reader
- B. Key Vault Secrets User
- C. Key Vault Contributor
- D. Key Vault Administrator

**정답: B**
**설명:**
**RBAC 권한 모델**을 사용하여 Key Vault에서 비밀을 읽으려면 최소한 **Key Vault Secrets User** 역할이 필요합니다.

| 역할 | 비밀 읽기? | 비밀 쓰기? | Key Vault 구성 관리? |
|-----|---------|---------|-----------------|
| Key Vault Reader | ❌ (메타데이터만) | ❌ | ❌ |
| **Key Vault Secrets User** | **✅** | **❌** | **❌** |
| Key Vault Secrets Officer | ✅ | ✅ | ❌ |
| Key Vault Contributor | ❌ (제어 플레인) | ❌ (데이터 플레인) | ✅ |
| Key Vault Administrator | ✅ | ✅ | ✅ |

**Key Vault 참조 흐름:**
```
App Service (WebApp1)
  → 관리 ID → Azure AD 토큰
  → Key Vault Secrets User 역할
  → Vault1에서 비밀 읽기
  → 앱 설정에 주입
```

**시험 팁**: App Service가 관리 ID를 통해 Key Vault 비밀 읽기 → **Key Vault Secrets User** 할당(최소 권한).

---
