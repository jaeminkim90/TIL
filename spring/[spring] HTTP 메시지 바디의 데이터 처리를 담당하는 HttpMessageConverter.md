> ### π βοΈ 
> **μ΄ κΈμ HTTP λ©μμ§ λ°λμμ λ°μ΄ν°λ₯Ό μ§μ  μ½μ΄μμΌ νκ±°λ, μ λ¬ν΄μΌ ν  λ μ¬μ©λλ `HTTP λ©μμ§ μ»¨λ²ν°`μ λμ μλ¦¬λ₯Ό νμ΅νκ³  μ λ¦¬ν ν¬μ€νμ΄λ€.**

---

# HTTP λ©μμ§ λ°λμ λ°μ΄ν° μ²λ¦¬λ₯Ό λ΄λΉνλ HttpMessageConverter

`HTTP λ©μμ§ μ»¨λ²ν°`λ HTTP APIμ²λΌ HTTP λ©μμ§ λ°λμμ λ°μ΄ν°λ₯Ό μ§μ  μ½μ΄μμΌ νκ±°λ, μ λ¬ν΄μΌ ν  λ λμνλ€. λ·° ννλ¦ΏμΌλ‘ HTMLμ μμ±ν΄μ μλ΅νλ κ²½μ°λ λμνμ§ μλλ€.

`@ResponseBody`μ μ¬μ© μλ¦¬λ₯Ό ν΅ν΄ λ©μμ§ μ»¨λ²ν°κ° μΈμ  μ¬μ©λλμ§ μ΄ν΄λ³΄μ.
![](https://images.velog.io/images/woply/post/d568e127-2f67-4996-9159-ea239dff57c8/image.png)

> **`@ResponseBody`κ° λμνλ κ³Όμ **
> - HTTPμ BODYμ λ¬Έμ λ΄μ©μ μ§μ  λ°ν
> - `viewResolver` λμ μ `HttpMessageConverter`κ° λμ 
> - κΈ°λ³Έ λ¬Έμμ²λ¦¬: `StringHttpMessageConverter` 
> - κΈ°λ³Έ κ°μ²΄μ²λ¦¬: `MappingJackson2HttpMessageConverter` 
> - byte μ²λ¦¬ λ±λ± κΈ°ν μ¬λ¬ `HttpMessageConverter`κ° κΈ°λ³ΈμΌλ‘ λ±λ‘λμ΄ μμ 

> **μ°Έκ³ ** 
> μλ΅μ κ²½μ° ν΄λΌμ΄μΈνΈμ `HTTP Accept` ν€λμ `μλ²μ μ»¨νΈλ‘€λ¬ λ°ν νμ` μ λ³΄λ₯Ό μ‘°ν©ν΄μ `HttpMessageConverter`κ° μ νλλ€.  


μ€νλ§ MVCλ λ€μμ κ²½μ°μ HTTP λ©μμ§ μ»¨λ²ν°λ₯Ό μ μ©νλ€. 
- **HTTP μμ²­**: `@RequestBody`, `HttpEntity(RequestEntity)`
- **HTTP μλ΅**: `@ResponseBody`, `HttpEntity(ResponseEntity)` 


---

## 1. HTTP λ©μμ§ μ»¨λ²ν° μΈν°νμ΄μ€ 

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

HTTP λ©μμ§ μ»¨λ²ν°λ HTTP μμ²­, HTTP μλ΅ λ λ€ μ¬μ©λλ€. 
- `canRead()`, `canWrite()` : λ©μμ§ μ»¨λ²ν°κ° ν΄λΉ ν΄λμ€, λ―Έλμ΄ νμμ μ§μνλμ§ μ²΄ν¬νλ€.
- `read()`, `write()` : λ©μμ§ μ»¨λ²ν°λ₯Ό ν΅ν΄μ λ©μμ§λ₯Ό μ½κ³  μ°λ κΈ°λ₯μ΄λ€. 

---

## 2. HTTP λ©μμ§ μ»¨λ²ν°μ μ’λ₯

μ€νλ§ λΆνΈμ κΈ°λ³Έ λ©μμ§ μ»¨λ²ν°λ μλμ κ°λ€.

> (μΌλΆ μλ΅) 
> 0 = **ByteArrayHttpMessageConverter**
> 1 = **StringHttpMessageConverter**
> 2 = **MappingJackson2HttpMessageConverter**


μ€νλ§ λΆνΈλ λ€μν λ©μμ§ μ»¨λ²ν°λ₯Ό μ κ³΅νλλ°, λμ ν΄λμ€ νμκ³Ό λ―Έλμ΄ νμμ λͺ¨λ μ²΄ν¬νκ³  μ΄λ€ λ©μμ§ μ»¨λ²ν°λ₯Ό μ¬μ©ν μ§ κ²°μ νλ€. μ°μ μμ λλ‘ λ©μμ§ μ»¨λ²ν° μ¬μ©μ κ²ν νλ©΄μ, μ‘°κ±΄μ λ§μ‘±νμ§ μμΌλ©΄ λ€μ μ°μ μμμ λ©μμ§ μ»¨λ²ν°λ‘ λμ΄κ° μ¬μ© μ‘°κ±΄μ κ²ν νλ€.

λͺ κ°μ§ μ£Όμν λ©μμ§ μ»¨λ²ν°λ₯Ό μμλ³΄μ. 


### ByteArrayHttpMessageConverter  : byte[] λ°μ΄ν° μ²λ¦¬

- **ν΄λμ€ νμ**: `byte[]`, **λ―Έλμ΄νμ**: `*/*`  
- μμ²­ μ) `@RequestBody byte[] data`
- μλ΅ μ) `@ResponseBody return byte[]`, μ°κΈ° λ―Έλμ΄νμ `application/octet-stream`
  

### StringHttpMessageConverter  : String λ¬Έμλ‘ λ°μ΄ν° μ²λ¦¬ 

- **ν΄λμ€ νμ**: `String`, **λ―Έλμ΄νμ**: `*/*`  
- μμ²­ μ) `@RequestBody String data`  
- μλ΅ μ) `@ResponseBody return "ok"`,  μ°κΈ° λ―Έλμ΄νμ `text/plain`  

> **StringHttpMessageConverterκ° λμνλ κ²½μ°**
> 
```java
content-type: application/json
@RequestMapping
void hello(@RequetsBody String data) {}
```

### MappingJackson2HttpMessageConverter: application/json 

- **ν΄λμ€ νμ**: `κ°μ²΄` λλ `HashMap`, **λ―Έλμ΄νμ** `application/json` κ΄λ ¨ 
- μμ²­ μ) `@RequestBody HelloData data`
- μλ΅ μ) `@ResponseBody return helloData`,  μ°κΈ° λ―Έλμ΄νμ `application/json` κ΄λ ¨ 

> **MappingJackson2HttpMessageConverterκ° λμνλ κ²½μ°**
> 
```java
content-type: application/json
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```



> **MessageConverterκ° λμνμ§ μλ κ²½μ°**
> 
```java
content-type: text/html
@RequestMapping
void hello(@RequetsBody HelloData data) {}
```

---

## 3. λ©μμ§ μ»¨λ²ν°μ HTTP μμ²­ λ°μ΄ν° μ²λ¦¬ κ³Όμ 
- HTTP μμ²­μ΄ μ€κ³ , μ»¨νΈλ‘€λ¬μμ `@RequestBody`, `HttpEntity` νλΌλ―Έν°λ₯Ό μ¬μ©νλ€. 
- λ©μμ§ μ»¨λ²ν°κ° λ©μμ§λ₯Ό μ½μ μ μλμ§ νμΈνκΈ° μν΄ `canRead()`λ₯Ό νΈμΆνλ€. 
  - λμ ν΄λμ€ νμμ μ§μνλκ°?
  μ) `@RequestBody`μ λμ ν΄λμ€ (`byte[]`, `String`, `HelloData`)
  - HTTP μμ²­μ Content-Type(λ―Έλμ΄ νμ)μ μ§μνλκ°?
  μ) `text/plain`, `pplication/json`, `*/*`  
- canRead()μ‘°κ±΄μ λ§μ‘±νλ©΄ read()  λ₯Ό νΈμΆν΄μ κ°μ²΄ μμ±νκ³ , λ°ννλ€. 
  
---

## 4. λ©μμ§ μ»¨λ²ν°μ HTTP μλ΅ λ°μ΄ν° μ²λ¦¬ κ³Όμ 

- μ»¨νΈλ‘€λ¬μμ `@ResponseBody`, `HttpEntity`λ‘ κ°μ΄ λ°νλλ€. 
- λ©μμ§ μ»¨λ²ν°κ° λ©μμ§λ₯Ό μΈ μ μλμ§ νμΈνκΈ° μν΄ `canWrite()`λ₯Ό νΈμΆνλ€. 
  - λμ ν΄λμ€ νμμ μ§μνλκ°?
  μ) returnμ λμ ν΄λμ€ (`byte[]`, `String`, `HelloData`) 
  - HTTP μμ²­μ Accept λ―Έλμ΄ νμμ μ§μνλκ°? (`@RequestMapping`μ `produces`λ₯Ό μλ―Ένλ€) 
  μ) `text/plain`, `application/json`, `*/*`  
- `canWrite()`μ‘°κ±΄μ λ§μ‘±νλ©΄ `write()`λ₯Ό νΈμΆν΄μ HTTP μλ΅ λ©μμ§ λ°λμ λ°μ΄ν°λ₯Ό μμ±νλ€. 
  

---

## 5. HTTP λ©μμ§ μ»¨λ²ν°μ νΈμΆ νμ΄λ°



`HTTP λ©μμ§ μ»¨λ²ν°`λ μ€νλ§ MVC μ΄λμ―€μμ μ¬μ©λλ κ²μΌκΉ? 

**SpringMVC κ΅¬μ‘°** 
![](https://images.velog.io/images/woply/post/3b3bbeb2-5dea-4b88-a45a-cb4c27d9b3ee/image.png)


`HTTP λ©μμ§ μ»¨λ²ν°`λ μ λΈνμ΄μ κΈ°λ°μ μ»¨νΈλ‘€λ¬μΈ `@RequestMapping`μ μ²λ¦¬νλ νΈλ€λ¬ μ΄λν° `RequestMappingHandlerAdapter(μμ²­ λ§€ν ν¨λ€λ¬ μ΄λν°)`κ° νΈμΆνλ€. 


### RequestMappingHandlerAdapterμ λμ μλ¦¬ 


![](https://images.velog.io/images/woply/post/97c798cb-c761-462f-9cf5-481711e2c5b6/image.png)

μ λΈνμ΄μ κΈ°λ°μ μ»¨νΈλ‘€λ¬λ λ§€μ° λ€μν νλΌλ―Έν°λ₯Ό μ¬μ©ν  μ μλ€. `HttpServletRequest`, `Model`μ λ¬Όλ‘ μ΄κ³ , `@RequestParam`, `@ModelAttribute`κ°μ μ λΈνμ΄μ, κ·Έλ¦¬κ³  `@RequestBody`, `HttpEntity`κ°μ HTTP λ©μμ§λ₯Ό μ²λ¦¬νλ λΆλΆκΉμ§ μ²λ¦¬κ° κ°λ₯ν λ§€μ° μ μ°ν μ»¨νΈλ‘€λ¬λ€.

### ν΅μ¬μ ArgumentResolverμ μλ€

μ¬μ€ μ΄λ κ² λ€μν νλΌλ―Έν°λ₯Ό μ¬μ©ν  μ μκ³ , μ μ°νκ² μ²λ¦¬ν  μ μλ μ΄μ λ `ArgumentResolver`κ° μκΈ° λλ¬Έμ΄λ€. 

μ λΈνμ΄μ κΈ°λ° μ»¨νΈλ‘€λ¬λ₯Ό μ²λ¦¬νλ `RequestMappingHandlerAdaptor`λ `ArgumentResolver`λ₯Ό νΈμΆν΄μ μ»¨νΈλ‘€λ¬(νΈλ€λ¬)κ° νμλ‘ νλ λ€μν νλΌλ―Έν°μ κ°(κ°μ²΄)μ μμ±νλ€. `ArgumentResolver`κ° νμν νλ¦¬λ―Έν°μ κ°μ λ°νν΄μ£Όλ©΄ νΈλ€λ¬(μ»¨νΈλ‘€λ¬)λ₯Ό νΈμΆνλ©΄μ κ°μ λκΈ΄λ€. 

μ€νλ§μ 30κ°κ° λλ `ArgumentResolver`λ₯Ό κΈ°λ³ΈμΌλ‘ μ κ³΅νλ€. μ΄λ€ μ’λ₯μ `ArgumentResolver`κ° μλμ§ νμΈν΄λ³΄μ.

> **μ°Έκ³ **
> κ°λ₯ν νλΌλ―Έν° λͺ©λ‘μ λ€μ κ³΅μ λ©λ΄μΌμμ νμΈν  μ μλ€. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-arguments

`ArgumentResolver`μ μ νν μ΄λ¦μ `HandlerMethodArgumentResolver`λ€. νΈμμ μ€μ¬μ `ArgumentResolver`λΌκ³  λΆλ₯Έλ€. 

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


### ArgumentResolverμ λμ μλ¦¬

`ArgumentResolver`λ `supportsParameter()`λ₯Ό νΈμΆν΄μ ν΄λΉ νλΌλ―Έν°λ₯Ό μ§μνλμ§ νμΈνλ€. κ·Έλ¦¬κ³  μ²λ¦¬κ° κ°λ₯ν  κ²½μ° `resolveArgument()`λ₯Ό νΈμΆν΄μ μ€μ  κ°μ²΄λ₯Ό μμ±νλ€. μμ±λ κ°μ²΄λ `RequestMappingHandlerAdaptor`λ‘ λ°νλκ³ , νΈλ€λ¬(μ»¨νΈλ‘€λ¬) νΈμΆμ μ¬μ©λλ€. 

νμμ λ°λΌ μ§μ  `HandlerMethodArgumentResolver` μΈν°νμ΄μ€λ₯Ό κ΅¬ννμ¬, μνλ `ArgumentResolver`λ₯Ό λ§λ€ μ μλ€. 



### ReturnValueHandlerλ λΉμ·ν κ΅¬μ‘°λ‘ λμνλ€ 

`HandlerMethodReturnValueHandler`λ₯Ό μ€μ¬μ `ReturnValueHandle`μ΄λΌ λΆλ₯Έλ€. ν° λ§₯λ½μ `ArgumentResolver`μ λΉμ·νλ€. λ€λ§, μλ΅ κ°μ λ³ννκ³  μ²λ¦¬νλ€λ μ°¨μ΄κ° μλ€. 

μ»¨νΈλ‘€λ¬μμ StringμΌλ‘ λ·° μ΄λ¦μ λ°νν΄λ, λμν  μ μλ μ΄μ λ `ReturnValueHandler`κ° μκΈ° λλ¬Έμ΄λ€. μ΄λ€ μ’λ₯λ€μ΄ μλμ§ μ΄ν΄λ³΄μ. 

μ€νλ§μ 10μ¬κ°κ° λλ `ReturnValueHandler`λ₯Ό μ§μνλ€. `ModelAndView`, `@ResponseBody`, `HttpEntity`, `String` λ±μ΄ μλ€.

> μ°Έκ³ 
> κ°λ₯ν μλ΅ κ° λͺ©λ‘μ λ€μ κ³΅μ λ©λ΄μΌμμ νμΈν  μ μλ€. 
> https://docs.spring.io/spring-framework/docs/current/reference/html/web.html#mvc-ann-return-types


## HTTP λ©μμ§ μ»¨λ²ν°λ @RequestBodyμ HTTPEntityμ μ²λ¦¬λ₯Ό λλλ€


`HTTP λ©μμ§ μ»¨λ²ν°`λ₯Ό μ¬μ©νλ `@RequestBody`μ `HTTPEntityλ νΈλ€λ¬(μ»¨νΈλ‘€λ¬)` νΈμΆμ νμν νλΌλ―Έν° κ°μ `RequestMappingHandlerAdaptor`μ λ°νν΄μΌ νλ€. `ArgumentResolver`λ μ§μ  νλΌλ―Έν° κ°μ μμ±νμ§ μκ³ , `HTTP λ©μμ§ μ»¨λ²ν°`μ λμμ λ°μ νλΌλ―Έν°μ κ°μ λ°ννλ€.

**HTTP λ©μμ§ μ»¨λ²ν° μμΉ**
![](https://images.velog.io/images/woply/post/ae0545fc-bb7b-4287-86e7-da81b30fb289/image.png)

**μμ²­μ κ²½μ°**

`@RequestBody`λ₯Ό μ²λ¦¬νλ `ArgumentResolver`κ° μκ³ , `HttpEntity`λ₯Ό μ²λ¦¬νλ `ArgumentResolver`κ° μλ€. μ΄ `ArgumentResolver`λ€μ΄ `HTTP λ©μμ§ μ»¨λ²ν°`λ₯Ό μ¬μ©ν΄μ νμν κ°μ²΄λ₯Ό μμ±νλ€.


**μλ΅μ κ²½μ°**

`@ResponseBody`μ `HttpEntity`λ₯Ό μ²λ¦¬νλ `ReturnValueHandler`κ° μλ€. μμ²­κ³Ό λ§μ°¬κ°μ§λ‘ `HTTP λ©μμ§ μ»¨λ²ν°`λ₯Ό νΈμΆν΄μ μλ΅ κ²°κ³Όλ₯Ό λ°λλ€. 

μ€νλ§ MVCλ `@RequestBody`λ `@ResponseBody`κ° μμΌλ©΄, `RequestResponseBodyMethodProcessor(ArgumentResolver)`λ₯Ό μ¬μ©νλ€. `HttpEntity`κ° μμΌλ©΄, `HttpEntityMethodProcessor(ArgumentResolver)`λ₯Ό μ¬μ©νλ€. 

> **μ°Έκ³ ** 
> HttpMessageConverterλ₯Ό κ΅¬νν ν΄λμ€λ₯Ό νλ² νμΈν΄λ³΄μ. 


> **νμ₯** 
> μ€νλ§μ λ€μμ λͺ¨λ μΈν°νμ΄μ€λ‘ μ κ³΅νλ€. λ°λΌμ, νμμ λ°λΌ μΈμ λ μ§ κΈ°λ₯ νμ₯μ΄ κ°λ₯νλ€.
- HandlerMethodArgumentResolver 
- HandlerMethodReturnValueHandler
- HttpMessageConverter 
>
> νμ§λ§ μ€νλ§μ΄ νμν λλΆλΆμ κΈ°λ₯μ μ κ³΅νκΈ° λλ¬Έμ μ€μ  κΈ°λ₯μ νμ₯ν  μΌμ κ±°μ μλ€. κΈ°λ₯ νμ₯μ `WebMvcConfigurer`λ₯Ό μμ λ°μμ μ€νλ§ λΉμΌλ‘ λ±λ‘νλ©΄ λλ€.  


**WebMvcConfigurer νμ₯**
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