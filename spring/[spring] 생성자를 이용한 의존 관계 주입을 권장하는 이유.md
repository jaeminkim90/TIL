> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 왜 생성자 주입 방식을 권장할까? 


스프링의 의존 관계 주입은 크게 4가지 방법이 있다. 당연히 각각의 장점과 단점이 다르다. 상황에 따라 적합한 방법을 사용하는 것이 가장 좋다. 그러나 일반적으로 생성자 주입 방식을 권장한다. 생성자 주입 방식의 가장 큰 장점은 **불변**과 **누락 방지**를 보장한다는 것이다.

의존 관계를 주입하는 4가지 방법과 함께 생성자 주입 방식의 장점을 자세히 살펴보자.

## 1. 스프링의 의존 관계 주입 방법 4가지

의존 관계를 주입하는 방법은 크게 4가지다. 

- 생성자 주입 
- 수정자 주입(setter 주입) 
- 필드 주입 
- 일반 메서드 주입 

### 1-1. 생성자 주입 방식

**생성자 주입**은 말 그대로 생성자를 이용해 의존 관계를 주입한다. 생성자는 객체가 처음 생성되는 시점에서 호출된다. 바로 그 시점에서 파라미터를 이용해 의존 관계가 주입되는 것이다. 생성자 호출 시점은 객체 생성 시점으로 한정되어 있다. 단 1회만 호출된다는 특징은 **의존 관계의 불변성**과 **의존 관계의 필수성**으로 연결된다.

**의존 관계의 불변성**은 한 번 정해지면, 변화하지 않는다는 의미다. 생성자 호출 시점에서만 의존 관계가 주입되기 때문에 의존 관계에 변화가 발생하지 않는다. 불변의 특성을 갖게된다.

**불변성**은 개발에서 특히 중요하다. 무한하게 열려있는 코드보다, 명확한 제약이 존재하는 코드가 본래의 목적에 더 충실할 가능성이 크다. 또한, 불변성이 없으면 누군가 의도치 않게 변경할 수 있는 가능성이 있다. 원인 파악이 힘든 버그의 원인이 될 수 있다.

**의존 관계의 필수성**은 생성자 고유의 역할에서 기인한다.생성자는 객체 생성에 필수적으로 필요한 요소를 파라미터로 받는다. 따라서, 생성자의 의존 관계로 주입되는 객체가 있다면 필수 의존 관계와 다름 없다. 

필드 변수에 `final`을 사용하면, 객체 생성 시점에 초기화가 이뤄져야 한다. 생성자를 이용하여 `final` 타입을 가진 필드 변수를 초기화하도록 설계하면, 누구든 쉽게 필수 의존 관계를 파악할 수 있다. 가장 빠르고, 명확한 컴파일 오류가 발생하기 때문이다.

아래는 **생성자 주입 방식**으로 의존 관계를 주입하는 예시 코드다.
```java
@Component
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
```


### 1-2. 수정자 주입 방식(setter 주입)

`setter`로 불리는 수정자 메서드를 이용하는 방식이다. 필드의 값을 직접 변경하지 않고, `setter`와 `getter` 메서드를 사용해 값을 읽거나 수정하는 **자바빈 프로퍼티 규약**을 이용한다.


클래스에 별도의 `public` 매서드를 이용하여 의존 관계를 주입한다. `public`을 사용한다는 점에서 장점과 단점이 발생한다. 언제나 열려있는 `public` 메서드는 선택과 변경의 가능성이 있는 의존 관계 주입에 필요하다. 반면, `public` 특성으로 인해 불변성을 보장할 수 없다. 언제든 외부에서 변경이 가능하다는 잠재적 문제가 있다. 

아래는 setter를 이용한 수정자 주입 방식의 예시 코드다. `@Autowired`가 없으면 값이 의존 관계 주입이 되지 않는다.

```java
@Component
    public class OrderServiceImpl implements OrderService {

        private MemberRepository memberRepository;
        private DiscountPolicy discountPolicy;

        @Autowired
        public void setMemberRepository(MemberRepository memberRepository) {
            this.memberRepository = memberRepository;
        }

        @Autowired
        public void setDiscountPolicy(DiscountPolicy discountPolicy) {
            this.discountPolicy = discountPolicy;
        }

} 
```

### 1-3. 필드 주입 방식

이름처럼 필드 변수로 직접 주입하는 방법이다. 코드가 간결하다는 장점이 있지만, 외부에서 변경이 불가능하다는 치명적인 단점이 있다. 일반적으로 권장하지 않는 방식이다. 간혹, 애플리케이션의 실제 코드와 관계 없는 테스트 코드를 다룰 때나, 스프링 설정을 목적으로 만드는 `@Configuration`에서만 한정적으로 사용한다.

필드 주입 방식의 예시 코드는 아래와 같다.
		  

```java
@Component
public class OrderServiceImpl implements OrderService {

    @Autowired private MemberRepository memberRepository;

    @Autowired private DiscountPolicy discountPolicy;
} 
```


### 1-4. 일반 메서드 주입 방식

사실상 수정자 주입 방식과 거의 동일하다. `setter`가 아닌 일반 메서드를 이용한다는 점이 소소한 차이점이다. 한번에 여러 필드를 주입 받을 수 있다는 장점은 있지만, 일반적으로 잘 사용하는 방법은 아니다.


아래는 일반 메서드 주입 방식의 예시 코드이다.
```java
@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired 
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}

```


> 참고
4가지 방식 모두 의존 관계로 주입하려는 클래스가 스프링 컨테이너에 등록되어있는 Bean이어야 한다. 스프링 Bean이 아닌 클래스는 `@Autowired`가 기능하지 않는다.


## 2. 생성자 주입 방식은 불변과 누락 방지를 보장한다.

앞서 살펴본 대로 4가지 방식 모두 나름의 장점과 단점을 가지고 있다. 그럼에도, 스프링을 포함한 대부분의 DI 프레임워크가 생성자 주입 방식을 권장한다.

생성자 주입 방식의 가장 큰 장점인 **불변**과 **누락 방지**의 중요성이 크기 때문이다. 대부분의 의존 관계 주입은 애플리케이션 실행 시점에서 한번 일어난다. 그리고는 종료 전까지 변경할 일이 극히 드물다. 오히려 변경이 발생하지 않도록 설계해야 하는 일이 더 많다.

**불변**을 의도적으로 설계해야 하는데, 생성자 주입 방식은 **불변**을 보장하기 때문에 개발자가 느끼는 장점의 체감 효용이 더욱 크다.

`final`을 이용한 **누락 방지** 생성자 주입 방식의 큰 장점이다. 프레임워크 없이 순수한 자바 코드로 단위 테스트를 진행할 경우, 생성자 지정 방식은 컴파일 오류를 발생시킨다. 개발자가 필요한 의존 관계를 빠르게 파악할 수 있다. 


필드 변수에 `final` 타입이 지정되어 있으면, 오직 생성자 주입 방식에서만 초기화가 가능하다. 따라서, 생성자 주입 방식만 `final` 키워드를 사용할 수 있다.

```java
 java: variable discountPolicy might not have been initialized
```


---
## 3. 요약

- 생성자 주입 방식을 권장하는 이유는 **불변성**과 **누락 방지**를 보장해주기 때문이다. 
- 항상 생성자 주입 방식을 사용하자. 가끔 옵션이 필요할 경우 간헐적으로 수정자 주입 방식을 사용하자. 필드 주입 방식은 사용하지 말자.



