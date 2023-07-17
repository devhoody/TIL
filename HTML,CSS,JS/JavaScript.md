## 목차 

- [자바스크립트란?](#자바스크립트란)
  - [Document Object](#document-object)
  - [자바스크립트 탄생배경](#자바스크립트-탄생배경)
- [자바스크립트 기본 타입](#자바스크립트-기본-타입)
- [자바스크립트의 특징](#자바스크립트의-특징)
- [LiFO](#lifo)
  - [LiFO란?](#lifo란)
- [FiFO](#fifo)
  - [FiFo란?](#fifo란)
  - [Dequeue](#dequeue)
- [JSON](#json)
  - [JSON 표기법](#json-표기법)
  - [JSON을 이용한 초기화](#json을-이용한-초기화)
- [제어구조](#제어구조)
  - [for-in](#for-in)
  - [for-of](#for-of)
- [Function](#function)
  - [함수 만드는 3가지 방법](#함수-만드는-3가지-방법)
  - [함수 매개변수 특징](#함수-매개변수-특징)
    - [숫자 와  undefined 연산  ⇒ NaN](#숫자-와--undefined-연산---nan)
  - [전역 변수의 특징](#전역-변수의-특징)
    - [var 유무에 대한 차이](#var-유무에-대한-차이)
  - [지역 변수의 특징](#지역-변수의-특징)
  - [\*출처](#출처)


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
- symbol

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

# 제어구조

## for-in

- `객체의 속성`들을 반복적으로 순회하면서 해당 속성에 대한 작업을 수행할 때 사용됩니다.
- value값이 존재하는 key-value 쌍만을 취급하는 제어문

```jsx
var names = [,"철수","영희","맹구",,"동천"];

//for
for(var i =0; i<names.length; i++)
    console.log(names[i])

//for-in
for(var i in names)
    console.log(names[i]) // ["철수","영희","맹구","동천"]

//for-of
for(var name of names)
     console.log(name); // [undefined,"철수","영희","맹구", undefined,"동천"];

```

## for-of

- 데이터 구조를 반복적으로 순회하면서 각 `value`에 대한 작업을 수행할 때 사용.
- 기본 양식

```jsx
for (variable of iterable) {
  // 반복 수행할 코드
}

// variable : 현재 반복되고 있는 항목 변수
// iterable : 반복 가능한(iterable) 객체
```

# Function

- JS에서 모든 함수는 `객체`이다.
    - *new Function()*
- 함수가 반복되면 객체도 계속해서 생성되므로, 메모리 오버가 생길 수 있으므로 주의해야한다.

## 함수 만드는 3가지 방법

```jsx
var add = new Function("x, y", "return x + y");
  console.log(add(3,4))

// 뉴렉쌤 추천 
var add = function(x,y){return x+y;};
  console.log(add(3,4))

// 위 두개와 같이 사용 안됌
function add(x,y){return x+y;};
  console.log(add(3,4));
```

- *함수명이 add인 add()와 변수명인 add와 중복해서 사용할 수는 없다.*

## 함수 매개변수 특징

- js는 arguments를 받는게 따로있다.
    - argument는 `가변 객체`로 입력받는 매개변수의 개수에 따라 길이가 정해진다.
    
    ```jsx
    var add = function(){
    	  var x = arguments[0]
    	  var y = arguments[1]
    		var z = arguments[2]
    	
    	  console.log("arguments :" + arguments.length)
    	  var sum = 0;
    	
    	  for(var arg of arguments)
    	      sum += arg
    	
    	  return x+y+z; 
    };
    console.log(add(2,3,4)) // 9 출력 
    ```
    

### 숫자 와  undefined 연산  ⇒ NaN

- Number와 undefined 와의 연산은 NaN값을 도출한다.

```jsx
var add = function(){
    var x = arguments[0] // 2
    var y = arguments[1] // 3
    var z = arguments[2] // 4
    var x1 = arguments[3] // 5

    var y1 = arguments[4] 
    console.log(y1) // undefined
    console.log("y1+x:" + (y1+x)) // NaN
    console.log("y1-x:" + (y1-x)) // NaN
    console.log("y1*x:" + (y1*x)) // NaN
    console.log("y1/x:" + (y1/x)) // NaN

    return sum; 
};
console.log(add(2,3,4,5)) // 14

```

## 전역 변수의 특징

- 전역 변수를 생성하는 방법에는 두가지가 있다.
    - <script> 필드에서 var를 이용하여 전역변수 선언하는 방법
    - `a=1` 로 선언과 동시에 값을 대입하는 방법
- 자바스크립트도 `전역변수를 준비물`로 인식한다.

```jsx
alert(a) // undefined 출력 => 준비물 a는 인식한다.
var a= 1; // var 라는건 준비물로 인식해달라는 것!

alert(a) // "a is not defined" 에러 발생. 
a = 1; // 준비물로 인식을 하지않아 window 객체에 속성 a가 되지 않는다. => 미리 준비 x
```

- `전역공간에 있는 변수는 모두 window.a(전역객체)의 속성`으로 들어가게된다.

```jsx
console.log(window.a); // window 속성에 a값 있다고 가정 후 출력 
console.log(a); // window 속성에 a 없다고 가정 후 출력
 
a = 1; // window 속성에 들어감 

console.log(window.a); // 1출력 
console.log(a); // 1 출력
```

### var 유무에 대한 차이

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/85ae6527-d408-4418-a2f3-04e559a55426/Untitled.png)

- `변수를 미리 준비하는 것`의 유무
- 전역 공간에서의 선언은 곧 `window(전역) 객체의 속성`이 된다.
- 위의 경우 var a; 에 의해 window안에는 a 함수가 존재하게 된다.
    
    ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ddb2617-6bdf-487b-81d9-623bcd26a8e5/Untitled.png)
    

## 지역 변수의 특징

- `함수 내에서 변수가 선언`(ex) var a)된다면 **지역변수화**가 되어 전역변수인 window객체내 속성으로 존재하지 않는다.
    - *이는 함수 내에서 a = 1과 같이 값을 대입하더라도 지역변수화를 제어할 수 없다.*
- 지역 내에 var로 선언되지 않으면 해당 변수는 전역변수로 간주된다.
- 지역변수에 선언되는 변수가 `우선순위가 가장 높다.`

```jsx
// var a가 있을때
var f1 = function() {
        a = 1; 

        var a; // 지역변수 a가 우선순위

        a++;

        alert(a); // 2 => 지역변수 a
        alert(window.a); // undefined => 전역변수 a
    }
alert(a); // 에러가 뜸. => 전역변수 a

// var a가 없을때
var f1 = function() {
        a = 1; // 전역변수 a가 우선순위

        //var a; 

        a++;

        alert(a); // 2 => 전역변수 a
        alert(window.a); // undefined => 전역변수 a
    }

alert(a); // 2 => 전역변수 a
```

## *출처

- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Functions/arguments

\*Reference

- https://www.allaboutlean.com/fifo-benefits/fifo-vs-lifo/
