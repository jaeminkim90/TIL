> ### 📖 ✏️ 
> **서블릿 컨테이너의 역할과 HttpServletRequest, HttpServletResponse를 사용하여 HTTP 요청/응답 메시지를 사용하는 기본적인 방법을 학습하고 정리한 포스팅입니다.**

---


# 서블릿 컨테이너와 HttpServletRequest, HttpServletResponse 사용 방법

서블릿 컨테이너는 톰캣처럼 서블릿을 지원하는 WAS다. 주로 서블릿 객체를 생성, 초기화, 호출, 종료하는 생명주기를 관리한다. 한 가지 중요한 특징은, 동시 요청을 위한 멀티 쓰레드 처리를 지원한다는 점이다. 덕분에 개발자는 클라이언트의 요청을 큰 어려움 없이 멀티 쓰레드로 처리할 수 있다.

---

## 1. 서블릿 컨테이너의 동작 방식


### 1-1. 서블릿 컨테이너가 서블릿을 만든다
스프링 부트를 사용하는 프로젝트는 톰캣 서버를 내장한다. 내장된 톰캣 서버가 서블릿 컨테이너를 생성한다. 실제 서블릿 객체는 서블릿 컨테이너가 생성한다.

![](https://images.velog.io/images/woply/post/3b454939-3518-4235-a23d-0f9f6cb6e354/image.png)

### 1-2. 서블릿은 HTTP 요청을 처리한다

웹 브라우저에서 서버로 요청을 보내면, WAS는 request / response 객체를 생성하여 서블릿 컨테이너에 있는 서블릿 객체를 호출한다. 

![](https://images.velog.io/images/woply/post/c797d9a5-2f23-43a2-a732-ff6f8b936b22/image.png)

서블릿 객체는 service 메서드를 호출하면서 매개변수로 request / response를 전달한다.

내부 로직에 의해 필요한 작업이 모두 완료되면, 서블릿 객체는 종료된다. 이때, WAS가 response 정보를 통해 HTTP 응답 메시지를 만들어 반환한다. 웹 브라우저가 그 결과를 처리한다.


![](https://images.velog.io/images/woply/post/ae37160b-8894-4c84-a4d6-15b401ab8fb7/image.png)

---

## 2. HttpServletRequest의 역할

HttpServletRequest는 HTTP 요청 정보를 편리하게 사용할 수 있도록 도와주는 객체다. 클라이언트가 보내는 HTTP 요청 메시지를 parsing하여, 그 내용물을 담고 있다. 개발자는 HttpServletRequest 객체에서 HTTP 요청 정보를 편리하게 조회하고, 활용할 수 있다.

아래와 같은 HTTP 요청 메지가 서버로 들어왔을 때, HttpServletRequest를 이용하여 조회할 수 있는 주요 내용을 알아보자.

> **HTTP 요청 메시지**
```
POST /save HTTP/1.1
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
>
> username=kim&age=20
```

> **HttpServletRequest를 이용해 조회할 수 있는 정보**
- **START LINE** 
  - HTTP 메소드 
  - URL 
  - 쿼리 스트링 
  - 스키마, 프로토콜 
```
POST /save HTTP/1.1
```
- **헤더** 
  - 헤더 조회
```
Host: localhost:8080
Content-Type: application/x-www-form-urlencoded
```
- **바디** 
  - form 파라미터 형식 조회 
  - message body 데이터 직접 조회 
```
username=kim&age=20
```

HttpServletRequest 객체는 그 외 여러가지 부가기능도 함께 제공한다. 특히, HTTP 요청이 시작부터 끝날 때 까지 유지되는 **임시 저장소 기능**은 자주 사용되는 부가기능이다. 서버 내에서 다양한 로직을 수행 할 때 유용하게 사용할 수 있다.

> **임시 저장소 기능** 
> - HTTP 요청이 시작되는 시점부터, 끝나는 시점까지 임시로 데이터를 저장할 수 있다.
저장: `request.setAttribute(name, value)`
조회: `request.getAttribute(name)`

> **세션 관리 기능**
```
request.getSession(create: true)
```

### 2-1. HttpServletRequest가 제공하는 기본적인 조회 기능

앞서 언급한대로, HttpServletRequest는 HTTP 요청 정보를 조회할 때 사용한다. HttpServletRequest가 제공하는 기본적인 조회 기능을 살펴보자.

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

    // start-line 정보
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
        System.out.println("request.isSecure() = " + request.isSecure()); //https 사용 유무
        System.out.println("--- REQUEST-LINE - end ---");
        System.out.println();
    }

    //Header 모든 정보
    private void printHeaders(HttpServletRequest request) {
        System.out.println("--- Headers - start ---");

        /* 예전 방식
        Enumeration<String> headerNames = request.getHeaderNames();
        while(headerNames.hasMoreElements()) {
            String headerName = headerNames.nextElement();
            System.out.println(headerName+" : "+headerName );
        }
        */

        // 간결한 문법
        request.getHeaderNames().asIterator()
                .forEachRemaining(headerName -> System.out.println(headerName + " : " + headerName));

        System.out.println(" test");
        request.getHeader("host"); // 하나만 꺼내 보고 싶을 때

        System.out.println("--- Headers - end ---");
        System.out.println();
    }

    //Header 편리한 조회
    private void printHeaderUtils(HttpServletRequest request) {
        System.out.println("--- Header 편의 조회 start ---");
        System.out.println("[Host 편의 조회]");
        System.out.println("request.getServerName() = " + request.getServerName()); //Host 헤더
        System.out.println("request.getServerPort() = " + request.getServerPort()); //Host 헤더
        System.out.println();


        System.out.println("[Accept-Language 편의 조회]");
        request.getLocales().asIterator()
                .forEachRemaining(locale -> System.out.println("locale = " + locale));
        System.out.println("request.getLocale() = " + request.getLocale());
        System.out.println();

        System.out.println("[cookie 편의 조회]");
        if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
        System.out.println();

        System.out.println("[Content 편의 조회]");
        System.out.println("request.getContentType() = " + request.getContentType());
        System.out.println("request.getContentLength() = " + request.getContentLength());
        System.out.println("request.getCharacterEncoding() = " + request.getCharacterEncoding());
        System.out.println("--- Header 편의 조회 end ---");
        System.out.println();
    }

    // 기타 정보
    private void printEtc(HttpServletRequest request) {
        System.out.println("--- 기타 조회 start ---");
        System.out.println("[Remote 정보]");
        System.out.println("request.getRemoteHost() = " + request.getRemoteHost()); //
        System.out.println("request.getRemoteAddr() = " + request.getRemoteAddr()); //
        System.out.println("request.getRemotePort() = " + request.getRemotePort()); //
        System.out.println();
        System.out.println("[Local 정보]");
        System.out.println("request.getLocalName() = " + request.getLocalName()); //
        System.out.println("request.getLocalAddr() = " + request.getLocalAddr()); //
        System.out.println("request.getLocalPort() = " + request.getLocalPort()); //
        System.out.println("--- 기타 조회 end ---");
        System.out.println();
    }
}
```

> **start-line 정보 결과**
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

> **헤더 정보 결과**
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

> **Header 편리한 조회 결과**
```
--- Header 편의 조회 start ---
[Host 편의 조회]
request.getServerName() = localhost
request.getServerPort() = 8080
>
[Accept-Language 편의 조회]
locale = ko_KR
locale = ko
locale = en_US
locale = en
request.getLocale() = ko_KR
>
[cookie 편의 조회]
Idea-7bc98fcd: d7c18bac-7365-428f-8caf-49ca1e4244dc
>
[Content 편의 조회]
request.getContentType() = null
request.getContentLength() = -1
request.getCharacterEncoding() = UTF-8
--- Header 편의 조회 end ---
```

> **기타 정보 결과**
(기타 정보는 HTTP 메시지의 정보는 아니다.)
```
--- 기타 조회 start ---
[Remote 정보]
request.getRemoteHost() = 0:0:0:0:0:0:0:1
request.getRemoteAddr() = 0:0:0:0:0:0:0:1
request.getRemotePort() = 49288
>
[Local 정보]
request.getLocalName() = localhost
request.getLocalAddr() = 0:0:0:0:0:0:0:1
request.getLocalPort() = 8080
--- 기타 조회 end ---
```

---

## 3. HttpServletResponse의 역할

HttpServletResponse는 HTTP 요청에 대한 응답 메시지를 생성하고, 기타 편의 기능을 제공한다.

HTTP 응답 메시지는 아래와 같이 크게 3가지 내용이다. 

1. **단순 텍스트 응답**
- ex) `writer.println("ok");`
2. **HTML 응답**
3. **HTTP API**
- ex) 메시지 바디에 JSON 데이터를 담아서 응답

HTTP 응답 메시지를 작성하는 방법은 아래와 같다. 


### 3-1. HttpServletResponse 기본 사용법과 편의 메서드 예시

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


        // [Header 편의 메서드]
        content(response);
        cookie(response);
        redirect(response);

        PrintWriter writer = response.getWriter();
        writer.println("ok~!");
    }

    // Content 편의 메서드
    private void content(HttpServletResponse response) {
        //Content-Type: text/plain;charset=utf-8
        //Content-Length: 2
        //response.setHeader("Content-Type", "text/plain;charset=utf-8");
        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");
        //response.setContentLength(8); //(생략시 자동 생성)
    }
    
    // 쿠키 편의 메서드
    private void cookie(HttpServletResponse response) {
        // Set-Cookie: myCookie=good; Max-Age=600;
        // response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
        Cookie cookie = new Cookie("myCookie", "good");
        cookie.setMaxAge(600); //600초
        response.addCookie(cookie);
    }

    // redirect 편의 메서드
    private void redirect(HttpServletResponse response) throws IOException {
        // Status Code 302
        // Location: /basic / hello - form.html
        // response.setStatus(HttpServletResponse.SC_FOUND); //302
        // response.setHeader("Location", "/basic/hello-form.html");
        response.sendRedirect("/basic/hello-form.html");
    }
}
```

### 3-2. HttpServletResponse HTML 응답

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
        writer.println("<div>안녕?</div>");
        writer.println("</body>");
        writer.println("</html>");
    }
}
```

HTTP 응답으로 HTML을 반환할 때는 content-type을 text/html 로 지정해야 한다. 


### 3-3. HttpServletResponse API JSON 응답

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
HTTP 응답으로 JSON을 반환할 때는 content-type을 application/json 로 지정해야 한다. Jackson 라이브러리가 제공하는 `objectMapper.writeValueAsString()` 를 사용하면 객체를 JSON 문자로 변경할 수 있다. 

> **참고**   
> application/json 은 스펙상 utf-8 형식을 사용하도록 정의되어 있다. 그래서 스펙에서 charset=utf-8 
과 같은 추가 파라미터를 지원하지 않는다. 따라서 content-type을 application/json;charset=utf-8로 작성할 필요가 없다. 단, response.getWriter()를 사용하면 추가 파라미터를 자동으로 추가한다. response.getOutputStream()을 사용하여 출력하면 된다.
