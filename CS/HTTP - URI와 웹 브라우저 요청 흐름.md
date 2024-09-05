## URI ( Uniform Resource Identifier )

URI 는 로케이터 (locator) , 이름 (name) 또는 줄 다 추가로 분류 될 수 있다.

그래서 URI 라는 큰 개념 안에 URL, URN 으로 나뉘게 된다.

<img width="670" alt="image 1" src="https://github.com/user-attachments/assets/dbe8568c-7243-4c13-9900-bd51ecbfe80f">

<br/>
<br/>

## URI 정의

- Uniform : 리소스 식별하는 통일된 방식
- Resource : 자원 , URI로 식별할 수 있는 모든 것 ( 제한 없음 )
- Identifier : 다른 항목과 구분하는데 필요한 정보

- URL - Locator : 리소스가 있는 위치를 지정
- URN - Name : 리소스가 이름을 부여
- 위치는 변할 수 있지만 , 이름은 변하지 않는다.
- urn:isbn:8960777331 ( 어떤 택의 isbn URN )
- URN 이름만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

## URL

<br/>
<br/>

### PORT

- scheme://[userinfo@]host**[:port]**[/path][?query][#fragment]
- https://www.google.com**:443**/search?q=hello&hl=ko

- 프로토콜 ( https )
- 호스트명 ( [www.google.com](http://www.google.com) )
- 포트 번호 ( 443 )
- 패스 ( /search )
- 쿼리 파라미터 (q=hello&hl=ko )

### scheme

- 주로 프로토콜 사용
- 프로토콜 : 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙 ex ) http, https, ftp 등등
- http 는 80 포트 , https 는 443 포트를 주로 사용, 포트는 생략 가능
- https 는 http에 보안 추가 ( HTTP Secure )

### userinfo

- URL 에 사용자 정보를 포함해서 인증
- 거의 사용안함

### host

- 호스트명
- 도메인명 또는 IP 주소를 직접 사용가능

### PORT

- 접속포트
- 일반적으로 생략, 생략시 http 는 80 , https 는 443

### path

- 리소스 경로, 계층적 구조
- Ex ) /home/file1.jpg

### qeury

- key=value 형태
- ? 로 시작 , &로 추가 가능 ?keyA=valueA&keyB=valueB
- queyr parameter, query string 등으로 불림, 웹서버에 제공하는 파라미터, 문자 형태

### fragment

- html 내부 북마크 등에 사용
- 서버에 전송하는 정보 아님

<br/>
<br/>

## 웹 브라우저 요청 흐름

- https://www.google.com**:443**/search?q=hello&hl=ko

해당 주소로 요청이 되었다 가정하면 다음과 같은 흐름으로 동작하게 된다.

1. DNS 조회 : IP : 200.200.200.2 - > HTTPS PORT 생략, 443 → HTTP 요청 메세지 생성 (애플리케이션 계층 )
2. SOCKET 라이브러리를 통해 OS 계층으로 전달
3. HTTP 메세지를 포함하여 TCP/IP 패킷을 생성 ( OS 계층 )
4. LAN 을 통해 IP 주소에 해당하는 서버에 전송
5. HTTP 응답 메세지 전송
6. HTML 렌더링
