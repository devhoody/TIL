- [Spring](#spring)
  - [AnnotationConfigApplicationContext](#annotationconfigapplicationcontext)
    - [설정방법 실습](#설정방법-실습)
  - [@ComponentScan, @CompononetScans](#componentscan-compononetscans)
  - [@AutoWired](#autowired)
  - [xml + 어노테이션](#xml--어노테이션)
    - [dao나 service의 중복 제거](#dao나-service의-중복-제거)
  - [스프링 빈과 의존관계](#스프링-빈과-의존관계)
    - [컴포넌트 스캔과 자동 의존관계 설정 @Controller, @Service, @Repository](#컴포넌트-스캔과-자동-의존관계-설정-controller-service-repository)
    - [소스코드 없이 자바 코드를 이용하여 bean 객체 생성하기](#소스코드-없이-자바-코드를-이용하여-bean-객체-생성하기)
  - [Spring을 이용한 hello 페이지 구현](#spring을-이용한-hello-페이지-구현)
    - [**ResponseBody**](#responsebody)
    - [**requestMapping**](#requestmapping)
  - [Spring을 이용한 쿼리스트링 값 받기](#spring을-이용한-쿼리스트링-값-받기)
  - [Spring을 이용한 View 구현하기](#spring을-이용한-view-구현하기)
    
# Spring

- 스프링이란?
    - **객체를 생성하고 조립 및 결합**을 대신해주는 프레임워크
- 스프링을 사용하는 이유
    - 이 안에 객체를 대신 만들어주는 등의 편리성을 제공한다.

## AnnotationConfigApplicationContext

- **Annotation**을 이용하여 **ApplicationContext에 저장**하는 방법
- 장점
    - 파일을 따로 만들필요가 없다.
    - 설정을 많이 할 필요가 없다.

### 설정방법 실습

- App.java
    
    ```java
    ApplicationContext context = new AnnotationConfigApplicationContext(SpringConpig.class);
    ```
    
- SpringConpig.java - 새로 생성
    - 범주를 spring까지 하면 그 이하의 패키지들을 모두 포함가능하다.
    
    ```java
    @ComponentScan("com.newlecture.spring")
    ```
    
- DmMenuDao.java
    
    ```java
    @Component
    public class DmMenuDao implements MenuDao {
    ```
    

## @ComponentScan, @CompononetScans

- Config 자바 파일에서 컴포넌트들을 Scan하는데 필요하다.
```java
@ComponentScan("com.newlecture.spring")
```

## @AutoWired

- `injection 위치`를 설정하는 Annotation
- 종류 3가지
    - `field injection` ← 뉴렉쌤 추천
    - `**constructor injection**` <- 김영한님 추천
      - 왜? → 의존관계가 한번 세팅된 이후로는 변경될 가능성이 거의 zero. 세팅 이후 수정 못하도록 생성자 주입으로 관계를 세팅한다.
    - **setter injection**
    - 인젝션 시점에 초기화 등의 조건이 필요할 때 constructor, setter injection 사용.
    
    ```java
    @Autowired // field injection
    private MenuDao dao; // 인터페이스를 쓰는 이유??
    
    @Autowired // constructor injection
    public MenuServiceImp() {
    //		dao = new DmMenuDao();
    }
    
    @Autowired // setter injection
    public void setDao(MenuDao dao) {
    	this.dao = dao;
    }
    ```
    


## xml + 어노테이션

- xml과 어노테이션 설정을 동시에 수행할 수 있다.
- xml파일에서 beans 수정
    
    ```java
    <beans 
        xmlns="http://www.springframework.org/schema/beans"
        **xmlns:context="http://www.springframework.org/schema/context"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation=
            "http://www.springframework.org/schema/beans
            https://www.springframework.org/schema/beans/spring-beans.xsd
            **http://www.springframework.org/schema/context
            https://www.springframework.org/schema/context/spring-context.xsd"
            >
    <context:annotation-config/>
    ```
    

### dao나 service의 중복 제거

- dao와 service가 xml과 Annotation에 각각 중복되어 적용이 될 경우, **xml에서 설정된 클래스 경로의 하위 폴더에서 Annotation을 찾게 된다.**
- 만약 Annotation의 스캔 폴더를 xml에서 설정하기 위해서는 따로 설정 코드를 작성해야한다.
    - *이 때, xml의 dao, service 설정 코드는 주석처리.*
    
    ```java
    // 검색범위 : spring 이하 패키지
    <context:component-scan base-package="com.newlecture.spring"/>
    ```
    
## 스프링 빈과 의존관계
- 스프링 컨테이너에 Bean을 등록하기 위해 아래 두 가지 방식이 존재한다.

### 컴포넌트 스캔과 자동 의존관계 설정 @Controller, @Service, @Repository

- Component의 종류로 Controller와 Service, Repository 각각에 맞게 Annotation으로 사용한다.
  - 정형화된 객체의 경우에만 위의 3가지 Component사용이 가능하다.
  - 


### 소스코드 없이 자바 코드를 이용하여 bean 객체 생성하기

- 소스코드가 없어 직접 자바코드를 이용하여 bean을 생성할 경우, `@Configuration` , `@Bean`을 이용
- `@Configuration` : @component중의 종류 하나
- 실무에서는 정형화된 Controller, Service, Repository 같은 코드는 컴포넌트 스캔을 사용한다. 하지만, 정형화되지 않거나, 상황에 따라 구현 클래스를 변경해야하면 설정을 통해 스프링 빈으로 등록한다.
  - *구현클래스 변경? -> 나중에 레포지토리만 갈아 끼우기 위한 방법*

```java
@Configuration
public class BeanConfig {

	@Bean
	public List list() {
		return new ArrayList();
	}
}
```
- 김영한

```java
@Bean
public MemberService memberService(){
    return new MemberService(memberRepository();
}

@Bean
public MemberRepository memberRepository(){
    return new MemoryMemberRepository();
}

// 레포지토리 변경시
@Bean
public MemberRepository memberRepository(){
    return new DbMemberRepository(); // 해당 레포지토리만 변경
}
```

## Spring을 이용한 hello 페이지 구현

- homecontroller 생성

### **ResponseBody**

- http의 Body박스에 직접 값을 넣어서 클라이언트에게 전달하는 예약어

### **requestMapping**

- 클래스의 **문서**를 설정한 **url**에 매핑시켜주는것.
- Mapping위치 설정은 class와 메소드에 각각 설정하여 url에 연결 가능
    
    ```java
    @Controller
    @RequestMapping("/menu")
    public class MenuController {
    
        @RequestMapping("list")
        public String list() {
            return "List 출력";
        }
    
        @RequestMapping("detail")
        public String detail() {
            return "상세메뉴 출력";
        }
    
    }
    ```
    

## Spring을 이용한 쿼리스트링 값 받기

- url을 @RequestMapping으로 연결한 후, 쿼리스트링과 동일한 변수명으로 값을 대입하면 그대로 쿼리스트링 값을 받을 수 있다.
- 실습
    
    ```java
    @Controller
    public class HomeController {
    	@RequestMapping("/index") // localhost/index 에 연결 
    		public String index(String s) {
    			System.out.println(s);
    	
    			return "/WEB-INF/view/index.jsp"; // index.jsp에 연결
    		}
    }
    ```
    
    - 전에 설정해놓은 indexController에서 @webServlet(”/index”)를 주석처리 → *webservlet이 파일명보다 우선순위를 가지므로 주석처리해야 index.html 파일에 접근이 가능하다.*
    

## Spring을 이용한 View 구현하기

- forwading을 이용하여 jsp파일을 가져와 view를 출력한다.

```java
@Controller
@RequestMapping("/menu")
public class MenuController {

    @RequestMapping("list")
    public String list() {
        return "/WEB-INF/view/menu/list.jsp";
    }

    @RequestMapping("detail")
    public String detail() {
        return "/WEB-INF/view/menu/detail.jsp";
    }

}
```