- [스프링 빈](#스프링-빈)
  - [스프링 빈 조회 - 상속관계](#스프링-빈-조회---상속관계)
  - [BeanFactory와 ApplicationContext](#beanfactory와-applicationcontext)
    - [BeanFactory](#beanfactory)
    - [ApplicationContext](#applicationcontext)
  - [XML 설정 사용](#xml-설정-사용)
  - [스프링 빈 설정 메타 정보 - BeanDefinition](#스프링-빈-설정-메타-정보---beandefinition)
- [싱글톤 컨테이너](#싱글톤-컨테이너)
  - [WWH](#wwh)
  - [싱글톤 패턴](#싱글톤-패턴)
  - [싱글톤 컨테이너](#싱글톤-컨테이너-1)
  - [싱글톤 방식의 주의점](#싱글톤-방식의-주의점)

# 스프링 빈 
## 스프링 빈 조회 - 상속관계

- 부모 타입을 조회하면 자식 타입은 다 끌려나온다.
    - 모든 자바 객체의 부모 타입은 `Object`를 조회하면, 모든 스프링 빈을 조회한다.

> 실제 테스트케이스를 만들때는 Sysout을 만들지않는게 좋다.

## BeanFactory와 ApplicationContext

> BeanFactory나 ApplicationContext를 **스프링 컨테이너**라고 부른다.
> 
- **BeanFactory는 직접 사용하지 않고 주로 ApplicationContext를 사용한다.**

### BeanFactory

- 스프링 컨테이너의 최상위 인터페이스
- 스프링 빈을 관리하고 조정하는 역할 → GetBean() 등

### ApplicationContext

- BeanFactory 기능을 상속받아서 제공
- **BeanFactory 기능 + 부가기능**
- **왜 필요해?**
    - 애플리케이션 운영 시 스프링 빈 관리 뿐만아니라 더 많은 부가 기능이 필요하다.
- **ApplcationContext의 추가 기능**
    - **메시지소스를 활용한 국제화 기능**
        - 한국→ 한국어, 영어권 → 영어
    - **환경변수**
        - 로컬, 개발, 운영등을 구분해서처리
    - **애플리케이션 이벤트**
        - 이벤트를 발행하고 구독하는 모델을 지원
    - **편리한 리소스 조회**
        - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회

## XML 설정 사용

- ApplicationContext를 상속받는 Context로는 자바 코드를 사용하는`AnnotationConfigApplicationContext` 말고도 xml을 이용한 `GenericXmlApplicationContext`가 있다.
- 하지만 잘 사용하지 않고 대부분 Annotation을 이용한다.
- AppConfig의 xml파일
    
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    
        <bean id="memberService" class="hello.core.member.MemberServiceImpl">
            <constructor-arg name="memberRepository" ref="memberRepository" />
        </bean>
    
        <bean id="memberRepository"
              class="hello.core.member.MemoryMemberRepository" />
    
        <bean id="orderService" class="hello.core.order.OrderServiceImpl">
            <constructor-arg name="memberRepository" ref="memberRepository" />
            <constructor-arg name="discountPolicy" ref="discountPolicy" />
        </bean>
    
        <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy" />
    </beans>
    ```
    
- xml 테스트 클래스
    
    ```java
    public class XmlAppContext {
        @Test
        void xmlAppContext() {
            ApplicationContext ac = new
                    GenericXmlApplicationContext("appConfig.xml");
            MemberService memberService = ac.getBean("memberService",
                    MemberService.class);
            assertThat(memberService).isInstanceOf(MemberService.class);
        }
    }
    ```
    

## 스프링 빈 설정 메타 정보 - BeanDefinition

- **스프링은 다양한 설정 형식을 어떻게 사용할 수있나?**
    - 스프링이 다양한 형태의 설정 정보를 **BeanDefinition을 추상화**해서 사용한다.
- 스프링 컨테이너는 BeanDefinition인 빈 메타정보를 기반으로 스프링 빈을 생성한다.
    - **@Bean, <Bean> 당 각각 하나씩 메타 정보가 생성된다.**

- 각 Context에 맞는 `Reader`가 지정된 Config파일을 읽어서 BeanDefinition을 생성한다.

# 싱글톤 컨테이너

## WWH

- **싱글톤이란?**
    - 여러 클라이언트들이 1개의 인스턴스 객체를 `공유`하는 기술
- **왜 싱글톤 컨테이너를 사용하지?**
    - 스프링은 온라인 서비스를 지원하는데 필요한 기술이다. 만약에 고객이 1만명이 A서비스를 호출했다면, A서비스에 필요한 객체가 1만개가 만들어진다. ⇒ `메모리 낭비에 최악!`
    - 해결방안
        - A서비스에 필요한 객체를 1개만 만들고 이를 공유하도록 하자! —> 싱글톤
- 순수한 DI 컨테이너 사용 코드(스프링 사용 x)
    
    ```java
    package hello.core.singleton;
    
    import hello.core.AppConfig;
    import hello.core.member.MemberService;
    import org.junit.jupiter.api.DisplayName;
    import org.junit.jupiter.api.Test;
    
    import static org.assertj.core.api.Assertions.assertThat;
    
    public class SingletonTest {
        @Test
        @DisplayName("스프링 없는 순수한 DI 컨테이너") void pureContainer() {
            AppConfig appConfig = new AppConfig();
    //1. 조회: 호출할 때 마다 객체를 생성
            MemberService memberService1 = appConfig.memberService();
    //2. 조회: 호출할 때 마다 객체를 생성
            MemberService memberService2 = appConfig.memberService();
    //참조값이 다른 것을 확인
            System.out.println("memberService1 = " + memberService1); System.out.println("memberService2 = " + memberService2);
            //memberService1 != memberService2
            assertThat(memberService1).isNotSameAs(memberService2);
        }
    }
    ```
    

## 싱글톤 패턴

- 싱글톤 패턴이란?
    - **클래스의 인스턴스가 딱 1개만 생성**되는 것을 보장하는 디자인 패턴
    - `private 생성자` 이용하여 패턴을 적용할 수 있다.
- 싱글톤 서비스 생성
    
    ```java
    public class SingleTonService {
    
        // 싱글톤 서비스 인스턴스 1개 생성
        private static final SingleTonService instance = new SingleTonService();
    
        // 2개 이상 싱글톤 인스턴스 객체 생성 막기 (Private + 생성자)
        private SingleTonService() {
    
        }
    
        // 싱글톤 서비스 호출
        public static SingleTonService getInstance() {
            return instance;
        }
    
        public void logic() {
            System.out.println("싱글톤 객체 로직 호출");
        }
    }
    ```
    
- **스프링은 자동으로 싱글톤 패턴을 적용시켜준다.**
- **싱글톤 패턴 문제점**
    - 싱글톤 패턴을 **`구현하는 코드`** 자체가 많이 들어간다.
    - 클라이언트가 `구체 클래스에 의존`할 수 있다. → `DIP, OCP 위반`
        - `SingletonServcie.getInstance()` 를 사용하므로!
    - 테스트하기 어려움.
    - `private 생성자`  → 자식 클래스 생성 어려움.
    - 결과적으로 유연성이 떨어진다.
    - **안티 패턴**으로 불리기도 함.

## 싱글톤 컨테이너

- 사용될 인스턴스 객체를 1개만 미리 만들어두어 공유하는 방식의 컨테이너
- **스프링컨테이너 == 싱글톤 컨테이너**
- 스프링 컨테이너를 이용하면 알아서 인스턴스 객체를 만들어주므로 싱글톤 패턴의 문제점을 모두 제거시켜준다.
    - DIP, OCP, 테스트, private 생성자로 부터 자유롭게 싱글톤을 사용할 수 있다.
- 싱글톤 객체를 생성하고 관리하는 기능 ⇒ `싱글톤 레지스트리`
- 싱글톤 컨테이너 테스트 코드
    
    ```java
    @Test
    @DisplayName("스프링컨테이너 싱글톤 테스트")
    void springContainer() {
    //        AppConfig appConfig = new AppConfig();
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
        MemberService memberService1 = ac.getBean("memberService", MemberService.class);
        MemberService memberService2 = ac.getBean("memberService", MemberService.class);
    
    //참조값이 같으 것을 확인
        System.out.println("memberService1 = " + memberService1);
        System.out.println("memberService2 = " + memberService2);
        //memberService1 != memberService2
        assertThat(memberService1).isSameAs(memberService2);
    }
    ```
    

## 싱글톤 방식의 주의점

- 싱글톤은 여러 클라이언트가 인스턴스 객체 1개를 공유하는 것이다. 그래서 **인스턴스 객체를 상태 유지(Stateful)하게 설계하면 안된다.**
- `무상태(UnStateful)`로 설계해야한다!
    - 특정 클라이언트에 **의존적인 필드가 있으면 안된다.**
    - 특정 클라이언트가 **값을 변경할 수 있는 필드가 있으면 안된다.**
    - 가급적 **읽기만 가능해야 한다.**
    - 필드 대신에 자바에서 **공유되지 않는, 지역변수, 파라미터, ThreadLocal 등을 사용해야 한다.**
- StatefulService 코드 - **상태유지**
    
    ```java
    public class StatefulService {
    		// 클래스 필드에 price 변수 생성(공유)
        private int price;
    
        public void order(String name, int price){
            System.out.println("name = " + name + " price = " + price);
            this.price = price;
        }
    
        public int getPrice(){
            return price;
        }
    }
    ```
    
    - price를 클래스 필드에 공유.
    - order의 매개변수가 클래스 필드의 price에 대입된다.
- 싱글톤 상태 유지 테스트 코드
    
    ```java
    @Test
    void statefulServiceSingleton(){
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
    
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);
    
        //ThreadA : 10000원 주문
        statefulService1.order("userA", 10000);
        //ThreadB : 20000원 주문
        statefulService1.order("userB", 20000);
    
        //ThreadA: 사용자A 주문 금액 조회
        int price = statefulService1.getPrice();
        //ThreadA: 사용자A는 10000원을 기대했지만, 기대와 다르게 20000원 출력
        System.out.println("price = " + price);
    }
    ```
    
    - 싱글톤은 같은 인스턴스 객체(StatefulService)를 공유하기때문에 그 안의 Price값이 20000원으로 바뀌어 출력된다.
- 싱글톤서비스를 무상태로 만들어 해결
    - StatefulService 코드 (**무상태 - 파라미터 사용**)
        
        ```java
        public class StatefulService {
        //    private int price;
        
            public int order(String name, int price){
                System.out.println("name = " + name + " price = " + price);
        //        this.price = price;
                return price; // 파라미터 사용
            }
        
        //    public int getPrice(){
        //        return price;
        //    }
        }
        ```
        
        - 테스트 코드
        
        ```java
        @Test
        void statefulServiceSingleton(){
            AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        
            StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
            StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);
        
            //ThreadA : 10000원 주문
            int price = statefulService1.order("userA", 10000);
            //ThreadB : 20000원 주문
            statefulService1.order("userB", 20000);
        
            //ThreadA: 사용자A 주문 금액 조회
        //        int price = statefulService1.getPrice();
            //ThreadA: 사용자A는 10000원을 기대했지만, 기대와 다르게 20000원 출력
            System.out.println("price = " + price);
        }
        ```