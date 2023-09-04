- [Servlet(Server Application Let)](#servletserver-application-let)
  - [WAS와 WS](#was와-ws)
  - [정적페이지, 동적페이지](#정적페이지-동적페이지)
  - [플랫폼(기반)](#플랫폼기반)
    - [HttpServlet](#httpservlet)
    - [번역하기 - class 파일 생성](#번역하기---class-파일-생성)
    - [배포하기](#배포하기)
    - [클래스 파일 실행하기](#클래스-파일-실행하기)
    - [HttpServletRequest request, HttpServletResponse response)](#httpservletrequest-request-httpservletresponse-response)
    - [화면에 서블릿 출력하기](#화면에-서블릿-출력하기)
    - [주의사항](#주의사항)
  - [이클립스에 톰캣 설정하기(mybatisapp)](#이클립스에-톰캣-설정하기mybatisapp)


# Servlet(Server Application Let)

- **서버 애플리케이션 조각**
- 사용자가 목록 조회 요청 → 웹서버에서는 자신의 디렉토리에서 꺼냄 → 코드를 실행해서 사용자에게 전달
- 자바 → 서블릿 API를 기반으로 작성.
    - 서블릿 API는 **tomcat내 lib폴더**에 내재되어있다.

## WAS와 WS

- WS(WebServer)
    - 클라이언트에서 중요한 내용의 디렉토리 내 `Servlet을 찾아 WAS로 전달해주는 역할`
- WAS(WebApplicationServer)
    - Servlet Runtime Enviroment
    - 웹서버로부터 받은 `Servlet 파일을 실행시켜 페이지를 만들어 클라이언트에게 전달`.

## 정적페이지, 동적페이지

- 동적페이지 : 클라이언트가 요청한대로 **WAS가 코드를 실행하여 페이지를 만들어** 클라이언트에게 전달
- 정적페이지 : 만들어져있던 걸 **그대로 보여주는 페이지**
- 자바로 동적으로 만들어내는 페이지 : JSP
- *서비스가 분가하지않으면 서블릿에 포함되었을 것이다.*

## 플랫폼(기반)

- Servlet의 기반은 ServletAPI, ServletAPI의 기반은 Java이다.
- ServletAPI는 톰캣의 lib폴더에 내장되어 있다.

### HttpServlet

- 서블릿 API에 포함되어있는 내장 클래스
- java클래스 생성

```java
public class MyServlet extends HttpServlet {
    // Servlet logic goes here
}
```

### 번역하기 - class 파일 생성

- javac를 이용하여 작성한 자바 파일을 번역한다.
- **`Cannot find symbol`** 오류발생
    - 해결방법
        - 라이브러리가 있는 경로(클래스 패스)를 번역할때 알려준다.
        - 또는, 실행할 파일 내에 위치시킨다.
    - `-cp`를 이용한 설정방법
        
        ```java
        javac -cp "servlet-api.jar경로" List.java
        ```
        
    - 오류 발생
        - 톰캣 버전에 따라 ServletAPI가 다르기 때문에 확인해야한다.
        - 톰캣 10.1이 참조하는 ServletAPI : **6.0**
        - 만약 이후에도 오류가 발생하면 `lib/*`을 이용한다.
            
            ```java
            javac -cp "톰캣폴더/lib/*" List.java
            ```
            

### 배포하기

- 생성된 class 파일을 홈디렉토리 파일 내 classes 폴더안에 넣는다. 없으면 생성하여 넣는다.
    - 이 때, **클라이언트에게 전송되지 말아야할 파일을 담은 폴더인 WEB-INF 폴더** 안에 classes폴더가 생성되어있어야 한다.
    - WAS가 읽어들일 수 있는 위치에 둬야한다.
        - `home/WEB-INF`
        - home디렉토리는 **웹서버가 사용자에게 돌려줄 자원이 있는 공간**
        

### 클래스 파일 실행하기

- url에서 디렉토리명 대신 사용할 이름을 설정한다.
    - **tomcat/conf** 에 있는 `server.xml`의 클래스패스이름을 cafe로 변경.
- **WS가 classes폴더 내에있는  클래스파일(List.class)을 WAS에다가 로드**(이때, WAS를 Runtime 환경이라고 한다.) ⇒ **WAS에서 요청이 들어오면 로드된 페이지를 제공!**
    - WS, WAS를 톰캣이 가지고 있다.
- **web.xml에 <servlet> <servlet-mapping> 설정.**
    - **`<servlet>`**
        - servlet : 클래스파일을 메모리에 올려준다. ⇒ 실행된다는 소리.
        - 루트가 classes임!
        - 꺼내줄때 필요한 이름을 설정해줘야한다.
        - `<servlet>` : 실행할 클래스 파일
        - `<servlet-name>` : mapping과 연결할 이름
    - **`<servlet-mapping>`**
        - 서블릿 보따리에서 어떤 녀석을 사용할까라는 것을 연결해주는 역할.
        - `<servlet-name>`
        - `<url-pattern>`

### HttpServletRequest request, HttpServletResponse response)

- HttpServletRequest - 사용자가 요청하는 것을 받는 객체
- HttpServletResponse - 사용자에게 보내고자 하는 기능을 하는 객체

### 화면에 서블릿 출력하기

```java
public void service(HttpServletRequest request, HttpServletResponse response)
            throws IOException, ServletException {
    // OutputStream os = response.getOutputStream();
    PrintWriter out = response.getWriter(); // getWriter : 문자출력시 사용

    out.println("hello servlet"); // 문자 출력메소드
```

### 주의사항

- 한글이 깨짐
- 통합개발환경을 쓸 필요가 있음.
- 서블릿을 직접 출력하고자하면 힘들다.

## 이클립스에 톰캣 설정하기(mybatisapp)

- **perference →server → runtime Enviroment** 에서 톰캣 추가.
- Servlet은 Controller패키지에 넣는다.
- HttpServlet에서 오류발생
    - 클래스 패스를 이클립스가 인식하지못하기 때문에 클래스 패스를 따로 설정해주어야한다.
  
- 톰캣이 실행되어야한다. 그러기 위해선 pom.xml에서 웹프로젝트라는 것을 명시해줘야함.
    
    ```xml
    <packaging>war</packaging> 
    ```
    
- maven update하면 webapp 폴더 생성
- war로 패키징하면 webapp에 있는 것(정적인문서)들이 옮겨진다 = 배포
