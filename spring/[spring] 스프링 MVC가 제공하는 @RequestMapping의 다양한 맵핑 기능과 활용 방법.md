> ### 📖 ✏️ 
> **이 글은 스프링 MVC가 제공하는 @RequestMapping의 다양한 매핑 기능과 활용 방법을 학습하고 정리한 포스팅이다.**

---

# 스프링 MVC가 제공하는 @RequestMapping의 다양한 맵핑 기능과 활용 방법

요청 맵핑(@RequestMapping)이란 서버로 들어오는 요청에 대해 알맞은 컨트롤러가 호출될 수 있도록 매칭하는 작업을 의미한다. 단순하게 URL을 매핑할 수도 있고, 맵핑과 동시에 URL 정보를 데이터로 활용할 수도 있다. spring MVC가 제공하는 @RequestMapping의 다양한 맵핑 기능과 활용법을 알아보자.


---

## 1. @RequestMapping로 URL 직접 맵핑

```java
@RestController
public class MappingController {

    private Logger log = LoggerFactory.getLogger(getClass());

    /**
     * 기본 요청
     * 배열을 이용하여 둘다 허용 =>  {"/hello-basic", "/hello-basic/"}
     * HTTP 메서드 모두 허용 GET, HEAD, POST, PUT, PATCH, DELETE
     */
    @RequestMapping("/hello-basic")
    public String helloBasic() {

        log.info("basic");
        return "OK >_< ";
    }
```

### 1-1. @RestController 
  
- `@Controller` 는 반환 값이 String 이면 뷰 이름으로 인식된다. 그래서 뷰를 찾고 뷰가 랜더링 된다. 
- `@RestController`는 반환 값으로 뷰를 찾는 것이 아니라, HTTP 메시지 바디에 바로 입력한다. 따라서 실행 결과로 ok 메세지를 받을 수 있다.

### 1-2. @RequestMapping("/hello-basic")`
- `/hello-basic` URL 호출이 오면 이 메서드가 실행되도록 매핑한다. 
- 대부분의 속성을 `배열[]` 로 제공하므로 다중 설정이 가능하다. 아래 두가지 요청은 다른 URL이지만, 스프링은 다음 URL 요청들을 같은 요청으로 매핑한다. Postman으로 테스트를 해볼 수 있다.
  - ex) {`"/hello-basic"`, `"/hello-go"`}
  - 요청 가능 URL: `/hello-basic`, `/hello-go/`


### 1-3. HTTP 메서드를 지정하지 않을 경우 모든 메서드를 사용한다

`@RequestMapping`에 method 속성으로 HTTP 메서드를 지정하지 않으면 HTTP 메서드와 무관하게 호출된다. 
- 다음 메서드를 모두 허용한다
**ex) GET, HEAD, POST, PUT, PATCH, DELETE** 

---


## 2. method 속성을 이용해 HTTP 메서드 지정 매핑 

`@RequestMapping`의 `method` 속성을 이용하면, 특정 HTTP 메서드만 지정하여 맵핑할 수 있다. 만약, 아래와 같이 `method = RequestMethod.GET`으로 지정하였다면, POST 요청 시 스프링 MVC는 HTTP 405 상태코드(Method Not Allowed)를 반환한다. 

```java
/**
 * method 특정 HTTP 메서드 요청만 허용
 * GET, HEAD, POST, PUT, PATCH, DELETE
 */
@RequestMapping(value = "/mapping-get-v1", method = RequestMethod.GET)
public String mappingGetV1() {
    log.info("mappingGetV1");
    return "ok";
}
```

---


## 3. @RequestMapping과 method 속성을 한번에 사용하는 애노테이션


요청 맵핑과 메서드 속성을 한 번에 사용할 수 있는 **`HTTP 축약 메서드 매핑 애노테이션`**이 있다. HTTP 메서드를 축약한 애노테이션을 사용하면 더 직관적인 코드를 구현할 수 있다. 코드 내부를 살펴보면 `@RequestMapping`과 `method`를 지정해서 사용하는 것을 확인할 수 있다. 

> **`HTTP 축약 메서드 매핑 애노테이션`**의 종류
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

## 4. @PathVariable(경로 변수)를 이용하여 URL을 데이터로 사용하기 

`@RequestMapping`은 URL 경로를 바로 데이터로 받을 수 있도록 템플릿 기능을 제공한다. 아래와 같이 `@PathVariable`을 사용하면 URL과 매칭되는 영역의 데이터를 편리하게 조회할 수 있다. 또한, `@PathVariable`의 변수 이름과 URL 영역의 이름이 같다면 별도의 이름을 지정하지 않아도 사용할 수 있는 `생략 기능`을 제공한다. 

```java
/**
 * PathVariable(경로 변수) 사용
 * 변수명이 같으면 생략 가능
 *
 * @PathVariable("userId") String userId -> @PathVariable userId
 */
@GetMapping("/mapping/{userid}")
public String mappingPath(@PathVariable String userid) {
    log.info("mappingPath userId={}", userid);
    return "ok";
}
```

최근 HTTP API는 다음과 같이 리소스 경로에 식별자를 넣는 스타일을 선호한다. `@PathVariable`을 이용하여 편리하게 식별자를 활용할 수 있다.

---


## 5. @PathVariable의 다중 사용

`@PathVariable`는 2개 이상의 경로 변수를 사용할 수 있다.

```java
/**
 * @PathVariable는 다중 사용이 가능하다.
 */
@GetMapping("/mapping/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable String userId, @PathVariable Long orderId) {
    log.info("mappingPath userId={}, orderId={}", userId, orderId);
    return "ok";
}
```

---


## 6. 특정 파라미터 조건으로 매핑하는 방법 

`@RequestMapping`는 파라미터를 이용하여 특정한 맵핑 조건을 부여 할 수 있다. 아래와 같이 여러가지 조건으로 맵핑이 가능하다. 조건에 따라 특정 파라미터가 있거나, 없을 때만 맵핑이 된다. 잘 사용하지는 않는 기능이라고 한다.

> **`@RequestMapping`의 다양한 추가 옵션 설정 방법**
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

## 7. 헤더 조건으로 매핑하는 방법

`@RequestMapping`는 특정한 헤더 조건을 부여하여 맵핑할 수 있다. 6번 파라미터 매핑과 비슷하지만, HTTP 헤더를 사용한다는 차이점이 있다.

> **`@RequestMapping`의 다양한 추가 헤더 조건 설정 방법**
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


## 8. consume을 이용하여 미디어 타입(Content-Type) 조건으로 맵핑하는 방법

`consumes` 설정을 이용하면, `Content-Type` 헤더 정보를 추가 매핑 조건으로 사용할 수 있다. 

> **다양한 `consumes` 설정 방법 예시**
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
HTTP 요청의 `Content-Type` 헤더를 기반으로 미디어 타입으로 매핑한다. 만약 조건에 맞지 않으면 `HTTP 415 상태코드(Unsupported Media Type)`을 반환한다. 

---

## 9. produce를 이용하여 미디어 타입 반환(Accept) 조건으로 맵핑하는 방법

> **다양한 `produce` 설정 방법 예시**
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
HTTP 요청의 `Accept` 헤더를 기반으로 미디어 타입으로 매핑한다. 만약 맞지 않으면 `HTTP 406 상태코드(Not Acceptable)`을 반환한다. 



---


## 10. @RequestMapping를 활용해 회원 관리 API 만들어 보기

회원 관리를 HTTP API로 만든다고 가정하고 @RequestMapping을 어떻게 활용할 수 있는지 알아보자. 실제 데이터가 넘어가는 부분을 생략한다. URL 맵핑 활용에 대해서만 다뤄 볼 것이다.


> **회원 관리 API** 
- 회원 목록 조회: **GET** -> `/users`
- 회원 등록: **POST** -> `/users`
- 회원 조회: **GET** ->  `/users/{userId}`
- 회원수정: **PATCH** -> `/users/{userId}`
- 회원 삭제: **DELETE** -> `/users/{userId}`


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
- 클래스 레벨에 공통되는 매핑 정보를 명시하면, 메서드 레벨에서 해당 정보를 조합하여 사용한다.
