> ### ๐ โ๏ธ 
> 1. ** TIL ์๋ฆฌ์ฆ์ ์์ฑ๋ ๊ธ์ '๋งค์ผ ๋งค์ผ ํ์ตํ ์ง์ ์กฐ๊ฐ์ ๋ฉ๋ชจํด ๋์ ํฌ์คํ'์๋๋ค. ๊ณต์ ๊ฐ ์๋ ๊ฐ์ธ์ ์ธ ํ์ต ๋ด์ฉ ๊ธฐ๋ก์ ๋ชฉ์ ์ผ๋ก ์์ฑ๋์์์ ์๋ ค๋๋ฆฝ๋๋ค.**
> 2. ๊ทธ ์ธ ์๋ฆฌ์ฆ์ ์์ฑ๋ **๊ณต์  ๋ชฉ์ ์ ํฌ์คํ**์ ์๊ฐ์ด ๋  ๋๋ง๋ค ๋ณ๋๋ก ์์ฑํ๊ณ  ์์ต๋๋ค. ์ฃผ๋ก, TIL ์๋ฆฌ์ฆ์ ์์ฑ๋ ๋ด์ฉ์์ **ํน์  ์ฃผ์ ๋ฅผ ์ ์ ํ๊ณ , ๋ ๊น์ด ๊ณต๋ถํ ํ ์ ๋ฆฌํ์ฌ ์์ฑํฉ๋๋ค.**


---

# ์คํ๋ง DB ์ ๊ทผ ๊ธฐ์  5๊ฐ์ง (JDBC / JDBC Template / JPA / ์คํ๋ง ๋ฐ์ดํฐ JPA)
>**1. ์์ JDBC๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์**
>**2. ์คํ๋ง ํตํฉ ํ์คํธ ๋ฐฉ๋ฒ**
>**3. ์คํ๋ง JdbcTemplate์ ์ด์ฉํ๋ ๋ฐฉ์**
>**4. JPA๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์**
>**5. ์คํ๋ง ๋ฐ์ดํฐ JPA๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์**

---


## 1. ์์ JDBC๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์
<br>

**1. build.gradle ํ์ผ์ jdbc, h2 ๋ฐ์ดํฐ๋ฒ ์ด์ค ๊ด๋ จ ๋ผ์ด๋ธ๋ฌ๋ฆฌ ์ถ๊ฐํ๋ค. **
```java
implementation 'org.springframework.boot:spring-boot-starter-jdbc'
runtimeOnly 'com.h2database:h2'
```


**2. ์คํ๋ง ๋ถํธ ๋ฐ์ดํฐ๋ฒ ์ด์ค ์ฐ๊ฒฐ ์ค์ ์ ์ถ๊ฐํ๋ค.  **  
   ๊ฒฝ๋ก: `resources/application.properties`

```java
     spring.datasource.url=jdbc:h2:tcp://localhost/~/test
     spring.datasource.driver-class-name=org.h2.Driver
     spring.datasource.username=sa
```



**3. `MemberRepository` ์ธํฐํ์ด์ค๋ฅผ ์์ํ๋ jdbc ์ฐ๊ฒฐ ํด๋์ค๋ฅผ ๊ตฌํํ๋ค.**
DB ์ฐ๊ฒฐ๊ณผ ๋ฐ์ดํฐ ์ฌ์ฉ๊น์ง์ ๊ตฌ์กฐ๊ฐ ๋ณต์กํ๊ณ , ๊ฐ๋ฐ์๊ฐ ์ง์  ์์ฑํด์ผ ํ๋ ์ฝ๋์ ์์ด ์๋นํ๋ค.
   
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
                throw new SQLException("id ์กฐํ ์คํจ");
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


**4. ์คํ๋ง ๋น์ ๊ด๋ฆฌํ๋ `SpringConfig`์์ ๊ธฐ์กด `MemoryMemberRepository`๋ก ์ค์ ๋์ด ์๋ ์์กด ๊ด๋ฆฌ ํ์ผ์ `JdbcMemberRepository`๋ก ๋ณ๊ฒฝํ๋ค.** ์คํ๋ง์ `DI (Dependencies Injection)`์ ์ฌ์ฉํ๋ฉด ๊ธฐ์กด ์ฝ๋๋ฅผ ์ ํ ์๋์ง ์๊ณ , ์ค์ ๋ง์ผ๋ก ๊ตฌํ ํด๋์ค๋ฅผ ๋ณ๊ฒฝํ  ์ ์๋ค . ์ด๊ฒ์ ๊ฐ์ฒด์งํฅ์ ํฐ ์ฅ์  ์ค ํ๋์ด๋ค. ์ด์ ๊ฐ์ด ํ์ฅ์๋ ์ด๋ ค ์๊ณ , ์์  ๋ณ๊ฒฝ์๋ ๋ซํ์๋ ๋ฐฉ์์ `๊ฐ๋ฐฉ-ํ์ ์์น(OCP, Open-Closed Principle)` ์ด๋ผ๊ณ  ํ๋ค.


   
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


## 2. ์คํ๋ง ํตํฉ ํ์คํธ ๋ฐฉ๋ฒ


์๋ฐ ์ฝ๋ ํ์คํธ์ ์คํ๋ง ์ปจํ์ด๋๋ฅผ ์ด์ฉํ DB ์ฌ์ฉ์ ํตํฉ ํ์คํธ๋ฅผ ํด๋ณผ ์ ์๋ค. ํ์ง๋ง, ์ผ๋ฐ์ ์ผ๋ก ์คํ๋ง์ ๋์์ ์งํํ๋ ํตํฉ ํ์คํธ๋ณด๋ค, ์๋ฐ์ฝ๋ ๋ฒ์์์ ์งํํ๋ ๋จ์ ํ์คํธ๊ฐ ์ข์ ํ์คํธ์ผ ํ๋ฅ ์ด ๋๋ค.

`@SpringBootTest` : ์คํ๋ง ์ปจํ์ด๋์ ํ์คํธ๋ฅผ ํจ๊ป ์คํํ๋ค. 
`@Transactional` : ํ์คํธ ์ผ์ด์ค์ ์ด ์ ๋ธํ์ด์์ด ์์ผ๋ฉด, ํ์คํธ ์์ ์ ์ ํธ๋์ญ์์ ์์ํ๊ณ , ํ์คํธ ์๋ฃ ํ์ ํญ์ ๋กค๋ฐฑํ๋ค. ์ด๋ ๊ฒ ํ๋ฉด DB์ ๋ฐ์ดํฐ๊ฐ ๋จ์ง ์์ผ๋ฏ๋ก(DB์ ๋ฐ์๋์ง ์๋๋ค) ๋ค์ ํ์คํธ์ ์ํฅ์ ์ฃผ์ง ์๋๋ค. ์คํ๋ง ํ์คํธ๋ฅผ ์งํํ  ๋ `@Transactional`์ ์ฌ์ฉํ๋ฉด DB์ ๋ณํ๋ฅผ ์ฃผ์ง ์์ผ๋ฉด์ ํ์คํธ๋ฅผ ๋ฐ๋ณต์ ์ผ๋ก ์งํํ  ์ ์๋ค. 




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
    void ํ์๊ฐ์() {
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
    public  void ์ค๋ณต_ํ์_์์ธ() {
        // given
        Member member1 = new Member();
        member1.setName("spring");

        // when
        Member member2 = new Member();
        member2.setName("spring");

        // then
        memberService.join(member1);

        //assertThrows๋ 2๋ฒ์งธ ์ธ์์ ํด๋นํ๋ ๋ก์ง์ด ์คํ๋๋ค๊ฐ ์์ธ๊ฐ ๋ฐ์ํ๋ฉด 1๋ฒ์งธ ์ธ์์ ๊ฐ์ ํ์์ธ์ง ํ์ธํ๋ค.
        // assertThrows๋ ์์ธ ๊ฐ์ฒด๋ฅผ ๋ฐํํ๋ค.
        IllegalStateException e = assertThrows(IllegalStateException.class, () -> memberService.join(member2));

        // ๋ฐํ๋ IllegalStateException ๊ฐ์ฒด๋ฅผ ์ด์ฉํด ๋ฉ์์ง ๋์ผ์ฑ ๊ฒ์ฆ๋ ๊ฐ๋ฅํ๋ค.
        assertThat(e.getMessage()).isEqualTo("์ด๋ฏธ ์กด์ฌํ๋ ํ์์๋๋ค");

    }


}
```



---


## 3. ์คํ๋ง JdbcTemplate์ ์ด์ฉํ๋ ๋ฐฉ์


์์ Jdbc์ ๋์ผํ ํ๊ฒฝ์ค์ ์ ํ๋ฉด ๋๋ค. ์คํ๋ง JdbcTemplate๊ณผ MyBatis ๊ฐ์ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ JDBC API์์ ๋ณธ ๋ฐ๋ณต ์ฝ๋๋ฅผ ๋๋ถ๋ถ ์ ๊ฑฐํด์ค๋ค. ํ์ง๋ง SQL์ ์ง์  ์์ฑํด์ผ ํ๋ค. 


**1. `JdbcTemplateMemberRepository` ๊ตฌํ**

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


**2. ๋ง์ฐฌ๊ฐ์ง๋ก `SpringConfig` ์ค์  ํ์ผ์ ํตํด `JdbcTemplateMemberRepository`๋ฅผ ์ฌ์ฉํ๋๋ก ์์ ํ๋ค.**

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

JDBCํํ๋ฆฟ์ ์ฌ์ฉํ๋ฉด, JDBC์ PreparedStatement์ Resultset ๋ฑ์ ์ด์ฉํด ๊ฐ์ ์ค๋นํ๊ณ , ๋ฐ์์์ ํผ์น๋ ์์์ ์๋ํํ๋ค. ํ์ง๋ง ์ฟผ๋ฆฌ๋ฌธ์ ์ง์  ์์ฑํด์ผ ํ๋ค. ๋ค๋ง, DB์ ์๋ก์ด ๊ฐ์ ์ ์ฅํ๋ Insert ๋ฌธ์ ์ปฌ๋ผ ๊ฐ์ ์ด์ฉํด key๋ฅผ ๋ฐ๊ณ , key๋ก value๋ฅผ ํ์ธํ  ์ ์์ผ๋ฏ๋ก ์ฟผ๋ฆฌ๋ฌธ์ ์์ฑํ์ง ์๋๋ค.

JDBC์ JDBCํํ๋ฆฟ์ ์ฐจ์ด๋ง ๋ด๋, ๋ฐ๋ณตํด์ผ ํ๋ ์ฝ๋์ ์์ด ํฌ๊ฒ ์ค์ด๋ ๋ค. ์ดํ JPA๋ฅผ ์ฌ์ฉํ๋ฉด ์ฟผ๋ฆฌ๋ฌธ๋ ์๋์ผ๋ก ๋ง๋ค์ด์ฃผ๊ธฐ ๋๋ฌธ์ ์์ํด์ผ ํ๋ ์ฝ๋์ ์์ ๋ ์ค ์ผ ์ ์๋ค.


---



## 4. JPA๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์ 

JPA์๋ฐ ํ์ค ORM ์ธํฐํ์ด์ค๋ค. ๊ทธ ๊ตฌํ์ฒด๋ก ํ์ด๋ฒ๋ค์ดํธ๋ฅผ ๋ง์ด ์ฌ์ฉํ๋ค. ORM์ `Object Relational Mapping` ์ฆ, ๊ฐ์ฒด์ ๊ด๊ณํ ๋ฐ์ดํฐ๋ฒ ์ด์ค๋ฅผ ๋งตํํ๋ ๊ธฐ์ ์ด๋ค. JPA๋ ๊ธฐ์กด์ ๋ฐ๋ณต ์ฝ๋๋ ๋ฌผ๋ก ์ด๊ณ , ๊ธฐ๋ณธ์ ์ธ SQL๋ JPA๊ฐ ์ง์  ๋ง๋ค์ด์ ์คํํด์ค๋ค. JPA๋ฅผ ์ฌ์ฉํ๋ฉด, SQL๊ณผ ๋ฐ์ดํฐ ์ค์ฌ์ ์ค๊ณ์์ ๊ฐ์ฒด ์ค์ฌ์ ์ค๊ณ๋ก ํจ๋ฌ๋ค์์ ์ ํ์ ํ  ์ ์๋ค. JPA๋ฅผ ์ฌ์ฉํ๋ฉด ๊ฐ๋ฐ ์์ฐ์ฑ์ ํฌ๊ฒ ๋์ผ ์ ์๋ค. 


**1. `build.gradle`์ `implementation 'org.springframework.boot:spring-boot-starter-data-jpa`์ ์ถ๊ฐํ๋ค. **
`spring-boot-starter-data-jpa` ๋ ๋ด๋ถ์ jdbc ๊ด๋ จ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ํฌํจํ๋ค. ๋ฐ๋ผ์ jdbc๋ ์ ๊ฑฐํด๋ ๋๋ค. 


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


**2. ์คํ๋ง ๋ถํธ์ JPA ์ค์  ์ถ๊ฐ**
`resources/application.properties`์ ์๋์ ๊ฐ์ด ์ค์ ์ ์ถ๊ฐํ๋ค.

```java
  spring.datasource.url=jdbc:h2:tcp://localhost/~/test
  spring.datasource.driver-class-name=org.h2.Driver
  spring.datasource.username=sa

  spring.jpa.show-sql=true
  spring.jpa.hibernate.ddl-auto=none
```

`spring.jpa.show-sql=true` ์ JPA๊ฐ ๋ง๋๋ ์ฟผ๋ฆฌ๋ฌธ์ ํ์ธํ  ์ ์๋๋ก ์ค์ ํ๋ค. `spring.jpa.hibernate.ddl-auto=none` ์ JPA๊ฐ ๊ฐ์ฒด์ ๋ฐ๋ผ ํ์ด๋ธ์ ์๋์ผ๋ก ์์ฑํ์ง ์๋๋ก ํ๋ค. `none`์ด ์๋ `create`๋ฅผ ์ฌ์ฉํ๋ฉด ์ํฐํฐ ์ ๋ณด๋ฅผ ๋ฐํ์ผ๋ก ํ์ด๋ธ๋ ์ง์  ์์ฑํด์ค๋ค. 




**3. JPA ์ํฐํฐ ๋งตํ**

`@Id` : Id๋ฅผ PK๋ก ์ฌ์ฉํ๋ค.
`@GeneratedValue(strategy = GenerationType.IDENTITY)`:  PK๋ฅผ DB๊ฐ ์๋์ผ๋ก ์์ฑ, ๊ด๋ฆฌํ๋ IDENTITY ์ ๋ต ์ฌ์ฉ์ ๋ช์ํ๋ค.

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


๊ฐ์ฒด์ ๋ณ์๋ช๊ณผ DB์ ์ปฌ๋ผ ๋ช์ ๋งค์นญํ๋ ค๋ฉด ์๋์ ๊ฐ์ด @Column์ ์ฌ์ฉํ๋ค.
```
@Column(name = "username")
private String name;
```


**4. `JpaMemberRepository` ๊ตฌํ**


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

JPA๋ ์ํฐํฐ ๋งค๋์ ๋ก ๋์์ ํ๋ค. `...data-jpa` ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ๋ฐ์ผ๋ฉด ์คํ๋ง๋ถํธ๊ฐ ์ํฐํฐ ๋งค๋์ ๋ฅผ ์๋์ผ๋ก ์์ฑํ๋ค. ํ์ํ๋ค๋ฉด `Injection` ๋ฐ์์ ๋ฐ๋ก ์ฌ์ฉํ  ์ ์๋ค.

**5. ์๋น์ค ๊ณ์ธต์ ํธ๋์ญ์ ์ถ๊ฐ **
ํ๊ฐ์ง ์ฃผ์ํ  ์ ์ JPA ์ฌ์ฉํ๋ ค๋ฉด ํญ์ ํธ๋์ญ์์ด ์์ด์ผ ํ๋ค. JPA๋ ๋ชจ๋  ๋ฐ์ดํฐ ๋ณ๊ฒฝ์ด ํธ๋์ญ์ ์์์ ์คํ๋๊ธฐ ๋๋ฌธ์ด๋ค. ์๋์ ๊ฐ์ด ํด๋์ค์ ์ ๋ธํ์ด์์ ๋ฌ์์ค๋ ์ข์ง๋ง, DB์ ์๋ก ๊ฐ์ ์ ์ฅํ๋ join() ์คํ ์์๋ง ํ์ํจ์ผ๋ก, join()์๋ง ์ ๋ธํ์ด์์ ์ถ๊ฐํด์ค๋ ๋ฌด๋ฐฉํ๋ค.

```java
import org.springframework.transaction.annotation.Transactional

@Transactional
public class MemberService {}
```

**6. JPA๋ฅผ ์ฌ์ฉํ๋๋ก ์คํ๋ง ์ค์  ๋ณ๊ฒฝ **

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


**7. ์คํ๋ง๊ณผ JPA๋ฅผ ์ด์ฉํด DB์ ๊ฐ์ ์ ์ฅํ๋ ํตํฉ ํ์คํธ ์งํ**

```java
@SpringBootTest
@Transactional
class MemberServiceIntegrationTest {

    @Autowired MemberService memberService;
    @Autowired MemberRepository memberRepository;

    @Test
    void ํ์๊ฐ์() {
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

์๋์ ๊ฐ์ด ์๋์ผ๋ก ์ฟผ๋ฆฌ๊ฐ ์์ฑ๋์๋ค.

![](https://images.velog.io/images/woply/post/853bc8dc-4afe-44b1-8b66-9cd03191f36d/image.png)

---

### JPA๋ฅผ ์ด์ฉํ DB ์ ๊ทผ ํน์ง

**1. JPA๋ฅผ ์ฌ์ฉํ๋ฉด ์ฟผ๋ฆฌ ์์ฑ ์์ด ์๋์ ๊ฐ์ ๋ฐฉ์์ผ๋ก ๊ฐ์ฒด๋ฅผ DB์ ์ ์ฅํ  ์ ์๋ค.**

```java
public Member save(Member member) {
    em.persist(member);
    return member;
}
```

**2. PK ๊ฐ์ ์ด์ฉํด ๋ฐ์ดํฐ๋ฅผ ์ฐพ์ ๊ฒฝ์ฐ ์๋์ ๊ฐ์ด ์ฌ์ฉํ  ์ ์๋ค.** PK ๊ธฐ๋ฐ์ด ์๋ ๋ค๋ฅธ Column ์ ๋ณด๋ฅผ ์ด์ฉํด Data๋ฅผ ์กฐํํ  ๋๋ JPQL์ด ํ์ํ๋ค.
```java
public Optional<Member> findById(Long id) {
    Member member = em.find(Member.class, id);
    return Optional.ofNullable(member);
}
```

**3. JPQL์ ํ์ด๋ธ์ด ์๋ ์ํฐํฐ๋ฅผ ๋์์ผ๋ก ์ฟผ๋ฆฌ๋ฅผ ๋ณด๋ธ๋ค. ์ด๊ฒ ๋ด๋ถ์์ SQL๋ก ๋ฒ์ญ๋๋ค. **

 

```java
public List<Member> findAll() {
    return em.createQuery("select m from Member m", Member.class)
            .getResultList();
}
```
`select`์ ๋์์ด `member` ๊ฐ์ฒด ์์ฒด๋ค. 
`from Member m` ์ `from Member as m`์ ์ค์ฌ ์ฌ์ฉํ ๊ฒ์ด๋ค.

---

## 5. ์คํ๋ง ๋ฐ์ดํฐ JPA๋ฅผ ์ด์ฉํ๋ ๋ฐฉ์

์คํ๋ง ๋ถํธ์ JPA๋ง์ผ๋ก๋ ๊ฐ๋ฐ ์์ฐ์ฑ์ด ํฌ๊ฒ ์ฆ๊ฐํ์ง๋ง, `์คํ๋ง ๋ฐ์ดํฐ JPA`๊น์ง ์ฌ์ฉํ๊ฒ ๋๋ฉด ๋ฆฌํฌ์งํ ๋ฆฌ์ ๊ตฌํ ํด๋์ค๋ง์  ๊ฐ๋จํ ์ธํฐํ์ด์ค๋ง์ผ๋ก ๋์ฒดํ  ์ ์๋ค. ๋ฐ๋ณต์์์ด ๋ง์๋ CRUD ๊ธฐ๋ฅ๋ `์คํ๋ง ๋ฐ์ดํฐ JPA`๊ฐ ์ ๊ณตํ๊ธฐ ๋๋ฌธ์ ๊ฐ๋ฐ์๊ฐ ํต์ฌ ๋น์ฆ๋์ค ๋ก์ง์ ๋ ์ง์คํ  ์ ์๋๋ก ๋์์ค๋ค.

`์คํ๋ง ๋ฐ์ดํฐ JPA`๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด์ ์๋์ ๊ฐ์ด ์ธํฐํ์ด์ค๋ฅผ ๊ตฌํํด์ผ ํ๋ค. ํด๋น ์ธํฐํ์ด์ค๋ `JpaRepository` ์ธํฐํ์ด์ค์ ๋ฆฌํฌ์งํ ๋ฆฌ์ ๊ธฐ๋ณธ ์ธํฐํ์ด์ค๋ก ๋ง๋ค์๋ `MemberRepository`๋ฅผ ์์๋ฐ๋๋ค. ์ธํฐํ์ด์ค๋ ๋ค์ค ์์์ด ๊ฐ๋ฅํ๋ค. 

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


์คํ๋ง ๋ฐ์ดํฐ JPA ์ธํฐํ์ด์ค ๊ตฌํ์ด ์๋ฃ๋๋ฉด ์๋์ ๊ฐ์ด `Config` ํ์ผ์ ํ๋ ๋ณ์๋ก `MemberRepository`๋ฅผ ๋ง๋ค๊ณ  ์์ฑ์๋ฅผ ํตํด ์ฃผ์ํ๋ค. `JpaRepository` ๋ฅผ ์์๋ฐ์ ๊ตฌํ๋ ์ธํฐํ์ด์ค๊ฐ ๊ตฌํ์ฒด๋ฅผ ์๋์ผ๋ก ๋ง๋ค์ด ์ฃผ๊ณ , ์คํ๋ง ๋น์ ์๋ ๋ฑ๋กํ๋ค.


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

๋ ๊ฐ์ง ์์์ ์๋ฃํ๊ณ  ์์ ๋ง๋ค์๋ ํตํฉ ํ์คํธ ์ฝ๋๋ฅผ ์คํํ๋ค. ์ ์์ ์ผ๋ก insert ์ฟผ๋ฆฌ๊ฐ ๋์ํ๋ฉฐ, DB ์ ๊ทผ์ด ๊ฐ๋ฅํ๋ค.

> **๋์ ์๋ฆฌ**<br>
`SpringDataJpaMemberRepository`๋ฅผ ๊ตฌํํ  ๋ ์์๋ฐ์๋ `JpaRepository`์ ์์ ๊ตฌ์กฐ๋ฅผ ์ดํด๋ณด๋ฉด, `PagingAndSortingRepository`, `CrudRepository` ๋ฑ์ ์์๋ฐ๊ณ  ์๋ค.(ํ๋จ ์ฐธ๊ณ  ์ด๋ฏธ์ง)<br>
>`CrudRepository`์์ CRUD์ ํ์ํ ๊ธฐ๋ณธ์ ์ธ DB API๋ฅผ ์ ๊ณตํจ์ผ๋ก, ์ธํฐํ์ด์ค์ ์คํ๋ง์ ์๋ ๊ตฌํ์ฒด ์์ฑ ๊ธฐ๋ฅ๋ง์ผ๋ก ์๋ก์ด ํตํฉ ํ์คํธ๋ฅผ ํตํ `DB insert` ๊ธฐ๋ฅ์ด ์ ์์ ์ผ๋ก ๋์ํ๋ค.<br>
>๋ค๋ง, JpaRepository๊ฐ ์๋์ผ๋ก ์์ฑํ๋ ๊ตฌํ์ฒด๋ ๊ณตํต์ผ๋ก ์ ์ฉ๋  ์ ์๋ ๊ธฐ๋ฅ(๊ณตํต ์ฌ์ฉ์ด ๊ฐ๋ฅํ ๋ฉ์๋)๋ง ์ ๊ณตํ๋ค. ๋น์ฆ๋์ค ํน์ฑ์ ๋ฐ๋ผ ๋ค๋ฅธ ํ๋๋ณ์๋ฅผ ์ด์ฉํ DB ์ ๊ทผ์ด ํ์ํ๋ค๋ฉด ์ธํฐํ์ด์ค์ ๋ฉ์๋๋ฅผ ์ถ๊ฐํด์ผ ํ๋ค.<br>
> findBy๋ค์ ๋๋ฌธ์๋ก ์ฐ๊ฒฐ๋๋ ๊ฐ์ column ๊ฐ์ผ๋ก ์ฌ์ฉํ๋ ๋ฉ์๋ ์์ฑ ๊ท์น์ ์ด์ฉํ์ฌ `findByName()`, `findByEmail()` ๊ณผ ๊ฐ์ด ๊ฐ๋จํ๊ฒ ๊ตฌํํ  ์ ์๋ค.<br>
> ์๋ฅผ ๋ค์ด, ์๋์ ๊ฐ์ด ๋ฉ์๋๋ฅผ ์์ฑํ๋ค๋ฉด, 
```java 
Optional<Member> findByName(String name);
```
>`์คํ๋ง ๋ฐ์ดํฐ JPA`๋ ์๋์ผ๋ก ์๋์ ๊ฐ์ `JPQL` ์ฟผ๋ฆฌ๋ฅผ ์์ฑํ๊ณ  ์คํํ๋ค.
```java
select m form Member m where m.name = ?
```
>

> **์ฐธ๊ณ  ์ด๋ฏธ์ง**
![](https://images.velog.io/images/woply/post/8d13627a-f414-4cf0-9ab5-413cb6cb33a4/image.png)![](https://images.velog.io/images/woply/post/fc6f7922-7c68-4795-9090-977cbaf35252/image.png)






