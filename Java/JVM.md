<img width="441" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/e078eef3-39dd-4605-af6a-6335a788d026">

IntellJ에서 자바 프로그램을 실행하면 다음과 같은 경로를 발견할 수 있다. JDK를 설치하면 해당 경로의 파일이 설치되는데, JavaVirtualMachine은 뭐길래 설치되는 걸까? 이번 게시글에서는 JVM에 대해서 자세히 알아보고자 한다.

# JVM을 사용하는 이유

> 자바는 모든 운영체제에 종속적이지 않다.
> 

JVM을 사용하는 이유를 알아보기 전에, 자바 이전에 나온 C언어의 컴파일 과정을 간단히 알아보기로 하자.

C언어는 소스 파일을 기계어로 컴파일한다. 기계어는 CPU가 읽어 해당 명령어들을 실행한다. 하지만, 컴파일러는 운영 체제에 종속되어 있어 서로 호환이 불가능하다는 단점이 있다. 

<img width="833" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/d657c177-b7ca-43c0-8121-20df8a050967">


예를 들어, 윈도우에서 작성한 C언어 파일은 리눅스에서 실행이 안된다.

JAVA는 JVM을 이용해 자바로 작성한 소스파일을 모든 운영체제에서 호환이 가능하도록 한다. → 이를 WORA라고 한다.

## WORA(Write Once, Read Anywhere)

JVM을 통해 한번 작성된 JAVA 소스 파일은 운영체제에 상관 없이 독립적으로 실행이 가능하게 된다. 즉, “**한번 작성하면 어디서든 실행할 수 있다.”** 라는 의미를 갖는다.

<img width="849" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/869b01ce-4f26-4961-b93b-9c6bd047a099">

위의 그림처럼, 자바 소스파일이 컴파일되면 각 운영체제에 맞는 JVM이 이를 해석하여 실행하게 된다. 따라서, JVM을 설치하려면 운영 체제에 맞는 JDK를 설치해야 한다. JDK에 대한 자세한 내용은 다음 글에서 설명하겠다.

<img width="397" alt="4" src="https://github.com/devhoody/TIL/assets/124743189/231263df-95e4-491a-b945-0248346e0fa7">

오라클은 운영체제 별 JDK를 제공하고 있다.

정리하자면, JVM을 사용하는 가장 큰 이유 중 하나는 WORA 이다. 

자바와 C언어의 실행 과정을 비교하면 다음과 같다.

![5](https://github.com/devhoody/TIL/assets/124743189/274cb318-5807-4acb-b900-7d7911fdcb1a)


이제 JVM이 무엇인지 알아보자.

# `JVM이란 무엇인가`

<img width="395" alt="6" src="https://github.com/devhoody/TIL/assets/124743189/839a9a1f-d504-4657-a6d9-266ed1d15adc">

JVM은 자바 바이트코드(.class 파일)를 읽어 프로그램을 실행하는 자바 가상 머신이다. 자바 가상 머신이라는 용어가 생소한데, 자바 파일을 실행하기 위한 모든 과정이 JVM에서 일어난다고 생각하면 된다. 자바 파일을 실행하면 OS가 JVM을 메모리에 배치하고 배치된 JVM 내에서 자바 파일이 실행된다. 쉽게 말해서 **메모리에 올라간 자바 공장** 이다.

> 바이트 코드 : 자바의 소스파일이 컴파일되어 변환된 코드로 JVM이 읽을 수 있는 코드
> 

JVM이 제공하는 기능을 알아보자.

## JVM이 제공하는 기능

- 운영체제에 독립적이다. → `WORA(Write Once, Run Anywhere)`
- **메모리 관리** → 메모리 누수와 같은 문제 방지, 가비지 컬렉션(CG)
- **가상 머신 스택** → 각각의 스레드에 대해 가상 머신 스택을 생성하여 메서드 호출, 지역 변수 관리
- **클래스 로딩 최적화**

# `실행하는 방법`

<img width="338" alt="7" src="https://github.com/devhoody/TIL/assets/124743189/f5127939-9223-4f85-a2eb-003121919e3e">

JVM을 이용한 자바 파일이 실행 순서는 다음과 같다.

1. 소스코드(.java)를 컴파일러가 바이트코드(.class)로 컴파일한다.
2. 생성된 클래스 파일은 JVM의 클래스 로더에 의해 JVM 내 런타임 데이터 영역에 로딩된다.
3. JVM 내 실행 엔진(인터프리터, JIT 컴파일러)이 런타임 데이터 영역에 있는 클래스 파일을 번역한다.
4. 번역된 기계어를 통해 CPU에서 실행되어 사용자에게 서비스를 제공한다.

실행하는 과정의 자세한 내용은 아래 글로 정리했다.



<aside>
💡 JVM을 이용한 자바 파일이 실행 순서는 다음과 같다.

1. 소스코드(.java)를 컴파일러가 바이트코드(.class)로 컴파일한다.
2. 생성된 클래스 파일은 JVM의 클래스 로더에 의해 JVM 내 런타임 데이터 영역에 로딩된다.
3. JVM 내 실행 엔진(인터프리터, JIT 컴파일러)이 런타임 데이터 영역에 있는 클래스 파일을 번역한다.
4. 번역된 기계어를 통해 CPU에서 실행되어 사용자에게 서비스를 제공한다.
</aside>

JVM은 앞서 컴파일 과정은 거쳐 생성된 클래스파일(바이트코드)를 가져와 컴퓨터가 실행할 수 있도록 추가 작업한다. 

해당 문서에서는 2) ~ 4) 과정을 살펴보고 1)과정이나 JVM 개념에 대해서는 아래 글을 참고하자.

2) ~ 4) 과정은 아래 그림에서 빨간색 부분을 나타낸다.

<img width="847" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/d95a1998-cbc8-4bb0-88df-bfa79b28e086">

# JVM 구성요소 키워드

JVM은 **클래스 로더, 런타임 데이터 영역, 실행 엔진, 네이티브 영역**으로 나눌 수 있다.

<img width="801" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/96184ad2-ba64-47b8-8beb-ef1c46125044">

<aside>
💡 JVM 구성요소 키워드

- **클래스 로더**
- **런타임 데이터 영역**
    - 메서드 영역 (+ 상수풀)
    - 힙
    - 스택
    - pc레지스터
    - 네이티브 메서드 스택
- **실행 엔진**
    - 인터프리터
    - JIT 컴파일러
    - GC(가비지 컬렉터)
- **JNI - 네이티브 메서드 인터페이스**
- **네이티브 메서드 라이브러리**
</aside>

# 클래스 로더

> 클래스 파일을 JVM 내 Runtime Data Area에 로딩한다.
> 

<img width="444" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/23349c35-169b-4ab9-9280-a73fc6e8fd8b">

바이트 코드로 작성된 클래스 파일을 JVM 내 메모리 영역인 런타임 데이터 영역에 배치한다.

한번에 클래스 파일 모두를 배치하는게 아니고 **애플리케이션에서 필요할 경우, 동적으로 메모리에 배치한다.**

크게 *로딩 → 링크 → 초기화* 과정을 거친다.

- **로딩** : 클래스 파일을 JVM 메모리에 로드한다.
- **링크** : 클래스 파일을 사용하기위해 검증하고 참조를 설정한다.
    - 링크 3단계 : *검증 → 준비 → 분석*
        - **검증** : 읽어들인 클래스가 제대로된 자바 클래스인지 확인한다.
        - **준비** : 클래스가 필요로 하는 메모리를 할당한다.
        - **분석** : 클래스의 상수 풀에 있는 심볼릭 참조를 참조 가능한 상태로 변경한다.
- **초기화** : 클래스 변수들을 적절한 값으로 초기화한다.

# 런타임 데이터 영역

> JVM의 메모리 영역
> 

<img width="639" alt="4" src="https://github.com/devhoody/TIL/assets/124743189/63bceb8f-e220-4257-801a-02c31ab6d2fe">

- JVM의 메모리 영역, 자바 애플리케이션을 실행할 때 사용되는 데이터를 적재하는 영역

### 스레드 공유에 따른 영역 구분

- 모든 스레드가 공유 : 메서드 영역, 힙 영역
- 각각 스레드 생성 : 스택 영역, PC 레지스터, 네이티브 메서드 스택

## 메서드 영역

바이트 코드를 처음 메모리 공간에 올릴 때, 클래스와 관련된 정보(초기화 대상 등)를 저장하는 영역

JVM이 종료될 때까지 저장되어 있다.

전역 변수(static), 상수 풀, 메타 데이터 등 **모든 클래스가 공유하는 영역**을 나타낸다.

> 상수 풀(Runtime Constant Pool) : JVM은 상수 풀을 통해 메서드나 필드의 실제 메모리 주소를 찾아 참조한다.
> 

## 힙 영역

프로그램이 실행되면서 런타임이 사용하는 동적 영역이다.

**new 연산자**를 이용해 생성된 인스턴스들이 적재된다.

ex) Person person = **new Person → Person 이라는 인스턴스가 힙 영역에 생성된다.**

GC(Garbage Collector)의 타깃이 되는 영역이다. GC에 대해서는 아래에서 자세히 살펴보자.

## 스택 영역

<img width="626" alt="5" src="https://github.com/devhoody/TIL/assets/124743189/89f16429-a88b-4023-b55f-f3ec3a2f4fe6">

LIFO 구조로 메서드 실행 시 담긴 지역 변수, 메개 변수가 저장되는 임시 저장소이다.

지역 변수, 매개 변수 등 다양한 정보는 `스택 프레임`에 담긴다.

> 스택프레임 : 특정 메서드만을 위한 공간, 현재 실행중인 메서드 정보가 들어있다.
> 

메서드가 종료되면 해당 스택 프레임도 스택 영역에서 제거된다.

기본 타입 변수는 스택 영역에 직접 값을 갖는다.

> 에러발생 : StackOverFlow ⇒ 스택 프레임이 쌓여 스택 영역이 꽉차 더이상 들어가지 못하는 경우 발생하는 에러
> 

## PC 레지스터

스레드마다 생성되고 JVM 명령의 주소값이 저장되는 공간이다.

명령의 주소값은 어떤 부분을 어떤 명령으로 실행해야할 지에 대한 기록을 가지고 있다.

## 네이티브 메서드 스택

자바 이외의 언어로 작성된 네이티브 코드를 위한 메모리이다.

실행엔진의 **JIT 컴파일러가 컴파일한 네이티브 코드가 이 영역에 저장**된다.

> 네이티브 코드 : 자바 이전의 언어이다. 예를 들어, C언어, C++, 어셈블리 등이 있다.
> 

## 실행엔진

실행 엔진은 런타임 데이터 영역에 올라온 바이트코드를 명령어 단위로 읽어 기계어로 번역하는 기능을 한다.

인터프리터와 JIT 컴파일러가 함께 이 기능을 수행한다.

여기서는 간단히 나타내고 자세한 것은 아래 글을 참고하자.

[컴파일 과정](https://www.notion.so/816e5447d5c645508af78390f503824c?pvs=21)

### 인터프리터

인터프리터는 컴파일러와 마찬가지로 프로그래밍 언어를 번역하는 기능을 수행한다.

자바에서는 바이트 코드의 각 행을 번역하고 다음 행을 번역하는 순서로 연속적으로 수행한다.

### JIT 컴파일러

- 바이트 코드를 한꺼번에 읽은 후, 네이티브 코드로 변환한다. 이 후, 캐싱해 두었다가 필요에 따라 네이티브 코드로 직접 실행한다.
- 컴파일된 네이티브 코드를 실행하므로 속도가 인터프리터보다 빠르다.
- 다만, 네이티브 코드로 변환하는데도 비용이 소모되므로, JVM은 처음에는 인터프리터를 사용하다가 일정 기준이 넘어가면 JIT 컴파일러를 이용한다.

### 가비지 컬렉터

- 힙 영역에 있는 쓰지 않고 방치된 데이터를 제거해주는 역할을 한다.
- 쓸데없는 메모리 낭비를 줄여준다.
- 상세한 내용은 아래 링크를 참고하자.

# JNI(Java Native Interface)

- 자바 애플리케이션에서 자바 이외의 언어로 만들어진 애플리케이션과 상호작용할 때 사용되는 인터페이스
- JVM의 네이티브 메서드 스택을 이용하는 것도 해당 인터페이스의 기능 중 하나이다.

# Native Method Library

- C, C++로 작성된 라이브러리



# Reference 
[https://inpa.tistory.com/entry/JAVA-☕-JDK-JRE-JVM-개념-구성-원리-💯-완벽-총정리#jvm_java_virtual_machine](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JDK-JRE-JVM-%EA%B0%9C%EB%85%90-%EA%B5%AC%EC%84%B1-%EC%9B%90%EB%A6%AC-%F0%9F%92%AF-%EC%99%84%EB%B2%BD-%EC%B4%9D%EC%A0%95%EB%A6%AC#jvm_java_virtual_machine)

[https://inpa.tistory.com/entry/JAVA-☕-JVM-내부-구조-메모리-영역-심화편#실행_엔진_execution_engine](https://inpa.tistory.com/entry/JAVA-%E2%98%95-JVM-%EB%82%B4%EB%B6%80-%EA%B5%AC%EC%A1%B0-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EC%98%81%EC%97%AD-%EC%8B%AC%ED%99%94%ED%8E%B8#%EC%8B%A4%ED%96%89_%EC%97%94%EC%A7%84_execution_engine)

https://www.quora.com/How-is-a-Java-compiler-different-from-the-general-compiler-process

https://youtu.be/UzaGOXKVhwU?si=7OcXiqY9OQQ5Us7E