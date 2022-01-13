![](https://images.velog.io/images/woply/post/32953ff1-78b5-48a0-8893-aac41b353caa/image.png)

<br>

> 이 글은 'NextStep - 자바 TDD / 클린코드' 강의를 수강하며 학습한 내용을 담고 있습니다. 맛보기 과제 해당하는 1️⃣문자열 계산기를 만들며, 처음으로 TDD와 리팩토링을 적용해 본 과정을 기록하였습니다.

<br>

---

<br>

# TDD & 단위 테스트 실습 - 문자열 계산기

<br>

---
문자열 계산기 실습의 요구사항은 아래와 같았다. 단위 테스트의 정확한 개념과 과제의 방향을 이해하지 못한 상태로 일단 기능을 구현해봤다.

---

<br>

다음 요구사항을 JUnit을 활용해 단위 테스트 코드를 추가해 구현한다.

> ** 요구사항**
- 사용자가 입력한 문자열 값에 따라 사칙연산을 수행할 수 있는 계산기를 구현해야 한다.
- 문자열 계산기는 사칙연산의 계산 우선순위가 아닌 입력값에 따라 계산 순서가 결정된다. 즉, 수학에서는 곱셈, 나눗셈이 덧셈, 뺄셈보다 먼저 계산해야 하지만 이를 무시한다.
- 예를 들어 "2 + 3 x 4 / 2"와 같은 문자열을 입력할 경우 2 + 3 * 4 / 2의 실행 결과인 10을 출력해야 한다.  
> 

<br>

> **힌트**

- 문자열을 입력받은 후(scanner의 nextLine() 메소드 활용) 빈 공백 문자열을 기준으로 문자들을 분리해야 한다.
```java
String value = scanner.nextLine();
String[] values = value.split(" ");
```
> - 문자열을 숫자로 변경하는 방법

```java
int number = Integer.parseInt("문자열");
```

---

<br>

## 1) 개념조차 이해하지 못했던 1차 구현

<br>

 내가 알고 있는 TDD에 대한 개념은 매우 어설펐다. 일단 요구사항을 먼저 구현하고,  최소 단위로 메서드를 나누면 될 거라고 막연하게 생각했다. 그 결과 아래와 같이 코드를 작성하긴 했으나, 잘 모르는 내가 봐도 전혀 TDD스럽지 않았다. 기본 요구 사항은 충족하였지만, 어떻게 단위테스트를 적용해야 할지 전혀 감이 오지 않았다. 함께 공부하고 있는 친구들에게 질문했더니 일단 피드백 강의를 먼저 수강하는 게 좋겠다는 조언을 받았다.

내가 작성한 1차 구현 결과는 아래와 같다.  

```java
@Test
void calculatorTest() {

    String actual = "2 + 3 * 4 / 2";

    // 2. 분리
    String[] values = actual.split(" ");
    System.out.println("쪼개진 문자 식: " + Arrays.toString(values));


    // 3. 순차적으로 계산
    int surNumber = 0;
    int result = 0;

    for (int i = 2; i < values.length; i++) {
        int tempResult = 0;
        surNumber = (Integer.parseInt(values[i]));


        if (i == 2) {
            result = (Integer.parseInt(values[i - 2]));
        }

        if (values[i - 1].equals("+")) {
            tempResult = (result + surNumber);
        } else if (values[i - 1].equals("-")) {
            tempResult = (result - surNumber);
        } else if (values[i - 1].equals("*")) {
            tempResult = (result * surNumber);
        } else if (values[i - 1].equals("/")) {
            tempResult = (result / surNumber);
        }


        result = tempResult;
        i++; // 연산 기호는 뛰어넘고 숫자만 다루기 위해 i++
    }

    // 4. 결과 출력
    System.out.println("문자열 계산기 결과: " + result);
}
```

---

<br>

## 2) 2차 구현에 도전하다

<br>

친구들의 조언대로 피드백 강의를 먼저 수강하면서 **몇 가지 중요한 포인트를 이해**할 수 있었다.

<br>

1. **기능의 목적과 기능의 범위를 정의할 수 있어야 한다.** 기능의 목적은 클래스의 단위가 되고, 기능의 범위는 메서드와 테스트 코드의 단위가 된다. 기능이 독립적으로 동작하도록 클래스와 메서드를 구성하고, 테스트 코드를 통해 검증한다. 

<br>

2. **기능의 범위를 정의할 수 없는 것은 프로그램에 대한 도메인 지식이 부족하기 때문이다.** 일단 전체 프로그램상 어떤 기능이 필요한지 이해하는 것이 필요하다. 이를 위해서는 요구사항을 충족하는 전체 프로그램을 만들어보는 것이 도움이 된다. 만드는 과정을 통해서 프로그램의 기능 단위가 명확하게 파악되면 미련 없이 버린다. 머릿속에 남은 프로그램의 도메인 지식을 활용하여 1번에 해당하는 작업을 처음부터 다시 진행한다.

<br>

3. **프로덕트에 해당하는 코드를 작성하기 전에 테스트 코드를 작성한다.** 테스트 코드가 완성되면 테스트를 통과하는 프로덕션 코드를 작성하는 방식으로 프로그램을 구현한다.

4. 테스트 코드와 프로덕트 코드의 1차 구현이 마무리되면, 더 나은 코드를 위해 리팩토링 작업을 진행한다. 그리고 **리팩토링을 반복한다.**

<br>

이제야 내가 만든 문자열 계산기를 어떻게 리팩토링 해야 할 지 감을 잡을 수 있었다. 일단 요구 조건을 충족하는 문자열 계산기를 구현해봤기 때문에 어떤 기능 단위로 메서드를 나눠야 할지 감이 왔다. 클래스까지 나눠야 할지는 잘 모르겠다. 일단 메서드 단위로 먼저 나눠보고 친구들에게 의견을 구한 다음, 3차 리팩토링에서 고민해보기로 하였다. 

<br>

무엇보다 피드백 강의를 통해 얻을 수 있었던 큰 깨달음은 **'프로그램에 대한 도메인 지식이 부족하면 TDD가 막막하게 느껴진다'**는 것이었다. 

한 편으로는 **"TDD란, 개발자가 프로그램의 복잡도를 제어하기 위해  사용하는 단순화, 모듈화 기법"**이라는 생각도 들었다.

배움을 통해 상황이 나아졌으니, 다시 작업 순서를 구상해보았다. 우선 문자열 계산기의 기능 단위를 4개로 나눠보았다. 

**1. `문자 분리(splitString)`**: 문자열을 입력받고, 문자 단위로 쪼개서 배열에 담아 리턴한다. 
**2. `숫자만 분리하기(creatNumberList)`**: 문자 단위로 쪼개진 배열을 받아, List에 숫자만 담아 리턴한다.
**3. `연산자만 분리하기(creatOperatorList)`**: 문자 단위로 쪼개진 배열을 받아, List에 연산자만 담아 리턴한다.
**4. `계산(calculate)`**: 숫자 List 객체와  연산자 List 객체를 넣으면 순차적으로 계산하여 결과를 리턴한다.


이어서 구상해본 기능 단위마다 실패하는 테스트 코드를 먼저 작성해보고, 이후 테스트가 통과하는 프로덕트 코드를 구현했다. 

### 1. 문자 분리(splitString) 기능의 실패하는 테스트 코드 구현

프로덕트 코드의 `StringCalculator` 클래스에 `splitString()`를 통해 문자 분리 기능을 구현하는 것으로 가정했다.

```java
@Test
@DisplayName("문자열을 받아서 분리하는 테스트")
void splitStringTest() {
    String userString = "1 + 2 + 3";

    String[] values = StringCalculator.splitString(userString);
    // splitString에 문자열 원형을 넣으면 문자 단위로 쪼개진 배열을 반환한다.

    assertThat(Arrays.toString(values)).isEqualTo("[1, +, 2, +, 3]");

}
```

그리고 이어서 프로덕트 코드를 구현했다.

테스트 코드에 작성한대로 `splitString()`를 구현했고,  리턴 타입은 `String Array`, 매개변수는 `String` 객체를 받도록 하였다.

매개변수로 받아온 문자열을 공백(" ")을 기준으로 쪼갰다. 리턴 타입이 배열이기 때문에 별도의 로컬 변수는 만들지 않았다.

```java
public static String[] splitString(String userString) {

    return userString.split(" ");

}
```
 <br>
 <br>

### 2. 숫자만 분리하기(creatNumberList) 기능의 실패하는 테스트 코드 구현

테스트 코드는 분리된 문자 배열을 매개변수로 넣으면 숫자만 담겨있는 ArrayList를 반환하는 creatNumberList()를 구현했다고 가정했다.

```java
@Test
@DisplayName("문자 단위로 쪼개진 배열을 받아, 숫자만 리턴하는 테스트")
void creatNumberListTest() {
    String[] values = {"2", "+", "3", "+", "1"};
    List<Integer> expectList = new ArrayList<Integer>(Arrays.asList(2,3,1));
    List<Integer> onlyNumber = StringCalculator.creatNumberList(values);
    assertThat(onlyNumber.equals(expectList)).isTrue();
}
```


그리고 이어서 프로덕트 코드를 구현했다. 

문자 단위로 나눠진 `String` 배열을 매개변수로 받아 아스키코드값으로 숫자에 해당하는 문자만 분리했다. 숫자로 확인된 문자는 `int` 타입으로 파싱하여 `List`에 담아 반환한다.

```java
public static List<Integer> creatNumberList(String[] values) {
    List<Integer> numberArray = new ArrayList<>();

    for (String s : values) {
         char numberCheck = s.charAt(0);
        if(numberCheck > 48 && numberCheck < 58){
            numberArray.add(Integer.parseInt(s));
        }
    }
    return numberArray;
}

```
 
 <br>
 <br>

### 3. 연산자만 분리하기(creatOperatorList) 기능의 실패하는 테스트 코드 구현

테스트 코드는 문자 단위로 쪼개진 배열 객체를 매개변수로 넣으면 연산자만 담겨있는 `ArrayList`를 반환하는  `creatOperatorList()`를 구현했다고 가정했다.
   
```java
@Test
@DisplayName("문자 단위로 쪼개진 배열을 받아, 연산자만 리턴하는 테스트")
void creatOperatorListTest() {
    String[] values = {"1", "+", "2", "+", "3"};
    List<String> expectList = new ArrayList<String>(Arrays.asList("+","+"));
    List<String> onlyOperator = StringCalculator.creatOperatorList(values);
    assertThat(onlyOperator.equals(expectList)).isTrue();
}
```

그리고 이어서 프로덕트 코드를 구현했다. 

2번 과정과 비슷한 로직을 처리한다. 차이점은 숫자를 분리하지 않고, 연산자를 분리한다는 점이다. 2번에서 구현한 `creatNumberList()`에서 숫자를 검증하는 로직을 반대로 적용했다. 2번을 먼저 구현했기 때문에 비교적 간단하게 해결 할 수 있었다. 

```java
public static List<String> creatOperatorList(String[] values) {
    List<String> operatorArray = new ArrayList<>();

    for (String s : values) {
        char operatorCheck = s.charAt(0);
        if (operatorCheck < 48 || operatorCheck > 58) {
            operatorArray.add(s);
        }
    }
    return operatorArray;
}
```


> **TIP)** 실패하는 테스트 코드 작성 후 빠르게 해당 메서드를 만드는 방법: 아직 구현되지 않아 빨간색으로 표시되는 메서드에 커서를 두고 `option + enter(Mac 기준)` 를 누르면 프로덕트 코드에 자동으로 메서드를 생성할 수 있다.

<br>
<br>

### 4. 계산(calculate)기능의 실패하는 테스트 구현
테스트 코드는 `숫자 List` 객체와 `연산자 List` 객체를 매개변수로 받아 순차적으로 계산을 수행 후 결과 값을 리턴하는 `calculate()`를 구현했다고 가정했다.
   
```java
@Test
@DisplayName("문자 단위로 쪼개진 배열을 받아, 연산자만 리턴하는 테스트")
void calculateTest() {
    List<Integer> numberList = new ArrayList<Integer>(Arrays.asList(1, 2, 3));
    List<String> operatorList = new ArrayList<String>(Arrays.asList("+", "+"));
    int result = StringCalculator.calculate(numberList, operatorList);
    assertEquals(6, result);
}
```

이어서 프로덕트 코드를 구현했다. if문을 이용하여 매개변수로 받은 `숫자 List`와 `연산자 List`가 순차적으로 계산되도록 로직을 작성했다. 계산이 완료되면 최종 결괏값을 리턴한다. 

```java
public static int calculate(List<Integer> numberList, List<String> operatorList) {

    int operatorListIndex = 0;
    int result = 0;

    for (int i = 1; i < numberList.size(); i++) {

        if(i==1) {
            result = (numberList.get(i - 1));
        }

        if (operatorList.get(operatorListIndex).equals("+")) {
            result = result + (numberList.get(i));
        } else if (operatorList.get(operatorListIndex).equals("-")) {
            result = result - (numberList.get(i));
        } else if (operatorList.get(operatorListIndex).equals("*")) {
            result = result * (numberList.get(i));
        } else if (operatorList.get(operatorListIndex).equals("/")) {
            result = result / (numberList.get(i));
        }
        operatorListIndex++;
    }
    return result;
}
```


---
<br>

## 나의 첫 TDD & 리팩토링 실습 후기

<br>

피드백 강의를 보고 학습한 개념을 이용해 1차 리팩토링을 진행해 보았다. 문자열 계산기 실습을 통해 TDD와 리팩토링의 개념을 조금 더 이해할 수 있어서 좋았다. 스스로 결과물을 돌아봤을 때, **기능 단위를 메서드를 재정의했다는 점과 실패하는 단위 테스트를 먼저 작성하고 프로덕트 코드를 작성했다는 점이 특별히 뿌듯했다.**

하지만 아직 리팩토링이 필요한 코드가 많아 보였다. 특히 `calculate()`와 같은 로직은 기능을 더 단순하게 분리할 수 있을 거 같다. 어떻게 해야 더 나은 코드로 개선할 수 있을지 고민이 필요해 보인다. 

다음 과제인 야구 게임은 친구들과 함께 코드 리뷰를 진행하기로 하였다. 야구 게임은 나 자신의 능력으로는 더 개선이 어렵다고 판단되는 수준까지 리팩토링을 진행해 볼 예정이다.