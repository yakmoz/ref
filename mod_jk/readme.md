##os x 를 위한 mod_jk 컴파일 .so 입니다.

이문서는 10.9 당시의 기준으로 만든것이나 10.10 에서도 아래의 os name 10.9 부분을 제외하고는 동일합니다.
그부분만 10.10 으로 바꿔서 글을 수정합니다.

그리고 ... mod_jk.so 파일을 첨부는 했지만 그냥 컴파일해서 install 을 통한 설치가 아니면... 안되는걸 수 있으니 가능하면 직접 컴파일해야한다! 직접설치를 하지 않으면 mod_jk.so 파일에 @가 붙어버린다. 해서 mod_jk.so 파일을 못찾더라 orz 

- 환경 : 레티나 15 , os x 10.9.2 --> 10.10   입니다. 


## mod_jk 컴파일 (os x Mavericks‎)


apache 의 module 디렉토리에 mod_jk 존재 확인해봐라
`/usr/libexec/apache2` 에서 찾아봐라. 없을것이다.

mod_jk 컴파일을 위해 

1. [http://tomcat.apache.org/download-connectors.cgi](http://tomcat.apache.org/download-connectors.cgi) 에 가서 mod_jk 소스를 다운로드 받는다. 다운로드 받아서 푼 다운 그 디렉토리의 native 로 들어간다. 

2. configure

    ``` bash
    ./configure --with-apxs=/usr/sbin/apx
    ```
    
    64비트이니 그냥 아래로 하길 바란다. 
    ``` bash
    ./configure CFLAGS='-arch x86_64' APXSLDFLAGS='-arch x86_64' --with-apxs=/usr/sbin/apxs
    ```

    하면 컴파일 실패가 나는데
    checking for C compiler default output file name... configure: error: C compiler cannot create executables

3. 본인은 이런 에러가 나왔다. 바로 윗줄을 보면 
/Applications/Xcode.app/Contents/Developer/Toolchains/OSX10.10.xctoolchain/usr/bin/cc  (10.9 버전은 OSX10.9)
호출중에 에러가 난것으로 해당 위치자체가 존재하지 않는다. 즉 컴파일러를 못찾아서 에러난것이다.

4. 위의 디렉토리를 보면 /Applications/Xcode.app/Contents/Developer/Toolchains/ 까지는 존재한다. 그 안에 내용을 보면 
XcodeDefault.xctoolchain 디렉토리가 있다. 즉 mod_jk 를 위한 컴파일러의 위치가 OSX10.10.xctoolchain/ 가 아니라 XcodeDefault.xctoolchain 인 것이다. 해서 소프트링크를 만들자. 

5. /Applications/Xcode.app/Contents/Developer/Toolchains 로 이동해서

    ``` bash
    sudo ln -s ./XcodeDefault.xctoolchain/ ./OSX10.10.xctoolchain
    또는
    sudo ln -s ./XcodeDefault.xctoolchain/ ./OSX10.11.xctoolchain
    ```

6. 다시 configure 해보면 성공할 것이다.
    `make` 실행
    ~~sudo make install` 실행 (sudo 의 이유는 /usr/libexec/apache2/ 에 설치되기 때문임 )~~
    Mac Os 특정버전부터 더이상 /usr/libexec/apache2 이하에 파일을 복사할수 없게 해놨다. 보안의 강화이긴한데 이게 sudo 도 안먹힌다.

    사실 이미 거기 말고도 다른곳에 놔두고 참조하게 할 수 있는 방법이 있다. 본체를 건드리지 못하게 해놨다고 생각하면 편하다. 
    make install 대신 컴파일한곳 (native) 이하 apache-2.0 디렉토리가 있다. 들어가면 mod_jk.so 파일이 있다. 이 파일을 /etc/apache2/other 에 복사해두면된다.



!!! 만약 설치시 
`could not detect a 32-bit integer type` 나 stdio.h 가 없다며 
에러가 나오면 
`xcode-select --install` 를 통한 추가 설치가 필요할 수 있다. 

!!! 만약 설치시 `apr_lib.h` 파일을 찾지못한다고 나오면?
해당 헤더파일의 위치를 직접 configure 할때 선언해야한다. find 로 찾아도 되는데 보통 /usr/include/apr-1 에 있을것이다. 아니면 찾아야한다. 

``` bash
./configure CFLAGS='-arch x86_64' APXSLDFLAGS='-arch x86_64' --with-apxs=/usr/sbin/apxs LDFLAGS='-L/usr/include/apr-1' CFLAGS='-I/usr/include/apr-1'
``` 

이후는 make 로 컴파일 하여 파일생성하면됨.(문제가 있다면 make clean 하고 make 바란다) 


