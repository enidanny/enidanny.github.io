---
title: "NCS (5) - nRF Connect SDK 예제 따라하기 - Bluetooth: Mesh light"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - NCS 2.x.x
  - nordic semiconductor
  - 노르딕
  - 노르딕 펌웨어 개발
  - nRF Connect SDK 개발
  - 블루투스 메시
  - 블루투스 메시 튜토리얼
  - 노드
  - Bluetooth mesh
  - mesh light
  - generic model
  - generic onoff server
  - tutorial
  - nRF Mesh
toc: true
toc_sticky: true
---

## 5. nRF Connect SDK 예제 따라하기 - Bluetooth: Mesh light

이번 포스팅에서는 `nRF Connect SDK` (이하, **`SDK`**) 예제 (`sample`) 중 `Bluetooth: Mesh light` 예제를 따라해보고 어떤 식으로 동작하는지에 대해 간략하게 설명하고자 한다. 보다 자세한 내용이 필요하다면, 아래의 노르딕 (`nordic`) 공식 페이지를 참조하면 될 것 같다.

**Reference:** [https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/samples/bluetooth/mesh/light/README.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/nrf/samples/bluetooth/mesh/light/README.html)
{: .notice--primary}

### 5.1 Bluetooth: Mesh light 예제 빌드 및 업로드 (flash)

`SDK` 에서 `Create a new application` 메뉴를 선택 후, 노르딕에서 제공되는 샘플 어플리케이션 중 `Bluetooth: Mesh light` 예제를 다운받아 빌드 및 업로드를 진행한다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig1.png" alt="">
</figure>

>`SDK` 에 대한 세세한 내용 (`e.g. 개발환경 설치, 빌드, 등`)은 별도로 다루고 있지 않으니, 관련 내용을 처음 접하는 분들은 **[이전 포스팅](https://enidanny.github.io/nrf%20connect%20sdk/nRF-Connect-SDK-intro/)** 의 소개된 링크를 참고해서 어느정도 `SDK` 개발에 익숙해지는 것이 먼저일 듯 하다.

참고로 필자는 **`SDK v2.0.2`** 버전을 이용하였으며, 별도의 코드 수정 없이 노르딕에서 제공된 예제 코드를 그대로 사용하였으므로 각자 사용하는 보드 (`e.g. nRF52840-DK`)에 정상적으로 실행 파일이 업로드 되었다면 PC 에서 추가로 작업할 부분은 없다.

---

### 5.2 Bluetooth: Mesh light `main.c` 구조 ~~분석~~

본 예제의 `main.c` 에서 사용되는 헤더는 다음과 같다.

🗂️ `<bluetooth/mesh/...>` 로 시작하는 헤더가 `mesh` 기능을 사용하기 위한 공통 라이브러리라고 보면 되고, 본 예제에서 구체적으로 사용되는 모델 (`model`)은 `model_handler.h` 에 정의되어있다. 본 예제의 경우 `mesh profile` 의 `Generic OnOff Server` 모델과 노르딕 하드웨어 LED 를 어떤 식으로 연결하고 설정했는지에 대한 부분이 `model_handler.h` 헤더와 `model_handler.c` 파일에 정의되어있으니 추가적으로 확인해보면 좋을 듯하다.

<figure style="width: 87%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig2.png" alt="">
</figure>

`main.c` 의 소스코드는 상당히 간단한 형태이다.

<figure style="width: 90%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig3.png" alt="">
</figure>

* `bt_enable`: 블루투스 (or BLE) 기능 초기화 함수. BLE 초기화가 성공적으로 완료되었을 때 불러올 수 있는 `callback` 함수를 인자로 전달받을 수 있다. (본 예제에서는 `bt_ready`)

참고로 `bt_ready` 콜백 함수에서 실제로 블루투스 메시 (`mesh`)와 직접적으로 관련이 있는 함수는 위의 코드에서 별도로 표시한 `1, 2` 부분 밖에 없다.

>다른 함수들은 메시 기능을 사용하지 않는 블루투스 예제에서도 동일하게 사용되며, 보통의 경우는 `bt_enable` 호출 시 인자를 `NULL` 값으로 설정하고 나머지 초기화 함수들을 그 뒤에 이어서 호출하는 방식이다. 아래 `peripheral-uart` 예제의 `main()` 함수를 참고할 것.

<figure style="width: 90%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig4.png" alt="">
</figure>

그럼에도 불구하고.. 메시 예제 샘플의 코드가 `bt_enable(bt_ready)` 이러한 형태로 구성된 이유는 아무래도 `mesh profile` 자체가 `BLE stack` 을 기반으로 동작하기 때문에, 반드시 블루투스 기능 초기화가 마무리 된 이후에 관련 함수들을 사용하기 위함인 것으로 추정된다 🍒.

---
### 5.3 nRF Mesh mobile app. 이용해서 테스트 하기

`SDK` 에서 제공되는 메시 샘플은 기본적으로 노르딕에서 제공해주는 **[nRF Mesh](https://www.nordicsemi.com/Products/Development-tools/nrf-mesh)** 스마트폰 앱을 이용해서 제어가 가능하다. 참고로 필자는 현재 보급형 안드로이드 폰 (갤럭시 A 시리즈)을 사용하고 있으며, **`nRF Mesh`** 앱 버전은 `3.2.4` 이다.

#### 5.3.1 프로비저닝 (provisioning)

**`nRF Mesh`** 앱을 이용해서 기기를 메시 네트워크에 추가하는 (프로비저닝, provisioning) 과정은 다음과 같다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig5.png" alt="">
</figure>

>`Identify` 버튼을 누르면, Mesh Light 샘플이 업로드 된 기기의 LED 가 깜빡이는 것을 확인할 수 있을 것이고, 이어서 `Provision` 버튼을 눌러 Provisioning 을 진행하면 된다. (`Provision` 버튼 클릭 이후 OOB 설정을 묻는 메시지 박스가 뜰텐데, 본인의 경우 일단 기본 옵셥 `No-OOB` 로 선택했음)

<figure style="width: 90%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig6.png" alt="">
</figure>

프로비저닝이 시작되면 **`nRF Mesh`** 앱과 기기 사이의 명령어 교환이 이루어지고, 잠시 뒤 설정까지 완료될 것이다(~~오류가 발생하지 않는다면~~). 프로비저닝이 완료되면, 이제 `Mesh light` 샘플 기기를 **`nRF Mesh`** 앱과 같은 메시 네트워크에 구성된 노드 (`Node`)가 되고, 위의 사진에서 볼 수 있듯이 앱의 `Network` 메뉴에서 `Mesh light` 노드가 표시되는 것을 확인할 수 있다.

#### 5.3.2 Element 및 Model 구조

`Mesh light` 샘플 노드의 내부 구성요소는 4 개의 `element` 와 6 개의 `model` 로 구성된다.

<figure style="width: 90%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light-fig7.png" alt="">
</figure>

`element`와 `model` 에 대해서는 개념이 조금 생소할 수 있는데, 일단 필자는 **"특정 `model` 을 이용해서 다루고자하는 대상이 `element`"** 라고 이해하고 있다.

본 `Mesh light` 샘플 예제에서는 결국 노드 (장치)에 포함된 `N` 개의 LED 를 제어하는 것이 목표인데... 이 때 노드가 갖는 각각의 LED 가 `element` 가 되는 것이고, 블루투스 메시 프로토콜을 이용해 해당 `LED` 의 상태를 보여주기 위해 본 예제에서는 `Generic OnOff Server` `model` 이 사용되었다.

>`Generic OnOff Server` 는 LED 같이 단순하게 `0` 혹은 `1` 의 상태로 표시할 수 있는 `element` 들의 정보를 교환하기 위해 고안된 `model` 이다. 하나의 `element` 내 동일한 `model` 을 포함할 수 없기 때문에, 본 예제에서는 4 개의 LED 상태를 나타내기 위해 4 개의 `element` 가 정의되어 있는 것.

참고로, 일반적으로 첫 번째 (특정 노드 내 `element` 주소 값이 가장 작은) `element` 에는 메시 네트워크의 무선 통신 관련 설정을 관리하기 위해 `Config. Server` 와 `Health Server` 모델을 포함하고 있는 듯 하다.

>`nRF Mesh` 앱 이용해서 LED 제어하는 방법까지 기록하려고 했으나, 생각보다 글이 길어지는 듯 해서 해당 내용은 다음 포스팅으로 미루려고 한다. **예제 따라하기** 포스팅이 처음이라서 그렇지, 다음부터는 이렇게까지 하나하나 (?) 기록하지는 않을 듯 하다


