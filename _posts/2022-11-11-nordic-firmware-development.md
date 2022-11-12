---
title: "BLE (17) - 노르딕 (nordic semiconductor) 펌웨어 개발 루틴"
categories:
  - BLE
tags:
  - Bluetooth
  - Bluetooth LE
  - nordic
  - nordic semiconductor
  - ST Microelectronics
  - Texas Instruments
  - TI
  - Dialog
  - 블루투스
  - 저전력 블루투스
  - 노르딕 펌웨어 개발
  - BLE 펌웨어 개발
  - ST 마이크로일렉트로닉스
  - 다이얼로그
  - 텍사스 인스트루먼트
toc: true
toc_sticky: true
---

## 17. 노르딕 (nordic semiconductor) 펌웨어 개발 루틴

필자의 경우 현재 일하는 직장에서 노르딕 (`nordic semiconductor`)사 제품 (e.g. `nRF52840 SoC`)을 이용해 저전력 블루투스 펌웨어를 개발하고 제품화하는 일을 하고있다. 노르딕 제품 이외에도 ST (`ST Microelectronics`)사 혹은 다이얼로그 (`Dialog`)사 제품 BLE 칩이 가장 많이 사용되는 것으로 알고 있다.

>이전에 자료 찾아보니 텍사스 인스트루먼트 (`TI`)사 홈페이지에도 BLE 관련 개발 문서가 잘 정리되어있던 것으로 봐서는 제품 개발도 꾸준히 하는 듯 하다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ble-dev-routine-fig3.png" alt="">
  <figcaption>출처: https://software-dl.ti.com/simplelink/esd/simplelink_cc13x0_sdk/2.20.00.38/exports/docs/ti154stack/html/ti154stack/frequency-hopping-mode.html</figcaption>
</figure>

다른 회사 BLE 칩은 개발 경험이 없어서 잘 모르겠으나... 일단, 노르딕 사 제품의 경우 기능 테스트를 위한 예제가 잘 구성되어 있고, 개발 문서도 꽤 상세하게 정리되어있다. 또, 찾아보면 BLE 표준 기술 관련해서 정리된 `white paper`도 꽤 있는 편이고, 관련 개발자들에게 질문을 남길 수 있는 **[전용 사이트](https://devzone.nordicsemi.com)**도 있어서 여러모로 BLE 응용 프로그램을 개발에 용이하다는 생각이다.

>노르딕 사 제품만 해당되는 것은 아니겠지만, BLE 펌웨어 개발의 경우는 대략 다음의 순서로 진행된다.

### 17.1 기본 기능 구현 (Basic application)

정해진 하드웨어 (커스텀 보드 or 개발 보드)를 가지고 기본적으로 동작시키고자 하는 기능을 구현한다. 이 과정에서는 내부에서 계산되는 수치나 동작 오류등을 확인하기위해 디버깅 모듈 (e.g. UART 시리얼 통신 모듈)을 활성화한 상태로 진행한다.

앞서 이야기했듯이 노르딕 개발 플랫폼에는 (`nRF5 SDK or nRF Connect SDK`) 시리얼 통신을 비롯해 각각의 하드웨어 제어를 위한 다양한 예제가 정리되어있다.

>BLE 통신 관련 예제부터, TWI (I2C), UART 등의 시리얼 통신, GPIO 테스트 예제 등

---

### 17.2 저전력 구동 테스트 (Low-power operation)

기본 기능 구현이 되었다면, 디버깅을 위해 활성화 상태로 두었던 기능 (e.g. printf(...))들을 모두 OFF 시켜서 전력 소모를 최소화 한다. 어떤 회사 제품을 사용하더라도, 데이터 시트를 확인해보면 BLE 칩을 구동하고 통신 기능을 활성화 했을 때의 전력 소모가 어느정도 되는지 나와있을 것이다.

노르딕 제품의 경우 아래의 사이트를 통해 통신 단계에서 소비되는 전류를 확인할 수 있으며, nRF PPK (power profile kit) 를 이용하면 uA 단위의 전류 그래프를 얻을 수 있다.

* **[https://devzone.nordicsemi.com/power/w/opp](https://devzone.nordicsemi.com/power/w/opp)**

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ble-dev-routine-fig1.png" alt="">
  <figcaption>출처: https://devzone.nordicsemi.com/power/w/opp</figcaption>
</figure>

---

### 17.3 무선 업데이트 (Device Firmware Update)

저전력 구동 테스트 까지 마무리했다면, 끝으로 무선 업데이트 기능을 추가할 수 있으면 좋다. 노르딕의 경우 `Device Firmware Update` 라는 `DFU` 기능을 추가할 수 있는데, 이를 이용해 무선 연결을 통해 펌웨어를 업데이트 할 수 있다.

따라서 하드웨어 분해 후 PCB 기판에 외부 디버거를 연결해서 매번 프로그램을 업로드할 수고를 덜 수 있기 때문에 제품화까지 진행을 한다면 무선 업데이트 기능을 추가하는 것은 필수라고 할 수 있다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ble-dev-routine-fig2.png" alt="">
</figure>

>구글 플레이스토어나 애플 앱스토어에 들어가보면 알겠지만, 노르딕 사의 경우 관련 스마트폰 어플도 다양하게 제공이 되어서 여러모로 개발에 용이하다는 생각이다.