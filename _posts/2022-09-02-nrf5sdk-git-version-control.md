---
title: "nRF5 SDK (14) - git 이용해서 nRF5 SDK 버전 관리하기"
categories:
  - nRF5 SDK
tags:
  - git
  - VCS
  - 깃
  - 버전 관리
  - 깃 연동
  - 노르딕 펌웨어 개발
  - 노르딕 git
  - nordic git
  - nRF5 SDK 깃 연동
toc: true
toc_sticky: true
---

## 14. git 이용해서 nRF5 SDK 버전 관리하기

개발자에게 있어서 버전 관리 (`Version Control`)는 필수인데.. 본인의 경우 현 직장에서 노르딕(`nordic`) `BLE` 칩 개발을 혼자 담당하고 있어서 그 동안은 제대로 관리를 하지 않고 있었다. 이번 포스트에서는 `git` 이용해서 `nRF5 SDK` 개발 시 버전 관리하는 방법에 대해 기록해두려고 한다.

**NOTICE:** 이번 포스트의 경우는 `nRF5 SDK` 에 대해 어느 정도 알고 있다는 가정하에 작성했으며, 본인의 경우 `git` 에 대해 기본적인 개념밖에 모르기 떄문에, `git` 이나 버전 관리 관련해서 보다 자세한 내용을 알고 싶다면, 다른 분들 포스팅을 참고할 것.
{: .notice--success}

### 14.1 repository 생성

먼저, `nRF5 SDK` 를 이용해서 특정 어플리케이션을 개발한다고 가정해보자. 

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-4.png" alt="">
</figure>

본인의 경우 `BLE` 펌웨어 개발 시, 로컬 `nRF5 SDK` 디렉토리 내에 📁`nRF5_SDK.../examples/ble_peripheral/` 경로 내에 어플리케이션 디렉토리를 하나 생성해서 빌드를 하는 편이다. 위 그림에서는 `ble_app_uart` 라는 `nordic` 에서 기본으로 제공되는 예제 프로그램을 버전 관리할 어플리케이션 대상으로 가정하고 있다.

실제로 펌웨어 개발 단계에서는 보통의 경우 `main.c` 소스 코드만을 다루기 때문에, 위 그림의 폴더를 로컬 `repository` 로 설정한다고 생각하면 될 것 같다.

> `main.c` 소스코드 이외에 하드웨어 설정이나, 특정 라이브러리를 변경해서 쓰는 경우도 있지만 본 포스팅에서는 그 부분은 다루지 않음.

>또한, 작업하는 nRF5 SDK 전체를 repository 로 사용해도 되기는 한데, 대부분의 driver 함수 (API)는 변경하거나 할 일이 거의 없기 때문에, 본인처럼 혼자서 프로젝트를 관리하는 경우에는 어플리케이션 단위로 버전 관리를 하는게 적당하지 않을까 싶다.

본론으로 돌아와서.. 앞서 소개한 그림을 예로 들자면, 

📁**`nRF5_SDK.../examples/ble_peripheral/ble_app_uart`** 

경로의 폴더와 파일을 저장할 repository 를 하나 생성해야하는데, 본인은 개인 `github` 계정 내에 아래 그림과 같이 private repository 를 생성해서 사용했다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-1.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-3.png" alt="">
</figure>

> respsitory 이름은 대충 ble_app_uart_example 로 지었음.

해당 repository 를 아무 디렉토리에 내려 받으면 (`clone`), 아래 그림과  `.git` 이라는 폴더가 포함된 비어있는 디렉토리를 내려받을 수 있는데, 이 `.git` 폴더를 내가 관리하고자 하는 디렉토리에 넣으면 커밋이나 푸시 등의 명령을 이용해서 버전 관리가 가능해진다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-5.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-6.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-7.png" alt="">
</figure>

---

### 14.2 Visual Studio Code 실행 후 작업 폴더에 추가

위의 과정만 끝냈다면 보통의 경우처럼 `SEEGER Embedded Studio` (이하, `SES`) 이용해서 어플리케이션을 개발하고, `.git` 폴더를 추가한 디렉토리에서 커밋, 푸쉬 작업을 해주면 끝이다. 이를 `Visual Studio Code` (이하, `VS Code`)를 이용해서 하면 좀 더 편리할 것이다.

`VS Code` 실행 후 아래 그림과 같이 **`탐색기`** 항목에서 작업할 폴더를 추가해주면, 쉽게 버전 관리를 할 수 있다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-9.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-10.png" alt="">
</figure>

> 아래 그림에서 볼 수 있는 것처럼 `commit` 이나 `push` 를 보다 편하게 할 수 있음.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-11.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-12.png" alt="">
</figure>

그래서 요새는 작업할 때 `VS Code` 와 `SES` 를 둘 다 켜놓고 작업을 하고 있다. 개발은 `VS Code` 로 하고 빌드하고 디버깅하는 것은 `SES` 이용해서 함. 오히려 자체적으로 크로스 체크도 되는 것 같고 버전 관리도 쉬워서... 처음에는 조금 불편했는데, 한 일주일 정도 작업하고 나니깐 조금은 익숙해진 것 같음.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-16.png" alt="">
</figure>

> nRF Connect SDK 의 경우 온전히 `VS Code` 내에서 개발할 수 있기 때문에 `nRF5 SDK` 보다는 버전 관리하기가 편할 것 같기도 하다.

---

### 14.3 SEEGER Embedded Stuido (SES) 셋팅

대부분의 어플리케이션 개발 플랫폼에서는 버전 관리 (`Version Control System (VCS)`) 기능이 제공되고 있는데, 아래 링크에 접속해보면 알겠지만 `SES` 에서는 반쪽짜리 `VCS` 기능만을 제공하고 있다.

* [https://wiki.segger.com/VCS_Configuration](https://wiki.segger.com/VCS_Configuration)

`VCS` 기능을 이런 식으로 제공한다고 나와있긴한데... 실제 적용해보면 의미없는 기능이고, `SES` 내에서 `push` 도 되지 않고, 변경된 코드 비교하기도 불편해서 개발에 전혀 도움이 안 된다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-14.png" alt="">
</figure>

위 그림처럼 `SES` 실행 후, 상단의 **`Tools > Options...`** 에 들어가서 `Source Control` 내 정보를 입력해주면 (본인의 경우 `git` 을 이용하기 떄문에 `Git` 부분의 정보를 입력했음), `SES` 에서도 버전 관리 시스템이 인식되기는 하는데... 아래 그림처럼 `Applicaion` 폴더 내에 체크 표시가 생기는 정도...?

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/nRF5-SDK-VCS/fig4-15.png" alt="">
</figure>

> 본인이 소개한 것처럼 `.git` 폴더를 예제 디렉토리 안에 넣었다면, 위 그림처럼 `nRF5 SDK` 의 다른 영역에 있는 라이브러리 코드는 버전 관리 대상이 아니므로 ⛔ 표시가 뜰 것이다. 실제 버전 관리 대상이 되는 코드는 📁`Application` 폴더에 있는 파일들만 해당되므로, 여기에만 ✔️ 표시가 되어있음.

근데, `SES` 셋팅은 굳이 안해도 된다. 결과적으로는 `SES` 에서 버전 관리 기능이 제공되지 않는다고 봐야해서 ✔️ 표시 뜨고 안 뜨고의 차이 정도라고 생각하면 됨.