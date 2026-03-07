## Amazon Kendra

* **지능형 검색 서비스**입니다.
* 주요 목적은 **사람 전문가와 대화하듯 검색 결과를 제공**하는 것입니다.
* 다음과 같은 다양한 질문 유형을 지원합니다.

  * **Factoid**: 누가, 무엇을, 어디서
  * **Descriptive**: 예를 들어 “고양이가 못되게 굴지 않게 하려면 어떻게 해야 하나요?” 같은 설명형 질문
  * **Keyword**: 예를 들어 “keynote address는 몇 시인가요?”처럼 단어가 여러 의미를 가질 수 있는 경우에도, **Kendra가 의도를 파악**하는 데 도움을 줍니다.

## 핵심 개념

* **Index**

  * 검색 가능한 데이터를 **효율적인 방식으로 구성한 검색 인덱스**입니다.

* **Data Source**

  * 데이터가 실제로 저장되어 있는 위치입니다.
  * Kendra는 이 위치에 연결하여 데이터를 인덱싱합니다.
  * 예시:

    * Amazon S3
    * Confluence
    * Google Workspace
    * Amazon RDS
    * OneDrive
    * Salesforce
    * Kendra Web Crawler
    * Amazon WorkDocs
    * Amazon FSx

* Kendra는 **스케줄**을 기준으로 데이터 소스를 인덱스와 동기화하도록 설정할 수 있습니다.

* 이를 통해 인덱스를 **최신 상태로 유지**할 수 있습니다.

* **Documents**

  * **정형 데이터**와 **비정형 데이터**를 모두 지원합니다.
  * 예시:

    * 정형 데이터: FAQ
    * 비정형 데이터: HTML, PDF 등

## 기타

* Kendra는 다른 AWS 서비스와도 통합됩니다.
* 예:

  * **IAM**
  * **AWS IAM Identity Center (SSO)**
  * 그 외 기타 AWS 서비스
