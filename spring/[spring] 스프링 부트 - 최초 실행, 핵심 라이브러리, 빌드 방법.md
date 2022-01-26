## 스프링 부트 최초 실행

> ### 📖 ✏️ 
> 1. ** TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

```java
@SpringBootApplication
public class HelloSpringApplication {

	public static void main(String[] args) {
		SpringApplication.run(HelloSpringApplication.class, args);
	}

}
```

main()가 가장 먼저 실행되고, `SpringApplication.run()`의 파라미터로 있는 클래스가 실행되면서 스프링부트가 최초 동작한다.

`@SpringBootApplication`가 선언되어 있으므로, 스프링부트 어플리케이션이 실행된다.
내장 톰캣 웹서버를 자체적으로 동작시키면서 스프링부트가 실행된다.

---


## 스프링 부트의 핵심 라이브러리


스프링 부트는 다양한 라이브러리가 여러 단계의 의존성 관계를 맺고 있고 있다.  

스프링 부트는 자동으로 의존성 관리를 해주기 때문에 자동으로 필요한 라이브러리를 모두 가져온다.

스프링 부트의 핵심적인 라이브러리는 아래와 같다.

- spring-boot-starter-web 
	- spring-boot-starter-tomcat: 톰캣 (웹서버) 
	- spring-webmvc: 스프링 웹 MVC 
	  
- spring-boot-starter-thymeleaf: 타임리프 템플릿 엔진(View) 
  
- spring-boot-starter(공통): 스프링 부트 + 스프링 코어 + 로깅 
	- spring-boot 
		- spring-core 
	- spring-boot-starter-logging logback, slf4j 


---



## 스프링부트가 최초 페이지(Welcome Page)를 찾는 방법



>7.1.6. Welcome Page

>Spring Boot supports both static and templated welcome pages. It first looks for an index.html file in the configured static content locations. If one is not found, it then looks for an indext  template. If either is found, it is automatically used as the welcome page of the application.

> 출처: https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-welcome-page

![](https://images.velog.io/images/woply/post/7c314959-d180-444c-a5d7-60a80b39fe05/image.png)
> 이미지 출처: 김영한님 <스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술> 강의 자료

index.html을 통해 컨트롤러로 이동하게 되면 viewResolver 가 리턴 값으로 화면을 찾는다.
스프링 부트 템플릿 엔진은 viewName 맵핑을 지원한다.

---


## 스프링 부트 빌드하고 실행하기


1. ./gradlew build 
2. cd build/libs 
3. java -jar hello-spring-0.0.1-SNAPSHOT.jar 
4. 실행확인 

서버에서 실행시킬 때는 서버에 `java -jar (파일명).jar `를 넣어주고 실행 시키면 된다.

```shellscript
 woply@Jaeminui-MacBookPro  ~/study/hello-spring   master   ll
total 48
-rw-r--r--@ 1 woply  staff   1.4K  1  5 06:47 HELP.md
drwxr-xr-x  6 woply  staff   192B  1  5 16:32 build
-rw-r--r--@ 1 woply  staff   498B  1  5 06:47 build.gradle
drwxr-xr-x@ 3 woply  staff    96B  1  5 06:47 gradle
-rwxr-xr-x@ 1 woply  staff   7.9K  1  5 06:47 gradlew
-rw-r--r--@ 1 woply  staff   2.7K  1  5 06:47 gradlew.bat
drwxr-xr-x  3 woply  staff    96B  1  5 16:58 out
-rw-r--r--@ 1 woply  staff    34B  1  5 06:47 settings.gradle
drwxr-xr-x@ 4 woply  staff   128B  1  5 06:47 src

 ✘ woply@Jaeminui-MacBookPro  ~/study/hello-spring   master  ./gradlew build

 woply@Jaeminui-MacBookPro  ~/study/hello-spring   master  cd build

 woply@Jaeminui-MacBookPro  ~/study/hello-spring/build   master  cd libs

 woply@Jaeminui-MacBookPro  ~/study/hello-spring/build/libs   master  java -jar hello-spring-0.0.1-SNAPSHOT.jar
```

build 폴더를 삭제할 때는  ./gradlew clean 을 실행한다.  

기존 build 파일을 완전히 지우고 다시 빌드할 때는 ./gradlew clean build 를 실행한다.


---

## 스프링 부트의 정적 컨텐츠 기능


스프링 부트 공식 문서에서는 `/static` 경로에 있는 정적 콘텐츠를 사용한다고 한다.

![](https://images.velog.io/images/woply/post/86d78e8f-be53-4740-bb4f-efdf7b3497c0/image.png)

> 출처: https://docs.spring.io/spring-boot/docs/2.3.1.RELEASE/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content


![](https://images.velog.io/images/woply/post/1b4419b1-b001-45ee-9ed0-29de47aca0fc/image.png)
![](https://images.velog.io/images/woply/post/b01a70e9-9bef-4715-89fd-b738e77fb9b2/image.png)

`/statuc` 경로에 html 파일을 넣어두고,  파일명을 url로 넣으면 해당 html파일을 읽어온다

![](https://images.velog.io/images/woply/post/ee1dd236-91bf-4807-a45d-0ad990a21b39/image.png)

스프링 부트 내부에서 처음부터 `/static` 경로를 찾아보지 않는다. 
요청 url에 해당하는 컨트롤러가 있는지 먼저 찾아보고 없을 경우    `/resoutces/static` 에서 `hello-static.html` 파일을 찾는다.