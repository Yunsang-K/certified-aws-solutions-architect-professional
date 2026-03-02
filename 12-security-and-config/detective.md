## Amazon Detective

* **목적**

  * GuardDuty, Macie, Security Hub 등에서 나온 **보안 Finding**을 더 깊게 파고들어 **원인 분석(Root cause)과 조사(Investigation)** 를 빠르게 수행하도록 돕는 서비스.

* **핵심 기능**

  * **ML + 그래프(관계) 분석**을 사용해 의심스러운 활동의 연관관계를 자동으로 연결하고, “무슨 일이 왜 일어났는지”를 추적하기 쉽게 만든다.
  * 개별 로그를 수동으로 이어 붙이는 대신, **조사용 통합 뷰(unified view)** 를 제공한다.

* **수집/분석 데이터 소스**

  * **VPC Flow Logs**
  * **AWS CloudTrail**
  * **GuardDuty findings / 이벤트**
  * 위 데이터를 자동으로 수집·처리해서 조사에 필요한 맥락을 구성한다.

* **산출물(무엇을 보여주나)**

  * 엔터티(예: IAM 사용자/Role, EC2 인스턴스, IP, VPC 등) 간 **연결 그래프**
  * 타임라인/행동 패턴/관련 이벤트 등 **시각화된 컨텍스트**
  * “이 Finding과 연관된 주체/네트워크 흐름/호출 API가 무엇인지”를 빠르게 좁혀준다.

* **시험 포인트(자주 나오는 구분)**

  * GuardDuty/Security Hub/Macie = **탐지/집계**
  * Detective = **조사/원인분석(Investigation & Root cause)**
