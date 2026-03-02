## VPC Flow Logs

### 개요

* **복잡한 네트워크 환경에서 장애 진단/트러블슈팅에 필수적인 로그**입니다.
* **패킷 “메타데이터”만** 수집하며 **패킷 본문(payload/content)은 수집하지 않습니다.**

  * 본문이 필요하면 인스턴스에 **패킷 스니퍼(예: tcpdump 등)** 를 설치해 캡처해야 합니다.
* 수집 가능한 메타데이터 예시:

  * **출발지/목적지 IP**, **출발지/목적지 포트**, **패킷/바이트 수**, 기타 외부에서 관측 가능한 정보 등

### 수집 범위(적용 지점)

* **VPC 단위**: 해당 VPC 내 **모든 ENI(네트워크 인터페이스)** 대상
* **Subnet 단위**: 해당 Subnet 내 **모든 ENI** 대상
* **ENI 단위**: 특정 ENI의 트래픽만 대상

### 중요 포인트(EXAM)

* **VPC Flow Logs는 실시간이 아닙니다.**

  * 모니터링 대상 ENI에서 트래픽이 발생한 시점과 **Flow Logs에 반영되는 시점 사이에 지연**이 있습니다. *(시험에서 자주 나오는 함정 포인트)*

### 저장/전송 대상(Destination)

* Flow Logs 출력 대상은 다음 중 선택 가능:

  * **Amazon S3**
  * **CloudWatch Logs**
  * **Kinesis Data Firehose**

### 캡처 필터(수집 조건)

* **ACCEPT 트래픽만**
* **REJECT 트래픽만**
* **ALL(전체)**

---

## Flow Log 레코드 필드 구성

* `<version>`
* `<account-id>`
* `<interface-id>`
* `<srcaddr>`: 출발지 IP
* `<dstaddr>`: 목적지 IP
* `<srcport>`: 출발지 포트 (포트가 없는 프로토콜이면 **0**, 예: ICMP ping)
* `<dstport>`: 목적지 포트 *(원문에 `dscport`는 보통 `dstport` 오타로 보는 게 자연스럽습니다)*
* `<protocol>`: 프로토콜 번호

  * **ICMP = 1**, **TCP = 6**, **UDP = 17** *(시험 암기 포인트)*
* `<packets>`
* `<bytes>`
* `<start>`
* `<end>`
* `<action>`: `ACCEPT` 또는 `REJECT`
* `<log-status>`

---

## 참고(로그에 안 찍히는 트래픽)

VPC Flow Logs는 **모든 트래픽을 기록하지 않습니다.** 예를 들어 아래는 **기록되지 않을 수 있습니다**:

* 인스턴스 메타데이터 IP: **169.254.169.254**
* AWS Time Sync: **169.254.169.123**
* **DHCP**
* **Amazon DNS**
* **Windows 라이선스 관련 트래픽** 등
