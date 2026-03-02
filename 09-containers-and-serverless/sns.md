## SNS (Amazon Simple Notification Service)

* **개요**: SNS는 **고가용성(HA)**, **내구성(durable)**, **보안(secure)**을 갖춘 **Pub/Sub(게시-구독) 메시징 서비스**입니다.
* **엔드포인트 성격**: SNS는 **퍼블릭 AWS 서비스**로, 기본적으로 **공용 엔드포인트로의 네트워크 연결**이 필요합니다.
* **역할**: 메시지의 **전송과 전달(delivery) 조정/오케스트레이션**을 담당합니다.
* **메시지 크기 제한**: 메시지 payload는 **최대 256 KB**입니다.

### 핵심 구성 요소: Topic

* **SNS Topic**은 SNS의 기본 단위이며, **권한(permissions)** 과 **구성(configurations)** 이 Topic 수준에서 관리됩니다.
* **Publisher(발행자)** 가 Topic으로 메시지를 발행(publish)합니다.
* **Subscriber(구독자)** 가 Topic을 구독(subscribe)하고, 발행된 메시지를 전달받습니다.

### 지원 구독자(Subscriber) 유형

SNS Topic은 다음 대상으로 메시지를 푸시할 수 있습니다.

* **HTTP/HTTPS**
* **Email (JSON)**
* **SQS 큐**
* **모바일 푸시 알림**
* **SMS**
* **Lambda 함수**

### 대표 사용 패턴

* **AWS 전반의 알림 허브**로 자주 사용됩니다. 예: **CloudWatch 알림**이 SNS를 많이 활용.
* **Fan-out 아키텍처**: 하나의 Topic에서 여러 **SQS 큐**로 동시에 전달(일반적으로 “SNS → 다수 SQS” 패턴).

### 전달 제어/신뢰성 기능

* **구독자별 필터링(Filter Policy)** 적용 가능(특정 조건의 메시지만 해당 구독자에게 전달).
* **Delivery Status(전달 상태 확인)** 지원 대상: **HTTP, Lambda, SQS**
* **Delivery Retry(재시도)** 지원

### 가용성/확장/보안

* SNS는 **리전 내에서 고가용성 및 확장성**을 제공하는 관리형 서비스입니다.
* **Server-Side Encryption (SSE)** 지원.
* **교차 계정(Cross-account) 접근**: **Topic Policy**로 다른 AWS 계정에 Topic 접근 권한을 부여할 수 있습니다.
