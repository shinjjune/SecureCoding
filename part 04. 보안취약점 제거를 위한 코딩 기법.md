## part 04. 보안취약점 제거를 위한 코딩 기법

### 1. 입력값 검증 부재와 삽입

* 규범화(Canonicalization): 데이터 손실 없이 입력 데이터를 가장 간단하면서 대등한 형태로 축소하는 과정
* 정규화(Normalization): 데이터 손실은 있지만 알려진 가장 간단한 형태로 변환하는 과정
* 새니타이즈(Sanitization): 데이터를 받은 서브시스템의 요구사항에 맞게 데이터를 가공하는 과정
* 검증(Validation): 입력 데이터가 프로그램의 정당한 프로그램 입력 영역 안에 있는지 확인하는 과정



#### - SQL 삽입

피해: 1. DB 정보 열람 및 추가, 수정, 삭제/ 2. 프로시저를 통해 운영체제 명령어 수행/ 3. 웹 애플리케이션을 조정		해  다른 시스템 공격/ 4. 외부 프로그램 사용/ 5. 불법 로그인/

search.jsp

\-----------------------------

String ptext = request.getParameter("text");

String query = "select * from data where keyword = '" + ptext + "'";

Statement stmt = connection.createStatement();

stmt.executeQuery(query);

정상적인 요청 ⇒ search.jsp?text=abcd

⇒ select * from data where keyword = 'abcd'

비정상적인 요청 - SQL Injection 공격 유형



#1 항상 참이 되는 입력 -> 모든내용이 반환 = 권한 밖의 데이터에 대해 접근이 가능.

⇒ search.jsp?text=abcd' or 'a' = 'a

⇒ select * from data where keyword = 'abcd' or 'a' = 'a'

⇒ data 테이블에 모든 데이터를 조회해서 반환



\#2 오류를 유발하는 입력

⇒ search.jsp?text=abcd'

⇒ select * from data where keyword = 'abcd''

→ 홑따움표의 개수가 일치하지 않아서 오류가 발생 → 오류 메시지에 대한 처리가 불완전하여 시스템 내부 정보가 사용자 화면에 출력될 수 있음

