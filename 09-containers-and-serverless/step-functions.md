# AWS Step Functions

* Step Functions는 **Lambda 단독 사용의 한계**를 보완하기 위한 워크플로 오케스트레이션 서비스이다.
* Lambda 함수는 **최대 실행 시간이 15분**으로 제한된다.
* Lambda를 여러 개 **연쇄 호출**로 이어 붙일 수는 있지만, 일반적으로 **안티패턴**으로 간주되며 구성/예외처리/재시도 관리가 복잡해지기 쉽다. 또한 Lambda 런타임은 **상태 비저장(stateless)** 이다.
* Step Functions는 **상태 머신(State Machine)** 을 만들어 워크플로를 정의할 수 있게 해준다.
* 상태(State)는 작업을 수행하거나, 분기/결정을 내리거나, 데이터를 입력받아 **변형**하고 **출력**할 수 있다.
* 상태 머신 실행의 **최대 지속 시간은 1년**이다.
* Step Functions 워크플로는 크게 2가지 유형이 있다.

  * **Standard 워크플로**: 기본 유형이며 **최대 1년 실행** 가능.
  * **Express 워크플로**: **고처리량 이벤트 처리**(IoT, 스트리밍 데이터 처리 및 변환)에 최적화. **최대 5분 실행**.
* Step Functions는 다음과 같은 트리거로 시작할 수 있다: **API Gateway, IoT Rules, EventBridge, Lambda**, 또는 **수동 실행**.
* 상태 머신 정의는 **Amazon States Language(ASL)** 의 **JSON 템플릿**으로 작성한다.

## States (상태)

* 사용 가능한 상태 타입:

  * `SUCCEED`, `FAIL`
  * `WAIT`: 지정한 기간/시각/날짜까지 대기
  * `CHOICE`: 입력 값에 따라 분기 경로 선택
  * `PARALLEL`: 병렬 브랜치 실행
  * `MAP`: 리스트 입력을 받아 각 항목에 대해 지정된 작업 집합 수행
  * `TASK`: 상태 머신이 수행하는 **단일 작업 단위**. 다음과 같은 서비스와 통합 가능:

    * **Lambda, Batch, DynamoDB, ECS, SNS, SQS, Glue, SageMaker, EMR, 다른 Step Functions** 등
