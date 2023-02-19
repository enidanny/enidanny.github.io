---
title: "NCS (7) - Zephyr OS 주요 특징 - RTOS 와 커널 (Kernel)"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - nordic semiconductor
  - 노르딕
  - 노르딕 펌웨어 개발
  - nRF Connect SDK 개발
  - Zephyr OS
  - RTOS
  - Kernel
  - small footprint
  - 실시간 운영체제
  - 커널
  - 운영체제와 커널
  - Operating System
  - nRF Connect SDK
toc: true
toc_sticky: true
---

## 7. Zephyr OS 주요 특징 - RTOS 와 커널 (Kernel)

<span style="color:#A02050"><b>Zephyr OS 란?</b></span> 자원이 한정된 임베디드 (**`embedded`**) 기기 같은 장치를 구동하기 위해 고안된 실시간 운영체제 (**`Real-Time OS`**, 이하 **`RTOS`**)의 일종이다.

이러한 유형의 **`RTOS`**는 단순 동작을 수행하는 임베디드 기기에서부터 비교적 정교한 웨어러블 기기나 IoT 기기를 구동하는데에도 활용될 수 있는데, 노르딕 사의 통합 개발 환경인 nRF Connect SDK 는 Zephyr OS 를 기반으로 동작한다.

>**Zephyr OS** 특징에 대해 살펴보기전, 먼저 **운영체제와 커널**에 대해 살펴보자.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-zephyr-fig1.png" alt="">
</figure>

### 7.1 운영체제 (OS)와 커널

`nRF Connect SDK` 와 `RTOS` 에 대해 공부하다보면 커널이라는 단어가 사용되고 있는 것을 종종 볼 수 있다 (특히, 커널이라는 용어는 컴퓨터 공학이나 머신러닝 등의 분야에서 서로 다른 형태로도 사용되는 것으로 알고 있다). **[노르딕 개발 페이지](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.1.2/zephyr/introduction/index.html)**에서는 **Zephyr OS** 에 대해 다음과 같이 언급하고 있다.

>The Zephyr OS is based on a "small-footprint" kernel designed for use on resource-constrained and embedded systems

여기서의 **"small-footprint" 커널 (Kernel)을 기반으로 한다**는 것은 무슨 뜻일까?

---

#### 7.1.1 운영체제의 시작

이와 관련해서 구글링 하던 도중 실시간 운영체제 (RTOS)와 커널 (Kernel)에 관해서 잘 정리된 오래된 글을 하나 발견할 수 있었다.

* **[RTOS와 Kernel by 히언](http://recipes.egloos.com/5072853)**

위 블로그 포스팅의 내용을 정확히 이해했는지는 모르겠으나, 필자의 입맛에 맞게 각색을 좀 해보자면 대략 다음과 같다.

1. **운영체제(`OS`)**가 없던 시절에는 사용자(`user`)가 특정 소프트웨어를 만든 뒤에도 이를 실행시키는 과정에 직접 관여를 해야했다.

2. 그런데, 이러한 과정을 반복하다보니 소프트웨어의 기능은 저마다 달라도 **동작하는 구조는 대부분 비슷**하다는 사실을 알게 되었다.

3. 나중에는 소프트웨어를 동작시킬 **"관리자"**를 하나 만들어두고, 해당 관리자에게 **"특정 소프트웨어를 실행해라"**라고 전달하는 식의 구조가 자리 잡게 되었다.

위에서 언급한 관리자가 현재 운영체제라고 부르는 소프트웨어의 시초가 되었다고 한다. **Zephyr OS**의 경우 운영체제 중에서도 멀티태스킹과 인터럽트 처리 기능 등 실시간 동작 특성에 보다 초점을 맞춘 임베디드 기기 전용 운영체제라고 할 수 있겠다.

#### 7.1.2 커널이란?

특정 목적 (임베디드 기기 제어)을 위해 고안된 운영체제인 RTOS 또한, 내부적으로는 다양한 기능을 수행하기 때문에 이 중 중요한 부분을 따로 구분지어서 부르기 시작했는데 이것들을 가리켜 커널 (Kernel)이라고 한다.

주로 <span style="color:#205050"><b>스케쥴링 (Scheduling), 메모리 관리, ISR (Interrupt Service Routine)</b></span> 등을 관리하는 부분에 해당하는 것으로 보인다

>다만, 위에서 참고한 블로그 페이지를 보면, 작성자가 글을 썼던 **2009년**도에도 커널이라는 용어를 정의하는 것에 있어서 논란이 많았던 것으로 보인다.

아무튼... 적당히 정리하자면, <span style="color:#A02040"><b>커널이란?</b></span> RTOS 핵심 서비스 (Threads, Scheduling, CPU Idling, Interrupts,. etc) 제공자 정도로 이해할 수 있을 것 같고, **이러한 커널로부터 핵심 서비스**를 제공받기 위해서는 **`커널 API`**를 이용해 관련된 명령어를 호출하면 되는 것으로 보인다.

---

### 7.2 Zephyr OS 특징

본 주제로 돌아와서 **Zephyr OS** 의 특징에 대해 살펴보자면 대략 다음과 같다.

#### 7.2.1 Small-footprint

앞서 언급한 노르딕 개발 페이지에서 설명하는 Zephyr OS 특성을 다시 살펴보자

>The Zephyr OS is based on a <span style="color:#A02040"><b>"small-footprint"</b></span> kernel designed for use on resource-constrained and embedded systems

여기서 **`footprint`**라는 단어는 메모리 (그중에서도 RAM) 영역에서 애플리케이션이 차지하는 사이즈를 가리키는 것으로 보인다. 물론, 경우에 따라서는 **실행중인 프로그램이 차지하는 영역 이외의 남겨진 여유 용량**을 가리키기도 하겠지만 핵심은 **"프로그램이 차지하는 영역 혹은 사용하는 자원"**이 얼마나 되는가를 나타내기 위해 사용되는 용어인 셈이다.

>The configurable nature of the kernel allows you to incorporate only those features needed by your application, making it ideal for systems with limited amounts of memory (as little as 2 KB!)

길게 늘려서 설명하긴 했지만, 쉽게 말해서 `Zephyr OS`가 자원을 적게 쓰는 운영체제라는 것을 강조하려는 것으로 보인다.

### 7.2.2 Multiple architectures

**Zephyr OS**는 다음과 같이 다양한 시스템 아키텍쳐를 지원하고 있다.

* `ARCv2 (EM and HS) and ARCv3 (HS6X)`
* `ARMv6-M, ARMv7-M, and ARMv8-M (Cortex-M)`
* `ARMv7-A and ARMv8-A (Cortex-A, 32- and 64-bit)`
* `ARMv7-R, ARMv8-R (Cortex-R, 32- and 64-bit)`
* `Intel x86 (32- and 64-bit)`
* `MIPS (MIPS32 Release 1 specification)`
* `NIOS II Gen 2`
* `RISC-V (32- and 64-bit)`
* `SPARC V8`
* `Tensilica Xtensa`

### 7.2.3 Distinguishing Features

노르딕 개발 문서에서 언급하고 있는 **`Zephyr OS`** 특징은 다음과 같다. 물론, 이중에는 기본적으로 **`RTOS`** 로서 갖는 특징들도 있는 것으로 보이는데, 아무래도 노르딕 칩 개발 관점에서는 저전력 블루투스 (BLE) 기능을 지원해주는 점을 핵심 요소로 꼽을 수 있을 듯 하다.

* 멀티 스레드(`**thread`**) 및 ISR, 메모리 할당, 스레드 동기화 및 데이터 교환
* 전원 관리 서비스 (Power Management)
* 다중 스케쥴링 알고리즘 관련 기능
* <span style="color:#2060A0"><b>저전력 블루투스 및 블루투스 5.0 (메시 프로토콜)</b></span> 지원

>그 외 세세한 기능들의 경우 아래의 링크에서 확인할 수 있다

---

**References**

https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.1.2/zephyr/introduction/index.html