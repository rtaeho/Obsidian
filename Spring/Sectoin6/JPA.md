JDBC 템플릿에서는 SQL을 직접 작성해야했는데, JPA는 직접 만들어서 실행해줌

#### build.gradle 파일에 JPA, h2 데이터베이스 관련 라이브러리 추가
`build.gradle`
```java
dependencies {  
    implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'  
    implementation 'org.springframework.boot:spring-boot-starter-web'  
// implementation 'org.springframework.boot:spring-boot-starter-jdbc'  
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa' // jdbc 관련 라이브러리 포함  
    runtimeOnly 'com.h2database:h2'  
    testImplementation('org.springframework.boot:spring-boot-starter-test') {  
        exclude group: 'org.junit.vintage', module: 'junit-vintage-engine'  
    }  
}
```

#### 스프링 부트에 JPA 설정 추가
`resources/application.properties`

```java
spring.datasource.url=jdbc:h2:tcp://localhost/~/test
 spring.datasource.driver-class-name=org.h2.Driver
 spring.datasource.username=sa

 spring.jpa.show-sql=true
 spring.jpa.hibernate.ddl-auto=none
```

**`show-sql`**
	: JPA가 생성하는 SQL을 출력
**`ddl-auto`**
	: JPA는 테이블을 자동으로 생성하는 기능을 제공하는데 `none` 를 사용하면 해당 기능을 끔
	`create` 를 사용하면 엔티티 정보를 바탕으로 테이블도 직접 생성
#### JPA 엔티티 매핑
`hello/hellospring/domain/Member.java`
```java

 package hello.hellospring.domain;

 import javax.persistence.Entity;
 import javax.persistence.GeneratedValue;
 import javax.persistence.GenerationType;
 import javax.persistence.Id;

 @Entity public class Member {

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

#### JPA 회원 리포지토리
`hello/hellospring/repository/JpaMemberRepository.java`
```java
package hello.hellospring.repository;  
  
import hello.hellospring.domain.Member;  
  
import jakarta.persistence.EntityManager;  
  
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
        return em.createQuery("select m from Member m", Member.class).getResultList();  
    }  
  
    public Optional<Member> findByName(String name) {  
        List<Member> result = em.createQuery("select m from Member m where m.name = :name ", Member.class)  
                .setParameter("name", name)  
                .getResultList();  
        return result.stream().findAny();  
    }  
}
```

#### 서비스 계층에 트랜잭션 추가
```java
 import org.springframework.transaction.annotation.Transactional
 
 @Transactional
 public class MemberService {}
```
`org.springframework.transaction.annotation.Transactional` 를 사용
스프링은 해당 클래스의 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋
만약 런타임 예외가 발생하면 롤백
JPA를 통한 모든 데이터 변경은 트랜잭션 안에서 실행해야 함

#### JPA를 사용하도록 스프링 설정 변경
```java
package hello.hellospring;

 import hello.hellospring.repository.*;
 import hello.hellospring.service.MemberService;
 import org.springframework.context.annotation.Bean;
 import org.springframework.context.annotation.Configuration;

 import javax.persistence.EntityManager;
 import javax.sql.DataSource;

 @Configuration public class SpringConfig {

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
