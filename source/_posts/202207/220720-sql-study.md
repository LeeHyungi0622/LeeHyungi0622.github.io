---
title: 220721 ANSI SQL & NON ANSI SQL
date: 2022-07-21 13:45:00
tags:
  - SQL
  - TIL
categories:
  - SQL
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220721_ansi_non_ansi_sql.png" alt="ANSI JOIN AND NON-ANSI JOIN">
</div>

<br/>
<br/>

이번 포스팅에서는 ANSI SQL과 NON ANSI SQL의 각기 다른 방식으로 JOIN 쿼리를 작성했을때의 차이점에 대해서 간단하게 포스팅하려고 한다.  

## <ins><b>ANSI & NON-ANSI SQL</b></ins>

표준 ANSI 방식의 JOIN 쿼리에서는 JOIN 키워드와 ON 절을 사용하여 두 테이블을 합치며, 필터 조건은 WHERE 절에 작성을 해준다.

아래의 쿼리는 department name이 HR 부서인 employee의 이름과 부서 정보를 출력해주는 쿼리이다.  
```sql
-- ANSI
-- JOIN 키워드를 사용해서 ON clause에서 조인되는 조건을 명시했다면 표준 ANSI 방식
SELECT e.emp_name, d.dept_name
FROM employee e
INNER JOIN department d on d.dept_id = e.dept_id
WHERE d.dept_name = 'HR';

-- INNER JOIN에서는 ON 절에 AND 필터 조건을 붙여서 작성해도 결과는 같다.
SELECT e.emp_name, d.dept_name
FROM employee e
INNER JOIN department d ON d.dept_id = e.dept_id AND d.dept_name = 'HR';

-- 하지만, OUTER JOIN에서는 위와같이 ON 절에 필터조건을 거는 경우 문제가 될 수 있다.
```

```sql
-- NON-ANSI
-- JOIN 키워드 대신 ,(comma)를 사용해서 조인 조건을 명시했다면 NON-ANSI 방식
SELECT e.emp_name, d.dept_name
FROM employee e
,department d WHERE d.dept_id = e.dept_id 
AND d.dept_name = 'HR';
```

아래의 쿼리는 모든 employee의 이름과 부서 정보를 출력해주는 쿼리이다.
모든 employee의 정보를 출력해주기 위해서는 department 테이블에 명기된 부서 정보와 matching되지 않은 employee의 정보도 출력해야 되기 때문에 OUTER JOIN을 해서 모든 employee의 정보를 출력해줘야 한다.

<!-- more -->

```sql
-- ANSI
-- JOIN 키워드를 사용해서 ON clause에서 조인되는 조건을 명시했다면 표준 ANSI 방식
SELECT e.emp_name, d.dept_name
FROM employee e
LEFT OUTER JOIN department d on d.dept_id = e.dept_id
WHERE d.dept_name = 'HR';
```

ANSI 방식의 SQL 쿼리에서 LEFT OUTER JOIN을 할때에는 OUTER를 생략하여 LEFT JOIN으로 작성할 수도 있다. 왼쪽 테이블을 기준으로 OUTER JOIN을 하기 때문에 오른쪽 부서 정보 테이블이 NULL인 경우도 모두 포함하여 모든 employee의 정보를 출력한다.

ANSI 방식의 JOIN에서는 간단하게 JOIN 키워드만 변경을 하면, FULL OUTER JOIN, LEFT OUTER JOIN, RIGHT OUTER JOIN 등을 손쉽게 할 수 있다.

하지만 NON-ANSI 방식의 쿼리문에서는 약간 까다로워지는 경우가 생긴다. NON-ANSI 방식으로 LEFT (OUTER) JOIN을 하기 위해서는 ORACLE DB에서 작성을 해야한다. ORACLE DB에서는 `(+)` symbol을 사용해서 OUTER JOIN 쿼리를 작성하게 되는데, (+) symbol은 조인하고자 하는 기준이 되는 열의 반대 열에 붙이도록 한다.
PostgreSQL에서는 NON-ANSI 방식을 사용해서 내부 조인은 가능하지만, 외부 조인은 할 수 없고, MS-SQL, MYSQL도 마찬가지다. (`단, ORACLE에서만 NON-ANSI 방식으로 OUTER JOIN이 가능하다`)  

```sql
-- NON-ANSI / LEFT OUTER JOIN
-- 모든 employee를 출력하기 위해서 employee를 기준으로 OUTER JOIN을 해야되기 때문에 department의 조인 조건 절에 (+) symbol을 붙인다.
SELECT e.emp_name, d.dept_name
FROM employee e
,    department d
WHERE d.dept_id(+) = e.dept_id

-- RIGHT OUTER JOIN
-- 모든 department를 출력(Employee 정보가 NULL인 경우도 포함)
SELECT e.emp_name, d.dept_name
FROM employee e
,    department d
WHERE d.dept_id = e.dept_id(+)
```

## <ins><b>ANSI SQL이 NON-ANSI SQL 보다 좋은 점</b></ins>

### **1. 쿼리문이 더 짧고 간결해지며, 가독성이 좋아지고 디버깅하기 쉬워진다.**

```sql
-- ANSI 
-- FROM 절의 테이블과 JOIN 키워드 우측의 테이블을 각 각 왼쪽 오른쪽 테이블로 정의한다.
SELECT e.emp_name, d.dept_name, m.manager_name, p.project_name
FROM employee e
LEFT JOIN department d on d.dept_id = e.dept_id
RIGHT JOIN manager m on m.manager_id = e.manager_id
LEFT JOIN projects p on p.team_member_id = e.emp_id;

-- NON-ANSI (ORACLE)
SELECT e.emp_name, d.dept_name, m.manager_name, p.project_name
FROM employee e
,    department d 
,    manager m
,    projects p
WHERE d.dept_id (+) = e.dept_id
AND m.manager_id = e.manager_id (+)
AND p.team_member_id (+) = e.emp_id;
```

### **2. JOIN 조건과 FILTER 조건을 서로 분리할 수 있다.(ON 절 = JOIN 조건 / FILTER 조건 = WHERE 조건)**

```sql
-- 부서명이 HR인 경우만 emp_name을 출력
SELECT e.emp_name, d.dept_name
FROM employee e
LEFT JOIN department d ON e.dept_id = d.dept_id
WHERE d.dept_name = 'HR';

-- 부서명이 HR인 경우만 dept_name을 표기하고, 나머지는 null로 채워서 출력
SELECT e.emp_name, d.dept_name
FROM employee e
LEFT JOIN department d ON e.dept_id = d.dept_id AND d.dept_name = 'HR';
```

### **3. 우연한 CROSS JOINS을 피할 수 있다.**
두 개의 테이블을 특정 조인 조건 없이 합치는 경우, CROSS JOIN이 발생할 수 있다.

```sql
-- NON-ANSI (ORACLE)
SELECT e.emp_name, d.dept_name, m.manager_name, p.project_name
FROM employee e
,    department d 
,    manager m
,    projects p
WHERE d.dept_id (+) = e.dept_id
-- AND m.manager_id = e.manager_id (+)
AND p.team_member_id (+) = e.emp_id;
```

ANSI SQL로 작성을 하는 경우에는 ON 절에서 조인 조건을 빼먹고 쓰지 않은 경우에 별도의 에러 메시지를 통해 문제를 해결 할 수 있다.

### **4. ANSI는 모든 RDBMS와 시스템에서 범용적으로 사용 가능한다.**

