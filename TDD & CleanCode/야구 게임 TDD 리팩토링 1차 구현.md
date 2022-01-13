
> 📌 이 글은 'NextStep - 자바 TDD / 클린코드' 강의를 수강하며 학습한 내용을 담고 있습니다. 1차 과제 해당하는 2️⃣ 숫자야구게임을 직접 구현해보며, TDD와 리팩토링을 연습해 본 과정을 기록하였습니다.


---

# 야구 게임 TDD / 리팩토링 - 1차 구현 리뷰

먼저 기능 요구사항을 확인 하였습니다. 요구 사항은 아래와 같았습니다.

## 1. 요구 사항 파악하기
<br>

> ### 1-1. 기능 요구사항
<br>

- 기본적으로 1부터 9까지 서로 다른 수로 이루어진 3자리의 수를 맞추는 게임이다.
- 같은 수가 같은 자리에 있으면 스트라이크, 다른 자리에 있으면 볼, 같은 수가 전혀 없으면 포볼 또는 낫싱이란 힌트를 얻고, 그 힌트를 이용해서 먼저 상대방(컴퓨터)의 수를 맞추면 승리한다.
  - e.g. 상대방(컴퓨터)의 수가 425일 때, 123을 제시한 경우 : 1스트라이크, 456을 제시한 경우 : 1볼 1스트라이크, 789를 제시한 경우 : 낫싱
- 위 숫자 야구 게임에서 상대방의 역할을 컴퓨터가 한다. 컴퓨터는 1에서 9까지 서로 다른 임의의 수 3개를 선택한다. 게 임 플레이어는 컴퓨터가 생각하고 있는 3개의 숫자를 입력하고, 컴퓨터는 입력한 숫자에 대한 결과를 출력한다.
- 이 같은 과정을 반복해 컴퓨터가 선택한 3개의 숫자를 모두 맞히면 게임이 종료된다. 
- 게임을 종료한 후 게임을 다시 시작하거나 완전히 종료할 수 있다.

<br>

> ### 2-2. 프로그래밍 요구사항
<br>

-  자바 코드 컨벤션을 지키면서 프로그래밍한다.
	- 기본적으로 Google Java Style Guide을 원칙으로 한다.
	- 단, 들여쓰기는 '2 spaces'가 아닌 '4 spaces'로 한다.
- indent(인덴트, 들여쓰기) depth를 2가 넘지 않도록 구현한다. 1까지만 허용한다.
	- 예를 들어 while문 안에 if문이 있으면 들여쓰기는 2이다.
	- 힌트: indent(인덴트, 들여쓰기) depth를 줄이는 좋은 방법은 함수(또는 메소드)를 분리하면 된다.
- else 예약어를 쓰지 않는다.
  - 힌트: if 조건절에서 값을 return하는 방식으로 구현하면 else를 사용하지 않아도 된다.
  - else를 쓰지 말라고 하니 switch/case로 구현하는 경우가 있는데 switch/case도 허용하지 않는다.
- 모든 로직에 단위 테스트를 구현한다. 단, UI(System.out, System.in) 로직은 제외
  - 핵심 로직을 구현하는 코드와 UI를 담당하는 로직을 구분한다.
  - UI 로직을 InputView, ResultView와 같은 클래스를 추가해 분리한다.
- 3항 연산자를 쓰지 않는다.
  - 함수(또는 메소드)가 한 가지 일만 하도록 최대한 작게 만들어라.
  
> ### 3-3. 기능 목록 및 commit 로그 요구사항
<br>

  - 기능을 구현하기 전에 README.md 파일에 구현할 기능 목록을 정리해 추가한다.
  - git의 commit 단위는 앞 단계에서 README.md 파일에 정리한 기능 목록 단위로 추가한다.
    - 참고문서: [AngularJS Commit Message Conventions](https://gist.github.com/stephenparish/9941e89d80e2bc58a153)
  > - AngularJS Commit Message Conventions 중
  - commit message 종류를 아래와 같이 구분
<br>

```
feat (feature)
fix (bug fix)
docs (documentation)
style (formatting, missing semi colons, …)
refactor
test (when adding missing tests)
chore (maintain)
```

---

## 2. 구현하기
<br>

### 2-1. 요구 사항에 맞춰 기능 단위로 작업 나눠보기
<br>

가장 먼저 구현해야 할 기능을 일정한 단위로 나누었습니다. 앞서 문자열 계산기 과제를 통해 학습한 개념을 적용하니 처음보다는 수월하게 느껴졌습니다. 구현해야 할 기능들을 쭉 나열한 후에, 기능들의 목적을 생각해보면서 클래스 단위로 그룹핑을 하였습니다.

기능들이 공통적으로 추구한다고 생각하는 목적에 따라 클래스를 큼직하게 나누었고, 기능의 특징에 따라 메서드 이름을 지어보았습니다. 일단 목적에 따라 클래스를 구성하였지만, 패키지 단위와 클래스 단위의 구분은 어떻게 해야할지 문득 궁금증이 생겼습니다. 지금 상태에서는 패키지까지 분류하여 구상하는 것이 불필요하게 느껴졌기 때문에 일단은 목적에 따라 클래스만 나누었습니다.

아래는 코드 구현에 앞서 기능들의 단위와 목적에 따라 그룹핑한 내용입니다. 1,2,3 각 번호는 기능의 목적이자, 클래스 단위입니다. 하위 내용은 기능 단위로 메서드를 구상한 내용입니다. 3번에 해당하는 게임 진행하기(Play) 클래스는 처음에는 생각을 미쳐 하지 못하였고, 1번과 2번을 모두 구현하고 필요하다는 것을 깨달아 뒤 늦게 추가하였습니다.   
<br>

> **1. 게임 준비하기: `ReadyGame`**
- 사용자에게 3자리 수 입력받기: `inputUserNumber()`
- 컴퓨터의 랜덤 수 3자리 생성하기: `creatRandomNumber()`
- 입력 받은 숫자를 3개의 문자로 쪼갰을 때, 각각 1~9의 범위를 가지고 있으며, 총 3자리가 맞는지 검증: `validateNumber()`

<br>

> **2. 결과 비교하기: `Compare`**
- 동일한 숫자가 있는지 확인: `checkSameNumber()`
- 같은 자리에 있는지 확인: `checkSameLocation()`
- 결과 판단하기: `refereeGame()`

<br>

> **3. 게임 진행하기: `Play`**
- 순차적으로 게임을 진행: `playGame()`
- 게임을 최초 실행하는: `Main()`

---

### 2-2. 게임을 준비하는 ReadyGame 클래스
<br>

> **사용자에게 3자리 수 입력 받기: `inputUserNumber()`**

<br>

실패하는 테스트에서 가정하고 있는 `inputUserNumber()`는 사용자에게 직접 입력을 받기 위해 Scanner를 사용합니다. 테스트 코드에서 Scanner는 실행을 중단시키기 때문에 임의의 3자리 숫자를 입력받은 것으로 가정하여 테스트 코드를 작성했습니다.

검증 메서드를 구현하기 전 작성한 실패하는 테스트 코드입니다. 

```java
@Test
void inputUserNumberTest() {

    String userNumber = "123";// Sacanner를 통해 사용자가 3자리 수를 입력했다고 가정
    assertThat(userNumber.length()).isEqualTo(3);

}
```


프로덕트 코드는 Scanner를 이용하여 사용자에게 직접 입력을 받습니다.

```java
public static String inputUserNumber() {
    String userNumber;
    Scanner scanner;

    scanner = new Scanner(System.in);
    userNumber = scanner.nextLine();

    return userNumber;
}
```

처음 코드를 작성했을 때는 `inputUserNumber()` 실행 중에 검증 메소드를 호출하는 것이 좋을지, 아니면 main 메서드에서 입력받는 메서드와 검증 메서드를 따로 호출하는 것이 좋을 지 잘 판단되지 않았습니다. 

짧은 고민을 통해 메서드는 핵심 기능만 구현하고, 실행은 main()에서 순차적으로 풀어가는 방향을 선택했습니다.(과제를 진행하던 도중에 함께 공부하는 친구로부터 이것이 좋은 방법이 아니라는 것을 배웠습니다. main()에서는 게임을 시작하는 최소한의 메서드 호출만 담당하도록 코드를 수정하였습니다.)

<br>

> **컴퓨터의 랜덤 수 3자리 생성하기: `creatRandomNumber()`**

<br>

실패하는 테스트는 컴퓨터가 3개의 수를 생성하고 List 타입으로 반환하는 `creatRandomNumber()` 구현을 전제로 하였습니다. 테스트는 `List`에 담긴 3개의 숫자가 모두 1~9 범위에 해당하며, 중복되지 않는 숫자인지 확인합니다.

``` java
@Test
void creatRandomNumberTest() {
    List<Integer> computerNumber = ReadyGame.creatRandomNumber();
    SoftAssertions softly = new SoftAssertions();

    assertThat(computerNumber.size()).isEqualTo(3); // 3자리 숫자 확인

    for (Integer number : computerNumber) {
        assertThat(number > 0 && number < 10).isEqualTo(true); // 각 숫자의 범위가 1~10에 해당하는지 확인
        assertThat(Collections.frequency(computerNumber, number)).isEqualTo(1); // 해당 값이 1개만 존재하는지 확인
    }
}
```

다음으로 테스트를 통과할 수 있는 프로덕트 코드를 구현하였습니다. 프로덕트 코드는 1~9 범위 내에서 서로 다른 수 3개를 생성하는 기능을 가지며, 반환 타입은 `List<Integer>`으로 구현하였습니다.

```java
public static List<Integer> creatRandomNumber() {

    List<Integer> computerNumber = new ArrayList<>();
    Random random = new Random();

    for (int i = 0; i < 3; i++) {
        int randomNumber = random.nextInt(10);
        if (!(computerNumber.contains(randomNumber + 1))) {
            computerNumber.add(randomNumber + 1);
            continue;
        }
        i--;
    }
    return computerNumber;
}
```

<br>

> **문자열로 받은 숫자를 3개의 정수로 쪼개기: `splitNumber()`**

<br>

실패하는 테스트는 사용자가 입력한 3자리의 연속된 문자열을 `Integer` 타입의 `List` 객체로 변환하는 `splitNumber()` 구현을 가정했습니다.

```java
@Test
void splitNumberTest() {
    String userNumber = "456";
    List<Integer> userNumberList = ReadyGame.splitNumber(userNumber);

    assertThat(userNumberList.toString()).isEqualTo("[4, 5, 6]");
    
}
```

실제 코드는 2단계로 로직이 나눠집니다. 문자열을 문자 배열로 변환하고, 문자 배열을 다시 List에 넣었습니다.

```java
public static List<Integer> splitNumber(String userNumber) {

    List<Integer> userNumberList = new ArrayList<>();
    String[] splitUserNumber = userNumber.split("");
    for (String eachNumber :splitUserNumber ){
        userNumberList.add(Integer.parseInt(eachNumber));
    }
    return userNumberList;
}
```
<br>

> **사용자와 컴퓨터의 숫자가 조건에 부합하는지 확인하는 메서드: `validateNumber()`**

<br>

처음 작성했던 테스트 코드는 간단했습니다. `List` 객체를 프로적트 코드에 전달하면 검증 여부를 `true`나 `false`로 확인하는 방식으로 테스트를 진행하려고 생각했습니다. 프로덕트 코드에서 List 객체의 사이즈(숫자의 총 갯수)가 3개 인지, 각 숫자가 1~9 범위에 해당하는지, 중복 값은 없는지 확인하기 때문에 true를 전달 받으면 검증을 통과하는 것으로 생각했습니다.

하지만 테스트의 의미가 프로덕트 코드의 기능이 정상적으로 작동하는지 확인하는 것에 가깝다는 생각에 방식을 조금 바꿨습니다. 프로덕트 코드의 실행 결과와 테스트 코드에서 별도로 검증한 결과가 일치하는지 확인하는 방법으로 테스트 코드를 변경하였습니다. 

이에 대한 의문이 들어 친구와 상의를 하면서 또 다른 배움을 얻을 수 있었습니다. 이런 경우엔 프로덕트 코드의 더 작은 단위의 메서드로 리팩토링하면 테스트를 더 명확하게 해볼 수 있다는 것을 알게 되었습니다.


```java
@Test
void validateNumberTest() {
    List<Integer> userNumberList = Arrays.asList(1, 2, 3);
    boolean validateResult = ReadyGame.validateNumber(userNumberList);

    assertThat(validateResult).isEqualTo(true); // 테스트 1: 프로덕트 코드에 기대하는 결과가 true인지 확인

    assertThat(userNumberList.size()).isEqualTo(3); // 테스트 2: 3자리 숫자 확인

    for (Integer number : userNumberList) {
        assertThat(number > 0 && number < 10).isEqualTo(true); // 각 숫자의 범위가 1~10에 해당하는지 확인
        assertThat(Collections.frequency(userNumberList, number)).isEqualTo(1); // 해당 값이 1개만 존재
    }
}
```


사용자의 입력 숫자를 검증하는 `validateNumberTest()`는 앞서 작성한 테스트 코드와 크게 다르지 않습니다. 3자리의 숫자인지 확인하고, 각 숫자의 범위가 1~10인지, 중복된 값은 없는지 확인합니다. 

```java
public static boolean validateNumber(List<Integer> userNumberList) {
    boolean verificationResult = false;

    if (!(userNumberList.size() == 3)) {
        return verificationResult;
    }

    for (Integer number : userNumberList) {
        if (!(0 < number && number < 10)) {
            return verificationResult;
        }

        if (!(Collections.frequency(userNumberList, number) == 1)) {
            return verifica**텍스트**tionResult;
        }
    }
    return true;
}
```


여기까지는 게임을 진행하기 위해 사용자의 숫자와 컴퓨터의 숫자를 준비하고 검증하는 과정에 해당합니다.

준비 과정 구현을 마치고 판별 과정 구현에 들어가기 전에 함께 공부하는 친구들과 TDD에 대해 간단하게 대화를 나누었습니다. `given / when / then` 구조를 나누어서 테스트 코드를 진행하라는 조언을 들었습니다. 본격적인 게임 구현에 들어가면서는 조언에 따라 `given / when / then` 구분 방식을 적용하여 진행하였습니다.
 
본격적인 게임 진행 과정에 앞서 구현해야 할 코드의 흐름을 생각해 보았습니다. 야구 게임은 사용자가 입력한 3개의 숫자가 컴퓨터의 숫자와 자릿수까지 모두 일치해야 종료됩니다. 숫자만 일치할 경우 볼이 되고, 자릿수까지 일치할 경우 스트라이크입니다. 따라서 숫자 자체가 몇 개나 동일한지 확인하는 것이 우선이라는 생각이 들었습니다. 그 후 자릿수까지 일치하는 경우를 확인하면 `스트라이크-볼-낫싱` 여부를 쉽게 판단할 수 있기 때문입니다.

우선 두 숫자 List를 대조해보면서 `동일한 숫자가 존재하는지 확인`하고, `동일한 자릿수를 확인`하고, `결과를 종합하여 판정`하는 순서로 작업 과정을 구상하였습니다.

---


### 2-3. 숫자를 비교하여 게임의 결과를 확인하는 Compare 클래스   

<br>

> **동일한 숫자가 있는지 확인하는 메서드 `checkSameNumber()`**

<br>


실패하는 테스트를 먼저 구현하였습니다.

- `given`: 테스트 전제 조건에 해당하는 `given`은 서로 다른 숫자 List 객체 2개가 제공되어야 합니다. 사용자의 입력 숫자 3자리와 컴퓨터가 생성한 핸덤수 3자리에 해당합니다.
- `when`: 일치하는 숫자의 갯수를 확인해주는 메서드 `checkSameNumber()`를 실행합니다. 메서드는 일치하는 숫자의 갯수를 반환해 줍니다.
- `then`: 일치하는 숫자가 예상한 숫자와 동일한지 확인합니다.

```java
@Test
void 동일한_숫자_갯수_확인() throws Exception {
    // given
    // 숫자 list 2개가 주어진다
    List<Integer> userNumberList = Arrays.asList(1, 3, 5);
    List<Integer> computerNumberList = Arrays.asList(5, 2, 3);

    // when
    // 일치하는 숫자를 확인해주는 메서드를 실행한다
    int sameNumberAmount = compare.checkSameNumber(userNumberList, computerNumberList);

    // then
    // 일치하는 숫자를 확인한다.
    assertThat(sameNumberAmount).isEqualTo(2);
}
```

프로덕트 코드 `checkSameNumber()`는 두 개의 List를 순차적으로 비교하여 같은 숫자의 갯수가 몇 개인지 확인합니다. 인덴트 1을 맞추기 위해 실제 확인하는 로직은 `sameNumberCount()`로 분리하였다.

```java
public int checkSameNumber(List<Integer> userNumberList, List<Integer> computerNumberList) {

    int sameNumberCount =0;
    for(int i = 0; i < userNumberList.size(); i++ ) {
        sameNumberCount += sameNumberCount(userNumberList,computerNumberList.get(i));
    }
    return sameNumberCount;
}


private int sameNumberCount(List userNumberList, int compareNumber) {
    return Collections.frequency(userNumberList,compareNumber);
}
```

<br>

> **같은 자리에 있는지 확인하는 메서드 `checkSameLocation()`**

<br>

실패하는 테스트를 먼저 구현하였습니다. 같은 숫자가 같은 자릿수에 위치하는지 확인해주는 `checkSameLocation()`를 구현하였다고 가정했습니다.
 
```java
@Test
void 동일한_숫자의위치_갯수_확인() throws Exception {

    // given
    // 3개의 숫자로 구성된 List 객체 2개가 주어진다.
    List<Integer> userNumberList = Arrays.asList(1, 2, 5);
    List<Integer> computerNumberList = Arrays.asList(1, 5, 2);

    // when
    // 두 List를 비교하여 동일한 위치에 동일한 수를 가진 숫자가 몇 개인지 확인하는 메서드를 실행한다
    int sameLocationNumberAmount = compare.checkSameLocation(userNumberList, computerNumberList);

    // then
    // 일치하는 숫자가 정확한지 확인한다.
    assertThat(sameLocationNumberAmount).isEqualTo(2);
}
```

이어서 프로덕트 코드를 구현했습니다. 반복문을 통해 각각의 숫자 List 객체를 순회하며 `자릿수가 같은 숫자 갯수를 카운트`하는 메서드와 `실제 비교를 담당`하는 메서드를 분리하였습니다.

```java
public int checkSameLocation(List<Integer> userNumberList, List<Integer> computerNumberList) {

    int sameLocationNumberCount = 0;

    for (int i = 0; i < userNumberList.size(); i++) {
        sameLocationNumberCount += CountSameLocationNumber((userNumberList.get(i)), computerNumberList.get(i));
    }
    return sameLocationNumberCount;
}

private int CountSameLocationNumber(int userNumber, int computerNumber) {
    if (userNumber == computerNumber) {
        return 1;
    }
    return 0;
}
```

<br>

> **게임 결과를 확인하는 메서드 `refereeGame()`**

<br>

먼저 `checkSameNumber()`를 통해 파악된 동일한 숫자의 갯수를 '볼'의 갯수로 파악하고, 이 중 `checkSameLocation()`를 통해 동일한 위치가 파악된 숫자는 '스트라이크' 재분류하였습니다. 테스트 코드는 `동일한 숫자의 갯수`와 `동일한 자릿수의 갯수`를 통해 게임의 결과가 정상적으로 반환되는지 확인합니다. 실패하는 테스트 코드는 게임의 결과를 '볼'과 '스트라이크' 갯수로 환산하는 메서드인 `refereeGame()`를 구현하였다고 가정하였습니다.

```java
@Test
void 게임_결과_확인() throws Exception {

    // given
    // '동일한 숫자의 갯수'와 '위치가 동일한 숫자의 갯수'가 주어진다.

    int sameNumberAmount = 3;
    int sameLocationNumberAmount = 1;

    // when
    // 볼과 스트라이크로 환산하는 메서드를 통해 결과를 받는다.
    String gameResult = compare.refereeGame(sameNumberAmount, sameLocationNumberAmount);

    // then
    // 게임 결과가 정확한지 확인한다.
    assertThat(gameResult).isEqualTo("1 스트라이크 2 볼");
    //assertThat(gameResult).isEqualTo("낫싱");
}
```

이어서 프로덕트 코드를 구현했습니다. 일치하는 숫자나 자릿수가 전혀 없는 경우 낫싱, 숫자만 같은 경우 볼, 자릿수까지 같은 경우 스트라이크로 처리한 결과를 반환합니다. 숫자와 자릿수가 모두 일치하는 3스트라이크가 되면 아웃을 반환하고 경기가 종료됩니다.

```
public String refereeGame(int sameNumberAmount, int sameLocationNumberAmount) {

    int ball = sameNumberAmount - sameLocationNumberAmount;
    int strike = sameLocationNumberAmount;

    if (ball ==0 && strike ==0) {
    return "낫싱";
    }

    if (ball ==3 && strike ==3) {
        return "아웃!";
    }

    return strike + " 스트라이크 "+ball+" 볼";
}
```

---

<br>

### 2-4. 게임을 진행하는 Play 클래스

<br>

마지막으로 전체 게임이 진행되는 play 클래스를 만들고 연속적인 플레이가 가능하도록 구현하였습니다.

```java
import java.util.List;
import java.util.Scanner;

public class Play {

    public void playGame() {

        ReadyGame readyGame = new ReadyGame();
        Compare compare = new Compare();
        boolean gameState = true;


        // 1. 게임 준비
        System.out.println("숫자 야구를 시작합니다.");
        List<Integer> computerNumber = readyGame.creatRandomNumber();
        System.out.println(computerNumber);

        while (gameState) {
            System.out.println("숫자를 입력하세요.\n>");
            List<Integer> userNumber = readyGame.splitNumber(readyGame.inputUserNumber());

            // 사용자의 입력 숫자가 잘못된 경우 재입력 받는다.
            retryUserNumber(userNumber);

            // 2. 게임 진행
            int sameNumberCount = compare.checkSameNumber(userNumber, computerNumber);
            int sameLocationNumberCount = compare.checkSameLocation(userNumber, computerNumber);

            // 게임의 결과를 확인한다
            String gameResult = compare.refereeGame(sameNumberCount, sameLocationNumberCount);
            System.out.println(gameResult);

            // 숫자를 모두 맞혔을 경우, 사용자의 입력을 반복하는 while 값을 false로 변경한다.
            gameState = stopGameLoop(sameNumberCount, sameLocationNumberCount);

            // 숫자를 모두 맞혔을 경우, 게임 재개 여부를 확인한다.
            if (askRegame(sameNumberCount, sameLocationNumberCount) == 1) {
                playGame();
            }
        }
    }

    private List<Integer> retryUserNumber(List<Integer> userNumber) {

        ReadyGame readyGame = new ReadyGame();
        List<Integer> retryUserNumber = userNumber;

        while (!(readyGame.validateNumber(retryUserNumber))) {
            System.out.println("잘못된 숫자입니다. 숫자를 다시 입력하세요.\n>");
            List<Integer> tempUserNumber = readyGame.splitNumber(readyGame.inputUserNumber());
            retryUserNumber = tempUserNumber;
        }
        return retryUserNumber;

    }

    private int askRegame(int sameNumberCount, int sameLocationNumberCount) {

        int userChoose = 2;

        if (sameNumberCount == 3 && sameLocationNumberCount == 3) {
            System.out.println("숫자를 모두 맞히셨습니다! 게임 종료");
            System.out.println("게임을 새로 시작하려면 1, 종료하려면 아무 숫자나 입력해주세요.");
            Scanner scanner = new Scanner(System.in);
            userChoose = scanner.nextInt();

        }
        return userChoose;
    }

    private boolean stopGameLoop(int sameNumberCount, int sameLocationNumberCount) {
        boolean gameState = true;
        if (sameNumberCount == 3 && sameLocationNumberCount == 3) {
            return false;
        }
        return gameState;
    }


    public static void main(String[] args) {
        Play play = new Play();
        play.playGame();

    }
}

```

---
여기까지 1차 구현 과정을 완료했습니다. 내일은 피드백 강의를 수강한 후에 리팩토링 포인트를 학습하고, 다시 처음부터 구현해 볼 예정입니다.

---

<br>

> ## 1차 구현 후기
<br>

 1차 구현을 진행해 보니, 예상했던 시간보다 많은 시간을 사용하였습니다. 구현을 해보면서 생각치 못했던 요소를 고려해야 하는 상황이 반복되었습니다. 일부분은 코드를 수정하였고, 일부분은 2차 구현을 해보면서 다시 시도해보자고 생각한 것도 있었습니다. TDD를 아예 모르고 코드를 작성했던 때와 비교하면 많은 발전이 있다고 생각하지만, 부족한 점 역시 많이 느껴집니다. 꾸준한 노력과 점진적인 개선을 다짐하며 1차 구현을 마무리하였습니다.