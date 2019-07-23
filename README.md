# SecureCoding

이론강의(Typora로 작성)


## 실무 보안 기술

## 2019.07.22

### - SQL injection

외부 입력값을 쿼리 조작 문자열 포함 여부를 검증하지 않고 쿼리 작성 및 실행에 사용하는 경우, 쿼리의 구조와 의미가 변경되서 실행되는 것

```
- 데이터베이스 또는 데이터에 대해 공격 -> 권한 밖의 데이터를 조회, 수정, 삭제, 생성
- 서버에 대한 공격 -> 데이터베이스가 동작하는 서버의 제어권을 획득해서 원격지에서 해당 서버를 제어
```

### - XSS 공격(Cross-Site Scripting)

크로스 사이트 스크립트(팅)

공격자가 전달한 스크립트 코드가 사용자 브라우져를 통해서 실행되는 것

```
- 브라우저에 저장된 또는 pc에 저장된 정보를 탈취
- 가짜 페이지를 만들고 사용자로 하여금 입력을 유도해서 입력값을 탈취
- 해당 pc의 제어권을 탈취하여 원격에서 제어(좀비화) ---> BeEF(f/w)
```



​	cf) 반사 xss

### - Command Injection

​	운영체제 명령어 삽입

: 영체제 명령어 실행 부분이 존재하는 경우

외부 입력값을 검증, 제한 없이 운영체제 명령어 실행 부분에 

운영체제 명령어 또는 명령어의 일부로 사용되는 경우 발생



//외부입력값을 검증 제한 없이 명령어로 사용하는 경우

//.../doSomething.jsp?cmd=dir -> 서버에서 dir한 결과가 반환

//의도하지 않은 명령어가 전달되어서 실행될 수 있다. = 서버 제어권이 공격자에게 넘어감

//.../doSomething.jsp?cmd=ipconfig

String input = request.getParameter("cmd");

Runtime.getRuntime( ).exec(input);



//**외부 입력값을 검증 제한 없이 명령어의 일부로 사용하는 경우**

//...\doSomething.jsp?fname=help.txt -> c:\data\help.txt 내용이 화면에 출력

//...\doSomething.jsp?fname=help.txt %26 ipconfig ->**의도하지 않은 추가 명령어가 실행된다.**

String input= request.getParameter("cmd");

Runtime.getRuntime( ).exec("type c:\data\" + input);



### - CSRF(크로스사이트 요청위조,Cross-site request forgery)

서버로 전달된 요청을 요청 절차와 주체에 대한 검증을 수행하지 않고 요청을 처리했을 때 발생

referer(요청이 발생한 위치): 순차적 요청이 필요한 곳



### - 동적 웹페이지

사용자가 요청하는 시점에 특정한 조건에 따라 내용이 다르게 보이도록...CGI(common gateway interface)

parameter(인자) --> web application server (WAS)

### - 정적...

: 이미지(gif,jpg,png), html, JavaScript,css --> web server

### - 세션

클라이언트와 서버사이 연결되어있다는 것을 알려준다. 관계가 있다.

세션관리가 중요하다. 정보유출관리

http: stateless, connectionless, 연결지향적이지 않다.

- 세션하이재킹 : 서버를 속인다. 

### - 쿠키 (Cookie)

; 흔적, 요청과 요청 사이의 관계를 이어주는



- 입력값 검증 : 입력값의 정상 여부를 확인하는 과정 

 EX) ID 중복 체크 로직을 구현

#1 입력화면 

ID: ________________________[ 체크]

#2 사용자 입력을 요청 파라미터로 서버 전달

http: //.../check.jsp?id=__________________

#3 요청 파라미터로 전단된 값은 서버 내부 처리를 위해서 사용

select *from users where id =____________________



### - owasp(와스프)

2019.07.23

### - DRM/DLP

### - 패스워드

#### 1) 생성 /변경 

-영 숫 특 8자리 이상/ 2가지 10자리 이상

-사전에 등록된 pw 사용 x

-규칙성 x

-개인정보 x

#### 2) 계정(보관)

해쉬 : 유일한 값을 뽑아냄(길이일정/유일/단(일)방향성) a!=b=> H(a)!=H(b)

​	-일반적인 암호화: 양방향, KEY

​	-비도?128?256? 암호화 강도

​	-단방향

#### 3) 관리정책

​	-변경주기(3~6개월)

​	-히스토리 관리

​	-최소사용기간





## SecureCoding 개발보안 필요성!

​		1) 취약점 DB:cwe, cve, sans top25,owfsp top10

​		2) securecoding guide : cert, 개발보안가이드

​		3) 방법론: MS-SDL, 7touch-point, CLASP





- CWE(Common Weakness Enumeration): 원인 vs CVE(Common Vulerabilities and Exposures): 공격결과

- CWSS(Common Weakness Scoring System): 1위가 가장 위험함

- SANS top 25 : 시대가 바뀌면서 cwss순서가 바뀜,  3가지 카테코리로 나눔

  - 구성요소간 안전하지 않은 상호작용(6개)

    - 기능의 특수문자 SQL: ', #, ;, _ /URL: ?, & =  =>메타문자(의미)
    - 이스케이프: 의미를 떼고 문자 그 자체로 남긴다.
    - SQL-map,pangolin
    - Command Injection: 운영체제 명령어 삽입

  - 위험한 자원관리(8개)

    - 특정 디렉터리에 존재하는 파일을 내려주는 서비스 -> 파일 다운로드

      ​	.../download.jsp?filename=abc.gif

      ​	new File("/data/upload/" + filename);

      ​	.../download.jsp?filename=../../../../../../etc/passwd 

      ​	-> 경로조작 -> 접근할 수 없는 디렉터리와 파일에 접근이 가능해진다.

    - 형식문자열(format string): 출력형식을 지정하는 문자, 처리 함수를 사용

  - 허점이 많은 방어, 방어 미비(11개)

    - 입력  --> 처리 --> 출력

      1) 신뢰하는 입력 => 자기가 가지고 있는 값 = 서버

      2) 신뢰할수 없는 입력(사용을 최소화) => 외부입력 값 => 사용자 입력

    - 암호화 : 

      1) 안전한 알고리즘 : 비도는 키길이에 비례. 인코딩/디코딩 안전x

      2) 키길이 준수

      3) 키 보관

    - 인증시도 회수제한

    - 솔트(->해시:고정된크기, 유일한): 크래킹을 방어 시스템이 부여하는 임의의 값

  - PG(payment gateway): 결제 대행 업체

- OWASP TOP 10

- CERT(Computer Emergency Response Team)





- 정적: 소스코드(White-Box)->코드리뷰: 형태, 문법(구문), 내용
- 동적: 실행파일(Black-Box)->디버깅, 부하테스트, 모의해킹(침투테스트): (결과)값, 성능, 보안



## -왜 SW 개발 보안을 해야하는가

- 릴리즈 이전 SW 취약점을 50% 줄이면, 침해사고 대응 비용 75% 감소 -Garther
- 보안 취약점의 92% 는 네트워크가 아닌 애플리케이션에서 발견 -NIST
- 릴리즈 이후 오류 수정은 약 $30,000의 비용 소요, 하지만 개발 중 오류 수정은 약 $5,000면 충분-NIST
- 릴리즈 이후 오류 수정하고자 할 경우 설계 단계보다 비용 100배 증가 -IBM
