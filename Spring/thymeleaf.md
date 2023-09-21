- [HTML파일에 Thymeleaf 지시자 설정](#html파일에-thymeleaf-지시자-설정)
- [header 넣기](#header-넣기)
  - [header에 스타일 넣기](#header에-스타일-넣기)
- [layout만들기](#layout만들기)
  - [thymeleaf layout dialect](#thymeleaf-layout-dialect)
- [thymeleaf를 이용한 제어문 - 반복문](#thymeleaf를-이용한-제어문---반복문)



## HTML파일에 Thymeleaf 지시자 설정

- model을 템플릿(HTML)에서 이용하기위해서 thymeleaf를 이용한다.
    
    [Tutorial: Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#using-texts)
    
- 방법
    - `<html>`에 thymeleaf가 인식할 수 있도록 하는 코드를 넣는다.
        
        ```html
        <html xmlns:th="http://www.thymeleaf.org">
        ```
        
    - controller에서 model에 값 삽입
    - `th:`를 이용해서 html파일에 model의 값을 집어넣는다.
        - `th:`를 이용한 태그 내에 있는 HTML코드는 Model 값으로 자동으로 대체되어 삭제하지 않아도 된다. 이는 퍼블리셔와 협업할 때 좋은 이점을 갖는다.
            
            ```html
            <body th:text="${test}">
               <h1>메뉴 리스트 페이지입니다.</h1>
            </body>
            
            ```
            
## header 넣기

- inc 디렉토리에 header html 파일을 넣어 모든 페이지가 공용할 수 있도록 한다.
- `fragment`를 이용하여 html에 넣어줄 태그를 설정한다.
    
    ```html
    <header class="header md:header" th:fragment="h">
    ```
    
- 기본 태그에 값을 대입하는 방법 2가지
    - `Insert` : 기본 태그를 `포함`하여 전달
        
        ```html
        <div th:insert="~{inc/header::h}"></div>
        ```
        
    - `Replace` : 기본 태그를 `빼고` 전달
        
        ```html
        <div th:replace="~{inc/header::.header}"></div>
        ```
        
- `::`
    - 파일 내 일정 부분만 넣는다는 예약어
    - 적용 가능한 범위 : `fragment, class명, id명`
    - 없으면 header파일 전체 코드를 가져온다.
- `~{}`
    - 홈 디렉토리에서 출발한다는 의미

### header에 스타일 넣기

- 기존에 만든 head의 CSS를 붙여넣고 경로를 재지정해주면 적용가능하다.

## layout만들기

- layout을 만드는 이유
    - header, footer 같이 모든 페이지에 필요한 요소의 경우, 수정이나 추가할 경우, **모든 페이지를 수정하는 작업을 반복해야 한다.** 이를 해결하기위해 layout 문서를 따로 만들어준다.
    

### thymeleaf layout dialect

- **dialect란?**
    - 타임리프에서 쓰는 명령어
- **layout dialect란?**
    - layout을 만드는데 유용한 기능을 제공하는 라이브러리
    - layout페이지에 연결된 태그가 layout과 결합된다.
- **초기설정**
    - pom.xml에 라이브러리 추가
        
        ```html
        <dependency>
            <groupId>nz.net.ultraq.thymeleaf</groupId>
            <artifactId>thymeleaf-layout-dialect</artifactId>
        </dependency>
        
        ```
        
    - 레이아웃과 연결할 페이지 모두 html태그에 연결 코드 추가
        - decorate에는 레이아웃 페이지의 경로를 입력한다.
        
        ```html
        <html lang="en" 
        	xmlns:th="http://www.thymeleaf.org"
        	xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
        	layout:decorate="~{inc/layout.html}"> <!-- 루트폴더에서 inc/layout.html 지정 -->
        ```
        
- 사용방법
    - `layout:fragment`를 이용하여 두 페이지를 연결
    
    ```html
    <div layout:fragment="main">
    ```

## thymeleaf를 이용한 제어문 - 반복문

[Tutorial: Using Thymeleaf](https://www.thymeleaf.org/doc/tutorials/3.1/usingthymeleaf.html#iteration)