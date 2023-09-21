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