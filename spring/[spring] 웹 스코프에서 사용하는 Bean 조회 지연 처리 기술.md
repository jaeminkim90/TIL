> ### π βοΈ 
> 1. **TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---

# μΉ μ€μ½νμμ μ¬μ©νλ Bean μ‘°ν μ§μ° μ²λ¦¬ κΈ°μ 

μΉ μ€μ½νλ μΉ νκ²½μμ λμνλ μ€μ½νλ€. μ’λ₯λ ν¬κ² 4κ°μ§κ° μλ€. μ€μ½νλ§λ€ μλͺμ£ΌκΈ°μ νΉμ±μ λ€λ₯΄μ§λ§, νλ‘ν νμκ³Ό λ€λ₯΄κ² μ€νλ§μ΄ μ€μ½νμ μ’λ£μμ κΉμ§ κ΄λ¦¬νλ€. λ°λΌμ μ’λ£ λ©μλκ° νΈμΆλλ€.

> **μΉ μ€μ½ν μ’λ₯**   
> - `request`: HTTP μμ²­μ΄ λ°μν μμ λΆν°, μ’λ£λ  λκΉμ§ μ μ§λλ€. κ°κ°μ HTTP μμ²­λ§λ€ λ³λμ λΉ μΈμ€ν΄μ€κ° μμ±λκ³ , κ΄λ¦¬λλ€. 
> - `session`: HTTP Sessionκ³Ό λμΌν μλͺμ£ΌκΈ°λ₯Ό κ°μ§λ€.
> - `application`: μλΈλ¦Ώ μ»¨νμ€νΈ(ServletContext)μ λμΌν μλͺμ£ΌκΈ°λ₯Ό κ°μ§λ€. 
> - `websocket`: μΉ μμΌκ³Ό λμΌν μλͺμ£ΌκΈ°λ₯Ό κ°μ§λ€.

μΉ μ€μ½νλ μλͺμ£ΌκΈ°μ λ²μλ§ λ€λ₯΄μ§ λμνλ λ°©μμ λΉμ·νλ€.

---

## 1. μΉ μ€μ½νμμ λ°μνλ λ¬Έμ 

μΉ μ€μ½νλ μΉ νκ²½μμ λμνλ€. μΉ μ€μ½νλ μμ²­κ³Ό μλ΅μΌλ‘ λμνλ μΉ νκ²½μ νΉμ§μΌλ‘ μΈν΄ μμμΉ λͺ»ν λμμ΄ μ€νλκ±°λ, μμνλ λμμ μλ¬κ° λ°μν  μ μλ€.

μ΄λ€ λ¬Έμ κ° μλμ§ μ§μ  νμΈν΄λ³΄μ. λ¨Όμ , μΉ μ€μ½ν νμ€νΈκ° κ°λ₯νλλ‘ μΉ νκ²½μ κ΅¬μΆνλ€. `build.gradle`μ λΌμ΄λΈλ¬λ¦¬λ₯Ό μΆκ°νμ¬ web νκ²½μ κ΅¬μΆν  μ μλ€. `spring-boot-starter-web`λΌμ΄λΈλ¬λ¦¬λ₯Ό μΆκ°νλ©΄ μ€νλ§ λΆνΈλ λ΄μ₯ ν°μΌ μλ²λ₯Ό νμ©ν΄μ μΉ μλ²μ μ€νλ§μ ν¨κ» μ€νμν¨λ€. 

```java
// web λΌμ΄λΈλ¬λ¦¬ μΆκ°
implementation 'org.springframework.boot:spring-boot-starter-web'
```
```java
// ν°μΊ£ μ€ν λ‘κ·Έ
Tomcat started on port(s): 8080 (http) with context path ''
Started CoreApplication in 0.914 seconds (JVM running for 1.528)
```

### 1-1. request μ€μ½νλ μμ²­μ΄ μμΌλ©΄ μμ±λμ§ μλλ€.

`request` μ€μ½νλ `HTTP` μμ²­μ΄ λ°μν μμ λΆν°, μ’λ£ μμ κΉμ§ μ μ§λλ€. `request` μ€μ½νλ₯Ό μ΄μ©νμ¬ μλμ κ°μ΄ `HTTP request` μμ²­μ λ°λΌ μλ‘μ΄ κ°μ²΄κ° μμ±λκ³  κ΄λ¦¬λλ κ³Όμ μ νμ€νΈν΄λ³΄μ. κΈ°λνλ κ²°κ³Όλ¬Όμ μλμ κ°λ€

![](https://images.velog.io/images/woply/post/fcda04c2-1c01-4fc5-9a4d-859e7fe66efe/image.png)



```java
// κΈ°λνλ λ‘κ·Έ
// λ‘κ·Έ ν¬λ§·: [UUID][requestURL] {message} 
// UUIDλ₯Ό μ¬μ©ν΄μ HTTP μμ²­μ κ΅¬λΆ 
// requestURL μ λ³΄λ μΆκ°λ‘ λ£μ΄μ μ΄λ€ URLμ μμ²­ν΄μ λ¨μ λ‘κ·ΈμΈμ§ νμΈ
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

νμ€νΈ μ½λλ μλμ κ°λ€.

**MyLogger**

`MyLogger`λ λ‘κ·Έλ₯Ό μΆλ ₯νλ ν΄λμ€λ€. `@Scope(value = "request")`λ₯Ό μ¬μ©ν΄μ `request`μ€μ½νλ‘ μ§μ νλ€. λ°λΌμ, μ΄ Beanμ HTTP μμ²­ λΉ νλμ© μμ±λκ³ , HTTP μμ²­μ΄ λλλ μμ μ μλ©Έλλ€. `MyLogger`λ μμ±λλ μμ μ μλμΌλ‘ `@PostConstruct` μ΄κΈ°ν λ©μλλ₯Ό νΈμΆνμ¬ uuidλ₯Ό μμ±νλ€. HTTP μμ²­ λΉ νλμ© μμ±λλ―λ‘, uuidλ₯Ό ν΅ν΄ λ€λ₯Έ HTTP μμ²­κ³Ό κ΅¬λΆν  μ μλ€. μ΄ Beanμ΄ μλ©Έλλ μμ μλ `@PreDestroy`κ° νΈμΆλμ΄ μ’λ£ λ©μμ§λ₯Ό λ¨κΈ΄λ€. `requestURL`μ Beanμ΄ μμ±λλ μμ μλ μ μ μμΌλ―λ‘, μΈλΆμμ `setter`λ‘ μλ ₯ λ°λλ€.

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

`LogDemoController`μ λ‘κ±°κ° μ μλνλμ§ νμΈνλ νμ€νΈμ© μ»¨νΈλ‘€λ¬λ€. `LogDemoController`κ° `HttpServletRequest`λ₯Ό μ΄μ©νμ¬ μμ²­ URLμ λ°λλ€. `requestURL` κ°μ λ°μΌλ©΄ `MyLogger` κ°μ²΄μ μ μ₯νλ€. `MyLogger` κ°μ²΄λ `HTTP` μμ²­ λ¨μλ‘ μμ±λλ―λ‘, λ€λ₯Έ `HTTP` μμ²­μΌλ‘ μΈν΄ URL κ°μ΄ λμ²΄λ  μΌλ €λ μλ€. URLμ μ μ₯ν νμ `MyLogger` κ°μ²΄λ₯Ό μ΄μ©νμ¬ "controller test"λΌλ λ‘κ·Έλ₯Ό λ¨κΈ΄λ€.

```java
@Controller
@RequiredArgsConstructor // μλμΌλ‘ νλ λ³μ μμ‘΄ κ΄κ³λ₯Ό μ£Όμνλ μμ±μλ₯Ό λ§λ€μ΄ μ€λ€.
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
λΉμ¦λμ€ λ‘μ§μ΄ μλ μλΉμ€ κ³μΈ΅μμλ λ‘κ·Έλ₯Ό μΆλ ₯νλ€. `request` μ€μ½νλ₯Ό κ°μ§ `MyLogger`κ° μμ΄ μλΉμ€ κ³μΈ΅μ μΉ κΈ°μ μ μ’μλμ§ μκ³ , μ½λλ₯Ό κΉλνκ² μ μ§ν  μ μλ€.  

> μ°Έκ³    
> `request` μ€μ½νλ₯Ό μ¬μ©νμ§ μκ³  νλΌλ―Έν°λ‘ μ΄ λͺ¨λ  μ λ³΄λ₯Ό μλΉμ€ κ³μΈ΅μ λκΈ΄λ€λ©΄, νλΌλ―Έν°κ° λ§μμ μ§μ λΆν΄μ§λ€. λν, μΉκ³Ό κ΄λ ¨λ μ λ³΄κ° μΉκ³Ό κ΄λ ¨μλ μλΉμ€ κ³μΈ΅κΉμ§ λμ΄κ°κ² λλ€. μΉκ³Ό κ΄λ ¨λ λΆλΆμ μ»¨νΈλ‘€λ¬κΉμ§λ§ μ¬μ©νλ κ²μ΄ μ’λ€. μλΉμ€ κ³μΈ΅μ μΉ κΈ°μ μ μ’μλμ§ μκ³ , κ°κΈμ  μμνκ² μ μ§νλ κ²μ΄ μ μ§λ³΄μ μΈ‘λ©΄μμ μ λ¦¬νλ€.

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


μ€νμ ν΄λ³΄λ©΄ μλμ κ°μ΄ κΈ°λνλ κ²°κ³Όλ₯Ό μ»μ μ μλ€.


**κΈ°λνλ μΆλ ₯ κ²°κ³Ό**
```
[d06b992f...] request scope bean create
[d06b992f...][http://localhost:8080/log-demo] controller test
[d06b992f...][http://localhost:8080/log-demo] service id = testId
[d06b992f...] request scope bean close
```

**μ νλ¦¬μΌμ΄μ μ€ν μμ μ μ€λ₯κ° λ°μνλ€**
`request` μ€μ½ν Beanμ `HTTP` μμ²­μ΄ λ°μν΄μΌ μμ±λλ€. λ°λΌμ, μ€μ  μμ²­μ΄ λ°μνμ§ μμ μμ μμλ Beanμ μμ±ν  μ μλ€. μλμ κ°μ μλ¬κ° λ°μνλ€.
```
Error creating bean with name 'myLogger': Scope 'request' is not active for the current thread; consider defining a scoped proxy for this bean if you intend to refer to it from a singleton;
```

---

## 2. HTTP Request μμ΄ Request Beanμ μ¬μ©νλ λ°©λ²

μ΄λ₯Ό ν΄κ²°νλ λ°©λ²μ λ κ°μ§κ° μλ€.   

**1. `Provider`λ₯Ό μ¬μ© νλ λ°©λ²**
**2. `νλ‘μ`λ₯Ό μ¬μ©νλ λ°©λ²**


### 2-1. Providerλ₯Ό μ¬μ© νλ λ°©λ²

μλμ κ°μ΄ `Provider`λ₯Ό μ¬μ©νλ©΄, `MyLogger` κ°μ²΄κ° Beanμ λ±λ‘λλ κ²μ΄ μλλΌ, `MyLogger`λ₯Ό μ‘°νν  μ μλ `ObjectProvider` κ°μ²΄κ° BeanμΌλ‘ λ±λ‘λλ€. λ°λΌμ, `Controller`μ μμ‘΄ κ΄κ³ μ£Όμμ΄ κ°λ₯νλ€.

```java
// λ³κ²½ μ 
private final MyLogger myLoggerProvideer;

// λ³κ²½ ν
private final ObjectProvider<MyLogger> myLoggerProvideer;
```

μ΄ν μμ²­μ΄ λ°μνλ©΄, Controllerμμ μμ²­ κ²½λ‘κ° λ§΅νλμ΄ μλ `MyLogger`λ₯Ό μ‘°ννλ€. μ‘°νλ₯Ό ν΅ν΄ μλ‘μ΄ Beanμ΄ μμ±λκ³ , `myLogger` μ°Έμ‘° λ³μλ μ΄κΈ°νλμ΄ μ¬μ©μ΄ κ°λ₯νλ€. 

```java
@RequestMapping("log-demo")
@ResponseBody
public String logDemo(HttpServletRequest request) {
    MyLogger myLogger = myLoggerProvider.getObject(); // myLoggerλ₯Ό λ°λλ€

    String requestURL = request.getRequestURI().toString();
    myLogger.setRequestURL(requestURL);

    myLogger.log("controller test");
    logDemoService.logic("testId");
    return  "OK";
}
```

`LogDemoService`μμλ `MyLogger` κ°μ²΄λ₯Ό μ¬μ©νλ―λ‘, λμΌνκ² μ½λλ₯Ό μμ νλ€

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

μ½λ μμ  ν `http://localhost:8080/log-demo`μ μ μνμ¬ λ‘κ·Έλ₯Ό νμΈν΄λ³΄λ©΄, μλμ κ°μ΄ μμ²­λ§λ€ μλ‘μ΄ uuidμ ν¨κ» Beanμ μμ±κ³Ό μλ©Έ κ³Όμ μ λ‘κ·Έλ‘ λ³Ό μ μλ€.

![](https://images.velog.io/images/woply/post/8f7466c3-ac72-4ca9-a4eb-b79fe8ed1c97/image.png)

λμμλ¦¬λ₯Ό κ°λ¨ν μ΄ν΄λ³΄λ©΄, `MyLogger` κ°μ²΄λ `getObject()`κ° νΈμΆλλ μμ μ λ§λ€μ΄μ§λ€. κ·Έλ¦¬κ³  `MyLogger`κ° μμ±λλ©΄μ `@PostConstruct`κ° μ μΈλμ΄ μλ `init()`μ΄ νΈμΆλλ€. `init()`μ λ΄λΆ μ½λλ `UUID`λ₯Ό μμ±νκ³ , νλ λ³μμ μ΄κΈ°ν νλ€. μμ±κ³Ό λμμ `UUID`κ° ν¬ν¨λ λ¬Έμμ΄ λ‘κ·Έλ₯Ό μ»μ μ μλ μ΄μ λ€.

μ€μν μ μ **κ°μ HTTP μμ²­μ΄λΌλ©΄ `Controller`μμ νΈμΆνλ, `Service`μμ νΈμΆνλ λμΌν Beanμ΄ νΈμΆλλ€λ μ **μ΄λ€. μ΄λ `UUID`μ μ°Έμ‘°κ°μ μΌμΉ μ¬λΆλ‘ νμΈν  μ μλ€.


### 2-2. νλ‘μλ₯Ό μ¬μ© νλ λ°©λ²

νλ‘μλ₯Ό μ¬μ©νμ¬ μ€μ½ν λ¬Έμ λ₯Ό ν΄κ²°ν  μλ μλ€.

νλ‘μλ₯Ό μ¬μ©νκΈ° μν΄μλ λ¨Όμ  μλμ κ°μ΄`@Scope`μ `proxyMode = ScopedProxyMode.TARGET_CLASS`λ₯Ό μΆκ°ν΄μΌ νλ€. μ΄λ, μ μ© λμμ΄ ν΄λμ€λ©΄ `TARGET_CLASS`λ₯Ό μ μΈνκ³ , μΈν°νμ΄μ€λ©΄ `INTERFACES`λ₯Ό μ μΈνλ€. μ΄λ κ² νλ©΄, `MyLogger`μ κ°μ§ νλ‘μ ν΄λμ€λ₯Ό λ§λ€μ΄λκ³  `HTTP request`μ μκ΄ μμ΄ κ°μ§ νλ‘μ ν΄λμ€λ₯Ό λ€λ₯Έ Beanμ λ―Έλ¦¬ μμ‘΄ κ΄κ³λ‘ μ£Όμν  μ μλ€.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class MyLogger {
}
```


μ μ²΄ μ½λμ νλ‘μλ₯Ό λ°©μμ μ μ©ν΄λ³΄μ. `@Scope` μ λΈνμ΄μμ λ€μκ³Ό κ°μ΄ λ³κ²½νμλ€. `@Scope(value = "request", proxyMode = copedProxyMode.TARGET_CLASS)`

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

`Controller`λ λ§μΉ `MyLogger` κ°μ²΄λ₯Ό μ§μ  νΈμΆνλ λ― μ¬μ©ν  μ μλ€.

```java
@Controller
@RequiredArgsConstructor // μλμΌλ‘ νλ λ³μ μμ‘΄ κ΄κ³λ₯Ό μ£Όμνλ μμ±μλ₯Ό λ§λ€μ΄ μ€λ€.
public class LogDemoController {

    private final LogDemoService logDemoService;
    private final MyLogger myLogger;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) throws InterruptedException {
        String requestURL = request.getRequestURI().toString();

        // νλ‘μκ° μ μ©λ myLogger ν΄λμ€ μ λ³΄ νμΈ
        System.out.println("myLogger.getClass() = " + myLogger.getClass());
        
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        Thread.sleep(3000);
        logDemoService.logic("testId");
        return  "OK";
    }
}
```

Serviceλ λμΌνλ€.

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

μ€νν΄λ³΄λ©΄ μ λμνλ€. Providerλ₯Ό μ¬μ©ν  λμ μμ ν λμΌν κ²°κ³Όκ° λμ¨λ€.

### 2-3. νλ‘μμ λμ μλ¦¬

`myLogger` μΈμ€ν΄μ€μ ν΄λμ€ μ λ³΄λ₯Ό ν΅ν΄ νλ‘μμ λμ μλ¦¬λ₯Ό μ΄ν΄λ³΄μ.

```java
System.out.println("myLogger = " + myLogger.getClass()); 
```

**μΆλ ₯ κ²°κ³Ό**
μΆλ ₯ κ²°κ³Όλ₯Ό νμΈν΄λ³΄λ©΄, μμν `MyLogger`ν΄λμ€κ° μλλΌ `MyLogger$ $EnhancerBySpringCGLIB`λΌλ ν΄λμ€λ‘ λ§λ€μ΄μ§ κ°μ²΄κ° λμ  λ±λ‘λ κ²μ νμΈν  μ μλ€. `@Scope`μ `proxyMode = ScopedProxyMode.TARGET_CLASS`λ₯Ό μ€μ νλ©΄ μ€νλ§ μ»¨νμ΄λλ `CGLIB`λΌλ λ°μ΄νΈμ½λλ₯Ό μ‘°μνλ λΌμ΄λΈλ¬λ¦¬λ₯Ό μ¬μ©ν΄μ, `MyLogger`λ₯Ό μμλ°μ κ°μ§ νλ‘μ κ°μ²΄λ₯Ό μμ±νλ€. μ€νλ§ μ»¨νμ΄λλ μ΄ κ°μ§ νλ‘μ κ°μ²΄λ₯Ό μμ‘΄ κ΄κ³λ‘ μ£Όμνλ€.

```java
myLogger.getClass() = class hello.core.common.MyLogger$$EnhancerBySpringCGLIB$$93f44027
```


![](https://images.velog.io/images/woply/post/681ee372-589d-4d99-88c0-3e7e65e89dd7/image.png)


κ°μ§ νλ‘μ κ°μ²΄λ μ§μ§ `myLogger`λ₯Ό μ°Ύλ λ°©λ²μ μκ³  μλ€. μμ²­μ΄ λ°μνλ μμ μμ μ§μ§ Beanμ μ°Ύμ νΈμΆνλ€. λ§μ½, ν΄λΌμ΄μΈνΈκ° `myLogger.logic()`μ νΈμΆνλ©΄ κ°μ§ νλ‘μ κ°μ²΄μ λ©μλλ₯Ό νΈμΆν κ²μ΄λ€. νμ§λ§, κ°μ§ νλ‘μ κ°μ²΄κ° μ§μ§ `myLogger.logic()` λ₯Ό νΈμΆνλ€. 

νλ‘μ κ°μ²΄λ₯Ό μ¬μ©νλ©΄ ν΄λΌμ΄μΈνΈλ λ§μΉ μ±κΈν€ λΉμ μ¬μ©νλ―μ΄ νΈλ¦¬νκ² Request μ€μ½νλ₯Ό μ¬μ©ν  μ μλ€. 

---

## 3. μμ½

- μΉ μ€μ½νμ λ²μ νΉμ§μΌλ‘ μΈν΄ λ°μνλ λ¬Έμ λ Providerμ νλ‘μ λ°©λ²μΌλ‘ ν΄κ²°ν  μ μλ€.
- Providerμ νλ‘μμ ν΅μ¬ μμ΄λμ΄λ κΌ­ νμν μμ κΉμ§ μ§μ§ κ°μ²΄μ μ‘°νλ₯Ό μ§μ°μ²λ¦¬ νλ€λ μ μ΄λ€.
- μ λΈνμ΄μ μ€μ  λ³κ²½λ§μΌλ‘ μλ³Έ κ°μ²΄λ₯Ό νλ‘μ κ°μ²΄λ‘ λμ²΄ν  μ μλ€. μ΄κ²μ λ€νμ±κ³Ό DI μ»¨νμ΄λκ° κ°μ§ ν° κ°μ μ΄λ€.
- κΌ­ μΉ μ€μ½νκ° μλμ΄λ νλ‘μλ μ¬μ©ν  μ μλ€. 