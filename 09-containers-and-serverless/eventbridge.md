## CloudWatch Events & EventBridge 

* **(근)실시간 시스템 이벤트 스트림 전달**

  * AWS 서비스에서 발생하는 상태 변화/작업 결과를 이벤트로 받음
  * 예: **EC2 인스턴스 시작/중지**, Auto Scaling 스케일 이벤트 등

* **EventBridge = CloudWatch Events의 확장/후속**

  * **CloudWatch Events**: AWS 서비스 이벤트 중심
  * **EventBridge**: CloudWatch Events 기능을 포함하면서

    * **SaaS/서드파티 이벤트 소스**(파트너) 연동
    * **커스텀 애플리케이션 이벤트**(자체 발행) 처리
    * **추가 Event bus** 구성 가능 (멀티 버스 설계)

* **이벤트 버스(Event bus) 기반 동작**

  * 둘 다 **default event bus**가 있음
  * (핵심 차이)

    * CloudWatch Events는 **사실상 default 버스만 사용**하는 단순 구조로 이해하면 됨
    * EventBridge는 **custom event bus**를 만들어 **도메인/계정/팀 단위 분리** 같은 아키텍처가 가능

* **Rule(규칙)로 라우팅**

  * **이벤트 패턴 매칭 규칙**: 들어오는 이벤트(JSON)를 조건으로 필터링해서 타깃으로 전달
  * **스케줄 기반 규칙**: cron/rate 형태로 정해진 시간에 트리거

* **이벤트는 JSON**

  * 어떤 리소스가 바뀌었는지(예: instance-id), 어떤 상태로 바뀌었는지(state), 발생 시간(time) 등 메타데이터 포함

### SAP 관점에서 자주 같이 묶이는 포인트

* **느슨한 결합(Loosely coupled)** 이벤트 기반 통합의 대표 패턴
* **다계정/멀티도메인 설계**에서 EventBridge의 **custom bus + 규칙 + 타깃** 조합이 자주 출제됨
* 스케줄 트리거가 필요하면 “EventBridge(또는 CloudWatch Events) 스케줄 룰”이 기본 선택지로 자주 등장
