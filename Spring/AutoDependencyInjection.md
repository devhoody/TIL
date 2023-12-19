- [의존관계 자동 주입](#의존관계-자동-주입)
  - [Annotation 직접 만들기](#annotation-직접-만들기)
  - [조회한 빈이 모두 필요할 때→ List, Map](#조회한-빈이-모두-필요할-때-list-map)
  - [자동, 수동의 올바른 실무 운영 기준](#자동-수동의-올바른-실무-운영-기준)
    - [그럼 수동 빈 등록은 언제?](#그럼-수동-빈-등록은-언제)

# 의존관계 자동 주입
## Annotation 직접 만들기

- Annotation을 직접 만드는 이유
    - `@Qualifier("mainDiscountPolicy")` 같이 문자로 적으면 **컴파일 시 타입 체크가 안된다.**
- Annotation 직접 만드는 방법
    
    ```java
    @Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Inherited
    @Documented
    @Qualifier("mainDiscountPolicy")
    public @interface MainDiscountPolicy {
    }
    ```
    
- 기존 - RateDiscountPolicy
    
    ```java
    @Component
    @Qualifier("mainDiscountPolicy")
    public class RateDiscountPolicy implements DiscountPolicy{
    
        private final int discountPercent = 10;
    
        @Override
        public int discount(Member member, int price) {
            if(member.getGrade() == Grade.VIP) {
                return price * discountPercent / 100;
            } else
                return 0;
        }
    } 
    ```
    
- Annotation 적용 후
    
    ```java
    @Component
    **@MainDiscountPolicy // 직접 만든 Annotation 사용**
    public class RateDiscountPolicy implements DiscountPolicy {
    
    		private final int discountPercent = 10;
    
        @Override
        public int discount(Member member, int price) {
            if(member.getGrade() == Grade.VIP) {
                return price * discountPercent / 100;
            } else
                return 0;
        }
    }
    ```
    
- 의존 관계 주입에는 타입 앞에 `@직접 생성한 Annotation명` 을 넣으면 된다.
    - 생성자
        
        ```java
        @Autowired
        public OrderServiceImpl(MemberRepository memberRepository,**@MainDiscountPolicy** DiscountPolicy rateDiscountPolicy) {
            this.memberRepository = memberRepository;
            this.discountPolicy = rateDiscountPolicy;
        }
        ```
        

## 조회한 빈이 모두 필요할 때→ List, Map

- 조회한 타입에 해당하는 빈을 다 조회하고 싶다 → List, Map
    - 예를 들어, 할인 서비스를 클라이언트가 fix, rate를 선택해서 받을 수 있도록 한다.
- `AllBeanTest`
    
    ```java
    public class AllBeanTest {
    
        @Test
        void findAllBean() {
            ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);
            DiscountService discountService = ac.getBean(DiscountService.class);
            Member member = new Member(1L, "memberA", Grade.VIP);
            int discountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");
    
            //then
            assertThat(discountService).isInstanceOf(DiscountService.class);
            assertThat(discountPrice).isEqualTo(1000);
    
            int rateDiscountPrice = discountService.discount(member, 20000, "rateDiscountPolicy");
            assertThat(rateDiscountPrice).isEqualTo(2000);
        }
    
        static class DiscountService {
            private final Map<String, DiscountPolicy> policyMap;
            private final List<DiscountPolicy> policies;
    
            @Autowired
            public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
                this.policyMap = policyMap;
                this.policies = policies;
                System.out.println("policyMap = " + policyMap);
                System.out.println("policies = " + policies);
            }
    
            public int discount(Member member, int price, String discountCode) {
                DiscountPolicy discountPolicy = policyMap.get(discountCode);
    
                System.out.println("discountCode = " + discountCode);
                System.out.println("discountPolicy = " + discountPolicy);
    
                return discountPolicy.discount(member,price);
            }
        }
    }
    ```
    
    - Map의 key를 discountCode로 적용함으로써 코드에 맞는 discountPolicy를 꺼내올 수 있다.

## 자동, 수동의 올바른 실무 운영 기준

- `자동 기능을 기본으로 사용하자`
- **자동 빈 등록을 해도 OCP, DIP를 지킬 수 있다.**

### 그럼 수동 빈 등록은 언제?

`기술 로직 빈`

`다형성을 적극 활용하는 업무 로직`

- 애플리케이션에는 크게 업무 로직과 기술 로직으로 나눌 수 있다.
    - **업무 로직 빈**
        - Controller, Service, Repository처럼 **비즈니스의 요구사항을 개발할 때** 필요한 로직
        - 업무 로직은 위의 3단계처럼 패턴이 유사하고 수가 많으므로 자동 기능을 사용하는 것이 좋다.
    - **기술 로직 빈**
        - **기술적인 문제**나 **공통 관심사(AOP)**를 처리할 때 사용된다. 데이터베이스 연결처럼 **업무 로직을 처리하기위한 하부 로직, 공통 로직이 이에 해당한다.**
        - 기술 로직은 애플리케이션에 광범위하게 영향을 끼치고 업무 로직에 비해 수가 적고 눈에 띄지 않아 오류 발생시 찾기 어렵다는 단점이 있다. 그래서 이러한 기술 로직들은 수동 등록을 통해 명확하게 드러내는 것이 좋다.
- 다형성을 적극 활용하는 빈의 경우
    
    ```java
    @Autowired
    public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
        this.policyMap = policyMap;
        this.policies = policies;
        System.out.println("policyMap = " + policyMap);
        System.out.println("policies = " + policies);
    }
    ```
    
    - DiscountService가 의존 관계 자동 주입으로 Map을 주입받는다고 가정하면, 해당 코드에서는 Map에 어떤 빈이 존재하는지 한눈에 들어오지 않는다. 만약, 해당 코드를 내가 아닌 타인에 의해 건네 받게 된다면 코드를 조작하기가 쉽지않다.
    - 따라서, 다형성을 활용하는 빈의 경우에는 **`수동 등록을 적용하여 한 눈에 보기 좋게`** 하는 것이 좋다.
        
        ```java
        @Configuration
         public class DiscountPolicyConfig {
             @Bean
             public DiscountPolicy rateDiscountPolicy() {
                 return new RateDiscountPolicy();
             }
             @Bean
             public DiscountPolicy fixDiscountPolicy() {
                 return new FixDiscountPolicy();
             }
        }
        ```
        
        - 이처럼 수동 등록을 하면, 한눈에 할인 정책에 rate와 fix가 있다는 것을 확인할 수 있다.
        - 만약 자동 등록으로 한눈에 보이게 하고 싶다면, `패키지`를 적극 활용하자!

스프링과 스프링 부트가 자동으로 등록하는 수 많은 빈들은 예외

- 스프링 부트의 경우, DataSource와 같은 데이터베이스 연결에 사용하는 기술 로직을 내부에서 자동으로 등록해준다. 이런 부분은 스프링 부트가 의도한 대로 사용하는 것이 좋다.

