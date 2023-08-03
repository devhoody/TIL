- [이미지 만들기](#이미지-만들기)
    - [drawImage()](#drawimage)
  - [중심좌표가 캐릭터의 가운데가 되도록 그리기](#중심좌표가-캐릭터의-가운데가-되도록-그리기)
  - [캐릭터 모션 속도 조절](#캐릭터-모션-속도-조절)
  - [키보드 입력받기](#키보드-입력받기)
  - [캔버스를 바로 focus시키기](#캔버스를-바로-focus시키기)
    - [focus시 캔버스 아웃라인 없애기](#focus시-캔버스-아웃라인-없애기)
  - [캐릭터의 걷기 모션 on/ off](#캐릭터의-걷기-모션-on-off)


# 이미지 만들기

- html에 있는 이미지를 js파일로 가져온다.
    - draw가 먼저 호출된다.
        - 파일이 로드되기전에 draw가 먼저 실행되기 때문에 그려지지않는다.

  - 코드
  
  ```jsx
  constructor(x = 0, y = 0) {
      this.#x = x
      this.#y = y
      // this.#img = document.createElement("img")
      // this.#img.src = "image/boy.png"
      this.#img = document.querySelector("img")
      // this.#img.src = "./image/boy.png"
  }
  
  /** @param {CanvasRenderingContext2D} ctx */
  draw(ctx) {
      ctx.drawImage(this.#img, this.#x, this.#y)
  }
  ```
    

### drawImage()
- image를 가져와 그리기 위해서 `ctx 객체의 drawImage() 메소드`를 이용한다.
- 
```jsx
ctx.drawImage(this.#img, w*2, 0, w, h, this.#x-w/2,this.#y-h/2, w, h)
```

## 중심좌표가 캐릭터의 가운데가 되도록 그리기

- 캐릭터가 그려지는 좌표가 캐릭터의 `좌측 상단`이다. 따라서 중심좌표에 해당하는 점(arc)을 그려 해당 중심축으로 캐릭터를 이동시킨다.
- boy.js
    
    ```jsx
    let w = 48;
    let h = 48;
    
    // w/2, h/2 : 반지름만큼 이동하면 되므로 지름인 48의 절반인 24이다.
    
    ctx.drawImage(this.#img, w*2, 0, w, h, this.#x-w/2,this.#y-h/2, w, h)
    ctx.arc(this.#x, this.#y, 5, 0, Math.PI * 2, true);
    ctx.fillStyle = "red"
    ctx.fill()
    ```
    

## 캐릭터 모션 속도 조절

- `Index 값을 이용`하여 캐릭터의 모션 속도를 조절할 수 있다.

```jsx
// 방법 1
if (this.#speed++ % 4 == 0) {
    this.#imgIndexH++;
    this.#imgIndexH %= 4
}

// 방법 2
update() {
    if (this.#walkingDelay == 0) {
        this.#imgWalkingIndex++;
        this.#walkingDelay = 15;
    }
    this.#imgWalkingIndex %= 4;
    this.#walkingDelay--;
}

// drawImage 메소드에 가져올 이미지를 Index를 이용해 가져온다.
// 매개변수가 많아지면 줄바꿈을 이용하여 가독성을 높여준다.
draw(ctx) {
    let w = 48;
    let h = 48;

    ctx.drawImage(
        this.#img,

        w * this.#imgDirectionIndex, 
        h * this.#imgWalkingIndex,
        w,
        h,

        this.#x - w / 2,
        this.#y - h / 2,
        w,
        h
    )
    // ctx.arc(this.#x, this.#y, 5, 0, Math.PI * 2, true);
    // ctx.fillStyle = "red"
    // ctx.fill()
}
```

## 키보드 입력받기

- `window.onload`
    - 문서가 로드되면 실행되는 것.
    - html문서의 defer 대용!
    
    ```jsx
    <script type="module" src="app.js" defer></script>
    ```
    
- `생성자 함수에 onkeydown`을 이용하여 키보드 아무키나 입력받기

```jsx
constructor() {

    this.#obj.onkeydown = function () {
        console.log("keydown");
    }.bind(this)
}
```

## 캔버스를 바로 focus시키기

- 캔버스의 생성자 함수에 focus 메소드를 사용하면 해당 캔버스가 켜지자마자 자동으로 포커스가 맞춰진다.
- focus를 쓰기 위해서는 html 내 canvas 태그에 속성값 `tabindex`를 추가한다.
    
    ```jsx
    <canvas  id="canvas" tabindex="5" width="900" height="700"></canvas>
    ```
    

### focus시 캔버스 아웃라인 없애기

- html의 스크립트 태그 안에서 canvas의 `outline 속성을 none`으로 만들면 focus시 아웃라인이 사라진다.

```jsx
#canvas{
    border : 10px dotted blue;
    outline : none;
}
```

## 캐릭터의 걷기 모션 on/ off

- 키보드 아무키나 입력하였을때 걷기 모션을 실행한다.
- isWalking 변수를 이용하여 false 일때, 걷기 모션을 안하고, true일 때,걷기 모션을 진행한다.
- boy.js
    
    ```jsx
    
    #isWalking
    
    constructor(x,y) {
        this.#x = x
        this.#y = y
        this.#img = document.querySelector("img")
        this.#imgWalkingIndex = 0;
        this.#imgDirectionIndex = 0;
        this.#walkingDelay = 15;
        this.#isWalking = false; // 걷기모션 x
    }
    
    //
    
    moveTo(x,y) {
        this.#isWalking = true
    }
    
    update() {
    		if (this.#isWalking && this.#walkingDelay == 0) {
    			this.#imgWalkingIndex++;
    			this.#walkingDelay = 15;
    }
    ```
    
    - **intro-canvas.js**
        - 생성자 함수 안에 `onkeydown 메소드`를 이용하여 키보드의 입력을 받을 때 실행하는 구문을 만든다.
    
    ```jsx
    constructor() {
    		this.#obj.onkeydown = function () {
    		    console.log("keydown");
    		    this.#boy.moveTo(100, 100);
    		}.bind(this)
    }
    ```