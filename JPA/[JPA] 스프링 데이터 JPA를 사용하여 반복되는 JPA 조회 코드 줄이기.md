> ### 📖 ✏️ 
> **이 글은 JPA를 사용하여 데이터를 조회할 때, 반복되는 코드 구현 작업을 개선하는데 큰 도움이 되는 스프링 데이터 JPA에 대한 간단한 소개와 사용 방법을 정리한 포스팅이다.**

---

# 스프링 데이터 JPA를 사용하여 반복되는 JPA 조회 코드 줄이기

JPA는 엔티티 매니저를 이용해 데이터를 저장하고 조회한다. JPA만 사용하게 되면, 엔티티마다 Repository를 만들고 기본적인 CRUD 코드를 구현해야한다. 간단한 코드지만 반복된 작업이 발생하므로, 작업 효율성이 떨어진다. 스프링 데이터 JPA는 기본적인 조회 코드를 매우 간단하게 구현한다. 인터페이스 방식을 사용하지면, 개발자가 별도의 구현체를 만들지 않아도 사용할 수 있다. 스프링 데이터 JPA를 통해 구체적으로 어떤 작업을 간소화 할 수 있는지 살펴보자.

---

## 1. 순수 JPA 기반 Repository의 단점

두 개의 엔티티(`Member`와 `Team`)와 데이터를 조회하는 `Repository` 코드를 구현한다고 가정해보자. Query 작성을 크게 간소화해주는 JPA지만 기본적인 CRUD 구현이 반복될수록, 비슷한 코드의 구현 작업도 반복된다. 엔티티 매니저가 제공하는 `persist()`나 `find()` 같은 메서드를 이용하여도, 기능마다 메서드 형태로 구현하는 작업을 해주어야 한다. 

아래는 JPA 방식으로 구현한 `Member`와 `Team`의 `Reposiroty` 코드이다. 얼핏 보아도 상당히 유사하다. 엔티티가 많아진다면, 반복된 코드 작성에 소요되는 시간도 늘어갈 것이다. 이와 같은 방식을 개선할 수 있다면 상당한 코드 생산성 증가가 예상된다.

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
          return em.createQuery("select t from Team t”, Team.class)
                  .getResultList();
}
      public Optional<Team> findById(Long id) {
          Team team = em.find(Team.class, id);
          return Optional.ofNullable(team);
}

```

---

## 2. Repository를 자동으로 만들어 주는 스프링 데이터 JPA

스프링 데이터 JPA를 사용하면 반복된 `Repository` 코드 개발을 획기적으로 개선할 수 있다. 스프링 데이터 JPA는 인터페이스 상속 방식으로 사용할 수 있다. 인터페이스 타입으로 Repository를 만들고, `JpaRepository<T, ID>`를 상속한다. 여기서 `T`는 엔티티 타입을 의미하고, `ID`는 식별자 타입(PK)를 의미한다

**JpaRepository를 상속받아 구현한 MemberRepository 인터페이스**
```java
public interface MemberRepository extends JpaRepository<Member, Long> {
  }
```

스프링 데이터 JPA는 별도의 인터페이스 구현체를 만들지 않아도 사용이 가능하다. 스프링 데이터 JPA가 구현 클래스를 대신 생성하기 때문이다. `JpaRepository<T, ID>`를 상속받게 되면, `org.springframework.data.repository.Repository`를 `import`하게 되는데, 자동으로 구현체 생성의 대상이 된다. 이로인해 인터페이스인 `MemberRepository`는 `JpaRepository<T, ID>`가 가지고 있는 모든 메서드를 사용할 수 있게 된다. 

![](https://media.vlpt.us/images/woply/post/75e0d193-e48d-44ec-821f-a1810d762916/image.png)

따라서, 직접 CRUD를 메서드 형태로 구현하지 않고도 CRUD 메서드를 사용할 수 있다. `JpaRepository<T, ID>`를 상속받은 `MemberRepository`를 테스트 해보자. 아래와 같이 직접 메서드 구현을 하지 않았지만, 정상적으로 `save`, `findById`, `delete` 등을 사용할 수 있다.


**MemberRepository의 CRUD 테스트 코드**
```java
@SpringBootTest
@Transactional
@Rollback(false)
class MemberRepositoryTest {


	@Autowired
	MemberRepository memberRepository;
	
	@Test
	void testMember() throws Exception{

		// proxy 객체를 반환한다. memberRepository에 데이터 JPA가 구현체를 만들어서 Injection한다
		System.out.println("memberRepository = " + memberRepository.getClass());

		// 저장
		Member member = new Member("memberA");
		Member savedmember = memberRepository.save(member);

		// 조회
		// 반환 값이 Optional
		Optional<Member> findMember1 = memberRepository.findById(savedmember.getId());

		// 호출 코드에서 get()을 이용하면, 바로 member 객체를 반환받을 수도 있다
		Member findMember2 = memberRepository.findById(savedmember.getId()).get();

		// 검증
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

		// 단건 조회 검증
		Member findMember1 = memberRepository.findById(member1.getId()).get();
		Member findMember2 = memberRepository.findById(member2.getId()).get();
		assertThat(findMember1).isEqualTo(member1);
		assertThat(findMember2).isEqualTo(member2);

		// 리스트 조회 검증
		List<Member> members = memberRepository.findAll();
		long count = memberRepository.count();

		// 카운트 검증
		assertThat(members.size()).isEqualTo(count);
		assertThat(members.size()).isEqualTo(2);

		// 삭제 검증
		memberRepository.delete(member1);
		memberRepository.delete(member2);
		long deletedCount = memberRepository.count();
		assertThat(deletedCount).isEqualTo(0);
	}
}
```

### 2-1. JpaRepository가 제공하는 공통 인터페이스

`JpaRepository` 인터페이스는 공통 CRUD를 포함하여 다양한 메서드를 제공한다. 아래 상속 관계를 살펴보자. 기본적인 CRUD는 물론이며, 페이징을 지원하는 전체 조회 메서드(findAll)까지 사용할 수 있다. 사실상 대부분의 공통 메서드를 제공한다.

![](https://media.vlpt.us/images/woply/post/f16e9d97-5ad5-4ea9-94a2-4d5ad2f3f118/image.png)

그 중에서도 주로 사용하는 메서드는 다음과 같다.

> - **save(S)** : 새로운 엔티티는 저장하고 이미 있는 엔티티는 병합한다.
> - **delete(T)** : 엔티티 하나를 삭제한다. 내부에서 EntityManager.remove() 호출
> - **findById(ID)** : 엔티티 하나를 조회한다. 내부에서 EntityManager.find() 호출
> - **getOne(ID)** : 엔티티를 프록시로 조회한다. 내부에서 EntityManager.getReference() 호출 
> - **findAll(...)** : 모든 엔티티를 조회한다. 정렬( Sort )이나 페이징( Pageable ) 조건을 파라미터로 제공할 수 있다.





