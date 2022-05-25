---
title: "BLE (16) - Brief Intoduction to nRF21540 : Bluetooth LE range extender"
categories:
  - BLE
tags:
  - Bluetooth LE
  - nordic
  - nRF21540
  - FEM
toc: true
toc_sticky: true
---

## 16.  BLE RF 증폭기 (FEM) 소개 / Brief Intoduction to nRF21540 : Bluetooth LE FEM (Front-End-Module) for range extension

### 16.1 Reference link 

- [https://www.nordicsemi.com/-/media/Software-and-other-downloads/Product-Briefs/nRF21540-PB-v2.0.pdf?la=en&hash=9E9CB6E7FF8487C83F62CF5BE1471656304CF29A](https://www.nordicsemi.com/-/media/Software-and-other-downloads/Product-Briefs/nRF21540-PB-v2.0.pdf?la=en&hash=9E9CB6E7FF8487C83F62CF5BE1471656304CF29A)
- [https://www.youtube.com/watch?v=95QnfEIdwy8](https://www.youtube.com/watch?v=95QnfEIdwy8)
- [https://www.youtube.com/watch?v=WC0WYfxLQuQ](https://www.youtube.com/watch?v=WC0WYfxLQuQ)

---

### 16.2 Brief Introduction

>The `nRF21540` is an RF front-end module (FEM) that improves range and connection robustness for Nordic Semiconductor’s short-range wireless portfolio. 

nRF52, nRF53 SoC 와 연동해서 RF 성능 증폭 가능

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ble-nrf21540-fig1.png" alt="">
</figure>

As you can see, Based on the `nordic`'s brief datasheet, "Link budget" between two RF devices could be improved 📡. In the conditions of using `nRF52840`, `1Mbps` datarate,

* **Tx: + 20 dBm**
* **Rx: - 100 dBm**

위 그림 보면 `nRF52840` SoC 기준, 최대 Tx Power **`+ 8 dBm`**, 수신 감도 (`RSSI`)는 **`- 95 dBm`** 까지 수신 가능. `nRF21540` FEM 이용할 경우, 송신 전력은 추가로 <span style="color:#A02020"><b>+ 12 dBm</b></span> 높여서 최대 **`+ 20 dBm`** 까지 설정할 수 있고, 수신 가능한 수신 감도의 세기를 **`- 100 dBm`** 까지 조정 가능.

---

### 16.3 Trade-off : Power consumption 🔌 

Current consumption of `nRF21540` SoC:

* Set Tx power up to + 20 dBm: `110 mA`
* Set Tx power up to + 10 dBm: `38 mA`
* Rx current: `2.9 mA`

Actually, those electrical properties are significantly <span style="color:#A02020"><b>critical (not helpful)</b></span> to the portable IoT devices which might be operated by using batteries 🔋. Also, it should be considered the current consumption of major `Bluetooth LE SoC`.

`nRF21540` FEM; 즉, RF 증폭기 자체의 소비 전류가 저정도인데, 함께 연동해서 사용하는 `nRF52`, `nRF53` SoC 에서의 RF 소비 전류까지 고려하면 배터리로 수개월 이상 동작해야하는 IoT 장치에는 적합하지 않을 것 같음. (설령, 해당 수치가 `nRF52840` SoC 에서의 RF 전류까지 고려했다고 해도 상당한 수준...)

>`nRF52840` SoC 에서 스캔 활성 시 (Rx) 소비 전류가 이미 5 mA 를 훨씬 상회하는 수준이라, 아마도 `nRF21540` specification 에 명시된 소비 전류 값은 `nRF21540` FEM 자체 소비전류 일 것 같음.

>단, 스마트폰처럼 매일 충전해서 사용할 수 있다면, 특정 순간에만 일시적으로 RF 전력을 높이는 것은 문제가 없을 것 같고, 선택적으로 Rx 단 감도만 개선한다던가, 혹은 외부로부터 전원을 공급받을 수 있는 장치 (e.g. 공유기, AP) 등에서 BLE 송수신 거리 증폭을 위해 사용한다면 이점을 가져갈 수 있을 것으로 보임.
