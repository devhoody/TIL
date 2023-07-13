# 자바스크립트란?

- 입출력 플랫폼을 이용하여 `사용자의 입출력을 가능하게하기 위한` 프로그래밍 언어

## Document Object

- 자바스크립트를 이용하여 사용자 입출력을 하기위해 사용되는 다큐먼트 객체

## 자바스크립트 탄생배경

- 입출력 객체를 통해 사용자가 입력한 값을 서버에 전송하고자할 때, 유효성 검사 없이 입력한 값들을 계속 서버에 전송하게 되면 서버에 과부하가 걸릴 위험이 있다. 이에 `유효성 검사를 통해 서버에 전송하는 데이터를 제어하기 위해 사용되는 언어`가 바로 자바스크립트이다.

# 자바스크립트 기본 타입

- Boolean
- Number
- String
- Undefined, null
  - Undefined는 `초기`에 할당되지 않은 변수의 값, null은 `명시적`으로 할당하지 않은 값

# 자바스크립트의 특징

1. `변수의 타입`을 명시적으로 선언하지 않아도 된다.
2. `‘(single quotation) 과 , “(double quotation)`의 차이가 거의 없다.
3. 객체는 가변 길이를 `동적`으로 가진다.
4. `Wrapper 클래스`에 의해 값에 박스가 자동으로 생성된다.
5. 자바와는 다르게 코드 끝에 `‘;’`를 꼭 붙일 필요는 없다.

# LiFO

## LiFO란?

- LiFO(Last input First Output, 선입선출)으로 `마지막으로 들어간 값이 가장 먼저 나가게되는 데이터 흐름`을 말한다. 대표적인 예시로는 자료구조인 `Stack`이 이에 해당한다.
- 관련 함수로는 `push(), pop()`이 있다.

```jsx
var nums = new Array();

nums.push(4); // 값 넣기
var n1 = nums.pop(); // 마지막 값 빼내기
```

# FiFO

## FiFo란?

- FiFO(First input First Output, 선입선출)로 `가장 먼저 들어간 값이 가장 먼저 나가게 되는 데이터 흐름`을 말한다. 대표적인 예시로는 자료구조인 `Que`가 이에 해당한다.
- 관련 함수로는 `push(), shift()`가 있다.

## Dequeue

- Dequeue는 `양쪽에서 삽입 삭제가 가능`한 Stack과 Que을 혼합하여 사용되는 자료구조이다.

# JSON

- 객체를 생성하는 간단한 표현식

## JSON 표기법

```jsx
var n = true; // var n = new Boolean(true);
var n = 3; // var n = new Number(3);
var s = "hello", or 'hello' // var s = new String("hello");
var ar = []; // var ar = new Array();
var ob = {}; // var ob = new Object();
```

## JSON을 이용한 초기화

```jsx
//JSON 사용 전
var notice = new Object();
notice.id = 1;
notice.name = "후디";

//JSON 사용 후
var notice = { id: 1, name: "후디" };
```

\*Reference

- https://www.allaboutlean.com/fifo-benefits/fifo-vs-lifo/
