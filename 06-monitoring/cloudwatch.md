# CloudWatch

* CloudWatch는 **메트릭을 수집(ingest)·저장(store)·관리(manage)** 하는 서비스를 제공한다.
* **퍼블릭 서비스**이며 퍼블릭 엔드포인트를 제공한다.
* 많은 서비스가 CloudWatch와 **네이티브(관리 플레인) 통합**되어 있다(예: EC2).

  * 단, EC2의 기본 메트릭은 **호스트 외부 관측(하이퍼바이저/플랫폼 레벨)** 중심이다.
  * 인스턴스 내부(메모리, 디스크, 애플리케이션 등) 메트릭은 **CloudWatch Agent**를 사용해 수집할 수 있다.
* CloudWatch는 **Agent 또는 CloudWatch API**를 통해 **온프레미스에서도 사용 가능**하다.
* CloudWatch는 데이터를 **지속적으로(persistent)** 저장한다.
* 데이터는 **콘솔/CLI/API**로 조회 가능하며, 또한 **대시보드(Dashboards)** 와 **이상 탐지(Anomaly Detection)** 를 제공한다.
* **CloudWatch Alarms**: 메트릭에 반응하며, **알림(Notify)** 또는 **액션 수행(Action)** 용도로 사용한다.
* 네트워크 연결:

  * 퍼블릭 서브넷 인스턴스는 **IGW**를 통해 CloudWatch에 접속 가능
  * 프라이빗 서브넷 인스턴스는 **VPC Interface Endpoint(PrivateLink)** 로 CloudWatch에 접속 가능

---

## CloudWatch - 데이터

* **Namespace**: 메트릭을 담는 컨테이너

  * 예: EC2는 `AWS/EC2`, Lambda는 `AWS/Lambda`
  * 동일한 `MetricName`이라도 Namespace로 서비스/출처를 **구분**한다.
* **Data point**: `timestamp`, `value`, `unit(선택)`로 구성
* **Metric**: 시간 순으로 정렬된 Data point 집합

  * EC2 기본 메트릭 예: `CPUUtilization`, `NetworkIn`, `DiskWriteBytes`
* 모든 메트릭은 `Namespace`와 `MetricName`을 가진다.

  * 예: `AWS/EC2`의 `CPUUtilization`
* **Dimension**: `name/value` 쌍

  * 예: `CPUUtilization`을 **인스턴스별로 분리**하기 위한 차원(InstanceId 등)
* Dimension은 **집계(aggregate)** 에도 사용 가능

  * 예: ASG 내 모든 인스턴스의 메트릭을 집계해 보기
* **Resolution(해상도)**: 표준(60초) 또는 고해상도(1초)
* **Metric resolution**: 특정 메트릭이 가질 수 있는 **최소 데이터 포인트 간격(최소 period)**
* 데이터 보존(리텐션):

  * 60초 미만(서브 60s) 정밀도 데이터는 **3시간** 보관
  * 60초 정밀도 데이터는 **15일** 보관
  * 5분 정밀도 데이터는 **63일** 보관
  * 1시간 정밀도 데이터는 **455일** 보관
* 데이터는 시간이 지날수록 **더 큰 구간으로 집계(aggregate)되어** 더 오래 보관되며, 해상도는 낮아진다.
* **Statistics**: 기간 내 데이터를 특정 방식으로 **집계**해서 조회
* **Percentile**: 데이터셋 내에서 특정 값의 **상대적 위치(백분위)**

---

## CloudWatch Alarms

* **Alarm**: 특정 기간 동안 메트릭을 감시
* 상태:

  * 임계치 조건 만족 시 `ALARM`
  * 정상 시 `OK`
* Alarm은 하나 이상의 **Action**을 설정 가능하며, 예:

  * **SNS 토픽으로 알림 전송**
  * **Auto Scaling 정책 수행(Desired capacity 조정 등)**
  * **EventBridge 연동**으로 다른 서비스/워크플로우 트리거
* 고해상도 메트릭은 **고해상도 알람**을 사용할 수 있다.

---

## CloudWatch Logs

* CloudWatch Logs는 크게 **수집(ingestion)** 과 **구독(subscription/전달)** 기능을 제공한다.
* 로그 데이터를 저장·모니터링·접근 제공을 위한 **퍼블릭 서비스**다.
* AWS 서비스의 네이티브 로그 수집뿐 아니라, **온프레미스/IoT/애플리케이션** 로그도 수집할 수 있다.
* **CloudWatch Agent**: 커스텀 애플리케이션 로그 수집에 사용
* VPC Flow Logs 또는 CloudTrail(AWS API 호출/계정 이벤트) 로그 스트림도 수집 가능
* CloudWatch Logs는 **리전 서비스**이며, 일부 글로벌 서비스 로그는 `us-east-1`로 전달되는 경우가 있다.
* **Log event** 구성:

  * `Timestamp`
  * `Raw message`
* **Log Stream**: 동일 소스에서 나온 log event의 시퀀스
* **Log Group**: Log Stream의 컬렉션

  * 리텐션/권한/암호화를 설정할 수 있다.
  * 기본값은 **무기한 보관**이다.
* **Metric Filter**: Log Group에서 패턴을 찾아 **메트릭을 생성**

  * 예: 로그에서 “failed SSH” 패턴 발생 횟수를 메트릭으로 만들기
* CloudWatch에서 로그 내보내기:

  * **S3 Export**: `Create-Export-Task`로 내보내며 최대 **12시간** 소요 가능, **실시간 아님**, **SSE-S3**로 암호화
  * **Subscription(구독 전달)**: 실시간 전달. 대상:

    * **Kinesis Data Firehose**(준실시간)
    * **OpenSearch(구 ElasticSearch)**: Lambda 또는 커스텀 Lambda 경유
    * **Kinesis Data Streams**: KCL Consumer 등 임의 소비자 처리
* Subscription Filter로 **로그 집계/중앙화 아키텍처**를 구성할 수 있다.

---

## CloudWatch Dashboards

* 핵심 메트릭에 빠르게 접근하기 위한 대시보드 기능
* 대시보드는 **글로벌**이다.
* 여러 리전의 그래프를 한 대시보드에 포함 가능
* 타임존/시간 범위를 변경 가능
* 자동 새로고침 설정 가능(10초, 1분, 2분, 5분, 15분)
* 과금:

  * 무료: **대시보드 3개(각 최대 50개 메트릭)**
  * 유료: **$3/대시보드/월**

---

## CloudWatch Synthetics Canary

* **Synthetics Canary**는 API/URL을 모니터링하는 **구성 가능한 스크립트**다.
* 사용자의 행동을 재현해 **프로덕션 배포 전** 문제를 미리 탐지하는 목적에 적합하다.
* 엔드포인트의 **가용성/지연시간**을 점검할 수 있다.
* 로드 시간 데이터와 UI **스크린샷**을 저장할 수 있다.
* CloudWatch Alarms와 연동된다.
* 스크립트는 **Node.js 또는 Python**으로 작성 가능
* 헤드리스 Chrome 브라우저에 대한 프로그래매틱 접근 제공
* 1회 실행 또는 주기 실행 가능
* Canary Blueprint:

  * **Heartbeat Monitor**: URL 로드, 스크린샷 및 HAR 파일 저장
  * **API Canary**: REST API 기본 Read/Write 테스트
  * **Broken Link Checker**: 페이지 내 모든 링크 검사
  * **Visual Monitoring**: 실행 시 스크린샷을 베이스라인과 비교
  * **Canary Recorder**: Recorder로 웹 동작을 기록해 테스트 스크립트 자동 생성
  * **GUI Workflow Builder**: 웹페이지에서 액션 수행 가능 여부 검증(예: 로그인 폼 테스트
