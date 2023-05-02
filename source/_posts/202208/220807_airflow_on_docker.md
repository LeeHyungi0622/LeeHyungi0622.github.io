---
title: 220807 Apache Airflow 
date: 2022-08-07 09:35:00
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

## <ins><b>Backfilling</b></ins>

DAG를 처음 실행하게 되면, scheduler는 자동으로 non-triggered DagRuns을 시작 날짜(start_date)와 현재(now) 사이 시점에서 실행하게 된다. 
catch up mechanism은 자동으로 non-triggered DagRun을 마지막으로 실행된 날짜와 현재 시간 사이에서 실행할 수 있도록 허용한다.

예를들어, 만약에 DAG를 2일동안 중지시키고나서 DAG를 다시 시작했다면, 이 기간 동안 트리거되지 않은 DAG 실행에 해당하는 일부 DAG 실행이 발생합니다. 
Backfilling mechanism은 historical DagRuns를 실행하도록 하는데, 예를들어 start date 이전의 기간에 DagRun을 실행할 수 있다. 
방법은 Airflow DAG Backfill 명령을 실행하는 명령을 사용하면 된다. 

(`예를들어 01/03(start_date)부터 01/07(now)까지 DAG RUN을 실행했고, start_date 이전인 01/01부터 01/02 기간동안 DAG RUN을 실행하고자 한다면, Backfilling mechanism을 위한 명령을 사용하면 된다`)

```python
with DAG('my_dag', start_date=datetime(2022, 1, 1), schedule_interval='@daily', catchup=False) as dag:
```
이렇게 catchup=False로 설정값을 바꿔주면, non-triggered DAG RUN이 실행되게 된다. 이 mechanism은 과거에 non-triggered DAG RUN을 자동으로 재실행할때 사용된다.

## <ins><b>Executor</b></ins>

이전 포스팅에서도 다뤘던 내용이지만, Executor는 이름 자체는 Task를 실행할 것 같지만, Task를 실행하지 않는다. 단지 tasks를 시스템에서 어떻게 실행할 것인가에 대해 정의한다.

Executor에는 다양한 종류가 있는데, local executors와 remote executors가 있다. `local executor`는 여러 개의 task를 single machine에서 실행을 하고, `sequential executor`는 single machine에서 한 번에 하나의 task를 실행할때 사용된다. 

remote executor에는 Celery executor가 있는데, tasks를 multiple machine, 그리고 salary cluster에서 실행한다. K8s executor는 multiple machine에서 K8s cluster의 multiple pods에서 multiple tasks를 실행한다.

Executor의 변경은 Airflow의 환경설정 파일에서 executor parameter를 변경함으로써 적용할 수 있다. (`사용되는 executor에 따라 변경해야 되는 별도의 환경설정 요소가 있다`)

<!-- more -->

### **Executor configuration 확인**

  Airflow의 Executor의 configuration 파일을 확인하기 위해 docker container(scheduler)의 cfg 파일을 현 위치에 복사하도록 한다.

  ```zsh
  $docker cp materials_airflow-scheduler_1:/opt/airflow/airflow.cfg .
  ```

  현재 Airflow를 docker를 사용해서 띄워서 사용하고 있고, Docker-compose 파일의 환경변수 정의를 보면, `AIRFLOW__CORE__EXECUTOR: CeleryExecutor`를 통해 CeleryExector가 사용되고 있음을 확인할 수 있다. 하지만, scheduler container에서 복사한 airflow.cfg 파일의 정의를 보면, SequentialExecutor가 정의되고 있음을 알 수 있다. 

  이는 configuration 파일에 정의된 내용을 docker-compose.yml 파일에서 정의된 환경변수가 덮어쓰고 있다는 의미이다. 따라서 환경설정에 관한 내용은 docker-compose.yml 파일에서 정의할 수 있도록 해야한다.

### **Sequential Executor**

sequential executor는 Airflow를 수동으로 설치했을때 설정되는 default executor이다. Sequential Executor는 Web Server, Scheduler, SQLite로 구성되며, DAG가 실행될때, Scheduler는 한 번에 하나의 Task를 실행한다. (`동시에 여러 개의 Task 실행 불가능`)

따라서 매우 간단한 실험을 하거나, 디버깅을 하는 경우에만 사용이 되며, executor 설정에 대한 환경변수 부분만 수정해주면 사용할 수 있다.

### **Local Executor**

Local Executor는 Sequential Executor에서 한 단계 더 나아가서 단일 기계에서 여러 작업을 동시에 할 수 있도록 해준다. 그 외에도 데이터베이스가 Sequential Executor에서는 SQLite인것에 반해 Loal Executor에서는 Postgres 데이터베이스를 사용한다.

SequentialExecutor와 달리 Local Executor에서는 두 개이상의 Task에 대해 동시 실행이 가능하다.

  ```python
  executor=LocalExecutor
  sql_alchemy_conn=postgresql+psycopg2://<user>:<password>@<host>/<db>
  ```
단, 단일 머신에 한정해서 Task가 실행되기 때문에 확장성이 좋지 않으며, 리소스가 단일 기계로 제한된다는 단점이 있다.

### **Celery Executor**

Celery Executor는 동시에 여러 컴퓨터에서 작업을 실행하기 위해 Celery 클러스터를 사용한다. 

우선 간단한 구성을 살펴보면, Web server, Meta database(Postgres), Scheduler 그리고 추가적으로 Worker가 있는데, 이는 실제로 Task를 실행하는 것을 담당하는 기계인 Airflow의 Worker이다. 만약에 3명의 Airflow Worker가 있다면, 작업을 실행하는 3대의 machine이 있다는 것을 의미하고, 더 많은 작업을 실행하기 위해 더 많은 리소스가 필요한 경우에는 추가적으로 Airflow Worker를 추가하기만 하면 된다.

Celery Queue는 `Result Backend`와 `Broker`, 두 가지 구성요소로 구성되어 있는데, Result Backend에는 Airflow worker가 Task의 상태정보를 저장하는데 사용이 된다. 그리고 Broker는 Scheduler가 실행을 하기 위한 task를 보내면, worker가 task 실행을 위해 pull해서 사용하기 위한 queue의 역할을 한다.  

Scheduler가 Celery Queue의 Broker에 DAG의 Task를 전달하면, Worker 중 하나에서 해당 Task를 pull하게 된다. 그리고 Worker에서 작업이 완료되면, 작업 상태가 Result Backend에 저장이 된다. Result Backend는 Airflow의 Database와 같은 역할을 하며, 원하는 경우, 다른 데이터베이스를 사용할 수 있다. 

Celery Executor를 사용할때에는 Celery Queue를 설치해야 되는데, 이는 Redis나 RabbitMQ가 될 수 있다.

  ```pyton
  executor=CeleryExecutor
  sql_alchemy_conn=postgresql+psycopg2://<user>:<password>@<host>/<db>
  celery_result_backend=postgresql+psycopg2://<user>:<password>@<host>/<db>
  celery_broker_url=redis://:@redis:6379/0
  ``` 