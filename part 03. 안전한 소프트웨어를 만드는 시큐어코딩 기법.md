## part 03. 안전한 소프트웨어를 만드는 시큐어코딩 기법

### 2. HTTP 와 웹 구조

#### HTTP

1) Stateless = Connectionless 연결 유지 x 

2) 요청&응답 구조



시작 : 방식(GET POST) ;HEAD, PUT, DELETE, OPTIONS   URI   http

헤더: _____________________________________________________________

​		_________________________________________________________

Content-Type, Content-Length, referer, Cookie

(본문) 



### 3 . 인증과 인가

* 인증 : 주체의 신원을 식별하는 작업
  * 인식기반: 패스워드, OTP
  * 소유기반: 메모리카드, 스마트카드
  * 신체특징기반: 홍채, 지문, 정맥, 음성
* 인가 : 주체가 자원에 접근하여 원하는 작업을 할 수 있는 권한 확인
  * 접근제어목록(ACL)
  * 접근통제표(ACM)
  * 강제적 접근통제(MAC)
  * 역할기반 접근통제(RBAC)
* 웹 인증 방식
  * 세션을 사용하지 않는 HTTP 인증
    * Basic Authentication: 인증정보를 Base64 방식으로 인코딩해 Authorization 서버에 전달
    * HTTP Digest Authentication: 로그인 요청마다 해시값을 달리(Challenge)해tj 서버에 전달, 서버는 클라이언트로부터 전달된 값과 패스워드 및 Chanllenge 수를 해시한 값을 비교해 같은지 검증한다.
    * HTTP NTLM Authentication : Chanllenge/response 방식 
    * Anonymous Authentication : 익명 계정을 이용해 서버의 자원을 클라이언트에 제공
  * 세션을 사용하는 인증
    * Form Based Authentication : HTML <form> 태그 이용해 인증정보를 서버에 전송,SSL 이용



### 4. 세션과 쿠키

#### - 세션: 서버가 사용자정보를 유지하기위해 서버의 메모리에 세션영역 할당, 세션토큰

#### - 쿠키: 사용자정보를 유지하고자 서버에서 생성해 응답헤더에 붙여 클라이언트에 전송하는 정보, 서버가 부여한 값을 다시 서버에 올려보내 서버가 동일한 클라이언트에서 왔다는 것을 식별.

cf) WebStorage: 쿠키의 용량을 키워 논 개념, 모바일 환경에서 원할한 서비스를 유지, 임시저장공간



### 5. 인코딩 스키마

- ASCII : 미국 정보 교환 표준 코드(출력가능:95, 출력불가능:33)
- URL Encoding : URL 메타문자(URL에서 제어 기능을 가진 문자)를 인코딩 할 때 사용
- HTML 인코딩 : 서버에서 <script> 태그를 보내줌, 브라우져는 실행, 출력 
- Base64 인코딩 : 64개의 데이터, 최소 6bit



### 6. 정규식

 문자열의 검색, 치환, 추출을 위해 사용되는 패턴(내가 원하는 데이터를 표현하는 방식)
