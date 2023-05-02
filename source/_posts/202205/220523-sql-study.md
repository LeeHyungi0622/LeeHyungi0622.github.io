---
title: 22040523 SQL NL Join / Sort Merge Join / Hash Join에 대한 이해
date: 2022-05-23 19:10:00
tags:
  - SQL
  - TIL
categories:
  - SQL
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220523_sql_img.jpeg" alt="SQL JOIN">
</div>

<br/>
<br/>

이번 포스팅에서는 이번에 SQLD 자격증 시험 준비를 하면서 새롭게 알게 된 `NL Join, Sort Merge Join, Hash Join`에 대한 내용을 간략하게 정리하려고 한다.
이번 SQLD 자격증 시험은 단순 자격증 취득 목적이 아닌, 내가 SQL에 대해 이론적으로나 실제 SQL 쿼리의 사용에 대한 이해가 어느정도 되는지 확인하고, 부족한 부분을 확인하고, 부족한 부분은 개인적인 공부를 통해 채워나가기 위해서 준비하게 되었다.

SQLD 시험 공부는 `2022/05/09에 시작`했으니, 오늘부로 14일(2주)째인데, 데이터 모델링의 이해부터 SQL 활용부분까지 전체적으로 이론공부도 했고, 직접 부분 실습도 해보았다.
공부를 하면서 실제로 내가 잘 알지 못하는 부분에 대한 내용도 알게 되었고, 문제풀이를 통해서 내가 알고 있다고 착각하고 있는 부분에 대해서 다시 복습할 수 있는 좋은 계기가 되었다.
그리고 이전에 SQL 성능 향상을 위한 튜닝에 대한 내용에 대해서 얼핏 들어보았었는데, 시험에서 `SQL 최적화 기본 원리`에 대한 내용을 통해서 SQL 튜닝이 뭔지, 내가 작성한 SQL 쿼리의 실행계획을 어떻게 확인하고 분석하는지에 대해서 아직 기본적인 내용만 알고 있지만, 대략적으로 어떤 내용인지 알게 되었고, 나중에 직접 SQL 쿼리를 작성하면서 실행계획 부분도 한 번 꼼꼼하게 확인해봐야겠다고 느꼈다.
자 그럼 이번 포스팅에서는 NL Join, Sort Merge Join 그리고 Hash Join에 대해서 한 번 정리해보도록 하겠다.

<!-- more -->

<div align="center">
  <img src="/images/post_images/220523_sql_join.JPG" alt="SQLD자격검정 실전문제(NL Join/Sort Merge Join/Hash Join 필기)">
</div>

문제를 풀면서 NL Join, Sort Merge Join, Hash Join에 대한 이해가 부족하다고 느껴서 급하게 이론 내용을 찾아 정리를 하였다.

## <ins><b>NL Join</b></ins>

NL Join은 Nested Loop Join으로, 프로그래밍에서 중첩 For-loop와 유사한 방식이라고 이해할 수 있다.
예를들어, 부서와 직원 테이블이 있다고 가정하자. NL Join에서는 Outer Table(부서 테이블)에서 Inner Table(직원 테이블)로 1차적으로 부서 테이블의 첫 행에 해당하는 부서에 속한 직원들에 대한 정보를 가져오고나서 Outer Table로 돌아오고, Join이 된다.
이러한 일련의 과정이 부서테이블에 있는 부서의 갯수만큼 N번 발생한다.(N번 For-loop 돈다고 생각하면 됨)

따라서 위의 내용을 통해 이해한 NL Loop의 특성에 따라 Outer Table과 Inner Table에는 조건이 필요하다.

- ### **[Outer Table]**
- 소량의 데이터를 가진 테이블을 Outer Table로 지정하는 것이 성능향상에 좋다.

- ### **[Inner Table]**
- 만약에 Inner Table의 `Join Column에 Index가 걸려있지 않으면, 굉장한 비효율이 발생`하게 된다.
  그 이유는 Outer Table에서 Inner Table로 일치하는 데이터를 찾기 위해서 갈때, 한 건 한 건 가게 되는데, `Join Column에 Index가 걸려있지 않다면, 매번 Full scan을 해야되는 경우가 생긴다.`
- 건 by 건으로 Join하는 방식이기 때문에 `대량의 테이블을 Join하는 방식으로는 적합하지 않다.`
- `OLTP성 환경의 쿼리에 적절`하다. 흔히 말하는 트랜잭션 처리에 OLTP(Online Transaction Processing)의 의미가 포함되어있다. 트랜잭션의 주 특징은 연산 실패시 Rollback이 지원된다는 것이며, 주로 대규모의 처리보다는 `소규모의 정교한 데이터 구성이 필요한 데이터의 처리가 중점`이 된다.

ref. `OLAP`(Online Analytical Processing)는 Database 자체적으로 운용되는 시스템이 아닌, DW 등의 시스템과 연관되어 Data를 분석하고 의미있는 정보로 치환하거나, 복잡한 모델링을 가능하게 하는 분석 방법이다.

## <ins><b>Sort Merge Join</b></ins>

- Sort Merge Join 방식도 NL Join과 같이 중첩 For문과 유사한 방식으로 처리된다. `(단, 차이점은 Join하고자 하는 두 테이블을 Join 기준 칼럼을 기준으로 Sort하고나서 Join을 시킨다)`

- 적절한 index가 없어서 NL Join을 쓰기에 너무 비효율적인 경우에 사용된다.

- 동등조인(Equal join)이 아니라 `범위로 Join을 하는 경우`, 이 경우에 Sort Merge Join은 적절한 수행원리이다.
  (동등 연산자(=)가 아닌, 범위로 두 테이블을 조인할때 사용되는 수행원리)

- Table Random Access가 발생하지 않고, Sorting 작업이 PGA 영역에서 수행되다.

ref. `PGA(Program Global Area)영역과 SGA(System Global Area)영역에 대한 정의 `

<div align="center">
  <img src="/images/post_images/220523_oracle_pga_sga_area.png" alt="PGA와 SGA">
</div>

- `PGA는` 사용자마다 공유하지 않고, 개별적으로 사용하는 영역을 말한다. 데이터베이스에 접속하는 모든 사용자에게 할당되는 각 각의 서버 프로세스가 독자적으로 사용하는 오라클의 메모리 영역이다.
- `SGA는` 각 각의 서버 프로세스에 할당되는 메모리 영역인 PGA와는 다르게 모든 사용자가 공유 가능한 메모리 공간이다.

- 경합이 발생하지 않아서 성능에 유리하다는 장점을 가지고 있다.

## <ins><b>Hash Join</b></ins>

- `Batch에서 쓰면 좋은 수행원리`이다.
- `대용량 테이블을 조인`할때 좋다.
- Hash Join에서는 작은 집합인 Outer Table(직원 테티블)을 Build Input으로 삼아서 Hash 영역으로 올라간다. `(Hash 영역 = PGA영역으로 처리속도가 매우 빠르다)`
- Hash 영역으로 작은 집합이 Build input으로 올라갈때는 Join 칼럼을 기준으로 해시함수가 적용되기 때문에 `Key column에 중복이 없을 수록 성능에 유리`하다.
- 큰 집합(프로브 테이블(Probe table))에 해당하는 테이블이 해시함수를 통해 해시 테이블을 탐색하면서 조인을 한다.
- `동등조인(Equal Join)만 가능`하고, Sort Merge Join과 같이 Random Access에 따른 부하가 없다.
- `Hash 영역`에 들어가는 테이블(Outer table)의 크기가 충분히 작아야 한다. 그 이유는 Hash 영역의 사이즈가 정해져있는데, 테이블 용량이 정해진 사이즈를 초과할 경우, 디스크 영역을 추가적으로 사용해야되는 경우가 발생하기 때문에 성능에 매우 불리하게 된다.
- 수행빈도가 높은 OLTP 환경에서는 좋지 않은 수행방법이다. 그 이유는 Hash Join으로 수행하게 되면, 오히려 CPU나 메모리 사용량이 늘어서 성능이 저하될 수 있다.

## <ins><b>SQL 성능 향상을 위한 튜닝</b></ins>

(1) 내가 작성한 쿼리가 어떻게 실행되고 있는지 `실행계획을 확인`한다.
(2) NL Join / Sort Merge Join / Hash Join을 실행계획은 통해 확인해서 `상황에 맞지 않은 수행원리인 경우, Oracle의 Hint를 써서 다른 방식으로 쿼리를 유도를 해본다.`
(3) I/O나 실행시간이 어떻게 변화하는지 확인을 해서 더 좋은 방향으로 튜닝을 한다.
