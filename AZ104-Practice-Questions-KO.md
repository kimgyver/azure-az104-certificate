# AZ-104 예상 문제 (세트 2) - 한국어

새로운 50문제 세트입니다. 각 문항은 정답과 짧은 해설을 포함합니다.

---

## 1) ID 및 거버넌스

### 문제 1
팀이 VM은 관리해야 하지만, 다른 사람에게 권한 부여는 못 하게 해야 합니다.
- A. Owner
- B. Contributor
- C. User Access Administrator
- D. Reader

**정답: B**
**해설:** Contributor는 리소스 관리는 가능하지만 RBAC 역할 할당은 불가합니다.

### 문제 2
모든 리소스에 `Environment` 태그를 강제하려면?
- A. RBAC
- B. 리소스 잠금
- C. Azure Policy
- D. 관리 그룹

**정답: C**
**해설:** 필수 태그 같은 거버넌스 규칙은 Azure Policy가 담당합니다.

### 문제 3
전체 리소스 관리 권한 없이 RBAC 할당만 가능한 역할은?
- A. Contributor
- B. Reader
- C. User Access Administrator
- D. Virtual Machine Contributor

**정답: C**
**해설:** User Access Administrator는 액세스 관리 전용 역할입니다.

### 문제 4
삭제는 막고 수정은 허용하려는 잠금은?
- A. ReadOnly lock
- B. Delete lock
- C. CanNotModify lock
- D. Audit lock

**정답: B**
**해설:** Delete lock은 삭제만 차단합니다.

### 문제 5
Azure Policy 할당 가능한 범위는?
- A. 테넌트만
- B. 구독만
- C. 리소스 그룹만
- D. 관리 그룹/구독/리소스 그룹/리소스

**정답: D**
**해설:** 정책은 여러 스코프에서 할당 가능합니다.

### 문제 6
여러 구독을 계층 구조로 묶어 중앙 거버넌스를 하려면?
- A. 리소스 그룹
- B. 관리 그룹
- C. 태그
- D. 가용성 집합

**정답: B**
**해설:** 구독 계층 관리는 관리 그룹이 핵심입니다.

### 문제 7
온프레미스 AD로 SSPR 비밀번호 쓰기 저장을 하려면?
- A. Azure AD Free
- B. Azure AD Premium P1/P2
- C. Office 365 E1만
- D. 라이선스 불필요

**정답: B**
**해설:** Password Writeback은 Premium P1/P2가 필요합니다.

### 문제 8
VM이 Key Vault에 비밀값 없이 접근하도록 하는 가장 좋은 방식은?
- A. 스크립트에 클라이언트 시크릿 저장
- B. 관리 ID(Managed Identity)
- C. 로컬 관리자 계정
- D. 계정 키 공유

**정답: B**
**해설:** 관리 ID는 코드 내 자격 증명 하드코딩을 없앱니다.

### 문제 9
구독 전체에 최소 권한 읽기 전용 접근을 주려면?
- A. Reader를 구독 범위에 할당
- B. Contributor를 리소스 그룹 범위에 할당
- C. Owner를 구독 범위에 할당
- D. User Access Administrator

**정답: A**
**해설:** Reader는 조회 전용 권한입니다.

### 문제 10
관리 그룹 최대 깊이(루트 제외)는?
- A. 4
- B. 6
- C. 8
- D. 10

**정답: B**
**해설:** 루트를 제외하고 6단계까지 가능합니다.

---

## 2) 스토리지

### 문제 11
장애 시 보조 지역에서 읽기도 하려면?
- A. LRS
- B. GRS
- C. RA-GRS
- D. ZRS

**정답: C**
**해설:** RA-GRS는 보조 엔드포인트 읽기를 제공합니다.

### 문제 12
일반적으로 가장 안전한 SAS 유형은?
- A. Account SAS
- B. Service SAS
- C. User Delegation SAS
- D. 모두 동일

**정답: C**
**해설:** Microsoft Entra 자격 증명 기반 서명이라 보안성이 높습니다.

### 문제 13
계정 키 교체 없이 URL 접근을 중앙에서 빠르게 철회하려면?
- A. Ad-hoc SAS
- B. Stored Access Policy + Service SAS
- C. 퍼블릭 액세스
- D. 계정 키 공유

**정답: B**
**해설:** 저장된 액세스 정책으로 SAS 권한/만료를 중앙 통제할 수 있습니다.

### 문제 14
거의 접근하지 않고 리하이드레이션 지연 허용 가능 데이터 계층은?
- A. Hot
- B. Cool
- C. Archive
- D. Premium

**정답: C**
**해설:** Archive는 장기 저빈도 데이터에 최적입니다.

### 문제 15
Blob Cool 최소 보존 기간은?
- A. 7일
- B. 30일
- C. 90일
- D. 180일

**정답: B**
**해설:** Cool 계층은 최소 30일 보존이 적용됩니다.

### 문제 16
Blob Archive 최소 보존 기간은?
- A. 30일
- B. 90일
- C. 120일
- D. 180일

**정답: D**
**해설:** Archive 계층 최소 보존은 180일입니다.

### 문제 17
온프레미스 70TB+를 저대역폭 환경에서 이전하려면?
- A. AzCopy
- B. Azure File Sync
- C. Azure Data Box
- D. FTP

**정답: C**
**해설:** 대용량 오프라인 이전은 Data Box가 적합합니다.

### 문제 18
대부분 시나리오에서 기본 추천 스토리지 계정 종류는?
- A. Storage (GPv1)
- B. BlobStorage
- C. StorageV2 (GPv2)
- D. Premium BlockBlob만

**정답: C**
**해설:** 최신 기능과 범용성 때문에 GPv2가 기본 추천입니다.

### 문제 19
Azure에서 SMB 파일 공유가 필요할 때는?
- A. Blob 컨테이너
- B. Azure Files
- C. Managed Disk
- D. Queue

**정답: B**
**해설:** Azure Files가 SMB/NFS 파일 공유를 제공합니다.

### 문제 20
스토리지 계정 기본 액세스 키 개수는?
- A. 1
- B. 2
- C. 3
- D. 4

**정답: B**
**해설:** 무중단 키 교체를 위해 2개 키를 제공합니다.

---

## 3) 컴퓨팅

### 문제 21
계획된 유지보수 중에도 2대 VM 가용성을 유지하려면?
- A. Availability Set
- B. Backup Vault
- C. ASR만 구성
- D. Auto-shutdown

**정답: A**
**해설:** 가용성 집합은 업데이트/장애 도메인 분산을 제공합니다.

### 문제 22
기존 VM에 NIC 추가 전 먼저 해야 할 작업은?
- A. VM 리사이즈
- B. VM 중지/할당 해제
- C. 게스트 OS 재부팅
- D. 부트 진단 활성화

**정답: B**
**해설:** NIC 변경은 Deallocate 상태에서만 가능합니다.

### 문제 23
고객 관리 키 디스크 암호화에 Key Vault 외에 필요한 것은?
- A. NSG
- B. Disk Encryption Set
- C. Recovery Vault
- D. Route Table

**정답: B**
**해설:** DES가 디스크와 Key Vault 키를 연결합니다.

### 문제 24
반복 가능한 Azure 인프라 배포 도구는?
- A. ARM/Bicep
- B. 포털 클릭만
- C. RDP 스크립트만
- D. 수동 런북만

**정답: A**
**해설:** ARM/Bicep은 선언형 IaC 방식입니다.

### 문제 25
Custom Script Extension의 주요 목적은?
- A. 자동 스케일
- B. VM 배포 후 스크립트 실행
- C. 디스크 암호화
- D. OS 교체

**정답: B**
**해설:** 설치/초기 구성 스크립트를 VM에서 실행합니다.

### 문제 26
VM Scale Set 기본 축소(Scale-in) 정책은?
- A. 가장 오래된 인스턴스
- B. 가장 최신 인스턴스
- C. 인스턴스 ID가 가장 큰 인스턴스
- D. CPU가 가장 낮은 인스턴스

**정답: C**
**해설:** 기본은 highest instance ID 제거입니다.

### 문제 27
가용성 집합 SLA(2개 이상 VM)는?
- A. 99.9%
- B. 99.95%
- C. 99.99%
- D. 99.999%

**정답: B**
**해설:** Availability Set SLA는 99.95%입니다.

### 문제 28
가용성 영역 SLA(2개 이상 영역)는?
- A. 99.95%
- B. 99.97%
- C. 99.99%
- D. SLA 없음

**정답: C**
**해설:** 영역 분산 배포 시 99.99% 목표가 가능합니다.

### 문제 29
CPU 기반 자동 확장이 필요한 서비스는?
- A. Availability Set
- B. VM Scale Set
- C. Recovery Services Vault
- D. Azure Policy

**정답: B**
**해설:** VMSS가 자동 확장을 제공합니다.

### 문제 30
기존 VM 상태 기반으로 새 VM 만드는 일반 절차는?
- A. Snapshot -> Managed Disk -> Create VM
- B. NIC 내보내기
- C. NSG 복제
- D. 원본 VM 재시작

**정답: A**
**해설:** 스냅샷-디스크-새 VM 흐름이 표준입니다.

---

## 4) 네트워킹

### 문제 31
게이트웨이 서브넷의 필수 이름은?
- A. AzureGateway
- B. VNetGatewaySubnet
- C. GatewaySubnet
- D. 자유 이름

**정답: C**
**해설:** 이름은 고정값 GatewaySubnet입니다.

### 문제 32
게이트웨이 서브넷 권장 크기는?
- A. /30
- B. /29
- C. /27 또는 /28
- D. /32

**정답: C**
**해설:** 확장성을 고려해 /27 또는 /28 권장입니다.

### 문제 33
다중 S2S + P2S를 지원하는 VPN 게이트웨이 유형은?
- A. Policy-based
- B. Route-based
- C. Basic 전용
- D. ExpressRoute 전용

**정답: B**
**해설:** Route-based가 기능 범위가 넓습니다.

### 문제 34
온프레미스 VPN 장치 정보를 Azure에서 표현하는 리소스는?
- A. Virtual Network Gateway
- B. Local Network Gateway
- C. Application Gateway
- D. Traffic Manager

**정답: B**
**해설:** LNG가 상대편 공인 IP/주소 대역을 보관합니다.

### 문제 35
vNet 피어링 통신을 위해 필요한 것은?
- A. 단방향 피어링만
- B. 양방향 구성
- C. VPN 게이트웨이 필수
- D. CIDR 중첩

**정답: B**
**해설:** 양쪽 vNet에서 피어링을 구성해야 합니다.

### 문제 36
NSG 우선순위 규칙은?
- A. 숫자가 큰 규칙 우선
- B. 숫자가 작은 규칙 우선
- C. 나중에 만든 규칙 우선
- D. Deny가 항상 무조건 우선

**정답: B**
**해설:** 우선순위 숫자가 낮을수록 먼저 평가됩니다.

### 문제 37
특정 트래픽이 NSG에서 허용/차단되는지 점검하는 도구는?
- A. Packet Capture
- B. Connection Monitor
- C. IP Flow Verify
- D. Topology

**정답: C**
**해설:** 매칭된 규칙과 허용/차단 결과를 확인할 수 있습니다.

### 문제 38
대상까지의 실제 라우팅 경로 판단 도구는?
- A. Effective Security Rules
- B. Next Hop
- C. NSG Flow Logs
- D. DDoS Dashboard

**정답: B**
**해설:** Next Hop이 유효 라우팅 결정을 보여줍니다.

### 문제 39
대상 서비스에 private IP를 붙이지 않고 PaaS를 사설 경로로 접근하려면?
- A. Private Endpoint
- B. Service Endpoint
- C. vNet Peering
- D. VPN Gateway

**정답: B**
**해설:** Service Endpoint는 Microsoft 백본 경로를 사용합니다.

### 문제 40
VM 인터넷 불가 시 NSG 외에 확인할 항목은?
- A. 공인 IP/NAT 경로
- B. NIC NSG
- C. UDR 라우팅 테이블
- D. 모두

**정답: D**
**해설:** 보안 규칙, 라우팅, 출구 경로를 함께 봐야 합니다.

---

## 5) LB, DNS, DR, 모니터링

### 문제 41
Standard Load Balancer 백엔드 VM에 Basic 공인 IP가 있으면?
- A. 정상 동작
- B. 호환 불가
- C. 같은 서브넷이면 가능
- D. 재부팅 후 가능

**정답: B**
**해설:** Standard LB는 Standard 공인 IP(또는 공인 IP 없음)만 호환됩니다.

### 문제 42
Standard Load Balancer SLA는?
- A. SLA 없음
- B. 99.9%
- C. 99.95%
- D. 99.99%

**정답: D**
**해설:** Standard LB SLA는 99.99%입니다.

### 문제 43
시험에서 자주 나오는 LB 프로브 관련 IP는?
- A. 10.0.0.1
- B. 168.63.129.16
- C. 169.254.169.254
- D. 8.8.8.8

**정답: B**
**해설:** NSG에서 필요한 포트 허용 여부를 함께 확인해야 합니다.

### 문제 44
TLS 종료 + URL 경로 라우팅이 필요하면?
- A. Standard Load Balancer
- B. Application Gateway
- C. Basic Load Balancer
- D. VPN Gateway

**정답: B**
**해설:** Application Gateway는 L7 HTTP/HTTPS 기능을 제공합니다.

### 문제 45
`contoso.com`을 Azure DNS 권한 네임서버로 만들려면?
- A. A 레코드만 생성
- B. 도메인 등록기관 NS를 Azure DNS NS로 변경
- C. ASR 활성화
- D. Route Table 생성

**정답: B**
**해설:** 등록기관에서 NS 위임을 바꿔야 Azure DNS가 authoritative 됩니다.

### 문제 46
하이브리드에서 Azure VM이 온프레 `.local` 이름을 해석하려면?
- A. 기본 Azure DNS만 사용
- B. vNet DNS 서버를 AD DNS/DC IP로 지정
- C. DDoS Standard 활성화
- D. 공용 CNAME 생성

**정답: B**
**해설:** AD 도메인을 아는 DNS 서버로 질의가 가야 합니다.

### 문제 47
Azure Backup용 Recovery Services Vault 지역은?
- A. 원본 VM과 같은 지역
- B. 아무 지역
- C. DR 대상 지역만
- D. 페어 지역만

**정답: A**
**해설:** 백업 Vault는 원본 VM과 동일 지역이 원칙입니다.

### 문제 48
East US -> West US ASR 복제 시 Vault 위치는?
- A. East US
- B. West US
- C. 아무 지역
- D. 필요 없음

**정답: B**
**해설:** ASR Vault는 복구(대상) 지역에 둡니다.

### 문제 49
ASR 테스트 장애 조치 시 네트워크는?
- A. 운영 복구망만 사용
- B. 대상 지역의 격리 테스트망
- C. 원본 지역 운영망
- D. 네트워크 불필요

**정답: B**
**해설:** 테스트는 운영 영향 차단을 위해 격리망이 정석입니다.

### 문제 50
CPU 80% 초과 5분 시 메일 알림 구성은?
- A. 진단 설정만
- B. 메트릭 경보 + 작업 그룹
- C. NSG 흐름 로그만
- D. 정책 할당만

**정답: B**
**해설:** 경보 규칙과 작업 그룹을 함께 구성해야 알림이 발송됩니다.

---

## 빠른 암기 숫자
- 관리 그룹 깊이: 6 (루트 제외)
- 가용성 집합 SLA: 99.95%
- 가용성 영역 SLA: 99.99%
- Standard Load Balancer SLA: 99.99%
- Cool 최소 보존: 30일
- Archive 최소 보존: 180일
- 게이트웨이 서브넷 이름: GatewaySubnet

최종 업데이트: 2026-05-03
