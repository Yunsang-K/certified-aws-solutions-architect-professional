# AWS SAP Study Plan

AWS SAP-C02 학습 세션에서 읽을 파일 순서를 정의한다.

## 기본 루틴

1세션당 다음을 수행한다.

1. 레포 파일 1~2개 읽기
2. 서비스 비교표 만들기
3. SAP 스타일 문제 10문제 출제
4. 채점 후 오답노트와 배운점 정리

## 우선순위 파일 순서

1. `01-accounts/organizations.md`
2. `01-accounts/policies.md`
3. `02-identity/identity-center.md`
4. `03-networking/vpc.md`
5. `03-networking/transit-gateway.md`
6. `03-networking/direct-connect.md`
7. `03-networking/route53.md`
8. `13-disaster-recovery/dr.md`
9. `07-databases/rds.md`
10. `07-databases/aurora.md`
11. `07-databases/dynamodb.md`
12. `11-migrations/dms.md`
13. `11-migrations/datasync.md`
14. `09-containers-and-serverless/sqs.md`
15. `09-containers-and-serverless/step-functions.md`
16. `06-monitoring/billing.md`
17. `12-security-and-config/kms.md`
18. `12-security-and-config/config.md`

## 세션 배치 초안

### Session 001

- `01-accounts/organizations.md`
- `01-accounts/policies.md`

주제:

- AWS Organizations
- SCP
- IAM Policy
- Permission Boundary
- Management Account
- OU 설계

### Session 002

- `02-identity/identity-center.md`
- `03-networking/vpc.md`

주제:

- IAM Identity Center
- Cross-account access
- VPC 기본 설계
- Public/Private subnet
- NAT Gateway
- VPC Endpoint

### Session 003

- `03-networking/transit-gateway.md`
- `03-networking/direct-connect.md`

주제:

- Transit Gateway
- TGW Route Table
- TGW Peering
- Direct Connect
- VPN
- Hybrid networking

### Session 004

- `03-networking/route53.md`
- `13-disaster-recovery/dr.md`

주제:

- Route 53 Routing Policy
- Failover
- Multi-Region
- Backup and Restore
- Pilot Light
- Warm Standby
- Active/Active

### Session 005

- `07-databases/rds.md`
- `07-databases/aurora.md`
- `07-databases/dynamodb.md`

주제:

- RDS Multi-AZ
- Read Replica
- Aurora Global Database
- DynamoDB Global Tables
- RTO/RPO
- Database migration 판단

## 5세션 후 누적 리포트

Session 001~005 완료 후 다음을 작성한다.

- 정답률
- 오답 목록
- 약점 태그 TOP 5
- 반복 오답
- 다음 5세션 보완 계획
