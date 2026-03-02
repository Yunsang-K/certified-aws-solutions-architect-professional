# AWS Systems Manager Parameter Store

* **시스템 구성 값**(문서, 설정, 시크릿, 문자열 등)을 **내구성·보안·확장성** 있게 저장하는 서비스
* 데이터는 **Key-Value** 형식으로 저장
* 많은 AWS 서비스가 Parameter Store와 **네이티브 통합**(예: CloudFormation, EC2 User Data/SSM Run Command, Lambda 환경변수/코드, ECS/EKS 등)
* 저장 가능한 값 타입은 3가지

  * **String**
  * **StringList**
  * **SecureString**(KMS로 암복호화되는 암호화 파라미터)
* 저장 예시: **라이선스 키**, **DB 접속 문자열**, **전체 애플리케이션 설정**, **비밀번호/토큰** 등
* 파라미터는 **계층형 경로(hierarchical)** 로 구성 가능(예: `/prod/app/db/password`)
* 파라미터는 **버전(versioning)** 을 지원하여 변경 이력을 관리 가능
* **평문(plaintext)** 과 **암호문(ciphertext)** 저장 모두 가능하며, SecureString은 **KMS 통합**으로 복호화 사용
* **Public Parameters**: AWS가 관리·제공하는 파라미터(예: 리전별 최신 AMI ID 같은 값)
* Parameter Store는 **퍼블릭 AWS 서비스 엔드포인트**를 통해 접근(네트워크 관점에서 “public service”로 분류)
* 접근 제어는 **IAM과 강하게 통합**(권한 정책으로 조회/수정/경로 단위 제한, KMS 키 권한과 함께 고려)

시험 포인트로는 보통 **SecureString = KMS**, **계층형 경로 + IAM으로 최소권한**, **버전 관리**, 그리고 Secrets Manager와의 차이(자동 로테이션 필요 여부 등)가 자주 나옵니다.
