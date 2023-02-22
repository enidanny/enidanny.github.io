---
title: "NCS (9) - Zephyr RTOS 쓰레드 (Threads) 종류"
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
  - Thread
  - 우선순위
  - 워큐 쓰레드
  - 워크 아이템
  - Workqueue
toc: true
toc_sticky: true
---

## 9. Zephyr RTOS 쓰레드 (Threads) 종류

**`Zephyr RTOS`** 와 같이 임베디드 장치를 위해 고안된 실시간 운영체제에서의 쓰레드는 (데스크톱 `CPU` 의 성능 지표로 언급되는 쓰레드와는 달리) 특정 동작을 정의해둔 단위 실행 블록 (함수)에 가깝다. 이번 포스팅에서는 아래 참고링크의 내용을 토대로 `Zephyr RTOS` 구성 요소 중 쓰레드에 대해 보다 자세히 살펴볼 예정이다.

* **[https://academy.nordicsemi.com/topic/scheduler/](https://academy.nordicsemi.com/topic/scheduler/)**

>A thread is the **"smallest logical unit of execution"** for the RTOS scheduler that is competing for the CPU time.

### 9.1 쓰레드 종류

**[이전 포스팅](https://enidanny.github.io/nrf%20connect%20sdk/nRF-Connect-SDK-Zephyr-RTOS-comp/)** 의 **`8.2`** 에서 언급했듯이, <span style="color:#20a0a0"><b>Zephyr RTOS</b></span> 를 기반으로하는 **nRF Conenct SDK** 에서의 쓰레드의 상태는 크게 **"실행중, 실행가능, 실행불가"** 세 가지로 구분된다.

또한, **`nRF Connect SDK`** 개발 단계에서 주로 다루게 되는 쓰레드는 "`Preemptible` 쓰레드"로 음의 우선순위를 갖지 않는다는(`non-negative`) 특징이 있는데, 이는 다시 다음의 세 가지 유형의 쓰레드로 세분화 된다.

* **`System threads`**
* **`User-created threads`**
* **`Worqueue threads`**

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-thread-fig1.png" alt="">
</figure>

#### 9.1.1 System threads

**시스템 쓰레드**는 **`RTOS`** 초기화 단계에서 자동적으로 생성되는 쓰레드로, **`nRF Connect SDK`** 에서는 기본적으로 두 개의 시스템 쓰레드가 자동으로 생성된다 (**`main`** and **`idle`** threads).

* `main thread`: **RTOS** 초기화 루틴 실행 후 애플리케이션 내에 main() 함수를 호출. 만약 main() 함수가 없을 경우에는 자동으로 main thread 가 종료된다.
* `idle thread`: 실행시킬 쓰레드 등 다른 작업이 없을 때 실행되는 쓰레드. 비어있는 루프문을 실행시키면서 다음 명령어를 대기하거나 (노르딕 디바이스의 경우)전원 관리 모듈에 의해 전력 소모를 줄이는 서비스를 동작시키기도 한다.

>시스템 쓰레드는 애플리케이션 구동을 위한 기본적인 쓰레드이며, 붙여진 명칭을 보면 대략적으로 그 쓰임새를 유추할 수 있다.

#### 9.2 User-created threads

**사용자 (= 개발자)**가 직접 정의하는 쓰레드로 특정 업무 (`예: 센서 데이터 측정, 데이터 프로세싱 등`)를 위해 생성 후 활용하는 단위 실행 블록에 해당 된다.

대부분의 쓰레드가 그렇겠지만 특히나 사용자가 직접 정의하는 **`User-created threads`** 의 경우 스케쥴러 (`Scheduler`)가 해당 쓰레드를 관리할 수 있도록 우선순위를 배정해주고 경우에 따라서는 **`"Rescheduling point"`**를 지정해주기도 해야한다.

#### 9.3 Worqueue threads

**nRF Connect SDK** 에는 `워크 아이템 (= a work item)`이라는 요소가 있다.

실제로는 단순한 사용자 정의 함수에 불과하지만, 이러한 함수들을 호출하기 위해 사용되는 전용 쓰레드인 워큐 쓰레드 (`Workqueue threads`)가 있으며 그 때문인지 앞서 언급한 특정 함수들을 가리켜 워크 아이템이라고 구분지어 부르고 있다.

워큐 쓰레드 또한 이름에서 짐작할 수 있듯 `FIFO` 메커니즘으로 동작하는 큐 (`Queue`)라고 보면 될 듯 하다. 굳이 차이점이 있다면 워크 아이템을 처리하기 위한 큐에 해당한다는 것. **그렇다면, 굳이 특정 함수를 워크 아이템으로 정의해서 워큐 쓰레드에 의해 처리하고자하는 이유는 뭘까?**

>The main use of this is to offload non-urgent work from an ISR or a high-priority thread to a lower priority thread.

급하게 처리해야할 작업이 아니라면 이러한 일들은 (`work items`) 굳이 높은 우선순위를 갖는 쓰레드나 `ISRs` 에서 처리하도록 하지 말고, **우선순위가 낮은 워큐 쓰레드** 에 작업을 맡겨서 CPU 효율을 증진시키기 위함인 듯 하다.

---

### 9.2 워큐 쓰레드

**nRF Connect SDK** 에서 구성하는 시스템은 하나 이상의 워큐 쓰레드 가질 수 있는데, 일단 시스템 워큐 쓰레드라는게 자동으로 생성된다. 여기서 다소 특이한(?) 부분은 **워큐 쓰레드를 통해 관리되는 아이템 (work items)을 실질적으로 실행시키는 주체는 "`시스템 쓰레드`"** 라는 점이다.

워큐 쓰레드와 아이템은 다음과 같은 방식으로 활용될 수 있다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-thread-fig2.png" alt="">
</figure>

>위 그림을 보면 우선순위가 높은 `쓰레드` 혹은 `ISRs` 에서는 아이템 (수행해야하는 업무)을 워큐 쓰레드에 배정만 하고 해당 루틴을 빠져나오는 것을 볼 수 있다. 이는 높은 우선 순위가 요구되는 루틴의 경우 다른 기능의 수행을 차단해버릴 수 있기 때문에 (<span style="color:#A02020"><b>Blocking</b></span>), 시간이 오래 소요될 것으로 예상되는 업무를 워크 아이템으로 배정하고 해당 루틴을 빠져나오는 식으로 설계하는 듯 하다.
