---
title: 220805 Apache Airflow 
date: 2022-08-05 10:48:00
tags:
  - Airflow
  - Data-Pipeline
categories:
  - Airflow
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220804_apache_airflow.png" alt="Apache Airflow">
</div>

<br/>
<br/>

## <ins><b>Apache Airflow UI 구성</b></ins>

Apache Airflow UI에서 DAGs 리스트를 보면, 현재 Apache Airflow에 포함되어있는 DAG의 목록이 출력된다. 리스트 중 하나의 아이템을 살펴보면, DAG의 이름의 좌측에 해당 DAG를 Pause/Unpause 할 수 있는 토글 버튼이 있고, 이름의 아래에는 data pipeline이나 팀 또는 기능별로 묶어서 관리할 수 있도록 태그가 표시되어있다. `(단, tag에 따라 permission을 부여할 수 없다)`

그 외에 Owner에 대한 정보와 Runs 칼럼에서는 `queued, success, running, failed` 상태별로 현재 DAG이 실행 상태를 확인할 수 있다. Last Run 및 Next Run 항목을 통해서는 DAG가 언제 마지막으로 실행이 되었고, 그 다음 실행은 언제 되는지에 대해 확인할 수 있다.

Recent Tasks에서는 총 15개의 상태 정보로 나뉘어 활성화된 DAG의 Task들의 실행 상태에 대해서 확인을 할 수 있다. (`none, removed, scheduled, queued, running, success, shutdown, ...`)

Actions에서는 강제로 Trigger 시키거나 DAG를 삭제할 수 있는데, DAG를 삭제한다는 의미가 DAG 자체를 삭제하는 것이 아닌, Meta store에서 DAG RUN Instance를 삭제한다는 것을 의미한다.

## <ins><b>Apache Airflow DAG item 상세보기</b></ins>

DAG의 이름을 클릭하면, Grid를 통해 실행한 DAG들의 상태 정보에 대해서 모니터링 할 수 있으며, Graph View를 통해서는 DAG의 각 Tasks가 어떤 Tasks를 dependencies로 가지고 있는지에 대해서 구조적으로 확인을 할 수 있다.

`Landing Times`에서는 DAG에서의 모든 작업들이 scheduled 상태에서 completion으로 완료되는데 얼마나 걸렸는지에 대한 정보를 시각화된 그래프로 확인할 수 있다. 만약 시간이 오래걸린다면, 걸리는 시간을 줄일 수 있도록 별도의 대응이 필요하다.

`Calendar view`에서는 각 각의 네모칸이 특정 DAG의 상태 다이어그램 정보를 종합해서 보여준다. 특정 DAG가 특정 날짜에 문제가 생긴다면, 빨간색으로 칸이 표시되며, 성공적으로 DAG가 시행이 되었다면 해당 일자에 초록색으로 포시된다. 점으로 표시된 칸은 얼마나 많은 다이어그램이 해당 날에 계획되어있는지에 대한 정보를 제공한다. 따라서 이러한 모니터링 정보를 기반으로 어떤 날에 데이터 파이프라인을 수정해서 문제를 해결해야되는지 알 수 있다.

`Gantt view`에서는 DAG의 특정 Task가 완료되는데에 얼마나 시간이 걸렸는지와 pipeline에서 bottleneck이 발생했는지에 대한 전반적인 overview를 제공한다. 상대적으로 긴 직사각형은 그만큼 Task를 실행하는데 시간이 걸렸다는 의미이고, 오래걸린 작업에 대해서는 어떻게 하면 작업이 완료되는데 걸리는 시간을 단축시킬 수 있는지에 대한 고민이 필요하다. 

직사각형이 overlapping되었다는 것은 복수 개의 Task를 동시에 실행할 수 있다는 것을 의미한다. (`DAG parallelism`)

`Code view`에서는 데이터 파이프라인의 코드에 접근할 수 있는데, 이를 통해서 적용한 수정사항이 DAG에 제대로 적용이 되었는지 확인을 할 수 있다.

<!-- more -->

## <ins><b>첫 번째 데이터 파이프라인 작성 in Airflow</b></ins>

`(1)` Create table with Postgres separator 

이 단계에서는 어떻게 SQL request를 Airflow에서 실행하는지 알 수 있다.

`(2)` API 사용가능 유무 확인

이 단계에서는 API를 사용할 수 있는지에 대해 확인할 수 있으며, 특별한 종류의 operator를 사용하여, 다음 Task로 넘어가기 전에 특정 이벤트를 기다릴 수 있도록 설정할 수 있다. 

`(3)` User 정보 추출 -> process_user -> store_user

추출된 User 정보를 Airflow에서 가장 보편적인 operator를 사용해서 처리할 수 있다. (`User 정보를 Password 데이터 베이스에 저장`)

## <ins><b>DAG?</b></ins>

DAG는 방향성 비순환 그래프로, Node는 Task를 나타내고, Edge는 각 Task간의 Dependency 나타낸다.

  ### **DAG Skeleton**

  `(1)`  Import the DAG object

  `(2)`  Instantiate a the DAG object

  `(3)`  Define a unique dag id

  `(4)`  Define a start date

  `(5)`  Define a scheduled interval

  `(6)`  Define the catchup parameter

    ```python
    # 이 파일이 DAG정보를 담고 있는 파일인지 알 수 있다.
    from airflow import DAG

    from datetime import datetime

    # 첫 번째 parameter는 DAG ID로, unique identifier로 DAG의 이름이 된다. 이 ID는 Airflow instance의 모든 태그들에 대해 유일해야 한다.
    # 두 번째 parameter는 start_date로, DAG가 언제 schedule이 시작되느지에 대한 날짜 정보를 넣는다.
    # 세 번째 parameter는 schedule interval로, DAG가 trigger되는 빈도 수에 대해서 정의한다. (매일, 매 15분, 주에 1번 등...) @daily는 매일 자정을 의미한다.
    # 네 번째 parameter는 catchup으로, false로 setting 하는 것이 좋다. catchup은 default로 true로 설정이 되어있는데, 이 의미는 현재와 start date 사이에
    # DAG가 triggered되어있지 않으면, Airflow UI로부터 데이터 파이프라인의 scheduling을 시작하면, 해당 기간내(현재~마지막으로 DAG가 실행된 날짜 or 시작 날짜)의
    # 모든 non-triggered DAG를 실행한다. 따라서 false로 설정함으로써 직접 세세하게 실행할 DAG를 컨트롤 할 수 있으며, 한 번에 많은 양의 DAG를 동시에 실행시키지 않아도 된다.
    with DAG('user_processing', start_date=datetime(2022, 1, 1), schedule_interval='@daily', catchup=False) as dag:
        None
    ```

## <ins><b>Operator?</b></ins>

  Operator는 데이터 파이프라인에서 하나의 Task를 정의한다. 따라서 하나의 Operator에는 두 개 이상의 Task가 위치하면 안된다. 
  그 이유는 예를들어 Python Operator가 존재한다고 가정하고, Operator 내에 Cleaning Data와 Processing Data가 같이 존재한다면, Processing Data가 실패했을때 같은 Operator 내에 존재하는 두 개의 Task에 대해 모두 재시작을 해야한다. (`Processing Data에 대한 작업만 재시작해야 한다`)

  따라서 하나의 Operator에는 하나의 Task가 배치되도록 구성해야 한다. `PythonOperator(Cleaning Data) -> PythonOperator(Processing Data)`

## <ins><b>Providers?</b></ins>

  Apache Airflow는 module화 되어 만들어졌다. 

  처음 Apache Airflow를 시작했을때, 아래의 명령을 통해서 Airflow Core를 설치했다.

  ```zsh
  # Airflow Core
  $ pip install apache-airflow
  ```

  만약에 AWS, Snowflake, Dbt, Databricks와 같이 사용되어야 한다면, 추가적으로 provider를 설치해야 한다.
  
  ```zsh
  # AWS
  $pip install apache-airflow-providers-amazon
  # Snowflake
  $pip install apache-airflow-providers-snowflake
  # Dbt
  $pip install apache-airflow-providers-dbt-cloud
  # Databricks
  $pip install apache-airflow-providers-databricks
  ```

## <ins><b></b></ins>