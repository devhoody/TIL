- [싱글톤](#싱글톤)
  - [@Configuration**과 싱글톤**](#configuration과-싱글톤)
  - [@Configuration**과 바이트코드 조작의 마법**](#configuration과-바이트코드-조작의-마법)
    - [`@Configuration` **을 적용하지 않고**, `@Bean` **만 적용하면 어떻게 될까**?](#configuration-을-적용하지-않고-bean-만-적용하면-어떻게-될까)
    - [**정리**](#정리)


# 싱글톤

## @Configuration**과 싱글톤**

- AppConfig를 보면 MemberService와 OrderSerivce에 의해 MemberRepository가 2번 호출되면서 new MemoryMemberRepository()가 2번 실행되는 것을 알 수 있다.
- 자바 코드로 보면 객체 2개가 생성되므로 싱글톤이 깨지는게 아닌가? 생각이 든다.
- 이를 스프링은 어떻게 해결하는지 코드로 살펴보자
- **검증코드**
    - memberServiceImpl
        
        ```java
        // 테스트 용도
        public MemberRepository getMemberRepository() {
            return memberRepository;
        }
        ```
        
    - orderServiceImpl
        
        ```java
        // 테스트 용도
        public MemberRepository getMemberRepository() {
            return memberRepository;
        }
        ```
        
    - 테스트코드
        
        ```java
        public class ConfigurationSingletonTest {
        
            @Test
            void configurationTest(){
                AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        
                MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
                OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
                MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
        
                MemberRepository memberRepository1 = memberService.getMemberRepository();
                MemberRepository memberRepository2 = orderService.getMemberRepository();
        
                System.out.println("memberService -> memberRepository = " + memberRepository1);
                System.out.println("orderService -> memberRepository = " + memberRepository2);
                System.out.println("memberRepository = " + memberRepository);
        
                Assertions.assertThat(memberRepository1).isSameAs(memberRepository2);
            }
        }
        ```
        
        - 실행결과
            
            ```java
            memberService -> memberRepository = hello.core.member.MemoryMemberRepository@41294f8
            orderService -> memberRepository = hello.core.member.MemoryMemberRepository@41294f8
            memberRepository = hello.core.member.MemoryMemberRepository@41294f8
            ```
            
            - 모두 같은 인스턴스를 참조하고있다.
            - 인스턴스 객체가 2개가 생성되어야하는데? 혹시 호출이 2번 되는게 아닌가?
- 객체 호출 2번 유무 테스트
    - AppConfig에 호출 로그 남기기
        
        ```java
        @Bean
        public MemberService memberService() {
            System.out.println("call AppConfig.memberService");
            return new MemberServiceImpl(memberRepository());
        }
        
        @Bean
        public MemberRepository memberRepository() {
            System.out.println("call AppConfig.memberRepository");
            return new MemoryMemberRepository();
        }
        
        @Bean
        public OrderService orderService() {
            System.out.println("call AppConfig.orderService");
            return new OrderServiceImpl(memberRepository(), discountPolicy());
        }
        ```
        
    - 결과
        
        ```java
        call AppConfig.memberService
        call AppConfig.memberRepository
        call AppConfig.orderService
        memberService -> memberRepository = hello.core.member.MemoryMemberRepository@41294f8
        orderService -> memberRepository = hello.core.member.MemoryMemberRepository@41294f8
        memberRepository = hello.core.member.MemoryMemberRepository@41294f8
        ```
        
        - 예상대로라면 MemoryMemberRepository는 @Bean 에 해당하는 스프링 빈을 만들때 3번 만들어져야 한다.
            1. memberService 스프링 빈 생성
            2. memberRepository 스프링 빈 생성
            3. orderService 스프링 빈 생성
        - 그런데 출력 결과는 모두 1번씩 출력된다. 왜??!!!

## @Configuration**과 바이트코드 조작의 마법**

- 스프링이 클래스의 바이트코드를 조작한다?
    - 스프링 컨테이너는 싱글톤 레지스트리이므로 스프링 빈이 싱글톤이 되도록 보장해주어야 한다. 그러기위해 클래스의 바이트코드를 조작한다.
- 테스트코드(`configurationDeep`)
    
    ```java
    @Test
    void configurationDeep(){
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
        AppConfig bean = ac.getBean(AppConfig.class);
        System.out.println("bean = " + bean.getClass());
    }
    ```
    
    - 테스트 결과
        
        ```java
        bean = class hello.core.AppConfig$$SpringCGLIB$$0
        ```
        
        - 순수하게 자바 코드만 읽는다면 클래스 타입은 **hello.core.AppConfig** 이 출력될 것이다.
        - 하지만 그 뒤에 `SpringCGLIB`가 붙은 이유는 무엇일까?
            - 스프링이 **CGLIB라는 바이트코드 조작 라이브러리**를 사용하여 **AppConfig클래스를 `상속`받은 임의의 다른 클래스**를 만들고 그 클래스를 스프링 빈으로 등록한 것이다.
                
                ![스크린샷 2023-12-12 오후 4.59.32.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e2d630d3-4b8f-4cb4-b886-9409e67db84f/972e28a7-cd1d-4f69-8bb1-e55d6d17f1a4/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-12-12_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_4.59.32.png)
                
            - 따라서 임의의 클래스를 이용함으로써 싱글톤을 보장해준다.
        - **참고로 `AppConfig@CGLIB`는 AppConfig의 자식타입이므로, AppConfig 타입으로 조회할 수 있다.**

### `@Configuration` **을 적용하지 않고**, `@Bean` **만 적용하면 어떻게 될까**?

- 테스트 결과 - `configurationDeep`
    
    ```java
    call AppConfig.memberService
    call AppConfig.memberRepository
    call AppConfig.memberRepository
    call AppConfig.orderService
    call AppConfig.memberRepository
    
    bean = class hello.core.AppConfig
    ```
    
    - `SpringCGLIB` 이 붙지 않은 것을 보니, 스프링이 CGLIB를 이용하지 않고 순수하게 자바 코드에 의해 스프링 빈으로 등록된 것을 알 수 있다.
    - **memberRepository가 3번 호출된다.**
        - 스프링 빈으로 1개의 memberRepository가 생성되어야 스프링이 싱글톤 레지스트리를 적용할 텐데 그러지 못했다.
- 테스트 결과 - `configurationTest`
    
    ```java
    memberService -> memberRepository = hello.core.member.MemoryMemberRepository@5e3f861
    orderService -> memberRepository = hello.core.member.MemoryMemberRepository@2fb0623e
    memberRepository = hello.core.member.MemoryMemberRepository@49b2a47d
    ```
    
    - 3개의 memberRepository는 각각의 MemoryMemberRepository를 갖고 있다.

### **정리**

- **@Configuration이 없을 때** → 싱글톤 X, MemberRepository처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때, **`싱글톤을 보장하지 않음.`**
- **@Configuration이 있을 때** → 스프링이 바이트코드 조작 라이브러리인 `CGLIB`를 이용하여 원래의 클래스(AppConfig)를 `상속`받은 **임의의 클래스(AppConfig@CGLIB)**를 만들어 스프링 컨테이너에 빈으로 등록한다. → `싱글톤 보장!`