> ### 📖 ✏️ 
> **이 글은 세션 기능을 구현할 때 사용할 수 있는 두 가지 방법(서블릿이 제공하는 `HttpSession`와 스프링이 제공하는 `@SessionAttribute`)을 학습하고 정리한 포스팅이다.**

---

# 서블릿이 제공하는 HttpSession, 스프링이 제공하는 @SessionAttribute

세션이라는 개념은 대부분의 웹 애플리케이션 개발 시 사용된다. 웹 개발에 필수적인 역할을 하는 서블릿과 스프링은 각각 세션을 쉽게 사용할 수 있는 별도의 기능을 제공한다. 서블릿이 제공하는 `HttpSession`와 스프링이 제공하는 `@SessionAttribute`의 사용 방법을 살펴보자. 직접 구현하는 방식의 세션보다 훨씬 빠르고 간편하게 세션을 구현할 수 있다. 


---

## 1. HttpSession을 이용하여 세션을 직접 구현

서블릿이 제공하는 `HttpSession`은 `JSESSIONID`이라는 이름의 세션을 자동으로 생성한다.

> ex) Cookie: JSESSIONID=5B78E23B513F50164D6FDD8C97B0AD05

`HttpSession`에 데이터를 보관하고 조회할 때, 같은 이름이 중복 되어 사용되므로 아래와 같이 상수를 정의하면 좋다.

**SessionConst**
```java
package hello.login.web;

public class SessionConst {
    public static final String LOGIN_MEMBER = "loginMember";
} 
```

로그인을 담당하는 컨트롤러에 세션을 구현해보자. 세션을 생성하려면  `request.getSession(true)`를 사용하면 된다. 세션의 `create` 옵션은 아래와 같다.

- `request.getSession(true)` 
	- 세션이 있으면 기존 세션을 반환한다. 
	- 세션이 없으면 새로운 세션을 생성해서 반환한다. 
- `request.getSession(false)` 
	- 세션이 있으면 기존 세션을 반환한다. 
	- 세션이 없으면 새로운 세션을 생성하지 않는다. null 을 반환한다. 
    
    
세션에 데이터를 보관하는 방법은 `request.setAttribute(..)`와 비슷하다. 하나의 세션에 여러 값을 저장할 수 있다. 

```java
session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);
```


**LoginController - loginV3()** 

```java
// Post 요청 시, 검증을 담당하는 @Valid와 에러 메시지를 관리하는 BindingResult가 필요하다
@PostMapping("/login")
public String loginV3(@Valid @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletRequest request) {

    if (bindingResult.hasErrors()) {
        // bindingResult에 문제가 있으면 다시 돌려보낸다.
        return "login/loginForm";
    }

    // 성공로직: form에서 id와 password 꺼내서 검증한다
    Member loginMember = loginService.login(form.getLoginId(), form.getPassword());

    // id와 pass가 맞지 않을 경우 bindingResult를 사용하여 에러 메시지를 출력한다
    if (loginMember == null) {
        bindingResult.reject("loginFail", "아이디 또는 비밀번호 오류");
        return "login/loginForm";
    }

    // 로그인 성공 처리
    // getSession()은 세션이 있으면 있는 세션을 반환하고, 없으면 신규 세션을 생성한다
    HttpSession session = request.getSession();
    // 세션의 로그인 회원 정보를 보관한다
    session.setAttribute(SessionConstant.LOGIN_MEMBER, loginMember);

    return "redirect:/";
}
```

### 1-1. HttpSession을 이용하여 로그아웃 처리하기

`session.invalidate()`를 이용하면 세션을 제거할 수 있다. 세션을 삭제하는 방식으로 로그아웃 기능을 구현할 수 있다.


**LoginController - logoutV3()** 

```java
// 로그아웃 처리 - V3
@PostMapping("/logout")
public String logoutV3(HttpServletRequest request) {
    // 새로 생성하지 않는 조건(false)로 세션을 조회한다
    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate(); // 세션 정보를 삭제한다
    }
    return "redirect:/";
}
```

### 1-2. 로그인 화면에서는 세션을 생성하지 않기

`request.getSession()`를 사용하면 기본 값이 `create:true`가 된다. 로그인 하지 않을 사용자도 의미 없는 세션이 만들어진다. 따라서 세션을 찾아서 사용하는 시점에는 `create: false` 옵션을 사용하여 세션을 생성하지 않도록 설정한다.


**HomeController - homeLoginV3()** 
```java
@GetMapping("/")
public String homeLoginV3(HttpServletRequest request, Model model) {

    // 로그인 화면에서 세션이 생성되지 않도록 false 처리
    HttpSession session = request.getSession(false);
    if (session == null) {
        return "home";
    }

    // 상수를 이용해 세션에서 로그인 객체를 꺼낸다
    Member loginMember = (Member) session.getAttribute(SessionConstant.LOGIN_MEMBER);

    // 세션에 회원 데이터가 없으면 home으로 이동
    if (loginMember == null) {
        return "home";
    }

    // 세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember);
    return "loginHome";

}
```



### 1-3. 세션이 제공하는 정보 조회하기

아래와 같이 세션이 제공하는 정보를 확인할 수 있다. log 예제를 참고해보자.

> - `sessionId`: 세션Id, JSESSIONID 의 값이다. 
예) 34B14F008AA3527C9F8ED620EFD7A4E1 
> - `maxInactiveInterval`: 세션의 유효 시간
예) 1800초, (30분) 
> - `creationTime`: 세션 생성일시 
> - `lastAccessedTime`: 세션과 연결된 사용자가 최근에 서버에 접근한 시간이다. 클라이언트에서 서버로 `sessionId(JSESSIONID)`를 요청한 경우에 갱신된다. 
> - `isNew`: 새로 생성된 세션인지, 클라이언트에서 서버로 `sessionId(JSESSIONID)`를 요청해서 조회된 세션인지 여부를 확인한다. 


**SessionInfoController - log 예제**

```java
package hello.login.web.session;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpSession;
import java.util.Date;

@Slf4j
@RestController
public class SessionInfoController {

    @GetMapping("/session-info")
    public String sessionInfo(HttpServletRequest request) {
        HttpSession session = request.getSession(false);// session을 받고, 없으면 만들지 않는다

        // 세션이 없을 경우
        if (session == null) {
            return "세션이 없습니다.";
        }

        // asIterator: 세션이 있을 경우, 세션의 name 정보를 조회하는 작업을 전체 반복한다.
        // forEachRemaining: 모든 요소가 처리되거나 작업이 예외를 발생시킬 때까지 남아 있는 각 요소에 대해 지정된 작업을 수행
        session.getAttributeNames().asIterator()
                .forEachRemaining(name -> log.info("session name={}, value={}", name, session.getAttribute(name)));

        log.info("sessionId={}", session.getId());
        log.info("getMaxInactiveInternal={}", session.getMaxInactiveInterval()); // 활성화 기간
        log.info("creationTime={}", new Date(session.getCreationTime()));
        log.info("lastAccessedTime={}", new Date(session.getLastAccessedTime())); // 사용자가 마지막 접근한 시간
        log.info("isNew={}", session.isNew()); // 새로 생성된 세션 여부

        return "세션 출력";
    }
}

```

### 1-3. 세션의 타임아웃과 종료 시점 설정

세션은 사용자가 로그아웃을 직접 호출해서 `session.invalidate()`가 호출 되는 경우에 삭제된다. 하지만, 대부분의 사용자는 로그아웃을 직접 하지 않는다. 그냥 웹 브라우저를 종료하는 경우가 대부분이다. 문제는 HTTP가 비 연결성(ConnectionLess)이므로, 서버 입장에서는 해당 사용자가 웹 브라우저를 종료한 것인지 인식할 수 없다. 따라서, 서버에서는 세션 데이터를 언제 삭제해야 하는지 판단하기 어렵다. 

이와 같은 문제로 인해 세션을 무한정 보관하면 아래와 같은 문제가 발생한다.

> - 세션과 관련된 쿠키(JSESSIONID)를 탈취 당했을 경우, 오랜 시간이 지난 후에 해당 쿠키로 발생하는 악의적인 요청이 유효하다. 
> - 세션은 기본적으로 메모리에 생성된다. 메모리의 크기가 무한하지 않기 때문에 꼭 필요한 경우만 생성해서 사용해야 한다. 10만 명의 사용자가 로그인하면 10만 개의 세션이 생성된다.


그렇다면, 세션의 유효 기간은 얼마나 설정하는 것이 좋을까? 일반적으로 생성 시점으로부터 30분 정도를 유효 기간으로 부여한다. 하지만, 단순히 생성 후 30분을 유효 기간으로 부여하면 30분마다 재로그인을 해야 한다. 이에 대한 대안으로 사용자가 서버에 마지막 요청을 보낸 시간을 기준으로 30분 정도의 유효 기간을 부여한다. HttpSession이 바로 이 방식을 사용한다.

사용자가 서비스를 계속 이용하고 있다면, 세션의 유효 기간이 계속 30분으로 갱신되기 때문에 불필요한 재로그인의 번거로움을 해결할 수 있다.

세션의 타임아웃은 아래와 같이 스프링 부트의 글로벌 설정을 이용할 수 있다. 

**application.properties**

```java
server.servlet.session.timeout=60
// 60초, 기본은 1800(30분) 
// (글로벌 설정은 분 단위로 설정해야 한다. 60(1분), 120(2분), ...) 
```


특정 세션 단위로 시간을 설정할 경우 아래와 같이 설정한다.

```java
session.setMaxInactiveInterval(1800);
//1800초   
```

세션의 타임아웃 시간은 해당 세션과 관련된 `JSESSIONID`를 전달하는 `HTTP 요청`이 있을 경우, 현재 시간으로 다시 초기화 된다. 초기화가 되면 세션 타임아웃으로 설정한 시간 동안 세션을 추가로 사용할 수 있다.

서블릿의 `HttpSession`이 제공하는 타임아웃 기능은 세션을 안전하고 편리하게 사용할 수 있도록 도와준다. 다만, 세션에 불필요한 데이터가 들어가지 않도록 주의해야 한다. 메모리 문제가 발생할 수 있으므로, 언제나 최소한의 데이터만 보관해야 한다. 

`(보관한 데이터 용량 * 사용자 수)`로 세션의 메모리 사용량이 급격하게 늘어나면, 장애의 원인이 되기도 한다. 또한 세션의 시간을 불필요하게 길게 설정하지 않도록 한다. 유효 기간이 늘어나면 메모리 사용도 누적된다. 약 30분을 전후로 상황에 맞게 적당한 시간을 설정하자.


---

## 2. 스프링이 제공하는 @SessionAttribute을 이용하여 세션 구현

스프링은 세션을 더 편리하게 사용할 수 있도록 `@SessionAttribute`을 지원한다. 이미 로그인 된 사용자를 찾을 때는 아래와 같이 사용한다. 이 기능은 세션을 생성하지 않는다. 

```java
@SessionAttribute(name = "loginMember", required = false) Member loginMember
```

아래 예제 코드를 살펴보자. 세션을 찾고, 세션에 들어있는 데이터를 찾는 번거로운 과정을 스프링이 한번에 처리해준다.

**HomeController - homeLoginV3Spring()** 

```java
@GetMapping("/")
public String homeLoginV3Spring(
        // @SesseionAttribute는 속성에 해당하는 세션을 가지고 있는 Member 객체를 꺼낸다
        @SessionAttribute(name = SessionConstant.LOGIN_MEMBER, required = false) Member loginMember, Model model) {

    // 세션에 회원 데이터가 없으면 home으로 이동
    if (loginMember == null) {
        return "home";
    }

    // 세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember);
    return "loginHome";
}
```

 
### 2-1. TrackingModes 


로그인을 처음 시도하면 아래와 같이 URL에 jsessionid이 포함되어 있다.
`http://localhost:8080/;jsessionid=F59911518B921DF62D09F0DF8F83F872`

이것은 웹 브라우저가 쿠키를 지원하지 않을 때 쿠키 대신 URL을 통해서 세션을 유지하는 방법이다. 서버 입장에서 웹 브라우저가 쿠키를 지원하는지 하지 않는지 최초에는 판단하지 못하므로, 쿠키 값도 전달하고, URL에 jsessionid 도 함께 전달한다. 이 방법을 사용하려면 URL에 값을 계속 포함해야 한다. 참고로 타임리프 같은 템플릿은 엔진을 통해서 URL에 `jsessionid`를 자동으로 포함하는 기능을 제공한다. 
 
만약 URL 전달 방식을 사용하지 않고, 항상 쿠키를 이용해 세션을 유지하고 싶다면 아래와 같이 설정해야 한다. 이렇게 하면 URL에 `jsessionid`가 노출되지 않는다. 

**application.properties**
```java
server.servlet.session.tracking-modes=cookie
```


