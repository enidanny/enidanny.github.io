---
title: "Github 블로그 Tips (4) - 로컬 서버 Jekyll 블로그 업로드"
categories:
  - Github Blog
tags:
  - HowToStart
  - Jekyll
  - Ruby
  - 루비
  - 지킬
toc: true
toc_sticky: true
---


## 4. 로컬 서버 Jekyll 블로그 업로드 관련 이슈 정리

깃허브 블로그를 운영하고는 있지만 `git` 도 잘 쓸 줄 모르고, `Jekyll` 이라던가 블로그가 어떻게 생성되는지도 모른다 (~~이 부분까지는 시간 투자하기가 싫어서 ⌚~~). 최근에 블로그에 다국어 번역 플러그인 (`e.g. polyglot`)을 추가해보거나, 블로그 테마를 바꿔보려고 이것저것 시도 했는데, 결국은 그냥 원래쓰던 포맷 유지해서 쓰기로 결정..

일단, 이것저것 시도하는 과정에서 **로컬 서버에 Jekyll 블로그** 올리는 거라도 할 수 있게 되서 이거라도 대충 정리해 놓으려 한다. 

### 4.1 참고한 포스트

시작하기에 앞서 주로 참고했던 블로그는 다음과 같고,

* [깃허브 블로그 생성하기](https://iingang.github.io/posts/windows-github-set/#%EB%A1%9C%EC%BB%AC%EC%97%90%EC%84%9C-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0)
* [깃허브 블로그 테마 변경](https://veggietech.tistory.com/32)

로컬 서버를 활성화하는 과정이 굳이 필요하냐고 하면? 대답은 **`No`**

깃허브 블로그를 시작하고 싶은데 아래의 과정이 잘 안 된다면 일단은 이거 하지말고, `본인아이디.github.io` 블로그를 관리하는 것부터 시작해도 문제 없다. 나도 최근까지는 로컬 서버를 따로 사용하지 않고 있었음. 그런데도 굳이 이걸 하는 이유라고 하면, 정식으로 포스팅하기 전 실제 내 블로그에 반영될 내용을 확인하기가 상당히 수월하다.

```js
git add *
git commit -m "..."
git push
```

로컬 서버를 사용할 수 없으면 위와 같은 예시 명령어를 입력해서 실제 내 블로그를 일일이 업데이트 해가면서 포스팅할 결과물을 확인해야하는데, 한 번 업데이트 할 때마다 `1~2` 분 정도 기다려야 한다. 로컬 서버 이용하는 경우에는 이 과정이 거의 실시간으로 진행되서, 내가 쓴 글이 실제로 어떻게 블로그에 표현되는지 바로 바로 확인할 수 있다.

>거의 1년동안 매번 `git push` 를 해가면서 업데이트 내용을 확인했던 나로서는 로컬 서버 사용하고 나니깐 정말 포스트 작성하기가 개🐶편하다는 걸 새삼 느끼고 있음.


---
### 4.2 Ruby Installer 설치

* Ruby Installer 다운로드 링크: **[https://rubyinstaller.org/downloads/](https://rubyinstaller.org/downloads/)**
* 설치 후, Ruby Command 창 실행

설치가 되었다면, 시작 메뉴에서 Ruby 검색했을 때 명령어창 모양의 Command Prompt 프로그램이 확인될 것 (`Start Command Prompt with Ruby`). 해당 프로그램을 실행해서 다음 명령어를 이용해 필요한 기본 모듈(?) 들을 설치하자.

```js
gem install jekyll
gem install minima
gem install bundler
gem install jekyll-feed
gem install tzinfo-data
```

위의 명령어 입력 후 아래의 버전 확인 명령어가 제대로 동작한다면 제대로 설치된 것.

```js
jekyll -v
```

---
### 4.3 Jekyll 테마 다운로드 및 깃허브 연동

위에 적어둔 **[깃허브 블로그 생성하기](https://iingang.github.io/posts/windows-github-set/#%EB%A1%9C%EC%BB%AC%EC%97%90%EC%84%9C-%EB%B8%94%EB%A1%9C%EA%B7%B8-%EC%8B%A4%ED%96%89%ED%95%98%EA%B8%B0)** 링크를 참조하거나, `깃허브 블로그 시작하기` 같은 키워드로 구글링을 해서 본인이 업로드할 깃허브 블로그를 준비할 것.

>예를 들어 `본인아이디.github.io` 링크에 블로그를 업로드할 수 있도록, 본인의 깃허브 계정의 저장소 (repository)를 하나 만들고 해당 저장소와 연동이 되어있어야 함.

로컬 저장소가 준비되었다면, `git bash` 를 실행시켜서 로컬 저장소 폴더로 이동 후 다음의 명령어를 실행 (사실 이 부분은 `Ruby Installer` 에서 해야하는 건지 `git bash` 에서 해야하는 건지 잘 모르겠는데, 본인은 걍 git 에서 진행했음.).

```js
gem install tzinfo
gem install tzinfo-data
bundle install
bundle update
bundle install
```

설치가 완료되면 아래의 명령어를 이용해 로컬 서버를 활성화 시킬 수 있고, **`http://127.0.0.1:4000/`** 를 인터넷 도메인창에 입력하면 본인의 로컬 서버를 확인할 수 있을 것이다.

* `bundle exec jekyll s`

혹은

* `bundle exec jekyll serve` 

---

### 4.4 `bundle exec jekyll s` 명령어 오류 해결

아마도 위의 로컬 서버 활성 명령어를 입력하면 곧바로 활성화가 되지 않고 에러 메시지가 뜰 것이다. 앞서 언급한 **깃허브 블로그 생성하기** 또는 참고한 포스트에 명시한 **[깃허브 블로그 테마 변경](https://veggietech.tistory.com/32)** 글을 보면 해당 오류를 해결하는 몇 가지 방법이 나온다.

* `Timezone data error`

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ruby-fig1.png" alt="">
</figure>

위의 그림에서 볼 수 있는 것처럼 블로그 로컬 저장소 폴더를 보면 `Gemfile` 이라는 파일이 하나 있을텐데, 일단 `timezone data` 관련 오류 해결을 위해 `Gemfile` 에 아래의 명령어를 추가하는 것부터 진행했음. 해당 파일을 수정할 때, 어떤 포스트에서는 `gemspec` 이란 문구를 지우라고 이야기하기도 하는데, 이건 테마에 따라 차이가 있는 것 같다.

```js
gem "tzinfo"
gem "tzinfo-data"
```

<figure style="width: 100%" class="align-center">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/blog-ruby-fig2.png" alt="">
</figure>

>이 부분에 대해서는 사실 따로 공부한게 아니라 잘 모르는데, `.gemspec` 형식의 파일을 보면 (본인의 경우는 현재 `minimal-mistakes` 테마를 쓰고 있음), 뭔가 해당 테마에서 사용되는 플러그인이라든지 관련 모듈 정보가 들어있는 느낌인데, `Gemfile` 에서 이 부분을 지워버리면 다른 오류가 발생함.

* `webrick error`

`bundle` 명령을 다시 실행해보니, `... cannot load such file -- webrick (LoadError)` 이런 문구와 함께 오류가 발생했고, 이는 아래의 명령어를 입력하는 것으로 해결할 수 있었음.

```js
bundle add webrick
```