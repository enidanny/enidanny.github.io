---
title: "IoT (12) - SPI 프로토콜"
categories:
  - nRF5 SDK
tags:
  - nRF5SDK
  - SAADC  
  - AC voltage
  - IoT
  - 교류 전압
toc: true
toc_sticky: true
---

## 12. SPI 프로토콜

이번 포스팅에서 다룰 주제는 임베디드 레벨에서 기본적으로 활용되는 시리얼 통신 중 하나인 SPI 프로토콜이다. `SPI` 통신은 `I2C` 와 `UART` 와 함께 센서 등의 모듈 제어 시 가장 많이 활용되는 통신으로, `I2C`와 마찬가지로 `1:N` 통신이 가능한 프로토콜이다.

`SPI` 통신에 관해서는 전문적으로 정리된 글들이 많으니, 조금만 찾아봐도 소스코드를 비롯해서 필요한 정보를 얻을 수 있을 것이다. 본 포스팅에서는 다음에 명시한 참고 링크 (`Analog Device 사`)의 내용을 토대로, **`펌웨어 개발 관점`**에서 필요한 핵심적인 내용 위주로 이야기를 해보려고 한다.

* **[https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html](https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html)**

### 12.1 프로토콜 개요

다음은 `SPI` 프로토콜 기반 시스템에서의 기본 하드웨어 구성이다. 2개의 핀 (`SCL, SDA`)을 필요로 하는 `I2C` 와 달리, `SPI` 에서는 기본적으로 4개의 핀을 필요로 한다. (물론 `3-Wire` 나, `QSPI` 등의 경우 개수가 다르긴 하지만, 본 포스팅에서는 **`4-Wire SPI`** 에 대해 주로 다루고자 한다.)

<figure style="width: 60%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig1.png" alt="">
</figure>

각각의 핀은 다음과 같은 역할을 수행한다

* `CS`: Slave 선택 (`Chip Select`)
* `SCLK`: 클럭 신호 공급
* `MOSI`: Master -> Slave 데이터 라인
* `MISO`: Slave -> Master 데이터 라인

센서 및 제조사마다 이름이 조금씩 상이할 수는 있으나, 대부분 이러한 형태로 핀 이름을 정의하고 있다. `CS` 핀은 Master 에서 상호작용할 Slave 모듈을 선택하는 (주로 `Active Low`) 용도로 사용되며, `SPI` 통신의 경우 데이터 쓰기 (`write`)와 읽기 (`read`)가 동시에 수행되는 Full-Duplex 형태의 프로토콜이므로, 총 4 개의 핀을 필요로 한다.

>Full-Duplex 구조이긴 하나, Master (e.g. 임베디드 프로세서)와 Slave (e.g. 센서) 사이의 인터페이스를 살펴보면 상호작용하는 규칙은 `I2C`와 크게 다르지 않다 (접근할 레지스터 주소를 입력하고 쓰기 혹은 읽기 등의 업무를 수행). 다만, 내부적으로 보면 명령어를 주고 받을 때마다 `MOSI`, `MISO` 핀에서 동시에 (비록 Dummy 값이더라도) 데이터를 주고 받고 있는 것을 볼 수 있다.

---

### 12.2 1:N 통신 지원

앞서 언급했듯이 `SPI` 통신은 `I2C` 처럼 `1:N` 통신이 가능한 프로토콜에 해당된다. `I2C`는 Slave 기기의 고유 주소 값으로 (`7-bit or 10-bit`) 기기를 구분했다면, `SPI` 에서는 `CS` 핀을 이용해 원하는 Slave 기기를 직접 선택하는 방식이다.

<figure style="width: 85%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig2.png" alt="">
  <figcaption>출처: https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html</figcaption>
</figure>

>따라서, Slave 개수가 하나 추가될 때마다, Master 기기에서는 이를 위한 `CS` 핀을 추가로 할당해야한다는 단점아닌 단점이 있다. 

`CS` 핀이 늘어나는 문제를 보완하기 위해 다음의 그림과 같은 `Daisy-Chain` 구조를 이용하기도 한다. 

<figure style="width: 70%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig3.png" alt="">
  <figcaption>출처: https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html</figcaption>
</figure>

#### 12.2.1 장단점

필자는 주로 `I2C` 기반 센서를 제어하다보니, 처음 `SPI` 통신을 접했을 때 다음과 같은 의문이 들었었다.

>**어떤 이점 때문에 `SPI` 프로토콜을 사용할까?**

핀 개수도 적게 소모하는 `I2C` 프로토콜과 비교했을 때는 동일하게 `1:N` 통신을 지원한다는 점에서 큰 이점이 없는 것으로 보이지만, 개인적으로 생각했을 때는 크게 다음의 두 가지 이점이 있을 것으로 예상된다.

* **`통신 속도:`** 첫째는 당연한 이야기지만, **통신 속도**에서의 이점이다. 근래에는 속도가 꽤 향상되기는 했으나, 기본적으로 `100 kbps` 수준의 통신 속도를 지원하는 `I2C` 와 비교했을 때, `SPI`는 수 `Mbps` 의 통신 속도를 제공한다. 즉, 고속 데이터 교환이 필요한 구조에서는 `SPI` 통신이 더 우위를 점한다.

* **`하드웨어 구조:`** 이 부분은 전적으로 필자의 뇌피셜이므로 아닐 수 있는데, `I2C` 와 달리 `SPI` 통신을 지원하는 Slave 기기는 기기의 고유 주소값을 필요로 하지 않으므로 하드웨어 구조 혹은 메모리 측면에서 이점이 있지 않을까 예상된다. (다만, 최근에는 `I2C`와 `SPI` 프로토콜을 모두 지원하는 기기들도 많아서... 아무래도 아닐 가능성이 높을 듯 하다.🤨)

---

### 12.3 통신 모드 (0, 1, 2, 3)

`SPI` 프로토콜의 또 하나의 특징은 데이터를 읽는 타이밍 (`Polarity`)과 위상 (`Phase`)에 따라 총 4가지의 통신 모드가 존재한다는 것이다.

<figure style="width: 70%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig4.png" alt="">
  <figcaption>출처: https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html</figcaption>
</figure>

>뭔가 엄청 복잡하고 피곤해보이지만, 가만 보면 `CPOL = 0 or 1` 값과 `CPHA = 0 or 1` 값에 따라서 어느 시점에 어떻게 데이터를 주고 받을지를 결정하는 규칙일 뿐이다.

데이터 통신을 하지 않는 대기 상태 (`IDLE`)에서 클럭 핀 (`SCLK`)의 레벨을 `High`로 둘 것인가 `Low`로 둘 것인가를 정하는 것이 `CPOL` 값이고, 데이터를 읽는 타이밍이 `rising edge` 인지 `falling edge` 인지를 선택하는 것이 `CPHA` 값이다.

>참고로 읽는 타이밍이 `rising edge`라면 쓰는 타이밍은 그 반대인 `falling edge` 이다.

`SPI` 통신 모드의 경우는 어느 정도 합의된 것으로 보이지만, 개발 환경에 따라 조금씩 상이할 수 있으므로 자신이 개발하는 플랫폼의 설정을 잘 확인해야할 것이다. (간혹, `A` 플랫폼에서 `SPI MODE0` 라는 설정 값이, `B` 플랫폼에서는 `SPI MODE1`에 대응될 수도 있다.)

#### 12.3.1 ADXL343 가속도 센서 통신 모드 확인하기

통신 모드 관련해서는 앞서 참고한 링크 (https://www.analog.com/en/analog-dialogue/articles/introduction-to-spi-interface.html)에도 상세하게 나와있지만, 이를 이해하는 가장 좋은 방법은 본인이 직접 사용할 센서를 이용해서 테스트해보는 것이다.

마침 필자가 가지고 있는 `ADXL343` 이라는 가속도 센서의 데이터 시트를 보면서 실제로 `SPI` 통신 모드를 어떻게 확인하면 되는지 알아보자.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig5.png" alt="">
</figure>

제어하고자 하는 센서의 데이터 시트에서 `SPI` 통신과 관련된 부분을 확인해보면, 대부분 아래 그림처럼 해당 센서의 `SPI` 통신 타이밍이 어떻게 설정되었는지 명시되어 있다. 현재 필자가 참고한 데이터 시트의 경우는 `CPOL, CPHA` 값 모두 `1` 인 조건 (`SPI MODE 3`)에서 상호작용한다고 명시되어있다.

<figure style="width: 65%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig8.png" alt="">
</figure>

대부분의 센서 데이터 시트에서는 아래 그림과 같은 다이어그램을 함께 제공해준다. 이를 보면서 데이터 시트에서 명시한 `CPOL`, `CPHA` 값이 이와 일치하는지 확인해보도록 하자.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig6.png" alt="">
</figure>

#### 12.3.2 CPOL 확인하기

먼저, `CPOL` 값은 위의 그림에서 통신을 하지 않는 시점에 (`CS = High`) `SCLK` 값이 어떻게 되어있는지 보면 된다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig7.png" alt="">
</figure>

>그림에서 볼 수 있듯이 `IDLE` 상태에서의 `SCLK` 값이 `High` 이다. 따라서 `CPOL` 값은 `1` 로 설정된 것이 맞다는 것을 확인할 수 있다.

#### 12.3.3 CPHA 확인하기

`CPHA` 값은 다이어그램에서 확인해보기가 어려울 수도 있긴한데, 이 부분도 확인은 가능하다. 맨 처음에 살펴본 것과 달리 핀 이름이 `SDI, SDO` 라고 되어있어서 헷갈릴 수 있으나, 명령을 보내고 데이터를 받는 주체 (Master)가 누구인지를 생각해보면 된다.

아래 그림에서 `write` 시점 (`Figure 27`)을 보면, `SDI` 라인을 통해 원하는 레지스터의 주소와 데이터를 전송하고 있다는 것을 볼 수 있다. 즉, 해당 핀이 앞서 살펴본 `MOSI` 핀에 해당되며, 이때 데이터를 쓰는 타이밍 (`Shift-out`)을 보면 `rising edge` 에 수행되고 있는 것을 볼 수 있다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-spi-fig9.png" alt="">
</figure>

자연스럽게 `read` 신호는 `SDO` 라인을 통해 들어오고 있으며 (`Figure 28`), 이러한 Data sampling 동작은 `write` 와 반대되는 `falling edge`에 수행되고 있는 것을 볼 수 있다. 즉, `CPHA` 값도 데이터 시트에서 명시한 `1` 값이 맞다는 것을 알 수 있다.

---

### 12.4 nRF5 SDK 에서의 SPI 제어

이전 포스팅에서 살펴본 것처럼, `SPI` 예제 또한 `nRF5 SDK` 에서 제공해주기 때문에 이와 관련해서는 별도로 예제를 다루지는 않으려고 한다 (**절대 귀찮아서~~가 아니~~다**).

>구글링을 조금만 해봐도 어떤 식으로 명령어를 주고 받아야하는지 정리한 글들이 많으니, `SPI` 관련 소스 코드는 다른 블로그의 글들을 참고하자.

다만, 한가지 유의할 점으로는... `I2C` 와 비슷하게 `SPI` 명령어에도 `write / read` 데이터를 구분하는 `1-bit`가 존재한다. `I2C`의 경우는 해당 비트가 Slave 고유 주소 (`7-bit`) 뒤에 0 또는 1로 위치하는데, `nRF5 SDK` 에서는 tx (write), rx (read) 명령에 따라 해당 비트를 자동으로 채워준다.

<span style="color:#A01020"><b>"이 부분은 `SPI` 관련 함수에는 생략되어 있으므로, 직접 채워줘야 하니 참고하길 바란다."</b></span>

>앞서 살펴본 **12.3** 의 다이어그램을 보면, Address bit (`SPI` 로 제어할 Slave 기기의 레지스터 주소) 앞쪽에 write (1)와 read (0) 명령을 구분하기 위한 비트와 여러 레지스터에 접근할 것인지를 묻는 비트 (`MB`)가 위치하는데, `nRF5 SDK`에서는 이 부분을 직접 채워줘야 한다.

<!---
<span style="color:#A0a020"><b></b></span>
--->