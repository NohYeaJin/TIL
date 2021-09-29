- 객체의 참조와 테이블의 외래 키를 매핑하는 것이 이 장의 목표다
- 방향: 단방향, 양방향
- 다중성: 다대일, 일대다, 일대일, 다대다
- 연관관계의 주인: 객체를 양방향 연관관계로 만들면 주인을 정해야 한다

## 단방향 연결관계

- ex: 회원과 팀
    
    ⇒ 회원 객체와 팀 객체는 `단방향` ,  회원 테이블과 팀 테이블은 `양방향` 
    
- 참조를 통한 연관관계는 언제나 단방향
    
    ⇒ 양방향을 원한다면 반대쪽에도 필드 추가해야
    
    ⇒ 그러나 정확히 말하자면 이건 단방향 2개임
    
- 반면 테이블은 외래키 하나로 양방향 조인 가능

### 순수한 객체 연관관계

```java
public class Member{
	private String id;
	private String username;
	
	private Team team;

	public void setTeam(Team team){
		this.team = team;
	}

	//Getter, Setter
}

public class Team{
		private String id;
		private String name;
		//Getter Setter
}
```

```java
public static void main(String[] args){
	//생성자
	Member member1 = new Member("member1","회원1");
	Member member2 = new Member("member2","회원2");

	Team team1 = new Team("team1","팀1");

	member1.setTeam(team1);
	member2.setTeam(team1);

	Team findTeam = member1.getTeam();
}
```

⇒ 참조로 연관관계 탐색 = `객체 그래프 탐색`

### 테이블 연관관계

- Member(id, Team team, username) ⇒ Team (id, name)
- 다대일 관계

```java
@Entity
public class Member {
	@Id
	@Column(name = "MEMBER_ID")
	private String id;

	private String username;

	//연관관계 매핑 
	@ManyToOne
	@JoinColumn(name="TEAM_ID")
	private Team team;

	public void setTeam(Team team){
		this.team = team;
	}
}
```

```java
@Entity
public class Team{
	@Id
	@Column(name = "TEAM_ID")
	private String id;
	
	private String name;
}
```

- `@ManyToOne` 말그대로 다대일 관계 매핑 정보
- `@JoinColumn(name="TEAM_ID")` 외래키 매핑할 때 사용
    
    ⇒ name 속성에는 매핑할 외래키 이름 지정 
    
    ⇒ 생략 가능 
    

### joinColumn

⇒ 외래키를 매핑할 때 사용 

⇒ name , 매핑할 외래 키 이름, 필드명 + _ + 참조하는 테이블의 기본키 컬럼명

⇒ referencedColumnName , 외래 키가 참조하는 테이블 컬럼명 , 기본키 컬럼명

⇒ foreignkey(DDL), 외래키 제약 조건 직접 지정

⇒ unique,nullable, insertable, updatable,columnDefinition, table 은 `@Column`의 속성과 같다

### ManyToOne

⇒ 다대일 관계 

⇒ optional, false로 설정하면 연관 엔티티가 항상 존재해야

⇒ fetch , 글로벌 패치 전략

⇒ cascade , 영속성 전이 기능

⇒ targetEntity , 연관된 엔티티의 타입 정보 설정(잘 사용X)

```java
@OneToMany
private List<Member> members;

@OneToMany(targetEntity = Member.class)
private List members;
```

## 연관관계 사용

## 저장