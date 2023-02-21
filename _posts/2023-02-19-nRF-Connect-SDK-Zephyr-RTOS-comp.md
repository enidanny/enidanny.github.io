---
title: "NCS (8) - Zephyr RTOS 기본 구성요소"
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
  - 실시간 운영체제
  - nRF Connect SDK
  - 쓰레드
  - 스케쥴러
  - 인터럽트
  - Thread
  - Scheduler
  - ISR
toc: true
toc_sticky: true
---

## 8. Zephyr RTOS 기본 구성요소: Threads, Scheduler, ISRs

### 8.1 실시간 운영체제 기반 SDK

이전 세대 노르딕 사 개발 환경이었던 **`nRF5 SDK`** 와 비교했을 때, **`nRF Connect SDK`** 가 갖는 차별점 중 하나는 <span style="color:#20a080"><b>Zephyr RTOS</b></span> 를 기반으로 동작한다는 점일 것이다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-rtos-fig1.png" alt="">
</figure>

> **Zephyr RTOS** 는 다양한 커널 (`Kernel`) 서비스와 쓰레드 (`threads`)를 기반으로 해서 멀티 쓰레드 (`multi-threaded`) 애플리케이션을 구동하는 역할을 수행한다.

**[이전 포스트](https://enidanny.github.io/nrf%20connect%20sdk/nRF-Connect-SDK-Zephyr-OS/)** 에서 설명 했듯이, 임베디드 시스템에서 이야기하는 커널은 **"CPU 스케쥴링이나, 메모리, ISR 관리 등"** 을 담당하는 부분를 가리킨다 (혹은 그러한 서비스 자체를 커널이라고 봐도 될 것 같다).

이번 포스팅에서는 아래 링크의 정리된 내용을 토대로 `nRF Connect SDK` 에서 활용되는 `Zephyr RTOS` 의 기본 구성요소 대해 정리하고자 한다.

* **[https://academy.nordicsemi.com/topic/scheduler/](https://academy.nordicsemi.com/topic/scheduler/)**

---

### 8.2 쓰레드 (Threads)

쓰레드 (`Threads`)는 이어서 살펴볼 스케쥴러에 의해 관리되는 최소 실행 단위이다. **`Zephyr OS`** 와 같이 처럼 임베디드 기기를 위한 고안된 실시간 운영체제 내에서는 **`CPU`** 에서 반복적으로 수행할 동작을 정의해 놓은 일종의 함수라고 봐도 될 것 같다.

> **`nRF Connect SDK`** 개발 환경에 있는 쓰레드의 종류는 크게 다음의 두 가지로 구분할 수 있다.

* `Cooperative Thread` 
* `Preemptible Thread` 

여기서 음의 우선순위 (`negative priority`)를 갖는 **Cooperative** 쓰레드의 경우 용도는 제한적이라 개발자가 직접적으로 다루는 경우는 많지 않은 것으로 보인다. 실제로 개발하면서 다루게 될 쓰레드는 대부분 **Preemptible** 쓰레드에 해당한다 (*사실 이 부분은 위에 참고할 페이지의 내용을 토대로 작성한 거라 확실하는 않다*).

> 쓰레드의 상태는 다음과 같이 구분할 수 있다. 실제 원문 링크에는 `Running, Ready, Unready` 등의 단어를 혼용해서 사용하고 있다.

* 실행중 (**`Running`**): 문자 그대로 현재 CPU가 실행 중인 쓰레드.
* 실행가능 (**`Runnable`**): 스케쥴러에 의해 다음에 실행될 블록으로 선택되기를 기다리고 있는 쓰레드. 다른 쓰레드나 시스템 (e.g. ISRs)에서 사용되지 않으므로 `CPU` 내 시간 관련 자원 (e.g. Timer)만을 사용하며 대기하고 있으므로 이 상태를 대기(Ready) 상태라고 부르기도 하는 듯 하다.
* 실행불가 (**`Non-runnable`**): 하나 이상의 요소에 의해 실행할 수 없는 상태에 놓여있는 쓰레드. **가령, 해당 쓰레드에서 필요로하는 변수의 우선순위가 다른 쓰레드에 있다거나 해서 사용할 수 없는 경우 실행불가 상태에 놓이게 된다.** 이러한 경우 해당 쓰레드는 스케쥴링 배정에 포함되지 않으며, 이 상태를 준비되지 않은 상태 (`Unready`)라고 부르기도 한다.

---

### 8.3 스케쥴러 (Scheduler)

CPU에서 동작시킬 쓰레드를 결정해주는 요소. 특정한 규칙 (<span style="color:#2070a0"><b>스케쥴링 알고리즘</b></span>)을 이용해서 `CPU`가 다음에 수행할 업무 (`쓰레드`)를 배정해주는 역할을 수행한다. 자원이 한정적이기 때문에 정해진 알고리즘에 따라 실행시킬 쓰레드 순서를 배치할 관리자 (스케쥴러)가 필요한 것이다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-rtos-fig2.png" alt="">
</figure>

>노르딕 사 반도체 칩 (`SoC`)의 경우 한 번에 실행 가능한 쓰레드의 개수는 애플리케이션 코어 개수와 동일하다. 코어가 한 개라면 (`e.g. nRF52840`) 한 번에 하나의 쓰레드만 실행시킬 수 있다. 

nRF Connect SDK에서 사용되는 Zephyr RTOS는 기본적으로 "tickless RTOS"이다. 따라서, 스케쥴러 활성화를 위해 주기적으로 타이머를 체크하는 대신, 특정 "이벤트"에 반응해 (event-driven) 스케쥴러가 활성화된다. 따라서, 프로그램 내에 여러 개의 쓰레드가 정의되어 있다면, 어떤 식으로든 스케쥴러를 활성화시킬 포인트 (<span style="color:#2070a0"><b>Rescheduling Point</b></span>)가 정의되어 있어야 한다.

>이러한 <span style="color:#2070a0"><b>"Rescheduling Point"</b></span>가 없다면? CPU는 하나의 쓰레드만 계속해서 동작시키게 될 것!!

`nRF Connect SDK` 에서는 **`"k_yield()"`** 처럼 현재 실행 중인 쓰레드 상태를 바꾸는 (`예: "Running" to "Runnable"`) 함수나 세마포어 등의 요소를 활용해 이러한 이벤트 (`Rescheduling point`)를 생성하거나, 데이터 패싱이나 타임 슬라이싱 등에 의해 쓰레드의 상태가 결정되기도 한다.

 >쓰레드와 스케쥴러에 대해서는 추후에 보다 자세하게 다룰 예정이다.

---

### 8.4 인터럽트 서비스 루틴 (Interrup Service Routine, ISR)

**인터럽트 (`Interrupt`)** 에 대해 아예 모른다면 위키피디아 등의 **[인터럽트](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8)** 관련 글을 먼저 읽어볼 것을 권장한다. (구글링으로 다른 사람들이 간단하게 정리한 글을 보는 것이 더 나을 수도 있다. ~~아니면 ChatGPT 한테...~~ )

간략하게 이야기하자면 <span style="color:#A05040"><b>인터럽트</b></span> 란 프로세서 (`e.g. CPU`)가 지금 하고 있는 작업보다 중요한 일을 처리해야 할 때, 이를 수행하도록 알리는 방법의 일종이다. 이러한 인터럽트의 발생으로 인해 우선적으로 처리할 업무를 수행하는 과정을 **인터럽트 서비스 루틴 (이하, `ISR`)**  이다. 

>쓰레드의 경우 스케쥴러에 의해 그 순서가 관리되고 어느 정도 동기화되어있는데 반해, `ISR`은 비동기적인 예외처리 이벤트라고 할 수 있다. 인터럽트가 발생하게 되면, `CPU`는 현재 실행 중이던 쓰레드는 잠시 멈춰두고, `ISR`을 먼저 처리하게 된다. 기존에 실행 중이던 쓰레드는 (갑작스럽게 발생한) `ISR`의 처리가 끝난 뒤에 다시 진행된다.

따라서, `ISR`을 통해 호출되는 콜백 함수에서는 시간이 오래 걸리는 작업을 수행하거나 다른 쓰레드에 영향을 주는 (`e.g. blocking`) 작업을 수행해서는 안된다.

---

### 8.5 요약

`쓰레드`는 **Zephyr RTOS**의 기본 실행 단위이며, 이러한 쓰레드의 순서를 관리하는 것이 `스케쥴러`이다. 인터럽트를 처리하는 `ISR` 의 우선순위는 대개 쓰레드 보다 높기 때문에 전체 프로그램 동작에 영향을 주지 않도록 유의해야 한다.