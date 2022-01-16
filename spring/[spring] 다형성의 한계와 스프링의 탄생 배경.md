> 📌 이 글은 다형성만으로 올바른 객체 지향 설계가 어려운 이유와 스프링이 어떤 문제를 해결하기 위해 탄생하였는지 학습하고 정리한 내용입니다.<br><br>
> 본문의 핵심 아이디어는 김영한님의 '스프링 핵심 원리 - 기본 편' 강의를 수강하며 학습하였습니다.
<br><br>
다형성과 인터페이스의 분리만으로는 진정한 객체 지향 설계의 5가지 원칙을 충족할 수 없다는 것을 알게되었으며, 객체 지향 설계의 요건을 충족하는 설계를 위해서는 무엇을 개선해야 하는지 이해하는 계기가 되었습니다. 

<br>

# 다형성의 한계와 스프링의 탄생 배경

흔히 객체 지향의 핵심은 다형성에 있다고 말합니다. 그러나 다형성만으로는 바람직한 객체 지향 설계를 구현할 수 없습니다. **좋은 객체 지향 설계를 위해 다형성을 사용할 때, 발견하게 되는 구조적 한계점과 해결책을 정리**하였습니다.   

다형성의 한계와 대안은 스프링 프레임워크를 탄생시킨 배경이기도 합니다. 스프링의 핵심 개념인 `제어의 역전(IoC)`와 `의존 관계 주입(DI)`는 다형성의 한계를 극복하고, 객체 지향 설계를 따를 수 있도록 도와주는 도구입니다.   
<br>

---

<br>

## 1. 다형성과 인터페이스 분리의 한계

주문 도메인을 설계하고 구현한다고 가정해 보겠습니다. 주문 도메인에는 	`할인 정책`이 반영되어야 합니다. 기획자는 서비스 배포 이후에도 다양한 `할인 정책`이 도입될 수 있다고 합니다.<br>

`할인 정책`이라는 인터페이스를 설계하고, 새로운 할인 정책이 도입되거나 변경될 때, 할인 정책 인터페이스의 `구현체`를 변경해야 합니다. **할인 정책이라는 인터페이스를 이용해 역할을 정의하고, 해당 인터페이스의 구현체를 통해 구현을 분리하는 방식의 설계**입니다.<br>

주문 도메인 클래스의 구조는 아래와 같습니다. 앞서 말씀드린 `할인 정책` 인터페이스의 구현체로 `정액 할인 정책(FixDiscountPolicy)`과 `정률 할인 정책(Rate)DiscountPolicy`을 가정했습니다.<br>

아래 전체 구조도에는 말씀드린 내용 외에 `회원 저장(MemberRepository)`에 대한 내용도 있으나, 참고만 해주셔도 무방할 거 같습니다.

<br>

![](https://images.velog.io/images/woply/post/718da537-6abe-4696-b866-733808b9d3bf/image.png)
> 이미지 출처: 김영한님 '스프링 핵심 원리 - 기본 편' 강의 자료

<br>

현재의 `할인 정책(DiscountPolicy)`은 `고정 할인 정책(FixDiscountPolicy)`을 채택하고 있습니다. `고정 할인 정책(FixDiscountPolicy)`은 아래와 같이 주문 서비스의 구현체인  `OrderServiceImpl`에서 객체가 생성됩니다.

<br>

```java
public class OrderServiceImpl implements OrderService {

      private final MemberRepository memberRepository = new MemoryMemberRepository();

      private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); // 고정 할인 정책 구현체를 생성

      @Override
      public Order createOrder(Long memberId, String itemName, int itemPrice) {

          Member member = memberRepository.findById(memberId);
          int discountPrice = discountPolicy.discount(member, itemPrice);

          return new Order(memberId, itemName, itemPrice, discountPrice);
      }
}
```

<br>

이 때, 할인 정책이 변경되어 `고정 할인 정책(FixDiscountPolicy)`이 아닌 `정률 할인 정책(RateDiscountPolicy)`이 채택된다면 `OrderServiceImpl`의 코드를 아래와 같이 변경해야 합니다.

<br>

```java
public class OrderServiceImpl implements OrderService { 
    // private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); 
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy(); // 할인 정책 변경에 따른 코드 수정
  }
```

<br>

그리고 **바로 이 지점에서 다형성과 인터페이스 분리만으로 완전한 객체 지향 설계에 도달할 수 없다는 사실이 드러납니다.**

다형성과 인터페이스 분리는 **'역할과 구현을 분리'** 한다는 점에서 객체지향 설계의 **`ISP`(Interface segregation principle 인터페이스 분리 원칙)**와 **`LSP`(Liskov substitution principle 리스코프 치환 원칙)**를 충족합니다.<br>

하지만, **`SRP`(single responsibility principle 단일 책임 원칙), `OCP`(Open/closed principle 개방-폐쇄 원칙), `DIP`(Dependency inversion principle 의존 관계 역전 원칙)은 충족 할 수 없습니다.**

<br>

> 참고: SOLID
클린코드로 유명한 로버트 마틴이 좋은 객체 지향 설계의 5가지 원칙을 정리하고, 앞글자를 따서 SOLID로 지칭하였다.<br>
- SRP: 단일 책임 원칙(single responsibility principle)
- OCP: 개방-폐쇄 원칙 (Open/closed principle)
- LSP: 리스코프 치환 원칙 (Liskov substitution principle) 
- ISP: 인터페이스 분리 원칙 (Interface segregation principle) 
- DIP: 의존관계 역전 원칙 (Dependency inversion principle)

<br>

---

<br>

## 2. 객체 지향 설계의 요건을 충족하지 못하는 이유

<br>

### 2-1) DIP를 충족하지 못한 이유

<br>


`DIP`를 따르는 방법 중 하나는 클라이언트 객체가 오직 인터페이스만을 의존하는 것입니다. 구현체를 직접 의존 관계로 설정하면 안됩니다. 클라이언트 객체가 인터페이스와 인터페이스 구현체를 모두 의존하게 되면 이후 구현체 변경이 필요할 때, 클라이언트의 코드 수정이 불가피하기 때문입니다.<br>

하지만, 지금 `OrderServiceImpl`는 `DiscountPolicy` 뿐만 아니라 `FixDiscountPolicy`를 동시에 의존하고 있습니다.

<br>

```java
private final DiscountPolicy discountPolicy = new FixDiscountPolicy(); 
```

<br>

### 2-2) OCP를 충족하지 못한 이유
`OCP`는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다는 원칙입니다. 클라이언트가 인터페이스를 의존하고 있을 때, 구현체가 변경된다면 확장에 해당합니다. 그러나 클라이언트의 코드가 수정되어서는 안 됩니다. 변경에 닫혀있어야 하기 때문입니다.<br>

현재 `OrderServiceImpl`는 확장에는 열려있습니다. 언제든지 `new FixDiscountPolicy()`를 `new RateDiscountPolicy()`로 수정하여 구현체를 변경할 수 있기 때문입니다. 

하지만, **변경에는 닫혀있지 않습니다.** 구현체를 변경하기 위해서는 `OrderServiceImpl`의 코드를 직접 수정하여 주입 받는 의존관계의 변경해야 합니다. `FixDiscountPolicy`를 `RateDiscountPolicy`로 변경하려면, `OrderServiceImpl`의 소스 코드 변경이 불가피합니다.

<br>

### 2-3) SRP를 충족하지 못한 이유
단일 책임을 의미하는 `SRP`는 클래스에 단 하나의 책임을 부여해야 한다고 말합니다. 하나의 책임이라는 것은 정의하기에 따라 모호함이 존재하지만, 중요한 기준은 변경입니다. 책임의 범위 내에서 변경이 발생했을 때, 사이드 이펙트가 적으면 단일 책임 원칙을 잘 따른 것이라고 볼 수 있습니다.<br>

하지만 `OrderServiceImpl`는 주문 실행이라는 책임과 할인 정책 선택이라는 책임을 동시에 지니고 있습니다. 

<br>

---

<br>

## 3. '관심사의 분리'라는 아이디어

<br>

좋은 객체 지향 설계를 위한 해결책은 **'관심사의 분리'**라는 아이디어에서 시작됩니다. 

뮤지컬 공연이라는 실제 세계의 서비스를 통해 이 문제의 해결책인 **'관심사의 분리'**가 구체적으로 무엇을 의미하는지 설명해보겠습니다. 뮤지컬 홍보물을 보면 하나의 역할에 다수의 배우가 참여하고 있다는 것을 알 수 있습니다. 동일한 역할을 연기하는 주연 배우가 2명에서 많게는 3명인 경우도 있습니다. `배역`이라는 **개념적 역할**이 존재하고, 배역을 소화하는 `실제 배우`가 **역할의 실체**를 담당하는 것입니다. 
> 참고) 배역: 인터페이스 / 배우: 인터페이스 구현체로 비유하고자 합니다.

<br>

이때, `배우의 관심사`는 성공적인 공연을 위해 **배역을 잘 연기하는 것**입니다. **`역할의 실체`를 잘 구현하는 것이 배우의 관심사**입니다. 그런데 **배우에게 다른 배우의 섭외를 맡긴다면 배우는 자신의 관심사에 집중하지 못하는 상황**이 됩니다.<br>

이는 **주문 서비스라는 역할에만 관심이 있는 `OrderServiceImpl`에게 `DiscountPolicy`라는 역할을 담당해줄 구현체를 정해달라고 맡기는 것과 같습니다.**

배우의 섭외는 공연 기획자가 담당하고 배우는 배역에만 집중해야 하는 것처럼, **애플리케이션 내에서도 구현 객체를 생성하고, 연결하는 역할을 별도로 구성해야 합니다.** `AppConfig`를 통해 `OrderServiceImpl` 객체의 생성뿐만 아니라, `FixDiscountPolicy` 객체를 의존 관계로 주입하는 일을 맡기는 것입니다.


`AppConfig`를 이용해 아래와 같이 설계를 변경해 보겠습니다. 이제 `AppConfig` 객체를 이용해 `orderService()`를 호출하면 `MemoryMemberRepository`와 `FixDiscountPoliocy`를 의존관계로 주입하고 있는 `OrderServiceImpl` 구현체를 생성합니다. `AppConfig`는 메서드의 이름과 리턴 타입만으로도 전체 구성 정보(역할과 구현)를 잘 표현합니다.

<br>

```java
public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    private MemoryMemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    public OrderService orderService() {
        return new OrderServiceImpl(memberRepository(), discountPolicy());
    }

    public DiscountPolicy discountPolicy() {
        return new FixDiscountPoliocy();
    }
}
```

<br>

`OrderServiceImpl` 구현체는 오직 역할에 해당하는 인터페이스만 주입을 받고, 구현체에 대한 관심(의존 관계 주입)을 제거합니다. 그러면 실제 `OrderServiceImpl`가 생성될 때는 생성자를 통해서 `AppConfig`가 지정한 구현체를 의존 관계로 주입받게 됩니다.<br>

이제 **`OrderServiceImpl`는 인터페이스만 의존하고 있지만, `AppConfig`를 통해 구현체를 의존 관계로 주입을 수 있게 되었습니다.**

<br>

```java
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    // 생성자를 통해 인터페이스를 주입받는다. 구현체에 의존하지 않는다.     
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);

        int discountPrice = discountPolicy.discount(member, itemPrice);
        // 설계가 잘된 이유는 discountPolisy가 알아서 해주기 때문이다.
        // 단일 책임 원칙을 잘 지킨 경우라고 할 수 있다.

        return new Order(memberId, itemName, itemPrice, discountPrice);


    }
}
```

<br>

AppConfig를 통해 구조도가 아래와 같이 변경되었습니다.

<br>

![](https://images.velog.io/images/woply/post/48a72ffa-fc17-4ec2-95cd-39c250917cda/image.png)
> 이미지 출처: 김영한님 '스프링 핵심 원리 - 기본 편' 강의 자료

<br>

**객체의 `생성`과 `구성`을 담당하는 영역인 `AppConfig`와 객체의 `실행`과 `사용`을 담당하는 영역인 `OrderServiceImpl`로 분리**되었습니다. 이로 인해 **객체 지향 설계의 원칙을 모두 충족**할 수 있게 됩니다.

<br>

---

<br>

## 4. 객체 지향 설계의 5가지 원칙을 충족하는 구조

<br>

1번(다형성과 인터페이스 분리의 한계)에서 할인 정책이 변경되는 상황을 가정하였습니다. 이제 `고정 할인 정책(FixDiscountPolicy)`이 아닌 `정률 할인 정책(RateDiscountPolicy)`으로 변경해야 할 경우, 아래와 같이 `AppConfig`의 `DiscountPolicy` 메서드 반환 내용만 변경해 주면 됩니다.

<br>

```java
public DiscountPolicy discountPolicy() {
    // return new FixDiscountPolicy();  // 기존 할인 정책
    return new RateDiscountPolicy(); // 변경된 할인 정책
}
```

<br>

공연 기획자와 같이 애플리케이션의 구성과 생성을 담당하는 **`AppConfig`의 등장으로, 객체 지향 설계의 원칙인 DIP, OCP, SRP를 충족**할 수 있게 되었습니다. 이로 인해 다형성과 인터페이스 분리를 이용해 온전한 객체 지향 설계가 가능하게 됩니다.

<br>

### 4-1. DIP,OCP,SRP를 충족하는 구조

<br>

`AppConfig`가 애플리케이션에 필요한 구현체를 선택하고 생성해주면서, 클라이언트에 해당하는 **`OrderServiceImpl`은 오직 `DiscountPolicy` 인터페이스만을 의존 관계로 갖게 됩니다.** 따라서, **인터페이스만을 의존해야 하는 DIP를 충족**합니다. 

또한, **`OrderServiceImpl`는 사용에 필요한 어떤 코드도 변경이 필요하지 않습니다.** 확장에 개방되어 있으면서도, 변경에 닫혀있어야 하는 **`OCP`를 충족**합니다. 

마지막으로, **`OrderServiceImpl`가 오직 실행에만 집중할 수 있게 됩니다.** 관심사를 분리하여 실행을 담당하는 클라이언트 객체는 오직 실행의 책임만을 갖습니다. 한 클래스는 하나의 책임만 갖는 **`SRP`를 충족**합니다. 

<br>

---

<br>

## 5. 다형성, 인터페이스 분리, 관심사의 분리로 객체 지향 설계 가능

<br>

애플리케이션을 하나의 공연이라고 생각하면, **`관심사의 분리`는 기획자와 실제 공연에 참여하는 배우의 역할을 나눈 것과 같습니다.** 관심사의 분리를 통해 배우가 다른 배우의 섭외까지 책임져야 하는 구조에서 **오직 배역에만 집중할 수 있는 구조로 변경**되었습니다. **공연 기획자의 역할은 `AppConfig`가 담당**합니다.<br>

`AppConfig`는 애플리케이션의 **전체 동작 방식을 구성(config)하기 위해, 구현 객체를 생성하고, 연결하는 책임**을 갖습니다. 배우에 해당하는 `클라이언트 객체`는 **실행(배역을 연기)의 책임**을 갖습니다. 관심사가 분리된 것입니다.

<br>

> 정리해보면, 객체 지향 설계를 하나의 공연으로 비유하였습니다. 다형성을 하나의 배역에 여러 명의 배우가 연기하는 것으로 비유하였고, 인터페이스의 분리는 배역마다 서로 다른 대본과 시나리오가 부여되는 것으로 비유하였습니다. 여기에 기획자와 배우의 역할 구분을 통해 관심사의 분리를 비유하였습니다. 이로써 객체 지향 설계의 5가지 원칙을 충족하는 애플리케이션(공연) 구조를 설계할 수 있게 되었습니다. 

<br>

`AppConfig`와 같은 객체의 등장은 **다형성만으로는 한계가 있었던 객체 지향 설계를 구조적으로 개선하는 해결책**입니다. 그리고 `AppConfig`와 같은 해결책이 바로 **스프링의 탄생**을 이끌었습니다. <br>

**스프링은 다형성의 한계를 고민하며 더 쉽게 좋은 객체 지향 설계를 따를 수 있도록 고안된 객체 지향 설계 프레임워크**입니다. 스프링은 `제어의 역전(IoC)`과 `의존 관계 주입(DI)`을 통해 **다형성의 한계를 해결**합니다.

---

<br>

여기까지 객체 지향 설계의 5가지 원칙을 기준으로, **다형성이 갖는 한계**를 알아보았습니다. **다형성의 한계는 동시에 객체 지향 프레임워크인 스프링의 탄생을 이끈 아이디어**입니다. 스프링의 아이디어가 어디에서 출발했는지 이해하고, 코드를 통해 직접 느껴보면서 스프링이라는 프레임워크가 갖고 있는 철학을 이해하는 데 많은 도움이 되었습니다.