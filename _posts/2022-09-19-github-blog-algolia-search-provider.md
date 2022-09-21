---
title: "Github 블로그 Tips (6) - Algolia, Travis 이용해서 블로그 내 검색 기능 (Site search) 활성화 하는 법"
categories:
  - Github Blog
tags:
  - HowToStart
  - Jekyll
  - 깃허브 블로그
  - 검색엔진
  - 지킬 검색엔진
  - 블로그 내 검색
  - 정적 페이지
  - 지킬 페이지
  - github
  - github page
  - minimal-mistake
  - search
  - search provider
  - algolia
  - jekyll-algolia
  - travis
  - site search
toc: true/   
toc_sticky: true
---

## 6. Algolia, Travis 이용해서 블로그 내 검색 기능 (Site search) 활성화 하는 법

`Jekyll` 페이지 구축하는 것에 대한 지식이 전무하다보니, 이미 있는 기능 활성화 시키기도 쉽지가 않다. 이번 포스트에는 필자가 블로그 내 검색 기능 (`Site search`) 활성화 시키면서 찾아본 링크 정도만 간략하게 정리하고, 본인 깃허브 블로그의 `repository` `config` 파일 설정 방법에 정도만 기록해두려고 한다.

> 프론트엔드 개발자들(?)이 봤을 때는 별 거 아닐 것 같은데.. ~~ㅂㄷㅂㄷ~~

### 6.1 `_config.yml` 내 검색 기능 활성화

기본적으로 필자가 사용중인 `minimal-mistake` 테마는 `lunr` 라는 search provider 를 이용해서 검색 기능을 활성화시킬 수 있다. 아래의 코드에서 볼 수 있듯이, `_config.yml` 파일에서 search 기능을 `true` 로 변경하면 페이지 우측 상단에 검색 아이콘이 나타나는 것을 볼 수 있을 것이다.🔍

```js
search                   : true #, false (default) <---
search_full_content      : true #, false (default) <---
search_provider          : # lunr (default), algolia, google
algolia:
  application_id         : # YOUR_APPLICATION_ID
  index_name             : # YOUR_INDEX_NAME
```

> 검색 아이콘을 눌러보면 아래 그림과 같이 정상적으로 기능이 동작하는 것을 확인할 수 있는데, `UI` 라고 해야하나? 디자인이 상당히 부자연스럽다. 날(`raw`) 것 그대로의 느낌이다.

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig1.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig2.png" alt="">
</figure>

---

### 6.2 Algolia, Travis 설정 관련 링크 🗃️

위의 그림처럼 search provider 로 `lunr` 를 사용하고 싶지는 않아서, 대신에 `algolia` 를 이용해 블로그 내 검색 기능을 설정하는 방법에 대해 찾아보았다. 일단 시작은 아래의 블로그 링크를 보면서 시작했는데... 아무래도 해당 포스팅이 작성된게 `2021년 3월` 이라서 그런지 `algolia` 사이트도 그렇고 `travis` 도 `UI` 나 페이지 디자인이 꽤 많이 바뀌었다.

* **[검색 & CI 기능 추가](https://im-wali.github.io/githubpage/add-search/)**

물론, 전반적인 내용은 동일해서 진행하는데 있어서 크게 문제는 없었다. 다만, 이대로 진행해도 정상적으로 검색 기능이 활성화가 되지 않았다는게 문제. 추가로 이런저런 블로그의 내용을 보면서 여러 시도를 했고... 지금은 정상적으로 검색 기능이 동작하고 있다.

* **[깃블로그에 검색 기능을 추가해보도록 하자.](https://xinfolab.github.io/blog/blog-maker-4/)**
* **[깃허브 블로그 만들기 [6] - 검색도구 algolia 적용](https://velog.io/@shg4821/%EA%B9%83%ED%97%88%EB%B8%8C-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EB%A7%8C%EB%93%A4%EA%B8%B0-6-%EA%B2%80%EC%83%89%EB%8F%84%EA%B5%AC-algolia-%EC%A0%81%EC%9A%A9#--travis-ci-%EC%84%A4%EC%A0%95)**

처음 포스팅을 쓰려고 했을때만 해도 일목요연하게 정리를 해보려고 했는데, `algolia` 나 `travis` ~~이딴것들~~ 에 대해 모르다보니, 결국에는

> "이것저것 테스트하다보니 되더라"

라고 말할 수 밖에 없는 상황이 되었다.

---

### 6.3 jekyll-algolia 오류 디버깅

주먹구구 식으로 기능을 추가하기는 했는데.. 본인이 작업한 순서는 위의 다른 링크에서 설명한 것과 동일하다.

>📁 1. `algolia` 가입 (`github` 계정 이용) 후 API Key 생성 <br>📁 2. `travis` 가입 (`github` 계정 이용) 후 위의 참고 링크의 내용대로 진행

어떤 블로그에서는 `travis` 가입하고 블로그 페이지 연동하는 부분에 대해서는 `git push` 단계에서 디버깅하기 위해서라는 식으로 설명했던 것 같은데, `algolia` 검색엔진을 사용하려면 `travis` 에도 가입해서 블로그 repository 를 연동시켜야 하는 것 같았다.

>📁 3. `jekyll-algolia` 플러그인 설치

위의 1, 2 과정을 모두 진행했음에도 블로그 내에 검색 기능이 정상적으로 활성화 되지 않았는데... 최종적으로는 `jekyll-algolia` 플러그인 설치를 완료한 뒤에야 검색 기능이 정상적으로 동작했다.
(혹은 `jekyll-algolia` 플러그인 활성화를 먼저 진행했어야 했을 수도 있을 것 같다.) 해당 내용은 아래의 공식 페이지 내용을 따라서 진행하면 된다.

* **[https://mmistakes.github.io/minimal-mistakes/docs/configuration/#site-search](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#site-search)**
* **[https://community.algolia.com/jekyll-algolia/github-pages.html](https://community.algolia.com/jekyll-algolia/github-pages.html)**

혹시나 필자처럼 `jekyll` 페이지를 다루는 것에 익숙하지 않은 사람들을 위해, 현재 운영중인 `minimal-mistake` 테마에서 필자가 수정한 파일들은 다음과 같다.

#### 6.3.1 `_config.yml`

`_config.yml` 파일 내 `plugins:` 항목에 `jekyll-algolia` 추가

```yml
#...
# Plugins (previously gems:)
plugins:
  - jekyll-paginate
  - jekyll-sitemap
  - jekyll-gist
  - jekyll-feed
  - jekyll-include-cache
  - jekyll-algolia
```

#### 6.3.2 `Gemfile`

```js
source "https://rubygems.org"
gemspec

gem "tzinfo"
gem "tzinfo-data"
gem 'jekyll-admin', group: :jekyll_plugins
gem 'wdm', '>= 0.1.0'
gem "webrick", "~> 1.7"

group :jekyll_plugins do
    gem "jekyll-feed"
    gem "jekyll-seo-tag"
    gem "jekyll-sitemap"
    gem "jekyll-paginate"
    gem "jekyll-include-cache"
    gem "jekyll-algolia"
end
```

#### 6.3.3 `.travis.yml`

```yml
language: ruby
cache: bundler
before_install:
  - gem install bundler
gemfile: Gemfile
script:
  - bundle exec jekyll algolia --config _config.yml
branches:
  only:
    # Change this to gh-pages if you're deploying using the gh-pages branch
    - master
rvm:
 - 2.4
```

#### 6.3.4 `Rakefile`

`Rakefile` 파일 마지막에 아래 코드 추가 (의미가 있는지는 모르겠음)

```js
#...
# Generate the site
sh "bundle exec jekyll build"

# Run Algolia 
sh "bundle exec jekyll algolia"
```

---

### 6.4 `bundle install` 및 플러그인 설치

위에서의 코드 수정 이후 `bundle install` 명령을 실행해서 `Gemfile` 에서 사용되는 패키지(?)들을 설치하였고, 앞서 소개한 **[minimal-mistake 관련 페이지](https://mmistakes.github.io/minimal-mistakes/docs/configuration/#site-search)** 내용을 따라서 아래의 명령어를 입력해주었다.

>`ALGOLIA_API_KEY=... bundle exec jekyll algolia`

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig3.png" alt="">
</figure>

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig4.png" alt="">
</figure>

만약 플러그인(~~맞냐?~~) 설치 과정에서 문제가 없을 경우, 아래 그림에서 볼 수 있듯이 `algolia` 페이지에서 설정했던 인덱스 이름 (필자의 경우 `enidanny` 로 했음)이 표시되면서, 뭔가 진행되고 있는 듯한 프로그래스 바가 표시되는 것을 볼 수 있을 것이다. (여기까지하면 진짜 끝)

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig5.png" alt="">
</figure>

>▼최종 적용 예시

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/github-blog-search-fig6.png" alt="">
</figure>

검색 아이콘 눌렀을 때의 디자인도 뭔가 정갈해졌고, 검색어 입력 전에는 최근 작성된 글 순서로 포스팅 목록이 표시된다. 검색어 입력 시 해당 키워드가 제목 혹은 본문에 포함된 글들이 검색되는 것을 확인할 수 있을 것이다. 

**⚠️ 검색 목록 업데이트:** 위 과정에서 `Admin API Key` 를 이용해서 `ALGOLIA_API_KEY=... bundle exec jekyll algolia` 명령어를 입력하는 시점에 `Jekyll` 페이지에 업로드된 글들이 검색 목록에서 조회가 되는 것 같다. 즉, 그 이후에 작성된 글들은 검색이 되지 않을텐데.. 일단 필자는 포스팅 하나를 새로 올릴 때마다 (어차피 그렇게 자주 쓰지도 않으니깐..) 검색 목록을 업데이트 하려고 한다. 이를 자동화하는 방법도 있을 것 같은데 일단은 모르겠음. (혹은 한 두 달에 한 번씩 업데이트 해도 될 것 같다.)
{: .notice--primary}

>지킬`Jekyll` 알못(~~ㅈㄹ못~~) 입장에서 작성한 글이지만, 누군가에겐 도움이 되었길 😅