# JavaScript

## 1. querySelector 메서드를 이용한 HTML 요소 선택

```JavaScript
document.querySelector('class, id name').style.backgroundColor="black"
```

`document.querySelector('class, id name'):`

querySelector 메서드를 이용하여 클래스 또는 ID 이름으로 HTML 요소를 선택한다.
`클래스명은 .클래스명, id명은 #id명` 을 이용한다.

`style.backgroundColor`

선택한 HTML요소에 CSS 속성을 부여할 때 사용한다.

만약 HTML요소 전체를 선택하고자 할때 : `document.querySelectorAll('*')`을 사용하면 된다.

## 2. if-else를 이용한 토글 구현

```JavaScript
<input id="night_day" type="button" value="night" onclick ="
        if(document.querySelector('#night_day').value === 'night') {
            document.querySelector('body').style.backgroundColor='black';
            document.querySelector('body').style.color='white';
            document.querySelector('#night_day').value= 'day';
        }   else {
            document.querySelector('body').style.backgroundColor='white';
            document.querySelector('body').style.color='black';
            document.querySelector('#night_day').value= 'night';
        }
    ">
```

## 3. JavaScript에서의 함수

```JavaScript
function functionName(parameter){

}
```

함수는 중복되는 부분이 많을 때 사용하면 효과적인 리팩토링 기능을 갖는다.

함수는 꼭 `<script></script>`에 넣지 않아도 된다.

## 4. JavaScript에서의 객체

```JavaScript
var 객체명 = {
    Key1 : Value1
    Key2 : Value2
    ...
}
```

객체는 값이 될 수 있는 속성들의 모음으로 key-value 쌍의 모음이다.
약간 `자바의 컬렉션` 과도 비슷하다.

해당 강의에서는 함수가 많아짐에 따라 함수를 묶을 수 있는 key값으로 body와 Link로 구분했다.

```JavaScript
// Link끼리
var Link = {
    setColor : function (color) {
        var alist = document.querySelectorAll('a');
        var i =0;
            while (i<alist.length) {
                alist[i].style.color = color;
                i = i+1;
            }
        }
    }

// Body끼리
var Body = {
    setColor : function(color){
        document.querySelector('body').style.color = color;
    },
    setBackgroundColor : function(color){
        document.querySelector('body').style.backgroundColor = color;
    }
}
```

객체를 사용하는 건 `점 표기법`인 `(Key값.Value값)`을 이용한다.

```Javascript
function nightDayHandler(self) {
    var target = document.querySelector('body');
if(self.value === 'night') {
    Body.setBackgroundColor('black');
    Body.setColor('white');
    self.value= 'day';

    Link.setColor('powderblue');
}   else {
    Body.setBackgroundColor('white');
    Body.setColor('black');
    self.value= 'night';

    Link.setColor('blue');
    }

}
```

추가로 `생성자 함수를 사용한 객체 생성` 과 `ES6에서의 class를 이용한 객체생성`를 알아보았다.

### 4.1 생성자 함수를 사용한 객체 생성

```Javascript
function Person(name, age, gender) {
  this.name = name;
  this.age = age;
  this.gender = gender;
}

var person = new Person('Reto', 31, Male);
```

함수를 선언하고 this를 이용한 key값 설정, new Person을 이용하여 key값에 맞는 value를 지정한다.

### 4.2 ES6에서의 class를 이용한 객체생성

```Javascript
class Person {
  constructor(name, age, gender) {
    this.name = name;
    this.age = age;
    this.gender = gender;
  }
}

var person = new Person('John', 30, 'Male');
```

## 4. JavaScript 파일 이용하기

```Javascript
<script src ="colors.js"></script>
```

`src=파일명.js`를 이용하여 js 파일을 이용한다.

## 5. jQuery

> jQuery를 본격적으로 배울 때 추가예정

### 5.1 jQuery불러오기

```JavaScript
<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.6.4/jquery.min.js"></script>
```

### 5.2 jQuery 이용하기

```JavaScript
$('클래스명').css('background-color', 'red');

$('a').css("color", color);
$('body').css("color", color);
```
