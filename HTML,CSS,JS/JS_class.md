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
  - [ball.js 클래스 적용하기](#balljs-클래스-적용하기)
    - [ES6 이전에 귀찮은점](#es6-이전에-귀찮은점)
  - [ES6 모듈 시스템](#es6-모듈-시스템)
    - [export 키워드](#export-키워드)
    - [모듈화된 두 스크립트 내의 기능의 이름이 같을 경우](#모듈화된-두-스크립트-내의-기능의-이름이-같을-경우)
    - [default](#default)
    - [import에 기능이 많아져 이름을 붙이기 귀찮을때는 \* 을 이용.](#import에-기능이-많아져-이름을-붙이기-귀찮을때는--을-이용)
    - [단순히 다른 js파일에 있는 데이터를 읽고 싶을때. (이용x)](#단순히-다른-js파일에-있는-데이터를-읽고-싶을때-이용x)

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

## ball.js 클래스 적용하기

- 은닉화 한번에 적용하기 ⇒ 일괄 텍스트 변경 기능 이용하기
  - `this. ⇒ this.#`

### ES6 이전에 귀찮은점

1. 여러 자바스크립트 파일을 이용해야하는데 충돌날 가능성이 있다.
2. 기존에는 index.html에 script의 파일을 등록해주어야한다. 

```jsx
<script src="Ball.js" defer></script>
```

- 각 파일끼리 충돌을 방지하고 고립화하기 위한 모듈 시스템이 필요하다.

## ES6 모듈 시스템

- 이름충돌이 안나도록 이용할 자바스크립트 파일을 넣는 것을 html에게 맡긴다.

```jsx
<script type="module" src="game.js" defer></script>
```

### export 키워드

- 노출하고 싶은 기능을 `export`써서 노출시킨다.
    - 해당 기능은 작성한 자바스크립트 파일안에서 작성!
    - 기능 이름은 그대로 작성한다.
- 안그러면 일반적으로 자바스크립트 내의 기능은 숨겨져 있는 기능으로 되어있음. ⇒ 고립화!
- 기본 양식 2개
    - **기능을 구현하고 따로 export 문을 작성하여 export한다.**
        
        ```jsx
        function test() {}
        function test1() {}
        
        export {test, test1};
        ```
        
    - **기능 구현 시 export 키워드를 붙여 export한다.**
        - import를 할때 원하는 이름을 붙이기위해 `as` 를 쓰지만, `default`를 붙이면 as를 쓰지 않고도 자신이 원하는 이름으로 기능명을 사용할 수 있다.
        
        ```jsx
        export dafault function test() {} // import시 바로 원하는 이름으로 작성
        export function test1() {} // import시 as를 이용하여 원하는 이름으로 이용
        
        // import문
        import aa, { test1, test2 } from './module/lib2.js';
        ```
        
- 내보내고(export)⇒  받는다.(import)
- 객체나 함수 이외에도 다양한 형식도 노출이 가능하다.

### 모듈화된 두 스크립트 내의 기능의 이름이 같을 경우

- 기능의 이름을 import에서  `as` 를 이용해 바꿔주어야한다.

```jsx
import { test as lib1Test, test1 as lib1Test1 } from './module/lib1.js';
import { test, test1 } from './module/lib2.js';
import { Ball } from './ball.js';
```

### default

- `as를 이용하지않고` 원하는 기능 이름을 사용하게 만들어줌

```jsx
import { test as lib1Test, test1 as lib1Test1 } from './module/lib1.js';
import aa, { test1, test2 } from './module/lib2.js';
import { Ball } from './ball.js';
```

### import에 기능이 많아져 이름을 붙이기 귀찮을때는 * 을 이용.

- `import * as JS파일명` 을 이용해 해당 js파일 내에 존재하는 export들을 꺼낸다.
- 해당 기능들을 이용할 때에는 객체지향처럼 `js파일명.기능` 으로 호출할 수 있다.

```jsx
import * as lib1 from './module/lib1.js';

lib1.test();
console.log(lib1.data.x);
```

### 단순히 다른 js파일에 있는 데이터를 읽고 싶을때. (이용x)

- 모든 모듈들이 공유하고 있는 객체(window객체)를 이용해서 단순히 읽는다.
- 자바스크립트 파일에서의 let으로 선언된 변수는 모든 모듈들이 공유하고있는 window객체로 들어가지 않음.