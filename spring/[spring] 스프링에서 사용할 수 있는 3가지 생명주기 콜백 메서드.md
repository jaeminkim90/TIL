# 스프링에서 사용할 수 있는 3가지 생명주기 콜백 메서드  


데이터베이스 커넥션 풀이나, 네트워크 소켓을 이용하다 보면 객체의 초기화와 종료 사이클을 고민하게 된다. 스프링도 초기화 작업과 종료 작업이 필요한 상황이 있다. 만약, Bean 객체 생성 직후에 초기화 작업이 필요하거나, 종료 직전에 반드시 실행해야 하는 작업이 있다면 어떻게 처리하는 것이 좋을까?


## 1. 간단한 실험으로 스프링 Bean의 생명주기 확인

간단하게 외부 네트워크 연결되는 (모형) 객체를 하나 만들었다고 가정해보자. (모형) 객체를 통해 스프링의 초기화 작업과 종료 작업이 어떻게 진행되는지 살펴보려 한다. 실제 네트워크에 연결하는 것은 아니고, 문자열 출력을 통해 전체 과정에서 연결과 종료가 어떤 순서로 일어나는지 확인해 보는 것이 목적이다.

아래 코드를 실행해보면, 생성자가 호출됨에 따라 URL을 출력하는`connect()`가 실행된다. 하지만 결과를 보면 URL 정보가 Null로 표기되어 있다.

```java
public class BeanLifeCycleTest {

    @Test
    public void lifeCycleTest() {
        ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
        NetworkClient bean = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig {
        @Bean
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
    }
}
```

```java
public class NetworkClient {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
        connect();
        call("초기화 연결 메시지");
    }

    public void setUrl(String url) {
        this.url = url;
    }

    // 서비스 시작 시 호출 메서드
    public void connect() {
        System.out.println("connect = " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " / message: " + message);
    }

    // 서비스 종료 시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
}
```

결과
![](https://images.velog.io/images/woply/post/f3112c02-d873-40a1-9908-a2fe9aeeca8e/image.png)

생성자 부분을 보면 URL 정보 없이 `connect()`가 호출되고 있다. 객체를 생성하는 단계에는 URL 정보가 없고, 객체를 생성한 다음에 외부에서 수정자 주입을 통해 `setUrl()`이 호출되어야 URL 정보가 생기기 떄문이다.


## 2. 스프링 Bean의 생명주기

스프링 Bean은 아래와 같은 라이프사이클을 가진다.

> **스프링 빈의 생명 주기**
`스프링 컨테이너생성` > `스프링 빈 생성` > `의존관계 주입` > `초기화 콜백` > `사용` > `소멸 전 콜백` > `스프링 종료` 
>- `초기화 콜백`: 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출 
> - `소멸 전 콜백`: 빈이 소멸되기 직전에 호출 

### 2-1. 스프링은 콜백 메서드를 지원한다 

스프링 빈은 객체를 생성하고, 의존관계 주입이 모두 끝나야 필요한 데이터를 사용할 수 있다. 따라서, 초기화 작업은 의존관계 주입이 모두 완료되고 난 다음에 진행되어야 한다. 개발자가 의존관계 주입이 모두 완료된 시점을 별도로 확인할 수 없기 때문에 콜백 메서드를 이용해야 한다.

스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려준다. 그리고 이와 관련된 다양한 기능을 제공한다. 또한, 스프링은 스프링 컨테이너가 종료되기 직전에 소멸 콜백을 준다. 

개발자는 콜백 메서드를 이용하여 생성과 초기화 단계를 분리할 수 있고, 종료 전에 필요한 작업을 진행할 수 있다.

스프링은 크게 3가지 방법으로 빈 생명주기 콜백을 지원한다.

- 인터페이스(`InitializingBean`, `DisposableBean`)
- 설정 정보에 초기화 메서드, 종료 메서드 지정
- `@PostConstruct`, `@PreDestroy` 애노테이션 지원


> 참고
객체의 생성과 초기화는 분리하는 것이 좋다. 생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다. 반면에 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행한다. 따라서, 생성자 안에서 무거운 초기화 작업을 함께 하는 것 보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다.
>
>초기화는 객체의 동작이다. 객체 생성은 메모리 할당까지로 한정하는 것이 좋다. 필수 데이터 세팅까지만하고, 실제 동작하는 것은 초기화 메서드를 따로 실행하는 것이 좋다.


### 2-2. 인터페이스 InitializingBean, DisposableBean 

`InitializingBean` 인터페이스는 `afterPropertiesSet()` 메서드로 초기화를 지원하고, `DisposableBean` 인터페이스는 `destroy()` 메서드로 소멸을 지원한다. 

아래 예시 코드를 살펴보자. `NetworkClient` 클래스가 `InitializingBean`와 `DisposableBean`를 상속 받아, `afterPropertiesSet()`와 `destroy()`를 `@Override`하여 사용한다.


```java
public class NetworkClient implements InitializingBean, DisposableBean {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    // 서비스 시작 시 호출 메서드
    public void connect() {
        System.out.println("connect = " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " / message: " + message);
    }

    // 서비스 종료 시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("NetworkClient.afterPropertiesSet");
        connect();
        call("초기화 연결 메시지");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("NetworkClient.destroy");
        disconnect();
    }
}
```

실행 결과를 확인해보자. 초기화 메서드가 의존 관계 주입 완료 후에 호출되었다. 또한, 스프링 컨테이너 종료 직전에 소멸 메서드가 호출되었다.

![](https://images.velog.io/images/woply/post/492ffaed-ffc6-4222-a3b4-f2ff895257f2/image.png)


인터페이스를 이용한 방식은 스프링 프레임워크 안에서만 사용이 가능하다는 단점이 있다. 또한 초기화, 소멸 메서드의 이름을 변경할 수 없음으로, 외부 라이브러리에는 적용이 불가하다.

인터페이스를 이용하는 방식은 스프링 초창기에 사용하던 방법이다. 현재는 더 나은 방법들이 있어서 거의 사용하지 않는다.



### 2-3. 빈 등록 초기화, 소멸 메서드 지정 

설정 정보에 아래와 같은 방식으로 직접 초기화, 소멸 메서드를 지정하여 사용할 수 있다.
`@Bean(initMethod = "init", destroyMethod = "close")`

아래 예시 코드를 보면 `@Configuration` 클래스의 `networkClient()`에 콜백 메서드 정보를 추가한 것을 볼 수 있다. 초기화 메서드를 `init`으로, 소멸 메서드를 `close`로 설정하였다.

```java
public class BeanLifeCycleTest {

    @Test
    public void lifeCycleTest() {
        ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
        NetworkClient bean = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig {
        @Bean(initMethod = "init", destroyMethod = "close")
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
    }
}
```

```java
public class NetworkClient {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    // 서비스 시작 시 호출 메서드
    public void connect() {
        System.out.println("connect = " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " / message: " + message);
    }

    // 서비스 종료 시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
    
    // 콜백 메서드 
    public void init() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }
    
    // 콜백 메서드
    public void close() {
        System.out.println("NetworkClient.close");
        disconnect();
    }
}
```

실행 결과, 의존 관계 주입이 완료된 후에 초기화 메서드가 실행되었다. 정상적으로 URL 정보가 출력되었음을 확인 할 수 있다.

![](https://images.velog.io/images/woply/post/bbfa17da-67f1-4539-bbbb-f6a515a6238f/image.png)

설정 정보 방식은 메서드 이름을 자유롭게 지정할 수 있다. 스프링 Bean이 스프링 코드에 의존하지도 않는다. 특정 메서드를 설정 정보에 등록하여 사용하는 방식이기 떄문에 코드 수정이 불가한 외부 라이브러리에도 초기화, 소멸 메서드를 적용할 수 있다.

또한, 한가지 장점이 더 있다. 종료 메서드를 지정하는 `destroyMethod`는 기본 값이 inferred이다. 추론이라는 의미로, `close`나 `shutdown`처럼 소멸 콜백 메서드에 주로 사용하는 이름을 찾아서 자동으로 호출한다. 따라서, 직접 스프링 Bean으로 등록하지 않아도 동작한다. 만약, 추론 기능의 사용을 원하지 않는다면, `destroyMethod=""`을 이용하여 빈 공백을 지정해야 한다.



### 2-4. 애노테이션 @PostConstruct, @PreDestroy  

스프링에서 초기화 콜백 메서드와 소멸 콜백 메서드를 사용하는 가장 편리한 방법은 `@PostConstruct`, `@PreDestroy` 애노테이션을 이용하는 것이다.

`@PostConstruct`, `@PreDestroy`은 최신 스프링에서 가장 권장하는 방법이다. 매서드 위에 애노테이션 하나만 붙이면 되으로, 사용법이 매우 간단하다. 또한, 스프링에 종속적인 기술이 아니라 SR-250 라는 자바 표준이다. 따라서, 스프링이 아닌 다른 컨테이너에서도 동작한다. 애노케이션의 패키지를 보면 `javax.annotation.PostConstruct`으로 표기된 것을 확인할 수 있다.

애노테이션 방식은 @Bean을 이용하지 않는다. 따라서 컴포넌트 스캔 방식과 함께 사용할 때 더욱 유용한다. 

유일한 단점은 외부 라이브러리에 적용할 수 없다는 점이다. 외부 라이브러리를 초기화, 종료 콜백 메서드가 필요하다면 `@Bean`의 기능을 사용하는 것이 좋다. 

아래 예시 코드를 통해 사용법을 살펴보자.

```java 
public class BeanLifeCycleTest {

    @Test
    public void lifeCycleTest() {
        ConfigurableApplicationContext ac = new AnnotationConfigApplicationContext(LifeCycleConfig.class);
        NetworkClient bean = ac.getBean(NetworkClient.class);
        ac.close();
    }

    @Configuration
    static class LifeCycleConfig {
        @Bean
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
        }
    }
}
```

```java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class NetworkClient {
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url = " + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    // 서비스 시작 시 호출 메서드
    public void connect() {
        System.out.println("connect = " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " / message: " + message);
    }

    // 서비스 종료 시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }

    // 콜백 메서드
    @PostConstruct
    public void init() {
        System.out.println("NetworkClient.init");
        connect();
        call("초기화 연결 메시지");
    }

    // 콜백 메서드
    @PreDestroy
    public void close() {
        System.out.println("NetworkClient.close");
        disconnect();
    }
}
```


실행 결과를 보면, 의존 관계 주입 이후 `init()`이 실행되어 정상적으로 URL이 출력된다. 또한, 컨테이너 종료 직전에 `close()`가 실행되었다.

![](https://images.velog.io/images/woply/post/175c0a59-68d5-436b-a728-0bd355d8a418/image.png)


---

## 3. 요약
- `@PostConstruct`, `@PreDestroy` 애노테이션을 사용하는 것이 좋다. 
- 다만, 코드 수정이 불가능한 외부 라이브러리의 초기화와 소멸 콜백 메서드가 필요할 경우, @Bean의 `initMethod`, `destroyMethod`를 사용하면 된다. 

