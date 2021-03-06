> ### π βοΈ 
> **μ΄ κΈμ μΈμ κΈ°λ₯μ κ΅¬νν  λ μ¬μ©ν  μ μλ λ κ°μ§ λ°©λ²(μλΈλ¦Ώμ΄ μ κ³΅νλ `HttpSession`μ μ€νλ§μ΄ μ κ³΅νλ `@SessionAttribute`)μ νμ΅νκ³  μ λ¦¬ν ν¬μ€νμ΄λ€.**

---

# μλΈλ¦Ώμ΄ μ κ³΅νλ HttpSession, μ€νλ§μ΄ μ κ³΅νλ @SessionAttribute

μΈμμ΄λΌλ κ°λμ λλΆλΆμ μΉ μ νλ¦¬μΌμ΄μ κ°λ° μ μ¬μ©λλ€. μΉ κ°λ°μ νμμ μΈ μ­ν μ νλ μλΈλ¦Ώκ³Ό μ€νλ§μ κ°κ° μΈμμ μ½κ² μ¬μ©ν  μ μλ λ³λμ κΈ°λ₯μ μ κ³΅νλ€. μλΈλ¦Ώμ΄ μ κ³΅νλ `HttpSession`μ μ€νλ§μ΄ μ κ³΅νλ `@SessionAttribute`μ μ¬μ© λ°©λ²μ μ΄ν΄λ³΄μ. μ§μ  κ΅¬ννλ λ°©μμ μΈμλ³΄λ€ ν¨μ¬ λΉ λ₯΄κ³  κ°νΈνκ² μΈμμ κ΅¬νν  μ μλ€. 


---

## 1. HttpSessionμ μ΄μ©νμ¬ μΈμμ μ§μ  κ΅¬ν

μλΈλ¦Ώμ΄ μ κ³΅νλ `HttpSession`μ `JSESSIONID`μ΄λΌλ μ΄λ¦μ μΈμμ μλμΌλ‘ μμ±νλ€.

> ex) Cookie: JSESSIONID=5B78E23B513F50164D6FDD8C97B0AD05

`HttpSession`μ λ°μ΄ν°λ₯Ό λ³΄κ΄νκ³  μ‘°νν  λ, κ°μ μ΄λ¦μ΄ μ€λ³΅ λμ΄ μ¬μ©λλ―λ‘ μλμ κ°μ΄ μμλ₯Ό μ μνλ©΄ μ’λ€.

**SessionConst**
```java
package hello.login.web;

public class SessionConst {
    public static final String LOGIN_MEMBER = "loginMember";
} 
```

λ‘κ·ΈμΈμ λ΄λΉνλ μ»¨νΈλ‘€λ¬μ μΈμμ κ΅¬νν΄λ³΄μ. μΈμμ μμ±νλ €λ©΄  `request.getSession(true)`λ₯Ό μ¬μ©νλ©΄ λλ€. μΈμμ `create` μ΅μμ μλμ κ°λ€.

- `request.getSession(true)` 
	- μΈμμ΄ μμΌλ©΄ κΈ°μ‘΄ μΈμμ λ°ννλ€. 
	- μΈμμ΄ μμΌλ©΄ μλ‘μ΄ μΈμμ μμ±ν΄μ λ°ννλ€. 
- `request.getSession(false)` 
	- μΈμμ΄ μμΌλ©΄ κΈ°μ‘΄ μΈμμ λ°ννλ€. 
	- μΈμμ΄ μμΌλ©΄ μλ‘μ΄ μΈμμ μμ±νμ§ μλλ€. null μ λ°ννλ€. 
    
    
μΈμμ λ°μ΄ν°λ₯Ό λ³΄κ΄νλ λ°©λ²μ `request.setAttribute(..)`μ λΉμ·νλ€. νλμ μΈμμ μ¬λ¬ κ°μ μ μ₯ν  μ μλ€. 

```java
session.setAttribute(SessionConst.LOGIN_MEMBER, loginMember);
```


**LoginController - loginV3()** 

```java
// Post μμ²­ μ, κ²μ¦μ λ΄λΉνλ @Validμ μλ¬ λ©μμ§λ₯Ό κ΄λ¦¬νλ BindingResultκ° νμνλ€
@PostMapping("/login")
public String loginV3(@Valid @ModelAttribute LoginForm form, BindingResult bindingResult, HttpServletRequest request) {

    if (bindingResult.hasErrors()) {
        // bindingResultμ λ¬Έμ κ° μμΌλ©΄ λ€μ λλ €λ³΄λΈλ€.
        return "login/loginForm";
    }

    // μ±κ³΅λ‘μ§: formμμ idμ password κΊΌλ΄μ κ²μ¦νλ€
    Member loginMember = loginService.login(form.getLoginId(), form.getPassword());

    // idμ passκ° λ§μ§ μμ κ²½μ° bindingResultλ₯Ό μ¬μ©νμ¬ μλ¬ λ©μμ§λ₯Ό μΆλ ₯νλ€
    if (loginMember == null) {
        bindingResult.reject("loginFail", "μμ΄λ λλ λΉλ°λ²νΈ μ€λ₯");
        return "login/loginForm";
    }

    // λ‘κ·ΈμΈ μ±κ³΅ μ²λ¦¬
    // getSession()μ μΈμμ΄ μμΌλ©΄ μλ μΈμμ λ°ννκ³ , μμΌλ©΄ μ κ· μΈμμ μμ±νλ€
    HttpSession session = request.getSession();
    // μΈμμ λ‘κ·ΈμΈ νμ μ λ³΄λ₯Ό λ³΄κ΄νλ€
    session.setAttribute(SessionConstant.LOGIN_MEMBER, loginMember);

    return "redirect:/";
}
```

### 1-1. HttpSessionμ μ΄μ©νμ¬ λ‘κ·Έμμ μ²λ¦¬νκΈ°

`session.invalidate()`λ₯Ό μ΄μ©νλ©΄ μΈμμ μ κ±°ν  μ μλ€. μΈμμ μ­μ νλ λ°©μμΌλ‘ λ‘κ·Έμμ κΈ°λ₯μ κ΅¬νν  μ μλ€.


**LoginController - logoutV3()** 

```java
// λ‘κ·Έμμ μ²λ¦¬ - V3
@PostMapping("/logout")
public String logoutV3(HttpServletRequest request) {
    // μλ‘ μμ±νμ§ μλ μ‘°κ±΄(false)λ‘ μΈμμ μ‘°ννλ€
    HttpSession session = request.getSession(false);
    if (session != null) {
        session.invalidate(); // μΈμ μ λ³΄λ₯Ό μ­μ νλ€
    }
    return "redirect:/";
}
```

### 1-2. λ‘κ·ΈμΈ νλ©΄μμλ μΈμμ μμ±νμ§ μκΈ°

`request.getSession()`λ₯Ό μ¬μ©νλ©΄ κΈ°λ³Έ κ°μ΄ `create:true`κ° λλ€. λ‘κ·ΈμΈ νμ§ μμ μ¬μ©μλ μλ―Έ μλ μΈμμ΄ λ§λ€μ΄μ§λ€. λ°λΌμ μΈμμ μ°Ύμμ μ¬μ©νλ μμ μλ `create: false` μ΅μμ μ¬μ©νμ¬ μΈμμ μμ±νμ§ μλλ‘ μ€μ νλ€.


**HomeController - homeLoginV3()** 
```java
@GetMapping("/")
public String homeLoginV3(HttpServletRequest request, Model model) {

    // λ‘κ·ΈμΈ νλ©΄μμ μΈμμ΄ μμ±λμ§ μλλ‘ false μ²λ¦¬
    HttpSession session = request.getSession(false);
    if (session == null) {
        return "home";
    }

    // μμλ₯Ό μ΄μ©ν΄ μΈμμμ λ‘κ·ΈμΈ κ°μ²΄λ₯Ό κΊΌλΈλ€
    Member loginMember = (Member) session.getAttribute(SessionConstant.LOGIN_MEMBER);

    // μΈμμ νμ λ°μ΄ν°κ° μμΌλ©΄ homeμΌλ‘ μ΄λ
    if (loginMember == null) {
        return "home";
    }

    // μΈμμ΄ μ μ§λλ©΄ λ‘κ·ΈμΈμΌλ‘ μ΄λ
    model.addAttribute("member", loginMember);
    return "loginHome";

}
```



### 1-3. μΈμμ΄ μ κ³΅νλ μ λ³΄ μ‘°ννκΈ°

μλμ κ°μ΄ μΈμμ΄ μ κ³΅νλ μ λ³΄λ₯Ό νμΈν  μ μλ€. log μμ λ₯Ό μ°Έκ³ ν΄λ³΄μ.

> - `sessionId`: μΈμId, JSESSIONID μ κ°μ΄λ€. 
μ) 34B14F008AA3527C9F8ED620EFD7A4E1 
> - `maxInactiveInterval`: μΈμμ μ ν¨ μκ°
μ) 1800μ΄, (30λΆ) 
> - `creationTime`: μΈμ μμ±μΌμ 
> - `lastAccessedTime`: μΈμκ³Ό μ°κ²°λ μ¬μ©μκ° μ΅κ·Όμ μλ²μ μ κ·Όν μκ°μ΄λ€. ν΄λΌμ΄μΈνΈμμ μλ²λ‘ `sessionId(JSESSIONID)`λ₯Ό μμ²­ν κ²½μ°μ κ°±μ λλ€. 
> - `isNew`: μλ‘ μμ±λ μΈμμΈμ§, ν΄λΌμ΄μΈνΈμμ μλ²λ‘ `sessionId(JSESSIONID)`λ₯Ό μμ²­ν΄μ μ‘°νλ μΈμμΈμ§ μ¬λΆλ₯Ό νμΈνλ€. 


**SessionInfoController - log μμ **

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
        HttpSession session = request.getSession(false);// sessionμ λ°κ³ , μμΌλ©΄ λ§λ€μ§ μλλ€

        // μΈμμ΄ μμ κ²½μ°
        if (session == null) {
            return "μΈμμ΄ μμ΅λλ€.";
        }

        // asIterator: μΈμμ΄ μμ κ²½μ°, μΈμμ name μ λ³΄λ₯Ό μ‘°ννλ μμμ μ μ²΄ λ°λ³΅νλ€.
        // forEachRemaining: λͺ¨λ  μμκ° μ²λ¦¬λκ±°λ μμμ΄ μμΈλ₯Ό λ°μμν¬ λκΉμ§ λ¨μ μλ κ° μμμ λν΄ μ§μ λ μμμ μν
        session.getAttributeNames().asIterator()
                .forEachRemaining(name -> log.info("session name={}, value={}", name, session.getAttribute(name)));

        log.info("sessionId={}", session.getId());
        log.info("getMaxInactiveInternal={}", session.getMaxInactiveInterval()); // νμ±ν κΈ°κ°
        log.info("creationTime={}", new Date(session.getCreationTime()));
        log.info("lastAccessedTime={}", new Date(session.getLastAccessedTime())); // μ¬μ©μκ° λ§μ§λ§ μ κ·Όν μκ°
        log.info("isNew={}", session.isNew()); // μλ‘ μμ±λ μΈμ μ¬λΆ

        return "μΈμ μΆλ ₯";
    }
}

```

### 1-3. μΈμμ νμμμκ³Ό μ’λ£ μμ  μ€μ 

μΈμμ μ¬μ©μκ° λ‘κ·Έμμμ μ§μ  νΈμΆν΄μ `session.invalidate()`κ° νΈμΆ λλ κ²½μ°μ μ­μ λλ€. νμ§λ§, λλΆλΆμ μ¬μ©μλ λ‘κ·Έμμμ μ§μ  νμ§ μλλ€. κ·Έλ₯ μΉ λΈλΌμ°μ λ₯Ό μ’λ£νλ κ²½μ°κ° λλΆλΆμ΄λ€. λ¬Έμ λ HTTPκ° λΉ μ°κ²°μ±(ConnectionLess)μ΄λ―λ‘, μλ² μμ₯μμλ ν΄λΉ μ¬μ©μκ° μΉ λΈλΌμ°μ λ₯Ό μ’λ£ν κ²μΈμ§ μΈμν  μ μλ€. λ°λΌμ, μλ²μμλ μΈμ λ°μ΄ν°λ₯Ό μΈμ  μ­μ ν΄μΌ νλμ§ νλ¨νκΈ° μ΄λ ΅λ€. 

μ΄μ κ°μ λ¬Έμ λ‘ μΈν΄ μΈμμ λ¬΄νμ  λ³΄κ΄νλ©΄ μλμ κ°μ λ¬Έμ κ° λ°μνλ€.

> - μΈμκ³Ό κ΄λ ¨λ μΏ ν€(JSESSIONID)λ₯Ό νμ·¨ λΉνμ κ²½μ°, μ€λ μκ°μ΄ μ§λ νμ ν΄λΉ μΏ ν€λ‘ λ°μνλ μμμ μΈ μμ²­μ΄ μ ν¨νλ€. 
> - μΈμμ κΈ°λ³Έμ μΌλ‘ λ©λͺ¨λ¦¬μ μμ±λλ€. λ©λͺ¨λ¦¬μ ν¬κΈ°κ° λ¬΄ννμ§ μκΈ° λλ¬Έμ κΌ­ νμν κ²½μ°λ§ μμ±ν΄μ μ¬μ©ν΄μΌ νλ€. 10λ§ λͺμ μ¬μ©μκ° λ‘κ·ΈμΈνλ©΄ 10λ§ κ°μ μΈμμ΄ μμ±λλ€.


κ·Έλ λ€λ©΄, μΈμμ μ ν¨ κΈ°κ°μ μΌλ§λ μ€μ νλ κ²μ΄ μ’μκΉ? μΌλ°μ μΌλ‘ μμ± μμ μΌλ‘λΆν° 30λΆ μ λλ₯Ό μ ν¨ κΈ°κ°μΌλ‘ λΆμ¬νλ€. νμ§λ§, λ¨μν μμ± ν 30λΆμ μ ν¨ κΈ°κ°μΌλ‘ λΆμ¬νλ©΄ 30λΆλ§λ€ μ¬λ‘κ·ΈμΈμ ν΄μΌ νλ€. μ΄μ λν λμμΌλ‘ μ¬μ©μκ° μλ²μ λ§μ§λ§ μμ²­μ λ³΄λΈ μκ°μ κΈ°μ€μΌλ‘ 30λΆ μ λμ μ ν¨ κΈ°κ°μ λΆμ¬νλ€. HttpSessionμ΄ λ°λ‘ μ΄ λ°©μμ μ¬μ©νλ€.

μ¬μ©μκ° μλΉμ€λ₯Ό κ³μ μ΄μ©νκ³  μλ€λ©΄, μΈμμ μ ν¨ κΈ°κ°μ΄ κ³μ 30λΆμΌλ‘ κ°±μ λκΈ° λλ¬Έμ λΆνμν μ¬λ‘κ·ΈμΈμ λ²κ±°λ‘μμ ν΄κ²°ν  μ μλ€.

μΈμμ νμμμμ μλμ κ°μ΄ μ€νλ§ λΆνΈμ κΈλ‘λ² μ€μ μ μ΄μ©ν  μ μλ€. 

**application.properties**

```java
server.servlet.session.timeout=60
// 60μ΄, κΈ°λ³Έμ 1800(30λΆ) 
// (κΈλ‘λ² μ€μ μ λΆ λ¨μλ‘ μ€μ ν΄μΌ νλ€. 60(1λΆ), 120(2λΆ), ...) 
```


νΉμ  μΈμ λ¨μλ‘ μκ°μ μ€μ ν  κ²½μ° μλμ κ°μ΄ μ€μ νλ€.

```java
session.setMaxInactiveInterval(1800);
//1800μ΄   
```

μΈμμ νμμμ μκ°μ ν΄λΉ μΈμκ³Ό κ΄λ ¨λ `JSESSIONID`λ₯Ό μ λ¬νλ `HTTP μμ²­`μ΄ μμ κ²½μ°, νμ¬ μκ°μΌλ‘ λ€μ μ΄κΈ°ν λλ€. μ΄κΈ°νκ° λλ©΄ μΈμ νμμμμΌλ‘ μ€μ ν μκ° λμ μΈμμ μΆκ°λ‘ μ¬μ©ν  μ μλ€.

μλΈλ¦Ώμ `HttpSession`μ΄ μ κ³΅νλ νμμμ κΈ°λ₯μ μΈμμ μμ νκ³  νΈλ¦¬νκ² μ¬μ©ν  μ μλλ‘ λμμ€λ€. λ€λ§, μΈμμ λΆνμν λ°μ΄ν°κ° λ€μ΄κ°μ§ μλλ‘ μ£Όμν΄μΌ νλ€. λ©λͺ¨λ¦¬ λ¬Έμ κ° λ°μν  μ μμΌλ―λ‘, μΈμ λ μ΅μνμ λ°μ΄ν°λ§ λ³΄κ΄ν΄μΌ νλ€. 

`(λ³΄κ΄ν λ°μ΄ν° μ©λ * μ¬μ©μ μ)`λ‘ μΈμμ λ©λͺ¨λ¦¬ μ¬μ©λμ΄ κΈκ²©νκ² λμ΄λλ©΄, μ₯μ μ μμΈμ΄ λκΈ°λ νλ€. λν μΈμμ μκ°μ λΆνμνκ² κΈΈκ² μ€μ νμ§ μλλ‘ νλ€. μ ν¨ κΈ°κ°μ΄ λμ΄λλ©΄ λ©λͺ¨λ¦¬ μ¬μ©λ λμ λλ€. μ½ 30λΆμ μ νλ‘ μν©μ λ§κ² μ λΉν μκ°μ μ€μ νμ.


---

## 2. μ€νλ§μ΄ μ κ³΅νλ @SessionAttributeμ μ΄μ©νμ¬ μΈμ κ΅¬ν

μ€νλ§μ μΈμμ λ νΈλ¦¬νκ² μ¬μ©ν  μ μλλ‘ `@SessionAttribute`μ μ§μνλ€. μ΄λ―Έ λ‘κ·ΈμΈ λ μ¬μ©μλ₯Ό μ°Ύμ λλ μλμ κ°μ΄ μ¬μ©νλ€. μ΄ κΈ°λ₯μ μΈμμ μμ±νμ§ μλλ€. 

```java
@SessionAttribute(name = "loginMember", required = false) Member loginMember
```

μλ μμ  μ½λλ₯Ό μ΄ν΄λ³΄μ. μΈμμ μ°Ύκ³ , μΈμμ λ€μ΄μλ λ°μ΄ν°λ₯Ό μ°Ύλ λ²κ±°λ‘μ΄ κ³Όμ μ μ€νλ§μ΄ νλ²μ μ²λ¦¬ν΄μ€λ€.

**HomeController - homeLoginV3Spring()** 

```java
@GetMapping("/")
public String homeLoginV3Spring(
        // @SesseionAttributeλ μμ±μ ν΄λΉνλ μΈμμ κ°μ§κ³  μλ Member κ°μ²΄λ₯Ό κΊΌλΈλ€
        @SessionAttribute(name = SessionConstant.LOGIN_MEMBER, required = false) Member loginMember, Model model) {

    // μΈμμ νμ λ°μ΄ν°κ° μμΌλ©΄ homeμΌλ‘ μ΄λ
    if (loginMember == null) {
        return "home";
    }

    // μΈμμ΄ μ μ§λλ©΄ λ‘κ·ΈμΈμΌλ‘ μ΄λ
    model.addAttribute("member", loginMember);
    return "loginHome";
}
```

 
### 2-1. TrackingModes 


λ‘κ·ΈμΈμ μ²μ μλνλ©΄ μλμ κ°μ΄ URLμ jsessionidμ΄ ν¬ν¨λμ΄ μλ€.
`http://localhost:8080/;jsessionid=F59911518B921DF62D09F0DF8F83F872`

μ΄κ²μ μΉ λΈλΌμ°μ κ° μΏ ν€λ₯Ό μ§μνμ§ μμ λ μΏ ν€ λμ  URLμ ν΅ν΄μ μΈμμ μ μ§νλ λ°©λ²μ΄λ€. μλ² μμ₯μμ μΉ λΈλΌμ°μ κ° μΏ ν€λ₯Ό μ§μνλμ§ νμ§ μλμ§ μ΅μ΄μλ νλ¨νμ§ λͺ»νλ―λ‘, μΏ ν€ κ°λ μ λ¬νκ³ , URLμ jsessionid λ ν¨κ» μ λ¬νλ€. μ΄ λ°©λ²μ μ¬μ©νλ €λ©΄ URLμ κ°μ κ³μ ν¬ν¨ν΄μΌ νλ€. μ°Έκ³ λ‘ νμλ¦¬ν κ°μ ννλ¦Ώμ μμ§μ ν΅ν΄μ URLμ `jsessionid`λ₯Ό μλμΌλ‘ ν¬ν¨νλ κΈ°λ₯μ μ κ³΅νλ€. 
 
λ§μ½ URL μ λ¬ λ°©μμ μ¬μ©νμ§ μκ³ , ν­μ μΏ ν€λ₯Ό μ΄μ©ν΄ μΈμμ μ μ§νκ³  μΆλ€λ©΄ μλμ κ°μ΄ μ€μ ν΄μΌ νλ€. μ΄λ κ² νλ©΄ URLμ `jsessionid`κ° λΈμΆλμ§ μλλ€. 

**application.properties**
```java
server.servlet.session.tracking-modes=cookie
```


