---
title: "Github 블로그 Tips (5) - Github 블로그 일 방문자 수 표시하는 법"
categories:
  - Github Blog
tags:
  - HowToStart
  - Jekyll
  - 깃허브 블로그
  - 방문자수
  - 조회수
  - 정적 페이지
  - github
  - github page
  - HITS
  - minimal-mistake
toc: true/   
toc_sticky: true
---

## 5. Github 블로그 일 방문자 수 표시하는 법

깃허브 블로그는 **[구글 애널리틱스](https://analytics.google.com/)** 를 이용하면 통계 정보를 확인할 수 있는데, **네이버**나 다른 플랫폼에서처럼 방문자 수를 표시하거나, 구글 애널리틱스 정보를 연동시키는 기능이 없는 듯 하다(가능한 지는 잘 모르겠음). 

>아무래도 로컬 (본인 PC)에서 작성한 포스팅을 깃허브 보관함에 올려서 블로그 페이지처럼 보여주는 방식이라서 그런 것 같은데...

찾아보니깐 <span style="color:#A02050"><b>Jekyll</b></span> 등을 이용해 정적 페이지 방식의 블로그는 이런 조회수를 기록하는 기능이 따로 없다고 하는데... **[HITS](https://hits.seeyoufarm.com/)** 페이지를 이용해 조회수를 표시할 수 있는 방법이 있길래 본인 블로그에도 적용을 해보았음.

### 5.1 참고한 포스트 🗃️

자세한 내용은 아래의 참고한 링크를 확인할 것.

* **[https://khw11044.github.io/blog/githubpages/2020-12-26-making-blog-08/](https://khw11044.github.io/blog/githubpages/2020-12-26-making-blog-08/)**
* **[https://dev-yakuza.posstree.com/ko/share/hit-counts/](https://dev-yakuza.posstree.com/ko/share/hit-counts/)**

---

### 5.2 방문자 태그 설정 방법

**[HITS](https://hits.seeyoufarm.com/)** 링크를 타고 들어가보면 아래와 같은 페이지를 볼 수 있는데, 본인의 URL 주소를 **`TARGET URL`** 영역에 쓰면되고 (필자의 경우는 `https://enidanny.github.io/`) 아래의 **`OIPTIONS`** 메뉴에서 방문자 태그 색상이나 아이콘 등을 커스터마이징할 수 있다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-visit-fig1.png" alt="">
</figure>

커스터마이징 완료 후 스크롤을 아래로 내리면 **`MARKDOWN`, `HTML`** 등의 파일에 적용가능한 코드가 자동으로 생성되어있는데, 이제 이걸 본인 깃허브 블로그를 구성하는 코드에 적절한 위치에 넣어주면된다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-visit-fig2.png" alt="">
</figure>

필자는 현재 `minimal-mistake` 테마를 이용해서 깃허브 블로그를 운영하고 있는데.. 주먹구구식으로 구축한 페이지라서 어디에 위 링크를 넣어야하는지에 대해서는 잘 모르는데, 일단 필자는 📁 `blog repository > includes` 폴더 안에 있는 `masthead.html` 파일에 `html link` 를 추가했다. (위치 설명이 애매해서 아래 이미지에 코드를 추가한 부분을 표시해두었음)

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-visit-fig3.png" alt="">
</figure>

---

### 5.3 코드 적용 결과

위의 이미지의 `masthead.html` 파일에 표시된 위치에 복사한 `.html` 코드를 붙이면, 아래 그림에서 볼 수 있듯이 현재 블로그에 적용되어있는 일 방문자 수 태그가 설정된다.

<figure style="width: 95%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-visit-fig4.png" alt="">
</figure>

>방문자 태그 위치를 다른 곳으로 하고 싶긴했는데... 일일이 테스트하다보니 결과적으로는 현재 위치로 설정하였음 😐.

---

### 5.4 참고할 사항

`-` 이 방식으로 일 방문자 태그를 달면, **"등록한 날을 기준으로"** 데이터베이스가 쌓이기 시작한다.

`-` 필자처럼 기본 옵션 위주로 설정하면 일 방문자 태그가 **`TODAY / TOTAL`** 형식으로 표시되는데, 여기서 **`일 방문자`** 정보는 한국 시간은 아닌 것 같고... 미국 시간? **`HITS`** 페이지 서버의 시간을 기준으로 업데이트 되는 것 같다.

`-` **`HITS`** 를 이용해서 방문자 수를 기록하는 방식은 **`URL`** 을 클릭한 횟수(?) 만큼 카운트가 되어서, 구글 애널리틱스 페이지를 기준으로 조회되는 방문자수하고 차이가 난다
>블로그에 표시되는 일 방문자수가 구글 애널리틱스 보다 거의 두 배 넘게 기록이 되는 것 같다..?
