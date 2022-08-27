---
title: "IoT(11) - 펌웨어 프로그래밍 이야기: bare-metal vs RTOS application"
categories:
  - IoT
tags:
  - firmware
  - bare-metal
  - RTOS
  - nRF5 SDK
  - nRF Connect SDK
  - 펌웨어
  - 실시간 운영체제
  - 노르딕 세미컨덕터
  - 저전력 블루투스
toc: true
---

## 11. 펌웨어 프로그래밍 이야기: bare-metal vs RTOS application 🧿

이번 포스트에서는 펌웨어 프로그래밍에 대해 조금 다뤄보려고 한다.

펌웨어라는 단어를 인터넷에 검색해보면 이런저런 설명이 나올텐데.. 결국 요약하자면 마이크로 프로세서와 같이 **작은 반도체 칩에서 동작하는 소프트웨어(`software`)** 라고 할 수 있을 것 같다. 이러한 소프트웨어는 하드웨어 (마이크로 프로세서) 각각의 핀을 직접적으로 제어할 수 있는데, 보통의 소형 전자기기를 구동하는 반도체 칩에는 이러한 소프트웨어들이 업로드 되어 있다.

### 11.1 featrues

흔히들 펌웨어를 하드웨어와 소프트웨어 중간 역할을 하는 요소라고 이야기한다.

>흔히 우리가 소프트웨어라고 생각하는 PC 에서 접하는 각종 어플리케이션은 운영체제 (OS)를 기반으로 하고 콘솔 장치 (키보드, 마우스 등)를 통해 특정 기능들을 수행할 수 있도록 해주는 것들이다.

그에 반해, 펌웨어의 경우는 (개인적인 생각이지만) 전적으로 `PCB` 하드웨어 상의 동작을 제어하는 단계에 초점을 맞추고 있다. 결과적으로는 이를 통해서 하나의 제품을 구동시키기 위한 프로그램을 완성시키는 것이 목적이긴 하지만, 이를 위해서는 하드웨어 (프로세서)의 핀 구성이 어떻게 되어있고, 회로 구성이나 디지털 모듈들을 어떻게 제어해야하는지 알아야 한다.

* **즉, 펌웨어는 하드웨어 레벨의 디지털 신호를 제어하기 위한 소프트웨어인 셈**

펌웨어 어플리케이션은 크게 `bare-metal` 어플리케이션과 `RTOS` 기반 어플리케이션으로 구분할 수 있다. 필자가 그 동안 개발했던 노르딕 (`nordic`) 어플리케이션은 `bare-metal` 어플리케이션에 해당되는데, 이러한 용어(?)가 있다는 것도 비교적 최근에 `nRF Connect SDK` 관련 자료를 공부하면서 알게되었다.

**참고링크: [https://academy.nordicsemi.com/topic/bare-metal-vs-rtos-programming/](https://academy.nordicsemi.com/topic/bare-metal-vs-rtos-programming/)**

---

### 11.2 bare-metal application

`bare-metal` (베어메탈) 어플리케이션은 별도의 운영체제 (OS) 없이 동작하는 펌웨어를 가리킨다. 프로세서에 전원이 공급되면 기본적으로 이를 구동시키기 위한 기본 세팅 준비를 시작하게 되는데 (bootloader, protocol stack 초기화 등), 해당 작업이 끝나고 나면 단일 무한 루프 (`e.g. for(;;){...}`)를 계속해서 반복 수행하는 형태로 프로그램이 동작하게 된다.

<figure style="width: 78%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-firmware-fig1.png" alt="">
  <figcaption>출처: https://academy.nordicsemi.com/topic/bare-metal-vs-rtos-programming/</figcaption>
</figure>

위 그림과 같이 하나의 무한 루프를 순차적으로 (`sequential logic`) 반복 수행하기 떄문에, 이러한 로직에서 벗어날 수 있는 예외처리 방식은 인터럽트 (ISR: interrupt service routine) 가 거의 유일하다고 보면 된다.

>필자가 주로 개발하는 노르딕 사 저전력 블루투스 (BLE) 칩들도 `nRF5 SDK` 를 활용해 개발하는 경우 bare-metal 구조로 어플리케이션을 개발하게 되어있다. (`e.g. nRF52832, nRF52840` 등). 또한, 아두이노 같은 기기도 대표적인 `bare-metal` 어플리케이션 기반 IoT 기기라고 할 수 있다.

`bare-metal` 어플리케이션의 경우 **전력소모가 적고, 메모리를 적게 사용하며, 속도도 빠른 편** 이라고 알려져있다 (아마도 뇌(`OS`)를 거치지 않고 곧바로 행동해서 그럴 수도?). 하지만, 단일 루프 안에서 모든 로직을 구현해야하기 때문에, 구현하고자 하는 어플리케이션에서 특정 기능이 추가될 수록 펌웨어 프로그램의 소스 코드 복잡도가 기하급수적으로 증가한다🧾.

`main` 루프가 하나라고 해서, 아무 생각없이 전역 변수를 선언해가며 코딩하다가는 나중에는 각각의 기능을 모듈화할 수 없을 정도로 코드가 뒤엉켜있을지도.. (물론 이 부분은 단순 `bare-metal` 프로그래밍의 문제는 아니긴 함)

---

### 11.3 RTOS based application

실시간 운영체제 (`Real-Time OS`)는 실시간 동작 특성을 보장하는 운영체제인데, 이를 이용할 경우 위에서 언급한 `bare-metal` 어플리케이션의 한계점을 극복하기가 한껏 수월해진다. `RTOS` 기반 어플리케이션에서는 쓰레드 (`thread`)라고 불리는 서로 다른 실행 블록들이 `RTOS` 에 의해 제어된다🧱.

<figure style="width: 80%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/iot-firmware-fig2.png" alt="">
  <figcaption>출처: https://academy.nordicsemi.com/topic/bare-metal-vs-rtos-programming/</figcaption>
</figure>

>가령, `A`, `B`, `C` 의 서로 다른 동작에 대해 정의된 쓰레드가 있다면, `RTOS` 에서는 사전에 정의된 로직에 따라 각각의 동작 (쓰레드)에 자원을 얼마나 투자하고, 어떤 순서로 어떤 규칙으로 동작시킬지를 결정하는 일을 수행하는 것.

노르딕 사의 저전력 블루투스 칩 중 `nRF52`, `nRF53` 제품군을 비롯해, 비교적 최근에 개발된 `nRF9160` 무선 칩의 경우는 `nRF Connect SDK (NCS)` 를 이용한 어플리케이션 개발이 가능한데, `NCS` 는 `Zephyr` 이름의 오픈소스 `RTOS` 를 기반으로 동작한다.

따라서, 보다 복잡한 어플리케이션을 효율적으로 구성하는게 가능하다 (~~맞나?~~). 또한, `RTOS` 를 이용하는 경우 특히 오픈소스의 경우 펌웨어 개발 단계에서 자주 사용하는 코드 블록 (함수, 라이브러리, 드라이버, 프로토콜 스택 등)들이 사전에 이미 정의되어 있는 경우가 많다. 이러한 자원들을 곧바로 가져다가 적용할 수 있다는 것도 하나의 장점일 듯!

* **`Zephyr RTOS` in nRF Connect SDK:** `RTOS` 기반 프로그래밍 경험이 없다보니 다른 프로세서의 경우도 개발이 비슷한지는 모르겠는데, `NCS` 에서는 `Semaphore, Mutex, ISR` 등을 활용하여 각각의 쓰레드에서 사용할 자원이나 우선순위 레벨까지도 소스 코드 레벨에서 설정이 가능하다.

장점이기도 하지만, 한편으로는 그만큼 최종 어플리케이션이 얼마나 효과적이고 효율적으로 동작할지가 개발자 역량에 많이 좌지우지 된다🙃. `NCS` 기초 관련 `DevAcademy` 자료 중에서는 `Lesson 7 & 8` 에서 `RTOS` 관련 주제를 다루고 있으니, 관심이 있다면 참고해보는 것도 좋을 듯 하다.

**참고링크: [https://academy.nordicsemi.com/topic/scheduler/](https://academy.nordicsemi.com/topic/scheduler/)**