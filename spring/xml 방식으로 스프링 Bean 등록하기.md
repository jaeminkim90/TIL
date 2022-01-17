> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# xml 방식으로 스프링 Bean 등록
![](https://images.velog.io/images/woply/post/5b8dadb5-2a78-46be-b69c-c6e439be3423/image.png)

스프링 컨테이너는 다양한 형식으로 스프링 Bean을 등록할 수 있도록 설계 되어 있다. Bean 등록은 주로 **1) 애노테이션 기반의 자바 코드를 이용한 방식**과 **2) xml 파일을 이용한 방식**을 많이 사용한다. 필요할 경우 **3) 직접 설정 파일을 만드는 방식**도 가능하다. 최근에는 xml 방식을 거의 사용하지 않지만, 레거시 프로젝트를 유지보수해야 할 경우 자주 만날 수 있다.

---


## 1. 애노테이션 기반 자바 코드 설정 방식
애노테이션을 이용한 자바 코드 설정 방식은 현재 가장 많이 사용하는 방식이다. `new AnnotationConfigApplicationContext("설정할 클래스 이름".class)`를 이용하여 설정할 클래스를 파라미터에 넣어주면 된다, 

## 2. xml 설정 방식
최근에는 스프링 부트를 이용한 애플리케이션 개발이 주류를 이루면서 xml 기반의 설정 방식은 잘 사용하지 않는다. 하지만 여전히 많은 레거시 프로젝트가 xml로 되어 있으므로, xml을 이용한 Bean 등록 방식을 알고 있는 것이 좋다.

또한, xml 방식은 컴파일 없이 Bean 설정 정보를 변경할 수 있다는 장점이 있으므로, 필요할 경우 사용할 수 있어야 한다.

애노테이션 기반의 자바 코드 설정 방식에서는 `ApplicationContext`의 구현체로 `AnnotationConfigApplicationContext`를 사용하지만, xml 방식에서는 `GenericXmlApplicationContext` 구현체를 사용한다.


### 2-1. Xml을 이용한 Bean 등록

`ApplicationContext`의 구현체로 `GenericXmlApplicationContext`를 사용한다.

```java
public class XmlAppContext {
    
    @Test
    void xmlAppContext() {
        ApplicationContext ac = new GenericXmlApplicationContext("AppConfig.xml");
        MemberService memberService = ac.getBean("memberService", MemberService.class);
        Assertions.assertThat(memberService).isInstanceOf(MemberService.class);
    }
}
```


XML 파일은 resources 디렉토리에 생성해야 한다.
![](https://images.velog.io/images/woply/post/d3eed80f-d49b-471b-a55d-f944c69e5c37/image.png)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="memberService" class="hello.core.member.MemberServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
    </bean>

    <bean id="memberRepository" class="hello.core.member.MemoryMemberRepository"/>

    <bean id="orderService" class="hello.core.order.OrderServiceImpl">
        <constructor-arg name="memberRepository" ref="memberRepository"/>
        <constructor-arg name="discountPolicy" ref="discountPolicy"/>
    </bean>
    
    <bean id="discountPolicy" class="hello.core.discount.RateDiscountPolicy"/>

</beans>
```

> `xml` 기반의 `appConfig.xml` 스프링 설정 파일과 자바 코드로 된 `AppConfig.java` 설정 클래스를 비교해보면 구조가 거의 비슷하다. 형식만 조금 다를 뿐이다.