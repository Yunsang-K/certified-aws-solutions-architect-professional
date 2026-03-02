## AWS Global Accelerator (SAP-C02 기준 번역)

* **사용자 → AWS**로 가는 트래픽(데이터 흐름)을 최적화하도록 설계된 서비스
* **CloudFront와 유사하게** AWS에 호스팅된 서비스와 통신할 때 성능을 개선함
* Global Accelerator는 **2개의 Anycast IP 주소**를 제공함

  * **Anycast IP**는 특수한 형태의 IP 주소
* 일반적인 IP는 **Unicast IP**라고 하며, **네트워크에서 하나의 장치(엔드포인트)**를 가리킴
* 반대로 **Anycast IP**는 **여러 장치/지점에서 동시에 동일한 IP를 광고(announce)**할 수 있고, 트래픽은 **사용자(소스)에 가장 가까운 지점**으로 라우팅됨
* Global Accelerator는 **AWS Edge Location**을 사용함

  * 여러 Edge Location이 해당 Anycast IP를 광고하기 때문에, 사용자 트래픽은 **가장 가까운 Edge Location**으로 유입됨
* AWS는 **자체 글로벌 백본 네트워크(전용 광(光) 링크 기반)**를 보유하고 있으며, Edge Location은 트래픽을 이 네트워크로 **릴레이**하여 성능(지연/경로 안정성 등)을 개선함

---

## CloudFront vs Global Accelerator (핵심 비교 번역)

* **CloudFront**: 콘텐츠를 Edge Location에 **캐시**해서 고객 가까이 가져옴
  **Global Accelerator**: 고객 트래픽을 **AWS 글로벌 네트워크로 빨리 태워서**, 서비스(오리진)까지의 경로를 최적화함
* **Global Accelerator는 네트워크 레벨 제품**이라 **TCP/UDP 기반 애플리케이션 전반**(웹앱의 HTTP/HTTPS 포함)에 적용 가능
  **CloudFront는 HTTP/HTTPS 콘텐츠 캐싱** 중심
* **Global Accelerator는 캐시를 하지 않음**
  또한 **HTTP/HTTPS 프로토콜을 이해(해석)하지 않는** L4 성격의 서비스임 (즉, 콘텐츠 최적화/캐싱 기능이 아님)
