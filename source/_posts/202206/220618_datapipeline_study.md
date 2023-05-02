---
title: 220618 데이터 파이프라인 구축 오프라인 수업 / 4주차 오프라인 수업 전 준비
date: 2022-06-18 23:42:00
tags:
  - AWS
  - Data-Pipeline
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220604_preparation.jpeg" alt="Preparation">
</div>

<br/>
<br/>

이번 포스팅에서는 내일 있을 데이터 파이프라인 구축 관련 수업에서 실습할 내용들에 대해 미리 실습을 해보고, 개념적인 부분을 좀 다져보려고 한다.
내일 수업에서는 Apache Kafka를 실습하는데, Kafka cluster의 각 Broker와 Zookeeper, Producer와 Consumer를 전부 Docker container로 띄우고 관리한다. 이미 Docker에 대해 공부했고, Kubernetes에 대해서 추가적으로 공부를 하는 중이라 이번 수업을 통해 Kafka의 각 구성요소들을 Docker container에 띄워서 관리하면 많은 도움이 될 것 같다.
그리고 여지까지 했던 실습들과 비교했을때 상대적으로 구조가 복잡하기 때문에 미리 Kafka의 개념적인 내용을 머릿속에 그리면서 실습하게 될 전체적인 구조에 대해서 파악하고 가야 할 필요성을 느꼈다. 


<!-- more -->

## <ins><b>Apache Kafka</b></ins>

  우선 실습은 EC2 인스턴스 (Ubuntu)를 하나 띄워서 인스턴스 내부에서 진행하게 된다.

  **(1)** Kafka cluster의 각 Broker, Zookeeper, 그리고 Producer와 Consumer를 Docker로 띄워서 관리를 할 것이기 때문에 우선적으로 `docker와 docker-compose를 설치`한다.
  **(2) consumer 구성에 대한 docker-compose.yml 파일**
  dockedr-compose.yml파일은 multi consumer와 single consumer로 나뉘어져있는데, 이름 그대로 multi consumer의 docker-compose.yml에는 producer_host가 하나, consumer_host가 1, 2로 두 개 설정되어있다. 그리고 single consumer의 docker-compose.yml에는 producer_host 하나와 consumer_host 하나가 설정되어있다. 
  이 차이점에 유의해서 실습하도록 하자. 앞서 두 파일의 차이점에 대해서 살펴보았으니, 이제 두 파일의 공통점에 대해서 살펴보면, 두 파일은 공통적으로 zookeeper에 대한 container service 3개, kafka에 대한 container service 3개, kafka_manager에 대한 container service 1개, 그런데 zookeeper가 Kafka cluster에 대해 관리를 해준다고 알고 있었는데, kafka manager는 뭐지? 찾아보니, `Kafka manager는 CMAK로, 브라우저상에서 Kafka 클러스터와 Topic(Partitions, Replication Factor)을 손쉽게 추가하고 관리`할 수 있게 해주는 것 같다. 
  <br/>

  - ### <ins><b>Zookeeper</b></ins>
  
    zookeeper는 항상 재시작 되도록 설정이 되어있으며, port는 `2181, 2182, 2183번`으로 설정되어있고, 환경변수로는 SERVER_ID, CLIENT_PORT, TICK_TIME, INIT_LIMIT, SYNC_LIMT, ZOOKEEPER_SERVERS(zk1, zk2, zk3:2888:3888)에 대한 정보를 담고 있다. 
    위의 각 각의 환경변수의 의미에 대해서 한 번 찾아보고 정리를 해보자.

    https://docs.docker.com/config/containers/start-containers-automatically/#:~:text=Docker%20provides%20restart%20policies%20to,process%20managers%20to%20start%20containers.

    `restart:always`설정은 컨테이너가 항상 실행되도록 한다. 컨테이너가 중지되면 즉시 다시 시작되며, 직접적인 중단(docker stop)된 경우에는 컨테이너를 다시 시작하지 않고, Docker daemon이 재시작되거나 컨테이너 자체가 수동으로 재시작되면 다시 시작된다. (`오류로 인한 중단인 경우에 컨테이너가 다시 시작`) 
    반면에 producer_host와 consumer_host의 경우에는 `restart: on-failure`로 설정이 되어있다. 
    이 옵션은 error(non-zero exit code)때문에 exit이 되거나 docker daemon이 컨테이너를 재시작하는 시도의 횟수에 대해 `:maz-retries`옵션으로 제한한 경우 재시작되도록 할때 사용된다.

    `restart:always`와 `restart: on-failure`의 차이는 전자는 컨테이너가 오류로 인해 중지되는 경우에 한해 컨테이너가 재시작되고, 후자는 컨테이너가 오류로 인해 exit되는 경우와 Docker daemon의 컨테이너 재시작 시도 횟수에 대한 제한이 걸려있는 경우에 컨테이너가 재시작되도록 설정할때 사용된다.

    (`docker 데몬은 docker를 이용하기 위해서 반드시 실행중이어야 된다. 하지만 모종의 이유로 인해 데몬이 꺼지거나 서버 재부팅시 데몬이 실행되도록 해놓지 않았다면, 이용하던 컨테이너들이 제공하던 서비스를 전부 이용할 수 없게 되는 경우가 생긴다.`)
    
    - #### **Docker 데몬 실행**
      ```zsh
      # dockerd이 foreground 형태로 실행이 된다.
      $dockerd
      # dockerd이 background 형태로 실행이 된다.
      $dockerd &
      # service혹은 systemctl 명령을 통해 docker 데몬 실행 가능
      $service docker start (stop|restart)
      $systemctl start docker
      ```
    일반적으로 production workload에 `always, on-failure, unless-stopped` 이 세가지 옵션 중에 하나가 사용되며, Docker container는 장기간 실행되는 백그라운드 서비스에 자주 사용되기 때문에 일반적으로 문제가 발생하면 다시 시작되기를 원한다. 

    producer_host와 consumer_host의 `tty:true` 옵션에 대해서 찾아보기
    tty는 terminal로, linux환경에서 docker container에 연결해서 command를 실행할때 필요한 옵션이다.
    이러한 이유로 producer_host와 consumer_host에만 tty:true option이 추가되있다.