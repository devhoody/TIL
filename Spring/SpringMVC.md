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
    - [@ModelAttribute의 전역화](#modelattribute의-전역화)
  - [PRG Post/Redirect/Get](#prg-postredirectget)
  - [RedirectAttributes](#redirectattributes)
- [HTTP 요청 메세지 - 단순텍스트](#http-요청-메세지---단순텍스트)
  - [HttpEntity](#httpentity)
  - [RequestEntity, ResponseEntity](#requestentity-responseentity)
    - [**RequestEntity**](#requestentity)
    - [**ResponseEntity**](#responseentity)
- [메시지, 국제화](#메시지-국제화)
  - [국제화](#국제화)
- [MVC Validation](#mvc-validation)
  - [V1 - @ModelAttribute, RedirectAttributes, Model](#v1---modelattribute-redirectattributes-model)
    - [errors?](#errors)
  - [V2 - BindingResult](#v2---bindingresult)
    - [FieldError 생성자](#fielderror-생성자)
    - [**ObjectError 생성자**](#objecterror-생성자)
    - [`#fields`](#fields)
  - [V2 - BindingResult2](#v2---bindingresult2)
    - [BindingResult와 Errors](#bindingresult와-errors)
  - [FieldError, ObjectError](#fielderror-objecterror)
    - [FieldError 생성자](#fielderror-생성자-1)
- [파일 업로드](#파일-업로드)
  - [파일명 암호화하기](#파일명-암호화하기)
    - [**원인**](#원인)
    - [**해결방법**](#해결방법)
    - [**결과**](#결과)
- [관련 어노테이션](#관련-어노테이션)
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

### @ModelAttribute의 전역화

- 여러 페이지에서 동일하게 사용되는 객체를 사용하고자 할 때, 컨트롤러 내 메소드마다 객체를 넣어 일일이 만들어주는 번거로움이 발생한다.
- 이를 해결하기 위해서 해당 컨트롤러에 @ModelAttribute를 이용해 메소드를 만들면 이를 컨트롤러 내 모든 메소드에서 사용이 가능하다.

```java
// 자동 ModelAttribute

@ModelAttribute("regions")
public Map<String, String>regions(){
    Map<String, String> regions = new LinkedHashMap<>();
    regions.put("SEOUL", "서울");
    regions.put("BUSAN", "부산");
    regions.put("JEJU", "제주");
    return regions;
}
```

## PRG Post/Redirect/Get

<aside>
💡 Post를 이용해서 기존 return 방식으로 템플릿을 조회할 때, 새로고침을 하면 계속 Post 요청이 실시되는 버그가 발생할 수 있다.

```java
@PostMapping("add")
public String addItemV4(Item item){
    Item save = itemRepository.save(item);
    return "basic/items";
}
```

- 새로고침 → 마지막에 한 행동을 다시 하는 것
</aside>

- 해결방법 → Redirect
    
    ```java
    @PostMapping("add")
    public String addItemV5(Item item){
        Item save = itemRepository.save(item);
        return "redirect:/basic/items/" + item.getId();
    }
    ```
    
    - 마지막 행동을 Get으로 실시하여 새로고침시 Get 요청을 실행함으로써 post 요청을 막을 수 있다.
    - URL에 변수를 사용하는 것의 문제점
        - 하지만 여기서 item.getId()를 이용했는데, 이는 URL인코딩이 안되기 때문에 위험하다.
        - RedirectAttributes 사용

## RedirectAttributes

<aside>
💡 고객이 저장이 된 것인지 안된것인지 확신이 들지 않음. “저장되었습니다.” 멘트를 넣어줌으로써 고객 UX를 개선할 수 있다.

</aside>

- **RedirectAttributes란?**
    - **URL 인코딩**과 **pathVariable, 쿼리 파라미터**를 처리해준다.
- 사용 코드
    
    ```java
    @PostMapping("add")
    public String addItemV5(Item item, RedirectAttributes redirectAttributes){
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true); // 쿼리스트링으로 들어감
    
        return "redirect:/basic/items/{itemId}";
    }
    ```
    
    - 이전에는 item.getId() 변수를 직접 넣어주었지만, redirectAttributes를 이용해서 URL에 pathVariable 처리를 하였다.
    - pathVariable 처리가 안된 값(status)은 쿼리 파라미터로 처리된다.
        - [`http://localhost:8080/basic/items/3?status=true`](http://localhost:8080/basic/items/3?status=true)
    - **item.html**
        
        ```html
        <div class="py-5 text-center">
            <h2>상품 상세</h2>
        </div>
        
        <h2 th:if="${param.status}" th:text="'저장 완료'"></h2>
        ```
        
        - `th:if` : 해당 조건이 참이면 실행
        - `${param.status}`
            - param : 타임리프에서 쿼리 파라미터를 쉽게 조회해주는 기능
        - 이렇게 하면 쿼리 파라미터 속성 중 status가 참일 경우, “저장 완료” 라는 문구가 뜸으로하여 고객 입장에서 저장이 제대로 되었는지 확인이 가능하다.
    
    스프링 MVC 구조 이해를 복습하는 것도 추천함
    
    - 실무자들 중에서도 스프링MVC의 구조를 정확히 이해한 사람이 별로 없음
    - 관련 에러에 대해서 어디가 문제인지 잘 알 수 있음.
    
    이후
    
    - 개발자는 컨트롤러 구현이 20%면 검증이 80%의 업무량을 차지한다.
    - 예외 처리가 진짜 복잡하다.

# HTTP 요청 메세지 - 단순텍스트

## HttpEntity

- **HttpEntity란?**
    - Http를 통해 전달되는 **header, body 정보를 담은 객체**
- **HttpEntity 사용 이유**
    - HTTP header, body 정보를 편리하게 조회한다.
- **HttpEntity 기능**
    - 메시지 바디 정보를 직접 조회 → `getBody()`
    - 요청 파라미터를 조회하는 기능과 관계가 없다. `@RequestParam`, `@PathVariable` x
    - **응답에도 사용이 가능하다.**
        - 메시지 바디에 정보를 직접 반환
        - 헤더 정보 포함 가능
        - view 조회 x

## RequestEntity, ResponseEntity

- HttpEntity를 상속받아 추가적인 기능을 제공하는 RequestEntity와 ResponseEntity가 있다.

### **RequestEntity**

- `HttpMethod`, `url 정보`가 추가, 요청에서 사용

### **ResponseEntity**

- 응답에서 http상태 코드를 설정할 수 있다.
- 예시
    
    ```java
    ResponseEntity.ok()
    		  .header(HttpHeaders.CONTENT_DISPOSITION, contentDisposition)
    		  .body(resource);
    ```


# 메시지, 국제화

> 기획자가 기존의 상품명이라는 이름을 상품이름으로 변경해달라고 요청한다면? 그 양이 수천개라면 어떻게 변경할까?
> 
- 메시지 기능 : 다양한 메시지를 한 곳에서 관리하도록 하는 기능

**message.properties**

```xml
label.item=상품
label.item.id=상품 ID 
label.item.itemName=상품명
label.item.price=가격
label.item.quantity=수량
```

## 국제화

메시지 파일을 각 나라별로 결도로 관리하는 것

**message_en.properties**

```xml
label.item=Item
label.item.id=Item ID
label.item.itemName=Item Name
label.item.price=price
label.item.quantity=quantity
```

**한국인지 영어권인지 인식하는 방법**

1. HTTP : `‘accept-language’` 헤더 값 이용
2. 사용자가 직접 페이지에서 언어 선택 후 `쿠키` 이용

스프링은 자동으로 기본적인 메시지와 국제화 기능을 지원한다.

**스프링 부트 메시지 소스 설정**

- **application.properties**
    
    `spring.messages.basename=messages,config.i18n.messages`
    
- 스프링 부트 메시지 기본값
    
    `spring.messages.basename=messages`

# MVC Validation

- 개발자에게 구현과 검증의 비중을 따지자면 30 : 70 으로 생각된다. 그만큼 검증은 서버뿐만아니라 클라이언트에게도 직접적인 영향을 주는 대단히 중요한 기능이기에 확실하게 체득을 할 필요가 있다.

## V1 - @ModelAttribute, RedirectAttributes, Model

- 오류처리 메시지를 Map에 넣어 처리한다.

```java
@PostMapping("/add")
public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes, Model model) {

    //검증  오류 결과를 보관
    Map<String, String> errors = new HashMap<>();

    //검증 로직
    if(!StringUtils.hasText(item.getItemName())){
        errors.put("itemName", "상품 이름은 필수입니다.");
    }
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        errors.put("price", "가격은 1,000 ~ 1,000,000 까지 허용합니다.");
    }
    if(item.getPrice() != null && item.getQuantity() != null){
        int result = item.getQuantity() * item.getPrice();
        if(result < 10000) {
            errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 값 = " + result);
        }
    }

    //검증에 실패하면 다시 입력 폼으로
    if(!errors.isEmpty()){
        log.info("errors ={}", errors);
        model.addAttribute("errors", errors);
        return "validation/v1/addForm";
    }

    //성공 로직
    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v1/items/{itemId}";
}
```

HTML

```html
<form action="item.html" th:action th:object="${item}" method="post">
    <div th:if="${errors?.containsKey('globalError')}">
        <p class="field-error" th:text="${errors['globalError']}">전체 오류 메시지</p>
    </div>

    <div>
        <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
        <input type="text" id="itemName" th:field="*{itemName}"
               th:class="${errors?.containsKey('itemName')} ? 'filed-error form-control' : 'form-control'"
               class="form-control" placeholder="이름을 입력하세요">
        <div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">
            상품명 오류
        </div>
    </div>
</form>
```

### errors?

- null일 경우, `nullPorintException`이 발생하는 대신 ‘null’을 반환하는 문법이다.

- **V1의 문제점**
    - 하지만 뷰 템플릿에서 같은 필드명은 중복해서 작성해야한다.
    - Integer에 문자열을 담을 수가 없어 고객이 입력한 문제가 사라지고 고객 입장에서 무엇을 입력했는지 알 수가 없다.
    - 결국 고객이 입력한 값도 어디선가 별도로 관리가 되어야 한다.

## V2 - BindingResult

- BindingResult 이용
- 자동으로 view에 넘어감 → *Model에 넣어주지않아도 된다.*
- FieldError, ObjectError를 이용해서 오류 메시지를 처리한다.
- 결
- **@ModelAttribute 다음에 BindingResult가 바로 붙어있어야 한다. →** 그래야 ModelAttribute에 있는 오브젝트를 인식하고 사용할 수 있다.

### FieldError 생성자

`public FieldError(String objectName, String field, String defaultMessage) {}`

objectName : @ModelAttribute명

field : 오류 발생 필드명

defaultMessage : 기본 오류 메시지

### **ObjectError 생성자**

 `public ObjectError(String objectName, String defaultMessage) {}`

- ObjectError : 글로벌로 나타나는 에러를 담는 객체
- objectName : @ModelAttribute명
- defaultMessage : 오류 기본 메시지

### `#fields`

- BindingResult가 제공하는 검증 오류가 접근할 수 있다.
- `th:errors` : 필드명에 에러가 발생하면 태그를 출력한다. **th:if의 편의버전**
- `th:errorclass` : th:field에서 지정한 필드에 에러가 있으면 클래스를 추가시켜준다.

```html
<!-- BindingResult 사용전 -->
<div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">
    상품명 오류
</div>

<!-- BindingResult 사용후 -->
<div class="field-error" th:errors="*{itemName}">
    상품명 오류
</div>
```

**HTML**

```html
<!-- BindingResult 사용전 -->
<input type="text" id="itemName" th:field="*{itemName}"
                   th:class="${errors?.containsKey('itemName')} ? 'filed-error form-control' : 'form-control'"
                   class="form-control" placeholder="이름을 입력하세요">

<!-- BindingResult 사용후 -->
<input type="text" id="itemName" th:field="*{itemName}"
	     th:errorclass="field-error" class="form-control" placeholder="이름을 입력하세요">
```

## V2 - BindingResult2

- 스프링이 제공하는 검증 오류를 보관하는 객체가 `BiningResult`
- `BindingResult` 가 있으면 `@ModelAttribute` 에 데이터 바인딩 시 오류가 발생해도 컨트롤러가 호출된다!
    - `BindingResult`가 없으면 바로 오류 페이지를 출력한다.

**BindingResult에 검증 오류를 적용하는 3가지 방법**

- `@ModelAttribute` 의 객체에 타입 오류 등으로 바인딩이 실패하는 경우 스프링이 `FieldError` 생성해서 `BindingResult` 에 넣어준다.
- 개발자가 직접 넣어준다.
- `**‘Validator’**` 사용

### BindingResult와 Errors

- 기능 수 : BindingResult > Errors
- 기능은 유사하나 대체적으로 Errors보다 **BindingResult를 주로 사용한다.**

## FieldError, ObjectError

> 사용자가 입력한 값을 오류가 발생해도 그대로 남겨두고 싶다면?
> 

### FieldError 생성자

```java
public FieldError(String objectName, String field, String defaultMessage);

public FieldError(String objectName, String field, @Nullable Object
rejectedValue, boolean bindingFailure, @Nullable String[] codes, @Nullable
Object[] arguments, @Nullable String defaultMessage)
```

objectName : @ModelAttribute

`field` : 오류가 발생한 필드명

`rejectedValue` : 사용자가 입력한 값

`bindingFailure` : 타입과 같은 바인딩 차이인지 검증 오류인지 구분하기위한 값

`codes` : 메시지 코드

`arguments` : 메시지에서 사용하는 arguments

`defaultMessage` : 기본 오류 메시지

**th:field 기능**

- th:field는 정상 작동 시 `Model`에 있는 값을 사용하고, 오류 발생 시 `FieldError`에서 값을 사용한다.

**스프링의 바인딩 오류처리**

- 스프링은 타입 불일치같은 바인딩 오류시 자동으로 fieldError을 생성하여 사용자 입력 값을 넣는다.
- 그 후, BindingResult에 값을 담아 컨트롤러를 호출한다.
- 따라서, 바인딩 오류에도 사용자 입력 값이 그대로 유지될 수 있다.
- ***바인딩 오류 → fieldError생성 및 입력값 주입 → BindingResult에 값 담기 → 컨트롤러 호출***
  

# 파일 업로드

## 파일명 암호화하기

> 프로필 사진 업로드 시 SQL에 파일명이 한글로 제대로 저장은 되나, 페이지에 사진이 제대로 출력되지 않았다.
> 

### **원인**

- 프로필 사진 파일의 파일명이 한글일 때, HTTP를 통해 전달된 Response의 Body 내 `path`에서 **한글이 깨져서 전송**되었다.
    
    ```json
    {
        "timestamp": "2024-01-23T11:40:58.168+00:00",
        "status": 404,
        "error": "Not Found",
        "message": "No message available",
        "path": "/css/image/user/%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%80%E1%85%B3%E1%84%85%E1%85%A2%E1%84%86%E1%85%A5%E1%84%89%E1%85%B3.png"
    }
    ```
    

### **해결방법**

- `UUID`를 이용하여 저장할 때, 파일명을 `암호화`시켜 한글 깨짐 현상을 방지한다.
- **파일명을 암호화하는 메소드(getUuid)**를 만든다.
    
    ```java
    //UUID생성
    public static String getUuid(){
    	return UUID.randomUUID().toString().replaceAll("-", "");
    }
    ```
    
- 파일명은 ****`UTF-8`**로 변환**한 후, `UUID`를 이용하여 파일명을 암호화한다.
    
    ```java
    // 파일명 인코딩
    String fileName = img.getOriginalFilename();
    String encodedFileName = new String(fileName.getBytes("8859_1"),"UTF-8"); //UTF-8로 변환
    
    String ext = encodedFileName.substring(encodedFileName.lastIndexOf(".")); //확장명 얻음
    String savedFileName = getUuid() + ext; //암호화된 파일명
    log.info("savedFileName= {}", savedFileName);
    
    //파일명저장 
    user.setProfileImg(savedFileName); 
    ```
    

### **결과**

- 다음 결과들에서 알 수 있듯이 파일명이 암호화되어 저장되는 것을 알 수 있다.
- **log 결과**
    
    ```java
    INFO 90866 --- [nio-8000-exec-1] c.m.web.controller.api.UserController    : savedFileName= 464fc976e21b4d67a208279eda26b9bd.png
    ```
    
- **SQL 테이블 결과**
    
    <img width="292" alt="스크린샷 2024-01-23 오후 9 01 26" src="https://github.com/devhoody/TIL/assets/124743189/1646f72a-8483-4427-adb7-bceacba05b6b">

- **path**
    
    `"/css/image/user/464fc976e21b4d67a208279eda26b9bd.png”`
    
- 등록 후 새로고침해도 사진이 제대로 나온다!

    <img width="311" alt="스크린샷 2024-01-23 오후 9 03 42" src="https://github.com/devhoody/TIL/assets/124743189/88f9d480-0851-4422-95fe-c6d824562ece">

- 참고로 마우스 오른쪽 버튼 클릭 후 `‘이미지를 다른 이름으로 저장’`하면 내가 암호화했던 그 파일명이 나오는 것을 알 수 있다.
    
<img width="589" alt="스크린샷 2024-01-23 오후 9 05 31" src="https://github.com/devhoody/TIL/assets/124743189/af64ef27-775f-421c-930b-1b499a24a3a4">

- 구글이나 무료 배포 사진을 다운받을 때, 가끔 이렇게 암호화된 파일명이 나오는 것을 보았는데, 그 이유를 이제야 알 것 같다. 또 하나 배웠다.



# 관련 어노테이션 
## @RequiredArgsConstructor

- `final` 이 붙은 멤버변수만 사용해서 생성자를 자동으로 만들어준다.

```java
@RequiredArgsConstructor
public class BasicItemController {

    private final ItemRepository itemRepository;
```

- `final` 이 붙은 ItemRepository의 생성자를 자동으로 생성해준다.
- 스프링에서는 생성자가 1개일 때, 자동으로 해당 생성자에 Autowired로 의존관계를 주입한다.
- 따라서 final 키워드를 빼지 말것!

## @PostConstruct

- 테스트용 데이터 추가를 위해 사용하는 어노테이션
- 해당 빈의 의존관계가 모두 주입된 후, 초기화 용도로 사용된다.

```java
@PostConstruct
public void init(){
    itemRepository.save(new Item("itemA", 10000, 5));
    itemRepository.save(new Item("itemB", 20000, 10));
}
```