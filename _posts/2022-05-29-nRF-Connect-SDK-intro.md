---
title: "NCS (1) - NCS 소개 및 개발 가이드 / nRF Connect SDK Intro. & Development Guide"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - RTOS
  - Zephyr
  - Nordic semiconductor
  - 노르딕
  - 제퍼
  - 펌웨어
  - 노르딕 펌웨어 개발
  - 실시간 운영체제
toc: true
toc_sticky: true
---

## 1. NCS 소개 및 개발 가이드 / nRF Connect SDK Intro. & Development Guide

이전에 업로드 했었던 **[포스트](https://enidanny.github.io/nrf5%20sdk/nrf5sdk-nrfconnectsdk/)** 에서 간략하게 소개하긴 했었지만, 개인 개발 공부하는 겸 본격적으로 <span style="color:#209090"><b>nRF Connect SDK</b></span> **(이하, `NCS`)** 에 대해 정리하고자 함. 초반에는 `nRF5 SDK` 와 동일하게 `SEGGER Embedded Studio` (이하 `SES`) 를 통해서 개발 환경을 제공했었던 것 같은데.. 요새 튜토리얼이나 관련 자료들을 찾아보면, 이제는 **`Visual Studio Code` (이하, `VS Code`)** 이용하는 것으로 어느 정도 확정이 된 것 같음 🎬.

>여러 이유가 있겠지만, `SES` 에서는 **`VCS` (Version control system)** 기능이 제대로 지원되지 않는 것으로 알고 있음. 그 밖에도 코드 가독성이나 UX/UI 환경 등 `VS Code` 가 훨씬 사용하기 좋은 것 같음. 🖥️

### 1.1 nRF Connect SDK Installation and Setup

아래의 설치 관련 문서 및 유튜브 관련 영상 참고.

* **[https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/getting_started.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/getting_started.html)**
* **[https://www.youtube.com/playlist?list=PLx_tBuQ_KSqEt7NK-H7Lu78lT2OijwIMl](https://www.youtube.com/playlist?list=PLx_tBuQ_KSqEt7NK-H7Lu78lT2OijwIMl)**

{% include youtubeplayer.html id="2cv_jjqk5hg" %}

>위 영상은 **`NCS v1.7.0`** 기준으로 설치 과정을 설명하고 있지만, 필자가 위 영상 보면서 현재 **`Toolchain Manager v0.10.3`** 에서 제공되는 최신 버전인 **`NCS v1.9.1`** 이용해서 기본 개발 환경 설치했을 때는 크게 문제되는 부분은 없었음.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-intro-fig0.png" alt="">
</figure>

>어차피 `Toolchain Manager` 통해서 설치되는 버전은 어느 정도 검증이 된 안정적인 버전이라.. 기왕 처음 시작하는 거라면 최신 버전을 설치하는게 낫다고 생각. (Linux/Mac OS 사용하는 경우에는 다를 수 있음)

위 영상을 포함해서 앞서 소개한 **플레이리스트**에서 다루는 내용들은 **`DevAcademy Course`** 페이지에서도 다루는 내용들이지만, 기본적으로 `NCS` 설치부터 소스 코드 업로드하는 전반적인 과정을 다루고 있는 **`1 ~ 4`** 영상 정도는 보면서 따라해보는 것을 권장함 🚋.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-intro-fig2.png" alt="">
</figure>

---

### 1.2 Welcome to the nRF Connect SDK

아래의 링크는 `NCS` 관련된 문서들이 정리되어 있는 페이지이다.

* **[https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/index.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/index.html)**

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-intro-fig1.png" alt="">
</figure>

앞서 소개했던 `NCS` 설치 링크를 포함해서 각종 관련 문서들이 있음. 위 그림에 표시했듯이 우측상단의 버튼을 이용해서 원하는 버전의 `NCS` 문서를 볼 수 있고, 상단의 <span style="color:#209090"><b>nRF Connect SDK, nrfx, Zephyr Project</b></span> 버튼 등을 누르면 `NCS` 에서 사용된 각 모듈에 대한 상세한 정보들을 확인할 수 있음.

특히, `Zephyr RTOS` 나 `MCUboot` 같은 모듈은 `nordic` 외부에서 제공되는 오픈 소스 (`open source`)이므로, 관련 모듈의 문서에서는 `NCS` 내부에서 활용되는 것 이상으로 상세하고 방대한 내용들을 다루고 있음.

>자료는 상당히 많은데 어디서부터 시작해야할 지가 다소 막막함. 그런데..

---

### 1.3 DevAcademy 🏙️

최근 들어 `Nordic` 에서 `NCS` 에 대한 전반적인 내용들을 정리한 강의 (?) 사이트를 제공해주고 있다. 총 **8 개**의 주제 (**Lesson**)들을 다루고 있고, 주제별 간단한 설명과 함께, 실습 (`Exercise`)과 퀴즈 (`Quiz`) 등을 제공해주고 있음.

* **[https://academy.nordicsemi.com/courses/nrf-connect-sdk-fundamentals/](https://academy.nordicsemi.com/courses/nrf-connect-sdk-fundamentals/)**

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-intro-fig3.png" alt="">
</figure>

>앞서 소개한 `NCS` 설치를 다룬 영상에서 최소한 앞의 네 개의 영상은 보는 것을 권장했던 이유가, 본 페이지에서 실습 소스을 진행하려면 기본적으로 `NCS` 이용해서 소스 코드를 **빌드**하고, 자신이 사용하는 하드웨어 (`e.g. nRF528XX-DK, nRF9160-DK`)로 **업로드** 하는데 문제가 없어야 한다 ❗.

위 페이지에서 제공하는 강의 자료 중 개인적으로는 `NCS` 에서 사용되는 `Devicetree` 개념과 `Device driver model` 을 이용해서 하드웨어를 제어하는 방법에 소개하는 <span style="color:#A02050"><b>Lesson 2</b></span>  와 `RTOS` 기반의 어플리케이션 개발 방식과 몇 가지 `kernerl` 서비스 개념에 대해 다루고 있는 <span style="color:#A02050"><b>Lesson 7, 8</b></span> 내용이 가장 중요한 듯..

특히, `NCS` 는 **`Zephyr RTOS`** 🔮 기반으로 프로그램이 제어되기 때문에, 

* `main()` 함수 내에서 `inifinite-loop`를 생성하고 (**`e.g. for(;;){...}`**),
* 해당 `loop`를 기반으로 단일 `sequential logic` 을 구성하는 `nRF5 SDK` (`Bare-metal application`) 과는

구조적인 측면에서 큰 차이를 보이고 있음.

>특히, 필자의 경우 *(Real-Time)* `OS` 에서 다루는 활용되는 개념들이나 기능들을 다뤄본 적이 없었음 (`e.g. Semaphores, Mutexes`). 개념을 이해하고, 실제 프로그램에서 적절히 활용하는데 시간이 좀 걸릴 것 같다는 생각.. 이 든다~~람쥐~~.