---
title: 220404 SQL Basic-1
date: 2022-04-02 22:26:00
tags:
  - SQL
  - TIL
categories:
  - SQL
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220402_sql_img.png" alt="SQL"/>
</div>

이번 포스팅에서는 SQL의 기본적인 부분에 대해서 정리해보려고 한다.

## <ins><b>SQL?</b></ins>

SQL(Structured Query Language)의 약어로, RDBMS(관계형 데이터베이스 관리 시스템)을 접근하여 조작할 수 있으며, ANSI 표준을 따른다.

## <ins><b>SQL로 할 수 있는 일은?</b></ins>

- 데이터베이스를 질의(=조회 실행, Query)할 수 있다.
- 행을 삽입(Insert), 갱신(Update), 삭제(Delete) 할 수 있다.
- 데이터베이스, 테이블, 프로시져, 뷰 등을 생성(Create)할 수 있으며, 권한을 줄 수 있다.

## <ins><b>관계형 데이터 베이스 관리 시스템(RDBMS/Relational Database Management System)?</b></ins>

- RDBMS는 SQL의 기본이 되는 데이터베이스이다.
- MY-SQL, ORACLE, DB2, MySQL 등이 대표적인 RDBMS이다.
- RDBMS는 테이블(Table)이라고 불리는 객체를 갖는다.
- 테이블(Table)은 행과 열로 구성이 되어있다.

## <ins><b>관계형 데이터베이스의 구조</b></ins>

관계형 데이터베이스는 `2차원의 테이블`과 `관계`를 이용한다.

<!-- more -->

## <ins><b>테이블(Table)</b></ins>

테이블은 행과 열로 구성이 되어있으며, `행은 로우, 레코드, 튜플, Observation이라고 불린다.`
테이블에는 하나의 기본키(Primary Key)가 있으며, 속성(열, 컬럼, 필드, 도메인, variable)로 구성된다. 그리고 행과 열의 교차점에 있는 것이 `데이터 값(컬럼 값)`이라고 한다.
행을 유일하게 식별할 수 있는 데이터 값을 `기본 키(Primary Key)`라고 한다. 그리고 기본 키 값을 가지고 있는 열을 `기본키 열`이라고 한다.

## <ins><b>다른 프로그래밍 언어와의 비교</b></ins>

공통 요소에는 데이터 타입, 변수, 함수, 명령어, 연산자가 있는데, 다른 프로그래밍 언어와 공통적으로 있는 요소이다.

<!-- |제목|내용|설명|
|:---|---:|:---:|
|왼쪽정렬|오른쪽정렬|중앙정렬|
|왼쪽정렬|오른쪽정렬|중앙정렬|
|왼쪽정렬|오른쪽정렬|중앙정렬| -->

|           문자            |      숫자      |                 날짜                 |
| :-----------------------: | :------------: | :----------------------------------: |
|          CHAR(n)          |  NUMBER(p,s)   |                 DATE                 |
|        VARCHAR2(n)        |                |                                      |
| 'ABC','가나다','1','1234' | 1, 1200, 1,235 | 17/10/06/11:00, 2022-01-01, 20200101 |

`(1) 변수` : @v ex) v = 'Steve' or v = 100

`(2) 함수` : 함수는 SQL에서 빈번하게 사용된다. (`SUM(), AVG(), MAX()`)

`(3) 명령어` : 문법을 만들고 실행하기 위한 키워드, 예약어 (SQL을 만드는 근간)
예) SELECT, FROM, WHERE, GROUP BY, ORDER BY

`(4) 연산자` : 연산을 위해 정의된 기호 (), \* / + - = < >

### **차이나는 요소**

`(5) (중요) 테이블 / 관계` : 데이터를 담아 놓은 데이터 덩어리 / 데이터끼리 연결하는 개념

`(6) SQL, 데이터베이스 이론(프로그래밍 랭귀지 이론)` : 해당 랭귀지를 위한 문법과 이론들

### **실행과정**

USER -> SQL -> DBMS -> DB
USER <- <- <- DBMS <- DB

### **SQL 학습**

- Retrieve DML(데이터 조작어) : 주로 많이 사용
- DDL(데이터 정의어) : DML 다음으로 많이 사용
- DCL(데이터 제어어)
- TCL(트랜잭션 제어어)

|         구분 (중요도 순)          |                             문장                             |                                               설명                                                |
| :-------------------------------: | :----------------------------------------------------------: | :-----------------------------------------------------------------------------------------------: |
|           Retrieve(DML)           |                            SELECT                            |                                  데이터베이스로부터 데이터 검색                                   |
|  DML(Data Manipulation Language)  |                    INSERT, UPDATE, DELETE                    | 개별적으로 데이터베이스 테이블에서 새로운 행을 입력하고 기존의 행을 변경하고, 원치 않는 행을 제거 |
| TCL(Transaction Control Language) |                 COMMIT, ROLLBACK, SAVEPOINT                  |                                  DML명령문으로 만든 변경을 관리                                   |
|   DDL(Data Definition Language)   | CREATE, ALTER, DROP(구조삭제), REMANE, TRUNCATE(auto-commit) |                                  데이터 구조를 생성, 변경, 제거                                   |
|    DCL(Data Control Language)     |                        GRANT, REVOKE                         |                  ORACLE 데이터베이스와 그 구조에서 엑세스 권한을 제공하거나 제거                  |

### **SQL 도메인**

**(기본)**
`ERD` -> `Table, Key, Relation, Join` -> `DML, DDL, TCL` 순으로 확장하면서 학습

**(심화)**
`스키마, 관계대수, 개념/논리/물리/각종 이론` -> `정규화, 반정규화, 함수적 종속성, 연결함정, 이상현상` -> ... -> `Index, Optimizer, Tunning` -> `OLAP, MDM, DB보안`순으로 확장
