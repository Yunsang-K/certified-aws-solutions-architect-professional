# AWS Certified Solutions Architect – Professional (SAP-C02) 노트

## 목차

1. 고급 권한 및 계정

   * [MFA](01-accounts/mfa.md)
   * [IAM](01-accounts/iam.md)
   * [AWS Organizations](01-accounts/organizations.md)
   * [STS](01-accounts/sts.md)
   * [정책(Policies)](01-accounts/policies.md)
   * [AWS Resource Access Manager - RAM](01-accounts/ram.md)
   * [AWS 서비스 할당량(Service Quotas)](01-accounts/service-quotas.md)

2. 고급 자격 증명 및 페더레이션

   * [SAML 2.0 자격 증명 페더레이션](02-identity/saml.md)
   * [IAM Identity Center (기존 AWS Single Sign-On - SSO의 후속 서비스)](02-identity/identity-center.md)
   * [Amazon Cognito](02-identity/cognito.md)
   * [Amazon Workspaces](02-identity/workspaces.md)
   * [AWS Control Tower](02-identity/control-tower.md)

3. 네트워킹 및 하이브리드

   * [VPC - 가상 프라이빗 클라우드](03-networking/vpc.md)
   * [VPN](03-networking/vpn.md)
   * [AWS Transit Gateway](03-networking/transit-gateway.md)
   * [BGP - 경계 경로 프로토콜](03-networking/bgp.md)
   * [AWS Global Accelerator](03-networking/global-accelerator.md)
   * [DX - Direct Connect](03-networking/direct-connect.md)
   * [Route53](03-networking/route53.md)
   * [AWS PrivateLink](03-networking/privatelink.md)

4. 스토리지 서비스

   * [FSx](04-storage/fsx.md)
   * [EFS - Elastic File System](04-storage/efs.md)
   * [S3](04-storage/s3.md)
   * [Amazon Macie](04-storage/macie.md)
   * [EBS 및 인스턴스 스토어](04-storage/ebs.md)
   * [AWS Transfer Family](04-storage/transfer-family.md)

5. 컴퓨팅, 확장 및 로드 밸런싱

   * [리전 및 글로벌 AWS 아키텍처](05-compute/aws-architecture.md)
   * [EC2](05-compute/ec2.md)
   * [ELB - Elastic Load Balancer](05-compute/elb.md)
   * [ASG - Auto Scaling Groups](05-compute/asg.md)
   * [Gateway Load Balancer (GWLB)](05-compute/gwlb.md)
   * [기타 컴퓨팅 관련 서비스](05-compute/others.md)

6. 모니터링, 로깅 및 비용 관리

   * [CloudWatch](06-monitoring/cloudwatch.md)
   * [CloudTrail](06-monitoring/cloudtrail.md)
   * [AWS X-Ray](06-monitoring/xray.md)
   * [비용 할당 태그(Cost Allocation Tags)](06-monitoring/cost-allocation-tags.md)
   * [AWS Trusted Advisor](06-monitoring/trusted-advisor.md)
   * [AWS Billing and Cost Management](06-monitoring/billing.md)
   * [AWS Compute Optimizer](06-monitoring/compute-optimizer.md)

7. 데이터베이스

   * [RDS - 관계형 데이터베이스 서비스](07-databases/rds.md)
   * [Amazon Aurora](07-databases/aurora.md)
   * [DynamoDB](07-databases/dynamodb.md)
   * [AWS OpenSearch (이전 명칭: ElasticSearch)](07-databases/opensearch.md)
   * [Amazon Athena](07-databases/athena.md)
   * [Amazon Neptune](07-databases/neptune.md)
   * [Amazon Quantum Ledger Database - QLDB](07-databases/quantum-ledger.md)

8. 데이터 분석

   * [Kinesis](08-data-analytics/kinesis.md)
   * [EMR - Elastic Map Reduce](08-data-analytics/emr.md)
   * [Amazon Redshift](08-data-analytics/redshift.md)
   * [AWS Batch](08-data-analytics/aws-batch.md)
   * [AWS QuickSight](08-data-analytics/quicksight.md)
   * [기타 데이터 분석 관련 서비스](08-data-analytics/others.md)

9. 애플리케이션 서비스, 컨테이너 및 서버리스

   * [ECS - Elastic Container Service](09-containers-and-serverless/ecs.md)
   * [EKS - Elastic Kubernetes Service](09-containers-and-serverless/eks.md)
   * [SNS - Simple Notification Service](09-containers-and-serverless/sns.md)
   * [SQS - Simple Queue Service](09-containers-and-serverless/sqs.md)
   * [Amazon MQ](09-containers-and-serverless/mq.md)
   * [AWS Lambda](09-containers-and-serverless/lambda.md)
   * [CloudWatch Events 및 EventBridge](09-containers-and-serverless/eventbridge.md)
   * [API Gateway](09-containers-and-serverless/api-gateway.md)
   * [AWS Step Functions](09-containers-and-serverless/step-functions.md)
   * [SWF - Simple Workflow Service](09-containers-and-serverless/swf.md)
   * [Amazon Mechanical Turk](09-containers-and-serverless/mechanical-turk.md)
   * [Elastic Transcoder 및 AWS Elemental MediaConvert](09-containers-and-serverless/mediaconvert.md)
   * [AWS IoT](09-containers-and-serverless/iot.md)
   * [AWS SAM - Serverless Application Model](09-containers-and-serverless/sam.md)
   * [AWS App Runner](09-containers-and-serverless/app-runner.md)

10. 캐싱, 전송 및 엣지

* [CloudFront](10-caching/cloudfront.md)
* [ElastiCache](10-caching/elasticache.md)

11. 마이그레이션 및 확장

* [클라우드 마이그레이션의 6R](11-migrations/6r.md)
* [DMS - Database Migration Service](11-migrations/dms.md)
* [VM 마이그레이션 AWS <=> 온프레미스](11-migrations/vm-migration.md)
* [Storage Gateway](11-migrations/storage-gateway.md)
* [Snowball 및 Snowmobile](11-migrations/snow.md)
* [AWS DataSync](11-migrations/datasync.md)

12. 보안 및 구성 관리

* [AWS GuardDuty](12-security-and-config/guard-duty.md)
* [AWS Config](12-security-and-config/config.md)
* [AWS Inspector](12-security-and-config/inspector.md)
* [암호화 및 KMS](12-security-and-config/kms.md)
* [CloudHSM](12-security-and-config/cloudhsm.md)
* [AWS Certificate Manager - ACM](12-security-and-config/acm.md)
* [AWS Systems Manager Parameter Store](12-security-and-config/parameter-store.md)
* [AWS Secrets Manager](12-security-and-config/secrets-manager.md)
* [VPC Flow Logs](12-security-and-config/vpc-flow-logs.md)
* [애플리케이션(7계층) 방화벽](12-security-and-config/application-firewalls.md)
* [AWS Shield](12-security-and-config/shield.md)
* [AWS 네트워크 및 DNS 방화벽](12-security-and-config/network-and-dns-firewall.md)
* [AWS Detective](12-security-and-config/detective.md)
* [Security Hub](12-security-and-config/security-hub.md)
* [AWS Audit Manager](12-security-and-config/audit-manager.md)

13. AWS의 재해 복구 및 비즈니스 연속성

* [DR/BC 아키텍처](13-disaster-recovery/dr.md)
* [AWS Elastic Disaster Recovery (DRS)](13-disaster-recovery/drs.md)

14. 코드형 인프라

* [CloudFormation](14-iac/cloudformation.md)

15. 배포 및 관리

* [Service Catalog](15-deployment/service-catalog.md)
* [CI/CD](15-deployment/cicd.md)
* [Elastic Beanstalk - EB](15-deployment/eb.md)
* [AWS OpsWorks](15-deployment/opsworks.md)
* [AWS Systems Manager (SSM)](15-deployment/ssm.md)
* [AWS Proton](15-deployment/proton.md)

16. 기타

* [Amazon Lex 및 Amazon Connect](16-other/lex.md)
* [Amazon Comprehend](16-other/comprehend.md)
* [Amazon Kendra](16-other/kendra.md)
* [Amazon Polly](16-other/polly.md)
* [AWS Rekognition](16-other/rekognition.md)
* [Kinesis Video Streams](16-other/kinesis-video-streams.md)
* [AWS Glue](16-other/glue.md)
* [AWS Device Farm](16-other/device-farm.md)
* [Amazon Textract](16-other/textract.md)
* [Amazon Transcribe](16-other/transcribe.md)
* [Amazon Forecast](16-other/forecast.md)
* [Amazon Fraud Detector](16-other/fraud-detector.md)

## 시험 설명

AWS Certified Solutions Architect - Professional은 AWS에서 클라우드 아키텍처를 설계하고 배포한 실무 경험이 2년 이상인 사람을 대상으로 합니다. 시험 응시 전, 다음 역량을 갖추고 있을 것을 권장합니다.

* AWS CLI, AWS API, AWS CloudFormation 템플릿, AWS Billing Console, AWS Management Console, 스크립트 언어, Windows 및 Linux 환경에 대한 이해
* 엔터프라이즈의 여러 애플리케이션과 프로젝트 전반에 걸쳐 아키텍처 설계에 대한 모범 사례 가이드를 제공할 수 있는 능력과, 비즈니스 목표를 애플리케이션/아키텍처 요구사항에 매핑할 수 있는 능력
* 클라우드 애플리케이션 요구사항을 평가하고, 애플리케이션 구현·배포·프로비저닝을 위한 아키텍처 권장사항을 제시할 수 있는 능력
* 핵심 AWS 기술(예: VPN, AWS Direct Connect)을 사용한 하이브리드 아키텍처와 지속적 통합 및 배포 프로세스를 설계할 수 있는 능력

## 준비

* 공식 시험 가이드: [https://d1.awsstatic.com/training-and-certification/docs-sa-pro/AWS-Certified-Solutions-Architect-Professional_Exam-Guide.pdf](https://d1.awsstatic.com/training-and-certification/docs-sa-pro/AWS-Certified-Solutions-Architect-Professional_Exam-Guide.pdf)

* 시험에는 두 가지 유형의 문제가 포함될 수 있습니다.

  * 객관식(Multiple choice)
  * 복수 응답형(Multiple response)

* 최소 합격 점수: 750/1000

* 문항 수: 75문항

* 시험 시간: 190분 (영어 비원어민의 경우 30분 추가 시간 요청 가능)

## 출처

* 이 노트는 Adrian Cantrill의 [AWS Certified Solutions Architect - Professional 과정](https://learn.cantrill.io/p/aws-certified-solutions-architect-professional)과 Stephane Maarek의 [Ultimate AWS Certified Solutions Architect Professional 2021](https://www.udemy.com/course/aws-solutions-architect-professional/)을 기반으로 작성되었습니다.
* 노트에 포함된 모든 이미지는 Adrian Cantrill의 강의용 GitHub [저장소](https://github.com/Ernyoke/aws-sa-pro)에서 가져온 것입니다.
