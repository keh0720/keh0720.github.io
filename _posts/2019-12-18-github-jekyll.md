---
layout: post
title:  "Github + Jekyll"
date:   2019-12-18 16:36:23 +0900
categories: [jekyll]
---

Github에서repository 생성 시 xxx.github.io로 생성하면 HTML페이지를 만들 수 있다. 
이 기능과 Jekyll을 이용하면 간단하게 블로그를 만들 수 있다.

# Jekyll 설치
---

> official site : [https://jekyllrb.com/](https://jekyllrb.com/)

jekyll을 사용하기 위해서는 ruby가 필요하다.
아래는 MAC에서 설치하는 스크립트이고 다른 환경은 공식 홈페이지를 참조하면 된다.

{% highlight bash %}
$ xcode-select --install

$ brew install ruby

$ ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580)

# Or use rbenv
$ brew install rbenv

$ rbenv init

$ curl -fsSL https://github.com/rbenv/rbenv-installer/raw/master/bin/rbenv-doctor | bash

$ rbenv install 2.6.3

$ rbenv global 2.6.3

$ ruby -v
ruby 2.6.3p62 (2019-04-16 revision 67580)

# install Jekyll
$ gem install --user-install bundler jekyll

$ export PATH=$HOME/.gem/ruby/2.6.0/bin:$PATH
{% endhighlight %}

설치가 완료되면커맨드 명령만으로 웹사이트를 생성 할 수 있게 된다.

{% highlight bash %}
$ jekyll new test-site

$ cd test-site && bundle exec jekyll serve
Configuration file: /Users/uihyeok/dev/github/test-site/_config.yml
            Source: /Users/uihyeok/dev/github/test-site
       Destination: /Users/uihyeok/dev/github/test-site/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
       Jekyll Feed: Generating feed for posts
                    done in 0.32 seconds.
 Auto-regeneration: enabled for '/Users/uihyeok/dev/github/test-site'
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
{% endhighlight %}

default로 4000포트로 실행되어 http://localhost:4000으로 접속하면 아래와 같이 확인 할 수 있다.

![jekyll-test-site](/assets/images/jekyll-test-site.png)

# Theme 선택
---

이제 마음에 드는 테마를 골라보자. 
공식홈페이지에서 링크되어 있는 사이트가 있다.
여기에서 테마를 선택하면 대부분 github를 통해 제공된다. 나의 github로 fork하거나 다운로드 받은 후 수정해서 사용하면 된다.

* [jamstackthemes.dev](jamstackthemes.dev)
* [jekyllthemes.org](jekyllthemes.org)
* [jekyllthemes.io](jekyllthemes.io)

# Github repository 생성
---

아래와 같이 원하는이름.github.io로 repository를 생성한다.
fork 했으면 repository 이름을 변경하면 된다.

![create github repository](/assets/images/create-github-repo.png)

이 repository로 수정한 내용을 push하면 https://id.github.io로 publish된다. 
반영되는데 딜레이가 조금 있다.


