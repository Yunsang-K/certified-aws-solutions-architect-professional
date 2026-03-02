# AWS SAM(Serverless Application Model)

* 서버리스 애플리케이션은 단순히 **Lambda 함수 1개**가 아니라, 보통 아래 같은 **여러 AWS 서비스 조합**으로 구성됩니다.

  * 프런트엔드 코드/정적 자산: **S3 + CloudFront**
  * API 엔드포인트: **API Gateway**
  * 컴퓨팅: **AWS Lambda**
  * 데이터베이스: **DynamoDB**
  * 이벤트 소스, 권한(IAM), 기타 연동 리소스 등

* AWS에서 말하는 **SAM 관련 구성요소**는 크게 2가지로 정리할 수 있습니다.

## 1) AWS SAM Template Specification

* **CloudFormation의 확장(Extension)** 입니다.
* 서버리스 애플리케이션을 더 쉽게 정의하도록, CloudFormation에 서버리스 전용 문법/리소스를 추가합니다.
* 대표적으로 다음 요소를 활용합니다.

  * **Transform**: SAM 문법을 CloudFormation으로 변환해주는 선언(예: `AWS::Serverless-2016-10-31`)
  * **Globals**: 함수/API 등 여러 리소스에 공통 설정을 일괄 적용
  * **Resources**:

    * 일반 CloudFormation 리소스도 함께 사용 가능
    * SAM 전용 리소스(예: `AWS::Serverless::Function`, `AWS::Serverless::Api` 등)도 사용 가능

## 2) AWS SAM CLI

* 개발/테스트/배포 워크플로우를 지원하는 CLI 도구입니다.
* 주요 기능:

  * **로컬 테스트**(예: API Gateway/Lambda를 로컬에서 흉내 내 실행)
  * **로컬 invoke**(함수 단위 실행/디버깅)
  * **AWS로 배포**(패키징, 변경셋 적용 등 배포 자동화)
