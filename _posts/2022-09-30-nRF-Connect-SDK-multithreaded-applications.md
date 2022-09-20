---
title: "NCS (7) - multithreaded application"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - NCS 2.x.x
  - nordic semiconductor
  - 노르딕
  - 노르딕 펌웨어 개발
  - nRF Connect SDK 개발
  - multithreaded application
toc: true
toc_sticky: true
---

## 7. multithreaded application

nRF Connect SDK (이하 SDK) 에서 사용되는 Zephyr RTOS 관련 개념들을 정리하려고함. 최소 실행 단위인 thread 를 비롯해서, ISR, schedular 같은 개념에 대해 스터디

### 7.1 쓰레드와 상태

일단, 쓰레드(thread) 에 대해서는 이미 PC 에 사용되는 CPU 의 스펙을 설명할때 자주 사용되는 개념이라서 많이 들어봤을 것. RTOS 에서는 쓰레드를 다음과 같이 정의하고 있다.

>A thread is the smallest logical unit of execution for the RTOS scheduler (covered later in this topic) that is competing for the CPU time. - 출처: [https://academy.nordicsemi.com/topic/scheduler/](https://academy.nordicsemi.com/topic/scheduler/)

필자가 이해하기로는 RTOS 기반 시스템에서는 `CPU` 가 정해진 순서에 따라 본인에게 할당되는 최소 실행 단위인 쓰레드를 실행시키는 듯하다. 쉬지않고 업무 (쓰레드)를 처리하는 직원 (CPU)을 이용해 회사 (어플리케이션)를 운영하는 느낌인데, 어떤 업무 (쓰레드)에 시간을 얼마나 소모하고 다른 업무를 수행할지 등을 결정해주는게 스케쥴러인 듯한 느낌이다.

RTOS (혹은 Zephyr RTOS) 에서의 쓰레드는 `Running, Runnable, Non-runnable` 중 하나의 상태를 갖는다고 한다.

* **`Running`:** 현재 CPU 에 의해 실행되고 있는 상태. 만약 특정 쓰레드가 `Running` 상태라면 이는 스케쥴러에서 해당 쓰레드를 실행하라고 CPU 한테 이미 명령을 마친 상태.
* **`Runnable`:** 이름을 통해 짐작이 가겠지만, CPU 가 실행시키기를 기다리고 있는 상태 (다른 시스템이나 쓰레드에서도 접근하고 있지 않음). 이러한 상태에 있는 쓰레드는 스케쥴링 알고리즘에 의해 CPU 에서 다음에 처리해야하는 업무로 배정된다.
* **`Non-runnable`:** `Unready` 상태라고도 불리는데, 현재 실행중인 쓰레드는 아니지만, 해당 쓰레드에서 필요한 자원이 준비되지 않아서 CPU 에서 여유가 생겨도 해당 쓰레드를 실행시킬 수 없는 상태를 가리킨다. 이러한 상태에 놓인 쓰레드는 스케쥴링 알고리즘에 의해 다음 실행 순번에 포함되지 않음.

---

### 7.2 쓰레드 종류 in Zephyr RTOS

이러한 내용이 실제 프로그래밍 할 때 필요할지는 모르겠지만...

참고한 링크: [https://academy.nordicsemi.com/topic/scheduler/](https://academy.nordicsemi.com/topic/scheduler/)

System Trhead 는 Zephyr RTOS 초기화 단계에서 자동으로 생성되는 쓰레드. 이러한 유형이 main 하고 idle 쓰레드가 있다고 한다.

#### 7.2.1 System threads

* main 쓰레드: 에서는 RTOS 초기화에 필수적인 요소드를 실행시키고, main() 함수가 있다면 이를 실행시킨다 (RTOS 어플리케이션에서는 main 함수가 없는 경우도 있음). 없다면 main thread 종료시킨다.
* idle 쓰레드: 에서는 다른 업무가 없을 때 돌아가는 쓰레드 그냥 비어있는 반복문이 돌아가거나 power management 모듈이 활성화되어서 전력 소모를 줄이기도 한다. CPU 가 다음 쓰레드에 배정될때까지 대기하는 상태라고 할 수 있겠다.

#### 7.2.2 User-created threads

개발자 (사용자)가 정의한 쓰레드 (개념이 익숙치 않다면, CPU 에게 일정 시간마다 수행하라고 지시할 함수 정도로 생각해도 될 것 같다~~맞나?~~) 해당 쓰레드의 경우 우선 순위나 스케쥴러가 CPU 에게 해당 쓰레드를 할당해주기 위한 정보를 가지고 있어야 함.

#### 7.2.3 Workqueue threads

SDK 의 실행 유닛으로는 work item 이란 이름을 갖는 녀석들이 있는데, workqueue thread 라는 dedicated thread (전용 쓰레드?)에 의해 호출되는 실행 유닛이라고 한다. 바꿔 말하면 workqueue threqd 라는 것은 work item 을 호출하기 위해 사용되는 쓰레드라는 것인데...
(~~무슨말인지 모르겠다~~)

1. SDK 실행 단위 중 work-item 이 있다.
2. 사용자 정의 함수
3. 전용 스레드 (workqueue thread) 에 의해 호출된다

workqueue 스레드라는 전용 쓰레드가 있고, 이는 work-item 이라는 또다른 실행 단위 (사용자 정의함수)를 호출한다.

workqueue 스레드는 결국 사용자 정의함수들 work-items 처리하는 스레드인데,



