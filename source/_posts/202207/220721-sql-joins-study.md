---
title: 220721 SQL JOINS
date: 2022-07-21 13:48:00
tags:
  - SQL
  - TIL
categories:
  - SQL
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220721_sql_joins.png" alt="SQL JOINS">
</div>

<br/>
<br/>

이번 포스팅에서는 SQL의 JOIN에 대해서 종합적으로 정리를 해보려고 한다. 

## <ins><b>INNER JOIN</b></ins>
 
department에 속하는 employee name을 출력

```sql
-- INNER JOIN에서 INNER 생략 가능 
-- JOIN을 할때에는 ON 절에 작성하는 JOIN 조건의 column이름은 달라도 관계없다. Column의 값이 중요하다.
 SELECT e.emp_name, d.dept_name
 FROM employee e 
 JOIN department d ON e.dept_id = d.dept_id
```

## <ins><b>OUTER JOIN</b></ins>

### <ins><b>LEFT JOIN</b></ins>

모든 employee 이름과 department 이름 출력

```sql
-- LEFT JOIN = INNER JOIN + ANY additional records from the LEFT TABLE.
SELECT e.emp_name, d.dept_name
FROM employee e
LEFT JOIN department d ON e.dept_id = d.dept_id;
```

### <ins><b>RIGHT JOIN</b></ins>

```sql
-- RIGHT JOIN = INNER JOIN + ANY additional records from the RIGHT TABLE.
SELECT e.emp_name, d.dept_name
FROM employee e
RIGHT JOIN department d ON e.dept_id = d.dept_id;
```
<!-- more -->

모든 employee의 manager, department, project 정보 출력

```sql
-- PROJECT에 두 개 이상 참여하고 있는 employee의 경우에는 복수 emp_name으로 출력
SELECT e.emp_name, d.dept_name, m.manager_name, p.project_name
FROM employee e
LEFT JOIN department d ON e.dept_id = d.dept_id
INNER JOIN manager m ON m.manager_id = e.manager_id 
LEFT JOIN project p ON p.team_member_id = e.emp_id;
```

### <ins><b>FULL (OUTER) JOIN</b></ins>

```sql
-- FULL JOIN = INNER JOIN + All remaining records from LEFT TABLE (returns null value for any columns fetch)
--                        + ALL remaining records from RIGHT TABLE (returns null value for any columns fetch)

SELECT e.emp_name, d.dept_name
FROM employee e
FULL OUTER JOIN department d ON d.dept_id = e.dept_id;

-- INNER JOIN 결과가 출력되고 뒤이어 (department table을 기준으로 RIGHT OUTER JOIN한 결과 - INNER JOIN 결과) + (employee table을 기준으로 LEFT OUTER JOIN한 결과 - INNER JOIN 결과)가 붙어서 출력이 된다.
```

## <ins><b>CROSS JOIN</b></ins>

특정 기업 정보(기업명, 위치 등)만 가지고 있는 테이블이 있다고 가정하고, 해당 정보를 모든 employee에 출력해야한다면 어떻게 해야될까?
바로 CROSS JOIN을 사용하면 모든 employee 정보에 기업 정보들을 붙여서 출력할 수 있다.

```sql
--CROSS JOIN은 cartesian product를 반환한다.
SELECT e.emp_name, d.dept_name
FROM employee e -- 6 records
CROSS JOIN department d; -- 4 records
-- 6 records * 4records = 24 records
```

```sql
SELECT e.emp_name, d.dept_name, c.company_name, c.location
FROM employee e
INNER JOIN department d ON e.dept_id = d.dept_id
CROSS JOIN company c;
```

## <ins><b>NATURAL JOIN</b></ins>

SELF JOIN은 INNER JOIN과 같다고 착각할 수 있지만, 그렇지 않다.
NATURAL JOIN은 INNER JOIN과 같이 특정 조인 조건을 주지 않아도 같은 COLUMN명을 가진 COLUMN을 서로 JOIN함으로써 INNER JOIN과 같은 결과를 낼 수 있다.

```sql
SELECT e.emp_name, d.dept_name
FROM employee e
NATURAL JOIN department d;

ALTER TABLE department RENAME COLUMN dept_id TO id;

-- COLUMN이 수정하면, 서로 일치하는 COLUMN이 없기 때문에 NATURAL JOIN이 아닌 CROSS JOIN과 같은 효과를 낸다. 
-- NATURAL JOIN은 column name만을 비교해서 JOIN을 해주기 때문에 COLUMN명만 같고 값이 다른 경우에는 문제가 될 수 있기 때문에 권장되지 않는다.
```

## <ins><b>SELF JOIN</b></ins>

부모 이름과 나이 정보를 그 자식의 이름과 나이 정보와 매칭해서 출력되도록 sql 쿼리를 작성하시오.

```sql
SELECT * FROM family;

SELECT 
child.name AS child_name, 
child.age AS child_age, 
parent.name AS parent_name, 
parent.age AS parent_age
FROM family AS child
JOIN family AS parent ON child.parent_id = parent.member_id

-- LEFT JOIN으로 수정하면, 부모 정보가 없는 child의 정보도 포함된 테이블 정보가 출력된다.
```