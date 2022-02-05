> ### 📖 ✏️ 
> **이 글은 스프링이 제공하는 HTTP 요청 데이터의 조회 방법과 활용 방법을 학습하고 정리한 포스팅이다.**

---

# HTTP 요청 데이터를 조회하고, 사용하는 거의 모든 방법

---

## 1. HTTP Request 헤더 정보를 조회하는 방법


애노테이션 기반의 스프링 컨트롤러는 다양한 파라미터를 지원한다. 컨트롤러 메서드의 파라미터로 HTTP Request 헤더 정보를 조회하는 방법을 알아보자.


**RequestHeaderController** 

```java
package hello.springmvc.basic.request;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpMethod;
import org.springframework.util.MultiValueMap;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.RequestHeader;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Locale;

@Slf4j
@RestController
public class RequestHeaderController {

    @RequestMapping("/headers")
    public String headers(HttpServletRequest request,
                          HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale, // 언어 정보
                          @RequestHeader MultiValueMap<String, String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false) String cookie
    ) {

        log.info("request={}", request);
        log.info("response={}", response);
        log.info("httpMethod={}", httpMethod);
        log.info("locale={}", locale);
        log.info("headerMap={}", headerMap);
        log.info("header host={}", host);
        log.info("myCookie={}", cookie);
        return "OK !";
    }
}

```

> **헤더 정보를 조회하는 다양한 방법**
- `HttpServletRequest` 
- `HttpServletResponse` 
- `HttpMethod` : HTTP 메서드를 조회한다.
  - `org.springframework.http.HttpMethod`
- `Locale` : Locale 정보를 조회한다. 
- `@RequestHeader MultiValueMap<String, String> headerMap` 
  - 모든 HTTP 헤더를 `MultiValueMap` 형식으로 조회한다.
- `@RequestHeader("host") String host`
  - 특정 HTTP 헤더를 조회한다. 
  - 속성
    - 필수 값 여부: required 
    - 기본 값 속성: defaultValue   
- `@CookieValue(value = "myCookie", required = false) String cookie`
  - 특정 쿠키를 조회한다. 
  - 속성 
    - 필수 값 여부: required 
    - 기본 값: defaultValue 

**`MultiValueMap`**
- MAP과 유사하다. 다만, 하나의 키에 여러 값을 받을 수 있다는 차이가 있다. 
- HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다. 
  - ex) `keyA=value1&keyA=value2` 
	  
```java
MultiValueMap<String, String> map = new LinkedMultiValueMap();
map.add("keyA", "value1");
map.add("keyA", "value2");

//[value1,value2
List<String> values = map.get("keyA");
```


**`@Slf4j`** 
아래와 같은 코드를 자동으로 생성해서 로그를 선언한다. 개발자는 편리하게 log를 사용할 수 있다. 
```java
private static final org.slf4j.Logger log =
org.slf4j.LoggerFactory.getLogger(RequestHeaderController.class);
```

> **참고** 
> 1. `@Conroller`의 사용 가능한 파라미터 목록은 다음 공식 메뉴얼에서 확인할 수 있다. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments
> 2. `@Conroller`의 사용 가능한 응답 값 목록은 다음 공식 메뉴얼에서 확인할 수 있다. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types



---

## 2. HTTP Request 쿼리 파라미터와 HTML Form의 데이터를 사용하는 방법

클라이언트에서 서버로 요청 데이터를 전달하는 방법은 크게 3가지가 있다. 그 중 `key-value` 방식으로 데이터를 전달하는 **쿼리 파라미터**와 **HTML Form** 데이터를 사용하는 방법을 알아보자. 

> **클라이언트에서 서버로 요청 데이터를 전달하는 3가지 방법**
>
> **1. GET - 쿼리 파라미터** 
- ex) `/url?username=hello&age=20`   
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달 
- 예) 검색, 필터, 페이징등에서 많이 사용하는 방식 
>
> **2. POST - HTML Form** 
- `content-type`: `application/x-www-form-urlencoded`  
- 메시지 바디에 쿼리 파리미터 형식으로 전달 -> `username=hello&age=20` 
- 예) 회원 가입, 상품 주문, HTML Form 사용 
>
> **3. HTTP message body에 데이터를 직접 담아서 요청** 
- HTTP API에서 주로 사용
- JSON, XML, TEXT 등 다양한 데이터 형식을 사용하지만 JSON 방식을 주로 사용
- POST, PUT, PATCH에 주로 사용


### 2-1. getParameter()를 이용하는 방식
`HttpServletReques`의 `request.getParameter()`를 GET 방식의 쿼리 파라미터 데이터와 POST 방식의 HTML Form 데이터를 모두 조회할 수 있다. 두 형식이 동일하기 때문이다. 두 가지 방식을 간단히 **요청 파라미터(request parameter)**조회라고 한다. 가장 기본적인 조회 방법은 `HttpServletRequest`가 제공하는`getParameter()`를 이용하는 방법이다.

> **GET, 쿼리 파라미터 데이터 전송 방식 예시**
> `http://localhost:8080/request-param?username=hello&age=20` 

> **POST, HTML Form 데이터 전송 방식 예시**  
```java 
POST /request-param ...
content-type: application/x-www-form-urlencoded
username=hello&age=20
```


**RequestParamController** 

```java
package hello.springmvc.basic.request;

import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Slf4j
@Controller
public class RequestParamController {

    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        log.info("username={}, age={}", username, age);

        response.getWriter().write("OK!");
    }
}

```
**main/resources/static/basic/hello-form.html**
```HTML
<!DOCTYPE html>
<html>
<head>

    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<form action="/request-param-v1" method="post">
    username: <input type="text" name="username"/>
    age: <input type="text" name="age"/>
    <button type="submit">전송</button>
</form>
</body>
</html>

```

---

### 2-2. @RequestParam() - 기본 기능


스프링이 제공하는 `@RequestParam`을 사용하면 요청 파라미터를 더 편리하게 사용할 수 있다. `@RequestParam`은 다양한 기능을 제공한다. `@RequestParam`의 활용 방법을 잘 이해할 수 있도록 가장 기본적인 사용 형태부터 시작하여 단계적으로 고급 기능을 알아보자.


`@RequestParam`의 `name(value)` 속성을 이용하면 파라미터 데이터를 변수로 바로 받아서 사용할 수 있다. 
> `@RequestParam("username") String memberName`은
=> `request.getParameter("username")`과 같다.

```java
/**
 * @RequestParam 사용: 파라미터 이름으로 바인딩
 * @ResponseBody 추가: View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력 */

@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-v2")
public String requestParamV2(
        @RequestParam("username") String memberName,
        @RequestParam("age") int memberAge) {
    log.info("username={}, age={}", memberName, memberAge);
    return "OK!";
}
```

- `@RequestParam` : 파라미터 이름으로 바인딩 
- `@ResponseBody` : View 조회를 무시하고, HTTP message body에 직접 해당 내용 입력 


### 2-3. @RequestParam() - 이름 속성 생략 기능

`@RequestParam`를 사용할 때, HTTP 파라미터 이름이 변수 이름과 같으면  `@RequestParam(name="xx")`의 `(name="xx")` 속성을 생략할 수 있다. 

```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-v3")
public String requestParamV3(
        //  @RequestParam의 이름을 생략하려면 요청 파라미터로 넘어오는 이름과 @RequestParam 파라미터 변수명이 같아야 한다.
        @RequestParam String username,
        @RequestParam int age) {
    log.info("username={}, age={}", username, age);
    return "OK!";
}
```

### 2-4. @RequestParam() - 단순 타입일 때, @RequestParam 생략 기능

파라미터 정보가 단순 타입(String, int, Integer)이면 `@RequestParam`를 아예 생략할 수 있다. 단, 요청 파라미터로 넘어오는 이름과 파라미터의 변수명이 같아야 한다.

```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-v4")
public String requestParamV4(String username, int age) {  
    log.info("username={}, age={}", username, age);
    return "OK!";
}
```

> **주의**
> `@RequestParam` 애노테이션을 생략하면 스프링 MVC는 내부에서 `required=false`를 적용한다. `required`는 필수 파라미터 여부를 지정한는 옵션이다.

> **참고** 
> 애노테이션을 완전히 생략하는 것은 명확성을 해칠 수 있다. `@RequestParam`의 존재만으로 요청 파리미터에서 데이터를 읽는 다는 사실을 전달할 수 있기 때문이다. 


### 2-5. @RequestParam() - 필수 파라미터를 지정하는 기능

 
`required`를 사용하면 필수 파라미터 여부를 지정할 수 있다. `required`는 기본값이 true다. 따라서 별도로 지정하지 않을 경우 반드시 해당 파라미터가 존재해야 데이터를 조회할 수 있다.

`@RequestParam(required = true) String username`과 같이 필수 파라미터가 지정되어 있다면 `@/request-param`는 `username` 정보가 없으므로 400 예외가 발생한다. 반면, `/request-param?username=kim`은 통과한다. 만약  `username=`과 같이 아무 값도 없을 경우 빈문자로 인식되어 통과한다. 


```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-required")
public String requestParamRequired(
        // required ture일 경우, 해당 데이터가 요청 파라미터에 필수로 포함되어야 한다.
        // required는 기본값이 true다.
        @RequestParam(required = true) String username,
        @RequestParam(required = false) Integer age) { // 기본형인 int는 null 불가

    log.info("username={}, age={}", username, age);
    return "OK!";
}
```


> **주의!**
- 기본형(primitive)에 null을 입력하지 않도록 한다. `null`을 `int`에 입력하는 것은 불가능하다. 500 예외가 발생한다. 따라서, `null`을 받을 수 있는 `Integer`타입이나 기본값을 적용할 수 있는 `defaultValue`를 사용해야 한다.



### 2-6. @RequestParam() - 기본 값을 적용하는 requestParamDefault 기능


`defaultValue`를 사용하면, 파라미터 값이 없을 때 미리 지정해놓은 기본값을 사용한다. 한 가지 참고할 점은 `defaultValue`가 빈 문자의 경우에도 적용된다는 점이다.

```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-default")
public String requestParamDefault(
        // defaultValue가 있으면, 항상 기본값이 존재하기 때문에 required 옵션은 무의미해진다.
        @RequestParam(required = true, defaultValue = "guest") String username,
        @RequestParam(required = false, defaultValue = "-1") int age) {

    log.info("username={}, age={}", username, age);
    return "OK!";
}
```
defaultValue를 사용하면 기본 값이 존재하기 때문에 required는 의미가 없다. 



### 2-7. @RequestParam() - 파라미터를 Map으로 조회하는 requestParamMap 기능

`requestParamMap`을 이용하면 요청 파라미텉 정보를 Map으로 받을 수 있다. 파라미터의 값이 1개가 확실하다면 `Map`을 사용해도 되지만, 그렇지 않다면 `MultiValueMap`을 사용할 수 있다.

- **`@RequestParam Map`**
  - `Map(key=value)`
	  
- **`@RequestParam MultiValueMap`**
  - `MultiValueMap(key=[value1, value2, ...]`
  ex) (key=userIds, value=[id1, id2])


```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "OK!";
}
```


---


### 2-8. 요청 파라미터를 객체로 만드는 @ModelAttribute 

실제 개발을 하면 요청 파라미터의 데이터를 객체에 넣어주야 하는 경우가 많다. 보통 아래와 같은 코드를 작성하게 된다. 
 
```java
@ResponseBody // 리턴 String을 응답 메시지로 바로 전달한다
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "OK!";
}
```

스프링은 이 과정을 완전히 자동화하는 `@ModelAttribute` 기능을 제공한다. `@ModelAttribute`도 `@RequestParam` 처럼 다양한 세부 기능을 제공한다. 단계별로 기능을 확장하며, 사용방법을 알아보자. 

먼저, 요청 파라미터를 바인딩 받을 객체를 만들어야 한다.

```java
package hello.springmvc.basic;

import lombok.Data;

@Data
public class HelloData {
    private String username;
    private int age;
}

```

롬복 `@Data`는 아래와 같은 애노테이션을 자동으로 포함한다. 
- @Getter
- @Setter
- @ToString
- @EqualsAndHashCode
- @RequiredArgsConstructor



`@ModelAttribute` 사용하면, 마치 마법처럼 `HelloData` 객체가 생성되고, 요청 파라미터의 값도 모두 들어가 있다. `model.addAttribute(helloData)` 코드가 함께 적용되기 때문이다. 

```java
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String modelAttributeV1(@ModelAttribute HelloData helloData) {

    log.info(helloData.toString());
    log.info("helloData={}",helloData);
    log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

    return "OK!";
}
```

스프링 MVC는 `@ModelAttribute`가 있으면 아래와 같은 과정을 자동으로 실행한다. 
- `HelloData` 객체를 생성한다. 
- 요청 파라미터의 이름으로 `HelloData` 객체의 프로퍼티를 찾는다. 그리고 해당 프로퍼티의 `setter`를 호출해서 파라미터의 값을 입력(바인딩) 한다. 
  - 예) 파라미터 이름이 username 이면 setUsername() 메서드를 찾아서 호출하면서 값을 입력한다. 

> **프로퍼티(property)란?**
> 보통 필드 변수라고 한다. 객체에 `getUsername()`, `setUsername()` 메서드가 있으면, 이 객체는 `username`이라는 프로퍼티를 가지고 있는 것이다. `username` 프로퍼티의 값을 변경하면 `setUsername()`이 호출되고, 조회하면 `getUsername()`이 호출된다. 

```java
class HelloData {
    getUsername();
    setUsername();
} 
```

> 참고
> `age=abc` 처럼 숫자가 들어가야 할 곳에 문자를 넣으면 `BindException`이 발생한다.


### 2-9. @ModelAttribute의 생략 기능

`@ModelAttribute`은 생략이 가능하다. 그런데 `@RequestParam`도 생략이 가능하다보니, 어떤 것이 생략되어 동작하는지 혼란스러울 수 있다.

스프링은 해당 기능을 생략할 때 아래와 같은 규칙을 적용한다.
- String , int , Integer 같은 단순 타입 => `@RequestParam` 
- 나머지 => `@ModelAttribute` (argument resolver 로 지정해둔 타입 외) 

```java
@ResponseBody
@RequestMapping("/model-attribute-v2")
public String modelAttributeV2(HelloData helloData) {
    log.info(helloData.toString());
    log.info("helloData={}",helloData);
    log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());

    return "OK!";
}
```



---
## 3. HTTP Request 메시지 바디의 문자 데이터를 사용하는 방법

HTTP 메시지 바디를 통해 데이터가 직접 넘어오는 경우에는 `@RequestParam`과 `@ModelAttribute`를 사용할 수 없다. 물론, HTML Form 형식으로 전달되는 경우는 요청 파라미터에 해당한다. 

메시지 바디로 전달되는 단순 텍스트 데이터는 `InputStream()`를 이용하여 읽을 수 있다. 아울러, `HttpEntity`와 `RequestBody`를 사용하는 방법도 있다. 단계적으로 살펴보자.

```java
package hello.springmvc.basic.request;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpEntity;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.io.InputStream;
import java.io.Writer;
import java.nio.charset.StandardCharsets;

@Slf4j
@Controller
public class RequestBodyStringController {

    @PostMapping("/request-body-string-v1")
    public void requestBodyString(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        log.info("messageBody={}", messageBody);

        response.getWriter().write("OK!!");
    }
    
    /**
     * InputStream(Reader): HTTP 요청 메시지 바디의 내용을 직접 조회
     * OutputStream(Writer): HTTP 응답 메시지의 바디에 직접 결과 출력
     */
    @PostMapping("/request-body-string-v2")
    public void requestBodyString(InputStream inputStream, Writer responseWriter) throws IOException {

        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);
        responseWriter.write("OK!!");
    }

    /**
     * HttpEntity: HTTP header, body 정보를 편라하게 조회
     * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 *
     * 응답에서도 HttpEntity 사용 가능
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     */
    @PostMapping("/request-body-string-v3")
    public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) {
        String messageBody = httpEntity.getBody();

        log.info("httpEntity = {}", httpEntity);
        log.info("httpEntity.getBody = {} ", httpEntity.getBody());
        log.info("messageBody={}", messageBody);

        return new HttpEntity<>("ok");
    }

    /**
     * @RequestBody
     * - 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 *
     * @ResponseBody
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 */
    @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStringV4(@RequestBody String messageBody) {
        log.info("messageBody={}", messageBody);
        return "ok";
    }
}

```

### 3-1. InputStream()을 이용해 단순 텍스트 데이터 사용

아래와 같이 `getInputStream`을 이용하여 `HttpServletRequest` 객체에서 문자 데이터를 읽어낼 수 있다. 

**requestBodyString - v1**

```java
@Slf4j
@Controller
public class RequestBodyStringController {

    @PostMapping("/request-body-string-v1")
    public void requestBodyString(HttpServletRequest request, HttpServletResponse response) throws IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        log.info("messageBody={}", messageBody);

        response.getWriter().write("OK!!");
    }
```

또 다른 방법으로, 아래와 같이 `InputStream`을 직접 사용할 수도 있다. 

스프링 MVC는 다음 파라미터를 지원한다. 
- `InputStream(Reader)`: HTTP 요청 메시지 바디의 내용을 직접 조회 
- `OutputStream(Writer)`: HTTP 응답 메시지의 바디에 직접 결과 출력 

**requestBodyString - v2**
```java
/**
 * InputStream(Reader): HTTP 요청 메시지 바디의 내용을 직접 조회
 * OutputStream(Writer): HTTP 응답 메시지의 바디에 직접 결과 출력
 */
@PostMapping("/request-body-string-v2")
public void requestBodyString(InputStream inputStream, Writer responseWriter) throws IOException {

    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
    log.info("messageBody={}", messageBody);
    responseWriter.write("OK!!");
}
```



### 3-2. HttpEntity()를 이용하는 방법

`HttpEntity`를 이용하면 `HTTP header`와 `body` 정보를 한번에 조회할 수 있다. `@RequestParam`나 `@ModelAttribute`를 사용하지 않고 바디 정보를 직접 조회한다. 

내부적으로는 `HttpMessageConverter`를 사용하여 `StringHttpMessageConverter`가 호출되는 방식이다. `HttpEntity`는 요청 뿐만 아니라, 응답에도 사용할 수 있다.

View 조회 없이 메시지 바디 정보를 직접 반환한다. 응답도 마찬가지로 대부적으로 `HttpMessageConverter`가 사용되어 `StringHttpMessageConverter`가 호출되는 방식이다. 

**requestBodyString - V3** 
```java
@PostMapping("/request-body-string-v3")
public HttpEntity<String> requestBodyStringV3(HttpEntity<String> httpEntity) {
    String messageBody = httpEntity.getBody();

    log.info("httpEntity = {}", httpEntity);
    log.info("httpEntity.getBody = {} ", httpEntity.getBody());
    log.info("messageBody={}", messageBody);

    return new HttpEntity<>("ok");
}
```

참고로 `HttpEntity`를 상속받은 다음 객체들도 같은 기능을 제공한다. 

**`RequestEntity`** 
- `HttpMethod`, `url` 정보가 추가된다. 요청에서 사용한다. 
	  
**`ResponseEntity`** 
 - HTTP 상태 코드 설정이 가능하다. 응답에서 사용한다.
`return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED)`

> **참고**
> 스프링MVC 내부에서 HTTP 메시지 바디를 읽어서 문자나 객체로 변환해서 전달해주는데, 이때 `HTTP 메시지 컨버터(HttpMessageConverter)`라는 기능을 사용한다. 


### 3-3. @RequestBody()를 이용하는 방법

`@RequestBody`를 사용하면 HTTP 메시지 바디 정보를 편리하게 조회할 수 있다. 참고로 헤더 정보가 필요하다면 `HttpEntity`를 사용하거나 `@RequestHeader`를 사용하면 된다. 
이렇게 메시지 바디를 직접 조회하는 기능은 요청 파라미터를 조회하는 `@RequestParam`, `@ModelAttribute`와는 전혀 관계가 없다. 


> **참고: 요청 파라미터 vs HTTP 메시지 바디**
- 요청 파라미터를 조회하는 기능: `@RequestParam`, `@ModelAttribute` 
- HTTP 메시지 바디를 직접 조회하는 기능: `@RequestBody` 
  

@ResponseBody를 사용하면 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다. 물론 이 경우에도 view를 사용하지 않는다. 


**`@RequestBody`**
- 메시지 바디 정보를 직접 조회(@RequestParam X, @ModelAttribute X)
- HttpMessageConverter 사용 -> StringHttpMessageConverter 적용 

**`@ResponseBody`**
- 메시지 바디 정보 직접 반환(view 조회X)
- HttpMessageConverter 사용 -> StringHttpMessageConverter 적용

**requestBodyString - V4** 

```java
/**

 */
@ResponseBody
@PostMapping("/request-body-string-v4")
public String requestBodyStringV4(@RequestBody String messageBody) {
    log.info("messageBody={}", messageBody);
    return "ok";
}
```


---


## 4. HTTP Request 메시지 바디의 JSON 데이터를 사용하는 방법


이번에는 HTTP API에서 주로 사용하는 JSON 데이터 형식을 조회해보자. 기존 서블릿에서 사용했던 방식과 비슷하게 시작하여 점진적으로 리팩토링하며 다양한 기능을 알아보자.

```java
package hello.springmvc.basic.request;

import com.fasterxml.jackson.databind.ObjectMapper;
import hello.springmvc.basic.HelloData;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpEntity;
import org.springframework.stereotype.Controller;
import org.springframework.util.StreamUtils;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.servlet.ServletInputStream;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.nio.charset.StandardCharsets;

/**
 * {"username":"hello", "age":20}
 * content-type: application/json
 */
@Slf4j
@Controller
public class RequestBodyJsonController {
    private ObjectMapper objectMapper = new ObjectMapper();

    @PostMapping("/request-body-json-v1")
    public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {

        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);

        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", data.getUsername(), data.getAge());

        response.getWriter().write("ok!");
    }

    /**
     * @RequestBody
     * HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     *
     * @ResponseBody
     * - 모든 메서드에 @ResponseBody 적용
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
     */
    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return "ok";
    }

    /**
     * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
     * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (content-
    type: application/json)
     *
     */
    @ResponseBody
    @PostMapping("/request-body-json-v3")
    public String requestBodyJsonV3(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return "ok";
    }

    @ResponseBody
    @PostMapping("/request-body-json-v4")
    public String requestBodyJsonV4(HttpEntity<HelloData> httpEntity) {
        HelloData data = httpEntity.getBody();
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return "ok";
    }

    /**
     * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
     * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter
     * (content-type: application/json)
     *
     * @ResponseBody 적용
     * - 메시지 바디 정보 직접 반환(view 조회X)
     * - HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter 적용(Accept: application/json)
     */
    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return data;
    }
}

```

### 4-1. HttpServletRequest로 JSON 데이터 읽어오기

`HttpServletRequest`를 사용하여 HTTP 메시지 바디에서 직접 JSON 데이터를 받아와 문자 데이터로 변환한다. 문자로 된 JSON 데이터는 Jackson 라이브러리인 `objectMapper`를 이용해 자바 객체로 변환할 수 있다. 


**RequestBodyJsonController** 

```java
/**
 * {"username":"hello", "age":20}
 * content-type: application/json
 */
@Slf4j
@Controller
public class RequestBodyJsonController {
    private ObjectMapper objectMapper = new ObjectMapper();

    @PostMapping("/request-body-json-v1")
    public void requestBodyJsonV1(HttpServletRequest request, HttpServletResponse response) throws IOException {

        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);

        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", data.getUsername(), data.getAge());

        response.getWriter().write("ok!");
    }

```

### 4-2. @RequestBody로 JSON 데이터 읽어오기

`@RequestBody`를 사용하여 JSON 데이터를 읽어와 문자로 변환한다. 문자로 된 JSON 데이터는 Jackson 라이브러리인 `objectMapper`를 이용해 자바 객체로 변환할 수 있다. 

```java
/**
 * @RequestBody
 * HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 *
 * @ResponseBody
 * - 모든 메서드에 @ResponseBody 적용
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> StringHttpMessageConverter 적용
 */
@ResponseBody
@PostMapping("/request-body-json-v2")
public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
    HelloData data = objectMapper.readValue(messageBody, HelloData.class);
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return "ok";
}
```

### 4-3. @RequestBody로 직접 만든 객체를 지정하기
  
바디 메시지를 문자로 변환하고, 다시 객체로 변환하는 과정은 번거롭다. `@ModelAttribute`처럼 한번에 객체로 변환할 수는 없을까?

`HttpEntity`, `@RequestBody`를 사용하면 **HTTP 메시지 컨버터**가 HTTP 메시지 바디의 내용을 우리가 원하는 문자나 객체 등으로 변환해준다. **HTTP 메시지 컨버터**는 문자 뿐만 아니라 JSON도 객체로 변환해 준다. 

> **@RequestBody 객체 파라미터** 
- @RequestBody HelloData data 
- @RequestBody 에 직접 만든 객체를 지정할 수 있다. 

```java
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter (content-
type: application/json)
 *
 */
@ResponseBody
@PostMapping("/request-body-json-v3")
public String requestBodyJsonV3(@RequestBody HelloData data) {
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return "ok";
}
```


> **주의! - `@RequestBody`는 생략 불가능** 
> `@ModelAttribute` 에서 학습한 내용을 떠올려보자. 
>
> 스프링은 `@ModelAttribute`, `@RequestParam` 생략시 다음과 같은 규칙을 적용한다. 
- String , int , Integer 같은 단순 타입 => `@RequestParam` 
- 나머지 => `@ModelAttribute` (argument resolver 로 지정해둔 타입 외) 

따라서 이 경우 HelloData에 `@RequestBody`를 생략하면 `@ModelAttribute`가 적용되어버린다. HTTP 메시지 바디가 아니라 요청 파라미터를 처리하게 된다.
`HelloData data` -> `@ModelAttribute HelloData data` 

> **주의!**
> HTTP 요청시에 `content-type`이 `application/json`인지 확인해야 한다. 그래야만 JSON을 처리할 수 있는 `HTTP 메시지 컨버터`가 실행된다. 


### 4-4. HttpEntity로 직접 만든 객체를 지정하기

```java
/**
 * @RequestBody 생략 불가능(@ModelAttribute 가 적용되어 버림)
 * HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter
 * (content-type: application/json)
 *
 * @ResponseBody 적용
 * - 메시지 바디 정보 직접 반환(view 조회X)
 * - HttpMessageConverter 사용 -> MappingJackson2HttpMessageConverter 적용(Accept: application/json)
 */
@ResponseBody
@PostMapping("/request-body-json-v5")
public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return data;
}
```


**@ResponseBody**
응답의 경우에도 **@ResponseBody**를 사용하면 해당 객체를 HTTP 메시지 바디에 직접 넣어줄 수 있다. 물론 이 경우에도 `HttpEntity`를 사용해도 된다. 

**@RequestBody요청**
- JSON 요청 -> HTTP 메시지 컨버터 객체 

**@ResponseBody응답** 
- 객체 -> HTTP 메시지 컨버터 JSON 응답 


