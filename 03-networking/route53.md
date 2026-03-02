아래는 **AWS Certified Solutions Architect – Professional(SAP) 관점**에서, 주신 Route 53 / DNS 노트를 **한국어로 정확히 번역**한 버전입니다. (용어는 시험에서 흔히 쓰는 표현으로 맞췄고, 원문 구조는 최대한 유지했습니다.)

---

# Route 53

## DNS 기초

* DNS는 **디스커버리(발견) 서비스**로, 기계가 필요로 하는 정보를 사람이 이해할 수 있는 정보로(또는 그 반대로) 변환한다.
* 예: `www.amazon.com` → `104.98.34.131`
* DNS 데이터베이스는 거대한 **분산(Distributed) 데이터베이스**다.
* DNS는 DNS 리졸버 서버가 네임 서버(NS)에 있는 **존 파일(Zone File)**을 찾아 질의(Query)하여, DNS 이름에 필요한 IP 주소를 얻을 수 있게 한다.

## DNS 용어

* **DNS 클라이언트(DNS Client)**: 고객 PC, 노트북, 태블릿 등 엔드 디바이스를 의미한다.
* **DNS 리졸버(DNS Resolver)**: 디바이스 또는 서버에서 동작하며, DNS 질의를 대신 수행하는 소프트웨어.
* **DNS 존(DNS Zone)**: DNS 데이터베이스의 일부(예: `amazon.com`).
* **존 파일(Zone file)**: 특정 도메인의 DNS 정보를 담는 **실제(물리적) 데이터베이스**.
* **DNS 네임 서버(DNS Name Server)**: 존 파일을 호스팅하는 서버.

## DNS 루트

* DNS는 트리 구조이며, 최상단이 **DNS 루트(Root)**다.
* DNS 루트는 **13개의 특수 네임 서버(루트 서버, DNS Root Servers)**가 호스팅한다.
* **Root hints 파일**: 모든 루트 서버의 주소를 담고 있는 OS 파일.
* **권한(Authority)**: DNS에서 “신뢰되는” 존재(예: root hints 파일).
* **위임(Delegation)**: 신뢰된 권한이 자신의 일부를 다른 엔터티에 위임하여, 그 엔터티가 위임받은 영역에 대해 **권한(Authoritative)**을 갖게 하는 것.

## Route 53 소개

* Route 53은 **관리형 DNS 서비스**다.
* 두 가지 주요 서비스 제공:

  * 도메인 등록(Register domains)
  * 관리형 네임 서버에서 존 파일(Hosted Zone) 호스팅
* 글로벌 서비스이며, 데이터베이스는 전 세계에 분산되고 내구성이 높다.

## Hosted Zone

* “DNS as a Service”
* 존 파일을 생성/관리할 수 있다.
* 존 파일은 AWS 관리형 네임 서버 **4개**에 호스팅된다.
* Hosted Zone은 다음 중 하나:

  * **퍼블릭(Public)**: 공용 인터넷에서 접근 가능, 퍼블릭 DNS 시스템의 일부
  * **프라이빗(Private)**: 하나 이상의 VPC에 연결되어 VPC 내부에서만 사용
* Hosted Zone에는 DNS 레코드(Recordset)를 저장한다.

## DNS 레코드 타입

* **NS(Name Server)**: DNS 위임(Delegation)을 가능하게 한다.
* **A / AAAA 레코드**: 호스트 이름을 IP 주소로 매핑.

  * A: IPv4
  * AAAA: IPv6
* **CNAME(Canonical Name)**: 이름 → 이름 매핑.
  예: `ftp`, `mail`, `www` 등이 서로 다른 서버를 참조하도록 구성 가능.
  CNAME은 **IP로 직접 포인팅 불가**, 오직 다른 이름만 가리킨다.
* **MX 레코드**: 메일 라우팅에 사용. 우선순위(priority)와 값(value)로 구성 가능.
  레코드가 가리키는 도메인 끝에 `.`(닷)을 붙이면 **FQDN(정규화된 전체 도메인명)**으로 취급된다.
* **TXT 레코드**: 도메인에 임의의 텍스트 저장. 도메인 소유권 증명에 자주 사용.

## DNS TTL(Time To Live)

* 레코드가 캐시에 저장되는 시간을 의미한다.
* 리졸버는 TTL(초 단위) 동안 레코드를 저장한다.
* TTL은 트레이드오프:

  * 낮은 TTL: 변경 반영이 빠르지만 질의가 많아짐
  * 높은 TTL: 질의가 줄지만 레코드 변경 시 유연성이 떨어짐

## 퍼블릭 Hosted Zone

* Hosted Zone은 글로벌 DNS 데이터베이스의 특정 구간을 담당하는 DNS DB다.
* Route 53에서 도메인을 등록하면 Hosted Zone이 자동 생성된다. 별도로 Hosted Zone만 생성할 수도 있다(이 경우 외부에서 NS 값을 업데이트해야 함).
* Hosted Zone은 월별 요금이 부과된다.
* Hosted Zone에는 A/AAAA/MX/NS/TXT 등 DNS 레코드를 호스팅한다.
* Hosted Zone은 해당 도메인에 대해 **권한(Authoritative)**을 가진다.
* 퍼블릭 Hosted Zone 생성 시 Route 53은 **퍼블릭 네임 서버 4개**를 할당하고, 그 네임 서버들에 존 파일이 호스팅된다.
* 글로벌 DNS에 연결하려면 NS 레코드를 사용해 이 네임 서버들을 가리키게 해야 한다.
* 외부 등록 도메인도 Route 53 퍼블릭 Hosted Zone을 가리킬 수 있다.

## 프라이빗 Hosted Zone

* 퍼블릭과 유사하지만 **공용 인터넷에서 접근 불가**.
* AWS의 VPC에 연결되며, 기본적으로 같은 계정 내 VPC와 공유된다(크로스 계정 공유도 가능).
* **Split-view(Split Horizon) DNS**: 같은 존 이름으로 퍼블릭/내부용을 분리 구성 가능.
  프라이빗 네트워크에서 내부 시스템에 접근할 때 공용 인터넷을 거치지 않도록 하는 데 유용.

## CNAME vs Alias 레코드

* CNAME의 문제:

  * A 레코드: 이름 → IP
  * CNAME: 이름 → 이름
  * **APEX(루트/네이키드 도메인)**에는 CNAME을 둘 수 없다.
  * 많은 AWS 서비스는 DNS 이름(예: ELB)을 사용한다.
* APEX 도메인이 다른 도메인을 가리켜야 한다면 **ALIAS 레코드**를 사용한다.
* ALIAS는 이름을 **AWS 리소스**에 매핑한다.
* APEX/일반 레코드 모두 사용 가능.
* AWS 리소스를 가리키는 ALIAS 질의에는 추가 과금이 없다.
* ALIAS는 “서브타입”으로, 예: A 레코드 ALIAS, CNAME 레코드 ALIAS 형태가 가능하다.

## Simple Routing

* 이름당 1개의 레코드를 생성한다.
* 하나의 레코드에 여러 값을 둘 수 있다.
* 질의 시 모든 값을 클라이언트에 반환한다.
* 클라이언트가 값 중 하나를 선택해 접속한다.
* 제한: **헬스 체크를 지원하지 않음**.

## Health Checks

* 헬스 체크는 Route 53 레코드와 분리된 리소스지만, 레코드에서 이를 사용한다(별도로 생성 후 레코드에 연결).
* 전 세계에 분산된 헬스 체커(fleet)가 수행한다.
* AWS 대상만이 아니라 IP가 있는 모든 엔드포인트가 대상이 될 수 있다.
* 기본 30초마다 검사(10초는 추가 비용).
* TCP / HTTP / HTTPS / HTTP(S)+문자열 매칭 지원.

  * TCP 연결은 4초 내 완료 필요
  * 2xx 또는 3xx를 연결 후 2초 내 반환해야 함
  * 상태 코드 수신 후 응답 본문도 2초 내 수신해야 함
* 문자열 매칭은 응답 본문 **처음 5120자** 안에 해당 문자열이 **완전히** 포함되어야 성공.
* 결과로 `Healthy` 또는 `Unhealthy`로 분류된다.
* 헬스 체크 유형 3가지:

  * Endpoint 체크
  * CloudWatch Alarm 체크
  * Calculated(다른 체크를 조합)
* 헬스 체커의 **18% 이상**이 건강하다고 보고하면 대상은 Healthy로 간주된다.

## Failover Routing

* 같은 이름으로 레코드 2개(Primary, Secondary)를 둘 수 있다.
* Primary에 헬스 체크를 수행한다.
* Primary가 실패하면 Secondary 주소를 반환한다.
* Active-Passive 장애조치에 사용한다.

## Multi Value Routing

* Simple + Failover의 혼합에 가깝다.
* 같은 이름으로 여러 레코드를 만들고, 각 레코드에 헬스 체크를 연결할 수 있다.
* 질의 시 최대 8개 레코드를 반환한다. 8개 초과면 무작위로 8개 선택.
* 클라이언트가 하나를 선택해 접속한다.
* 헬스 체크 실패 레코드는 반환되지 않는다.
* 실제 로드 밸런서의 대체재는 아니다.

## Weighted Routing

* 간단한 로드 밸런싱 또는 신규 API 버전 테스트(카나리/블루그린)에 사용.
* 레코드별 weight(가중치)를 설정한다.
* 같은 이름의 총 가중치 대비 각 레코드 비율로 응답이 결정된다.
* weight=0이면 해당 레코드는 반환되지 않는다. 단, 모두 0이면 전부 반환된다.
* 헬스 체크와 결합 가능.

  * 주의: 헬스 체크로 unhealthy가 되어도 **총 가중치 계산에서 제외되지 않는다**.
  * 선택된 레코드가 unhealthy면 healthy가 선택될 때까지 다시 선택한다.

## Latency-Based Routing

* 성능(사용자 경험) 최적화 목적.
* 레코드마다 리전(region)을 지정한다.
* AWS는 전 세계에서 각 리전까지의 지연(latency) DB를 유지한다.
* 요청이 들어오면 요청 출발지 기준 **가장 낮은 지연**의 대상으로 응답한다.
* 헬스 체크와 결합 가능: 최저 지연 대상이 실패하면 다음 후보를 반환한다.
* 지연 DB는 실시간 업데이트가 아니다.

## Geolocation Routing

* Latency 대신 사용자/리소스의 “지리적 위치”로 결정한다.
* 레코드에 위치를 태깅한다(국가 ISO2, 대륙, default). 미국은 주(state) 단위도 가능.
* 질의 시 IP 기반으로 사용자의 위치를 판단한다.
* 가장 가까운 레코드를 반환하는 것이 아니라, **일치하는(관련 있는) 레코드**를 반환한다.
* 매칭 순서:

  1. 주(미국만)
  2. 국가
  3. 대륙
  4. default
* 매칭이 없으면 `NO ANSWER` 반환.
* 지역 제한 콘텐츠에 적합하며, 위치 기반 로드 밸런싱에도 사용 가능.

## Geoproximity Routing

* 고객과 리소스 간 거리 기반으로 “가까운” 레코드를 반환하려는 목적.
* 규칙 정의:

  * AWS 리소스면 리전
  * 외부 리소스면 위도/경도
  * **Bias(바이어스)**: 거리 계산/영향 범위를 조정
* Bias는 `+` 또는 `-`로 리전 영향 범위를 확대/축소해 라우팅을 유도할 수 있다.

## Route 53 상호 운용(Interoperability)

* Route 53은 도메인 등록기관(Registrar)과 DNS 호스팅(Hosted Zone) 역할을 모두 수행할 수 있다.
* 도메인은 외부 등록기관에서도 등록 가능하다.

**Route 53로 도메인을 등록할 때 발생하는 단계**

* Route 53이 등록 비용을 수납
* 네임 서버 4개 할당
* 네임 서버에 존 파일 생성(호스팅)
* 해당 TLD 레지스트리와 통신하여, 도메인에 4개 NS 주소를 등록

**Route 53이 Registrar만 하는 경우**

* 도메인 비용은 Route 53에 지불하지만, 네임 서버는 다른 업체가 제공
* 해당 네임 서버 정보를 Route 53에 등록하면, Route 53이 TLD 레지스트리에 반영

**Route 53을 호스팅만 쓰는 경우**

* 보통 기존 도메인(외부 등록)을 위해 사용
* Route 53에 Hosted Zone을 만들고, 할당된 NS 주소를 외부 등록기관에 등록한다.

## Route 53에서 DNSSEC 구현

* 콘솔/CLI에서 DNSSEC를 활성화할 수 있다.
* 시작 시 KMS에서 비대칭 키 페어가 필요(생성/사용).
* KMS 키로부터 **KSK(Key-Signing Key)**가 생성되며, Route 53이 공개키/개인키를 사용한다.
* 이 키는 **us-east-1**에 있어야 한다.
* Route 53은 내부적으로 **ZSK(Zone-Signing Key)**를 생성한다.
* Hosted Zone에 **DNSKEY 레코드**로 KSK/ZSK의 공개키 부분을 추가한다. 이는 DNSSEC 리졸버가 서명을 검증할 공개키를 알게 한다.
* KSK 개인키로 DNSKEY 레코드를 서명하여 **RRSIG** 및 DNSKEY 레코드가 생성된다.
* 여기까지가 서명 구성(1단계).
* 이후 상위 존(Parent)과 **신뢰 체인(Chain of trust)**를 설정해야 한다.
* 상위 존은 **DS 레코드**를 추가해야 하며, 이는 KSK 공개키의 해시다.

  * 도메인이 Route 53 등록이면 콘솔/CLI로 자동 처리 가능(적절한 TLD에 DS 등록까지 Route 53이 연계)
  * 외부 등록 도메인이면 DS 레코드를 수동으로 등록해야 한다.
* 완료되면 TLD가 DS를 통해 이 도메인을 신뢰한다.
* 도메인 존은 KSK 또는 ZSK로 각 레코드에 서명한다.
* DNSSEC 활성화 시 CloudWatch 알람 권장:

  * `DNSSECInternalFailure`
  * `DNSSECKeySigningKeyNeedingAction`
  * 둘 다 긴급 대응 필요

## 고급 VPC DNS 및 DNS 엔드포인트

* 모든 VPC에서 `VPC + .2` IP는 DNS 용도로 예약된다.
* 모든 서브넷에서도 `.2`는 Route 53 Resolver 용도로 예약된다.
* 이 주소를 통해 VPC 리소스는 Route 53 퍼블릭 및 연결된 프라이빗 Hosted Zone에 접근할 수 있다.
* Route 53 Resolver는 VPC 내부에서만 접근 가능하므로, 하이브리드(온프렘/클라우드) 통합에서 인바운드/아웃바운드 모두 문제가 생길 수 있다.

**Route 53 엔드포인트(Resolver endpoints)**

* VPC 인터페이스(ENI)로 제공되며, VPN 또는 DX를 통해 접근 가능하다.
* 2가지 타입:

  * Inbound: 온프렘이 Route 53 Resolver로 질의 전달 가능
  * Outbound: 여러 서브넷의 ENI가 온프렘 DNS로 질의 전달

    * 규칙(Rules)로 어떤 질의를 포워딩할지 제어
    * Outbound 엔드포인트는 IP가 할당되며, 온프렘에서 화이트리스트 가능
* 엔드포인트는 서비스로 제공되며 고가용성(HA)이고, 부하에 따라 자동 확장한다.
* 엔드포인트당 약 **초당 10,000 QPS** 처리 가능.
