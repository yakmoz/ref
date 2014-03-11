# 스프링 정리노트
아.. 젠장 계속 잊어먹어서 안되겠다.

하나로 정리한다.

## 스프링 MVC 의 기초적인 흐름 
정확히는 스프링 MVC 의 역할이고 그 시작점에 대해서 얘기해보자. 


원래 java 는 웹서비스를 위한 서블릿을 제공하고 있다. 서블릿을 선언하고 이름을 정하고 어떤 클래스에의해 처리될지를 명시한뒤에 그 서블릿이 어떤 url에 맵핑될지 선언하는 형태이다.

아래는 자바 서블릿 예제이다. 아래의 HelloServlet 은 Servlet 을 extends 한 클래스이다.
	
	<servlet>
		<servlet-name>hello</servlet-name>
		<servlet-class>whiteship.HelloServlet</servlet-class>
	</servlet>
	
	<servlet-mapping>
		<servlet-name>hello</servlet-name>
		<url-pattern>/hello</url-pattern>
	</servlet-mapping>

위와같은 선언의 경우 경로를 일일이 선언해야 하는 케이스가 발생할 수 있다. 
스프링을이용하게 되면 스프링의 dispatcherservlet과 어노테이션에 의한 auto-scan 을 이용하게 된다.

우선 스프링 MVC 호출에 따른 처리과정은 다음과 같다.
간단하게 정리하자면.. 이 모든것을 총괄하는 커맨드센터가 <u>**DispatcherServlet**</u> 이다.

	클라이언트 ---- 요청 ----> DispatcherServlet 
	
	DispatcherServlet ---- 처리할 컨트롤러 파악 ----> HandlerMapping
	DispatcherServlet <---- 처리할 컨트롤러 파악 ---- HandlerMapping
	
	DispatcherServlet ---- 처리요청 ----> Controller 
	DispatcherServlet <---- ModelAndView 리턴 ---- Controller
	DispatcherServlet ---- ModelAndView 결과를 보여줄 View 파악 ----> ViewResolver
	DispatcherServlet <---- View 리턴 ---- ViewResolver
	DispatcherServlet ---- 결과물 출력요청 ----> View


아래가 예제이다.

	<servlet>
		<servlet-name>spring</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
	</servlet>
	
	<servlet-mapping>
		<servlet-name>spring</servlet-name>
		<url-pattern>/app/*</url-pattern>
	</servlet-mapping>

1. 스프링의 DispatcherServlet 을 등록하고 /app 이하의 모든 처리를 한다. 즉 url 에서는 [DOMAIN ROOT]/app/.... 으로 오면 / 이하로 app 으로 시작한것으로 dispatcherservlet 의 처리대상이 된다. 
2. 해당 dispacher 동작을 위해 이름-servlet.xml 파일이 필요하다.
3. WEB-INF 디렉토리 선택후 우클릭 New --> Spring Bean Configuration File 을 클릭한다.
4. spring 이라고 서블릿을 만들었으니 spring-servlet.xml 을 만든다.
5. xml 창에서 아래의 namespace 탭 클릭 --> context 네임스페이스 를 선택하고(ex. beans , context) 저장 
6. 다시 xml 화면에 와서 beans /beans 사이에 아래를 추가한다. 아시다시피 컴포넌트 스캔이다.
   <context:component-scan base-package="whiteship"/>
   
   기본페키지 이하의 어노테이션으로 달린 빈들을 검색하게 된다. 
   
   이렇게 만들고 나면 이제 별도의 서블릿 맵핑없이 스프링을 이용해 처리하게 된다. 위의 component-scan 의 대상이 되도록 어노테이션만 달면된다.(Controller, Service, Component, Repository 등)
   
7. 이제 서블릿대신 어노테이션을 단 `HelloController`컨트롤러를 만든다.

		@Controller
		public class HelloController {
		
			@RequestMapping( "/hello")
			public String hello(@RequestParam String name, Model mode) {
				model.addAttribute( "hello", "hello" + name);
				return "/WEB-INF/views/hello.jsp" ;
			}
   		}
		
	한가지 주의할것은 xml 설정에 /app 이하의 처리를 하게 되다. HelloController 에서 메소드에 `/hello` 되어있는데 스프링이 받아들이기는 
	
	> /app/* 의 호출이 왔으니 해당 서블릿 설정으로 읽어드린 bean 중에 * 에 대한 requestMapping 을 찾자
	
	가 된다. 즉 호출시 /app/hello 라고 하면 /app 이하부분인 hello 에 대한 requestMapping 을 찾는다. 즉 xml 설정의 서블릿 맵핑 설정은 /app/* 중 `/app` 자체가 ROOT 가 되는것이다. 해서 `HelloController` 클래스에 `RequestMapping("/app")` 을 달지 않는것이다. 이것 달았다가는 url 호출을 `/app/app/hello` 해야 한다.
   
Controller 는 처리의 시작점이라고 생각해도 된다. dispatcher 는 이번 요청을 처리할 컨트롤러를 핸들러 맵핑을 통해서 알게 되면 해당 컨트롤러에게 일을 넘긴다.

그리고 가능하면 비지니스빈 과 웹관련빈은 별도의 관리를 하도록 작성해야 한다.(component-scan 시에도 서로를 배제한 스캔을 해도록)

## 빈 분리 
바로 위에서 말한 비지니스 빈과 웹관련 빈의 구분인데 web.xml 에 2가지 xml 설정을 기술한다고 치자.

### 1. 뒷단의 비지니스 부분 (CLL)

스프링으로 만든 WAS(Web Application Server) 가 기동될때 당연히 읽어뎔여지는 설정들이 있다. ContextLoaderListener 는 서블릿의 ServletContextListener 를 확장한것으로 웹어플리켕션이 서블릿 컨테이너에 로딩될대 실행되는 리스너이다.

아래가 그 설정이다.

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext*.xml</param-value>
	</context-param>

내용을 보면 스프링 설정파일을 지정했다. ContextLoadListener 가 하는 일은 웹 어플리케이션이 로딩될 때 WAC 를 만드는것이다. 이렇게 생성된 WAC 는 contextConfigLocation에 설정한 xml 빈 설정파일을 사용하여 WA에서 사용할 객체를 관리해준다. 위의 설정파일은 `src/applicationContext*.xml` 이 된다.


### 2. 앞단의 컨트롤러 부분? (DS) 
CLL 가 아닌 DS 를 살펴보자면..

	<servlet>
		<servlet-name>spring30</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/webmvc-config.xml</param-value>
		</init-param>
	</servlet>   

	<servlet-mapping>
		<servlet-name>spring30</servlet-name>
		<url-pattern>/app/*</url-pattern>
	</servlet-mapping>

DispatcherServlet 을 설정하고 url 맵핑했다. DispatcherServlet 은 Servlet 을 확장한 클래스이다(url 맵핑기능. 일종의 대문역할).
앞에서 봤듯이 /app 으로 오는 모든 처리를 해당 spring30 서블릿이 처리한다. 그 처리할 빈은 어디에있느냐? webmvc-config.xml 로 지정해놓은걸 볼 수 있다. 

1,2 는 WAC 를 만드는 방법이 다르다고 한다. DS 의 경우는..

1. CLL 에서 만든 WAC 가 있다면 그것을 상속받는 WAC 를 만든다. 
2. CLL 은 어플리케이션당 WAC 한개, DS 는 서블릿 설정당 WAC 한개씩 만든다. 

즉 CLL 은 전체에서 사용할 WAC 를 만들고 DS 는 해당 서블릿에서만 사용할 WAC 를 만든다.

이런식으로 상속구조를 가진다.

중요한 특징

- 자식 WAC 에서 부모 WAC 의 빈을 참조할 수 있다.
- 부모 WAC 에서는 자식 WAC 의 빈을 참조할 수 없다.
- 자식 WAC 에서 어떤 빈을 참조시 우선 자신 내부를 참조한다. 없으면 부모쪽 빈을 참조하는데 그래도 없으면 예외가 발생한다.

보통은 나누는 방법이

- WAC(CLL) : 웹에 종속적이지 않은 빈 (비지니스 관련..)
	Service,Repository, Component ... 
- WAC(DS) : 웹에 종속적인 빈(컨트롤러,MVC 관련 빈)
	Controller 


### CLL 과 DS 의 설정
CLL은 비지니스 관련 빈들을 관리하게 될 것이다. applicationContext.xml 파일의 내용을 보면...

	<context:property-placeholder location="classpath*:*.properties" />

	<context:component-scan base-package="sample">
		<context:exclude-filter expression="org.springframework.stereotype.Controller"
			type="annotation" />
	</context:component-scan>

component autoscan 의 내용중 exclude 로 된것을 볼 수 있다. MVC 의 @Controller 를 배제한것이다.
해당 어노테이션은 빼고 빈등록(Component, Service, Repository)을 하게 될 것이다.





## ApplicationContext
Spring 에서는 IoC 컨테이너 혹은 Spring 컨테이너 라고도 부른다? 또는 BeanFactory(AC 는 BeanFactory의 완벽한 superset)

아래는 백기선님의 [Spring MVC에서 사용하는 ApplicationContext와 WebApplicationContext](http://whiteship.tistory.com/category/Spring%20MVC) 설명 글이다. 

>보통 스프링 설정 파일이 최소한 두 개이상 있을 겁니다. xxx-servlet.xml 과 나머지로 나눌 수 있습니다. 그중에서 xxx-servlet.xml은 DispatcherServlet이 WebApplicationContext를 만들 때 사용하고, 나머지는  ContextLoaderListener 또는 ContextLoaderServlet이 일반적인 ApplicationContext를 만들 때 사용합니다.

> 이게 끝이 아닙니다. WebApplicationContext는 바로 이 ApplicationContext를 상속받아서 여러 서블릿들이 공통으로 사용하는 빈들을 사용할 수 있게 되는 겁니다. 따라서 만들어지는 순서도 중요한데, Listener가 아니라 ContextLoaderServlet을 사용했을 때는 load 머시기 설정 값에 1을 줘서 DispatcherServlet보다 먼저 만들게 해야 합니다. 그래서 WebApplicationContext를 만들 때 해당 ApplicationContext를 상속받아서 그 안에 있는 빈들을 사용할 수 있게 되겠죠.

> 이런 구조로 설계한 건, DispatcherServlet이 하나의 웹 애플리케이션에서 여러 개일 수 있기 때문입니다. 여러 개의 DispatcherServlet에서 공통으로 사용할 빈들을 상위에 있는 ApplicationContext에 선언해두고 공유할 수 있게 하는 거죠.

그냥 간단히 빈을 가져오게 해주는 역할이라고 볼까? (돌려주는) 

	
	
	
	
	


### web/WEB-INF 
WEB-INF 이하는 브라우저로 접근할 수 없다. 해서 이곳에 노출되지 말아야 하는 파일들을 놓으면된다. 그외 파일은 web 에 두어도 된다.

 

