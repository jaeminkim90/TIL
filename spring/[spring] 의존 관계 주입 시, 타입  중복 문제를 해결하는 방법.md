> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---


# μμ‘΄ κ΄κ³ μ£Όμ μ, νμ μ€λ³΅ λ¬Έμ λ₯Ό ν΄κ²°νλ λ°©λ²



μ€νλ§ μ»¨νμ΄λκ° μμ‘΄ κ΄κ³λ₯Ό μ£Όμν  λ, `@Autowired` λ νμ(Type)μΌλ‘ ν΄λμ€λ₯Ό μ‘°ννλ€. νμμΌλ‘ μ‘°ννλ λ°©μμ `getBean()`μ μ΄μ©νμ¬ Beanμ μ‘°ννλ κ²κ³Ό μ μ¬νλ€. λΆλͺ¨ νμμ μ‘°ννλ©΄ μμ νμκΉμ§ λͺ¨λ μ‘°ν λμμ ν¬ν¨λλ€.

μλ₯Ό λ€μ΄, `DiscountPolicy` νμμ μ‘°ννλ©΄ νμ νμμΈ `FixDiscountPolicy`μ `RateDiscountPolicy`κ° λͺ¨λ μ‘°ν λμμ ν¬ν¨λλ€.

## 1. μ€λ³΅λ νμμ ν΄λμ€κ° μ‘΄μ¬ν  κ²½μ° μ΄λ€ μΌμ΄ λ°μν κΉ?

λ¬Έμ λ λ ν΄λμ€ λͺ¨λ μ€νλ§ BeanμΌλ‘ λ±λ‘λμ΄ μλ κ²½μ°λ€. μ€λ³΅λ κ²°κ³Όκ° μ‘°νλλ λ¬Έμ κ° λ°μνλ€. μλμ κ°μ΄ λμΌν νμμ κ°μ§ λ ν΄λμ€λ₯Ό λͺ¨λ Beanμ λ±λ‘νκ³ , μμ‘΄ κ΄κ³λ₯Ό μ£Όμνλ μ½λλ₯Ό μ€νν΄λ³΄μ.

**`DiscountPolicy` νμμ κ°μ§ `FixDiscountPolicy.class`**
```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```
**`DiscountPolicy` νμμ κ°μ§ `RateDiscountPolicy.class`**
```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {}
```

**μμ‘΄ κ΄κ³ μλ μ£Όμμ μ€ννλ μ½λ**
```java
@Autowired
private DiscountPolicy discountPolicy
```

**μ€ν κ²°κ³Ό, `NoUniqueBeanDefinitionException` μ€λ₯κ° λ°μνλ€.** 
```java
NoUniqueBeanDefinitionException: No qualifying bean of type
'hello.core.discount.DiscountPolicy' available: expected single matching bean
but found 2: fixDiscountPolicy,rateDiscountPolicy
```

μ€λ₯λ©μμ§λ νλμ Beanμ κΈ°λνμΌλ, `fixDiscountPolicy`μ `rateDiscountPolicy` 2κ°μ Beanμ λ°κ²¬νλ€κ³  μλ €μ€λ€. μ΄λ, νΉμ  ν΄λμ€λ₯Ό μμ‘΄ κ΄κ³λ‘ μ£ΌμνκΈ° μν΄ νμ νμμ μ§μ ν  μλ μμ κ²μ΄λ€. νμ§λ§, νμ νμμΌλ‘ μ§μ νλ κ²μ DIPλ₯Ό μλ°°νκ³  μ μ°μ±λ λ¨μ΄μ§λ€. λν, μ΄λ¦λ§ λ€λ₯΄κ³  μμ ν λμΌν νμμ μ€νλ§ Bean 2κ°κ° μμ λλ ν΄κ²°μ±μ΄ λ  μ μλ€.


## 2. μ€λ³΅λ νμ λ¬Έμ λ₯Ό ν΄κ²°νλ 4κ°μ§ λ°©λ²

μ¬λ¬κ°μ Beanμ΄ λ±λ‘λμμ λ, ν΄κ²°ν  μ μλ 4κ°μ§ λ°©λ²μ΄ μλ€.

- @Autowired νλ λͺ λ§€μΉ­ 
- @Qualifier -> @QualifierλΌλ¦¬ λ§€μΉ­ ->  λΉ μ΄λ¦ λ§€μΉ­ 
- @Primary μ¬μ© 
- @Annotation μ§μ  λ§λ€μ΄ μ¬μ©νκΈ°

### 2-1. @Autowired - νλλͺ λ§€μΉ­

`@Autowired`λ₯Ό μ΄μ©νμ¬ λ³λμ νλλͺμ λ§€μΉ­νλ λ°©λ²μ΄λ€. νλλͺ λ§€μΉ­μ νμ λ§€μΉ­μ μ°μ  μ€ννλ€. νμμ΄ λμΌν ν΄λμ€κ° λ¨ νλμΌ κ²½μ° νλλͺ λ§€μΉ­μ λμνμ§ μλλ€. νμμ΄ μ€λ³΅λλ κ²½μ°μλ§ 2μ°¨ μ‘°κ±΄μΌλ‘ νλλͺ λ§€μΉ­μ μ¬μ©νλ€.

```java
// κΈ°μ‘΄ μ½λ
@Autowired
private DiscountPolicy discountPolicy

// νλλͺμ Bean μ΄λ¦μΌλ‘ λ³κ²½ 
@Autowired
private DiscountPolicy rateDiscountPolicy
```


### 2-2. @Qualifier - Bean μ΄λ¦ λ§€μΉ­

`@Qualifier`λ μΆκ° κ΅¬λΆμλ₯Ό μ¬μ©νλ λ°©λ²μ΄λ€. νμ μ€λ³΅μΌλ‘ μΈν΄ ν΄λμ€ μ νμ΄ λΆκ°ν  κ²½μ°, μΆκ° κ΅¬λΆμκ° λμΌν ν΄λμ€λ₯Ό μ°Ύμ μ£Όμνλ€. μΆκ°μ μΈ νμμ μ€μ νλ κ²μ΄μ§, μ€μ  λΉ μ΄λ¦μ λ³κ²½νλ κ²μ μλλ€. μ¬μ© λ°©λ²μ Bean λ±λ‘ μ, `@Qualifier`λ₯Ό μΆκ°ν΄μ£Όλ©΄ λλ€.



```java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
```

```java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
	this.memberRepository = memberRepository;
	this.discountPolicy = discountPolicy;
}
```

μλμ κ°μ΄ μλμΌλ‘ `@Bean` λ±λ‘μμλ `@Qualifier`λ₯Ό μ¬μ©ν  μ μλ€.
```java
@Bean
@Qualifier("mainDiscountPolicy")
public DiscountPolicy discountPolicy() {
	return new ...
}
```

`@Qualifier`λ λͺ¨λ  μ½λμ `@Qualifier`λ₯Ό λΆμ¬μΌ νλ€λ λ¨μ μ΄ μλ€.

### 2-3. @Primary - μ°μ μμ λΆμ¬

`@Primary`λ λ¨μν μ°μ μμλ₯Ό λΆμ¬νλ λ°©λ²μ΄λ€. `@Autowired`λ₯Ό ν΅ν μμ‘΄ κ΄κ³ μ£Όμ κ³Όμ μμ μ¬λ¬κ°μ Beanμ΄ μ‘°νλλ©΄ `@Primary` λΆμ΄μλ ν΄λμ€κ° μ£Όμ μ°μ κΆμ κ°μ§λ€. 

μλμ κ°μ΄ `RateDiscountPolicy`μ `@Primary`κ° μκ³ , `FixDiscountPolicy`λ `@Primary`κ° μλ κ²½μ°, `DiscountPolicy` νμμ κ°μ§ ν΄λμ€ μ€μμλ`RateDiscountPolicy`κ° μ°μ κΆμ κ°μ§λ€.

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}

```
> μ°Έκ³ 
`@Primary`λ λ²μ©μ μ΄λ©°, λν΄νΈκ°μ μ±κ²©μ κ°λλ€. λ°λ©΄, `@Qualifier`λ μΈλΆ μ΅μμ μ±κ²©μ κ°λλ€. `@Primary`μ `@Qualifier`κ° λ λ€ μ‘΄μ¬ν  κ²½μ° `@Qualifier`μ μ°μ κΆμ΄ μλ€. **μ€νλ§μ μλλ³΄λ€λ μλμ΄, λμ λ²μμ μ νκΆ λ³΄λ€λ μ’μ λ²μμ μ νκΆμ΄ μ°μ  μμκ° λλ€.** λ°λΌμ, `@Qualifier`μ μ°μ κΆμ΄ λλ€.


### 2-4. @Annotationμ μ§μ  λ§λ€μ΄ μ¬μ©

μ§μ  `@Annotation`μ λ§λ€μ΄ μ€λ³΅ νμ λ¬Έμ λ₯Ό ν΄κ²°ν  μλ μλ€. νΉν, λλ²μ§Έ λ°©λ²μΌλ‘ μΈκΈνλ `@Qualifier("λ±λ‘λ Beanμ μ΄λ¦")` λ°©μμ μμν λ¨μ μ λ³΄μνλ€. `@Qualifier`μ μΆκ°νλ Beanμ μ΄λ¦μ λ¬Έμμ΄λ‘ μΈμλλ€. λ¬Έμμ΄μ μ»΄νμΌ λ¨κ³μμ μλ¬ νμΈμ΄ μ΄λ ΅λ€. νμ§λ§ `@Annotation`μ μ§μ  λ§λ€κ² λλ©΄, μ»΄νμΌ λ¨κ³μμ μ€νμλ‘ μΈν μλ¬λ₯Ό νμΈν  μ μλ€.

`@Annotation`μ λ§λλ λ°©λ²μ μλμ κ°μ΄ μλ‘μ΄ μ λΈνμ΄μμ μμ±νκ³ , νμν μ λΈνμ΄μμ μ μΈνλ€. μ λΈνμ΄μμ μμμ΄λΌλ κ°λμ΄ μλ€. μ¬λ¬ μ λΈνμ΄μμ λͺ¨μμ μ¬μ©νλ κΈ°λ₯μ μ€νλ§μ΄ μ§μνλ€.


```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, ElementType.ANNOTATION_TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
@Qualifier("mainDiscountPolicy")
public @interface MainDiscountPolicy {
}
```

μ¬μ© λ°©λ²μ λ€λ₯Έ μ λΈνμ΄μκ³Ό λμΌνλ€. μλμ κ°μ΄ ν΄λμ€ μ μΈ μ½λ μμ μ λΈνμ΄μμ μ μΈνμ¬ μ¬μ©ν  μ μλ€.

```java
@Component
@MainDiscountPolicy
public class RateDiscountPolicy implements DiscountPolicy {}
```

μλμ κ°μ΄ μμ‘΄ κ΄κ³ μλ μ£Όμ μμλ `@Qulifier`μ λμΌν λ°©λ²μΌλ‘ μ¬μ©ν  μ μλ€.

```java
//μμ±μ μλ μ£Όμ
@Autowired
public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
    this.memberRepository = memberRepository;
    this.discountPolicy = discountPolicy;
}

//μμ μ μλ μ£Όμ 
@Autowired 
public DiscountPolicy setDiscountPolicy(@MainDiscountPolicy DiscountPolicy discountPolicy) {
    return discountPolicy;
}
```

νκ°μ§ μ μν  μ μ λ¬΄λΆλ³ν μ¬μ©μ μ§μν΄μΌ νλ€λ μ μ΄λ€. μ λΈνμ΄μμ μ§μ  λ§λ€μ΄ μ¬μ©νλ©΄ νμ μ€λ³΅μ λ°©μ§ν  λΏλ§ μλλΌ, λλ§μ μ λΈνμ΄μμ λ§λ€μ΄ μ΄λ€λ μ μμ μ μ μ΄ μκΈ΄λ€. νμ§λ§ μ€νλ§μ΄ μ κ³΅νλ κΈ°λ₯μ λλ ·ν λͺ©μ  μμ΄ λ¬΄λΆλ³νκ² μ¬μ μνλ κ²μ μ¬λ¬ κ°λ°μκ° ν¨κ» μ μ§λ³΄μνλ κ³Όμ μ νΌλμ ν€μΈ μ μμλ―λ‘, νΉλ³ν μ΄μ κ° μλ€λ©΄ μ¬μ©μ μ§μνλ κ²μ΄ μ’λ€. 


---

## 3. μμ½
- μμ‘΄ κ΄κ³ μ£Όμ μ, νμμ μ€λ³΅ λ¬Έμ λ₯Ό ν΄κ²°νλ λ°©λ²μ 4κ°μ§κ° μλ€.
- μΌλ°μ μΈ μ°μ μμλ `@Primary`λ‘ λΆμ¬νκ³ , μΈλΆ μ€μ μ `@Qualifier`λ₯Ό μ΄μ©νλ©΄ μ’λ€.
- `@Primary`μ `@Qualifier`κ° λͺ¨λ μ¬μ©λ κ²½μ°, `@Qualifier`μ μ°μ κΆμ΄ μλ€.