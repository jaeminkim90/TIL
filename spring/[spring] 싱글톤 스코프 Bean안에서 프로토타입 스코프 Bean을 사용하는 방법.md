
> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---


# 싱글톤 타입 Bean안에서 프로토타입 스코프를 사용하는 방법

스프링 Bean은 스코프를 가지고 있다. Bean 스코프는 Bean의 생명주기를 의미한다. Bean은 생명주기에 따라 생성되는 시점과 소멸되는 시점이 정해진다. 일반적으로 싱글톤 Bean의 경우 하나의 객체가 생성되어, 공유 사용되는 특징이 있다. 따라서 생명주기도 컨테이너의 생성 시점부터 컨테이너의 종료 시점까지다.

반면, 프로토타입 스코프를 가진 Bean은 조회 시점에 생성된다. 또한, 스프링 컨테이너가 의존 관계 주입까지만 관리한다.

문제는 서로 다른 스코프를 가진 두 개의 Bean이 함께 사용되는 경우다. 싱글톤 Bean안에서 프로토타입 스코프를 가진 Bean이 의존 관계로 주입되어 있으면, 본래의 스코프가 보장되지 않는다. 

물론, 이러한 문제를 해결할 수 있는 방법이 있다. 구체적인 해결 방법을 소개하기 앞서, 스프링이 지원하는 주요 스코프의 특징을 확인해 볼 것이다. 이어서 두 스코프가 함께 사용될 때 어떤 문제가 발생하는지 살펴보고, 결론적으로 문제를 해결하는 몇 가지 방법을 검토해 볼 것이다.

## 1. 스프링에서 지원하는 주요 스코프

스프링에는 싱글콘 스코프외에 몇 가지 다양한 스코프를 지원한다. 대표적인 스코프는 아래와 같다.

**주요 스코프**
- `싱글톤`: 기본 스코프다. 스프링 컨테이너의 시작과 종료까지 유지되는 가장 넓은 범위의 스코프다. 
- `프로토타입` : Bean이 조회되는 시점에서 스프링 컨테이너가 Bean을 생성하고, 의존관계 주입까지만 관여한다. 매우 짧은 범위의 스코프다.

**웹 관련 스코프**
- `request`: 웹 요청(request)이 발생한 시점부터 종료된 시점까지 유지되는 스코프다. 
- `session`: 웹 세션(session)이 생성된 시점부터 종료된 시점까지 유지되는 스코프다.
- `application`: 웹의 서블릿 컨텍스트와 같은 생명주기를 유지하는 스코프다.
	  

### 1-1. 스코프 지정 방법

별도의 Bean 스코프를 지정하지 않으면 기본 스코프인 싱글콘이 적용된다. 만약, 특정 스코프를 지정하고 싶다면 아래와 같은 방법을 사용한다. `자동 컴포넌트 스캔 방식`과 `수동 등록 방식` 모두 `@Scope` 애노테이션을 선언하고 스코프 범위를 명시하면 된다.

**컴포넌트 스캔 자동 등록** 
```java
@Scope("prototype")
@Component
public class HelloBean {}
```

**수동 등록** 
```java
@Scope("prototype")
@Bean
PrototypeBean HelloBean() {
  return new HelloBean();
}
```

### 1-2. 프로토타입 스코프의 특징

프로토타입 스코프는 몇 가지 독특한 특징을 가지고 있다. 먼저, 싱글톤 스코프와 달리 프로토타입 스코프는 조회 시, 매번 새로운 Bean을 생성한다. 스프링 컨테이너가 같은 인스턴스를 공유하여 사용하는 싱글콘 스코프와 큰 차이가 있다. 또한, 컨테이너 생성 시점에 Bean이 생성되지 않는다. 프로토타입을 가진 Bean은 조회되는 시점에 생성된다. 그림으로 살펴보자.


> **싱글톤 Bean은 늘 같은 인스턴스를 공유하여 사용한다** 
> 1. 싱글톤 스코프의 Bean을 스프링 컨테이너에 요청한다.
> 2. 스프링 컨테이너는 본인이 관리하는 스프링 Bean을 반환한다. 
> 3. 같은 요청이 반복되면, 스프링 컨테이너는 같은 인스턴스를 공유하여 사용한다.
> ![](https://images.velog.io/images/woply/post/ac675f7c-95d4-4575-90f7-60c6d33969e7/image.png)


> **프로토타입 Bean은 조회 시점에 Bean이 생성되고, 필요한 의존관계를 주입한다**
> 1. 프로토타입 스코프의 Bean을 스프링 컨테이너에 요청한다. 
> 2. 스프링 컨테이너는 이 시점에서 프로토타입 Bean을 생성하고, 필요한 의존관계를 주입한다.
![](https://images.velog.io/images/woply/post/537ba5e7-9480-47b9-9bde-7d61bc1de4e2/image.png) 
> 3. 스프링 컨테이너는 생성한 프로토타입 Bean을 클라이언트에 반환한다. 
> 4. 스프링 컨테이너에 같은 요청이 오면, 항상 새로운 프로토타입 Bean을 생성하여 반환한다.
![](https://images.velog.io/images/woply/post/dcfeed29-ad77-4199-bddd-976248a1e868/image.png)



한 가지, 중요한 점은 **스프링 컨테이너가 프로토타입 Bean을 생성하고, 의존관계 주입, 초기화까지만 관리를 담당**한다는 점이다. 클라이언트에 Bean이 반환되면 더 이상 관리하지 않는다. 따라서, **`@PreDestroy` 같은 종료 콜백 메서드가 호출되지 않는다.**



---

## 2. 싱글톤과 프로토타입을 함께 쓰면 어떤 문제가 발생할까?

프로토타입 스코프는 항상 새로운 객체 인스턴스를 생성해야 한다. 하지만, 싱글톤 Bean과 함께 사용하면 본래의 기능이 동작하지 않는다. 아래 그림을 통해 어떤 문제가 발생하는지 살펴보자.

### 2-1. 싱글톤에서 프로토타입 스코프가 작동하지 않는 이유

- `clientBean`은 싱글톤 스코프를 가진다. 따라서, 스프링 컨테이너 생성 시점에 함께 생성되고, 의존 관계가 주입된다.
- 컨테이너는 `clientBean`의 의존 관계를 자동으로 주입한다. 주입 시점에 스프링 컨테이너에 프로토타입 Bean을 요청한다. 
- 스프링 컨테이너는 `PrototypeBean`을 생성해서 `clientBean`에 반환한다. `PrototypeBean`의 count필드 값은 0이다. 
- 이제 `clientBean`은 `PrototypeBean`의 참조값을 내부 필드에 보관한다.
![](https://images.velog.io/images/woply/post/24174e3d-a67b-476d-b642-6e6e930cef54/image.png)

- 컨테이너로부터`clientBean`을 반환 받은 클라이언트 A는 `clientBean.logic()`를 호출한다. `logic()`은 필드 변수의 count 값을 1 증가시키는 작업을 수행한다.
- 클라이언트 A의 `clientBean.count` 값은 1이 된다.

- 다음으로, 클라이언트 B가 `clientBean`을 스프링 컨테이너에 요청한다. `clientBean`은 싱글톤이다. 따라서, 항상 같은 `clientBean` 객체가 반환된다.  

![](https://images.velog.io/images/woply/post/bc8d9704-1ed3-4677-88e1-6e0f5ef735d2/image.png)

- **바로 이 지점에서 문제가 발생한다. `clientBean`이 내부에 가지고 있는 `PrototypeBean`은 생성 시점에 의존 관계로 주입된 Bean이다. 프로토타입을 가진 객체지만 싱글톤 스코프에 종속되어있다. `PrototypeBean`은 `clientBean` 호출 시 새로 생성되지 않는다.**
- 클라이언트 B가 `clientBean.logic()`를 호출하면 count 값은 2가 된다. 클라이언트 A가 올려놓은 count이 누적되어 있기 때문이다.

```java
public class SingletonWithPrototypeTest1 {

    @Test
    void prototypeFind() {
        ApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);

        PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
        prototypeBean1.addCount();
        assertThat(prototypeBean1.getCount()).isEqualTo(1);

        PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);
        prototypeBean2.addCount();
        assertThat(prototypeBean1.getCount()).isEqualTo(1);
    }

    @Test
    void singletonClientUsePrototype() {
        ApplicationContext ac =
                new AnnotationConfigApplicationContext(ClientBean.class, PrototypeBean.class);
        ClientBean clientBean1 = ac.getBean(ClientBean.class);
        int count1 = clientBean1.logic();
        assertThat(count1).isEqualTo(1);

        ClientBean clientBean2 = ac.getBean(ClientBean.class);
        int count2 = clientBean2.logic();
        assertThat(count2).isEqualTo(1);
    }

    @Scope("singleton")
    // @RequiredArgsConstructor: 생성자를 자동으로 만들어 준다.
    static class ClientBean {
        private final PrototypeBean prototypeBean; // 생성 시점에 주입된다.

        @Autowired // 생성자가 한 개일 때는 생략 가능
        public ClientBean(PrototypeBean prototypeBean) {
            this.prototypeBean = prototypeBean;
        }

        public int logic() {
            prototypeBean.addCount();
            return prototypeBean.getCount();
        }
    }

    @Scope("prototype")
    static class PrototypeBean {
        private int count = 0;

        public void addCount() {
            count++;
        }

        public int getCount() {
            return count;
        }

        @PostConstruct
        public void init() {
            System.out.println("PrototypeBean.init: " + this);
        }

        @PreDestroy
        public void destroy() {
            System.out.println("PrototypeBean.destroy");
        }
    }
}
```
스프링은 일반적으로 싱글톤 Bean을 사용한다. 위 예제는 싱글톤 Bean이 프로토타입 Bean을 사용하고 있다. 싱글톤 Bean은 생성 시점에만 의존 관계를 주입받는다. 주입받은 객체가 프로토타입 Bean이지만, 싱글톤에 종속되어 있어 본래의 기능이 작동하지 않는 문제가 발생했다.

> 참고   
> 만약 클라이언트 A와 클라이언트 B가 각각 다른 Bean을 통해 동일한 프로토타입 Bean을 주입받았다면, 새로운 프로토타입 Bean이 생성될 것이다. 물론, 사용할 때 마다 새로 생성되는 것은 아니다.


## 3. 문제 해결: ObjectProvider 또는  Provider 사용
 
싱글톤 Bean과 프로토타입 Bean을 함께 사용하면서, 각 스코프가 가진 고유한 기능을 모두 유지하는 가장 쉬운 방법은 매번 새로 조회하는 것이다. `getBean()`을 이용하여 스프링 컨테이너에 Bean을 요청하는 방법이다.

아래 코드를 실행해 보면 `ac.getBean()`를 통해서 항상 새로운 프로토타입 Bean이 생성되는 것을 확인할 수 있다. 이와 같은 방식을 **Dependency Lookup(DL): 의존관계 조회(탐색)**이라고 한다.

```java
@Autowired
private ApplicationContext ac;

public int logic() {
    PrototypeBean prototypeBean = ac.getBean(PrototypeBean.class); // Dependency Lookup (DL) 의존관계 조회(탐색)
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
}
```
하지만, 매번 `ApplicationContext` 인스턴스 전체를 주입받는 방식은 스프링 컨테이너에 지나치게 종속적인 구조가 될 수 있다. 단위 테스트의 어려움도 발생한다. 

`getBean()` 정도의 기능을 콤팩트하게 지원하는 무언가가 필요한데, **Provider**가 바로 그 역할을 한다.

### 3-1. ObjectProvider 

`ObjectProvider`는 스프링 컨테이너 대신 지정한 Bean을 찾아가 `DL(Dependency Lookup(DL) 의존관계 조회)`을 제공한다. 과거에는 `ObjectFactory`를 사용했는데, 지금은 여러가지 편의 기능이 추가된 `ObjectProvider`를 주로 사용한다.

`ObjectProvider`의 사용 방법은 간단하다. 자동 의존 관계 주입을 위해 필드 변수에 `ObjectProvider`를 선언한다. 그러면, 스프링 컨테이너는 Bean을 최초 등록하는 시점에서 `PrototypeBean`이 아닌, `ObjectProvider`를 의존 관계로 주입한다. `ObjectProvider`는 별도로 Bean을 지정하지 않아도 스프링이 자동으로 인스턴스를 생성하고, 의존 관계 주입까지 처리한다.

그리고 `PrototypeBean`이 필요한 시점에서 `getObject()`로 호출하여 사용한다. `getBean()`과 동일한 기능인 `DL(Dependency Lookup(DL) 의존관계 조회)`를 통해 새로운 Bean을 생성하고 반환한다.

```java
@Autowired
private ObjectProvider<PrototypeBean> prototypeBeanProvider;

public int logic() {
    PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
} 
```

### 3-2. Provider

`ObjectProvider`대신 `Provider`를 사용할 수도 있다. `Provider`는 `get()`을 통해 Bean을 조회한다. 전반적인 기능은 `ObjectProvider`와 동일하다. 

`Provider`은 `javax.inject.Provider`라는 `JSR-330` 자바 표준을 사용하는 방법이다. 자바 표준이기 때문에 스프링에 종속적이지 않다. `javax.inject:javax.inject:1` 라이브러리를 추가하면 사용할 수 있다.


```java
//implementation 'javax.inject:javax.inject:1' gradle 추가 필수 
@Autowired 
private Provider<PrototypeBean> provider; 

public int logic() {
    PrototypeBean prototypeBean = provider.get();
    prototypeBean.addCount();
    int count = prototypeBean.getCount();
    return count;
} 
```

```java
package javax.inject;
public interface Provider<T> {
    T get();
} 

```


---

## 4. 요약

- 매번 새로운 객체가 필요하다면, 프로토타입 스코프를 사용한다.
- 싱글톤 스코프와 프로토타입 스코프를 함께 사용할 경우 프로토타입 스코프가 정상적으로 기능하지 않을 수 있다. 그럴땐, ObjectProvider나 JSR330 Provider를 사용하여 문제를 해결할 수 있다.

> 참고   
> 스프링과 자바 표준에서 제공하는 기능이 겹칠 때가 있다. 대부분 스프링이 더 다양하고 편리한 기능을 제공한다. 따라서, 특별하게 다른 컨테이너를 사용할 일이 없다면 스프링의 기능을 사용하는 것이 좋다.