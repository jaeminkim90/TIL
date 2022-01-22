> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---


# 여러 개의 빈을 조회하는 방법


특정 타입에 해당하는 스프링 Bean을 모두 조회해야 하는 경우가 있다. 예를 들면, 클라이언트가 다양한 할인 방식(정액 할인, 정률 할인) 중 하나를 직접 선택해야 하는 상황이다. 이때, 스프링을 사용하면 Map이나 List로 복수의 Bean을 모두 조회하여 담을 수 있다. 


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

        @Autowired // 생성자가 1개일 경우, @Autowired 생략 가능
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

> 참고
- 생성자가 1개일 경우, `@Autowired`를 생략할 수 있다.

**로직 분석** 

- `DiscountService` 는 Map으로 모든 `DiscountPolicy`  를 주입받는다. 이때, `fixDiscountPolicy`, `rateDiscountPolicy`가 주입된다.

- `discount()`  메서드는 `discountCode` 로 `fixDiscountPolicy`가  넘어오면 Map에서 `fixDiscountPolicy`스프링 Bean을 찾아서 실행한다. 물론, `rateDiscountPolicy`가 넘어오면 `rateDiscountPolicy`  스프링 Bean을 찾아서 실행한다. 

**주입 분석** 
- `Map <String, DiscountPolicy>`: Map의 key에 스프링 Bean의 이름을 넣어주고, Value는 `DiscountPolicy`타입으로 조회한 스프링 Bean을 담아준다. 

- `List <DiscountPolicy>` : `DiscountPolicy` 타입으로 조회한 모든 스프링 Bean을 담아준다. 만약 해당하는 타입의 스프링 Bean이 없으면, 빈 컬렉션이나 Map을 주입한다. 


> 참고
스프링 컨테이너를 생성하면서 스프링 Bean을 등록할 수 있다. 
스프링 컨테이너는 생성자에 클래스 정보를 받는다. 파라미터에 들어간 클래스는 스프링 Bean으로 자동 등록된다. 
```java
new AnnotationConfigApplicationContext(AutoAppConfig.class,DiscountService.class);
```
이 코드는 컨테이너 생성과 동시에 `AutoAppConfig`, `DiscountService`를 스프링 Bean으로 자동 등록한다. 따라서, 코드의 동작을 2가지로 나누어 이해할 수 있다.
- `new AnnotationConfigApplicationContext()`를 통해 스프링 컨테이너를 생성한다. 
- 파라미터로 들어간 `AutoAppConfig.class`, `DiscountService.class`는 자동으로 스프링 Bean에 등록된다. 