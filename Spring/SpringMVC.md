- [스프링 MVC - 기본 기능](#스프링-mvc---기본-기능)
- [로깅](#로깅)
    - [로그 사용시 장점](#로그-사용시-장점)
  - [로깅 라이브러리](#로깅-라이브러리)
- [요청 매핑](#요청-매핑)
  - [RequestMapping](#requestmapping)
    - [@PathVariable](#pathvariable)
  - [미디어 타입 조건 매핑](#미디어-타입-조건-매핑)
    - [HTTP 요청 Content-Type, consume](#http-요청-content-type-consume)
    - [HTTP 요청 Accept, produce](#http-요청-accept-produce)
  - [HTTP 요청 - 기본, 헤더 조회](#http-요청---기본-헤더-조회)
- [HTTP **요청 파라미터**](#http-요청-파라미터)
  - [**요청 파라미터** - **쿼리 파라미터**, HTML Form](#요청-파라미터---쿼리-파라미터-html-form)
    - [@RequestParam](#requestparam)
    - [파라미터 필수 여부  - required](#파라미터-필수-여부----required)
    - [빈 문자열 처리 및 기본값 등록 - defaultValue](#빈-문자열-처리-및-기본값-등록---defaultvalue)
    - [파라미터를 Map으로 조회하기 - **requestParamMap**](#파라미터를-map으로-조회하기---requestparammap)
  - [@ModelAttribute](#modelattribute)
- [@RequiredArgsConstructor](#requiredargsconstructor)
- [@PostConstruct](#postconstruct)


# 스프링 MVC - 기본 기능


# 로깅

- `System.out.println`을 이용해서 정보를 출력했는데, 실무에서는 로깅 라이브러리를 사용해서 로그를 출력한다.
- 로깅을 사용하는 이유
    - log를 통해 요청된 시간, 실행 프로세스, 목적과 함께 로그를 남기기 위해서 사용한다.
    - 서버의 목적에 맞춰서 로그를 남긴다. sout은 목적에 상관없이 무조건 출력된다.

- 출력 순서 : **시간, 로그 레벨, 프로세스 ID, 쓰레드명, 클래스명, 로그 메시지**
- 사용 방법
    - properties나 yml에 logging 레벨을 설정한다.
        - properties의 경우
            
            ```xml
            #전체 로그 레벨 설정(기본 info)
            logging.level.root=info
            
            #com.hocafe 패키지와 그 하위 로그 레벨 설정
            logging.level.hello.springmvc=debug
            ```
            
        - yml의 경우
            
            ```xml
            logging:
              level:
                root: info
                com.hocafe: debug
            ```
            
    - 로그가 필요한 클래스에 LoggerFactory를 이용하거나 lombok Annotation인 `@Slf4j`을 입력한 후, `log.` 을 이용하여 로그를 출력하면 된다.
        
        ```java
        @Slf4j // Slf4j 이용 
        @Controller
        public class MemberController {
        	// LoggerFactory 이용 
        	private final Logger log = LoggerFactory.getLogger(getClass());
        	
          public String reg() {
              log.info("member reg");
              return "/member/reg";
          }
        
        	...
        ```
        
    - 올바른 출력 방법
        - 출력방법에는 `+ 를 이용하는 방법`과 `{}을 이용하는 방법`이 있다. **{} 방법을 추천하는데, 그 이유는 +의 경우, 로그 출력 시 연산을 해야하므로 불필요한 메모리가 사용된다. 이를 방지하기위해서 {}를 사용하는 것이 좋다.**
        
        ```java
        // {}을 이용한 방법
        log.info("회원가입 완료 이름 : {}", member.getName());
        
        // {}을 이용한 방법 - 여러 값 입력 
        log.info("회원가입 완료 이름 : {}, 나이 : {} ", name, age);
        
        // +를 이용한 방법
        log.info("회원가입 완료 이름 : " + member.getName());
        ```
        

### 로그 사용시 장점

1. **쓰레드 정보, 클래스 이름 같은 부가 정보**를 볼 수 있고, **출력 모양을 조정**할 수 있다.
2. 로그 레벨에 따라 개발 또는 운영 서버 등 **상황에 맞게 출력 조정이 가능**하다.
3. 파일이나 네트워크 등, **로그를 별도의 위치에 남길 수 있다.** 파일의 경우, 일별, 특정 용량에 따른 로그 분할도 가능하다.
4. 성능도 System.out 보다 좋다. (내부 버퍼링, 멀티 쓰레드 등…)

## 로깅 라이브러리

- 스프링 부트 라이브러리 안에 로깅 라이브러리가 포함되어 있다.
    - `SLF4J` ← 인터페이스
    - `Logback` ← 실제 구현체
- 로그 레벨
    - `trace → debug → info → warn → error`
    - 서버 상황에 맞춰서 레벨을 설정한다.
        - **개발 단위 : debug**, **운영서버 : info**

# 요청 매핑

## RequestMapping

- 입력한 url에 매핑하여 컨트롤러를 실행시켜준다.
- HTTP 메서드를 모두 허용한다.

### @PathVariable

- RequestParam은 쿼리스트링 값을 받는 반면, PathVariable은 경로 변수를 이용하여 매개 변수 값을 받을 수 있다.

```java

@GetMapping("/users/{userId}")
public String mappingPath(@PathVariable("userId") String data) {
     log.info("mappingPath userId={}", data);
     return "ok";
 }
```

- 여러 매개 변수도 받을 수 있다.

```java
@GetMapping("/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable("userId") String userId,
													@PathVariable("orderId") String orderId) {
     log.info("mappingPath userId={}, orderId={}", userId, orderId);
     return "ok";
 }
```

## 미디어 타입 조건 매핑

### HTTP 요청 Content-Type, consume

- HTTP 요청 시 header의 정보 중, 설정한 Content-Type과 일치할 경우 Mapping한다.
    
    ```java
    @PostMapping(value = "/mapping-consume", consumes = "application/json")
     public String mappingConsumes() {
         log.info("mappingConsumes");
         return "ok";
     }
    ```

### HTTP 요청 Accept, produce

- Http 요청 시 header의 정보 중, 클라이언트가 받을 content-type인 Accept에 따라 Mapping을 시킨다.
    
    ```java
    @PostMapping(value = "/mapping-produce", produces = "text/html")
    public String mappingProduces() {
         log.info("mappingProduces");
         return "ok";
    }
    ```

## HTTP 요청 - 기본, 헤더 조회

- 애노테이션 기반의 스프링 컨트롤러는 다양한 파라미터를 지원한다.
- header정보 조회
    
    ```java
    @RequestMapping("/headers")
    public String headers(HttpServletRequest request,
                          HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap<String, String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false) String cookie
                          ){
    
        log.info("request={}", request);
        log.info("response={}", response);
        log.info("httpMethod={}", httpMethod); // HTTP 메소드
        log.info("locale={}", locale); // 언어
        log.info("headerMap={}", headerMap); // content-type 등..
        log.info("header host={}", host); // host url
        log.info("myCookie={}", cookie); // cookie 정보
    
        return "ok";
    }
    ```
    
    - header에 담긴 내용들을 조회할 수 있다.
    

# HTTP **요청 파라미터**

- 클라이언트 → 서버로의 요청 데이터 전송 방법 3가지
    1. **GET- 쿼리 파라미터**
        - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
        - 예) 검색, 필터, 페이징등에서 많이 사용하는 방식
    2. **html form**
        - content-type : application/x-www-form-urlencoded
        - 예) 회원 가입, 상품 주문, HTML Form 사용
    3. **HTTP message body** 
        - HTTP API에서 주로 사용, JSON, XML, TEXT
        - 데이터 형식은 주로 JSON을 사용한다.

## **요청 파라미터** - **쿼리 파라미터**, HTML Form

### @RequestParam

- 파라미터 이름으로 바인딩 한다.

```java
// reg?name=hi?age=30
@PostMapping("reg")
  public String reg(@RequestParam("name") String name,
                    @RequestParam("age") Long age){
```

### 파라미터 필수 여부  - required

- 만약 파라미터 값이 필수가 아닐 경우, required=false로 처리할 수 있다.
    
    ```java
    // reg?name=hi
    @PostMapping("reg")
      public String reg(@RequestParam("name") String name,
                        @RequestParam(value = "age", required = false) Long age){
    ```
    
    - 여기서 만약 required = true일 경우, Long이 아닌 기본형인 int나 long이면 null값이 발생하므로  오류발생.
        - `Rapper클래스인 Integer나 Long`로 변경하거나 `required = false`을 이용하여 처리하자.

### 빈 문자열 처리 및 기본값 등록 - defaultValue

- 쿼리 파라미터 값에 아무것도 작성하지 않을 경우, 빈 문자열이 전송될 가능성이 있다. 이를 방지하기 위해 defaultValue를 이용하자.
    
    ```java
    // reg?name= => name에 빈문자열 전송
    @PostMapping("reg")
      public String reg(@RequestParam(value = "name", defaultValue = "guest") String name,
                        @RequestParam(value = "age") Long age){
    ```
    

### 파라미터를 Map으로 조회하기 - **requestParamMap**

- 파라미터 값을 따로 받지 않고 Map을 이용하여 받을 수 있다.
    
    ```java
    // reg?name=hi?age=30
    @PostMapping("reg")
      public String reg(@RequestParam Map<String, String> paramMap){
    ```
    

## @ModelAttribute

- 파라미터를 @RequestParam으로 받아서 사용했지만, 직접 객체에 입력하여 중간 단계를 제거할 수 있다.
    
    ```java
    // reg?name=hi?age=30
    @PostMapping("reg")
      public String reg(@ModelAttribute Member member){
    ```
    
    - 이 때, Member 객체에는 name, age 속성이 존재해야한다.
- @ModelAttibute 작동  방식
    - Member 객체 생성
    - 요청 파라미터 이름(name, age)로 Member객체의 프로퍼티를 찾는다. 해당 객체의 Setter를 이용하여 값을 주입한다.
        - 프로퍼티란? - getName, setName 처럼 Getter, Setter 를 말한다.

# @RequiredArgsConstructor

- `final` 이 붙은 멤버변수만 사용해서 생성자를 자동으로 만들어준다.

```java
@RequiredArgsConstructor
public class BasicItemController {

    private final ItemRepository itemRepository;
```

- `final` 이 붙은 ItemRepository의 생성자를 자동으로 생성해준다.
- 스프링에서는 생성자가 1개일 때, 자동으로 해당 생성자에 Autowired로 의존관계를 주입한다.
- 따라서 final 키워드를 빼지 말것!

# @PostConstruct

- 테스트용 데이터 추가를 위해 사용하는 어노테이션
- 해당 빈의 의존관계가 모두 주입된 후, 초기화 용도로 사용된다.

```java
@PostConstruct
public void init(){
    itemRepository.save(new Item("itemA", 10000, 5));
    itemRepository.save(new Item("itemB", 20000, 10));
}
```