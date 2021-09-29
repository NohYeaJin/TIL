- `Criteria`나 `QueryDSL` (SQL을 자바코드로 동적으로 만드는 기술)은

결국 JPQL( `객체지향 쿼리언어` ) 을 편리하게 사용하기 위함임

## 객체지향 쿼리란?

- 테이블이 아닌 객체를 대상으로 검색하는 객체지향 쿼리
- SQL을 추상화해서 특정 DB에 의존하지 않는다

JPQL 사용 ⇒ JPA가 해석 ⇒ 적절한 SQL 만들어서 DB 조회 ⇒ 엔티티 생성후 반환

JPA가 공식 지원하는 기능

- JPQL
- Criteria 쿼리 (JPQL 작성 도와주는 API)
- 네이티브 SQL (JPQL대신 SQL 사용 가능)
- QueryDSL (JPQL 작성 도와주는 오픈소스 프레임워크)
- JDBC 직접 사용 , MyBatis같은 매퍼 사용

### JPQL

⇒ 엔티티 객체를 조회하는 객체지향 쿼리 

⇒ SQL보다 간결 

```java
@Column(name="name")
private String username;
```

```java
//쿼리 생성
String jpql = "select m from Member as m where m.username='kim'";
List<Member> resultList = em.createQuery(jpql,Member.class).getResultList();
```

⇒ Member는 엔티티 이름, m.username은 엔티티 객체의 필드명

⇒ createQuery에 jpql과 반환할 엔티티 클래스 타입 넘겨주고 

⇒ getResultList() 로 JPQL을 SQL로 변환해서 DB 조회 

⇒ 조회 결과로 Member 엔티티 생성 후 반환

```sql
select 
	member.id as id,
	member.age as age,
	member.team_id as team,
	member.name as name
from
	Member member
where 
	member.name = 'kim'
```

### Criteria 소개

⇒ 문자가 아닌, query.select(m).where(...) 처럼 프로그래밍 코드로 JPQL 작성 가능

⇒ 따라서 컴파일 시점에서 오류를 발견 가능

```java
//Criteria 사용 준비 
CriteriaBuilder cb = em.getCriteriaBuilder();
CriteriaQuery<Member> query = cb.createQuery(Member.class);

//루트 클래스(조회를 시작할 클래스)
Root<Member> m = query.from(Member.class);

//쿼리 생성
CriteriaQuery<Member> cq = query.select(m).where(cb.equal(m.get("username"),"kim"));
List<Member> resultList = em.createQuery(cq).getResultList();
```

⇒ username 이 문자인게 흠 ⇒ 메타 모델로 수정 가능 

⇒ JPA는 어노테이션 프로세서로 member 엔티티 클래스로부터 Member_ 라는 Criteria 전용 클래스 생성

```java
//메타 모델 사용 전 -> 사용 후
m.get("username") => m.get(Member_.username)
```

⇒ Criteria 는 상당히 장황한 편

### QueryDSL 소개

⇒ 코드 기반 + 단순 + 쉬움 + 한눈에 들어옴

```java
//준비
JPAQuery query = new JPAQuery(em);
QMember member = Qmember.member;

//쿼리 결과 조회
List<Member> members = query.from(member).where(member.username.eq("kim")).list(member);

```

⇒ 어노테이션 프로세서로 쿼리 전용 클래스를 만들어줘야 ⇒ QMember는 쿼리 전용 클래스

### 네이티브 SQL 소개

⇒ sql을 직접 사용할 수 있는 기능

⇒ 특정 DB에 의존해야 할 때 ⇒ oracle의 connect by 기능, SQL 힌트

⇒ 표준화되어 있지 않은 기능 사용 위해 

```java
String sql = "SELECT ID,AGE,TEAM_ID, NAME FROM MEMBER WHERE NAME='KIM'";
List<Member> resultList = em.createNativeQuery(sql, Member.class).getResultList();
```

### JDBC 사용

아는 내용이라 넘어감

⇒ 스프링을 사용하면 JPA와 마이바티스를 쉽게 통합 가능

AOP를 활용 + DB에 접근하는 메소드를 호출할 때마다 영속성 컨텍스트를 플러시 하면 해결

(mybatis의 문제: 트랜잭션으로 가격을 1000원에서 2000원으로 변경했지만 아직 플러쉬되지 않았기 때문에 조회하면 1000원인 `영속성 컨텍스트 동기화 문제`)

## JPQL

### 기본 문법과 쿼리 API

⇒ select, update, delete 문을 사용 가능 , insert는 EntityManager.persist() 있으므로 쓰지 않음

```java
select_문 :: =
	select 절
	from 절
	[where 절]
	[groupby_절]
	[having_절]
	[orderby_절]

update_문 :: = update_절 [where 절]
delete_문 :: = delete_절 [where 절]
```

### select 문

`SELECT m FROM Member AS m where m.username = 'Hello'`

⇒ 엔티티와 속성(Member, username)은 대소문자 구별

⇒ Member는 엔티티 이름(클래스 명 X) `@Entity(name="xx")`로 지정

⇒ 별칭은 필수(Member m 이라고 별칭) 

### TypeQuery, Query

JPQL을 실행하려면 쿼리 객체를 만들어야 

⇒ 반환 타입이 명확하면 TypeQuery

⇒ 명확하지 않으면 Query

```java
TypedQuery<Member> query = em.createQuery("SELECT m FROM Member m", Member.class);

List<Member> resultList = query.getResultList();
for(Member member: resultList){
	System.out.println("member = " + member);
}
```

```java
Query query = em.createQuery("SELECT m.username, m.age from Member m");
List resultList = query.getResultList();

for (Object o : resultList) {
	Object[] result = (Object[]) o;
	System.out.println("username = " + result[0]);
	System.out.println("age = " + result[1]);
```