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



### web/WEB-INF 
WEB-INF 이하는 브라우저로 접근할 수 없다. 해서 이곳에 노출되지 말아야 하는 파일들을 놓으면된다. 그외 파일은 web 에 두어도 된다.

 

