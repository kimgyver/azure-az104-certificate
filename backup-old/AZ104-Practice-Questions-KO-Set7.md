# AZ-104 예상 문제 (세트 7) - 한국어

`expected_questiions/3-1.txt`, `expected_questiions/3-2.txt`의 실제 문제 문장을 전부 검토한 뒤, 중복을 제거해 정리한 세트입니다. 각 문항은 정답과 짧은 해설을 포함합니다.

---

## 1) 거버넌스, ID, RBAC

### 문제 1
Azure Policy 할당이 `modify` 효과를 사용하여 스토리지 계정에 필수 태그를 자동으로 추가합니다. 정책 평가 후 기존 스토리지 계정 여러 개가 비준수로 표시됩니다. 기존 비준수 스토리지 계정에 태그를 실제로 적용하려면 무엇을 구성해야 합니까?
- A. 정책을 다시 할당하고 24시간 기다린다
- B. 정책 할당에 대한 수정 작업(Remediation Task)을 만든다
- C. 정책 효과를 `deny`로 바꾼다
- D. 스토리지 계정을 다시 배포한다

**정답: B**
**해설:** `modify`와 `deployIfNotExists`는 기존 리소스를 자동 수정하지 않습니다. 기존 비준수 리소스를 수정하려면 Remediation Task가 필요합니다.

### 문제 2
사용자 지정 Azure Policy 정의가 각 VM에 대해 관련 확장 리소스가 존재하는지 검사해야 합니다. 확장이 없으면 VM을 비준수로 표시하되 배포는 차단하면 안 됩니다. 어떤 effect를 사용해야 합니까?
- A. Deny
- B. Modify
- C. AuditIfNotExists
- D. DeployIfNotExists

**정답: C**
**해설:** `AuditIfNotExists`는 관련 리소스가 없으면 비준수로만 표시하고 배포는 차단하지 않습니다.

### 문제 3
Microsoft Entra 관리자 역할을 할당할 수 있는 그룹을 만들어야 합니다. 이 그룹은 사용자의 부서 속성에 따라 멤버가 자동 추가/제거되어야 합니다. 이것이 가능합니까?
- A. 가능하다. 동적 사용자 멤버십 Security 그룹으로 만들면 된다
- B. 가능하다. 동적 사용자 멤버십 Microsoft 365 그룹으로 만들면 된다
- C. 불가능하다. 역할 할당 가능 그룹은 Assigned 멤버십만 지원한다
- D. 불가능하다. Entra 역할은 그룹에 할당할 수 없다

**정답: C**
**해설:** 역할 할당 가능 그룹은 보안상 이유로 동적 멤버십을 지원하지 않으며 Assigned 멤버십만 사용할 수 있습니다.

### 문제 4
공유 Azure 대시보드를 사용자 그룹과 공유하려고 합니다. 이 사용자들은 대시보드를 보고 로컬 편집은 가능해야 하지만, 변경 내용을 서버의 원본 대시보드에 게시해서는 안 됩니다. 어떤 Azure RBAC 역할을 할당해야 합니까?
- A. Contributor
- B. Reader
- C. Owner
- D. Monitoring Contributor

**정답: B**
**해설:** Reader는 공유 대시보드를 볼 수 있고 개인 복사본이나 로컬 편집은 가능하지만 원본 대시보드에 게시할 수는 없습니다.

### 문제 5
외부 협업 설정에서 User Administrator 또는 Guest Inviter 역할을 가진 사용자만 게스트를 초대할 수 있도록 제한해야 합니다. 어떤 Guest invite settings 옵션을 선택해야 합니까?
- A. Everyone including guests can invite
- B. Member users and users assigned to specific admin roles can invite guests
- C. Only users assigned to specific admin roles can invite guests
- D. No one in the organization can invite guest users

**정답: C**
**해설:** 일반 멤버는 제외하고 특정 관리자 역할만 게스트 초대를 허용하려면 이 옵션을 선택해야 합니다.

### 문제 6
Azure PowerShell을 사용하여 Microsoft Entra ID에서 새 사용자를 만들고 사용자 속성을 관리하려고 합니다. 최소 권한 역할은 무엇입니까?
- A. Global Administrator
- B. Privileged Role Administrator
- C. User Administrator
- D. Directory Writer

**정답: C**
**해설:** User Administrator는 사용자 생성과 사용자 속성 관리를 위한 최소 권한 역할입니다.

### 문제 7
동료가 새 사용자 생성, 사용자 암호 재설정, 사용자 라이선스 관리를 수행해야 합니다. 최소 권한 Microsoft Entra 역할은 무엇입니까?
- A. License Administrator
- B. Helpdesk Administrator
- C. User Administrator
- D. Global Administrator

**정답: C**
**해설:** User Administrator는 사용자 생성, 암호 재설정, 라이선스 관리를 모두 수행할 수 있는 최소 권한 역할입니다.

### 문제 8
RG1 범위에서 Contributor 역할을 가진 User1이 RG1에 CanNotDelete 잠금을 만들어야 합니다. 이를 허용하려면 무엇을 해야 합니까?
- A. User1의 Contributor 역할을 제거한다
- B. 포털에서 권한 상승을 사용하게 한다
- C. Virtual Machine Contributor를 구독 범위에 할당한다
- D. RG1 범위에서 User Access Administrator 역할을 할당한다

**정답: D**
**해설:** 잠금 관리에는 `Microsoft.Authorization/locks/*` 권한이 필요하며, 최소 권한 내장 역할은 User Access Administrator입니다.

### 문제 9
MG1 관리 그룹에서 Reader 역할을, RG1 리소스 그룹에서 Contributor 역할을 가진 User1의 RG1 리소스에 대한 유효 권한은 무엇입니까?
- A. Reader
- B. Contributor
- C. Owner
- D. 권한 없음

**정답: B**
**해설:** Azure RBAC는 누적(additive) 모델이므로 RG1 범위의 Contributor 권한이 적용되어 최종적으로 Contributor 수준 권한을 갖습니다.

### 문제 10
Microsoft Entra admin center에서 CSV로 사용자 일괄 생성을 하려고 합니다. 필수 열은 무엇입니까?
- A. User principal name, Initial password, Department, Job title
- B. Name, User principal name, Initial password, Usage location
- C. Name, User principal name, Display name, Initial password
- D. Name, User principal name, Initial password, Block sign in

**정답: D**
**해설:** 필수 열은 Name, User principal name, Initial password, Block sign in입니다.

### 문제 11
MG1 관리 그룹과 RG1 리소스 그룹 모두에서 리소스 삭제를 방지하려고 합니다. 리소스 잠금 구현에 대해 올바른 설명은 무엇입니까?
- A. MG1에 잠금을 적용하면 RG1에 상속된다
- B. MG1에는 잠금을 적용할 수 있지만 RG1에는 적용할 수 없다
- C. MG1과 RG1 모두 잠금 대신 Azure Policy만 사용해야 한다
- D. RG1에는 잠금을 적용할 수 있지만 MG1에는 적용할 수 없다

**정답: D**
**해설:** 리소스 잠금은 구독, 리소스 그룹, 리소스 범위에만 적용할 수 있으며 관리 그룹에는 적용할 수 없습니다.

---

## 2) 스토리지, 파일, 백업

### 문제 12
Azure Storage에서 가장 높은 보안 수준으로 Blob 리소스에 위임 액세스를 제공하려고 합니다. Microsoft가 권장하는 SAS 유형은 무엇입니까?
- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Stored access SAS

**정답: C**
**해설:** User delegation SAS는 Microsoft Entra ID 기반이라 저장소 계정 키를 직접 사용하지 않아 가장 안전합니다.

### 문제 13
Blob 컨테이너 `container1`에 대해 파트너 앱에 임시 읽기/쓰기 권한을 제공해야 하며, 저장소 계정 키 재생성 없이 빠르게 권한을 취소할 수 있어야 합니다. 어떤 접근 방식을 사용해야 합니까?
- A. 짧은 만료 시간을 가진 ad hoc Service SAS
- B. Stored access policy와 연결된 Service SAS
- C. Stored access policy와 연결된 Account SAS
- D. Stored access policy와 연결된 User delegation SAS

**정답: B**
**해설:** Stored access policy는 Service SAS에서만 지원되며, 정책을 수정/삭제해 키 재생성 없이 빠르게 액세스를 취소할 수 있습니다.

### 문제 14
특정 Blob에 대해 30일 동안만 임시 읽기 권한을 제3자 애플리케이션에 제공해야 합니다. SAS를 사용하면서 가장 안전한 방법은 무엇입니까?
- A. Account SAS
- B. Service SAS
- C. User delegation SAS
- D. Shared key authentication

**정답: C**
**해설:** 가장 안전한 SAS 유형은 Entra ID 기반의 User delegation SAS입니다.

### 문제 15
`storage1`을 온프레미스 AD DS에 등록해 Azure 파일 공유에 대한 AD DS 인증을 사용하려고 합니다. `AzFilesHybrid`가 기본적으로 생성하는 객체 유형은 무엇입니까?
- A. 사용자 객체
- B. 그룹 객체
- C. 컴퓨터 객체
- D. 서비스 주체 객체

**정답: C**
**해설:** `Join-AzStorageAccountForAuth`는 기본적으로 컴퓨터 객체를 생성합니다.

### 문제 16
Windows VM `VM1`에 Azure Disk Encryption을 활성화하려고 합니다. Key Vault에서 미리 활성화해야 하는 액세스 정책 설정은 무엇입니까?
- A. Azure Resource Manager for template deployment
- B. Azure Disk Encryption for volume encryption
- C. Azure Backup for deployment
- D. Purge protection only

**정답: B**
**해설:** Azure Disk Encryption은 Key Vault에서 `Azure Disk Encryption for volume encryption` 설정을 필요로 합니다.

### 문제 17
Microsoft Entra Domain Services 인증이 활성화된 Azure 파일 공유가 있습니다. FileUsers 그룹에 SMB를 통한 읽기/쓰기 권한을 부여하려면 파일 공유 수준에서 어떤 기본 제공 역할을 할당해야 합니까?
- A. Storage File Data SMB Share Reader
- B. Storage File Data SMB Share Contributor
- C. Storage File Data SMB Share Elevated Contributor
- D. Reader

**정답: B**
**해설:** 읽기와 쓰기를 모두 허용하는 최소 기본 제공 역할은 Storage File Data SMB Share Contributor입니다.

### 문제 18
AD DS 인증이 활성화된 Azure 파일 공유가 있습니다. LocalSystem 컨텍스트에서 실행되는 앱이 도메인 조인 VM의 컴퓨터 계정으로 파일 공유에 접근해야 합니다. 구성 노력을 최소화하려면 share-level permissions에서 무엇을 구성해야 합니까?
- A. 각 컴퓨터 계정에 Storage File Data SMB Share Contributor를 직접 할당한다
- B. 저장소 계정 키 인증으로 전환한다
- C. 모든 인증된 ID에 대한 기본 share-level permission을 구성한다
- D. Reader 역할을 저장소 계정 범위에 할당한다

**정답: C**
**해설:** 컴퓨터 계정은 Entra ID에 직접 동기화되지 않으므로 Azure RBAC로 직접 부여할 수 없고, 기본 share-level permission이 최소 설정 해법입니다.

### 문제 19
보호된 Azure 파일 공유가 있는 스토리지 계정이 있습니다. 백업 정책은 Snapshot tier only를 사용합니다. 여러 파일이 삭제되었고, 삭제된 파일만 원래 위치로 복원해야 합니다. 어떤 복원 유형을 선택해야 합니까?
- A. File Recovery with Original Location as the recovery destination
- B. Full Share Recovery with Alternate Location as the destination
- C. File Recovery with Alternate Location as the recovery destination
- D. Full Share Recovery with Original Location as the destination

**정답: A**
**해설:** Snapshot tier Azure Files 백업은 파일 단위 복원을 지원하며, 원래 위치 복원이 필요하면 File Recovery + Original Location을 선택합니다.

### 문제 20
여러 VM에 연결된 관리 디스크에 대해 비용 효율적인 point-in-time 백업을 만들려 합니다. 이전 백업 이후 변경된 데이터만 저장해야 합니다. 어떤 스냅샷 유형을 사용해야 합니까?
- A. Full snapshot
- B. Incremental snapshot
- C. Page blob snapshot
- D. Differential snapshot

**정답: B**
**해설:** Incremental snapshot은 이전 스냅샷 이후 변경된 블록만 저장합니다.

### 문제 21
공용 DNS 영역 `fabrikam.com`에서 동일한 이름의 여러 A 레코드를 포함하는 레코드 집합을 만들어 부하를 분산하려고 합니다. 하나의 레코드 집합에 추가할 수 있는 최대 레코드 수는 얼마입니까?
- A. 10
- B. 20
- C. 50
- D. 100

**정답: B**
**해설:** Azure DNS의 하나의 레코드 집합에는 최대 20개의 레코드를 포함할 수 있습니다.

### 문제 22
여러 Blob 컨테이너가 있는 저장소 계정에서 User1이 `container1`의 Blob만 읽을 수 있도록 하려 합니다. 다른 컨테이너에는 접근하면 안 됩니다. Storage Blob Data Reader 역할은 어느 범위에 할당해야 합니까?
- A. 구독 범위
- B. 저장소 계정 범위
- C. 컨테이너 `container1` 범위
- D. 각 Blob 개체 범위만 가능하다

**정답: C**
**해설:** 최소 권한을 만족하려면 `container1` 컨테이너 범위에 역할을 할당해야 합니다.

### 문제 23
Blob soft delete가 14일로 활성화되어 있고, Blob versioning도 활성화되어 있습니다. 개발자가 기존 Blob을 덮어쓰면 이전 콘텐츠는 어떻게 됩니까?
- A. Soft-deleted snapshot이 자동 생성된다
- B. 이전 Blob은 즉시 영구 삭제된다
- C. 이전 버전이 자동으로 생성된다
- D. 보존 기간 동안 변경되지 않는다

**정답: C**
**해설:** Versioning이 활성화된 상태에서 덮어쓰기를 하면 기존 콘텐츠는 previous version으로 보존됩니다.

### 문제 24
SMB와 NFS 파일 공유가 섞여 있는 Azure 저장소 계정이 있습니다. Azure Backup으로 백업할 수 있는 파일 공유는 무엇입니까?
- A. SMB와 NFS 모두 가능
- B. SMB만 가능
- C. NFS만 가능
- D. Premium 파일 공유만 가능

**정답: B**
**해설:** Azure Backup의 Azure Files 백업은 SMB 파일 공유만 지원합니다.

### 문제 25
다른 구독에서 필요한 권한 없이 `storage1`에 대한 private endpoint를 만들었습니다. 이 private endpoint의 초기 연결 상태는 무엇입니까?
- A. Approved
- B. Connected
- C. Pending
- D. Rejected

**정답: C**
**해설:** 대상 리소스에 대한 권한이 없으면 private endpoint 연결은 승인을 기다리는 Pending 상태로 시작합니다.

### 문제 26
VM1은 Recovery Services vault에 백업되고 있습니다. 60일 보존 기간으로 on-demand 백업을 만들었고, 일주일 뒤 정책 보존 기간을 14일로 바꿨습니다. on-demand 복구 지점은 어떻게 됩니까?
- A. 즉시 14일로 변경된다
- B. 30일로 재설정된다
- C. 60일 동안 유지된다
- D. 다음 백업 시 삭제된다

**정답: C**
**해설:** On-demand 백업의 보존 기간은 생성 시점에 고정되며 이후 정책 변경의 영향을 받지 않습니다.

### 문제 27
`storage1` 범위에서 Storage Blob Data Contributor 역할이 할당된 User1이 Azure Portal에서 컨테이너를 볼 수 없다고 합니다. 최소 권한을 유지하면서 Portal에서 컨테이너를 탐색할 수 있게 하려면 무엇을 해야 합니까?
- A. Storage Blob Data Reader 역할만 추가한다
- B. Reader 역할을 저장소 계정 범위에 추가한다
- C. Contributor 역할을 Reader로 바꾼다
- D. Owner 역할을 추가한다

**정답: B**
**해설:** Azure Portal에서 스토리지 리소스를 탐색하려면 ARM Reader 권한이 추가로 필요합니다. 데이터 평면 역할만으로는 포털 탐색이 충분하지 않습니다.

### 문제 28
Archive tier의 Block Blob을 지정된 기간 후 Hot tier로 자동 rehydrate하려고 합니다. Lifecycle management policy를 사용할 예정이라면 어떻게 해야 합니까?
- A. `enableAutoTierToHotFromCool` 속성을 사용한다
- B. `tierToHot` 액션을 구성한다
- C. 먼저 `tierToCool` 후 `tierToHot`을 사용한다
- D. 다른 방법을 사용해야 한다. Lifecycle policy는 Archive rehydrate를 지원하지 않는다

**정답: D**
**해설:** Lifecycle management policy는 Hot/Cool/Archive로 더 차가운 계층 이동만 지원하며, Archive에서 Hot/Cool로의 rehydrate는 지원하지 않습니다.

---

## 3) 네트워킹, DNS, 애플리케이션 접근

### 문제 29
Application Gateway 뒤의 App Service가 올바른 호스트 이름으로 요청을 받도록 하려면 무엇을 설정해야 합니까?
- A. 백엔드 풀에 사용자 지정 도메인을 넣는다
- B. Backend HTTP settings에서 `Pick host name from backend target`을 사용한다
- C. 프런트엔드 인증서만 교체한다
- D. Backend pool에 공용 IP를 지정한다

**정답: B**
**해설:** App Service는 호스트 헤더 기반 멀티테넌트 서비스이므로 Backend HTTP settings에서 백엔드 대상 호스트 이름을 사용해야 합니다.

### 문제 30
서브넷에 연결된 NSG 때문에 인터넷에서 VM1로의 RDP가 허용되는지, 그리고 어떤 특정 NSG 규칙이 결정에 영향을 주는지 즉시 확인하려면 무엇을 사용해야 합니까?
- A. NSG flow logs
- B. Connection troubleshoot
- C. IP flow verify
- D. Traffic analytics

**정답: C**
**해설:** IP flow verify는 특정 트래픽의 허용/거부 여부와 어떤 NSG 규칙이 적용되었는지 바로 보여줍니다.

### 문제 31
`Subnet1`에 `Microsoft.Storage` 서비스 엔드포인트가 활성화되어 있습니다. 특정 저장소 계정으로만 접근을 제한하려는 service endpoint policy의 제한 사항은 무엇입니까?
- A. 같은 지역의 저장소 계정에만 적용 가능하다
- B. 온프레미스 네트워크에서 오는 트래픽에는 적용되지 않는다
- C. 최소 `/24` 서브넷이 필요하다
- D. 정책당 하나의 저장소 계정만 허용할 수 있다

**정답: B**
**해설:** Service endpoint policy는 Azure 서브넷에서 시작하는 트래픽만 필터링하며 온프레미스 트래픽에는 적용되지 않습니다.

### 문제 32
VPN 게이트웨이와 BGP를 사용하는 VNet에서 특정 서브넷의 트래픽을 먼저 NVA로 보내고 싶습니다. 서브넷에 자동으로 추가되는 온프레미스 경로를 막으려면 route table에서 무엇을 구성해야 합니까?
- A. Propagate gateway routes를 Enabled로 둔다
- B. BGP route propagation을 비활성화한다
- C. 기본 경로를 Internet으로 설정한다
- D. Virtual network gateway를 next hop으로 설정한다

**정답: B**
**해설:** BGP route propagation을 끄면 게이트웨이에서 학습한 온프레미스 경로가 자동으로 서브넷에 적용되지 않습니다.

### 문제 33
`ASG-Web`이 이미 VNet1의 VM들을 포함하고 있습니다. NSG 규칙에서 `ASG-Web`에서 `ASG-DB`로 TCP 443을 허용하려면 `ASG-DB`에는 어떤 VM들이 멤버가 될 수 있습니까?
- A. East US 지역의 아무 VM이나 가능하다
- B. VNet1의 VM만 가능하다
- C. VM3만 가능하다
- D. 구독의 아무 VM이나 가능하다

**정답: B**
**해설:** 같은 NSG 규칙에서 소스/대상 ASG를 사용할 때 두 ASG의 NIC는 같은 VNet에 있어야 합니다.

### 문제 34
공용 DNS 영역 `contoso.com`의 zone apex를 Azure Traffic Manager 프로필로 해석되게 하려면 어떤 레코드를 만들어야 합니까?
- A. CNAME 레코드
- B. TXT 레코드
- C. Alias 레코드
- D. PTR 레코드

**정답: C**
**해설:** zone apex에는 CNAME을 사용할 수 없으므로 Traffic Manager를 가리키는 Alias 레코드를 사용해야 합니다.

### 문제 35
VNet1에 Azure Bastion Standard SKU가 배포되어 있습니다. Azure Portal 계정이 없는 외부 계약자가 VM1에 안전하게 접속해야 합니다. 무엇을 구성해야 합니까?
- A. Just-in-time VM access
- B. Azure Bastion Shareable Link
- C. 공용 IP와 NSG 3389 허용
- D. P2S VPN

**정답: B**
**해설:** Azure Bastion Standard의 Shareable Link를 사용하면 포털 계정이 없는 사용자도 브라우저 기반으로 안전하게 접속할 수 있습니다.

### 문제 36
민감한 데이터를 처리하는 VM들에 대해 세션 녹화와 public IP 없는 Bastion 배포가 필요합니다. 어떤 Azure Bastion SKU를 사용해야 합니까?
- A. Developer
- B. Basic
- C. Standard
- D. Premium

**정답: D**
**해설:** Session recording과 private-only deployment는 Azure Bastion Premium SKU 기능입니다.

---

## 4) 컴퓨팅, App Service, ACR, AKS

### 문제 37
Azure Container Instances에서 여러 컨테이너가 포함된 container group을 배포하려고 합니다. multi-container group을 지원하는 운영체제는 무엇입니까?
- A. Windows
- B. Linux
- C. Linux와 Windows 모두
- D. Nano Server만 가능

**정답: B**
**해설:** ACI의 multi-container group은 Linux에서만 지원됩니다.

### 문제 38
WebApp1의 `staging` 배포 슬롯을 production으로 교체하기 전에 production 설정으로 검증하고, 실패 시 취소할 수 있어야 합니다. 무엇을 사용해야 합니까?
- A. 일반 swap
- B. Swap with preview
- C. Auto swap
- D. Traffic routing only

**정답: B**
**해설:** Swap with preview는 production 설정을 staging에 미리 적용해 검증하고 필요하면 취소할 수 있습니다.

### 문제 39
App Service의 staging 슬롯으로 production 트래픽 10%를 보내도록 설정했습니다. 한 사용자가 staging 슬롯으로 라우팅되었을 때, 세션은 얼마나 오래 해당 슬롯에 고정됩니까?
- A. 브라우저가 닫힐 때까지
- B. 30분
- C. 1시간 또는 쿠키가 삭제될 때까지
- D. 슬롯 swap 전까지

**정답: C**
**해설:** App Service traffic routing은 `x-ms-routing-name` 쿠키를 사용하며, 기본적으로 1시간 또는 쿠키 삭제 시까지 슬롯 고정이 유지됩니다.

### 문제 40
Basic tier App Service plan에서 실행 중인 WebApp1에 staging deployment slot을 만들려면 먼저 무엇을 해야 합니까?
- A. WebApp1을 다시 배포한다
- B. App Service plan을 Standard 이상으로 scale up한다
- C. 새 리소스 그룹을 만든다
- D. WebApp1을 PremiumV3로 이전한다

**정답: B**
**해설:** Deployment slot은 Standard 이상 App Service plan에서만 지원됩니다.

### 문제 41
Standard tier App Service plan의 webapp1에 여러 사용자 지정 도메인을 같은 IP에서 TLS/SSL로 구성하면서 비용을 최소화하려면 어떤 바인딩 유형을 사용해야 합니까?
- A. IP-based SSL
- B. SNI SSL
- C. TLS mutual auth
- D. Basic SSL

**정답: B**
**해설:** SNI SSL은 하나의 공유 IP에서 여러 도메인을 지원하고 IP-based SSL보다 비용 효율적입니다.

### 문제 42
AKS 시스템 노드 풀에서 애플리케이션 Pod의 스케줄링을 막으면서 시스템 Pod는 계속 실행하려면 어떤 taint를 적용해야 합니까?
- A. `node-role.kubernetes.io/system:NoExecute`
- B. `kubernetes.azure.com/mode=system:NoSchedule`
- C. `SystemOnly=true:PreferNoSchedule`
- D. `CriticalAddonsOnly=true:NoSchedule`

**정답: D**
**해설:** AKS 시스템 노드 풀 보호의 표준 taint는 `CriticalAddonsOnly=true:NoSchedule`입니다.

### 문제 43
여러 Azure 지역의 AKS 클러스터에서 ACR1로부터 이미지 pull 지연 시간을 줄이려 합니다. 현재 ACR1은 Standard tier입니다. 먼저 무엇을 해야 합니까?
- A. Geo-replication을 바로 활성화한다
- B. ACR1을 Premium tier로 업그레이드한다
- C. AKS마다 별도 ACR을 만든다
- D. ACR 콘텐츠를 Storage 계정으로 복사한다

**정답: B**
**해설:** Geo-replication은 Premium tier ACR에서만 지원되므로 먼저 Premium으로 업그레이드해야 합니다.

### 문제 44
Availability set `AS1`이 기본 설정으로 만들어졌습니다. 관리 디스크를 사용하는 VM을 `AS1`에 배포할 때 지원되는 fault domain과 update domain 수는 얼마입니까?
- A. Fault domains 2, Update domains 5
- B. Fault domains 3, Update domains 5
- C. Fault domains 3, Update domains 20
- D. Fault domains 2, Update domains 20

**정답: B**
**해설:** 기본 Availability Set은 3개의 fault domains와 5개의 update domains를 지원합니다.

---

## 5) Bicep, ARM, 예산, 모니터링

### 문제 45
`storage.bicep` 모듈의 출력 `storageEndpoint`를 main Bicep 파일에서 참조하려면 어떤 구문을 사용해야 합니까?
- A. `storageEndpoint`
- B. `module.storageEndpoint`
- C. `<moduleName>.outputs.<outputName>`
- D. `reference(moduleName).storageEndpoint`

**정답: C**
**해설:** Bicep 모듈 출력은 `moduleName.outputs.outputName` 형태로 참조합니다.

### 문제 46
가상 머신에 대한 모든 권한을 주되 삭제만 제외하는 사용자 지정 역할을 만들려면 역할 정의에서 어떤 속성 조합을 사용해야 합니까?
- A. `Actions`만 사용해 허용한다
- B. `NotActions`만 사용해 삭제를 차단한다
- C. `Actions`에 VM 전체 권한, `NotActions`에 delete를 넣는다
- D. `DataActions`에 delete를 제외한다

**정답: C**
**해설:** 사용자 지정 역할에서 예외를 만들 때는 `Actions`와 `NotActions`를 함께 사용합니다.

### 문제 47
`deployStorage` 매개변수가 true일 때만 스토리지 계정을 조건부 배포하려면 Bicep 리소스 선언에서 어떤 구문을 사용해야 합니까?
- A. `condition: deployStorage`
- B. `= if (deployStorage) { ... }`
- C. `when deployStorage { ... }`
- D. `? :` 삼항 연산자

**정답: B**
**해설:** Bicep의 조건부 리소스 배포는 `= if (condition) { }` 구문을 사용합니다.

### 문제 48
조건부로 배포되는 스토리지 계정이 배포된 경우에만 Blob endpoint를 반환하고, 아니면 빈 문자열을 반환하는 Bicep 출력 선언은 무엇입니까?
- A. `output blobEndpoint string = if (deployStorage) ...`
- B. `output blobEndpoint string = storageAccount.properties.primaryEndpoints.blob ?? ''`
- C. `output blobEndpoint string = deployStorage ? storageAccount.properties.primaryEndpoints.blob : ''`
- D. `output blobEndpoint string = when (deployStorage) ... else ''`

**정답: C**
**해설:** Bicep 출력의 조건 처리에는 `? :` 삼항 연산자를 사용합니다.

### 문제 49
ARM linked template가 반환한 `planId` 출력을 App Service의 `serverFarmId`에 사용하려면 어떤 표현식을 사용해야 합니까?
- A. `[resourceId('planId')]`
- B. `[deployment().outputs.planId]`
- C. `[reference('linkedTemplateDeployment').outputs.planId.value]`
- D. `[variables('planId')]`

**정답: C**
**해설:** Linked template의 출력은 `reference('<deploymentName>').outputs.<output>.value` 형식으로 참조합니다.

### 문제 50
구독 범위에서 Cost Management Reader 역할을 가진 User1은 예산 `Budget1`에 대해 무엇을 할 수 있습니까?
- A. 예산을 생성하고 수정할 수 있다
- B. 예산을 삭제할 수 있다
- C. 예산을 볼 수만 있다
- D. 예산 알림 임계값을 바꿀 수 있다

**정답: C**
**해설:** Cost Management Reader는 예산을 조회할 수 있지만 생성, 수정, 삭제는 할 수 없습니다.

### 문제 51
여러 구독 비용을 관리 그룹 범위 예산으로 모니터링하려고 합니다. 이 예산을 만들 때 무엇을 예상해야 합니까?
- A. 관리 그룹 범위에서는 예산을 만들 수 없다
- B. Action group을 구독 범위 예산과 동일하게 구성할 수 있다
- C. 예산 임계값 경고는 지원되지 않는다
- D. Action group은 관리 그룹 범위 예산에서 구성할 수 없다

**정답: D**
**해설:** 관리 그룹 범위 예산은 이메일 알림은 가능하지만 action group을 통한 자동화 트리거는 지원하지 않습니다.

### 문제 52
VM1에 대해 Azure Portal에서 VM Insights를 기본 데이터 수집 규칙으로 활성화했습니다. 기본적으로 수집되는 데이터는 무엇입니까?
- A. 게스트 성능 메트릭과 프로세스/종속성 데이터
- B. 게스트 성능 메트릭과 Windows 이벤트 로그
- C. 호스트 메트릭과 게스트 메트릭
- D. 게스트 성능 메트릭만

**정답: D**
**해설:** 기본 VM Insights 설정은 비용 최적화를 위해 guest performance metrics만 수집하고 프로세스/종속성 데이터는 기본으로 끄는 구성이 사용됩니다.

### 문제 53
Log Analytics에서 시간대별 이벤트 개수를 집계한 결과를 시간 기반 선형 차트로 시각화하려면 쿼리 끝에 어떤 연산자를 추가해야 합니까?
- A. `render linechart`
- B. `render timechart`
- C. `visualize time`
- D. `chart time`

**정답: B**
**해설:** 시간축 기반 시계열 시각화는 `render timechart`를 사용합니다.

---

*Set 7 종료 — 53문항*

**주제 커버리지 요약:**
- Q1-Q11: Azure Policy, Entra ID, RBAC, 잠금, 대시보드, 게스트 초대, 사용자 역할
- Q12-Q28: SAS, Azure Files, AD DS, Azure Disk Encryption, 백업, Blob 보호, Azure DNS 한도, Archive rehydrate
- Q29-Q36: Application Gateway, NSG 진단, Service Endpoint Policy, BGP 전파, ASG, DNS, Bastion
- Q37-Q44: ACI, App Service 슬롯, AKS, ACR, Availability Set
- Q45-Q53: Bicep/ARM 출력, 조건부 배포, 예산, VM Insights, KQL 시각화
