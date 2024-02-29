현업에서는 직접 JDBC API로 코딩하지 않기에 가볍게 참고만 하면 됨

아래와 같은 JDBD 리포지토리를 작성

#### JDBC 회원 리포지토리
`hello/hellospring/repository/JdbcMemberRepository.java`
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
            pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);  
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

#### 스프링 설정 변경

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

        return new MemoryMemberRepository();

        return new JdbcMemberRepository(dataSource);
    }

}
```

#### 구현 클래스 이미지
![[Pasted image 20240229164411.png]]

개방-폐쇄 원칙(OCP, Open-Closed Principle) 
	: 확장에는 열려있고, 수정, 변경에는 닫혀있음
	
스프링의 DI (Dependencies Injection)을 사용하면 기존 코드를 전혀 손대지 않고, 설정만으로 구현 클래스를 변경 가능 
데이터를 DB에 저장하므로 스프링 서버를 다시 실행해도 데이터가 안전하게 저장