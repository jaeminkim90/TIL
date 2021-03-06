> ### π βοΈ 
> **μ΄ κΈμ μ€νλ§μ μ΄μ©ν΄ μ λΈνμ΄μμΌλ‘ κ²μ¦ λ‘μ§μ κ΅¬νν  μ μλ `Bean Validation` κΈ°λ₯μ μ¬μ©νλ λ°©λ²μ νμ΅νκ³ , μ λ¦¬ν ν¬μ€νμ΄λ€.**

---

# μ€νλ§μ΄ μ§μνλ Bean Validator κΈ°λ₯ μ¬μ© λ°©λ² 

μλ μ½λλ **Bean Validator**μ΄ μ μ©λ μμ μ΄λ€. 		`/validation/v3/items/add` URL POST μμ²­μ λ§΅ννλ `addItem()`λ₯Ό λ³΄λ©΄ `@Validated`κ° μ μ©λμ΄ μλ€.


---


## 1. Bean Validator λΌμ΄λΈλ¬λ¦¬ μ μ© 

**Bean Validator**λ₯Ό μ¬μ©νλ €λ©΄ `build.gradle`μ λΌμ΄λΈλ¬λ¦¬λ₯Ό μΆκ°ν΄μΌ νλ€. μ€νλ§ λΆνΈλ `implementation 'org.springframework.boot:spring-boot-starter-validation'` λΌμ΄λΈλ¬λ¦¬κ° μ μ©λμ΄ μμ λ, μλμΌλ‘ **Bean Validator**λ₯Ό μΈμνκ³  μ€νλ§μ ν΅ν©νλ€.


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

        // κ²μ¦μ μ€ν¨νλ©΄ λ€μ μλ ₯ νΌμΌλ‘ μ΄λ
        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v3/addForm";
        }

        // μ±κ³΅ λ‘μ§
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

**Bean Validator μ λΈνμ΄μμ΄ μ μ©λ Item ν΄λμ€**
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

    @NotBlank(message = "κ³΅λ°± λΆκ°")
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


μ€νν΄λ³΄λ©΄ μ λΈνμ΄μ κΈ°λ°μ **Bean Validator**κ° μ μμ μΌλ‘ λμνλ κ²μ νμΈν  μ μλ€. 

μ€νλ§ λΆνΈλ `LocalValidatorFactoryBean`μ κΈλ‘λ² Validatorλ‘ λ±λ‘νλ€. μ΄ Validatorκ° `@NotNull` κ°μ μ λΈνμ΄μμ λ³΄κ³  κ²μ¦μ μννλ€.

κΈλ‘λ² Validatorκ° μ μ©λμ΄ μκΈ° λλ¬Έμ μ»¨νΈλ‘€λ¬μλ `@Valid` λλ `@Validated`λ§ μ μ©νλ©΄ λλ€. κ²μ¦ μ€λ₯κ° λ°μνλ©΄ `FieldError`, `ObjectError`λ₯Ό μμ±ν΄μ `BindingResult`μ λ΄λλ€. 


> **μ°Έκ³ **
> μλμ κ°μ΄ κΈλ‘λ² Validatorλ₯Ό μ§μ  λ±λ‘νλ©΄ μ€νλ§ λΆνΈλ `Bean Validator`λ₯Ό κΈλ‘λ² Validatorλ‘ λ±λ‘ν  μ μλ€. λ°λΌμ μ λΈνμ΄μ κΈ°λ°μ **Bean Validator**κ° λμνμ§ μλλ€. 

**κΈλ‘λ² Validatorλ₯Ό μ§μ  λ±λ‘νλ λ°©μ**
```java
@SpringBootApplication
public class ItemServiceApplication implements WebMvcConfigurer {

    // κΈλ‘λ² κ²μ¦κΈ° μΆκ° 
    @Override public Validator getValidator() { 
    return new ItemValidator();
    }
    // ... 
} 
```

κ²μ¦ κΈ°λ₯μ `@Validated` λλ `@Valid`λ₯Ό λͺ¨λ μ¬μ©ν  μ μλ€. `@Validated`λ μ€νλ§ μ μ© κ²μ¦ μ λΈνμ΄μμ΄κ³ , `@Valid`λ μλ° νμ€ κ²μ¦ μ λΈνμ΄μμ΄λ€. κ²μ¦ κΈ°λ₯μ λμΌνμ§λ§ `@Validated`λ λ΄λΆμ `groups`λΌλ κΈ°λ₯μ ν¬ν¨νλ€.


## 2. Bean Validatorμ λμ μλ¦¬

κ²μ¦ μμλ₯Ό μ΄ν΄λ³΄λ©΄, λ¨Όμ  `@ModelAttribute`κ° κ°κ°μ νλμ νμ λ³νμ μλνλ€. νμ λ³νμ μ±κ³΅νλ©΄ `Validator`λ₯Ό μ μ©νκ³ , μ€ν¨νλ©΄ `typeMismatch` λ‘ `FieldError`λ₯Ό μΆκ°νλ€.
   
μ€μν μ μ λ°μΈλ©μ μ±κ³΅ν νλλ§ **Bean Validation**λ₯Ό μ μ©νλ€λ κ²μ΄λ€. **Bean Validator**λ λ°μΈλ©μ μ€ν¨ν νλλ₯Ό μ μ©νμ§ μλλ€. μ μκ°ν΄λ²λ©΄ νμ λ³νμ μ±κ³΅ν΄μ λ°μΈλ©μ μ±κ³΅ν νλλ§μ΄ **Bean Validation**μ μ μ©νλ μλ―Έκ° μλ€. κ°μ΄ μ‘΄μ¬νμ§ μμΌλ©΄ κ²μ¦μ λμλ λΆμ¬νκΈ° λλ¬Έμ΄λ€.


> μ) 
> - **`BeanValidation` μ±κ³΅ μΌμ΄μ€**
> `itemName`μ λ¬Έμ `"A"` μλ ₯  -> νμ λ³ν μ±κ³΅ -> `itemName`νλμ `BeanValidation` μ μ©
> - **`BeanValidation` μ€ν¨ μΌμ΄μ€**
> `price` μ λ¬Έμ `"A"` μλ ₯ -> `"A"`λ₯Ό μ«μ νμ λ³ν μλ μ€ν¨ -> `typeMismatch FieldError` μΆκ° -> `price` νλ `BeanValidation` λ―Έμ μ© 