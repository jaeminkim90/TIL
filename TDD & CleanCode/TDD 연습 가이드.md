> ## 📖 ✏️ 
> 1. ** TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**<br>
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.** <br>


---

## TDD와 단위 테스트는 다른 개념이다
TDD => TFD(Test First Development) + 리팩토링
테스트 코드를 먼저 구현한다는 점과 리팩토링이 포함되는 개념이다. 
작은 단위로 기능이 추가될 떄 마다 설계를 개선해 나가는 과정이다.

## TDD의 개념
실패하는 테스트를 구현한다
테스트가 성공하도록 프로덕션 코드를 구현한다
프로덕션 코드와 테스트 코드를 리팩토랑헌더

## TDD 원칙
1. 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드(production code)를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.


## TDD 연습 가이드
### 구현할 기능 목록 작성하기
- 가장 먼저 구현할 기능 목록을 작성한 후에 TDD로 도전한다.
- 역량도 중요하지만, 연습이 필요하다.
- TDD가 막막하다면 일단 구현해보면서 도메인 지식을 확보한다. 그리고 다시 기능 설계부터 TDD 방식으로 구현해 본다.
- 기능 목록 중 가장 만만한 녀석부터 TDD로 구현을 시작한다.
- 구현해야 할 기능을 정리한 To Do List는 언제든 변경될 수 있다.


### 클래스를 작성할 때는 인풋과 아웃풋을 정하는 것이 중요하다

**1단계 - Util 성격의 기능이 TDD로 도전하기 좋다**
- ex) 사용자로부터 입력 받는 3개 숫자 예외 처리
	- 1~9의 숫자인가?
	- 중복 값이 있는가?
	- 3자리인가?

**2단계 - 테스트 가능한 부분에 대해 TDD로 도전**
- 위치와 숫자 값이 같은 경우 - 스트라이크
- 위치는 다른데 숫자 값이 같은 경우 - 볼
- 숫자 값이 다른 경우 - 낫싱

### 인스턴스의 필드 변수에 직접 접근하기 보다 객체에 메시지를 보내는 방식을 사용하는 것이 좋다.

> ex)
```java
public BallStatus play(Ball ball) {
    if (ball.matchBallNo(ballNo)) {
        return BallStatus.BALL;
    }
    return BallStatus.NOTHING;
}
private boolean matchBallNo(int ballNo) {
    return this.ballNo == ballNo;
}
```


### 메서드를 분리해서 사용한다

> **분리 전**
```java
public Balls(List<Integer> answers) {
    List<Ball> balls = new ArrayList<>();
    for (int i = 0; i < 3; i++) {
        balls.add(new Ball(i+1, answers.get(i)));
    }
    this.balls = balls;
}
```

> **분리 후**
```java
public Balls(List<Integer> answers) {
    this.balls = mapBall(answers);
}
private List<Ball> mapBall(List<Integer> answers) {
    List<Ball> balls = new ArrayList<>();
    for (int i = 0; i < 3; i++) {
        balls.add(new Ball(i+1, answers.get(i)));
    }
    return balls;
}
```
