> ### 📖 ✏️ 
> **이 글은 `Bean Validation` 사용 시, 여러 form에 개별 적용할 수 없는 문제점과 두 가지 해결 방법(groups, Form 객체 분리)을 학습하고 정리한 포스팅이다.**


---

# Bean Validation의 한계점과 해결 방안

데이터를 등록하는 페이지와 수정하는 페이지의 검증 요구사항이 다를 경우 **Bean Validation**을 이용한 검증 방식은 한계점이 있다. 동시에 두 개 `form`에 각기 다른 검증 조건을 부여할 수 없기 때문이다. 이를 해결할 수 있는 방법은 **Bean Validation**이 제공하는 `groups` 기능을 이용하거나 `form 객체를 분리`하는 것이다.

**Bean Validation**은 어떤 한계점을 가지고 있으며, `groups` 기능과 `form 객체를 분리`하는 방식으로 어떻게 이 문제를 해결할 수 있는지 살펴보자.


---

## 1. Bean Validation은 동시에 각기 다른 검증 조건을 부여할 수 없다.

아래와 같이 `등록 검증 요구사항`과 `수정 검증 요구사항`을 각각 충족시켜야 한다고 가정해보자. 

>**등록 검증 요구사항**
- 가격, 수량은 숫자만 입력 가능
- 상품명은 필수 입력
- 가격은 1,000원 이상 ~ 1,000,000원 이하
- 수량은 최대 9999까지 입력 가능
- 가격 * 수량의 총합은 최소 10,000원 이상

>**수정 검증 요구사항**
- 수정 시에는 수량을 무제한으로 변경 가능
- 수정 시에는 id 값 필수 입력


등록과 수정 로직은 모두 `Item` 객체를 사용한다. 애노테이션 방식으로 동작하는 **Bean Validation**을 이용하면 서로 다른 두 검증 조건을 충족시킬 수 없다. **수정 검증 요구사항**을 반영하여 아래와 같이 `Item` 클래스의 애노테이션 검증 조건을 변경한다면, **수정 검증 요구사항**은 충족하지만, 등록 로직은 정상적으로 동작할 수 없다. 

```java
package hello.itemservice.domain.item; 

@Data
public class Item {

    @NotNull //수정 요구사항 추가 
    private Long id; 
    
    @NotBlank
    private String itemName;
    
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;

    @NotNull    
    //@Max(9999) //수정 요구사항 추가 
    private Integer quantity; 
    
    //... 
} 
```



---

## 2. Bean Validation - groups을 이용한 해결 방법


**Bean Validation**은 그룹으로 묶어 검증 조건을 각기 다르게 부여할 수 있는 `groups`라는 기능을 제공한다. `groups`를 이용하면, 등록 시 사용할 검증 조건과 수정 시 사용할 검증 조건을 그룹 단위로 나누어 검증 조건을 적용할 수 있다.

`groups`을 이용하려면 아래와 같이 별도의 인터페이스를 생성해야 한다. 

> **등록 검증용 그룹**
```java
package hello.itemservice.domain.item;
public interface SaveCheck {
} 
```
> **수정 검증용 그룹**
```java
package hello.itemservice.domain.item; 
public interface UpdateCheck {
}
```


`groups`을 적용하는 방법은 간단하다. 검증 조건 애노테이션에는 `groups` 속성으로 적용할 그룹을 지정하고, 컨트롤러에는 어떤 그룹의 검증 조건을 사용할지 지정한다.


**Item 클래스에 groups 적용**
```java
package hello.itemservice.domain.item;

import lombok.Data;
import org.hibernate.validator.constraints.Range;
import org.hibernate.validator.constraints.ScriptAssert;

import javax.validation.constraints.Max;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotNull;

@Data
// @ScriptAssert(lang = "javascript", script = "_this.price * _this.quantity >= 10000 ", message = "총합이 10,000을 넘어야 합니다.")
public class Item {

    @NotNull(groups = UpdateCheck.class) // 수정 요구사항 추가
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

**저장 로직에 SaveCheck Groups 적용** 
```java
@PostMapping("/add")
public String addItemV2(@Validated(SaveCheck.class) @ModelAttribute Item item,BindingResult bindingResult, RedirectAttributes redirectAttributes) {
//... 
} 
```

**수정 로직에 UpdateCheck Groups 적용** 
```java
@PostMapping("/{itemId}/edit")
public String editV2(@PathVariable Long itemId, @Validated(UpdateCheck.class) @ModelAttribute Item item, BindingResult bindingResult) {
//... 
} 
```


> **참고**
> `@Valid`는 `groups`를 지원하지 않는다. `groups`를 사용하려면 `@Validated`를 사용해야 한다. 


---

## 3. Bean Validation - Form 전송 객체를 분리하여 사용하는 방법

`groups` 기능은 전반적인 복잡도가 올라가는 단점이 있다. 실제로 실무에서는 `groups` 기능을 잘 사용하지 않는다고 한다. Form 전송 객체를 분리하여 사용하는 방법이 여러면에서 효율적이기 때문이다.

데이터 등록 시 form에서 전달하는 데이터가 Item 도메인 객체와 딱 맞아 떨어지는 일은 거의 없다. 회원 등록 시 회원과 관련된 데이터만 전달받는 것이 아니라, 약관 정보 등 다양한 정보가 form으로 넘어오기 때문이다. 

`Item`과 관계없는 수 많은 부가 데이터가 넘어오기 때문에 보통의 경우 `Item`을 직접 전달받지 않는다. 복잡한 폼의 데이터를 컨트롤러까지 전달할 별도의 객체를 만들어 사용한다.

예를 들면, `ItemSaveForm`이라는 등록 데이터 폼 전달 객체를 만들고 `@ModelAttribute`로 사용한다. `ItemSaveForm`를 통해 컨트롤러에서 폼 데이터를 전달 받는다. 이후 컨트롤러에서 필요한 데이터만 추려내 `Item`을 생성한다.


form 전달 객체를 사용하는 방식의 전체 프로세스는 아래와 같다. 
> **form 전달 객체 방식**
> **`HTML Form` -> `ItemSaveForm` -> `Controller` -> `Item 생성` -> `Repository`** 

이러한 방식은 전송하는 폼 데이터가 복잡해도 최적화된 폼 객체를 사용하기 때문에 데이터 전달에 문제가 없다. 또한 등록과 수정용 폼 객체를 별도로 관리하여 검증의 중복 문제를 해결할 수 있다.
  
등록 페이지의 데이터를 담는 ItemSaveForm과 수정 페이지의 데이터를 담는 ItemUpdateForm으로 form 객체를 분리하고, 각기 다른 검증 로직을 구현해보자.

**ItemSaveForm - ITEM 저장용 폼**
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

**ItemUpdateForm - ITEM 수정용 폼** 
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

    // 수정에서는 수량은 자유롭게 변경할 수 있다.
    private Integer quantity;
}

```


컨트롤러의 처리 로직도 등록용 폼 객체와 수정용 폼 객체의 사용을 구분해야 한다.

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

        // 복합 조건(가격*수량 10,000원 이상) 검증
        if (form.getPrice() != null && form.getQuantity() != null) {
            int resultPrice = form.getPrice() * form.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        // 검증에 실패하면 다시 입력 폼으로 이동
        if (bindingResult.hasErrors()) {
            log.info("errors = {} ", bindingResult);
            return "validation/v4/addForm";
        }

        // 성공 로직일 때 아이템 생성하여 save 실행
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

        // 복합 조건(가격*수량 10,000원 이상) 검증
        if (form.getPrice() != null && form.getQuantity() != null) {
            int resultPrice = form.getPrice() * form.getQuantity();
            if (resultPrice < 10000) {
                bindingResult.reject("totalPriceMin", new Object[]{10000, resultPrice}, null);
            }
        }

        // 검증에 실패하면 다시 입력 폼으로 이동
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


등록 로직은 `Item` 대신에 `ItemSaveform`을 전달 받는다. 그리고 `@Validated`로 검증을 수행한다. `BindingResult`로 검증 결과를 받는다. 

컨트롤러에는 한 가지 작업을 더 추가해야 한다. 아래와 같이 검증이 완료된 폼 객체 데이터를 `Item`으로 변환해야 하기 때문이다.

**폼 객체를 Item으로 변환** 

```java
Item item = new Item(); 

item.setItemName(form.getItemName()); 
item.setPrice(form.getPrice()); 
item.setQuantity(form.getQuantity());

Item savedItem = itemRepository.save(item); 
```

수정 로지도 등록 로직과 동일한 프로세스를 가진다. 폼 객체를 `Item` 객체로 변환하는 과정까지 동일하다.

```java
@PostMapping("/{itemId}/edit")
public String edit(@PathVariable Long itemId, @Validated @ModelAttribute("item") ItemUpdateForm form, BindingResult bindingResult) {
//... 
} 
```
