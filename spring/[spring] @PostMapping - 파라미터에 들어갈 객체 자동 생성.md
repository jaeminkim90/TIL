> ### 📖 ✏️ 
> 1. ** TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**

---

# @PostMapping - 파라미터에 들어갈 객체 자동 생성

아래와 같이 HTML 파일에서 `summit`을 하게 되면, `input` 태그의 `name` 속성이 **'name'**인 데이터가 `post` 방식으로 `action` 속성에 지정된 경로로 전달된다.
```html
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">이름</label>
            <input type="text" id="name" name="name" placeholder="이름을 입력하세요"> </div>
        <button type="submit">등록</button> </form>
</div> <!-- /container -->
```

아래는 action 속성에 해당하는 경로를 Mapping하고 있는 Controller 메서드 create()다. 

```java
@PostMapping("/members/new")
public String create(MemberForm form) {
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/"; // home 화면으로 redirect
}
```


이 메서드를 파라미터로 `MemberForm`의 객체를 필요로 한다. 스프링은 `MemberForm`의 객체를 자동으로 생성하여 파라미터 전달 객체로 사용한다. 

```java
public class MemberForm {
    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```


`MemberFrom` 객체는 필드 변수에 `private` 접근제한자를 갖고 있는 `name`이 있다. `private`이기 때문에 `setter`를 통해 `name` 값을 변경할 수 있다. 스프링은 `post` 방식으로 `Controller`에 `Mapping`될 때, **객체 생성부터 setter를 이용하여 MemberForm 객체의 name 변수의 초기화까지 자동으로 진행한다.**

이후 로직을 통해 MemberForm 객체의 name 값이 member 객체에 setName되고, memberService의 join()까지 실행된다.

