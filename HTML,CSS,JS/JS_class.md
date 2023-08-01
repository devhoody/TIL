# Class
- [Class](#class)
  - [Constructor](#constructor)
  - [Strict Mode](#strict-mode)
    - [Strict Mode 특징1 - 이전에 허용된 실수를 용납하지 않는다.](#strict-mode-특징1---이전에-허용된-실수를-용납하지-않는다)
  - [은닉화](#은닉화)
    - [캡슐화를 위한 보호모드](#캡슐화를-위한-보호모드)
    - [Getters/ Setters 지원](#getters-setters-지원)
    - [Static의 getter setter](#static의-getter-setter)
  - [클래스 상속](#클래스-상속)
    - [상속으로부터 받은 데이터를 확장하기(com 추가)](#상속으로부터-받은-데이터를-확장하기com-추가)
    - [자식, 부모 클래스와 객체 간의 관계](#자식-부모-클래스와-객체-간의-관계)

## Constructor

- `new` 키워드를 이용해서 클래스의 객체를 만들때 자동으로 실행된다.
- class 내의 값 초기화를 위해 이용되기도 한다.

```jsx
class Exam {
	kor;
	eng;
	math;

  constructor(kor=0, eng=0, math=0) {
    this.kor = kor;
    this.eng = eng;
    this.math = math;
  }
```

 *kor, eng, math에 직접 0으로 초기화를 시키고, 값을 입력하면 생성자 함수에 의해 객체 내에 입력받는다.

## Strict Mode

- 선언 시 해당 위치가 적용 범위에 해당한다.
    - 함수 내에서 선언하면 해당 함수 내에서만 엄격모드가 적용.
- **클래스 내의 모든 영역은 strict mode가 적용되어 있다.**

```jsx
"use strict"
```

### Strict Mode 특징1 - 이전에 허용된 실수를 용납하지 않는다.

```jsx
"use strict";
let mistypedVariable; 

mistypedVaraible = 17; // 오타 발생

console.log(mistypedVaraible); // 오타로 인한 오류 발생
```

## 은닉화

### 캡슐화를 위한 보호모드

- `#변수명`
    - 은닉화 하고자하는 변수에는 전부 #을 붙여주어야 한다.
    
    ```jsx
    class Exam {
      #kor
      #eng
      #math
    
    	constructor(kor=0, eng=0, math=0) {
    	  this.#kor = kor;
    	  this.#eng = eng;
    	  this.#math = math;
    	}
    	
    	total() {
    	  return this.#kor + this.#eng + this.#math;
    	}
      
    }
    let exam = new Exam();
    console.log(exam.#kor);
    console.log(exam.total());
    ```


### Getters/ Setters 지원

<aside>
💡 get vs **defineProperty**

</aside>

- 자바스크립트에도 자바처럼 Getters와 Setters가 존재한다.

```jsx
getKor() {
  return this.#kor;
}

setKor(value) {
  this.#kor = value;
}

let exam = new Exam();
exam.setKor(100);
console.log(exam.getKor());
```

- getter와 setter를 단순화 시켜 `get kor()`, `set kor()`로 선언할 수 있다.

```jsx
get kor() {
  return this.#kor;
}

set kor(value) {
  this.#kor = value;
}

let exam = new Exam();
exam.kor++;
console.log(exam.kor);
```

- 클래스안에서 static 멤버를 쓸때는 this처럼 해당 클래스의 멤버라는 걸 꼭 표시해 주어야한다.

```jsx
class Exam {
	#kor
	#eng
	#math
	
	static count = 0;
	
	constructor(kor=0, eng=0, math=0) {
	  this.#kor = kor;
	  this.#eng = eng;
	  this.#math = math;
	
	  Exam.count++; // Exam을 꼭 붙여주어야한다. 
}
```

- static은 클래스 안에서 선언되어도 선언 위치만 클래스 안에 있지 소속은 `전역변수`에 속하게된다. 그래서 Exam을 붙이지 않으면 에러 발생
    - *this를 붙여도 사용은 가능하나, 모든 객체가 공유가 되기에 this보다는 클래스명을 명시하여 사용하는게 좋다.*

- *static변수인 count가 생성자 함수에 속하는게 맞나??*
    - 생성자 함수는 클래스 내의 멤버들을 생성해주는 함수인데 static변수는 클래스 내의 멤버가 아닌 전역변수 멤버이기에 생성자 함수에 속하면 안된다.
    - 따라서 static변수를 위한 다른 함수를 정의해주어야 한다.
- static은 인스턴스를 넘겨받지 못한다. ⇒ `exam.getKor() (x) → Exam.getKor(exam)(o)`
- static은 프로그램이 실행될 때 메모리 영역에 할당되므로, 인스턴스 과정(객체생성)없이 모두가 접근할 수 있는 공유자원인 것이다. 따라서 this를 이용하지 못한다.
    
    

### Static의 getter setter

- static은 클래스 내의 변수를 이용하는게 아니기 때문에 this가 아닌 클래스 이름을 이용하여 값을 대입하거나 할당한다.

```jsx
class Exam {
	#kor
	#eng
	#math
	static #count = 0;
	
	static {
	Exam.#count = 100; // this(x) => Exam 클래스 이용
	}
	
	static get count() {
		return Exam.#count; // this(x) => Exam 클래스 이용
	}
	
	static set count(value) {
		Exam.#count = value; // this(x) => Exam 클래스 이용
	}
}
```

## 클래스 상속

- 자바와 마찬가지로 `extends`를 이용하면 상속이 가능하다.

### 상속으로부터 받은 데이터를 확장하기(com 추가)

- 자바와 다른점
    - 자바스크립트에서는 자식의 생성자를 선언할 때, `꼭 부모의 생성자(super)를 호출해야한다.`
        
        ```jsx
        class NewlecExam extends Exam{
          // com 생성
          #com;
          // 생성자 생성
          constructor(kor, eng, math, com) {
            super(kor, eng, math); // super 필수!
            this.#com = com;
          }
        
          // total 오버라이드
          total() {
            return super.total() + this.#com; // super 필수!
          }
        }
        ```
        

### 자식, 부모 클래스와 객체 간의 관계

```jsx
let exam2 = new NewlecExam(10,10,10,10);
console.log(exam2.total());
console.log(typeof exam2, typeof Exam, typeof NewlecExam);
console.log(exam instanceof Exam, exam2 instanceof Exam); // true true
console.log(exam instanceof NewlecExam, exam2 instanceof NewlecExam); // false true
console.log(Object.hasOwn(exam.__proto__, 'total')); // true
```

- `hasOwn` : 객체가 속성을 갖고 있는가에 대한 함수

```jsx
Object.hasOwn(obj, prop);
```

- *for in을 이용하여 key값을 꺼내면 #이 아닌 값들만 나온다.*