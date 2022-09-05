---
title: "NCS (3) - nRF Connect SDK 2.x.x 시작하기"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - NCS 2.x.x
  - 노르딕
  - 노르딕 펌웨어 개발
  - nRF Connect SDK 개발
  - NCS 버전 업데이트
  - getting started
  - tutorial
toc: true
toc_sticky: true
---

## 3. nRF Connect SDK 2.x.x 시작하기 (getting started)

현 직장에서 `nRF5 SDK` 기반으로 개발을 하다보니 `nRF Connect SDK (이하 NCS)` 개발 공부가 계속 미뤄지고 있다. `NCS` 공부좀 해보려고 했을 당시만 해도 설치했던 `SDK`가  `v.1.9.1` 버전이었는데, 지금은 최신 릴리즈가 `v2.0.2` 버전이다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig1.png" alt="">
</figure>

간만에 블루투스 메시 (`Bluetooth mesh`) 기능을 좀 테스트 해보려고 했는데, 시작하기 전에 `nRF Connect SDK` 를 새로 다운 받아야할 것 같다. 

* [https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/gs_updating.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/gs_updating.html)

원래는 위의 링크를 이용해서 `SDK` 업데이트를 진행하면 되는 줄 알았는데, 해당 링크는 기존에 설치된 `SDK` 를 업데이트 하는 기능이니 새로운 버전을 설치하려면 `repository` 에서 `git clone` 으로 새로 내려받거나, Toolchanin Manager 를 이용해서 다른 버전의 `SDK` 를 받아야 할 것 같다.


### 3.1 Download New SDK, after updating "Toolchanin Manager"

본인은 `Windows OS` 를 사용하고 있기 때문에, `nRF Coonect for Desktop` App 에서 제공되는 `Tool chanin Manager` 를 이용해 `v2.0.2 SDK` 를 새로 내려받았다.

* **`version info`**: nRF Connect for Desktop: **`v3.11.1`**, Toolchain Manager: **`v1.1.3`**

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig7.png" alt="">
</figure>

---

### 3.2 Create new application (basic sample flashing test)

새로운 버전의 `SDK` 가 정상적으로 설치되었는지 확인차 예제 샘플 (`peripheral_uart`)을 빌드하고 업로드 하는 것 까지 테스트를 해봤다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig3.png" alt="">
</figure>

>위 그림에서 볼 수 있듯이, 새로운 어플리케이션 프로젝트 생성 시 적용할 `SDK` 버전을 선택할 수 있으니 메모리가 여유가 있다면 굳이 이전 버전 `SDK` 를 지우지는 않아도 될 것 같다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig4.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig6.png" alt="">
</figure>

> 어플리케이션 생성 및 빌드, 플래시 (업로드) 기능은 이전 `~ v1.9.2` 까지의 버전과 동일한 듯 하다. (크게 이상한 점을 느끼지는 못했음)

다만, 아래 그림에서 볼 수 있듯이, `project configuration (=prj_x.conf)` 파일 형식이 조금 바뀐 듯 하다 (원래 이렇게 여러 개가 있었나?). `app.overlay, usb.overlay` 파일도 원래 프로젝트에 포함되던 파일인지는 모르겠는데... `v2.x.x` 버전으로 업데이트 되면서 `SDK` 구조도 조금 변경된 것 같다. (더 좋은 방향으로 수정된 건 맞겠지?...😒)

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-update-fig5.png" alt="">
</figure>

일전에 소개했던 **[https://academy.nordicsemi.com/topic/devicetree/](https://academy.nordicsemi.com/topic/devicetree/)** 페이지에도 `v.2.x.x` 버전을 사용하는 경우를 별도로 분리해놓은 것으로 봐서는 뭔가 구조가 조금 바뀐 것 같은데... 이 부분은 실제 개발해보면서 확인해야할 것 같다.

