# JPA - JPA란?

<br/>

### JPA란?

<br/>

Java Persistance API  : 자바 진영의 ORM 기술 표준이다.

EJB 라는 기존의 Entity Bean 이 있었다. 하지만 단점이 너무나 많아서 다른 개발자가 하이버네이트를 개발해 오픈 소스로 공유했는데 여러 개발자들이 사용하기 시작해서 JAVA 개발자들이 표준으로 들여 JPA 로 자리잡기 시작했다. 

<br/>
<br/>

### ORM 이란?

<br/>

Object-Relational-Mapping (객체 관계 매핑) 

객체와 관계형 데이터베이스 (RDB) 를 매핑해주는 기술로서 객체는 객체대로 설계하고 데이터베이스는 RDB 대로 설계하고 그 중간에서 ORM 프레임워크가 각각 매핑해줘서 SQL 이라던지 페러다임 불일치 등등 의 문제들을 해결을 해준다.  

<br/>
<br/>

### JPA 동작

<br/>

<img width="779" alt="1" src="https://github.com/user-attachments/assets/dc69f2bf-7f55-4925-bba9-140e29f94e19">

<br/>

JPA 는 어플리케이션과 JDBC 사이에서 동작한다. 

DB에 데이터 작업을 요청하려면 JDBC 를 사용해야한다. 그 작업에 대한 코드를 기존에는 개발자가 작성했었다. 

그 작업들을 mybatis 나 jdbc templete 과 같이 JPA 가 대신 해주는 역할이다. 

하지만 JPA 는 다른 프레임워크와는 다른 점들이 있다. 

<img width="784" alt="2" src="https://github.com/user-attachments/assets/5344516c-8b4f-4127-a01e-68835c3e0d68">

DAO 에서 Entity 를 객체로 JPA 에 persist 해주면 JPA 는 Entity 를 분석해서 INSERT SQL 를 RDB 에 맞는 문법으로 자동으로 작성해줘서 JDBC API 를 사용해 DB 로 날려주고 이 과정과 결과에서 일어나는 패러다임 불일치하는 부분들을 알아서 Object 로 변형시켜주면서 해결해준다. 

<br/>
<br/>

## JPA 의 장점

<br/>

### SQL 중심적인 개발에서 객체 중심으로 개발로 바뀌며 생산성, 유지보수,성능 등이 증가했다.

<br/>

- 저장 : jpa.persist(member)
- 조회 : Member member = jpa.find(memberId)
- 수정 : member.setName(”     “ )
- 삭제 : jpa.remove(member)

대표적인 접근법을 보면 기존에 객체를 다루는 방법과 매우 유사하여 유지보수에 아주 뛰어나다. 

<br/>
<br/>


### 패러다임 불일치 해결

<br/>

- JPA 와의 상속

```java
// 저장 
jpa.persist(member)
// 이후 jpa 처리
INSERT INTO MEMBER .... 
INSERT INTO ADDRESS .... 

// 저장 
Member member = jpa.find(Member.class, memberId)
// 이후 jpa 처리
SELECT M.*, A.* FROM MEMBER M JOIN ADDRESS A ON M.ADDRESS_ID = A.ADDRESS_ID

```

<br/>
<br/>

- 연관관계, 객체 그래프 탐색

```java
// 연관관계 저장
member.setTeam(team);
jpa.persist(member);

// 객체 그래프 탐색 
Member member = jpa.find(Member.class,memberId);
Team team = member.getTeam();
```

<br/>

기존에는 FK 가 있는 테이블을 Join 해서 등등 의 작업이 한정된 범위만 가능했지만 JPA 를 사용하면 

간단한 코드만으로 객체와 같이 그래프 탐색이 자유로워진다. 

이것만으로 기존의 계층간의 분리가 어려워지고 엔티티에서 오는 데이터를 신뢰할 수 없었던 것을 지연 로딩이라는 기술로 해결하였다. 

<br/>
<br/>

- 비교하기 ( 성능 최적화 기능 )

<br/>

1. 1차 캐시와 동일성(identity) 보장 

```java
String memberId = "100";
Member member1 = jpa.find(Member.class,memberId)
Member member2 = jpa.find(Member.class,memberId)

member1 == member2 // true 
```

동일한 transactional 안에서는 같음이 보장되어 같은 엔티티를 반환하며 조회 성능이 향상된다. 

DB Isolation Level 이 Read Commit 이어도 어플리케이션에서 Repeatable Read 보장한다. 

<br/>
<br/>

2. 트랜잭션을 지원하는 쓰기 지연 (transactional write-behind)

```java
transaction.begin(); // 트랜잭션 시작 

em.persist(memberA);
em.persist(memberB);
em.persist(memberC); // 여기까지 INSERT SQL DB에 보내지 않는다. 

transaction.commit(); // 커밋하는 순간 데이터베이스에 INSSERT SQL 을 모아서 보낸다. 
```

트랜잭션을 커밋할 때까지 INSERT SQL 을 모으고 JDBC BATCH SQL 기능을 사용해서 한번에 SQL을 전송한다.  버퍼 라이팅 기술로 추후에 배치 SQL 작업을할 떄 성능을 많이 끌어올릴수 있다.

<br/>
<br/>

3. 지연 로딩 (Lazy-Loading) 과 즉시 로딩

```java
// 지연 로딩 : 객체가 실제 사용될떄 로딩
Member member = memberDAO.find(memberId); // SELECT * FROM MEMBER
Team team = member.getTeam();
String teamName = team.getName(); // SELECT * FROM TEAM

// 즉시 로딩 : JOIN SQL 로 한번에 연관된 객체까지 미리 조회
Member member = memberDAO.find(memberId); // SELECT M.*,T.* FROM MEMBER M JOIN ... 
Team team = member.getTeam();
String teamName = team.getName(); 
```

상황마다 연관관계의 데이터가 필요할 떄 안 필요할때 가끔 필요할떄 등의 필요도가 다른데 지연 로딩과 즉시 로딩을 선택적으로 적용하여 성능을 최적화 할 수 있다.
