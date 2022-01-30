> ### 📖 ✏️ 
> **클라이언트가 HTTP 요청 메시지를 이용하여 서버로 데이터를 전달하는 3가지 방법을 학습하고 정리한 포스팅입니다.**

---



# HTTP 요청 메시지를 이용하여 서버에 데이터를 전달하는 3가지 방법

클라이언트가 HTTP 요청 메시지를 이용해 서버에 데이터를 전달하는 방법은 크게 3가지다.

1. **GET - 쿼리 파라미터** 
- `url?username=hello&age=20`
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달 
- 예) 검색, 필터, 페이징등에서 많이 사용하는 방식 
  
2. **POST - HTML Form** 
- content-type: `application/x-www-form-urlencoded` 
- 메시지 바디에 쿼리 파리미터 형식으로 전달   
  - ex) `username=hello&age=20` 
- 회원 가입, 상품 주문, HTML Form에 주로 사용 
  
3. **HTTP message body에 데이터를 직접 담아서 요청** 
- HTTP API에서 주로 사용
  - ex) JSON, XML, TEXT
- 데이터 형식은 주로 JSON 사용 
- POST, PUT, PATCH 방식으로 서버에 전달


---

## 1. GET 쿼리 파라미터 방식으로 데이터 전송

GET 방식은 쿼리 파라미터를 이용해 데이터를 전송한다. 메시지 바디로 데이터 전송은 가능하나, 사용하지 않는다. GET 방식으로 `username=hello`와 `age=20`이라는 데이터를 전송한다면 아래와 같이 쿼리 파라미터를 작성해야 한다.

```
http://localhost:8080/request-param?username=hello&age=20 
```
쿼리 파라미터로 전달할 텍스트 데이터는 URL에 ?를 기입하여 시작한다. 추가 파라미터는 &로 구분한다.

서버에서는 HttpServletRequest가 제공하는 다음 메서드를 통해 쿼리 파라미터를 편리하게 조회할 수 있다.

> **쿼리 파라미터 조회 메서드** 
```java
String username = request.getParameter("username"); //단일 파라미터 조회 
Enumeration<String> parameterNames = request.getParameterNames(); //파라미터 이름들 모두 조회 
Map<String, String[]> parameterMap = request.getParameterMap(); //파라미터를 Map 으로 조회 
String[] usernames = request.getParameterValues("username"); //복수 파라미터 조회 
```

**쿼리 파라미터 조회 예시 코드** 
```java
/**
 * 1. 파라미터 전송 기능
 * http://localhost:8080/request-param?username=hello&age=20
 * <p>
 * 2. 동일한 파라미터 전송 가능
 * http://localhost:8080/request-param?username=hello&username=kim&age=20 
 */
@WebServlet(name = "RequestParamServlet", urlPatterns = "/request-param")
public class RequestParamServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        System.out.println("[전체 파라미터 조회] - start");
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> System.out.println(paramName + "=" + request.getParameter(paramName)));
        System.out.println("[전체 파라미터 조회] - end");


        System.out.println();
        System.out.println("[단일 파라미터 조회] - start");
        String username = request.getParameter("username");
        System.out.println("username = " + username);
        String age = request.getParameter("age");
        System.out.println("age = " + age);
        System.out.println("[단일 파라미터 조회] - end");

        System.out.println();
        System.out.println("[이름이 같은 복수 파라미터 조회] - start");
        String[] usernames = request.getParameterValues("username");
        for (String name : usernames) {
            System.out.println("name = " + name);
        }
        response.getWriter().write("OK");
        System.out.println("[이름이 같은 복수 파라미터 조회] - end");
    }
}
```

**결과**
```java
[전체 파라미터 조회] - start
username=hello
age=20
ticket=what
[전체 파라미터 조회] - end

[단일 파라미터 조회] - start
username = hello
age = 20
[단일 파라미터 조회] - end

[이름이 같은 복수 파라미터 조회] - start
name = hello
name = hello2
[이름이 같은 복수 파라미터 조회] - end
```

> **참고: 복수 파라미터에서 단일 파라미터 조회 방법**
`request.getParameter()`는 하나의 파라미터 이름에 대해서 단 하나의 값만 조회할 수 있다. 파라미터 이름이 중복일 경우, `request.getParameterValues()`를 사용해야 한다. 파라미터 이름이 중복일 때, `request.getParameter()`를 사용하면 `request.getParameterValues()`의 첫 번째 값을 반환한다. 



## 2. POST HTML Form 방식으로 데이터 전송


클라이언트는 HTML의 Form을 이용해서 서버로 데이터를 전송할 수 있다. 주로 회원 가입, 상품 주문 등에서 사용한다.

HTML의 Form을 이용한다면, content-type은 `application/x-www-form-urlencoded`를 사용해야 한다. 실제 데이터는 메시지 바디에 쿼리 파리미터 형식으로 담을 수 있다.  

**POST HTML Form 방식 예시 코드**
```HTML
<!DOCTYPE html>

<html>
<head>

    <meta charset="UTF-8">
    <title>Title</title>

</head>

<body>
<form action="/request-param" method="post">
    username: <input type="text" name="username" /> 
    age: <input type="text" name="age" /> 
    <button type="submit">전송</button> 
</form>
</body>
</html>

```

POST 방식으로 HTML Form을 전송하면 웹 브라우저는 아래와 같이  HTTP 메시지를 만든다. 웹 브라우저 개발자 모드에서 확인해 볼 수 있다. 

- 요청 URL: `http://localhost:8080/request-param` 
- content-type: `application/x-www-form-urlencoded`
- message body: `username=hello&age=20`


`application/x-www-form-urlencoded` 형식은 `GET 쿼리 파라미터` 형식과 동일하다. 데이터가 담기는 위치만 다르다. 따라서, 작성 방법과 조회 방법이 `GET 쿼리 파라미터`와 동일하다. 서버에서는 GET과 POST 구분없이 `request.getParameter()` 로 조회할 수 있다.


> **참고** 
> content-type은 HTTP 메시지 바디의 데이터 형식을 지정한다. GET URL 쿼리 파라미터 형식은 content-type이 없다. HTTP 메시지 바디를 사용하지 않기 때문이다.   
>   
> HTTP 메시지 바디를 사용하는 POST HTML Form 형식은 반드시 content-type를 통해 데이터의 형식을 지정해야 한다. Form을 이용해 데이터를 전송하는 형식은 `application/x-www-form-urlencoded`다.



---


## 3. HTTP API 방식으로 데이터 전송

HTTP API 방식은 HTTP message body에 데이터를 직접 담아서 요청한다. HTTP API는 다양한 데이터 형식을 지원한다. 최근에는 주로 JSON을 사용한다. API 방식은 POST뿐만 아니라, PUT과 PATCH 형식으로도 사용할 수 있다.

### 3-1 . HTTP API - 단순 텍스트 전송

HTTP 메시지 바디의 데이터는 InputStream을 사용해서 직접 읽을 수 있다. 'hello'라는 간단한 텍스트 전송을 통해 살펴보자.

```java
@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-body-string")
public class RequestBodyStringServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        ServletInputStream inputStream = request.getInputStream(); // 바이트 코드로 바로 얻을 수 있다.
        String messegeBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messegeBody = " + messegeBody);
        response.getWriter().write("OK~!");
    }
}
```

> **참고**   
> `inputStream`은 byte 코드를 반환한다. byte 코드를 문자(String) 형식으로 보려면 문자표(Charset) 지정이 필요하다. 여기서는 `UTF_8 Charset`을 사용했다.

> **출력 결과**   
> `messageBody = hello` 
- content-type: text/plain 
- message body: hello  

---

### 3-2 . HTTP API - JSON 전송

이번에는 JSON 형식으로 데이터를 전달해보자. JSON은 HTTP API에서 가장 많이 사용하는 데이터 방식이다.

> **JSON 형식 전송**    
- content-type: `application/json`
- message body: `{"username": "hello", "age": 20}`
- 결과: `messageBody = {"username": "hello", "age": 20}` 

우선, JSON 형식으로 파싱할 수 있도록 객체를 생성한다.

```java
package hello.servlet.basic;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class HelloData {

    private String username;
    private int age;
}
```

`objectMapper`를 이용하여 JSON 데이터를 객체로 mapping한다. 객체에 담긴 JSON 내용을 확인해보자.

```java
@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messageBody = " + messageBody);

        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);

        System.out.println("helloData.getUsername() = " + helloData.getUsername());
        System.out.println("helloData.getAge() = " + helloData.getAge());

        response.getWriter().write("O.K");
    }
}
```


> **출력 결과**
```java
messageBody={"username": "hello", "age": 20}
data.username=hello
data.age=20
```


> **참고**   
> JSON 결과를 파싱하여 자바 객체로 사용하려면, `Jackson`, `Gson` 같은 JSON 변환 라이브러리가 필요하다. 스프링 부트로 Spring MVC를 선택하면 Jackson 라이브러리(ObjectMapper)를 기본으로 제공한다. 
