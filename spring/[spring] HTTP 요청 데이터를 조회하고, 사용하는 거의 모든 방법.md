> ### ๐ โ๏ธ 
> **์ด ๊ธ์ ์คํ๋ง์ด ์ ๊ณตํ๋ HTTP ์์ฒญ ๋ฐ์ดํฐ์ ์กฐํ ๋ฐฉ๋ฒ๊ณผ ํ์ฉ ๋ฐฉ๋ฒ์ ํ์ตํ๊ณ  ์ ๋ฆฌํ ํฌ์คํ์ด๋ค.**

---

# HTTP ์์ฒญ ๋ฐ์ดํฐ๋ฅผ ์กฐํํ๊ณ , ์ฌ์ฉํ๋ ๊ฑฐ์ ๋ชจ๋  ๋ฐฉ๋ฒ

---

## 1. HTTP Request ํค๋ ์ ๋ณด๋ฅผ ์กฐํํ๋ ๋ฐฉ๋ฒ


์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์คํ๋ง ์ปจํธ๋กค๋ฌ๋ ๋ค์ํ ํ๋ผ๋ฏธํฐ๋ฅผ ์ง์ํ๋ค. ์ปจํธ๋กค๋ฌ ๋ฉ์๋์ ํ๋ผ๋ฏธํฐ๋ก HTTP Request ํค๋ ์ ๋ณด๋ฅผ ์กฐํํ๋ ๋ฐฉ๋ฒ์ ์์๋ณด์.


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
                          Locale locale, // ์ธ์ด ์ ๋ณด
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

> **ํค๋ ์ ๋ณด๋ฅผ ์กฐํํ๋ ๋ค์ํ ๋ฐฉ๋ฒ**
- `HttpServletRequest` 
- `HttpServletResponse` 
- `HttpMethod` : HTTP ๋ฉ์๋๋ฅผ ์กฐํํ๋ค.
  - `org.springframework.http.HttpMethod`
- `Locale` : Locale ์ ๋ณด๋ฅผ ์กฐํํ๋ค. 
- `@RequestHeader MultiValueMap<String, String> headerMap` 
  - ๋ชจ๋  HTTP ํค๋๋ฅผ `MultiValueMap` ํ์์ผ๋ก ์กฐํํ๋ค.
- `@RequestHeader("host") String host`
  - ํน์  HTTP ํค๋๋ฅผ ์กฐํํ๋ค. 
  - ์์ฑ
    - ํ์ ๊ฐ ์ฌ๋ถ: required 
    - ๊ธฐ๋ณธ ๊ฐ ์์ฑ: defaultValue   
- `@CookieValue(value = "myCookie", required = false) String cookie`
  - ํน์  ์ฟ ํค๋ฅผ ์กฐํํ๋ค. 
  - ์์ฑ 
    - ํ์ ๊ฐ ์ฌ๋ถ: required 
    - ๊ธฐ๋ณธ ๊ฐ: defaultValue 

**`MultiValueMap`**
- MAP๊ณผ ์ ์ฌํ๋ค. ๋ค๋ง, ํ๋์ ํค์ ์ฌ๋ฌ ๊ฐ์ ๋ฐ์ ์ ์๋ค๋ ์ฐจ์ด๊ฐ ์๋ค. 
- HTTP header, HTTP ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ์ ๊ฐ์ด ํ๋์ ํค์ ์ฌ๋ฌ ๊ฐ์ ๋ฐ์ ๋ ์ฌ์ฉํ๋ค. 
  - ex) `keyA=value1&keyA=value2` 
	  
```java
MultiValueMap<String, String> map = new LinkedMultiValueMap();
map.add("keyA", "value1");
map.add("keyA", "value2");

//[value1,value2
List<String> values = map.get("keyA");
```


**`@Slf4j`** 
์๋์ ๊ฐ์ ์ฝ๋๋ฅผ ์๋์ผ๋ก ์์ฑํด์ ๋ก๊ทธ๋ฅผ ์ ์ธํ๋ค. ๊ฐ๋ฐ์๋ ํธ๋ฆฌํ๊ฒ log๋ฅผ ์ฌ์ฉํ  ์ ์๋ค. 
```java
private static final org.slf4j.Logger log =
org.slf4j.LoggerFactory.getLogger(RequestHeaderController.class);
```

> **์ฐธ๊ณ ** 
> 1. `@Conroller`์ ์ฌ์ฉ ๊ฐ๋ฅํ ํ๋ผ๋ฏธํฐ ๋ชฉ๋ก์ ๋ค์ ๊ณต์ ๋ฉ๋ด์ผ์์ ํ์ธํ  ์ ์๋ค. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments
> 2. `@Conroller`์ ์ฌ์ฉ ๊ฐ๋ฅํ ์๋ต ๊ฐ ๋ชฉ๋ก์ ๋ค์ ๊ณต์ ๋ฉ๋ด์ผ์์ ํ์ธํ  ์ ์๋ค. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types



---

## 2. HTTP Request ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ์ HTML Form์ ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ

ํด๋ผ์ด์ธํธ์์ ์๋ฒ๋ก ์์ฒญ ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ ๋ฐฉ๋ฒ์ ํฌ๊ฒ 3๊ฐ์ง๊ฐ ์๋ค. ๊ทธ ์ค `key-value` ๋ฐฉ์์ผ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ **์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ**์ **HTML Form** ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ์ ์์๋ณด์. 

> **ํด๋ผ์ด์ธํธ์์ ์๋ฒ๋ก ์์ฒญ ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ 3๊ฐ์ง ๋ฐฉ๋ฒ**
>
> **1. GET - ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ** 
- ex) `/url?username=hello&age=20`   
- ๋ฉ์์ง ๋ฐ๋ ์์ด, URL์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ์ ๋ฐ์ดํฐ๋ฅผ ํฌํจํด์ ์ ๋ฌ 
- ์) ๊ฒ์, ํํฐ, ํ์ด์ง๋ฑ์์ ๋ง์ด ์ฌ์ฉํ๋ ๋ฐฉ์ 
>
> **2. POST - HTML Form** 
- `content-type`: `application/x-www-form-urlencoded`  
- ๋ฉ์์ง ๋ฐ๋์ ์ฟผ๋ฆฌ ํ๋ฆฌ๋ฏธํฐ ํ์์ผ๋ก ์ ๋ฌ -> `username=hello&age=20` 
- ์) ํ์ ๊ฐ์, ์ํ ์ฃผ๋ฌธ, HTML Form ์ฌ์ฉ 
>
> **3. HTTP message body์ ๋ฐ์ดํฐ๋ฅผ ์ง์  ๋ด์์ ์์ฒญ** 
- HTTP API์์ ์ฃผ๋ก ์ฌ์ฉ
- JSON, XML, TEXT ๋ฑ ๋ค์ํ ๋ฐ์ดํฐ ํ์์ ์ฌ์ฉํ์ง๋ง JSON ๋ฐฉ์์ ์ฃผ๋ก ์ฌ์ฉ
- POST, PUT, PATCH์ ์ฃผ๋ก ์ฌ์ฉ


### 2-1. getParameter()๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์
`HttpServletReques`์ `request.getParameter()`๋ฅผ GET ๋ฐฉ์์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ๋ฐ์ดํฐ์ POST ๋ฐฉ์์ HTML Form ๋ฐ์ดํฐ๋ฅผ ๋ชจ๋ ์กฐํํ  ์ ์๋ค. ๋ ํ์์ด ๋์ผํ๊ธฐ ๋๋ฌธ์ด๋ค. ๋ ๊ฐ์ง ๋ฐฉ์์ ๊ฐ๋จํ **์์ฒญ ํ๋ผ๋ฏธํฐ(request parameter)**์กฐํ๋ผ๊ณ  ํ๋ค. ๊ฐ์ฅ ๊ธฐ๋ณธ์ ์ธ ์กฐํ ๋ฐฉ๋ฒ์ `HttpServletRequest`๊ฐ ์ ๊ณตํ๋`getParameter()`๋ฅผ ์ด์ฉํ๋ ๋ฐฉ๋ฒ์ด๋ค.

> **GET, ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ๋ฐ์ดํฐ ์ ์ก ๋ฐฉ์ ์์**
> `http://localhost:8080/request-param?username=hello&age=20` 

> **POST, HTML Form ๋ฐ์ดํฐ ์ ์ก ๋ฐฉ์ ์์**  
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
    <button type="submit">์ ์ก</button>
</form>
</body>
</html>

```

---

### 2-2. @RequestParam() - ๊ธฐ๋ณธ ๊ธฐ๋ฅ


์คํ๋ง์ด ์ ๊ณตํ๋ `@RequestParam`์ ์ฌ์ฉํ๋ฉด ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ๋ ํธ๋ฆฌํ๊ฒ ์ฌ์ฉํ  ์ ์๋ค. `@RequestParam`์ ๋ค์ํ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. `@RequestParam`์ ํ์ฉ ๋ฐฉ๋ฒ์ ์ ์ดํดํ  ์ ์๋๋ก ๊ฐ์ฅ ๊ธฐ๋ณธ์ ์ธ ์ฌ์ฉ ํํ๋ถํฐ ์์ํ์ฌ ๋จ๊ณ์ ์ผ๋ก ๊ณ ๊ธ ๊ธฐ๋ฅ์ ์์๋ณด์.


`@RequestParam`์ `name(value)` ์์ฑ์ ์ด์ฉํ๋ฉด ํ๋ผ๋ฏธํฐ ๋ฐ์ดํฐ๋ฅผ ๋ณ์๋ก ๋ฐ๋ก ๋ฐ์์ ์ฌ์ฉํ  ์ ์๋ค. 
> `@RequestParam("username") String memberName`์
=> `request.getParameter("username")`๊ณผ ๊ฐ๋ค.

```java
/**
 * @RequestParam ์ฌ์ฉ: ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ผ๋ก ๋ฐ์ธ๋ฉ
 * @ResponseBody ์ถ๊ฐ: View ์กฐํ๋ฅผ ๋ฌด์ํ๊ณ , HTTP message body์ ์ง์  ํด๋น ๋ด์ฉ ์๋ ฅ */

@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-v2")
public String requestParamV2(
        @RequestParam("username") String memberName,
        @RequestParam("age") int memberAge) {
    log.info("username={}, age={}", memberName, memberAge);
    return "OK!";
}
```

- `@RequestParam` : ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ผ๋ก ๋ฐ์ธ๋ฉ 
- `@ResponseBody` : View ์กฐํ๋ฅผ ๋ฌด์ํ๊ณ , HTTP message body์ ์ง์  ํด๋น ๋ด์ฉ ์๋ ฅ 


### 2-3. @RequestParam() - ์ด๋ฆ ์์ฑ ์๋ต ๊ธฐ๋ฅ

`@RequestParam`๋ฅผ ์ฌ์ฉํ  ๋, HTTP ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ด ๋ณ์ ์ด๋ฆ๊ณผ ๊ฐ์ผ๋ฉด  `@RequestParam(name="xx")`์ `(name="xx")` ์์ฑ์ ์๋ตํ  ์ ์๋ค. 

```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-v3")
public String requestParamV3(
        //  @RequestParam์ ์ด๋ฆ์ ์๋ตํ๋ ค๋ฉด ์์ฒญ ํ๋ผ๋ฏธํฐ๋ก ๋์ด์ค๋ ์ด๋ฆ๊ณผ @RequestParam ํ๋ผ๋ฏธํฐ ๋ณ์๋ช์ด ๊ฐ์์ผ ํ๋ค.
        @RequestParam String username,
        @RequestParam int age) {
    log.info("username={}, age={}", username, age);
    return "OK!";
}
```

### 2-4. @RequestParam() - ๋จ์ ํ์์ผ ๋, @RequestParam ์๋ต ๊ธฐ๋ฅ

ํ๋ผ๋ฏธํฐ ์ ๋ณด๊ฐ ๋จ์ ํ์(String, int, Integer)์ด๋ฉด `@RequestParam`๋ฅผ ์์ ์๋ตํ  ์ ์๋ค. ๋จ, ์์ฒญ ํ๋ผ๋ฏธํฐ๋ก ๋์ด์ค๋ ์ด๋ฆ๊ณผ ํ๋ผ๋ฏธํฐ์ ๋ณ์๋ช์ด ๊ฐ์์ผ ํ๋ค.

```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-v4")
public String requestParamV4(String username, int age) {  
    log.info("username={}, age={}", username, age);
    return "OK!";
}
```

> **์ฃผ์**
> `@RequestParam` ์ ๋ธํ์ด์์ ์๋ตํ๋ฉด ์คํ๋ง MVC๋ ๋ด๋ถ์์ `required=false`๋ฅผ ์ ์ฉํ๋ค. `required`๋ ํ์ ํ๋ผ๋ฏธํฐ ์ฌ๋ถ๋ฅผ ์ง์ ํ๋ ์ต์์ด๋ค.

> **์ฐธ๊ณ ** 
> ์ ๋ธํ์ด์์ ์์ ํ ์๋ตํ๋ ๊ฒ์ ๋ชํ์ฑ์ ํด์น  ์ ์๋ค. `@RequestParam`์ ์กด์ฌ๋ง์ผ๋ก ์์ฒญ ํ๋ฆฌ๋ฏธํฐ์์ ๋ฐ์ดํฐ๋ฅผ ์ฝ๋ ๋ค๋ ์ฌ์ค์ ์ ๋ฌํ  ์ ์๊ธฐ ๋๋ฌธ์ด๋ค. 


### 2-5. @RequestParam() - ํ์ ํ๋ผ๋ฏธํฐ๋ฅผ ์ง์ ํ๋ ๊ธฐ๋ฅ

 
`required`๋ฅผ ์ฌ์ฉํ๋ฉด ํ์ ํ๋ผ๋ฏธํฐ ์ฌ๋ถ๋ฅผ ์ง์ ํ  ์ ์๋ค. `required`๋ ๊ธฐ๋ณธ๊ฐ์ด true๋ค. ๋ฐ๋ผ์ ๋ณ๋๋ก ์ง์ ํ์ง ์์ ๊ฒฝ์ฐ ๋ฐ๋์ ํด๋น ํ๋ผ๋ฏธํฐ๊ฐ ์กด์ฌํด์ผ ๋ฐ์ดํฐ๋ฅผ ์กฐํํ  ์ ์๋ค.

`@RequestParam(required = true) String username`๊ณผ ๊ฐ์ด ํ์ ํ๋ผ๋ฏธํฐ๊ฐ ์ง์ ๋์ด ์๋ค๋ฉด `@/request-param`๋ `username` ์ ๋ณด๊ฐ ์์ผ๋ฏ๋ก 400 ์์ธ๊ฐ ๋ฐ์ํ๋ค. ๋ฐ๋ฉด, `/request-param?username=kim`์ ํต๊ณผํ๋ค. ๋ง์ฝ  `username=`๊ณผ ๊ฐ์ด ์๋ฌด ๊ฐ๋ ์์ ๊ฒฝ์ฐ ๋น๋ฌธ์๋ก ์ธ์๋์ด ํต๊ณผํ๋ค. 


```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-required")
public String requestParamRequired(
        // required ture์ผ ๊ฒฝ์ฐ, ํด๋น ๋ฐ์ดํฐ๊ฐ ์์ฒญ ํ๋ผ๋ฏธํฐ์ ํ์๋ก ํฌํจ๋์ด์ผ ํ๋ค.
        // required๋ ๊ธฐ๋ณธ๊ฐ์ด true๋ค.
        @RequestParam(required = true) String username,
        @RequestParam(required = false) Integer age) { // ๊ธฐ๋ณธํ์ธ int๋ null ๋ถ๊ฐ

    log.info("username={}, age={}", username, age);
    return "OK!";
}
```


> **์ฃผ์!**
- ๊ธฐ๋ณธํ(primitive)์ null์ ์๋ ฅํ์ง ์๋๋ก ํ๋ค. `null`์ `int`์ ์๋ ฅํ๋ ๊ฒ์ ๋ถ๊ฐ๋ฅํ๋ค. 500 ์์ธ๊ฐ ๋ฐ์ํ๋ค. ๋ฐ๋ผ์, `null`์ ๋ฐ์ ์ ์๋ `Integer`ํ์์ด๋ ๊ธฐ๋ณธ๊ฐ์ ์ ์ฉํ  ์ ์๋ `defaultValue`๋ฅผ ์ฌ์ฉํด์ผ ํ๋ค.



### 2-6. @RequestParam() - ๊ธฐ๋ณธ ๊ฐ์ ์ ์ฉํ๋ requestParamDefault ๊ธฐ๋ฅ


`defaultValue`๋ฅผ ์ฌ์ฉํ๋ฉด, ํ๋ผ๋ฏธํฐ ๊ฐ์ด ์์ ๋ ๋ฏธ๋ฆฌ ์ง์ ํด๋์ ๊ธฐ๋ณธ๊ฐ์ ์ฌ์ฉํ๋ค. ํ ๊ฐ์ง ์ฐธ๊ณ ํ  ์ ์ `defaultValue`๊ฐ ๋น ๋ฌธ์์ ๊ฒฝ์ฐ์๋ ์ ์ฉ๋๋ค๋ ์ ์ด๋ค.

```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-default")
public String requestParamDefault(
        // defaultValue๊ฐ ์์ผ๋ฉด, ํญ์ ๊ธฐ๋ณธ๊ฐ์ด ์กด์ฌํ๊ธฐ ๋๋ฌธ์ required ์ต์์ ๋ฌด์๋ฏธํด์ง๋ค.
        @RequestParam(required = true, defaultValue = "guest") String username,
        @RequestParam(required = false, defaultValue = "-1") int age) {

    log.info("username={}, age={}", username, age);
    return "OK!";
}
```
defaultValue๋ฅผ ์ฌ์ฉํ๋ฉด ๊ธฐ๋ณธ ๊ฐ์ด ์กด์ฌํ๊ธฐ ๋๋ฌธ์ required๋ ์๋ฏธ๊ฐ ์๋ค. 



### 2-7. @RequestParam() - ํ๋ผ๋ฏธํฐ๋ฅผ Map์ผ๋ก ์กฐํํ๋ requestParamMap ๊ธฐ๋ฅ

`requestParamMap`์ ์ด์ฉํ๋ฉด ์์ฒญ ํ๋ผ๋ฏธํ ์ ๋ณด๋ฅผ Map์ผ๋ก ๋ฐ์ ์ ์๋ค. ํ๋ผ๋ฏธํฐ์ ๊ฐ์ด 1๊ฐ๊ฐ ํ์คํ๋ค๋ฉด `Map`์ ์ฌ์ฉํด๋ ๋์ง๋ง, ๊ทธ๋ ์ง ์๋ค๋ฉด `MultiValueMap`์ ์ฌ์ฉํ  ์ ์๋ค.

- **`@RequestParam Map`**
  - `Map(key=value)`
	  
- **`@RequestParam MultiValueMap`**
  - `MultiValueMap(key=[value1, value2, ...]`
  ex) (key=userIds, value=[id1, id2])


```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "OK!";
}
```


---


### 2-8. ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ๊ฐ์ฒด๋ก ๋ง๋๋ @ModelAttribute 

์ค์  ๊ฐ๋ฐ์ ํ๋ฉด ์์ฒญ ํ๋ผ๋ฏธํฐ์ ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ฒด์ ๋ฃ์ด์ฃผ์ผ ํ๋ ๊ฒฝ์ฐ๊ฐ ๋ง๋ค. ๋ณดํต ์๋์ ๊ฐ์ ์ฝ๋๋ฅผ ์์ฑํ๊ฒ ๋๋ค. 
 
```java
@ResponseBody // ๋ฆฌํด String์ ์๋ต ๋ฉ์์ง๋ก ๋ฐ๋ก ์ ๋ฌํ๋ค
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "OK!";
}
```

์คํ๋ง์ ์ด ๊ณผ์ ์ ์์ ํ ์๋ํํ๋ `@ModelAttribute` ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. `@ModelAttribute`๋ `@RequestParam` ์ฒ๋ผ ๋ค์ํ ์ธ๋ถ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. ๋จ๊ณ๋ณ๋ก ๊ธฐ๋ฅ์ ํ์ฅํ๋ฉฐ, ์ฌ์ฉ๋ฐฉ๋ฒ์ ์์๋ณด์. 

๋จผ์ , ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ๋ฐ์ธ๋ฉ ๋ฐ์ ๊ฐ์ฒด๋ฅผ ๋ง๋ค์ด์ผ ํ๋ค.

```java
package hello.springmvc.basic;

import lombok.Data;

@Data
public class HelloData {
    private String username;
    private int age;
}

```

๋กฌ๋ณต `@Data`๋ ์๋์ ๊ฐ์ ์ ๋ธํ์ด์์ ์๋์ผ๋ก ํฌํจํ๋ค. 
- @Getter
- @Setter
- @ToString
- @EqualsAndHashCode
- @RequiredArgsConstructor



`@ModelAttribute` ์ฌ์ฉํ๋ฉด, ๋ง์น ๋ง๋ฒ์ฒ๋ผ `HelloData` ๊ฐ์ฒด๊ฐ ์์ฑ๋๊ณ , ์์ฒญ ํ๋ผ๋ฏธํฐ์ ๊ฐ๋ ๋ชจ๋ ๋ค์ด๊ฐ ์๋ค. `model.addAttribute(helloData)` ์ฝ๋๊ฐ ํจ๊ป ์ ์ฉ๋๊ธฐ ๋๋ฌธ์ด๋ค. 

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

์คํ๋ง MVC๋ `@ModelAttribute`๊ฐ ์์ผ๋ฉด ์๋์ ๊ฐ์ ๊ณผ์ ์ ์๋์ผ๋ก ์คํํ๋ค. 
- `HelloData` ๊ฐ์ฒด๋ฅผ ์์ฑํ๋ค. 
- ์์ฒญ ํ๋ผ๋ฏธํฐ์ ์ด๋ฆ์ผ๋ก `HelloData` ๊ฐ์ฒด์ ํ๋กํผํฐ๋ฅผ ์ฐพ๋๋ค. ๊ทธ๋ฆฌ๊ณ  ํด๋น ํ๋กํผํฐ์ `setter`๋ฅผ ํธ์ถํด์ ํ๋ผ๋ฏธํฐ์ ๊ฐ์ ์๋ ฅ(๋ฐ์ธ๋ฉ) ํ๋ค. 
  - ์) ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ด username ์ด๋ฉด setUsername() ๋ฉ์๋๋ฅผ ์ฐพ์์ ํธ์ถํ๋ฉด์ ๊ฐ์ ์๋ ฅํ๋ค. 

> **ํ๋กํผํฐ(property)๋?**
> ๋ณดํต ํ๋ ๋ณ์๋ผ๊ณ  ํ๋ค. ๊ฐ์ฒด์ `getUsername()`, `setUsername()` ๋ฉ์๋๊ฐ ์์ผ๋ฉด, ์ด ๊ฐ์ฒด๋ `username`์ด๋ผ๋ ํ๋กํผํฐ๋ฅผ ๊ฐ์ง๊ณ  ์๋ ๊ฒ์ด๋ค. `username` ํ๋กํผํฐ์ ๊ฐ์ ๋ณ๊ฒฝํ๋ฉด `setUsername()`์ด ํธ์ถ๋๊ณ , ์กฐํํ๋ฉด `getUsername()`์ด ํธ์ถ๋๋ค. 

```java
class HelloData {
    getUsername();
    setUsername();
} 
```

> ์ฐธ๊ณ 
> `age=abc` ์ฒ๋ผ ์ซ์๊ฐ ๋ค์ด๊ฐ์ผ ํ  ๊ณณ์ ๋ฌธ์๋ฅผ ๋ฃ์ผ๋ฉด `BindException`์ด ๋ฐ์ํ๋ค.


### 2-9. @ModelAttribute์ ์๋ต ๊ธฐ๋ฅ

`@ModelAttribute`์ ์๋ต์ด ๊ฐ๋ฅํ๋ค. ๊ทธ๋ฐ๋ฐ `@RequestParam`๋ ์๋ต์ด ๊ฐ๋ฅํ๋ค๋ณด๋, ์ด๋ค ๊ฒ์ด ์๋ต๋์ด ๋์ํ๋์ง ํผ๋์ค๋ฌ์ธ ์ ์๋ค.

์คํ๋ง์ ํด๋น ๊ธฐ๋ฅ์ ์๋ตํ  ๋ ์๋์ ๊ฐ์ ๊ท์น์ ์ ์ฉํ๋ค.
- String , int , Integer ๊ฐ์ ๋จ์ ํ์ => `@RequestParam` 
- ๋๋จธ์ง => `@ModelAttribute` (argument resolver ๋ก ์ง์ ํด๋ ํ์ ์ธ) 

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
## 3. HTTP Request ๋ฉ์์ง ๋ฐ๋์ ๋ฌธ์ ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ

HTTP ๋ฉ์์ง ๋ฐ๋๋ฅผ ํตํด ๋ฐ์ดํฐ๊ฐ ์ง์  ๋์ด์ค๋ ๊ฒฝ์ฐ์๋ `@RequestParam`๊ณผ `@ModelAttribute`๋ฅผ ์ฌ์ฉํ  ์ ์๋ค. ๋ฌผ๋ก , HTML Form ํ์์ผ๋ก ์ ๋ฌ๋๋ ๊ฒฝ์ฐ๋ ์์ฒญ ํ๋ผ๋ฏธํฐ์ ํด๋นํ๋ค. 

๋ฉ์์ง ๋ฐ๋๋ก ์ ๋ฌ๋๋ ๋จ์ ํ์คํธ ๋ฐ์ดํฐ๋ `InputStream()`๋ฅผ ์ด์ฉํ์ฌ ์ฝ์ ์ ์๋ค. ์์ธ๋ฌ, `HttpEntity`์ `RequestBody`๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ๋ ์๋ค. ๋จ๊ณ์ ์ผ๋ก ์ดํด๋ณด์.

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
     * InputStream(Reader): HTTP ์์ฒญ ๋ฉ์์ง ๋ฐ๋์ ๋ด์ฉ์ ์ง์  ์กฐํ
     * OutputStream(Writer): HTTP ์๋ต ๋ฉ์์ง์ ๋ฐ๋์ ์ง์  ๊ฒฐ๊ณผ ์ถ๋ ฅ
     */
    @PostMapping("/request-body-string-v2")
    public void requestBodyString(InputStream inputStream, Writer responseWriter) throws IOException {

        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
        log.info("messageBody={}", messageBody);
        responseWriter.write("OK!!");
    }

    /**
     * HttpEntity: HTTP header, body ์ ๋ณด๋ฅผ ํธ๋ผํ๊ฒ ์กฐํ
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด๋ฅผ ์ง์  ์กฐํ(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ *
     * ์๋ต์์๋ HttpEntity ์ฌ์ฉ ๊ฐ๋ฅ
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
     * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ
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
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด๋ฅผ ์ง์  ์กฐํ(@RequestParam X, @ModelAttribute X)
     * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ *
     * @ResponseBody
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
     * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ */
    @ResponseBody
    @PostMapping("/request-body-string-v4")
    public String requestBodyStringV4(@RequestBody String messageBody) {
        log.info("messageBody={}", messageBody);
        return "ok";
    }
}

```

### 3-1. InputStream()์ ์ด์ฉํด ๋จ์ ํ์คํธ ๋ฐ์ดํฐ ์ฌ์ฉ

์๋์ ๊ฐ์ด `getInputStream`์ ์ด์ฉํ์ฌ `HttpServletRequest` ๊ฐ์ฒด์์ ๋ฌธ์ ๋ฐ์ดํฐ๋ฅผ ์ฝ์ด๋ผ ์ ์๋ค. 

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

๋ ๋ค๋ฅธ ๋ฐฉ๋ฒ์ผ๋ก, ์๋์ ๊ฐ์ด `InputStream`์ ์ง์  ์ฌ์ฉํ  ์๋ ์๋ค. 

์คํ๋ง MVC๋ ๋ค์ ํ๋ผ๋ฏธํฐ๋ฅผ ์ง์ํ๋ค. 
- `InputStream(Reader)`: HTTP ์์ฒญ ๋ฉ์์ง ๋ฐ๋์ ๋ด์ฉ์ ์ง์  ์กฐํ 
- `OutputStream(Writer)`: HTTP ์๋ต ๋ฉ์์ง์ ๋ฐ๋์ ์ง์  ๊ฒฐ๊ณผ ์ถ๋ ฅ 

**requestBodyString - v2**
```java
/**
 * InputStream(Reader): HTTP ์์ฒญ ๋ฉ์์ง ๋ฐ๋์ ๋ด์ฉ์ ์ง์  ์กฐํ
 * OutputStream(Writer): HTTP ์๋ต ๋ฉ์์ง์ ๋ฐ๋์ ์ง์  ๊ฒฐ๊ณผ ์ถ๋ ฅ
 */
@PostMapping("/request-body-string-v2")
public void requestBodyString(InputStream inputStream, Writer responseWriter) throws IOException {

    String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
    log.info("messageBody={}", messageBody);
    responseWriter.write("OK!!");
}
```



### 3-2. HttpEntity()๋ฅผ ์ด์ฉํ๋ ๋ฐฉ๋ฒ

`HttpEntity`๋ฅผ ์ด์ฉํ๋ฉด `HTTP header`์ `body` ์ ๋ณด๋ฅผ ํ๋ฒ์ ์กฐํํ  ์ ์๋ค. `@RequestParam`๋ `@ModelAttribute`๋ฅผ ์ฌ์ฉํ์ง ์๊ณ  ๋ฐ๋ ์ ๋ณด๋ฅผ ์ง์  ์กฐํํ๋ค. 

๋ด๋ถ์ ์ผ๋ก๋ `HttpMessageConverter`๋ฅผ ์ฌ์ฉํ์ฌ `StringHttpMessageConverter`๊ฐ ํธ์ถ๋๋ ๋ฐฉ์์ด๋ค. `HttpEntity`๋ ์์ฒญ ๋ฟ๋ง ์๋๋ผ, ์๋ต์๋ ์ฌ์ฉํ  ์ ์๋ค.

View ์กฐํ ์์ด ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด๋ฅผ ์ง์  ๋ฐํํ๋ค. ์๋ต๋ ๋ง์ฐฌ๊ฐ์ง๋ก ๋๋ถ์ ์ผ๋ก `HttpMessageConverter`๊ฐ ์ฌ์ฉ๋์ด `StringHttpMessageConverter`๊ฐ ํธ์ถ๋๋ ๋ฐฉ์์ด๋ค. 

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

์ฐธ๊ณ ๋ก `HttpEntity`๋ฅผ ์์๋ฐ์ ๋ค์ ๊ฐ์ฒด๋ค๋ ๊ฐ์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. 

**`RequestEntity`** 
- `HttpMethod`, `url` ์ ๋ณด๊ฐ ์ถ๊ฐ๋๋ค. ์์ฒญ์์ ์ฌ์ฉํ๋ค. 
	  
**`ResponseEntity`** 
 - HTTP ์ํ ์ฝ๋ ์ค์ ์ด ๊ฐ๋ฅํ๋ค. ์๋ต์์ ์ฌ์ฉํ๋ค.
`return new ResponseEntity<String>("Hello World", responseHeaders, HttpStatus.CREATED)`

> **์ฐธ๊ณ **
> ์คํ๋งMVC ๋ด๋ถ์์ HTTP ๋ฉ์์ง ๋ฐ๋๋ฅผ ์ฝ์ด์ ๋ฌธ์๋ ๊ฐ์ฒด๋ก ๋ณํํด์ ์ ๋ฌํด์ฃผ๋๋ฐ, ์ด๋ `HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ(HttpMessageConverter)`๋ผ๋ ๊ธฐ๋ฅ์ ์ฌ์ฉํ๋ค. 


### 3-3. @RequestBody()๋ฅผ ์ด์ฉํ๋ ๋ฐฉ๋ฒ

`@RequestBody`๋ฅผ ์ฌ์ฉํ๋ฉด HTTP ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด๋ฅผ ํธ๋ฆฌํ๊ฒ ์กฐํํ  ์ ์๋ค. ์ฐธ๊ณ ๋ก ํค๋ ์ ๋ณด๊ฐ ํ์ํ๋ค๋ฉด `HttpEntity`๋ฅผ ์ฌ์ฉํ๊ฑฐ๋ `@RequestHeader`๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค. 
์ด๋ ๊ฒ ๋ฉ์์ง ๋ฐ๋๋ฅผ ์ง์  ์กฐํํ๋ ๊ธฐ๋ฅ์ ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ์กฐํํ๋ `@RequestParam`, `@ModelAttribute`์๋ ์ ํ ๊ด๊ณ๊ฐ ์๋ค. 


> **์ฐธ๊ณ : ์์ฒญ ํ๋ผ๋ฏธํฐ vs HTTP ๋ฉ์์ง ๋ฐ๋**
- ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ์กฐํํ๋ ๊ธฐ๋ฅ: `@RequestParam`, `@ModelAttribute` 
- HTTP ๋ฉ์์ง ๋ฐ๋๋ฅผ ์ง์  ์กฐํํ๋ ๊ธฐ๋ฅ: `@RequestBody` 
  

@ResponseBody๋ฅผ ์ฌ์ฉํ๋ฉด ์๋ต ๊ฒฐ๊ณผ๋ฅผ HTTP ๋ฉ์์ง ๋ฐ๋์ ์ง์  ๋ด์์ ์ ๋ฌํ  ์ ์๋ค. ๋ฌผ๋ก  ์ด ๊ฒฝ์ฐ์๋ view๋ฅผ ์ฌ์ฉํ์ง ์๋๋ค. 


**`@RequestBody`**
- ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด๋ฅผ ์ง์  ์กฐํ(@RequestParam X, @ModelAttribute X)
- HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ 

**`@ResponseBody`**
- ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
- HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ

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


## 4. HTTP Request ๋ฉ์์ง ๋ฐ๋์ JSON ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ


์ด๋ฒ์๋ HTTP API์์ ์ฃผ๋ก ์ฌ์ฉํ๋ JSON ๋ฐ์ดํฐ ํ์์ ์กฐํํด๋ณด์. ๊ธฐ์กด ์๋ธ๋ฆฟ์์ ์ฌ์ฉํ๋ ๋ฐฉ์๊ณผ ๋น์ทํ๊ฒ ์์ํ์ฌ ์ ์ง์ ์ผ๋ก ๋ฆฌํฉํ ๋งํ๋ฉฐ ๋ค์ํ ๊ธฐ๋ฅ์ ์์๋ณด์.

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
     * HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ
     *
     * @ResponseBody
     * - ๋ชจ๋  ๋ฉ์๋์ @ResponseBody ์ ์ฉ
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
     * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ
     */
    @ResponseBody
    @PostMapping("/request-body-json-v2")
    public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
        HelloData data = objectMapper.readValue(messageBody, HelloData.class);
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return "ok";
    }

    /**
     * @RequestBody ์๋ต ๋ถ๊ฐ๋ฅ(@ModelAttribute ๊ฐ ์ ์ฉ๋์ด ๋ฒ๋ฆผ)
     * HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter (content-
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
     * @RequestBody ์๋ต ๋ถ๊ฐ๋ฅ(@ModelAttribute ๊ฐ ์ ์ฉ๋์ด ๋ฒ๋ฆผ)
     * HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter
     * (content-type: application/json)
     *
     * @ResponseBody ์ ์ฉ
     * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
     * - HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter ์ ์ฉ(Accept: application/json)
     */
    @ResponseBody
    @PostMapping("/request-body-json-v5")
    public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
        log.info("username={}, age={}", data.getUsername(), data.getAge());
        return data;
    }
}

```

### 4-1. HttpServletRequest๋ก JSON ๋ฐ์ดํฐ ์ฝ์ด์ค๊ธฐ

`HttpServletRequest`๋ฅผ ์ฌ์ฉํ์ฌ HTTP ๋ฉ์์ง ๋ฐ๋์์ ์ง์  JSON ๋ฐ์ดํฐ๋ฅผ ๋ฐ์์ ๋ฌธ์ ๋ฐ์ดํฐ๋ก ๋ณํํ๋ค. ๋ฌธ์๋ก ๋ JSON ๋ฐ์ดํฐ๋ Jackson ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ธ `objectMapper`๋ฅผ ์ด์ฉํด ์๋ฐ ๊ฐ์ฒด๋ก ๋ณํํ  ์ ์๋ค. 


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

### 4-2. @RequestBody๋ก JSON ๋ฐ์ดํฐ ์ฝ์ด์ค๊ธฐ

`@RequestBody`๋ฅผ ์ฌ์ฉํ์ฌ JSON ๋ฐ์ดํฐ๋ฅผ ์ฝ์ด์ ๋ฌธ์๋ก ๋ณํํ๋ค. ๋ฌธ์๋ก ๋ JSON ๋ฐ์ดํฐ๋ Jackson ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ธ `objectMapper`๋ฅผ ์ด์ฉํด ์๋ฐ ๊ฐ์ฒด๋ก ๋ณํํ  ์ ์๋ค. 

```java
/**
 * @RequestBody
 * HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ
 *
 * @ResponseBody
 * - ๋ชจ๋  ๋ฉ์๋์ @ResponseBody ์ ์ฉ
 * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
 * - HttpMessageConverter ์ฌ์ฉ -> StringHttpMessageConverter ์ ์ฉ
 */
@ResponseBody
@PostMapping("/request-body-json-v2")
public String requestBodyJsonV2(@RequestBody String messageBody) throws IOException {
    HelloData data = objectMapper.readValue(messageBody, HelloData.class);
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return "ok";
}
```

### 4-3. @RequestBody๋ก ์ง์  ๋ง๋  ๊ฐ์ฒด๋ฅผ ์ง์ ํ๊ธฐ
  
๋ฐ๋ ๋ฉ์์ง๋ฅผ ๋ฌธ์๋ก ๋ณํํ๊ณ , ๋ค์ ๊ฐ์ฒด๋ก ๋ณํํ๋ ๊ณผ์ ์ ๋ฒ๊ฑฐ๋กญ๋ค. `@ModelAttribute`์ฒ๋ผ ํ๋ฒ์ ๊ฐ์ฒด๋ก ๋ณํํ  ์๋ ์์๊น?

`HttpEntity`, `@RequestBody`๋ฅผ ์ฌ์ฉํ๋ฉด **HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ**๊ฐ HTTP ๋ฉ์์ง ๋ฐ๋์ ๋ด์ฉ์ ์ฐ๋ฆฌ๊ฐ ์ํ๋ ๋ฌธ์๋ ๊ฐ์ฒด ๋ฑ์ผ๋ก ๋ณํํด์ค๋ค. **HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ**๋ ๋ฌธ์ ๋ฟ๋ง ์๋๋ผ JSON๋ ๊ฐ์ฒด๋ก ๋ณํํด ์ค๋ค. 

> **@RequestBody ๊ฐ์ฒด ํ๋ผ๋ฏธํฐ** 
- @RequestBody HelloData data 
- @RequestBody ์ ์ง์  ๋ง๋  ๊ฐ์ฒด๋ฅผ ์ง์ ํ  ์ ์๋ค. 

```java
/**
 * @RequestBody ์๋ต ๋ถ๊ฐ๋ฅ(@ModelAttribute ๊ฐ ์ ์ฉ๋์ด ๋ฒ๋ฆผ)
 * HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter (content-
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


> **์ฃผ์! - `@RequestBody`๋ ์๋ต ๋ถ๊ฐ๋ฅ** 
> `@ModelAttribute` ์์ ํ์ตํ ๋ด์ฉ์ ๋ ์ฌ๋ ค๋ณด์. 
>
> ์คํ๋ง์ `@ModelAttribute`, `@RequestParam` ์๋ต์ ๋ค์๊ณผ ๊ฐ์ ๊ท์น์ ์ ์ฉํ๋ค. 
- String , int , Integer ๊ฐ์ ๋จ์ ํ์ => `@RequestParam` 
- ๋๋จธ์ง => `@ModelAttribute` (argument resolver ๋ก ์ง์ ํด๋ ํ์ ์ธ) 

๋ฐ๋ผ์ ์ด ๊ฒฝ์ฐ HelloData์ `@RequestBody`๋ฅผ ์๋ตํ๋ฉด `@ModelAttribute`๊ฐ ์ ์ฉ๋์ด๋ฒ๋ฆฐ๋ค. HTTP ๋ฉ์์ง ๋ฐ๋๊ฐ ์๋๋ผ ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ ์ฒ๋ฆฌํ๊ฒ ๋๋ค.
`HelloData data` -> `@ModelAttribute HelloData data` 

> **์ฃผ์!**
> HTTP ์์ฒญ์์ `content-type`์ด `application/json`์ธ์ง ํ์ธํด์ผ ํ๋ค. ๊ทธ๋์ผ๋ง JSON์ ์ฒ๋ฆฌํ  ์ ์๋ `HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ`๊ฐ ์คํ๋๋ค. 


### 4-4. HttpEntity๋ก ์ง์  ๋ง๋  ๊ฐ์ฒด๋ฅผ ์ง์ ํ๊ธฐ

```java
/**
 * @RequestBody ์๋ต ๋ถ๊ฐ๋ฅ(@ModelAttribute ๊ฐ ์ ์ฉ๋์ด ๋ฒ๋ฆผ)
 * HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter
 * (content-type: application/json)
 *
 * @ResponseBody ์ ์ฉ
 * - ๋ฉ์์ง ๋ฐ๋ ์ ๋ณด ์ง์  ๋ฐํ(view ์กฐํX)
 * - HttpMessageConverter ์ฌ์ฉ -> MappingJackson2HttpMessageConverter ์ ์ฉ(Accept: application/json)
 */
@ResponseBody
@PostMapping("/request-body-json-v5")
public HelloData requestBodyJsonV5(@RequestBody HelloData data) {
    log.info("username={}, age={}", data.getUsername(), data.getAge());
    return data;
}
```


**@ResponseBody**
์๋ต์ ๊ฒฝ์ฐ์๋ **@ResponseBody**๋ฅผ ์ฌ์ฉํ๋ฉด ํด๋น ๊ฐ์ฒด๋ฅผ HTTP ๋ฉ์์ง ๋ฐ๋์ ์ง์  ๋ฃ์ด์ค ์ ์๋ค. ๋ฌผ๋ก  ์ด ๊ฒฝ์ฐ์๋ `HttpEntity`๋ฅผ ์ฌ์ฉํด๋ ๋๋ค. 

**@RequestBody์์ฒญ**
- JSON ์์ฒญ -> HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ ๊ฐ์ฒด 

**@ResponseBody์๋ต** 
- ๊ฐ์ฒด -> HTTP ๋ฉ์์ง ์ปจ๋ฒํฐ JSON ์๋ต 


