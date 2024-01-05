- [컴포넌트 스캔과 의존관계 자동 주입 시작하기](#컴포넌트-스캔과-의존관계-자동-주입-시작하기)
  - [탐색 위치와 기본 위치 저장](#탐색-위치와-기본-위치-저장)
    - [컴포넌트 스캔의 기본 대상](#컴포넌트-스캔의-기본-대상)


# 컴포넌트 스캔과 의존관계 자동 주입 시작하기

```java
@Component
public class MemberServiceImpl implements MemberService{

    private final MemberRepository memberRepository;

    @Autowired // = ac.getBean("memberRepository", MemberRepository.class)
    // 생성자 주입
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
```

- **컴포넌트 스캔이란?**
    - 설정 정보 없이 자동으로 스프링 빈을 등록하는 기능
- **컴포넌트 스캔이 필요한 이유**
    - 기존에는 자바 코드나 xml을 이용하여 직접 스프링 빈을 등록하는 과정을 거쳤다.
    - 직접 스프링 빈을 등록하는 과정 없이 실행
- `@Component`가 붙은 클래스를 스캔해서 스프링 빈으로 등록
- 자동으로 스프링이 빈을 등록하므로 **의존관계 주입은 @Autowired로 자동으로 주입시켜주어야 한다.**
    - @
- 스프링 빈의 기본 이름은 클래스명을 사용하되 **맨 앞글자만 소문자**로 사용한다.
    - 빈 이름 기본 전략 : `MemberServiceImpl` → `memberServiceImpl`
- `@Autowired`
    - 타입으로 검색하여 자동으로 의존 관계를 주입. → `ac.getBean("memberRepository", MemberRepository.class)`와 같음

## 탐색 위치와 기본 위치 저장

- 탐색의 root가 되는 위치를 지정해 줄 수 있다.
- **탐색 위치를 설정해 주는 이유**
    - 탐색 위치를 설정해주지 않으면 스프링이 라이브러리 포함 모든 파일을 탐색하게된다.
- **탐색 위치 설정 방법**
    
    ```java
    @ComponentScan(
    	  basePackages = "hello.core", // 탐색 패키지 설정
    	  basePackageClasses = MemberService.class, // 탐색 클래스 설정
    	  excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
    )
    ```
    
    - `basePackages`와 `basePackageClasses`를 이용하여 각각 탐색위치를 패키지 또는 클래스를 지정한다.
- 탐색위치 Default
    - `@ComponentScan`이 등록된 설정 정보 클래스의 패키지로부터 하위를 탐색
        
        ```java
        package hello.core;
        
        ...
        
        @Configuration
        @ComponentScan(
                excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
        )
        public class AutoAppConfig {
        
        }
        ```
        
        - 설정 클래스인 `AutoAppConfig`의 패키지가 `hello.core`이므로 해당 프로젝트에서는 **hello.core 이하의 패키지를 탐색**한다.
- 권장하는 방법
    - 패키지 위치를 따로 지정해주지않고, **설정 정보 클래스의 위치를 프로젝트의 최상단**에 두는 것이다.

### 컴포넌트 스캔의 기본 대상

- `@Component`
- `@Controller` : 스프링 MVC 컨트롤러로 인식
- `@Service`
- `@Repository` : 스프링 데이터 접근 계층으로 인식하고 데이터 계층의 예외를 스프링 예외로 전환
- `@Configuration` : 스프링 설정 정보를 인식하고 스프링 빈이 싱글톤을 유지하도록 처리

> 애노테이션은 상속관계가 없다. 그래서 애노테이션이 다른 것을 들고있는 것은 자바가 지원하는 기능이 아니라 **스프링이 지원하는 기능이다.**
>