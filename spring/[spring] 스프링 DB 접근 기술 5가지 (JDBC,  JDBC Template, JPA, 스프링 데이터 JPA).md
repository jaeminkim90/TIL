> ### 📖 ✏️ 
> 1. ** TIL 시리즈에 작성된 글은 '매일 매일 학습한 지식 조각을 메모해 놓은 포스팅'입니다. 공유가 아닌 개인적인 학습 내용 기록을 목적으로 작성되었음을 알려드립니다.**
> 2. 그 외 시리즈에 작성된 **공유 목적의 포스팅**은 시간이 날 때마다 별도로 작성하고 있습니다. 주로, TIL 시리즈에 작성된 내용에서 **특정 주제를 선정하고, 더 깊이 공부한 후 정리하여 작성합니다.**


---

# 스프링 DB 접근 기술 5가지 (JDBC / JDBC Template / JPA / 스프링 데이터 JPA)
>**1. 순수 JDBC를 이용하는 방식**
>**2. 스프링 통합 테스트 방법**
>**3. 스프링 JdbcTemplate을 이용하는 방식**
>**4. JPA를 이용하는 방식**
>**5. 스프링 데이터 JPA를 이용하는 방식**

---


## 1. 순수 JDBC를 이용하는 방식
<br>

**1. build.gradle 파일에 jdbc, h2 데이터베이스 관련 라이브러리 추가한다. **
```java
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
runtimeOnly 'com.h2database:h2'
```


**2. 스프링 부트 데이터베이스 연결 설정을 추가한다.  **  
   경로: `resources/application.properties`

```java
     spring.datasource.url=jdbc:h2:tcp://localhost/~/test
     spring.datasource.driver-class-name=org.h2.Driver
     spring.datasource.username=sa
```



**3. `MemberRepository` 인터페이스를 상속하는 jdbc 연결 클래스를 구현한다.**
DB 연결과 데이터 사용까지의 구조가 복잡하고, 개발자가 직접 작성해야 하는 코드의 양이 상당하다.
   
```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.springframework.jdbc.datasource.DataSourceUtils;

import javax.sql.DataSource;
import java.sql.*;
import java.util.ArrayList;
import java.util.List;
import java.util.Optional;

public class JdbcMemberRepository implements MemberRepository {
    private final DataSource dataSource;

    public JdbcMemberRepository(DataSource dataSource) {
        this.dataSource = dataSource;

    }

    @Override
    public Member save(Member member) {
        String sql = "insert into member(name) values(?)";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql,
                    Statement.RETURN_GENERATED_KEYS);
            pstmt.setString(1, member.getName());
            pstmt.executeUpdate();
            rs = pstmt.getGeneratedKeys();
            if (rs.next()) {
                member.setId(rs.getLong(1));
            } else {
                throw new SQLException("id 조회 실패");
            }
            return member;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findById(Long id) {
        String sql = "select * from member where id = ?";
        Connection conn = null;
        PreparedStatement pstmt = null;

        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setLong(1, id);
            rs = pstmt.executeQuery();
            if (rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            } else {
                return Optional.empty();
            }
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public List<Member> findAll() {
        String sql = "select * from member";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            rs = pstmt.executeQuery();

            List<Member> members = new ArrayList<>();
            while (rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                members.add(member);
            }
            return members;
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    @Override
    public Optional<Member> findByName(String name) {
        String sql = "select * from member where name = ?";
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        try {
            conn = getConnection();
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, name);
            rs = pstmt.executeQuery();
            if (rs.next()) {
                Member member = new Member();
                member.setId(rs.getLong("id"));
                member.setName(rs.getString("name"));
                return Optional.of(member);
            }

            return Optional.empty();
        } catch (Exception e) {
            throw new IllegalStateException(e);
        } finally {
            close(conn, pstmt, rs);
        }
    }

    private Connection getConnection() {
        return DataSourceUtils.getConnection(dataSource);
    }

    private void close(Connection conn, PreparedStatement pstmt, ResultSet rs) {
        try {
            if (rs != null) {
                rs.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (pstmt != null) {
                pstmt.close();
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
        try {
            if (conn != null) {
                close(conn);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private void close(Connection conn) throws SQLException {
        DataSourceUtils.releaseConnection(conn, dataSource);

    }
}
```


**4. 스프링 빈을 관리하는 `SpringConfig`에서 기존 `MemoryMemberRepository`로 설정되어 있던 의존 관리 파일을 `JdbcMemberRepository`로 변경한다.** 스프링의 `DI (Dependencies Injection)`을 사용하면 기존 코드를 전혀 손대지 않고, 설정만으로 구현 클래스를 변경할 수 있다 . 이것은 객체지향의 큰 장점 중 하나이다. 이와 같이 확장에는 열려 있고, 수정 변경에는 닫혀있는 방식을 `개방-폐쇄 원칙(OCP, Open-Closed Principle)` 이라고 한다.


   
```java
package hello.hellospring;

import hello.hellospring.repository.JdbcMemberRepository;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class SpringConfig {
    private final DataSource dataSource;
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }
    @Bean
    public MemberRepository memberRepository() {
       //return new MemoryMemberRepository();
        return new JdbcMemberRepository(dataSource);
    }
    //
}
```



---


## 2. 스프링 통합 테스트 방법


자바 코드 테스트와 스프링 컨테이너를 이용한 DB 사용의 통합 테스트를 해볼 수 있다. 하지만, 일반적으로 스프링을 띄워서 진행하는 통합 테스트보다, 자바코드 범위에서 진행하는 단위 테스트가 좋은 테스트일 확률이 높다.

`@SpringBootTest` : 스프링 컨테이너와 테스트를 함께 실행한다. 
`@Transactional` : 테스트 케이스에 이 애노테이션이 있으면, 테스트 시작 전에 트랜잭션을 시작하고, 테스트 완료 후에 항상 롤백한다. 이렇게 하면 DB에 데이터가 남지 않으므로(DB에 반영되지 않는다) 다음 테스트에 영향을 주지 않는다. 스프링 테스트를 진행할 때 `@Transactional`을 사용하면 DB에 변화를 주지 않으면서 테스트를 반복적으로 진행할 수 있다. 




```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.transaction.annotation.Transactional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.assertThrows;

@SpringBootTest
@Transactional
class MemberServiceIntegrationTest {

    @Autowired MemberService memberService;
    @Autowired MemberRepository memberRepository;

    @Test
    void 회원가입() {
        // given
        Member member = new Member();
        member.setName("spring1");

        // when
        Long saveId = memberService.join(member);

        // then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());


    }

    @Test
    public  void 중복_회원_예외() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        // when
        Member member2 = new Member();
        member2.setName("spring");

        // then
        memberService.join(member1);

        //assertThrows는 2번째 인자에 해당하는 로직이 실행되다가 예외가 발생하면 1번째 인자와 같은 타입인지 확인한다.
        // assertThrows는 예외 객체를 반환한다.
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));

        // 반환된 IllegalStateException 객체를 이용해 메시지 동일성 검증도 가능하다.
        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다");

    }


}
```



---


## 3. 스프링 JdbcTemplate을 이용하는 방식


순수 Jdbc와 동일한 환경설정을 하면 된다. 스프링 JdbcTemplate과 MyBatis 같은 라이브러리는 JDBC API에서 본 반복 코드를 대부분 제거해준다. 하지만 SQL은 직접 작성해야 한다. 


**1. `JdbcTemplateMemberRepository` 구현**

```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.jdbc.core.namedparam.MapSqlParameterSource;
import org.springframework.jdbc.core.simple.SimpleJdbcInsert;
import javax.sql.DataSource;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Optional;

public class JdbcTemplateMemberRepository implements MemberRepository {
    private final JdbcTemplate jdbcTemplate;
    public JdbcTemplateMemberRepository(DataSource dataSource) {
        jdbcTemplate = new JdbcTemplate(dataSource);
    }
    @Override
    public Member save(Member member) {
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");

        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());
        Number key = jdbcInsert.executeAndReturnKey(new
                MapSqlParameterSource(parameters));
        member.setId(key.longValue());
        return member;
    }
    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from member where id = ?", memberRowMapper(), id);
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member", memberRowMapper());
    }
    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query("select * from member where name = ?", memberRowMapper(), name);
        return result.stream().findAny();
    }
    private RowMapper<Member> memberRowMapper() {
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setId(rs.getLong("id"));
            member.setName(rs.getString("name"));
            return member;
        }; }
}
```


**2. 마찬가지로 `SpringConfig` 설정 파일을 통해 `JdbcTemplateMemberRepository`를 사용하도록 수정한다.**

```java
package hello.hellospring;

import hello.hellospring.repository.JdbcMemberRepository;
import hello.hellospring.repository.JdbcTemplateMemberRepository;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class SpringConfig {
    private final DataSource dataSource;
    public SpringConfig(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        // return new MemoryMemberRepository();
        // return new JdbcMemberRepository(dataSource);
        return new JdbcTemplateMemberRepository(dataSource);
    }

}
```

JDBC템플릿을 사용하면, JDBC와 PreparedStatement와 Resultset 등을 이용해 값을 준비하고, 받아와서 펼치는 작업을 자동화한다. 하지만 쿼리문은 직접 작성해야 한다. 다만, DB에 새로운 값을 저장하는 Insert 문은 컬럼 값을 이용해 key를 받고, key로 value를 확인할 수 있으므로 쿼리문을 작성하지 않는다.

JDBC와 JDBC템플릿의 차이만 봐도, 반복해야 하는 코드의 양이 크게 줄어든다. 이후 JPA를 사용하면 쿼리문도 자동으로 만들어주기 때문에 작업해야 하는 코드의 양을 더 줄 일 수 있다.


---



## 4. JPA를 이용하는 방식 

JPA자바 표준 ORM 인터페이스다. 그 구현체로 하이버네이트를 많이 사용한다. ORM은 `Object Relational Mapping` 즉, 객체와 관계형 데이터베이스를 맵핑하는 기술이다. JPA는 기존의 반복 코드는 물론이고, 기본적인 SQL도 JPA가 직접 만들어서 실행해준다. JPA를 사용하면, SQL과 데이터 중심의 설계에서 객체 중심의 설계로 패러다임을 전환을 할 수 있다. JPA를 사용하면 개발 생산성을 크게 높일 수 있다. 


**1. `build.gradle`에 `implementation 'org.springframework.boot:spring-boot-starter-data-jpa`을 추가한다. **
`spring-boot-starter-data-jpa` 는 내부에 jdbc 관련 라이브러리를 포함한다. 따라서 jdbc는 제거해도 된다. 


```java
dependencies {
   implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
   implementation 'org.springframework.boot:spring-boot-starter-web'
   // implementation 'org.springframework.boot:spring-boot-starter-jdbc'
   implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
   runtimeOnly 'com.h2database:h2'
   testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```


**2. 스프링 부트에 JPA 설정 추가**
`resources/application.properties`에 아래와 같이 설정을 추가한다.

```java
  spring.datasource.url=jdbc:h2:tcp://localhost/~/test
  spring.datasource.driver-class-name=org.h2.Driver
  spring.datasource.username=sa

  spring.jpa.show-sql=true
  spring.jpa.hibernate.ddl-auto=none
```

`spring.jpa.show-sql=true` 은 JPA가 만드는 쿼리문을 확인할 수 있도록 설정한다. `spring.jpa.hibernate.ddl-auto=none` 은 JPA가 객체에 따라 테이블을 자동으로 생성하지 않도록 한다. `none`이 아닌 `create`를 사용하면 엔티티 정보를 바탕으로 테이블도 직접 생성해준다. 




**3. JPA 엔티티 맵핑**

`@Id` : Id를 PK로 사용한다.
`@GeneratedValue(strategy = GenerationType.IDENTITY)`:  PK를 DB가 자동으로 생성, 관리하는 IDENTITY 전략 사용을 명시한다.

```java
package hello.hellospring.domain;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Member {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}

```


객체의 변수명과 DB의 컬럼 명을 매칭하려면 아래와 같이 @Column을 사용한다.
```
@Column(name = "username")
private String name;
```


**4. `JpaMemberRepository` 구현**


```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import javax.persistence.EntityManager;
import java.util.List;
import java.util.Optional;

public class JpaMemberRepository implements MemberRepository {

    private final EntityManager em;
    public JpaMemberRepository(EntityManager em) {
        this.em = em;
    }

    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class, id);
        return Optional.ofNullable(member);
    }

    public List<Member> findAll() {
        return em.createQuery("select m from Member m", Member.class)
                .getResultList();
    }

    public Optional<Member> findByName(String name) {

        List<Member> result = em.createQuery("select m from Member m where m.name = :name", Member.class)
                .setParameter("name", name)
                .getResultList();
        return result.stream().findAny();
    } }
```

JPA는 엔티티 매니저로 동작을 한다. `...data-jpa` 라이브러리를 받으면 스프링부트가 엔티티 매니저를 자동으로 생성한다. 필요하다면 `Injection` 받아서 바로 사용할 수 있다.

**5. 서비스 계층에 트랜잭션 추가 **
한가지 주의할 점은 JPA 사용하려면 항상 트랜잭션이 있어야 한다. JPA는 모든 데이터 변경이 트랜잭션 안에서 실행되기 때문이다. 아래와 같이 클래스에 애노테이션을 달아줘도 좋지만, DB에 새로 값을 저장하는 join() 실행 시에만 필요함으로, join()에만 애노테이션을 추가해줘도 무방하다.

```java
import org.springframework.transaction.annotation.Transactional

@Transactional
public class MemberService {}
```

**6. JPA를 사용하도록 스프링 설정 변경 **

```
package hello.hellospring;
  import hello.hellospring.repository.*;
  import hello.hellospring.service.MemberService;
  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import javax.persistence.EntityManager;
  import javax.sql.DataSource;
  @Configuration
  public class SpringConfig {
   
         private final DataSource dataSource;
        private final EntityManager em;
        public SpringConfig(DataSource dataSource, EntityManager em) {
            this.dataSource = dataSource;
            this.em = em;
}
        @Bean
        public MemberService memberService() {
            return new MemberService(memberRepository());
        }
        @Bean
        public MemberRepository memberRepository() {
          return new MemoryMemberRepository();
          return new JdbcMemberRepository(dataSource);
          return new JdbcTemplateMemberRepository(dataSource);
            return new JpaMemberRepository(em);
        }
}
```


**7. 스프링과 JPA를 이용해 DB에 값을 저장하는 통합 테스트 진행**

```java
@SpringBootTest
@Transactional
class MemberServiceIntegrationTest {

    @Autowired MemberService memberService;
    @Autowired MemberRepository memberRepository;

    @Test
    void 회원가입() {
        // given
        Member member = new Member();
        member.setName("spring1");

        // when
        Long saveId = memberService.join(member);

        // then
        Member findMember = memberService.findOne(saveId).get();
        assertThat(member.getName()).isEqualTo(findMember.getName());


    }
}
```

아래와 같이 자동으로 쿼리가 생성되었다.

![](https://images.velog.io/images/woply/post/853bc8dc-4afe-44b1-8b66-9cd03191f36d/image.png)

---

### JPA를 이용한 DB 접근 특징

**1. JPA를 사용하면 쿼리 작성 없이 아래와 같은 방식으로 객체를 DB에 저장할 수 있다.**

```java
public Member save(Member member) {
    em.persist(member);
    return member;
}
```

**2. PK 값을 이용해 데이터를 찾을 경우 아래와 같이 사용할 수 있다.** PK 기반이 아닌 다른 Column 정보를 이용해 Data를 조회할 때는 JPQL이 필요하다.
```java
public Optional<Member> findById(Long id) {
    Member member = em.find(Member.class, id);
    return Optional.ofNullable(member);
}
```

**3. JPQL은 테이블이 아닌 엔티티를 대상으로 쿼리를 보낸다. 이게 내부에서 SQL로 번역된다. **

 

```java
public List<Member> findAll() {
    return em.createQuery("select m from Member m", Member.class)
            .getResultList();
}
```
`select`의 대상이 `member` 객체 자체다. 
`from Member m` 은 `from Member as m`을 줄여 사용한 것이다.

---

## 5. 스프링 데이터 JPA를 이용하는 방식

스프링 부트와 JPA만으로도 개발 생산성이 크게 증가하지만, `스프링 데이터 JPA`까지 사용하게 되면 리포지토리에 구현 클래스마저 간단한 인터페이스만으로 대체할 수 있다. 반복작업이 많았던 CRUD 기능도 `스프링 데이터 JPA`가 제공하기 때문에 개발자가 핵심 비즈니스 로직에 더 집중할 수 있도록 도와준다.

`스프링 데이터 JPA`를 사용하기 위해서 아래와 같이 인터페이스를 구현해야 한다. 해당 인터페이스는 `JpaRepository` 인터페이스와 리포지토리의 기본 인터페이스로 만들었던 `MemberRepository`를 상속받는다. 인터페이스는 다중 상속이 가능하다. 

```java
package hello.hellospring.repository;
import hello.hellospring.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.Optional;

public interface SpringDataJpaMemberRepository extends JpaRepository<Member,Long>, MemberRepository {

    @Override
    Optional<Member> findByName(String name);
}
```


스프링 데이터 JPA 인터페이스 구현이 완료되면 아래와 같이 `Config` 파일에 필드 변수로 `MemberRepository`를 만들고 생성자를 통해 주입한다. `JpaRepository` 를 상속받아 구현된 인터페이스가 구현체를 자동으로 만들어 주고, 스프링 빈을 자동 등록한다.


```java
@Configuration
public class SpringConfig {

    private final MemberRepository memberRepository;

    @Autowired
    public SpringConfig(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }


    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository);
    }
}
```

두 가지 작업을 완료하고 앞서 만들었던 통합 테스트 코드를 실행한다. 정상적으로 insert 쿼리가 동작하며, DB 접근이 가능하다.

> **동작 원리**<br>
`SpringDataJpaMemberRepository`를 구현할 때 상속받았던 `JpaRepository`의 상속 구조를 살펴보면, `PagingAndSortingRepository`, `CrudRepository` 등을 상속받고 있다.(하단 참고 이미지)<br>
>`CrudRepository`에서 CRUD에 필요한 기본적인 DB API를 제공함으로, 인터페이스와 스프링의 자동 구현체 생성 기능만으로 새로운 통합 테스트를 통한 `DB insert` 기능이 정상적으로 동작한다.<br>
>다만, JpaRepository가 자동으로 생성하는 구현체는 공통으로 적용될 수 있는 기능(공통 사용이 가능한 메서드)만 제공한다. 비즈니스 특성에 따라 다른 필드변수를 이용한 DB 접근이 필요하다면 인터페이스에 메서드를 추가해야 한다.<br>
> findBy뒤에 대문자로 연결되는 값을 column 값으로 사용하는 메서드 생성 규칙을 이용하여 `findByName()`, `findByEmail()` 과 같이 간단하게 구현할 수 있다.<br>
> 예를 들어, 아래와 같이 메서드를 작성한다면, 
```java 
Optional<Member> findByName(String name);
```
>`스프링 데이터 JPA`는 자동으로 아래와 같은 `JPQL` 쿼리를 작성하고 실행한다.
```java
select m form Member m where m.name = ?
```
>

> **참고 이미지**
![](https://images.velog.io/images/woply/post/8d13627a-f414-4cf0-9ab5-413cb6cb33a4/image.png)![](https://images.velog.io/images/woply/post/fc6f7922-7c68-4795-9090-977cbaf35252/image.png)






