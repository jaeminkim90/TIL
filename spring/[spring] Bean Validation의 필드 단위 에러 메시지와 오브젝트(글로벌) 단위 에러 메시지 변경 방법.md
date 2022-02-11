> ### 📖 ✏️ 
> **이 글은 `Bean Validation`을 이용한 검증 시, 필드 단위 의 에러 메시지와 오브젝트(글로벌) 단위의 에러 메시지를 커스텀 하여 사용하는 방법을 학습하고 정리한 포스팅이다.**


---

# Bean Validation의 에러 메시지 변경 방법

**Bean Validation** 기본으로 제공하는 오류 메시지는 커스텀하여 사용할 수 있다. 필드 단위의 에러메시지와 오브젝트 단위의 에러메시지로 나눠서 살펴보자.

---

## 1. 필드 단위의 에러메시지 변경 방법

**Bean Validation**을 적용하고 **bindingResult**에 등록된 검증 오류 코드를 확인해보자. 오류 코드가 애노테이션 이름으로 등록된다. 마치 `typeMismatch`와 유사하다. `NotBlank`라는 오류 코드를 기반으로 `MessageCodesResolver`를 통해 다양한 메시지 코드가 순서대로 생성된다. 

> **@NotBlank** 
- `NotBlank.item.itemName` 
- `NotBlank.itemName` 
- `NotBlank.java.lang.String` 
- `NotBlank` 
  
> **@Range** 
- `Range.item.price` 
- `Range.price` 
- `Range.java.lang.Integer` 
- `Range` 

오류 메시지의 변경은 해당 메시지 코드를 `errors.properties`에 수정 등록하면 된다. 직접 메시지 내용을 수정해보자.

> **errors.properties**  
> ```java
#Bean Validation 추가
NotBlank={0} 공백 X
Range={0}, {2} ~ {1} 허용 
Max={0}, 최대 {1}
```

`{0}`은 필드명이고, `{1}`과 `{2}`는 각 애노테이션 마다 다르게 사용된다. `Range`의 index 순서가 {2} ~ {1} 인 이유는 Annotation의 속성 이름이 알파벳 순서로 처리되기 때문이다. `Range`에는 `Min`과 `Max` 두가지 속성이 있다. `Max`가 `Min`보다 알파벳 기준으로 우선순위가 높다.

오류 메시지 수정후 실행을 해보면 방금 등록한 메시지가 정상 작동되는 것을 확인 할 수 있다.

### 1-1. 필드 단위 BeanValidation 메시지가 결정되는 순서

1. 생성된 메시지 코드 순서대로 `messageSource`에서 메시지를 찾는다.
2. 애노테이션의 `message` 속성을 사용한다.
ex) `@NotBlank(message = "공백! {0}")`  
3. 라이브러리가 제공하는 기본 메시지 내용을 사용한다.
ex) 공백일 수 없습니다. 


**애노테이션에 message 속성 사용 예시**
```java
@NotBlank(message = "공백은 입력할 수 없습니다.") 
private String itemName; 
```


---

## 2. 오브젝트 단위의 에러메시지 변경 방법

**Bean Validation**에서 특정 필드(`FieldError`)가 아닌 해당 오브젝트 관련 오류(`ObjectError`)도 메시지 커스텀이 가능하다. 두 가지 방법이 있다. 

### 2-1. @ScriptAssert()를 사용하는 방법

```java
@Data
@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000")
    public class Item {
    //... 
} 
```

실행해보면 정상 수행되는 것을 확인할 수 있다. 메시지 코드도 아래와 같이 생성된다. 

**ScriptAssert 메시지 코드** 
```java
ScriptAssert.item
ScriptAssert
```

필드 오류 메시지처럼 `@ScriptAssert()` 애노테이션에 직접 메시지를 추가하는 것도 가능하다.

```java
@ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000 ", message = "총합이 10,000을 넘어야 합니다.")
```

### 2-2. 자바 코드로 직접 구현

`@ScriptAssert()`을 이용하는 방식은 기능 대비 제약이 많고 사용성이 떨어진다. 검증 범위가 객체를 넘어서는 경우 대응이 어렵다는 점도 문제가 된다. 따라서 오브젝트 오류(글로벌 오류)의 경우 `@ScriptAssert`을 억지로 사용하는 것 보다, 오브젝트 오류 관련 부분만 직접 자바 코드로 작성하는 것이 여러 면에서 낫다.


**ValidationItemControllerV3 - 글로벌 오류 직접 추가**
```java
@PostMapping("/add")
public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

    // 복합 조건(가격*수량 10,000원 이상) 검증
    if (item.getPrice() != null && item.getQuantity() != null) {
        int resultPrice = item.getPrice() * item.getQuantity();
        if (resultPrice < 10000) {
            bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
        }
    }
```