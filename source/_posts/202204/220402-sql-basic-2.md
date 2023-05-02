---
title: 220402 SQL Basic-2
date: 2022-04-04 22:30:00
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

## <ins><b>SELECT 문의 주요 기능</b></ins>

- Selection : 질의에 대해 반환하고자하는 테이블의 행을 선택하기 위한 기능
- projection : 질의에 대해 반환하고자 하는 테이블의 열을 선택하기 위한 기능
- Join : 공유 테이블 양쪽의 열에 대해 링크를 생성하여 다른 테이블에 저장되어있는 데이터를 결합하여 가져오기 위해 SQL의 조인 기능을 사용

### **SELECT 절로 할 수 있는 것들**

- 특정 열과 모든 열을 선택
- 연산자를 사용 +, -, \*, /
- 그룹화
- Null 값의 처리
- 별칭 (Alias)
- 중복 행의 제거 (Distinct) 등

### **SELECT 문의 기본 문법**

`SELECT` [DISTINCT] `열 이름`[as Alias]
`FROM` `테이블 이름`
[WHERE 조건식]
[ORDER BY 열 이름 [ASC OR DESC]];

[] : 선택사항

### **SELECT 문장 작성 규칙**

명령어는 대문자로 작성하고, 나머지는 소문자로 작성하면 가독성이 좋아진다.

<!-- more -->

### **SELECT 문장 작성 규칙**

```sql
SELECT *
FROM hr.employees;
```

### **--는 주석처리할 때 사용**

### **출력 결과를 파일로 내보내기**

Oracle SQL Developer에서 테이블의 데이터를 선택해서 마우스 우측 클릭하고 `"export"`를 선택하면 파일을 추출할 수 있는 창이 팝업되고, 원하는 확장자를 선택해서 추출하면 된다.

### **특정 열 선택하기**

`SELECT` `열 이름`[as Alias]
`FROM` `테이블 이름`;

```sql
-- 예시

SELECT employee_id, first_name, last_name
FROM hr.employees;
```

### **조건식과 연산자 적용하기**

`SELECT` \*
`FROM` `테이블 이름`
`WHERE` `조건식`;

```sql
SELECT *
FROM hr.employees
WHERE email LIKE 'V'||'%';
-- ||는 concat 명령어이다.

-- 위의 WHERE 절과 같은 의미이다.
WHERE email LIKE 'V%'
-- 이메일이 대문자 V로 시작되는 모든 값(조건에 만족하는 모든 열의 값)을 출력하라는 의미
```

연산자의 종류에는 `비교연산, SQL연산, 논리연산`, 부정비교, 부정SQL이 있고, 마지막 두 개를 제외하고 나머지가 가장 많이 쓰인다.

### **비교연산 실습**

```sql
-- 비교연산 실습
SELECT *
FROM employees
WHERE salary >= 6000;

-- ORDER BY 절 (default: ASC)
SELECT *
FROM hr.employees
WHERE salary >= 6000
ORDER BY salary;
```

### **SQL연산 실습**

```sql
-- SQL연산
-- BETWEEN A AND B
SELECT *
FROM hr.employees
WHERE salary BETWEEN 6000 AND 8000
ORDER BY salary;

-- IN (LIST)
SELECT *
FROM hr.employees
WHERE salary IN (6000, 8000)
ORDER BY salary;

-- LIKE '비교문자열' : 비교문자열과 형태가 일치한다.(%(모든 문자열), _ (자릿수) 사용)

SELECT *
FROM hr.employees
WHERE email LIKE 'B'||'%'
ORDER BY salary;

-- 총 대문자 B를 포함한 6자 이메일 값만 출력
SELECT *
FROM hr.employees
WHERE email LIKE 'B'||'_____'
ORDER BY salary;


-- IS NULL : NULL값만 출력

SELECT *
FROM hr.employees
WHERE manager_id IS NULL
ORDER BY salary;
```

### **논리연산 실습**

```sql
-- AND : 앞 뒤 조건의 동시만족

SELECT *
FROM hr.employees
WHERE salary = 8000 AND JOB_ID = 'SA_REP'
ORDER BY salary;

-- OR : 한쪽만 TRUE라도

SELECT *
FROM hr.employees
WHERE salary = 8000 OR salary = 6000
ORDER BY salary;

-- NOT : 뒤의 조건과 같지 않으면

-- 이후의 연산자는 직접 실습해보기
```

## <ins><b>주어진 데이터로 boxplot 그래프 그리기</b></ins>

전체 매출과 스테이크의 매출을 비교하기 위해서 전처리된 dataset을 만들어주고, 해당 데이터를 boxplot 그래프로 그려서 실제 판매량이 어떻게 되는지 비교를 한다.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

table_df = pd.read_csv('table.csv')

table_df
sns.boxplot(x='TYPE', y='SALES_', data=table_df)
```

<div align="center">
  <img src="/images/post_images/220403_steak_overall_boxplot.png" alt="steak sale vs overall sales boxplot"/>
</div>

boxplot을 x=TYPE, y=SALES\_를 기준으로 그려보았다.
결과 그래프를 분석해보면, `boxplot은 최대값, 최소값, 중앙값, 사분위수 등 이상치를 표시`한다.

`박스 아랫면에 있는 면을 일사분이라고 하며, 전체 데이터값의 25%값`을 알려주며, `박스의 중앙라인은 중앙값(전체 데이터의 50%), 박스 윗면은 삼분위수(전체 데이터의 75%)`전체 데이터가 어떻게 되는지 나타낸다. `위에 점으로 표시된 것은 이상치`를 표시한다.

박스의 크기가 각 각의 전체 판매(overallsales)로 왼쪽의 박스는 전체 매출, 두번째 박스는 스테이크의 판매 매출(steak_sales)을 나타낸다.

`세일즈 값이 박스 크기이기 때문에, 스테이크 값이 전체 매출값에 비해서 높다는 판단`을 내릴 수 있다.

### **엑셀에서 했던 데이터 전처리 작업을 SQL로 하기**

```sql
SELECT order_no, item_id, reserv_no, quantity, ROUND(sales/1000, 0) AS SALES_, 'overall_sales' AS TYPE
FROM order_info
UNION ALL
SELECT order_no, item_id, reserv_no, quantity, ROUND(sales/1000, 0) AS SALES_, 'Stake' AS TYPE
FROM order_info
WHERE item_id = 'M0005'
```

위 아래 있는 SELECT문의 사이에 UNION ALL이 있기 때문에 두 테이블을 붙여주게 된다.
