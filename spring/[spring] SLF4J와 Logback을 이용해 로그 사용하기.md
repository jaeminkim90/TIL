> ### 📖 ✏️ 
> **이 글은 스프링 부트 라이브러리가 기본으로 제공하는 `SLF4J`와 `Logback`을 이용해 로그를 사용하는 방법을 학습하고 정리한 포스팅이다.**

---

# SLF4J와 Logback을 이용해 로그 사용하기

운영 시스템에서는 `System.out.println()`같은 시스템 콘솔을 사용해서 필요한 정보를 출력하지 않는다. 별도의 로깅 라이브러리를 사용해서 로그를 출력한다. 최소한의 로그 사용법을 알아보자. 

---

## 1. 어떤 로깅 라이브러리를 사용해야 할까?

스프링 부트 라이브러리를 사용하면 스프링 부트 로깅 라이브러리`spring-boot-starter-logging`가 함께 포함된다. 스프링 부트 로깅 라이브러리는 `SLF4J`와 `Logback` 로깅 라이브러리를 사용한다. 

- SLF4J - http://www.slf4j.org 
- Logback - http://logback.qos.ch 

로그 라이브러리는 `Logback`, `Log4J`, `Log4J2` 등등 수 많은 라이브러리가 존재한다. 다양한 로그 라이브러리를 통합해서 인터페이스로 제공하는 것이 `SLF4J` 라이브러리다.

`SLF4J`는 인터페이스이고, 그 구현체로 `Logback` 같은 로그 라이브러리를 선택하면 된다. 실무에서는 대부분 스프링 부트가 기본으로 제공하는 `Logback`을 사용한다고 한다. 

---

## 2. 로그 선언 방법

```java
private Logger log = LoggerFactory.getLogger(getClass()); 
private static final Logger log = LoggerFactory.getLogger(Xxx.class) 
```
>`@Slf4j` : 롬복을 이용하여 `SLF4J` 사용 가능 



---

## 3. 로그 호출 방법

- `log.info("hello")` 
- `System.out.println("hello")`


**ex) LogTestController **
```java
package hello.springmvc.basic;

import lombok.extern.slf4j.Slf4j;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
public class LogTestController {

    // @Slf4j가 있으면 해당 역할을 대신 한다.
    // private final Logger log = LoggerFactory.getLogger(getClass());

    @RequestMapping("/log-test")
    public String logTest() {
        String name = "Spring";

        log.trace("trace log = {}", name);
        log.debug("debug log = {}", name);
        log.info("info log = {}", name);
        log.warn("warn log = {}", name);
        log.error("error log = {}", name);

        return "ok";
    }
}

```

**테스트 실행 URL**
http://localhost:8080/log-test 


### LogTestController 매핑 정보 

**@RestController**
- `@Controller`는 반환 값이 `String`이면 뷰 이름으로 인식된다. 그래서 뷰를 찾고 뷰가 랜더링 된다. 
- `@RestController`는 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 바로 입력한다. 따라서 실행 결과로 ok 메세지를 받을 수 있다.
  

### 로그 옵션

1. **로그가 출력되는 포맷**
- 시간, 로그 레벨, 프로세스 ID, 쓰레드 명, 클래스명, 로그 메시지 
	  
2. **로그 레벨 설정을 변경해서 출력 결과를 다르게 할 수 있다.**
- **LEVEL**: TRACE > DEBUG > INFO > WARN > ERROR
- 개발 서버는 `debug` 출력 
- 운영 서버는 `info` 출력 
	 

---

## 4. 로그 레벨을 설정하는 방법 

**`application.properties`**

```java
#전체 로그 레벨 설정(기본 info)
logging.level.root=info 

#hello.springmvc 패키지와 그 하위 로그 레벨 설정 
logging.level.hello.springmvc=debug 
```

---

## 5. 올바른 로그 사용법 

**`log.debug("data="+data)`와 같은 방식은 사용하지 않는다**
- 로그 출력 레벨을 `info`로 설정해도, 실제로는 해당 코드에 있는 `"data="+data`가 실행된다. 결과적으로 문자 연산이 발생한다. 의도치 않게 불필요한 연산 작업을 수행시키는 꼴이다. 로그가 많다면, 문제가 발생할 여지가 있다.
	  
**`log.debug("data={}", data)`와 같은 방식을 사용한다**
- 로그 출력 레벨을 `info`로 설정하면 아무일도 발생하지 않는다.  따라서, 의미없는 연산이 발생하지 않는다. 더 효율적으로 리소스를 사용할 수 있다.
	  
---

## 6. 로그 사용시 장점 
시스템 콘솔로 직접 출력하는 것 보다 로그를 사용하면 다음과 같은 장점이 있다. 실무에서는 항상 로그를 사용한다고 한다.

**1. 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수 있다.**
**2. 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영서버에서는 출력하지 않는 등 로그 내용을 상황에 맞게 조절할 수 있다.**
**3. 시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등, 로그를 별도의 위치에 남길 수 있다. 특히, 파일로 남길 때는 날짜나 특정 용량을 기준으로 로그를 분할하는 것도 가능하다.** 
**4. 성능도 일반 System.out보다 좋다. (내부 버퍼링, 멀티 쓰레드 등등) 그래서 실무에서는 꼭 로그를 사용한다고 한다.** 

---

## 7. 참고 자료

로그에 대한 더 자세한 내용은 `slf4j`, `logback` 공식 사이트를 통해 찾아볼 수 있다.
- SLF4J - http://www.slf4j.org 
- Logback - http://logback.qos.ch 
	  
스프링 부트가 제공하는 로그 기능은 아래 링크에서 확인할 수 있다.
	- https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot- features.html#boot-features-logging

    