---
title: 220403 SQL Basic
date: 2022-04-03 02:11:00
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

이번 포스팅에서는 SQL의 기초에 대한 내용을 정리하겠다.

## <ins><b>비교 분석하기</b></ins>

SUM, COUNT, GROUP_BY를 사용하여 총 판매 금액과 총 판매 수량을 비교한다.

판매금액을 구하는 방법,
판매 수량을 구하는 방법,
메뉴 아이템 별로 나누는 방법 (그룹화)

## <ins><b>단일행 함수와 그룹 함수</b></ins>

### **함수?**

- 함수란 어떤 결과를 위해 미리 만들어 놓은 명령어(도구)이다.
- 함수들은 기본적인 SQL문을 더욱 강력하게 사용할 수 있게 해주며, 데이터 값을 조작하는데 도움을 준다.

### **SQL함수의 특징**

- 데이터 값을 계산하거나 조작한다.
- 데이터 값을 조작, 날짜와 숫자 등 데이터 타입을 상호 변환, 행에 대해 조작한다.`(단일 행 함수)`
- 행의 그룹에 대해 계산하거나 요약 `(그룹함수)`

(1) `단일 행 함수` : 문자 함수, 숫자 함수, 날짜 함수, 변환 함수, 일반 함수
(`1:1로 값이 mapping되어 수정`된다)

#### **데이터 타입의 종류**

- `문자` : CHAR(n) : n크기만큼 고정길이의 문자 타입을 저장 (최대 2000바이트)
- `문자` : VARCHAR2(n) : n크기만큼 가변 길이의 문자 타입을 저장한다.
  -> 데이터를 관리하고 저장하는데 효율성을 위해서 가변/불가변 데이터 타입을 구분해서 사용한다. 고정 데이터 분석(CHAR)/유연한 데이터 분석(VARCHAR2)
- `숫자` : NUMBER(p, s) : 수자타입을 저장 (p: 정수, s: 소수 자릿수)
- `날짜` : DATE : 날짜 타입 : 날짜 타입을 저장 9999년 12월 31일까지 저장

<!-- more -->

#### **단일 행 함수의 종류**

- `문자타입 함수` : 문자를 입력받아 문자와 숫자를 반환
- `숫자 타입 함수` : 숫자를 입력받아 숫자를 반환
- `날짜 타입 함수` : 날짜에 대한 연산을 한다. 숫자를 반환하는 MONTHS_BETWEEN 함수를 제외하고 모든 날짜 타입 함수는 날짜 값을 반환
- `변환 타입 함수` : 임의의 데이터 타입의 값을 다른 데이터 타입으로 변환
- `일반 함수` : 그 외 NVL, DECODE, CASE, WHEN, 순위 함수 등

#### **단일 행 함수의 특징**

- 행별로 하나의 결과를 반환
- SELECT, WHERE, ORDER BY 절 등에서 사용
- 중첩 사용가능 `(안쪽(하위) -> 바깥쪽(상위) 단계순으로 진행)`

#### **주요 단일 행 문자타입 함수 요약**

- LOWER : 문자열을 소문자로
- UPPER : 문자열을 대문자로
- INITCAP : 문자열의 첫 번째 문자를 대문자로
- SUBSTR : 문자열 중 일부분을 선택 / 인덱스는 1부터 시작, 마지막 인덱스 번호는 끝지점 가리킴
- REPLACE : REPLACE('[TARGETR 문자열]', '[TARGET 문자]', '[수정 문자]')
- CONCAT : 두 문자열을 연결 (|| 연산자와 같다)
- LENGTH : 문자열의 길이
- INSTR : 명명된 문자의 위치를 구한다. INSTR('ABCD', 'D') -> 4
- LPAD : 왼쪽부터 특정 문자로 자리를 채운다. LPAD('ABCD', 6, \*) -> \*\*ABCD
- RPAD : 오른쪽부터 특정 문자로 자리를 채운다.
- LTRIM : 주어진 문자열의 왼쪽 문자를 지운다. LTRIM('ABCD', 'AB')
- RTRIM : 주어진 문자열의 오른쪽 문자를 지운다. RTRIM('ABCD', 'CD')

#### **주요 단일 행 숫자타입 함수 요약**

- \*ROUND : 숫자를 반올림한다.(0이 소숫점 첫째자리)
- \*TRUNC : 숫자를 절삭한다. TRUNC(15.451, 1) -> 15.4 (소수점 첫째 자리에서 절삭)
- MOD : 나누기 후 나머지를 구한다.
- CEIL : 숫자를 정수로 올림한다. (무조건 반올림 정수+1)
- FLOOR : 숫자를 정수로 내림한다. (무조건 소수점 이하 버리기)
- SIGN : 양수(1), 음수(-1), 0인지 구분하여 출력
- POWER : 거듭제곱 출력
- SQRT : 제곱근 출력

#### **날짜 타입 함수**

- MONTHS_BETWEEN : 두 날짜 사이의 개월수를 계산
- ADD_MONTHS : 월을 날짜에 더한다. ADD_MONTHS(HIRE_DATE, 5)
- NEXT_DAY : 명시된 날짜부터 돌아오는 요일에 대한 날짜를 출력(SUNDAY:1, MONDAY:2,)
- LAST_DAY : 월의 마지막 날을 계산
- ROUND : 날짜를 가장 가까운 연도 또는 월로 반올림 (YEAR or MONTH) ROUND(HIRE_DATE, 'MONTH')
- TRUNC : 날짜를 가장 가까운 연도 또는 월로 절삭 (YEAR or MONTH) TRUNC(HIRE_DATE, 'YEAR')

```sql
- DUAL : Oracle에서 제공하는 더미 테이블
- SYSDATE : 시스템 날짜(오늘)
SELECT SYSDATE
FROM DUAL;
```

#### **연산 결과에 따른 타입 변환**

```sql
SELECT 1,2,3
FROM DUAL;

- 암묵적으로 문자타입의 숫자와 숫자와의 연산을 숫자로 변환해서 연산해준다.
SELECT 1 + '2'
FROM DUAL;
```

#### **타입 변환함수**

```python
SELECT TO_CHAR(SYSDATE, 'YY'),
SELECT TO_CHAR(SYSDATE, 'YYYY'),
       TO_CHAR(SYSDATE, 'MM'),
       TO_CHAR(SYSDATE, 'MON'),
       TO_CHAR(SYSDATE, 'YYYYMMDD'),
       TO_CHAR(TO_DATE('20171008'), 'YYYYMMDD'),
FROM dual;

SELECT TO_NUMBER('123')
FROM dual;

SELECT TO_DATE(20171007, 'YYMMDD')
FROM dual;
SELECT TO_DATE('20171007', 'YYMMDD')
FROM dual;
```

## <ins><b>일반 함수</b></ins>

일반 함수 중에서 가장 많이 사용되는 함수들을 정리

### **순위함수(RANK)**

```sql
SELECT employee_id,
       salary,
       RANK()   OVER(ORDER BY salary DESC)RANK_급여,
       DENSE_RANK() OVER(ORDER BY salary DESC)DENSE_RANK_급여,
       ROW_NUMBER() OVER(ORDER BY salary DESC)ROW_NUMBER_급여
FROM hr.employees
WHERE employee_id BETWEEN 100 AND 106;

- RANK() : salary를 기준으로 내림차순 정렬을 하고, 순위를 매긴다. (중복 순위가 있는 경우, 그 다음 순위는 한 단계 건너뛴다. 공동순위 있음)
- DENSE_RANK() : salary를 기준으로 내림차순 정렬을 하고, 순위를 매긴다. (중복 순위와 상관없이 순위를 순차적으로 출력한다. 공동순위 있음)
- ROW_NUMBER() : salary를 기준으로 내림차순 정렬을 하고, 순위를 매긴다.(중복 순위가 있는 경우, 순차적으로 순위를 할당한다. 공동순위 없음)
```

### **NULL 값을 특정 값으로 치환하는 NVL함수**

```sql
-- commission_pct의 값이 NULL인 경우, 1로 치환해서 수식을 계산한다.

SELECT salary * NVL(commission_pct, 1)
FROM hr.employees
WHERE employee_id BETWEEN 100 AND 106
ORDER BY commission_pct;
```

### **DECODE 문**

열을 선택하고, 특정 조건값이면 치환값으로 업데이트하고, 특정 조건값이면 인상여부에 대한 칼럼에 '10%인상', '미인상' 값을 넣어 출력한다.

```sql
DECODE(열 이름, 조건값, 치환값(조건 값에 해당할 경우, 출력값), 기본값(조건 값에 해당하지 않을 경우 출력값))

SELECT first_name,
       last_name,
       department_id,
       salary,
       DECODE(department_id, 60, salary*1.1, salary) 조정된급여,
       DECODE(department_id, 60, '10%인상', '미인상') 인상여부
FROM hr.employees
WHERE employee_id BETWEEN 100 AND 106;
```

### **CASE 문**

CASE문은 DECODE와 달리 복수의 조건을 줄 수 있다.

```sql
CASE
    WHEN 조건1 THEN 출력값1
    WHEN 조건2 THEN 출력값2
END AS [칼럼이름]

-- job_id가 'IT_PROG'인 직원중에 급여가 9000이상이면 '상위급여', 6000과 8999 사이이면 '중위급여', 그 외에는 '하위급여'로 출력한다.

SELECT employee_id, first_name, last_name, salary,
    CASE
        WHEN salary >= 9000 THEN '상위급여'
        WHEN salary BETWEEN 6000 AND 8999 THEN '중위급여'
        ELSE '하위급여'
    END AS 급여등급
FROM hr.employees
WHERE job_id = 'IT_PROG';
```

(2) `그룹함수` : 함수, GROUP BY, HAVING
(`기준 열의 행에 대해 그룹으로 묶어서 함수를 적용`한다)

여러 행 또는 테이블 전체에 대해 함수가 적용되어 하나의 결과를 가져오는 함수를 말한다.
`그룹 당 하나의 결과가 주어지도록 하기 위해 '행의 집합'에 대해 연산할 경우 GROUP BY 절을 이용`하고, `HAVING절을 이용하여 그룹에 대한 조건을 제한` 할 수 있다.

## <ins><b>그룹함수의 종류</b></ins>

- COUNT (행의 갯수를 센다) : \*(whildcard)의 경우에는 null도 개수에 포함해서 센다.
  `(그 외 나머지는 null 값을 제외하고 연산을 한다)`
- SUM (합계)
- AVG (평균)
- MAX (최댓값)
- MIN (최솟값)
- STDDEV (표준편차)
- VARIANCE (분산)

```sql
SELECT COUNT(*)행갯수, AVG(salary)급여
FROM hr.employees;

SELECT SUM(salary) AS 총월급여, SUM(salary)*0.1 AS 인상분, SUM(salary)*1.1 AS 변경후_총월급여
FROM hr.employees;
```

## <ins><b>GROUP BY 절</b></ins>

그룹 함수는 여러 행 또는 테이블 전체에 대해 함수가 적용되어 하나의 결과를 가져온다.
그룹 당 하나의 결과가 주어지도록 행의 집합에 대해 그룹화 연산할 경우, GROUP BY절을 사용하도록 한다.
그리고 GROUP BY절은 그룹함수와 같이 자주 사용된다.

```sql
SELECT A, SUM(B)
FROM table_name
GROUP BY A

--A열의 값을 기준으로 그룹화를 하고, B열은 그룹화된 그룹값들의 합을 구한다.

SELECT job_id AS 직무, SUM(salary) AS 직무별_총급여, AVG(salary) AS 직무별_평균급여
FROM hr.employees
WHERE employee_id >= 10
GROUP BY job_id
ORDER BY 직무별_총급여 DESC, 직무별_평균급여;

-- hr.employees 테이블에서 job_id, salary의 합계, salary의 평균급여 정보를 출력한다. 조건은 employee_id가 10이상인 직원들을 job_id로 그룹화하고, 직무별 총급여를 내림차순으로, 직무별 평균급여를 오름차순으로 정렬을 해서 출력한다.
```

## <ins><b>HAVING 절</b></ins>

HAVING절은 그룹으로 묶여진 결과값에 대해서 다시 한 번 조건문을 주는 것이다.

HAVING절의 위치는 GROUP BY 절 다음에 위치를 하며, 그 다음으로 ORDER BY 절이 위치를 한다.
`GROUP BY [열이름] -> HAVING [조건식] -> ORDER BY [열 이름]`

```sql
SELECT job_id AS 직무, SUM(salary) AS 직무별_총급여, AVG(salary) AS 직무별_평균급여
FROM hr.employees
WHERE employee_id >= 10
GROUP BY job_id
HAVING SUM(salary) > 30000
ORDER BY 직무별_총급여 DESC, 직무별_평균급여;

-- hr.employees 테이블에서 직무, 직무별 총급여, 직무별 평균급여 칼럼을 출력하는데, employee_id가 10이상이고 job_id를 기준으로 그룹화하며, salary가 30000초과하는 데이터를 직무별 총급여를 기준으로 내림차순, 직무별 평균급여를기준으로 오름차순하여 출력한다.
```

## <ins><b>총 판매 금액과 총 판매 수량을 조회하여 비교</b></ins>

```sql
SELECT item_id,
       SUM(sales) AS SALE,
       SUM(QUANTITY) AS QTY,
       COUNT(order_no) AS ORDER_CNT
FROM order_info
GROUP BY item_id
ORDER BY SUM(sales) DESC

-- order_info 테이블에서 item_id, 판매수익의 합계, 양의 합계, 주문번호 칼럼을 출력을 하는데, item_id를 기준으로 그룹화를 하고, 판매 총 수익을 기준으로 내림차순하여 출력한다.
```
