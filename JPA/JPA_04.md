# 4장 엔티티 매핑

## 대표 어노테이션

- 객체와 테이블 매핑 : @ Entity,  @  Table
- 기본 키 매핑: @ Id
- 필드와 컬럼 매핑: @ Column
- 연관관계 매핑: @ ManyToOne, @ JoinColumn

## Entity

JPA로 관리할거면 entity 어노테이션은 필수

→ 속성 : name

→ 기능: 엔티티 이름 지정, 보통 기본 값인 클래스 이름 사용, 다른 파일과

        충돌나지 않게 해야

→ 기본값: 설정하지 않으면 클래스 이름을 그대로 사용

### 주의 사항

1. 기본 생성자 필수 (파라미터 없는 public, protected)
2. final 클래스, enum, interface,inner 클래스에는 사용 불가
3. 저장할 필드에 final 사용 불가

→ JPA가 엔티티 객체 생성할 때 기본 생성자 사용하므로 생성자는 필수

`public Member(){}`

```java
public Member(String name){
	this.name = name
}
```

## @Table

→ name :맵핑할 테이블 이름 (기본값: 엔티티 이름)

→ catalog: catalog 기능이 있는 DB에서 맵핑

→ schema: schema 기능이 있는 DB에서 스키마 맵핑

→ uniqueConstraints : DDL 생성 시에 유니크 제약 조건을 만든다, 복합 유니크 제약 조건도 만들 수 있다 , 자동 생성 기능으로 DDL 만들때만 사용(?)

## 다양한 매핑 사용

1. 회원은 일반 회원과 관리자로 구분
2. 회원 가입과 수정일이 있어야 한다
3. 회원을 설명할 수 있는 필드가 있어야 한다. 이 필드는 길이 제한이 없다

```java
package jpabook.start;

import javax.persistence.*;  

@Entity
@Table(name="MEMBER")
public class Member {

    @Id
    @Column(name = "ID")
    private String id;

    @Column(name = "NAME")
    private String username;

    private Integer age;
		
		//==추가==

		//자바의 enum타입 쓰려면 @Enumerated 어노테이션으로 맵핑
		@Enumerated(enumType.String)
		private RoleType roleType;
		
		//자바의 날짜 타입은 @Temporal을 사용해서 맵핑한다 
		@Temporal(TemporalType.TIMESTAMP)
		private Date createdDate;

		@Temporal(TemporalType.TIMESTAMP)
		private Date lastModifiedDate;
		//회원 설명 필드는 길이 제한이 없다 => CLOB 타입으로 저장
		// @Lob으로 CLOB, BLOB 타입을 매핑할 수 있다 
		@Lob
		private String description;

		//getter, setter
    public String getId() {
        return id;
    }

    public void setId(String id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

## 데이터 베이스 스키마 자동 생성

- jpa는 DB 스키마를 자동 생성
- 매핑 정보와 DB 방언을 사용해서 DB 스키마 생성

⇒ 스키마 자동 생성 기능 사용하려면

`<property name = "hibernate.hbm2ddl.auto" value = "create" >`

⇒ hibernate.show_sql 속성으로 DDL 출력 가능

### [hibenate.hbm2ddl.auto](http://hibenate.hbm2ddl.auto) 속성

⇒ create : 기존 테이블 삭제 후 새로 생성

⇒ create-drop: 테이블 삭제, 생성 후 DDL 제거

⇒ update: DB 테이블과 엔티티 매핑정보 비교, 변경 사항 수정

⇒ validate: DB 테이블과 엔티티 매핑정보 일치하지 않으면 경고, 

⇒ none: 자동 생성 기능 사용하지 않으려면 none사용 or 삭제

## DDL 생성 기능

`@Column(name = "NAME", nullable = false, length = 10)`

⇒ NOT NULL 제약 조건과 문자크기 10으로 제한

`@Table(name="MEMBER", uniqueConstraints = {@UniqueConstraint ( name = "Name_Age_UNIQUE", columnNames = {"NAME", "AGE"})})`

`ALTER TABLE MEMBER ADD CONSTRAINT NAME_AGE_UNIQUE UNIQUE (NAME, AGE)`

⇒ length나 nullable 속성 포함한 이런 기능들은 DDL 생성할 때만 사용, 실행 로직엔 영향 X

⇒ 파악을 위해 붙이는 것도 좋음 

## 기본키 매핑

- 직접할당: 기본 키를 애플리케이션에서 직접 할당
- 자동 생성: 대리키 사용방식
    - IDENTITY: 기본 키 생성을 db에 위임 (DB에 의존)
    - SEQUENCE: db 시퀀스를 사용해서 기본 키 할당 (DB에 의존)
    - TABLE: 키 생성 테이블 사용(의존x)

`@Id` 는 기본 키 직접 할당

`@Id`+  `@GeneratedValue` 는 자동 생성 전략

## 기본키 직접 할당 전략

```java
@Id
@Column(name = "id")
private String id;
```

`@Id` 적용 가능한 자바 타입

⇒ 자바 기본형

⇒ 자바 래퍼형(Wrapper)

⇒ String

⇒ java.util.Date

⇒ java.sql.Date

⇒ java.math.BigDecimal

⇒ java.math.BigInteger

em.persist()로 저장하기 전에 기본키를 직접 할당

```java
Board board = new Board();
board.setId("id1") //기본키 직접 할당
em.persist(board);
```

## IDENTITY 전략

기본키 생성을 DB에 위임하는 전략 ⇒ MySQL, PostgreSQL, SQL server, DB2에서 사용

⇒ 밑의 예제는 mysql의 auto_increment

```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

⇒ Identity 전략은 테이블에 값을 저장하고 나서야 기본키 값을 구할 수 있을 때

⇒ JPA는 키 값을 얻어오기 위해 DB를 추가로 조회 

## SEQUENCE 전략

⇒ 오라클, PostgreSQL, DB2, H2 에서 사용

```java
@Entity
@SequenceGenerator(
		name = "BOARD_SEQ_GENERATOR",
		sequenceName = "BOARD_SEQ", //매핑할 db 시퀀스 이름
		initialValue = 1, allocationSize = 1)
public class Board{

	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE,
									generator = "BOARD_SEQ_GENERATOR")
	private Long id;
```

⇒ 사용 코드 

```java
Board board = new Board();
em.persist(board);
```

⇒ em.persist()를 호출 할 때 먼저 DB 시퀀스를 사용해서 식별자 조회, 조회한 식별자를 엔티티에 할당한 후에 엔티티를 영속성 컨텍스트에 저장 , 커밋하면 엔티티를 DB에 저장

즉 Identity 전략은 먼저 엔티티를 DB에 저장하고, 엔티티의 식별자에 할당
sequence는 식별자에 할당 후에 DB에 저장

### @SequenceGenerator

⇒ name: 식별자 이름(필수)

⇒ sequenceName: DB에 등록된 시퀀스 이름

⇒ initialValue: 처음 시작하는 수

⇒ allcationSize: 시퀀스 한 번 호출에 증가하는 수

⇒ catalog,schema: DB의 catalog, schema이름

## Table 전략

⇒ 키 생성 전용 테이블 하나 만들고, 이름과 값으로 사용할 칼럼 만들어서

시퀀스를 흉내냄

```java
create table MY_SEQUENCES (
	sequence_name varchar(255) not null,
	next_val bigint,
	primary key (sequence_name)
)
```

⇒ 키 생성 용도로 사용할 테이블을 만들어야 한다

 

## Auto 전략

`@GenerationType.AUTO` 는 IDENTITY, SEQUENCE, TABLE 중 하나를 자동으로 선택

⇒ 오라클은 시퀀스, MYSQL은 IDENTITY

## 필드와 컬럼 매핑: 레퍼런스

⇒ `@Column` 컬럼 매핑

1. name= 테이블 컬럼 이름
2. insertable, updatable, table은 거의 사용X (table은 엔티티 한개를 두개 이상 테이블에 매핑)
3. nullable, unique(한 컬럼에 간단히 유니크 제약조건), columnDefinition, length(String에만)
4. precision, scale (BigDecimal 또는 인티저에서 precision은 소수점 포함한 전체 자리수

    scale은 소수 자리수) 

⇒ `@Enumerated` 자바의 enum 타입을 매핑

1. EnumType.ORDINAL: ENUM순서를 데이터베이스에 저장
2. EnumType.STRING: enum 이름을 DB에 저장 

⇒ `@Temporal` 날짜 타입 매핑

1. TemporalType.DATE: date 타입과 매핑(2013-10-11)
2. TemporalType.TIME: time 타입과 매핑(11:11:11)
3. TemporalType.TIMESTAMP: timestamp 타입과 매핑(2013-10-11 11:11:11)

datetime은 mysql, timestamp는 oracle

⇒ `@Lob` CLOB, BLOB 타입 매핑

⇒ 지정할 수 있는 속성 없음

⇒ 문자면 CLOB 나머지는 BLOB

⇒ CLOB: String,char[],java.sql.CLOB

⇒ BLOB: byte[], java.sql.BLOB

⇒ `@Transient` 특정 필드를 매핑하지 않는다

⇒ `@Access` JPA가 엔티티에 접근하는 방식 지정

1. 필드 접근: AccessType.FIELD
2. 프로퍼티 접근: AccessType.PROPERTIES 접근자 사용
3. 둘 다 사용도 가능

```java
@Entity
@Access(AccessType.FIELD)
Public class Member{
	@Id
	private String id; //필드 접근이나 동일

 @Id 
	public String getId(){
		return id;//프로퍼티 접근 
}

@Id
private String id;

@Access(AccessType.PROPERTY)
public String getFullName(){
	return firstName + lastName;
}
// 둘다 사용한 경우, 필드 접근 방식 + 회원 엔티티 저장시에 first+last로 저장된다 
```