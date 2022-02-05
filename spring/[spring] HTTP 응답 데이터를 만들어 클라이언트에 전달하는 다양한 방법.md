> ### 📖 ✏️ 
> **이 글은 스프링이 제공하는 HTTP 응답 데이터의 조회 방법과 활용 방법을 학습하고 정리한 포스팅이다.**

---
# HTTP 응답 데이터를 만들어 클라이언트에 전달하는 다양한 방법


스프링(서버)에서 응답 데이터를 만드는 방법은 크게 3가지이다. 하나씩 살펴보자.

1. **정적 리소스 **
- 예) 웹 브라우저에 정적인 HTML, css, js를 제공할 때는 정적 리소스를 사용한다. 

2. **뷰 템플릿 사용 **
- 예) 웹 브라우저에 동적인 HTML을 제공할 때는 뷰 템플릿을 사용한다. 
  
3. **HTTP 메시지 사용 **
- HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달한다. HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 전달한다.


---

## 1. 정적 리소스 응답 데이터 보내기 

정적 리소스는 해당 파일을 변경 없이 그대로 서비스하는 것을 의미한다. 스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다. 
> - `/static`
> - `/public`
> - `/resources`
> - `/META-INF/resources` 

`src/main/resources`는 리소스를 보관하는 곳이자, 클래스 패스의 시작 경로다. 따라서, 해당 디렉토리에 리소스를 넣어두면 스프링 부트가 정적 리소스로 서비스를 제공한다. 

**정적 리소스 경로**
`src/main/resources/static`

만약 아래와 같은 경로에 파일이 들어 있다면, 웹 브라우저에서는 `static` 하위 경로만 URL에 포함하면 된다.

> **리소스 경로**
> `src/main/resources/static/basic/hello-form.html`
> **웹 브라우저 접속 경로**
> `http://localhost:8080/basic/hello-form.html`


## 2. 뷰 템플릿을 이용해서 응답 데이터 보내기

뷰 템플릿을 거쳐서 HTML이 생성되고, 뷰가 응답을 만들어 전달하는 방식이다. 일반적으로 HTML을 동적으로 생성하는 용도로 사용하지만, 다른 것들도 가능하다. 뷰 템플릿이 만들 수 있는 것이라면 뭐든지 가능하다. 

스프링 부트는 기본 뷰 템플릿 경로를 제공한다. 
**뷰 템플릿 경로** 
`src/main/resources/templates`

**뷰 템플릿 생성** 
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

**ResponseViewController - 뷰 템플릿을 호출하는 컨트롤러**

```java
package hello.springmvc.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    // HTML 텍스트 반환
    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
        ModelAndView mav = new ModelAndView("/response/hello").addObject("data", "hello!");

        return mav;
    }

    // 클래스에 @Controller가 붙어있으면 메서드 return 문자열이 View의 논리 이름이 된다. 뷰 리졸버가 물리 경로로 바꿔준다.
    // 주의! 만약 메서드에 @ResponseBody를 사용하면 View 경로가 단순 응답 텍스트가 된다.
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
        model.addAttribute("data", "hello!");
        return "/response/hello";
    }

    // 컨트롤러의 @RequestMapping 경로와 View의 논리적 이름이 같으면 반환을 void로 지정해도 View가 랜더가 된다.
    @RequestMapping("/response/hello")
    public void responseViewV3(Model model) {
        model.addAttribute("data", "hello!");
    }
}

```

### 2-1. String을 반환하는 경우 - View or HTTP 메시지 

`@ResponseBody`가 없으면 `response/hello`로 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다. `@ResponseBody`가 있으면 뷰 리졸버를 실행하지 않고, HTTP 메시지 바디에 직접 `response/hello` 라는 문자가 입력된다. 

여기서는 뷰의 논리 이름인 `response/hello`를 반환하면 다음 경로의 뷰 템플릿이 렌더링 되는 것을 확인할 수 있다. 
> **뷰 템플릿 경로**: `templates/response/hello.html` 

Void를 반환하는 경우, `@Controller 를 사용하고, `HttpServletResponse`, `OutputStream(Writer)`같은 HTTP 메시지 바디를 처리하는 파라미터가 없으면 요청 URL을 참고해서 논리 뷰 이름으로 사용한다. 
- 요청 URL: `/response/hello`  
- 실행: `templates/response/hello.html`
	  
참고로 이 방식은 명시성이 너무 떨어지고 사용에 적합한 경우도 많지 않아 권장하지 않는다.

```java
package hello.springmvc.basic.response;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class ResponseViewController {

    // HTML 텍스트 반환
    @RequestMapping("/response-view-v1")
    public ModelAndView responseViewV1() {
        ModelAndView mav = new ModelAndView("/response/hello").addObject("data", "hello!");

        return mav;
    }
}
```

**HTTP 메시지** 
`@ResponseBody`, `HttpEntity`를 사용하면, 뷰 템플릿 사용하지 않는다. HTTP 메시지 바디에 직접 응답 데이터를 출력한다.

``` java
 
    // 클래스에 @Controller가 붙어있으면 메서드 return 문자열이 View의 논리 이름이 된다. 뷰 리졸버가 물리 경로로 바꿔준다.
    // 주의! 만약 메서드에 @ResponseBody를 사용하면 View 경로가 단순 응답 텍스트가 된다.
    @RequestMapping("/response-view-v2")
    public String responseViewV2(Model model) {
        model.addAttribute("data", "hello!");
        return "/response/hello";
    }

    // 컨트롤러의 @RequestMapping 경로와 View의 논리적 이름이 같으면 반환을 void로 지정해도 View가 랜더가 된다.
    @RequestMapping("/response/hello")
    public void responseViewV3(Model model) {
        model.addAttribute("data", "hello!");
    }
}
```




### 2-2. Thymeleaf 스프링 부트 설정 

**라이브러리 추가 방법**
> **build.gradle**
> ```java
`implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`
```

스프링 부트가 자동으로 `ThymeleafViewResolver`와 필요한 스프링 빈들을 등록한다. 그리고 다음 설정도 사용한다. 이 설정은 기본 값이므로, 변경이 필요할 때만 설정하여 사용하면 된다.

> **application.properties**
```java
spring.thymeleaf.prefix=classpath:/templates/
spring.thymeleaf.suffix=.html
```

> **참고** 
> 스프링 부트의 타임리프 관련 추가 설정은 다음 공식 사이트를 참고하자. (페이지 안에서 thymeleaf 검색) 
> https://docs.spring.io/spring-boot/docs/2.4.3/reference/html/appendix-application-properties.html#common-application-properties-templating



---

## 3. HTTP API, 메시지 바디에 직접 데이터를 입력하여 응답 보내기
  

HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 한다. 보통은 HTTP 메시지 바디에 JSON 형식으로 데이터를 실어 보낸다.  

> 참고
> HTML이나 뷰 템플릿을 사용해도 HTTP 응답 메시지 바디에 HTML 데이터가 담겨서 전달된다. 
여기서 설명하는 내용은 정적 리소스나 뷰 템플릿을 거치지 않고, 직접 HTTP 응답 메시지를 전달하는 경우를 말한다. 


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

서블릿을 직접 다룰 때 처럼 HttpServletResponse 객체를 통해서 HTTP 메시지 바디에 직접 ok 응답 메시지를 전달한다. 
```java
response.getWriter().write("ok")
```

**responseBodyV2 **

`ResponseEntity`엔티티는 `HttpEntity`를 상속 받는다. `HttpEntity`는 HTTP 메시지의 헤더, 바디 정보를 가지고 있다. `ResponseEntity`는 여기에 더해서 HTTP 응답 코드를 추가로 설정할 수 있다. 예를 들어, `HttpStatus.CREATED`를 설정하면 201 응답이 나간다.


**responseBodyV3** 

`@ResponseBody`를 사용하면 view를 사용하지 않고, HTTP 메시지 컨버터를 통해서 HTTP 메시지를 직접 입력할 수 있다. `ResponseEntity`도 동일한 방식으로 동작한다. 


**responseBodyJsonV1** 
`ResponseEntity`를 반환한다. HTTP 메시지 컨버터를 통해서 JSON 형식으로 변환되어서 반환된다. 


**responseBodyJsonV2** 

`ResponseEntity`는 HTTP 응답 코드를 설정할 수 있는데, `@ResponseBody`를 사용하면 이런 것을 설정하기 까다롭다. `@ResponseStatus(HttpStatus.OK)` 애노테이션을 사용하면 응답 코드도 설정할 수 있다. 물론 애노테이션이기 때문에 응답 코드를 동적으로 변경할 수는 없다. 프로그램 조건에 따라서 동적으로 변경하려면 `ResponseEntity`를 사용하면 된다. 


**@RestController** 
`@Controller` 대신에 `@RestController` 애노테이션을 사용하면, 해당 컨트롤러에 모두 `@ResponseBody`가 적용되는 효과가 있다. 따라서, 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 데이터를 입력한다. 이름 그대로 `Rest API(HTTP API)`를 만들 때 사용하는 컨트롤러이다. 

참고로 `@ResponseBody`는 클래스 레벨에 두면 전체에 메서드에 적용된다. `@RestController` 에노테이션 안에 `@ResponseBody`가 적용되어 있다.

