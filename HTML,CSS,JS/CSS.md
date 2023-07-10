# CSS

- HTML문서의 스타일, 레이아웃, 디자인을 정의하는 스타일 시트 언어

# Selector

- 스타일을 적용할 HTML 요소를 지정하는 역할

```css
selector {
  property: value;
}
```

- selector : HTML 적용 요소
- property : 스타일 속성
- value : property 속성 값

## Selector 종류

- Elemant (Tag명)
- class (.class명)
- id (#id명)
- `*` (univercial selector)

### class와 id 차이

- class : CSS 작성자가 명명, id : HTML 작성자가 명명

## 속성연산자

- **h1[class]** : h1태그 중, `class라는 속성`이 있는 것
  - h1[class = “h1”] : h1태그 중, `class 명이 h1`인 것
- **span[lang `~=` “en-kr”]** : span태그 중, lang의 속성에서 `“en-kr”을 포함`하고 있는 것
- **span[lang `|=` “zh”]** : span 태그 중, lang의 속성에서 `“zh”나 “zh-”까지 포함`하는 것
- **a[href `^=` #]** : a 태그 중, href 속성이 `#으로 시작`하는 것
- **a[href `$=` “.com”]** : a 태그 중, href 속성의 `끝이 .com`으로 끝나는 것
- **a[href `*=` “example”]** : a 태그 중, href 속성 중 `문자열과 상관없이 example을 포함`하는 것
- **input[type= “email” `i`]** : input 태그 중, type 속성 중 email을 `대소문자 상관없이 포함`하는 것 (`대소문자 상관 있으면 i 제거`)
- `.class1.class2` : class1과 class2를 모두 갖는 태그

```html
<div class="class1">
  <p class="class2">텍스트</p>
</div>
<!-- 위의 경우 class1과 class2에 모두 해당하는 <p>태그가 적용된다. -->
```

## 콤비네이션 선택자

- `A B`
  - 특정 요소의 모든 하위 요소 중에서 특정 요소 찾기.
  - _A의 자손 중에서 B를 찾으세요._
- `A > B`
  - _A의 직계 자식 중에서 B를 찾아라._
  ```css
  section > .h1 {color : Red}
  /* section 태그에서 클래스가 h1인 것을 찾아라.
  ```
- `A + B`
  - Adjacent sibling selector
  - _바로 이웃하는 동생, 바로 옆 동생!_
  ```css
  section + .B {color : Red}
  /* section 태그에서 클래스가 B인 것의 바로 옆 요소를 지정
  ```
- `A ~ B`
  - _동생들 중에서 찾음._
  ```css
  section ~ .B {color : Red}
  /*section 태그에서 클래스가 B인 것의 동생들을 지정
  ```

## Selector 우선순위

- CSS 규칙의 우선순위를 나타내고, 스타일이 적용되는 순서를 말한다.
- `범위가 좁을 수록` 우선순위 높다.
  - `tag < .class(class) < #id(id)`
- `복합연산자`가 기본 연산자보다 우선순위 높다.
- `동급이면 작성순서`에 따라 적용 (아래로 갈수록 덮어쓰는거)
- 예시
  ```html
  <!DOCTYPE html>
  <html>
    <head>
      <style>
        h1 {
          color: blue;
        }
        #id {
          color: red;
        }
        .class {
          color: green;
        }
      </style>
    </head>
    <body>
      <h1 id="id" class="class">제목1</h1>
    </body>
  </html>
  ```
- 우선순위 : #id > .class > h1 이므로 h1태그의 속성값인 제목은 <style> 태그의 #id의 속성값인 빨간색으로 나타난다.

# CSS 구조화

- `Inline style`
  - inline태그인 <style> 이용하는 방법
  ```html
  <p style="color : red"></p>
  ```
- `Internal stylesheet`
  - html파일 내에 <head>내 <style> 이용하는 방법
  - 현재 페이지만을 위한 스타일
  ```html
  <head>
    <style>
      p {
        color: orange;
      }
    </style>
  </head>
  ```
- `External stylesheet`
  - 별도의 파일(.css)을 이용하는 방법
  - 비슷한 스타일의 CSS을 이용한다면 중복을 제거하기 용이함.
  ```css
  p {
    color: orange;
  }
  ```
  - 실습
    ```html
    <link href="css/style.css" type="text/css" rel="stylesheet" />
    ```
- 우선순위 : `Inline > Internal > External`

## External stylesheet를 위한 두가지 방법

### A방법 (html파일에 동시에 넣기)

- style.css와 index.css 동시에 호출

```html
<link href="css/style.css" type="text/css" rel="stylesheet" />
<link href="css/index.css" type="text/css" rel="stylesheet" />
```

### B방법 (CSS파일에 참조하여 넣기)

- index.css ⇒ style.css 호출

```css
@import url(style.css);
```

- 참조하는 내용이 두개 정도이면 B방법을 하는 것이 좋다.

## 스타일 적용 순서

- _레이아웃 블록 스타일 - > 콘텐츠 블록 스타일 → 텍스트 스타일 → 콘텐츠 레이아웃_

## top 섹션에 이름짓기

- 초보자인 경우, 클래스마다 하나씩 확인후 다음 클래스를 지정할 필요가 있다.

```css
(1).header => (2) <header class ="header"></header> (3) .header{채우기}
```
