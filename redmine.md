

# Redmine 설치하기

세상 참 좋아졌다. 이런툴이 공짜라니.. 근데 사실 라이센스 정책에 대해서 자세히 보지 않아 정말 공짜인지 아닌지는 모르겠다. 단, 검색했을때 무료 라고 본걸로 기억한다.

한마디로 업무관리툴이다. 개인이 아닌 팀단위등의 업무를 관리할때 유용하게 쓸 수 있다.

지라를 써봤다면 뭐 대충 감이 오겠지만.. 아무튼 이 툴을 지금부터 맥에 설치하는걸 같이 해보자.

사이트에 자세한 설명이 있어서 따로 뭐 할게 있나 싶지만 막상해보니, 도움이 될만한 정보가 있어서 이렇게 글을 쓴다. 

## 다운로드

당연하겠지만, 우선 설치하기위해 다운로드 하자. [홈페이지](http://www.redmine.org) 에 가서 Download 를 클릭하면 최신 stable 버전이 보일것이다. 어차피 윈도에 설치할것도 아니니 tar.gz 을 받으면된다.

현재 2014년 7월 16일 버전으로는 2.5.2 버전이 보인다. 

다운로드 하고 나서 잘보면 위쪽에 

> Guide >> [Installation Guide](http://www.redmine.org/projects/redmine/wiki/Installation_Guide)

가 보일것이다. 함께 보자. 어차피 해당 가이드대로 하고 중간중간 도움을 주는게 목적이다.

다운로드 했으면 압축을 풀자.

``` bash
$ tar xvzf 파일명 
```

그리고 해당 redmine 디렉토리로 이동하자.


## DB 설치

우선 본인은 설치환경은

OS | OS X 10.9.4 
---|---
DB | mariadb 10.0.10

나머지는 os x 기본버전이다. 사실 ruby 를 homebrew 로 새로 설치하긴 했지만 그 과정은 필요없어보인다. 만약 진행중에 제대로 되지 않는다면 homebrew 를 통해서 ruby 를 설치해보도록 한다.(os x 의 ruby 이기 때문에 homebrew 의 입장에서는 ruby 는 그냥 새로운 설치이다)

설치 가이드에는 mysql 에 대해서 언급하고 있지만 대부분 그러하듯 mariadb 에서도 잘 된다. postgreSQL 에서도 잘 되겠지만 여기서는 mysql 류의 제품을 이용할 것이다.

homebrew 의 설치가이드는 생략한다. 검색하면 정말 너무 너무 쉽게 나오므로 그정도의 수고는 해줄거라 믿는다.

``` bash
$ brew install mariadb 
```

설치가 되고 나면 가이드에서 나온대로 해보자

우선 mariadb 에 접속하자. 
``` bash
$ mysql -uroot
```

``` sql
CREATE DATABASE redmine CHARACTER SET utf8;
CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'my_password';
GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
```

당연한 얘기지만 제대로 할때는 위의 패스워드등은 알아서 변경하도록 한다.

그리고 DB 를 시작하자.

``` bash 
$ mysql.server start
```


## DB 접속설정

현재 우리는 Redmine 을 풀어놓은 디렉토리에 있다. 설정디렉토리에 파일을 하나 만들어야한다. 직접이동할 필요는 없다. `config` 디렉토리인데 그 안에 보면 `database.yml.example` 이라는 파일이 있고 이 파일을 `database.yml` 로 copy 해서 복사본 하나 만들자.

``` bash
$ copy config/database.yml.example config/database.yml
```

그리고 파일을 열고  `production` 부분을 수정한다.
메뉴얼을 보면 알겠지만 ruby 버전에 따라 내용이 달라진다. 그전에 이미 샘플을 보니 아답터가 mysql2 로 되어있으므로 고민하지 말고 ruby1.9 라고 되어있는 내용으로 적용하자.


``` 
production:
  adapter: mysql2
  database: redmine
  host: localhost
  username: redmine
  password: my_password
```

## 의존요소 설치

Redmine 은 gems (ruby's) 을 관리하기 위해 Bundler 를 사용한다.

``` bash
$ gem install bundler
```

여기서! 만약 Library 관련 디렉토리에 설치되는건데 권한이 없다고 하면 gem 앞에 sudo 를 붙여서 설치하도록 한다.

가이드 대로 다음단계를 진행해보자.

``` bash
$ bundle install --without development test
```

자 여기서 에러를 보일 것이다. 아니라면 그냥 넘어가면 된다.
본인의 경우는

> Gem::Installer::ExtensionBuildError: ERROR: Failed to build gem native extension.

``` bash
Gem::Installer::ExtensionBuildError: ERROR: Failed to build gem native extension.

/System/Library/Frameworks/Ruby.framework/Versions/2.0/usr/bin/ruby extconf.rb 
checking for Ruby version >= 1.8.5... yes
checking for xcrun... yes
checking for Magick-config... no
Can't install RMagick 2.13.2. Can't find Magick-config in /usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin:/opt/X11/bin
```

이런 내용이 나왔고 Magick-config 를 찾을수 없다고 나온다. brew 를 이용해서 별도 설치하자.

### Magic 관련 설치

이제부터의 내용은 가이드대로 했다가 보인처럼 magic 관련 에러가 난 경우 따라해보면 된다.
이 내용은 [이곳](ㄹㅇㄴㅁㄹㅇㄴㅁ) 을 통해서 찾았다.

우선 magick ,pkgconfig 를  brew 로 설치하자.
그리고 마지막에 MagickWand.h 해더위치를 C_INCLUDE_PATH 를 통해서 알려주고 install 하면된다. 

``` bash
$ brew install imagemagick
==> /usr/local/Cellar/imagemagick/6.8.6-3

$ brew install pkgconfig
==> /usr/local/Cellar/pkg-config/0.28

$ C_INCLUDE_PATH=/usr/local/Cellar/imagemagick/6.8.6-3/include/ImageMagick-6 PKG_CONFIG_PATH=/usr/local/Cellar/imagemagick/6.8.6-3/lib/pkgconfig/ gem install rmagick
Successfully installed rmagick-2.13.2
```

본인은 이렇게 별도 설치를 마치고 다시 가이드대로 설치를 진행했다.

``` bash
$ bundle install --without development test
```

결과는 성공이다.

사실 가이드에 나와있는데 해당 ImageMagick 을 빼고 설치하면 위의 과정을 할 필요는 없다. 

이 다음부터는 가이드대로 그대로 진행하면 아무 문제 없이 설치완료가 된다. 언어를 골라주는 부분이 있긴한데 거기서는 ko 를 고르면 되겠지?

루비는 2.x 버전으로 생각하고 가이드를 따르면된다. 결과는 성공이고...

앞에서 우리가 ruby gem 을 설치할때 한가지 재밌는걸 봤다.

``` bash
bundle install --without development test
Fetching gem metadata from https://rubygems.org/.........
Fetching additional metadata from https://rubygems.org/..
Resolving dependencies...
Using rake 10.1.1
Using i18n 0.6.11
Using multi_json 1.10.1
Using activesupport 3.2.19
Using builder 3.0.0
Using activemodel 3.2.19
Using erubis 2.7.0
Using journey 1.0.4
Using rack 1.4.5
Using rack-cache 1.2
Using rack-test 0.6.2
Using hike 1.2.3
Using tilt 1.4.1
Using sprockets 2.2.2
Using actionpack 3.2.19
Using mime-types 1.25.1
Using polyglot 0.3.5
Using treetop 1.4.15
Using mail 2.5.4
Using actionmailer 3.2.19
Using arel 3.0.3
Using tzinfo 0.3.40
Using activerecord 3.2.19
Using activeresource 3.2.19
Using awesome_nested_set 2.1.6
Using bundler 1.6.3
Using coderay 1.1.0
Using rack-ssl 1.3.4
Using json 1.8.1
Using rdoc 3.12.2
Using thor 0.19.1
Using railties 3.2.19
Using jquery-rails 2.0.3
Using mysql2 0.3.16
Using net-ldap 0.3.1
Using ruby-openid 2.3.0
Using rack-openid 1.4.2
Using rails 3.2.19
Using redcarpet 2.3.0
Using rmagick 2.13.2
Your bundle is complete!
Gems in the groups development and test were not installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

여기서 잘보면 redcarpet 이 보이는가? 이는 Markdown 처리기라고 알고 있다. 즉 Redmine 을 설치하고 

`관리 > 설정 > 일반` 탭에서 `본문형식`을 보면 셀렉트박스로 되어있을 것이고 거기에 보면 `markdown` 이 있다. 

실제로 markdown 으로 해두고 textarea 등에서 markdown 문법을 쓰니 잘 됐다.

혹시 잘 진행되지 않는점이 있으면 알려주시면 글을 추가하도록 하겠다. +_+/

