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

`_config.yml` 파일에서 아래의 코드처럼 search 기능을 true 로 변경하면 쉽게 활성화되기는 한다

```json
search                   : true #, false (default)
search_full_content      : true #, false (default)
search_provider          : # lunr (default), algolia, google
algolia:
  application_id         : # YOUR_APPLICATION_ID
  index_name             : # YOUR_INDEX_NAME
```

근데 아래 그림에서 볼 수 있듯이 활성화는되는데,
뭔가... UI 라고해야하나? 엄청 못생겼다..

물론 키원드 같은걸 검색해보면 블로그 내에 관련된 포스팅이 뜨기는 함.

---

근데 이렇게는 사용하기 싫으니깐^^

https://im-wali.github.io/githubpage/add-search/#1-algolia

여기 블로그 보면서 따라해보는것으로 시작. 아무래도 참고한 포스트는 2021년 3월에 작성된 글이라
지금하고 algolia 홈페이지 UI 나 설정이 꽤 다른 부분이 있다. (근데 보면서 대충 따라하면 할만함)