
> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---


# μ»΄ν¬λνΈ μ€μΊ μ, Bean μ΄λ¦μ μ€λ³΅κ³Ό μΆ©λ

μ»΄ν¬λνΈ μ€μΊμμ κ°μ Bean μ΄λ¦μ λ±λ‘νλ©΄ μ΄λ»κ² λ κΉ? μλ λ κ°μ§ μν©μ λ€λ₯Έ κ²°κ³Όκ° λνλλ€.

- 1. μλλΉλ±λ‘vsμλλΉλ±λ‘
- 2. μλλΉλ±λ‘vsμλλΉλ±λ‘ 

## μλ Bean λ±λ‘ vs μλ Bean λ±λ‘
μ»΄ν¬λνΈ μ€μΊμ μν΄ μλμΌλ‘ μ€νλ§ λΉμ΄ λ±λ‘λλλ°, κ·Έ μ΄λ¦μ΄ κ°μ κ²½μ° μ€νλ§μ μ€λ₯λ₯Ό λ°μμν¨λ€. μ€λ₯ λ©μμ§λ μλμ κ°λ€.

```java\
ConflictingBeanDefinitionException μμΈ λ°μ!
```

μλ λΉ λ±λ‘μ κ²½μ°, μ€λ³΅λ μ΄λ¦μ κ°μ§ Bean μ€ μ΄λ νλλ₯Ό μμλ‘ μ ννμ¬ λ±λ‘νμ§ μλλ€. μλ¬λ₯Ό λ°μμν€κ³ , μ΄λ¦μ μ€λ³΅μ ν΄κ²°λμ΄μΌ μ μμ μΌλ‘ Bean λ±λ‘μ΄ μ΄λ£¨μ΄μ§λ€.


## μλ Bean λ±λ‘ vs μλ Bean λ±λ‘ 
μλ Bean λ±λ‘κ³Ό μλ Bean λ±λ‘ μ€ Bean μ΄λ¦μ΄ μ€λ³΅λμ΄ μΆ©λνλ€λ©΄, μλ Bean λ±λ‘μ μ°μ κΆμ΄ μλ€. μλ Beanμ΄ μλ Beanμ μ€λ²λΌμ΄λ©νλ λ°©μμΌλ‘ λ±λ‘λλ€.

μλ νμ€νΈλ₯Ό ν΅ν΄ μ΄ν΄λ³΄μ.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {}
```


```java
@Configuration
@ComponentScan(
        basePackages = "hello.core",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
    // μ€λ³΅ μ΄λ¦μ κ°μ§ Beanμ μλμΌλ‘ λ±λ‘
    @Bean(name = "memoryMemberRepository")
    MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```


νμ€νΈ μ½λλ₯Ό ν΅ν΄ μ€νλ§ μ»¨νμ΄λλ₯Ό μμ±νλ©΄, μ€λ³΅λ μ΄λ¦μ΄ μ‘΄μ¬νμ§λ§ νμ€νΈλ ν΅κ³Όνλ€.  κ·Έλ¦¬κ³  μλμ κ°μ λ‘κ·Έλ₯Ό νμΈν  μ μλ€. `memoryMemberRepository`μ μ΄λ¦μ΄ λμΌν Beanμ΄ μ‘΄μ¬νμ¬ μ€λ²λΌμ΄λλμλ€λ λ©μμ§λ€.

```
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing 
```

> μ°Έκ³ 
μ΅κ·Ό μ€νλ§ λΆνΈμμλ μλ Bean λ±λ‘κ³Ό μλ Bean λ±λ‘μ΄ μΆ©λλλ©΄ μ€λ₯κ° λ°μνλλ‘ κΈ°λ³Έ κ°μ λ°κΎΈμλ€.

> μλ Bean λ±λ‘κ³Ό μλ Bean λ±λ‘ μ€λ₯μ μ€νλ§ λΆνΈ μλ¬ 
```
Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true 
```