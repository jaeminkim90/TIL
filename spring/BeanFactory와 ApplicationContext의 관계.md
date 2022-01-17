> ### 📖 ✏️ 
> 1. **TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---
# BeanFactory와 ApplicationContext의 관계

## 1. BeanFactory 

BeanFactory는 스프링 컨테이너의 최상위 인터페이스다. 모든 스프링 Bean을 관리하고 조회하는 역할을 담당한다. 스프링 Bean에 등록된 객체를 조회하는 `getBean()`은 	`getBean()`가 제공하는 메서드이다. ApplicationContext를 통해 `getBean()`를 사용할 수 있는 이유는 `BeanFactory`를 상속받았기 때문이다. 스프링 Bean과 관련된 대부분의 기능은 `BeanFactory`가 제공한다.

![](https://images.velog.io/images/woply/post/10901a8c-f9b2-4a8a-93be-5319d45fe1e9/image.png)


## 2. ApplicationContext 

`ApplicationContext` 객체는 `BeanFactory`의 기능을 모두 상속받아 제공한다. 대부분의 Bean 관련 기능은 `BeanFactory`가 제공하지만, `ApplicationContext`는 애플리케이션을 개발할 때 필요한 다양한 부가 기능을 담당한다.


### 2-1. ApplicatonContext가 제공하는 부가기능 

![](https://images.velog.io/images/woply/post/926d3c12-7700-4a25-b1f5-46c17bb25b02/image.png)

**1) 메시지 소스를 활용한 국제화 기능**
- 클라이언트의 언어를 인식하여 보여지는 언어를 다르게 제공할 수 있다.

**2) 환경변수**
- 개발 환경(로컬, 테스트 서버, 실제 운영 환경 등)에 따라 필요한 환경 변수를 다르게 설정할 수 있다. 
- ex) 환경에 따라 연결하는 데이터 베이스가 다르다. 
  
**3) 애플리케이션 이벤트** 
- 이벤트를 발행하고 구독하는 모델을 편리하게 지원할 수 있다.

**4) 편리한 리소스 조회** 
- 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회할 수 있다. 

---

## 3. 요약

- `ApplicationContext`는 `BeanFactory`의 기능을 상속받아, 대부분의 Bean 관리기능을 제공한다. 
- `ApplicationContext`만의 고유한 기능으로는 애플리케이션 개발에 필요한 다양한 부가 기능을 제공한다. 
- `BeanFactory`를 직접 사용할 일은 거의 없다. 부가기능이 포함된 `ApplicationContext`를 주로 사용한다. 
- `BeanFactory`과 `ApplicationContext` 모두 스프링 컨테이너라고 부른다. 하지만 일반적으로는 `BeanFactory`를 상속받은 `ApplicationContext`를 지칭하는 경우가 많다.