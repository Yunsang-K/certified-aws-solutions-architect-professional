# CloudFront

* 콘텐츠 전송 네트워크(CDN) 서비스입니다.
* 원본(Origin) 위치에 있는 콘텐츠를 **시청자(Viewer)**에게 더 빠르고 안정적으로 전달하는 것이 목적입니다.
* 이를 위해 **캐싱(Cache)**과 **전 세계 글로벌 네트워크**를 활용합니다.

## CloudFront 용어 및 아키텍처

* **Origin(오리진)**: 콘텐츠의 원본 위치

  * S3 또는 커스텀 오리진(공인 라우팅 가능한 IPv4 주소 등)이 될 수 있습니다.
* **Distribution(배포/디스트리뷰션)**: CloudFront의 구성 단위

  * 설정 대부분이 Distribution 내부(직접 또는 간접)에서 이뤄지며, CloudFront 네트워크로 배포됩니다.
* **Edge Location(엣지 로케이션)**: 전 세계에 분산된 캐시 지점

  * 리전보다 규모는 작지만 개수가 훨씬 많고 더 촘촘히 분포합니다.
  * 정적 데이터 전달에 사용됩니다.
* **Regional Edge Cache(리저널 엣지 캐시)**: Edge Location보다 큰 캐시 계층

  * 개수는 더 적지만 캐시 계층을 하나 더 제공해 효율을 높입니다.
* CloudFront 아키텍처:

  * ![CloudFront Architecture](images/CloudFrontArchitecture1.png)
* **S3 오리진 사용 시**, Edge Location에서 캐시 미스가 나면 “리저널 엣지 캐시”를 사용하지 않습니다.

  * **커스텀 오리진만** 리저널 엣지 캐시를 활용할 수 있습니다.
* **Origin fetch(오리진 페치)**: Edge Location에서 캐시 미스가 발생했을 때 오리진에서 객체를 가져오는 동작
* **Behavior(비헤이비어)**: Distribution 내부의 라우팅/정책 구성 단위

  * 오리진은 비헤이비어에 연결되고, 비헤이비어는 디스트리뷰션에 연결됩니다.
  * ![CloudFront Behavior](images/CloudFrontArchitecture2.png)

## CloudFront Behaviors

* Distribution 레벨에서 설정되는 대표 항목(고수준 옵션)

  * Price Class
  * WAF 연결
  * Alternate Domain Names(CNAME)
  * SSL 인증서 유형
  * SNI 설정
  * Security Policy
  * 지원 HTTP 버전
  * 기타 등등
* 하나의 Distribution은 **1개(기본 비헤이비어)** 또는 **여러 개 비헤이비어**를 가질 수 있습니다.
* 요청은 비헤이비어의 **패턴**에 따라 매칭됩니다.
* 기본 비헤이비어는 와일드카드(`*`) 패턴이며, 더 구체적인 비헤이비어에 매칭되지 않은 모든 요청을 처리합니다.
* 비헤이비어에 매칭된 이후 적용 가능한 주요 설정

  * Origin 또는 Origin Group
  * Viewer Protocol Policy(예: HTTP → HTTPS 리디렉션)
  * 허용 HTTP 메서드
  * Field-Level Encryption
  * 캐시 설정

    * Legacy Cache Settings
    * Cache Policy + Origin Request Policy(AWS 권장)
  * TTL(최소/기본/최대)
  * 비헤이비어 단위 접근 제한(Private)

    * Trusted Key Groups(AWS 권장)
    * Trusted Signer(레거시)
  * 객체 자동 압축(Compress objects automatically)
  * Lambda@Edge 함수 연결

## TTL과 Invalidation

* ![TTL and Invalidations](images/CloudFrontTTL.png)
* Edge Location은 객체가 **TTL 기간 내**라면 “만료되지 않음”으로 간주합니다.
* 캐시 히트가 많을수록 오리진 부하가 감소합니다.
* 기본 TTL(객체 유효 기간)은 **24시간**이며, 비헤이비어에서 정의합니다.
* 최소 TTL, 최대 TTL: 개별 객체 TTL이 가질 수 있는 하한/상한을 지정합니다.
* 오리진이 헤더로 객체별 TTL을 지정할 수 있습니다.

  * `Cache-Control: max-age`(초): 객체 TTL을 초 단위로 지정
  * `Cache-Control: s-maxage`(초): `max-age`와 동일한 의미로 사용
  * `Expires`(날짜/시간): 만료 시각을 지정
* 위 헤더 값이 최소/최대 범위를 벗어나면 **min/max 값으로 보정**됩니다.
* S3 오리진의 경우, **객체 메타데이터**로 캐시 관련 커스텀 헤더를 구성할 수 있습니다.
* Invalidation(캐시 무효화)

  * Distribution에서 수행하며, **모든 Edge Location에 적용**됩니다(전파에 시간 소요).
  * TTL과 무관하게 패턴에 매칭되는 객체를 무효화합니다.
  * 무효화에는 비용이 발생하며, 일반적으로 **무효화 파일 수와 무관하게** 비용 구조가 동일하게 적용됩니다(정책/티어에 따라 상이할 수 있음).
* 무효화 대신 **버전이 포함된 파일명(Versioned file names)**을 고려할 수 있습니다.

  * 브라우저 로컬 캐시 무효화에도 도움
  * 로깅 개선
  * 수동 Invalidation 비용/운영 부담 감소
* **S3 Object Versioning**과 **버전 파일명**은 다른 개념입니다.

## CloudFront와 SSL

* 각 Distribution은 기본 도메인 이름(CNAME)을 가집니다.
* 이 기본 도메인에 HTTPS를 활성화할 수 있습니다.
* CloudFront는 Alternate Domain Names(CNAME)를 지원합니다.
* Alternate Domain Names 추가 과정

  * HTTPS를 쓰면, 해당 도메인과 매칭되는 인증서를 Distribution에 연결해야 합니다.
  * HTTPS를 쓰지 않더라도 도메인 소유 검증이 필요하며, 이를 위해서도 **해당 도메인과 매칭되는 SSL 인증서가 필요**합니다.
  * 결과적으로 **HTTPS 사용 여부와 무관하게** 인증서가 필요합니다.
* 인증서는 ACM(AWS Certificate Manager)로 가져옵니다.
* ACM은 리전 서비스이므로, CloudFront 같은 글로벌 서비스용 인증서는 **us-east-1**에 발급/가져와야 합니다.
* 비헤이비어에서 HTTP/HTTPS 처리 옵션

  * HTTP와 HTTPS 모두 허용
  * HTTP를 HTTPS로 리디렉션
  * HTTPS만 허용(HTTP 요청은 실패)
* CloudFront 통신 구간은 2개로 나뉩니다.

  * Viewer → CloudFront(뷰어 프로토콜)
  * CloudFront → Origin(오리진 프로토콜)
* 두 구간 모두 유효한 퍼블릭 인증서(중간 인증서 포함)가 필요합니다. **Self-signed 인증서는 불가**합니다.
* 오리진이 S3이면 오리진 프로토콜의 인증서를 따로 신경 쓸 필요가 없습니다(S3가 자체적으로 처리).

  * S3 버킷에 별도 인증서를 적용할 수 없습니다.

## CloudFront와 SNI(Server Name Indication)

* 과거에는 HTTPS 사이트마다 전용 IP가 필요했습니다.
* 암호화는 TCP 연결 레벨에서 발생하고, Host 헤더는 L7(HTTP)에서 처리됩니다.
* TLS 암호화는 HTTP 레벨에서 목적지를 판단하기 전에 수행됩니다.
* 2003년에 TLS 확장으로 **SNI**가 도입되어, TLS 핸드셰이크 단계에서 접속하려는 도메인을 전달할 수 있게 됐습니다.
* 이를 통해 **하나의 IP/서버에서 여러 HTTPS 사이트(각자 인증서)**를 호스팅할 수 있습니다.
* 오래된 브라우저는 SNI를 지원하지 않을 수 있어, CloudFront가 전용 IP를 제공해야 하며 추가 비용이 발생합니다.
* CloudFront는

  * SNI 모드(무료)
  * 전용 IP 할당(Distribution당 월 $600)
    을 선택할 수 있습니다.
* CloudFront SSL/SNI 아키텍처:

  * ![SSL/SNI architecture](images/CloudFrontSSLSNI.png)
* S3 오리진은 오리진 프로토콜 인증서가 불필요합니다.

  * ALB/EC2/온프레미스 등 커스텀 오리진은 **오리진 DNS 이름과 매칭되는 퍼블릭 인증서**가 필요합니다.

## 오리진 유형과 구성

* 오리진은 CloudFront가 콘텐츠를 가져오는 원본 위치입니다.
* 캐시 미스 시 오리진 페치가 발생합니다.
* **Origin Group**으로 복원력(Resiliency)을 높일 수 있습니다(그룹을 비헤이비어에 연결).
* 오리진 범주

  * Amazon S3 버킷
  * AWS MediaPackage 채널 엔드포인트
  * AWS MediaStore 컨테이너 엔드포인트
  * 그 외(웹 서버 등): 커스텀 오리진
* S3를 웹사이트 호스팅(정적 웹사이트 엔드포인트)으로 쓰면 CloudFront는 이를 **커스텀 오리진**으로 취급합니다.
* S3 오리진 설정 옵션

  * Origin Path: 버킷 최상위 대신 특정 경로를 오리진으로 사용
  * Origin Access Control(OAC): CloudFront만 S3에 접근하도록 제한(권장)
  * Origin Access Identity(OAI, 레거시): 동일 목적의 레거시 방식
  * Custom Headers(선택): S3로 전달할 커스텀 헤더
* S3 오리진은 “뷰어 프로토콜”이 “오리진 프로토콜”에 매칭됩니다.

  * 즉, 최종 사용자가 HTTP로 요청하면 CloudFront도 S3에 HTTP로 접근합니다.
* 커스텀 오리진 설정 옵션

  * Origin Path
  * Minimum Origin SSL Protocol: 오리진과 통신 시 최소 TLS 버전(가능한 최신 권장)
  * Origin Protocol Policy: HTTP only / HTTPS only / Viewer와 매칭
  * HTTP/HTTPS Port: 80/443 외 포트 사용 가능
  * Origin Custom Headers: 오리진에 커스텀 헤더 전달(CloudFront만 접근 허용 같은 보안 용도)

## 캐싱 성능과 최적화

* Cache Hit: Edge 캐시에 객체가 존재
* Cache Miss: 캐시에 없어 오리진 페치 필요
* 성능을 높이려면 **캐시 히트 비율**을 최대화해야 합니다.
* CloudFront는 다음 요소를 기준으로 캐싱 동작을 구성할 수 있습니다.

  1. 객체 이름(경로)
  2. Query string(예: `index.html&lang=en`)
  3. 쿠키
  4. 요청 헤더
* 이 정보는 먼저 CloudFront에 도달하고, 설정에 따라 오리진으로 전달될 수 있습니다.
* CloudFront가 어떤 값을 기준으로 캐싱할지(캐시 키)에 따라 성능이 크게 달라집니다.
* 최적화 권장 사항

  * 애플리케이션에 꼭 필요한 헤더만 오리진으로 전달
  * 객체를 바꾸는 요인만 캐시 키에 포함
  * 캐시 키에 포함되는 요소가 많을수록 캐싱 효율은 떨어집니다.

## CloudFront 보안

### S3 오리진의 OAI/OAC, 커스텀 오리진

* S3 오리진

  * **OAI(Origin Access Identity, 레거시)**

    * CloudFront Distribution에 연결 가능한 “아이덴티티”
    * Distribution이 OAI로 동작하며, S3 버킷 정책에서 이 OAI를 허용 대상으로 설정 가능
    * 패턴: S3 버킷을 CloudFront만 접근 가능하도록 잠금
    * Edge Location이 OAI 권한으로 버킷 접근
    * 버킷 정책으로 사용자의 직접 접근 차단 가능
    * OAI는 여러 Distribution/버킷에 재사용 가능하나, 보통은 “Distribution 1개당 OAI 1개”가 관리가 단순
  * **OAC(Origin Access Control, 권장)**

    * 목적은 OAI와 동일(S3를 CloudFront 전용으로 제한)
    * 활성화 시 CloudFront가 S3 요청을 서명(Sign)함
    * 활성화 후 버킷 정책을 수정해 해당 Distribution 요청을 허용해야 함
* 커스텀 오리진

  * OAI를 사용할 수 없습니다.
  * 대안 1: 커스텀 헤더 + HTTPS로 보호(CloudFront만 해당 헤더를 보내도록 구성)
  * 대안 2: CloudFront IP 대역으로 접근 제한(CloudFront IP Range는 공개됨)

### 프라이빗 Distribution(서명 URL/쿠키)

* CloudFront 배포는 2가지 모드

  * Public: 누구나 접근 가능
  * Private: **Signed URL 또는 Signed Cookie**가 있어야 접근 가능
* 비헤이비어가 1개면 전체 배포가 Public/Private로 결정됩니다.
* 비헤이비어가 여러 개면 비헤이비어별로 Public/Private를 나눌 수 있습니다.
* 프라이빗 구성 방식 2가지

  * 레거시: 루트 계정으로 **CloudFront Key** 생성 → 해당 계정을 **Trusted Signer**로 등록
  * 권장(신규):

    * **Trusted Key Group** 생성 후 서명자(Signers) 연결
    * 키 그룹이 서명 URL/쿠키 생성에 사용할 수 있는 키를 정의
    * 장점

      * 루트 계정에 의존하지 않음
      * API로 키/키그룹 관리 가능
      * 더 많은 키를 유연하게 연결 가능
* Signed URL vs Signed Cookie

  * Signed URL: 특정 객체 1개에 대한 접근 제공

    * 클라이언트가 쿠키를 지원하지 않으면 Signed URL 권장
  * Signed Cookie: 객체 그룹(특정 타입/경로 등) 또는 광범위한 접근 제공

    * 애플리케이션 URL을 유지해야 하는 경우 유리

### Geo Restriction(지역 제한)

* 특정 위치에서만 콘텐츠 접근을 허용/차단하는 기능
* 제한 유형 2가지

  * **CloudFront Geo Restriction**

    * 국가 단위 화이트리스트/블랙리스트
    * **국가만** 기준으로 동작
    * GeoIP DB(정확도 99.8% 주장) 사용
    * Distribution 전체에 적용
    * ![Geo Restriction Architecture](images/CloudFrontGeoRestriction.png)
  * **서드파티 지오로케이션**

    * 사용자/속성 등 다양한 조건으로 커스터마이징 가능
    * CloudFront 앞단에 애플리케이션 서버가 있어야 하며, 접근 여부를 판단한 뒤 서명 URL/쿠키를 발급
    * 브라우저가 이를 CloudFront에 제출해 권한 검증
    * ![3rd Party GeoLocation Architecture](images/CloudFront3rdPartyGeoLocation.png)

### Field-Level Encryption(필드 단위 암호화)

* Edge Location에서 요청의 특정 필드를 공개키로 암호화할 수 있습니다.
* 비밀번호/결제정보 같은 민감 필드 보호에 유용합니다.
* HTTPS 터널과 별개로 동작합니다.
* 복호화에는 개인키가 필요합니다.
* 필요 시 오리진에서 필드를 복호화합니다.
* ![Field-Level encryption architecture](images/FieldLevelEncryption2.png)

## Lambda@Edge

* Edge Location에서 경량 Lambda 함수를 실행할 수 있습니다.
* Viewer ↔ Origin 사이 데이터(요청/응답)를 가공하는 용도로 사용합니다.
* 일반 Lambda 대비 제약

  * 지원 런타임: Node.js, Python
  * VPC 리소스 접근 불가(퍼블릭 영역에서 실행)
  * Lambda Layers 미지원
* 크기/시간 제한(일반 Lambda와 다름)

  * Viewer 측: 메모리 128MB / 타임아웃 5초
  * Origin 측: 함수 크기는 일반 Lambda와 동일 / 타임아웃 30초
* 대표 사용 사례

  * A/B 테스트(보통 Viewer Request)
  * S3 오리진 간 마이그레이션(보통 Origin Request)
  * 디바이스 타입별 다른 객체 제공(보통 Origin Request)
  * 국가별 콘텐츠 제공(보통 Origin Request)
  * 예시 링크(원문에 포함):

    * `https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/lambda-examples.html#lambda-examples-redirecting-examples`
