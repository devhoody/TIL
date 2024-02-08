- [SpringMVC - Validation](#springmvc---validation)
  - [검증 처리 원리](#검증-처리-원리)
  - [실습](#실습)
    - [`errors?`](#errors)
  - [`BindingResult`](#bindingresult)
    - [`#fields`](#fields)
  - [BindingResult2](#bindingresult2)
    - [BindingResult에 검증 오류 적용 3가지](#bindingresult에-검증-오류-적용-3가지)
    - [BindingResult와 Errors](#bindingresult와-errors)
  - [FieldError, ObjectError](#fielderror-objecterror)
    - [스프링 바인딩 오류시 처리](#스프링-바인딩-오류시-처리)
  - [오류 코드와 메시지 처리](#오류-코드와-메시지-처리)
  - [오류 코드와 메시지 처리2](#오류-코드와-메시지-처리2)
    - [rejectValue(), reject()](#rejectvalue-reject)
  - [오류 코드와 메시지 처리3](#오류-코드와-메시지-처리3)
  - [MessageCodesResolver](#messagecodesresolver)
    - [DefaultMessageCodesResolver 메시지 생성방법](#defaultmessagecodesresolver-메시지-생성방법)
    - [동작방식](#동작방식)
  - [오류코드와 메시지 처리5](#오류코드와-메시지-처리5)
    - [errors.properties 작성 요령](#errorsproperties-작성-요령)
  - [스프링이 직접 만든 오류 메시지 처리(오류코드와 메시지 처리6)](#스프링이-직접-만든-오류-메시지-처리오류코드와-메시지-처리6)
    - [오류 메시지 수정 방법](#오류-메시지-수정-방법)
- [Validator 분리](#validator-분리)
- [Validator 분리2](#validator-분리2)
  - [`@Validated`](#validated)
    - [@Validated, @Valid](#validated-valid)


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

## BindingResult2

- `BindingResult 유무`에 따른 타입 오류페이지 전환 차이
    - **있을 때** : `오류 페이지 이동 x`
    - **없을 때** : `400 오류 페이지 이동`

### BindingResult에 검증 오류 적용 3가지

1. `@ModelAttribute`의 객체에 타입 오류 같은 바인딩 오류시 **스프링이 FieldError를 직접 생성**하여 `BindingResult`에 넣어준다.
2. **개발자가 직접 넣는다.**
3. **Validator**를 사용한다.

### BindingResult와 Errors

- BindingResult는 Errors를 상속 받는 인터페이스
    - Errors는 단순한 오류 저장 기능만 제공, BindingResult가 더 많은 기능을 제공한다.
    - BindingResult를 더 많이 사용한다.

## FieldError, ObjectError

- 오류시 사용자 입력 결과를 FieldError와 ObjectError가 갖고 있는다.
- rejectedValue : 오류 발생시 사용자 입력 값을 저장하는 필드
- `th:Field` : 정상 출력인 겨우 model에 있는 값을 사용하지만, 오류가 발생하면 FieldError에 담기 ㄴ값을 사용한다.

### 스프링 바인딩 오류시 처리

- 타입 오류같은 바인딩 오류시 스프링은 FieldError를 생성한다. 생성한 FieldError를 BindingResult에 담고나서 컨트롤러를 호출한다.

## 오류 코드와 메시지 처리

- 오류 메시지를 한곳에서 일괄적으로 관리하면 좋다.
    
    `bindingResult.addError(new FieldError("item", "itemName", item.getItemName(),false, **null, null, "상품 이름은 필수입니다."**));`
    
- **`errors.properties`,`codes`, `arguments` 이용**

## 오류 코드와 메시지 처리2

- ‘FieldError’, ‘ObjectError’ 다루기 너무 번거롭다.
- 오류 코드도 좀더 자동화 하기

### rejectValue(), reject()

```java
//rejectValue()
bindingResult.rejectValue("price", "range", new Object[]{1000, 1000000}, null);

//reject()
bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
```

- `rejectValue()`
    - `field` : field명
    - `errorCode` : 오류코드
    - `errorArgs` : 오류 메시지에서 필요한 치환 값, {0},{1} 등
    - `defaultMessage` : 기본 메시지,
- `reject()`
    - `errorCode`, `errorArgs`, `defaultMessage`
- FieldError를 직접 넣어줄때는 오류 코드는 ‘range.item.price’를 모두 작성해주어야했다. rejectValue()를 이용하면 앞의 오류코드인 ‘range’만으로 메시지를 출력할 수 있다.

## 오류 코드와 메시지 처리3

> 디테일한 오류 메시지나 간단한 오류메시지로 단계별로 사용할 수 있다.
> 
- 범용적인 단순오류 메시지부터 범용적이진 않은 디테일한 오류 메시지 순서로 사용하는 것이 좋다.
    - ***범용적 → 디테일***
- ‘`required`’를 `errorcode`로 넣으면, 처음에는 **디테일한 오류 메시지를 찾고 다음으로 단순 메시지를 찾는다.**
- ***properties만으로도 모든 메시지를 관리할 수 있다.***

**errors.properties**

```java
#Level1
required.item.itemName=상품 이름은 필수입니다.
range.item.price=가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity=수량은 최대 {0} 까지 허용합니다.
totalPriceMin=가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}

#Level2
required = 필수 값 입니다.
range = 범위 오류입니다.
max = 최대값 오류입니다.
```

## MessageCodesResolver

> required만 넣엇는데 어떻게 상세메시지와 단순메시지 둘다 가능할까?
> 
- **MessageCodesResolver란?**
    - **검증 오류 코드로 메시지 코드를 작성**하는 인터페이스
    - `DefaultMessageCodesResolver` : 구현 객체

### DefaultMessageCodesResolver 메시지 생성방법

- 객체오류
    - 객체 오류의 경우 다음 순서로 2가지 생성
        - 1.: `code + "." + object name`
        - 2.: `code`
    - 코드
        
        ```java
        String[] messageCodes = codesResolver.resolveMessageCodes("required", "item");
        ```
        
        1. `required.item`
        2. `required`
- 필드오류
    - 필드 오류의 경우 다음 순서로 4가지 생성
        1. `: code + "." + object name + "." + field`
        2. `code + "." + field`
        3. `code + "." + field type`
        4. `code`
    - 코드
        
        ```java
        String[] messageCodes = codesResolver.resolveMessageCodes("requried", "item", "itemName", String.class);
        ```
        
        1. `required.item.itemName`
        2. `requried.itemName`
        3. `required.java.lang.String`
        4. `required`

### 동작방식

- `rejectValue()`, `reject()`는 내부에서 `MessageCodesResolver`를 사용한다.
- FieldError, ObjectError의 생성자를 보면, 오류 코드가 하나가 아니라 배열을 통해 여러 오류 코드가 들어간다.

## 오류코드와 메시지 처리5

- `MessageCodesResolver`는 **구체적인것 → 범용적인 순서**로 메시지 코드를 만든다.
    - `required.item.itemName` →… → `required`

### errors.properties 작성 요령

1. `ObjectError`와 `FieldError`를 구분하여 작성한다.
2. **level1(구체적)부터 범용적순서**로 단계별로 작성한다.

**errors.properties**

```xml
**# ObjectError

#level-1
totalPriceMin.item = 상품의 가격 * 수량의 합은 {0}원 이상이어야 합니다. 현재 값 = {1}
#level-2
totalPriceMin = 전체 가격은 {0}원 이상이어야 합니다. 현재 값 = {1}

# FieldError

# level-1
required.item.itemName = 상품 이름을 입력해주세요.
range.item.price = 가격은 {0} ~ {1} 까지 허용합니다.
max.item.quantity = 수량은 최대 {0} 까지 허용합니다. 
# level-2
# level-3
required.java.lang.String = 필수 문자입니다.
range.java.lang.String = {0} ~ {1} 까지의 문자를 입력해주세요.
range.java.lang.Integer = {0} ~ {1} 까지의 숫자를 입력해주세요.
max.java.lang.Integer = {0} 까지의 숫자를 허용합니다.
max.java.lang.String = {0} 까지의 문자를 허용합니다.

# level-4
required = 필수 값 입니다.**
```

## 스프링이 직접 만든 오류 메시지 처리(오류코드와 메시지 처리6)

- **검증 오류코드 종류 2가지**
    - **개발자가 직접 설정**한 오류 코드
    - **스프링이 직접 검증 오류에 추가**한 경우(타입 오류 등)
- 스프링이 직접 만든 오류의 경우, 오류 메시지는 **스프링이 만든 메시지**가 출력된다.
    
    <img width="547" alt="1" src="https://github.com/devhoody/TIL/assets/124743189/7e5a5a50-a419-4c24-9007-2898a8f87f33">

    

### 오류 메시지 수정 방법

- 타입 오류가 발생하면 다음과 같이 스프링이 `MessageCodesResolver`를 이용하여 다음과 같은 codeName이 출력되는 것을 알 수 있다.
    - `typeMismatch.item.price` `typeMismatch.price` `typeMismatch.java.lang.Integer` `typeMismatch`
        
        <img width="1244" alt="2" src="https://github.com/devhoody/TIL/assets/124743189/720348b8-55c8-4b1b-aa2f-d390c79e4bff">
        
    - 스프링은 타입 오류가 발생하면 `typeMismatch`라는 오류코드를 사용한다.

**error.properties**

```xml
typeMismatch.java.lang.Integer=숫자를 입력해주세요.
typeMismatch=타입 오류입니다.
```

- 실행결과
    
    <img width="547" alt="3" src="https://github.com/devhoody/TIL/assets/124743189/cc110bb0-7b01-4097-8157-9c9c7f657fe7">

    
    - 내가 지정한 오류 메시지가 출력되는 것을 확인할 수 있다.


# Validator 분리

> 복잡한 검증 로직을 별도의 클래스로 분리하자.
> 

**ItemValidator**

```java
@Component
public class ItemValidator implements Validator {
    @Override
    public boolean supports(Class<?> clazz) {
        return Item.class.isAssignableFrom(clazz);
    }

    @Override
    public void validate(Object target, Errors errors) {
        Item item = (Item)target;
        //이름 검증
        if(!StringUtils.hasText(item.getItemName())){
            errors.rejectValue("itemName", "required");
        }
        //가격 검증
        if(item.getPrice() == null || item.getPrice() < 1000 || item.getPrice() > 1000000){
            errors.rejectValue("price", "range", new Object[]{1000, 1000000}, null);
        }

        //수량 검증
        if(item.getQuantity() == null || item.getQuantity() >9999){
            errors.rejectValue("quantity", "max", new Object[]{9999}, null);
        }

        // 가격 * 수량 검증
        if(item.getPrice() != null && item.getQuantity() != null){
            int resultPrice = item.getPrice() * item.getQuantity();
            if(resultPrice < 10000){
                errors.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }
    }
}
```

- 스프링은 체계적인 검증을 위해 다음 인터페이스를 제공한다.
    
    ```java
    public interface Validator {
         boolean supports(Class<?> clazz);
         void validate(Object target, Errors errors);
    }
    ```
    
    - `supports()` : 해당 **검증기를 지원하는 여부 확인**
    - `validate(Object target, Errors errors)` : **검증 대상 객체**와 **bindingResult**

**ItemController - addFormV5**

```java
public class ValidationItemControllerV2 {

	**private final ItemValidator itemValidator;**

	@PostMapping("/add")
    public String addItemV5(@ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        **itemValidator.validate(item, bindingResult);**

        // 검증 실패시 되돌리기
        if(bindingResult.hasErrors()){
            log.info("errors = {}", bindingResult);
            return "/validation/v2/addForm";
        }

        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v2/items/{itemId}";
    }
}
```

- 기존에 있던 검증로직을 `ItemValidator`로 이동.
- `ItemValidator`를 Autowired하여 컨트롤러에 주입한다.

# Validator 분리2

- `WebDataBinder`를 이용하면 스프링의 추가적인 도움을 받을 수 있다.
    - `WebDataBinder` : 스프링의 파라미터 바인딩 역할과 검증 기능을 내부에 포함하고있다.

**ItemController - addFormV6**

```java
**@InitBinder
 public void init(WebDataBinder dataBinder) {
     log.info("init binder {}", dataBinder);
     dataBinder.addValidators(itemValidator);
 }**

@PostMapping("/add")
public String addItemV6(**@Validated** @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

    // 검증 실패시 되돌리기
    if(bindingResult.hasErrors()){
        log.info("errors = {}", bindingResult);
        return "/validation/v2/addForm";
    }

    Item savedItem = itemRepository.save(item);
    redirectAttributes.addAttribute("itemId", savedItem.getId());
    redirectAttributes.addAttribute("status", true);
    return "redirect:/validation/v2/items/{itemId}";
}
```

- 컨트롤러에 다음처럼 `WebDataBinder`에 우리가 만든 검증기를 추가하면 컨트롤러가 호출될 때마다 자동으로 검증을 한다.

## `@Validated`

- 검증기를 실행하라는 애노테이션
- `WebDataBinder`에 등록한 검증기를 실행한다.
- 여러 검증기를 등록할 경우는 어떻게 특정 검증기를 찾을까? → Validator의 `support()` 사용
- `support(Item.class)`
    - `ItemValidator`에 `support()`에 `Item.class`를 넣었기 때문에 **ItemValidator의 validate()가 호출된다.**

### @Validated, @Valid

- `@Validated` : **스프링** 전용 검증 애노테이션
- `@Valid` : **자바** 표준 검증 애노테이션