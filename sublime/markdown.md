# sublime Text 에서 markdown 설정하자
이것저것 다 써봤다. 유료 marked나 2 는 안써봤지만 (markdown pro 는 빼고 가겠다)

결론은 하나! sublime Text 가 만능인 이유가 여기서도 드러났다. markdown life 를 주로 쓰다가 하루패드를 좀 써보다가 결국은 sublimeText 에 3가지 플러그인을 까는걸로 우선은 정착한다. 하루패드가 나빠서 그런건 아니다. 괜찮게 보이길래 나름 약소하나마 기부도 했다. 다만 본인에게는 몇가지 문제점이 있어서 다른걸 찾다가 결국 다시 sublime 으로 왔다. json 도 그랬듯이...

**[여기](http://dataleaf.tistory.com/26)** 를 참고했다.

그리고 아래는 os x 에서 따라하는 내용이라 윈도에서는 cmd 대신 control 등으로 키를 달리 눌러야 할 것이다.

* * * 


1. 우선 누구나 알다시피 sublime Text 에 기능확장을 위해서 Package Control 을 설치해야한다. 
방법은 간단하다. `shift+cmd+p` 를 눌러서 `Package Control: install Package` 를 선택하면 된다. 그러면 서브라임의 아래 상태창에 뭔가 진행되는게 보일것이다. 그리고 여담으로 위의 명령 타이핑을 다 칠 필요도 없다는걸 알게 될것이다.

2. 다시 `cmd+shift+p` 를 눌러 `Package Control:install Package` 를 선택한다. 그러면 뭔가 상태창아래에서 진행바가 보일것이고 곧 입력받을 수 있는 작은창을 보게 된다. 거기서 플러그인 4가지를 찾아서 설치할 것이다. 사실 저중에 그냥 Markdown Preview 만 깔아도 미리보기를 통해 작업은 가능하다. 하지만 나머지도 다 설치해주면 더 나은 환경 구성을 할 수 있다.
    - MarkdownEditing : .md(마크다운확장자) 파일을 편집하기 위한 플러그인
    - SmartMardDown : 문단 folding 관련 플러그인
    - MarkDown Preview : 웹으로 미리보기 위한 플러그인. 
    - MarkDown TOC : 설치하고 나서 tools > markdown TOC > insert TOC 하면된다.
3. 이제 설정을 해보자. 우선 마크다운 자체의 하이라이팅을 위해 설정을 하자.
    Preferences > Package Settings > Markdown Editing > Markdown (Standard)Settings 를 선택하고 아래의 코드를 넣는다. 그럼 에디팅 화면이 바뀌는걸 볼 수 있다.  
    
    ``` json
    {"color_scheme": "Packages/Color Scheme - Default/Dawn.tmTheme"}
    ```

    테마는 다른 테마도 있으니 검색해보면 테마이름을 확인해볼 수 있다. 필요에 따라서  Markdown GFM Settings -User 에 뭔가 값을 설정할 필요가 있다면 설정하면된다.

4. 단축키로 결과물을 브라우저로 확인하는 방법이 있다. Preview 를 설치하면 설명서가 나오는데 그곳에 보면 키바인딩에 아래와 같이 추가 설정하라고 되어있다. ',' 를 필요에 따라 넣도록 한다. 그리고 키 바인딩은 각자의 취향대로 하면된다. 아래는 alt+m 으로 되어있다.
    ``` json
    { "keys": ["alt+m"], "command": "markdown_preview", "args": {"target": "browser", "parser":"markdown"} }
    ```

이정도면 기본설정은 마쳤다. 뭔가 제대로 표시되지 않는것 같다면 sublime 을 재시작 해보자. 그런데 뭔가 횡한 preview 라는 느낌이 들지 않는가? github 마크업으로 작성했다면 github preview 로 preview 를 할 필요가 있다. 

`cmd+shift+p` 를 눌러서 mgp 라고 치면 `Markdown Preview:Github Flavored Markdown:Preview in Browser` 가 나오는걸 볼 수 있다. 해당 뷰로 보면 fenced code block 도 잘 적용되어서 보여진다. 

* * * 
!!!혹시 패키지 컨트롤 창을 띄워서 나오는 명령에 바로 단축키 맵핑하시는법 아시는분 있의면 댓글로 알려주시면 감사하겠습니다.

