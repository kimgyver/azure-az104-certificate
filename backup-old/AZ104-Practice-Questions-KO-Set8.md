# AZ-104 예상 문제 (세트 8) — 한국어

출처: `expected_questions/4.txt` — 모든 질문 프롬프트, 함정, 검증된 정답 해설 검토 완료.

---

## 1) 컴퓨팅 — Custom Script Extension

### 문제 1
Windows Server를 실행하는 Azure 가상 머신 VM1에 Azure PowerShell을 사용하여 Custom Script Extension을 배포하려 합니다. 확장 소스가 Microsoft임을 식별하는 파라미터는 무엇입니까?

- A. `-ExtensionName`
- B. `-Location`
- C. `-Publisher`
- D. `-TypeHandlerVersion`

**정답: C**
**해설:** `-Publisher` 파라미터는 VM 확장을 제공하는 게시자를 지정합니다. Custom Script Extension의 경우 Windows는 `Microsoft.Compute`, Linux는 `Microsoft.Azure.Extensions`를 사용합니다. 이 문제는 스크립트가 실행되는 방식이 아니라 확장 제공자를 식별하는 파라미터를 묻습니다.

### 문제 2
Windows Server Azure VM에서 Custom Script Extension으로 IIS를 구성하는 PowerShell 스크립트를 실행했습니다. VM이 시작될 때마다 동일한 스크립트가 자동으로 실행되도록 해야 합니다. 어떻게 해야 합니까?

- A. 재시작할 때마다 Custom Script Extension을 다시 활성화한다
- B. 시작 Runbook으로 Azure Automation을 구성한다
- C. 일정에 따라 Azure Run Command를 사용한다
- D. Windows Task Scheduler에서 시스템 시작 시 트리거되는 예약 작업을 만든다

**정답: D**
**해설:** Custom Script Extension은 배포 시점에 한 번만 실행되며 VM 재시작 시 재실행되지 않습니다. Windows에서 반복적인 부팅 시 스크립트 실행에는 시작 트리거가 있는 Windows Task Scheduler를 사용합니다. Azure Automation도 기술적으로 가능하지만 이 시험 문제에서 테스트하는 내용에 비해 과도한 솔루션입니다.

### 문제 3
Ubuntu 22.04를 실행하는 Azure VM에서 VM이 부팅될 때마다 Bash 스크립트를 실행해야 합니다. 스크립트는 VM에 로컬로 저장되어 있습니다. 올바른 OS 수준 메커니즘은 무엇입니까?

- A. Custom Script Extension
- B. Windows Task Scheduler
- C. `WantedBy=multi-user.target`으로 구성된 systemd 서비스 유닛
- D. Azure Automation State Configuration

**정답: C**
**해설:** Linux VM에서 반복 시작 스크립트를 위한 올바른 메커니즘은 부팅 시 실행되도록 구성된 systemd 서비스 유닛(`WantedBy=multi-user.target`)입니다. Custom Script Extension은 프로비저닝 시 한 번 실행됩니다. Windows Task Scheduler는 Windows 전용입니다. Azure Automation State Configuration은 임의 스크립트 실행이 아닌 구성 상태를 적용합니다.

### 문제 4
Windows Azure VM에서 IIS 설정을 구성하기 위해 Custom Script Extension을 배포했습니다. VM이 할당 해제 후 재시작될 때 CSE 스크립트가 다시 실행됩니까?

- A. 예, CSE는 기본적으로 VM 시작 시마다 재실행됩니다
- B. 예, `forceUpdateTag` 속성이 설정된 경우에 재실행됩니다
- C. 아니요, CSE는 처음 적용될 때만 실행되고 VM 재시작 시 재실행되지 않습니다
- D. 아니요, Azure Automation이 트리거로 구성되지 않으면 재실행되지 않습니다

**정답: C**
**해설:** Custom Script Extension은 처음 배포될 때 정확히 한 번 실행됩니다. VM 재시작, 할당 해제, 재배포 시 재실행되지 않습니다. 단, 확장을 명시적으로 제거하고 새 `forceUpdateTag`로 다시 추가하는 경우는 예외입니다. 이는 각 부팅 시 실행되는 Windows Task Scheduler나 systemd와의 중요한 동작 차이입니다.

### 문제 5
Windows Server를 실행하는 Azure VM에서 VM 시작 시 PowerShell 스크립트 실행을 자동화해야 합니다. 솔루션은 추가 Azure 관리 서비스 없이 작동해야 하며 VM 확장 구성을 수정하지 않아야 합니다. 무엇을 구성해야 합니까?

- A. Start VM 경고에 연결된 Azure Automation Runbook
- B. 시작 트리거 파라미터가 있는 Custom Script Extension
- C. "시스템 시작 시" 트리거가 설정된 Windows Task Scheduler
- D. Azure Monitor 작업 그룹을 통해 예약된 Azure VM Run Command

**정답: C**
**해설:** Windows Task Scheduler는 Windows VM의 반복 시작 스크립트를 위한 기본 OS 수준 메커니즘입니다. 추가 Azure 서비스가 필요하지 않으며 VM 확장 구성을 수정하지 않습니다. Custom Script Extension은 반복 실행을 지원하지 않습니다.

### 문제 6
개발자가 Linux VM용 Custom Script Extension을 구성하고 있으며 올바른 게시자 이름을 확인해야 합니다. `-Publisher` 파라미터의 올바른 값은 무엇입니까?

- A. `Microsoft.Compute`
- B. `Microsoft.OSTCExtensions`
- C. `Microsoft.Azure.Extensions`
- D. `Microsoft.Automation`

**정답: C**
**해설:** Linux VM의 경우 Custom Script Extension 게시자는 `Microsoft.Azure.Extensions`입니다. Windows VM의 경우 `Microsoft.Compute`입니다. `Microsoft.OSTCExtensions`는 이전 Linux CSE 버전에 사용되었으며 현재는 사용 중단되었습니다. `Microsoft.Automation`은 VM 확장 게시자가 아닙니다.

---

## 2) ID, 거버넌스, RBAC

### 문제 7
조직의 게스트 사용자만 포함하는 Microsoft Entra ID 동적 사용자 그룹을 만들어야 합니다. 어떤 멤버십 규칙을 사용해야 합니까?

- A. `(user.companyName -eq "Contoso")`
- B. `(user.objectType -eq "Guest")`
- C. `(user.mail -contains "contoso")`
- D. `(user.userType -eq "Guest")`

**정답: D**
**해설:** `user.userType`은 Entra 동적 그룹 규칙에서 게스트 사용자(`"Guest"`)와 구성원 사용자(`"Member"`)를 구분하는 올바른 기본 제공 속성입니다. `companyName`은 모든 게스트에 설정되지 않을 수 있어 신뢰할 수 없습니다. `mail` 기반 패턴은 정확하지 않습니다. `user.objectType`은 동적 그룹 규칙의 유효한 속성이 아닙니다.

### 문제 8
Microsoft Entra ID에서 내부(구성원) 사용자만 포함하는 동적 그룹 멤버십 규칙을 만들고 있습니다 — 게스트는 제외해야 합니다. 올바른 규칙은 무엇입니까?

- A. `(user.userType -eq "Member")`
- B. `(user.userType -ne "Guest")`
- C. `(user.accountEnabled -eq true)`
- D. `(user.externalUserState -eq "PendingAcceptance")`

**정답: B**
**해설:** `(user.userType -ne "Guest")`는 `userType`이 "Guest"가 아닌 모든 사용자와 일치하여 내부 구성원 사용자만 효과적으로 포함합니다. `(user.userType -eq "Member")`도 동일해 보이지만 일부 계정에서 `user.userType`이 null일 수 있으므로 `-ne "Guest"` 형식이 더 안정적입니다.

### 문제 9
구독 범위에서 사용자에게 두 개의 사용자 지정 RBAC 역할이 할당되어 있습니다. Role1은 모든 Blob 데이터 작업을 허용하고 `NotDataActions`를 통해 삭제를 제외합니다. Role2는 `DataActions`를 통해 Blob 삭제를 명시적으로 허용합니다. 사용자가 수행할 수 있는 Blob 데이터 작업은 무엇입니까?

- A. `NotDataActions`가 Role2를 재정의하므로 삭제를 제외한 모든 Blob 작업
- B. 읽기 및 쓰기만 가능
- C. Azure RBAC는 누적 방식이므로 삭제를 포함한 모든 Blob 작업
- D. 충돌하는 역할이 서로 상쇄되므로 Blob 작업 없음

**정답: C**
**해설:** Azure RBAC는 누적(합집합) 권한 모델입니다 — 모든 할당된 역할의 권한이 합산됩니다. `NotDataActions`는 동일한 역할 정의 내에서만 권한을 차감하며, 전역 거부가 아니고 다른 역할을 재정의하지 않습니다. Role2가 DataAction으로 Blob 삭제를 명시적으로 허용하므로 유효한 합집합에 삭제가 포함됩니다.

### 문제 10
보안 엔지니어가 Azure RBAC의 `NotDataActions`가 사용자의 데이터 작업을 "명시적으로 거부"한다고 설명합니다. 이것이 맞습니까?

- A. 예, `NotDataActions`는 다른 역할이 재정의할 수 없는 전역 거부를 만듭니다
- B. 예, 하지만 리소스 그룹 범위 이상에서만
- C. 아니요, `NotDataActions`는 동일한 역할 정의 내에서만 권한을 차감하며 다른 역할 할당에는 영향이 없습니다
- D. 아니요, `NotDataActions`는 데이터 플레인이 아닌 관리 플레인 작업에만 적용됩니다

**정답: C**
**해설:** `NotDataActions`는 거부 규칙이 아닙니다. 동일한 역할 정의가 부여한 권한에서 특정 데이터 작업만 제거합니다. 동일한 사용자에게 할당된 다른 역할이 해당 데이터 작업을 허용하면 사용자는 접근 권한을 유지합니다. Azure RBAC는 근본적으로 누적 방식이며 역할 간에 기본 제공 거부 재정의가 없습니다.

### 문제 11
사용자가 Azure Cost Management 청구 데이터와 예산을 볼 수 있지만 리소스를 수정할 수 없는 최소 RBAC 역할을 할당해야 합니다. 어떤 역할을 할당해야 합니까?

- A. Contributor
- B. Reader
- C. Cost Management Reader
- D. Billing Account Reader

**정답: C**
**해설:** Cost Management Reader는 리소스를 수정할 권한 없이 비용 관리 데이터, 예산, 지출 보고서에 대한 읽기 전용 접근을 허용합니다. 일반 Reader 역할도 읽기 접근을 제공하지만 더 광범위합니다. Contributor는 리소스 수정을 허용하여 과권한입니다.

### 문제 12
Microsoft Entra ID에서 규칙 `(user.department -eq "Finance")`를 사용하여 동적 그룹을 만들었습니다. Finance 부서의 사용자가 `accountEnabled` 값이 `false`입니다. 이 사용자가 그룹에 포함됩니까?

- A. 아니요, 비활성화된 계정은 동적 그룹에서 자동으로 제외됩니다
- B. 예, 동적 그룹 멤버십은 계정 상태와 관계없이 속성 규칙만을 기반으로 합니다
- C. 아니요, 활성화된 계정만 동적 그룹 규칙에 대해 평가됩니다
- D. 예, 하지만 계정이 다시 활성화될 때까지 보류 중으로 표시됩니다

**정답: B**
**해설:** Entra ID 동적 그룹 멤버십은 사용자의 속성이 멤버십 규칙과 일치하는지 여부에 의해서만 결정됩니다. 규칙에 명시적으로 포함되지 않는 한 `accountEnabled`는 그룹 멤버십 평가에 영향을 미치지 않습니다. `(user.department -eq "Finance")`와 일치하는 비활성화된 계정도 여전히 그룹의 구성원이 됩니다.

### 문제 13
타사 애플리케이션 서비스 주체가 Azure Key Vault에서 비밀을 나열하고 읽을 수 있어야 하지만 생성하거나 삭제할 수는 없어야 합니다. Key Vault 범위에서 어떤 Azure 기본 제공 RBAC 역할을 할당해야 합니까?

- A. Key Vault Contributor
- B. Key Vault Administrator
- C. Key Vault Secrets Officer
- D. Key Vault Secrets User

**정답: D**
**해설:** Key Vault Secrets User는 비밀에 대한 `get` 및 `list` 권한을 부여합니다 — 읽기 전용 애플리케이션에 필요한 정확한 권한입니다. Key Vault Secrets Officer는 생성 및 삭제 권한을 추가합니다(과권한). Key Vault Administrator는 전체 Key Vault 관리를 허용합니다.

---

## 3) 모니터링 — VM Insights

### 문제 14
Azure 구독에서 모든 가상 머신에 VM Insights를 사용하도록 계획하고 있습니다. VM Insights가 수집하는 모니터링 데이터를 저장하는 곳은 어디입니까?

- A. Azure Storage 계정
- B. Application Insights 리소스
- C. Azure Monitor 작업 영역
- D. Log Analytics 작업 영역

**정답: D**
**해설:** VM Insights는 성능 메트릭, 프로세스 데이터, 종속성 데이터를 Log Analytics 작업 영역에 저장합니다. Application Insights는 VM 게스트 OS 데이터가 아닌 애플리케이션 성능 모니터링(APM)용입니다. Azure Monitor 작업 영역은 VM Insights가 아닌 Prometheus 메트릭에 사용됩니다.

### 문제 15
50개의 가상 머신이 포함된 Azure 구독이 있습니다. Azure Policy를 사용하여 모든 가상 머신에서 VM Insights를 사용하도록 계획하고 있습니다. 정책 이니셔티브를 할당하기 전에 먼저 어떤 리소스를 만들어야 합니까?

- A. Log Analytics 작업 영역
- B. Application Insights 리소스
- C. Azure Monitor 작업 영역
- D. VM Insights 데이터 수집 규칙(DCR)

**정답: D**
**해설:** Azure Policy를 통해 VM Insights를 활성화할 때 필요한 전제 조건은 VM Insights 데이터 수집 규칙(DCR)입니다. DCR은 수집할 데이터와 데이터 스트림 구성 방법을 정의합니다. Log Analytics 작업 영역도 필요하지만 먼저 존재해야 하는 정책 할당 전제 조건은 DCR입니다.

### 문제 16
모니터링 엔지니어가 VM Insights와 함께 사용되는 데이터 수집 규칙(DCR)이 정의하는 것이 무엇인지 묻습니다. DCR의 목적을 가장 잘 설명하는 것은 무엇입니까?

- A. VM 진단 로그의 백업 보존 일정을 정의합니다
- B. VM 데이터 수집에 대한 청구 대상 Log Analytics 작업 영역을 지정합니다
- C. VM에서 수집할 성능 데이터와 데이터 스트림 구성 방법을 정의합니다
- D. VM 메트릭이 임계값을 초과할 때 발생하는 경보 규칙을 구성합니다

**정답: C**
**해설:** 데이터 수집 규칙(DCR)은 에이전트에서 수집해야 하는 데이터(성능 카운터, 로그 스트림 등)와 데이터 처리 및 전달 방법을 정의합니다. Azure Policy를 통한 VM Insights의 경우 에이전트에게 수집할 내용을 알려주기 때문에 정책 이니셔티브를 할당하기 전에 DCR이 존재해야 합니다.

### 문제 17
Azure VM에서 실행되는 .NET 애플리케이션의 요청률, 예외 수, 종속성 호출 기간과 같은 애플리케이션 수준 성능 데이터를 모니터링하려고 합니다. 어떤 Azure 모니터링 리소스를 사용해야 합니까?

- A. Log Analytics 작업 영역이 있는 VM Insights
- B. Application Insights
- C. Azure Monitor 작업 영역(Prometheus)
- D. Storage 계정이 있는 Azure Diagnostics 확장

**정답: B**
**해설:** Application Insights(Azure Monitor APM)는 요청률, 응답 시간, 예외 및 종속성 추적과 같은 애플리케이션 수준 원격 분석을 위해 설계되었습니다. VM Insights는 애플리케이션 코드가 아닌 게스트 OS(CPU, 메모리, 프로세스, 네트워크 연결)를 모니터링합니다.

### 문제 18
기본 설정을 사용하여 Azure Portal을 통해 가상 머신에 VM Insights를 구성합니다. 이 기본 포털 온보딩 시나리오에서 VM Insights가 기본적으로 수집하는 데이터는 무엇입니까?

- A. 애플리케이션 추적 및 예외 로그
- B. 네트워크 보안 그룹 흐름 로그
- C. 게스트 성능 메트릭만(CPU, 메모리, 디스크, 네트워크)
- D. 전체 종속성 맵 데이터 및 프로세스 인벤토리

**정답: C**
**해설:** 기본 설정으로 Portal을 통해 VM Insights를 온보딩하면 게스트 성능 메트릭(CPU, 메모리, 디스크, 네트워크)만 수집됩니다. 전체 종속성 맵(Service Map)과 프로세스 인벤토리는 Azure Monitor 에이전트 외에 Dependency Agent가 필요합니다. 이는 명시적으로 활성화해야 하는 선택적 구성 요소입니다.

### 문제 19
Azure Kubernetes Service(AKS) 및 기타 컨테이너화된 워크로드에서 Prometheus 스타일 메트릭 수집에 특별히 사용되는 Azure Monitor 작업 영역 유형은 무엇입니까?

- A. Log Analytics 작업 영역
- B. Application Insights 리소스
- C. Azure Monitor 작업 영역
- D. Azure Monitor의 메트릭 네임스페이스

**정답: C**
**해설:** Azure Monitor 작업 영역은 AKS 및 컨테이너화된 워크로드에서 스크랩된 Prometheus 호환 메트릭을 위해 특별히 설계된 저장소입니다. Log Analytics 작업 영역은 로그와 VM Insights 데이터를 저장합니다. Application Insights는 APM 원격 분석용입니다.

---

## 4) 스토리지와 파일 — Azure Files AD DS

### 문제 20
Azure 파일 공유가 있는 스토리지 계정이 포함된 Azure 구독이 있습니다. Microsoft Entra Connect를 통해 Microsoft Entra ID에 동기화된 온-프레미스 AD DS 환경이 있습니다. Azure 파일 공유에 AD DS 인증을 사용하도록 설정해야 합니다. `Join-AzStorageAccount` cmdlet이 기본적으로 AD DS에 만드는 개체 유형은 무엇입니까?

- A. 사용자 계정
- B. 서비스 로그온 계정
- C. 관리 서비스 계정
- D. 컴퓨터 계정

**정답: D**
**해설:** 기본적으로 `Join-AzStorageAccount`는 Kerberos/SMB 인증을 지원하는 서버 스타일 네트워크 리소스로 스토리지 계정을 나타내기 위해 AD DS에 컴퓨터 계정을 만듭니다. `-DomainAccountType ServiceLogonAccount`를 지정하면 서비스 로그온 계정을 만들 수 있습니다. 그룹 관리 서비스 계정(gMSA)은 이 시나리오에서 지원되지 않습니다.

### 문제 21
Azure 파일 공유에 대한 AD DS 인증을 구성하기 위해 `Join-AzStorageAccount`를 실행했습니다. 보안 정책에 따라 기본 개체 유형 대신 서비스 로그온 계정을 사용해야 합니다. cmdlet에 어떤 파라미터를 추가해야 합니까?

- A. `-DomainAccountType ComputerAccount`
- B. `-DomainAccountType ServiceLogonAccount`
- C. `-DomainAccountType ManagedServiceAccount`
- D. `-UseServiceAccount $true`

**정답: B**
**해설:** `-DomainAccountType ServiceLogonAccount` 파라미터는 기본 컴퓨터 계정 대신 서비스 로그온 계정을 만들도록 `Join-AzStorageAccount`에 지시합니다. `ComputerAccount`는 기본값이므로 지정할 필요가 없습니다. `ManagedServiceAccount`와 `-UseServiceAccount`는 이 cmdlet의 유효한 파라미터 값이 아닙니다.

### 문제 22
Azure Files AD DS 인증을 구성하고 있습니다. 동료가 gMSA(그룹 관리 서비스 계정)를 AD DS 개체로 사용할 수 있는지 묻습니다. 올바른 답은 무엇입니까?

- A. 예, gMSA는 Azure Files AD DS 인증에 권장되는 계정 유형입니다
- B. 예, 하지만 프리미엄 계층 파일 공유에만 해당됩니다
- C. 아니요, gMSA는 지원되지 않습니다. 컴퓨터 계정 또는 서비스 로그온 계정만 사용할 수 있습니다
- D. 아니요, gMSA는 Azure Arc가 필요하며 스토리지 계정에는 지원되지 않습니다

**정답: C**
**해설:** `Join-AzStorageAccount`는 컴퓨터 계정(기본값)과 서비스 로그온 계정(`-DomainAccountType ServiceLogonAccount`) 두 가지 AD DS 개체 유형을 지원합니다. gMSA는 파일 공유 계층에 관계없이 Azure Files AD DS 인증에 지원되지 않습니다.

### 문제 23
Azure 파일 공유에 대한 AD DS 인증을 성공적으로 구성했습니다. 사용자들이 파일 공유를 탐색할 수 있지만 도메인에 가입되지 않은 컴퓨터에서 마운트할 수 없다고 보고합니다. 가장 가능성 높은 원인은 무엇입니까?

- A. Recovery Services 자격 증명 모음이 스토리지 계정과 동일한 지역에 없습니다
- B. 스토리지 계정 방화벽이 SMB 포트 445를 차단하고 있습니다
- C. Entra ID에 대한 AD DS 동기화가 완료되지 않았습니다
- D. `Join-AzStorageAccount` cmdlet이 잘못된 개체 유형을 만들었습니다

**정답: B**
**해설:** Azure Files AD DS 인증은 SMB(포트 445)를 사용합니다. 많은 기업 네트워크와 ISP는 아웃바운드 포트 445를 차단합니다. AD DS 자격 증명을 사용하여 Azure 파일 공유를 마운트하려는 도메인에 가입되지 않은 컴퓨터는 포트 445가 차단된 경우 실패합니다.

### 문제 24
AD DS(Active Directory Domain Services) 인증으로 Azure Files를 구성해야 합니다. `Join-AzStorageAccount`를 실행하기 전에 PowerShell cmdlet을 실행하는 컴퓨터에 대해 무엇이 참이어야 합니까?

- A. 컴퓨터는 관리 ID가 있는 Azure VM이어야 합니다
- B. 컴퓨터는 사용하려는 AD DS 도메인에 도메인 가입되어 있어야 합니다
- C. 컴퓨터는 Windows Server 2022를 실행 중이어야 합니다
- D. 컴퓨터에 AzureAD PowerShell 모듈이 설치되어 있어야 합니다

**정답: B**
**해설:** `Join-AzStorageAccount`는 대상 AD DS 도메인에 도메인 가입된 컴퓨터에서 실행해야 합니다. cmdlet이 스토리지 계정을 대신하여 오프라인 도메인 가입을 수행하고 도메인 컨트롤러에 대한 접근이 필요하기 때문입니다. 컴퓨터가 Azure VM일 필요는 없습니다 — 온-프레미스 도메인 가입 컴퓨터도 작동합니다.

---

## 5) 네트워킹과 DNS

### 문제 25
`contoso.com`이라는 공용 DNS 영역이 포함된 Azure 구독이 있습니다. 세 개의 웹 서버에는 IP 주소 10.1.1.10, 10.1.1.11, 10.1.1.12가 있습니다. 클라이언트가 `www.contoso.com`을 쿼리할 때 세 가지 IP 주소가 모두 반환되도록 하려고 합니다. 어떻게 해야 합니까?

- A. IP 주소당 하나씩 세 개의 별도 A 레코드를 만든다
- B. 부하 분산 별칭을 가리키는 CNAME 레코드를 만든다
- C. Azure Load Balancer를 배포하고 단일 A 레코드가 이를 가리키게 한다
- D. `www`에 대한 단일 A 레코드 집합을 세 가지 IP 주소 값으로 만든다

**정답: D**
**해설:** Azure DNS는 단일 A 레코드 집합에서 여러 IP 값을 지원합니다. 쿼리 시 기본 클라이언트 측 라운드 로빈 선택을 위해 모든 IP가 반환됩니다. 이는 DNS 수준 분산에 불과합니다 — 상태 확인, 장애 조치, 세션 선호도가 없습니다.

### 문제 26
`www.contoso.com`에 대한 Azure DNS A 레코드 집합에 기본 부하 분산을 위해 세 가지 IP 주소를 구성했습니다. 사용자가 이 설정이 웹 서버 중 하나를 사용할 수 없을 때 자동 장애 조치를 제공하는지 묻습니다. 올바른 답은 무엇입니까?

- A. 예, Azure DNS는 비정상 IP 주소를 자동으로 제거합니다
- B. 예, 하지만 DNS 영역에 Azure Traffic Manager 통합이 활성화된 경우에만
- C. 아니요, Azure DNS A 레코드는 상태 확인을 수행하지 않으며 모든 IP는 항상 반환됩니다
- D. 아니요, 하지만 Azure DNS는 30초 프로브 후 도달할 수 없는 IP를 제거합니다

**정답: C**
**해설:** Azure DNS는 A 레코드 값에 대한 상태 모니터링을 수행하지 않습니다. 레코드 집합의 모든 IP는 서버 가용성에 관계없이 항상 반환됩니다. 상태 인식 라우팅과 자동 장애 조치를 위해서는 Azure Traffic Manager(DNS 기반) 또는 Azure Load Balancer(네트워크 수준)를 사용합니다.

### 문제 27
유럽 사용자는 유럽 서버로, 북미 사용자는 미국 서버로 안내되도록 DNS 기반 지리적 부하 분산을 구성해야 합니다. 어떤 Azure 서비스를 사용해야 합니까?

- A. 여러 IP 값이 있는 Azure DNS A 레코드
- B. Azure Load Balancer
- C. Azure Application Gateway
- D. 지리적 라우팅 프로필이 있는 Azure Traffic Manager

**정답: D**
**해설:** 지리적 라우팅 방법이 있는 Azure Traffic Manager는 클라이언트의 DNS 확인자의 지리적 위치를 기반으로 DNS 쿼리를 안내합니다. 여러 IP가 있는 Azure DNS A 레코드는 지리적 인식 없이 라운드 로빈만 수행합니다. Azure Load Balancer는 지리적 라우팅이 없는 L4 네트워크 부하 분산기입니다.

### 문제 28
Azure DNS 영역에서 `www.contoso.com`에 대한 CNAME 레코드를 만들어 `backend.contoso.com`을 가리키게 했습니다. 동료가 동일한 영역에서 `www.contoso.com`에 대한 A 레코드를 만들려고 합니다. 어떻게 됩니까?

- A. 두 번째 레코드가 만들어지고 두 레코드가 공존합니다
- B. Azure DNS가 자동으로 CNAME을 A 레코드로 변환합니다
- C. CNAME이 동일한 이름의 다른 레코드와 공존할 수 없으므로 A 레코드 만들기가 실패합니다
- D. A 레코드가 CNAME 레코드를 덮어씁니다

**정답: C**
**해설:** DNS 표준은 동일한 이름의 CNAME 레코드가 다른 레코드 유형과 공존하는 것을 금지합니다. Azure DNS에서 이미 CNAME이 있는 이름에 A 레코드를 추가하려고 하면(또는 그 반대) 오류가 반환됩니다. 이것은 Azure에 특정한 것이 아니라 표준 DNS 제약입니다.

### 문제 29
Azure 공용 DNS 영역이 있습니다. 개발 팀이 관리하는 별도의 DNS 영역에 `dev.contoso.com` 하위 도메인을 위임해야 합니다. 상위 영역에서 어떤 유형의 DNS 레코드를 만들어야 합니까?

- A. 새 영역의 IP를 가리키는 A 레코드
- B. 새 영역을 가리키는 CNAME 레코드
- C. 위임된 영역의 이름 서버를 가리키는 `dev.contoso.com`에 대한 NS(이름 서버) 레코드
- D. 상위 영역의 SOA 레코드 업데이트

**정답: C**
**해설:** DNS 하위 도메인 위임은 자식 영역의 권한 있는 이름 서버를 가리키는 상위 영역에 NS(이름 서버) 레코드를 만들어 수행합니다. 이는 확인자에게 `dev.contoso.com` 아래의 레코드에 대해 위임된 영역을 쿼리하도록 알려줍니다.

---

## 6) 백업, 복구, SQL

### 문제 30
East US 지역에 있는 스토리지 계정 storage1이 있습니다. storage1에는 Azure 파일 공유 fileshare1이 있습니다. fileshare1을 보호하기 위해 Azure Backup을 구성해야 합니다. Recovery Services 자격 증명 모음을 어느 지역에 만들어야 합니까?

- A. 임의의 지역
- B. West US(페어링된 지역)
- C. 중앙 허브 지역
- D. East US(스토리지 계정과 동일한 지역)

**정답: D**
**해설:** Azure Backup은 Recovery Services 자격 증명 모음이 보호된 리소스와 동일한 지역에 있어야 합니다. Azure 파일 공유 백업의 경우 자격 증명 모음은 스토리지 계정과 같은 지역에 있어야 합니다. 이는 재해 복구를 위해 교차 지역 복제를 사용하는 Azure Site Recovery와 다릅니다.

### 문제 31
Recovery Services 자격 증명 모음 Vault1이 포함된 Azure 구독이 있습니다. 일별 백업 일정으로 Azure 가상 머신에 대한 표준 백업 정책을 만들었습니다. 즉시 복원 스냅샷 보존 기간을 구성해야 합니다. 유효한 보존 기간의 범위는 무엇입니까?

- A. 1일~7일
- B. 7일~35일
- C. 1일~30일
- D. 1일~5일

**정답: D**
**해설:** Azure VM 즉시 복원 스냅샷 보존은 1일~5일 사이로 구성할 수 있습니다. 이 스냅샷은 자격 증명 모음 스토리지로 폴백하기 전에 더 빠른 복원 작업을 위해 VM 근처에 로컬로 저장됩니다. vault 계층 보존(수년), SQL PITR(최대 35일), SQL LTR(최대 10년)과 혼동하지 마십시오.

### 문제 32
DTU 구매 모델을 사용하는 Basic 서비스 계층의 Azure SQL Database가 있습니다. 최대 지원되는 PITR(특정 시점 복원) 백업 보존 기간을 구성해야 합니다. 구성할 수 있는 최대 일수는 얼마입니까?

- A. 35일
- B. 14일
- C. 10년
- D. 7일

**정답: D**
**해설:** Azure SQL Database Basic 계층(DTU 모델)은 최대 PITR 보존 7일을 지원합니다. Standard 및 Premium DTU 계층, General Purpose 및 Business Critical vCore 계층은 최대 35일을 지원합니다. 10년 옵션은 별도 기능인 LTR(장기 보존)을 나타냅니다. "35일" 규칙은 보편적으로 적용되지 않으며 Basic 계층 예외는 자주 나오는 시험 함정입니다.

### 문제 33
DTU 구매 모델을 사용하는 Standard 서비스 계층의 Azure SQL Database가 있습니다. 구성할 수 있는 최대 PITR 백업 보존 기간은 얼마입니까?

- A. 7일
- B. 14일
- C. 35일
- D. 90일

**정답: C**
**해설:** Azure SQL Database Standard, Premium(DTU) 및 General Purpose, Business Critical(vCore) 계층은 모두 PITR 보존을 최대 35일까지 지원합니다. Basic DTU 계층만 7일로 제한됩니다. 90일 이상 옵션은 별도로 수동 구성된 기능인 LTR(장기 보존)을 통해서만 사용할 수 있습니다.

### 문제 34
규정 준수 요구 사항을 충족하기 위해 5년 동안 Azure SQL Database 백업을 보존해야 합니다. PITR 보존 기간은 이미 최대값으로 설정되어 있습니다. 어떤 기능을 구성해야 합니까?

- A. Azure Portal 고급 설정을 사용하여 PITR 보존을 35일 이상으로 늘린다
- B. 연간 보존 정책을 사용하여 LTR(장기 보존) 백업을 구성한다
- C. SQL Database를 대상으로 하는 Azure Backup 정책을 만든다
- D. 지역 중복 백업 스토리지를 활성화하고 보존을 5년으로 설정한다

**정답: B**
**해설:** LTR(장기 보존)은 Azure SQL Database의 주간, 월간 또는 연간 백업 복사본을 최대 10년까지 보존할 수 있습니다. PITR은 포털 설정에 관계없이 35일을 초과하여 연장할 수 없습니다. SQL Database 서비스 내의 LTR 기능이 다년 규정 준수 보존을 위한 주요 메커니즘입니다.

### 문제 35
3계층 애플리케이션이 Azure VM에서 실행되고 Azure Site Recovery로 보호됩니다. 복구 계획을 만들었습니다. 장애 조치 중에 SQL Server VM이 먼저 시작되고 그 다음 앱 서버, 마지막으로 웹 서버가 시작되도록 해야 합니다. 복구 계획에서 무엇을 구성해야 합니까?

- A. 각 가상 머신에 대한 우선 순위 수준을 설정한다
- B. 가상 머신 간 종속성을 구성한다
- C. 가상 머신을 다른 가용성 집합에 배치한다
- D. 복구 계획 내의 별도 그룹에 가상 머신을 추가한다

**정답: D**
**해설:** Azure Site Recovery 복구 계획은 장애 조치 중 VM 시작 순서를 제어하기 위해 그룹을 사용합니다. 번호가 낮은 그룹이 먼저 시작되며 동일한 그룹 내의 VM은 병렬로 시작됩니다. 이 시나리오의 경우: 그룹 1 = SQL Server, 그룹 2 = 앱 서버, 그룹 3 = 웹 서버. "Priority", "dependency", "Availability Sets"는 ASR 복구 계획에서 시작 순서를 제어하지 않는 일반적인 혼동 요소입니다.

### 문제 36
3계층 애플리케이션에 대한 Azure Site Recovery 복구 계획이 있습니다. 세 개의 VM이 모두 동일한 그룹 1에 추가되었습니다. 장애 조치가 시작됩니다. VM은 어떤 순서로 시작됩니까?

- A. 그룹에 추가된 순서대로 시작됩니다
- B. VM 이름의 알파벳 순서로 시작됩니다
- C. 동시에 병렬로 시작됩니다
- D. VM 크기에 따라 시작됩니다(작은 VM 먼저)

**정답: C**
**해설:** Azure Site Recovery 복구 계획에서 동일한 그룹 내의 모든 VM은 동시에 병렬로 시작됩니다. 순차적 순서를 적용하려면 VM을 별도의 그룹에 배치해야 합니다. 그룹 번호만이 순서를 결정합니다 — 낮은 그룹 번호가 먼저 시작됩니다. 그룹 내에서는 순서 지정 메커니즘이 없습니다.

### 문제 37
실수로 삭제로부터 Azure VM 워크로드를 보호하고 20일 전 상태로 VM을 복원할 수 있어야 합니다. 어떤 서비스를 사용해야 하며 자격 증명 모음은 어디에 있어야 합니까?

- A. 페어링된 지역의 Azure Site Recovery 자격 증명 모음
- B. VM과 동일한 지역의 Recovery Services 자격 증명 모음(일별 백업을 최소 20일 이상 보존하는 정책 포함)
- C. 교차 지역 복원이 활성화된 모든 지역의 Recovery Services 자격 증명 모음
- D. 보조 지역의 지역 중복 스토리지가 있는 Azure Backup

**정답: B**
**해설:** Azure Backup(Recovery Services 자격 증명 모음 사용)은 실수로 삭제로부터 보호하고 특정 시점 VM 복원을 지원합니다. 자격 증명 모음은 VM과 동일한 지역에 있어야 합니다. 백업 정책 보존 기간은 20일 전 상태로 복원할 수 있도록 최소 20일 이상으로 설정해야 합니다. Azure Site Recovery는 재해 복구(DR)용이며 장애 조치를 제공하지만 실수로 삭제 복구를 위해 설계되지 않았습니다.

---

## 7) AKS와 컨테이너

### 문제 38
AKS(Azure Kubernetes Service) 클러스터에 Spot 노드 풀을 만들고 제거 정책을 Deallocate로 설정했습니다. Azure가 Spot 노드를 제거한 후 클러스터가 제대로 확장되지 않고 할당량 문제가 발생합니다. 이 동작의 원인은 무엇입니까?

- A. `spot-max-price` 설정이 Deallocate 제거 정책과 충돌합니다
- B. 클러스터 자동 크기 조정기가 Deallocate 정책을 사용할 때 제거된 Spot 노드를 감지할 수 없습니다
- C. Deallocate 제거 정책은 리소스를 해제하기 위해 수동 개입이 필요합니다
- D. 중지됨-할당 해제 상태의 노드가 컴퓨팅 할당량에 포함됩니다

**정답: D**
**해설:** 제거 정책이 Deallocate로 설정되면 제거된 Spot VM은 `Stopped (deallocated)` 상태로 전환됩니다. 실행 중이 아니더라도 구독의 컴퓨팅 할당량에 계속 포함됩니다. 이로 인해 AKS 자동 크기 조정기가 새 노드를 만들 수 없습니다. Delete 제거 정책은 일반적으로 노드가 제거될 때 할당량을 완전히 해제하므로 Spot 노드 풀에 선호됩니다.

### 문제 39
AKS Spot 노드 풀을 구성하고 있으며 제거된 노드가 컴퓨팅 할당량을 소모하지 않아 클러스터 자동 크기 조정기가 대체 노드를 만들 수 있도록 하려고 합니다. 어떤 제거 정책을 구성해야 합니까?

- A. Deallocate
- B. Stop
- C. Hibernate
- D. Delete

**정답: D**
**해설:** Delete 제거 정책은 Azure에 의해 제거될 때 확장 집합에서 Spot VM을 완전히 제거합니다. 이렇게 하면 해당 VM과 관련된 할당량이 해제되어 AKS 자동 크기 조정기가 새 대체 Spot 노드를 만들 수 있습니다. Deallocate 정책은 노드를 중지됨-할당 해제 상태로 유지하여 할당량을 보유하고 자동 크기 조정을 차단합니다.

### 문제 40
Delete 제거 정책을 사용하는 Spot 노드 풀이 있는 AKS 클러스터가 있습니다. Azure가 Spot 노드를 제거합니다. 제거된 VM에 어떤 일이 발생합니까?

- A. VM이 Stopped (deallocated) 상태로 이동하고 할당량이 유지됩니다
- B. VM이 기본 확장 집합에서 삭제되고 할당량이 해제됩니다
- C. VM이 일시 중단되고 5분 후 재시작이 시도됩니다
- D. VM이 관리자 검토를 기다리는 격리 상태로 배치됩니다

**정답: B**
**해설:** Delete 제거 정책을 사용하면 Azure가 Spot VM을 회수할 때 기본 Virtual Machine Scale Set(VMSS)에서 완전히 삭제됩니다. 해당 VM과 관련된 컴퓨팅 할당량이 즉시 해제되어 AKS 클러스터 자동 크기 조정기가 새 노드를 프로비저닝할 수 있습니다.

### 문제 41
AKS Spot 노드 풀을 위한 워크로드를 설계하고 있습니다. Spot 노드에 가장 적합한 워크로드 유형은 무엇입니까?

- A. 영구 스토리지 요구 사항이 있는 스테이트풀 데이터베이스 애플리케이션
- B. 다른 모든 서비스의 인증을 처리하는 API 게이트웨이
- C. 중단되고 재시작될 수 있는 배치 이미지 처리 작업
- D. 지속적인 연결을 유지해야 하는 서비스 메시 제어 플레인

**정답: C**
**해설:** Spot VM은 Azure가 용량이 필요할 때 언제든지 제거될 수 있습니다. Spot 노드 풀은 배치 작업, CI/CD 파이프라인, 병렬 데이터 처리와 같이 상태 비저장이고 중단 가능한 워크로드에 가장 적합합니다. 스테이트풀 데이터베이스, API 게이트웨이, 서비스 메시 제어 플레인은 지속적인 가용성이 필요하며 Spot 노드에 적합하지 않습니다.

### 문제 42
ACR1이라는 Azure Container Registry가 있습니다. CI/CD 파이프라인 서비스 주체가 최소 권한 원칙을 준수하면서 ACR1에서 이미지를 푸시 및 가져올 수 있는 권한을 부여해야 합니다. 레지스트리는 기본 RBAC 레지스트리 권한 모드를 사용합니다. 서비스 주체에게 어떤 Azure 기본 제공 역할을 할당해야 합니까?

- A. AcrPull
- B. Reader
- C. Contributor
- D. AcrPush

**정답: D**
**해설:** AcrPush는 컨테이너 이미지에 대한 pull(`Microsoft.ContainerRegistry/registries/pull/read`)과 push(`Microsoft.ContainerRegistry/registries/push/write`) 권한 모두를 포함하여 CI/CD 파이프라인을 위한 최소 권한 역할입니다. AcrPull은 이미지 pull만 허용하므로 부족합니다. Contributor는 파이프라인에 필요한 것 이상의 레지스트리 관리 권한을 허용하므로 과권한입니다.

### 문제 43
배포 파이프라인에 Azure Container Registry에서 컨테이너 이미지만 가져올 수 있는 역할을 할당해야 합니다 — 푸시하거나 레지스트리를 관리할 수 없어야 합니다. 어떤 역할을 할당해야 합니까?

- A. AcrPush
- B. AcrPull
- C. Reader
- D. AcrDelete

**정답: B**
**해설:** AcrPull은 이미지 pull(읽기) 권한만 부여하며 이미지를 검색하지만 절대 푸시할 필요가 없는 배포 파이프라인에 올바른 최소 권한 역할입니다. AcrPush는 push와 pull 모두를 포함하며 읽기 전용 시나리오에서는 과권한입니다. Reader는 Azure에서 레지스트리 리소스에 대한 읽기 접근을 허용하지만 이미지를 pull하는 데이터 플레인 권한을 부여하지 않습니다.

### 문제 44
보안 검토에서 Azure Container Registry에 할당된 서비스 주체가 Contributor 역할을 가지고 있음을 확인합니다. 해당 주체는 프로덕션 배포 파이프라인에서 컨테이너 이미지를 pull하는 데만 사용됩니다. 권장 수정 방법은 무엇입니까?

- A. Contributor를 Owner로 교체하여 더 명시적인 제어를 추가한다
- B. 최소 권한 원칙을 따르기 위해 Contributor를 AcrPull로 교체한다
- C. push 및 pull 모두 허용하기 위해 Contributor를 AcrPush로 교체한다
- D. Contributor를 유지하지만 push 작업을 제한하는 조건부 접근 정책을 추가한다

**정답: B**
**해설:** Contributor는 레지스트리에 대한 전체 리소스 관리 권한(레지스트리 설정 만들기, 업데이트, 삭제)을 부여하며, 읽기 전용 pull 파이프라인에 필요한 것을 훨씬 초과합니다. 최소 권한 원칙은 이미지 pull 권한만 부여하는 AcrPull을 사용하도록 요구합니다.

---

## 8) 인프라 코드 (IaC) — Bicep

### 문제 45
구독 범위에 배포된 Bicep 파일이 있습니다. 파일에는 스토리지 계정을 배포하는 모듈이 포함되어 있습니다. StorageRG라는 특정 리소스 그룹에 스토리지 계정 모듈을 배포해야 합니다. 모듈 선언에 어떤 범위를 설정해야 합니까?

- A. `scope: subscription()`
- B. `scope: tenant()`
- C. `scope: managementGroup()`
- D. `scope: resourceGroup('StorageRG')`

**정답: D**
**해설:** 구독 범위 Bicep 파일에서 모듈은 기본적으로 구독 범위를 상속합니다. 특정 리소스 그룹에 모듈을 배포하려면 `resourceGroup('StorageRG')`를 사용하여 범위를 재정의해야 합니다. `resourceGroup()` 함수는 현재 구독 내에서 지정된 리소스 그룹을 대상으로 하는 범위 객체를 반환합니다.

### 문제 46
Bicep 모듈 선언에서 `scope: resourceGroup('StorageRG')` 표현식은 무엇을 합니까?

- A. StorageRG라는 새 리소스 그룹이 없으면 만듭니다
- B. 모듈을 StorageRG 내에서 읽기 전용 접근으로 제한합니다
- C. 모듈 배포를 위해 지정된 리소스 그룹을 대상으로 하는 범위 객체를 반환합니다
- D. 모듈의 출력을 StorageRG 리소스 그룹 컨텍스트에 할당합니다

**정답: C**
**해설:** Bicep의 `resourceGroup('name')` 함수는 지정된 리소스 그룹을 참조하는 범위 객체를 반환합니다. 모듈의 `scope` 속성에 사용되면 상위 Bicep 파일의 범위에 관계없이 해당 모듈이 배포하는 모든 리소스를 지정된 리소스 그룹에 배포합니다. 리소스 그룹을 만들거나 접근 권한을 변경하지 않습니다.

### 문제 47
구독 범위 Bicep 파일이 있습니다. 파일 내의 모듈에 `scope` 속성이 정의되어 있지 않습니다. 모듈의 리소스가 어디에 배포됩니까?

- A. 테넌트 루트 관리 그룹에
- B. 구독 범위에(상위 Bicep 파일과 동일)
- C. Azure CLI 컨텍스트에 구성된 기본 리소스 그룹에
- D. 모듈에 명시적 범위가 필요하므로 배포가 실패합니다

**정답: B**
**해설:** 모듈에 명시적 `scope` 속성이 없으면 상위 Bicep 파일의 범위를 상속합니다. 상위 파일이 구독을 대상으로 하면 모듈도 구독을 대상으로 합니다. 이는 스토리지 계정 및 VM과 같이 리소스 그룹 범위가 필요한 리소스는 명시적 `scope: resourceGroup(...)` 재정의 없이는 해당 모듈에서 직접 배포할 수 없음을 의미합니다.

### 문제 48
구독 수준에 배포되는 Bicep 파일을 작성하고 있습니다. 동일한 Bicep 배포에서 리소스 그룹과 그 안에 스토리지 계정을 배포하려고 합니다. 올바른 접근 방식은 무엇입니까?

- A. 범위 변경 없이 기본 Bicep 파일에 리소스 그룹과 스토리지 계정을 모두 배포한다
- B. 기본 파일에서 구독 범위로 리소스 그룹을 배포한 다음 스토리지 계정에 `scope: resourceGroup('rgName')`이 있는 모듈을 사용한다
- C. `scope: subscription()`을 사용하여 단일 모듈에 두 리소스를 모두 배포한다
- D. 두 개의 별도 배포를 사용한다 — 리소스 그룹과 스토리지 계정은 동일한 Bicep 파일에 있을 수 없다

**정답: B**
**해설:** 구독 범위 Bicep 파일에서 리소스 그룹 리소스는 직접 배포할 수 있습니다(구독 수준 리소스이므로). 스토리지 계정은 리소스 그룹 수준 리소스이며 구독 범위에서 배포할 수 없습니다. 올바른 패턴은 구독 범위에서 리소스 그룹을 배포한 다음 `scope: resourceGroup('rgName')`이 있는 Bicep 모듈을 사용하여 해당 리소스 그룹에 스토리지 계정을 배포하는 것입니다.

### 문제 49
Bicep을 사용하여 관리 그룹 수준 Azure Policy 정의를 배포해야 합니다. Bicep 파일 상단에 선언해야 하는 올바른 `targetScope`는 무엇입니까?

- A. `targetScope = 'subscription'`
- B. `targetScope = 'resourceGroup'`
- C. `targetScope = 'tenant'`
- D. `targetScope = 'managementGroup'`

**정답: D**
**해설:** 관리 그룹 범위에서 리소스를 배포하려면(관리 그룹 수준 정책 정의 또는 역할 할당 등) Bicep 파일에서 `targetScope = 'managementGroup'`을 선언해야 합니다. 구독 범위는 단일 구독을 대상으로 합니다. 리소스 그룹 범위는 기본값이며 리소스 그룹을 대상으로 합니다. 테넌트 범위는 관리 그룹 만들기와 같은 루트 수준 배포에 사용됩니다.

### 문제 50
`storage.bicep`이라는 Bicep 모듈 파일이 스토리지 계정을 배포합니다. 기본 Bicep 파일은 구독 범위를 대상으로 합니다. `scope: resourceGroup('RG1')`으로 모듈을 참조합니다. 배포 후 상위 Bicep 파일에서 모듈의 스토리지 계정 기본 엔드포인트 출력을 어떻게 참조합니까?

- A. `storageModule.outputs.primaryEndpoint`
- B. `reference(storageModule).primaryEndpoint`
- C. `listKeys(storageModule, '2021-01-01').primaryEndpoint`
- D. `storageModule.primaryEndpoint`

**정답: A**
**해설:** Bicep에서 모듈의 출력은 `<모듈이름>.outputs.<출력이름>`을 통해 접근합니다. 모듈 `storageModule`이 `primaryEndpoint`라는 출력을 정의하면 상위 파일은 `storageModule.outputs.primaryEndpoint`로 접근합니다. `reference()` 함수는 Bicep이 아닌 ARM 템플릿에 사용됩니다. `listKeys()`는 엔드포인트가 아닌 접근 키를 검색합니다.
