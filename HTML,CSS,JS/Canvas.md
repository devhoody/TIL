

> 이벤트 리스너  정리 추가해야된다.


# Canvas

### canvas API란?

- `HTML에 그래픽을 입히기 위한 수단.` SVG와 같은 그래픽 요소를 추가하기 위해 쓰이는 다양한 API가 존재하는데 Canvas는 아주 `Raw한 기능을 제공하는 API`이다. 하지만 아주 Raw하기 때문에 다른 그래픽 API에 비해 작업하기 어렵다는 단점이 있다.

### 사각형 그리는 방법

- `fillRect` - 색을 채운 사각형
- `clearRect` - 색을 비운 사각형
- `strokeRect` - 테두리만 그린 사각형
- 기본 양식
    
    ```jsx
    ctx = 캔버스명.getContext("2d")
    
    ctx.fillRect(x, y, width, height);
    ctx.clearRect(x, y, width, height);
    ctx.strokeRect(x, y, width, height);
    
    // x : 사각형의 왼쪽 상단 모서리의 x좌표
    // y : 사각형의 왼쪽 상단 모서리의 y좌표
    
    ```
    
  

- beginPath ⇒ 그림 그리기 시작
- moveto(그림 안그리고 이동) ⇒ lineto( 그림 그리면서 이동)

```jsx
ctx.fillRect(25,25,100,100);
ctx.clearRect(45,45,60,60);
ctx.strokeRect(50,50,50,50);

ctx.beginPath();
ctx.moveTo(75, 50);
ctx.lineTo(100,75);
ctx.stroke();

ctx.beginPath();
ctx.moveTo(100,75);
ctx.lineTo(75,100);
ctx.stroke();
```

## 라인과 도형 색 채우기(fill과 stroke의 차이)

### moveTo, lineTo, fill

- fill은 closePath를 하지 않아도 `자동`으로 색이 채워진다.

### moveTo, lineTo, closePath, stroke, fill

- stroke는 자동으로 닫히지 않아 `closePath`를 해야한다.

## arc

- 원의 x,y는 원의 중심 좌표를 말한다.

```jsx
arc(x, y, radius, startAngle, endAngle, counterclockwise)
// counterclockwise = false => 시계방향
// radius : 반지름 

arcTo(x1, y1, x2, y2, radius)
```

## 베지어곡선

```jsx
quadraticCurceTo()
bezierCurveTo()
```

## Path2D object

Path2D 객체를 이용해서 변수를 이용한 도형 그리는 방법

```jsx
// 기본 방식 
var shape2 = new Path2D();
shape2.arc(160,60,50,0,Math.PI*2);
ctx.stroke(shape2);

// 직접 매개변수로 도형의 속성값을 정의할 수 있다.
var shape3 = new Path2D("M10 10 h 110 v 100 h -110 z") 
ctx.stroke(shape3)
```

# 이벤트 리스너

- 발생하는 사건에 적용하는 방법
- x,y의 기준점을 잘 잡아야 한다.

# 원 이동하기

## 클릭한 위치에 원이 이동하는 방법

```jsx
canvas.onclick = function (e) {
    ctx.clearRect(0,0,canvas.width,canvas.height); // 0부터 초기화
    drawCircle(e);
};

var drawCircle = function(e){
    var ball = new Path2D();
    ball.arc(e.x, e.y, 30, 0, Math.PI * 2, true);
    ctx.fill(ball);
}
```

## 새로고침해도 마지막 그린 원이 저장되는 방법

### 1. 브라우저 간에 공유되는 저장소(localStorage) 이용하기

- `localStorage`란?
    - document의 Storage 객체에 접근할 수 있다. 저장한 데이터는 브라우저 세션간에 공유된다.
    - localStorage의 데이터는 페이지 프로토콜 별로 구분하는데, http, https는 서로 다른 localStorage에 저장된다.
    - 키와 값은 **항상** 각 문자에 2바이트를 할당하는 UTF-16 `[DOMString](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/String)`의 형태로 저장

```jsx
canvas.onclick = function (e) {
    clearCanvas(); // 클리어
    drawCircle(e.x,e.y); // 클릭원

    localStorage.setItem('x',e.x);
    localStorage.setItem('y',e.y);
};

var clearCanvas = function(){
    ctx.clearRect(0,0,canvas.width,canvas.height);
}

var drawCircle = function(x,y){
    var ball = new Path2D();
    ball.arc(x, y, 30, 0, Math.PI * 2, true);
    ctx.fill(ball);
}

var x = localStorage.getItem('x') || 100; // localStorage의 key값 "x"의 value가 없으면 100 입력.
var y = localStorage.getItem('y') || 100; // localStorage의 key값 "y"의 value가 없으면 100 입력.

drawCircle(x,y);
```

## * Reference
- https://developer.mozilla.org/en-US/docs/Web/API/Path2D/Path2D
- https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D
- https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent
- https://developer.mozilla.org/ko/docs/Web/API/Window/localStorage