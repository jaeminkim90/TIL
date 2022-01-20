> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---


# Bean 등록 시, 타입 중복 문제를 해결하는 방법



스프링 컨테이너가 의존 관계를 주입할 때, `@Autowired` 는 타입(Type)으로 클래스를 조회한다. 타입으로 조회하는 방식은 `getBean()`을 이용하여 Bean을 조회하는 것과 유사하다. 부모 타입을 조회하면 자식 타입까지 모두 조회 대상에 포함된다.

예를 들어, `DiscountPolicy` 타입을 조회하면 하위 타입인 `FixDiscountPolicy`와 `RateDiscountPolicy`가 모두 조회 대상에 포함된다.

## 1. 중복된 타입의 클래스가 존재할 경우 어떤 일이 발생할까?

문제는 두 클래스 모두 스프링 Bean으로 등록되어 있는 경우다. 중복된 결과가 조회되는 문제가 발생한다. 아래와 같이 동일한 타입을 가진 두 클래스를 모두 Bean에 등록하고, 의존 관계를 주입하는 코드를 실행해보자.

**`DiscountPolicy` 타입을 가진 `FixDiscountPolicy.class`**
```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```
**`DiscountPolicy` 타입을 가진 `RateDiscountPolicy.class`**
```java
@Component
public class RateDiscountPolicy implements DiscountPolicy {}
```

**의존 관계 자동 주입을 실행하는 코드**
```java
@Autowired
private DiscountPolicy discountPolicy
```

**실행 결과, `NoUniqueBeanDefinitionException` 오류가 발생한다.** 
```java
NoUniqueBeanDefinitionException: No qualifying bean of type
'hello.core.discount.DiscountPolicy' available: expected single matching bean
but found 2: fixDiscountPolicy,rateDiscountPolicy
```

오류메시지는 하나의 Bean을 기대했으나, `fixDiscountPolicy`와 `rateDiscountPolicy` 2개의 Bean을 발견했다고 알려준다. 이때, 특정 클래스를 의존 관계로 주입하기 위해 하위 타입을 지정할 수도 있을 것이다. 하지만, 하위 타입으로 지정하는 것은 DIP를 위배하고 유연성도 떨어진다. 또한, 이름만 다르고 완전히 동일한 타입의 스프링 Bean 2개가 있을 때는 해결책이 될 수 없다.


## 2. 중복된 타입 문제를 해결하는 3가지 방법

여러개의 Bean이 등록되었을 때, 해결할 수 있는 3가지 방법이 있다.

- @Autowired 필드 명 매칭 
- @Qualifier -> @Qualifier끼리 매칭 ->  빈 이름 매칭 
- @Primary 사용 

### 2-1. @Autowired - 필드명 매칭

`@Autowired`를 이용하여 별도의 필드명을 매칭하는 방법이다. 필드명 매칭은 타입 매칭을 우선 실행한다. 타입이 동일한 클래스가 단 하나일 경우 필드명 매칭은 동작하지 않는다. 타입이 중복되는 경우에만 2차 조건으로 필드명 매칭을 사용한다.

```java
// 기존 코드
@Autowired
private DiscountPolicy discountPolicy

// 필드명을 Bean 이름으로 변경 
@Autowired
private DiscountPolicy rateDiscountPolicy
```


### 2-2. @Qualifier - Bean 이름 매칭

`@Qualifier`는 추가 구분자를 사용하는 방법이다. 타입 중복으로 인해 클래스 선택이 불가할 경우, 추가 구분자가 동일한 클래스를 찾아 주입한다. 추가적인 표식을 설정하는 것이지, 실제 빈 이름을 변경하는 것은 아니다. 사용 방법은 Bean 등록 시, `@Qualifier`를 추가해주면 된다.



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

아래와 같이 수동으로 `@Bean` 등록시에도 `@Qualifier`를 사용할 수 있다.
```java
@Bean
@Qualifier("mainDiscountPolicy")
public DiscountPolicy discountPolicy() {
	return new ...
}
```

`@Qualifier`는 모든 코드에 `@Qualifier`를 붙여야 한다는 단점이 있다.

### 2-3. @Primary - 우선순위 부여

`@Primary`는 단순히 우선순위를 부여하는 방법이다. `@Autowired`를 통한 의존 관계 주입 과정에서 여러개의 Bean이 조회되면 `@Primary` 붙어있는 클래스가 주입 우선권을 가진다. 

아래와 같이 `RateDiscountPolicy`에 `@Primary`가 있고, `FixDiscountPolicy`는 `@Primary`가 없는 경우, `DiscountPolicy` 타입을 가진 클래스 중에서는`RateDiscountPolicy`가 우선권을 가진다.

```java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

```

```java
@Component
public class FixDiscountPolicy implements DiscountPolicy {}

```
> 참고
`@Primary`는 범용적이며, 디폴트값의 성격을 갖는다. 반면, `@Qualifier`는 세부 옵션의 성격을 갖는다. `@Primary`와 `@Qualifier`가 둘 다 존재할 경우 `@Qualifier`에 우선권이 있다. **스프링은 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 선택권이 우선 순위가 높다.** 따라서, `@Qualifier`의 우선권이 높다.


@Primary 는 기본값 처럼 동작하는 것이고, @Qualifier 는 매우 상세하게 동작한다. 이런 경우 어떤 것이 우선권을 가져갈까? 스프링은 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 선택권이 우선 순위가 높다. 따라서 여기서도 @Qualifier 가 우선권이 높다 


---

## 3. 요약
- 의존 관계 주입 시, 타입의 중복 문제를 해결하는 방법은 3가지가 있다.
- 일반적인 우선순위는 `@Primary`로 부여하고, 세부 설정은 `@Qualifier`를 이용하면 좋다.
- `@Primary`와 `@Qualifier`가 모두 사용된 경우, `@Qualifier`에 우선권이 있다.