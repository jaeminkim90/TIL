> ### ๐ โ๏ธ 
> 1. **TIL ์๋ฆฌ์ฆ์ ์์ฑ๋ ๊ธ์ '๋งค์ผ ๋งค์ผ ํ์ตํ ์ง์ ์กฐ๊ฐ์ ๋ฉ๋ชจํด ๋์ ํฌ์คํ'์๋๋ค. ๊ณต์ ๊ฐ ์๋ ๊ฐ์ธ์ ์ธ ํ์ต ๋ด์ฉ ๊ธฐ๋ก์ ๋ชฉ์ ์ผ๋ก ์์ฑ๋์์์ ์๋ ค๋๋ฆฝ๋๋ค.**
> 2. ๊ทธ ์ธ ์๋ฆฌ์ฆ์ ์์ฑ๋ **๊ณต์  ๋ชฉ์ ์ ํฌ์คํ**์ ์๊ฐ์ด ๋  ๋๋ง๋ค ๋ณ๋๋ก ์์ฑํ๊ณ  ์์ต๋๋ค. ์ฃผ๋ก, TIL ์๋ฆฌ์ฆ์ ์์ฑ๋ ๋ด์ฉ์์ **ํน์  ์ฃผ์ ๋ฅผ ์ ์ ํ๊ณ , ๋ ๊น์ด ๊ณต๋ถํ ํ ์ ๋ฆฌํ์ฌ ์์ฑํฉ๋๋ค.**

---

# ์คํ๋ง์ ์ด์ฉํ DI(์์กด ๊ด๊ณ ์ฃผ์) ๋ฐฉ๋ฒ
์๋ฐ ์ฝ๋๋ฅผ ์ด์ฉํ DI ๋ฐฉ์์์ AppConfig๋ ์ ํ๋ฆฌ์ผ์ด์์ ์์กด ๊ด๊ฒ ์ฃผ์๊ณผ ์ธ์คํด์ค ์์ฑ์ ๋ด๋นํ๋ค. ์คํ๋ง์ผ๋ก ์ ํํ๋ ์ฒซ๋ฒ์งธ ๋จ๊ณ๋ก AppConfig์ ์ค์ ์ ๊ตฌ์ฑํ๋ค๋ ๋ป์ @Configuration์ ์ถ๊ฐํ๊ณ , ๊ฐ ๋ฉ์๋์ @Bean์ ๋ถ์ฌ์ค๋ค. ์ด๋ ๊ฒ ํ๋ฉด ์คํ๋ง ์ปจํ์ด๋๊ฐ AppConfig์ ๋ชจ๋  ๋ฉ์๋๋ฅผ ์คํํ๊ณ  ๋ฐํ๋๋ ์ธ์คํด์ค๋ฅผ ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋กํ๋ค. 

```java
package hello.core;

import hello.core.discount.DiscountPolicy;
import hello.core.discount.FixDiscountPoliocy;
import hello.core.discount.RateDiscountPolicy;
import hello.core.member.MemberService;
import hello.core.member.MemberServiceImpl;
import hello.core.member.MemoryMemberRepository;
import hello.core.order.OrderService;
import hello.core.order.OrderServiceImpl;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemoryMemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        //return new FixDiscountPoliocy();
        return new RateDiscountPolicy(); // ๊ธฐํ์๊ฐ ๋ฐฐ์ฐ๋ฅผ ๋ณ๊ฒฝํ๋ฏ, ํ ์ธ ์ ์ฑ์ ๋ณ๊ฒฝํ๋ค.
    }
}

```

๋ค์์ผ๋ก ๊ธฐ์กด์ AppConfig ๊ฐ์ฒด๋ฅผ ์ง์  ์์ฑํ์ฌ ์ฌ์ฉํ๋ MemberApp ํด๋์ค๋ฅผ ์๋์ ๊ฐ์ด ๋ณ๊ฒฝํ๋ค.

```java
public class MemberApp {

    public static void main(String[] args) {
        // AppConfig appConfig = new AppConfig();
        // MemberService memberService = appConfig.memberService();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
    }
}

```

- ์คํ๋ง์ ApplicationContext ๊ฐ์ฒด๊ฐ ๋ชจ๋  ์คํ๋ง ๋น์ ๊ด๋ฆฌํ๋ค. ์คํ๋ง์ ์ด์ฉํด ์ ํ๋ฆฌ์บ์์์ ์คํํ  ๋๋ ApplicationContext ๊ฐ์ฒด๋ฅผ ์ฌ์ฉํ๋ค.
- ๊ตฌํ์ฒด์ธ AnnotationConfigApplicationContext ๊ฐ์ฒด์ ํ๋ผ๋ฏธํฐ๋ก AppConfig๋ฅผ ๋ฃ์ด์ฃผ๋ฉด @Bean์ผ๋ก ๋ฑ๋ก๋ ๋ชจ๋  ์์ฑ ๊ฐ์ฒด๋ฅผ ๋ฑ๋กํ๊ณ  ๊ด๋ฆฌํด์ค๋ค.
- ๋ฐ๋ผ์, ๋ฉ์๋ ์คํ๋ AppConfig ๊ฐ์ฒด๋ฅผ ์ด์ฉํ์ง ์๋๋ค. ApplicationContext ๊ฐ์ฒด๋ฅผ ์ด์ฉํ์ฌ ๊ฐ์ฒด๋ฅผ ์์ฑํ๋ค.
- ๊ฐ์ฒด๋ฅผ ์์ฑํ  ๋ ํ๋ผ๋ฏธํฐ๋ 2๊ฐ๊ฐ ํ์ํ๋ค. ์ฒซ๋ฒ์งธ ํ๋ผ๋ฏธํฐ๋ ํธ์ถํ  ๋ฉ์๋์ ์ด๋ฆ์ด๋ค. ๋๋ฒ์งธ ํ๋ผ๋ฏธํฐ๋ ํ์์ด๋ค. ํ์์ด ์ผ์นํด์ผ ๋ฐํ๋๋ ๊ฐ์ฒด๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.


๊ทธ๋ฆฌ๊ณ  ์คํ์ ํด๋ณด๋ฉด ์๋์ ๊ฐ์ ๋ฉ์์ง๋ฅผ ํ์ธํ  ์ ์๋ค.


![](https://images.velog.io/images/woply/post/c36c9796-9a4d-4a22-975b-7b6b5ce6485e/image.png)


- ์คํ๋ง์ @Configuration์ผ๋ก ๋ฑ๋กํ AppConfig๋ฅผ ๋น๋กฏํ์ฌ @Bean์ผ๋ก ๋ฑ๋กํ ๋ฉ์๋๋ฅผ ์๋์ผ๋ก ๋ฑ๋กํ๋ค. ๋ฉ์๋ ์ด๋ฆ์ key ๊ฐ, ๋ฉ์๋์ ๋ฐํ ๊ฐ์ฒด๋ฅผ value๋ก ๋ด์ ์คํ๋ง ์ปจํ์ด๋์ ๋ฑ๋กํ๋ค. 
- @Bean์ผ๋ก ๋ฑ๋กํ ๋ฉ์๋์ ๋ฐํ๋๋ ์ธ์คํด์ค๋ฅผ ์ฌ์ฉํ๊ณ  ์ถ์ ๋๋ ApplicationContext ๊ฐ์ฒด์ getBean()์ ์ด์ฉํ๋ค.

์ถ๊ฐ๋ก OrderApp ํด๋์ค๋ ์คํ๋ง์ ์ด์ฉํ๋ ๋ฐฉ์์ผ๋ก ๋ณ๊ฒฝํ  ์ ์๋ค. ์๋์ ๊ฐ์ด ์ฝ๋๋ฅผ ๋ณ๊ฒฝํ๋ฉด ์คํ๋ง ์ปจํ์ด๋๊ฐ ๊ด๋ฆฌํ๋ AppConfig์ ๋ชจ๋  ์ธ์คํด์ค๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.

```java 
public class OrderApp {

    public static void main(String[] args) {
        // AppConfig appConfig = new AppConfig();
        // MemberService memberService = appConfig.memberService();
        // OrderService orderService = appConfig.orderService();

        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);
    }
}
```


---

## ์์ํ ์๋ฐ ์ฝ๋๋ก DI-> ์คํ๋ง ์ปจํ์ด๋๋ฅผ ์ด์ฉํ DI

- ApplicationContext๋ฅผ ์คํ๋ง ์ปจํ์ด๋๋ผ๊ณ  ํ๋ค. ๊ธฐ์กด์๋ ๊ฐ๋ฐ์๊ฐ AppConfig ๋ฅผ ์ฌ์ฉํด์ ์ง์  ๊ฐ์ฒด๋ฅผ ์์ฑํ๊ณ  DI๋ฅผ ํ์ง๋ง, ์ด์ ๋ถํฐ๋ ์คํ๋ง ์ปจํ์ด๋๋ฅผ ํตํด์ DI๋ฅผ ํ  ์ ์๋ค. 
- ์คํ๋ง ์ปจํ์ด๋๋ @Configuration์ด ๋ถ์ AppConfig ๋ฅผ ์ค์ (๊ตฌ์ฑ) ์ ๋ณด๋ก ์ฌ์ฉํ๋ค. ์ฌ๊ธฐ์ @Bean์ด๋ผ ์ ํ ๋ฉ์๋๋ฅผ ๋ชจ๋ ํธ์ถํด์ ๋ฐํ๋ ๊ฐ์ฒด๋ฅผ ์คํ๋ง ์ปจํ์ด๋์ ๋ฑ๋กํ๋ค. ์ด๋ ๊ฒ ์คํ๋ง ์ปจํ์ด๋์ ๋ฑ๋ก๋ ๊ฐ์ฒด๋ฅผ ์คํ๋ง ๋น์ด๋ผ ํ๋ค. 
- ์คํ๋ง ๋น์ @Bean์ด ๋ถ์ ๋ฉ์๋์ ์ด๋ฆ์ ์คํ๋ง ๋น์ ์ด๋ฆ์ผ๋ก ์ฌ์ฉํ๋ค. (ex. memberService, orderService)
- ์คํ๋ง ์ปจํ์ด๋๋ฅผ ์ด์ฉํ DI ๋ฐฉ์์ ์ด์ฉํ๋ฉด ๊ฐ๋ฐ์๊ฐ AppConfig ๊ฐ์ฒด๋ฅผ ์ง์  ์ด์ฉํ์ง ์๋๋ค. ์คํ๋ง ์ปจํ์ด๋๋ฅผ ํตํด์ ํ์ํ ์คํ๋ง ๋น(๊ฐ์ฒด)๋ฅผ ์ฐพ์ ์ ์๋ค.
- ์คํ๋ง ์ปจํ์ด๋์ ๋ฑ๋ก๋ ์คํ๋ง ๋น์ applicationContext.getBean() ๋ฉ์๋๋ฅผ ์ฌ์ฉํด์ ์ฐพ์ ์ ์๋ค. 