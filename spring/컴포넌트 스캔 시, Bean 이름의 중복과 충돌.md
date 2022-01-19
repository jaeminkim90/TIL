
> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---


# 컴포넌트 스캔 시, Bean 이름의 중복과 충돌

컴포넌트 스캔에서 같은 Bean 이름을 등록하면 어떻게 될까? 아래 두 가지 상황은 다른 결과가 나타난다.

- 1. 자동빈등록vs자동빈등록
- 2. 수동빈등록vs자동빈등록 

## 자동 Bean 등록 vs 자동 Bean 등록
컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다. 오류 메시지는 아래와 같다.

```java\
ConflictingBeanDefinitionException 예외 발생!
```

자동 빈 등록의 경우, 중복된 이름을 가진 Bean 중 어느 하나를 임의로 선택하여 등록하지 않는다. 에러를 발생시키고, 이름의 중복을 해결되어야 정상적으로 Bean 등록이 이루어진다.


## 수동 Bean 등록 vs 자동 Bean 등록 
수동 Bean 등록과 자동 Bean 등록 중 Bean 이름이 중복되어 충돌한다면, 수동 Bean 등록에 우선권이 있다. 수동 Bean이 자동 Bean을 오버라이딩하는 방식으로 등록된다.

아래 테스트를 통해 살펴보자.

```java
@Component
public class MemoryMemberRepository implements MemberRepository {}
```


```java
@Configuration
@ComponentScan(
        basePackages = "hello.core",
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
)
public class AutoAppConfig {
    // 중복 이름을 가진 Bean을 수동으로 등록
    @Bean(name = "memoryMemberRepository")
    MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```


테스트 코드를 통해 스프링 컨테이너를 생성하면, 중복된 이름이 존재하지만 테스트는 통과한다.  그리고 아래와 같은 로그를 확인할 수 있다. `memoryMemberRepository`의 이름이 동일한 Bean이 존재하여 오버라이드되었다는 메시지다.

```
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing 
```

> 참고
최근 스프링 부트에서는 수동 Bean 등록과 자동 Bean 등록이 충돌나면 오류가 발생하도록 기본 값을 바꾸었다.

> 수동 Bean 등록과 자동 Bean 등록 오류시 스프링 부트 에러 
```
Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true 
```