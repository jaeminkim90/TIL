> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# 스프링 컨테이너는 BeanDefinition을 이용한다.

스프링은 애노테이션 기반의 자바 코드 설정 방식, xml 파일을 이용한 방식, 직접 설정 파일을 만드는 방식 등 스프링 컨테이너에 Bean을 등록하는 다양한 방식을 지원한다.

스프링이 다양한 설정 방식을 지원할 수 있는 이유는 `BeanDefinition`라는 Bean 메타 정보 인터페이스를 사용하기 때문이다. 스프링 컨테이너는 `BeanDefinition` 타입을 의존하고 있다. 따라서, 설정 방식이 다양해도 스프링 컨테이너에게 `BeanDefinition` 구현체를 전달하면, 메타 데이터를 읽고Bean을 컨테이너에 등록할 수 있다.

이는 클라이언트 객체가 직접 구현체를 생성하지 않고 인터페이스만 의존 관계로 맺고 있으면, 실제 구현체의 생성과 의존 관계 주입은 별도의 `@Configuration` 파일에서 담당하는 것과 같다.

스프링 컨테이너는 `BeanDefinition` 인터페이스의 구현체면 무엇이든 설정 파일로 읽을 수 있다. 구현체가 자바 코드로 만들어진 것인지, xml 파일로 만들어진 것인지는 Bean 등록 과정에 영향을 미치지 않는다.

![](https://images.velog.io/images/woply/post/52b80ed6-866e-4aad-bdb9-68336c5a4def/image.png)

`BeanDefinition` 을 **Bean 설정 메타 정보**라고 한다. 

애노테이션 기반의 자바 설정 파일에서는 `@Bean`이 선언되어 있는 메서드 마다 하나의 `BeanDefinition`이 생성되어 스프링 컨테이너에 전달된다. 

xml 방식의 설정 파일에서는 `<bean>` 하나당 `BeanDefinition`이 생성된어 스프링 컨테이너에 전달된다. 

스프링 컨테이너는 전달받은 `BeanDefinition`의 메타 정보를 기반으로 스프링 Bean을 생성하고 등록한다.

스프링 Bean이 등록되는 방법은 2가지가 있다. 직접 등록이 되는 경우와 팩토리 역할을 Bean이 하는 경우다. 애노테이션 기반의 자바 코드 설정 방식은 팩토리 역할의 빈을 사용하는 경우에 해당한다.

---

## 실제 코드 레벨에서 어떤 일이 일어나고 있을까?

애노테이션 기반의 자바 설정 방식을 사용하는 경우를 보면, `AnnotationConfigApplicationContext`는 `AnnotatedBeanDefinitionReader`를 사용해서 `AppConfig.class`를 읽고 `BeanDefinition`을 생성한다.

xml 방식의 경우, `GenericXmlApplicationContext`가 `XmlBeanDefinitionReader`를 사용해서 `appConfig.xml`설정 정보를 읽고, `BeanDefinition` 을 생성한다. 


  ![](https://images.velog.io/images/woply/post/99cf4c02-fb51-4291-bd3a-56e8f95d4c61/image.png)

`ApplicationContext`의 구현체는 스프링 Bean 등록을 위해 설정 정보가 담긴 `BeanDefinition`을 읽고 컨테이너에 Bean을 등록한다.

---

## 메타 정보를 직접 확인해보자

`getBeanDefinition()`를 이용하면 `ApplicationContext` 구현체가 만드는 `BeanDefinition`의 메타 정보를 확인 할 수 있다.

```java
public class BeanDefinitionTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
    @Test
    @DisplayName("빈 설정 메타정보 확인")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                System.out.println("beanDefinitionName = " + beanDefinitionName +
                        " | beanDefinition = " + beanDefinition);
            }
        }
    }
}
```

이 때, `ApplicationContext`를 반환 타입으로 직접 사용할 수 없다. `getBeanDefinition()` 을 사용할 수 없기 때문이다.

```java
ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class); 
//위와 같은 경우에는 getBeanDefinition를 사용할 수 없다.
```


`BeanDefinition` 메타 정보를 출력하면 아래와 같은 내용을 확인할 수 있다.


![](https://images.velog.io/images/woply/post/b9e5ba32-0d50-41f5-b9fc-6b2c566c6a72/image.png)


BeanDefinition에 포함되어 있는 주요 메타 정보는 아래와 같다.

- `BeanClassName`: 생성할 빈의 클래스 명(자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음) 
- `factoryBeanName`: 팩토리 역할의 빈을 사용할 경우 이름 (ex. appConfig)
- `factoryMethodName`: 빈을 생성할 팩토리 메서드 지정 (ex. memberService)
- `Scope` : 싱글톤(기본값) 
- `lazyInit`: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때 까지 최대한 생성을 지연처리 하는지 여부 
- `InitMethodName`: 빈을 생성하고, 의존관계를 적용한 뒤에 호출되는 초기화 메서드 명 
- `DestroyMethodName`: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명 
- `Constructor arguments`, `Properties`: 의존관계 주입에서 사용한다. (자바 설정 처럼 팩토리 역할의 빈을 사용하면 없음) 

---

## 요약
- 스프링 컨테이너는 `BeanDefinition`을 이용하여 다양한 방식의 설정 정보를 읽고, Bean에 등록할 수 있다.
- `BeanDefinition`은 스프링이 다양한 형태의 설정 정보를 추상화해서 사용하는 방식이다.
