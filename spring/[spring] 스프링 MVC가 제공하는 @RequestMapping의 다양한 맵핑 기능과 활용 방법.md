> ### ๐ โ๏ธ 
> **์ด ๊ธ์ ์คํ๋ง MVC๊ฐ ์ ๊ณตํ๋ @RequestMapping์ ๋ค์ํ ๋งคํ ๊ธฐ๋ฅ๊ณผ ํ์ฉ ๋ฐฉ๋ฒ์ ํ์ตํ๊ณ  ์ ๋ฆฌํ ํฌ์คํ์ด๋ค.**

---

# ์คํ๋ง MVC๊ฐ ์ ๊ณตํ๋ @RequestMapping์ ๋ค์ํ ๋งตํ ๊ธฐ๋ฅ๊ณผ ํ์ฉ ๋ฐฉ๋ฒ

์์ฒญ ๋งตํ(@RequestMapping)์ด๋ ์๋ฒ๋ก ๋ค์ด์ค๋ ์์ฒญ์ ๋ํด ์๋ง์ ์ปจํธ๋กค๋ฌ๊ฐ ํธ์ถ๋  ์ ์๋๋ก ๋งค์นญํ๋ ์์์ ์๋ฏธํ๋ค. ๋จ์ํ๊ฒ URL์ ๋งคํํ  ์๋ ์๊ณ , ๋งตํ๊ณผ ๋์์ URL ์ ๋ณด๋ฅผ ๋ฐ์ดํฐ๋ก ํ์ฉํ  ์๋ ์๋ค. spring MVC๊ฐ ์ ๊ณตํ๋ @RequestMapping์ ๋ค์ํ ๋งตํ ๊ธฐ๋ฅ๊ณผ ํ์ฉ๋ฒ์ ์์๋ณด์.


---

## 1. @RequestMapping๋ก URL ์ง์  ๋งตํ

```java
@RestController
public class MappingController {

    private Logger log = LoggerFactory.getLogger(getClass());

    /**
     * ๊ธฐ๋ณธ ์์ฒญ
     * ๋ฐฐ์ด์ ์ด์ฉํ์ฌ ๋๋ค ํ์ฉ =>  {"/hello-basic", "/hello-basic/"}
     * HTTP ๋ฉ์๋ ๋ชจ๋ ํ์ฉ GET, HEAD, POST, PUT, PATCH, DELETE
     */
    @RequestMapping("/hello-basic")
    public String helloBasic() {

        log.info("basic");
        return "OK >_< ";
    }
```

### 1-1. @RestController 
  
- `@Controller` ๋ ๋ฐํ ๊ฐ์ด String ์ด๋ฉด ๋ทฐ ์ด๋ฆ์ผ๋ก ์ธ์๋๋ค. ๊ทธ๋์ ๋ทฐ๋ฅผ ์ฐพ๊ณ  ๋ทฐ๊ฐ ๋๋๋ง ๋๋ค. 
- `@RestController`๋ ๋ฐํ ๊ฐ์ผ๋ก ๋ทฐ๋ฅผ ์ฐพ๋ ๊ฒ์ด ์๋๋ผ, HTTP ๋ฉ์์ง ๋ฐ๋์ ๋ฐ๋ก ์๋ ฅํ๋ค. ๋ฐ๋ผ์ ์คํ ๊ฒฐ๊ณผ๋ก ok ๋ฉ์ธ์ง๋ฅผ ๋ฐ์ ์ ์๋ค.

### 1-2. @RequestMapping("/hello-basic")`
- `/hello-basic` URL ํธ์ถ์ด ์ค๋ฉด ์ด ๋ฉ์๋๊ฐ ์คํ๋๋๋ก ๋งคํํ๋ค. 
- ๋๋ถ๋ถ์ ์์ฑ์ `๋ฐฐ์ด[]` ๋ก ์ ๊ณตํ๋ฏ๋ก ๋ค์ค ์ค์ ์ด ๊ฐ๋ฅํ๋ค. ์๋ ๋๊ฐ์ง ์์ฒญ์ ๋ค๋ฅธ URL์ด์ง๋ง, ์คํ๋ง์ ๋ค์ URL ์์ฒญ๋ค์ ๊ฐ์ ์์ฒญ์ผ๋ก ๋งคํํ๋ค. Postman์ผ๋ก ํ์คํธ๋ฅผ ํด๋ณผ ์ ์๋ค.
  - ex) {`"/hello-basic"`, `"/hello-go"`}
  - ์์ฒญ ๊ฐ๋ฅ URL: `/hello-basic`, `/hello-go/`


### 1-3. HTTP ๋ฉ์๋๋ฅผ ์ง์ ํ์ง ์์ ๊ฒฝ์ฐ ๋ชจ๋  ๋ฉ์๋๋ฅผ ์ฌ์ฉํ๋ค

`@RequestMapping`์ method ์์ฑ์ผ๋ก HTTP ๋ฉ์๋๋ฅผ ์ง์ ํ์ง ์์ผ๋ฉด HTTP ๋ฉ์๋์ ๋ฌด๊ดํ๊ฒ ํธ์ถ๋๋ค. 
- ๋ค์ ๋ฉ์๋๋ฅผ ๋ชจ๋ ํ์ฉํ๋ค
**ex) GET, HEAD, POST, PUT, PATCH, DELETE** 

---


## 2. method ์์ฑ์ ์ด์ฉํด HTTP ๋ฉ์๋ ์ง์  ๋งคํ 

`@RequestMapping`์ `method` ์์ฑ์ ์ด์ฉํ๋ฉด, ํน์  HTTP ๋ฉ์๋๋ง ์ง์ ํ์ฌ ๋งตํํ  ์ ์๋ค. ๋ง์ฝ, ์๋์ ๊ฐ์ด `method = RequestMethod.GET`์ผ๋ก ์ง์ ํ์๋ค๋ฉด, POST ์์ฒญ ์ ์คํ๋ง MVC๋ HTTP 405 ์ํ์ฝ๋(Method Not Allowed)๋ฅผ ๋ฐํํ๋ค. 

```java
/**
 * method ํน์  HTTP ๋ฉ์๋ ์์ฒญ๋ง ํ์ฉ
 * GET, HEAD, POST, PUT, PATCH, DELETE
 */
@RequestMapping(value = "/mapping-get-v1", method = RequestMethod.GET)
public String mappingGetV1() {
    log.info("mappingGetV1");
    return "ok";
}
```

---


## 3. @RequestMapping๊ณผ method ์์ฑ์ ํ๋ฒ์ ์ฌ์ฉํ๋ ์ ๋ธํ์ด์


์์ฒญ ๋งตํ๊ณผ ๋ฉ์๋ ์์ฑ์ ํ ๋ฒ์ ์ฌ์ฉํ  ์ ์๋ **`HTTP ์ถ์ฝ ๋ฉ์๋ ๋งคํ ์ ๋ธํ์ด์`**์ด ์๋ค. HTTP ๋ฉ์๋๋ฅผ ์ถ์ฝํ ์ ๋ธํ์ด์์ ์ฌ์ฉํ๋ฉด ๋ ์ง๊ด์ ์ธ ์ฝ๋๋ฅผ ๊ตฌํํ  ์ ์๋ค. ์ฝ๋ ๋ด๋ถ๋ฅผ ์ดํด๋ณด๋ฉด `@RequestMapping`๊ณผ `method`๋ฅผ ์ง์ ํด์ ์ฌ์ฉํ๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. 

> **`HTTP ์ถ์ฝ ๋ฉ์๋ ๋งคํ ์ ๋ธํ์ด์`**์ ์ข๋ฅ
- @GetMapping
- @PostMapping
- @PutMapping
- @DeleteMapping
- @PatchMapping

```java
@GetMapping(value = "/mapping-get-v2")
public String mappingGetV2() {
    log.info("mapping-get-v2");
    return "ok";
}
```


---

## 4. @PathVariable(๊ฒฝ๋ก ๋ณ์)๋ฅผ ์ด์ฉํ์ฌ URL์ ๋ฐ์ดํฐ๋ก ์ฌ์ฉํ๊ธฐ 

`@RequestMapping`์ URL ๊ฒฝ๋ก๋ฅผ ๋ฐ๋ก ๋ฐ์ดํฐ๋ก ๋ฐ์ ์ ์๋๋ก ํํ๋ฆฟ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. ์๋์ ๊ฐ์ด `@PathVariable`์ ์ฌ์ฉํ๋ฉด URL๊ณผ ๋งค์นญ๋๋ ์์ญ์ ๋ฐ์ดํฐ๋ฅผ ํธ๋ฆฌํ๊ฒ ์กฐํํ  ์ ์๋ค. ๋ํ, `@PathVariable`์ ๋ณ์ ์ด๋ฆ๊ณผ URL ์์ญ์ ์ด๋ฆ์ด ๊ฐ๋ค๋ฉด ๋ณ๋์ ์ด๋ฆ์ ์ง์ ํ์ง ์์๋ ์ฌ์ฉํ  ์ ์๋ `์๋ต ๊ธฐ๋ฅ`์ ์ ๊ณตํ๋ค. 

```java
/**
 * PathVariable(๊ฒฝ๋ก ๋ณ์) ์ฌ์ฉ
 * ๋ณ์๋ช์ด ๊ฐ์ผ๋ฉด ์๋ต ๊ฐ๋ฅ
 *
 * @PathVariable("userId") String userId -> @PathVariable userId
 */
@GetMapping("/mapping/{userid}")
public String mappingPath(@PathVariable String userid) {
    log.info("mappingPath userId={}", userid);
    return "ok";
}
```

์ต๊ทผ HTTP API๋ ๋ค์๊ณผ ๊ฐ์ด ๋ฆฌ์์ค ๊ฒฝ๋ก์ ์๋ณ์๋ฅผ ๋ฃ๋ ์คํ์ผ์ ์ ํธํ๋ค. `@PathVariable`์ ์ด์ฉํ์ฌ ํธ๋ฆฌํ๊ฒ ์๋ณ์๋ฅผ ํ์ฉํ  ์ ์๋ค.

---


## 5. @PathVariable์ ๋ค์ค ์ฌ์ฉ

`@PathVariable`๋ 2๊ฐ ์ด์์ ๊ฒฝ๋ก ๋ณ์๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.

```java
/**
 * @PathVariable๋ ๋ค์ค ์ฌ์ฉ์ด ๊ฐ๋ฅํ๋ค.
 */
@GetMapping("/mapping/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
    log.info("mappingPath userId={}, orderId={}", userId, orderId);
    return "ok";
}
```

---


## 6. ํน์  ํ๋ผ๋ฏธํฐ ์กฐ๊ฑด์ผ๋ก ๋งคํํ๋ ๋ฐฉ๋ฒ 

`@RequestMapping`๋ ํ๋ผ๋ฏธํฐ๋ฅผ ์ด์ฉํ์ฌ ํน์ ํ ๋งตํ ์กฐ๊ฑด์ ๋ถ์ฌ ํ  ์ ์๋ค. ์๋์ ๊ฐ์ด ์ฌ๋ฌ๊ฐ์ง ์กฐ๊ฑด์ผ๋ก ๋งตํ์ด ๊ฐ๋ฅํ๋ค. ์กฐ๊ฑด์ ๋ฐ๋ผ ํน์  ํ๋ผ๋ฏธํฐ๊ฐ ์๊ฑฐ๋, ์์ ๋๋ง ๋งตํ์ด ๋๋ค. ์ ์ฌ์ฉํ์ง๋ ์๋ ๊ธฐ๋ฅ์ด๋ผ๊ณ  ํ๋ค.

> **`@RequestMapping`์ ๋ค์ํ ์ถ๊ฐ ์ต์ ์ค์  ๋ฐฉ๋ฒ**
 * params="mode",
 * params="!mode"
 * params="mode=debug"
 * params="mode!=debug" (! = )
 * params = {"mode=debug","data=good"}

```java
@GetMapping(value = "/mapping-param", params = "mode=debug")
public String mappingParam() {
    log.info("mappingParam");
    return "ok";
}
```


---

## 7. ํค๋ ์กฐ๊ฑด์ผ๋ก ๋งคํํ๋ ๋ฐฉ๋ฒ

`@RequestMapping`๋ ํน์ ํ ํค๋ ์กฐ๊ฑด์ ๋ถ์ฌํ์ฌ ๋งตํํ  ์ ์๋ค. 6๋ฒ ํ๋ผ๋ฏธํฐ ๋งคํ๊ณผ ๋น์ทํ์ง๋ง, HTTP ํค๋๋ฅผ ์ฌ์ฉํ๋ค๋ ์ฐจ์ด์ ์ด ์๋ค.

> **`@RequestMapping`์ ๋ค์ํ ์ถ๊ฐ ํค๋ ์กฐ๊ฑด ์ค์  ๋ฐฉ๋ฒ**
 * headers="mode",
 * headers="!mode"
 * headers="mode=debug"
 * headers="mode!=debug" (! = )

```java
@GetMapping(value = "/mapping-header", headers = "mode=debug")
public String mappingHeader() {
    log.info("mappingHeader");
    return "ok";
}
```

---


## 8. consume์ ์ด์ฉํ์ฌ ๋ฏธ๋์ด ํ์(Content-Type) ์กฐ๊ฑด์ผ๋ก ๋งตํํ๋ ๋ฐฉ๋ฒ

`consumes` ์ค์ ์ ์ด์ฉํ๋ฉด, `Content-Type` ํค๋ ์ ๋ณด๋ฅผ ์ถ๊ฐ ๋งคํ ์กฐ๊ฑด์ผ๋ก ์ฌ์ฉํ  ์ ์๋ค. 

> **๋ค์ํ `consumes` ์ค์  ๋ฐฉ๋ฒ ์์**
 * consumes = "text/plain"
 * consumes = {"text/plain", "application/*"}
 * consumes = MediaType.TEXT_PLAIN_VALUE 
 * consumes = "application/json"
 * consumes = "!application/json"
 * consumes = "application/*"
 * consumes = "*\/*" 
 * consumes = MediaType.APPLICATION_JSON_VALUE => "application/json"

```java
@PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)
public String mappingConsumes() {
    log.info("mappingConsumes");
    return "ok";
}
```
HTTP ์์ฒญ์ `Content-Type` ํค๋๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ๋ฏธ๋์ด ํ์์ผ๋ก ๋งคํํ๋ค. ๋ง์ฝ ์กฐ๊ฑด์ ๋ง์ง ์์ผ๋ฉด `HTTP 415 ์ํ์ฝ๋(Unsupported Media Type)`์ ๋ฐํํ๋ค. 

---

## 9. produce๋ฅผ ์ด์ฉํ์ฌ ๋ฏธ๋์ด ํ์ ๋ฐํ(Accept) ์กฐ๊ฑด์ผ๋ก ๋งตํํ๋ ๋ฐฉ๋ฒ

> **๋ค์ํ `produce` ์ค์  ๋ฐฉ๋ฒ ์์**
 * produces = "text/html"
 * produces = "!text/html" 
 * produces = "text/*"
 * produces = "*\/*"
 * produces = "text/plain"
 * produces = {"text/plain", "application/*"}
 * produces = MediaType.TEXT_PLAIN_VALUE
 * produces = "text/plain;charset=UTF-8"
 * MediaType.TEXT_HTML_VALUE => "text/html"
 

```java
@PostMapping(value = "/mapping-produce", produces = MediaType.TEXT_HTML_VALUE)
public String mappingProduces() {
    log.info("mappingProduces");
    return "ok";
}
```
HTTP ์์ฒญ์ `Accept` ํค๋๋ฅผ ๊ธฐ๋ฐ์ผ๋ก ๋ฏธ๋์ด ํ์์ผ๋ก ๋งคํํ๋ค. ๋ง์ฝ ๋ง์ง ์์ผ๋ฉด `HTTP 406 ์ํ์ฝ๋(Not Acceptable)`์ ๋ฐํํ๋ค. 



---


## 10. @RequestMapping๋ฅผ ํ์ฉํด ํ์ ๊ด๋ฆฌ API ๋ง๋ค์ด ๋ณด๊ธฐ

ํ์ ๊ด๋ฆฌ๋ฅผ HTTP API๋ก ๋ง๋ ๋ค๊ณ  ๊ฐ์ ํ๊ณ  @RequestMapping์ ์ด๋ป๊ฒ ํ์ฉํ  ์ ์๋์ง ์์๋ณด์. ์ค์  ๋ฐ์ดํฐ๊ฐ ๋์ด๊ฐ๋ ๋ถ๋ถ์ ์๋ตํ๋ค. URL ๋งตํ ํ์ฉ์ ๋ํด์๋ง ๋ค๋ค ๋ณผ ๊ฒ์ด๋ค.


> **ํ์ ๊ด๋ฆฌ API** 
- ํ์ ๋ชฉ๋ก ์กฐํ: **GET** -> `/users`
- ํ์ ๋ฑ๋ก: **POST** -> `/users`
- ํ์ ์กฐํ: **GET** ->  `/users/{userId}`
- ํ์์์ : **PATCH** -> `/users/{userId}`
- ํ์ ์ญ์ : **DELETE** -> `/users/{userId}`


**MappingClassController** 

```java
package hello.springmvc.basic.requestmapping;

import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/mapping/users")
public class MappingClassController {
    
    /**
     * GET /mapping/users
     */
    @GetMapping
    public String users() {
        return "get users";
    }

    /**
     * POST /mapping/users
     */
    @PostMapping
    public String addUser() {
        return "post user";
    }

    /**
     * GET /mapping/users/{userId}
     */
    @GetMapping("/{userId}")
    public String findUser(@PathVariable String userId) {
        return "get userId=" + userId;
    }

    /**
     * PATCH /mapping/users/{userId}
     */
    @PatchMapping("/{userId}")
    public String updateUser(@PathVariable String userId) {
        return "update userId=" + userId;
    }

    /**
     * DELETE /mapping/users/{userId}
     */
    @DeleteMapping("/{userId}")
    public String deleteUser(@PathVariable String userId) {
        return "delete userId=" + userId;
    }
}

```

 
**`@RequestMapping("/mapping/users")`**  
- ํด๋์ค ๋ ๋ฒจ์ ๊ณตํต๋๋ ๋งคํ ์ ๋ณด๋ฅผ ๋ช์ํ๋ฉด, ๋ฉ์๋ ๋ ๋ฒจ์์ ํด๋น ์ ๋ณด๋ฅผ ์กฐํฉํ์ฌ ์ฌ์ฉํ๋ค.
