- [빈 스코프](#빈-스코프)
  - [스코프 종류와 범위](#스코프-종류와-범위)
  - [스코프 등록 방법](#스코프-등록-방법)
  - [프로토타입 스코프](#프로토타입-스코프)
  - [프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점](#프로토타입-스코프---싱글톤-빈과-함께-사용시-문제점)
  - [**프로토타입 스코프** - **싱글톤 빈과 함께 사용시** Provider**로 문제 해결**](#프로토타입-스코프---싱글톤-빈과-함께-사용시-provider로-문제-해결)
    - [스프링 컨테이너에 요청](#스프링-컨테이너에-요청)
    - [Java Provider](#java-provider)
  - [웹스코프](#웹스코프)
  - [request 스코프 예제 만들기](#request-스코프-예제-만들기)
    - [웹 라이브러리 추가](#웹-라이브러리-추가)
    - [로그 기록 클래스 - MyLogger](#로그-기록-클래스---mylogger)
    - [컨트롤러 - LogDemoController](#컨트롤러---logdemocontroller)
    - [업무로직 - logDemoService](#업무로직---logdemoservice)
  - [스코프와 Provider](#스코프와-provider)
  - [웹 스코프와 프록시](#웹-스코프와-프록시)
    - [웹 스코프와 프록시 동작 원리](#웹-스코프와-프록시-동작-원리)

# 빈 스코프

- 스코프란?
    - 존재할 수 있는 범위
- 빈 스코프란?
    - 빈이 존재할 수 있는 범위
    - ex) 싱글톤 스코프, 프로토타입 스코프, 웹 스코프…

## 스코프 종류와 범위

- **싱글톤 스코프** : 스프링 빈의 기본, 스프링 컨테이너가 생성되고 소멸되기까지 유지. 가장 긴 스코프
- **프로토타입 스코프** : 스프링 컨테이너가 빈의 생성부터 초기화까지만 담당하고 나머지는 클라이언트에게 맡김
- **웹 스코프** : 웹과의 통신 과정에서의 스코프
    - **request :** 웹 요청이 들어오고 나가기 까지의 스코프
    - **session** : 웹 세션이 생성되고 소멸될 때까지의 스코프
    - **application** : 서블릿 컨텍스트와 같은 범위의 스코프

## 스코프 등록 방법

- 빈을 자동, 수동으로 등록할 경우 모두 적용 가능하다.
- `@Scope` 가 없다면 스프링의 기본 스코프인 싱글톤 스코프가 적용된다.
- 코드
    
    ```java
    // 자동 싱글톤 스코프
    @Scope("sington")
    @Component
    public class Hello{
    }
    
    // 수동
    @Scope("prototype")
    @Bean
    PrototypeBean Hello(){
    }
    ```
    

## 프로토타입 스코프

- 싱글톤 스코프는 하나의 인스턴스로 클라이언트들이 공유하는 개념이었다면, 프로토타입 스코프는 **조회할 때마다 새로운 인스턴스를 만들어 반환**한다.
- **스프링 컨테이너는 프로토타입 빈을 생성→의존관계 주입→초기화 까지만 처리한다.**
- 종료 메서드가 호출되지 않는다.
- 코드
    
    ```java
    public class SingletonTest {
         @Test
         public void singletonBeanFind() {
             AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(SingletonBean.class);
             SingletonBean singletonBean1 = ac.getBean(SingletonBean.class);
             SingletonBean singletonBean2 = ac.getBean(SingletonBean.class);
             System.out.println("singletonBean1 = " + singletonBean1);
             System.out.println("singletonBean2 = " + singletonBean2);
             assertThat(singletonBean1).isSameAs(singletonBean2);
    					
    				ac.close(); //종료 
    			}
    
         @Scope("singleton")
         static class SingletonBean {
             
    					@PostConstruct
    	         public void init() {
    						  System.out.println("SingletonBean.init");
    						}
    
    					@PreDestroy
    					public void destroy() {
    					    System.out.println("SingletonBean.destroy");
    					}
    		} 
    }
    ```
    
    - 실행결과
    
    ```java
    SingletonBean.init
    singletonBean1 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
    singletonBean2 = hello.core.scope.PrototypeTest$SingletonBean@54504ecd
    org.springframework.context.annotation.AnnotationConfigApplicationContext -
    Closing SingletonBean.destroy
    ```
    
    - 같은 인스턴스를 호출한 것을 확인할 수 있다.
- 

## 프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점

- 싱글톤 빈과 프로토타입 빈을 함께 사용할 경우, 프로토타입 빈이 새로운 인스턴스를 반환하지 않는 문제점이 발생할 수 있다.

    
    - 클라이언트 A와 모두 clientBean을 호출한다. 스프링 컨테이너는 싱글톤 빈이므로 항상 같은 빈을 반환한다.
    - 이 때, 프로토타입 빈은 이미 클라이언트 A가 호출할 때, clientBean에 주입된 채로 존재한다. 이는 주입 시점에 스프링 컨테이너가 요청해서 프로토타입 빈이 생성된 것이다. 매번 새로 생성되는 것 아님!
    - 

## **프로토타입 스코프** - **싱글톤 빈과 함께 사용시** Provider**로 문제 해결**

- (싱글톤 빈 + 매번 새로운 프로토타입 빈 반환) 을 구현하려면 어떻게 해야 할까? → Provider 사용

### 스프링 컨테이너에 요청

- 직접 `ApplicationContext` 을 주입시켜 메소드를 호출할 때마다 새로운 프로토타입 빈을 만들어 반환한다.
    
    ```java
    @Autowired
    **private ApplicationContext ac;**
    
    public int logic() {
       **PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class);**
       prototypeBean.addCount();
       int count = prototypeBean.getCount();
       return count;
    }
    ```
    
    - 이처럼 의존관계를 외부에서 주입받는 것(DI)가 아니라 직접 필요한 의존관계를 찾는 것을 `DL(Dependency Lokup, 의존관계 탐색)`이라고 한다.
    - 단점 - ApplicationContext는 스프링에 종속되있으므로 코드가 스프링에 종속되고, 단위 테스트가 어려워진다.
    
    ### ObjectProvider
    
    - 지정한 빈을 컨테이너에서 대신 찾아주는 DL서비스 제공
    
    ```java
    @Autowired
    **private ObjectProvider<PrototypeBean> prototypeBeanProvider;**
    
    public int logic() {
       PrototypeBean prototypeBean = **prototypeBeanProvider.getObject();**
       prototypeBean.addCount();
       int count = prototypeBean.getCount();
       return count;
    }
    ```
    

### Java Provider

- 라이브러리를 추가하여 사용
    - 스프링 부트 3.0이상 → build.gradle에 `jakarta.inject:jakarta.inject-api:2.0.1` 추가

```java
@Autowired
**private Provider<PrototypeBean> provider;**

public int logic() {
   PrototypeBean prototypeBean = **provider.get();**
   prototypeBean.addCount();
   int count = prototypeBean.getCount();
   return count;
}
```

- provider.get()으로 스프링 컨테이너 내부에서 해당 빈을 찾는다.
- 자바 표준으로 단위테스트나 mock 코드를 만들기 용이하다.
- 자바 표준이므로 스프링 이외의 컨테이너에서도 사용 가능하다.

## 웹스코프

- **request 스코프란?**
    - Http 요청이 있을 때, 스프링 컨테이너에 의해 호출되는 빈
    - http 요청이 시작하고 응답이 끝날 때까지 존재한다.
- request 스코프가 필요한 이유
    - HTTP 요청이 동시에 들어오기 때문에 어떤 요청이 남긴 로그인지 구분하기 어렵다.
- 사용 방법
    - `@Scope(value = “request”)`
    
    ```java
    @Scope(value = "request")
    public class MyLogger {
    }
    ```
    
- 특징
    - 웹 환경에서만 동작한다.
    - 스프링이 종료시점까지 관리하므로 종료 메서드까지 호출된다.

## request 스코프 예제 만들기

- 웹 스코프는 웹 환경에서만 동작하므로 웹 환경의 라이브러리를 추가해야 한다.

### 웹 라이브러리 추가

- buile.gradle
    - `implementation 'org.springframework.boot:spring-boot-starter-web’`

### 로그 기록 클래스 - MyLogger

- 어떤 클라이언트가 http 요청을 했는지 기록하기 위함
- `UUID` : 클라이언트 고유 번호 → 요청할 때마다 바뀐다.
    - 자바의 util 클래스에 존재.
- `requestURL` : http요청한 URL

### 컨트롤러 - LogDemoController

- http요청을 받아 응답하기 위해 controller를 생성한다.
- requestURL을 얻기 위해 HttpServletRequest를 사용했다.
    - requestURL은 모든 클라이언트에게 **공통적인 부분**이다. 따라서, **스프링 인터셉터나 서블릿 필터를 이용하여 requestURL을 얻는 것이 좋다.**
- controller코드
    
    ```java
    @Controller
    @RequiredArgsConstructor
    public class LogDemoController {
    
        private final LogDemoService logDemoService;
    		private final MyLogger myLogger;
        
    		@RequestMapping("log-demo")
        @ResponseBody
        public String logDemo(**HttpServletRequest request**){
            MyLogger myLogger = myLoggerProvider.getObject();
            String requestURL = request.getRequestURL().toString();
            myLogger.setRequestURL(requestURL);
    
            myLogger.log("controller test");
            logDemoService.logic("testId");
    
            return "OK";
        }
    }
    ```
    

### 업무로직 - logDemoService

- 로그를 남기는 서비스를 하는 클래스
- 코드
    
    ```java
    @Service
    @RequiredArgsConstructor
    public class LogDemoService {
    
    		private final MyLogger myLogger;
        
    		public void logic(String testId) {
            MyLogger myLogger = myLoggerProvider.getObject();
            myLogger.log("service id = " + testId);
        }
    }
    ```
    
    - 여기서 중요한 점은 requestURL은 Service가 아닌 Controller에서 입력했다. 그 이유는 웹과 관련된 기술의 경우, Contoller까지 이용하고 Service에서는 이용하지 않는 것이 유지보수에 좋기  때문이다.
- 실행하면 오류가 발생한다.
    - 이유 : request스코프 빈은 http 요청이 있을 때에 생성되어 주입된다. 즉, 스프링 컨테이너가 생성되는 것은 request 스코프 빈을 주입하지 않는다. 이를 Provider로 해결한다.

## 스코프와 Provider

> **Provider와 프록시 모두 실제 객체가 필요할 때까지 생성을 지연한다는 점이 중요하다**
> 
- request 스코프는 http 요청이 들어와야만 빈이 생성된다. 하지만 스프링 컨테이너를 만들때는 생성되지 않으므로 오류가 발생한다. 그럼 서버가 켜지는 것과 request 스코프 빈이 대기하고 있는 것을 어떻게 구현할까? 이것을 Provider가 해결해준다.
- `ObjectProvider` 이용
    - LogDemoController
        
        ```java
        private final ObjectProvider<MyLogger> myLoggerProvider;
        ```
        
    - LogDemoService
        
        ```java
        private final ObjectProvider<MyLogger> myLoggerProvider;
        ```
        
- Provider에 의해 **request 스코프 빈의 생성을 지연**할 수 있다.

## 웹 스코프와 프록시

- Provider없이 기존 코드에서 request 스코프에 대한 문제를 해결할 수 있는 방법은 없을까?
- 프록시를 이용하면 스프링 컨테이너가 request 스코프의 가짜 프록시 객체를 만들어 주입을 시켜놓는다. 이후, http요청이 들어오면 그떄서야 진짜 객체를 생성하여 주입을 시킨다.
- 코드
    
    ```java
    @Component
    @Scope(value = "request", **proxyMode = ScopedProxyMode.TARGET_CLASS**)
    public class MyLogger {
    }
    ```
    ```
    
    - 클래스에 적용한다면 `TARGET_CLASS`, 인터페이스에 적용한다면 `INTERFACES` 를 이용하면 된다.

### 웹 스코프와 프록시 동작 원리

- `CGLIB`이 여기서도 작용함.
- myLogger 클래스를 얻어보면 결과는 다음과 같다.
    
    ```java
    System.out.println("myLogger = " + myLogger.getClass());
    
    // 실행결과 
    // myLogger = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$b68b726d
    ```
    
    - 이처럼 내가 만든 MyLogger가 아닌 SpringCGLIB에 의해 생성된 가짜 프록시 객체가 반환되는 걸 알 수 있다.
- **동작원리**
    - CGLIB 라이브러리를 이용하여 내 클래스를 상속받은 가짜 프록시 객체를 만들어 주입시킨다.
    - 가짜 프록시 객체 안에는 실제 빈을 호출하는 메서드가 들어있어 Http 요청이 들어오면 실제 빈을 호출한다.
    - 단순히 위임 로직만 있으며, **싱글톤**으로 존재한다.
