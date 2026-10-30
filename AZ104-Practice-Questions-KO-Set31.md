# AZ-104 예상 문제 (세트 31) — 한국어

2026-04-17 스킬 가이드 기준 **갭 세트**입니다. 세트 11–23에서 비어 있거나 얇았던 공식 항목만 다룹니다. Identity/Policy, Azure Firewall, Application Gateway, AKS, PIM은 넣지 않았습니다.

| 영역 | 문제 |
|---|---|
| 컴퓨팅 (VM, 디스크, VMSS, 컨테이너) | 1–13 |
| 스토리지 (AzCopy, 개체 복제, 암호화) | 14–20 |
| 네트워킹 (NSG, DNS, Public IP, 엔드포인트, Watcher) | 21–30 |
| 모니터링·백업 (경고, KQL, RSV/Backup vault, CRR/ASR) | 31–40 |

---

## 1) 컴퓨팅: VM, 디스크, VMSS, 컨테이너

### 문제 1
Azure VM의 **임시 디스크(temp disk)** 와 **OS/데이터 디스크 캐시**까지 호스트에서 암호화해야 합니다. 게스트 OS 안에 BitLocker를 설치하고 싶지 않습니다. 무엇을 사용해야 합니까?

- A. Azure Disk Encryption (ADE)
- B. Encryption at host (호스트에서 암호화)
- C. 스토리지 서비스 암호화(SSE) with PMK만
- D. 스토리지 계정 HTTPS 전용

**정답: B**
**해설:** Encryption at host는 VM이 있는 호스트에서 데이터가 암호화됩니다. 임시 디스크, OS/데이터 디스크 캐시, 임시 OS 디스크가 포함됩니다. ADE는 게스트 OS 내부(BitLocker/DM-Crypt)입니다. SSE는 스토리지에 저장될 때 디스크를 암호화하지만, 호스트 캐시·임시 디스크는 다루지 않습니다.

---

### 문제 2
실행 중인 VM에 Encryption at host를 켜려고 합니다. 올바른 순서는 무엇입니까?

- A. 실행 중인 상태에서 VM 속성만 변경한다
- B. VM을 중지(할당 취소)한 뒤 Encryption at host를 사용하도록 설정하고 다시 시작한다
- C. ADE를 먼저 설치한 뒤 Encryption at host를 켠다
- D. OS 디스크를 스냅샷한 뒤 암호화 키만 Key Vault로 옮긴다

**정답: B**
**해설:** Encryption at host는 **새 VM 만들기** 또는 **할당 취소(deallocate) 후** 사용하도록 설정합니다. 실행 중에는 켤 수 없습니다. ADE와는 별개이며, ADE가 선행 조건이 아닙니다.

---

### 문제 3
VM1을 같은 구독·같은 리전의 다른 리소스 그룹으로 옮기려 합니다. 어떤 기능을 사용해야 합니까?

- A. Azure Site Recovery
- B. Azure Resource Mover
- C. Azure 리소스 이동(Move)
- D. Recovery Services vault에서 복원

**정답: C**
**해설:** 같은 리전 안에서 리소스 그룹(또는 같은 테넌트의 다른 구독)으로 옮기는 것은 **리소스 이동(Move)** 입니다. Resource Mover와 ASR은 **리전을 넘길 때** 사용합니다. 백업 복원은 새 VM을 만드는 것이지 기존 리소스 ID를 옮기는 것이 아닙니다.

---

### 문제 4
VM1을 **East US에서 West US로** 옮기려 합니다. 리소스 이동(Move)을 사용할 수 있습니까?

- A. 예. Move는 리전 간 이동을 지원한다
- B. 아니요. 리전 간에는 Azure Resource Mover, ASR 또는 백업/복원을 사용한다
- C. 예. 단 VM을 먼저 할당 취소해야 한다
- D. 아니요. 리전 간 이동은 지원되지 않는다

**정답: B**
**해설:** Move API는 **리전을 바꾸지 않습니다.** 리전 이동은 Azure Resource Mover, Azure Site Recovery, 또는 백업 후 대상 리전에 복원입니다. “지원 안 됨”은 오답입니다. 방법은 있습니다.

---

### 문제 5
VM1을 다른 구독으로 옮기려 합니다. 반드시 만족해야 하는 조건은 무엇입니까?

- A. 두 구독이 같은 Microsoft Entra 테넌트에 있어야 한다
- B. 두 구독이 같은 관리 그룹에만 있으면 테넌트가 달라도 된다
- C. 대상 구독이 다른 디렉터리여도 Owner면 된다
- D. VM에 공인 IP가 있으면 구독 이동은 불가하다

**정답: A**
**해설:** 구독 간 리소스 이동은 **같은 Entra 테넌트**여야 합니다. 관리 그룹이 같아도 디렉터리가 다르면 안 됩니다. 공인 IP는 제약 조건이 있을 수 있지만, “있으면 무조건 불가”는 아닙니다. NIC·디스크 등 종속 리소스를 함께 옮기는 것이 일반적입니다.

---

### 문제 6
새 Virtual Machine Scale Set을 만듭니다. 인스턴스를 표준 VM API로 관리하고, 나중에 서로 다른 VM 크기를 섞을 수 있어야 합니다. 어떤 오케스트레이션 모드를 선택해야 합니까?

- A. Uniform
- B. Flexible
- C. Availability Set
- D. Proximity Placement Group

**정답: B**
**해설:** **Flexible**이 권장 모드입니다. 표준 `Microsoft.Compute/virtualMachines` API, 혼합 VM 크기(instance mix), Spot+일반 혼합이 가능합니다. Uniform은 동일한 인스턴스를 스케일 셋 VM API로 관리합니다.

---

### 문제 7
Uniform 오케스트레이션 VMSS에 대해 올바른 설명은 무엇입니까?

- A. 스케일링 프로파일(VM 템플릿)은 선택 사항이다
- B. 스케일링 프로파일은 **필수**이며, 용량을 늘리면 그 템플릿으로 인스턴스가 만들어진다
- C. 기존 일반 VM을 세트에 자유롭게 추가할 수 있다
- D. Linux와 Windows를 한 세트에 섞을 수 있다

**정답: B**
**해설:** Uniform은 미리 정의한 **스케일링 프로파일**이 필수입니다. 이미지, NIC, 디스크, 자격 증명이 여기에 들어갑니다. 프로파일 없이 만드는 것, 기존 VM 추가, OS 혼용은 **Flexible** 쪽입니다.

---

### 문제 8
VMSS 자동 스케일에 평소 규칙, 주말 축소, 블랙프라이데이 하루 확대가 필요합니다. Azure Monitor 오토스케일 프로파일 타입을 어떻게 조합해야 합니까?

- A. Default만 세 개
- B. Default(평소) + Recurrence(주말) + Fixed-date(블랙프라이데이)
- C. Recurrence 세 개
- D. Fixed-date만. Default는 삭제한다

**정답: B**
**해설:** 프로파일은 세 종류입니다. **Default**(상시, 삭제 불가), **Recurrence**(요일 반복), **Fixed-date**(특정 기간). Default는 설정당 하나이고 지울 수 없습니다.

---

### 문제 9
오토스케일 엔진이 프로파일을 고르는 순서는 무엇입니까?

- A. Default → Recurrence → Fixed-date
- B. Recurrence → Fixed-date → Default
- C. Fixed-date → Recurrence → Default
- D. 우선순위 숫자가 작은 프로파일

**정답: C**
**해설:** 매번 **Fixed-date → Recurrence → Default** 순으로 평가하고, 처음 맞는 것 **하나만** 적용합니다. 블랙프라이데이 Fixed-date가 있으면 그날 Recurrence/Default는 쓰이지 않습니다.

---

### 문제 10
VMSS 오토스케일에서 Default 용량이 3, 최솟값 2, 최댓값 10입니다. 메트릭을 읽지 못하는 동안 현재 인스턴스가 2대입니다. 오토스케일은 무엇을 합니까?

- A. 최댓값 10까지 바로 확장한다
- B. 현재가 Default보다 작으므로 Default(3)까지 확장한다
- C. 아무 동작도 하지 않는다
- D. 최솟값 2로 축소한다

**정답: B**
**해설:** 메트릭을 못 읽을 때, **현재 용량 < Default**이면 Default까지 확장해 가용성을 지킵니다. 이미 Default 이상이면 축소하지 않습니다.

---

### 문제 11
Premium SSD 데이터 디스크 성능 계층을 P15에서 P20으로 올리려 합니다. VM을 중지해야 합니까?

- A. 예. 디스크 크기 변경과 동일하게 할당 취소가 필요하다
- B. 아니요. 실행 중에도 성능 계층만 변경할 수 있다
- C. 예. 디스크를 분리한 뒤에만 가능하다
- D. 아니요. 단 Ultra Disk로 변환해야 한다

**정답: B**
**해설:** Premium SSD는 **성능 계층(P-tier)** 을 VM 실행 중에 바꿀 수 있습니다. 디스크 크기를 키우는 것과 다릅니다. 크기 증가는 별도 작업입니다.

---

### 문제 12
Windows Server 장애 조치 클러스터(WSFC)가 여러 VM에서 **같은 관리 디스크**를 동시에 사용해야 합니다. 무엇을 사용해야 합니까?

- A. Azure Files 스냅샷
- B. Azure Shared Disk (공유 디스크)
- C. 각 VM에 동일한 VHD를 복사
- D. Storage 계정 파일 공유를 OS 디스크로 탑재

**정답: B**
**해설:** **Azure shared disks**는 SCSI PR을 사용해 여러 VM이 한 관리 디스크를 클러스터 스토리지로 씁니다. Premium SSD/Ultra 등 지원 SKU가 있습니다. 단순 VHD 복사나 Azure Files는 클러스터 공유 디스크가 아닙니다.

---

### 문제 13
컨테이너를 돌려야 합니다. 클러스터를 관리하고 싶지 않고, HTTP API가 없을 때는 인스턴스를 0으로 줄이며, 인그레스·리비전이 필요합니다. 무엇을 고릅니까?

- A. Azure Container Instances
- B. Azure Container Apps
- C. Azure Kubernetes Service
- D. Azure Batch만

**정답: B**
**해설:** **Container Apps**는 서버리스 컨테이너 앱 플랫폼입니다. 스케일 투 제로, HTTP 인그레스, 리비전이 있습니다. ACI는 오케스트레이션 없는 단기 컨테이너입니다. AKS는 Kubernetes API가 필요할 때입니다.

---

## 2) 스토리지: AzCopy, 개체 복제, 암호화

### 문제 14
수백 GB Blob을 스토리지 계정 A에서 B로 복사합니다. 데이터는 클라이언트 PC를 거치지 않고 Azure 내부에서 이동해야 하며, 스크립트로 반복해야 합니다. 무엇을 사용해야 합니까?

- A. Azure Storage Explorer GUI로 드래그
- B. AzCopy (서버 간 복사)
- C. 포털에서 Blob 다운로드 후 다시 업로드
- D. Robocopy

**정답: B**
**해설:** **AzCopy**는 원본·대상이 모두 Azure이면 **서버 간 복사**를 합니다. 트래픽이 PC를 거치지 않습니다. Storage Explorer는 탐색·소량 작업에 적합하고, 대량 자동화는 AzCopy입니다.

---

### 문제 15
AzCopy로 복사할 때 액세스 키를 스크립트에 넣고 싶지 않습니다. Microsoft Entra ID로 인증하려면 먼저 무엇을 합니까?

- A. `azcopy login` 후 적절한 RBAC(예: Storage Blob Data Contributor)가 있는지 확인
- B. 스토리지 계정 키를 환경 변수에만 넣는다
- C. 계정 SAS를 URL에 붙인다
- D. 공용 익명 액세스를 켠다

**정답: A**
**해설:** Entra ID 인증은 `azcopy login`(또는 관리 ID/서비스 주체)입니다. 데이터 평면 역할이 필요합니다. 계정 키·SAS도 가능하지만 “키를 넣지 않겠다”는 요구와 맞지 않습니다.

---

### 문제 16
원본 컨테이너와 대상 컨테이너를 주기적으로 **한 방향 동기화**하고, 원본에서 지운 파일은 대상에서도 지우려 합니다. AzCopy 하위 명령은 무엇입니까?

- A. `azcopy copy`
- B. `azcopy sync`
- C. `azcopy make`
- D. `azcopy jobs`

**정답: B**
**해설:** `azcopy sync`는 한 방향 동기화입니다. `--delete-destination`으로 대상의 고아 개체를 지울 수 있습니다. `copy`는 복사만 하고 삭제 동기화는 하지 않습니다.

---

### 문제 17
Blob **개체 복제(object replication)** 를 구성하려 합니다. 원본 스토리지 계정에서 반드시 켜야 하는 두 기능은 무엇입니까?

- A. 계층적 네임스페이스와 NFS 3.0
- B. Blob 버전 관리와 변경 피드(change feed)
- C. 정적 웹사이트와 Azure CDN
- D. 고객 관리 키와 불변 정책만

**정답: B**
**해설:** 개체 복제는 원본에 **Blob versioning**과 **change feed**, 대상에 **versioning**이 필요합니다. 복제 정책은 원본 계정에 만듭니다. HNS/NFS는 이 기능의 전제 조건이 아닙니다.

---

### 문제 18
개체 복제에 대한 올바른 설명은 무엇입니까?

- A. 동기 복제라 RPO는 0이다
- B. 블록 Blob을 비동기로 복제하며, 대상은 잠시 뒤처질 수 있다
- C. 페이지 Blob(디스크 VHD) 전용 기능이다
- D. LRS 계정에서는 사용할 수 없다

**정답: B**
**해설:** 개체 복제는 **비동기**입니다. 블록 Blob용입니다. 페이지 Blob이 아닙니다. LRS여도 계정 간 복제는 가능합니다. GRS와 다른 기능입니다. GRS는 Microsoft가 페어 리전에 복사하고, 개체 복제는 **내가 고른 계정/컨테이너**로 복사합니다.

---

### 문제 19
스토리지 계정 암호화에 **고객 관리 키(CMK)** 를 쓰려 합니다. 키는 어디에 두어야 합니까?

- A. 스토리지 계정 액세스 키와 같은 위치
- B. Azure Key Vault 또는 Managed HSM
- C. VM의 인증서 저장소
- D. Recovery Services vault

**정답: B**
**해설:** 스토리지 SSE의 CMK는 **Key Vault 또는 Azure Key Vault Managed HSM**의 키입니다. 액세스 키(계정 키)와 혼동하지 마세요. 계정 키는 인증용입니다.

---

### 문제 20
실수로 덮어쓴 Blob을 **이전 내용**으로 되돌리려 합니다. 일시 삭제만 켜져 있고 버전 관리는 꺼져 있습니다. 덮어쓴 Blob을 이전 버전으로 되돌릴 수 있습니까?

- A. 예. 일시 삭제가 덮어쓰기도 보존한다
- B. 아니요. 덮어쓰기는 버전 관리가 필요하고, 일시 삭제는 삭제된 Blob/버전 복구용이다
- C. 예. 수명 주기 관리가 이전 계층을 보관한다
- D. 아니요. 스냅샷만 덮어쓰기를 되돌린다. 버전 관리는 관련 없다

**정답: B**
**해설:** **일시 삭제**는 삭제된 Blob(및 버전)을 보존합니다. **덮어쓰기**로 바뀐 내용은 **버전 관리**(또는 수동 스냅샷)가 있어야 이전 상태가 남습니다. 수명 주기는 계층 이동/삭제 정책입니다.

---

## 3) 네트워킹

### 문제 21
서브넷 NSG와 NIC NSG가 모두 있습니다. NIC의 **유효 보안 규칙(Effective security rules)** 은 무엇을 보여 줍니까?

- A. NIC NSG만
- B. 서브넷 NSG만
- C. 서브넷 NSG와 NIC NSG가 **합쳐진** 실제 적용 규칙
- D. Azure Firewall 규칙만

**정답: C**
**해설:** 패킷은 서브넷 NSG와 NIC NSG **둘 다**를 통과해야 합니다. 한쪽이 Deny면 차단됩니다. 유효 보안 규칙은 그 결과를 NIC 기준으로 보여 줍니다. 문제 해결 시 여기부터 봅니다.

---

### 문제 22
같은 NSG에 인바운드 규칙이 두 개입니다. 우선순위 100: Allow TCP 443, 우선순위 200: Deny TCP 443. 443 트래픽은 어떻게 됩니까?

- A. Deny가 나중에 평가되므로 거부된다
- B. 숫자 작은 우선순위가 먼저이므로 Allow가 적용되어 허용된다
- C. 둘 다 맞으면 Deny가 항상 이긴다
- D. ASG가 없으면 규칙이 무시된다

**정답: B**
**해설:** NSG는 **우선순위 숫자가 작을수록 먼저** 적용되고, 첫 일치에서 멈춥니다. 100 Allow가 200 Deny보다 앞섭니다. “Deny가 항상 이긴다”는 유효 규칙 합치기(서로 다른 NSG)와 혼동한 것입니다.

---

### 문제 23
여러 NIC에 같은 NSG 규칙을 반복하지 않고, “웹 서버” 집합에만 80/443을 허용하려 합니다. 무엇을 사용해야 합니까?

- A. Application Security Group (ASG)
- B. Private DNS 영역
- C. NAT Gateway
- D. 서비스 엔드포인트

**정답: A**
**해설:** **ASG**는 NIC를 논리 그룹으로 묶어 NSG 원본/대상에 씁니다. IP가 바뀌어도 그룹만 유지하면 됩니다.

---

### 문제 24
VNet 안 VM들이 `vm1.corp.internal`처럼 **사설 DNS 이름**으로 서로를 해석해야 합니다. Azure 제공 DNS(168.63.129.16)만 쓰면 부족합니다. 최소 구성은 무엇입니까?

- A. 공용 Azure DNS 영역에 A 레코드 만들기
- B. Private DNS 영역 `corp.internal`을 만들고 VNet과 연결한 뒤 자동 등록을 사용한다
- C. 각 VM hosts 파일만 수정
- D. Traffic Manager 프로필

**정답: B**
**해설:** **Azure Private DNS** 영역을 VNet에 링크하고 **자동 등록(auto-registration)** 을 켜면 VM이 해당 영역에 A 레코드를 등록합니다. 한 VNet에서 자동 등록은 영역 하나에만 켤 수 있습니다. 공용 DNS는 인터넷 이름입니다.

---

### 문제 25
스토리지 계정에 **프라이빗 엔드포인트**를 만들었습니다. VNet 안 VM이 Blob FQDN을 **프라이빗 IP**로 해석하려면 무엇을 구성해야 합니까?

- A. 서비스 엔드포인트 Microsoft.Storage만
- B. `privatelink.blob.core.windows.net` Private DNS 영역과 VNet 링크(보통 자동 생성되는 DNS 구성)
- C. 스토리지 계정 방화벽에 0.0.0.0/0 허용
- D. NSG에서 443 아웃바운드 Deny

**정답: B**
**해설:** 프라이빗 엔드포인트는 NIC에 사설 IP를 줍니다. 이름 해석은 **Private DNS**(일반적으로 `privatelink.<서비스>.core.windows.net`)가 공인 FQDN을 그 IP로 보냅니다. 서비스 엔드포인트는 사설 IP를 만들지 않습니다.

---

### 문제 26
서비스 엔드포인트와 프라이빗 엔드포인트의 차이로 올바른 것은 무엇입니까?

- A. 둘 다 PaaS에 사설 IP를 할당한다
- B. 서비스 엔드포인트는 서브넷에서 Azure 백본으로 가지만 PaaS는 여전히 **공인 엔드포인트**이고, 프라이빗 엔드포인트는 VNet **사설 IP**로 PaaS를 노출한다
- C. 프라이빗 엔드포인트는 인터넷 전용이다
- D. 서비스 엔드포인트는 온프레에서만 동작한다

**정답: B**
**해설:** 서비스 엔드포인트는 서브넷 ID로 PaaS 방화벽을 잠그고 트래픽을 Azure 백본으로 붙입니다. 대상 주소는 공인입니다. 프라이빗 엔드포인트는 Private Link로 **내 VNet IP**가 대상입니다.

---

### 문제 27
Standard Load Balancer와 함께 쓸 공인 IP SKU는 무엇입니까? (2025년 이후 Basic 공인 IP는 사용 종료)

- A. Basic만
- B. Standard
- C. Gateway SKU
- D. Premium Front Door SKU

**정답: B**
**해설:** Standard LB에는 **Standard 공인 IP**가 필요합니다. Standard IP는 정적, NSG로 잠글 수 있고, 영역 중복을 고를 수 있습니다. Basic 공인 IP는 종료되었습니다.

---

### 문제 28
두 VM 사이 연결이 간헐적으로 실패합니다. **지속적으로** RTT·실패율을 측정하고 알림을 받으려면 Network Watcher에서 무엇을 사용해야 합니까?

- A. IP flow verify
- B. Next hop
- C. Connection Monitor
- D. Packet capture 한 번

**정답: C**
**해설:** **Connection Monitor**는 엔드포인트 쌍을 계속 검사합니다. IP flow verify는 NSG가 특정 5-tuple을 허용/거부하는 **일회** 점검입니다. Next hop은 라우팅 한 건입니다. Packet capture는 패킷 덤프입니다.

---

### 문제 29
“이 NSG가 이 원본 IP → 대상 IP:포트 패킷을 허용하는가?”를 **지금 한 번** 확인하고 싶습니다. 무엇을 사용해야 합니까?

- A. Connection Monitor
- B. IP flow verify
- C. Azure Advisor
- D. Service Health

**정답: B**
**해설:** **IP flow verify**가 그 질문입니다. Connection Monitor는 지속 모니터링입니다.

---

### 문제 30
서브넷에 UDR을 추가해 `0.0.0.0/0`을 NVA로 보냅니다. 같은 접두사일 때 우선순위는 무엇입니까?

- A. 시스템 경로 > BGP > UDR
- B. BGP > UDR > 시스템 경로
- C. UDR > BGP > 시스템 경로
- D. 가장 나중에 만든 경로

**정답: C**
**해설:** 접두사가 같으면 **UDR > BGP > 시스템 경로**입니다. 더 구체적인 접두사(긴 마스크)가 그보다 앞섭니다.

---

## 4) 모니터링 및 백업

### 문제 31
Azure **관리 디스크**만 백업하려고 합니다. 어떤 vault를 만들어야 합니까?

- A. Recovery Services vault
- B. Backup vault
- C. Key Vault
- D. Storage 계정 스냅샷만으로는 vault가 필요 없고, Disk Backup도 vault를 안 쓴다

**정답: B**
**해설:** **Backup vault**는 Azure Disks, Blobs, PostgreSQL, AKS 등 신규 워크로드용입니다. **Recovery Services vault**는 Azure VM, Azure Files, VM 위 SQL/HANA, MARS입니다. 디스크만이면 Backup vault입니다.

---

### 문제 32
Azure **VM 통째**를 백업합니다. 어느 vault입니까?

- A. Backup vault
- B. Recovery Services vault
- C. 둘 다 동일하므로 아무거나
- D. Log Analytics 작업 영역

**정답: B**
**해설:** IaaS VM 백업은 **Recovery Services vault**입니다. Backup vault에 VM을 넣지 않습니다.

---

### 문제 33
리전 장애 시 **몇 분 수준의 RPO**로 VM을 보조 리전에서 켜고, 장애 조치(failover) 후 커밋·다시 보호가 필요합니다. 백업 CRR로 충분합니까?

- A. 예. CRR이 ASR을 대체한다
- B. 아니요. 그건 Azure Site Recovery이다. CRR은 백업 시점을 페어 리전에 복원하는 것이다
- C. 예. GRS vault면 자동 failover가 된다
- D. 아니요. 유일한 방법은 Resource Mover이다

**정답: B**
**해설:** **ASR**은 복제 + failover + 커밋 + 다시 보호입니다. **CRR**은 Azure Backup의 페어 리전 복원이며 RPO가 수 시간대일 수 있습니다. GRS만으로는 자동 장애 조치가 없습니다.

---

### 문제 34
Recovery Services vault에서 Cross Region Restore를 쓰려면 백업 구성에서 무엇을 고릅니까?

- A. Storage replication = RA-GRS를 드롭다운에서 직접 선택
- B. Storage replication = **GRS**, 그다음 **Cross Region Restore 사용**
- C. Storage replication = LRS + CRR
- D. Storage replication = ZRS + RA-GZRS

**정답: B**
**해설:** vault 선택지는 **LRS / ZRS / GRS**입니다. RA-GRS 항목은 없습니다. GRS를 고르고 CRR을 켜면 뒤에서 RA-GRS가 됩니다. LRS/ZRS에서는 CRR이 없습니다.

---

### 문제 35
Azure Backup **보고서**로 백업 성공/실패 추세를 보려 합니다. 보통 먼저 무엇을 합니까?

- A. 각 VM에 Network Watcher 에이전트만 설치
- B. vault 진단 설정을 Log Analytics 작업 영역으로 보내고 Backup Reports(통합 문서)를 사용
- C. Storage Explorer에서 vault를 연다
- D. Cost Management 예산만 만든다

**정답: B**
**해설:** 백업 보고서는 **Log Analytics**에 진단(또는 보고서용 설정)을 보낸 뒤 Backup center/통합 문서로 봅니다. 보고서는 기본 vault 화면만으로는 부족합니다.

---

### 문제 36
토요일 02:00–06:00 유지 보수 동안 CPU 경고 **알림 메일**을 끄고 싶습니다. 경고 규칙 자체는 남겨 두고 포털에서 발생한 경고도 볼 수 있어야 합니다. 무엇을 사용해야 합니까?

- A. 경고 규칙을 삭제했다가 다시 만든다
- B. 경고 처리 규칙(Alert processing rule)으로 해당 시간에 알림을 억제
- C. 메트릭을 삭제한다
- D. NSG로 메트릭을 차단한다

**정답: B**
**해설:** **Alert processing rules**는 알림/액션을 억제하거나 액션 그룹을 추가합니다. 경고는 계속 발생·기록됩니다. 규칙을 지우는 것이 아닙니다.

---

### 문제 37
메트릭 경고가 발생하면 메일과 웹후크를 보내려 합니다. 알림의 **대상(메일, SMS, 웹후크, ITSM)** 을 정의하는 리소스는 무엇입니까?

- A. 경고 처리 규칙만
- B. 액션 그룹(Action group)
- C. Azure Policy
- D. Budget 연락처 역할만

**정답: B**
**해설:** **Action group**이 누구에게/어떻게 알릴지입니다. 경고 규칙은 조건, 액션 그룹은 통지, 처리 규칙은 그 통지를 억제·추가합니다.

---

### 문제 38
Log Analytics에서 최근 1시간 Heartbeat가 있는 컴퓨터를 보려 합니다. 올바른 KQL은 무엇입니까?

- A. `SELECT * FROM Heartbeat WHERE TimeGenerated > NOW()-1h`
- B. `Heartbeat | where TimeGenerated > ago(1h) | summarize by Computer`
- C. `Get-Heartbeat -LastHours 1`
- D. `az monitor heartbeat list --hours 1`

**정답: B**
**해설:** Azure Monitor 로그는 **KQL**입니다. SQL이 아닙니다. `ago(1h)`가 최근 1시간입니다.

---

### 문제 39
스토리지 계정 진단에서 로그와 메트릭을 보내려 합니다. 지원되는 대상이 **아닌** 것은 무엇입니까?

- A. Log Analytics 작업 영역
- B. 스토리지 계정
- C. Event Hubs
- D. Azure Front Door 프로필

**정답: D**
**해설:** 진단 설정 대상은 보통 **Log Analytics, 스토리지 계정, Event Hubs**(및 파트너 솔루션)입니다. Front Door 프로필은 진단 **대상**이 아닙니다.

---

### 문제 40
ASR로 보조 리전에 장애 조치를 테스트한 뒤, 프로덕션 failover를 확정했습니다. 나중에 원래 리전으로 되돌리려면 failover 이후 반드시 무엇을 해야 합니까?

- A. vault를 LRS로 바꾼다
- B. **다시 보호(reprotect)** 로 복제 방향을 바꾼 다음 failback
- C. 커밋을 취소한다
- D. Connection Monitor만 다시 만든다

**정답: B**
**해설:** **Commit**은 failover를 확정하고 기존 복구 지점을 지우며 되돌릴 수 없습니다. 원래 리전으로 돌아가려면 **재보호**로 반대 방향 복제를 만든 뒤 failback합니다. Commit 취소는 없습니다.

---

## 빠른 정답표

| 문제 | 정답 | 문제 | 정답 |
|---|---|---|---|
| 1 | B | 21 | C |
| 2 | B | 22 | B |
| 3 | C | 23 | A |
| 4 | B | 24 | B |
| 5 | A | 25 | B |
| 6 | B | 26 | B |
| 7 | B | 27 | B |
| 8 | B | 28 | C |
| 9 | C | 29 | B |
| 10 | B | 30 | C |
| 11 | B | 31 | B |
| 12 | B | 32 | B |
| 13 | B | 33 | B |
| 14 | B | 34 | B |
| 15 | A | 35 | B |
| 16 | B | 36 | B |
| 17 | B | 37 | B |
| 18 | B | 38 | B |
| 19 | B | 39 | D |
| 20 | B | 40 | B |
