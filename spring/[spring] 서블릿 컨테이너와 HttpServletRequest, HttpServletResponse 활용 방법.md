> ### ๐ โ๏ธ 
> **์๋ธ๋ฆฟ ์ปจํ์ด๋์ ์ญํ ๊ณผ HttpServletRequest, HttpServletResponse๋ฅผ ์ฌ์ฉํ์ฌ HTTP ์์ฒญ/์๋ต ๋ฉ์์ง๋ฅผ ์ฌ์ฉํ๋ ๊ธฐ๋ณธ์ ์ธ ๋ฐฉ๋ฒ์ ํ์ตํ๊ณ  ์ ๋ฆฌํ ํฌ์คํ์๋๋ค.**

---


# ์๋ธ๋ฆฟ ์ปจํ์ด๋์ HttpServletRequest, HttpServletResponse ์ฌ์ฉ ๋ฐฉ๋ฒ

์๋ธ๋ฆฟ ์ปจํ์ด๋๋ ํฐ์บฃ์ฒ๋ผ ์๋ธ๋ฆฟ์ ์ง์ํ๋ WAS๋ค. ์ฃผ๋ก ์๋ธ๋ฆฟ ๊ฐ์ฒด๋ฅผ ์์ฑ, ์ด๊ธฐํ, ํธ์ถ, ์ข๋ฃํ๋ ์๋ช์ฃผ๊ธฐ๋ฅผ ๊ด๋ฆฌํ๋ค. ํ ๊ฐ์ง ์ค์ํ ํน์ง์, ๋์ ์์ฒญ์ ์ํ ๋ฉํฐ ์ฐ๋ ๋ ์ฒ๋ฆฌ๋ฅผ ์ง์ํ๋ค๋ ์ ์ด๋ค. ๋๋ถ์ ๊ฐ๋ฐ์๋ ํด๋ผ์ด์ธํธ์ ์์ฒญ์ ํฐ ์ด๋ ค์ ์์ด ๋ฉํฐ ์ฐ๋ ๋๋ก ์ฒ๋ฆฌํ  ์ ์๋ค.

---

## 1. ์๋ธ๋ฆฟ ์ปจํ์ด๋์ ๋์ ๋ฐฉ์


### 1-1. ์๋ธ๋ฆฟ ์ปจํ์ด๋๊ฐ ์๋ธ๋ฆฟ์ ๋ง๋ ๋ค
์คํ๋ง ๋ถํธ๋ฅผ ์ฌ์ฉํ๋ ํ๋ก์ ํธ๋ ํฐ์บฃ ์๋ฒ๋ฅผ ๋ด์ฅํ๋ค. ๋ด์ฅ๋ ํฐ์บฃ ์๋ฒ๊ฐ ์๋ธ๋ฆฟ ์ปจํ์ด๋๋ฅผ ์์ฑํ๋ค. ์ค์  ์๋ธ๋ฆฟ ๊ฐ์ฒด๋ ์๋ธ๋ฆฟ ์ปจํ์ด๋๊ฐ ์์ฑํ๋ค.

![](https://images.velog.io/images/woply/post/3b454939-3518-4235-a23d-0f9f6cb6e354/image.png)

### 1-2. ์๋ธ๋ฆฟ์ HTTP ์์ฒญ์ ์ฒ๋ฆฌํ๋ค

์น ๋ธ๋ผ์ฐ์ ์์ ์๋ฒ๋ก ์์ฒญ์ ๋ณด๋ด๋ฉด, WAS๋ request / response ๊ฐ์ฒด๋ฅผ ์์ฑํ์ฌ ์๋ธ๋ฆฟ ์ปจํ์ด๋์ ์๋ ์๋ธ๋ฆฟ ๊ฐ์ฒด๋ฅผ ํธ์ถํ๋ค. 

![](https://images.velog.io/images/woply/post/c797d9a5-2f23-43a2-a732-ff6f8b936b22/image.png)

์๋ธ๋ฆฟ ๊ฐ์ฒด๋ service ๋ฉ์๋๋ฅผ ํธ์ถํ๋ฉด์ ๋งค๊ฐ๋ณ์๋ก request / response๋ฅผ ์ ๋ฌํ๋ค.

๋ด๋ถ ๋ก์ง์ ์ํด ํ์ํ ์์์ด ๋ชจ๋ ์๋ฃ๋๋ฉด, ์๋ธ๋ฆฟ ๊ฐ์ฒด๋ ์ข๋ฃ๋๋ค. ์ด๋, WAS๊ฐ response ์ ๋ณด๋ฅผ ํตํด HTTP ์๋ต ๋ฉ์์ง๋ฅผ ๋ง๋ค์ด ๋ฐํํ๋ค. ์น ๋ธ๋ผ์ฐ์ ๊ฐ ๊ทธ ๊ฒฐ๊ณผ๋ฅผ ์ฒ๋ฆฌํ๋ค.


![](https://images.velog.io/images/woply/post/ae37160b-8894-4c84-a4d6-15b401ab8fb7/image.png)

---

## 2. HttpServletRequest์ ์ญํ 

HttpServletRequest๋ HTTP ์์ฒญ ์ ๋ณด๋ฅผ ํธ๋ฆฌํ๊ฒ ์ฌ์ฉํ  ์ ์๋๋ก ๋์์ฃผ๋ ๊ฐ์ฒด๋ค. ํด๋ผ์ด์ธํธ๊ฐ ๋ณด๋ด๋ HTTP ์์ฒญ ๋ฉ์์ง๋ฅผ parsingํ์ฌ, ๊ทธ ๋ด์ฉ๋ฌผ์ ๋ด๊ณ  ์๋ค. ๊ฐ๋ฐ์๋ HttpServletRequest ๊ฐ์ฒด์์ HTTP ์์ฒญ ์ ๋ณด๋ฅผ ํธ๋ฆฌํ๊ฒ ์กฐํํ๊ณ , ํ์ฉํ  ์ ์๋ค.

์๋์ ๊ฐ์ HTTP ์์ฒญ ๋ฉ์ง๊ฐ ์๋ฒ๋ก ๋ค์ด์์ ๋, HttpServletRequest๋ฅผ ์ด์ฉํ์ฌ ์กฐํํ  ์ ์๋ ์ฃผ์ ๋ด์ฉ์ ์์๋ณด์.

> **HTTP ์์ฒญ ๋ฉ์์ง**
```
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
>
> username=kim&age=20
```

> **HttpServletRequest๋ฅผ ์ด์ฉํด ์กฐํํ  ์ ์๋ ์ ๋ณด**
- **START LINE** 
  - HTTP ๋ฉ์๋ 
  - URL 
  - ์ฟผ๋ฆฌ ์คํธ๋ง 
  - ์คํค๋ง, ํ๋กํ ์ฝ 
```
POST /save HTTP/1.1
```
- **ํค๋** 
  - ํค๋ ์กฐํ
```
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
```
- **๋ฐ๋** 
  - form ํ๋ผ๋ฏธํฐ ํ์ ์กฐํ 
  - message body ๋ฐ์ดํฐ ์ง์  ์กฐํ 
```
username=kim&age=20
```

HttpServletRequest ๊ฐ์ฒด๋ ๊ทธ ์ธ ์ฌ๋ฌ๊ฐ์ง ๋ถ๊ฐ๊ธฐ๋ฅ๋ ํจ๊ป ์ ๊ณตํ๋ค. ํนํ, HTTP ์์ฒญ์ด ์์๋ถํฐ ๋๋  ๋ ๊น์ง ์ ์ง๋๋ **์์ ์ ์ฅ์ ๊ธฐ๋ฅ**์ ์์ฃผ ์ฌ์ฉ๋๋ ๋ถ๊ฐ๊ธฐ๋ฅ์ด๋ค. ์๋ฒ ๋ด์์ ๋ค์ํ ๋ก์ง์ ์ํ ํ  ๋ ์ ์ฉํ๊ฒ ์ฌ์ฉํ  ์ ์๋ค.

> **์์ ์ ์ฅ์ ๊ธฐ๋ฅ** 
> - HTTP ์์ฒญ์ด ์์๋๋ ์์ ๋ถํฐ, ๋๋๋ ์์ ๊น์ง ์์๋ก ๋ฐ์ดํฐ๋ฅผ ์ ์ฅํ  ์ ์๋ค.
์ ์ฅ: `request.setAttribute(name, value)`
์กฐํ: `request.getAttribute(name)`

> **์ธ์ ๊ด๋ฆฌ ๊ธฐ๋ฅ**
```
request.getSession(create: true)
```

### 2-1. HttpServletRequest๊ฐ ์ ๊ณตํ๋ ๊ธฐ๋ณธ์ ์ธ ์กฐํ ๊ธฐ๋ฅ

์์ ์ธ๊ธํ๋๋ก, HttpServletRequest๋ HTTP ์์ฒญ ์ ๋ณด๋ฅผ ์กฐํํ  ๋ ์ฌ์ฉํ๋ค. HttpServletRequest๊ฐ ์ ๊ณตํ๋ ๊ธฐ๋ณธ์ ์ธ ์กฐํ ๊ธฐ๋ฅ์ ์ดํด๋ณด์.

``` java
@WebServlet(name = "requestHeaderServlet", urlPatterns = "/request-header")
public class RequestHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        printStartLine(request);
        printHeaders(request);
        printHeaderUtils(request);
        printEtc(request);

    }

    // start-line ์ ๋ณด
    private void printStartLine(HttpServletRequest request) {
        System.out.println("--- REQUEST-LINE - start ---");
        System.out.println("request.getMethod() = " + request.getMethod()); //GET
        System.out.println("request.getProtocal() = " + request.getProtocol()); // HTTP/1.1
        System.out.println("request.getScheme() = " + request.getScheme()); //http
        // http://localhost:8080/request-header
        System.out.println("request.getRequestURL() = " + request.getRequestURL());
        // /request-test
        System.out.println("request.getRequestURI() = " + request.getRequestURI());
        //username=hi
        System.out.println("request.getQueryString() = " +
                request.getQueryString());
        System.out.println("request.isSecure() = " + request.isSecure()); //https ์ฌ์ฉ ์ ๋ฌด
        System.out.println("--- REQUEST-LINE - end ---");
        System.out.println();
    }

    //Header ๋ชจ๋  ์ ๋ณด
    private void printHeaders(HttpServletRequest request) {
        System.out.println("--- Headers - start ---");

        /* ์์  ๋ฐฉ์
        Enumeration<String> headerNames = request.getHeaderNames();
        while(headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            System.out.println(headerName+" : "+headerName );
        }
        */

        // ๊ฐ๊ฒฐํ ๋ฌธ๋ฒ
        request.getHeaderNames().asIterator()
                .forEachRemaining(headerName -> System.out.println(headerName + " : " + headerName));

        System.out.println(" test");
        request.getHeader("host"); // ํ๋๋ง ๊บผ๋ด ๋ณด๊ณ  ์ถ์ ๋

        System.out.println("--- Headers - end ---");
        System.out.println();
    }

    //Header ํธ๋ฆฌํ ์กฐํ
    private void printHeaderUtils(HttpServletRequest request) {
        System.out.println("--- Header ํธ์ ์กฐํ start ---");
        System.out.println("[Host ํธ์ ์กฐํ]");
        System.out.println("request.getServerName() = " + request.getServerName()); //Host ํค๋
        System.out.println("request.getServerPort() = " + request.getServerPort()); //Host ํค๋
        System.out.println();


        System.out.println("[Accept-Language ํธ์ ์กฐํ]");
        request.getLocales().asIterator()
                .forEachRemaining(locale -> System.out.println("locale = " + locale));
        System.out.println("request.getLocale() = " + request.getLocale());
        System.out.println();

        System.out.println("[cookie ํธ์ ์กฐํ]");
        if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
        System.out.println();

        System.out.println("[Content ํธ์ ์กฐํ]");
        System.out.println("request.getContentType() = " + request.getContentType());
        System.out.println("request.getContentLength() = " + request.getContentLength());
        System.out.println("request.getCharacterEncoding() = " + request.getCharacterEncoding());
        System.out.println("--- Header ํธ์ ์กฐํ end ---");
        System.out.println();
    }

    // ๊ธฐํ ์ ๋ณด
    private void printEtc(HttpServletRequest request) {
        System.out.println("--- ๊ธฐํ ์กฐํ start ---");
        System.out.println("[Remote ์ ๋ณด]");
        System.out.println("request.getRemoteHost() = " + request.getRemoteHost()); //
        System.out.println("request.getRemoteAddr() = " + request.getRemoteAddr()); //
        System.out.println("request.getRemotePort() = " + request.getRemotePort()); //
        System.out.println();
        System.out.println("[Local ์ ๋ณด]");
        System.out.println("request.getLocalName() = " + request.getLocalName()); //
        System.out.println("request.getLocalAddr() = " + request.getLocalAddr()); //
        System.out.println("request.getLocalPort() = " + request.getLocalPort()); //
        System.out.println("--- ๊ธฐํ ์กฐํ end ---");
        System.out.println();
    }
}
```

> **start-line ์ ๋ณด ๊ฒฐ๊ณผ**
```
--- REQUEST-LINE - start ---
request.getMethod() = GET
request.getProtocal() = HTTP/1.1
request.getScheme() = http
request.getRequestURL() = http://localhost:8080/request-header
request.getRequestURI() = /request-header
request.getQueryString() = username=hello
>
request.isSecure() = false
--- REQUEST-LINE - end ---
```

> **ํค๋ ์ ๋ณด ๊ฒฐ๊ณผ**
```
--- Headers - start ---
host: localhost:8080
connection: keep-alive
cache-control: max-age=0
sec-ch-ua: "Chromium";v="88", "Google Chrome";v="88", ";Not A Brand";v="99"
sec-ch-ua-mobile: ?0
>
upgrade-insecure-requests: 1
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 11_2_0) AppleWebKit/537.36
(KHTML, like Gecko) Chrome/88.0.4324.150 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/
webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
sec-fetch-site: none
sec-fetch-mode: navigate
>
request.getHeaderNames().asIterator()
.forEachRemaining(headerName -> System.out.println(headerName + ":
>
sec-fetch-user: ?1
sec-fetch-dest: document
accept-encoding: gzip, deflate, br
accept-language: ko,en-US;q=0.9,en;q=0.8,ko-KR;q=0.7
--- Headers - end ---
```

> **Header ํธ๋ฆฌํ ์กฐํ ๊ฒฐ๊ณผ**
```
--- Header ํธ์ ์กฐํ start ---
[Host ํธ์ ์กฐํ]
request.getServerName() = localhost
request.getServerPort() = 8080
>
[Accept-Language ํธ์ ์กฐํ]
locale = ko_KR
locale = ko
locale = en_US
locale = en
request.getLocale() = ko_KR
>
[cookie ํธ์ ์กฐํ]
Idea-7bc98fcd: d7c18bac-7365-428f-8caf-49ca1e4244dc
>
[Content ํธ์ ์กฐํ]
request.getContentType() = null
request.getContentLength() = -1
request.getCharacterEncoding() = UTF-8
--- Header ํธ์ ์กฐํ end ---
```

> **๊ธฐํ ์ ๋ณด ๊ฒฐ๊ณผ**
(๊ธฐํ ์ ๋ณด๋ HTTP ๋ฉ์์ง์ ์ ๋ณด๋ ์๋๋ค.)
```
--- ๊ธฐํ ์กฐํ start ---
[Remote ์ ๋ณด]
request.getRemoteHost() = 0:0:0:0:0:0:0:1
request.getRemoteAddr() = 0:0:0:0:0:0:0:1
request.getRemotePort() = 49288
>
[Local ์ ๋ณด]
request.getLocalName() = localhost
request.getLocalAddr() = 0:0:0:0:0:0:0:1
request.getLocalPort() = 8080
--- ๊ธฐํ ์กฐํ end ---
```

---

## 3. HttpServletResponse์ ์ญํ 

HttpServletResponse๋ HTTP ์์ฒญ์ ๋ํ ์๋ต ๋ฉ์์ง๋ฅผ ์์ฑํ๊ณ , ๊ธฐํ ํธ์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค.

HTTP ์๋ต ๋ฉ์์ง๋ ์๋์ ๊ฐ์ด ํฌ๊ฒ 3๊ฐ์ง ๋ด์ฉ์ด๋ค. 

1. **๋จ์ ํ์คํธ ์๋ต**
- ex) `writer.println("ok");`
2. **HTML ์๋ต**
3. **HTTP API**
- ex) ๋ฉ์์ง ๋ฐ๋์ JSON ๋ฐ์ดํฐ๋ฅผ ๋ด์์ ์๋ต

HTTP ์๋ต ๋ฉ์์ง๋ฅผ ์์ฑํ๋ ๋ฐฉ๋ฒ์ ์๋์ ๊ฐ๋ค. 


### 3-1. HttpServletResponse ๊ธฐ๋ณธ ์ฌ์ฉ๋ฒ๊ณผ ํธ์ ๋ฉ์๋ ์์

```java
@WebServlet(name = "ResponseHeaderServlet", urlPatterns = "/response-header")
public class ResponseHeaderServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // [status-line]
        response.setStatus(HttpServletResponse.SC_OK);

        // [response-header]
        //response.setHeader("Content-type", "text/plain;charset=utf-8");
        response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
        response.setHeader("Pragma", "no-cache");
        response.setHeader("my-header", "hello");


        // [Header ํธ์ ๋ฉ์๋]
        content(response);
        cookie(response);
        redirect(response);

        PrintWriter writer = response.getWriter();
        writer.println("ok~!");
    }

    // Content ํธ์ ๋ฉ์๋
    private void content(HttpServletResponse response) {
        //Content-Type: text/plain;charset=utf-8
        //Content-Length: 2
        //response.setHeader("Content-Type", "text/plain;charset=utf-8");
        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        //response.setContentLength(8); //(์๋ต์ ์๋ ์์ฑ)
    }
    
    // ์ฟ ํค ํธ์ ๋ฉ์๋
    private void cookie(HttpServletResponse response) {
        // Set-Cookie: myCookie=good; Max-Age=600;
        // response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
        Cookie cookie = new Cookie("myCookie", "good");
        cookie.setMaxAge(600); //600์ด
        response.addCookie(cookie);
    }

    // redirect ํธ์ ๋ฉ์๋
    private void redirect(HttpServletResponse response) throws IOException {
        // Status Code 302
        // Location: /basic / hello - form.html
        // response.setStatus(HttpServletResponse.SC_FOUND); //302
        // response.setHeader("Location", "/basic/hello-form.html");
        response.sendRedirect("/basic/hello-form.html");
    }
}
```

### 3-2. HttpServletResponse HTML ์๋ต

```java
@WebServlet(name = "ResponseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Content-Type: text/html; charset=utf-8;
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("<div>์๋?</div>");
        writer.println("</body>");
        writer.println("</html>");
    }
}
```

HTTP ์๋ต์ผ๋ก HTML์ ๋ฐํํ  ๋๋ content-type์ text/html ๋ก ์ง์ ํด์ผ ํ๋ค. 


### 3-3. HttpServletResponse API JSON ์๋ต

```java
@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();


    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        // Content-type: application/json
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        HelloData helloData = new HelloData();
        helloData.setUsername("Jaemin");
        helloData.setAge(32);

        // {"username" = "jaemin", "age":20}
        String result = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(result);
    }
}
```
HTTP ์๋ต์ผ๋ก JSON์ ๋ฐํํ  ๋๋ content-type์ application/json ๋ก ์ง์ ํด์ผ ํ๋ค. Jackson ๋ผ์ด๋ธ๋ฌ๋ฆฌ๊ฐ ์ ๊ณตํ๋ `objectMapper.writeValueAsString()` ๋ฅผ ์ฌ์ฉํ๋ฉด ๊ฐ์ฒด๋ฅผ JSON ๋ฌธ์๋ก ๋ณ๊ฒฝํ  ์ ์๋ค. 

> **์ฐธ๊ณ **   
> application/json ์ ์คํ์ utf-8 ํ์์ ์ฌ์ฉํ๋๋ก ์ ์๋์ด ์๋ค. ๊ทธ๋์ ์คํ์์ charset=utf-8 
๊ณผ ๊ฐ์ ์ถ๊ฐ ํ๋ผ๋ฏธํฐ๋ฅผ ์ง์ํ์ง ์๋๋ค. ๋ฐ๋ผ์ content-type์ application/json;charset=utf-8๋ก ์์ฑํ  ํ์๊ฐ ์๋ค. ๋จ, response.getWriter()๋ฅผ ์ฌ์ฉํ๋ฉด ์ถ๊ฐ ํ๋ผ๋ฏธํฐ๋ฅผ ์๋์ผ๋ก ์ถ๊ฐํ๋ค. response.getOutputStream()์ ์ฌ์ฉํ์ฌ ์ถ๋ ฅํ๋ฉด ๋๋ค.
