


# 데이터베이스란?

- Data(자료) + Base(저장소)
- 프로그램 실행, 종료 여부에 관계 없이 데이터 저장 가능

## 데이터베이스 관리 시스템 (DBMS)

- **데이터베이스 관리용 소프트웨어**
- 여러 응용 소프트웨어(프로그램) 또는 시스템이 동시에 데이터베이스에 접근하여 사용 가능
- **데이터 저장 형태에 따라 RDBMS, NoSQL로 나눌 수 있음**
    - **RDBMS** : 데이터들의 관계를 기반으로 한 정형 데이터
    - **NoSQL** : key-value값으로 이루어진 비정형 데이터

# SQL

- 데이터베이스에서 데이터를 추출하고 조작하는데 사용하는 `데이터 처리 언어`
- 오라클 삭제하기힘드므로 설치시 삭제시 주의!

## DDL, DML, DCL

### DDL

- 외벽을 만드는 것
- 한번 만들고 끝

### DML

- 외벽을 만든 공간에 데이터가 수정이 가능한 것
- 매일 하는 것이므로 외워야한다.
    
    *따라치지말고 외우려고 노력하자*
    

## 테이블 생성하기 - CREATE

### 테이블 정의하기

- 자바의 `class`와 같은 것

```sql
CREATE TABLE MEMBER
```

## 오라클 SQL 데이터타입

- 공식문서

[SQL Language Reference](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Data-Types.html#GUID-94A82966-D380-4583-9AF1-AEE681881E64)

- 표준화 SQL의 데이터 타입이 있고, 파생된 SQL 각자의 데이터 타입이 존재한다.

### Oracle Built-in Data Types

- 오라클 내에서 사용하는 데이터타입
- 구분자는 `_`를 이용한다.
- 주석:`—-`

### Character

- 일반적으로 `싱글따옴표` 사용! (더블 따옴표x)
- `문자, 문자열 구분 x`
- 핸드폰 번호나 주민번호같은 경우, 숫자지만 930을 구백삼십이라고 하지 않기 때문에 문자열로 취급
- 영어권은 1byte = 1글자, 한글은 2byte이상이 한글자, ⇒ `한글은 CHAR로 글자수를 정할 필요가 있음.`
    
    ```sql
    NAME CHAR(100) -- CHAR로 등록
    ```
    
- CSS 등으로 인해 글자수가 엄청나게 필요할 수 있음⇒ # 데이터베이스란?

- Data(자료) + Base(저장소)
- 프로그램 실행, 종료 여부에 관계 없이 데이터 저장 가능

## 데이터베이스 관리 시스템 (DBMS)

- **데이터베이스 관리용 소프트웨어**
- 여러 응용 소프트웨어(프로그램) 또는 시스템이 동시에 데이터베이스에 접근하여 사용 가능
- **데이터 저장 형태에 따라 RDBMS, NoSQL로 나눌 수 있음**
    - **RDBMS** : 데이터들의 관계를 기반으로 한 정형 데이터
    - **NoSQL** : key-value값으로 이루어진 비정형 데이터

# SQL

- 데이터베이스에서 데이터를 추출하고 조작하는데 사용하는 `데이터 처리 언어`
- 오라클 삭제하기힘드므로 설치시 삭제시 주의!

## DDL, DML, DCL

### DDL

- 외벽을 만드는 것
- 한번 만들고 끝

### DML

- 외벽을 만든 공간에 데이터가 수정이 가능한 것
- 매일 하는 것이므로 외워야한다.
    
    *따라치지말고 외우려고 노력하자*
    

## 테이블 생성하기 - CREATE

### 테이블 정의하기

- 자바의 class와 같은 것

```sql
CREATE TABLE MEMBER
```

## 오라클 SQL 데이터타입

- 공식문서

[SQL Language Reference](https://docs.oracle.com/en/database/oracle/oracle-database/19/sqlrf/Data-Types.html#GUID-94A82966-D380-4583-9AF1-AEE681881E64)

- 표준화 SQL의 데이터 타입이 있고, 파생된 SQL 각자의 데이터 타입이 존재한다.

### Oracle Built-in Data Types

- 오라클 내에서 사용하는 데이터타입
- 구분자는 `_`를 이용한다.
- 주석:`—-`

### **Character**

- 일반적으로 `싱글따옴표` 사용! (더블 따옴표x)
- `문자, 문자열 구분 x`
- 핸드폰 번호나 주민번호같은 경우, 숫자지만 930을 구백삼십이라고 하지 않기 때문에 문자열로 취급
    - 근데 하이픈을 넣는게 좋나? 안넣는게 좋나?
- 영어권은 1byte = 1글자, 한글은 2byte이상이 한글자, ⇒ `한글은 CHAR로 글자수를 정할 필요가 있음.`
    
    ```sql
    NAME CHAR(100) // CHAR로 등록
    ```
    
- CSS 등으로 인해 글자수가 엄청나게 필요할 수 있음⇒ LONG 이용
- **CHAR, VARCHAR, NCHAR, NVARCHAR**
    - `CHAR` - **고정형** 문자
        - CHAR(8)로 하면 값 상관없이 8byte의 공간 차지.
    - `VARCHAR2` - **가변형** 문자
        - VARCHAR(100)로 해서 값이 2개 들어오면 나머지 98byte는 넘겨줌.
    - `NCHAR` (national CHAR)
        - `다국어`의 사용을 원활히하게 하기위해 사용
        - NCHAR(3) ⇒ 한글이든 영어든 **무조건 3글자**가 들어갈 수 있다!

    ### NVARCHAR ⇒ UTF16, VARCHAR ⇒ UTF8

    - **단일 언어만 입력 (NVARCHAR 이용)**
        - 바이트 점유 수 : `NVARCHAR < VARCHAR`
    - **숫자, 영문자 위주 입력 (VARCHAR 이용)**
        - 바이트 점유 수 : `NVARCHAR > VARCHAR`

    ### MAX SIZE인 4000byte를 넘었을 경우
    - LONG
        - 한 테이블의 한 칼럼만 가능
    - CLOB, NCLOB
        - 여러 테이블 사용 가능

### **Numeric**

- 숫자형식 `NUMBER[(p [,s])]`

### **Date**

- **Date** : 날짜만
- **TIMESTAMP** : 날짜 + 시간

### **LOB**

## 데이터 테이블에 올리기

### 임시 : INSERT

- `INSERT INTO` 테이블명 VALUES(’ ’,’ ’)

### 저장 COMMIT

### 되돌리기 ROLLBACK

- 커밋 전의 내용은 싹다 테이블에서 지워짐
- 삭제시에 롤백을 안하면 삭제가 안됌.
- 테이블은 삭제가 안된다. 그럼 테이블 생성은 자동 커밋이 되는거?

- LENGTH

  - 글자 길이 출력
  - LENGTHB : 바이트 출력

## 테이블 삭제

`DROP TABLE TABLE명`

## 테이블 수정하기

### ALTER

ALTER TABLE TABLE명 `MODIFY` 타입

ALTER TABLE TABLE명 `ADD` 타입

ALTER TABLE TABLE명 `DROP` 타입

# DML

## INSERT

`INSERT INTO <테이블> VALUES <값목록>`

- 필수와 필수가 아닌 값의 컬럼 지정하기
  
    - 필수일 경우 `테이블명()`을 이용한다
  
    - 필수가 아닌 값은 `null값`이 대입된다.


## SELECT

- 모든 필드(컬럼)값 `조회`
    - SELECT * FROM MEMBER;
    - SELECT MEMBER.* FROM MEMBER;
- 원하는 컬럼 별칭으로 조회하기
    - SELECT ID `AS USER_ID`, NAME, PWD `AS PASSWORD` FROM MEMBER;
    - SELECT ID `USER_ID`, NAME, PWD `AS PASSWORD` FROM MEMBER;
    - SELECT ID “`USER_ID`", NAME, PWD `AS PASSWORD` FROM MEMBER;
    

## UPDATE

- 테이블의 내용을 `수정`
- UPDATE는 절대로 함부로 하지마라.
    - WHERE을 안쓰면 싹다 바뀔 수가 있다.

```sql
UPDATE MEMBER SET PWD=’111’; 
UPDATE MEMBER SET PWD=’111’, NAME='홍길동'; // 멤버 전체의 이름이 홍길동으로 바뀜
UPDATE MEMBER SET PWD=’111’, NAME='홍길동' WHERE ID='NEWLEC'; // WHERE를 이용해서 특정 행 지정
```

## DELETE

- 테이블의 행 삭제

```sql
DELETE MEMBER;
DELETE MEMBER WHERE ID=33;
```
