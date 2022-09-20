---
title: "BLE (17) - Auracast Broadcast Audio"
categories:
  - BLE
tags:
  - Bluetooth
  - Bluetooth 5.2
  - Bluetooth LE
  - 블루투스
  - 블루투스 5.2
  - LE Audio
  - LE 오디오
  - Auracast
toc: true
toc_sticky: true
---

## 17. Titles

### 17.1 LE Audio Overview

BLE 무선 오디오 이슈 (이하 **`LE Audio`**)에 대해 이야기를 했던게 벌써 작년 (`2021년`) 8월이다. 블루투스 5.2 (`Bluetooth 5.2`) 표준에서 공개된 특징 중 하나가 바로 이 `LE Audio` 였는데, 근 1년이 지난 지금 어디까지 기술이 개발되었는지 궁금해서 최근에 찾아보게 되었다.

`-` **[https://www.somagnews.com/bluetooth-le-audio-technology-faster-and-more-efficient/](https://www.somagnews.com/bluetooth-le-audio-technology-faster-and-more-efficient/)**

`-` **[https://enidanny.github.io/ble/ble-bluetooth-52/#143-wireless-audio-application](https://enidanny.github.io/ble/ble-bluetooth-52/#143-wireless-audio-application)**

---

### 17.2 Auracast broadcast audio


LE Audio 핵심은 결국 저전력 & 복수의 싱크노드로 보낼 수 있는 능력이 핵심이었음.
그 중에서도 오디오 공유 기술이 접목된 무선 오디오를 Auracast broadcast audio 라고 부르는 듯 하다.

심심치 않게 블루투스 `SIG (special interset group)` 에서 발표한 새로운 무선 오디오 규격인 Auracast 관련 기사가 나오고 있다.

[https://www.androidauthority.com/bluetooth-sig-auracast-3173964/](https://www.androidauthority.com/bluetooth-sig-auracast-3173964/)

[https://www.digitaltrends.com/home-theater/bluetooth-auracast-le-audio-lc3-codec/](https://www.digitaltrends.com/home-theater/bluetooth-auracast-le-audio-lc3-codec/)



아래 참고한 기사에서도 명시되어 있듯이 Auracast 핵심은 하나의 음원 소스에서 근처에 있는 모든 수신기로 정보를 전달하는데 제한이 없음. Auracast 지원되는 어떤 기기에서든 동시에 음원이 전달된다는 것.

>Auracast broadcast audio will enable an audio transmitter – whether that be a smartphone, laptop or television – to broadcast audio to an unlimited number of nearby Bluetooth audio receivers, including speakers, earbuds or even hearing-aid device - 출처: [https://www.forbes.com/sites/marksparrow/2022/06/08/bluetooth-auracast-will-be-able-to-broadcast-sound-to-unlimited-users/?sh=76bb5440b92f](https://www.forbes.com/sites/marksparrow/2022/06/08/bluetooth-auracast-will-be-able-to-broadcast-sound-to-unlimited-users/?sh=76bb5440b92f)

---

또다른 특징으로는 공공장소에서 영상으로만 제공되던 광고나 영상들에 auracast 로 접근해서 듣는 것도 가능해질 거라는 것...? 특히 공공장소에서는 소음때문에 보통 소리를 꺼놓을텐데, 이를 auracast 로 송출하면 원하는 사람은 들을 수도 있을 것이다.

또한 극장이나 이런 곳에서도 auracast 지원되면 더 실감나게 소리를 들을 수도 있을 것... (이게 진짜 되는 세상이 올지는...ㅋㅋㅋ) 공항 등에서도 더 명확하게 auracast 로 정보를 받을 수도 있겠지. 차세대 ALS 기술로 활용될 것 (assistiv listening system)

[https://www.bluetooth.com/auracast/](https://www.bluetooth.com/auracast/)

자세한 내용은 위에 링크를 참조하자

---

Public Broadcast Profile (PBP) 이용해서 구현된 기술인 것...?

https://www.bluetooth.com/specifications/specs/public-broadcast-profile-1-0/