# AZ-104 예상 문제 (세트 14) — 한국어

---

## 1) Site Recovery, 백업, 컴퓨팅

### 문제 1
3계층 애플리케이션에는 SQL Server VM, 애플리케이션 서버 VM, 웹 서버 VM이 있습니다. Azure Site Recovery 복구 계획을 만들었습니다. 장애 조치(failover) 시 SQL Server가 먼저 시작되고, 그 다음 애플리케이션 서버, 마지막으로 웹 서버가 시작되도록 해야 합니다. 시작 그룹을 어떻게 구성해야 합니까?

- A. 세 VM 모두 Group 1에 배치하고 순서 목록을 정의한다
- B. SQL Server를 Group 1에, 애플리케이션 서버를 Group 2에, 웹 서버를 Group 3에 배치한다 (그룹당 VM 1개)
- C. SQL Server와 애플리케이션 서버를 Group 1에, 웹 서버를 Group 2에 배치한다
- D. 세 VM 모두 Group 2에 배치하고 각 VM 사이에 시작 지연을 설정한다

**정답: B**

**해설:** Site Recovery 복구 계획에서 시작 그룹은 순서대로 실행됩니다 — Group 1이 먼저, 그 다음 Group 2, 마지막으로 Group 3. 각 계층을 별도의 그룹에 배치하면(SQL → Group 1, App → Group 2, Web → Group 3) 장애 조치 시 DB → App → Web 의존성 순서가 보장됩니다.

---

### 문제 2
Azure VM 백업 정책을 구성할 때 인스턴트 복원(Instant Restore) 스냅샷 보존 기간의 유효 범위는 무엇입니까?

- A. 1~30일
- B. 7~14일
- C. 1~5일
- D. 1~7일

**정답: C**

**해설:** 인스턴트 복원 스냅샷 보존 기간은 1~5일로 구성할 수 있습니다. 이 스냅샷은 VM과 가까운 곳에 로컬로 저장되어 빠른 복원을 지원합니다. 보존 기간이 지나면 Recovery Services vault의 데이터만 참조됩니다.

---

### 문제 3
East US 지역에 Azure 파일 공유가 있습니다. 파일 공유에 Azure Backup을 구성해야 합니다. Recovery Services vault은 어느 지역에 만들어야 합니까?

- A. West US — 지리적 재해 복구 분리를 위해
- B. 어느 Azure 지역이든 가능 — Recovery Services vault은 전역 리소스
- C. East US — 보호 대상 리소스와 동일한 지역
- D. 온-프레미스 데이터 센터와 가장 가까운 Azure 지역

**정답: C**

**해설:** Azure Backup은 Recovery Services vault이 보호 대상 리소스와 동일한 지역에 있어야 합니다. 백업 스냅샷은 지역에 종속되어 있으며, 다른 지역의 vault은 파일 공유를 보호할 수 없습니다.

---

### 문제 4
AKS 클러스터에 Spot 노드 풀을 구성하고 제거 정책을 **Deallocate**로 설정했습니다. Azure 수요가 높은 시간대에 Spot 노드가 제거되었습니다. 클러스터 자동 크기 조정기가 새 노드를 추가하려 하지만 계속 실패합니다. 가장 가능성 높은 원인은 무엇입니까?

- A. Deallocate 제거 정책이 자동 크기 조정기의 노드 교체를 방지한다
- B. 해당 지역에서 Spot VM을 일시적으로 사용할 수 없다
- C. 제거된 Spot VM이 Stopped (deallocated) 상태로 유지되면서 컴퓨팅 할당량을 계속 사용하여 새 노드 프로비저닝이 실패한다
- D. 시스템 노드 풀이 구독의 모든 사용 가능한 할당량을 사용 중이다

**정답: C**

**해설:** Deallocate 제거 정책을 사용하면 제거된 Spot VM은 삭제되지 않고 Stopped (deallocated) 상태가 됩니다. 이 VM들은 구독의 vCPU 할당량을 계속 점유합니다. 자동 크기 조정기가 새 노드를 만들려 할 때 할당량이 이미 소진되어 프로비저닝이 실패합니다. Delete 제거 정책을 사용하면 VM이 즉시 삭제되어 할당량이 반환됩니다.

---

### 문제 5
스토리지 계정에 500GB의 Blob 데이터가 보관(Archive) 액세스 계층에 있습니다. 데이터가 Archive로 이동된 지 45일이 되었습니다. 최소 비용으로 즉시 읽기 접근이 가능하도록 해야 합니다. 어떤 리하이드레이션 방법을 사용해야 합니까?

- A. 표준 우선순위로 Cool 계층 스토리지 계정에 Blob 복사
- B. 표준 우선순위로 Blob 계층을 Cool로 설정
- C. 표준 우선순위로 Blob 계층을 Hot으로 설정
- D. 높은 우선순위(High Priority)로 Blob 계층을 Hot으로 설정

**정답: D**

**해설:** 즉시 읽기 접근을 위해서는 높은 우선순위(High Priority)로 Hot 계층에 리하이드레이션해야 합니다. 높은 우선순위는 10GB 미만의 객체를 1시간 이내에 완료합니다(표준 우선순위는 최대 15시간 소요). Hot 계층은 리하이드레이션 완료 후 즉시 읽기 접근을 제공합니다. 45일 경과로 조기 삭제 요금이 발생하지만, 즉시 접근 요구사항에는 높은 우선순위가 적합합니다.

---

### 문제 6
DTU 구매 모델을 사용하는 Basic 서비스 계층의 Azure SQL Database가 있습니다. 지정 시간 복원(PITR) 백업 보존 기간의 최대값은 며칠입니까?

- A. 7일
- B. 14일
- C. 90일
- D. 35일

**정답: D**

**해설:** Azure SQL Database PITR 백업 보존 기간은 모든 DTU 기반 서비스 계층(Basic, Standard, Premium)에서 1~35일로 구성할 수 있습니다. 90일 또는 180일은 장기 보존(LTR) 정책 또는 Azure SQL Managed Instance에 해당하며, DTU 계층의 표준 PITR에는 적용되지 않습니다.

---

### 문제 7
Azure 파일 공유에 온-프레미스 AD DS 인증을 활성화해야 합니다. **Join-AzStorageAccount** PowerShell cmdlet을 실행합니다. 이 cmdlet이 AD DS에 기본적으로 만드는 개체 유형은 무엇입니까?

- A. 사용자 계정
- B. 그룹 관리 서비스 계정(gMSA)
- C. 서비스 로그온 계정
- D. 컴퓨터 계정

**정답: D**

**해설:** 기본적으로 `Join-AzStorageAccount`는 스토리지 계정을 나타내는 **컴퓨터 계정**을 AD DS에 만듭니다. 스토리지 계정은 이 컴퓨터 개체의 ID로 인증하여 도메인에 가입된 머신에서 Kerberos 기반 SMB 인증을 사용할 수 있습니다. `-DomainAccountType ServiceLogonAccount` 매개변수를 지정하면 서비스 로그온 계정으로 만들 수도 있습니다.

---

### 문제 8
Azure VM이 Azure Portal에서 **Stopped (deallocated)** 상태로 표시됩니다. 이 상태를 올바르게 설명하는 것은 무엇입니까?

- A. VM의 OS는 종료되었지만 Azure가 여전히 컴퓨팅 호스트를 예약하고 컴퓨팅 비용을 청구한다
- B. VM과 연결된 디스크가 구독에서 삭제된다
- C. Azure 컴퓨팅 호스트 리소스가 해제되어 컴퓨팅 요금이 중단되지만, VM 개체와 연결된 디스크는 계속 존재한다
- D. VM은 컴퓨팅 비용이 청구되지만 스토리지 비용은 청구되지 않는다

**정답: C**

**해설:** Stopped (deallocated)는 Azure가 기저 컴퓨팅 호스트를 해제했음을 의미합니다. 이 상태에서 컴퓨팅 요금은 중단됩니다. 그러나 VM 정의와 연결된 관리 디스크는 Azure에 계속 존재하며 디스크 스토리지 요금은 계속 발생합니다. OS 종료만 된 Stopped 상태와 다르며, Stopped 상태에서는 Azure가 여전히 컴퓨팅 호스트를 예약하고 요금을 청구합니다.

---

## 2) ID 및 RBAC

### 문제 9
Microsoft Entra 테넌트에 SSPR과 MFA가 모두 활성화되어 있습니다. 사용자들은 각 기능에 대해 인증 방법을 별도로 등록해야 한다고 보고합니다. 사용자가 한 번의 세션에서 SSPR과 MFA 모두에 대한 인증 방법을 등록할 수 있도록 해야 합니다. 무엇을 구성해야 합니까?

- A. Microsoft Entra Connect에 비밀번호 쓰기 저장(Password Writeback) 활성화
- B. MFA 등록을 요구하는 조건부 액세스 정책 구성
- C. Microsoft Entra ID에서 통합 보안 정보 등록(combined security information registration) 활성화
- D. 두 등록 포털에 적용되는 명명된 위치(Named Location) 생성

**정답: C**

**해설:** Microsoft Entra 통합 보안 정보 등록은 사용자가 인증 방법을 한 번만 등록하면 SSPR과 MFA 모두에 적용되는 통합 환경을 제공합니다. 이 기능 없이는 사용자가 각 기능을 위해 별도의 등록 포털로 이동해야 합니다.

---

### 문제 10
테넌트에서 Microsoft Entra ID Free를 사용하고 있습니다. 관리자가 직책(job title)이 "Engineer"인 사용자를 자동으로 포함하는 동적 사용자 그룹을 만들려 합니다. 동적 멤버십 규칙 옵션을 사용할 수 없습니다. 먼저 무엇을 해야 합니까?

- A. Microsoft Entra ID B2B 협업 활성화
- B. 관리자에게 Groups Administrator 역할 할당
- C. 먼저 정적 그룹으로 만든 다음 동적으로 전환
- D. Microsoft Entra ID Premium P1 또는 P2로 업그레이드

**정답: D**

**해설:** 특성 규칙 기반으로 멤버를 자동으로 추가하거나 제거하는 동적 멤버십 그룹은 Microsoft Entra ID Premium P1 또는 P2가 필요합니다. Free 계층은 정적(수동 할당) 그룹 멤버십만 지원합니다. 필요한 라이선스를 프로비저닝하면 동적 규칙 편집기를 사용할 수 있게 됩니다.

---

### 문제 11
테넌트의 모든 외부 게스트 사용자를 자동으로 포함하는 동적 사용자 그룹을 Microsoft Entra ID에서 만들어야 합니다. 어떤 멤버십 규칙을 사용해야 합니까?

- A. `(user.companyName -eq "Guest")`
- B. `(user.mail -contains "@guest")`
- C. `(user.objectType -eq "Guest")`
- D. `(user.userType -eq "Guest")`

**정답: D**

**해설:** Entra ID의 `userType` 특성은 내부 사용자에게는 `Member`, 외부 B2B 게스트 사용자에게는 `Guest`로 설정됩니다. `(user.userType -eq "Guest")` 규칙은 도메인이나 회사 이름에 관계없이 테넌트의 모든 게스트 계정을 올바르게 식별합니다. `objectType`은 동적 그룹에서 유효한 특성이 아닙니다.

---

### 문제 12
새 관리자가 관리 센터를 사용하여 Microsoft Entra ID에서 사용자 계정을 만들고 관리해야 합니다. 최소 권한 원칙을 따라야 합니다. 어떤 Microsoft Entra 역할을 할당해야 합니까?

- A. 전역 관리자(Global Administrator)
- B. 청구 관리자(Billing Administrator)
- C. 암호 관리자(Password Administrator)
- D. 사용자 관리자(User Administrator)

**정답: D**

**해설:** 사용자 관리자(User Administrator)는 사용자 만들기, 사용자 속성 관리, 암호 재설정, 비권한 그룹 관리에 필요한 최소 권한을 제공합니다. 암호 관리자는 암호만 재설정할 수 있으며 사용자를 만들 수 없습니다. 전역 관리자는 요구사항보다 훨씬 더 많은 권한을 가지며 최소 권한 원칙에 위배됩니다.

---

### 문제 13
Azure 구독에 15개의 리소스 그룹이 있습니다. User1에게 구독 범위에서 **Contributor** 역할이 할당되고, 리소스 그룹 RG1에 **Reader** 역할이 할당되어 있습니다. RG1에서 User1의 유효 권한은 무엇입니까?

- A. Reader — 더 좁은 RG1 범위가 구독 수준 할당을 재정의한다
- B. Owner — Contributor와 Reader 권한을 결합하면 Owner로 에스컬레이션된다
- C. Reader — 명시적 리소스 그룹 수준 할당이 항상 상속된 역할보다 우선한다
- D. Contributor — Azure RBAC는 가산적 모델이며, 구독 수준 Contributor가 RG1까지 상속된다

**정답: D**

**해설:** Azure RBAC는 가산적(합집합) 모델을 사용합니다. 유효 권한은 모든 적용 가능한 범위의 모든 역할 할당의 합집합입니다. 구독 범위의 Contributor는 RG1을 포함한 모든 하위 리소스 그룹에 상속됩니다. RG1의 Reader 할당은 Contributor가 이미 모든 Reader 권한을 포함하므로 새로운 권한을 추가하지 않습니다. 유효 역할은 Contributor입니다.

---

### 문제 14
User1에게 스토리지 계정에 두 역할이 할당되어 있습니다. **Role1**은 모든 Blob DataAction을 허용하지만 Blob 삭제를 **NotDataActions**에 나열합니다. **Role2**는 Blob 삭제를 DataActions에 명시적으로 허용합니다. Blob 삭제 작업에 대한 User1의 유효 권한은 무엇입니까?

- A. Blob 삭제가 거부됨 — NotDataActions는 항상 모든 역할 할당에서 DataActions를 재정의한다
- B. Blob 삭제가 거부됨 — Role1의 NotDataActions가 전역적으로 우선하여 재정의할 수 없다
- C. 결과가 정의되지 않음 — 역할이 동일한 데이터 작업에서 충돌할 때
- D. Blob 삭제가 허용됨 — Azure RBAC는 가산적 합집합 모델이며, Role2의 DataActions가 독립적으로 Blob 삭제를 허용한다

**정답: D**

**해설:** `NotDataActions`는 동일한 역할 정의 내에서만 권한을 차감합니다 — 다른 역할 할당을 차단하는 거부 규칙을 만들지 않습니다. Azure RBAC는 가산적 합집합 모델을 사용합니다: 유효 권한 = 할당된 모든 역할의 DataActions 합집합 - 동일한 역할 내의 NotDataActions. Role2가 독립적으로 DataActions에서 Blob 삭제를 허용하므로 User1은 Blob을 삭제할 수 있습니다.

---

### 문제 15
User1에게 구독 범위에서 **Reader** 역할이 있습니다. User1이 리소스 그룹 RG2에서만 가상 머신을 만들고 관리할 수 있어야 합니다. 최소 권한 원칙을 사용해야 합니다. 무엇을 해야 합니까?

- A. 구독 범위에서 User1에게 Contributor 역할 할당
- B. RG2 범위에서 User1에게 Owner 역할 할당
- C. RG2 범위에서 User1에게 Contributor 역할 할당
- D. RG2 범위에서 User1에게 Virtual Machine Contributor 역할 할당

**정답: D**

**해설:** Virtual Machine Contributor는 VM 특정 생성 및 관리 권한을 제공하며 RG2에만 범위가 지정됩니다. RG2의 Contributor는 RG2의 모든 리소스 유형(스토리지, 네트워킹 등)을 수정할 수 있어 최소 권한 원칙에 위배됩니다. 구독 수준 Contributor는 더욱 과도한 권한입니다.

---

### 문제 16
신입 관리자가 Microsoft Entra ID Security Group의 주요 목적에 대해 질문합니다. 가장 정확한 설명은 무엇입니까?

- A. Security Group은 여러 사용자에게 동시에 이메일을 보내기 위한 메일링 리스트다
- B. Security Group은 Azure 리소스에 규정 준수 규칙을 적용하는 정책 개체다
- C. Security Group은 RBAC 역할과 액세스 권한을 여러 사용자에게 일괄 할당하기 위한 단위다
- D. Security Group은 NSG와 유사하게 VM 네트워크 트래픽 필터링을 제어한다

**정답: C**

**해설:** Microsoft Entra ID Security Group의 주요 목적은 액세스 관리입니다 — 사용자(및 기타 개체)를 그룹으로 묶어 RBAC 역할, 애플리케이션 할당, 라이선싱을 개별 사용자가 아닌 그룹 단위로 적용할 수 있게 합니다. 일부 그룹은 이메일을 활성화할 수 있지만(Mail-enabled Security Group), 이것이 주요 목적은 아닙니다.

---

## 3) 거버넌스, 정책, 라우팅

### 문제 17
구독에 Deny 효과가 있는 정책을 할당해야 합니다. 완전히 적용하기 전에 테스트 기간 동안 새 배포를 차단하지 않으면서 기존 리소스의 규정 준수를 평가하고 싶습니다. 정책 할당에서 무엇을 구성해야 합니까?

- A. 정책 효과를 Deny에서 Audit로 임시 변경
- B. 테스트가 완료될 때까지 정책 할당 전체 제거
- C. 할당 범위를 모든 리소스 그룹 제외로 설정
- D. 적용 모드를 DoNotEnforce로 설정

**정답: D**

**해설:** 적용 모드를 `DoNotEnforce`로 설정하면 정책 효과(차단)가 비활성화되면서도 리소스를 평가하고 규정 준수 결과를 보고합니다. 이를 통해 사용자를 차단하지 않고 기존 리소스에 대한 영향을 평가할 수 있습니다. 효과를 Disabled로 설정하면 평가 자체가 중단되어 규정 준수 데이터를 전혀 볼 수 없습니다.

---

### 문제 18
조직의 태깅 및 크기 표준을 준수하지 않는 가상 머신 배포를 즉시 차단하고 오류를 반환해야 합니다. 이를 달성하는 Azure Policy 효과는 무엇입니까?

- A. audit
- B. append
- C. auditIfNotExists
- D. deny

**정답: D**

**해설:** `deny` 효과는 정책 조건이 충족되지 않을 때 리소스 생성 또는 업데이트 요청을 차단하고 요청자에게 오류를 반환합니다. `audit`와 `auditIfNotExists`는 비규정 준수만 기록하며 작업을 차단하지 않습니다. `append`는 필수 필드를 추가하지만 배포를 차단하지 않습니다.

---

### 문제 19
조직에서 Azure 리소스가 승인된 지역(West Europe 및 North Europe)에만 배포될 수 있도록 해야 합니다. 이를 적용하는 데 어떤 Azure 기능을 사용해야 합니까?

- A. Azure RBAC — 사용자가 선택할 수 있는 위치를 제한하는 사용자 지정 역할 할당
- B. Azure Firewall — 승인되지 않은 지역으로의 트래픽을 차단하는 네트워크 규칙 생성
- C. 관리 그룹 잠금 — 승인되지 않은 지역의 리소스 생성을 방지하는 ReadOnly 잠금 적용
- D. Azure Policy — 허용된 위치를 제한하는 Deny 효과 정책 할당

**정답: D**

**해설:** Azure Policy는 *허용되는 것*을 제어합니다 — 리소스 위치 제약을 포함하여. 기본 제공 "허용된 위치" 정책 이니셔티브는 Deny 효과를 사용하여 승인된 지역 외부의 배포를 차단합니다. Azure RBAC는 *누가 무엇을 할 수 있는지*(권한/ID)를 제어하며, 배포 지역을 제한할 수 없습니다. 잠금은 변경을 방지하지만 위치 규칙을 적용할 수 없습니다.

---

### 문제 20
VNet1에는 여러 VM이 있는 서브넷(Subnet2)이 있습니다. Subnet2의 트래픽은 BGP를 통해 광고된 경로를 사용하여 ExpressRoute를 통해 온-프레미스 네트워크로 라우팅되어야 합니다. BGP 광고 경로와 동일한 주소 접두사를 가진 사용자 정의 경로(UDR)를 만들어 네트워크 가상 어플라이언스(NVA)를 가리킵니다. 어떤 경로가 우선합니까?

- A. BGP 경로 — 온-프레미스에서 동적으로 학습되었으므로 우선한다
- B. 시스템 경로 — 먼저 만들어졌으므로 우선한다
- C. UDR과 BGP 경로가 ECMP 로드 밸런싱으로 결합된다
- D. 사용자 정의 경로(UDR)가 BGP 경로보다 우선한다

**정답: D**

**해설:** 동일한 주소 접두사를 가진 여러 경로가 있을 때 Azure는 다음 우선순위에 따라 선택합니다: 사용자 정의 경로 > BGP 경로 > 시스템 경로. UDR은 주소 접두사가 일치할 때 항상 BGP 경로를 재정의하여, 온-프레미스에서 동적 경로가 광고되더라도 관리자가 트래픽 흐름을 제어할 수 있습니다.

---

### 문제 21
Azure 구독에 예산이 구성되어 있습니다. 예산이 임계값의 80%에 도달할 때마다 Contributor 역할을 가진 모든 사용자에게 자동으로 알림을 보내야 합니다. 개별 이메일 주소 구성이 필요 없어야 합니다. 예산 경고에서 무엇을 구성해야 합니까?

- A. 메일 배포 목록으로 이메일을 보내는 Action Group
- B. RBAC 할당을 쿼리하고 알림을 보내는 Logic App
- C. 경고 수신자로 Contributor 역할을 지정하는 Contact Roles
- D. Contributor 역할을 대상으로 하는 Azure Monitor의 경고 규칙

**정답: C**

**해설:** Azure Cost Management 예산 경고는 구독에서 지정된 RBAC 역할(예: Contributor)을 가진 모든 사용자를 자동으로 대상으로 하는 Contact Roles를 지원합니다. 이를 통해 개별 이메일 주소를 관리할 필요가 없습니다. Action Group은 알림을 보낼 수 있지만 RBAC 역할을 기반으로 동적으로 사용자를 대상으로 할 수 없습니다.

---

### 문제 22
상위 방화벽 정책(ParentPolicy)의 위협 인텔리전스 모드가 **Alert**로 설정되어 있습니다. ParentPolicy에서 상속하는 하위 방화벽 정책(ChildPolicy)을 만듭니다. ChildPolicy에서 유효한 위협 인텔리전스 모드 값은 무엇입니까?

- A. Off, Alert, 또는 Alert and deny — 세 가지 모두 하위 정책에서 사용 가능
- B. Off만 가능 — 하위 정책은 상속된 설정을 비활성화해야 함
- C. Alert and deny만 가능
- D. Alert만 가능

**정답: C**

**해설:** 하위 정책이 상위 정책에서 상속할 때 위협 인텔리전스 모드는 상위와 동일하거나 더 엄격한 값으로 구성해야 합니다. 상위가 Alert로 설정되어 있으므로 하위는 Alert(동일) 또는 Alert and deny(더 엄격)로 설정할 수 있습니다. Off(Alert보다 덜 제한적)로 설정하는 것은 하위 정책에서 허용되지 않습니다.

---

### 문제 23
Azure Application Gateway v2에 경로 기반 라우팅을 구성했습니다. URL 경로 규칙이 다음 순서로 나열되어 있습니다: `/*`, `/images/*`, `/api/*`. `/api/v1/users`에 대한 요청이 API 백엔드 풀 대신 기본 백엔드 풀로 라우팅되는 것을 발견했습니다. 이 문제를 해결하려면 어떻게 해야 합니까?

- A. Application Gateway v2 Premium SKU로 업그레이드하여 우선순위 기반 라우팅 활성화
- B. 경로 기반 규칙을 헤더 기반 라우팅 규칙으로 변환
- C. 더 구체적인 경로가 먼저 평가되도록 `/*`를 경로 규칙 목록의 맨 아래로 이동
- D. `/api/*`에 낮은 숫자 우선순위 값을 할당하여 먼저 처리되도록 함

**정답: C**

**해설:** Azure Application Gateway는 경로 규칙을 위에서 아래로 목록 순서대로 평가합니다(첫 번째 일치가 이김). 목록 상단의 와일드카드 `/*`가 `/api/v1/users`를 포함한 모든 요청과 먼저 일치합니다. `/*`를 목록 맨 아래로 이동하면 구체적인 경로(`/api/*`, `/images/*`)가 먼저 평가됩니다. NSG 규칙과 달리 Application Gateway 경로 규칙은 숫자 우선순위가 없으며, 순서는 목록 위치로 결정됩니다.

---

### 문제 24
Azure Firewall에 세 개의 Rule Collection Group이 있는 방화벽 정책이 있습니다: RCG1(우선순위 200), RCG2(우선순위 150), RCG3(우선순위 300). 각 그룹에는 네트워크 규칙과 애플리케이션 규칙이 모두 포함되어 있습니다. RCG2의 네트워크 규칙과 일치하는 트래픽이 도착합니다. 방화벽이 모든 Rule Collection Group에서 규칙 유형을 처리하는 전체 순서는 무엇입니까?

- A. RCG2(가장 낮은 우선순위)의 네트워크 규칙 → RCG2의 애플리케이션 규칙 → 다른 모든 그룹
- B. 모든 DNAT 규칙 → 모든 네트워크 규칙 → 모든 애플리케이션 규칙 (모든 그룹에 걸쳐)
- C. RCG2의 모든 규칙(가장 낮은 우선순위 번호) → RCG1의 모든 규칙 → RCG3의 모든 규칙
- D. 애플리케이션 규칙 → 네트워크 규칙 → DNAT 규칙

**정답: B**

**해설:** Azure Firewall은 규칙 유형별로 먼저 처리한 다음 각 유형 내에서 RCG 우선순위에 따라 처리합니다. 처리 순서는 DNAT 규칙(모든 RCG) → 네트워크 규칙(모든 RCG, RCG 우선순위 순) → 애플리케이션 규칙(모든 RCG, RCG 우선순위 순)입니다. 방화벽은 다음 RCG로 넘어가기 전에 하나의 RCG의 모든 규칙을 처리하지 않습니다.

---

## 4) 네트워킹

### 문제 25
세 개의 VM이 있는 백엔드 풀을 가진 Azure Standard Load Balancer(LB1)가 있습니다. 상태 프로브는 포트 80의 HTTP로 구성되어 있습니다. 웹 애플리케이션이 올바르게 실행되고 개인 IP를 통해 직접 접근할 수 있음에도 불구하고 모든 백엔드 VM이 비정상으로 표시됩니다. 이 문제를 해결하기 위해 백엔드 VM의 NSG 규칙에서 허용해야 하는 IP 주소는 무엇입니까?

- A. LB1의 공용 IP 주소
- B. 다른 모든 백엔드 VM의 개인 IP 주소
- C. Azure Virtual Network 게이트웨이 IP 주소
- D. 168.63.129.16

**정답: D**

**해설:** Azure Load Balancer 상태 프로브는 Azure 플랫폼의 고정 특수 IP 주소인 168.63.129.16에서 발생합니다. 백엔드 VM의 NSG가 이 IP에서의 인바운드 트래픽을 프로브 포트(80)에서 허용하지 않으면 프로브 요청이 삭제되고 VM이 비정상으로 표시됩니다. 168.63.129.16에서 TCP 포트 80을 허용하는 인바운드 NSG 규칙을 추가하면 문제가 해결됩니다.

---

### 문제 26
Subnet2에 사용자 정의 경로(UDR)를 구성하고 다음 홉 유형을 Virtual appliance로, Subnet1의 VM1 개인 IP를 가리키도록 설정했습니다. VM1은 네트워크 가상 어플라이언스(NVA)로 사용될 예정입니다. UDR을 적용한 후에도 Subnet2의 트래픽이 VM1을 통해 흐르지 않습니다. 이 문제를 해결하기 위해 무엇을 구성해야 합니까?

- A. Subnet1과 Subnet2 사이의 피어링 연결
- B. VM1의 네트워크 인터페이스에서 IP 전달(IP forwarding) 활성화
- C. Subnet1에도 경로 테이블 적용
- D. Subnet2에 모든 아웃바운드 트래픽을 허용하는 새 NSG 규칙

**정답: B**

**해설:** 기본적으로 Azure VM은 자신의 IP 주소를 대상으로 하지 않는 네트워크 패킷을 삭제합니다. VM1이 NVA로서 서브넷 간 트래픽을 전달하려면 Azure에서 VM1의 네트워크 인터페이스(NIC)에서 IP 전달을 활성화해야 합니다. IP 전달 없이는 Subnet2에서 VM1에 도착한 패킷이 전달되지 않고 삭제됩니다.

---

### 문제 27
조직에서 특정 가상 네트워크 내의 트래픽으로 Azure SQL Database 접근을 제한해야 하며, 공용 인터넷을 통한 트래픽은 허용하지 않아야 합니다. VNet의 개인 IP를 Azure SQL Database에 할당하여 인터넷 노출 없이 연결하는 Azure 기능은 무엇입니까?

- A. 서비스 엔드포인트 — VNet으로 접근을 제한하고 공용 접근을 방지
- B. Private Endpoint가 있는 Azure Private Link — VNet에서 SQL Database에 개인 IP 할당
- C. Virtual Network Gateway — VPN을 통해 모든 SQL 트래픽 라우팅
- D. Azure Firewall — SQL Database에 대한 모든 공용 접근 차단

**정답: B**

**해설:** Private Endpoint가 있는 Azure Private Link는 VNet의 개인 IP 주소를 Azure SQL Database에 매핑하는 네트워크 인터페이스를 만듭니다. 트래픽은 공용 인터넷 노출 없이 Azure 백본 내에서 완전히 흐릅니다. 서비스 엔드포인트는 서비스의 공용 IP를 유지하면서 VNet으로 접근을 제한하며, Private Endpoint는 해당 연결에 대한 공용 엔드포인트를 완전히 제거합니다.

---

### 문제 28
새로 배포된 가상 머신에서 Azure Storage 계정에 접근을 시도합니다. 스토리지 계정을 만드는 것 외에 추가 구성은 없었습니다. 기본 네트워크 접근 동작을 올바르게 설명하는 것은 무엇입니까?

- A. Azure Storage 계정은 기본적으로 프라이빗이며 Private Endpoint 구성이 필요하다
- B. Azure Storage 계정은 VM이 접근하기 전에 서비스 엔드포인트가 필요하다
- C. Azure Storage 계정은 기본적으로 공개적으로 접근 가능 — 모든 네트워크가 공용 엔드포인트에 접근할 수 있다
- D. Azure Storage 계정은 방화벽을 통해 명시적으로 열기 전까지 모든 접근을 차단한다

**정답: C**

**해설:** Storage 계정을 포함한 Azure PaaS 서비스는 기본적으로 공개적으로 접근 가능합니다. 스토리지 계정의 공용 엔드포인트(`storageaccount.blob.core.windows.net`)는 스토리지 방화벽 규칙, 서비스 엔드포인트 또는 Private Endpoint를 사용하여 명시적으로 제한하지 않는 한 모든 네트워크에 열려 있습니다. 조직에서는 접근을 제한하기 위해 네트워크 제한을 능동적으로 구성해야 합니다.

---

### 문제 29
스토리지 계정의 방화벽 구성(Firewalls and virtual networks → Selected networks)에 VNet1/Subnet1을 추가했습니다. Subnet1의 VM1이 여전히 스토리지 계정에 접근할 수 없습니다. 가장 가능성 높은 원인은 무엇입니까?

- A. 스토리지 계정 방화벽은 가상 네트워크 규칙이 아닌 Private Endpoint가 필요하다
- B. VM에 스토리지 방화벽 가상 네트워크 규칙을 사용하려면 공용 IP 주소가 있어야 한다
- C. 스토리지 계정 중복성 설정(LRS 대 GRS)이 네트워크 접근에 영향을 미친다
- D. Subnet1에서 Microsoft.Storage 서비스 엔드포인트가 활성화되어 있지 않다

**정답: D**

**해설:** 스토리지 계정 방화벽에 VNet/서브넷을 추가하는 것은 해당 서브넷에서 **Microsoft.Storage** 서비스 엔드포인트도 활성화된 경우에만 효과적입니다. 서비스 엔드포인트는 서브넷에서 Azure Storage로 가는 안전하고 최적화된 경로를 설정합니다. Subnet1에서 서비스 엔드포인트가 활성화되지 않으면 스토리지 방화벽이 소스를 허용된 서브넷으로 인식하지 못하고 접근이 거부됩니다.

---

### 문제 30
contoso.com에 대한 Azure DNS 공용 영역을 관리합니다. 세 개의 웹 서버 VM에 개인 IP 주소 10.1.1.10, 10.1.1.11, 10.1.1.12가 있습니다. `www.contoso.com`에 대한 DNS 쿼리가 세 IP 주소를 모두 반환하도록 해야 합니다. Azure DNS에서 무엇을 구성해야 합니까?

- A. 각각 다른 IP와 다른 이름(www1, www2, www3)을 가진 세 개의 별도 A 레코드
- B. Azure Traffic Manager 프로필을 가리키는 하나의 CNAME 레코드
- C. 세 IP 주소(10.1.1.10, 10.1.1.11, 10.1.1.12)를 모두 포함하는 `www`에 대한 하나의 A 레코드 집합
- D. IPv4 주소에 매핑된 세 개의 IPv6 주소가 있는 AAAA 레코드 집합

**정답: C**

**해설:** Azure DNS에서 A 레코드 집합은 동일한 이름 아래 여러 IP 값을 포함할 수 있습니다. `www.contoso.com`에 대한 쿼리는 세 IP 주소를 모두 응답으로 반환합니다. DNS 클라이언트는 일반적으로 순서대로 또는 임의로 시도하여 기본적인 라운드 로빈 분산을 제공합니다. 이는 부하 분산 장치 없이 다중 IP DNS 응답을 위한 간단한 접근 방식입니다.

---

### 문제 31
contoso.com에 대한 Azure DNS 영역이 있습니다. `www.contoso.com`에서 `backend.contoso.com`을 가리키는 CNAME 레코드가 있습니다. 동료가 `www.contoso.com`에 새 IP 주소를 가리키는 A 레코드를 만들려 합니다. 어떤 일이 발생합니까?

- A. A 레코드가 만들어지고 기존 CNAME보다 우선한다
- B. 두 레코드가 모두 만들어지고 Azure DNS가 응답에서 두 가지를 모두 반환한다
- C. A 레코드에 자리를 만들기 위해 CNAME이 자동으로 제거된다
- D. CNAME 레코드는 동일한 이름에 다른 레코드 유형과 공존할 수 없으므로 A 레코드 생성이 실패한다

**정답: D**

**해설:** DNS RFC와 Azure DNS 모두 CNAME 레코드는 동일한 DNS 이름에 다른 레코드 유형(A, AAAA, MX, TXT 등)과 공존할 수 없다고 규정합니다. 이는 정확히 동일한 이름(`www.contoso.com`)에 적용됩니다. A 레코드 생성은 충돌 오류와 함께 실패합니다.

---

### 문제 32
Azure App Service(WebApp1)에 **staging**이라는 배포 슬롯이 있습니다. 트래픽 라우팅이 프로덕션 트래픽의 20%를 스테이징 슬롯으로 보내도록 구성되어 있습니다. 사용자가 WebApp1에 접속하여 스테이징 슬롯으로 라우팅됩니다. 사용자가 브라우저를 닫고 30분 후에 다시 접속합니다. 어떤 슬롯으로 라우팅됩니까?

- A. 프로덕션 슬롯 — 80/20 라우팅 비율은 새 브라우저 세션마다 재평가된다
- B. 스테이징 슬롯 — App Service가 쿠키를 사용하여 1시간 동안 사용자를 동일한 슬롯에 고정한다
- C. 임의의 슬롯 — Azure가 매번 20% 가중치를 기반으로 임의로 스테이징 또는 프로덕션을 선택한다
- D. 프로덕션 슬롯 — 브라우저를 닫으면 항상 라우팅 할당이 초기화된다

**정답: B**

**해설:** Azure App Service 트래픽 라우팅은 `x-ms-routing-name`이라는 쿠키를 사용하여 사용자를 처음 할당된 슬롯에 고정합니다. 기본적으로 이 고정은 1시간 동안 지속됩니다. 사용자가 30분 후(1시간 만료 전)에 다시 접속하므로 쿠키가 여전히 유효하고 사용자는 스테이징 슬롯으로 다시 라우팅됩니다.

---

## 5) 스토리지, SAS, App Service

### 문제 33
WebApp1에 스테이징 배포 슬롯이 있습니다. 스테이징 슬롯에는 애플리케이션의 새 버전이 있습니다. 스왑을 완료하기 전에 프로덕션 구성 설정으로 애플리케이션이 올바르게 동작하는지 확인해야 합니다. 문제가 발견되면 작업을 취소할 수 있어야 합니다. 무엇을 해야 합니까?

- A. 표준 슬롯 스왑을 수행하고 직후에 로그를 모니터링한다
- B. 배포 슬롯 자동 스왑을 사용하여 스왑 전에 프로덕션 설정을 적용한다
- C. 프로덕션 슬롯을 테스트 슬롯에 복제하고 별도로 테스트한다
- D. 미리 보기로 스왑(Swap with preview, 다단계 스왑)을 사용하여 스테이징에 프로덕션 설정을 적용한 후 검증하고 커밋한다

**정답: D**

**해설:** 미리 보기로 스왑(다단계 스왑)은 2단계 프로세스입니다: 1단계에서는 프로덕션 슬롯 설정이 스테이징 슬롯에 적용되어 프로덕션 구성으로 앱을 검증할 수 있습니다. 2단계에서는 스왑을 커밋(트래픽이 새 버전으로 전환)하거나 취소(스테이징이 원래 설정으로 되돌아감)합니다. 표준 스왑은 사전 검증 단계 없이 즉시 트래픽을 전환합니다.

---

### 문제 34
Azure App Service에 사용자 지정 루트 도메인(contoso.com)을 추가해야 합니다. 도메인 소유권 확인의 일환으로 DNS 레코드를 추가해야 합니다. Azure App Service에서 루트 도메인 소유권 확인에 필요한 DNS 레코드는 무엇입니까?

- A. `<app>.azurewebsites.net`을 가리키는 `verify`라는 이름의 CNAME 레코드
- B. Azure에서 제공하는 도메인 확인 ID를 포함하는 `asuid`라는 이름의 TXT 레코드
- C. App Service 엔드포인트를 가리키는 MX 레코드
- D. `<app>.azurewebsites.net`을 가리키는 `asuid`라는 이름의 CNAME 레코드

**정답: B**

**해설:** Azure App Service는 루트 도메인 소유권 확인을 위해 `asuid`라는 이름의 TXT 레코드를 사용합니다(값은 App Service 도메인 확인 ID로 설정). 루트 도메인(contoso.com과 같은 apex 도메인)은 DNS 표준에 따라 apex 도메인의 CNAME이 SOA/NS 레코드와 충돌하므로 CNAME 레코드를 사용할 수 없습니다. `asuid` TXT 레코드가 필수 확인 방법입니다.

---

### 문제 35
Azure Storage 계정에 여러 Blob 컨테이너가 있습니다. 다양한 애플리케이션을 위해 여러 SAS(공유 액세스 서명)가 만들어졌습니다. SAS 토큰이 만료될 때까지 기다리지 않고 취소할 방법이 필요합니다. 취소를 위한 저장된 액세스 정책(stored access policy)을 지원하는 SAS 유형은 무엇입니까?

- A. 계정 SAS(Account SAS) — 계정 수준에서 저장된 액세스 정책 지원
- B. 사용자 위임 SAS(User delegation SAS) — Entra ID를 통한 저장된 액세스 정책 지원
- C. 서비스 SAS(Service SAS) — 컨테이너 또는 Blob에서 정의된 저장된 액세스 정책 지원
- D. 모든 SAS 유형이 저장된 액세스 정책을 동일하게 지원

**정답: C**

**해설:** 저장된 액세스 정책은 서비스 SAS만 지원합니다. 저장된 액세스 정책은 리소스(컨테이너, 큐, 테이블 또는 파일 공유)에서 정의되고 SAS 토큰이 이를 참조합니다. 취소하려면 저장된 액세스 정책을 수정하거나 삭제하여 연결된 모든 SAS 토큰을 즉시 무효화합니다. 계정 SAS와 사용자 위임 SAS는 저장된 액세스 정책을 지원하지 않으며 만료 전에 취소할 수 없습니다.

---

### 문제 36
스토리지 계정에 Azure 파일 공유가 있습니다. 사용자가 Microsoft Entra에 가입된 디바이스에서 공유에 접근합니다. 환경은 클라우드 전용으로 온-프레미스 도메인 컨트롤러가 없고 AD DS에 대한 연결도 없습니다. Azure 파일 공유에 대한 ID 기반 인증을 활성화해야 합니다. 어떤 ID 소스를 구성해야 합니까?

- A. 온-프레미스 AD DS — 도메인 컨트롤러 연결을 위한 VPN 구성
- B. Microsoft Entra Domain Services — Kerberos용 관리 도메인 배포
- C. 스토리지 계정 액세스 키 — 모든 클라이언트에 키 기반 인증 사용
- D. Microsoft Entra Kerberos — 도메인 컨트롤러 없이 Entra에 가입된 디바이스에서 SMB 접근 활성화

**정답: D**

**해설:** Microsoft Entra Kerberos를 사용하면 Entra에 가입된 디바이스가 온-프레미스 도메인 컨트롤러 없이 Microsoft Entra ID(클라우드 기반)에서 발급한 Kerberos 티켓을 사용하여 Azure Files에 인증할 수 있습니다. Azure AD DS는 관리 도메인 배포와 VM 도메인 가입이 필요합니다. 온-프레미스 AD DS는 도메인 컨트롤러 연결이 필요합니다. 액세스 키는 ID 기반 인증이 아닙니다.

---

### 문제 37
Windows Server에 Azure File Sync 에이전트를 설치합니다. 에이전트의 일부로 설치되는 두 가지 구성 요소는 무엇입니까?

- A. FileSyncSvc.exe — 동기화 세션을 위한 백그라운드 서비스; StorageSync.sys — 클라우드 계층화를 위한 파일 시스템 필터 드라이버
- B. AzureFileSyncAgent.exe — 동기화 엔진; AzBackupAgent.dll — 백업 통합 구성 요소
- C. FileSyncSvc.exe — 동기화 서비스; FileSyncSnapService — Volume Shadow Copy 통합
- D. StorageExplorer.exe — 파일 탐색 도구; StorageSync.sys — 계층화 필터 드라이버

**정답: A**

**해설:** Azure File Sync 에이전트는 두 가지 주요 구성 요소를 설치합니다: **FileSyncSvc.exe** — 서버 엔드포인트의 변경 사항을 모니터링하고 Azure와 동기화 세션을 시작하는 백그라운드 Windows 서비스; **StorageSync.sys** — 클라우드 계층화(로컬에 계층화된 파일 스텁 생성)를 가능하게 하고 온디맨드 파일 회수를 지원하는 파일 시스템 필터 드라이버.

---

### 문제 38
Azure VM(VM1)이 Premium SSD 데이터 디스크(크기 256GiB, 성능 계층 P15)로 실행 중입니다. 디스크 크기를 조정하거나 VM을 중지하지 않고 성능을 P20으로 높여야 합니다. 어떻게 해야 합니까?

- A. 디스크를 분리하고 계층을 변경한 다음 실행 중인 VM에 다시 연결한다
- B. VM1을 중지(할당 해제)하고 디스크 성능 계층을 P20으로 변경한 다음 다시 시작한다
- C. 디스크 크기를 조정하거나 VM을 중지하지 않고 실행 중인 디스크에서 직접 디스크 성능 계층을 P20으로 변경한다
- D. 새 P20 디스크를 만들고 P15 디스크에서 데이터를 복사한 다음 VM1에 연결한다

**정답: C**

**해설:** Azure Premium SSD는 VM을 중지하거나 디스크 크기를 조정하지 않고도 인-플레이스 성능 계층 변경을 지원합니다. 포털 또는 CLI를 통해 VM이 실행 중인 동안 디스크 속성에서 성능 계층(예: P15 → P20)을 업그레이드할 수 있습니다. 계층 변경은 디스크 크기 변경과 독립적이며 가동 중지 시간이 없습니다.

---

### 문제 39
경고가 발생할 때 알림을 보내기 위해 Azure Monitor Action Group을 만듭니다. Action Group의 유효한 **알림(notification)** 유형은 무엇입니까? (3개 선택)

- A. 웹훅(Webhook)
- B. 이메일(Email)
- C. Azure Function
- D. SMS
- E. 푸시 알림(Azure 모바일 앱)
- F. Logic App

**정답: B, D, E**

**해설:** 사람에게 직접 알리는 Action Group 알림 유형은 **이메일**, **SMS**, **푸시 알림**(Azure 모바일 앱)입니다. 웹훅, Azure Function, Logic App은 자동화된 프로세스를 트리거하는 *작업(action)* 유형으로, 사람에게 직접 알리지 않습니다. 사람에게 알리는 것과 응답을 자동화하는 것 사이의 이 구분은 시험에서 중요합니다.

---

### 문제 40
세 개의 Azure 웹 앱 모두 동일한 Azure Storage 계정에 접근해야 합니다. 스토리지 계정에 대한 역할 할당은 웹 앱이 배포되기 전에 구성되어야 합니다. 관리 ID를 구성해야 합니다. 어떤 유형의 관리 ID를 사용해야 합니까?

- A. 시스템 할당 관리 ID — 앱당 하나씩, 앱과 함께 자동으로 만들어짐
- B. 사용자 할당 관리 ID — 앱에 걸쳐 공유하고 사전에 만들 수 있는 독립적인 ID 리소스
- C. 인증서가 있는 서비스 주체 — 배포 전 구성 허용
- D. 관리 ID는 여러 App Service 인스턴스에서 지원되지 않음

**정답: B**

**해설:** 사용자 할당 관리 ID는 웹 앱이 배포되기 전에 만들 수 있는 독립적인 Azure 리소스입니다. 스토리지 계정에서 RBAC 역할 할당을 미리 구성할 수 있으며, 동일한 사용자 할당 ID를 세 웹 앱 모두에 연결하여 별도의 자격 증명을 관리하지 않고 동일한 스토리지 접근 권한을 부여할 수 있습니다. 시스템 할당 ID는 단일 리소스의 수명 주기에 종속되어 사전 생성하거나 공유할 수 없습니다.

---

## 6) ARM 템플릿, Bicep, 컨테이너, 모니터링

### 문제 41
App Service 플랜을 배포하고 리소스 ID를 `planId`로 출력하는 **aspDeployment**라는 ARM 템플릿 배포가 있습니다. 기본 템플릿에서 App Service 리소스의 `serverFarmId`로 이 출력 값을 참조해야 합니다. 올바른 표현식은 무엇입니까?

- A. `[outputs('aspDeployment').planId]`
- B. `[aspDeployment.outputs.planId]`
- C. `[reference('aspDeployment').outputs.planId.value]`
- D. `[resourceId('Microsoft.Web/serverfarms', 'aspDeployment')]`

**정답: C**

**해설:** ARM 템플릿에서 연결된 배포 출력은 `reference()` 함수를 사용하여 접근합니다: `reference('<deploymentName>').outputs.<outputName>.value`. 출력 개체에서 실제 값을 추출하려면 `.value` 접미사가 필요합니다. Bicep은 `.value` 없이 `moduleName.outputs.outputName` 다른 구문을 사용합니다.

---

### 문제 42
스토리지 계정을 배포하는 **stgModule**이라는 모듈을 정의하는 Bicep 파일이 있습니다. 모듈에는 `storageId`라는 출력이 있습니다. 부모 Bicep 파일에서 이 출력을 어떻게 참조합니까?

- A. `storageId`
- B. `module.storageId`
- C. `stgModule.outputs.storageId`
- D. `reference('stgModule').outputs.storageId.value`

**정답: C**

**해설:** Bicep에서 모듈 출력은 `<moduleName>.outputs.<outputName>` 구문을 사용하여 접근합니다. ARM 템플릿과 달리 Bicep은 `.value` 접미사가 필요 없으며 출력이 형식화된 값으로 직접 접근 가능합니다. `.value`가 있는 `reference()` 함수 구문은 ARM 템플릿 구문이며 Bicep에서는 사용하지 않습니다.

---

### 문제 43
`targetScope = 'subscription'`이 있는 Bicep 파일을 작성 중입니다. 동일한 배포의 일부로 **StorageRG**라는 특정 리소스 그룹에 스토리지 계정을 배포해야 합니다. 스토리지 계정을 어떻게 배포해야 합니까?

- A. 리소스 그룹 리소스 옆에 구독 범위 Bicep 파일에 직접 스토리지 계정 배포
- B. 별도의 Bicep 배포 파일 사용 — 다른 범위의 리소스가 동일한 배포에 있을 수 없다
- C. 리소스 그룹 범위에서 스토리지 계정을 배포하기 위해 `scope: resourceGroup('StorageRG')`가 있는 모듈 사용
- D. 파일의 기본 범위를 재정의하기 위해 스토리지 계정 리소스에서 `scope: subscription()` 사용

**정답: C**

**해설:** Bicep에서 개별 모듈은 `scope` 속성을 사용하여 부모 파일과 다른 범위를 대상으로 할 수 있습니다. 구독 범위 파일에서 배포할 때 구독 범위에서 리소스 그룹을 만들고, `scope: resourceGroup('StorageRG')`가 있는 모듈을 사용하여 리소스 그룹 범위에서 스토리지 계정을 배포합니다. 스토리지 계정은 리소스 그룹 범위 리소스이며 구독 범위에서 직접 배포할 수 없습니다.

---

### 문제 44
`targetScope` 선언 없이 새 Bicep 파일을 만듭니다. 기본적으로 어떤 배포 범위가 사용됩니까?

- A. subscription
- B. tenant
- C. managementGroup
- D. resourceGroup

**정답: D**

**해설:** Bicep 파일에서 `targetScope`를 생략하면 기본 배포 범위는 `resourceGroup`입니다. 구독, 관리 그룹 또는 테넌트 범위에서 배포하려면 `targetScope = 'subscription'`(또는 적절한 수준)을 명시적으로 선언해야 합니다. 명시적 `scope` 속성이 없는 모듈은 부모 파일의 범위를 상속합니다.

---

### 문제 45
**AppRG**라는 리소스 그룹을 만들고 동일한 배포에서 그 안에 스토리지 계정을 배포하는 구독 범위 Bicep 파일을 작성합니다. 올바른 접근 방식은 무엇입니까?

- A. 모듈 없이 동일한 파일에서 구독 범위로 리소스 그룹과 스토리지 계정 모두 배포
- B. 기본 파일의 구독 범위에서 리소스 그룹을 배포하고, 스토리지 계정에 대해 `scope: resourceGroup('AppRG')`가 있는 모듈 사용
- C. 스토리지 계정 리소스에서 `scope: subscription()`을 사용하여 새 리소스 그룹에 배치
- D. 두 개의 별도 배포 사용 — 리소스 그룹과 스토리지 계정이 동일한 Bicep 파일에 있을 수 없다

**정답: B**

**해설:** 리소스 그룹은 구독 범위에서 배포됩니다. 스토리지 계정은 리소스 그룹 범위 리소스입니다 — 구독 범위에서 직접 배포할 수 없습니다. 구독 범위 Bicep 파일의 올바른 패턴은 먼저 리소스 그룹을 만들고(`Microsoft.Resources/resourceGroups`), 그 다음 해당 리소스 그룹으로 범위가 지정된 모듈(`scope: resourceGroup('AppRG')`)을 사용하여 스토리지 계정을 배포하는 것입니다. 두 리소스 모두 동일한 배포에 공존할 수 있습니다.

---

### 문제 46
Azure Policy를 사용하여 구독의 50개 가상 머신 모두에서 VM Insights를 자동으로 활성화할 계획입니다. 정책 이니셔티브를 할당하기 전에 필수 리소스를 만들어야 합니다. 먼저 어떤 리소스를 만들어야 합니까?

- A. VM 애플리케이션 모니터링을 위한 Application Insights 작업 영역
- B. Prometheus 기반 메트릭을 위한 Azure Monitor 작업 영역
- C. 수집된 데이터를 저장하기 위한 Log Analytics 작업 영역
- D. 수집할 데이터를 정의하는 VM Insights 데이터 수집 규칙(DCR)

**정답: D**

**해설:** Azure Policy(최신 DCR 기반 접근 방식)를 통해 VM Insights를 활성화할 때 정책 이니셔티브는 **데이터 수집 규칙(DCR)**을 필수 구성 요소로 요구합니다. DCR은 VM에서 수집할 성능 메트릭, 프로세스 데이터, 로그를 정의합니다. 데이터를 저장하기 위해 Log Analytics 작업 영역도 필요하지만, DCR은 정책 이니셔티브가 참조하는 특정 리소스이며 먼저 만들어야 합니다.

---

### 문제 47
팀에서 여러 Azure 구독에 걸쳐 VM 모니터링 대시보드를 표준화하려 합니다. 매번 쿼리와 레이아웃을 다시 만들지 않고 동일한 모니터링 뷰를 만들기 위해 배포할 수 있는 재사용 가능한 정의가 필요합니다. 이 요구사항을 충족하는 Azure Monitor 기능은 무엇입니까?

- A. Azure Monitor 경고 — 일관된 경고 대시보드를 생성하는 재사용 가능한 규칙 정의
- B. Log Analytics 저장된 쿼리 — 작업 영역 간에 공유할 수 있는 쿼리 정의
- C. Azure 대시보드 JSON 내보내기 — 기존 대시보드 구성을 내보내고 가져오기
- D. Azure Workbook 템플릿 — 미리 정의된 쿼리, 차트, 레이아웃이 포함된 Workbook을 위한 재사용 가능한 청사진

**정답: D**

**해설:** Azure Workbook 템플릿은 미리 만들어진 KQL 쿼리, 메트릭 쿼리, 시각화, 매개변수 정의를 포함하는 재사용 가능한 청사진입니다. 처음부터 다시 만들지 않고 여러 환경에 걸쳐 Workbook 인스턴스를 만들기 위해 배포할 수 있습니다. 조직 전체 사용을 위해 갤러리에 게시할 수 있습니다. Workbook 템플릿은 Workbook(실행 중인 인스턴스)과 구별됩니다.

---

### 문제 48
Azure Container Registry가 있습니다. CI/CD 파이프라인 서비스 주체가 레지스트리에서 새 이미지를 푸시하고 기본 이미지를 가져올 수 있도록 역할을 할당해야 합니다. 최소 권한 원칙을 사용해야 합니다. 어떤 기본 제공 역할을 할당해야 합니까?

- A. AcrPull — 이미지 가져오기 전용
- B. Contributor — 컨테이너 레지스트리의 전체 관리
- C. AcrPush — 레지스트리에서 푸시 및 가져오기 작업 모두 허용
- D. Owner — CI/CD 파이프라인 실행 중 레지스트리 설정 구성에 필요

**정답: C**

**해설:** **AcrPush** 기본 제공 역할에는 `Microsoft.ContainerRegistry/registries/pull/read`와 `Microsoft.ContainerRegistry/registries/push/write` 권한이 모두 포함됩니다. CI/CD 파이프라인에 필요한 것이 정확히 이것입니다: 새로 빌드된 이미지 푸시 및 기본 이미지 가져오기. **AcrPull**은 가져오기만 허용합니다. **Contributor**는 전체 리소스 관리 권한(레지스트리 삭제 포함)을 부여하며, 파이프라인에 필요한 것보다 훨씬 많은 권한입니다.

---

### 문제 49
동일한 Azure Storage 계정에 접근해야 하는 여러 Azure 웹 앱이 있습니다. 시스템 할당 및 사용자 할당 관리 ID 중에서 선택해야 합니다. 핵심 차이점을 올바르게 설명하는 것은 무엇입니까?

- A. 시스템 할당 관리 ID는 여러 리소스에서 공유할 수 있으며, 사용자 할당은 불가능하다
- B. 사용자 할당 관리 ID는 웹 앱 삭제 시 삭제되며, 시스템 할당은 독립적으로 유지된다
- C. 시스템 할당 관리 ID는 리소스 배포 전에 사전 생성해야 하며, 사용자 할당은 자동으로 만들어진다
- D. 사용자 할당 관리 ID는 배포 전에 만들 수 있고 여러 리소스에 연결할 수 있는 독립적인 리소스이며, 시스템 할당은 단일 리소스의 수명 주기에 종속된다

**정답: D**

**해설:** 사용자 할당 관리 ID는 자체 수명 주기를 가진 독립적인 Azure 리소스입니다 — 웹 앱이 존재하기 전에 만들 수 있고, RBAC 할당을 미리 구성할 수 있으며, 동일한 ID를 여러 웹 앱에 연결할 수 있습니다. 시스템 할당 ID는 리소스가 만들어질 때 자동으로 만들어지고, 리소스 간 공유할 수 없으며, 리소스 삭제 시 함께 삭제됩니다.

---

### 문제 50
Azure Firewall 정책(ParentPolicy)의 위협 인텔리전스 모드가 **Alert**로 설정되어 있습니다. 두 번째 방화벽 정책(ChildPolicy)이 ParentPolicy에서 상속하며 다른 Azure Firewall 인스턴스에 할당되어 있습니다. 관리자가 연결 문제를 해결하기 위해 ChildPolicy의 위협 인텔리전스 모드를 **Off**로 설정하려 합니다. 결과는 무엇입니까?

- A. 하위 정책이 모든 상위 설정을 재정의할 수 있으므로 설정이 적용된다
- B. 구성이 실패 — 하위 정책의 위협 인텔리전스 모드는 상위 정책보다 덜 제한적일 수 없다
- C. 변경은 허용되지만 런타임에 상위 정책의 Alert 모드가 재정의한다
- D. Off 모드는 테스트 방화벽에 할당된 하위 정책에만 허용된다

**정답: B**

**해설:** Azure Firewall Manager에서 하위 정책은 상위 정책과 동일하거나 더 엄격한 위협 인텔리전스 모드를 가져야 합니다. 엄격성 순서는 Off < Alert < Alert and deny입니다. 상위가 Alert로 설정되어 있으므로 하위는 Alert 또는 Alert and deny만 사용할 수 있습니다. 하위를 Off(Alert보다 덜 제한적)로 설정하는 것은 유효하지 않으며 구성이 거부됩니다.

---

*AZ-104 강의 자료를 기반으로 생성된 문서입니다. 최종 업데이트: 2026-05-22*
