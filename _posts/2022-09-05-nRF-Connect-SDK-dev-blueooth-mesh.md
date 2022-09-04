---
title: "NCS (4) - nRF Connect SDK 이용해서 블루투스 메시 개발하기"
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
  - Bluetooth mesh
toc: true
toc_sticky: true
---

## 4. nRF Connect SDK 이용해서 블루투스 메시 개발하기 / Supported Bluetooth mesh features

이전에 `nRF5 SDK for Mesh` 를 이용해서 블루투스 메시 기능을 개발했던 적이 있었는데, `nRF5 SDK` 라인은 더 이상 노르덱 (`nordic`)에서 기능상의 업데이트를 진행하고 있지 않기도 하고, `nRF Connect SDK` 개발에 익숙해질 필요도 있을 것 같아서, 다시 메시 프로토콜 관련 공부를 해보려고 한다.

>이전에 블루투스 메시 (`Bluetooth mesh`) 관련해서 정리했던 내용이 ~~안타깝게도~~ 하나도 기억이 나질 않아서, 기본적으로 알아두어야 하는 개념부터 천천히 다시 정리해보려고 한다.

* version info: `nRF Connect SDK v2.0.2`

### 4.1 Supported Bluetooth mesh features in nRF Connect SDK

아래 페이지에 접속해보면, 블루투스 메시 관련해서 현재 `SDK v2.0.2` 에서 지원되는 기능들이 간략하게 정리되어있다.

* [https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_supported_features.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_supported_features.html)

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-mesh1-fig1.png" alt="">
  <figcaption>
  출처: https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_supported_features.html
  </figcaption>
</figure>

>`NCS` 는 `Zephyr` RTOS 를 기반으로 동작하는데, 위의 기능들이 `Zephyr` 의 블루투스 메시 프로파일 (`프로파일 (Profile)` = 표준 규격 정도라고 이해)을 통해 구현 가능한 범위인 듯 하다.

---

### 4.2 Core

블루투스 메시 네트워크를 구성하기 위한 기본적인 노드들의 역할 및 상태를 정의하며, 이는 `Relay, Low power, Friend` 특성들을 포함한다.

>`Relay` 특성의 경우 메시 네트워크의 기본적인 특징이겠으나, `Low power` 및 `Friend` 노드 특성의 경우 초기 `SDK` 버전에서는 구현이 제대로 안되었던 것으로 기억한다.

#### 4.2.1 Low power node (LPN)

블루투스 메시 프로토콜의 경우 배터리를 통해 전원을 공급받는 휴대용 기기에 적용하기에는 적합한 프로토콜이 아니다. 프로토콜 특성상 무선 (`radio`) 모듈을 상시 동작시켜야 하므로 전력 소모가 상당하기 때문.

이를 보완하기 위해 고안된 것이 바로 이 `Low power node (LPN)` 이다. `LPN` 은 단독으로 사용될 수 없고, 반드시 주변에 위치하는 `Friend Node (FN)` 와 함께 사용되어야 한다. `FN` 는 대개 외부 전원을 공급 받는 노드이고, 상시 네트워크의 다른 노드들과 통신을 주고 받으며, `LPN` 은 일정 시간마다 무선 모듈을 활성화 시켜서 `FN` 를 통해 네트워크에 참여하는 방식이다.

>만약 특정 공간 내에 다수의 `Friend Node` 들이 외부 전원을 공급받으며 네트워크를 유지하고 있는 환경이라면, 배터리 전원으로 동작하는 기기를 `Low Power Node` 로 동작시켜서 메시 네트워크를 이용해 효과적으로 운용할 수 있을 듯 하다.

---

### 4.3 Provisioning

당연하게도 프로비저닝 (`Provisioning`) 기능도 `Zephyr` RTOS 를 통해 지원된다.

>프로비저닝이란? 메시 네트워크에 포함되지 않은 장치 (`unprovisioned device`)를 네트워크에 포함시키는 일련의 과정을 가리킨다. 프로비저닝을 통해 특정 장치가 메시 네트워크에 포함되면, 해당 기기를 노드 (`node`)라고 부른다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-mesh1-fig2.png" alt="">
  <figcaption>
  출처: https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_concepts.html
  </figcaption>
</figure>

프로비저닝을 주도하는 주체를 `provisioneer` 라고 부르며, 이는 `Zephyr` 메시 스택을 이용해 구현할 수도 있고, 스마트폰 어플리케이션을 이용해서 `provisioneer` 장치를 구현할 수도 있다.

참고로 `Zephyr` 블루투스 메시 스택에서는 `Advertising and GATT` Provisioning bearers 를 이용해서 `provisionee` 역할을 하는 장치를 구현할 수 있고, `provisioner` 역할을 하는 장치의 경우는 `Advertising` Provisioning bearer 를 이용하는 경우에만 구현이 가능하다.

`bearer` 관련해서는 이전에 작성한 포스트를 참고할 것

* **[BLE (12) - 블루투스 메시: Advertising Bearer 포맷](https://enidanny.github.io/ble/ble-mesh-message/)**

---

### 4.4 Proxy Features and Foundation models

`Proxy` 라는 특성은 (기존의 스마트폰 처럼) 저전력 블루투스 (`Bluetooth LE, BLE`)만을 지원하는 기기들도 메시 네트워크에 참여할 수 있도록 하기 위해 고안된 기능이다.

>`Proxy` 서버로 동작 가능한 기기들을 통하면, `BLE` 프로토콜만 지원되는 기기에서도 메시 네트워크에 접근이 가능하다는 것.

또한, `foundation models` 의 경우 실제로 어떤 식으로 구현이 되는 것인지는 좀 더 살펴봐야겠는데, 기본적으로 4개의 (**`Configuration Server/Client, Health Server/Client`**) foundation model 이 있는데, 네트워크 관리자 (**`아마도 보편적으로는 provisioner 역할을 하는 장치가 담당할 것으로 보임`**)가 메시 노드를 관리하고 점검하기 위해 사용되는 model (**`일단은 GATT services 정도로 생각하고 넘어가자`**) 이라고 한다.

---

### 4.5 참고하면 좋은 블루투스 메시 관련 `nRF Connect SDK` 페이지

* **`Bluetooth mesh concepts:`** [https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_concepts.html#id5](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_concepts.html#id5)
* **`Bluetooth mesh stack architecture:`** [https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_architecture.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.0.2/nrf/ug_bt_mesh_architecture.html)