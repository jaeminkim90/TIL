> ### 📖 ✏️ 
> **서블릿에서 템플릿 엔진을 거쳐 MVC 패턴이 등장하기까지의 기술 발전 과정을 학습하면서, MVC 패턴이 어떤 문제를 해결하기 탄생했는지 이해해보고 내용을 정리한 포스팅입니다.**

---

# MVC 패턴이 등장한 이유 - 서블릿, JSP 방식의 한계


MVC 패턴은 서블릿과 템플릿 엔진을 이용한 개발 방식의 불편을 개선하기 위해 등장했다. 순수 자바 코드와 서블릿을 이용해 개발하던 방식에서 템플릿 엔진을 거쳐 MVC 패턴이 등장하기까지 어떤 불편과 해결 아이디어가 있었는지 살펴보자. 이는 스프링 프레임워크가 등장한 기술 발전의 배경을 이해하는데 도움이 된다.


> **MVC 패턴** 
![](https://images.velog.io/images/woply/post/9686420c-1565-445a-aca4-abc35dde2657/image.png)


## 1. HTML 페이지를 더 쉽게 만들어주는 템플릿 엔진의 등장

서블릿과 자바 코드만으로 HTML을 생성하는 방법은 불편하다. HTML 코드와 자바 코드가 혼재해 있고, 복잡하게 느껴진다. 동적인 페이지를 만드는 것도 불편하다. 서블릿을 이용해 HTML 페이지를 생성하기 위해서는 아래와 같이 코드를 작성해야 한다. 

```java
@WebServlet(name = "MemberFormServlet", urlPatterns = "/servlet/members/new-form")
public class MemberFormServlet extends HttpServlet {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        PrintWriter w = response.getWriter();
        w.write("<!DOCTYPE html>\n" +
                "<html>\n" +
                "<head>\n" +
                "    <meta charset=\"UTF-8\">\n" +

                "    <title>Title</title>\n" +
                "</head>\n" +
                "<body>\n" +
                "<form action=\"/servlet/members/save\" method=\"post\">\n" +
                "    username: <input type=\"text\" name=\"username\" />\n" +
                "    age:      <input type=\"text\" name=\"age\" />\n" +
                " <button type=\"submit\">전송</button>\n" + "</form>\n" +
                "</body>\n" +
                "</html>\n");
    }
}
```

템플릿 엔진은 **'HTML 문서에 동적으로 변경해야 하는 부분만 자바 코드를 사용하자'**는 아이디어에서 탄생했다. 템플릿 엔진에는 JSP, Thymeleaf, Freemarker, Velocity등이 있다. 

> **참고**   
JSP는 성능과 기능 경쟁력이 떨어지면서 현재는 시장에서 점차 사장되어 가는 추세다. 

## 2. 템플릿 엔진에 집중된 로직을 분리하기 위해 MVC 패턴 등장

템플릿 엔진은 자바 코드로 뷰(View)화면을 만들었던 HTML 작업을 깔끔하게 해결했다. 하지만, 템플릿 엔진도 동적인 화면을 위해 자바 코드를 사용해야 한다. 이는 템플릿 엔진 안에서 다양한 목적의 코드가 혼재하는 결과로 이어졌다. 결과적으로 템플릿 엔진이 너무 많은 역할을 하게 된다. 템플릿 엔진만으로는 프로젝트 규모가 커질수록 유지보수가 어려워지는 리스크를 해결할 수 없었다. 

**템플릿 엔진 예시 - 다양한 로직과 HTML 코드가 혼재한 JSP 파일**
```java
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="hello.servlet.domain.member.Member" %>
<%@ page import="hello.servlet.domain.member.MemberRepository" %>

<%
    // request, response는 바로 사용 가능
    MemberRepository memberRepository = MemberRepository.getInstance();

    System.out.println("MemberSaveServlet.service");
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));

    Member member = new Member(username, age);
    memberRepository.save(member);
%>
<html>
<head>
    <title>Title</title>
</head>
<body>
성공
<ul>
    <li>id=<%=member.getId()%></li>
    <li>username=<%=member.getUsername()%></li>
    <li>age=<%=member.getAge()%></li>
</ul>

<a href="/index.html">메인</a>
</body>
</html>
```

개발자들은 템플릿 엔진이 본래의 목적에 맞도록 **HTML로 화면(View)을 그리는 일**에만 집중하길 바랬다. MVC 패턴은 **템플릿 엔진에 모여있는 비즈니스 로직과 HTML 코드를 다시 분리하자**는 아이디어에서 탄생했다. 

구체적으로 어떤 문제가 있었는지 살펴보자.


### 2-1. MVC 패턴의 등장 - 너무 많은 역할

하나의 서블릿이나 JSP가 비즈니스 로직과 뷰 랜더링까지 처리하면서 너무 많은 역할을 담당하고 있었다. 이는 필연적으로 유지보수를 더 까다롭게 만들었다. 비즈니스 로직 변경이 일어나면, 관련된 JSP 코드를 모두 수정해야 한다. 또한, HTML 코드나 비즈니스 로직를 수정해야 할 때도 불필요한 코드를 함께 살펴봐야 했다.

### 2-2. MVC 패턴의 등장 - 변경의 라이프 사이클 

진짜 문제는 비즈니스 로직과 뷰 로직의 변경 사이클이 다르다는 점이었다. 예를 들어서 UI를 수정하는 일과 비즈니스 로직을 수정하는 일은 각각 다른 이유로 발생한다. 그리고 대부분의 경우 서로 영향을 미치지 않는다. 

이와 같이 변경의 라이프 사이클이 다른 코드를 하나의 코드로 관리하는 것은 유지보수 측면에서 좋지 않았다.


### 2-3. MVC 패턴의 등장 - 기능 특화에 따른 역할과 책임 분담 

JSP 같은 뷰 템플릿은 화면을 렌더링 하는데 최적화 되어있다. 따라서, 해당 부분의 역할과 책임을 부여하는 것이 좋다.


## 3. Model-View-Controller로 비즈니스 로직과 뷰 로직을 분리하다

MVC 패턴은 하나의 서블릿이나, JSP로 처리하던 것을 컨트롤러(Controller)와 뷰(View)라는 영역으로 분리하는 방법을 의미한다. 웹 애플리케이션은 대부분 MVC 패턴을 사용한다. 


> **컨트롤러**   
> HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다. 

> **모델**   
> 뷰에 출력할 데이터를 담아둔다. 뷰에서 필요한 데이터는 모두 모델이 전달하기 때문에 뷰는 비즈니스 로직이나 데이터 접근과 업무적인 분리가 가능하다. 오직 화면을 렌더링 하는 일에 집중할 수 있게 된다. 

> **뷰**
> 모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 주로 HTML을 생성한다.

> **참고**   
> 컨트롤러에 비즈니스 로직을 모두 담으면 컨트롤러의 역할이 비대해진다. 그래서 일반적으로 비즈니스 로직은 서비스(Service)라는 계층을 별도로 만들어서 처리한다. 컨트롤러는 비즈니스 로직이 있는 서비스 호출을 담당한다. 비즈니스 로직이 변경되면 비즈니스 로직을 호출하는 컨트롤러의 코드도 변경될 수 있다.





## 4. MVC 패턴의 한계를 해결하기 위해 등장한 프론트 컨트롤러(Front Controller)


MVC 패턴을 적용한 덕분에 컨트롤러의 역할과 뷰를 렌더링하는 역할을 명확하게 구분할 수 있다. 특히 뷰는 화면을 그리는 역할에 충실한 덕분에, 코드가 깔끔하고 직관적이다. 단순하게 모델에서 필요한 데이터를 꺼내고, 화면을 만들면 된다. 하지만, 여전히 컨트롤러는 중복 코드와 불필요한 코드가 많아 보인다.

> 1. **포워드 중복**  
> View로 이동하는 코드가 항상 중복 호출되어야 한다. 물론 이 부분을 메서드로 공통화해도 되지만, 해당 메서드 역시 직접 호출해야하는 문제가 있다. 
```java
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```



> 2. **ViewPath에 중복**   
> ViewPath를 설정할 때 반복되는 코드가 발생한다. 그리고 만약 jsp가 아닌 thymeleaf와 같이 다른 뷰로 변경한다면 전체 코드 수정이 필요하다. 
```java 
String viewPath = "/WEB-INF/views/new-form.jsp";
```
- prefix:`/WEB-INF/views/`
- suffix: `.jsp`  

> 3. **사용하지 않는 코드**   
> 아래와 같은 코드는 사용할 때도 있고, 사용하지 않을 때도 있다. 특히 response는 사용되지 않는 경우가 많다. 그리고 HttpServletRequest, HttpServletResponse를 사용하는 코드는 테스트 케이스를 작성하기도 어렵다.  
```java
  HttpServletRequest request, HttpServletResponse response
```

> 4. **공통 처리의 어려움**  
> 기능이 복잡해질수록 컨트롤러에서 공통으로 처리해야 하는 부분이 점점 더 많이 증가한다. 단순히 공통 기능을 메서드로 분리하면 될 거 같지만, 결과적으로 해당 메서드를 항상 호출해야 한다. 호출하는 코드 자체가 중복된다.


이 문제를 해결하기 위해 프론트 컨트롤러(Front Controller) 패턴이 도입되었다. 프론트 컨트롤러(Front Controller)는 컨트롤러 호출 전에 공통으로 필요한 기능을 처리한다. 입구를 하나로 하여, 공통 기능을 배치하는 방식으로 불필요한 중복 문제를 해결한다. 스프링 MVC의 핵심도 프론트 컨트롤러(Front Controller)에 있다.

