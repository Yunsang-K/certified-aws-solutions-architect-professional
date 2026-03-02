# AWS Inspector

* **Amazon Inspector**는 **EC2 인스턴스(및 해당 OS)**, 그리고 **컨테이너 워크로드**를 대상으로 **취약점(CVE)과 보안 모범 사례 대비 구성 편차**를 점검하는 서비스입니다.
* Inspector는 **일정 기간(예: 15분, 1시간, 1일 등)** 동안 평가(Assessment)를 수행하여, 인스턴스를 위험에 노출시킬 수 있는 **비정상 구성/노출(예: 네트워크 도달 가능성)** 등을 식별할 수 있습니다.
* 평가 종료 후 **심각도(Severity) 기준으로 정렬된 Findings 리포트**를 제공합니다.

## 평가(Assessment) 유형

* **Network Assessment**

  * **에이전트 없이(Agentless)** 수행 가능
  * 다만 **에이전트를 설치하면 OS 관점의 가시성**이 추가되어 더 풍부한 분석이 가능
* **Network and Host Assessment**

  * **에이전트 설치 필수**
  * Host 평가가 **OS 레벨 취약점/구성**을 확인하므로 에이전트가 필요

## Rule Packages

* **Rule package**는 “인스턴스에서 무엇을 점검할지”를 정의하는 체크 항목 묶음입니다.

### 예시 Rule Packages

* **Network Reachability**

  * **에이전트 없이도** 가능하며, 에이전트가 있으면 **OS 레벨 가시성**이 추가됨
  * **엔드-투-엔드(End-to-End) 도달 가능성**을 점검
  * 대표 Findings:

    * `RecognizedPortWithListener`
    * `RecognizedPortNoListener`
    * `RecognizedPortNoAgent`
    * `UnrecognizedPortWithListener`

* **Host Assessment**

  * **에이전트 필수**
  * 점검 범위:

    * **CVE(Common Vulnerabilities and Exposures)** 기반 취약점
    * **CIS(Center for Internet Security) Benchmarks**
    * Inspector 보안 베스트 프랙티스 기준 점검
