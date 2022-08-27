---
title: "일상 (4) - 구글 애널리틱스 (Google Analytics) 4 세팅하기"
categories:
  - Blog
tags:
  - Google Analytics 4
  - GA4
  - 구글
  - 구글 애널리틱스
toc: true
toc_sticky: true
---

## 구글 애널리틱스 (Google Analytics) 4 세팅하기

블로그를 처음 시작할 당시 [취미로 코딩하는 개발자](https://devinlife.com/howto%20github%20pages/google-search-console-and-analytics/) 포스트를 보면서 내 블로그를 구글 애널리틱스에 등록해두었는데, 이렇게 하면 **`Google Analytics`** 를 통해 내 블로그로 유입되는 방문자들의 경로, 위치, 어떤 포스트를 자주보는지 등 통계 정보를 수집할 수 있음. 그런데, 최근 **`Google Analytics`** 접속해보니 다음과 같은 문구가 뜨는 것을 확인했다.

>유니버설 애널리틱스는 2023년 7월 1일부터 표준 속성의 신규 데이터를 더 이상 처리하지 않습니다. 지금 Google 애널리틱스 4 속성을 설정하고 이 속성으로 전환하여 준비하세요.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig1.png" alt="">
</figure>

이게 뭔 캣(🐱)떡같은 소리인가 싶었는데, 대충 지금까지는 유니버셜 애널리틱스 (**`UA`**)란 녀석이 통계 자료를 수집하는 역할을 해줬는데 이제는 그만두게 되었으니, 구글 애널리틱스 4 (**`GA4`**)를 이용해서 통계자료를 수집하라는 것 같다. 일단 나는 아래의 포스트 내용을 토대로 작업을 시작하였음.

* **[https://growth-mkt-k.kr/how-to-setup-google-analytics-4/](https://growth-mkt-k.kr/how-to-setup-google-analytics-4/)**

---

### 4.1 구글 애널리틱스 4 속성 생성

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig2.png" alt="">
</figure>

위 그림과 같이 구글 애널리틱스 메인 화면 접속 후,

* 왼쪽 하단의 **관리 ⚙️** 메뉴 클릭
* **`Google` 애널리틱스 4 속성 설정 어시스턴트** 클릭

아래와 같은 페이지가 뜨는 것을 볼 수 있다. 쓰여진 글을 보면, ~~그까이꺼~~대충 기존에 사용하던 유니버설 애널리틱스 속성을 이용할 수 있게 `Google` 애널리틱스 4 속성을 생성한다는 것 (인수인계 해주겠다는 뜻) 같다.

* **시작하기** 클릭

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig3.png" alt="">
</figure>

* **속성 만들기** 클릭

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig4.png" alt="">
</figure>

참고로, 위 그림에서 **`"이 마법사의 다른 기능"`** 이라는 부분의 **`체크 박스`** ☑️를 체크해야하는 건지 아닌지는 잘 모르겠다. 나의 경우는 자동으로 체크가 되길래 그냥 체크된 상태로 두었음.

---

### 4.2 Google 태그 관리자 이용해서 데이터 수집 설정하기

* 위 과정 완료 후 왼쪽 하단의 **관리** ⚙️ > **데이터 스트림** 선택

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig5.png" alt="">
</figure>

**`기존 온페이지 태그 사용`** 항목 중 **`Google 태그 관리자`** 를 이용하는 방식으로 데이터 수집을 설정하고자 함.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig6.png" alt="">
</figure>

**`Google 태그 관리자`** 설정을 위해서 아래 그림에 표시한 것처럼, 구글 애널리틱스 상단의 자신 계정이름 선택 후 태그 관리자 메뉴를 선택하면 된다. 나는 블로그 초반에 이 부분을 설정해두지 않아서 이번에 처음 설정했음.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig7.png" alt="">
</figure>

다음의 왼쪽 그림과 같이 새 계정을 추가하고나서 (~~이렇게 하는게 맞나?~~), **`새 태그 추가`** 를 클릭해서 태그와 트리거를 설정해주었다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig8.png" alt="">
</figure>

태그의 이름은 참고한 포스트와 동일하게 **`GA4 Config Tag`** 로 설정해주었고, 다음과 같은 순서로 진행하였음. 아래 첨부한 그림을 보고 따라하면 될 것 같고, 여기까지 하면 기본적인 설정은 마무리가 되는 것 같다.

* **`태그 구성 > 태그 유형 선택 > Google 애널리틱스: GA4 구성`** 클릭
* **`트리거 > All pages`** 클릭

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig9.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig10.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ga4-fig11.png" alt="">
</figure>

---

### 4.3 그 외에 추가 작업

#### 4.3.1 구글 애널릭틱스 4 초기 설정 방법

데이터 설정을 포함해서 몇 가지 유용한(~~유용해 보이는?~~) 설정들을 정리해 놓은 포스트가 있으니 참고해서 본인이 설정하고 싶은 부분 설정해보면 좋을 것 같다.

* **[https://finedata.tistory.com/52?category=913224](https://finedata.tistory.com/52?category=913224)**

---

#### 4.3.1 _config.yml 설정 변경

데이터 스트림 메뉴를 보면 `G-` 로 시작하는 본인 계정의 아이디가 있을 텐데, 블로그 저장소 (`repository`)의 `_config.yml` 파일에서 `analytics` 항목의 `tracking_id` 를 기존의 유니버설 id 에서 새로 생성한 구글 애널리틱스 4 속성 id 로 변경하였음.

```yml
# Analytics
analytics:
  provider               : "google-gtag" # false (default), "google", "google-universal", "custom"
  google:
    tracking_id          : "G-6MBCCTFK3F"
    anonymize_ip         : # true, false (default)
```
>사실 이 부분은 꼭 필요한 것인지는 모르겠는데, 어차피 나중에 유니버설 애널릭틱스 지원이 종료되면 tracking_id 값은 Google Analytics 4 id 로 바꿔야할 것 같아서 그냥 바꿔두었다.