> ### π βοΈ 
> **μ΄ κΈμ JPAλ₯Ό μ¬μ©νμ¬ λ°μ΄ν°λ₯Ό μ‘°νν  λ, λ°λ³΅λλ μ½λ κ΅¬ν μμμ κ°μ νλλ° ν° λμμ΄ λλ μ€νλ§ λ°μ΄ν° JPAμ λν κ°λ¨ν μκ°μ μ¬μ© λ°©λ²μ μ λ¦¬ν ν¬μ€νμ΄λ€.**

---

# μ€νλ§ λ°μ΄ν° JPAλ₯Ό μ¬μ©νμ¬ λ°λ³΅λλ JPA μ‘°ν μ½λ μ€μ΄κΈ°

JPAλ μν°ν° λ§€λμ λ₯Ό μ΄μ©ν΄ λ°μ΄ν°λ₯Ό μ μ₯νκ³  μ‘°ννλ€. JPAλ§ μ¬μ©νκ² λλ©΄, μν°ν°λ§λ€ Repositoryλ₯Ό λ§λ€κ³  κΈ°λ³Έμ μΈ CRUD μ½λλ₯Ό κ΅¬νν΄μΌνλ€. κ°λ¨ν μ½λμ§λ§ λ°λ³΅λ μμμ΄ λ°μνλ―λ‘, μμ ν¨μ¨μ±μ΄ λ¨μ΄μ§λ€. μ€νλ§ λ°μ΄ν° JPAλ κΈ°λ³Έμ μΈ μ‘°ν μ½λλ₯Ό λ§€μ° κ°λ¨νκ² κ΅¬ννλ€. μΈν°νμ΄μ€ λ°©μμ μ¬μ©νμ§λ©΄, κ°λ°μκ° λ³λμ κ΅¬νμ²΄λ₯Ό λ§λ€μ§ μμλ μ¬μ©ν  μ μλ€. μ€νλ§ λ°μ΄ν° JPAλ₯Ό ν΅ν΄ κ΅¬μ²΄μ μΌλ‘ μ΄λ€ μμμ κ°μν ν  μ μλμ§ μ΄ν΄λ³΄μ.

---

## 1. μμ JPA κΈ°λ° Repositoryμ λ¨μ 

λ κ°μ μν°ν°(`Member`μ `Team`)μ λ°μ΄ν°λ₯Ό μ‘°ννλ `Repository` μ½λλ₯Ό κ΅¬ννλ€κ³  κ°μ ν΄λ³΄μ. Query μμ±μ ν¬κ² κ°μνν΄μ£Όλ JPAμ§λ§ κΈ°λ³Έμ μΈ CRUD κ΅¬νμ΄ λ°λ³΅λ μλ‘, λΉμ·ν μ½λμ κ΅¬ν μμλ λ°λ³΅λλ€. μν°ν° λ§€λμ κ° μ κ³΅νλ `persist()`λ `find()` κ°μ λ©μλλ₯Ό μ΄μ©νμ¬λ, κΈ°λ₯λ§λ€ λ©μλ ννλ‘ κ΅¬ννλ μμμ ν΄μ£Όμ΄μΌ νλ€. 

μλλ JPA λ°©μμΌλ‘ κ΅¬νν `Member`μ `Team`μ `Reposiroty` μ½λμ΄λ€. μΌν λ³΄μλ μλΉν μ μ¬νλ€. μν°ν°κ° λ§μμ§λ€λ©΄, λ°λ³΅λ μ½λ μμ±μ μμλλ μκ°λ λμ΄κ° κ²μ΄λ€. μ΄μ κ°μ λ°©μμ κ°μ ν  μ μλ€λ©΄ μλΉν μ½λ μμ°μ± μ¦κ°κ° μμλλ€.

**MemberJpaRepository**
```java
@Repository
    public class MemberJpaRepository {
        @PersistenceContext
        private EntityManager em;
  
       public Member save(Member member) {
          em.persist(member);
          return member;
}
      public void delete(Member member) {
          em.remove(member);
}
      public List<Member> findAll() {
          return em.createQuery("select m from Member m", Member.class)
                  .getResultList();
}

```

**TeamJpaRepository** 
```java
@Repository
  public class TeamJpaRepository {
      @PersistenceContext
      private EntityManager em;
      public Team save(Team team) {
          em.persist(team);
          return team;
}
      public void delete(Team team) {
          em.remove(team);
}
      public List<Team> findAll() {
          return em.createQuery("select t from Team tβ, Team.class)
                  .getResultList();
}
      public Optional<Team> findById(Long id) {
          Team team = em.find(Team.class, id);
          return Optional.ofNullable(team);
}

```

---

## 2. Repositoryλ₯Ό μλμΌλ‘ λ§λ€μ΄ μ£Όλ μ€νλ§ λ°μ΄ν° JPA

μ€νλ§ λ°μ΄ν° JPAλ₯Ό μ¬μ©νλ©΄ λ°λ³΅λ `Repository` μ½λ κ°λ°μ νκΈ°μ μΌλ‘ κ°μ ν  μ μλ€. μ€νλ§ λ°μ΄ν° JPAλ μΈν°νμ΄μ€ μμ λ°©μμΌλ‘ μ¬μ©ν  μ μλ€. μΈν°νμ΄μ€ νμμΌλ‘ Repositoryλ₯Ό λ§λ€κ³ , `JpaRepository<T, ID>`λ₯Ό μμνλ€. μ¬κΈ°μ `T`λ μν°ν° νμμ μλ―Ένκ³ , `ID`λ μλ³μ νμ(PK)λ₯Ό μλ―Ένλ€

**JpaRepositoryλ₯Ό μμλ°μ κ΅¬νν MemberRepository μΈν°νμ΄μ€**
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  }
```

μ€νλ§ λ°μ΄ν° JPAλ λ³λμ μΈν°νμ΄μ€ κ΅¬νμ²΄λ₯Ό λ§λ€μ§ μμλ μ¬μ©μ΄ κ°λ₯νλ€. μ€νλ§ λ°μ΄ν° JPAκ° κ΅¬ν ν΄λμ€λ₯Ό λμ  μμ±νκΈ° λλ¬Έμ΄λ€. `JpaRepository<T, ID>`λ₯Ό μμλ°κ² λλ©΄, `org.springframework.data.repository.Repository`λ₯Ό `import`νκ² λλλ°, μλμΌλ‘ κ΅¬νμ²΄ μμ±μ λμμ΄ λλ€. μ΄λ‘μΈν΄ μΈν°νμ΄μ€μΈ `MemberRepository`λ `JpaRepository<T, ID>`κ° κ°μ§κ³  μλ λͺ¨λ  λ©μλλ₯Ό μ¬μ©ν  μ μκ² λλ€. 

![](https://media.vlpt.us/images/woply/post/75e0d193-e48d-44ec-821f-a1810d762916/image.png)

λ°λΌμ, μ§μ  CRUDλ₯Ό λ©μλ ννλ‘ κ΅¬ννμ§ μκ³ λ CRUD λ©μλλ₯Ό μ¬μ©ν  μ μλ€. `JpaRepository<T, ID>`λ₯Ό μμλ°μ `MemberRepository`λ₯Ό νμ€νΈ ν΄λ³΄μ. μλμ κ°μ΄ μ§μ  λ©μλ κ΅¬νμ νμ§ μμμ§λ§, μ μμ μΌλ‘ `save`, `findById`, `delete` λ±μ μ¬μ©ν  μ μλ€.


**MemberRepositoryμ CRUD νμ€νΈ μ½λ**
```java
@SpringBootTest
@Transactional
@Rollback(false)
class MemberRepositoryTest {


	@Autowired
	MemberRepository memberRepository;
	
	@Test
	void testMember() throws Exception{

		// proxy κ°μ²΄λ₯Ό λ°ννλ€. memberRepositoryμ λ°μ΄ν° JPAκ° κ΅¬νμ²΄λ₯Ό λ§λ€μ΄μ Injectionνλ€
		System.out.println("memberRepository = " + memberRepository.getClass());

		// μ μ₯
		Member member = new Member("memberA");
		Member savedmember = memberRepository.save(member);

		// μ‘°ν
		// λ°ν κ°μ΄ Optional
		Optional<Member> findMember1 = memberRepository.findById(savedmember.getId());

		// νΈμΆ μ½λμμ get()μ μ΄μ©νλ©΄, λ°λ‘ member κ°μ²΄λ₯Ό λ°νλ°μ μλ μλ€
		Member findMember2 = memberRepository.findById(savedmember.getId()).get();

		// κ²μ¦
		assertThat(findMember1.get().getId()).isEqualTo(member.getId());
		assertThat(findMember1.get().getUsername()).isEqualTo(member.getUsername());
		assertThat(findMember1.get()).isEqualTo(member);

		assertThat(findMember2.getId()).isEqualTo(member.getId());
		assertThat(findMember2.getUsername()).isEqualTo(member.getUsername());
		assertThat(findMember2).isEqualTo(member);
	}

	@Test
	void basicCRUD() throws Exception {

		Member member1 = new Member("member1");
		Member member2 = new Member("member2");
		memberRepository.save(member1);
		memberRepository.save(member2);

		// λ¨κ±΄ μ‘°ν κ²μ¦
		Member findMember1 = memberRepository.findById(member1.getId()).get();
		Member findMember2 = memberRepository.findById(member2.getId()).get();
		assertThat(findMember1).isEqualTo(member1);
		assertThat(findMember2).isEqualTo(member2);

		// λ¦¬μ€νΈ μ‘°ν κ²μ¦
		List<Member> members = memberRepository.findAll();
		long count = memberRepository.count();

		// μΉ΄μ΄νΈ κ²μ¦
		assertThat(members.size()).isEqualTo(count);
		assertThat(members.size()).isEqualTo(2);

		// μ­μ  κ²μ¦
		memberRepository.delete(member1);
		memberRepository.delete(member2);
		long deletedCount = memberRepository.count();
		assertThat(deletedCount).isEqualTo(0);
	}
}
```

### 2-1. JpaRepositoryκ° μ κ³΅νλ κ³΅ν΅ μΈν°νμ΄μ€

`JpaRepository` μΈν°νμ΄μ€λ κ³΅ν΅ CRUDλ₯Ό ν¬ν¨νμ¬ λ€μν λ©μλλ₯Ό μ κ³΅νλ€. μλ μμ κ΄κ³λ₯Ό μ΄ν΄λ³΄μ. κΈ°λ³Έμ μΈ CRUDλ λ¬Όλ‘ μ΄λ©°, νμ΄μ§μ μ§μνλ μ μ²΄ μ‘°ν λ©μλ(findAll)κΉμ§ μ¬μ©ν  μ μλ€. μ¬μ€μ λλΆλΆμ κ³΅ν΅ λ©μλλ₯Ό μ κ³΅νλ€.

![](https://media.vlpt.us/images/woply/post/f16e9d97-5ad5-4ea9-94a2-4d5ad2f3f118/image.png)

κ·Έ μ€μμλ μ£Όλ‘ μ¬μ©νλ λ©μλλ λ€μκ³Ό κ°λ€.

> - **save(S)** : μλ‘μ΄ μν°ν°λ μ μ₯νκ³  μ΄λ―Έ μλ μν°ν°λ λ³ν©νλ€.
> - **delete(T)** : μν°ν° νλλ₯Ό μ­μ νλ€. λ΄λΆμμ EntityManager.remove() νΈμΆ
> - **findById(ID)** : μν°ν° νλλ₯Ό μ‘°ννλ€. λ΄λΆμμ EntityManager.find() νΈμΆ
> - **getOne(ID)** : μν°ν°λ₯Ό νλ‘μλ‘ μ‘°ννλ€. λ΄λΆμμ EntityManager.getReference() νΈμΆ 
> - **findAll(...)** : λͺ¨λ  μν°ν°λ₯Ό μ‘°ννλ€. μ λ ¬( Sort )μ΄λ νμ΄μ§( Pageable ) μ‘°κ±΄μ νλΌλ―Έν°λ‘ μ κ³΅ν  μ μλ€.





