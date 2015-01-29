# OS X (10.9.2 mavericks) 에서의 아파치 톰캣 연동

원래 보통 작은 규모의 사이트들이나, 엔지니어 혹은 개발자들이 연습삼아 apache 를 앞단에 두고(80포트) 자바 웹 서비스는 WAS (여기서는 톰캣)로 보내는 형태를 만들어 이용했다. 

>여기의 문서는 보통 다른 문서의 목표와 다르다. 이 문서의 목표는 아파치로 오는 요청을 죄다 톰캣으로 보내게 설정할 것이다. 보통은 그렇게 하지 않는다. 그러므로 원하는 내용에 맞지 않을 수 있으니 그에 맞는 메일을 찾거나 이 내용을 보고 학습하여 수정하기 바란다. 레퍼런스는 인터넷에 널렸다. :) gook luck~

## 준비물
- 아파치 (이미 설치되어 있다)
- 톰캣 (homebrew 로 설치할 것이다. 본인의 경우 `/usr/local/Cellar/tomcat/7.0.53` 에 설치됨)
- mod_jk 아파치에 설치될 모듈로 톰캣과 이어주는 역할을 한다. 이에 대한 추가설명은 따로 하겠다.


## 톰캣 설치 
톰캣의 경우는 맥의 패키지 관리툴인 [Homebrew](http://brew.sh) 를 이용해서 설치했다.
Homebrew 의 사용법은 홈페이지에서 확인바란다. [Juntai Park](http://rkjun.wordpress.com/) 님이 한글화에 참여하여 홈페이지를 무려 한글로!!! 볼 수 있으니 겁먹지 마시라. 

homebrew 가 이미 설치되어있다면 간단히

``` basg
brew install tomcat 
```

로 설치하면 된다.

그렇게 해서 설치되는 버전은 7버전대이며, 패스가 잡히는 곳에 sh 이 존재하므로 `catalina` 해보면 설명이 나오는걸 볼 수 있다.

brew 로 설치하는 이유는, 리눅스등을 써보신분은 알겠지만 패키지 관리자를 이용하면 업데이트나 설치/제거등이 용이하다. 패키지 관리자 없이 설치한 패키지가 업데이트될때마다 하나하나 지우고 깔거나 업데이트,패치 등을 찾아서 적용하는것은 상당히 번거러운 작업이다. 

과거에는 macport 등을 썼었지만 현재는 homebrew 로 대동단결이라고 봐도 된다. 

## mod_jk.so
아파치와 톰캣을 이어주는 방법은 한가지가 아니다. mod_jk 를 이용한 방법과 proxy 를 이용한 방법등이 있다. 여기서는 mod_jk 를 이용할 것이다.

아파치는 여러 모듈을 추가하여 기능을 확장할 수 있다. 그 확장모듈중 하나가 mod_jk 인데 파일로는 mod_jk.so 파일이다. 문제는 해당 so파일 ... 즉 dynamic lib 파일은 컴파일된 바이너리 파일로 보통 제공된다. 그 대상들은 널리 쓰이는 윈도나 리눅스등이다. 즉 맥버전으로 컴파일되어 생성된 바이너리를 mod_jk 를 제공하는 아파치사이트에서는 찾아볼 수 없다. 소스만 있을뿐이다. 

해서 맥유저는 해당 소스를 받아와서 직접 컴파일하여 so 파일을 생성해야한다. 

해당 글은 별도로 작성해놨으니 참고 바란다. [링크](https://github.com/yakmoz/ref/tree/master/mod_jk)

그 글에 따라 직접 컴파일 해도 되고, 아니면 컴파일 해놓은(매버릭스 버전) so 파일을 그냥 다운로드 해서 써도 된다. 

# 아파치의 구조
아파치는 나름 복잡하다. 상용에서도 많이 쓰이는 웹서버이다. 사실상 입구를 담당하고 그뒤로 WAS 에게 포워딩 하는 구조로 많이 쓰인다.

아파치의 설정은 httpd.conf 에 다 담을수도 있지만 설정을 파일로 할 수도 있다. 
우리는 앞으로 `JkWorkersFile` 파일과 `JkMountFile` 파일에 설정을 쓰게 하고 그걸 일게 아파치설정을 할 것이다.

해당 파일들에 대한 설명은 [아파치 톰캣 커넥터](http://tomcat.apache.org/connectors-doc/reference/apache.html) 글을 읽으면 알 수 있다. 

우선 살펴보자면

|Directive | 설명 |
---|---
JkWorkersFile| 톰캣 서블릿 컨테이너를 위한 worker 파일 이름이다. 설정의 글로벌 파트에 해당하며 선언은 오직 한번만 허용된다. 만약 `JkWorkerProperty` 설정을 이용하지 않으면, 올바른 `JkWorkersFile` 파일을 정의해야한다. 기본값은 없다.
JkMountFile| 여러개의 맵핑정보를 worker 에게 매핑해주는 정보를 담은 파일 . 보통 uriworkermap.properties 파일로 불리운다. 상속에 대한 룰은 JkMountCopy 를 살펴보라. 기본값은 없다. 

이런 설정을 이용할 것이다. 
 

## 설정을 위한 기본위치

### Apache
- 설정 : `/etc/apache2/이하`
- 모듈(.so) : `/usr/libexec/apache2 이하 `


## 설정
- 우선 httpd.conf 파일을 열어서 LoadModule 들이 설정되어 있는 라인에가서 

```
LoadModule jk_module libexec/apache2/mod_jk.so
```
내용을 추가한다. 물론 해당 .so 파일이 설정에서 가리키는 위치에 있어야 한다.

- jk_module 설정을 외부로 빼도 되고 그냥 `httpd.conf` 에 넣어도 된다. 

```
<IfModule jk_module>
    JkWorkersFile /etc/apache2/workers.properties
    JkLogFile     /var/mod_jk.log
    JkLogLevel    info
    JkMountFile   /etc/apache2/uriworkermap.properties
</IfModule>
```

- 해당 설정을 보면 앞에서 언급한 JkWorkersFile 과 JkMountFile ,2가지 설정을 이용하는걸 알 수 있다. 그설정들에 정의된 파일, 2개를 수정할것이다. 아마 2개의 파일은 이미 존재하고 있을것이다. 없으면 만들라. 별의미는 없다. 그 파일 내부에 설정된 내용을 쓰겠다는 의미일 뿐이다.

우선 `workers.properties` 를 수정하다.worker1 이라고 선언하고 내용을 설정한다.

```
worker.list=worker1
worker.worker1.port=8009
worker.worker1.host=localhost
worker.worker1.type=ajp13
worker.worker1.lbfactor=1
```
    

- 다음 `uriworkermap.properties` 를 수정하자.
```
/*=worker1
/*.jsp=worker1
```
사실 jsp 만 톰캣에 가도 되는데 그냥 /* 로 다 넘기고자 했다. woker1 으로 맵핑해둔다.


## 서버재시작
톰캣을 기동하고, 아파치를 기동/재기동 할 것이다.
아파치의 경우는 아마 sudo 를 앞에 붙여야 제대로 될 것이다. (ex. `sudo apachectl start`)

- 톰캣 기동

``` bash
catalina start
```
만약 그런파일이 없거나.. 등 패스에 걸려있지 않다고 보이면 직접 가서 실행하면 된다. 보통은 `/usr/local/bin` 에 링크로 존재한다.

다음은 아파치을 기동 혹은 재기동하자.

- 아파치 기동

``` bash
apachectl stop
apachectl start
```

실행되고 있지 않았다면 stop 시에 에러가 날 것이다. 무시하고 start 하자.


이렇게 하고 우선 톰캣이 제대로 떴는지 보기위해 `http://localhost:8080` 으로 접근해보자. 톰캣페이지가 잘 보인다면 톰캣은 잘 뜬것이다. (8080 포트를 변경했다면 바꾼 포트로 접근바란다)

다음 `http://localhost` 로 접근해보자. 만약 it worked 던가가 나오면 다시 `http://localhost/a.jsp` 도 한번 해보기 바란다. 파일이 없을테니 404 가 보일텐데 잘보면 아파치 톰캣의 404 란걸 알 수 있다.

그럼 우선 jsp 파일을 제대로 톰캣에 보낸건 맞다. 그냥 / 도 톰캣에 보내게 해놨기에 localhost 로 접근해도 톰캣이 나와야 한다. 

본인의 경우는 잘 됐는데 잘 안된다면 하나하나 확인하거나 다른 가이드 문서나 아파치 설정 문서를 잘 보기 바란다. 
