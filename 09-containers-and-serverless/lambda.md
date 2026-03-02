# AWS Lambda

* Lambda는 FaaS(Function-as-a-Service) 서비스입니다. 짧게 실행되는 단일 목적(집중형) 코드를 함수로 제공하면, AWS가 실행/확장/운영을 처리하고 **사용한 만큼만 과금**합니다.
* 모든 Lambda 함수는 지원되는 런타임(예: Python 3.8, Java 8, Node.js 등)에서 실행됩니다.
* 각 Lambda 함수는 **런타임 환경(실행 환경, execution environment)** 에 로드되어 실행됩니다.
* 함수를 만들 때 함수가 사용할 **리소스**를 정의합니다. **메모리**를 직접 지정하고, **CPU(vCPU)** 는 메모리 값에 따라 간접적으로 할당됩니다.
* 과금은 기본적으로 **호출 횟수**와 **실행 시간(duration)**, 그리고 지정한 리소스(메모리 등)에 기반합니다.
* Lambda는 AWS **서버리스 아키텍처**의 핵심 구성 요소입니다.
* Lambda가 지원하는 런타임 예:

  * Python
  * Ruby
  * Go
  * Java
  * C#
  * (Lambda Layer 등을 이용한 Custom 런타임: 예 Rust)
* 배포 패키지 크기 제한:

  * ZIP 압축: 50 MB
  * 압축 해제: 250 MB
  * Docker 이미지: 최대 10 GB
* Lambda 함수는 **상태 비저장(stateless)** 입니다. 즉, 한 번 실행이 끝나면 “남아 있는 데이터”를 전제로 하면 안 됩니다.
* 메모리는 **128 MB ~ 10240 MB** 범위에서 **1 MB 단위**로 설정 가능합니다.
* vCPU는 직접 지정하지 않고 메모리에 비례해 증가합니다. 예: **1769 MB 메모리 = 1 vCPU**
* 런타임 환경에는 기본적으로 **512 MB**의 임시 스토리지가 `/tmp` 로 제공됩니다. 이를 **최대 10240 MB**까지 확장할 수 있습니다.

  * 이 영역은 필요에 따라 사용 가능하지만, 실행마다 “비어 있다”고 가정하는 것이 안전합니다(재사용될 수 있으나 보장되지 않음).
* Lambda 함수의 최대 실행 시간은 **15분**이며, 초과 시 타임아웃이 발생합니다.
* Lambda 함수의 보안은 **Execution Role(실행 역할)** 로 제어됩니다. 함수에 연결된 IAM Role이며, 다른 AWS 서비스에 접근하기 위한 권한을 포함합니다.

---

## Lambda 네트워킹

* Lambda는 두 가지 네트워킹 모드를 가질 수 있습니다.

### 1) Public (기본)

* SQS, DynamoDB 같은 **퍼블릭 AWS 서비스** 및 **인터넷 상의 서비스**에 접근할 수 있습니다.
* 고객 VPC 설정 없이 동작하므로 **성능/단순성 측면에서 유리**합니다.
* 단, VPC 내부 리소스에는 기본적으로 직접 접근할 수 없습니다. (VPC 리소스가 퍼블릭 IP를 갖고, 보안 설정이 외부 접근을 허용하는 경우는 예외)

### 2) VPC Networking

* Lambda가 **VPC 내부에서 동작**하여, SG/NACL이 허용하는 범위 내에서 VPC 리소스에 접근할 수 있습니다.
* VPC 밖(인터넷/퍼블릭 서비스)으로 나가려면 VPC에 **NAT Gateway/인터페이스 엔드포인트** 등 추가 네트워크 구성이 필요합니다.
* VPC 연결을 위해 Lambda는 ENI를 생성해야 하므로 `EC2Networking` 권한이 필요합니다.
* VPC 연결 Lambda는 “완전히 VPC에 상주”한다기보다, VPC 접근을 위해 **ENI를 사용**합니다.

  * 동일 SG를 사용하는 함수들은 **공유 ENI**를 통해 VPC에 접근할 수 있습니다.
  * 다른 SG를 추가로 붙이면 **새 ENI**가 생성될 수 있습니다.
* 함수 생성 시 VPC 접근을 위한 ENI 준비 과정이 발생할 수 있으며, 초기 설정은 **최대 90초**까지 걸릴 수 있습니다. 이 초기 설정은 **매 호출마다가 아니라(일반적으로) 최초/필요 시점에** 발생합니다.

---

## Lambda 보안

* Lambda 보안 모델의 핵심은 2가지입니다.

  1. **Execution Role**: Lambda가 다른 AWS 리소스에 접근할 때 가정(assume)하는 IAM Role
  2. **Resource Policy**: S3 버킷 정책과 유사하게, **외부 계정**이 Lambda를 호출하도록 허용하거나 특정 AWS 서비스가 Lambda를 호출하도록 허용하는 정책

     * Resource policy는 CLI/API로 수정 가능(콘솔에서 제한이 있을 수 있음)

---

## Lambda 로깅

* Lambda는 **CloudWatch Logs** 및 **X-Ray**와 통합됩니다.
* 실행 로그는 CloudWatch Logs에 저장됩니다.
* 메트릭은 CloudWatch Metrics에 저장됩니다.
* X-Ray를 연동하면 분산 추적(distributed tracing)이 가능합니다.
* 로깅을 위해서는 Execution Role에 관련 권한을 부여해야 합니다.

---

## Lambda 호출 방식(Invocations)

Lambda는 크게 3가지 방식으로 호출됩니다.

### 1) 동기(Synchronous) 호출

* CLI나 API가 함수를 직접 호출하는 형태
* 호출자가 함수의 응답을 **대기**
* API Gateway도 일반적으로 Lambda를 동기 호출(서버리스 API의 대표 패턴)
* 오류 처리/재시도는 기본적으로 **클라이언트 측**에서 처리(통합 구성에 따라 달라질 수 있음)

### 2) 비동기(Asynchronous) 호출

* AWS 서비스가 이벤트로 Lambda를 호출하는 경우에 흔함(예: S3 이벤트)
* 호출 서비스는 응답을 기다리지 않음(“fire and forget”)
* 실패 처리는 Lambda 측에서 수행하며, 기본적으로 **0~2회 재시도**가 발생할 수 있습니다.
* 재시도에 대비해 함수는 **멱등성(idempotent)** 을 갖는 것이 중요합니다.
* 실패가 반복되면 DLQ로 보낼 수 있습니다.
* **Destination** 기능으로 성공/실패 이벤트를 SQS, SNS, 다른 Lambda, EventBridge 등으로 전달 가능하며, 성공/실패를 다른 대상으로 분기할 수 있습니다.

### 3) Event Source Mapping

* 이벤트를 “푸시”하지 않는 소스(예: Kinesis, DynamoDB Streams, SQS)에 주로 사용
* Event Source Mapper가 소스를 **폴링**하여 배치를 가져오고, 배치를 여러 Lambda 실행으로 분할해 처리할 수 있습니다.
* 배치 처리에서는 “부분 성공”이 어려운 케이스가 많아, **전부 성공 또는 전부 실패**로 다뤄지는 제약이 생길 수 있습니다(소스/기능 옵션에 따라 세부 동작은 달라질 수 있음).

추가 포인트

* 비동기 이벤트 처리에서는, 이벤트를 보내는 서비스에 대해 “명시적으로 읽기 권한”이 꼭 필요한 것은 아닙니다(호출 자체는 리소스 정책/서비스 연동으로 이뤄짐).
* Event Source Mapping은 mapper가 소스에서 읽어야 하므로, **Lambda Execution Role**에 소스 읽기 권한이 필요합니다.
* 함수 코드가 스트림/큐에서 직접 읽지 않더라도, 배치 수신/체크포인트 처리 등을 위해 실행 역할에 읽기 권한이 필요합니다.
* 지속적으로 실패하는 배치는 SQS 큐나 SNS 토픽으로 보내 후속 처리할 수 있습니다.

---

## Lambda 버전(Versions)

* 한 함수에 대해 여러 **버전**을 만들 수 있습니다.
* 버전은 “코드 + 구성(configuration)”의 스냅샷입니다.
* 버전을 Publish하면 **불변(immutable)** 이며, 고유 ARN을 가집니다.
* `$LATEST` 는 최신 상태를 가리키며 **불변이 아닙니다**.
* **Alias(DEV, STAGE, PROD 등)** 를 만들어 특정 버전을 가리키게 할 수 있으며, Alias는 다른 버전으로 변경 가능합니다.

---

## Lambda 시작 시간(Start-up Times)

* Lambda 코드는 실행 컨텍스트(execution context)에서 동작합니다.
* 최초 호출 시 실행 컨텍스트를 생성해야 하며 시간이 더 걸리는데 이를 **콜드 스타트(cold start)** 라고 합니다(100ms 이상일 수 있음).
* 일정 시간 내 재호출되면 동일 컨텍스트를 재사용할 수 있으며 이를 **웜 스타트(warm start)** 라고 합니다.
* 하나의 실행 환경은 한 시점에 보통 한 호출을 처리합니다. 병렬 호출이 늘어나면 실행 환경이 추가로 필요해 콜드 스타트가 발생할 수 있습니다.
* **Provisioned Concurrency** 를 사용하면 실행 환경을 미리 준비해 콜드 스타트를 줄일 수 있습니다.
* 성능 개선 팁:

  * `/tmp`에 데이터를 미리 내려받아 두면 동일 환경 재사용 시 다음 호출에서 재활용 가능(단, 보장 아님)
  * DB 커넥션 등을 핸들러 밖(전역)에서 생성하면 웜 스타트에서 재사용 가능

---

## Lambda 핸들러와 라이프사이클

* Lambda 실행은 라이프사이클을 가집니다.
* 주요 단계:

  * `INIT`: 실행 환경 생성 또는 언프리즈

    * 하위 구성:

      * `EXTENSION INIT`
      * `RUNTIME INIT`
      * `FUNCTION INIT`
    * INIT는 보통 **콜드 스타트에서만** 발생
  * `INVOKE`: 함수 핸들러 실행(콜드 스타트 포함)
  * `NEXT INVOKE`: 동일 환경 재사용 시의 후속 호출(웜 스타트)
  * `SHUTDOWN`: 비활성 시간이 지나면 환경 종료

    * 하위 구성:

      * `RUNTIME SHUTDOWN`
      * `EXTENSION SHUTDOWN`
    * Provisioned Concurrency로 콜드 스타트 가능성을 줄일 수 있음

---

## Lambda 버전과 Alias(상세)

* Publish 전(미게시) 함수는 변경/배포 가능
* `$LATEST` 는 편집/배포 가능
* 현재 상태를 Publish하면 불변 버전 생성
* Publish된 버전의 코드/의존성/런타임 설정/환경 변수는 변경 불가
* 각 버전은 고유 ARN(qualified ARN)을 가짐
* 버전 지정 없는 ARN(unqualified ARN)은 기본적으로 함수 자체(`$LATEST`)를 가리킴
* Alias는 특정 버전을 가리키는 포인터

  * 예: `PROD -> function:1`, `BETA -> function:2`
* Alias는 고유 ARN을 가짐
* Alias는 업데이트 가능(가리키는 버전 변경)
* 활용: PROD/DEV 분리, Blue/Green, A/B 테스트
* **Alias routing**: 요청을 v1/v2로 퍼센트 분기 가능

  * 두 버전은 동일 실행 역할과 동일 DLQ(또는 DLQ 없음)를 공유하며, 둘 다 Publish된 버전이어야 함

---

## Lambda 환경 변수(Environment Variables)

* Lambda 함수에 연결되는 Key-Value 값
* 기본적으로 `$LATEST` 에 연결되어 수정 가능
* Publish된 버전에서는 수정 불가
* 실행 환경에서 접근 가능
* KMS로 암호화 가능
* 코드 동작을 환경별로 조정하는 데 사용

---

## Lambda Layer

* 라이브러리/의존성을 함수 본문에서 분리하기 위한 메커니즘
* 배포 패키지 크기를 줄일 수 있음
* 여러 Lambda 함수에서 재사용 가능
* Layer의 라이브러리는 `/opt` 에 풀립니다.
* 명시적으로 지원되지 않는 런타임을 Layer/커스텀 런타임으로 구성하는 데 활용 가능

---

## Lambda 컨테이너 이미지(Container Images)

* 과거에는 “함수 생성 → 코드 업로드 → 실행” 중심의 FaaS 모델이 일반적이었음
* 많은 조직이 컨테이너 기반 CI/CD를 사용
* Lambda는 컨테이너 이미지로도 함수 패키징/배포 가능
* Lambda Runtime API: 컨테이너와 Lambda 간 상호작용을 위해 이미지에 포함되어야 함
* AWS Lambda Runtime Interface Emulator(RIE): 로컬에서 Lambda 테스트에 사용

---

## Lambda와 ALB

* Lambda 함수를 ALB 타깃 그룹에 등록할 수 있습니다.
* 사용자 ↔ ALB 통신은 HTTP/HTTPS이며, 사용자 관점에서는 EC2에 붙는 것과 유사합니다.
* ALB는 요청을 받으면 Lambda를 **동기 호출**합니다.
* ALB는 HTTP(S) 요청을 Lambda 이벤트(JSON)로 변환해 전달하고, Lambda의 JSON 응답을 다시 HTTP/HTTPS 응답으로 변환합니다.
* Multi-Value 헤더/쿼리스트링 예:

  * 예 URL: `http://catagram.io?&search=roffle&search=winkie`
  * Multi-value 미사용 시:

    ```json
    "queryStringParameters": {
      "search": "winkie"
    }
    ```
  * Multi-value 사용 시:

    ```json
    "multiValueQueryStringParameters": {
      "search": ["roffle", "winkie"]
    }
    ```
