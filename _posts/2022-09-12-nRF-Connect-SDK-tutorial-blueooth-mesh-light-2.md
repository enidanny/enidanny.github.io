---
title: "NCS (6) - nRF Connect SDK 예제 따라하기 - Bluetooth: Mesh light (2)"
categories:
  - nRF Connect SDK
tags:
  - NCS
  - NCS 2.x.x
  - nordic semiconductor
  - 노르딕
  - 노르딕 펌웨어 개발
  - nRF Connect SDK 개발
  - 블루투스 메시
  - 블루투스 메시 튜토리얼
  - 노드
  - 구독
  - Bluetooth mesh
  - mesh light
  - generic model
  - generic onoff server
  - tutorial
  - nRF Mesh
  - publish
  - subscribe
  - Group Address
toc: true
toc_sticky: true
---

## 6. nRF Connect SDK 예제 따라하기 - Bluetooth: Mesh light (2)

**[이전 포스트](https://enidanny.github.io/nrf%20connect%20sdk/nRF-Connect-SDK-tutorial-blueooth-mesh-light/)**🔗 에 이어서 **nRF Mesh**앱을 이용해 `Bluetooth: Mesh light` 예제가 테스트 과정을 간략하게 소개하고자 한다.

>다음부터는 테스트 결과정도만 간략하게 정리할 예정이다 :)

### 6.1 nRF Mesh 앱 이용해서 LED 제어하기

이전 포스트에서 소개한 내용을 그대로 따라했다면, 각자의 **nRF Mesh 앱**과 `Bluetooth: Mesh light` 예제가 업로드 된 **노드 (`node`)** 는 같은 메시 네트워크 상에 포함되어 있는 상태이다. 따라서, **nRF Mesh**앱을 이용해 노드의 `LED` 를 제어하는 것이 가능하다.

먼저 **nRF Mesh 앱** 을 이용해서 메시 네트워크에 추가시킨 노드에 연결하고, 노드가 가지고 있는 `element` 중 하나를 선택하여 `Generic On Off Server` 모델에 접근해보자. 아래 예시에서는 첫 번째 `element` (주소 값: `0x0002`)의 `Generic ...` 모델에 접근했지만, 다른 `element` 중 어느 것에 접근해도 동일한 절차로 진행된다고 보면 된다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light2-fig1.png" alt="">
</figure>

`Generic ...` 모델을 선택하면 위의 가운데 사진같은 화면이 표시될 텐데, `Generic ...` 모델을 이용해서 `element` (본 예제에서는 **LED**)를 제어하기 전 애플리케이션 키 (`App Key` 🔑)를 등록시켜야 한다. **`BIND KEY`** 버튼을 누르면, 위의 오른쪽 그림에서처럼 선택할 수 있는 **`App Key`** 목록이 뜰 것이다. (여기서는 하나만 보일텐데, 아마도 프로비저닝 및 설정 단계에서 별다른 조치 없이 **default** 조건으로 해서 그럴 것이다)

>예전에 작성했던 포스트에서 이 부분을 정리해두었는지는 모르겠는데... 블루투스 메시에서 네트워크에 포함된 노드간에 주고 받는 일종의 ID 카드로 애플리케이션 키와 네트워크 키가 있다. 일단은 메시 앱을 사용할 때 이렇게 사용하는 구나 정도로만 생각하고 넘어가자 (나중에 기회가 되면 자세하게 정리해보는 걸로...~~필자도 잘 모름^ 크흠..~~)

여기까지 했다면, 아래 그림과 같이 **`Bound App Keys`** 메뉴 하단에 선택한 애플리케이션 키 정보가 등록된 것을 확인할 수 있을 것이다. 또한, 화면을 아래로 내려서 **Generic On Off Controls** 메뉴 하단의 **`READ STATE`** 또는 **`OFF`** 버튼을 눌러보면 선택한 `element` (LED) 의 상태 정보를 확인하거나 변경할 수 있을 것이다. 메시 노드의 다른 `element` (LED) 도 동일한 방식으로 제어가 가능하다.

<figure style="width: 75%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light2-fig2.png" alt="">
</figure>

---

### 6.2 ~~Publish~~ Subscribe 테스트

위의 사진을 보면 `Generic On Off Server` 메뉴 가운데에 `Publish`, `Subscription` 메뉴가 있는 것을 볼 수 있을 것이다. 본 예제에서 사용된 예제는 `Server` 모델이므로 Subscribe 기능을 테스트가 가능하다 (**Publish** 기능도 테스트가 가능할 지는 잘 모르겠음). 블루투스 메시에서 사용되는 **Publish & Subscribe** 모델은 `MQTT` 프로토콜에서와 유사한 개념인데.. 
이전 예시처럼 하나의 `element` 에 포함된 모델은 직접 제어할 수도 있지만, `Group Address` 를 이용해서도 제어가 가능하다 🚐.

>만약 어떤 모델이 특정 `Group Address` 를 구독 (`Subscribe`) 하고 있다면 해당 주소를 통해 `Publish` 된 명령어 정보를 수신할 수 있는데, 이러한 정보는 해당 `Group Address` 를 구독하는 다수의 모델에 전달되므로... 결과적으로는 주로 하나 이상의 `element` 를 제어하기 위해 사용된다고 보면 된다.

아래 그림은 2 개의 `Group` (Address)을 임의로 생성한 뒤, 메시 노드에 있는 4개의 `element` 를 2 개의 `Group` 으로 나누어서 제어하기 위한 과정의 일부를 보여준다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/ncs-sample-mesh-light2-fig3.png" alt="">
</figure>

위의 제일 왼쪽 사진이 하나의 `element` 내에 있는 `Generic` 모델을 선택 후 `Group Address` 를 구독 (`Subscribe`) 하는 과정에 해당된다. 이 경우**nRF Mesh**앱에서 각각의 `Group Address` 로 **Publish** 하는 정보를 받아, 각 Group 의 `element` 상태가 변하게 된다.

---

### 6.3 기타 주의사항

**nRF Mesh** 앱 및 블루투스 메시 예제 테스트 시에 주의할 사항으로 **특정 보드에서 예제 소스 코드를 새로 업로드하기 전 반드시 reset 이후 작업을 진행할 것** 을 권장한다.

>만약, 메시 노드에 `unprovisioning` 상태로 reset 하는 기능이 포함되어있지 않다면,**nRF Mesh**앱을 강제로 reset 시킬 경우 이전에 추가했던 네트워크 정보가 사라지므로, 기존에 생성된 네트워크에 접근 자체가 불가능하다.

이 경우 단순히 펌웨어를 새로 올린다고 하더라도 `provisioning` 상태가 변경되지 않으므로 (아마도 `flash memory` 영역 내에 해당 정보가 저장되는 것으로 추정), 메모리를 전부 지운뒤 새로 업로드를 해야하는데... 경우에 따라서는 곤란한 상황이 생길 수도 있다🚀. 따라서, 펌웨어에 `unprovisioning` 기능을 넣어두거나, 소스 코드 변경하기 전 **nRF Mesh** 앱을 이용해 reset 작업을 수행할 것을 권장한다.