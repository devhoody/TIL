# 자바 프로그램이 실행되는 과정

<aside>
💡 JVM을 이용한 자바 파일이 실행 순서는 다음과 같다.

1. 소스코드(.java)를 컴파일러가 바이트코드(.class)로 컴파일한다.
2. 생성된 클래스 파일은 JVM의 클래스 로더에 의해 JVM 내 런타임 데이터 영역에 로딩된다.
3. JVM 내 실행 엔진(인터프리터, JIT 컴파일러)이 런타임 데이터 영역에 있는 클래스 파일을 번역한다.
4. 번역된 기계어를 통해 CPU에서 실행되어 사용자에게 서비스를 제공한다.
</aside>

해당 문서에서는 1)에 해당하는 컴파일 과정을 학습하도록 하겠다.

> 우리가 사용하는 언어를 컴퓨터는 어떻게 인식할 수 있을까?
> 

우리는 영어를 이용해서 코드를 작성한다. 

하지만, 컴퓨터한테 hello라고 외친다고한들 컴퓨터는 ???만 던질 뿐 이해하지 못한다. 

이를 해결하기 위해 우리가 작성한 코드를 번역하는 과정인 컴파일을 수행한다.

해당 글에서는 컴파일이 무엇이고, 컴파일 과정을 알아볼 것이다. 더 나아가 인터프리터와의 차이를 알아보자.

# 컴파일이란?

컴파일은 번역하는 기능. 즉, **우리의 언어로 작성된 소스 코드를 컴퓨터가 이해할 수 있는 언어로 번역**하는 기능을 말한다. 

- 번역 = 컴파일
- 번역기 = 컴파일러
    
<img width="669" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/26ac76ac-eed9-4b96-afb8-3dc862140e21">
    

<aside>
💡 대표적인 프로그래밍 언어인 C언어와 자바의 경우, 컴파일 결과물은 다음과 같다.

- C언어 → 기계어
- 자바 → 바이트코드
</aside>

## 컴파일을 하는 이유

컴퓨터는 0과 1을 이용하여 동작한다. 사람이 사용하는 언어를 컴퓨터가 이해하기위해서는 0과 1로 구성된 파일로 번역하는 과정이 필요하다. 즉, 컴파일을 통해 우리가 쓰는 언어로 컴퓨터를 조작할 수 있다.

# `컴파일 과정`

본인은 자바 백엔드 개발자이기에 **자바를 중심으로** 컴파일 과정을 설명하도록 하겠다.

1. 개발자가 자바 소스코드(.java)를 작성한다.
2. 자바 컴파일러가 자바 소스파일을 바이트코드(.class)로컴파일한다.
    - 자바 컴파일러는 `javac` 라는 명령어를 통해 소스파일을 컴파일한다.
    - 바이트 코드는 컴퓨터의 CPU가 읽을 수 없지만, JVM이 읽을 수 있는 코드이다.
    - 바이트 코드의 명령어는 1바이트 크기의 Opcode와 추가 피연산자로 이루어져있다.

### 컴파일 실습

- 맥OS -  터미널을 이용하여 직접 자바 파일을 컴파일시켜 클래스 파일을 생성해보자.
    
<img width="408" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/85394891-467c-4a70-851c-cdbd7764ad46">
    
1. 터미널을 켠 후, 자바 파일(Main.java)이 있는 경로로 이동한다.
2. `ls`를 이용하여 자바 파일이 있는지 확인한 후, `javac`를 이용하여 해당 자바 파일을 컴파일시킨다.
3. 컴파일이 완료되면 바이트 코드로 작성된 Main.class파일이 생성된 것을 알 수 있다. 
    
    <img width="1272" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/743a28fc-7f82-40f3-b5ae-f3f943de4ea2">
    
4. 이 class 파일을 JVM이 번역하여 실행한다.

<aside>
💡 바이트코드란?

- 컴퓨터가 이해할 수 있는 저수준 언어로 JVM이 인식할 수 있는 명령어들이 들어가 있다.
- 컴파일러가 소스코드를 번역하면 바이트 코드가 생성된다.
- 클래스 파일은 바이트코드로 작성된 파일이다.
</aside>

## 컴파일러의 기능

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

# 컴파일러, 인터프리터, JIT 컴파일러

> 자바 기준으로 작성한 문서입니다.
> 

세가지 모두 자바 코드를 CPU가 이해할 수 있는 언어로 번역하기 위한 과정에서 사용된다.

<img width="338" alt="4" src="https://github.com/devhoody/TIL/assets/124743189/81bbb4e9-8d8d-48be-a11f-3203931b5780">

## 컴파일러

컴파일러는 소스코드를 목적 코드(바이트 코드 등)으로 변환한다.  즉, 고레벨의 언어를 저레벨 언어로 변환하는 것을 말한다. 

자바의 경우, 목적 코드인 바이트코드로 변환하여 JVM이 사용할 수 있게끔 만들어준다.

코드 전체를 한번에 분석하여 번역을 수행한다.

## 인터프리터

인터프리터는 컴파일러와 마찬가지로 프로그래밍 언어를 번역하는 기능을 수행한다.

자바에서는 바이트 코드의 각 행을 번역하고 다음 행을 번역하는 순서로 연속적으로 수행한다.

각 행을 일일이 번역하는 과정을 거쳐야 하므로 속도가 느리다는 단점이 존재한다.

이를 해결하는 것이 JIT 컴파일러이다.

## JIT 컴파일러

JIT 컴파일러는 인터프리터의 단점을 보완하기위해 설계된  방식이다. 

인터프리터와 마찬가지로 바이트 코드를 Native Code로 변환한다. 즉, CPU가 이해할 수 있는 언어로 변환하는 과정을 수행한다.

인터프리터와 다른 점은 바이트 코드 전체를 번역한 후, 자주 사용되는 코드들은 캐시에 저장되어 한 번 저장된 코드들을 빠르게 실행할 수 있다.

저장한 것을 바로 호출하여 실행만하면 되므로 코드 양이 많은 경우, 인터프리터보다 속도가 빠르다는 장점이 있다.

하지만, 한번만 실행하면 되는 코드들은 인터프리터로 실행하는 것이 좋다. JVM은 인터프리터와 JIT컴파일러를 함께 사용한다. 



# 컴파일러, 인터프리터, JIT 컴파일러

> 자바 기준으로 작성한 문서입니다.
> 

세가지 모두 자바 코드를 CPU가 이해할 수 있는 언어로 번역하기 위한 과정에서 사용된다.

<img width="338" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/a5799417-ec8e-463d-bff3-f48e4c21a998">


## 컴파일러

<img width="669" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/15269656-410c-4d42-b18e-55b3ef50528b">

컴파일러는 소스코드를 목적 코드(바이트 코드 등)으로 변환한다.  즉, 고레벨의 언어를 저레벨 언어로 변환하는 것을 말한다. 

자바의 경우, 목적 코드인 바이트코드로 변환하여 JVM이 사용할 수 있게끔 만들어준다.

코드 전체를 한번에 분석하여 번역을 수행한다.

## 인터프리터와 JIT 컴파일러

인터프리터와 JIT 컴파일러 모두 자바의 JVM 내 실행엔진에서 동작한다.

**바이트코드를 번역**한다는 공통점이 있다.

## 인터프리터

<img width="340" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/8b5e6c2e-3247-4b75-bc75-aa6446a3855d">


> 자막 생성기
> 

인터프리터는 컴파일러와 마찬가지로 프로그래밍 언어를  번역하는 기능을 수행한다.

자바에서는 JVM의 실행엔진에서 런타임 데이터 영역에 올라온 바이트 코드의 각 행을 바이너리코드로 번역하고 다음 행을 번역하는 순서로 연속적으로 수행한다.

각 행을 일일이 번역하는 과정을 거쳐야 하므로 **속도가 느리다는 단점**이 존재한다.

이를 해결하는 것이 JIT 컴파일러이다.

## JIT 컴파일러

> 자주 사용되는 자막 스크립트
> 

JIT 컴파일러는 인터프리터의 단점을 보완하기위해 설계된  방식이다. 

인터프리터와 마찬가지로 JVM의 실행엔진에 존재한다.

인터프리터와 다른 점은 **바이트 코드 전체를 한번에 번역한 후, 자주 사용되는 코드들은 캐시에 저장**되어 한 번 저장된 코드들을 빠르게 실행할 수 있다. 그리고 Native Code로 번역되는 점이 다르다. (인터프리터는 바이트 코드 → 바이너리 코드)

저장한 것을 바로 호출하여 실행만하면 되므로 코드 양이 많은 경우, 인터프리터보다 속도가 빠르다는 장점이 있다.

하지만, 한번만 실행하면 되는 코드들은 인터프리터로 실행하는 것이 좋다. **JVM은 인터프리터와 JIT컴파일러를 함께 사용한다.** 

# 정리

- 컴파일러와 인터프리터, JIT 컴파일러는 모두 프로그래밍 언어를 번역하는 기능을 수행한다.
- 컴파일러는 소스코드 → 바이트 코드로 번역한다.
- 인터프리터와 JIT 컴파일러는 JVM 내의 실행엔진에 위치하여 바이트 코드를 번역한다.
- 인터프리터는 바이트 코드 → 바이너리 코드로 번역하고, 한 줄의 코드를 번역 후 다음 한 줄을 번역하는 방식으로 진행되어 속도가 느리다.
- JIT 컴파일러는 바이트 코드 → Native Code(C, C++등)로 번역하고, 한번에 번역 후, 자주 사용되는 언어를 Native Method Stack에 저장한 후, 호출하는 방식을 이용한다. 인터프리터 보다 속도가 빠르다.



# Reference

[JVM 구성요소 (feat, JIT 컴파일러)](https://www.notion.so/JVM-feat-JIT-168fbe0e60ed4e69a4b7823b0e790c8f?pvs=21)

https://www.javatpoint.com/internal-details-of-hello-java-program

[https://velog.io/@taebong98/자바-컴파일-과정-소스코드부터-실행까지](https://velog.io/@taebong98/%EC%9E%90%EB%B0%94-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EA%B3%BC%EC%A0%95-%EC%86%8C%EC%8A%A4%EC%BD%94%EB%93%9C%EB%B6%80%ED%84%B0-%EC%8B%A4%ED%96%89%EA%B9%8C%EC%A7%80)

https://velog.io/@jaeyunn_15/OS-Compiler-vs-Interpreter

https://www.javatpoint.com/internal-details-of-hello-java-program