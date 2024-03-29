
- [`JVM이란 무엇인가`](#jvm이란-무엇인가)
  - [WORA란?](#wora란)
- [`컴파일 하는 방법`](#컴파일-하는-방법)
  - [컴파일러의 기능](#컴파일러의-기능)
  - [컴파일 실습](#컴파일-실습)
- [`실행하는 방법`](#실행하는-방법)
  - [컴파일러의 기능](#컴파일러의-기능-1)
- [`바이트코드란 무엇인가`](#바이트코드란-무엇인가)
- [`JIT 컴파일러란 무엇이며 어떻게 동작하는지`](#jit-컴파일러란-무엇이며-어떻게-동작하는지)
  - [인터프리터란?](#인터프리터란)
  - [JIT 컴파일러](#jit-컴파일러)
- [`JVM 구성 요소`](#jvm-구성-요소)
  - [클래스 로더](#클래스-로더)
- [실행엔진](#실행엔진)
  - [인터프리터](#인터프리터)
  - [JIT 컴파일러](#jit-컴파일러-1)
  - [Garbage Collector](#garbage-collector)
- [런타임 데이터 영역](#런타임-데이터-영역)
  - [메서드 영역](#메서드-영역)
  - [힙 영역](#힙-영역)
  - [PC 레지스터](#pc-레지스터)
  - [스택 영역](#스택-영역)
  - [네이티브 메소드](#네이티브-메소드)
- [자바 네이티브 인터페이스](#자바-네이티브-인터페이스)
- [네이티브 메서드 인터페이스](#네이티브-메서드-인터페이스)
- [`JDK와 JRE의 차이`](#jdk와-jre의-차이)
- [JDK(Java Development Kit)](#jdkjava-development-kit)
- [JRE](#jre)
- [JDK vs JRE](#jdk-vs-jre)

## `JVM이란 무엇인가`

- 자바 바이트코드(기계어)를 읽어 프로그램을 실행하는 자바 가상 머신
- JRE에 포함되어 있다.
- 제공하는 기능
    - 운영체제에 독립적이다. → `WORA(Write Once, Run Anywhere)`
    - 메모리 관리 → 메모리 누수와 같은 문제 방지, 가비지 컬렉션(CG)
    - 가상 머신 스택 → 각각의 스레드에 대해 가상 머신 스택을 생성하여 메서드 호출, 지역 변수 관리
    - 클래스 로딩 최적화

### WORA란?

- 운영체제, 플랫폼에 상관 없이 독립적으로 하나의 자바 소스 파일과 컴파일러를 통해 코드를 실행할 수 있다. 즉, `한번 작성하면 어디서든 실행할 수 있다`라는 의미를 가진다.
- 예를 들어, 윈도우에서 작성한 자바 소스 파일을 JRE를 갖춘 리눅스에서도 동일하게 실행할 수 있다.

## `컴파일 하는 방법`

*전처리 → 컴파일 → 어셈블링 (→ 링크)*

- **`전처리`** : 실행할 프로그램에 관련된 실행 파일들을 선정한다.
- **`컴파일`** : **소스코드 → 어셈블리어(바이트 코드)**
    - **컴파일러**라는 프로그램이 소스코드를 어셈블리어로 번역
    - 과거에는 어셈블리어로 코드를 작성했다.
        - **어셈블리어?** → 기계어에 가까운 저수준 언어, 컴퓨터가 직접 이해할 수 있는 명령어로 구성되어있다.
- **`어셈블링`** : **어셈블리어 →  기계어(바이너리코드)**
    - 기계어 : 컴퓨터가 이해할 수 있는 코드로, 0과1(이진법)로 구성되어 있다.
    - **어셈블러**라는 프로그램이 어셈블리어를 기계어로 번역한다.
- **`링크`** : 프로그램이 여러 파일로 이루어져있어 합쳐야할 필요가 있을 경우 필요한 단계

### 컴파일러의 기능

- **구문 분석(Syntax Analysis)**
    - 자바 소스 코드에서 문법적 오류가 있는지 확인한다.
    - 변수 선언, 메소드 호출, 제어문 등의 구문 오류 검사
- **런타임 오류 처리(Error Handling)**
    - 컴파일 중 오류가 발생했을 경우, 오류 메시지를 출력하고 컴파일 과정을 중단한다.

### 컴파일 실습

- 맥OS -  터미널을 이용하여 직접 자바 파일을 컴파일시켜 클래스 파일을 생성해보자.
    
    <img width="409" alt="1" src="https://github.com/devhoody/allmap/assets/124743189/5bdfedf6-1157-427c-9871-da55ee08b539">

    
- 우선 터미널을 켠 후, 자바 파일([Compile.java](http://Compile.java))이 있는 경로로 이동한다. compiler라는 디렉토리에 Compile.java 파일을 생성했다.
- `ls`를 이용하여 자바 파일이 있는지 확인한 후, `javac`를 이용하여 해당 자바 파일을 컴파일시킨다.
- 컴파일이 완료되면 바이트 코드로 이루어진 Compile.class파일이 생성된 것을 알 수 있다.
    
    <img width="889" alt="2" src="https://github.com/devhoody/allmap/assets/124743189/de262c10-bca9-40ac-9459-fd6c3734be58">
    
- JVM이 해당 클래스 파일을 기계어로 번역하여 실행하는 역할을 하게 된다.


## `실행하는 방법`

1. *.java가 자바 컴파일러에 의해 .class로 변환된다.
2. class가 Class Loader를 통해 JVM에 로딩된다.
3. JVM의 인터프리터와 JIT compilier에 의해 바이트코드가 기계어로 변환된다.
4. JVM의 Runtime Data Area에서 기계어를 읽어 실질적인 프로그램을 실행한다.

### 컴파일러의 기능

- `토큰화(Tokens)`
    - 소스 코드를 토큰이라는 단위로 분할하는 과정
    - 토큰 : 의미있는 어휘 단위
        
        ```java
        int num = 10; // 토큰 : 'int', 'num', '=', '10'
        ```
        
- `구문 분석(Syntax Analysis)`
    - 자바 소스 코드에서 문법적 오류가 있는지 확인한다.
    - 변수 선언, 메소드 호출, 제어문 등의 구문 오류 검사
- **`런타임 오류 처리(Error Handling)`**
    - 컴파일 중 오류가 발생했을 경우, 오류 메시지를 출력하고 컴파일 과정을 중단한다.

위에서 컴파일러의 과정을 통해 클래스 파일을 생성했다면, 이번에는 JVM이 클래스 파일을 읽어 실행하는 과정을 살펴본다.

## `바이트코드란 무엇인가`

- 컴퓨터가 이해할 수 있는 저수준 언어, 컴퓨터가 인식할 수 있는 명령어들이 들어가 있다.
- 컴파일러가 소스코드를 번역하면 바이트 코드가 생성된다.
- 어셈블러가 바이트 코드를 기계어(바이너리코드)로 번역하여 최종적으로 0과 1로 된 파일이 만들어진다.

## `JIT 컴파일러란 무엇이며 어떻게 동작하는지`

- JVM에는 실행엔진(Execution Engine)이 있고 그 안에 인터프리터와 JIT 컴파일러가 존재한다. 해당 실행엔진을 통해 클래스 로더를 통해 넘어온 바이트 코드를 명령어 단위로 읽어서 실행한다.
- 해당 수행 과정에서 인터프리터와 JIT 컴파일러를 혼합하여 사용한다.

### 인터프리터란?

- 바이트 코드 명령어를 하나씩 읽어 해석하고 바로 실행한다. (실시간)
- JVM은 기본적으로 인터프리터를 사용하나, 같은 메소드가 자주 호출되면 매번 해석을 해야하므로 전체적인 속도가 느리다는 단점이 있다.

### JIT 컴파일러

- 인터프리터의 속도 개선을 위해 등장한 컴파일러이다.
- 반복되는 코드를 발견하여 바이트 코드 전체를 컴파일하여 기계어로 변환한다. 해당 코드를 더이상 인터프리팅하지 않고 캐싱해 두었다가 꺼내서 쓰는 방식을 지원한다.
- 인터프리팅된 기계어를 사용하기때문에 인터프리터보다 속도가 빠르다는 장점이 있다.
- 하지만, 바이트코드 전체를 Native Code로 변환하는 것은 많은 비용이 소모되므로, 처음 메소드 호출시에는 인터프리터를 이용하다가 어느정도 수준이 넘어가면 JIT컴파일러 방식을 이용하여 효율적으로 바이트 코드를 실행한다.
    

## `JVM 구성 요소`


JVM 구성 요소는 다음과 같이 이루어져 있다.

- 클래스 로더
- 실행 엔진(Execution Engine)
    - JIT 컴파일러
    - 인터프리터
    - Garbage Collector
- 런타임 데이터 영역 (Runtime Data Areas)
    - 메서드 영역
    - 힙 영역
    - PC 레지스터
    - 스택 영역
    - 네이티브 메소드
- 네이티브 메소드 인터페이스
- 네이티브 메소드 라이브러이

### 클래스 로더

- 바이트 코드로 번역된 클래스 파일을 런타임 데이터 영역으로 이동한다.

## 실행엔진

- 런타임 데이터 영역으로 올라온 클래스 파일의 바이트 코드를 명령어 단위로 실행한다.

### 인터프리터

- 바이트 코드를 명령어 단위로 하나씩 번역후 실행

### JIT 컴파일러

- 바이트 코드 전체를 한번에 번역하여 실행한다. 이후, 자주 이용되는 코드는 캐싱되어 호출될 때마다 꺼내서 실행한다.

### Garbage Collector

- 힙 영역에서 더이상 사용되지 않는 객체를 제거해주는 역할
- 메모리를 효율적으로 관리할 수 있도록 도와준다.

## 런타임 데이터 영역

- 메서드 영역과  힙 영역은 모든 스레드에서 공유한다. 나머지 영역은 각각의 스레드별로 생성된다.
    

### 메서드 영역

- 클래스와 런타임 상수 풀, 정적(static) 변수, 메서드와 필드, 메서드 바이트 코드 등을 보관하는 영역

### 힙 영역

- **`new 연산자`**로 생성된 객체(인스턴스)가 저장되는 영역

### PC 레지스터

- 현재 실행되고 있는 JVM주소를 갖고 있어 현재 실행되는 바이트 코드를 지정해주는 역할

### 스택 영역

- 실제 실행된 메서드와 지역 변수들이 스택 프레임의 형태로 담기는 영역
- 실행 후 종료된 메서드는 pop되어 제거된다. (LIFO)

### 네이티브 메소드

- 자바가 아닌 다른 언어로 작성된 코드를 실행하는 기능
- JIT 컴파일러에 의해 변환된 Native Code가 이곳에서 실행된다.

## 자바 네이티브 인터페이스

- 자바가 다른 언어로 만들어진 어플리케이션과 상호 작용을 하도록 도와주는 인터페이스를 제공하는 프로그램

## 네이티브 메서드 인터페이스

- C, C++로 작성된 라이브러리

## `JDK와 JRE의 차이`

## JDK(Java Development Kit)


- 자바를 이용하여 개발하기 위한 기능들이 담겨있는 kit
- JRE, Jar, 모니터링, 컴파일러 등
- **LTS** : Oracle에서 지원하는 장기 지원 버전 → **Java 8, 11, 17, 21**

## JRE

- JVM과 자바 프로그램을 실행시킬 때 필요한 라이브러리 API를 묶어 배포되는 패키지
- JDK가 설치될 때 함께 설치된다.
- JVM : 자바를 실행하는 가상 머신
    - 현지 요리사

## JDK vs JRE

- java 로 개발한다 → JDK, 컴파일된 java 파일을 실행한다. → JRE