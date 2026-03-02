## AWS Secrets Manager

* **SSM Parameter Store와 기능이 일부 겹칩니다.**
  둘 다 파라미터/값을 안전하게 저장할 수 있지만, Secrets Manager는 **“비밀(Secrets)” 관리에 더 특화**되어 있습니다.

* **Secrets Manager는 비밀정보 전용 서비스입니다.**
  예: DB 비밀번호, API Key, 토큰, 자격 증명 등.

* **접근 방법:**
  AWS **콘솔**, **CLI**, **API**, **SDK**를 통해 사용할 수 있습니다.

* **자동 로테이션(회전)을 지원합니다.**
  **Lambda 함수를 사용**해 주기적으로 비밀값을 자동으로 변경(rotate)하도록 구성할 수 있습니다.

* **특정 AWS 서비스와의 직접 통합을 제공합니다.**
  대표적으로 **RDS**와 통합되어, 로테이션 시 **Secrets Manager의 값과 DB 자격 증명 변경을 연동/동기화**하는 구성이 가능합니다(지원 범위/방식은 엔진과 구성에 따라 달라짐).

* **KMS로 암호화됩니다.**
  저장된 시크릿은 **AWS KMS(Customer managed key 또는 AWS managed key)** 를 사용해 암호화(Encryption at rest)됩니다.
