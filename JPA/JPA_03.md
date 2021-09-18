- jpa는 엔티티와 테이블을 매핑하는 설계부분, 매핑한 엔티티 사용 부분
- 엔티티 메니저는 엔티티 저장, 수정, 삭제, 조회

⇒ 가상의 DB라고 생각 

## 엔티티 매니저 팩토리와 엔티티 매니저

- DB를 하나만 사용하는 애플리케이션은 Entity Manager Factory 하나 생성

```java
 //공장 만들기 => 비용이 아주 많이 든다
EntityManagerFactory emf = Persistence.createEntityManagerFactory("jpabook");

//매니저 만들기 => 비용이 많이 절약된다
EntityManager em = emf.createEntityManager();
```

- 공장은 하나 만들어서 전체에서 공유
- 공장에서 엔티티 매니저 생성 비용은 거의 없음
- 엔티티 매니저 팩토리는 여러 스레드 동시접근 가능, 스레드 간 공유 가능
- 엔티티 매니저는 여러 스레드 동시 접근시 문제 발생 , 공유 불가

```java
<persistence-unit name="jpabook">

        <properties>

            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect" />

            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true" />
            <property name="hibernate.format_sql" value="true" />
            <property name="hibernate.use_sql_comments" value="true" />
            <property name="hibernate.id.new_generator_mappings" value="true" />

            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
```

- jpa 구현체들은 EntityManagerFactory 생성할 때 커넥션풀도 만드는데, 이건 J2SE 환경에서 사용하는 방법 (나중에 나올 예정)

## 영속성 컨텍스트

- `엔티티를 영구 저장하는 환경`
- 엔티티 매니저로 엔티티를 저장하거나 조회하면 엔티티 매니저는 영속성 컨텍스트에 엔티티를 보관하거나 관리
- `persist()` 메소드는 엔티티 매니저로 회원 엔티티를 `영속성 컨텍스트`에 저장

    영속성 컨텍스트는 논리적인 개념, 매니저로 접근, 관리 가능

## 엔티티 생명주기

- 비영속(new/transient): 영속성 컨텍스트와 전혀 관계 없는 상태

- 영속(managed): 컨텍스트에 저장된 상태

    em.find()나 JPQL을 사용해서 조회한 엔티티도 영속성 컨텍스트가 관리하는 영속 상태

- 준영속(detached): 영속성 컨텍스트에 저장되었다가 분리된 상태

- 삭제(removed): 삭제된 상태