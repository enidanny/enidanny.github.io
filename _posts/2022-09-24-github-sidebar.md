---
title: "Github 블로그 Tips (7) - minimal-mistake 테마 사이드바 추가하기"
categories:
  - Github Blog
tags:
  - HowToStart
  - Jekyll
  - 깃허브 블로그
  - 사이드바
  - github
  - github page
  - minimal-mistake
  - sidebar
toc: true/   
toc_sticky: true
---

## 7. minimal-mistake 테마 사이드바 추가하기 💎

이번 글을 끝으로 해서 `Github 블로그` 관련 글은 한 동안 쓰지 않을 것 같다. 최근 들어서 블로그 초창기에 구축하지 못했던 기능들을 이것저것 추가하고 있는데, 블로그 왼쪽에 사이드바를 추가하는 것도 그 중에 하나였다 🙃.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-sidebar-fig1.png" alt="">
</figure>

### 7.1 참고한 블로그

본인처럼 `minimal-mistake` 테마로 블로그를 운영중인 사람이라면 아래의 포스팅을 따라서 참고하면 될 듯 하다.

* **[`[minimal-mistakes]` 카테고리(category) 만들기](https://x2info.github.io/minimal-mistakes/%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC_%EB%A7%8C%EB%93%A4%EA%B8%B0/)**
* **[`[minimal-mistakes]` 카테고리 옆에 포스팅 갯수 추가하기](https://x2info.github.io/minimal-mistakes/%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC_%EB%A7%8C%EB%93%A4%EA%B8%B0/)**
* **[`[Github 블로그]` minimal-mistake 블로그 카테고리 만들기 (+ 전체 글 수)](https://ansohxxn.github.io/blog/category/)**

>필자도 Jekyll 페이지 관련 지식이 하나도 없는 상태에서 진행을 했기 때문에.. 아마 대부분 (?) 할 수 있지 않을까 싶다..!

---

### 7.2 사이드바 추가시 몇 가지 Tips 🧵

(`minimal-mistake` 테마) 블로그에 사이드바를 추가하는 과정은 위에 필자가 참고한 포스팅 글들을 보고 그대로 따라하면 될텐데... 여기에 몇 가지 첨언을 하자면,

* **본인이 참고한 블로그의 `github` respository 에 가서 어떤 식으로 페이지를 구축했는지 확인해볼 것:**

특히나 본인이 사용하는 테마와 동일하다면, 해당 블로그의 `github` 페이지 `repository` 의 동일한 설정 파일을 확인해보면 어떤 식으로 페이지를 구축해야하는 지 감이 잡힐 것이다.

>`github` 블로그의 respository 는 반드시 `public` 으로 발행해야하기 때문에 누구나 확인할 수 있다.

* **카테고리 (`category`) 이름하고, `permalink` 혹은 `url` 이름 혼동 주의:**

카테고리 페이지에 해당 카테고리의 글을 표시하도록 설정하는 과정에서 **`"assign posts = site.categories.blog"`** 형식으로 참조하는 포스팅을 설정하는 부분이 있다. 저기서 `blog` 부분은 본인이 포스팅에서 입력한 카테고리와 동일한 문구를 사용해야한다.

>예를 들어, 본인의 경우 `BLE` 라는 카테고리로 발행한 포스팅이 있는데, 해당 카테고리를 참조하기 위한 페이지의 참조링크 (`permalink`)는 `ble` 로 설정하였다. 이 경우 위의 `blog` 위치에는 참조링크의 주소가 아닌 사용한 카테고리의 이름 (`BLE`)를 적용해야 한다는 것.

만약 본인 카테고리에 띄어쓰기가 포함되어 있을 경우에는 (예시: `github blog`), `site.categories.['github blog']` 형태로 입력하면된다 (대괄호 + 작은따옴표 이용).

