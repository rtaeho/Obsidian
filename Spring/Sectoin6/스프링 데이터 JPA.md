#### 스프링 데이터 JPA 회원 리포지토리
```java
 package hello.hellospring.repository;
 import hello.hellospring.domain.Member;

 import org.springframework.data.jpa.repository.JpaRepository;
 import java.util.Optional;

 public interface SpringDataJpaMemberRepository extends JpaRepository<Member,Long>, MemberRepository {
     Optional<Member> findByName(String name);
 }
```

#### 스프링 데이터 JPA 회원 리포지토리를 사용하도록 스프링 설정 변경
```java

 package hello.hellospring;

 import hello.hellospring.repository.*;
 import hello.hellospring.service.MemberService;
 import org.springframework.context.annotation.Bean;
 import org.springframework.context.annotation.Configuration;

 @Configuration 
 public class SpringConfig {
     private final MemberRepository memberRepository;

     public SpringConfig(MemberRepository memberRepository) {
         this.memberRepository = memberRepository;

}

     @Bean
     public MemberService memberService() {
         return new MemberService(memberRepository);
     }

}
```
스프링 데이터 JPA가 `SpringDataJpaMemberRepository` 를 스프링 빈으로 자동 등록

#### 스프링 데이터 JPA 제공 클래스
![[Pasted image 20240229184609.png]]
#### 스프링 데이터 JPA 제공 기능
인터페이스를 통한 기본적인 CRUD
`findByName()` , `findByEmail()` 처럼 메서드 이름 만으로 조회 기능 제공 페이징 기능 자동 제공