> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 스프링을 이용한 DI(의존 관계 주입) 방법
자바 코드를 이용한 DI 방식에서 AppConfig는 애플리케이션의 의존 관게 주입과 인스턴스 생성을 담당했다. 스프링으로 전환하는 첫번째 단계로 AppConfig에 설정을 구성한다는 뜻의 @Configuration을 추가하고, 각 메서드에 @Bean을 붙여준다. 이렇게 하면 스프링 컨테이너가 AppConfig의 모든 메서드를 실행하고 반환되는 인스턴스를 스프링 빈으로 등록한다. 

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
        return new RateDiscountPolicy(); // 기획자가 배우를 변경하듯, 할인 정책을 변경한다.
    }
}

```

다음으로 기존에 AppConfig 객체를 직접 생성하여 사용했던 MemberApp 클래스를 아래와 같이 변경한다.

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

- 스프링은 ApplicationContext 객체가 모든 스프링 빈을 관리한다. 스프링을 이용해 애플리캐에션을 실행할 때도 ApplicationContext 객체를 사용한다.
- 구현체인 AnnotationConfigApplicationContext 객체에 파라미터로 AppConfig를 넣어주면 @Bean으로 등록된 모든 생성 객체를 등록하고 관리해준다.
- 따라서, 메서드 실행도 AppConfig 객체를 이용하지 않는다. ApplicationContext 객체를 이용하여 객체를 생성한다.
- 객체를 생성할 때 파라미터는 2개가 필요하다. 첫번째 파라미터는 호출할 메서드의 이름이다. 두번째 파라미터는 타입이다. 타입이 일치해야 반환되는 객체를 사용할 수 있다.


그리고 실행을 해보면 아래와 같은 메시지를 확인할 수 있다.


![](https://images.velog.io/images/woply/post/c36c9796-9a4d-4a22-975b-7b6b5ce6485e/image.png)


- 스프링은 @Configuration으로 등록한 AppConfig를 비롯하여 @Bean으로 등록한 메서드를 자동으로 등록한다. 메서드 이름을 key 값, 메서드의 반환 객체를 value로 담아 스프링 컨테이너에 등록한다. 
- @Bean으로 등록한 메서드와 반환되는 인스턴스를 사용하고 싶을 때는 ApplicationContext 객체의 getBean()을 이용한다.

추가로 OrderApp 클래스도 스프링을 이용하는 방식으로 변경할 수 있다. 아래와 같이 코드를 변경하면 스프링 컨테이너가 관리하는 AppConfig의 모든 인스턴스를 사용할 수 있다.

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

## 순수한 자바 코드로 DI-> 스프링 컨테이너를 이용한 DI

- ApplicationContext를 스프링 컨테이너라고 한다. 기존에는 개발자가 AppConfig 를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 DI를 할 수 있다. 
- 스프링 컨테이너는 @Configuration이 붙은 AppConfig 를 설정(구성) 정보로 사용한다. 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 한다. 
- 스프링 빈은 @Bean이 붙은 메서드의 이름을 스프링 빈의 이름으로 사용한다. (ex. memberService, orderService)
- 스프링 컨테이너를 이용한 DI 방식을 이용하면 개발자가 AppConfig 객체를 직접 이용하지 않는다. 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾을 수 있다.
- 스프링 컨테이너에 등록된 스프링 빈은 applicationContext.getBean() 메서드를 사용해서 찾을 수 있다. 