# JPA - SQL 중심적 개발의 문제점

<br/>
현시대는 객체를 DB에 저장해 관리하는 시대다 . 그러면 관리를 위해서 숨낳은 sql 을 사용해야한다.

<br/>


### 무한 반복과 지루한 코드

<br/>

java 객체를 sql 로, sql 을 java 객체로 바꾸는 작업을 무한 반복해야한다.

<br/>

```java
// 예시
public class Member {
	private String member;
	private String name ;
}
```

<br/>

Member 라는 객체가 있다면 CRUD 를 위해

<br/>

```sql
INSERT INTO MEMBER(MEMBER_ID,NAME) VALUES .....
SELECT MEMBER_ID,NAME FROM MEMBER ....
UPDATE MEMBER SET .....
```

각각에 맞는 수 많은 쿼리를 작성하고 날려야한다.

<br/>

### 필드 추가?

```sql
INSERT INTO MEMBER(MEMBER_ID,NAME,TEL) VALUES .....
SELECT MEMBER_ID,NAME,TEL FROM MEMBER ....
UPDATE MEMBER SET .....  TEL = ?
```

<br/>

만약에 TEL 이라는 새로운 필드가 추가되는 상황이 생긴다면 직접 작성해 놓은 sql 문을 각각 전부 찾아서 수정해주어야한다. 결국에는 수정 작업에서 일어나는 오류의 가능성이 다분하기 떄문에 수정은 치명적이다.

<br/>
<br/>

## 객체와 RDB 의 차이

<br/>

### 상속

객체에는 abstract , extends, implements 등 과 같이 부모-자식 관계를 상속으로 나타낼 수 있다.

데이터베이스에는 상속의 개념이 없다. 부모 역할을 하는 테이블을 만들고 슈퍼타입 서브타입 테이블을 만들어 저장하는 방법이 있다.

객체를 데이버테이스에 저장한다고 하면?

- 각각의 테이블에 따른 join sql 을 작성
- 각가의 객체 생성

등등의 한번 저장을 위해 sql 작성 외에도 더 작업이 들어간다.

<br/>

### 자바 컬렉션에 저장한다면?

<br/>

```java
// 저장
list.add(object)

// 조회
Object object = list.get(objectId);
       // 다형성까지 활용해 조회 가능하다.

```

<br/>
<br/>

### 연관관계

<br/>

객체 : 참조를 사용한다. ex) member.getTeam()

테이블 : 외래 키 (FK)를 사용 ex) JOIN ON M.TEAM_ID = T.TEAM_ID

            테이블은 레퍼런스라는 개념이 없기 떄문에 참조가 없다.

            대신에 Primary Key - Foreign Key 를 사용한다.

<br/>

![JPA - SQL 중심적 개발의 문제점 ](https://github.com/user-attachments/assets/ab21aec3-f141-4895-b578-13d25ab91de4)


<br/>

따라서 객체를 테이블에 맞추어 모델링 해야 sql 을 작성할때 사용이 가능하다.

하지만 데이터를 넣을떄 객체에서의 Team team 은 테이블에 들어 갈수 없기 때문에 getid() 를 작성해서 id 값을 넣어 주어야하고 조회 할떄는 join 후 새로운 객체를 생성하여 관련 정보를 다시 set 해주어 객체로 변경해 return 해주어야 하는 복잡하고 불편한 상황이 생긴다.

<br/>

이 과정을 자바 컬랙션에 저장한다고 생각하면?

<br/>

```java
list.add(member);
Member member = list.get(memberId);
Team team = list.getTeam();
```

<br/>

코드가 간결해지고 참조 관계를 활용하여 저장 및 조회를 할 수 있어진다.

<br/>

또 하나 객체는 연관관계를 자유롭게 탐색할 수 있다. 하지만 sql 은 한번 조회할떄 join 한 table에 한해서만 탐색이 가능해진다.

<br/>

```java
member.team  // ok
member.category  // ok
.....

// 데이터 조회 후 (member 와 team 테이블 Join )
SELECT M.*, T.* FROM MEMBER m JOIN TEAM T ON ......

member.team  // ok
member.category  // null
```

<br/>

이렇게 된다면 service 계층에서는 repository 또는 Dao 에서 넘어온 엔티티를 신뢰할 수 없다.

따라서 Controller, Service, Repository , Dao 등등의 진정한 계층 분할이 어려워진다.

물리적으로는 분할되어 있으나 논리적으로 분리되어있지 않아 의존성이 높아진다.

<br/>

SQL 중심적 개발의 문제점은 이러한 단점과 한계를 가지고 있다. 그래서 개발자들은 컬랙션처럼 데이터를 관리할 수 없을까 하면서 만들어진 것이 JPA 다. 이러한 단점과 한계들을 매우 간결하고 간편하게 해결하는 기술이 등장했다.
