---
title: 220808 Apache Airflow 
date: 2022-08-08 10:08:00
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

## <ins><b>Configuration 살펴보기</b></ins>

더 많은 Airflow Worker가 필요하다면, 추가 machine에서 celery worker를 명령한다.

### **Flower**

Airflow workers를 dashboard를 통해서 모니터링하기 위한 툴이다. Celery Executor를 사용하면, Flower에 접속해서 Celery Executor의 Administrator와 Airflow worker를 대시보드를 통해 모니터링 할 수 있다.

`localhost:5555/dashboard`

아래의 Worker dashboard를 살펴보면, Max concurrency 값이 16인 것으로 보아, 최대 16개의 Task를 동시에 실행하는 것이 가능하다. 이는 사용하는 PC의 리소스에 따라 줄일 수도 늘릴 수도 있다.

다음으로 Queues 메뉴는 유용하게 사용될 수 있는데, 특정 Task를 특정 worker로 라우팅되도록 할 수도 있다. 
예를들어 높은 리소스를 소비하는 Task가 존재하고, 현재 하나의 높은 리소스 Worker를 가진다고 가정하면, queue를 생성해서 queue를 Worker에 붙이고, 높은 리소스 소비 Task를 생성한 queue로 보내서 해당 Worker만 해당 작업을 실행할 수 있도록 queue를 지정해서 사용할 수 있다.

<!-- more -->

<div align="center">
  <img src="/images/post_images/220808_flower_dashboard_monitor.png" alt="Apache Airflow Worker monitoring">
</div>


## <ins><b>DAG 예시 제거하기</b></ins>

Airflow instance를 깨끗하게 유지시키기 위해서 아래의 방법으로 DAG를 UI로부터 제거한다.

(1) docker-compose.yml 파일에서 `AIRFLOW__CORE__LOAD_EXAMPLES` 환경변수를 true에서 false로 변경 후 파일을 저장한다.

(2) 아래 명령을 통해서 Airflow를 재시작한다.
  ```zsh
  $docker-compose down && docker-compose up -d
  ```

## <ins><b>Flower 모니터링</b></ins>

DAG를 Trigger한 후에 Flower를 통해 모니터링을 해보면, Active column을 통해 현재 DAG의 Task가 Celery worker에서 실행되고 있음을 확인할 수 있으며, status가 Processed를 거쳐 Succeeded로 완료가 되는 것을 확인할 수 있다.

Flower에서 Task를 모니터링할때 주의해서 봐야되는 부분은 `failure`와 `retried`부분으로, 이 부분에 대한 카운트가 있는지에 대해서 확인해야 한다. 이후에 Tasks 메뉴를 통해서 Task의 개별 UUID를 클릭해서 실행된 Task의 상세정보에 대해서 확인할 수 있다. 상세정보로는 Task가 현재 어떤 상태이고, 어느 Worker에서 실행이 되었는지에 대해서도 확인할 수 있다.

Task가 실행된 Worker는 queue에 따라 다른 Worker에서 실행할 수 있다.

## <ins><b>복수 개의 Queue가 필요한 이유</b></ins>

Airflow에서 queue(FIFO)는 trigger되기를 기다리는 Task들의 대기열로 볼 수 있다. Celery queue는 Broker와 Result Backend로 구성이 되어있고, Worker는 복수 개로 구성이 되어있다. 복수 개의 Worker는 각 각 5 CPUs, GPU, 1GPU등의 스펙을 가질 수 있는데, 이는 처리해야되는 Task에 따라서 Worker를 지정해서 처리해야한다.

만약 복잡한 연산처리가 필요한 경우에는 5CPUs Worker를 통해 Task가 실행되도록 해야하며, 머신러닝과 같은 처리를 위해서는 GPU의 성능이 좋은 Worker에서 처리를 해야한다. 그리고 그 이외의 경우에 일반적인 Task의 처리에서는 1CPU worker에서 처리되도록 분류해야한다. 이 경우에는 어떻게 분류를 해서 처리를 해야할까?

바로 Celery Queue에 세 개의 queue(high_cpu, ml_model, default)로 분류해서 high_cpu 큐는 5CPUs Worker에서 처리되는 Task를 쌓아서 처리하도록 하고, ml_model 큐는 GPU 성능이 좋은 Worker에서 처리가 되도록 Task를 쌓아둔다. 그리고 마지막으로 일반적인 Task를 쌓아두는 큐의 역할을 default queue가 담당하게 된다. 

## <ins><b>Celery worker 생성하기</b></ins>

Queue를 생성하기 전에, 우선 복수 개의 Celery Worker를 Apache Airflow 인스턴스에 생성해야 한다.
docker-compose.yml 파일에서 `airflow-worker` 서비스 항목을 복사해서 하나 더 생성을 한 다음에 아래 명령으로 서비스를 다시 시작한다.
서비스를 다시 시작하면, `localhost:5555` Flower web UI에서 두 개의 Worker가 생성이 된 것을 확인할 수 있다.

실제로 다수의 machine이 있을때, machine에서 아래의 명령을 실행함으로써 Celery worker로써 동작하도록 할 수 있다.

```zsh
$celery worker
```

## <ins><b>queue 생성하기</b></ins>

Airflow에서 queue를 생성하는 것은 간단하다. airflow-worker 서비스의 command 항목에서 celery worker를 해주는 경우, default queue를 통해 worker로 Task가 분배가 되지만, 아래와같이 worker 생성시에 command에 -q 옵션으로 Worker에 queue를 붙여주면, 특정 Task를 지정한 airflow worker에서 실행되도록 할 수 있다.

  ```yaml
  airflow-worker-1:
  <<: *airflow-common
  command: celery worker -q high_cpu
  healthcheck:
    test:
      - "CMD-SHELL"
      - 'celery --app airflow.executors.celery_executor.app inspect ping -d "celery@$${HOSTNAME}"'
    interval: 10s
    timeout: 10s
    retries: 5
  environment:
    <<: *airflow-common-env
    # Required to handle warm shutdown of the celery workers properly
    # See https://airflow.apache.org/docs/docker-stack/entrypoint.html#signal-propagation
    DUMB_INIT_SETSID: "0"
  restart: always
  depends_on:
    <<: *airflow-common-depends-on
    airflow-init:
      condition: service_completed_successfully
  ```

## <ins><b>Task를 지정한 queue에 보내기</b></ins>

DAG에서 Operator로 Task를 작성할때, Operator의 인자로 아래와 같이 queue를 지정해주면, 지정한 queue로 해당 Task가 전송된다.

```python
transform = BashOperator(
  task_id='transform',
  queue='high_cpu',
  bash_command='sleep 10'
)
```

## <ins><b>Airflow 동시성을 위한 parameters</b></ins>

병렬로 동시에 처리할 tasks와 DAG Runs를 정의하기 위해서는 아래의 parameters를 configuration settings에서 설정해줘야 한다.

### **parallelism / AIRFLOW__CORE__PARALELISM**
이 parameter는 Airflow의 Scheduler당 실행할 수 있는 task instances의 갯수에 대해서 정의한다. default로는 Scheduler당 최대 32개까지 동시에 실행이 가능하며, Scheduler의 수가 늘면 그 수가 배가 된다.

### **max_active_tasks_per_dag / AIRFLOW__CORE__MAX_ACTIVE_TASKS_PER_DAG**
이 parameter는 각 각의 DAG에서 동시(concurrency)에 실행할 수 있는 최대 task instance의 수를 정의한다. default로는 최대 16개의 tasks를 지정된 DAG에서 동시에 처리할 수 있다. (`모든 DAG 실행에서`)

### **max_active_runs_per_dag / AIRFLOW__CORE__MAX_ACTIVE_RUNS_PER_DAG**
이 parameter는 DAG당 최대 활성시킬 수 있는 DAG의 수를 정의한다. default로는 최대 16개의 DAG를 동시에 실행할 수 있다.

## <ins><b>Sequential executor에서의 SQLite의 특징</b></ins>

Sequential executor에서 사용되는 SQLite는 무제한으로 reader를 허용한다. 하지만 writer는 한 번에 한 명 밖에 허용하지 않는다. 이러한 이유로 SQLite는 복수의 Worker에서 병렬로 Tasks를 실행할 수 있는 Local executor와 Celery executor에서는 사용될 수 없다.

## <ins><b>Repetitive Patterns</b></ins>

만약 파일을 다운로드하는 Task 세 개가 주어지고, 세 개의 Task가 일괄적으로 Checking Files이라는 하나의 Task를 통해 파일 검사를 시행한다. 그 후에 세 개의 Task로 분리하여 다운로드 받은 파일들을 처리한다. 이런 구조로 되어있는 Task sequence에서는 다운로드하는 세 개의 Task를 그룹화해서 `Downloading Files`인 하나의 Task로 만들고, 다운로드 받은 파일을 처리하는 `Processing Files`도 하나의 Task로써 작성한다. 

이는 DAG의 Task 구조를 쉽게 읽고 유지보수하기 위함이다. 이는 `SubDAGs`나 `TaskGroup`을 사용해서 구현할 수 있다.

`SubDag를 사용해서 구현했을때에는 SubDag로 묶인 Task를 클릭해서 별도의 DAG 페이지에서 하위 Task 구성만 별도로 확인을 할 수 있었는데, TaskGroup을 사용해서 구현을 하면, 현재 DAG 구조에서 가시적으로 Group이 표시됨을 확인할 수 있었다.`

