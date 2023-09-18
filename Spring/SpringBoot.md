- [Spring boot](#spring-boot)
  - [Spring boot 프로젝트 생성](#spring-boot-프로젝트-생성)
    - [서버 자동 재시작](#서버-자동-재시작)
  - [임베디드 톰캣](#임베디드-톰캣)
  - [컨트롤러 만들기](#컨트롤러-만들기)
    - [경로 설정에 따른 패키지, 클래스 , 메소드 설정하기](#경로-설정에-따른-패키지-클래스--메소드-설정하기)
  - [view 만들기](#view-만들기)


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