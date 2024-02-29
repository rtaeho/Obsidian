스프링 JdbcTemplate과 MyBatis 같은 라이브러리는 JDBC API에서 본 반복 코드를 대부분 제거해주지만, SQL은 직접 작성해야함

#### 스프링 JDBCTemplate 회원 리포지토리
`hello/hellospring/repository/JdbcTemplateMemberRepository.java`
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
    } // 생성자가 하나일 경우 @Autowired 생략가능  
  
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
        List<Member> result = jdbcTemplate.query("select * from member where id = ? ", memberRowMapper(), id);  
        return result.stream().findAny();  
    }  
  
    @Override  
    public List<Member> findAll() {  
        return jdbcTemplate.query("select * from member", memberRowMapper());  
    }  
  
    @Override  
    public Optional<Member> findByName(String name) {  
        List<Member> result = jdbcTemplate.query("select * from member where name = ? ", memberRowMapper(), name);  
        return result.stream().findAny();  
    }  
  
    private RowMapper<Member> memberRowMapper() {  
        return (rs, rowNum) -> {  
            Member member = new Member();  
            member.setId(rs.getLong("id"));  
            member.setName(rs.getString("name"));  
            return member;  
        };  
    }  
}
```
순수 JDBC와 같은 환경 설정

#### JDBCTemplate를 사용하도록 스프링 설정 변경
`hello/hellospring/SpringConfig.java`
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
        //return new MemoryMemberRepository();  
		//return new JdbcMemberRepository(dataSource);  
		return new JdbcTemplateMemberRepository(dataSource);  
    }  
}
```
