# AWS Certificate Manager (ACM)

* **HTTPS(SSL/TLS)** 는 HTTP에서 발생하는 보안 문제를 해결하기 위해 설계되었다. *(원문: SSL/TSL → 보통 SSL/TLS)*
* HTTPS는 **전송 중 데이터 암호화(in-transit)** 와 **서버/서비스 신원 증명(인증서)** 을 제공한다.
* ACM은 **퍼블릭 인증서 발급(공개 CA)** 을 지원하며, 별도 서비스인 **ACM Private CA(사설 CA)** 를 통해 **프라이빗 CA** 도 구성할 수 있다.
* 프라이빗 CA를 사용하는 경우, 애플리케이션/클라이언트가 **해당 프라이빗 CA를 신뢰하도록(trust store에 추가 등)** 구성해야 한다.
* ACM을 통해 **인증서를 생성하거나(import 포함) 가져올 수 있다.**
* ACM이 **발급한 인증서** 는 **자동 갱신(auto renewal)** 이 가능하다.
  반면 **가져온(imported) 인증서** 는 **사용자가 직접 갱신해야 한다.** ✅ **EXAM**
* ACM의 인증서는 **ACM과 통합된(지원되는) AWS 서비스에만 배포/연결** 할 수 있다.
* 모든 서비스가 지원되는 것은 아니다. (예: **EC2 인스턴스에 직접 “배포”는 불가**) ✅ **EXAM**
* ACM은 **리전(Regional) 서비스** 이다.
* 인증서는 **생성/가져온 리전을 벗어나 이동할 수 없다.**
  예를 들어 **ap-southeast-2의 ALB** 에서 쓰려면 인증서도 **ap-southeast-2 리전의 ACM** 에 있어야 한다. ✅ **EXAM**
* **CloudFront 같은 글로벌 서비스** 에서 사용할 인증서는 **us-east-1(N. Virginia)** 에 저장/발급해야 한다. ✅ **EXAM**
