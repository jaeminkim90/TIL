> ### 📖 ✏️ 
> **이 글은 애노테이션으로 검증 로직을 구현할 수 있는 `Bean Validation`인터페이스와 구현체인 `hibernate Validator` 사용법을 학습하고 정리한 포스팅이다.**

---

# 애노테이션으로 검증 로직을 구현하는 Bean Validation 직접 사용법

**Bean Validation**은 보편적으로 사용되는 빈 값, 범위 등에 대한 검증 로직을 모든 프로젝트에 범용적으로 사용할 수 있도록 표준화한 인터페이스다. 애노테이션 기반을 동작하기 때문에 검증 로직을 편리하게 구현할 수 있다.
 
---
 
## 1. Bean Validation이란? 

아래는 **Bean Validation**의 사용 예시다. **Bean Validation**을 사용하면 간단한 애노테이션만으로도 공백, 빈칸, 범위, 최대치 등의 검증 로직을 구현할 수 있다.

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
**Bean Validation**은 특정한 구현체가 아니다. **Bean Validation 2.0(JSR-380)**이라는 기술 표준이다. 쉽게 말해, 다양한 검증 애노테이션과 여러 인터페이스의 모음이다. 마치 JPA가 표준 기술이고, 그 구현체로 하이버네이트가 있는 것과 같다. 

**Bean Validation**의 구현체는 **hibernate Validator**를 가장 많이 사용한다. 이름에 하이버네이트가 붙었을 뿐, ORM과는 관련이 없다.



> **참고 : 하이버네이트 Validator 관련 링크**
>
> **공식 사이트**: http://hibernate.org/validator
> **공식 메뉴얼**: https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single
> **검증 애노테이션 모음**: https://docs.jboss.org/hibernate/validator/6.2/reference/en-US/html_single/#validator-defineconstraints-spec


---

## 2. Bean Validation 사용법

스프링은 이미 **Bean Validation**를 스프링에 통합하여 지원한다. 하지만 이번 포스팅에서는 **Bean Validation**를 직접 사용하는 방법을 알아볼 것이다. 테스트 코드를 통해 **Bean Validation**를 직접 사용해보자.


### 2-1. Bean Validation 의존관계 추가 

**Bean Validation**을 사용하려면 의존관계 추가가 필요하다. 

`build.gradle`
```java
implementation 'org.springframework.boot:spring-boot-starter-validation'
```
`spring-boot-starter-validation` 의존관계를 추가하면 아래와 같이 2개의 라이브러리가 추가된다. 


>**`Jakarta Bean Validation`**
- `jakarta.validation-api`: Bean Validation 인터페이스 
- `hibernate-validator`: Bean Validation의 구현체 


### 2-2. Item 클래스에 Bean Validation 애노테이션 적용

**Bean Validation**을 사용하기 위해 검증을 적용할 클래스 필드에 **Bean Validation** 애노테이션을 적용한다.


다양한 검증 애노테이션이 있지만, 예제에서는 아래와 같이 4개의 애노테이션을 추가했다.
> **Bean Validation 애노테이션**
> - `@NotBlank`: 빈값 + 공백만 있는 경우를 허용하지 않는다. 
> - `@NotNull`: null 을 허용하지 않는다. 
> - `@Range(min = 1000, max = 1000000)`: 범위 안의 값이어야 한다. 
> - `@Max(9999)`: 최대 9999까지만 허용한다. 
 

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



> **참고**
>
> - `javax.validation.constraints.NotNull`: 특정 구현에 관계없이 제공되는 표준 인터페이스
> - `org.hibernate.validator.constraints.Range`: hibernate validator 구현체를 사용할 때만 제공되는 검증 기능 


### 2-2. Bean Validation 직접 구현

아래와 같이 **BeanValidationTest** 클래스를 생성하고 **Bean Validation**을 직접 구현하는 테스트 코드를 작성해보자.

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
        item.setItemName(" "); // 공백
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

아래 코드가 **빈 검증기(Bean Validation)**를 생성한다. 스프링이 제공하는 통합 기능을 사용하면, 직접 코드를 작성하는 경우는 거의 없다. 원리를 이해한다는 생각으로 학습해보자.

```java
ValidatorFactory factory = Validation.buildDefaultValidatorFactory();
Validator validator = factory.getValidator();
```

`factory.getValidator()`를 통해 얻은 `Validator`객체를 이용하여 검증 대상인 `item`을 검증기에 넣고 결과를 반환받는다. 

`Set`에는 `ConstraintViolation`이라는 검증 오류가 담긴다. 
검증 오류가 없을 경우 빈 값을 가진다.
```java
Set<ConstraintViolation<Item>> violations = validator.validate(item);
```

### 2-3. Bean Validation 실행 결과



아래는 `ConstraintViolation`의 출력 결과다. 검증 오류가 발생한 `객체`, `필드`, `메시지 정보` 등 다양한 정보를 확인할 수 있다. 

```java
violation = ConstraintViolationImpl{interpolatedMessage='공백일 수 없습니다', propertyPath=itemName, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.NotBlank.message}'}
violation.getMessage() = 공백일 수 없습니다

violation = ConstraintViolationImpl{interpolatedMessage='9999 이하여야 합니다', propertyPath=quantity, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{javax.validation.constraints.Max.message}'}
violation.getMessage() = 9999 이하여야 합니다

violation = ConstraintViolationImpl{interpolatedMessage='1000에서 1000000 사이여야 합니다', propertyPath=price, rootBeanClass=class hello.itemservice.domain.item.Item, messageTemplate='{org.hibernate.validator.constraints.Range.message}'}
violation.getMessage() = 1000에서 1000000 사이여야 합니다
```


