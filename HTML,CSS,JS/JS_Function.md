# 함수
- [함수](#함수)
  - [Rest Parameter](#rest-parameter)
  - [Spread Operater](#spread-operater)
    - [함수 호출 시 …(변수명)](#함수-호출-시-변수명)
    - [함수의 매개변수 뽀개기](#함수의-매개변수-뽀개기)
  - [Default Value](#default-value)
  - [Arrow Functions(람다 표현식)](#arrow-functions람다-표현식)
    - [Arrow Function은 this를 가지지 않는다.](#arrow-function은-this를-가지지-않는다)
    - [Arrow Function은 arguments를 가지지 않는다.](#arrow-function은-arguments를-가지지-않는다)
    - [Arrow Function은 생성자로 사용할 수 없다. this 관련 행위를 가지지 않는다.](#arrow-function은-생성자로-사용할-수-없다-this-관련-행위를-가지지-않는다)
    - [bind ()](#bind-)
  - [call(), apply(), bind ()](#call-apply-bind-)

## Rest Parameter

- `함수의 매개 변수`에서 `여러 인수를 배열로 수집`하는데 사용한다.
- 기본양식

```jsx
function functionName(...restParameters) {
  // Function body
}
```

- 예시

```jsx
function print(x, y, ...bb) {
    console.log(x, y, bb[0], bb.length, arguments[0], arguments[2], arguments.length);
  }
  
  print(3, 4, 5, 6, 7);
```

## Spread Operater

- 배열의 요소를 `개별 요소로 확장`시키고자할 때 사용한다.
- []으로 감싸진 배열을 `…` 을 이용하여 []을 제거시킨다.

### 함수 호출 시 …(변수명)

```jsx
function print(x, y, ...bb) {
  console.log(x, y, bb[0], bb.length, arguments[0], arguments[2], arguments.length);
}

function print1(nums) {
    console.log(nums);
  }

  let nums = [4, 29, 19, 49];

  print1(nums); // 매개변수 4 ,29, ,bb =>(19,49)
  print(...nums); // 4, 29, 19, 49
```

- 예제
    - 문제
    
    ```jsx
    {
        //1번 문제 - 민아
        // 아래의 코드에서 c(a, b)의 값을 예상해서 적어보세요.
    
        var a = [1, 2, 3];
        var b = ["you", "are"];
    
        var c = function (a, b) {
        console.log([[...a], ...[...b]][1]);
        };
        c(a, b);
    
        {
        // 이름 :
        // 예측한 정답은 여기에 기입~~~
        }
    ```
    

### 함수의 매개변수 뽀개기

- 크게 배열과 객체로 나누어 뽀개보았다.

```jsx
// 함수 내 배열 뽀개기
function print2([x, y, z]) { 
  console.log(x, y, z);
}

let nums = [4, 29, 19, 49];

print2(nums);

// 함수 내 객체 뽀개기 
function print3({kor, eng, math}) {
  console.log(kor, eng, math);
}

let exam = { kor: 30, eng: 20, math: 50 };
print3(exam);
```

## Default Value

- 함수의 기본값은 매개변수를 이용하여 직접 기본값을 설정해주어야한다.
- 방법은 총 3가지가 있다.
    - 값이용 다른 함수이용, 변수 이용,

```jsx
function func(){
	return 100;
}

function print4(x= func(), y = 10, z = x+1) { // x: 값 이용
    console.log(x, y, z);
    console.log(arguments.length);
    console.log(arguments[1]);
}

print4(10, null, 10); // 10, null, 10
print4(10, undefined, 10); // 10, 10, 10
print4(10, undefined); // 10, 10, 11
print4(1); // 1, 10, 12
```

- arguments는 전달된 인자만 값이 대입된다.

## Arrow Functions(람다 표현식)

- 함수 정의를 간편하게 사용 가능.
- 정렬을 하는 배열의 메소드 sort()를 간단하게 표현할 수 있는 방법
    - sort()는 `문자열을 기준으로 배열`하기때문에 정수를 정렬할 때 가장 앞자리의 수를 `아스키코드의 순서대로 비교`하여 정렬.

```jsx
let nums = [10, 20, 29, 82, 39, 28, 100];

console.log(nums.sort()); // 10, 100, 20, 28, 29, 39, 82
nums.sort(function (a, b) {
  return a - b;
});
console.log(nums); // 10, 20, 28, 29, 39, 82, 100

nums.sort((a, b) => a - b);
console.log(nums); // 10, 20, 28, 29, 39, 82, 100

nums.sort((a, b) => b - a);
console.log(nums); // 100, 82, 39, 29, 28, 20, 10
```

### Arrow Function은 this를 가지지 않는다.

- 일반 함수에서 `this`는 함수가 호출되는 방식에 따라 동적으로 바인딩된다.

```jsx
let x = 40;

let game = {
  x : 30,
  run() {
	    setTimeout( function() {
	      console.log(this); // window객체
	      this.x++; // undefined ++ => NaN
	      console.log(this.x); // NaN 
	    },1000)
		}
	}
  game.run();
}
```

- Arrow Function의 this는 객체 내에 있는 값을 참조한다.

```jsx
let x = 40;

let game = {
  x : 30,
  run() {
    setTimeout(() => {
      console.log(this); // {x : 30, run : f}
      this.x++; // 30++;
      console.log(this.x); // 31
    },1000);
	}
}

game.run();
}
```

- 해당 Arrow Function의 this는 객체 game을 참조한다. 따라서 this.x는 game객체 내의 x 변수의 값을 받는다.

### Arrow Function은 arguments를 가지지 않는다.

```jsx
let print = () => {
  console.log(arguments[0], arguments[1]);
}

print(2, 3); // 출력 안됨.
```


### Arrow Function은 생성자로 사용할 수 없다. this 관련 행위를 가지지 않는다.

```jsx
let print = function(){
  // console.log(arguments[0], arguments[1]);
  console.log(`normal function`, this);
};

let print1 = () => {
  // console.log(arguments[0], arguments[1]);
  console.log(`arrow function`, this);
}

print(2, 3);
new print(2, 3);

print1(2, 3);
// new print1(2, 3); // << 에러 발생!
console.log(print.prototype);
console.log(print1.prototype); // undefined
```

- 객체 내에 있는 함수의 this는 **객체에 있는 값**을 참조, 전역 변수 내에 있는 함수의 this는 `윈도우` 참조.

### bind ()

- 화살표함수를 이용해서 `arguments, this를 이용`하고자 할때 `bind를 이용`한다.
- 함수 내 **this의 참조를 다른 객체에 있는 변수와 연결시켜주기**

```jsx
let game = {
  x : 30,
  run() {
    console.log(this); // this : game
    setTimeout(function () {
      console.log(this); // this : window => game
      this.x++;
      console.log(this.x);
    }.bind(this), 1000); // this : game
  }
}

game.run();
```

- 함수를 호출하면서 객체를 붙여줄 수 있는 방법이 있다. 객체 내 함수가 없을때도!!
    - 이게 콜 어플라이 바인드!

## call(), apply(), bind ()

- bind : `함수를 정의`하면서 객체를 붙여준다.
- call : 호출할때 객체를 붙여주고, `나열`하면서 매개별수를 전달
- apply : 호출할 때 `배열로 묶어서` 매개변수를 전달

```jsx
let f = function () {
  console.log(this); // this : 윈도우
  this.x++;
  console.log(this.x);
};

let game2 = {
  x: 10
}

f.call(game,100,200); // 객체를 객체
f.call(game2); // 함수를 연결
f.apply(game, [100, 200]);
```