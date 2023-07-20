- [생성자](#생성자)
    - [this](#this)
    - [Constructor](#constructor)
    - [Constructor Overload](#constructor-overload)
    - [Constuctor 생성 방법 3가지](#constuctor-생성-방법-3가지)
  - [Prototype 기반의 캡슐화](#prototype-기반의-캡슐화)
    - [prototype이란?](#prototype이란)
    - [Prototype, 클래스에서의 상속간의 차이](#prototype-클래스에서의-상속간의-차이)
    - [사용예시](#사용예시)
- [값과 형식의 비교](#값과-형식의-비교)
  - [null, undefined](#null-undefined)
    - [null](#null)
    - [undefined](#undefined)
  - [Instance of](#instance-of)
    - [정수값](#정수값)
    - [BigInt](#bigint)
  - [인스턴스 관리하기](#인스턴스-관리하기)
    - [Object prototypes](#object-prototypes)
  - [prototype과 상속의 관계](#prototype과-상속의-관계)
- [\* Reference](#-reference)


# 생성자

- `new`를 이용해서 `객체를 생성하고 초기화`하는 메소드

```jsx
function Exam(){
    this.kor = 10;
    this.eng = 20;
    this.math = 30;

    this.total = function() {
        return this.kor + this.eng + this.math;
    }
}

var exam = new Exam();
console.log("total", exam.total())
```

### this

- this는 `해당 함수의 호출 방식`에  따라 this에 바인딩되는 객체가 달라진다.
    - 기본적으로 this 선언 시 `window`이다.
    - 함수나 객체 내에서 this 선언 시엔 해당 함수나 객체를 가리킨다.
- 함수에  호출되기 전까지 this는 undefined이다.

### Constructor

- constructor 메소드는 클래스의 `인스턴스 객체를 생성하고 초기화`하는 메소드
- `모든 메서드보다 앞서` 인스턴스 객체를 초기화할 때 사용되는 메소드이다.
- 객체가 만들어질때 Prototype Object와 함께 생성되었던 함수를 가리킨다.
    - *ex) exam.constructor = new Exam();*

```jsx
var exam1 = new exam.constructor(1,2,3);
// var exam1 = new Exam(1,2,3);
console.log("total", exam1.total()); // 6
console.log("avg:" + exam1.avg()); // 2
```

### Constructor Overload

- `logical or(||), and(&&)연산자`를 이용하여 매개변수의 값을 입력한다.

```jsx
function Exam(kor, eng, math){
    this.kor = kor || 0 // or 연산자는 두 값 중, 왼쪽이 truthy일 때, 왼쪽을 도출한다. 
    this.eng = eng || 0;
    this.math = math || 0;
}
```

### Constuctor 생성 방법 3가지

```jsx
//기본 생성자
var exam = new Exam();
console.log("total", exam.total())
console.log("avg:" + exam.avg());

console.log("-----------------");

//오버로드 생성자
var exam = new Exam(10,10,10); 
console.log("total", exam.total())
console.log("avg:" + exam.avg());

console.log("-----------------");

//constructor 이용
var exam1 = new exam.constructor(1,2,3); 
console.log("total", exam1.total())
console.log("avg:" + exam1.avg());
```

## Prototype 기반의 캡슐화

### prototype이란?

- 모든 객체들이 메소드와 속성들을 상속받기 위한 `템플릿`
- 자바 스크립트는 `prototype 기반의 언어`
    - **자바스크립트에서의 모든 객체는 prototype을 갖는다.**
- *Prototype chain??*
    - *프로토타입 객체는 또다시  상위 프로토타입으로부터 메소드와 속성을 상속받고 또 그 상위의 프로토타입으로부터 메소드와 속성을 상속받는다. 이를 `prototype chain` 이라고 한다.*

### Prototype, 클래스에서의 상속간의 차이

- prototype : 객체를 상속받는것( = 틀로 이용하는 것)
- 클래스간 상속 : 부모클래스의 타입을 틀로 사용하는 것

### 사용예시

- 객체마다 똑같은 연산을 가진다면, 하나로 `공유`할 필요가 있다. 그렇지 않다면 객체 생성시마다 계속해서 연산 함수가 추가되어 메모리 낭비가 일어날 수 있다.
- 공유할 함수들을 prototype으로 묶어준다면, 객체 생성시에도 해당 함수들을 따로 만들 필요가 없어 `코드를 작성시에 용이`하다.
- Prototype에 total()과 avg()넣기

```jsx
function Exam(kor,eng,math){ // 변경 가능있는 데이터는 함수로
    this.kor = kor || 0;
    this.eng = eng || 0;
    this.math = math || 0;
}

Exam.prototype = { // 공유하고자하는 것들은 프로토타입으로!
    total:function(){
        return this.kor+this.eng+this.math;
    },
    avg:function(){
        return this.total()/3;
    }
}

var exam5 = new Exam(2,2,2);
var exam6 = new Exam(3,3,3);
console.log(exam5.total === exam6.total) // 프로토타입의 total을 공유한다.
```

# 값과 형식의 비교

## null, undefined

### null

- reference 변수에 대해 `주소값이 없는 것`을 표현하기 위한 키워드
- primitive 변수에는 null값을 대입할 수 없다.

### undefined

- 어떤 타입의 변수든 “값이 없다” 라는걸 표현

## Instance of

- 참조변수가 참조하고 있는 인스턴스의 타입을 알기위해 `instanceof 연산자`를 이용한다
- 기본 변수는 typeof를 이용하면 된다.
- 예시) 배열 타입 확인

```jsx
console.log(nums instanceof Array); // true
console.log(Array.isArray(nums)); // true
```

### 정수값

- integer의 최대값 : `MAX_SAFE_INTEGER`
    - 이 이후의 숫자는 BigInt 이용

### BigInt

```jsx
var x = 0n; // 정수값에 n을 붙임 
```

## 인스턴스 관리하기

- 객체가 더이상 쓸모가 없어지면, null값을 대입해주어 조금 더 빨리 가비지를 통해 제거가 가능해진다.

```jsx
var arr = [1,2,3];

// 쓸모 없어짐.
arr = null; // null을 넣어줌으로써 가비지가 더 빨리 제거시킴
```

- 참조를 아무것도 하지않으면 가비지컬렉터가 제거해준다.
- 객체 속성 앞에다가 delete하면 속성이 제거된다.

```jsx
delete arr;
```

### Object prototypes

- 객체 상속 → 객체 공유
    - 같은 객체를 공유한다.
    
    ```jsx
    Exam.prototype // 생성자 이용
    
    var exam1 = new Exam();
    
    exam1.constructor.prototype; // 객체 이용 
    exam1.__prototype__;
    Object.getPrototypeOf(exam1) // Object객체 내 메소드 이용
    
    ```
    
- 객체 이용하여 prototype 객체 얻기

```jsx
console.log(newExam.__proto__); // kor : 2 eng : 2 math : 2 함수에 있는걸 
console.log(Object.getPrototypeOf(newExam)); // kor : 2 eng : 2 math : 2
console.log(NewlecExam.prototype); // kor : 2 eng : 2 math : 2
```

- 객체의 프로토타입에는 함수와 객체 모두 입력 가능하다.
- 객체의 프로토타입을 설정하면 constructor가 변경된다.

## prototype과 상속의 관계

- prototype 내 값
    
    ```jsx
    var base = {
            kor : 10,
            eng : 20,
            math : 30
        }
    ```
    
- shadowing
    - 부모에게 받은 값에 덮어씌우는것
    
    ```jsx
     var exam = {
            com : 10,
            __proto__ : base
        };
    
    exam.kor = 10; // exam내 kor 속성 생성, kor값 10 대입.
    ```
    
- **Object.getPrototypeOf()**
    - 지정된 객체의 프로토타입의 속성값을 반환한다.
        - 내부[prototype] 속성값 반환
    
    ```jsx
    getPrototypeOf(exam).kor = 4;
    ```

# \* Reference

- [https://inpa.tistory.com/entry/JS-📚-this-총정리](https://inpa.tistory.com/entry/JS-%F0%9F%93%9A-this-%EC%B4%9D%EC%A0%95%EB%A6%AC)
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/constructor
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/this