> ### π βοΈ 
> **μ΄ κΈμ `Bean Validation` μ¬μ© μ, μ¬λ¬ formμ κ°λ³ μ μ©ν  μ μλ λ¬Έμ μ κ³Ό λ κ°μ§ ν΄κ²° λ°©λ²(groups, Form κ°μ²΄ λΆλ¦¬)μ νμ΅νκ³  μ λ¦¬ν ν¬μ€νμ΄λ€.**


---

# Bean Validationμ νκ³μ κ³Ό ν΄κ²° λ°©μ

λ°μ΄ν°λ₯Ό λ±λ‘νλ νμ΄μ§μ μμ νλ νμ΄μ§μ κ²μ¦ μκ΅¬μ¬ν­μ΄ λ€λ₯Ό κ²½μ° **Bean Validation**μ μ΄μ©ν κ²μ¦ λ°©μμ νκ³μ μ΄ μλ€. λμμ λ κ° `form`μ κ°κΈ° λ€λ₯Έ κ²μ¦ μ‘°κ±΄μ λΆμ¬ν  μ μκΈ° λλ¬Έμ΄λ€. μ΄λ₯Ό ν΄κ²°ν  μ μλ λ°©λ²μ **Bean Validation**μ΄ μ κ³΅νλ `groups` κΈ°λ₯μ μ΄μ©νκ±°λ `form κ°μ²΄λ₯Ό λΆλ¦¬`νλ κ²μ΄λ€.

**Bean Validation**μ μ΄λ€ νκ³μ μ κ°μ§κ³  μμΌλ©°, `groups` κΈ°λ₯κ³Ό `form κ°μ²΄λ₯Ό λΆλ¦¬`νλ λ°©μμΌλ‘ μ΄λ»κ² μ΄ λ¬Έμ λ₯Ό ν΄κ²°ν  μ μλμ§ μ΄ν΄λ³΄μ.


---

## 1. Bean Validationμ λμμ κ°κΈ° λ€λ₯Έ κ²μ¦ μ‘°κ±΄μ λΆμ¬ν  μ μλ€.

μλμ κ°μ΄ `λ±λ‘ κ²μ¦ μκ΅¬μ¬ν­`κ³Ό `μμ  κ²μ¦ μκ΅¬μ¬ν­`μ κ°κ° μΆ©μ‘±μμΌμΌ νλ€κ³  κ°μ ν΄λ³΄μ. 

>**λ±λ‘ κ²μ¦ μκ΅¬μ¬ν­**
- κ°κ²©, μλμ μ«μλ§ μλ ₯ κ°λ₯
- μνλͺμ νμ μλ ₯
- κ°κ²©μ 1,000μ μ΄μ ~ 1,000,000μ μ΄ν
- μλμ μ΅λ 9999κΉμ§ μλ ₯ κ°λ₯
- κ°κ²© * μλμ μ΄ν©μ μ΅μ 10,000μ μ΄μ

>**μμ  κ²μ¦ μκ΅¬μ¬ν­**
- μμ  μμλ μλμ λ¬΄μ νμΌλ‘ λ³κ²½ κ°λ₯
- μμ  μμλ id κ° νμ μλ ₯


λ±λ‘κ³Ό μμ  λ‘μ§μ λͺ¨λ `Item` κ°μ²΄λ₯Ό μ¬μ©νλ€. μ λΈνμ΄μ λ°©μμΌλ‘ λμνλ **Bean Validation**μ μ΄μ©νλ©΄ μλ‘ λ€λ₯Έ λ κ²μ¦ μ‘°κ±΄μ μΆ©μ‘±μν¬ μ μλ€. **μμ  κ²μ¦ μκ΅¬μ¬ν­**μ λ°μνμ¬ μλμ κ°μ΄ `Item` ν΄λμ€μ μ λΈνμ΄μ κ²μ¦ μ‘°κ±΄μ λ³κ²½νλ€λ©΄, **μμ  κ²μ¦ μκ΅¬μ¬ν­**μ μΆ©μ‘±νμ§λ§, λ±λ‘ λ‘μ§μ μ μμ μΌλ‘ λμν  μ μλ€. 

```java
package hello.itemservice.domain.item; 

@Data
public class Item {

    @NotNull //μμ  μκ΅¬μ¬ν­ μΆκ° 
    private Long id; 
    
    @NotBlank
    private String itemName;
    
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull    
    //@Max(9999) //μμ  μκ΅¬μ¬ν­ μΆκ° 
    private Integer quantity; 
    
    //... 
} 
```



---

## 2. Bean Validation - groupsμ μ΄μ©ν ν΄κ²° λ°©λ²


**Bean Validation**μ κ·Έλ£ΉμΌλ‘ λ¬Άμ΄ κ²μ¦ μ‘°κ±΄μ κ°κΈ° λ€λ₯΄κ² λΆμ¬ν  μ μλ `groups`λΌλ κΈ°λ₯μ μ κ³΅νλ€. `groups`λ₯Ό μ΄μ©νλ©΄, λ±λ‘ μ μ¬μ©ν  κ²μ¦ μ‘°κ±΄κ³Ό μμ  μ μ¬μ©ν  κ²μ¦ μ‘°κ±΄μ κ·Έλ£Ή λ¨μλ‘ λλμ΄ κ²μ¦ μ‘°κ±΄μ μ μ©ν  μ μλ€.

`groups`μ μ΄μ©νλ €λ©΄ μλμ κ°μ΄ λ³λμ μΈν°νμ΄μ€λ₯Ό μμ±ν΄μΌ νλ€. 

> **λ±λ‘ κ²μ¦μ© κ·Έλ£Ή**
```java
package hello.itemservice.domain.item;
public interface SaveCheck {
} 
```
> **μμ  κ²μ¦μ© κ·Έλ£Ή**
```java
package hello.itemservice.domain.item; 
public interface UpdateCheck {
}
```


`groups`μ μ μ©νλ λ°©λ²μ κ°λ¨νλ€. κ²μ¦ μ‘°κ±΄ μ λΈνμ΄μμλ `groups` μμ±μΌλ‘ μ μ©ν  κ·Έλ£Ήμ μ§μ νκ³ , μ»¨νΈλ‘€λ¬μλ μ΄λ€ κ·Έλ£Ήμ κ²μ¦ μ‘°κ±΄μ μ¬μ©ν μ§ μ§μ νλ€.


**Item ν΄λμ€μ groups μ μ©**
```java
package hello.itemservice.domain.item;

import lombok.Data;
import org.hibernate.validator.constraints.Range;
import org.hibernate.validator.constraints.ScriptAssert;

import javax.validation.constraints.Max;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
// @ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000 ", message = "μ΄ν©μ΄ 10,000μ λμ΄μΌ ν©λλ€.")
public class Item {

    @NotNull(groups = UpdateCheck.class) // μμ  μκ΅¬μ¬ν­ μΆκ°
    private Long id;

    @NotBlank(groups = {SaveCheck.class, UpdateCheck.class})
    private String itemName;

    @NotNull(groups = {SaveCheck.class, UpdateCheck.class})
    @Range(min = 1000, max = 1000000, groups = {SaveCheck.class, UpdateCheck.class})
    private Integer price;

    @NotNull(groups = {SaveCheck.class, UpdateCheck.class})
    @Max(value = 9999, groups = {SaveCheck.class})
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

**μ μ₯ λ‘μ§μ SaveCheck Groups μ μ©** 
```java
@PostMapping("/add")
public String addItemV2(@Validated(SaveCheck.class) @ModelAttribute Item item,BindingResult bindingResult, RedirectAttributes redirectAttributes) {
//... 
} 
```

**μμ  λ‘μ§μ UpdateCheck Groups μ μ©** 
```java
@PostMapping("/{itemId}/edit")
public String editV2(@PathVariable Long itemId, @Validated(UpdateCheck.class) @ModelAttribute Item item, BindingResult bindingResult) {
//... 
} 
```


> **μ°Έκ³ **
> `@Valid`λ `groups`λ₯Ό μ§μνμ§ μλλ€. `groups`λ₯Ό μ¬μ©νλ €λ©΄ `@Validated`λ₯Ό μ¬μ©ν΄μΌ νλ€. 


---

## 3. Bean Validation - Form μ μ‘ κ°μ²΄λ₯Ό λΆλ¦¬νμ¬ μ¬μ©νλ λ°©λ²

`groups` κΈ°λ₯μ μ λ°μ μΈ λ³΅μ‘λκ° μ¬λΌκ°λ λ¨μ μ΄ μλ€. μ€μ λ‘ μ€λ¬΄μμλ `groups` κΈ°λ₯μ μ μ¬μ©νμ§ μλλ€κ³  νλ€. Form μ μ‘ κ°μ²΄λ₯Ό λΆλ¦¬νμ¬ μ¬μ©νλ λ°©λ²μ΄ μ¬λ¬λ©΄μμ ν¨μ¨μ μ΄κΈ° λλ¬Έμ΄λ€.

λ°μ΄ν° λ±λ‘ μ formμμ μ λ¬νλ λ°μ΄ν°κ° Item λλ©μΈ κ°μ²΄μ λ± λ§μ λ¨μ΄μ§λ μΌμ κ±°μ μλ€. νμ λ±λ‘ μ νμκ³Ό κ΄λ ¨λ λ°μ΄ν°λ§ μ λ¬λ°λ κ²μ΄ μλλΌ, μ½κ΄ μ λ³΄ λ± λ€μν μ λ³΄κ° formμΌλ‘ λμ΄μ€κΈ° λλ¬Έμ΄λ€. 

`Item`κ³Ό κ΄κ³μλ μ λ§μ λΆκ° λ°μ΄ν°κ° λμ΄μ€κΈ° λλ¬Έμ λ³΄ν΅μ κ²½μ° `Item`μ μ§μ  μ λ¬λ°μ§ μλλ€. λ³΅μ‘ν νΌμ λ°μ΄ν°λ₯Ό μ»¨νΈλ‘€λ¬κΉμ§ μ λ¬ν  λ³λμ κ°μ²΄λ₯Ό λ§λ€μ΄ μ¬μ©νλ€.

μλ₯Ό λ€λ©΄, `ItemSaveForm`μ΄λΌλ λ±λ‘ λ°μ΄ν° νΌ μ λ¬ κ°μ²΄λ₯Ό λ§λ€κ³  `@ModelAttribute`λ‘ μ¬μ©νλ€. `ItemSaveForm`λ₯Ό ν΅ν΄ μ»¨νΈλ‘€λ¬μμ νΌ λ°μ΄ν°λ₯Ό μ λ¬ λ°λλ€. μ΄ν μ»¨νΈλ‘€λ¬μμ νμν λ°μ΄ν°λ§ μΆλ €λ΄ `Item`μ μμ±νλ€.


form μ λ¬ κ°μ²΄λ₯Ό μ¬μ©νλ λ°©μμ μ μ²΄ νλ‘μΈμ€λ μλμ κ°λ€. 
> **form μ λ¬ κ°μ²΄ λ°©μ**
> **`HTML Form` -> `ItemSaveForm` -> `Controller` -> `Item μμ±` -> `Repository`** 

μ΄λ¬ν λ°©μμ μ μ‘νλ νΌ λ°μ΄ν°κ° λ³΅μ‘ν΄λ μ΅μ νλ νΌ κ°μ²΄λ₯Ό μ¬μ©νκΈ° λλ¬Έμ λ°μ΄ν° μ λ¬μ λ¬Έμ κ° μλ€. λν λ±λ‘κ³Ό μμ μ© νΌ κ°μ²΄λ₯Ό λ³λλ‘ κ΄λ¦¬νμ¬ κ²μ¦μ μ€λ³΅ λ¬Έμ λ₯Ό ν΄κ²°ν  μ μλ€.
  
λ±λ‘ νμ΄μ§μ λ°μ΄ν°λ₯Ό λ΄λ ItemSaveFormκ³Ό μμ  νμ΄μ§μ λ°μ΄ν°λ₯Ό λ΄λ ItemUpdateFormμΌλ‘ form κ°μ²΄λ₯Ό λΆλ¦¬νκ³ , κ°κΈ° λ€λ₯Έ κ²μ¦ λ‘μ§μ κ΅¬νν΄λ³΄μ.

**ItemSaveForm - ITEM μ μ₯μ© νΌ**
```java
package hello.itemservice.web.validation.form;

import lombok.Data;
import org.hibernate.validator.constraints.Range;

import javax.validation.constraints.Max;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
public class ItemSaveForm {

    @NotBlank
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull
    @Max(value = 9999)
    private Integer quantity;
}
```

**ItemUpdateForm - ITEM μμ μ© νΌ** 
```java
package hello.itemservice.web.validation.form;

import lombok.Data;
import org.hibernate.validator.constraints.Range;

import javax.validation.constraints.Max;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
public class ItemUpdateForm {

    @NotNull
    private Long id;

    @NotBlank
    private String itemName;

    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    // μμ μμλ μλμ μμ λ‘­κ² λ³κ²½ν  μ μλ€.
    private Integer quantity;
}

```


μ»¨νΈλ‘€λ¬μ μ²λ¦¬ λ‘μ§λ λ±λ‘μ© νΌ κ°μ²΄μ μμ μ© νΌ κ°μ²΄μ μ¬μ©μ κ΅¬λΆν΄μΌ νλ€.

**ValidationItemControllerV4** 

```java
package hello.itemservice.web.validation;

import hello.itemservice.domain.item.Item;
import hello.itemservice.domain.item.ItemRepository;
import hello.itemservice.domain.item.UpdateCheck;
import hello.itemservice.web.validation.form.ItemSaveForm;
import hello.itemservice.web.validation.form.ItemUpdateForm;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;
import org.springframework.web.servlet.mvc.support.RedirectAttributes;

import java.util.List;

@Slf4j
@Controller
@RequestMapping("/validation/v4/items")
@RequiredArgsConstructor
public class ValidationItemControllerV4 {

    private final ItemRepository itemRepository;

    @GetMapping
    public String items(Model model) {
        List<Item> items = itemRepository.findAll();
        model.addAttribute("items", items);
        return "validation/v4/items";
    }

    @GetMapping("/{itemId}")
    public String item(@PathVariable long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "validation/v4/item";
    }

    @GetMapping("/add")
    public String addForm(Model model) {
        model.addAttribute("item", new Item());
        return "validation/v4/addForm";
    }

    @PostMapping("/add")
    public String addItem(@Validated @ModelAttribute("item") ItemSaveForm form, BindingResult bindingResult, RedirectAttributes redirectAttributes) {

        // λ³΅ν© μ‘°κ±΄(κ°κ²©*μλ 10,000μ μ΄μ) κ²μ¦
        if (form.getPrice() != null && form.getQuantity() != null) {
            int resultPrice = form.getPrice() * form.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        // κ²μ¦μ μ€ν¨νλ©΄ λ€μ μλ ₯ νΌμΌλ‘ μ΄λ
        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v4/addForm";
        }

        // μ±κ³΅ λ‘μ§μΌ λ μμ΄ν μμ±νμ¬ save μ€ν
        Item item = new Item();
        item.setItemName(form.getItemName());
        item.setPrice(form.getPrice());
        item.setQuantity(form.getQuantity());

        Item savedItem = itemRepository.save(item);
        redirectAttributes.addAttribute("itemId", savedItem.getId());
        redirectAttributes.addAttribute("status", true);
        return "redirect:/validation/v4/items/{itemId}";
    }

    @GetMapping("/{itemId}/edit")
    public String editForm(@PathVariable Long itemId, Model model) {
        Item item = itemRepository.findById(itemId);
        model.addAttribute("item", item);
        return "validation/v4/editForm";
    }

    @PostMapping("/{itemId}/edit")
    public String edit(@PathVariable Long itemId, @Validated @ModelAttribute("item") ItemUpdateForm form, BindingResult bindingResult) {

        // λ³΅ν© μ‘°κ±΄(κ°κ²©*μλ 10,000μ μ΄μ) κ²μ¦
        if (form.getPrice() != null && form.getQuantity() != null) {
            int resultPrice = form.getPrice() * form.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        // κ²μ¦μ μ€ν¨νλ©΄ λ€μ μλ ₯ νΌμΌλ‘ μ΄λ
        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v4/editForm";
        }

        Item itemParam = new Item();
        itemParam.setItemName(form.getItemName());
        itemParam.setPrice(form.getPrice());
        itemParam.setQuantity(form.getQuantity());

        itemRepository.update(itemId, itemParam);
        return "redirect:/validation/v4/items/{itemId}";
    }
}

```


λ±λ‘ λ‘μ§μ `Item` λμ μ `ItemSaveform`μ μ λ¬ λ°λλ€. κ·Έλ¦¬κ³  `@Validated`λ‘ κ²μ¦μ μννλ€. `BindingResult`λ‘ κ²μ¦ κ²°κ³Όλ₯Ό λ°λλ€. 

μ»¨νΈλ‘€λ¬μλ ν κ°μ§ μμμ λ μΆκ°ν΄μΌ νλ€. μλμ κ°μ΄ κ²μ¦μ΄ μλ£λ νΌ κ°μ²΄ λ°μ΄ν°λ₯Ό `Item`μΌλ‘ λ³νν΄μΌ νκΈ° λλ¬Έμ΄λ€.

**νΌ κ°μ²΄λ₯Ό ItemμΌλ‘ λ³ν** 

```java
Item item = new Item(); 

item.setItemName(form.getItemName()); 
item.setPrice(form.getPrice()); 
item.setQuantity(form.getQuantity());

Item savedItem = itemRepository.save(item); 
```

μμ  λ‘μ§λ λ±λ‘ λ‘μ§κ³Ό λμΌν νλ‘μΈμ€λ₯Ό κ°μ§λ€. νΌ κ°μ²΄λ₯Ό `Item` κ°μ²΄λ‘ λ³ννλ κ³Όμ κΉμ§ λμΌνλ€.

```java
@PostMapping("/{itemId}/edit")
public String edit(@PathVariable Long itemId, @Validated @ModelAttribute("item") ItemUpdateForm form, BindingResult bindingResult) {
//... 
} 
```
