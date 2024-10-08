## API URI

### 좋은 URI 설계? → 중요한 것은 리소스 식별

- 리소스의 의미
  - 등록, 수정, 조회 같은 것들은 리소스가 아니다.
  - 회원 등록, 회원 수정 …. 중에서 “회원” 이라는 개념 자체가 리소스다.
- 식별하는 방법
  - 등혹하고 수정하고 등등을 전부 배제
  - 회원 이라는 리소스만 식별 → 회원 리소스를 URI 에 매핑

<br/>
<br/>

### 리소스 식별 , URI 계층 구조 활용

회원 목록 조회 / members

회원 조회 / members/{id}

회원 등록 / members/{id}

회원 수정 / members/{id}

회원 삭제 / members/{id}

(계층 구조상 상위를 컬렉션으로 보고 복수 단어 사용 권장 member → members )

<br/>

모두 members/{id} 로 설계하면 어떻게 구분하는가?

URI 는 리소스 자체만 식별하는데 이것은 리소스와 리소스를 대상으로 하는 행위를 분리하는 것이다.
리소스(명사) : 회원 / 행위(동사) : 등록 , 수정 , 삭제

<br/>

그러면 행위는 어떻게 구분하는가?

그래서 HTTP 메서드를 사용하여 행위(동사)를 구분하면 된다.

<br/>
<br/>

### HTTP 메서드

http 메서드는 클라리언트가 서버에 요청할때 기대하는 행동이다.

- 주요 메서드
  - GET : 리소스 조회
  - POST : 요청 데이터 처리 , 주로 등록에 사용
  - PUT : 리소스를 대체, 해당 리소스가 없으면 생성
  - PATCH : 리소스 부분 변경
  - DELETE : 리소스 삭제
- 기타 메서드

  - HEAD : GET 과 동일하지만 메시지 부분을 제외하고, 상태 줄롸 헤더만 반환
  - OPTIONS : 대상 리소스에 대한 통신 가능 옵견(메서드)을 설면 (주로 CORS 에서 사용)
  - CONNECT : 대상 자원으로 식별되는 서버에 대한 터널을 설정
  - TRACE : 대상 리소스에 대한 경로를 메시지 루프백 테스트를 수행

<br/>
<br/>

- GET 메서드

리소스 조회 , 서버에 전달하고 싶은 데이터는 query(쿼리 파라미터, 쿼리 스트링) 를 통해서 전달

메시지 바디를 사용해서 데이터를 전달할 수 있지만, 지원하지 않는 곳이 많아서 권장하지 않음

<br/>
<br/>

- POST 메서드

요청 데이터 처리, 메시지 바디를 통해 서버로 요청 데이터 전달

서버는 요청 데이터를 처리 / 메시지 바디를 통해 들어온 데이터를 처리하는 모든 기능을 수행한다.

주로 전달도니 데이터로 신규 리소스 등록, 프로세스 처리에 사용된다.

요청 데이터를 어떻게 처리한다는 뜻일까?

대상 리소스가 리소스의 고유 한 의미 체계에 따라 요청에 포함된 표현을 처리하도록 요청

- HTML 양식에 입력된 필드와 같은 데이터 블록을 데이터 처리 프로세스에 제공 - 회원 가입, 주문 등
- 게시판 그룹 , 메일링 리스트, 블로그 또는 유사한 기사 그룹에 메시지 게시 - 게시판 글쓰기, 갯글 달기
- 서버가 아직 식별하지 않은 새 리소스 생성 - 신규 주문 생성
- 기존 자원에 데이터 추가 - 한 문서 끝에 내용 추가하기

즉, 이 리소스 URI 에 POST 요청이 오면 요청 데이터를 어떻게 처리할지 리소스마다 따로 정해야함 → 리소스마다 스스로 정해야한다.

<br/>

- 정리

  1. 새 리소스 생성 ( 등록 )
     - 서버가 아직 식별하지 않은 새 리소스 생성
  2. 요청 데이터 처리

     - 단순히 데이터를 생성하거나, 변경하는 것을 넘어서 프로세스를 처리해야 하는 경우

     예 ) 주문 과정에서 결제 완료 → 배달 시작 → 배달 완료 와 같이 단순히 값 변경을 넘어 프로세스 상태가 변경 되는 경우

     - POST 의 결과로 새로운 리소스가 생성 되지 않을 수도 있음

     예 ) POST /orders/{orderId}/start-delivery (컨트롤 URI)

  3. 다른 메서드로 처리하기 애매한 경우
     - JSON으로 조회 데이터를 넘겨야 하는데 , GET 메서드를 사용하기 어려운 경우 → 애매하면 POST

<br/>
<br/>

- PUT 메서드

리소스를 대체할때 사용한다. 있다면? → 대체 (덮어버림) / 없다면? → 생성

“클라이언트가 리소스를 식별한다” (중요)

POST 와 차이점으로 중요한 점이 클라이언트가 리소스 위치를 알고 URI 를 지정하는 점이다.

그리고 조심해야할 점은 PUT 은 리소스를 “완전히” 대체하는 점이라 부분만 바꾸는 것이 불가능하다.

<br/>
<br/>

- PATCH 메서드

PUT 의 부분만 바꾸지 못하는 단점이 PATCH 는 가능해졌다.

가끔 PATCH 를 지원하지 않는 경우가 있는데 이럴떈 PUT 말고 POST 를 활용하자.

<br/>
<br/>

- DELETE 메서드

리소스를 제거한다.
