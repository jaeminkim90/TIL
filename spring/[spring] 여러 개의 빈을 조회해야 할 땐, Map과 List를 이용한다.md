> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---


# μ¬λ¬ κ°μ λΉμ μ‘°ννλ λ°©λ²


νΉμ  νμμ ν΄λΉνλ μ€νλ§ Beanμ λͺ¨λ μ‘°νν΄μΌ νλ κ²½μ°κ° μλ€. μλ₯Ό λ€λ©΄, ν΄λΌμ΄μΈνΈκ° λ€μν ν μΈ λ°©μ(μ μ‘ ν μΈ, μ λ₯  ν μΈ) μ€ νλλ₯Ό μ§μ  μ νν΄μΌ νλ μν©μ΄λ€. μ΄λ, μ€νλ§μ μ¬μ©νλ©΄ Mapμ΄λ Listλ‘ λ³΅μμ Beanμ λͺ¨λ μ‘°ννμ¬ λ΄μ μ μλ€. 


```java
public class AllBeanTest {

    @Test
    void findAllBean() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class, DiscountService.class);

        DiscountService discountService = ac.getBean(DiscountService.class);
        Member member = new Member(1L, "userA", Grade.VIP);
        int fixDiscountPrice = discountService.discount(member, 10000, "fixDiscountPolicy");

        assertThat(discountService).isInstanceOf(DiscountService.class);
        assertThat(fixDiscountPrice).isEqualTo(1000);

        int rateDiscountPrice = discountService.discount(member, 20000, "rateDiscountPolicy");

        assertThat(discountService).isInstanceOf(DiscountService.class);
        assertThat(rateDiscountPrice).isEqualTo(2000);
    }

    static class DiscountService {
        private final Map<String, DiscountPolicy> policyMap;
        private final List<DiscountPolicy> policies;

        @Autowired // μμ±μκ° 1κ°μΌ κ²½μ°, @Autowired μλ΅ κ°λ₯
        public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
            this.policyMap = policyMap;
            this.policies = policies;

            System.out.println("policyMap = " + policyMap);
            System.out.println("policies = " + policies);
        }

        public int discount(Member member, int price, String discountCode) {
            DiscountPolicy discountPolicy = policyMap.get(discountCode);
            return discountPolicy.discount(member, price);
        }
    }
}
```

> μ°Έκ³ 
- μμ±μκ° 1κ°μΌ κ²½μ°, `@Autowired`λ₯Ό μλ΅ν  μ μλ€.

**λ‘μ§ λΆμ** 

- `DiscountService` λ MapμΌλ‘ λͺ¨λ  `DiscountPolicy`  λ₯Ό μ£Όμλ°λλ€. μ΄λ, `fixDiscountPolicy`, `rateDiscountPolicy`κ° μ£Όμλλ€.

- `discount()`  λ©μλλ `discountCode` λ‘ `fixDiscountPolicy`κ°  λμ΄μ€λ©΄ Mapμμ `fixDiscountPolicy`μ€νλ§ Beanμ μ°Ύμμ μ€ννλ€. λ¬Όλ‘ , `rateDiscountPolicy`κ° λμ΄μ€λ©΄ `rateDiscountPolicy`  μ€νλ§ Beanμ μ°Ύμμ μ€ννλ€. 

**μ£Όμ λΆμ** 
- `Map <String, DiscountPolicy>`: Mapμ keyμ μ€νλ§ Beanμ μ΄λ¦μ λ£μ΄μ£Όκ³ , Valueλ `DiscountPolicy`νμμΌλ‘ μ‘°νν μ€νλ§ Beanμ λ΄μμ€λ€. 

- `List <DiscountPolicy>` : `DiscountPolicy` νμμΌλ‘ μ‘°νν λͺ¨λ  μ€νλ§ Beanμ λ΄μμ€λ€. λ§μ½ ν΄λΉνλ νμμ μ€νλ§ Beanμ΄ μμΌλ©΄, λΉ μ»¬λ μμ΄λ Mapμ μ£Όμνλ€. 


> μ°Έκ³ 
μ€νλ§ μ»¨νμ΄λλ₯Ό μμ±νλ©΄μ μ€νλ§ Beanμ λ±λ‘ν  μ μλ€. 
μ€νλ§ μ»¨νμ΄λλ μμ±μμ ν΄λμ€ μ λ³΄λ₯Ό λ°λλ€. νλΌλ―Έν°μ λ€μ΄κ° ν΄λμ€λ μ€νλ§ BeanμΌλ‘ μλ λ±λ‘λλ€. 
```java
new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);
```
μ΄ μ½λλ μ»¨νμ΄λ μμ±κ³Ό λμμ `AutoAppConfig`, `DiscountService`λ₯Ό μ€νλ§ BeanμΌλ‘ μλ λ±λ‘νλ€. λ°λΌμ, μ½λμ λμμ 2κ°μ§λ‘ λλμ΄ μ΄ν΄ν  μ μλ€.
- `new AnnotationConfigApplicationContext()`λ₯Ό ν΅ν΄ μ€νλ§ μ»¨νμ΄λλ₯Ό μμ±νλ€. 
- νλΌλ―Έν°λ‘ λ€μ΄κ° `AutoAppConfig.class`, `DiscountService.class`λ μλμΌλ‘ μ€νλ§ Beanμ λ±λ‘λλ€. 