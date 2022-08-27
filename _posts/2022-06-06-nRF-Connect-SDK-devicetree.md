---
title: "NCS (2) - Devicetree 구조 및 특징 / Devicetree Structure and Features"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - devicetree
  - 노르딕
  - 노르딕 펌웨어 개발
toc: true
toc_sticky: true
---

## 2. Devicetree 구조 및 특징 / Devicetree Structure and Features 🌳

<span style="color:#209090"><b>nRF Connect SDK</b></span> **(이하, `NCS`)** 에서 제어하고자 하는 하드웨어 장치에 접근하기 위해 위해 사용. 쉽게 말해 `GPIO` 같은 핀을 설정하는 방법의 일종인데, 일반적으로는 이러한 정보는 단순히 헤더 (`header`) 파일 (`.h`)상에서 정의하고 관리했으나, `NCS` 에서는 `Devicetree` 를 이용한다.

>A `devicetree` is a hierarchical data structure that describes hardware. The hardware described could be a development kit, SoC, SiP, module, defining everything ranging from the GPIO configurations of the LEDs on a development kit to the memory-mapped locations of peripherals.

* **[https://academy.nordicsemi.com/courses/nrf-connect-sdk-fundamentals/](https://academy.nordicsemi.com/courses/nrf-connect-sdk-fundamentals/)**
* **[https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/build/dts/index.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/build/dts/index.html)**

### 2.1 NCS Overview and Devicetree 🔭

`NCS` 에서는 어플리케이션에서 사용되는 각종 프로토콜이나 라이브러리 사용 유무를 정의하는 **`Kconfig (*.conf)`** 파일과 하드웨어 특성을 정의하는 **`devicetree (*.dts)`** 파일이 구분되어 있는데, 특히 `Devicetree source` **(이하 `*.dts`)** 파일과 소스 코드 (`*.c`) 부분이 별도로 나눠져 있기 때문에, 동일한 어플리케이션을 서로 다른 칩 (`SoC`)에 업로드 하기가 용이함.

<figure style="width: 90%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-dts-fig0.png" alt="">
</figure>

>예를 들어, `nRF52840-DK` 를 이용해 특정 어플리케이션을 개발했더라도, `*.dts` 파일만 새로 생성해주면 `nRF52832`, `nRF52833` 등 다른 `SoC` 를 사용하는 디바이스에 동일한 어플리케이션을 쉽게 업로드 할 수 있음.

---

### 2.2 Syntax and Structure

Devicetree 관련 구조와 문법은 **[DevAcademy - Lesson 2](https://academy.nordicsemi.com/topic/devicetree/)** 에서 간단하게 소개하고 있으며, 해당 파트에서 다루는 내용은 아래의 `developer.nordicsemi.com/nRF_Connect_SDK` 페이지에서 보다 상세하게 다루고 있음.

**[https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/build/dts/intro.html](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/latest/zephyr/build/dts/intro.html)**

{% highlight css linenos %}
/dts-v1/;

/ {
        a-node {
                subnode_nodelabel: a-sub-node {
                        foo = <3>;
                        label = "SUBNODE";
                };
        };
};
{% endhighlight %}

위 코드는 기본적인 Deviceetree 구조를 보여준다. 먼저, 첫 번째 라인 (`/dts-v1/;`)은 해당 `*.dts` 파일의 DTS Syntax 버전을 명시하고 있으며, `root` 노드 (`/`) 를 시작으로, `root` 노드의 `child` 노드에 해당하는 `a-node` 라는 이름의 노드와 `a-node` 의 `child` 노드에 해당하는 `a-sub-node` 노드가 어떤 식으로 구성되는지를 보여준다.

>Devicetree 의 노드 (`node`)는 각각의 위치를 나타낼 수 있는 경로(`path`) 값을 갖는데 (such as `Unix` file system), **`/`** 와 상위 노드의 이름을 이용해서 정의된다. 가령 앞서 예시로 든 문법 구조에서 `root` 노드의 주소는 **`/`** 가 되고, `a-sub-node` 노드의 주소는 **`/a-node/a-sub-node`** 가 되는 것. 

또한, **`a-sub-node`** 노드처럼 각 노드는 이름과 값의 쌍 (**`name/value pair`**)으로 이루어진  <span style="color:#A02020"><b>properties</b></span> 라는 요소들도 가질 수 있다.
{: .notice}

>Property values can be an array of `strings, bytes, numbers, or any mixture of types`. Also, in some cases, the values are an array of what are called `cells`. A **`cell`** is just a **`32-bit unsigned integer`**.

앞서 소개한 `developer.nordicsemi.com/nRF_Connect_SDK` 링크에 들어가보면 알겠지만, 이 밖에도 Devicetree 노드들의 여러 특성이나, `properties` 값의 데이터 형식 등 Devicetree 관련해서 다룰 주제들이 많지만, 결국에는 `NCS` 예제 (`samples`)를 하나 골라서 해당 예제의 `*.dts` 파일이 어떤 구조로 이루어져있고, `*.dts` 파일 또한 어떻게 구성되는지를 이해해야 한다.

---

### 2.3 Devicetree Specification

`NCS` 에서 예제 프로젝트 하나를 생성한 후 본인이 사용하는 보드를 하나 선정해서 `build` 를 해보면, 해당 보드의 이름으로 `*.dts` 파일이 생성될텐데, 내가 자주 사용하는 `nRF52840-DK` 보드를 이용하는 경우 `*.dts` 파일의 경우는 대략적으로 다음과 같이 구성되어 있음.

```
nrf52840dk_nrf52840.dts
└─ nrf52840_qiaa.dtsi
└─── nrf52840.dtsi
```

파일 구조 자체는 `nrf52840dk_nrf52840.dts` 에서 `nrf52840_qiaa.dtsi` 파일을 `include` 하고, 다시 `nrf52840_qiaa.dtsi` 에서 `nrf52840.dtsi` 파일을 `include` 하는 형태로 구성되어있음 🧱. 

>`nrf52840.dtsi` 에서는 해당 `SoC` 의 하드웨어 구성요소(노드)들을 정의하고 있고, `nrf52840_qiaa.dtsi` 에서는 `SoC` 의 패키징 정보를 토대로 메모리 사이즈를 지정해주고 있으며, `nrf52840dk_nrf52840.dts` 에서는 하위 `*.dtsi` 정보들을 토대로 외부 하드웨어 보드 (`nRF52840-DK`)와 `nRF52840 SoC` 를 연결해주고 있음.

아래 그림에서 볼 수 있듯이 `VS Code` 에서 `NCS` 실행 후, 예제 (`sample`) 프로젝트를 하나 빌드해보면, `Input files` 폴더 안에 해당 예제에서 사용되는 `*.dts` 파일이 있는데, 프로젝트에서 사용되는 `*.dts` 파일을 보면, 위에서 언급한 페이지에서 설명하지 않은 처음 보는 문법들도 있는 것으 볼 수 있음. 이 부분과 관련해서 보다 자세한 내용들은 아래의 **Devicetree specification** 문서를 확인할 것.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-dts-fig1.png" alt="">
</figure>

**[https://github.com/devicetree-org/devicetree-specification/releases/tag/v0.4-rc1](https://github.com/devicetree-org/devicetree-specification/releases/tag/v0.4-rc1)**