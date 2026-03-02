## Amazon API Gateway 개요

* **Amazon API Gateway**는 API를 **생성, 게시, 유지관리, 모니터링, 보안(보호)** 할 수 있게 해주는 관리형 서비스이다.
* 애플리케이션(클라이언트)과 백엔드 통합 대상(Integrations: Lambda, HTTP, AWS 서비스 등) 사이에서 **엔드포인트/진입점(Entry Point)** 역할을 한다.
* **고가용성(HA)** 및 **자동 확장(Scalable)** 특성을 갖는다.
* 다음 기능을 제공한다:

  * 인증/인가(Authorization)
  * 스로틀링(Throttling)
  * 캐싱(Caching)
  * CORS 처리
  * 요청/응답 변환(Transformations)
* **OpenAPI 스펙**을 지원하며, AWS 서비스와의 직접 통합을 지원한다.
* 기본적으로 **퍼블릭 서비스**이다.
* **REST API**, **WebSocket API**를 제공할 수 있다.

---

## 인증(Authentication)

API Gateway는 다음과 같은 인증 방식을 지원한다.

* **Amazon Cognito**
* **Lambda 기반 커스텀 인증(Custom Authorizer)**

  * 예: 클라이언트가 Bearer 토큰을 사용하는 시나리오 등
* **IAM 자격 증명(IAM credentials)**

또한 **인증 없이 공개(Open Access)** API로 구성할 수도 있다.

---

## 엔드포인트 유형(Endpoint Types)

* **Edge-Optimized**

  * 요청을 **가장 가까운 CloudFront POP(엣지 로케이션)** 으로 라우팅하여 전 세계 사용자 지연 시간을 줄인다.
* **Regional**

  * **리전 엔드포인트**를 직접 사용한다.
  * Edge-Optimized처럼 CloudFront POP 기반 최적화를 기본으로 사용하지 않는다.
* **Private**

  * **VPC 내부에서만 접근 가능**한 엔드포인트
  * 일반적으로 **VPC Interface Endpoint(PrivateLink)** 를 통해 접근한다.

---

## 스테이지(Stages)

* API 구성을 배포(Deploy)할 때, **스테이지(Stage)** 로 배포한다.
* 예: `dev`, `prod` 스테이지를 분리해 **서로 다른 설정과 URL**을 운영 가능
* 스테이지 단위로 **롤백(Rollback)** 이 가능하다.
* 스테이지에서 **카나리 배포(Canary Deployment)** 를 활성화할 수 있다.

  * 활성화 시, 새 배포는 “스테이지 전체”가 아니라 **카나리**로 먼저 배포된다.
  * **트래픽 분배 비율**을 카나리/기본(stage base) 간 조정 가능
  * 카나리를 검증 후 **기본 스테이지로 승격(promote)** 가능

---

## 오류 코드(Errors)

* **4XX**: 클라이언트 오류(요청 자체가 잘못됨)
* **5XX**: 서버 오류(요청은 유효하지만 백엔드/통합 쪽 문제)

대표 예시:

* **400 Bad Request**: 일반적인 클라이언트 요청 오류
* **403 Access Denied**: Authorizer가 거부했거나 WAF에서 차단됨
* **429 Too Many Requests**: 스로틀링에 의해 요청 제한 초과
* **502 Bad Gateway**: Lambda가 잘못된 출력을 반환(통합 결과가 비정상)
* **503 Service Unavailable**: 백엔드 엔드포인트가 오프라인
* **504 Integration Failure/Timeout**: 통합 타임아웃/실패 (통합 타임아웃 기본 제한: **29초**)

---

## 캐싱(Caching)

* 캐싱은 **스테이지 단위로 설정**한다.
* 스테이지 캐시 크기: **500MB ~ 237GB**
* 기본 TTL: **300초**, 설정 가능 범위: **0 ~ 3600초**
* 캐시는 암호화(Encryption) 가능
* **캐시 미스(Cache miss)** 인 경우에만 백엔드가 호출된다.

---

## 리소스와 메서드(Methods and Resources)

예시 URL:
`https://1nj7i16t37.execute-api.us-east-1.amazonaws.com/dev/listcats`

* URL 구조:

  * `[api-gateway-endpoint] / [stage] / [resource]`
* 스테이지(Stage)는 **논리적 구성 단위**이며, API는 스테이지에 배포되어야 변경이 반영된다.
* 메서드(Method)는 수행할 동작(HTTP Verb: GET/POST 등)을 의미한다.
* 메서드에 **통합(Integration)** 을 설정하여 실제 백엔드 기능(Lambda/HTTP/AWS 서비스)을 연결한다.

---

## 통합(Integrations)

API Gateway는 다음과 통합할 수 있다.

* **Lambda**
* **HTTP 엔드포인트**(온프레미스 또는 AWS)
* **Step Functions**
* **SNS**
* **DynamoDB**

API 처리 흐름은 크게 3단계:

1. **Request 단계**: 인증, 검증, 변환
2. **Integration 단계**: 백엔드 호출
3. **Response 단계**: 변환 후 클라이언트에 반환

또한 요청/응답은 다음 구성으로 나뉜다.

* **Method Request**: 클라이언트가 보내는 요청 정의(경로/헤더/쿼리/파라미터 등)
* **Integration Request**: Method Request에서 받은 값을 통합 대상으로 매핑/변환
* **Integration Response**: 백엔드 응답을 클라이언트로 보낼 형식으로 변환
* **Method Response**: 클라이언트에 반환되는 응답 형태 정의

---

## 통합 유형(Integration Types)

* **MOCK**

  * 테스트 용도, 백엔드 없이 **정적 응답** 반환
* **HTTP**

  * 커스텀 HTTP 통합
  * **Integration Request/Response를 직접 구성**해야 함
* **HTTP Proxy**

  * HTTP 통합의 하위 형태
  * 프록시 방식으로 **요청/응답을 거의 그대로 전달**(설정 단순화)
* **AWS**

  * AWS 서비스를 직접 노출하는 통합
  * **요청/응답 매핑**이 필요하고, Lambda에도 사용할 수 있으나 구성은 상대적으로 복잡
* **AWS_PROXY (Lambda Proxy)**

  * Integration Request/Response 정의가 거의 필요 없음
  * API Gateway가 요청을 **변경 없이** Lambda로 전달(프록시 이벤트)

---

## 매핑 템플릿(Mapping Templates)

* **비(Non-Proxy) 통합(AWS, HTTP)** 에서 사용
* 통합 간 매핑/변환 기능:

  * 파라미터 수정/이름 변경
  * 바디/헤더 수정
  * 필터링(불필요한 데이터 제거)
* 템플릿 언어: **VTL(Velocity Template Language)**
* 대표 사용 사례:

  * API Gateway의 REST API를 **SOAP API** 와 통합할 때 변환 계층으로 활용

---

## 스테이지와 배포(Stages and Deployments)

* API 편집은 곧바로 라이브에 반영되지 않는다(미게시 상태).
* 변경 사항은 반드시 **스테이지로 배포**되어야 적용된다.
* 스테이지별로 서로 다른 구성을 운영할 수 있다.
* 스테이지 구성은 불변(immutable)이 아니라서 수정/덮어쓰기/롤백이 가능하다.
* **Stage Variables**: 스테이지별 환경변수처럼 활용

---

## Swagger와 OpenAPI

* **OpenAPI(OAS)** 는 RESTful API를 정의하는 언어/플랫폼 독립적 표준 인터페이스이다.
* **OpenAPI v2 = Swagger**
* **OpenAPI v3**는 더 최신 버전
* OpenAPI는 다음을 정의한다:

  * 엔드포인트
  * 오퍼레이션(GET/POST 등)
  * 입력/출력 파라미터
  * 인증 방식
* API Gateway는 **OpenAPI 형식으로 Import/Export** 가능하며, **백업/마이그레이션**에 유용하다.
