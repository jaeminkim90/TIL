> ### 📖 ✏️ 
> **이 글은 스프링을 이용해 애노테이션으로 검증 로직을 구현할 수 있는 `Bean Validation` 기능을 사용하는 방법을 학습하고, 정리한 포스팅이다.**

---

# 스프링이 지원하는 Bean Validator 기능 사용 방법 

아래 코드는 **Bean Validator**이 적용된 예제이다. 		`/validation/v3/items/add` URL POST 요청을 맵핑하는 `addItem()`를 보면 `@Validated`가 적용되어 있다.


---


## 1. Bean Validator 라이브러리 적용 

**Bean Validator**를 사용하려면 `build.gradle`에 라이브러리를 추가해야 한다. 스프링 부트는 `implementation 'org.springframework.boot:spring-boot-starter-validation'` 라이브러리가 적용되어 있을 때, 자동으로 **Bean Validator**를 인식하고 스프링에 통합한다.


**ValidationItemControllerV3** 

```java
package hello.itemservice.web.validation;

import hello.itemservice.domain.item.Item;
import hello.itemservice.domain.item.ItemRepository;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.util.StringUtils;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.validation.ObjectError;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.WebDataBinder;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

import java.util.List;

@Slf4j
@Controller
@RequestMapping("/validation/v3/items")
@RequiredArgsConstructor
public class ValidationItemControllerV3 {

    private final ItemRepository itemRepository;

    @GetMapping
    public String items(Model model) {
        List<Item> items = itemRepository.findAll();
        model.addAttribute("items", items);
        return "validation/v3/items";
    }

    @GetMapping("/{itemId}")
    public String item(@PathVariable long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "validation/v3/item";
    }

    @GetMapping("/add")
    public String addForm(Model model) {
        model.addAttribute("item", new Item());
        return "validation/v3/addForm";
    }

    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute Item item, BindingResult bindingResult, RedirectAttributes redirectAttributes, Model model) {

        // 검증에 실패하면 다시 입력 폼으로 이동
        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v3/addForm";
        }

        // 성공 로직
        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v3/items/{itemId}";
    }

    @GetMapping("/{itemId}/edit")
    public String editForm(@PathVariable Long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "validation/v3/editForm";
    }

    @PostMapping("/{itemId}/edit")
    public String edit(@PathVariable Long itemId, @ModelAttribute Item item) {
        itemRepository.update(itemId, item);
        return "redirect:/validation/v3/items/{itemId}";
    }
}

```

**Bean Validator 애노테이션이 적용된 Item 클래스**
```java
package hello.itemservice.domain.item;

import lombok.Data;
import org.hibernate.validator.constraints.Range;

import javax.validation.constraints.Max;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
public class Item {

    private Long id;

    @NotBlank(message = "공백 불가")
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(9999)
    private Integer quantity;

    public Item() {
    }

    public Item(String itemName, Integer price, Integer quantity) {
        this.itemName = itemName;
        this.price = price;
        this.quantity = quantity;
    }
}
```


실행해보면 애노테이션 기반의 **Bean Validator**가 정상적으로 동작하는 것을 확인할 수 있다. 

스프링 부트는 `LocalValidatorFactoryBean`을 글로벌 Validator로 등록한다. 이 Validator가 `@NotNull` 같은 애노테이션을 보고 검증을 수행한다.

글로벌 Validator가 적용되어 있기 때문에 컨트롤러에는 `@Valid` 또는 `@Validated`만 적용하면 된다. 검증 오류가 발생하면 `FieldError`, `ObjectError`를 생성해서 `BindingResult`에 담는다. 


> **참고**
> 아래와 같이 글로벌 Validator를 직접 등록하면 스프링 부트는 `Bean Validator`를 글로벌 Validator로 등록할 수 없다. 따라서 애노테이션 기반의 **Bean Validator**가 동작하지 않는다. 

**글로벌 Validator를 직접 등록하는 방식**
```java
@SpringBootApplication
public class ItemServiceApplication implements WebMvcConfigurer {

    // 글로벌 검증기 추가 
    @Override public Validator getValidator() { 
    return new ItemValidator();
    }
    // ... 
} 
```

검증 기능은 `@Validated` 또는 `@Valid`를 모두 사용할 수 있다. `@Validated`는 스프링 전용 검증 애노테이션이고, `@Valid`는 자바 표준 검증 애노테이션이다. 검증 기능은 동일하지만 `@Validated`는 내부에 `groups`라는 기능을 포함한다.


## 2. Bean Validator의 동작 원리

검증 순서를 살펴보면, 먼저 `@ModelAttribute`가 각각의 필드에 타입 변환을 시도한다. 타입 변환에 성공하면 `Validator`를 적용하고, 실패하면 `typeMismatch` 로 `FieldError`를 추가한다.
   
중요한 점은 바인딩에 성공한 필드만 **Bean Validation**를 적용한다는 것이다. **Bean Validator**는 바인딩에 실패한 필드를 적용하지 않는다. 잘 생각해버면 타입 변환에 성공해서 바인딩에 성공한 필드만이 **Bean Validation**을 적용하는 의미가 있다. 값이 존재하지 않으면 검증의 대상도 부재하기 때문이다.


> 예) 
> - **`BeanValidation` 성공 케이스**
> `itemName`에 문자 `"A"` 입력  -> 타입 변환 성공 -> `itemName`필드에 `BeanValidation` 적용
> - **`BeanValidation` 실패 케이스**
> `price` 에 문자 `"A"` 입력 -> `"A"`를 숫자 타입 변환 시도 실패 -> `typeMismatch FieldError` 추가 -> `price` 필드 `BeanValidation` 미적용 