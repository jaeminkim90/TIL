> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 웹 스코프에서 사용하는 Bean 조회 지연 처리 기술

웹 스코프는 웹 환경에서 동작하는 스코프다. 종류는 크게 4가지가 있다. 스코프마다 생명주기의 특성은 다르지만, 프로토타입과 다르게 스프링이 스코프의 종료시점까지 관리한다. 따라서 종료 메서드가 호출된다.

> **웹 스코프 종류**   
> - `request`: HTTP 요청이 발생한 시점부터, 종료될 때까지 유지된다. 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다. 
> - `session`: HTTP Session과 동일한 생명주기를 가진다.
> - `application`: 서블릿 컨텍스트(ServletContext)와 동일한 생명주기를 가진다. 
> - `websocket`: 웹 소켓과 동일한 생명주기를 가진다.

웹 스코프는 생명주기의 범위만 다르지 동작하는 방식은 비슷한다.

---

## 1. 웹 스코프에서 발생하는 문제

웹 스코프는 웹 환경에서 동작한다. 웹 스코프는 요청과 응답으로 동작하는 웹 환경의 특징으로 인해 예상치 못한 동작이 실행되거나, 예상했던 동작에 에러가 발생할 수 있다.

어떤 문제가 있는지 직접 확인해보자. 먼저, 웹 스코프 테스트가 가능하도록 웹 환경을 구축한다. `build.gradle`에 라이브러리를 추가하여 web 환경을 구축할 수 있다. `spring-boot-starter-web`라이브러리를 추가하면 스프링 부트는 내장 톰켓 서버를 활용해서 웹 서버와 스프링을 함께 실행시킨다. 

```java
// web 라이브러리 추가
implementation 'org.springframework.boot:spring-boot-starter-web'
```
```java
// 톰캣 실행 로그
Tomcat started on port(s): 8080 (http) with context path ''
Started CoreApplication in 0.914 seconds (JVM running for 1.528)
```

### 1-1. request 스코프는 요청이 없으면 생성되지 않는다.

`request` 스코프는 `HTTP` 요청이 발생한 시점부터, 종료 시점까지 유지된다. `request` 스코프를 이용하여 아래와 같이 `HTTP request` 요청에 따라 새로운 객체가 생성되고 관리되는 과정을 테스트해보자. 기대하는 결과물은 아래와 같다

![](https://images.velog.io/images/woply/post/fcda04c2-1c01-4fc5-9a4d-859e7fe66efe/image.png)



```java
// 기대하는 로그
// 로그 포맷: [UUID][requestURL] {message} 
// UUID를 사용해서 HTTP 요청을 구분 
// requestURL 정보도 추가로 넣어서 어떤 URL을 요청해서 남은 로그인지 확인
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

테스트 코드는 아래와 같다.

**MyLogger**

`MyLogger`는 로그를 출력하는 클래스다. `@Scope(value = "request")`를 사용해서 `request`스코프로 지정했다. 따라서, 이 Bean은 HTTP 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸된다. `MyLogger`는 생성되는 시점에 자동으로 `@PostConstruct` 초기화 메서드를 호출하여 uuid를 생성한다. HTTP 요청 당 하나씩 생성되므로, uuid를 통해 다른 HTTP 요청과 구분할 수 있다. 이 Bean이 소멸되는 시점에는 `@PreDestroy`가 호출되어 종료 메시지를 남긴다. `requestURL`은 Bean이 생성되는 시점에는 알 수 없으므로, 외부에서 `setter`로 입력 받는다.

```java
@Component
@Scope(value = "request")
public class MyLogger {

    private String uuid;
    private  String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" +requestURL+ "] - " +  message );
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this );
    }

    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this );
    }
}
```


**LogDemoController**

`LogDemoController`은 로거가 잘 작동하는지 확인하는 테스트용 컨트롤러다. `LogDemoController`가 `HttpServletRequest`를 이용하여 요청 URL을 받는다. `requestURL` 값을 받으면 `MyLogger` 객체에 저장한다. `MyLogger` 객체는 `HTTP` 요청 단위로 생성되므로, 다른 `HTTP` 요청으로 인해 URL 값이 대체될 염려는 없다. URL을 저장한 후에 `MyLogger` 객체를 이용하여 "controller test"라는 로그를 남긴다.

```java
@Controller
@RequiredArgsConstructor // 자동으로 필드 변수 의존 관계를 주입하는 생성자를 만들어 준다.
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {

        String requestURL = request.getRequestURI().toString();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return  "OK";
    }
}
```


**Service**
비즈니스 로직이 있는 서비스 계층에서도 로그를 출력한다. `request` 스코프를 가진 `MyLogger`가 있어 서비스 계층은 웹 기술에 종속되지 않고, 코드를 깔끔하게 유지할 수 있다.  

> 참고   
> `request` 스코프를 사용하지 않고 파라미터로 이 모든 정보를 서비스 계층에 넘긴다면, 파라미터가 많아서 지저분해진다. 또한, 웹과 관련된 정보가 웹과 관련없는 서비스 계층까지 넘어가게 된다. 웹과 관련된 부분은 컨트롤러까지만 사용하는 것이 좋다. 서비스 계층은 웹 기술에 종속되지 않고, 가급적 순수하게 유지하는 것이 유지보수 측면에서 유리하다.

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;

    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```


실행을 해보면 아래와 같이 기대했던 결과를 얻을 수 없다.


**기대했던 출력 결과**
```
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

**애플리케이션 실행 시점에 오류가 발생한다**
`request` 스코프 Bean은 `HTTP` 요청이 발생해야 생성된다. 따라서, 실제 요청이 발생하지 않은 시점에서는 Bean을 생성할 수 없다. 아래와 같은 에러가 발생한다.
```
Error creating bean with name 'myLogger': Scope 'request' is not active for the current thread; consider defining a scoped proxy for this bean if you intend to refer to it from a singleton;
```

---

## 2. HTTP Request 없이 Request Bean을 사용하는 방법

이를 해결하는 방법은 두 가지가 있다.   

**1. `Provider`를 사용 하는 방법**
**2. `프록시`를 사용하는 방법**


### 2-1. Provider를 사용 하는 방법

아래와 같이 `Provider`를 사용하면, `MyLogger` 객체가 Bean에 등록되는 것이 아니라, `MyLogger`를 조회할 수 있는 `ObjectProvider` 객체가 Bean으로 등록된다. 따라서, `Controller`에 의존 관계 주입이 가능하다.

```java
// 변경 전
private final MyLogger myLoggerProvideer;

// 변경 후
private final ObjectProvider<MyLogger> myLoggerProvideer;
```

이후 요청이 발생하면, Controller에서 요청 경로가 맵핑되어 있는 `MyLogger`를 조회한다. 조회를 통해 새로운 Bean이 생성되고, `myLogger` 참조 변수는 초기화되어 사용이 가능하다. 

```java
@RequestMapping("log-demo")
@ResponseBody
public String logDemo(HttpServletRequest request) {
    MyLogger myLogger = myLoggerProvider.getObject(); // myLogger를 받는다

    String requestURL = request.getRequestURI().toString();
    myLogger.setRequestURL(requestURL);

    myLogger.log("controller test");
    logDemoService.logic("testId");
    return  "OK";
}
```

`LogDemoService`에서도 `MyLogger` 객체를 사용하므로, 동일하게 코드를 수정한다

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final ObjectProvider<MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```

코드 수정 후 `http://localhost:8080/log-demo`에 접속하여 로그를 확인해보면, 아래와 같이 요청마다 새로운 uuid와 함께 Bean의 생성과 소멸 과정을 로그로 볼 수 있다.

![](https://images.velog.io/images/woply/post/8f7466c3-ac72-4ca9-a4eb-b79fe8ed1c97/image.png)

동작원리를 간단히 살펴보면, `MyLogger` 객체는 `getObject()`가 호출되는 시점에 만들어진다. 그리고 `MyLogger`가 생성되면서 `@PostConstruct`가 선언되어 있는 `init()`이 호출된다. `init()`의 내부 코드는 `UUID`를 생성하고, 필드 변수에 초기화 한다. 생성과 동시에 `UUID`가 포함된 문자열 로그를 얻을 수 있는 이유다.

중요한 점은 **같은 HTTP 요청이라면 `Controller`에서 호출하던, `Service`에서 호출하던 동일한 Bean이 호출된다는 점**이다. 이는 `UUID`와 참조값의 일치 여부로 확인할 수 있다.


### 2-2. 프록시를 사용 하는 방법

프록시를 사용하여 스코프 문제를 해결할 수도 있다.

프록시를 사용하기 위해서는 먼저 아래와 같이`@Scope`에 `proxyMode = ScopedProxyMode.TARGET_CLASS`를 추가해야 한다. 이때, 적용 대상이 클래스면 `TARGET_CLASS`를 선언하고, 인터페이스면 `INTERFACES`를 선언한다. 이렇게 하면, `MyLogger`의 가짜 프록시 클래스를 만들어두고 `HTTP request`와 상관 없이 가짜 프록시 클래스를 다른 Bean에 미리 의존 관계로 주입할 수 있다.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
}
```


전체 코드에 프록시를 방식을 적용해보자. `@Scope` 애노테이션을 다음과 같이 변경하였다. `@Scope(value = "request", proxyMode = copedProxyMode.TARGET_CLASS)`

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS )
public class MyLogger {

    private String uuid;
    private  String requestURL;

    public void setRequestURL(String requestURL) {
        this.requestURL = requestURL;
    }

    public void log(String message) {
        System.out.println("[" + uuid + "]" + "[" +requestURL+ "] - " +  message );
    }

    @PostConstruct
    public void init() {
        uuid = UUID.randomUUID().toString();
        System.out.println("[" + uuid + "] request scope bean create:" + this );
    }

    @PreDestroy
    public void close() {
        System.out.println("[" + uuid + "] request scope bean close:" + this );
    }
}
```

`Controller`도 마치 `MyLogger` 객체를 직접 호출하는 듯 사용할 수 있다.

```java
@Controller
@RequiredArgsConstructor // 자동으로 필드 변수 의존 관계를 주입하는 생성자를 만들어 준다.
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) throws InterruptedException {
        String requestURL = request.getRequestURI().toString();

        // 프록시가 적용된 myLogger 클래스 정보 확인
        System.out.println("myLogger.getClass() = " + myLogger.getClass());
        
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        Thread.sleep(3000);
        logDemoService.logic("testId");
        return  "OK";
    }
}
```

Service도 동일하다.

```java
@Service
@RequiredArgsConstructor
public class LogDemoService {

    private final MyLogger myLogger;

    public void logic(String id) {
        myLogger.log("service id = " + id);
    }
}
```

실행해보면 잘 동작한다. Provider를 사용할 때와 완전히 동일한 결과가 나온다.

### 2-3. 프록시의 동작 원리

`myLogger` 인스턴스의 클래스 정보를 통해 프록시의 동작 원리를 살펴보자.

```java
System.out.println("myLogger = " + myLogger.getClass()); 
```

**출력 결과**
출력 결과를 확인해보면, 순수한 `MyLogger`클래스가 아니라 `MyLogger$ $EnhancerBySpringCGLIB`라는 클래스로 만들어진 객체가 대신 등록된 것을 확인할 수 있다. `@Scope`에 `proxyMode = ScopedProxyMode.TARGET_CLASS`를 설정하면 스프링 컨테이너는 `CGLIB`라는 바이트코드를 조작하는 라이브러리를 사용해서, `MyLogger`를 상속받은 가짜 프록시 객체를 생성한다. 스프링 컨테이너는 이 가짜 프록시 객체를 의존 관계로 주입한다.

```java
myLogger.getClass() = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$93f44027
```


![](https://images.velog.io/images/woply/post/681ee372-589d-4d99-88c0-3e7e65e89dd7/image.png)


가짜 프록시 객체는 진짜 `myLogger`를 찾는 방법을 알고 있다. 요청이 발생하는 시점에서 진짜 Bean을 찾아 호출한다. 만약, 클라이언트가 `myLogger.logic()`을 호출하면 가짜 프록시 객체의 메서드를 호출한 것이다. 하지만, 가짜 프로시 객체가 진짜 `myLogger.logic()` 를 호출한다. 

프록시 객체를 사용하면 클라이언트는 마치 싱글톤 빈을 사용하듯이 편리하게 Request 스코프를 사용할 수 있다. 

---

## 3. 요약

- 웹 스코프의 범위 특징으로 인해 발생하는 문제는 Provider와 프록시 방법으로 해결할 수 있다.
- Provider와 프록시의 핵심 아이디어는 꼭 필요한 시점까지 진짜 객체의 조회를 지연처리 한다는 점이다.
- 애노테이션 설정 변경만으로 원본 객체를 프록시 객체로 대체할 수 있다. 이것은 다형성과 DI 컨테이너가 가진 큰 강점이다.
- 꼭 웹 스코프가 아니어도 프록시는 사용할 수 있다. 