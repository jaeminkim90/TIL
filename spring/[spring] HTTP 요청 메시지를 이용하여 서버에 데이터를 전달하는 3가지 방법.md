> ### ๐ โ๏ธ 
> **ํด๋ผ์ด์ธํธ๊ฐ HTTP ์์ฒญ ๋ฉ์์ง๋ฅผ ์ด์ฉํ์ฌ ์๋ฒ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ 3๊ฐ์ง ๋ฐฉ๋ฒ์ ํ์ตํ๊ณ  ์ ๋ฆฌํ ํฌ์คํ์๋๋ค.**

---



# HTTP ์์ฒญ ๋ฉ์์ง๋ฅผ ์ด์ฉํ์ฌ ์๋ฒ์ ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ 3๊ฐ์ง ๋ฐฉ๋ฒ

ํด๋ผ์ด์ธํธ๊ฐ HTTP ์์ฒญ ๋ฉ์์ง๋ฅผ ์ด์ฉํด ์๋ฒ์ ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํ๋ ๋ฐฉ๋ฒ์ ํฌ๊ฒ 3๊ฐ์ง๋ค.

1. **GET - ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ** 
- `url?username=hello&age=20`
- ๋ฉ์์ง ๋ฐ๋ ์์ด, URL์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ์ ๋ฐ์ดํฐ๋ฅผ ํฌํจํด์ ์ ๋ฌ 
- ์) ๊ฒ์, ํํฐ, ํ์ด์ง๋ฑ์์ ๋ง์ด ์ฌ์ฉํ๋ ๋ฐฉ์ 
  
2. **POST - HTML Form** 
- content-type: `application/x-www-form-urlencoded` 
- ๋ฉ์์ง ๋ฐ๋์ ์ฟผ๋ฆฌ ํ๋ฆฌ๋ฏธํฐ ํ์์ผ๋ก ์ ๋ฌ   
  - ex) `username=hello&age=20` 
- ํ์ ๊ฐ์, ์ํ ์ฃผ๋ฌธ, HTML Form์ ์ฃผ๋ก ์ฌ์ฉ 
  
3. **HTTP message body์ ๋ฐ์ดํฐ๋ฅผ ์ง์  ๋ด์์ ์์ฒญ** 
- HTTP API์์ ์ฃผ๋ก ์ฌ์ฉ
  - ex) JSON, XML, TEXT
- ๋ฐ์ดํฐ ํ์์ ์ฃผ๋ก JSON ์ฌ์ฉ 
- POST, PUT, PATCH ๋ฐฉ์์ผ๋ก ์๋ฒ์ ์ ๋ฌ


---

## 1. GET ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ๋ฐฉ์์ผ๋ก ๋ฐ์ดํฐ ์ ์ก

GET ๋ฐฉ์์ ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ฅผ ์ด์ฉํด ๋ฐ์ดํฐ๋ฅผ ์ ์กํ๋ค. ๋ฉ์์ง ๋ฐ๋๋ก ๋ฐ์ดํฐ ์ ์ก์ ๊ฐ๋ฅํ๋, ์ฌ์ฉํ์ง ์๋๋ค. GET ๋ฐฉ์์ผ๋ก `username=hello`์ `age=20`์ด๋ผ๋ ๋ฐ์ดํฐ๋ฅผ ์ ์กํ๋ค๋ฉด ์๋์ ๊ฐ์ด ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ฅผ ์์ฑํด์ผ ํ๋ค.

```
http://localhost:8080/request-param?username=hello&age=20 
```
์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ก ์ ๋ฌํ  ํ์คํธ ๋ฐ์ดํฐ๋ URL์ ?๋ฅผ ๊ธฐ์ํ์ฌ ์์ํ๋ค. ์ถ๊ฐ ํ๋ผ๋ฏธํฐ๋ &๋ก ๊ตฌ๋ถํ๋ค.

์๋ฒ์์๋ HttpServletRequest๊ฐ ์ ๊ณตํ๋ ๋ค์ ๋ฉ์๋๋ฅผ ํตํด ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ๋ฅผ ํธ๋ฆฌํ๊ฒ ์กฐํํ  ์ ์๋ค.

> **์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ์กฐํ ๋ฉ์๋** 
```java
String username = request.getParameter("username"); //๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ 
Enumeration<String> parameterNames = request.getParameterNames(); //ํ๋ผ๋ฏธํฐ ์ด๋ฆ๋ค ๋ชจ๋ ์กฐํ 
Map<String, String[]> parameterMap = request.getParameterMap(); //ํ๋ผ๋ฏธํฐ๋ฅผ Map ์ผ๋ก ์กฐํ 
String[] usernames = request.getParameterValues("username"); //๋ณต์ ํ๋ผ๋ฏธํฐ ์กฐํ 
```

**์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ์กฐํ ์์ ์ฝ๋** 
```java
/**
 * 1. ํ๋ผ๋ฏธํฐ ์ ์ก ๊ธฐ๋ฅ
 * http://localhost:8080/request-param?username=hello&age=20
 * <p>
 * 2. ๋์ผํ ํ๋ผ๋ฏธํฐ ์ ์ก ๊ฐ๋ฅ
 * http://localhost:8080/request-param?username=hello&username=kim&age=20 
 */
@WebServlet(name = "RequestParamServlet", urlPatterns = "/request-param")
public class RequestParamServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        System.out.println("[์ ์ฒด ํ๋ผ๋ฏธํฐ ์กฐํ] - start");
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> System.out.println(paramName + "=" + request.getParameter(paramName)));
        System.out.println("[์ ์ฒด ํ๋ผ๋ฏธํฐ ์กฐํ] - end");


        System.out.println();
        System.out.println("[๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ] - start");
        String username = request.getParameter("username");
        System.out.println("username = " + username);
        String age = request.getParameter("age");
        System.out.println("age = " + age);
        System.out.println("[๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ] - end");

        System.out.println();
        System.out.println("[์ด๋ฆ์ด ๊ฐ์ ๋ณต์ ํ๋ผ๋ฏธํฐ ์กฐํ] - start");
        String[] usernames = request.getParameterValues("username");
        for (String name : usernames) {
            System.out.println("name = " + name);
        }
        response.getWriter().write("OK");
        System.out.println("[์ด๋ฆ์ด ๊ฐ์ ๋ณต์ ํ๋ผ๋ฏธํฐ ์กฐํ] - end");
    }
}
```

**๊ฒฐ๊ณผ**
```java
[์ ์ฒด ํ๋ผ๋ฏธํฐ ์กฐํ] - start
username=hello
age=20
ticket=what
[์ ์ฒด ํ๋ผ๋ฏธํฐ ์กฐํ] - end

[๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ] - start
username = hello
age = 20
[๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ] - end

[์ด๋ฆ์ด ๊ฐ์ ๋ณต์ ํ๋ผ๋ฏธํฐ ์กฐํ] - start
name = hello
name = hello2
[์ด๋ฆ์ด ๊ฐ์ ๋ณต์ ํ๋ผ๋ฏธํฐ ์กฐํ] - end
```

> **์ฐธ๊ณ : ๋ณต์ ํ๋ผ๋ฏธํฐ์์ ๋จ์ผ ํ๋ผ๋ฏธํฐ ์กฐํ ๋ฐฉ๋ฒ**
`request.getParameter()`๋ ํ๋์ ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ ๋ํด์ ๋จ ํ๋์ ๊ฐ๋ง ์กฐํํ  ์ ์๋ค. ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ด ์ค๋ณต์ผ ๊ฒฝ์ฐ, `request.getParameterValues()`๋ฅผ ์ฌ์ฉํด์ผ ํ๋ค. ํ๋ผ๋ฏธํฐ ์ด๋ฆ์ด ์ค๋ณต์ผ ๋, `request.getParameter()`๋ฅผ ์ฌ์ฉํ๋ฉด `request.getParameterValues()`์ ์ฒซ ๋ฒ์งธ ๊ฐ์ ๋ฐํํ๋ค. 



## 2. POST HTML Form ๋ฐฉ์์ผ๋ก ๋ฐ์ดํฐ ์ ์ก


ํด๋ผ์ด์ธํธ๋ HTML์ Form์ ์ด์ฉํด์ ์๋ฒ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ์กํ  ์ ์๋ค. ์ฃผ๋ก ํ์ ๊ฐ์, ์ํ ์ฃผ๋ฌธ ๋ฑ์์ ์ฌ์ฉํ๋ค.

HTML์ Form์ ์ด์ฉํ๋ค๋ฉด, content-type์ `application/x-www-form-urlencoded`๋ฅผ ์ฌ์ฉํด์ผ ํ๋ค. ์ค์  ๋ฐ์ดํฐ๋ ๋ฉ์์ง ๋ฐ๋์ ์ฟผ๋ฆฌ ํ๋ฆฌ๋ฏธํฐ ํ์์ผ๋ก ๋ด์ ์ ์๋ค.  

**POST HTML Form ๋ฐฉ์ ์์ ์ฝ๋**
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
    <button type="submit">์ ์ก</button> 
</form>
</body>
</html>

```

POST ๋ฐฉ์์ผ๋ก HTML Form์ ์ ์กํ๋ฉด ์น ๋ธ๋ผ์ฐ์ ๋ ์๋์ ๊ฐ์ด  HTTP ๋ฉ์์ง๋ฅผ ๋ง๋ ๋ค. ์น ๋ธ๋ผ์ฐ์  ๊ฐ๋ฐ์ ๋ชจ๋์์ ํ์ธํด ๋ณผ ์ ์๋ค. 

- ์์ฒญ URL: `http://localhost:8080/request-param` 
- content-type: `application/x-www-form-urlencoded`
- message body: `username=hello&age=20`


`application/x-www-form-urlencoded` ํ์์ `GET ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ` ํ์๊ณผ ๋์ผํ๋ค. ๋ฐ์ดํฐ๊ฐ ๋ด๊ธฐ๋ ์์น๋ง ๋ค๋ฅด๋ค. ๋ฐ๋ผ์, ์์ฑ ๋ฐฉ๋ฒ๊ณผ ์กฐํ ๋ฐฉ๋ฒ์ด `GET ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ`์ ๋์ผํ๋ค. ์๋ฒ์์๋ GET๊ณผ POST ๊ตฌ๋ถ์์ด `request.getParameter()` ๋ก ์กฐํํ  ์ ์๋ค.


> **์ฐธ๊ณ ** 
> content-type์ HTTP ๋ฉ์์ง ๋ฐ๋์ ๋ฐ์ดํฐ ํ์์ ์ง์ ํ๋ค. GET URL ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ ํ์์ content-type์ด ์๋ค. HTTP ๋ฉ์์ง ๋ฐ๋๋ฅผ ์ฌ์ฉํ์ง ์๊ธฐ ๋๋ฌธ์ด๋ค.   
>   
> HTTP ๋ฉ์์ง ๋ฐ๋๋ฅผ ์ฌ์ฉํ๋ POST HTML Form ํ์์ ๋ฐ๋์ content-type๋ฅผ ํตํด ๋ฐ์ดํฐ์ ํ์์ ์ง์ ํด์ผ ํ๋ค. Form์ ์ด์ฉํด ๋ฐ์ดํฐ๋ฅผ ์ ์กํ๋ ํ์์ `application/x-www-form-urlencoded`๋ค.



---


## 3. HTTP API ๋ฐฉ์์ผ๋ก ๋ฐ์ดํฐ ์ ์ก

HTTP API ๋ฐฉ์์ HTTP message body์ ๋ฐ์ดํฐ๋ฅผ ์ง์  ๋ด์์ ์์ฒญํ๋ค. HTTP API๋ ๋ค์ํ ๋ฐ์ดํฐ ํ์์ ์ง์ํ๋ค. ์ต๊ทผ์๋ ์ฃผ๋ก JSON์ ์ฌ์ฉํ๋ค. API ๋ฐฉ์์ POST๋ฟ๋ง ์๋๋ผ, PUT๊ณผ PATCH ํ์์ผ๋ก๋ ์ฌ์ฉํ  ์ ์๋ค.

### 3-1 . HTTP API - ๋จ์ ํ์คํธ ์ ์ก

HTTP ๋ฉ์์ง ๋ฐ๋์ ๋ฐ์ดํฐ๋ InputStream์ ์ฌ์ฉํด์ ์ง์  ์ฝ์ ์ ์๋ค. 'hello'๋ผ๋ ๊ฐ๋จํ ํ์คํธ ์ ์ก์ ํตํด ์ดํด๋ณด์.

```java
@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-body-string")
public class RequestBodyStringServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        ServletInputStream inputStream = request.getInputStream(); // ๋ฐ์ดํธ ์ฝ๋๋ก ๋ฐ๋ก ์ป์ ์ ์๋ค.
        String messegeBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("messegeBody = " + messegeBody);
        response.getWriter().write("OK~!");
    }
}
```

> **์ฐธ๊ณ **   
> `inputStream`์ byte ์ฝ๋๋ฅผ ๋ฐํํ๋ค. byte ์ฝ๋๋ฅผ ๋ฌธ์(String) ํ์์ผ๋ก ๋ณด๋ ค๋ฉด ๋ฌธ์ํ(Charset) ์ง์ ์ด ํ์ํ๋ค. ์ฌ๊ธฐ์๋ `UTF_8 Charset`์ ์ฌ์ฉํ๋ค.

> **์ถ๋ ฅ ๊ฒฐ๊ณผ**   
> `messageBody = hello` 
- content-type: text/plain 
- message body: hello  

---

### 3-2 . HTTP API - JSON ์ ์ก

์ด๋ฒ์๋ JSON ํ์์ผ๋ก ๋ฐ์ดํฐ๋ฅผ ์ ๋ฌํด๋ณด์. JSON์ HTTP API์์ ๊ฐ์ฅ ๋ง์ด ์ฌ์ฉํ๋ ๋ฐ์ดํฐ ๋ฐฉ์์ด๋ค.

> **JSON ํ์ ์ ์ก**    
- content-type: `application/json`
- message body: `{"username": "hello", "age": 20}`
- ๊ฒฐ๊ณผ: `messageBody = {"username": "hello", "age": 20}` 

์ฐ์ , JSON ํ์์ผ๋ก ํ์ฑํ  ์ ์๋๋ก ๊ฐ์ฒด๋ฅผ ์์ฑํ๋ค.

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

`objectMapper`๋ฅผ ์ด์ฉํ์ฌ JSON ๋ฐ์ดํฐ๋ฅผ ๊ฐ์ฒด๋ก mappingํ๋ค. ๊ฐ์ฒด์ ๋ด๊ธด JSON ๋ด์ฉ์ ํ์ธํด๋ณด์.

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


> **์ถ๋ ฅ ๊ฒฐ๊ณผ**
```java
messageBody={"username": "hello", "age": 20}
data.username=hello
data.age=20
```


> **์ฐธ๊ณ **   
> JSON ๊ฒฐ๊ณผ๋ฅผ ํ์ฑํ์ฌ ์๋ฐ ๊ฐ์ฒด๋ก ์ฌ์ฉํ๋ ค๋ฉด, `Jackson`, `Gson` ๊ฐ์ JSON ๋ณํ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๊ฐ ํ์ํ๋ค. ์คํ๋ง ๋ถํธ๋ก Spring MVC๋ฅผ ์ ํํ๋ฉด Jackson ๋ผ์ด๋ธ๋ฌ๋ฆฌ(ObjectMapper)๋ฅผ ๊ธฐ๋ณธ์ผ๋ก ์ ๊ณตํ๋ค. 
