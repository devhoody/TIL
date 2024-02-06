- [SpringMVC - Validation](#springmvc---validation)
  - [검증 처리 원리](#검증-처리-원리)
  - [실습](#실습)
    - [`errors?`](#errors)
  - [`BindingResult`](#bindingresult)
    - [`#fields`](#fields)


# SpringMVC - Validation

## 검증 처리 원리

- 등록처리를 위한  Post시 입력 폼을 비우거나 적합하지 않은 경우 검증이 실패된다.
- 해당 **검증이 실패된 내용을 Model에 담아 view에 전달**하고, view를 통해 클라이언트에게 실패 내용을 출력한다.

## 실습

<aside>
💡 요구사항

- 상품 이름 필수
- 가격 : 1,000~ 1,000,000 사이 허용
- 수량 : 1 ~ 9,999 사이 허용
- 총액 : 10,000원 이상이어야 한다.
</aside>

**Controller내 post 메서드**

```java
@PostMapping("/add")
public String addItem(@ModelAttribute Item item, RedirectAttributes redirectAttributes, Model model) {
		//error담을 객체 생성
    Map<String, String> errors = new HashMap<>(); 

    //이름 검증
    if (!StringUtils.hasText(item.getItemName())) {
        errors.put("itemName", "상품 이름은 필수입니다.");
    }
    //가격 검증
    if (item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000) {
        errors.put("price", "가격은 1,000 ~ 1,000,000 까지 허용합니다.");
    }
    //수량 검증
    if (item.getQuantity() == null || item.getQuantity() > 9999) {
        errors.put("quantity", "수량은 최대 9,999 까지 허용합니다.");
    }

    // 가격 * 수량 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            errors.put("globalError", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 : " + resultPrice);
        }
    }

    // 검증 실패시 되돌리기
    if (!errors.isEmpty()) {
        log.info("errors = {}", errors);
        model.addAttribute("errors", errors);
        return "/validation/v1/addForm";
    }
```

- 검증 실패시 **에러를 담을 객체(errors) 생성**
- `StringUtils.hasText()`를 이용하여 **String이 null값인 경우의 조건 작성**
- 검증 실패시 입력페이지로 되돌리기 위해 **입력 폼 return**

**addForm.html**

```html
<div class="py-5 text-center">
    <h2 th:text="#{page.addItem}">상품 등록</h2>
</div>

<form action="item.html" th:action th:object="${item}" method="post">
    **<div th:if="${errors?.containsKey('globalError')}">
        <p class="field-error" th:text="${errors['globalError']}">글로벌 오류발생</p>
    </div>**

    <div>
        <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
        <input type="text" id="itemName" th:field="*{itemName}"
               **th:class="${errors?.containsKey('itemName')} ? 'form-control field-error' : 'form-control'"**
               class="form-control" placeholder="이름을 입력하세요">
        **<div class="field-error" th:if="${errors?.containsKey('itemName')}" th:text="${errors['itemName']}">
            상품명오류
        </div>**
    </div>
    <div>
        <label for="price" th:text="#{label.item.price}">가격</label>
        <input type="text" id="price" th:field="*{price}"
               **th:class="${errors?.containsKey('price')} ? 'form-control field-error' : 'form-control'"**
               class="form-control" placeholder="가격을 입력하세요">
        **<div class="field-error" th:if="${errors?.containsKey('price')}" th:text="${errors['price']}">
            가격오류
        </div>**
    </div>
    <div>
        <label for="quantity" th:text="#{label.item.quantity}">수량</label>
        <input type="text" id="quantity" th:field="*{quantity}"
               **th:class="${errors?.containsKey('quantity')} ? 'form-control field-error' : 'form-control'"**
               class="form-control" placeholder="수량을 입력하세요">
        **<div class="field-error" th:if="${errors?.containsKey('quantity')}" th:text="${errors['quantity']}">
            수량오류
        </div>**
    </div>
```

- 타임리프의 기능 th:if, th:class, th:text를 이용하여 html form을 작성한다.
    - **th:if="`${errors?.containsKey('globalError')}`"**
    - `th:class` : **특정 조건에 따라 지정된 class를 입력**한다.

### `errors?`

- 에러를 담은 객체(`errors`)가 **null일 경우에 `nullPointerException`을 발생시키는 대신, null을 반환**하는 문법
- 상품 등록 폼을 들어갈 때, error가 없으므로 errors는 null을 가진다. errors?를 이용하여 **nullPointerException 발생을 막는다.**

보완해야할 것

- Integer 타입에 문자열 입력시 400에러 발생
- 뷰 템플릿에 반복되는 부분이 많다.
- 고객이 Integer에 문자열을 입력했을 때, 입력한 값도 유지해두어야한다.
- 결국 고객이 입력한 값을 따로 관리할 필요가 있다.

## `BindingResult`

- 위에서 보완해야할 점들을 해결하기 위해 **error를 담는 객체인 BindingResult를 이용**한다.
- `bindingResult의 위치` :  **반드시 `@ModelAttributes` 객체 다음에 와야한다.**
- **bindingResult는 자동으로 model 객체에 담겨 view로 전달된다.**

**controller내 post 메서드**

```java
@PostMapping("/add")
public String addItem(**@ModelAttribute Item item, BindingResult bindingResult**, RedirectAttributes redirectAttributes, Model model) {

    //이름 검증
    if(!StringUtils.hasText(item.getItemName())){
        **bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니다."));**
    }
    //가격 검증
    if(item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000){
        **bindingResult.addError(new FieldError("item", "price", "가격은 1,000 ~ 1,000,000 까지 허용합니다."));**
    }

    //수량 검증
    if(item.getQuantity() == null || item.getQuantity() >9999){
        **bindingResult.addError(new FieldError("item", "quantity", "수량은 최대 9,999 까지 허용합니다."));**
    }
    // 가격 * 수량 검증
    if(item.getPrice() != null && item.getQuantity() != null){
        int resultPrice = item.getPrice() * item.getQuantity();
        if(resultPrice < 10000){
            **bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야 합니다. 현재 : " + resultPrice ));**
        }
    }

    // 검증 실패시 되돌리기
    if(**bindingResult.hasErrors()**){
        log.info("errors = {}", bindingResult);
        return "/validation/v2/addForm";
    }

		...
}
```

- bindingResult의 위치는 항상 **@ModelAttribute의 뒤에 위치시킨다.**
    - 그래야 bindingResult가 인식할 Object를 찾을 수 있다.
- `FieldError`, `ObjectError`를 이용하여 각각 필드에러, 글로벌 에러를 담는다.
    - `FieldError` : **인식한 객체의 속성의 오류** 발생시 담는 객체
    - `ObjectError` : **FieldError를 제외한 오류**를 담는 객체 ex)글로벌 오류
- `bindingResult.hasErrors()`를 이용하여 에러가 있는 경우의 조건을 작성한다.
    - 참고로, 전의 코드인 `!errors.isEmpty()`의 경우, **`이중 부정`(에러가 비었다의 반대 = 에러가 있다.)**이 되어 결국은 “**에러가 존재할 경우”** 라는 의미를 나타낸다. 이처럼 이중 부정을 사용하면, **코드를 해석하는데 불편하다는 단점이 존재한다.** 따라서 클린 코드를 지향하는 개발자에게는 **bindingResult의 메소드를 사용하는 것을 추천**한다.

**addForm.html**

```html
<form action="item.html" th:action th:object="${item}" method="post">
    <div **th:if="${#fields.hasGlobalErrors()}"**>
        **<p class="field-error" th:each="err : ${#fields.globalErrors()}" th:text="${err}">글로벌 오류발생</p>**
    </div>

    <div>
        <label for="itemName" th:text="#{label.item.itemName}">상품명</label>
        <input type="text" id="itemName" th:field="*{itemName}"
               **th:errorclass="field-error"**
               class="form-control" placeholder="이름을 입력하세요">
        <div class="field-error" **th:errors="*{itemName}"**>
            상품명오류
        </div>
    </div>
    <div>
        <label for="price" th:text="#{label.item.price}">가격</label>
        <input type="text" id="price" th:field="*{price}"
               **th:errorclass="field-error"**
               class="form-control" placeholder="가격을 입력하세요">
        <div class="field-error" **th:errors="*{price}"**>
            가격오류
        </div>
    </div>
    <div>
        <label for="quantity" th:text="#{label.item.quantity}">수량</label>
        <input type="text" id="quantity" th:field="*{quantity}"
               **th:errorclass="field-error"**
               class="form-control" placeholder="수량을 입력하세요">
        <div class="field-error" **th:errors="*{quantity}"**>
            수량오류
        </div>
    </div>
```

- `#fields.hasGlobalErrors()`를 이용하여 글로벌 에러의 존재 조건을 작성한다.
    - `th:each`를 이용하여 존재하는 글로벌 에러들을 가져온다.
- `th:errorclass=”field-error”`
    - `th:errorclass` : `th:field`에 해당하는 필드명에 에러가 발생할 경우 입력한 클래스를 추가한다.
    - 기존의 th:if, th:class를 입력한 것들과 비교했을때, 훨씬 간결해진 것을 알 수 있다.
- `th:errors=”*{price}”`
    - `th:errors` : th:object로 지정된 객체의 특정 필드에 오류가 발생할 경우 bindingResult 내의 같은 필드명 오류를 반환한다.
    - *th:object로 item을 지정, th:errors가 price일 경우, price 관련 오류 발생시 bindingResult 내의 ‘price’ 필드명에 해당하는 오류 반환*

### `#fields`

- **`BindingResult`가 제공하는 검증 오류에 접근**을 해주는 타임리프 제공 기능