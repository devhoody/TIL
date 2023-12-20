- [빈 스코프](#빈-스코프)
  - [스코프 종류와 범위](#스코프-종류와-범위)
  - [스코프 등록 방법](#스코프-등록-방법)
  - [프로토타입 스코프](#프로토타입-스코프)
  - [프로토타입 스코프 - 싱글톤 빈과 함께 사용시 문제점](#프로토타입-스코프---싱글톤-빈과-함께-사용시-문제점)
  - [**프로토타입 스코프** - **싱글톤 빈과 함께 사용시** Provider**로 문제 해결**](#프로토타입-스코프---싱글톤-빈과-함께-사용시-provider로-문제-해결)
    - [스프링 컨테이너에 요청](#스프링-컨테이너에-요청)
    - [Java Provider](#java-provider)

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