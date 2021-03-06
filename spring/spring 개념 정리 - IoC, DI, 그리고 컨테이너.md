> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# IoC, DI, 그리고 컨테이너 

![](https://images.velog.io/images/woply/post/0167e86f-9139-4db7-b14e-310bca97e022/image.png)
![](https://images.velog.io/images/woply/post/d2bdda91-5915-4da3-834e-5227db7c7bff/image.png)



## 제어의 역전 IoC(Inversion of Control)


- 프로그램 제어 흐름을 클라이언트 구현 객체가 직접 제어하는 것이 아니라, 외부에서 관리하는 것을 제어의 역전(IoC)라고 한다.
- 전체 구성과 객체 생성을 담당하는 AppConfig와 같은 객체를 만들어서 구현 객체는 자신의 역할(실행)에만 집중시킨다. 제어의 흐름은 AppConfig가 담당한다. 구현 객체는 인터페이스만 의존하기 때문에 어떤 구현체가 실행될지 알 수 없다. 구현체의 생성과 의존 관계 주입은 AppConfig가 담당한다.


## 프레임워크 vs 라이브러리 

- 프레임워크가 내가 작성한 코드를 제어하고, 대신 실행하면 그것은 프레임워크가 맞다. (ex. JUnit)
- 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리다. 
  

## 의존관계 주입 DI(Dependency Injection) 

- OrderServiceImpl 은 DiscountPolicy 인터페이스만 의존한다. 실제 어떤 구현 객체가 사용될지는 모른다. 
- 의존관계는 **정적인 클래스 의존 관계와, 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계 둘을 분리**해서 생각해야 한다. 
- 여기서 정적인 클래스 의존 관계는 DiscountPolicy가 되고, 동적인 객체는 구현체인 FixDiscountPolicy 또는 RateDiscountPolicy가 된다.

### 정적인 클래스 의존 관계 
- 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존관계는 애플리케이션을 실행하지 않아도 분석할 수 있다. 
- 위 클래스 다이어그램에서는 OrderServiceImpl가 MemberRepository , DiscountPolicy를 정적인 클래스로 의존 관계를 맺고 있음을 알 수 있다.
- 클래스 의존관계 만으로는 실제 어떤 객체(동적인 클래스 의존 관계)가 OrderServiceImpl 에 주입 될지 알 수 없다. 


### 동적인 객체 인스턴스 의존 관계 
- 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계다. 
- 동적인 객체 인스턴스는 외부에서 구현 객체를 관리하는 AppConfig가 생성하고 연결한다.
![](https://images.velog.io/images/woply/post/b8f507f6-3e3a-4f25-a942-85df03a394a2/image.png)

### AppConfig가 의존 관계를 주입한다.
- 애플리케이션 실행 시점(런타임)에 외부(AppConfig)에서 실제 구현 객체를 생성하고, 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것을 의존관계 주입이라 한다. 
- AppConfig는 객체 인스턴스를 생성하고, 그 참조값을 전달하는 방식으로 의존 관계를 연결한다. 
- 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다. 
- 의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있다. 



## IoC 컨테이너, DI 컨테이너 

- AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해 주는 것을 IoC 컨테이너 또는 DI 컨테이너라 한다. 
- 의존관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라 한다. 또는 어셈블러, 오브젝트 팩토리 등으로 불리기도 한다. 