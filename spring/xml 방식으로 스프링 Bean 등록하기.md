> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---

# xml λ°©μμΌλ‘ μ€νλ§ Bean λ±λ‘
![](https://images.velog.io/images/woply/post/5b8dadb5-2a78-46be-b69c-c6e439be3423/image.png)

μ€νλ§ μ»¨νμ΄λλ λ€μν νμμΌλ‘ μ€νλ§ Beanμ λ±λ‘ν  μ μλλ‘ μ€κ³ λμ΄ μλ€. Bean λ±λ‘μ μ£Όλ‘ **1) μ λΈνμ΄μ κΈ°λ°μ μλ° μ½λλ₯Ό μ΄μ©ν λ°©μ**κ³Ό **2) xml νμΌμ μ΄μ©ν λ°©μ**μ λ§μ΄ μ¬μ©νλ€. νμν  κ²½μ° **3) μ§μ  μ€μ  νμΌμ λ§λλ λ°©μ**λ κ°λ₯νλ€. μ΅κ·Όμλ xml λ°©μμ κ±°μ μ¬μ©νμ§ μμ§λ§, λ κ±°μ νλ‘μ νΈλ₯Ό μ μ§λ³΄μν΄μΌ ν  κ²½μ° μμ£Ό λ§λ  μ μλ€.

---


## 1. μ λΈνμ΄μ κΈ°λ° μλ° μ½λ μ€μ  λ°©μ
μ λΈνμ΄μμ μ΄μ©ν μλ° μ½λ μ€μ  λ°©μμ νμ¬ κ°μ₯ λ§μ΄ μ¬μ©νλ λ°©μμ΄λ€. `new AnnotationConfigApplicationContext("μ€μ ν  ν΄λμ€ μ΄λ¦".class)`λ₯Ό μ΄μ©νμ¬ μ€μ ν  ν΄λμ€λ₯Ό νλΌλ―Έν°μ λ£μ΄μ£Όλ©΄ λλ€, 

## 2. xml μ€μ  λ°©μ
μ΅κ·Όμλ μ€νλ§ λΆνΈλ₯Ό μ΄μ©ν μ νλ¦¬μΌμ΄μ κ°λ°μ΄ μ£Όλ₯λ₯Ό μ΄λ£¨λ©΄μ xml κΈ°λ°μ μ€μ  λ°©μμ μ μ¬μ©νμ§ μλλ€. νμ§λ§ μ¬μ ν λ§μ λ κ±°μ νλ‘μ νΈκ° xmlλ‘ λμ΄ μμΌλ―λ‘, xmlμ μ΄μ©ν Bean λ±λ‘ λ°©μμ μκ³  μλ κ²μ΄ μ’λ€.

λν, xml λ°©μμ μ»΄νμΌ μμ΄ Bean μ€μ  μ λ³΄λ₯Ό λ³κ²½ν  μ μλ€λ μ₯μ μ΄ μμΌλ―λ‘, νμν  κ²½μ° μ¬μ©ν  μ μμ΄μΌ νλ€.

μ λΈνμ΄μ κΈ°λ°μ μλ° μ½λ μ€μ  λ°©μμμλ `ApplicationContext`μ κ΅¬νμ²΄λ‘ `AnnotationConfigApplicationContext`λ₯Ό μ¬μ©νμ§λ§, xml λ°©μμμλ `GenericXmlApplicationContext` κ΅¬νμ²΄λ₯Ό μ¬μ©νλ€.


### 2-1. Xmlμ μ΄μ©ν Bean λ±λ‘

`ApplicationContext`μ κ΅¬νμ²΄λ‘ `GenericXmlApplicationContext`λ₯Ό μ¬μ©νλ€.

```java
public class XmlAppContext {
    
    @Test
    void xmlAppContext() {
        ApplicationContext ac = new GenericXmlApplicationContext("AppConfig.xml");
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
    }
}
```


XML νμΌμ resources λλ ν λ¦¬μ μμ±ν΄μΌ νλ€.
![](https://images.velog.io/images/woply/post/d3eed80f-d49b-471b-a55d-f944c69e5c37/image.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="memberService" class="hello.core.member.MemberServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
    </bean>

    <bean id="memberRepository" class="hello.core.member.MemoryMemberRepository"/>

    <bean id="orderService" class="hello.core.order.OrderServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
        <constructor-arg name="discountPolicy" ref="discountPolicy"/>
    </bean>
    
    <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy"/>

</beans>
```

> `xml` κΈ°λ°μ `appConfig.xml` μ€νλ§ μ€μ  νμΌκ³Ό μλ° μ½λλ‘ λ `AppConfig.java` μ€μ  ν΄λμ€λ₯Ό λΉκ΅ν΄λ³΄λ©΄ κ΅¬μ‘°κ° κ±°μ λΉμ·νλ€. νμλ§ μ‘°κΈ λ€λ₯Ό λΏμ΄λ€.