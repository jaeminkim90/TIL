> ### π βοΈ 
> 1. ** TIL μλ¦¬μ¦μ μμ±λ κΈμ 'λ§€μΌ λ§€μΌ νμ΅ν μ§μ μ‘°κ°μ λ©λͺ¨ν΄ λμ ν¬μ€ν'μλλ€. κ³΅μ κ° μλ κ°μΈμ μΈ νμ΅ λ΄μ© κΈ°λ‘μ λͺ©μ μΌλ‘ μμ±λμμμ μλ €λλ¦½λλ€.**
> 2. κ·Έ μΈ μλ¦¬μ¦μ μμ±λ **κ³΅μ  λͺ©μ μ ν¬μ€ν**μ μκ°μ΄ λ  λλ§λ€ λ³λλ‘ μμ±νκ³  μμ΅λλ€. μ£Όλ‘, TIL μλ¦¬μ¦μ μμ±λ λ΄μ©μμ **νΉμ  μ£Όμ λ₯Ό μ μ νκ³ , λ κΉμ΄ κ³΅λΆν ν μ λ¦¬νμ¬ μμ±ν©λλ€.**

---

# @PostMapping - νλΌλ―Έν°μ λ€μ΄κ° κ°μ²΄ μλ μμ±

μλμ κ°μ΄ HTML νμΌμμ `summit`μ νκ² λλ©΄, `input` νκ·Έμ `name` μμ±μ΄ **'name'**μΈ λ°μ΄ν°κ° `post` λ°©μμΌλ‘ `action` μμ±μ μ§μ λ κ²½λ‘λ‘ μ λ¬λλ€.
```html
<div class="container">
    <form action="/members/new" method="post">
        <div class="form-group">
            <label for="name">μ΄λ¦</label>
            <input type="text" id="name" name="name" placeholder="μ΄λ¦μ μλ ₯νμΈμ"> </div>
        <button type="submit">λ±λ‘</button> </form>
</div> <!-- /container -->
```

μλλ action μμ±μ ν΄λΉνλ κ²½λ‘λ₯Ό Mappingνκ³  μλ Controller λ©μλ create()λ€. 

```java
@PostMapping("/members/new")
public String create(MemberForm form) {
    Member member = new Member();
    member.setName(form.getName());

    memberService.join(member);

    return "redirect:/"; // home νλ©΄μΌλ‘ redirect
}
```


μ΄ λ©μλλ₯Ό νλΌλ―Έν°λ‘ `MemberForm`μ κ°μ²΄λ₯Ό νμλ‘ νλ€. μ€νλ§μ `MemberForm`μ κ°μ²΄λ₯Ό μλμΌλ‘ μμ±νμ¬ νλΌλ―Έν° μ λ¬ κ°μ²΄λ‘ μ¬μ©νλ€. 

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


`MemberFrom` κ°μ²΄λ νλ λ³μμ `private` μ κ·Όμ νμλ₯Ό κ°κ³  μλ `name`μ΄ μλ€. `private`μ΄κΈ° λλ¬Έμ `setter`λ₯Ό ν΅ν΄ `name` κ°μ λ³κ²½ν  μ μλ€. μ€νλ§μ `post` λ°©μμΌλ‘ `Controller`μ `Mapping`λ  λ, **κ°μ²΄ μμ±λΆν° setterλ₯Ό μ΄μ©νμ¬ MemberForm κ°μ²΄μ name λ³μμ μ΄κΈ°νκΉμ§ μλμΌλ‘ μ§ννλ€.**

μ΄ν λ‘μ§μ ν΅ν΄ MemberForm κ°μ²΄μ name κ°μ΄ member κ°μ²΄μ setNameλκ³ , memberServiceμ join()κΉμ§ μ€νλλ€.

