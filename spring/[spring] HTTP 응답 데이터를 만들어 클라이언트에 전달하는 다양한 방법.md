> ### π βοΈ 
> **μ΄ κΈμ μ€νλ§μ΄ μ κ³΅νλ HTTP μλ΅ λ°μ΄ν°μ μ‘°ν λ°©λ²κ³Ό νμ© λ°©λ²μ νμ΅νκ³  μ λ¦¬ν ν¬μ€νμ΄λ€.**

---
# HTTP μλ΅ λ°μ΄ν°λ₯Ό λ§λ€μ΄ ν΄λΌμ΄μΈνΈμ μ λ¬νλ λ€μν λ°©λ²


μ€νλ§(μλ²)μμ μλ΅ λ°μ΄ν°λ₯Ό λ§λλ λ°©λ²μ ν¬κ² 3κ°μ§μ΄λ€. νλμ© μ΄ν΄λ³΄μ.

1. **μ μ  λ¦¬μμ€ **
- μ) μΉ λΈλΌμ°μ μ μ μ μΈ HTML, css, jsλ₯Ό μ κ³΅ν  λλ μ μ  λ¦¬μμ€λ₯Ό μ¬μ©νλ€. 

2. **λ·° ννλ¦Ώ μ¬μ© **
- μ) μΉ λΈλΌμ°μ μ λμ μΈ HTMLμ μ κ³΅ν  λλ λ·° ννλ¦Ώμ μ¬μ©νλ€. 
  
3. **HTTP λ©μμ§ μ¬μ© **
- HTTP APIλ₯Ό μ κ³΅νλ κ²½μ°μλ HTMLμ΄ μλλΌ λ°μ΄ν°λ₯Ό μ λ¬νλ€. HTTP λ©μμ§ λ°λμ JSON κ°μ νμμΌλ‘ λ°μ΄ν°λ₯Ό μ λ¬νλ€.


---

## 1. μ μ  λ¦¬μμ€ μλ΅ λ°μ΄ν° λ³΄λ΄κΈ° 

μ μ  λ¦¬μμ€λ ν΄λΉ νμΌμ λ³κ²½ μμ΄ κ·Έλλ‘ μλΉμ€νλ κ²μ μλ―Ένλ€. μ€νλ§ λΆνΈλ ν΄λμ€ν¨μ€μ λ€μ λλ ν λ¦¬μ μλ μ μ  λ¦¬μμ€λ₯Ό μ κ³΅νλ€. 
> - `/static`
> - `/public`
> - `/resources`
> - `/META-INF/resources` 

`src/main/resources`λ λ¦¬μμ€λ₯Ό λ³΄κ΄νλ κ³³μ΄μ, ν΄λμ€ ν¨μ€μ μμ κ²½λ‘λ€. λ°λΌμ, ν΄λΉ λλ ν λ¦¬μ λ¦¬μμ€λ₯Ό λ£μ΄λλ©΄ μ€νλ§ λΆνΈκ° μ μ  λ¦¬μμ€λ‘ μλΉμ€λ₯Ό μ κ³΅νλ€. 

**μ μ  λ¦¬μμ€ κ²½λ‘**
`src/main/resources/static`

λ§μ½ μλμ κ°μ κ²½λ‘μ νμΌμ΄ λ€μ΄ μλ€λ©΄, μΉ λΈλΌμ°μ μμλ `static` νμ κ²½λ‘λ§ URLμ ν¬ν¨νλ©΄ λλ€.

> **λ¦¬μμ€ κ²½λ‘**
> `src/main/resources/static/basic/hello-form.html`
> **μΉ λΈλΌμ°μ  μ μ κ²½λ‘**
> `http://localhost:8080/basic/hello-form.html`


## 2. λ·° ννλ¦Ώμ μ΄μ©ν΄μ μλ΅ λ°μ΄ν° λ³΄λ΄κΈ°

λ·° ννλ¦Ώμ κ±°μ³μ HTMLμ΄ μμ±λκ³ , λ·°κ° μλ΅μ λ§λ€μ΄ μ λ¬νλ λ°©μμ΄λ€. μΌλ°μ μΌλ‘ HTMLμ λμ μΌλ‘ μμ±νλ μ©λλ‘ μ¬μ©νμ§λ§, λ€λ₯Έ κ²λ€λ κ°λ₯νλ€. λ·° ννλ¦Ώμ΄ λ§λ€ μ μλ κ²μ΄λΌλ©΄ λ­λ μ§ κ°λ₯νλ€. 

μ€νλ§ λΆνΈλ κΈ°λ³Έ λ·° ννλ¦Ώ κ²½λ‘λ₯Ό μ κ³΅νλ€. 
**λ·° ννλ¦Ώ κ²½λ‘** 
`src/main/resources/templates`

**λ·° ννλ¦Ώ μμ±** 
`src/main/resources/templates/response/hello.html`

```HTML
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<p th:text="${data}">empty</p>
</body>
</html>

```

**ResponseViewController - λ·° ννλ¦Ώμ νΈμΆνλ μ»¨νΈλ‘€λ¬**

```java
package hello.springmvc.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    // HTML νμ€νΈ λ°ν
    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
        ModelAndView mav = new ModelAndView("/response/hello").addObject("data", "hello!");

        return mav;
    }

    // ν΄λμ€μ @Controllerκ° λΆμ΄μμΌλ©΄ λ©μλ return λ¬Έμμ΄μ΄ Viewμ λΌλ¦¬ μ΄λ¦μ΄ λλ€. λ·° λ¦¬μ‘Έλ²κ° λ¬Όλ¦¬ κ²½λ‘λ‘ λ°κΏμ€λ€.
    // μ£Όμ! λ§μ½ λ©μλμ @ResponseBodyλ₯Ό μ¬μ©νλ©΄ View κ²½λ‘κ° λ¨μ μλ΅ νμ€νΈκ° λλ€.
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
        model.addAttribute("data", "hello!");
        return "/response/hello";
    }

    // μ»¨νΈλ‘€λ¬μ @RequestMapping κ²½λ‘μ Viewμ λΌλ¦¬μ  μ΄λ¦μ΄ κ°μΌλ©΄ λ°νμ voidλ‘ μ§μ ν΄λ Viewκ° λλκ° λλ€.
    @RequestMapping("/response/hello")
    public void responseViewV3(Model model) {
        model.addAttribute("data", "hello!");
    }
}

```

### 2-1. Stringμ λ°ννλ κ²½μ° - View or HTTP λ©μμ§ 

`@ResponseBody`κ° μμΌλ©΄ `response/hello`λ‘ λ·° λ¦¬μ‘Έλ²κ° μ€νλμ΄μ λ·°λ₯Ό μ°Ύκ³ , λ λλ§ νλ€. `@ResponseBody`κ° μμΌλ©΄ λ·° λ¦¬μ‘Έλ²λ₯Ό μ€ννμ§ μκ³ , HTTP λ©μμ§ λ°λμ μ§μ  `response/hello` λΌλ λ¬Έμκ° μλ ₯λλ€. 

μ¬κΈ°μλ λ·°μ λΌλ¦¬ μ΄λ¦μΈ `response/hello`λ₯Ό λ°ννλ©΄ λ€μ κ²½λ‘μ λ·° ννλ¦Ώμ΄ λ λλ§ λλ κ²μ νμΈν  μ μλ€. 
> **λ·° ννλ¦Ώ κ²½λ‘**: `templates/response/hello.html` 

Voidλ₯Ό λ°ννλ κ²½μ°, `@Controller λ₯Ό μ¬μ©νκ³ , `HttpServletResponse`, `OutputStream(Writer)`κ°μ HTTP λ©μμ§ λ°λλ₯Ό μ²λ¦¬νλ νλΌλ―Έν°κ° μμΌλ©΄ μμ²­ URLμ μ°Έκ³ ν΄μ λΌλ¦¬ λ·° μ΄λ¦μΌλ‘ μ¬μ©νλ€. 
- μμ²­ URL: `/response/hello`  
- μ€ν: `templates/response/hello.html`
	  
μ°Έκ³ λ‘ μ΄ λ°©μμ λͺμμ±μ΄ λλ¬΄ λ¨μ΄μ§κ³  μ¬μ©μ μ ν©ν κ²½μ°λ λ§μ§ μμ κΆμ₯νμ§ μλλ€.

```java
package hello.springmvc.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    // HTML νμ€νΈ λ°ν
    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
        ModelAndView mav = new ModelAndView("/response/hello").addObject("data", "hello!");

        return mav;
    }
}
```

**HTTP λ©μμ§** 
`@ResponseBody`, `HttpEntity`λ₯Ό μ¬μ©νλ©΄, λ·° ννλ¦Ώ μ¬μ©νμ§ μλλ€. HTTP λ©μμ§ λ°λμ μ§μ  μλ΅ λ°μ΄ν°λ₯Ό μΆλ ₯νλ€.

``` java
 
    // ν΄λμ€μ @Controllerκ° λΆμ΄μμΌλ©΄ λ©μλ return λ¬Έμμ΄μ΄ Viewμ λΌλ¦¬ μ΄λ¦μ΄ λλ€. λ·° λ¦¬μ‘Έλ²κ° λ¬Όλ¦¬ κ²½λ‘λ‘ λ°κΏμ€λ€.
    // μ£Όμ! λ§μ½ λ©μλμ @ResponseBodyλ₯Ό μ¬μ©νλ©΄ View κ²½λ‘κ° λ¨μ μλ΅ νμ€νΈκ° λλ€.
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
        model.addAttribute("data", "hello!");
        return "/response/hello";
    }

    // μ»¨νΈλ‘€λ¬μ @RequestMapping κ²½λ‘μ Viewμ λΌλ¦¬μ  μ΄λ¦μ΄ κ°μΌλ©΄ λ°νμ voidλ‘ μ§μ ν΄λ Viewκ° λλκ° λλ€.
    @RequestMapping("/response/hello")
    public void responseViewV3(Model model) {
        model.addAttribute("data", "hello!");
    }
}
```




### 2-2. Thymeleaf μ€νλ§ λΆνΈ μ€μ  

**λΌμ΄λΈλ¬λ¦¬ μΆκ° λ°©λ²**
> **build.gradle**
> ```java
`implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
```

μ€νλ§ λΆνΈκ° μλμΌλ‘ `ThymeleafViewResolver`μ νμν μ€νλ§ λΉλ€μ λ±λ‘νλ€. κ·Έλ¦¬κ³  λ€μ μ€μ λ μ¬μ©νλ€. μ΄ μ€μ μ κΈ°λ³Έ κ°μ΄λ―λ‘, λ³κ²½μ΄ νμν  λλ§ μ€μ νμ¬ μ¬μ©νλ©΄ λλ€.

> **application.properties**
```java
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

> **μ°Έκ³ ** 
> μ€νλ§ λΆνΈμ νμλ¦¬ν κ΄λ ¨ μΆκ° μ€μ μ λ€μ κ³΅μ μ¬μ΄νΈλ₯Ό μ°Έκ³ νμ. (νμ΄μ§ μμμ thymeleaf κ²μ) 
> https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html#common-application-properties-templating



---

## 3. HTTP API, λ©μμ§ λ°λμ μ§μ  λ°μ΄ν°λ₯Ό μλ ₯νμ¬ μλ΅ λ³΄λ΄κΈ°
  

HTTP APIλ₯Ό μ κ³΅νλ κ²½μ°μλ HTMLμ΄ μλλΌ λ°μ΄ν°λ₯Ό μ λ¬ν΄μΌ νλ€. λ³΄ν΅μ HTTP λ©μμ§ λ°λμ JSON νμμΌλ‘ λ°μ΄ν°λ₯Ό μ€μ΄ λ³΄λΈλ€.  

> μ°Έκ³ 
> HTMLμ΄λ λ·° ννλ¦Ώμ μ¬μ©ν΄λ HTTP μλ΅ λ©μμ§ λ°λμ HTML λ°μ΄ν°κ° λ΄κ²¨μ μ λ¬λλ€. 
μ¬κΈ°μ μ€λͺνλ λ΄μ©μ μ μ  λ¦¬μμ€λ λ·° ννλ¦Ώμ κ±°μΉμ§ μκ³ , μ§μ  HTTP μλ΅ λ©μμ§λ₯Ό μ λ¬νλ κ²½μ°λ₯Ό λ§νλ€. 


**ResponseBodyController** 


```java
package hello.springmvc.basic.response;

import hello.springmvc.basic.HelloData;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.ResponseStatus;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Slf4j
@Controller
public class ResponseBodyController {

    @GetMapping("/response-body-string-v1")
    public void responseBodyV1(HttpServletResponse response) throws IOException {
        response.getWriter().write("OK!");
    }

    @GetMapping("/response-body-string-v2")
    public ResponseEntity<String> responseBodyV2() {
        return new ResponseEntity<>("ok!", HttpStatus.OK);
    }

    @ResponseBody
    @GetMapping("/response-body-string-v3")
    public String responseBodyV3() {
        return "OK! ";
    }


    @GetMapping("/response-body-json-v1")
    public ResponseEntity<HelloData> ressponseBodyJsonv1() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);
        return new ResponseEntity<>(helloData, HttpStatus.OK);
    }

    @ResponseStatus(HttpStatus.OK)
    @ResponseBody
    @GetMapping("/response-body-json-v2")
    public HelloData ressponseBodyJsonv2() {
        HelloData helloData = new HelloData();
        helloData.setUsername("userA");
        helloData.setAge(20);
        return helloData;
    }
}

```



**responseBodyV1** 

μλΈλ¦Ώμ μ§μ  λ€λ£° λ μ²λΌ HttpServletResponse κ°μ²΄λ₯Ό ν΅ν΄μ HTTP λ©μμ§ λ°λμ μ§μ  ok μλ΅ λ©μμ§λ₯Ό μ λ¬νλ€. 
```java
response.getWriter().write("ok")
```

**responseBodyV2 **

`ResponseEntity`μν°ν°λ `HttpEntity`λ₯Ό μμ λ°λλ€. `HttpEntity`λ HTTP λ©μμ§μ ν€λ, λ°λ μ λ³΄λ₯Ό κ°μ§κ³  μλ€. `ResponseEntity`λ μ¬κΈ°μ λν΄μ HTTP μλ΅ μ½λλ₯Ό μΆκ°λ‘ μ€μ ν  μ μλ€. μλ₯Ό λ€μ΄, `HttpStatus.CREATED`λ₯Ό μ€μ νλ©΄ 201 μλ΅μ΄ λκ°λ€.


**responseBodyV3** 

`@ResponseBody`λ₯Ό μ¬μ©νλ©΄ viewλ₯Ό μ¬μ©νμ§ μκ³ , HTTP λ©μμ§ μ»¨λ²ν°λ₯Ό ν΅ν΄μ HTTP λ©μμ§λ₯Ό μ§μ  μλ ₯ν  μ μλ€. `ResponseEntity`λ λμΌν λ°©μμΌλ‘ λμνλ€. 


**responseBodyJsonV1** 
`ResponseEntity`λ₯Ό λ°ννλ€. HTTP λ©μμ§ μ»¨λ²ν°λ₯Ό ν΅ν΄μ JSON νμμΌλ‘ λ³νλμ΄μ λ°νλλ€. 


**responseBodyJsonV2** 

`ResponseEntity`λ HTTP μλ΅ μ½λλ₯Ό μ€μ ν  μ μλλ°, `@ResponseBody`λ₯Ό μ¬μ©νλ©΄ μ΄λ° κ²μ μ€μ νκΈ° κΉλ€λ‘­λ€. `@ResponseStatus(HttpStatus.OK)` μ λΈνμ΄μμ μ¬μ©νλ©΄ μλ΅ μ½λλ μ€μ ν  μ μλ€. λ¬Όλ‘  μ λΈνμ΄μμ΄κΈ° λλ¬Έμ μλ΅ μ½λλ₯Ό λμ μΌλ‘ λ³κ²½ν  μλ μλ€. νλ‘κ·Έλ¨ μ‘°κ±΄μ λ°λΌμ λμ μΌλ‘ λ³κ²½νλ €λ©΄ `ResponseEntity`λ₯Ό μ¬μ©νλ©΄ λλ€. 


**@RestController** 
`@Controller` λμ μ `@RestController` μ λΈνμ΄μμ μ¬μ©νλ©΄, ν΄λΉ μ»¨νΈλ‘€λ¬μ λͺ¨λ `@ResponseBody`κ° μ μ©λλ ν¨κ³Όκ° μλ€. λ°λΌμ, λ·° ννλ¦Ώμ μ¬μ©νλ κ²μ΄ μλλΌ, HTTP λ©μμ§ λ°λμ μ§μ  λ°μ΄ν°λ₯Ό μλ ₯νλ€. μ΄λ¦ κ·Έλλ‘ `Rest API(HTTP API)`λ₯Ό λ§λ€ λ μ¬μ©νλ μ»¨νΈλ‘€λ¬μ΄λ€. 

μ°Έκ³ λ‘ `@ResponseBody`λ ν΄λμ€ λ λ²¨μ λλ©΄ μ μ²΄μ λ©μλμ μ μ©λλ€. `@RestController` μλΈνμ΄μ μμ `@ResponseBody`κ° μ μ©λμ΄ μλ€.

