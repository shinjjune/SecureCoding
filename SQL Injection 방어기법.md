####  ORM FrameWork

query의 유지보수 생산성 향상

EX) ibatis/ mybatis/ hibernate

### SQL Injection 방어기법

1. 정적쿼리를 사용 = 구조화된 쿼리 실행 = 파라미터화된 쿼리 실행 = 입력값에 따라 쿼리문의 구조가 바뀌지 않도록 한다. = PreparedStatement 객체를 이용해서 쿼리를 실행
2. ORM 프레임워크를 사용하는 경우, 외부 입력값을 쿼리맵에 바인딩할때 반드시 #기호를 이용한다.
3. 입력값을 검증 → 외부 입력값에 쿼리 조작 문자열 포함 여부를 검증 후 쿼리문 생성 및 실행에 사용
4. 오류 메시지에 시스템 정보가 노출되지 않도록 한다. ⇒ Error-based SQL Injection 공격을 완화
5. DB 사용자의 권한을 최소로 부여한다. = 해당 어플리케이션에서 사용하는 DB 객체에 대해서만 권한을 부여한다. ⇒ Stored Procedure 또는 UNION-based SQL Injection 공격을 완화

### 운영체제 명령어 삽입(Command Injection)

운영체제 명령어 실행 부분이 존재하는 경우

외부 입력값을 검증, 제한 없이 운영체제 명령어 실행 부분에 운영체제 명령어 또는 명령어의 일부로 사용되는 경우 발생

- 외부 입력값을 검증, 제한 없이 운영체제 명령어로 사용하는 경우

⇒ 입력값이 명령어로 사용

⇒ 정상 입력 : .../doSomething.jsp?cmd=ls

⇒ 비정상 입력 : .../doSomething.jsp?cmd=ifconfig ⇒ 의도하지 않은 명령어 실행이 가능 → 서버의 제어권이 탈취될 수 있음

String input = request.getParameter("cmd");Runtime.getRuntime().exec(input);

- 외부 입력값을 **검증, 제한** 없이 운영체제 명령어의 일부로 사용하는 경우

⇒ 입력값이 명령어의 파라미터로 사용

⇒ 정상 입력 : .../doSomething.jsp?data=help.txt ⇒ c:\data\help.txt 파일의 내용을 출력

⇒ 비정상 입력 : .../doSomething.jsp?data=help.txt **%26** ipconfig ⇒ c:\data\help.txt 파일 내용과 함께 서버의 IP 정보가 함께 출력 ⇒ 의도하지 않은 **추가** 명령어 실행이 가능 → 서버의 제어권이 탈취될 수 있음

String input = request.getParameter("data");

Runtime.getRuntime().exec("cmd.exe /c type c:\data\" + input);

## 제한 방법

1. White List 방식 = 허용 목록
2. Black List 방식 = 제한 목록, 모집합의 규모가 큰 경우, 변화가 심할 때

입력값 유형 a, b, c 중 a는 안전하고, b, c는 안전하지 않은 경우,

허용 목록 = [ a ] ⇒ a,b,c, x,y → a ⇐ 새로운 유형의 입력에 대해서도 동일한 보안성을 제공한다.

제한 목록 = [ b, c ] ⇒ a,b,c, x,y → a, x,y ⇐ 검증되지 않은 새로운 유형에 대해서 방어할 수 없다.

### 운영체제 명령어 삽입 취약점 방어 기법

1. 운영체제 명령어 실행 부분의 필요성 여부를 검증 후 대체 가능 여부를 판정
2. 사용할 명령어를 미리 정의하고, 정의된 범위 내에서 사용되도록 한다. = 화이트 리스트 방식의 입력값 제한
3. 추가 명령어 실행에 사용되는 &, |, ; 등의 문자를 입력값 필터링한다.



## xPath 삽입(Injection)

XML 문서에 데이터를 저장한 웹 애플리케이션은 사용자가 입력한 내용에 대한 데이터를 찾기 위해 xPath를 사용한다. 이러한 사용자의 입력값이 검증 작업을 수행하지 않고 xPath 쿼리에 입력된다면 애플리케이션 로직을 손상시키기 위해 쿼리를 변조하거나 권한을 가지지 않고도 특정 데이터를 추출할 수 있게 된다.



## LDAP( Lightweight Directory Access Protocol) 삽입

LDAP 은 기업 사용자 정보를 집중화하고 수천명의 사용자를 논리적인 그룹으로 배분한 후 서로 다른 시스템 사이에서 통일된 사용자 정보 공유를 가능하게 해 주는 용도로 사용된다. 

사용자의 크리덴셜(로그인명, 비밀번호, 기타 상세정보)이 LDAP 내의 단일 위치에 저장되기 때문에 관리자들이 관리하기 수월하며, 아울러 그룹 또는 팀 배치, 지리적 정보, 기업 조직 구도에 따른 멤버쉽과 같은 조직 정보들도 디렉터리에서 사용자의 위치에 따라 함께 정의되는 장점이 제공된다.



## SOAP(Simple Object Access Protocol)삽입