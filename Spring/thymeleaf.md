- [HTML파일에 Thymeleaf 지시자 설정](#html파일에-thymeleaf-지시자-설정)
- [header 넣기](#header-넣기)
  - [header에 스타일 넣기](#header에-스타일-넣기)
- [layout만들기](#layout만들기)
  - [thymeleaf layout dialect](#thymeleaf-layout-dialect)
- [thymeleaf를 이용한 제어문 - 반복문](#thymeleaf를-이용한-제어문---반복문)
- [데이터 출력 포맷 변환](#데이터-출력-포맷-변환)
  - [금액 - 3자리마다 콤마 찍기](#금액---3자리마다-콤마-찍기)
- [페이지 이동하기](#페이지-이동하기)
  - [list→ detail](#list-detail)
  - [th를 이용하여 쿼리스트링값 적용하기(th:href)](#th를-이용하여-쿼리스트링값-적용하기thhref)
  - [디테일에 가져온 쿼리스트링 값에 맞는 정보 구현하기 (th:object)](#디테일에-가져온-쿼리스트링-값에-맞는-정보-구현하기-thobject)
- [타임리프 메서드](#타임리프-메서드)
  - [타임리프 리터럴](#타임리프-리터럴)


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

## 데이터 출력 포맷 변환

- 숫자 변환하기(numbers)
    - `#numbers.formatInteger()`
    - `#numbers.decimal()`

### 금액 - 3자리마다 콤마 찍기

```html
th:text="${#numbers.formatInteger(qt * 4500, 3, 'COMMA')}"
```

## 페이지 이동하기

### list→ detail

- 정적인 요소는 퍼블리셔가 담당. 백엔드가 건드리지 않는다.
    - 동적인 요소만을 th 를 이용해서 작성한다.
    - a href → 정적 : 퍼블리셔 , th:href → 동적 : 백엔드
    - `<a href="detail?id=1" th:href="">카페라떼</a>`

### th를 이용하여 쿼리스트링값 적용하기(th:href)

- `@` : 절대경로를 나타내는 기호

```html
th:href="@{detail(id=${m.id})}"
```

### 디테일에 가져온 쿼리스트링 값에 맞는 정보 구현하기 (th:object)

- `th:object`
    - th:obejct란?
        - view에서 타임리프를 이용하여 데이터를 가져와 지역변수로 활용할 수 있도록 만드는 dialect중 하나로, 한개의 객체 안에 있는 데이터를 th:with 보다 편리하게 쓰게만드는 지역변수 선언 dialect
    - 하나의 구역에에 쓰이는 값이 한개의 객체에서 가져오는 내용이라면 지역변수로 객체를 선언한다.
    - 그러면 따로 객체 내 데이터를 가져올 때, `${객체.데이터명}으로` 쓰지 않고 `*{데이터명}` 으로만 쓸수 있어 편리성을 제공한다
        - 이 때, $ 대신 *사용하는 것에 주의 ⇒ 구역 내에서 전체를 쓰는거니까 `*`라고 이해하면 편할듯
    
    ```html
    <section th:object="${menu}">
    			<h1 th:text="*{korName}">카페라떼</h1> <!-- ${menu.korName}대신 사용-->
    			<h2 th:text="*{engName}">Caffe Latte</h2>
    ```

# 타임리프 - 스프링 통합과 폼

## 입력 폼 처리

- 기존의 html에서 타임리프의 입력 폼 기능을 적용해보자.
- `th:object`, `th:field` 이용
    - `th:obejct` : 커맨드 객체를 지정
    - `th:field` : id, name, value를 자동으로 입력해줌.
    - `*{}` : th:object 에서 지정한 객체에 접근하는 식
        - th:object=”${item}” ~ *{name} : item 객체의 name 속성 값을 조회한다.
- 사용 전

```html
<form action="item.html" th:action method="post">
	<input type="text" id="itemName" name="itemName" th:value="${item.itemName}">
```

- 사용 후

```html
<form action="item.html" th:action th:object="${item}" method="post">
	<input type="text" th:field="*{id}">
```

- 페이지 소스
    
    `<input type="text" class="form-control" placeholder="이름을 입력하세요" id="id" name="id" value="">`



# 타임리프 메서드

## 타임리프 리터럴

`th:onclick="|location.href='@{/basic/items/add}'|"`

- 원래는 타임리프에서 문자와 표현식 등은 분리되어 있기 때문에 더해서 사용해야 한다.
- 하지만 리터럴을 사용하면 더하기를 하지 않고 쉽게 문자로 치환되어 사용된다.

`th:action`

- HTML form에서 action에 값이 없으면 현재 URL에 데이터를 전송한다.
- 상품 등록 폼의 URL과 실제 상품 등록을 처리하는 URL을 똑같이 맞추고 HTTP 메서드로 두 기능을 구분한다.
    - 상품 등록 폼: GET ``/basic/items/add``
    - 상품 등록 처리: POST ``/basic/items/add``
- 이렇게 하면 하나의 URL로 등록 폼과, 등록 처리를 깔끔하게 처리할 수 있다.