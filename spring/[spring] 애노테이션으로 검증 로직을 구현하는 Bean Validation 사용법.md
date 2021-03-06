> ### π βοΈ 
> **μ΄ κΈμ μ λΈνμ΄μμΌλ‘ κ²μ¦ λ‘μ§μ κ΅¬νν  μ μλ `Bean Validation`μΈν°νμ΄μ€μ κ΅¬νμ²΄μΈ `hibernate Validator` μ¬μ©λ²μ νμ΅νκ³  μ λ¦¬ν ν¬μ€νμ΄λ€.**

---

# μ λΈνμ΄μμΌλ‘ κ²μ¦ λ‘μ§μ κ΅¬ννλ Bean Validation μ§μ  μ¬μ©λ²

**Bean Validation**μ λ³΄νΈμ μΌλ‘ μ¬μ©λλ λΉ κ°, λ²μ λ±μ λν κ²μ¦ λ‘μ§μ λͺ¨λ  νλ‘μ νΈμ λ²μ©μ μΌλ‘ μ¬μ©ν  μ μλλ‘ νμ€νν μΈν°νμ΄μ€λ€. μ λΈνμ΄μ κΈ°λ°μ λμνκΈ° λλ¬Έμ κ²μ¦ λ‘μ§μ νΈλ¦¬νκ² κ΅¬νν  μ μλ€.
 
---
 
## 1. Bean Validationμ΄λ? 

μλλ **Bean Validation**μ μ¬μ© μμλ€. **Bean Validation**μ μ¬μ©νλ©΄ κ°λ¨ν μ λΈνμ΄μλ§μΌλ‘λ κ³΅λ°±, λΉμΉΈ, λ²μ, μ΅λμΉ λ±μ κ²μ¦ λ‘μ§μ κ΅¬νν  μ μλ€.

```java
public class Item { 

    private Long id;
    
    @NotBlank
    private String itemName;
    
    @NotNull
    @Range(min = 1000, max = 1000000)
    private Integer price;
    
    @NotNull
    @Max(9999)
    private Integer quantity;

    //...
} 
```
**Bean Validation**μ νΉμ ν κ΅¬νμ²΄κ° μλλ€. **Bean Validation 2.0(JSR-380)**μ΄λΌλ κΈ°μ  νμ€μ΄λ€. μ½κ² λ§ν΄, λ€μν κ²μ¦ μ λΈνμ΄μκ³Ό μ¬λ¬ μΈν°νμ΄μ€μ λͺ¨μμ΄λ€. λ§μΉ JPAκ° νμ€ κΈ°μ μ΄κ³ , κ·Έ κ΅¬νμ²΄λ‘ νμ΄λ²λ€μ΄νΈκ° μλ κ²κ³Ό κ°λ€. 

**Bean Validation**μ κ΅¬νμ²΄λ **hibernate Validator**λ₯Ό κ°μ₯ λ§μ΄ μ¬μ©νλ€. μ΄λ¦μ νμ΄λ²λ€μ΄νΈκ° λΆμμ λΏ, ORMκ³Όλ κ΄λ ¨μ΄ μλ€.



> **μ°Έκ³  : νμ΄λ²λ€μ΄νΈ Validator κ΄λ ¨ λ§ν¬**
>
> **κ³΅μ μ¬μ΄νΈ**: http://hibernate.org/validator
> **κ³΅μ λ©λ΄μΌ**: https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single
> **κ²μ¦ μ λΈνμ΄μ λͺ¨μ**: https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec


---

## 2. Bean Validation μ¬μ©λ²

μ€νλ§μ μ΄λ―Έ **Bean Validation**λ₯Ό μ€νλ§μ ν΅ν©νμ¬ μ§μνλ€. νμ§λ§ μ΄λ² ν¬μ€νμμλ **Bean Validation**λ₯Ό μ§μ  μ¬μ©νλ λ°©λ²μ μμλ³Ό κ²μ΄λ€. νμ€νΈ μ½λλ₯Ό ν΅ν΄ **Bean Validation**λ₯Ό μ§μ  μ¬μ©ν΄λ³΄μ.


### 2-1. Bean Validation μμ‘΄κ΄κ³ μΆκ° 

**Bean Validation**μ μ¬μ©νλ €λ©΄ μμ‘΄κ΄κ³ μΆκ°κ° νμνλ€. 

`build.gradle`
```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```
`spring-boot-starter-validation` μμ‘΄κ΄κ³λ₯Ό μΆκ°νλ©΄ μλμ κ°μ΄ 2κ°μ λΌμ΄λΈλ¬λ¦¬κ° μΆκ°λλ€. 


>**`Jakarta Bean Validation`**
- `jakarta.validation-api`: Bean Validation μΈν°νμ΄μ€ 
- `hibernate-validator`: Bean Validationμ κ΅¬νμ²΄ 


### 2-2. Item ν΄λμ€μ Bean Validation μ λΈνμ΄μ μ μ©

**Bean Validation**μ μ¬μ©νκΈ° μν΄ κ²μ¦μ μ μ©ν  ν΄λμ€ νλμ **Bean Validation** μ λΈνμ΄μμ μ μ©νλ€.


λ€μν κ²μ¦ μ λΈνμ΄μμ΄ μμ§λ§, μμ μμλ μλμ κ°μ΄ 4κ°μ μ λΈνμ΄μμ μΆκ°νλ€.
> **Bean Validation μ λΈνμ΄μ**
> - `@NotBlank`: λΉκ° + κ³΅λ°±λ§ μλ κ²½μ°λ₯Ό νμ©νμ§ μλλ€. 
> - `@NotNull`: null μ νμ©νμ§ μλλ€. 
> - `@Range(min = 1000, max = 1000000)`: λ²μ μμ κ°μ΄μ΄μΌ νλ€. 
> - `@Max(9999)`: μ΅λ 9999κΉμ§λ§ νμ©νλ€. 
 

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



> **μ°Έκ³ **
>
> - `javax.validation.constraints.NotNull`: νΉμ  κ΅¬νμ κ΄κ³μμ΄ μ κ³΅λλ νμ€ μΈν°νμ΄μ€
> - `org.hibernate.validator.constraints.Range`: hibernate validator κ΅¬νμ²΄λ₯Ό μ¬μ©ν  λλ§ μ κ³΅λλ κ²μ¦ κΈ°λ₯ 


### 2-2. Bean Validation μ§μ  κ΅¬ν

μλμ κ°μ΄ **BeanValidationTest** ν΄λμ€λ₯Ό μμ±νκ³  **Bean Validation**μ μ§μ  κ΅¬ννλ νμ€νΈ μ½λλ₯Ό μμ±ν΄λ³΄μ.

```java
package hello.itemservice.validation;

import hello.itemservice.domain.item.Item;
import org.junit.jupiter.api.Test;

import javax.validation.ConstraintViolation;
import javax.validation.Validation;
import javax.validation.Validator;
import javax.validation.ValidatorFactory;
import java.util.Set;

public class BeanValidationTest {

    @Test
    void beanValidation() {
        ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
        Validator validator = factory.getValidator();

        Item item = new Item();
        item.setItemName(" "); // κ³΅λ°±
        item.setPrice(0);
        item.setQuantity(10000);

        Set<ConstraintViolation<Item>> violations = validator.validate(item);
        for (ConstraintViolation<Item> violation : violations) {
            System.out.println("violation = " + violation);
            System.out.println("violation.getMessage() = " + violation.getMessage());
        }
    }
}

```

μλ μ½λκ° **λΉ κ²μ¦κΈ°(Bean Validation)**λ₯Ό μμ±νλ€. μ€νλ§μ΄ μ κ³΅νλ ν΅ν© κΈ°λ₯μ μ¬μ©νλ©΄, μ§μ  μ½λλ₯Ό μμ±νλ κ²½μ°λ κ±°μ μλ€. μλ¦¬λ₯Ό μ΄ν΄νλ€λ μκ°μΌλ‘ νμ΅ν΄λ³΄μ.

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
```

`factory.getValidator()`λ₯Ό ν΅ν΄ μ»μ `Validator`κ°μ²΄λ₯Ό μ΄μ©νμ¬ κ²μ¦ λμμΈ `item`μ κ²μ¦κΈ°μ λ£κ³  κ²°κ³Όλ₯Ό λ°νλ°λλ€. 

`Set`μλ `ConstraintViolation`μ΄λΌλ κ²μ¦ μ€λ₯κ° λ΄κΈ΄λ€. 
κ²μ¦ μ€λ₯κ° μμ κ²½μ° λΉ κ°μ κ°μ§λ€.
```java
Set<ConstraintViolation<Item>> violations = validator.validate(item);
```

### 2-3. Bean Validation μ€ν κ²°κ³Ό



μλλ `ConstraintViolation`μ μΆλ ₯ κ²°κ³Όλ€. κ²μ¦ μ€λ₯κ° λ°μν `κ°μ²΄`, `νλ`, `λ©μμ§ μ λ³΄` λ± λ€μν μ λ³΄λ₯Ό νμΈν  μ μλ€. 

```java
violation = ConstraintViolationImpl{interpolatedMessage='κ³΅λ°±μΌ μ μμ΅λλ€', propertyPath=itemName, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.NotBlank.message}'}
violation.getMessage() = κ³΅λ°±μΌ μ μμ΅λλ€

violation = ConstraintViolationImpl{interpolatedMessage='9999 μ΄νμ¬μΌ ν©λλ€', propertyPath=quantity, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.Max.message}'}
violation.getMessage() = 9999 μ΄νμ¬μΌ ν©λλ€

violation = ConstraintViolationImpl{interpolatedMessage='1000μμ 1000000 μ¬μ΄μ¬μΌ ν©λλ€', propertyPath=price, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{org.hibernate.validator.constraints.Range.message}'}
violation.getMessage() = 1000μμ 1000000 μ¬μ΄μ¬μΌ ν©λλ€
```


