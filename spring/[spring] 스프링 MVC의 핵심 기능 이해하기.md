> ### ๐ โ๏ธ 
> **์ด ๊ธ์ ์คํ๋ง MVC์ ์ ์ฒด ๊ตฌ์กฐ๋ฅผ ์ดํดํ๊ธฐ ์ํด ํต์ฌ์ ์ธ ๊ธฐ๋ฅ์ธ ํธ๋ค๋ฌ, ํธ๋ค๋ฌ ์ด๋ํฐ, ๋ทฐ ๋ฆฌ์กธ๋ฒ์ ๋์ ์๋ฆฌ๋ฅผ ํ์ตํ๊ณ  ๋ค์ํ๊ฒ ์ ์ฉํด๋ณธ ๊ณผ์ ์ ์ ๋ฆฌํ ํฌ์คํ์ด๋ค.**

---

# ์คํ๋ง MVC์ ํต์ฌ ๊ธฐ๋ฅ ์ดํดํ๊ธฐ

์คํ๋ง MVC๋ ์ฝ๋ ๋ถ๋์ด ๋ง๊ณ  ๋ณต์กํ๋ค. ๋ด๋ถ ๊ตฌ์กฐ๋ฅผ ๋ค ํ์ํ๊ธฐ๋ ์ฝ์ง ์๋ค. ํ์ง๋ง ๋ฐ์ ์ ๊ฑฐ๋ญํด ์จ ์คํ๋ง MVC์ ํ์ํ ๊ธฐ๋ฅ์ด ์์ด์ ์ง์  ์ปจํธ๋กค๋ฌ๋ฅผ ๊ฐ๋ฐํ  ์ผ๋ ์์ ๊ฒ์ด๋ค. ์คํ๋ง MVC๋ ์  ์ธ๊ณ ์๋ง์ ๊ฐ๋ฐ์์ ์๊ตฌ์ฌํญ์ ๋ง์ถ์ด ๊ธฐ๋ฅ์ ๊ณ์ ํ์ฅํด ์๋ค. ์น ์ ํ๋ฆฌ์ผ์ด์์ ๋ง๋ค ๋ ํ์๋ก ํ๋ ๋๋ถ๋ถ์ ๊ธฐ๋ฅ์ด ์ด๋ฏธ ๋ค ๊ตฌํ๋์ด ์๋ค.

์คํ๋ง MVC๋ ํต์ฌ ๋์ ๋ฐฉ์์ ์ดํดํ๊ณ  ์์ผ๋ฉด ์ข๋ค. ๊ทธ๋์ผ๋ง ๋ฌธ์ ๊ฐ ๋ฐ์ํ์ ๋, ์์ธ์ ๋น ๋ฅด๊ฒ ํ์ํ  ์ ์๋ค. ๋ํ, ํ์ฅ์ด ํ์ํ  ๋ ์ด๋๋ฅผ ์๋ด์ผ ํ ์ง ๊ฐ์ ์ก์ ์ ์๋ค.

์คํ๋ง MVC์ ์ ์ฒด ๊ตฌ์กฐ๋ฅผ ์ดํด๋ณด๋ฉฐ, ํต์ฌ์ ์ธ ๊ธฐ๋ฅ์ธ ํธ๋ค๋ฌ, ํธ๋ค๋ฌ ์ด๋ํฐ, ๋ทฐ ๋ฆฌ์กธ๋ฒ์ ๋์ ์๋ฆฌ๋ฅผ ํ์ํด ๋ณผ ๊ฒ์ด๋ค. ๊ทธ๋ฆฌ๊ณ  ๋ง์ง๋ง์ผ๋ก ์ ๋ธํ์ด์ ๋ฐฉ์์ผ๋ก ์์ ์ธ๊ธํ ์คํ๋ง MVC์ ํต์ฌ ๊ธฐ๋ฅ์ ์ฌ์ฉํด๋ณด์.

**Spring MVC ๊ตฌ์กฐ**
![](https://images.velog.io/images/woply/post/dd420fe1-d3b9-4c0c-94c6-9ce424b77990/image.png)

---


## 1. DispatcherServlet ๊ตฌ์กฐ ์ดํด๋ณด๊ธฐ 


**`org.springframework.web.servlet.DispatcherServlet`**

์คํ๋ง MVC๋ ํ๋ก ํธ ์ปจํธ๋กค๋ฌ ํจํด์ผ๋ก ๊ตฌํ๋์ด ์๋ค. ๋์คํจ์ฒ ์๋ธ๋ฆฟ(DispatcherServlet)์ด ์คํ๋ง MVC์ ํ๋ก ํธ ์ปจํธ๋กค๋ฌ ์ญํ ์ ํ๋ค. ๋์คํจ์ฒ ์๋ธ๋ฆฟ(DispatcherServlet)์ด ์คํ๋ง MVC์ ํต์ฌ์ด๋ค. 


### DispacherServlet ์๋ธ๋ฆฟ ๋ฑ๋ก 

`DispacherServlet`์ ๋ถ๋ชจ ํด๋์ค์์ `HttpServlet`์ ์์ ๋ฐ๋๋ค. ๊ทธ๋ฌ๋ฏ๋ก, ๋์ผํ๊ฒ ์๋ธ๋ฆฟ์ผ๋ก ๋์ํ๋ค.

์๋ ์์ ๊ตฌ์กฐ๋ฅผ ์ฐธ๊ณ ํ์.

`DispatcherServlet` -> `FrameworkServlet`  -> `HttpServletBean` -> `HttpServlet`
	  
์คํ๋ง ๋ถํธ๋ `DispacherServlet`์ ์๋ธ๋ฆฟ์ผ๋ก ์๋ ๋ฑ๋กํ๋ฉด์ ๋ชจ๋  ๊ฒฝ๋ก(urlPatterns="/")์ ๋ํด์ ๋งคํํ๋ค. ๋จ, ์ธ๋ถ ๊ฒฝ๋ก๊ฐ ๋ช์๋์ด ์์ผ๋ฉด ์ฐ์ ์์๊ฐ ๋ ๋๊ฒ ๋ถ์ฌ๋๋ค. ๋ฐ๋ผ์, ๊ธฐ์กด์ ๋ฑ๋ก๋ ์๋ธ๋ฆฟ๋ ํจ๊ป ๋์ํ๋ค.


### DispacherServlet ์์ฒญ ํ๋ฆ 

`DispacherServlet`์ด ํธ์ถ๋๋ฉด `HttpServlet`์ด ์ ๊ณตํ๋ `serivce()`๊ฐ ํธ์ถ๋๋ค. ์คํ๋ง MVC๋ `DispatcherServlet`์ ๋ถ๋ชจ์ธ `FrameworkServlet`์์ `service()`๋ฅผ ์ค๋ฒ๋ผ์ด๋ํ๋ค. 

`FrameworkServlet.service()`๋ฅผ ์์์ผ๋ก ์ฌ๋ฌ ๋ฉ์๋๊ฐ ํธ์ถ๋๋ฉด์ `DispacherServlet.doDispatch()`๊ฐ ํธ์ถ๋๋ค. 

์ง๊ธ๋ถํฐ `DispacherServlet`์ ํต์ฌ์ธ `doDispatch()` ์ฝ๋๋ฅผ ๋ถ์ํด๋ณด์. ์ต๋ํ ๊ฐ๋จํ ์ค๋ชํ๊ธฐ ์ํด ์์ธ์ฒ๋ฆฌ, ์ธํฐ์ํฐ ๊ธฐ๋ฅ์ ์ ์ธํ๋ค. 

### DispacherServlet.doDispatch()

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {

    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    ModelAndView mv = null;

    // 1. ํธ๋ค๋ฌ ์กฐํ 
    mappedHandler = getHandler(processedRequest); 
    if (mappedHandler == null) { 
        noHandlerFound(processedRequest, response);
        return; 
    }

    //2.ํธ๋ค๋ฌ ์ด๋ํฐ ์กฐํ-ํธ๋ค๋ฌ๋ฅผ ์ฒ๋ฆฌํ  ์ ์๋ ์ด๋ํฐ 
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler()); 


    // 3. ํธ๋ค๋ฌ ์ด๋ํฐ ์คํ -> 4. ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ํตํด ํธ๋ค๋ฌ ์คํ -> 5. ModelAndView ๋ฐํ 
    mv = ha.handle(processedRequest, response, mappedHandler.getHandler());

    processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);


    private void processDispatchResult(HttpServletRequest request, HttpServletResponse response, HandlerExecutionChain mappedHandler, ModelAndView
    mv, Exception exception) throws Exception {

    // ๋ทฐ ๋ ๋๋ง ํธ์ถ
    render(mv, request, response);
    }




    protected void render(ModelAndView mv, HttpServletRequest request, HttpServletResponse response) throws Exception {
        View view;
        String viewName = mv.getViewName(); 


    //6. ๋ทฐ ๋ฆฌ์กธ๋ฒ๋ฅผ ํตํด์ ๋ทฐ ์ฐพ๊ธฐ, 7.View ๋ฐํ 
    view = resolveViewName(viewName, mv.getModelInternal(), locale, request);
    
    // 8. ๋ทฐ ๋ ๋๋ง 
    view.render(mv.getModelInternal(), request, response);
}
```


### Spring MVC ๊ตฌ์กฐ 
![](https://images.velog.io/images/woply/post/dd420fe1-d3b9-4c0c-94c6-9ce424b77990/image.png)




### ๋์ ์์ 

1. **ํธ๋ค๋ฌ ์กฐํ**: ํธ๋ค๋ฌ ๋งคํ์ ํตํด ์์ฒญ URL์ ๋งคํ๋ ํธ๋ค๋ฌ(์ปจํธ๋กค๋ฌ)๋ฅผ ์กฐํํ๋ค. 
2. **ํธ๋ค๋ฌ ์ด๋ํฐ ์กฐํ**: ํธ๋ค๋ฌ๋ฅผ ์คํํ  ์ ์๋ ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ์กฐํํ๋ค. 
3. **ํธ๋ค๋ฌ ์ด๋ํฐ ์คํ**: ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ์คํํ๋ค. 
4. **ํธ๋ค๋ฌ ์คํ**: ํธ๋ค๋ฌ ์ด๋ํฐ๊ฐ ์ค์  ํธ๋ค๋ฌ๋ฅผ ์คํํ๋ค. 
5. **ModelAndView ๋ฐํ**: ํธ๋ค๋ฌ ์ด๋ํฐ๋ ํธ๋ค๋ฌ๊ฐ ๋ฐํํ๋ ์ ๋ณด๋ฅผ `ModelAndView`๋ก ๋ณํํด์ ๋ฐํํ๋ค. 
6. **viewResolver ํธ์ถ**: ๋ทฐ ๋ฆฌ์กธ๋ฒ๋ฅผ ์ฐพ๊ณ  ์คํํ๋ค. JSP์ ๊ฒฝ์ฐ `InternalResourceViewResolver`๊ฐ ์๋ ๋ฑ๋ก๋๊ณ , ์ฌ์ฉ๋๋ค. 
7. **View ๋ฐํ**:๋ทฐ ๋ฆฌ์กธ๋ฒ๋ ๋ทฐ์ ๋ผ๋ฆฌ ์ด๋ฆ์ ๋ฌผ๋ฆฌ ์ด๋ฆ์ผ๋ก ๋ฐ๊พธ๊ณ , ๋ ๋๋ง ์ญํ ์ ๋ด๋นํ๋ ๋ทฐ ๊ฐ์ฒด๋ฅผ ๋ฐํํ๋ค. JSP์ ๊ฒฝ์ฐ `InternalResourceView(JstlView)`๋ฅผ ๋ฐํํ๋ค. ๋ด๋ถ์ `forward()` ๋ก์ง์ด ์๋ค. 
8. **๋ทฐ ๋ ๋๋ง**: ๋ทฐ๋ฅผ ํตํด์ ๋ทฐ๋ฅผ ๋ ๋๋งํ๋ค. 



### ์ธํฐํ์ด์ค ์ดํด๋ณด๊ธฐ 

์คํ๋ง MVC์ ํฐ ๊ฐ์ ์ `DispatcherServlet` ์ฝ๋์ ๋ณ๊ฒฝ ์์ด, ์ํ๋ ๊ธฐ๋ฅ์ ๋ณ๊ฒฝํ๊ฑฐ๋ ํ์ฅํ  ์ ์๋ค๋ ์ ์ด๋ค. ํต์ฌ ๊ธฐ๋ฅ์ ๋๋ถ๋ถ์ ์ธํฐํ์ด์ค๋ก ์ ๊ณตํ์ฌ, ์ฝ๊ฒ ํ์ฅ ํ  ์ ์๋ค.
  
์ด ์ธํฐํ์ด์ค๋ค๋ง ๊ตฌํํด์ `DispatcherServlet`์ ๋ฑ๋กํ๋ฉด ์ฌ๋ฌ๋ถ๋ง์ ์ปจํธ๋กค๋ฌ๋ฅผ ๋ง๋ค ์๋ ์๋ค. 


**์ฃผ์ ์ธํฐํ์ด์ค ๋ชฉ๋ก** 

- **ํธ๋ค๋ฌ ๋งคํ**: `org.springframework.web.servlet.HandlerMapping`
- **ํธ๋ค๋ฌ ์ด๋ํฐ**:  `org.springframework.web.servlet.HandlerAdapter`
- **๋ทฐ ๋ฆฌ์กธ๋ฒ**: `org.springframework.web.servlet.ViewResolver`
- **๋ทฐ**: `org.springframework.web.servlet.View`



---


## 2. ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ 


์คํ๋ง MVC๊ฐ ์ง์ํ๋ ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ๋ ์ด๋ค ๊ฒ๋ค์ด ์๋์ง, ์ด๋ป๊ฒ ์ฌ์ฉํด์ผ ํ๋์ง ์ดํด๋ณด์.

์ง๊ธ์ ์ ํ ์ฌ์ฉํ์ง ์์ง๋ง, ๊ณผ๊ฑฐ์ ์ฃผ๋ก ์ฌ์ฉํ๋ ๊ฐ๋จํ ์ปจํธ๋กค๋ฌ๋ก ํธ๋ค๋ฌ ๋งคํ๊ณผ ์ด๋ํฐ๋ฅผ ์ดํดํด๋ณด์. 


### Controller ์ธํฐํ์ด์ค 

**๊ณผ๊ฑฐ ๋ฒ์  ์คํ๋ง ์ปจํธ๋กค๋ฌ** 

`org.springframework.web.servlet.mvc.Controller`

```java
public interface Controller {
    ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception;
} 
```

์คํ๋ง๋ ์ฒ์์๋ ์ด๋ฐ ๋ฑ๋ฑํ ํ์์ ์ปจํธ๋กค๋ฌ๋ฅผ ์ ๊ณตํ๋ค. 

> ์ฐธ๊ณ  
> Controller ์ธํฐํ์ด์ค๋ @Controller ์ ๋ธํ์ด์๊ณผ๋ ์ ํ ๋ค๋ฅด๋ค. 

```java
package hello.servlet.web.springmvc.old;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component("/springmvc/old-controller")
public class OldController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        System.out.println("OldController.handleRequest");
        return null;
    }
}
```

**`@Component`**: ์ด ์ปจํธ๋กค๋ฌ๋ `/springmvc/old-controller`๋ผ๋ ์ด๋ฆ์ ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋ก๋์๋ค. ๋น์ ์ด๋ฆ์ผ๋ก URL์ ๋งคํํ  ๊ฒ์ด๋ค.

**์คํ** 
- http://localhost:8080/springmvc/old-controller 
- ์ฝ์์ `OldController.handleRequest` ์ด ์ถ๋ ฅ๋๋ค.

![](https://images.velog.io/images/woply/post/a2ea4bb5-a2f0-4314-865d-0810dabf5bb3/image.png)

์ด ์ปจํธ๋กค๋ฌ๋ ์ด๋ป๊ฒ ํธ์ถ๋  ์ ์์๊น? 
์ด ์ปจํธ๋กค๋ฌ๊ฐ ํธ์ถ๋๋ ค๋ฉด ๋ค์ 2๊ฐ์ง๊ฐ ํ์ํ๋ค. 


1. **HandlerMapping(ํธ๋ค๋ฌ ๋งคํ) **

- ํธ๋ค๋ฌ ๋งคํ์์ ์ด ์ปจํธ๋กค๋ฌ๋ฅผ ์ฐพ์ ์ ์์ด์ผ ํ๋ค. 
- ์) ์คํ๋ง ๋น์ ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ์ ์ ์๋ ํธ๋ค๋ฌ ๋งคํ์ด ํ์ํ๋ค. 


2. **HandlerAdapter(ํธ๋ค๋ฌ ์ด๋ํฐ)** 

- ํธ๋ค๋ฌ ๋งคํ์ ํตํด์ ์ฐพ์ ํธ๋ค๋ฌ๋ฅผ ์คํํ  ์ ์๋ ํธ๋ค๋ฌ ์ด๋ํฐ๊ฐ ํ์ํ๋ค. 
- ์) Controller  ์ธํฐํ์ด์ค๋ฅผ ์คํํ  ์ ์๋ ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ์ฐพ๊ณ  ์คํํด์ผ ํ๋ค. 

์คํ๋ง์ ์ด๋ฏธ ํ์ํ ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ๋๋ถ๋ถ์ ์ง์ํ๋ค. ๊ฐ๋ฐ์๊ฐ ์ง์  ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ๋ง๋๋ ์ผ์ ๊ฑฐ์ ์๋ค. 

### ์คํ๋ง ๋ถํธ๊ฐ ์๋ ๋ฑ๋กํ๋ ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ 

(์ค์ ๋ก๋ ๋ ๋ง์ง๋ง, ์ค์ํ ๋ถ๋ถ ์์ฃผ๋ก ์ค๋ชํ๊ธฐ ์ํด ์ผ๋ถ ์๋ต) 

**HandlerMapping ์์ ์ฐ์ ์์**

```
0 = RequestMappingHandlerMapping : ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์ปจํธ๋กค๋ฌ์ธ @RequestMapping์์ ์ฌ์ฉ 
1 = BeanNameUrlHandlerMapping : ์คํ๋ง ๋น์ ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ๋๋ค. 
```


**HandlerAdapter ์์ ์ฐ์ ์์**

```
0 = RequestMappingHandlerAdapter : ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์ปจํธ๋กค๋ฌ์ธ @RequestMapping์์ ์ฌ์ฉ 
1 = HttpRequestHandlerAdapter : HttpRequestHandler ์ฒ๋ฆฌ 
2 = SimpleControllerHandlerAdapter : Controller ์ธํฐํ์ด์ค(์ ๋ธํ์ด์X, ๊ณผ๊ฑฐ์ ์ฌ์ฉ) ์ฒ๋ฆฌ 
```

ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ ๋ชจ๋ ์์๋๋ก ํด๋นํ๋ ๊ฒ์ด ์๋์ง ์ฐพ๋๋ค. ์๋ค๋ฉด ๋ค์ ์์๋ก ๋์ด๊ฐ๋ค.



1. **ํธ๋ค๋ฌ ๋งคํ์ผ๋ก ํธ๋ค๋ฌ ์กฐํ **
	- `HandlerMapping`์ ์์๋๋ก ์คํํด์, ํธ๋ค๋ฌ๋ฅผ ์ฐพ๋๋ค. 
	- ์ด ๊ฒฝ์ฐ ๋น ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ์์ผ ํ๊ธฐ ๋๋ฌธ์ ์ด๋ฆ ๊ทธ๋๋ก ๋น ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ์์ฃผ๋ `BeanNameUrlHandlerMapping`์ด ์คํ์ ์ฑ๊ณตํ๊ณ  ํธ๋ค๋ฌ์ธ `OldController`๋ฅผ ๋ฐํํ๋ค. 


2. **ํธ๋ค๋ฌ ์ด๋ํฐ ์กฐํ** 
	- `HandlerAdapter`์ `supports()`๋ฅผ ์์๋๋ก ํธ์ถํ๋ค. 
	- `SimpleControllerHandlerAdapter`๊ฐ `Controller`์ธํฐํ์ด์ค๋ฅผ ์ง์ํ๋ฏ๋ก ๋์์ด ๋๋ค. 


3. **ํธ๋ค๋ฌ ์ด๋ํฐ ์คํ**
	- ๋์คํจ์ฒ ์๋ธ๋ฆฟ์ด ์กฐํํ `SimpleControllerHandlerAdapter`๋ฅผ ์คํํ๋ฉด์ ํธ๋ค๋ฌ ์ ๋ณด๋ ํจ๊ป ๋๊ฒจ์ค๋ค.
	- `SimpleControllerHandlerAdapter`๋ ํธ๋ค๋ฌ์ธ `OldController`๋ฅผ ๋ด๋ถ์์ ์คํํ๊ณ , ๊ทธ ๊ฒฐ๊ณผ๋ฅผ ๋ฐํํ๋ค.

> **๊ฒฐ๋ก **
> `OldController`๋ฅผ ์คํํ๋ฉด์ ์ฌ์ฉ๋ ๊ฐ์ฒด๋ ๋ค์๊ณผ ๊ฐ๋ค. 
```
HandlerMapping = BeanNameUrlHandlerMapping
HandlerAdapter = SimpleControllerHandlerAdapter
```


### HttpRequestHandler 

๋ง์ฝ Controller ์ธํฐํ์ด์ค๊ฐ ์๋ ๋ค๋ฅธ ํธ๋ค๋ฌ๋ผ๋ฉด ์ด๋ป๊ฒ ์ฐพ์๊น? ํธ๋ค๋ฌ ๋งคํ๊ณผ, ์ด๋ํฐ๋ฅผ ๋ ์ ์ดํดํ๊ธฐ ์ํด ๋ค๋ฅธ ํธ๋ค๋ฌ์ธ `HttpRequestHandler(์ปจํธ๋กค๋ฌ)`๋ฅผ ๋์์ผ๋ก ๋ค์ ํ ๋ฒ ๊ณผ์ ์ ์ดํด๋ณด์. ์ฐธ๊ณ ๋ก `HttpRequestHandler(์ปจํธ๋กค๋ฌ)`๋ ์๋ธ๋ฆฟ๊ณผ ๊ฐ์ฅ ์ ์ฌํ ํํ์ ํธ๋ค๋ฌ์ด๋ค. 


**HttpRequestHandler ์ธํฐํ์ด์ค **

```java
public interface HttpRequestHandler {
    void handleRequest(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException;
}
```

๊ฐ๋จํ๊ฒ ๊ตฌํํด๋ณด์. 


**MyHttpRequestHandler** 
```java
package hello.servlet.web.springmvc.old;

import org.springframework.stereotype.Component;
import org.springframework.web.HttpRequestHandler;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

@Component("/springmvc/request-handler")
public class MyHttpRequestHandler implements HttpRequestHandler {
    @Override
    public void handleRequest(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("MyHttpRequestHandler.handleRequest");
    }
}

```


**์คํ** 
- http://localhost:8080/springmvc/request-handler 
- ์น ๋ธ๋ผ์ฐ์ ์ ๋น ํ๋ฉด์ด ๋์ค๊ณ , ์ฝ์์ `MyHttpRequestHandler.handleRequest`๊ฐ ์ถ๋ ฅ๋๋ค.

1. **ํธ๋ค๋ฌ ๋งคํ์ผ๋ก ํธ๋ค๋ฌ ์กฐํ**
	- `HandlerMapping`์ ์์๋๋ก ์คํํด์, ํธ๋ค๋ฌ๋ฅผ ์ฐพ๋๋ค.
    - ์ด ๊ฒฝ์ฐ, ๋น ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ์์ผ ํ๋ฏ๋ก, ๋น ์ด๋ฆ์ผ๋ก ํธ๋ค๋ฌ๋ฅผ ์ฐพ์์ฃผ๋ `BeanNameUrlHandlerMapping`๊ฐ ์คํ์ ์ฑ๊ณตํ๊ณ  ํธ๋ค๋ฌ์ธ `MyHttpRequestHandler`๋ฅผ ๋ฐํํ๋ค. 


2. **ํธ๋ค๋ฌ ์ด๋ํฐ ์กฐํ** 
	- `HandlerAdapter`์ `supports()`๋ฅผ ์์๋๋ก ํธ์ถํ๋ค. 
    - `HttpRequestHandlerAdapter`๊ฐ `HttpRequestHandler` ์ธํฐํ์ด์ค๋ฅผ ์ง์ํ๋ฏ๋ก ๋์์ด ๋๋ค. 


3. **ํธ๋ค๋ฌ ์ด๋ํฐ ์คํ** 

	- ๋์คํจ์ฒ ์๋ธ๋ฆฟ์ด ์กฐํํ `HttpRequestHandlerAdapter`๋ฅผ ์คํํ๋ฉด์ ํธ๋ค๋ฌ ์ ๋ณด๋ ํจ๊ป ๋๊ฒจ์ค๋ค. 
    - `HttpRequestHandlerAdapter`๋ ํธ๋ค๋ฌ์ธ `MyHttpRequestHandler`๋ฅผ ๋ด๋ถ์์ ์คํํ๊ณ , ๊ทธ ๊ฒฐ๊ณผ๋ฅผ ๋ฐํํ๋ค. 


> **๊ฒฐ๋ก **
> `HttpRequestHandler`๋ฅผ ์คํํ๋ฉด์ ์ฌ์ฉ๋ ๊ฐ์ฒด๋ ๋ค์๊ณผ ๊ฐ๋ค.
```
HandlerMapping = BeanNameUrlHandlerMapping
HandlerAdapter = HttpRequestHandlerAdapter
```


### @RequestMapping 

๊ฐ์ฅ ์ฐ์ ์์๊ฐ ๋์ ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ๋ `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`์ด๋ค.
`@RequestMapping`์ ์๊ธ์๋ฅผ ๋ฐ์ ๋ง๋  ์ด๋ฆ์ด๋ค. ์ต๊ทผ ์คํ๋ง์์ ์ฃผ๋ก ์ฌ์ฉํ๋ ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์ปจํธ๋กค๋ฌ๋ฅผ ์ง์ํ๋ ๋งคํ๊ณผ ์ด๋ํฐ์ด๋ค. ์ค๋ฌด์์๋ 99.9% ์ด ๋ฐฉ์์ ์ปจํธ๋กค๋ฌ๋ฅผ ์ฌ์ฉํ๋ค๊ณ  ํ๋ค.


---


## 3. ๋ทฐ ๋ฆฌ์กธ๋ฒ 


๋ทฐ ๋ฆฌ์กธ๋ฒ์ ๋ํด์ ์์ธํ ์์๋ณด๊ธฐ ์ํด `OldController`๋ฅผ View ์กฐํ๊ฐ ๊ฐ๋ฅํ๋๋ก ๋ณ๊ฒฝํด๋ณด์.

View๋ฅผ ์ฌ์ฉํ  ์ ์๋๋ก ๋ค์ ์ฝ๋๋ฅผ ์ถ๊ฐํ๋ค. new-form์ JSP ํ์ผ์ด๋ค.
`return new ModelAndView("new-form");` 


```java
package hello.servlet.web.springmvc.old;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.ModelAndView;
import org.springframework.web.servlet.mvc.Controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Component("/springmvc/old-controller")
public class OldController implements Controller {

    @Override
    public ModelAndView handleRequest(HttpServletRequest request, HttpServletResponse response) throws Exception {
        System.out.println("OldController.handleRequest");
        return new ModelAndView("new-form");
    }
}

```



**์คํ**
- http://localhost:8080/springmvc/old-controller 
- ์น ๋ธ๋ผ์ฐ์ ์ `Whitelabel Error Page` ๊ฐ ๋์ค๊ณ , ์ฝ์์ `OldController.handleRequest`์ด ์ถ๋ ฅ๋๋ค.

์คํํด๋ณด๋ฉด ์ปจํธ๋กค๋ฌ๋ฅผ ์ ์ ํธ์ถ๋์ง๋ง, `Whitelabel Error Page` ์ค๋ฅ๊ฐ ๋ฐ์ํ๋ค.

`application.properties`์ ๋ค์ ์ฝ๋๋ฅผ ์ถ๊ฐํด์ผ ํ๋ค. 
```
spring.mvc.view.prefix=/WEB-INF/views/
spring.mvc.view.suffix=.jsp
```


### ๋ทฐ ๋ฆฌ์กธ๋ฒ - InternalResourceViewResolver 

์คํ๋ง ๋ถํธ๋ `InternalResourceViewResolver`๋ผ๋ ๋ทฐ ๋ฆฌ์กธ๋ฒ๋ฅผ ์๋์ผ๋ก ๋ฑ๋กํ๋ค. ์ด๋, `application.properties`์ ๋ฑ๋กํ `spring.mvc.view.prefix`, `spring.mvc.view.suffix`์ค์  ์ ๋ณด๋ฅผ ์ฌ์ฉํ์ฌ ๋ฑ๋กํ๋ค.

์ฐธ๊ณ ๋ก ์ค์  ์์ด ๋ค์๊ณผ ๊ฐ์ด ์ ์ฒด ๊ฒฝ๋ก๋ฅผ ์ฃผ์ด๋ ๋์ํ๋ค. ๋ค๋ง, ๊ถ์ฅํ์ง ์๋ ๋ฐฉ์์ด๋ค. 
`return new ModelAndView("/WEB-INF/views/new-form.jsp");`


**์คํ** 
- http://localhost:8080/springmvc/old-controller 
- ๋ฑ๋ก ํผ์ด ์ ์ ์ถ๋ ฅ๋๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. ๋ฌผ๋ก  ์ ์ฅ ๊ธฐ๋ฅ์ ๊ฐ๋ฐํ์ง ์์์ผ๋ฏ๋ก ํผ๋ง ์ถ๋ ฅ๋๊ณ , ๋ ์งํํ๋ฉด ์ค๋ฅ๊ฐ ๋ฐ์ํ๋ค. 


**์คํ๋ง MVC ๊ตฌ์กฐ** 

![](https://images.velog.io/images/woply/post/62d2635a-5dff-4e8c-b9c0-e4cc72e0ddd5/image.png)



### ์คํ๋ง ๋ถํธ๊ฐ ์๋ ๋ฑ๋กํ๋ ๋ทฐ ๋ฆฌ์กธ๋ฒ 
(์ค์ ๋ก๋ ๋ ๋ง์ง๋ง, ์ค์ํ ๋ถ๋ถ ์์ฃผ๋ก ์ค๋ชํ๊ธฐ ์ํด ์ผ๋ถ ์๋ต) 

```
1 = BeanNameViewResolver : ๋น ์ด๋ฆ์ผ๋ก ๋ทฐ๋ฅผ ์ฐพ์์ ๋ฐํํ๋ค. (์: ์์ ํ์ผ ์์ฑ ๊ธฐ๋ฅ์ ์ฌ์ฉ) 
2 = InternalResourceViewResolver : JSP๋ฅผ ์ฒ๋ฆฌํ  ์ ์๋ ๋ทฐ๋ฅผ ๋ฐํํ๋ค. 
```

1. **ํธ๋ค๋ฌ ์ด๋ํฐ ํธ์ถ** 

	- ํธ๋ค๋ฌ ์ด๋ํฐ๋ฅผ ํตํด `new-form`์ด๋ผ๋ ๋ผ๋ฆฌ ๋ทฐ ์ด๋ฆ์ ํ๋ํ๋ค. 

2. **ViewResolver ํธ์ถ** 
	- `new-form`์ด๋ผ๋ ๋ทฐ ์ด๋ฆ์ผ๋ก `viewResolver`๋ฅผ ์์๋๋ก ํธ์ถํ๋ค.
    - `BeanNameViewResolver`๋ `new-form`์ด๋ผ๋ ์ด๋ฆ์ ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋ก๋ ๋ทฐ๋ฅผ ์ฐพ์์ผ ํ๋๋ฐ ์๋ค. 
- `InternalResourceViewResolver`๊ฐ ํธ์ถ๋๋ค. 

3. **InternalResourceViewResolver** 
	- ์ด ๋ทฐ ๋ฆฌ์กธ๋ฒ๋ `InternalResourceView`๋ฅผ ๋ฐํํ๋ค. 


4. **๋ทฐ - InternalResourceView** 

	- `InternalResourceView`๋ JSP์ฒ๋ผ ํฌ์๋ `forward()` ๋ฅผ ํธ์ถํด์ ์ฒ๋ฆฌํ  ์ ์๋ ๊ฒฝ์ฐ์ ์ฌ์ฉํ๋ค. 


5. **view.render()** 
	- `view.render()` ๊ฐ ํธ์ถ๋๊ณ , `InternalResourceView`๋ `forward()`๋ฅผ ์ฌ์ฉํด์ JSP๋ฅผ ์คํํ๋ค. 

> **์ฐธ๊ณ ** 
> `InternalResourceViewResolver`๋ ๋ง์ฝ JSTL ๋ผ์ด๋ธ๋ฌ๋ฆฌ๊ฐ ์์ผ๋ฉด `InternalResourceView`๋ฅผ ์์๋ฐ์ `JstlView`๋ฅผ ๋ฐํํ๋ค. `JstlView`๋ JSTL ํ๊ทธ ์ฌ์ฉ์ ์ฝ๊ฐ์ ๋ถ๊ฐ ๊ธฐ๋ฅ์ด ์ถ๊ฐ๋๋ค. 

> **์ฐธ๊ณ ** 
> ๋ค๋ฅธ ๋ทฐ๋ ์ค์  ๋ทฐ๋ฅผ ๋ ๋๋งํ์ง๋ง, JSP์ ๊ฒฝ์ฐ `forward()`ํตํด์ ํด๋น JSP๋ก ์ด๋(์คํ)ํด์ผ ๋ ๋๋ง์ด ๋๋ค. JSP๋ฅผ ์ ์ธํ ๋๋จธ์ง ๋ทฐ ํํ๋ฆฟ๋ค์ `forward()` ๊ณผ์  ์์ด ๋ฐ๋ก ๋ ๋๋ง ๋๋ค. 

> **์ฐธ๊ณ **
> Thymeleaf ๋ทฐ ํํ๋ฆฟ์ ์ฌ์ฉํ๋ฉด `ThymeleafViewResolver`๋ฅผ ๋ฑ๋กํด์ผ ํ๋ค. ์ต๊ทผ์๋ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ง ์ถ๊ฐํ๋ฉด ์คํ๋ง ๋ถํธ๊ฐ ์ด๋ฐ ์์๋ ๋ชจ๋ ์๋ํํด์ค๋ค. 



---


## 4. ์คํ๋ง MVC - ์์ํ๊ธฐ 


์คํ๋ง์ด ์ ๊ณตํ๋ ์ปจํธ๋กค๋ฌ๋ ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ผ๋ก ๋์ํ๋ค. ๋งค์ฐ ์ ์ฐํ๊ณ  ์ค์ฉ์ ์ด๋ค. ํ์ง๋ง ์คํ๋ง์ด ์ฒ์๋ถํฐ ์ด๋ฐ ์ ์ฐํ ์ปจํธ๋กค๋ฌ๋ฅผ ์ ๊ณตํ ๊ฒ์ ์๋๋ค. 



### @RequestMapping 

์คํ๋ง์ ์ ๋ธํ์ด์์ ํ์ฉํ ๋งค์ฐ ์ ์ฐํ๊ณ , ์ค์ฉ์ ์ธ ์ปจํธ๋กค๋ฌ๋ฅผ ๋ง๋ค์๋๋ฐ ์ด๊ฒ์ด ๋ฐ๋ก `@RequestMapping` ์ ๋ธํ์ด์์ ์ฌ์ฉํ๋ ์ปจํธ๋กค๋ฌ์ด๋ค. 

**@RequestMapping์ ์ฌ์ฉํ๋ ํธ๋ค๋ฌ์ ์ด๋ํฐ**
- RequestMappingHandlerMapping
- RequestMappingHandlerAdapter
  
์์ ๋ณด์๋ฏ์ด ๊ฐ์ฅ ์ฐ์ ์์๊ฐ ๋์ ํธ๋ค๋ฌ ๋งคํ๊ณผ ํธ๋ค๋ฌ ์ด๋ํฐ๋ `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`์ด๋ค. 
`@RequestMapping`์ ์๊ธ์๋ฅผ ๋ฐ์ ๋ง๋  ์ด๋ฆ์ธ๋ฐ, ์คํ๋ง์์ ์ฃผ๋ก ์ฌ์ฉํ๋ ์ ๋ธํ์ด์ ๊ธฐ๋ฐ์ ์ปจํธ๋กค๋ฌ๋ฅผ ์ง์ํ๋ ํธ๋ค๋ฌ ๋งคํ๊ณผ ์ด๋ํฐ๋ค.

์ง๊ธ๊น์ง ๋ง๋ค์๋ ํ๋ ์์ํฌ์์ ์ฌ์ฉํ๋ ์ปจํธ๋กค๋ฌ๋ฅผ 	`@RequestMapping` ๊ธฐ๋ฐ์ ์คํ๋ง MVC ์ปจํธ๋กค๋ฌ๋ก ๋ณ๊ฒฝํด๋ณด์. 



### SpringMemberFormControllerV1 - ํ์ ๋ฑ๋ก ํผ 


```java
package hello.servlet.web.springmvc.v1;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

@Controller
public class SpringMemberFormControllerV1 {
    
    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
}

```


**@Controller**
- ์คํ๋ง์ด ์๋์ผ๋ก ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋กํ๋ค. (๋ด๋ถ์ `@Component` ์ ๋ธํ์ด์์ด ์์ด์ ์ปดํฌ๋ํธ ์ค์บ์ ๋์์ด ๋๋ค.) 
- ์คํ๋ง MVC์์ ์ ๋ธํ์ด์ ๊ธฐ๋ฐ ์ปจํธ๋กค๋ฌ๋ก ์ธ์ํ๋ค. 


**@RequestMapping**

- ์์ฒญ ์ ๋ณด๋ฅผ ๋งคํํ๋ค. ํด๋น URL์ด ํธ์ถ๋๋ฉด ์ด ๋ฉ์๋๊ฐ ํธ์ถ๋๋ค. ์ ๋ธํ์ด์์ ๊ธฐ๋ฐ์ผ๋ก ๋์ํ๊ธฐ ๋๋ฌธ์, ๋ฉ์๋์ ์ด๋ฆ์ ์์๋ก ์ง์ผ๋ฉด ๋๋ค. 

**ModelAndView**

- ๋ชจ๋ธ๊ณผ ๋ทฐ ์ ๋ณด๋ฅผ ๋ด์์ ๋ฐํํ๋ค.


`RequestMappingHandlerMapping`์ ์คํ๋ง ๋น ์ค์์ `@RequestMapping` ๋๋ `@Controller`๊ฐ ํด๋์ค ๋ ๋ฒจ์ ๋ถ์ด ์๋ ๊ฒฝ์ฐ์ ๋งคํ ์ ๋ณด๋ก ์ธ์ํ๋ค.

๋ฐ๋ผ์ ๋ค์ ์ฝ๋๋ ๋์ผํ๊ฒ ๋์ํ๋ค. 

```java
@Component //์ปดํฌ๋ํธ ์ค์บ์ ํตํด ์คํ๋ง ๋น์ผ๋ก ๋ฑ๋ก 
@RequestMapping 
public class SpringMemberFormControllerV1 { 
    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
} 
```

๋ฌผ๋ก  ์ปดํฌ๋์ค ์ค์บ ์์ด ๋ค์๊ณผ ๊ฐ์ด ์คํ๋ง ๋น์ผ๋ก ์ง์  ๋ฑ๋กํด๋ ๋์ํ๋ค. 

```java
@RequestMapping 
public class SpringMemberFormControllerV1 {

    @RequestMapping("/springmvc/v1/members/new-form")
    public ModelAndView process() {
        return new ModelAndView("new-form");
    }
} 
```

**ServletApplication **

```java
//์คํ๋ง ๋น ์ง์  ๋ฑ๋ก 
@Bean
TestController testController() { 
    return new TestController();
}
```


**์คํ** 
- http://localhost:8080/springmvc/v1/members/new-form 
- ํผ์ ํ์ธํ  ์ ์๋ค. ๋๋จธ์ง ์ฝ๋๋ ์ถ๊ฐํด๋ณด์. 


### SpringMemberSaveControllerV1 - ํ์ ์ ์ฅ 


```java
package hello.servlet.web.springmvc.v1;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@Controller
public class SpringMemberSaveControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v1/members/save")
    public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }
}

```

**mv.addObject("member", member)**
- ์คํ๋ง์ด ์ ๊ณตํ๋ `ModelAndView`๋ฅผ ํตํด Model ๋ฐ์ดํฐ๋ฅผ ์ถ๊ฐํ  ๋๋ `addObject()`๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค. ์ด ๋ฐ์ดํฐ๋ ์ดํ ๋ทฐ๋ฅผ ๋ ๋๋ง ํ  ๋ ์ฌ์ฉ๋๋ค. 


### SpringMemberListControllerV1 - ํ์ ๋ชฉ๋ก 


```java
package hello.servlet.web.springmvc.v1;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import java.util.List;

@Controller
public class SpringMemberListControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v1/members")
    public ModelAndView process() {
        List<Member> members = memberRepository.findAll();

        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);

        return mv;
    }
}

```

**์คํ** 
- ๋ฑ๋ก: http://localhost:8080/springmvc/v1/members/new-form 
- ๋ชฉ๋ก: http://localhost:8080/springmvc/v1/members 

---



## 5. ์คํ๋ง MVC - ์ปจํธ๋กค๋ฌ ํตํฉ 

`@RequestMapping`์ ์ ๋ณด๋ฉด ํด๋์ค ๋จ์๊ฐ ์๋๋ผ ๋ฉ์๋ ๋จ์์ ์ ์ฉ๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. ๋ฐ๋ผ์ ์ปจํธ๋กค๋ฌ ํด๋์ค๋ฅผ ์ ์ฐํ๊ฒ ํ๋๋ก ํตํฉํ  ์ ์๋ค.



### SpringMemberControllerV2 - ์ปจํธ๋กค๋ฌ๋ฅผ ๋ชจ์ ํด๋์ค

```java
package hello.servlet.web.springmvc.v2;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.List;


/**
 * ํด๋์ค ๋จ์ -> ๋ฉ์๋ ๋จ์
 * @RequestMapping ํด๋์ค ๋ ๋ฒจ๊ณผ ๋ฉ์๋ ๋ ๋ฒจ ์กฐํฉ
 */
@Controller
@RequestMapping("/springmvc/v2/members")
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/new-form")
    private ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    @RequestMapping
    public ModelAndView members() {
        List<Member> members = memberRepository.findAll();

        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);

        return mv;
    }

    @RequestMapping("/save")
    public ModelAndView save(HttpServletRequest request, HttpServletResponse response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("member", member);
        return mv;
    }
}

```



### ์ปจํธ๋กค๋ฌ ์กฐํฉ 

์ปจํธ๋กค๋ฌ ํด๋์ค๋ฅผ ํตํฉํ๋ ๊ฒ์ ๋์ด์ ์กฐํฉ๋ ๊ฐ๋ฅํ๋ค. ๋ค์ ์ฝ๋๋ `/springmvc/v2/members`๋ผ๋ ์ค๋ณต ๊ฒฝ๋ก๊ฐ ์กด์ฌํ๋ค.

```java
@RequestMapping("/springmvc/v2/members/new-form")
@RequestMapping("/springmvc/v2/members")
@RequestMapping("/springmvc/v2/members/save")
```

๋ฌผ๋ก  ์ด๋ ๊ฒ ์ฌ์ฉํด๋ ๋์ง๋ง, ์ปจํธ๋กค๋ฌ๋ฅผ ํตํฉํ ์์  ์ฝ๋๋ฅผ ๋ณด๋ฉด ์ค๋ณต์ ์ด๋ป๊ฒ ์ ๊ฑฐํ๋์ง ํ์ธํ  ์ ์๋ค. ํด๋์ค ๋ ๋ฒจ์ ๋ค์๊ณผ ๊ฐ์ด `@RequestMapping`์ ๋๋ฉด ๋ฉ์๋ ๋ ๋ฒจ๊ณผ ์กฐํฉ์ด ๋๋ค. 


**์กฐํฉ ๊ฒฐ๊ณผ** 
- **ํด๋์ค ๋ ๋ฒจ**: `@RequestMapping("/springmvc/v2/members")`
- ๋ฉ์๋ ๋ ๋ฒจ: `@RequestMapping("/new-form")` -> `/springmvc/v2/members/new-form`  
- ๋ฉ์๋ ๋ ๋ฒจ: `@RequestMapping("/save")` -> `/springmvc/v2/members/save`
- ๋ฉ์๋ ๋ ๋ฒจ: `@RequestMapping`  -> `/springmvc/v2/members`




## 6. ์คํ๋ง MVC - ์ค์ฉ์ ์ธ ๋ฐฉ์ 

์ด์  ํฌ์คํ์ธ [MVC ํ๋ ์์ํฌ ๋ง๋ค๊ธฐ](https://velog.io/@woply/spring-MVC-ํจํด์-๋ฐ์ -๊ณผ์ ์-ํตํด-ํ์ฌ์-์คํ๋ง-MVC-๊ตฌ์กฐ-์ดํดํ๊ธฐ)์์ v3์ `ModelView`๋ฅผ ๊ฐ๋ฐ์๊ฐ ์ง์  ์์ฑํด์ ๋ฐํํ๊ธฐ ๋๋ฌธ์, ๋ถํธํ๋ ๊ธฐ์ต์ด ๋  ๊ฒ์ด๋ค. ๋ฐ๋ฉด, v4๋ฅผ ๋ง๋ค๋ฉด์ ์ค์ฉ์ ์ผ๋ก ๊ฐ์ ํ ๊ธฐ์ต๋ ์์ ๊ฒ์ด๋ค.

์คํ๋ง MVC๋ ๊ฐ๋ฐ์๊ฐ ํธ๋ฆฌํ๊ฒ ๊ฐ๋ฐํ  ์ ์๋๋ก ์ ๋ง์ ํธ์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. ์ค๋ฌด์์๋ ์ง๊ธ๋ถํฐ ์ค๋ชํ๋ ๋ฐฉ์์ ์ฃผ๋ก ์ฌ์ฉํ๋ค๊ณ  ํ๋ค. 


### SpringMemberControllerV3 


```java
package hello.servlet.web.springmvc.v3;

import hello.servlet.domain.member.Member;
import hello.servlet.domain.member.MemberRepository;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * v3
 * Model ๋์
 * ViewName ์ง์  ๋ฐํ
 *
 * @RequestParam ์ฌ์ฉ
 * @RequestMapping -> @GetMapping, @PostMapping
 */
@Controller
@RequestMapping("/springmvc/v3/members")
public class SpringMemberControllerV3 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @GetMapping("/new-form")
    private String newForm() {
        return "new-form";
    }

    @PostMapping("/save")
    public String save(@RequestParam("username") String username,
                       @RequestParam("age") int age,
                       Model model) {

        Member member = new Member(username, age);
        memberRepository.save(member);

        model.addAttribute("member", member);

        return "save-result";
    }

    @PostMapping()
    public String members(Model model) {
        List<Member> members = memberRepository.findAll();

        model.addAttribute("members", members);

        return "members";
    }
}

```



**Model ํ๋ผ๋ฏธํฐ** 
- save(), members()๋ฅผ ๋ณด๋ฉด Model์ ํ๋ผ๋ฏธํฐ๋ก ๋ฐ๋ ๊ฒ์ ํ์ธํ  ์ ์๋ค. ์คํ๋ง MVC๋ ์ด๋ฐ ํธ์ ๊ธฐ๋ฅ์ ์ ๊ณตํ๋ค. 

**ViewName ์ง์  ๋ฐํ** 
- ๋ทฐ์ ๋ผ๋ฆฌ ์ด๋ฆ์ ๋ฐํํ  ์ ์๋ค. 

**@RequestParam ์ฌ์ฉ**

- ์คํ๋ง์ HTTP ์์ฒญ ํ๋ผ๋ฏธํฐ๋ฅผ `@RequestParam`์ผ๋ก ๋ฐ์ ์ ์๋ค. `@RequestParam("username")`์ `request.getParameter("username")`์ ๊ฑฐ์ ๊ฐ์ ์ฝ๋๋ผ ์๊ฐํ๋ฉด ๋๋ค. ๋ฌผ๋ก , GET ์ฟผ๋ฆฌ ํ๋ผ๋ฏธํฐ, POST Form ๋ฐฉ์์ ๋ชจ๋ ์ง์ํ๋ค. 


**@RequestMapping -> @GetMapping, @PostMapping **
- `@RequestMapping`์ URL๋ง ๋งค์นญํ๋ ๊ฒ์ด ์๋๋ผ, HTTP Method๋ ํจ๊ป ๊ตฌ๋ถํ  ์ ์๋ค. ์๋ฅผ ๋ค์ด์ URL์ด `/new-form`์ด๊ณ , HTTP Method๊ฐ GET์ธ ๊ฒฝ์ฐ๋ฅผ ๋ชจ๋ ๋ง์กฑํ๋ ๋งคํ์ ํ๋ ค๋ฉด ์๋์ ๊ฐ์ด ์ฒ๋ฆฌํ๋ค.

```java
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
```

์ด๊ฒ์ `@GetMapping`, `@PostMapping`์ผ๋ก ๋ ํธ๋ฆฌํ๊ฒ ์ฌ์ฉํ  ์ ์๋ค. ์ฐธ๊ณ ๋ก Get, Post, Put, Delete, Patch ๋ชจ๋ ์ ๋ธํ์ด์์ด ์ค๋น๋์ด ์๋ค. 

`@GetMapping` ์ฝ๋๋ฅผ ์ด์ด์ `@RequestMapping` ์ ๋ธํ์ด์์ ๋ด๋ถ์ ๋ชจ์ต์ ํ์ธํด๋ณด๋ฉด ๋์์ด ๋  ๊ฒ์ด๋ค.

**์คํ** 
- ๋ฑ๋ก: http://localhost:8080/springmvc/v3/members/new-form 
- ๋ชฉ๋ก: http://localhost:8080/springmvc/v3/members 
