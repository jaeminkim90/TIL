> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---

# μ€νλ§ μ»¨νμ΄λμ λ±λ‘λ Bean μ‘°ννκΈ°

μ€νλ§ μ»¨νμ΄λμ λ±λ‘λ Beanμ μ‘°ννλ λ€μν λ°©λ²μ΄ μλ€.

## 1. λ±λ‘λ λͺ¨λ  Bean μ‘°ν

``` java
package hello.core.beanfind;

import hello.core.AppConfig;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.config.BeanDefinition;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

class ApplicationContextInfoTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("λͺ¨λ  λΉ μΆλ ₯νκΈ°")
    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames(); // μ€νλ§μ λ±λ‘λ λͺ¨λ  λΉ μ΄λ¦ μ‘°ν
        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);// getBean(): Bean μ΄λ¦μΌλ‘ Bean κ°μ²΄(μΈμ€ν΄μ€)λ₯Ό μ‘°ν
            System.out.println("name = " + beanDefinitionName + " object: " + bean);
        }
    }

    @Test
    @DisplayName("μ νλ¦¬μΌμ΄μ λΉ μΆλ ₯νκΈ°")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            // getBeanDefinition(): Bean μ΄λ¦μΌλ‘ Beanμ λ©νλ°μ΄ν° μ‘°ν
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            // ROLE.APPLICATION: κ°λ°μκ° μ νλ¦¬μΌμ΄μ κ°λ°μ μν΄ λ±λ‘ν bean λλ μΈλΆ λΌμ΄λΈλ¬λ¦¬
            // ROLE.INFRASTRUCTURE: μ€νλ§ λ΄λΆμμ μ¬μ©νλ bean
            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                Object bean = ac.getBean(beanDefinitionName); // getBean(): Bean μ΄λ¦μΌλ‘ Bean κ°μ²΄(μΈμ€ν΄μ€)λ₯Ό μ‘°ν
                System.out.println("name = " + beanDefinitionName + " object: " + bean);
            }
        }
    }
}

```


### 1-1. λ±λ‘λ λͺ¨λ  bean μ‘°ννκΈ°
`getBeanDefinitionNames()`λ μ€νλ§μ λ±λ‘λ λͺ¨λ  Beanμ μ΄λ¦μ String λ°°μ΄λ‘ λ°ννλ€. λ°μμ¨ Beanμ μ΄λ¦μ getBean()μ μ΄μ©ν΄ λ€μ κ°μ²΄λ‘ λ°μμ¬ μ μλ€. λ¨, λ°νλλ κ°μ²΄λ Object νμμ΄λ€.

> `ac.getBeanDefinitionNames()` : μ€νλ§μ λ±λ‘λ λͺ¨λ  bean μ΄λ¦μ μ‘°ν
> `ac.getBean("μ΄λ¦")` : Bean μ΄λ¦μΌλ‘ Bean κ°μ²΄(μΈμ€ν΄μ€)λ₯Ό μ‘°ν


### 1-2. κ°λ°μκ° μ§μ  λ±λ‘ν bean μ‘°ννκΈ°

`getBeanDefinition()`μ μ¬μ©νλ©΄ Bean μ΄λ¦μΌλ‘ Beanμ λ©νλ°μ΄ν°λ₯Ό λ°μ μ μλ€. λ©νλ°μ΄ν°μλ `ROLE`μ΄λΌλ μ λ³΄κ° μ‘΄μ¬νλ€. `ROLE_APPLICATION`μ μ¬μ©μκ° μμ±νκ³  λ±λ‘ν Beanμ΄κ³ , `ROLE_INFRASTRUCTURE`μ μ€νλ§ λ΄λΆμμ μμ±λκ³  λ±λ‘λ Beanμ΄λ€.

`ROLE_APPLICATION`μ μ‘°κ±΄μΌλ‘ μ§μ  λ±λ‘ν Beanλ§ μ‘°νν  μ μλ€.


> `getBeanDefinition()` : Bean μ΄λ¦μΌλ‘ Beanμ λ©νλ°μ΄ν° μ‘°ν
> `ROLE_APPLICATION`: μΌλ°μ μΌλ‘ μ¬μ©μκ° μ μν bean. κ°λ°μκ° μ νλ¦¬μΌμ΄μ κ°λ°μ μν΄ λ±λ‘ν bean λλ μΈλΆ λΌμ΄λΈλ¬λ¦¬.
> `ROLE_INFRASTRUCTURE` : μ€νλ§μ΄ λ΄λΆμμ μ¬μ©νλ bean.


---

## 2. Beanμ μ΄λ¦κ³Ό νμμΌλ‘ μ‘°ν

### 2-1. μ΄λ¦κ³Ό νμμ λͺ¨λ μ΄μ©νμ¬ μ‘°ν

Beanμ μ΄λ¦μΌλ‘ μ‘°ννλ λ°©λ²μ μ€νλ§ μ»¨νμ΄λμμ μ€νλ§μ΄ κ΄λ¦¬νκ³  μλ Beanμ μ°Ύλ κ°μ₯ κΈ°λ³Έμ μΈ λ°©λ²μ΄λ€. `getBean()`μ μ΄μ©νλ©΄ μ΄λ¦κ³Ό νμμΌλ‘ κ°μ²΄λ₯Ό μ°Ύμ μ μλ€. νμλ§μΌλ‘λ μ‘°νν  μ μλ€. λ¨, κ°μ νμμ κ°μ§ κ°μ²΄κ° νλλ§ μ‘΄μ¬ν΄μΌ νλ€. `getBean()`μ λ¨μΌ κ°μ²΄λ₯Ό λ°ννκΈ° λλ¬Έμ΄λ€. λ§μ½, μ‘°ννλ μ΄λ¦μ κ°μ§ κ°μ²΄κ° μμ κ²½μ° `NoSuchBeanDefinitionException`μ΄ λ°μνλ€.  

```java
public class ApplicationContextBasicFindTest {
    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("λΉ μ΄λ¦μΌλ‘ μ‘°ν")
    void findBeanByName() {
        MemberService memberService = ac.getBean("memberService", MemberService.class);

        System.out.println("memberService = " + memberService);
        System.out.println("memberService.getClass() = " + memberService.getClass());

        assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
    }
}

```

> `getBean(λΉμ΄λ¦, νμ)`: μ§μ λ νμμ κ°μ²΄λ₯Ό λ°ννλ€. νλΌλ―Έν°μ bean μ΄λ¦λ§ λ£μ κ²½μ° object κ°μ²΄λ₯Ό λ°ννλ€.
> μ‘°ν λμ μ€νλ§ Beanμ΄ μμ κ²½μ°: `NoSuchBeanDefinitionException` μμΈ λ°μ



### 2-2. νμλ§ μ΄μ©νμ¬ μ‘°ν

νμμ μ΄μ©νμ¬ μ‘°νν  κ²½μ°, λ°λμ μ€νλ§ μ»¨νμ΄λμ λ±λ‘λ λ©μλμ λ°ν νμκ³Ό μΌμΉν  νμλ μλ€. μ€μ  λ©μλμ return κ°μ²΄μ ν΄λΉνλ μΈμ€ν΄μ€ νμμΌλ‘λ μ‘°νκ° κ°λ₯νλ€.

```java
@Test
@DisplayName("μ΄λ¦ μμ΄ νμμΌλ‘λ§ μ‘°ν")
void findBeanByType() {
    MemberService memberService = ac.getBean(MemberService.class);

    System.out.println("memberService = " + memberService);
    System.out.println("memberService.getClass() = " + memberService.getClass());

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
}
```

μλμ κ°μ΄ κ΅¬μ²΄μ μΈ `getBean()` νμμ λ©μλμ λ£μ΄ μ‘°νν  μ μμ§λ§, κΆμ₯νλ λ°©μμ μλλ€. μ­ν κ³Ό κ΅¬νμ κ΅¬λΆνκ³ , ν΄λΌμ΄μΈνΈ κ°μ²΄λ μ­ν μ μμ‘΄νλ κ²μ΄ μ’λ€. μ΄λ νμ€νΈ μ½λμμλ μ ν¨νλ€. κ΅¬νμ²΄ νμμ μμ‘΄νκ² λλ©΄ νμ₯μ μ μ°νμ§ λͺ»νλ©°, μ μ§λ³΄μ λΉμ©μ΄ μ¦κ°νλ€.

```java
@Test
@DisplayName("κ΅¬μ²΄ νμμΌλ‘ λΉ μ΄λ¦μΌλ‘ μ‘°ν")
void findBeanByName2() {
    MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);

    System.out.println("memberService = " + memberService);
    System.out.println("memberService.getClass() = " + memberService.getClass());

    assertThat(memberService).isInstanceOf(MemberServiceImpl.class);
}
```


---

## 3. νμμ΄ λμΌν κ°μ²΄κ° μμ λ μ‘°ν λ°©λ²

νμμ΄ λμΌν Beanμ μ‘°ννλ©΄ μλμ κ°μ΄ `NoUniqueBeanDefinitionException` μμΈκ° λ°μνλ€. 

![](https://images.velog.io/images/woply/post/84d81359-82b8-44c7-92ba-96d58b4539f9/image.png)


νμμ΄ λμΌν Beanμ μ‘°νν  λ κΈ°λ³Έμ μΌλ‘λ Beanμ μ΄λ¦μ ν΅ν΄ κ΅¬λΆν  μ μλ€. μλ° μ½λ λ°©μμ ν΅ν΄ μ€νλ§ Beanμ λ±λ‘νλ€λ©΄, λ©μλ μ΄λ¦μ΄ Beanμ μ΄λ¦μ΄ λλ€. Beanμ μ΄λ¦μ `@Bean(name ="μ΄λ¦")`μ ν΅ν΄ λ³λλ‘ μ§μ ν  μ μλ€.


```java
@Test
@DisplayName("νμμΌλ‘ μ‘°ν μ κ°μ νμμ΄ λ μ΄μ μμΌλ©΄, λΉ μ΄λ¦μ μ§μ νλ©΄ λλ€.")
void findBeanByName() {
    MemberRepository memberRepository = ac.getBean("memberRepository1",MemberRepository.class);
    assertThat(memberRepository).isInstanceOf(MemberRepository.class);
}

@Configuration
// class λ΄μ classλ₯Ό λ§λλ κ²½μ°λ scopeμ μμ classλ‘ νμ νλ€λ κ²μ μλ―Ένλ€.
static class SameBeanConfig {

    @Bean
    // MemoryMemberRepositoryλ₯Ό λ°ννλ λ©μλ
    public MemberRepository memberRepository1() {
        return new MemoryMemberRepository();
    }

    @Bean
    // λκ°μ λ°ν νμ(MemoryMemberRepository)μ κ°μ§ λ©μλλ₯Ό νλ λ λ§λ λ€.
    public MemberRepository memberRepository2() {
        return new MemoryMemberRepository();
    }
}
```

λμΌν νμμ κ°μ§, 2κ° μ΄μμ Bean κ°μ²΄λ₯Ό μ‘°ννκ³  μΆλ€λ©΄, `getBeansOfType()`μ μ¬μ©ν  μ μλ€. `getBeansOfType()`λ Mapμ μ΄μ©νμ¬ ν΄λΉ Bean κ°μ²΄λ₯Ό λ°ννλ€. Mapμ keyλ Beanμ μ΄λ¦μ μ¬μ©νκ³ , valueλ κ°μ²΄μ μ°Έμ‘°(μ£Όμ)κ° λ€μ΄ μλ€.


```java
@Test
@DisplayName("νΉμ  λ°ν νμ Bean λͺ¨λ μ‘°ννκΈ°")
void findAllBeanByType() {
    Map<String, MemberRepository> beansOfType = ac.getBeansOfType(MemberRepository.class);
    for (String key : beansOfType.keySet()) { // keySet(): mapμ λͺ¨λ  ν€ κ°μ κ°μ Έμ¨λ€
        System.out.println("key = " + key + "/ value = " + beansOfType.get(key));
    }
    System.out.println("beansOfType = " + beansOfType);
    assertThat(beansOfType.size()).isEqualTo(2);
}
```

> `getBeansOfType()`: λμΌν λ°ν νμμ κ°μ§ Beanμ Map κ°μ²΄λ‘ λ°μ μ μλ€.


---

## 4. μμ κ΄κ³λ₯Ό μ΄μ©ν Bean μ‘°ν

Beanμ μ‘°νν  λ λΆλͺ¨ νμμ μ§μ νλ©΄ μμ νμμ ν΄λΉνλ Bean κ°μ²΄κ° ν¨κ» μ‘°νλλ€. λ°λΌμ, λͺ¨λ  μλ° κ°μ²΄μ μ΅κ³  λΆλͺ¨μΈ Object νμμΌλ‘ μ‘°ννλ©΄, λͺ¨λ  μ€νλ§ λΉμ μ‘°νν  μ μλ€. νμ§λ§, λ§μ°¬κ°μ§λ‘ λ°νλλ κ°μ²΄κ° 2κ° μ΄μμΌ κ²½μ° `getBeansOfType()`λ₯Ό μ¬μ©ν΄μΌ νλ€. `getBean()`μ μ΄μ©ν  κ²½μ° `NoUniqueBeanDefinitionException`μ΄ λ°μνλ€.

![](https://images.velog.io/images/woply/post/f184e649-c9d7-4b51-9c63-c11cfe0799dc/image.png)

μ€λ³΅λ νμμ κ°μ§ Beanμ μ‘°ννλ κ²κ³Ό λ§μ°¬κ°μ§λ‘ μμ κ°μ²΄κ° λ μ΄μμΌ κ²½μ° Bean μ΄λ¦μ μ¬μ©νμ¬ κ΅¬λΆν  μ μλ€. λ³΅μμ κ°μ²΄κ° νμν κ²½μ° `getBean()`μ΄ μλ `getBeansOfType()`μ μ¬μ©ν΄μΌ νλ€.

### 4-1. μ΄λ¦μΌλ‘ κ°μ²΄λ₯Ό κ΅¬λΆνμ¬ μ‘°ν

```java
@Test
@DisplayName("λΆλͺ¨ νμμΌλ‘ μ‘°ν μ, μμμ΄ λ μ΄μ μμΌλ©΄ Bean μ΄λ¦μ μ§μ νλ€")
void findBeanByParentTypeBeanName() {
    DiscountPolicy rateDiscountPolicy = ac.getBean("rateDiscountPolicy", DiscountPolicy.class);
    // ac.getBean()μ λ°ν νμμ λΆλͺ¨μ΄μ§λ§, νΉμ  μ΄λ¦μ μ¬μ©νμ¬ RateDiscountPolicyλ₯Ό λ°μ μ μλ€.
    assertThat(rateDiscountPolicy).isInstanceOf(RateDiscountPolicy.class); // μΈμ€ν΄μ€ νμ μ¦λͺ
}
```

> `ac.getBean("rateDiscountPolicy", DiscountPolicy.class)`: rateDiscountPolicyκ° μ΄λ¦, DiscountPolicy.classκ° λ°ν νμμ΄ λλ€.
> `isInstanceOf()`: μΈμ€ν΄μ€μ νμμ κ²μ¦ν  μ μλ€.


### 4-1. κ°μ λΆλͺ¨ νμμ κ°μ§ Bean κ°μ²΄λ₯Ό MapμΌλ‘ μ‘°ν

``` java
@Test
@DisplayName("λΆλͺ¨ νμμΌλ‘ λͺ¨λ μ‘°ννκΈ°")
void findAllBeanByParentType() {
    Map<String, DiscountPolicy> beansOfType = ac.getBeansOfType(DiscountPolicy.class);
    assertThat(beansOfType.size()).isEqualTo(2);
    for (String key : beansOfType.keySet()) {
        System.out.println("key = " + key + " / value = " + beansOfType.get(key));
    }
}
```

Objectλ λͺ¨λ  κ°μ²΄μ λΆλͺ¨ νμμ΄λ―λ‘, νμ μ‘°νμ μ¬μ©ν  κ²½μ° Beanμ λ±λ‘λ λͺ¨λ  κ°μ²΄λ₯Ό MapμΌλ‘ λ°μ μ μλ€.

```java
@Test
@DisplayName(" Object νμμΌλ‘ λͺ¨λ μ‘°ννκΈ°")
void findAllBeansByObjectType() {
    Map<String, Object> beansOfType = ac.getBeansOfType(Object.class);
    // Objectλ λͺ¨λ  μλ° κ°μ²΄μ λΆλͺ¨ ν΄λμ€μ΄λ―λ‘, μ€νλ§ λ΄λΆμμ μ¬μ©νλ BeanκΉμ§ λͺ¨λ λ΄κΈ΄λ€.

    for (String key : beansOfType.keySet()) {
        System.out.println("key = " + key + " / value = " + beansOfType.get(key));
    }
}
```