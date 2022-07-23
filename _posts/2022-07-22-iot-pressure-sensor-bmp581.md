---
title: "IoT(10) - BMP581 디지털 기압계 (barometer) 개발"
categories:
  - IoT
tags:
  - barometer
  - BMP581
  - I2C
  - Bosch Sensortec
toc: true
---

## 10. BMP581 디지털 기압계 (barometer) 개발 QuickStart Guide

2022년 초에 나온 따끈따끈한(?) 신상 제품 `BMP581`. 그래서인지 데이터 시트가 뭔가 어설픈 느낌이 있다. 그래도 테스트 해보니 이전에 사용해봤던 `BMP390` 보다 전류 특성도 좋은 것 같고, 데이터 변환도 훨씬 간단하다. (**확실히 여러모로 개선된 느낌이 있음**)

데이터 시트 및 개발 자료는 아래 링크를 참고할 것.

> **[https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp581](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp581)**

### 10.1 주요 특징

데이터 시트 참조

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-bmp581-fig1.png" alt="">
</figure>

기압 측정 범위는 절대 기압으로 **`30 ~ 125 kPa`** 까지임. 

>참고로 1기압은 **101.325 kPa** 정도라고 알려져 있음.

센서 소비 전류의 경우는 데이터 시트를 보면 아래와 같이 기술되어 있기는 한데, `ODR` (output data rate), `OSR` (over-sampling rate) 그리고 동작 모드에 따라서 달라짐. 실제 센서를 적용할 하드웨어의 `VDD` 전압이나 데이터 측정 주기에 따라서도 달라지기 때문에 데이터 시트 만으로 판단하기는 어려운 부분이 있음. 

* Current consumption low power mode ODR = 1Hz (Room Temp.): 1.3 uA
* Current consumption high power mode ODR = 30Hz (Room Temp.): 75 uA
* Conversion time: 최대 수 ms 정도. (OSR 따라 다름)

`BMP581` 제어 관련해서 아래 깃허브 링크에 들어가보면 제조사 (`Bosch Sensortec`)에서 관련 라이브러리를 제공해주는 듯 하다.

* **[https://github.com/BoschSensortec/BMP5-Sensor-API](https://github.com/BoschSensortec/BMP5-Sensor-API)**

다만, 굳이 필요한지는 잘 모르겠다. 제어해야할 레지스터가 많거나 프로토콜이 복잡한 것도 아니라서... 본인이 사용하는 하드웨어의 I2C API 활용해서 적당히 제어하면 될 듯. 결국 핵심은 데이터 레지스터 읽고, `Pa` 단위로 변환하는게 끝.

---

### 10.2 BMP581 동작 모드

`BMP581` 동작 모드는 다음과 같음.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-bmp581-fig2.png" alt="">
</figure> 

데이터 시트를 읽어보면 알겠지만, `Sleep` 모드라는 단어와 `Standby` 모드라는 단어가 혼용되서 쓰이고 있음. 아마도 이전 버전 메뉴얼 그대로 가져다 써서 그런 것 같다. 그냥, **Sleep mode = Standby mode, Deep Sleep mode = Deep Standby mode** 이렇게 이해하면 될 듯.

센서는 기본적으로 `(Deep) Standby` 모드로 동작하고, 이후 입력되는 레지스터에 따라 모드가 전환됨. 참고로 위의 다이어그램 그림에서 볼 수 있듯이, 측정 모드 변경은 `Standby` 모드로 한 번 전환한 이후에 변경이 가능하다.

>예를 들어, 현재 센서가 normal 모드로 동작 중이라고 가정해보자. 이 상태에서는 곧바로 forced 모드나 continuous 모드로 전환될 수는 없고, Standby 모드로 한 번 전환한 뒤 변경해야 함.

#### 10.2.1 Standby mode

이전 측정 데이터가 데이터 레지스터 혹은 FIFO 에 보관은 되어 있으나 새로운 측정은 진행하지 않는 상태. 명령어를 읽고 쓰는 것이 가능하며, **특정 측정 조건 (`ODR, IIR` 설정 값)** 만족되면, 알아서 **`Deep Standby mode`** 로 동작하는 것으로 보임.

#### 10.2.2 Forced mode

단일 측정 모드. 센서 데이터 1회 측정 후 다시 `Standby` 모드로 복귀. 매우 천천히 측정하거나, `240 Hz` 이상의 빈도수로 데이터를 측정해야할 때 사용된다고 함 (센서를 제어하는 프로세스에서 강제적으로 여러번 명령어를 전송하는 셈).

#### 10.2.3 Normal mode

일종의 자동 측정 모드. `ODR` (output data rate) 값에 따라, 데이터 측정과 대기 상태 (`Standby mode`)가 주기적으로 전환된다. 가장 최근에 측정한 데이터를 Data Buffer (데이터 레지스터 혹은 FIFO)에 저장해둔다.

`ODR, OSR` 설정 값 등이 적절한지 여부는 데이터 시트를 보고 확인해야함.

> 가령, OSR: oversampling rate 은 높은데, ODR 설정 값도 상당히 빠른 경우 센서 데이터를 측정할 수 없는 것은 당연함. BMP581 에서는 config auto checking 기능 또한 있음 (문제 시, 관련 레지스터의 특정 Bit 값이 변경되는 것으로 기억).

* **Low-power Normal mode** 

앞서, 특정 조건이 만족되면 `Standby` 모드 대신 자동적으로 `Deep Standby` 모드로 동작하는 것을 언급. 해당 조건에서의 `Normal` 모드가  `Low-power Normal` 모드인 것으로 보임. 그 조건이란게 대략 다음과 같다.

```js
ODR_CONFIG.deep_dis = 0
ODR_CONFIG.odr < 5 Hz
FIFO_SEL.fifo_frame_sel = DIS
DSP_IIR.set_iir_t = BYPASS
DSP_IIR.set_iir_p = BYPASS
```

대충 요약하자면 5 Hz 이하의 빈도 수로 측정해야하고 (1초에 5회 이상 측정 불가), 센서에서 제공되는 `IIR` 필터 사용할 수 없음.

>`IIR` 필터는 어차피 센서 반응속도와 연관되기 때문에, 하드웨어에서 소프트웨어 필터링하는데 지장없으면 굳이 없어도 되겠다는 생각.

---

### 10.3 Simple Test

`BMP581` 센서에서는 **SPI-4wire / SPI-3wire / I2C / I3C** 총 네 종류의 인터페이스를 지원.
`I2C` 사용하는 경우에는 다음과 같이 회로 구성하면 됨.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-bmp581-fig3.png" alt="">
</figure>

>**i2c slave address** 는 SDO 값에 따라 달라진다 (`0x46` 또는 `0x47`). SDO 핀을 floating 시키지 않도록 주의할 것 (`I2C` 사용하는 경우)

Deep Standby & Low power-Normal mode 로 동작할 수 있도록 다음의 레지스터 값을 설정. 측정은 이렇게 해서 Normal 모드로 측정하고, 일정 시간마다 메인 프로세서 (`nRF52840`)에서 기압 데이터를 읽어오도록 했음.

**레지스터 설정:** ~~까먹음...ㅋㅋ~~
{: .notice--info}

>**소비전류 특성:** 테스트 보드에서의 기압 센서 동작 전후를 비교해보면, 센서 자체의 동작 전류는 <span style="color:#A02020"><b>2 uA</b></span> 정도인 것 같고, 사용 중인 프로세스 에서는 데이터 읽어오는 I2C reading 루틴에서 <span style="color:#A02020"><b>4.4 uA</b></span> 정도 소모하는 것을 확인. 결과적으로는 센서 데이터 읽는데 <span style="color:#A02020"><b>6.4 uA</b></span> 정도가 추가되는 것이긴 하지만, 이 정도면 충분히 저전력으로 제어된다고 생각됨.

다음은 회사 건물에서 계단을 오르내리면서 측정 데이터를 그래프로 출력한 결과임. **중간에 기압 빠르게 감소하는 부분**은 엘리베이터를 타고 로비에서 꼭대기 층으로 이동했을 때 측정된 데이터. (높이 올라갈 수록 기압이 낮다)

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-bmp581-fig4.png" alt="">
</figure>

---

### 10.4 참고사항

데이터 시트를 보면 알겠지만, `BMP581` 센서는 온도 측정이 가능한 기압계임. 센서 동작의 경우 온도 센서만 동작시킬지, 온도와 기압 센서를 함께 동작시킬지만 정할 수 있음. **기압 센서만 동작하는 경우에 대한 소비 전류도 기술되어 있으나, 실제로는 기압 센서만 구동시킬 수는 없다.**

>개인적으로는 `BMP581` 센서 내부에서 기압 데이터를 측정할 때 **자체 온도 정보를 가지고 기압 측정 값을 환산하는데 사용해야해서**, 온도 센서를 별도로 끌 수 없는 듯.

실제로 이전 제품군 중에서 `BMP390` 센서의 경우는 **기압 환산 방식이 보다 복잡했다.** 현재 온도 정보와 센서에 저장되어 있는 수십 개의 레지스터 정보를 읽어서 기압 환산 식을 적용해야했는데, `BMP581` 에서는 기압 데이터가 저장되는 레지스터만 읽어서 단위만 변환하면 됨.

>아마 이런 이유로 인해, 제어를 편하게 하려고 온도 센서를 끌 수 없도록 설계가 된 게 아닐까 생각.