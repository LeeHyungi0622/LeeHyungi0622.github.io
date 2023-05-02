---
title: 220403 SQL Basic-4
date: 2022-04-03 19:52:00
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

## <ins><b>전체 상품의 월별 매출 추이와 스테이크의 월 별 매출 추이 확인(동등 조인, SUBSTR, DECODE 응용)</b></ins>

문제를 해결하기 위해서는 `매 달 매출정보`와 `전체 매출과 스테이크 매출을 구분할 수 있는 별도의 조건이 필요`하다.

데이터 분석을 할때에는 핵심이 되는 부분을 선정해서 그 부분부터 점점 펼쳐나가는 분석기법이 필요하다.

## <ins><b>시계열 (Time Series)?</b></ins>

시간의 흐름에 따라 변화를 나타낸 그래프를 시계열 그래프라고 하는데, 시계열 그래프는 시간의 흐름을 보여주기 때문에 시계열이라고 한다.

## <ins><b>선 그래프의 사용</b></ins>

선 그래프는 `흐름/추이를 확인`할 때 매우 유용하게 사용되는 그래프이다.
선 그래프를 그리기 위해서는 JOIN이 필요하다. 가장 중요한 개념이기 때문에 잘 알아둬야 한다.

## <ins><b>ERD(Entity Relationship Diagram)?</b></ins>

ERD란 개체(테이블) 간의 관계를 이해하기 쉽게 그림으로 표현 한 것으로 데이터를 조작하고 분석하는 기초자료로 활용한다. 개체 간에는 관계(relation)이 있는데, 이러한 관계는 점선이나 실선으로 표현된 화살표 모양의 선으로 나타낸다.

`(1) P (Primary Key)` : 기본 키 (=주 키)라고 하며, 기본 키는 데이터를 식별하는 '식별자' 역할을 한다.
`(2) U (Unique Key)` : 고유 키라고 하며, 행에서 유일한 값을 갖는 데이터 값으로 구성된 열 (유일하게 식별할 수 있는 열이지만, 기본 키가 아닌 값)
`(3) F (Foreign Key)` : 외래 키(=보조 키)라고 하며, 참조 테이블의 기본 키 또는 고유 키를 참조한다.(다른 테이블과 연결을 위한 열로, 주로 다른 테이블 간의 연결을 위해 주로 다른 테이블의 기본 키를 참조한다.)
`(4) 키값이 아닌 열` : P, U, F를 제외한 나머지 열을 말한다.

ERD는 설계도로, ERD를 기반으로 SQL의 논리로직을 작성하게 된다.

<!-- more -->

### **관계 차수와 관계 선택 사양**

- ERD를 통해 관계 차수(cardinality)와 관계 선택 사양(optionality)을 표현한다.
- 관계 차수는 1:1, 1:N, M:N 등 하나의 개체(Primary key)에 몇 개의 개체가 대응되는지를 표현한다.
- 관계 선택 사양은 관계가 필수인지 아닌지(없을 수도 있는지)를 표현한다.

### **관계 차수(끝 모양)**

<div align="center">
  <img src="/images/post_images/.png" alt=""/>
</div>

### **관계 선택 사양(선 종류)**

<div align="center">
  <img src="/images/post_images/.png" alt=""/>
</div>

`실선`은 필수 관계를 나타내며, A와 B가 필수 관계임을 나타낸다. (B가 존재하려면 A가 반드시 존재)
`점선`은 선택적인 관계로, A와 B가 선택적 관계 (B는 A가 없어도 존재할 수 있음)

### **ERD 읽기 연습**

<div align="center">
  <img src="/images/post_images/.png" alt=""/>
</div>

## <ins><b>JOIN</b></ins>

한 개 이상의 테이블과 테이블을 서로 연결하여 사용하는 기법

## **조인(JOIN)의 종류**

### **이너 조인(INNER JOIN)(=동등 조인(equi)))**

조인 조건이 정확히 일치하는 경우에 결과를 출력한다.
A와 B테이블이 있다고 가정했을 때, A열에 있는 특정 열과 B열에 A의 특정 열과 동일한 값이 있는 열을 서로 연결 시키는 것을 말한다.

### **아우터 조인(OUTER JOIN)**

조인 조건이 정확히 일치하지 않아도 모든 결과를 출력한다. 이너 조인보다 좀 더 복잡하다.

`이너조인과 아우터 조인만 알아도 80-90% 테이블 간의 연결에 대해 80-90% 커버 가능`

### **곱집합(CARTESIAN PRODUCT)**

가능한 모든 행을 조인한다.

### **비동등 조인(non equi join)**

`조인 조건이 정확히 일치하지 않는 경우에 결과`를 출력한다.

### **자체 조인(self join)**

자체 테이블에서 조인하고자 할 때 사용된다.

## <ins><b>이너 조인(INNER JOIN)(=동등 조인(equi))) 자세히 알아보기</b></ins>

이너 조인을 할 때에는 WHERE절에서 두 테이블의 열이 갖고 있는 데이터 값을 논리적으로 연결하며, 연결 기호로는 등호(=)를 사용한다.
.으로 지정한 테이블 이름으로부터 열의 이름을 참조한다.

양쪽 테이블에서 조인 조건이 일치하는 행만 가져오는 가장 일반적이고 자주 쓰이는 조인이다.

```sql
SELECT *
FROM employees A, departments B -- 각 테이블을 별칭으로 지정
WHERE A.department_id = B.department_id;
```

### **이너 조인(inner join)의 조인 규칙**

- SELECT 절에는 출력할 열 이름을 기술하며, FROM 절에는 접근하려는 테이블 이름을 기술한다.
- WHERE 절에는 조인 조건을 기술한다.
- FROM 절 외의 절에는 조회의 명확성을위해 열 이름 앞에 테이블 이름을 붙인다.

```sql
SELECT A.employee_id, A.first_name, A.last_name, B.department_id, B.department_name
FROM hr.employees A,hr.departments B
WHERE A.department_id = B.department_id
ORDER BY A.employee_id;
```

## <ins><b>아웃터 조인(OUTER JOIN) 자세히 알아보기</b></ins>

이너 조인의 경우에는 조인하는 기준 열의 데이터가 일치해야 조인이 가능하다.
하지만, 아웃터 조인의 경우에는 조인하는 기준 열의 데이터가 일치하지 않아도 조인을 할 수 있다.
리포트나 데이터 분석을 할 때 항상 같은 값만으로 구성되지 않기 때문에 이런 경우에는 아웃터 조인을 사용한다.

JOIN은 WHERE절이 매우 중요하다.

```sql
-- 예시
SELECT 테이블 이름 1.열 이름 1, 테이블 이름 2.열 이름 2, ...
FROM   테이블 이름 1, 테이블 이름 2
WHERE  테이블 이름 1.열 이름 1 = 테이블 이름 2.열 이름 2(+);
```

- 아웃터 조인은 이너 조인 조건을 만족하지 못해 누락되는 행을 출력하기 위해 사용된다.
- 아웃터 조인은 이너 조인과 다르게 WHERE절의 끝에 (+) 기호를 붙여서 사용한다.
- (+) 기호는 조인할 행이 없는, 즉 데이터 값이 부족한 테이블의 열 이름 뒤에 기술해준다.
- (+) 기호는 아웃터 조인하려는 한쪽에만 기술할 수도 있다. 테이블 양쪽에는 기술할 수 없다.
  A.department_id(+) = B.department_id(+) `(X)`
- (+) 기호를 붙이면 데이터 값이 부족한 테이블에 null 값을 갖는 행이 생성되어 데이터 값이 충분한 테이블의 행들이 null 행에 조인하게 된다.

`(이너 조인)`
A 테이블 (+누락된 항목) + B 항목 (`matching되는 데이터가 부족한 쪽(조인을 하고자 하는 쪽)`)

`(아웃터 조인)`
위의 이너 조인에서는 null값과 matching되는 B의 열 항목이 없기 때문에 해당 항목이 누락이 되지만, 아웃터 조인에서는 null값인 경우에도 null값을 그대로 유지하면서 나머지 열 데이터를 null로 초기화시켜서 출력한다.

```sql
SELECT A.employee_id,A.first_name,A.last_name,A.department_id,B.department_id,B.department_name
FROM hr.employees A,hr.departments B
WHERE A.department_id = B.department_id(+)
ORDER BY A.employee_id;

-- hr.employees 테이블과 hr.departments 테이블을 각 각 A, B로 Alias해주고,
-- department_id를 기준으로 테이블 A와 B를 JOIN해준다. 조인해주려는 쪽이 B 테이블이기 때문에
-- WHERE 절에서 B.department_id(+)과 같이 B테이블의 칼럼명 뒤에 (+)를 붙여준다.
-- 출력순서는 A테이블의 employee_id를 기준으로 오름차순으로 한다.
-- 출력되는 칼럼은 A.employee_id, A.first_name, A.last_name, A.department_id, B.department_id, B.department_name으로 한다.
-- 만약에, 반대로 (+)기호를 주게되면, INNER JOIN과 같은 형태로 테이블이 출력되게 된다.
```

내가 출력하고자하는 기준열이 오른쪽에 있으면, `RIGHT OUTER JOIN`[(+)가 왼쪽],
내가 출력하고자하는 기준열이 왼쪽에 있으면, `LEFT OUTER JOIN`[(+)가 오른쪽] 이라고 말한다.

그 외의 다른 SQL에서는 `표준 SQL문법`인 아래의 형태로 OUTER JOIN을 한다.

```sql
SELECT A.employee_id,A.first_name,A.last_name,A.department_id,B.department_id,B.department_name
FROM hr.employees A LEFT OUTER JOIN hr.departments B
ON A.department_id = B.department_id
ORDER BY A.employee_id;
```

## <ins><b>집합 연산자 (SET OPERATOR)</b></ins>

- SELECT 문을 여러 개 연결하여 작성하며, 각 SELECT 문의 조회 결과를 하나로 합치거나 분리한다.
- 집합 연산자는 합집합, 교집합, 차집합의 논리와 같다.

### **집합 연산자의 종류**

- UNION : SELECT 문의 조회 결과의 합집합. `중복되는 행은 한 번만 출력`한다. (합집합)
- UNION ALL : SELECT 문의 조회결과의 합집합. `중복되는 행도 그대로 출력`한다. (합집합)
- INTERSET : SELECT 문의 조회 결과의 교집합. `중복되는 행만 출력`한다. (교집합)
- MINUS : 첫 번째 SELECT 문의 조회결과에서 두 번째 조회 결과를 뺀다. (차집합)

```sql
SELECT 열 이름1, 열 이름2, 열 이름3, ...
FROM 테이블 이름
집합 연산자 -- UNION, UNION ALL, INTERSET
SELECT 열 이름1, 열 이름2, 열 이름3, ...
FROM 테이블 이름
[ORDER BY 열 이름 [ASC or DESC]]
```
