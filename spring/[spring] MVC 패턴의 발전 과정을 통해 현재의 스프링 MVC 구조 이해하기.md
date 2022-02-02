MVC 패턴이 발전해 온 과정을 통해 현재의 스프링 MVC 구조를 이해해보자.


> ### 📖 ✏️ 
> **가장 기본적인 MVC 패턴에서 점진적으로 개선되어 가는 과정을 살펴보며, 현재의 스프링 MVC 구조를 이해할 수 있었다. 이 글은 학습한 내용을 정리한 포스팅이다.**

---

# MVC 패턴의 발전 과정을 통해 현재의 스프링 MVC 구조 이해하기

현재의 스프링 MVC 구조는 프런트 컨트롤러 역할을 하는 `Dispatcher Servlet`을 중심으로 핸들러 맵핑과 핸들러 어댑터를 이용해 필요한 컨트롤러를 찾고, 뷰 리졸버를 이용해 뷰를 렌더링 하는 구조로 되어 있다.

> **스프링 MVC의 구조**
> ![](https://images.velog.io/images/woply/post/4a2d1232-3f4d-43aa-be91-0b934bff6650/image.png)

현재의 스프링 MVC 구조는 단순한 형태의 MVC 구조에서 수년간 발전을 거듭해 온 결과물이다. 최초의 MVC 구조에서 어떤 문제를 해결하기 위해 개선을 거듭해 왔는지 살펴보자. MVC 구조의  발전 과정은 현재의 스프링 MVC 구조를 더 깊이 있게 이해할 수 있는 배경지식이 될 것이다.

HTML Form을 이용하여 회원을 저장하고, 목록을 조회하는 기능을 가진 간단한 웹 애플리케이션을 이용해 프런트 컨트롤러를 이용한 MVC 패턴을 점진적으로 리팩토링하는 방식으로 스프링 MVC 구조를 만들어 보자.  

---

## 프런트 컨트롤러 패턴의 등장

프런트 컨트롤러는 클라이언트 요청이 들어오면 가장 먼저 거치는 곳이다. 요청을 파악하고, 처리가 가능한 클라이언트로 연결한다. 마치 지휘통제실과 같다.

프런트 컨트롤러가 없을 때는 클라이언트 호출에 따라 해당 컨트롤러가 직접 연결되었다. 그러다보니, 공통 처리가 가능한 로직이 중복되었고, 일부 컨트롤러는 필요하지 않은 서블릿을 반드시 가지고 있어야 했다.

프런트 컨트롤러의 등장으로 각각의 컨트롤러는 공통 처리가 가능한 로직을 프런트 컨트롤러에 맡길 수 있게 되었고, 자신에게 꼭 필요한 기능만 가질 수 있게 되면서 구조가 더욱 효율적으로 개선되었다.


> **프론트 컨트롤러 도입 전** 
> ![](https://images.velog.io/images/woply/post/25528513-4a90-419a-9b7a-9645d2b0c128/image.png)

> **프론트 컨트롤러 도입 후** 
> ![](https://images.velog.io/images/woply/post/9786cb30-7ce0-49f0-8d4d-0396310f915a/image.png)



**`FrontController`  패턴 특징** 
- 프론트 컨트롤러 서블릿 하나로 클라이언트의 요청을 받음 
- 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
- 입구를 하나로! 
- 공통 처리 가능 
- 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿을 사용하지 않아도 됨 


**스프링 웹 MVC와 프론트 컨트롤러** 

FrontController는 스프링 웹 MVC의 핵심이다. 스프링 웹 MVC의 `DispatcherServlet`이 `FrontController`패턴으로 구현되어 있다. 



 ---
 

## 기본적인 프론트 컨트롤러의 구조 - version 1
가장 기본적인 형태의 프론트 컨트롤러 구조에서 현재의 스프랑 MVC 패턴까지 단계적으로 발전 과정을 적용해보자. 시작 단계인 version 1은 프런트 컨트롤러가 클라이언트 요청을 처리해 줄 컨트롤러를 찾아서 JSP를 랜더하는 역할만 담당한다.

> version 1 구조
> ![](https://images.velog.io/images/woply/post/1fa0599b-eb1f-42fa-946f-b9db77301e4c/image.png)


**ControllerV1**

```java
package hello.servlet.web.frontcontroller.v1;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public interface ControllerV1 {

    void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```

서블릿과 비슷한 모양의 컨트롤러 인터페이스를 도입한다. 각 컨트롤러들은 이 인터페이스를 구현하면 된다. 프론트 컨트롤러는 이 인터페이스를 호출해서 구현과 관계없이 로직의 일관성을 가져갈 수 있다. 이 인터페이스를 구현한 컨트롤러를 만들어보자.


**MemberFormControllerV1 - 회원 등록 컨트롤러** 
```java
public class MemberFormControllerV1 implements ControllerV1 {

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);// Controller에서 view로 이동할 때 사용
        dispatcher.forward(request, response); // 이동 명령
    }
}
```

**MemberSaveControllerV1 - 회원 저장 컨트롤러** 
```java
public class MemberSaveControllerV1 implements ControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        // Model에 데이터를 보관한다.
        request.setAttribute("member", member);

        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

**MemberListControllerV1 - 회원 목록 컨트롤러** 
```java
public class MemberListControllerV1 implements ControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        List<Member> members = memberRepository.findAll();

        request.setAttribute("members", members);

        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

내부 로직은 기존 서블릿과 거의 같다. 이제 프론트 컨트롤러를 만들어보자. 


**FrontControllerServletV1 - 프론트 컨트롤러** 

```java
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {

    private Map<String, ControllerV1> controllerMap = new HashMap<>();

    public FrontControllerServletV1() {
        controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServletV1.service");

        String requestURI = request.getRequestURI();

        ControllerV1 controller = controllerMap.get(requestURI);
        System.out.println("requestURI = " + requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        controller.process(request, response);
    }
}
```

**프런트 컨트롤러 - Version 1 분석**

1. **urlPatterns**

- `urlPatterns = "/front-controller/v1/*"`
  - `/front-controller/v1`을 포함한 하위 모든 요청은 이 서블릿이 받는다.
- 예) `/front-controller/v1`, `/front-controller/v1/a`  , `/front-controller/v1/a/b` 등


2. **controllerMap**

- 연결할 컨트롤러를 모아두는 Map이다.
- key: 매핑 URL 
- value: 호출될 컨트롤러 

3. **service()** 

먼저 `requestURI`를 조회해서 실제 호출할 컨트롤러를 `controllerMap`에서 찾는다. 만약 없다면 `404(SC_NOT_FOUND)` 상태 코드를 반환한다. 
컨트롤러를 찾고 `controller.process(request, response);`을 호출해서 해당 컨트롤러를 실행한다. 

4. **JSP** 
- JSP는 이전 MVC에서 사용했던 것을 그대로 사용한다. 


---


## View의 분리 - version 2 


version 1은 모든 컨트롤러에서 뷰로 이동하는 부분에 중복이 있다. 더 깔끔하게 역할을 분리하기 위해 별도로 뷰를 처리하는 객체가 등장했다.

**dispatcher.forward()의 중복이 많았던 version 1 방식**
```java
String viewPath = "/WEB-INF/views/new-form.jsp";
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```


> **V2의 구조** 
> ![](https://images.velog.io/images/woply/post/82fd4244-00af-4861-b803-7afbfc67ef0d/image.png)

 
뷰 객체 `MyView`는 이후 다른 버전에서도 함께 사용하므로, 패키지 위치를 `frontcontroller`에 두었다. 

```java
public class MyView {
    private String viewPath;

    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }

    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

Version 2의 컨트롤러는 뷰를 반환하는 특징이 있다. 인터페이스를 살펴보자.

**ControllerV2** 

```java
public interface ControllerV2 {

    MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```


**MemberFormControllerV2 - 회원 등록 폼** 

```java
public class MemberFormControllerV2 implements ControllerV2 {
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
}
```
별도의 뷰 객체를 반환하는 Version 2의 각 컨트롤러는 이제 복잡한 `dispatcher.forward()`를 직접 생성해서 호출하지 않아도 된다. 단순히 `MyView` 객체를 생성하고 거기에 뷰 이름만 넣고 반환하면 된다.

`ControllerV1`을 구현한 클래스와 `ControllerV2`를 구현한 클래스를 비교해보면, 이 부분의 중복이 확실하게 제거된 것을 확인할 수 있다. 

**MemberSaveControllerV2 - 회원 저장** 
```java
public class MemberSaveControllerV2 implements ControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        // Model에 데이터를 보관한다.
        request.setAttribute("member", member);

        return new MyView("/WEB-INF/views/save-result.jsp");
    }
}
```


**MemberListControllerV2 - 회원 목록** 
```java
public class MemberListControllerV2 implements ControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        List<Member> members = memberRepository.findAll();
        request.setAttribute("members", members);
        return new MyView("/WEB-INF/views/members.jsp");
    }
}
```


**프론트 컨트롤러 V2** 
```java
@WebServlet(name = "frontControllerServletV2", urlPatterns = "/front-controller/v2/*")
public class FrontControllerServletV2 extends HttpServlet {

    private Map<String, ControllerV2> controllerMap = new HashMap<>();

    public FrontControllerServletV2() {
        controllerMap.put("/front-controller/v2/members/new-form", new MemberFormControllerV2());
        controllerMap.put("/front-controller/v2/members/save", new MemberSaveControllerV2());
        controllerMap.put("/front-controller/v2/members", new MemberListControllerV2());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String requestURI = request.getRequestURI();

        ControllerV2 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        MyView view = controller.process(request, response);
        view.render(request, response);
    }
}
```

ControllerV2의 반환 타입이 MyView 이므로 프론트 컨트롤러는 컨트롤러의 호출 결과로 MyView 를 반환 받는다. 그리고 `view.render()`를 호출하면 `forward`로직을 수행해서 JSP가 실행된다. 


**MyView.render()**
```java
public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
}
```


프론트 컨트롤러의 도입으로 `MyView` 객체의 `render()`를 호출하는 부분을 모두 일관되게 처리할 수 있다. 각각의 컨트롤러는 `MyView` 객체를 생성만 해서 반환하면 된다. 


---




## Model의 등장 - version 3 



version2는 여전히 서블릿에 종속적이다. 컨트롤러 입장에서 `HttpServletRequest`, `HttpServletResponse`은 반드시 필요한 객체가 아니다. 사실, Request 객체 그 자체보다, 파라미터 데이터가 필요하다. 파라미터 데이터를 사용하기 위해 Request 객체를 쓰기 때문이다. 

version 2에서는 `Request.setAttribute`를 이용해서 파라미터 정보를 뷰로 전달했다. 이때  컨트롤러는 어쩔 수 없이 `Request`	객체를 제공하는 서블릿 기술이 필요했다.

하지만, 요청 파라미터 정보를 자바의 `Map`으로 대신 넘기도록 하면, 컨트롤러가 서블릿 기술에 종속되지 않고도 역할을 수행할 수 있다.

방법은 간단하다. request 객체 대신 별도의 Model 객체를 만들어서 파라미터 정보를 담고 컨트롤러에 전달한다.

컨트롤러가 서블릿 기술을 전혀 사용하지 않도록 변경해보자. 


### 뷰 이름의 중복을 제거한다 

컨트롤러에서 지정하는 뷰 이름을 보면, 중복이 존재한다. 컨트롤러는 뷰의 논리 이름을 반환하고, 실제 물리 위치의 이름은 프론트 컨트롤러에서 처리하도록 변경해보자. 이와 같은 구조는 향후 뷰의 폴더 위치가 변경되어도, 프론트 컨트롤러만 수정하면 된다.
이런게 바로 좋은 설계라 할 수 있다.

- `/WEB-INF/views/new-form.jsp` -> `new-form`
- `/WEB-INF/views/save-result.jsp`  -> `save-result` 
- `/WEB-INF/views/members.jsp`  -> `members` 



![](https://images.velog.io/images/woply/post/6899d812-807c-4c87-b0f3-9303c95ab74b/image.png)

**ModelView** 

Controller가 반환하는 ModelView는 데이터와 뷰 이름(논리 이름)를 모두 가지고 있다. 실제 뷰 경로(물리 이름)는 프런트 컨트롤러가 뷰 리졸버를 통해 얻는다.

지금까지 컨트롤러에서 서블릿에 종속적인 `HttpServletRequest`를 사용했다. 그리고 Model도 `request.setAttribute()`를 통해 데이터를 저장하고 뷰에 전달했다. 

서블릿의 종속성을 제거하기 위해 Model을 직접 만들고, 추가로 View 이름까지 전달하는 객체를 만들어보자. ModelView는 Request 객체의 역할을 대신한다.

참고로 ModelView 객체는 다른 버전에서도 사용하므로 패키지를 frontcontroller 에 둔다. 


**ModelView** 

```java
package hello.servlet.web.frontcontroller;

import java.util.HashMap;
import java.util.Map;

public class ModelView {
    private String viewName;
    // 핵심 데이터인 member 객체를 담을 목적으로 model을 만든다.
    private Map<String, Object> model = new HashMap<>(); 

    public ModelView(String viewName) {
        this.viewName = viewName;
    }

    public String getViewName() {
        return viewName;
    }

    public void setViewName(String viewName) {
        this.viewName = viewName;
    }

    public Map<String, Object> getModel() {
        return model;
    }

    public void setModel(Map<String, Object> model) {
        this.model = model;
    }
}

```

뷰의 이름과 뷰를 렌더링할 때 필요한 model 객체를 가지고 있다. model은 단순히 map으로 되어 있으므로 컨트롤러에서 뷰에 필요한 데이터를 key, value로 넣어주면 된다.


**ControllerV3** 
```java
package hello.servlet.web.frontcontroller.v3;

import hello.servlet.web.frontcontroller.ModelView;

import java.util.Map;

public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}

```

ControllerV3으로 만들어진 컨트롤러는 서블릿 기술을 전혀 사용하지 않는다. 따라서 구현이 매우 단순하다. 테스트 코드의 작성과 테스트 실행도 쉽다. `HttpServletRequest`가 제공하는 파라미터는 프론트 컨트롤러가 `paramMap`에 담아서 호출한다. 응답 결과로 뷰 이름과 뷰에 전달할 Model 데이터를 포함하는 `ModelView` 객체를 반환한다.



**MemberFormControllerV3 - 회원 등록 폼** 


```java
package hello.servlet.web.frontcontroller.v3.controller;

import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.v3.ControllerV3;

import java.util.Map;

public class MemberFormControllerV3 implements ControllerV3 {

    @Override
    public ModelView process(Map<String, String> paramMap) {
        return new ModelView("new-form"); // 논리적인 이름만 넣는다
    }
}

```


`ModelView`를 생성할 때 `new-form`이라는 view의 논리적인 이름을 지정한다. 실제 물리적인 이름은 프론트 컨트롤러에서 처리한다. 



**MemberSaveControllerV3 - 회원 저장** 

```java
package hello.servlet.web.frontcontroller.v3.controller;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.v3.ControllerV3;

import java.util.Map;

public class MemberSaveControllerV3 implements ControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public ModelView process(Map<String, String> paramMap) {

        //이미 프런트 컨트롤러에서 Map에 요청 파라미터 정보를 모두 담아놨다. paramMap에서 꺼내서 바로 쓸 수 있다.
        String username = paramMap.get("username");
        int age = Integer.parseInt(paramMap.get("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelView mv = new ModelView("save-result");
        mv.getModel().put("member", member); // ModelView 객체dml model 필드에 member를 넣는다
        return mv;
    }
}

```

**`paramMap.get("username");`**
파라미터 정보는 map에 담겨있다. map에서 필요한 요청 파라미터를 조회한다.

**`mv.getModel().put("member", member);`** 
모델은 단순한 map이므로 모델에 뷰에서 필요한 member 객체를 담고 반환한다. 


**MemberListControllerV3 - 회원 목록** 

```java
package hello.servlet.web.frontcontroller.v3.controller;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.v3.ControllerV3;

import java.util.List;
import java.util.Map;

public class MemberListControllerV3 implements ControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);

        return mv;
    }
}

```


**FrontControllerServletV3** 


```java
package hello.servlet.web.frontcontroller.v3;

import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.MyView;
import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;


import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@WebServlet(name = "frontControllerServletV3", urlPatterns = "/front-controller/v3/*")
public class FrontControllerServletV3 extends HttpServlet {

    private Map<String, ControllerV3> controllerMap = new HashMap<>();

    public FrontControllerServletV3() {
        controllerMap.put("/front-controller/v3/members/new-form", new MemberFormControllerV3());
        controllerMap.put("/front-controller/v3/members/save", new MemberSaveControllerV3());
        controllerMap.put("/front-controller/v3/members", new MemberListControllerV3());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String requestURI = request.getRequestURI();

        // controllerMap에서 요청 URI에 해당하는 컨트롤러 객체를 반환
        ControllerV3 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // paramMap = 모든 Parameter 데이터를 담은 Map
        Map<String, String> paramMap = creatParamMap(request);
        System.out.println("paramMap.toString() = " + paramMap.toString());

        // 논리이름과 member 객체를 가진 ModelView 객체 반환
        ModelView mv = controller.process(paramMap);

        // ModelView의 필드인 viewName의 값이자 논리 이름을 뽑는다 => new-form / save-result / members
        String viewName = mv.getViewName(); 

        // viewPath가 들어간 MyView 객체 생성
        MyView view = viewResolver(viewName);
        System.out.println("mv.getModel() = " + mv.getModel());
        
        // mv.getModel()를 이용해 member 객체를 함께 넘긴다.
        view.render(mv.getModel(), request, response);
    }

    // MyView 객체 생성 후 반환(논리 경로를 물리 경로로 변경하여 생성자 파라미터에 이용)
    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private Map<String, String> creatParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}

```

**`createParamMap()`**
`HttpServletRequest`에서 파라미터 정보를 꺼내서 Map으로 변환한다. 그리고 해당 `Map(paramMap)`을 컨트롤러에 전달하면서 호출한다. 


**뷰 리졸버**

**`MyView view = viewResolver(viewName)`**
컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경한다. 그리고 실제 물리 경로(viewPath)가 있는 MyView 객체를 반환한다. 
- 논리 뷰 이름: members
- 물리 뷰 경로: `/WEB-INF/views/members.jsp`

`view.render(mv.getModel(), request, response)`
- 뷰 객체를 통해서 HTML 화면을 렌더링 한다. 
- 뷰 객체의 `render()` 는 모델 정보도 함께 받는다. 
- JSP는 `request.getAttribute()`로 데이터를 조회하기 때문에, 모델의 데이터를 꺼내서 `request.setAttribute()`로 담아둔다. 
- JSP로 포워드 해서 JSP를 렌더링 한다.  


**MyView**

```java
package hello.servlet.web.frontcontroller;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Map;

// 화면 이동을 담당
public class MyView {
    private String viewPath;

    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }

    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
    
    public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        modelToRequestAttribute(model, request);
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }

    // JSP가 request.getAttribute()로 데이터를 조회하기 때문에 모델에서 데이터를 꺼내 request.setAttribute 작업을 해야한다.
    private void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
        model.forEach((key, value) -> request.setAttribute(key, value));
    }
}

```

---

## 더 단순하고 실용적인 컨트롤러의 등장 - version 4


앞서 만든 v3 컨트롤러는 서블릿 종속성을 제거하고 뷰 경로의 중복을 제거하는 등, 잘 설계된 컨트롤러다. 하지만 항상 `ModelView` 객체를 생성하고 반환해야 하는 부분은 조금은 번거로워 보인다. 좋은 프레임워크는 아키텍처도 중요하지만, 그와 더불어 실제 개발하는 개발자가 단순하고 편리하게 사용할 수 있어야 한다. 실용성이 중요하다. v3를 조금 변경해서 매우 편리하게 개발할 수 있는 version 4를 개발해보자. 
![](https://images.velog.io/images/woply/post/495e85ae-e9a6-4885-afae-d481cde43d05/image.png)


기본적인 구조는 V3와 같다. 대신에 컨트롤러가 `ModelView` 를 반환하지 않고, `ViewName`만 반환한다. 


**ControllerV4** 

```java
package hello.servlet.web.frontcontroller.v4;

import java.util.Map;

public interface ControllerV4 {

    /**
     *
     * @param paramMap
     * @param model
     * @return viewName
     */
    String process(Map<String, String> paramMap, Map<String, Object> model);
}

```

version 4는 인터페이스에 `ModelView`가 없다. model 객체는 파라미터로 전달하여 바로 사용한다. 컨트롤러는 뷰의 이름만 반환한다.


**MemberFormControllerV4 - 회원 등록 폼** 

```java
package hello.servlet.web.frontcontroller.v4.controller;

import hello.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.Map;

public class MemberFormControllerV4 implements ControllerV4 {
    
    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        return "new-form";
    }
}
```


단순하게 `new-form`이라는 뷰의 논리 이름만 반환한다.


**MemberSaveControllerV4 - 회원 저장** 


```java
package hello.servlet.web.frontcontroller.v4.controller;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import hello.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.Map;

public class MemberSaveControllerV4 implements ControllerV4 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        String username = paramMap.get("username");
        int age = Integer.parseInt(paramMap.get("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        model.put("member", member);
        return "save-result";
    }
}
```

`model.put("member", member)`
모델이 파라미터로 전달되기 때문에, 모델을 직접 생성하지 않아도 된다. 


**MemberListControllerV4 - 회원 목록** 


```java
package hello.servlet.web.frontcontroller.v4.controller;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import hello.servlet.web.frontcontroller.v4.ControllerV4;

import java.util.List;
import java.util.Map;

public class MemberListControllerV4 implements ControllerV4 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        List<Member> members = memberRepository.findAll();

        model.put("members", members);
        return "members";
    }
}

```

**FrontControllerServletV4** 

```java
package hello.servlet.web.frontcontroller.v4;

import hello.servlet.web.frontcontroller.MyView;
import hello.servlet.web.frontcontroller.v4.controller.MemberFormControllerV4;
import hello.servlet.web.frontcontroller.v4.controller.MemberListControllerV4;
import hello.servlet.web.frontcontroller.v4.controller.MemberSaveControllerV4;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@WebServlet(name = "frontControllerServletV4", urlPatterns = "/front-controller/v4/*")
public class FrontControllerServletV4 extends HttpServlet {

    private Map<String, ControllerV4> controllerMap = new HashMap<>();

    public FrontControllerServletV4() {
        controllerMap.put("/front-controller/v4/members/new-form", new MemberFormControllerV4());
        controllerMap.put("/front-controller/v4/members/save", new MemberSaveControllerV4());
        controllerMap.put("/front-controller/v4/members", new MemberListControllerV4());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        String requestURI = request.getRequestURI();

        // controllerMap에서 요청 URI에 해당하는 컨트롤러 객체를 반환
        ControllerV4 controller = controllerMap.get(requestURI);
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }
        
        // paramMap = 모든 Parameter 데이터를 담은 Map
        Map<String, String> paramMap = creatParamMap(request);
        Map<String, Object> model = new HashMap<>(); // 추가

        String viewName = controller.process(paramMap, model);

        MyView view = viewResolver(viewName);

        view.render(model, request, response);
    }

    // MyView 객체 생성 후 반환(논리 경로를 물리 경로로 변경하여 생성자 파라미터에 이용)
    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }

    private Map<String, String> creatParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}

```

`FrontControllerServletV4`는 이전 버전과 거의 동일하다.



**모델 객체 전달** 
`Map<String, Object> model = new HashMap<>(); //추가`
모델 객체를 프론트 컨트롤러에서 생성해서 넘겨준다. 참조값을 넘겨주므로, 컨트롤러에서 모델 객체에 값을 담으면 그대로 담긴다.


**뷰의 논리 이름을 직접 반환** 
컨트롤러가 직접 뷰의 논리 이름을 반환한다. 이 값을 사용해서 실제 물리 뷰를 찾을 수 있다. 

version 4 컨트롤러는 매우 단순하고 실용적인 구조를 가지고 있다. version 3에서 모델을 파라미터로 넘기고, 뷰의 논리 이름을 반환한다는 아이디어가 적용되었다. 

---

## 유연한 컨트롤러 사용을 돕는 어댑터의 등장 - version 5 


만약 ControllerV3 방식과 ControllerV4 방식을 동시에 사용해야 한다면 어떻게 해야할까? 여기서 어댑터라는 개념이 등장하게 된다.

```java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```

```java
public interface ControllerV4 {
    String process(Map<String, String> paramMap, Map<String, Object> model);
} 
```


**어댑터 패턴** 

지금까지의 버전은 한 가지 프론트 컨트롤러 인터페이스만 사용할 수 있었다. `ControllerV3` , `ControllerV4`는 완전히 다른 인터페이스이다. 따라서 호환이 불가능하다. 이때, 어댑터가 필요하다. 어댑터 패턴을 사용해서 프론트 컨트롤러가 다양한 방식의 컨트롤러를 처리할 수 있도록 변경해보자. 



**Version 5 구조** 
![](https://images.velog.io/images/woply/post/b29f71c6-eaef-4114-bf30-28967f8836ae/image.png)


**핸들러 어댑터** 
중간에 어댑터 역할을 하는 **핸들러 어댑터**가 추가되었다. **핸들러 어댑터**가 어댑터 역할을 해주는 덕분에 다양한 종류의 컨트롤러를 호출할 수 있다. 

**핸들러**
컨트롤러의 이름이 더 넓은 범위를 의미하는 핸들러로 변경되었다. 필요한 어댑터만 있다면 컨트롤러 뿐만 아니라 어떠한 것이든 처리가 가능하기 때문이다.


**MyHandlerAdapter 인터페이스**

```java
package hello.servlet.web.frontcontroller.v5;

import hello.servlet.web.frontcontroller.ModelView;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public interface MyHandlerAdapter {

    boolean supports(Object handler);

    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}

```


**`boolean supports(Object handler)`**
- handler는 컨트롤러를 말한다. 
- 어댑터가 해당 컨트롤러를 처리할 수 있는지 판단하는 메서드다. 
	  
**`ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler)`**
- 어댑터는 실제 컨트롤러를 호출하고, 그 결과로 `ModelView`를 반환한다.
- 실제 컨트롤러가 `ModelView`를 반환하지 못하면, 어댑터가 `ModelView`를 직접 생성해서 반환한다. 
- 이전에는 프론트 컨트롤러가 실제 컨트롤러를 호출했지만 이제는 어댑터가 실제 컨트롤러를 호출한다.(handle()안에서 해당 컨트롤러의 `process()`가 호출된다.)
	  

실제 어댑터를 구현해보자. 먼저 ControllerV3를 지원하는 어댑터를 구현한다.


**ControllerV3HandlerAdapter** 

```java
package hello.servlet.web.frontcontroller.v5.adapter;

import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.v3.ControllerV3;
import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ControllerV3HandlerAdapter implements MyHandlerAdapter {
    @Override
    public boolean supports(Object handler) {

        // MemberFormControllerV3가 ControllerV3의 인스턴스인지 확인
        return (handler instanceof ControllerV3);
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
        // MemberFormControllerV3를 ControllerV3로 캐스팅
        ControllerV3 controller = (ControllerV3) handler;

        // 요청 파라미터 내용을 Map에 담는다
        Map<String, String> paramMap = creatParamMap(request);

        // 컨트롤러의 process()를 이용하여 논리 이름이 담긴 ModelView를 얻는다
        // MemberFormControllerV3.process가 호출된다
        ModelView mv = controller.process(paramMap);

        return mv;

    }

    private Map<String, String> creatParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}

```

하나씩 분석해보자. 

`ControllerV3`을 처리할 수 있는 어댑터인지 확인한다.

```java
@Override
public boolean supports(Object handler) {

    // MemberFormControllerV3가 ControllerV3의 인스턴스인지 확인
    return (handler instanceof ControllerV3);
}

```

`handler`를 컨트롤러 V3로 변환한 다음에 V3 형식(파라미터와 리턴 타입)에 맞도록 호출한다. `ControllerV3`는 `ModelView`를 반환하므로 그대로 `ModelView`를 반환하면 된다. `supports()`를 통해 `ControllerV3`만 지원하기 때문에 타입 변환(캐스팅) 에러를 걱정할 필요 없이 실행할 수 있다.

```java
@Override
public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
    // MemberFormControllerV3를 ControllerV3로 캐스팅
    ControllerV3 controller = (ControllerV3) handler;

    // 요청 파라미터 내용을 Map에 담는다
    Map<String, String> paramMap = creatParamMap(request);

    // 컨트롤러의 process()를 이용하여 논리 이름이 담긴 ModelView를 얻는다
    // MemberFormControllerV3.process가 호출된다
    ModelView mv = controller.process(paramMap);

    return mv;
}

```


**FrontControllerServletV5 **
```java
package hello.servlet.web.frontcontroller.v5;

import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.MyView;
import hello.servlet.web.frontcontroller.v3.controller.MemberFormControllerV3;
import hello.servlet.web.frontcontroller.v3.controller.MemberListControllerV3;
import hello.servlet.web.frontcontroller.v3.controller.MemberSaveControllerV3;
import hello.servlet.web.frontcontroller.v5.adapter.ControllerV3HandlerAdapter;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

@WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
public class FrontControllerServletV5 extends HttpServlet {

    private final Map<String, Object> handlerMappingMap = new HashMap<>();
    private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();

    public FrontControllerServletV5() {
        initHandlerMappingMap(); // 전체 핸들러(컨트롤러) Mapping 정보를 필드 변수(Map)에 담는다.
        initHandlerAdapters(); // 전용 어댑터 객체를 필드 변수(List)에 담는다.
    }

    private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAdapter());
    }

    private void initHandlerMappingMap() {
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        //Handler Mapping Map에서 핸들러(컨트롤러)를 찾는다. => MemberFormControllerV3가 반환된다
        Object handler = getHandler(request);
        if (handler == null) {
            // 없을 경우 404 처리
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // 같은 컨트롤러 인터페이스를 사용하는 핸들러를 찾는다.
        // 여기서는 ControllerV3HandlerAdapter가 해당
        MyHandlerAdapter adapter = getHandlerAdapter(handler);

        // 논리 이름이 담긴 ModelView 반환
        ModelView mv = adapter.handle(request, response, handler);

        // 논리 이름을 이용하여 viewResolver로 전체 viewPath를 가지고 있는 MyView 반환
        String viewName = mv.getViewName();
        MyView view = viewResolver(viewName);

        // render()를 통해 dispatcher.forward() 실행
        view.render(mv.getModel(), request, response);

    }

    private MyHandlerAdapter getHandlerAdapter(Object handler) {

        // MemberFormControllerV3와 인스턴스가 동일한 어댑터를 찾는다.
        for (MyHandlerAdapter adapter : handlerAdapters) {
            // handlerAdapters를 모아놓은 List에서 같은 컨트롤러 인터페이스를 사용하는 어댑터 탐색
            if (adapter.supports(handler)) {
                return adapter;
            }
        }
        // 없을 경우 예외를 반환한다.
        throw new IllegalArgumentException("handler adapter를 찾을 수 없습니다. handler = " + handler);
    }

    private Object getHandler(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
        return handlerMappingMap.get(requestURI);
    }

    // MyView 객체 생성 후 반환(논리 경로를 물리 경로로 변경하여 생성자 파라미터에 이용)
    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }
}

```

**컨트롤러(Controller) -> 핸들러(Handler)로 이름 변경 **

이름을 컨트롤러에서 더 넒은 범위의 핸들러로 변경했다. 이전에는 컨트롤러를 직접 매핑해서 사용했지만, 이제는 (컨트롤러 뿐만 아니라)어댑터만 있으면 어떤 것이라도 URL에 매핑하여 사용할 수 있다.


**생성자** 
```java
public FrontControllerServletV5() {
    initHandlerMappingMap(); // 전체 핸들러(컨트롤러) Mapping 정보를 필드 변수(Map)에 담는다.
    initHandlerAdapters(); // 전용 어댑터 객체를 필드 변수(List)에 담는다.
}
```

생성자는 핸들러 매핑과 어댑터를 초기화(등록)한다. 


**매핑 정보** 
```java
private final Map<String, Object> handlerMappingMap = new HashMap<>();
```
매핑 정보의 값이 ControllerV3 , ControllerV4 같은 인터페이스에서 아무 값이나 받을 수 있는 Object 로 변경되었다. 


**핸들러 매핑** 

**`Object handler = getHandler(request)`**
```java
private Object getHandler(HttpServletRequest request) {
    String requestURI = request.getRequestURI();
    return handlerMappingMap.get(requestURI);
}
```
핸들러 매핑 정보인 `handlerMappingMap`에서 URL에 매핑된 핸들러(컨트롤러) 객체를 찾아서 반환한다. 


**핸들러를 처리할 수 있는 어댑터 조회**
**`MyHandlerAdapter adapter = getHandlerAdapter(handler)`**

```java
private MyHandlerAdapter getHandlerAdapter(Object handler) {

    // MemberFormControllerV3와 인스턴스가 동일한 어댑터를 찾는다.
    for (MyHandlerAdapter adapter : handlerAdapters) {
        
        // handlerAdapters를 모아놓은 List에서 같은 컨트롤러 인터페이스를 사용하는 어댑터 탐색
        if (adapter.supports(handler)) {
            return adapter;
        }
    }
    // 없을 경우 예외를 반환한다.
    throw new IllegalArgumentException("handler adapter를 찾을 수 없습니다. handler = " + handler);
}
```

handler를 처리할 수 있는 어댑터를 `adapter.supports(handler)`를 통해서 찾는다. handler가 `ControllerV3` 인터페이스를 구현했다면, `ControllerV3HandlerAdapter` 객체가 반환된다. 



**어댑터 호출 **

```java
// 논리 이름이 담긴 ModelView 반환
ModelView mv = adapter.handle(request, response, handler); 
```

어댑터의 `handle(request, response, handler)` 메서드를 통해 실제 어댑터가 호출된다. 어댑터는 `handler(컨트롤러)`를 호출하고 그 결과를 어댑터에 맞추어 반환한다. `ControllerV3HandlerAdapter`의 경우 어댑터의 모양과 컨트롤러의 모양이 유사해서 변환 로직이 단순하다. 


이어서 ControllerV4 를 사용할 수 있도록 기능을 추가해보자. 


![](https://images.velog.io/images/woply/post/b29f71c6-eaef-4114-bf30-28967f8836ae/image.png)


**FrontControllerServletV5에 ControllerV4 기능도 추가한다. **

```java
private void initHandlerAdapters() {
    handlerAdapters.add(new ControllerV3HandlerAdapter());

    // V4 핸들러 어댑터 추가ㅣ
    handlerAdapters.add(new ControllerV4HandlerAdapter());
}

private void initHandlerMappingMap() {
    handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberSaveControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());

    // V4 핸들러 추가
    handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberSaveControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
}

```

핸들러 매핑(handlerMappingMap)에 ControllerV4를 사용하는 컨트롤러를 추가하고, 해당 컨트롤러를 처리할 수 있는 어댑터인 `ControllerV4HandlerAdapter`도 추가한다. 



**ControllerV4HandlerAdapter** 

```java
package hello.servlet.web.frontcontroller.v5.adapter;

import hello.servlet.web.frontcontroller.ModelView;
import hello.servlet.web.frontcontroller.v4.ControllerV4;
import hello.servlet.web.frontcontroller.v5.MyHandlerAdapter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

public class ControllerV4HandlerAdapter implements MyHandlerAdapter {
    @Override
    public boolean supports(Object handler) {
        return (handler instanceof ControllerV4);
    }

    @Override
    public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {

        ControllerV4 controller = (ControllerV4) handler;

        // 요청 파라미터 내용을 Map에 담는다
        Map<String, String> paramMap = creatParamMap(request);

        // member나 members 데이터를 담는 model map
        HashMap<String, Object> model = new HashMap<>();

        // V4 process는 paramMap과 model(Map)을 파라미터로 받는다
        // 핸들러마다 가지고 있는 고유한 viewName이 반환된다.
        // model에 필요한 데이터는 controller에서 처리한다.
        String viewName = controller.process(paramMap, model);

        // 리턴 타입이 ModelView이므로, 반환할 ModelView 객체를 세팅한다.
        ModelView mv = new ModelView(viewName);
        mv.setModel(model);

        return mv;
    }

    private Map<String, String> creatParamMap(HttpServletRequest request) {
        Map<String, String> paramMap = new HashMap<>();
        request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> paramMap.put(paramName, request.getParameter(paramName)));
        return paramMap;
    }
}

```

하나씩 분석해보자. 

```java
@Override
public boolean supports(Object handler) {
    return (handler instanceof ControllerV4);
}
```
handler가 ControllerV4인 경우에만 처리하는 어댑터다. 


**핸들러 실행 로직** 
```java
@Override
public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {

    ControllerV4 controller = (ControllerV4) handler;

    // 요청 파라미터 내용을 Map에 담는다
    Map<String, String> paramMap = creatParamMap(request);
    // member나 members 데이터를 담는 model map
    HashMap<String, Object> model = new HashMap<>();

    // V4 process는 paramMap과 model(Map)을 파라미터로 받는다
    // 핸들러마다 가지고 있는 고유한 viewName이 반환된다.
    // model에 필요한 데이터는 controller에서 처리한다.
    String viewName = controller.process(paramMap, model);

    ...
}
```

`handle()` 메서드가 파라미터로 전달받는 `handler` 객체는 프런트 컨트롤러에서 넘겨준다. 프런트 컨트롤러는 `Object handler = getHandler(request);`를 통해 `handlerMappingMap`에서 조회하여 `handler`를 찾는다. 

`handler`는 `ControllerV4`로 캐스팅 되고, `paramMap`과 `model`을 만들어서 해당 컨트롤러를 호출한다. 그리고 `viewName`을 반환 받는다. 


**어댑터 변환** 

```java
@Override
public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {

    ...
    
    // 리턴 타입이 ModelView이므로, 반환할 ModelView 객체를 세팅한다.
    ModelView mv = new ModelView(viewName);
    mv.setModel(model);

    return mv;
}
```

어댑터에서 `ModelView` 객체를 만들고, model을 집어 넣는 작업은 단순하지만 중요하다. 어댑터의 핵심적인 역할이 드러나기 때문이다.

어댑터가 호출하는 `ControllerV4`는 뷰의 이름을 반환한다. 그런데 어댑터는 뷰의 이름이 아니라 `ModelView`를 만들어서 반환해야 한다. 여기서 어댑터가 꼭 필요한 이유가 나온다.

`ControllerV4`는 뷰의 이름을 반환했지만, 어댑터는 이것을 `ModelView`로 만들어서 형식을 맞추어 반환한다. 어댑터와 ControllerV4는 반환 타입이 다르지만, 어댑터가 프런트 컨트롤러에게 필요한 반환 타입을 만들어 준다.

```java
public interface ControllerV4 {
    String process(Map<String, String> paramMap, Map<String, Object> model);
}

public interface MyHandlerAdapter {
    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}
```



---

## 정리

여기까지 가장 간단한 구조의 프런트 컨트롤러 구조를 점진적으로 발전시켜왔다. 


**v1: 프론트 컨트롤러를 도입** 
- 기존 구조를 최대한 유지하면서 프론트 컨트롤러를 도입 


**v2: View 분리**

- 단순 반복 되는 뷰 로직 분리 


**v3: Model 추가** 

- 서블릿 종속성 제거 
- 뷰 이름 중복 제거 


**v4: 단순하고 실용적인 컨트롤러** 

- v3와 거의 비슷 
- 구현 입장에서 ModelView를 직접 생성해서 반환하지 않도록 편리한 인터페이스 제공 


**v5: 유연한 컨트롤러** 

- 어댑터 도입 
- 어댑터를 추가해서 프레임워크를 유연하고 확장성 있게 설계 

만약 여기에 애노테이션 기반 컨트롤러를 사용하고 싶다면, 해당 기능을 지원하는 어댑터만 추가하면 된다. 다형성과 어댑터 덕분에 기존 구조를 유지하면서 프레임워크 기능을 자유롭게 확장할 수 있게 되었다.

version 5가 현재의 스프링 MVC 프레임워크와 거의 동일한 구조를 가지고 있다. 스프링 MVC 프레임워크의 핵심 코드의 축약 버전이라 할 수 있다. 아래 그림을 통해 비교해보자.

**직접 만든 MVC 프레임워크 구조**
![](https://images.velog.io/images/woply/post/b29f71c6-eaef-4114-bf30-28967f8836ae/image.png)

**스프링 MVC 구조**
![](https://images.velog.io/images/woply/post/4a2d1232-3f4d-43aa-be91-0b934bff6650/image.png)

**직접 만든 프레임워크 -> 스프링 MVC 비교** 
- FrontController -> DispatcherServlet 
- handlerMappingMap -> HandlerMapping 
- MyHandlerAdapter -> HandlerAdapter
- ModelView -> ModelAndView
- viewResolver -> ViewResolver 
- MyView -> View 