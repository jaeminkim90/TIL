> ### 📖 ✏️ 
> **이 글은 HTTP 메시지 바디에서 데이터를 직접 읽어와야 하거나, 전달해야 할 때 사용되는 `HTTP 메시지 컨버터`의 동작 원리를 학습하고 정리한 포스팅이다.**

---

# HTTP 메시지 바디의 데이터 처리를 담당하는 HttpMessageConverter

`HTTP 메시지 컨버터`는 HTTP API처럼 HTTP 메시지 바디에서 데이터를 직접 읽어와야 하거나, 전달해야 할 때 동작한다. 뷰 템플릿으로 HTML을 생성해서 응답하는 경우는 동작하지 않는다.

`@ResponseBody`의 사용 원리를 통해 메시지 컨버터가 언제 사용되는지 살펴보자.
![](https://images.velog.io/images/woply/post/d568e127-2f67-4996-9159-ea239dff57c8/image.png)

> **`@ResponseBody`가 동작하는 과정**
> - HTTP의 BODY에 문자 내용을 직접 반환
> - `viewResolver` 대신에 `HttpMessageConverter`가 동작 
> - 기본 문자처리: `StringHttpMessageConverter` 
> - 기본 객체처리: `MappingJackson2HttpMessageConverter` 
> - byte 처리 등등 기타 여러 `HttpMessageConverter`가 기본으로 등록되어 있음 

> **참고** 
> 응답의 경우 클라이언트의 `HTTP Accept` 헤더와 `서버의 컨트롤러 반환 타입` 정보를 조합해서 `HttpMessageConverter`가 선택된다.  


스프링 MVC는 다음의 경우에 HTTP 메시지 컨버터를 적용한다. 
- **HTTP 요청**: `@RequestBody`, `HttpEntity(RequestEntity)`
- **HTTP 응답**: `@ResponseBody`, `HttpEntity(ResponseEntity)` 


---

## 1. HTTP 메시지 컨버터 인터페이스 

`org.springframework.http.converter.HttpMessageConverter`

```java
package org.springframework.http.converter;
    public interface HttpMessageConverter<T> {

      boolean canRead(Class<?> clazz, @Nullable MediaType mediaType);
      boolean canWrite(Class<?> clazz, @Nullable MediaType mediaType);

      List<MediaType> getSupportedMediaTypes();

      T read(Class<? extends T> clazz, HttpInputMessage inputMessage)
              throws IOException, HttpMessageNotReadableException;

      void write(T t, @Nullable MediaType contentType, HttpOutputMessage outputMessage)
              throws IOException, HttpMessageNotWritableException;
} 
```

HTTP 메시지 컨버터는 HTTP 요청, HTTP 응답 둘 다 사용된다. 
- `canRead()`, `canWrite()` : 메시지 컨버터가 해당 클래스, 미디어 타입을 지원하는지 체크한다.
- `read()`, `write()` : 메시지 컨버터를 통해서 메시지를 읽고 쓰는 기능이다. 

---

## 2. HTTP 메시지 컨버터의 종류

스프링 부트의 기본 메시지 컨버터는 아래와 같다.

> (일부 생략) 
> 0 = **ByteArrayHttpMessageConverter**
> 1 = **StringHttpMessageConverter**
> 2 = **MappingJackson2HttpMessageConverter**


스프링 부트는 다양한 메시지 컨버터를 제공하는데, 대상 클래스 타입과 미디어 타입을 모두 체크하고 어떤 메시지 컨버터를 사용할지 결정한다. 우선순위 대로 메시지 컨버터 사용을 검토하면서, 조건에 만족하지 않으면 다음 우선순위의 메시지 컨버터로 넘어가 사용 조건을 검토한다.

몇 가지 주요한 메시지 컨버터를 알아보자. 


### ByteArrayHttpMessageConverter  : byte[] 데이터 처리

- **클래스 타입**: `byte[]`, **미디어타입**: `*/*`  
- 요청 예) `@RequestBody byte[] data`
- 응답 예) `@ResponseBody return byte[]`, 쓰기 미디어타입 `application/octet-stream`
  

### StringHttpMessageConverter  : String 문자로 데이터 처리 

- **클래스 타입**: `String`, **미디어타입**: `*/*`  
- 요청 예) `@RequestBody String data`  
- 응답 예) `@ResponseBody return "ok"`,  쓰기 미디어타입 `text/plain`  

> **StringHttpMessageConverter가 동작하는 경우**
> 
```java
content-type: application/json
@RequestMapping
void hello(@RequetsBody String data) {}
```

### MappingJackson2HttpMessageConverter: application/json 

- **클래스 타입**: `객체` 또는 `HashMap`, **미디어타입** `application/json` 관련 
- 요청 예) `@RequestBody HelloData data`
- 응답 예) `@ResponseBody return helloData`,  쓰기 미디어타입 `application/json` 관련 

> **MappingJackson2HttpMessageConverter가 동작하는 경우**
> 
```java
content-type: application/json
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```



> **MessageConverter가 동작하지 않는 경우**
> 
```java
content-type: text/html
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```

---

## 3. 메시지 컨버터의 HTTP 요청 데이터 처리 과정
- HTTP 요청이 오고, 컨트롤러에서 `@RequestBody`, `HttpEntity` 파라미터를 사용한다. 
- 메시지 컨버터가 메시지를 읽을 수 있는지 확인하기 위해 `canRead()`를 호출한다. 
  - 대상 클래스 타입을 지원하는가?
  예) `@RequestBody`의 대상 클래스 (`byte[]`, `String`, `HelloData`)
  - HTTP 요청의 Content-Type(미디어 타입)을 지원하는가?
  예) `text/plain`, `pplication/json`, `*/*`  
- canRead()조건을 만족하면 read()  를 호출해서 객체 생성하고, 반환한다. 
  
---

## 4. 메시지 컨버터의 HTTP 응답 데이터 처리 과정

- 컨트롤러에서 `@ResponseBody`, `HttpEntity`로 값이 반환된다. 
- 메시지 컨버터가 메시지를 쓸 수 있는지 확인하기 위해 `canWrite()`를 호출한다. 
  - 대상 클래스 타입을 지원하는가?
  예) return의 대상 클래스 (`byte[]`, `String`, `HelloData`) 
  - HTTP 요청의 Accept 미디어 타입을 지원하는가? (`@RequestMapping`의 `produces`를 의미한다) 
  예) `text/plain`, `application/json`, `*/*`  
- `canWrite()`조건을 만족하면 `write()`를 호출해서 HTTP 응답 메시지 바디에 데이터를 생성한다. 
  

---

## 5. HTTP 메시지 컨버터의 호출 타이밍



`HTTP 메시지 컨버터`는 스프링 MVC 어디쯤에서 사용되는 것일까? 

**SpringMVC 구조** 
![](https://images.velog.io/images/woply/post/3b3bbeb2-5dea-4b88-a45a-cb4c27d9b3ee/image.png)


`HTTP 메시지 컨버터`는 애노테이션 기반의 컨트롤러인 `@RequestMapping`을 처리하는 핸들러 어댑터 `RequestMappingHandlerAdapter(요청 매핑 헨들러 어뎁터)`가 호출한다. 


### RequestMappingHandlerAdapter의 동작 원리 


![](https://images.velog.io/images/woply/post/97c798cb-c761-462f-9cf5-481711e2c5b6/image.png)

애노테이션 기반의 컨트롤러는 매우 다양한 파라미터를 사용할 수 있다. `HttpServletRequest`, `Model`은 물론이고, `@RequestParam`, `@ModelAttribute`같은 애노테이션, 그리고 `@RequestBody`, `HttpEntity`같은 HTTP 메시지를 처리하는 부분까지 처리가 가능한 매우 유연한 컨트롤러다.

### 핵심은 ArgumentResolver에 있다

사실 이렇게 다양한 파라미터를 사용할 수 있고, 유연하게 처리할 수 있는 이유도 `ArgumentResolver`가 있기 때문이다. 

애노테이션 기반 컨트롤러를 처리하는 `RequestMappingHandlerAdaptor`는 `ArgumentResolver`를 호출해서 컨트롤러(핸들러)가 필요로 하는 다양한 파라미터의 값(객체)을 생성한다. `ArgumentResolver`가 필요한 파리미터의 값을 반환해주면 핸들러(컨트롤러)를 호출하면서 값을 넘긴다. 

스프링은 30개가 넘는 `ArgumentResolver`를 기본으로 제공한다. 어떤 종류의 `ArgumentResolver`가 있는지 확인해보자.

> **참고**
> 가능한 파라미터 목록은 다음 공식 메뉴얼에서 확인할 수 있다. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments

`ArgumentResolver`의 정확한 이름은 `HandlerMethodArgumentResolver`다. 편의상 줄여서 `ArgumentResolver`라고 부른다. 

```java
public interface HandlerMethodArgumentResolver { 
    boolean supportsParameter(MethodParameter parameter);

    @Nullable 
    Object resolveArgument(MethodParameter parameter, 
                           @Nullable ModelAndViewContainer mavContainer,
                           NativeWebRequest webRequest, 
                           @Nullable WebDataBinderFactory binderFactory)
                           throws Exception;
}  
```


### ArgumentResolver의 동작 원리

`ArgumentResolver`는 `supportsParameter()`를 호출해서 해당 파라미터를 지원하는지 확인한다. 그리고 처리가 가능할 경우 `resolveArgument()`를 호출해서 실제 객체를 생성한다. 생성된 객체는 `RequestMappingHandlerAdaptor`로 반환되고, 핸들러(컨트롤러) 호출에 사용된다. 

필요에 따라 직접 `HandlerMethodArgumentResolver` 인터페이스를 구현하여, 원하는 `ArgumentResolver`를 만들 수 있다. 



### ReturnValueHandler도 비슷한 구조로 동작한다 

`HandlerMethodReturnValueHandler`를 줄여서 `ReturnValueHandle`이라 부른다. 큰 맥락은 `ArgumentResolver`와 비슷하다. 다만, 응답 값을 변환하고 처리한다는 차이가 있다. 

컨트롤러에서 String으로 뷰 이름을 반환해도, 동작할 수 있는 이유도 `ReturnValueHandler`가 있기 때문이다. 어떤 종류들이 있는지 살펴보자. 

스프링은 10여개가 넘는 `ReturnValueHandler`를 지원한다. `ModelAndView`, `@ResponseBody`, `HttpEntity`, `String` 등이 있다.

> 참고
> 가능한 응답 값 목록은 다음 공식 메뉴얼에서 확인할 수 있다. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types


## HTTP 메시지 컨버터는 @RequestBody와 HTTPEntity의 처리를 돕는다


`HTTP 메시지 컨버터`를 사용하는 `@RequestBody`와 `HTTPEntity도 핸들러(컨트롤러)` 호출에 필요한 파라미터 값을 `RequestMappingHandlerAdaptor`에 반환해야 한다. `ArgumentResolver`는 직접 파라미터 값을 생성하지 않고, `HTTP 메시지 컨버터`의 도움을 받아 파라미터의 값을 반환한다.

**HTTP 메시지 컨버터 위치**
![](https://images.velog.io/images/woply/post/ae0545fc-bb7b-4287-86e7-da81b30fb289/image.png)

**요청의 경우**

`@RequestBody`를 처리하는 `ArgumentResolver`가 있고, `HttpEntity`를 처리하는 `ArgumentResolver`가 있다. 이 `ArgumentResolver`들이 `HTTP 메시지 컨버터`를 사용해서 필요한 객체를 생성한다.


**응답의 경우**

`@ResponseBody`와 `HttpEntity`를 처리하는 `ReturnValueHandler`가 있다. 요청과 마찬가지로 `HTTP 메시지 컨버터`를 호출해서 응답 결과를 받는다. 

스프링 MVC는 `@RequestBody`나 `@ResponseBody`가 있으면, `RequestResponseBodyMethodProcessor(ArgumentResolver)`를 사용한다. `HttpEntity`가 있으면, `HttpEntityMethodProcessor(ArgumentResolver)`를 사용한다. 

> **참고** 
> HttpMessageConverter를 구현한 클래스를 한번 확인해보자. 


> **확장** 
> 스프링은 다음을 모두 인터페이스로 제공한다. 따라서, 필요에 따라 언제든지 기능 확장이 가능하다.
- HandlerMethodArgumentResolver 
- HandlerMethodReturnValueHandler
- HttpMessageConverter 
>
> 하지만 스프링이 필요한 대부분의 기능을 제공하기 때문에 실제 기능을 확장할 일은 거의 없다. 기능 확장시 `WebMvcConfigurer`를 상속 받아서 스프링 빈으로 등록하면 된다.  


**WebMvcConfigurer 확장**
```java
@Bean
public WebMvcConfigurer webMvcConfigurer() {

    return new WebMvcConfigurer() {
        @Override
        public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
        //... 
        }
        @Override 
        public void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
        //... 
        } 
    }; 
}         
```