# JPA - 영속성 컨텍스트

<br/>

### 영속성 컨텍스트란?

<br/>

엔티티를 연구 저장하는 환경

EntityManager.persist(entity) 이 코드는 사실 DB 에 저장하는 것이 아닌 영속성 컨텍스트에 저장하는 기능이다. 실체하기보다는 논리적인 개념이며 눈에 보이지 않는 공간이고 EntityManager 를 통해서 접근한다.

Spring 에서 Spring Container 와 비슷한 개념이다.

<br/>

![스크린샷 2024-08-01 오후 7.47.55.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/5bc7f1d3-8a49-42ba-a86f-2c94798fbd8f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.47.55.png)

<br/>
<br/>

### 엔티티의 생명 주기

<br/>

- 비영속 (new/transient) : 영속성 컨텍스트와 전혀 관계가 없는 새로운 상태 ( ex. 최초로 객체를 생성한 상태 )

<br/>

```java
// 객체 생성만 하고 EntityManager 에 넣지 않은 상태
Member member = new Member();
member.setId(1L);
member.setName("test");
```

<br/>
<br/>

- 영속 (managed) : 영속성 컨텍스트에 관리되는 상태

<br/>

```java
// 객체 생성
Member member = new Member();
member.setId(1L);
member.setName("test");

EntityManager em = emf.createEntityManager();
em.getTransaction().begin();

// 영속성 컨텍스트에 영속된 상태
// DB에 저장되지는 않은 상태
em.persist(member);
```

<br/>
<br/>

- 준영속 (detached) : 영속성 컨텍스트에 저장되었다가 분리된 상태

<br/>

```java
// 영속성 컨텍스트 안에 있는 객체를 지우는 상태
em.detached(member)
```

<br/>
<br/>

- 삭제 (removed) : 삭제된 상태

<br/>

```java
// 실제 DB에 있는 데이터를 지우는 상태
em.remove(member);
```

<br/>
<br/>

### 영속성 컨텍스트의 이점

<br/>

- 엔티티 조회, 1차 캐시

영속성 컨텍스트 안에 1차 캐시라고 불리는 공간이 있다.

persist 했을떄 1차 캐시 안에 @id 와 Entity 라는 개념으로 pk 와 객체가 저장이 되고

find() 를 하면 1차 캐시를 먼저 탐색해 해당하는 Entity 를 가져온다.

1차 캐시에 없다면 그 다음에 DB 로 넘어가 탐색하고 찾아온 Entity 를 1차 캐시에 저장해두고 결과값으로 반환한다.

모든 작업이 끝나고 transaction.commit()이 일어나면 1차 캐시의 객체를 종합해 한번에 DB에 SQL 을 날려 저장한다.

<br/>

- 영속 엔티티의 동일성 보장

```java
Member memberA = em.find(Member.class,"member1");
Member memberB = em.find(Member.class,"member1");

memberA == memberB // true
```

1차 캐시로 반복 가능한 읽기(REPETABLE READ) 등급의 트랜잭션 격리 수준을 데이터베이스가 아닌 애플리케이션 차원에서 제공한다. 결국엔 같은 트랜잭션 안에서 같은것을 조회 할 경우 동일성이 보장된 것이 나온다는 의미이다.

- 엔티티 등록 - 트랜잭션을 지원하는 쓰기 지연

<br/>

```java
EntityManager em = emf.createEntityManager();
EntityTransaction tx = em.getTransaction();

tx.begin();

em.persist(memberA);
em.persist(memberB);
// 여기까지 INSERT SQL 을 DB에 보내지 않는다.

// 커밋하는 순간, DB에 INSERT SQL 을 보낸다.
tx.commit(); // [트랜잭션] 커밋
```

<br/>

![스크린샷 2024-08-01 오후 9.04.39.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/83a2006c-ef6c-4c35-80fa-dd670aa32550/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.04.39.png)

<br/>

![스크린샷 2024-08-01 오후 9.07.34.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/2fb4e0b6-2f67-42d5-ae18-318abefb8cc9/6b3ae18f-da6a-44f2-9259-604febadf9dc/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-08-01_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_9.07.34.png)

<br/>

같은 트랜잭션 안에서 commit() 하기 전에 persist 했던 Entity 들에 대한 SQL 을 쓰기 지연을 통해 모아두었다가 commitI() 시점에 한번에 DB에 보낸다.

<br/>
<br/>

- 엔티티 수정 - 변경 감지(더티 체킹)

<br/>

```java
EntityManager em = emf.createEntityManager();
ENtityTransaction tx = em.getTransaction();

tx.begin(); // [트랜잭션] 시작

// 영속 엔티티 조회
Member memberA = em.find(Member.class,"memberA");

// 영속 엔티티 데이터 수정
memberA.setName("modified");

tx.commit(); // [트랜잭션] 종료
```

보통 수정하게 된다면 수정에 해당하는 SQL 을 작성해서 보내야하지 않을까 생각하지만

변경감지라는 기능 떄문에 단순히 컬랙션을 사용하듯이 데이터만 변경하고 트랜잭션 커밋을 해주면 자동으로 수정 SQL 을 날려준다.

원리는 이러하다.

1. DB에서 가져온 Entity 를 1차 캐시에 저장한다.
2. 수정한다.
3. commit()을 한다.
4. 1차 캐시에 저장된 Entity 와 commit 시점의 Entity 를 비교하고 변경사항이 있다면?

   변경 사항에 대한 UPDATE SQL 을 알아서 날려준다.

5. 결과적으로 수정하고 commit 만 날려준다면 UPDATE SQL 을 날려줄 필요가 없다.

<br/>

**플러시 란?**

영속성 컨텍스트의 변경 내용을 DB에 반영하는 것

변경 감지(더티 체킹) → 수정된 엔티티 쓰기 지연 SQL 저장소에 등록 → 쓰기지연 SQL 저장소의 쿼리를 DB에 전송

<br/>
<br/>

- 준영속

JPA 에서 관리하지 않는 상태

의도적으로 영속 상태를 detach 를 하여 준영속으로 만들 수 있다.
