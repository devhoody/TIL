- [Spring boot](#spring-boot)
  - [Spring boot 프로젝트 생성](#spring-boot-프로젝트-생성)
    - [서버 자동 재시작](#서버-자동-재시작)
  - [임베디드 톰캣](#임베디드-톰캣)
  - [컨트롤러 만들기](#컨트롤러-만들기)
    - [경로 설정에 따른 패키지, 클래스 , 메소드 설정하기](#경로-설정에-따른-패키지-클래스--메소드-설정하기)
  - [view 만들기](#view-만들기)
  - [자동으로 entity 메소드 만들기](#자동으로-entity-메소드-만들기)
    - [mybatis in spring stater](#mybatis-in-spring-stater)
    - [자동 Boilerplate code 추가 -  Lombok 라이브러리](#자동-boilerplate-code-추가----lombok-라이브러리)
    - [Builder 사용하기](#builder-사용하기)
  - [menuview 만들기](#menuview-만들기)
    - [MenuView Entity만들기](#menuview-entity만들기)
  - [입력받은 데이터를 저장하기](#입력받은-데이터를-저장하기)
  - [이미지 입력하기](#이미지-입력하기)
    - [enctype](#enctype)
    - [파일 크기 설정 방법](#파일-크기-설정-방법)
    - [파일 저장 및 저장 위치 지정](#파일-저장-및-저장-위치-지정)
  - [Properties와 yml](#properties와-yml)
  - [SSR, CSR](#ssr-csr)


# Spring boot

- SpringBoot란?
    - 자동으로 라이브러리를 관리해주거나 스프링과 관련된 설정을 해주는 프레임워크
- 사용하는 이유
    - **기능을 추가할 때마다 버전관리, 설정을 매번 해야하는 불편함**이 생겨 이를 해결하기 위함.
    - 웹서버가 내장되어있어 독립적으로 실행이 가능
- 설치방법
    - [Spring.io](http://Spring.io) 접속 → project → Spring Tools4 → 자기에 맞는 버전 설치



## Spring boot 프로젝트 생성

- 초기설정
    - jar
        - `jar  - 내장 톰캣` 사용
        - `war - 외장 톰캣` 사용
        
- DB설정으로 인해 웹서버 시작이 안될때
    - maven 라이브러리 설정에서 starter관련 dependency를 주석처리(web, test 이외 전부)

### 서버 자동 재시작

- *project 오른쪽클릭 → spring → devtools* 클릭
- pom.xml에 `devtools dependency` 추가 된것 확인
    
    ```java
    <dependency>
    	<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-devtools</artifactId>
    </dependency>
    ```
    

## 임베디드 톰캣

- 임베디드 톰캣이란?
    - 프레임워크에 속해있는 내장 톰캣
- Why
    - WAS인 톰캣이 기존에는 페이지를 생성하고 띄우는 용도였다면, 임베디드 톰캣은 단순히 화면을 띄우는 용도로 사용하기에 라이브러리로 설치가 가능하다.

## 컨트롤러 만들기

- 클라이언트와 관리자용 MenuController를 각각만든다.
    - 각각에 맞는 패키지, 클래스, 메소드를 유의하여 생성한다.

### 경로 설정에 따른 패키지, 클래스 , 메소드 설정하기

- 관리자 페이지의 경우 /admin/menu/list 이므로, 페이지의 url이 늘어난다. 이때, 패키지와 클래스, 메소드를 이용해서 구분할 수 있다.
- `admin - 패키지`
- `menu - 클래스`
- `list - 메소드`

```java
**package kr.co.rland.web.controller.admin;**

@Controller("adminMenuController")
@RequestMapping("/admin/menu")
**public class MenuController {**

	@ResponseBody
	**@RequestMapping("list")**
	public String list() {
	
		return "- 관리자메뉴리스트";
	}
```

## view 만들기

- 404오류가 나올때 확인해야 할 것
    - url 연결 문제
    - view가 없을때 문제

## 자동으로 entity 메소드 만들기

- Spring stater에 있는 mybatis는 자동 설정까지 포함된다.
- 따라서 Annotation만으로 기존의 Mapper 세팅이 가능하다.
    - **@Mapper**
        - IOc 컨테이너에 Mapper구현체를 담아준다.
    - **@Select**
        - SQL문을 자동으로 인식하게 만들어준다.
    
    ```java
    @Mapper
    public interface MenuRepository {
    	
    	@Select("select * from menu")
    	List<Menu> findAll();
    	
    }
    ```
    

### mybatis in spring stater

- starter안에 있는 라이브러리는 설정까지 포함되어 있다.

- ‘url’ - attribute 오류 발생
    - 원인
        - mybatis가 연결할 **DB서버가 설정되어 있지 않음.**
    - 해결방법
        - `application.properties`에서 연결 DB관련 설정 추가
        
        ```java
        spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
        spring.datasource.url=jdbc:mariadb://db.newlecture.com:3306/rlanddb?serverTimezone=Asia/Seoul&characterEncoding=UTF-8
        spring.datasource.username=rland
        spring.datasource.password=20231110
        ```
        
    - application properties
        
        [Common Application Properties](https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html)
        
- 오류발생  - `Property or field 'korName' cannot be found on object of type 'kr.co.rland.web.entity.Menu' - maybe not public or not valid?`
    - 의미
        - 진짜 property나 field가 없다는게 아니고 getter가 없다는 뜻
    - 해결방법
        - Getter 직접 추가
        - **Lombok라이브러리**를 이용한 **Boilerplate code** 자동 추가

### 자동 Boilerplate code 추가 -  Lombok 라이브러리

- **Boilerplate code란?**
    - 최소한의 필수적인 기본 설정 코드
- **Lombok이란?**
    - 기본적인 설정 코드인 `getter/setter, Tostring, constuctor` 등을  자동으로 추가해주는 라이브러리
- 설치방법
    - 라이브러리설치
    - lombok 수동 설치 - 맥북용
        
        [[macOS] lombok 다운로드 및 설치](https://hyuneexpress.tistory.com/entry/macOS-lombok롬복-다운로드-및-설치)
        

### Builder 사용하기

- Builder란?
    - 객체를 생성하는데 도움을 주는 Lombok의 Annotation
- Builder이유?
    - 기존에 객체 생성하는 방법 이외에 간편하게 데이터를 추가할 수 있다.
- Builder방법
    - 데이터를 삽입하고자 하는 클래스에서 해당 Annotation 추가
        
        ```java
        @Getter
        @Setter
        @AllArgsConstructor
        @NoArgsConstructor
        @ToString
        @Builder
        public class Menu {
        ```
        
    - 객체 생성 방법
        - `.Builder()`와 `.build()`를 이용하여 생성한다.
        
        ```java
        public String list(Model model) {
        		
        		model.addAttribute("list", service.getList());
        
        		Menu menu = Menu
        				.builder()
        				.id(1L)
        				.korName("아메리카노")
        				.build();
        
        		System.out.println(menu);
        
        		return "menu/list"; // template에서 list.html을 찾음.
        	}
        ```

## menuview 만들기

### MenuView Entity만들기

- `@AllArgsConstructor@NoArgsConstructor@Getter@Setter@ToString` ⇒ `@Data` 에 포함되어있는 것!
- *Entity는 절대로 상속받지 말것!*

```java
private long id;
    private String korName;
    private String engName;
    private int price;
    private String img;
    private Date regDate;
    private int hit;
    private long memberId;
    private int likeCount;
```

## 입력받은 데이터를 저장하기

- 업무로직은 내가 일하는 내용이니까 **메서드명을 쓰는 용어 그대로 사용**한다.
- 저장된 것을 확인하는 방법
    - **예외처리**
        - 서비스에서 메서드 타입을 `void`로 한다.
- dao에서 데이터를 넣을 때 사용하는 메서드 : `save`
    - 왜 insert가 아니고 save?
        - 요즘은 데이터 저장소가 DBMS뿐만 아니라 클라우드, 빅데이터 등등이 생기면서 저장소에 `저장`한다 라는 의미로 사용된다.
        - select도 요즘은 find로 사용되는 것도 같은 맥락

## 이미지 입력하기

- redirection 이해 필요
- 파일 요청방식(enctype) 설정 → 파일 저장 방식 설정

### enctype

- enctype이란?
    - view에서 쿼리스트링을 통해 입력받을 때, 해당 **입력의 인코딩 방식을 결정**하는 방법.
- enctype을 쓰는 이유
    - 기존에 쿼리스트링 → **여러 종류의 데이터를 전송하기위해**
- 기본타입은 파일을 보낼 수 없다.
    
    ```html
    enctype="application/x-www-form-urlencoded" <!-- 기본타입 -->
    enctype="multipart/form-data"
    ```
    
- `enctype="multipart/form-data"`는 종류에 상관없이 보낼 수 있음.
    - 서버에서도 받는 방식을 맞춰야함.⇒스프링부트 최신버젼에서는 이걸 자동으로 설정되어있음.
    - 기본설정에서 수정 필요한것 : 파일크기
        - 파일크기가 크면 500 오류 발생!
        
        
        ```xml
        There was an unexpected error (type=Internal Server Error, status=500).
        Maximum upload size exceeded
        org.springframework.web.multipart.MaxUploadSizeExceededException: Maximum upload size exceeded
        		at
        ```
        

### 파일 크기 설정 방법

- 두가지 방법이 있다.
    - `application.properties`이용
        - 아래 코드 추가
            
            ```xml
            spring.servlet.multipart.max-file-size=20MB
            spring.servlet.multipart.max-request-size=25MB
            spring.servlet.multipart.location -> 잘 안씀
            spring.servlet.multipart.file-size-threshold -> 잘 안씀
            ```
            
        - file-size와 request-size의 차이
            - `file-size` : **각각의 파일** 사이즈의 크기 지정
            - `request-size` : **모든 파일의 총합** 사이즈의 크기 지정
    - 새로운 파일(`application.yml`) 이용
        - mybatis, spring 파일 저장 사이즈 등 정의가 가능
        - **application.properties와 같은 위치(resources)**에 새로운 파일 생성
        - **공백**에 주의하여 작성
            
            ```xml
            mybatis:
              configuration:
                map-underscore-to-camel-case: true
              mapper-locations: mappers/*Mapper.xml
              type-aliases-package: kr.co.rland.web.entity
            
            spring:
              servlet:
                multipart:
                  max-file-size: 500MB
                  max-request-size: 500MB
            ```
            

### 파일 저장 및 저장 위치 지정

- InputStream, OutputStream을 이용하여 파일 저장 및 파일 위치를 지정할 수 있다.
    
    ```java
    InputStream fis = imgFile.getInputStream();
    OutputStream fos = new FileOutputStream("/Users/eddy/user/"+ imgFile.getOriginalFilename());
    
    int size =0;
    byte[] bfr = new byte[1024];
    while((size=fis.read(bfr)) != -1)
    	fos.write(bfr, 0, size);
    
    fis.close();
    fos.close();
    ```
    

## Properties와 yml

- yml의 장점
    - properties는 **패키지명을 계속 써줘야하는 번거로움이 존재. 이를 공백으로 퉁쳐서 제거해주는 것**이 yml이다.
- **Spring boot는 properties를 먼저 읽고, yml을 읽는다.**
- **resources 폴더에 모두 위치**한다.
- yml은 하부 속성을 **공백**으로 구분, `같은 공백 = 같은 위치`를 나타냄.


## SSR, CSR

- **SSR**
    - 어떤 요청을 하든지 문서를 반응해주는것. 문서를 **`서버 사이드`에서 랜더링**한다.
    - 페이지의 일부가 변하면 모든걸 **서버쪽에서 책임**
    - **`GET, POST`**만 존재
    - 애플리케이션 위치 : `**Server**`
- **CSR**
    - 문서를 클라이언트에서 만든다. ⇒ 서버에 데이터를 요청
        - 페이지의 일부만 바뀔때, 바뀐 부분에 해당하는 데이터만 서버쪽에서 받아서 그 부분만 변환
            - SSR보다 속도가 빠르다.
            - 새로고침 형태가 안되니 사용자 입장에서는 부드러움을 느낌.
        - 예시) 검색, 수량 등
    - SSR과 마찬가지로 하위 개념을 쿼리스트링으로 받음
    - GET, POST, PUT 등 여러메소드 존재
        - POST : 목록만 존재
    - 어플리케이션이 클라이언트에 있음
        - 어플리케이션이 클라이언트에 있으니 서버쪽에서 데이터를 끄집어낼 것이 필요! 그것이 데이터 API
        - 페이지컨트롤러 + **데이터API**
    
    # CSR
    
    ### 데이터API만들기(apiMenuController)
    
    ```java
    @Controller("apiMenuController")
    @RequestMapping("/api/menus")
    public class MenuController {
    
        @Autowired
        private MenuService service;
    
        @ResponseBody
        @GetMapping
        public List<Menu> list(){
    
            List<Menu> list = service.getList();
            return list;
        }
    }
    ```
    
    - 가져오는 데이터 형식 : JASON
    - **@RestController**
        - ResponseBody를 안써도 되는것임.
        - Rest가 뭔데?