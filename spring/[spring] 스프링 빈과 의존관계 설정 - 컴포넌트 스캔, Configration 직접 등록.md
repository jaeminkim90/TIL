> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 스프링 빈과 의존관계

## 컴포넌트 스캔과 자동 의존관계 설정 방법


회원 컨트롤러가 회원서비스와 회원 리포지토리를 사용할 수 있게 의존관계를 준비하려면 어떻게 해야할까?


생성자에 @Autowired 가 있으면 스프링이 연관된 객체를 스프링 컨테이너에서 찾아서 넣어준다. 이렇게 객체 의존관계를 외부에서 넣어주는 것을 DI (Dependency Injection), 의존성 주입이라 한다. 이전 테스트에서는 개발자가 직접 주입했고, 여기서는 @Autowired에 의해 스프링이 주입해준다. 

스프링 컨텐이너가 컨트롤러 객체를 생성하고 관리한다. 필요한 객체를 스프링 컨테이너에 등록하면 스프링이 관리해 준다. 그 작업을 해주는 것이 Autowired다. 생성자에 @autowired를 붙여놓으면 컨트롤러 생성시 컨텐이너가 연결시켜준다.

컨트롤러에서 서비스나 리포지토리 인스턴스를 생성하여 사용하려고 하면 아래와 같이 오류가 발생한다.

```
Consider defining a bean of type 'hello.hellospring.service. MemberService' in your configuration.
```

memberService가 스프링 빈으로 등록되어 있지 않기 때문이다. 

컨트롤러에서 사용하는 서비스 등의 클래스 객체도 @service라고 붙여줘야 스프링의 관리를 받을 수 있다. 그렇지 않으면 순수한 자바 코드일 뿐이라 스프링이 알수 없다. 표시를 해주는 것이다.

![](https://images.velog.io/images/woply/post/ce76ba34-da70-4ba7-b363-69c174706601/image.png)

Controller도 스프링이 제공하는 컨트롤러여서 스프링 빈으로 자동 등록된다. @Controller가 있으면 자동 등록된다.

---

스프링 빈을 등록하는 2가지 방법이 있다.
**1. 컴포넌트 스캔과 자동 의존관계 설정 **
**2. 자바 코드로 직접 스프링 빈 등록하기 **


---

### 1. 컴포넌트 스캔 방식

컴포넌트 스캔 원리 
- @Component 애노테이션이 있으면 스프링 빈으로 자동 등록된다. 
- @Controller 컨트롤러가 스프링 빈으로 자동 등록된 이유도 컴포넌트 스캔 때문이다. 
- @Component 를 포함하는 다음 애노테이션도 스프링 빈으로 자동 등록된다. 
```
@Controller 
@Service
@Repository
```

컨트롤러를 통해서 외부 요청을 받고, 서비스에서 비즈닉스 로직을 만들고, 리포지토리에서 데이터를 저장하는 것은 정형화 되어 있는 패턴이다. 이렇게 해놓으면 스프링이 뜰 때 컨트롤러 서비스 리포지토리를 가지고 올라온다.

![](https://images.velog.io/images/woply/post/2ea9cdfb-af26-4bd5-948d-0d9b4d54e324/image.png)

@autowired를 통해 memberService 와 memoryMemberRepository를 스프링 컨테이너에 스프링 빈으로 등록할 수 있다. 

기타 참고 내용
- 생성자에 @Autowired 를 사용하면 객체 생성 시점에 스프링 컨테이너에서 해당 스프링 빈을 찾아서 주입한다. 생성자가 1개만 있으면 @Autowired 는 생략할 수 있다.  
- 스프링은 스프링 컨테이너에 스프링 빈을 등록할 때, 기본으로 **싱글톤으로 등록**한다(유일하게 하나만 등록해서 공유한다) 따라서 같은 스프링 빈이면 모두 같은 인스턴스다. 설정으로 싱글톤이 아니게 설정할 수 있지만, 특별한 경우를 제외하면 대부분 싱글톤을 사용한다.
- 컴포넌트가 유효한 패키지 범위는 main 메서드를 포함하고 있는 패키지와 그 하위 패키지다.
![](https://images.velog.io/images/woply/post/85607cac-7088-4e70-bce4-5eb41a30d797/image.png)

---

### 2. 자바 코드로 직접 스프링 빈 등록하기


(회원 서비스와 회원 리포지토리의 @Service, @Repository, @Autowired 애노테이션을 제거한 상태에서) main 메서드가 위치한 패키지에 @Configurtion 역할을 할 클래스를 생성한다. @Configuration을 추가하면 구성파일로 사용할 수 있다.

아래와 같이 @Configuration 파일에 @Bean을 등록하면 스프링이 뜰 때 스프링 빈으로 해당 클래스를 등록한다. 컨트롤러는 스프링이 처음부터 관리하고 있기 때문에 컴포넌트 스캔만 가능하다. 컨트롤러 안에서 사용되는 생성자는 @Autowired를 사용해야 한다.

```java
@Configuration
public class SpringConfig {

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```



향후 다른 리포지토리로 변경이 예상된다면, 컴포넌트 스캔 방식보다 자바 코드를 이용한 `@Configuration 스프링 빈 직접 등록 방식`이 좋다. 

> 참고
> - XML로 설정하는 방식도 있지만 최근에는 잘 사용하지 않으므로 생략한다. 


---


#### 3가지 DI 방법(필드 주입, setter 주입, 생성자 주입)
 
#### 1. 생성자 주입 방식

생성자 주입 방식은 컨트롤러,서비스,리포지토리의 생성자를 이용해 의존성을 주입하는 경우이다. 의존관계가 실행중에 동적으로 변하는 경우는 거의 없으므로 생성자 주입을 권장한다. 

런타임 중 의존관계 변경이 필요한 경우 대부분 @Configuration 파일을 수정 후 서버 재부팅한다. 런타임 도중에 동적으로 의존 관계를 변경할 일은 사실상 없다고 보아도 무방하다.
  
```java
@Controller
public class MemberController {

    private final MemberService memberService;

    @Autowired
    public MemberController(MemberService memberService) {
        this.memberService = memberService;

    }

```

#### 2. 필드 주입 방식

필드 주입 방식은 필드 변수에 직접 주입하는 방법이다. 중간에 변경할 수 있는 방법이 없으므로 추천하지 않는다.

```java
@Controller
public class MemberController {

    @Autowired private MemberService memberService;
```

#### 3. setter 방식

setter 방식은은 의존관계가 실행중에 동적으로 변하는 경우가 거의 없음에도 불구하고 public 상태를 유지해야하기 떄문에 권장하지 않는다.

의존관계는 주로 로딩할 때 사용되는 것이지, 중간에 변경할 일이 거의 없다. 생성자 방식도 public이지만 생성자가 처음에 에플리케이션 조립 시점에서만 작동한다는 점이 setter와의 차이점이다.

```java
@Controller
public class MemberController {

    private MemberService memberService;

    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }
```


> **참고 사항**
> 실무에서는 주로 정형화된 컨트롤러, 서비스, 리포지토리 같은 코드에서 컴포넌트 스캔을 사용한다. 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 할 경우 @Configuration 설정을 통해 스프링 빈으로 등록한다.<br>
> 일종의 다형성을 활용하는 것인데, @configration 파일에서 인터페이스의 구현체만 변경해주면 되기 때문에 무척 간단하게 변경할 수있다. 컴포넌트 스캔을 사용하면 여러 파일의 코드를 변경해줘야 한다.
   
> **주의 사항**
> @Autowired 를 통한 DI는 컴포넌트 스캔 방식이든, @Configration 직접 등록 방식이든 반드시 스프링 빈에 등록되어 스프링의 관리 범위 안에 들어 있는 객체여야만 동작한다.
