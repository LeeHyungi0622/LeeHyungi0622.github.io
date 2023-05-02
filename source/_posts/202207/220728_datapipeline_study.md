---
title: 220728 Burrow - Kafka consumer Lag 모니터링
date: 2022-07-28 15:51:00
tags:
  - AWS
  - Data-Pipeline
  - Kafka
  - Lag
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220728_kafka_burrow.png" alt="Burrow">
</div>

<br/>
<br/>

이번 포스팅에서는 Kafka의 Consumer Lag를 모니터링할 때 필수적으로 사용되는 Burrow에 대해서 정리해보려고 한다.

이번 포트폴리오에 추가 할 사이드 프로젝트로 Kafka를 활용해서 Kafka cluster를 구성하고, Kafka-client 라이브러리를 활용하여 Python으로 Producer 및 Consumer를 구성하였다. Consumer에서는 ELK 스택의 docker 컨테이너를 연결하여, Producer로부터 유입된 로그 데이터를 Logstash를 통해 Elasticsearch에 저자을 하고, 저장된 로그 데이터를 Kibana를 통해서 시각화하였다. 

Kafka consumer는 kafka-client 라이브러리를 활용해서 Java, Python등의 언어로 개발을 할 수 있는데, 생성한 KafkaConsumer 객체를 통해 현재 lag 정보를 가져올 수 있다.

만약 lag을 실시간으로 모니터링하고 싶은 경우에는 Elasticsearch나 influxdb와 같은 곳으로 `Consumer lag metric` 정보를 보낸 뒤에 Grafana 대시모드를 통해서 실시간으로 시각화하여 확인 할 수 있다.

하지만 Consumer 단위에서 lag을 모니터링하는 것은 아주 위험하고, 운영요소가 많이 들어간다는 문제가 있다. 그 이유는 Consumer logic 단에서 lag을 수집하는 것은 Consumer 상태에 dependency가 걸리기 때문이다. 
만약에 Consumer가 비정상적으로 종료되게 된다면, 더 이상 Consumer는 lag 정보를 보낼 수 없기 때문에 더 이상 lag을 측정할 수 없는 문제가 발생한다.  

그리고 차후에 추가적으로 consumer가 개발될 때 마다 해당 consumer에 lag 정보를 특정 저장소에 저장할 수 있도록 로직을 개발해야되기 때문에 공수가 많이 든다. 

만약에 consumer lag을 수집할 수 없는 consumer라면, lag을 모니터링 할 수 없기 때문에 까다로워진다. 

이러한 이유로 인해 linkedIn에서는 Apache kafka와 함께 Kafka consumer lag을 효과적으로 모니터링 할 수 있도록 Burrow를 개발하였다.

Burrow는 오픈 소스 프로젝트로, Go 언어로 개발이 되었으며, 현재 깃허브에 올라가 있다. 이 Burrow는 Kafka와는 독립적인 consumer의 lag을 모니터링하기 위한 애플리케이션이다.

## <ins><b>Burrow의 특징</b></ins>

### **Multi-Kafka cluster를 지원한다.** 

  Kafka를 사용하는 기업에서는 보통 2개 이상의 Kafka cluster를 구성해서 사용하는데, 여러 개의 Kafka 클러스터를 구성하더라도 한 개의 Burrow만으로 모든 카프카 클러스터에 붙은 consumer의 lag을 모두 모니터링 할 수 있다.

<!-- more -->

### **Sliding window를 통한 Consumer의 상태 확인**

  Burrow에서는 sliding window를 통해서 consumer의 상태 정보를 `ERROR`, `WARNING`, `OK`로 나눠서 확인을 한다. 만약 일시적으로 데이터의 양이 많아져서 consumer offset이 증가하면 `WARNING`으로 표기하며, 데이터의 양이 많아졌는데, consumer가 데이터를 가져가지 않으면 `ERROR`로 정의한다.(`consumer의 문제 확인`) 

<br/>

### **HTTP API 제공**

  위에서 정의한 정보들을 HTTP API를 통해서 확인할 수 있다. HTTP API를 통해 받은 response 데이터를 시계열 DB와 같은 곳에 저장하는 애플리케이션을 만들어서 활용할 수도 있다.

이러한 Burrow의 탄생 배경을 위해 `LinkedIn Engineering`블로그를 확인하도록 하자. 

## <ins><b>Burrow 설치</b></ins>

**공식 문서 :** https://github.com/linkedin/Burrow

### **go 설치하기**

burrow는 go 언어로 개발된 애플리케이션이기 때문에 go를 우선적으로 설치해야 한다.

```zsh
$brew install go
```

### **Burrow.exe 파일 생성하기**

go 언어를 설치했다면, burrow GitHub repository를 clone해야한다.

```zsh
$git clone https://github.com/linkedin/Burrow.git
```

이후에 clone 받은 directory로 이동해서 `$go mod tidy` 및 `$go install` 명령을 통해 /User/[사용자명]/go/bin 하위에 `Burrow.exe 파일이 생성`이 된다.

### **설정 파일 수정하기**

Borrow.exe 파일 실행 전에 우선 설정 파일을 수정해야 한다. (`burrow.toml`- clone한 repo의 config 폴더 확인)

```zsh
[general]

[logging]
level="info"

[zookeeper]
servers=[ "localhost:2181"]

[client-profile.local]
client-id="burrow-local"
kafka-version="2.0.0"

[cluster.local]
class-name="kafka"
servers=[ "localhost:9092" ]
client-profile="local"
topic-refresh=120
offset-refresh=30

[consumer.local]
class-name="kafka"
cluster="local"
servers=[ "localhost:9092" ]
client-profile="local"
group-denylist="^(console-consumer-|python-kafka-consumer-|quick-).*$"
group-allowlist=""

[httpserver.default]
address=":8000"

[storage.default]
class-name="inmemory"
workers=20
intervals=15
expire-group=604800
min-distance=1
```

### **Burrow.exe 파일 실행하기**

```zsh
# Burrow.exe 파일의 위치 경로를 지정한 후 수정한 burrow.toml 설정 파일을 실행하기 위해 아래의 명령을 실행한다.
$/Users/[사용자명]/go/bin/Burrow --config-dir=/config
```

Burrow 서비스 확인은 `localhost:8000/burrow/admin` URL을 통해서 확인하도록 한다. 

Burrow EndPoint 정리 문서는 아래의 링크를 확인하도록 하자.

https://github.com/linkedin/Burrow/wiki/HTTP-Endpoint

### **[추가] Docker 컨테이너에 build된 Kafka cluster 모니터링**

만약 Kafka cluster를 docker-compose.yml 파일에서 정의해서 구축했다면, 같은 파일 내에서 burrow에 대한 설정을 할 수 있습니다. 또한 burrow.toml 파일에서 server에 대한 정의도 docker-compose.yml에서 정의한 서비스의 hostname을 기반으로 작성할 수 있습니다.

## <ins><b>Burrow Dashboard 설치</b></ins>

docker에서 Burrow dashboard를 구성하기 위해 필요한 이미지를 다운받습니다.

```zsh
$sudo docker pull joway/burrow-dashboard
```

아래의 method를 통해서 dashboard를 실행합니다.

```zsh
$sudo docker run --network host -e BURROW_BACKEND=http://localhost:8000 -p 80:80 joway/burrow-dashboard:latest
```

## <ins><b>Burrow 대시보드 만들기</b></ins>

Burrow endpoint url을 통해 health check나 offset의 변화에 대해서 모니터링을 할 수는 있지만, json 형태로 출력이 되기 때문에 시계열 형태의 그래프로 값의 변화 추이에 대해서 모니터링이 필요하다.

텔레그래프 -> ES -> Grafana 순으로 데이터를 전달하여 시각화하도록 구성한다. 따라서 텔레그래프와 ES, Grafana의 설치가 필요하다.

```zsh
$brew install telegraf

$cd /usr/local/Cellar/telegraf/1.23.3/bin
$telegraf config > telegraf.conf
```

telegraf.conf 파일에서 localhost 8086 포트에 대한 urls 정의 부분과 database 정의 부분에 대해 주석을 해제시켜준다.
아래의 설정은 telegraf에서 수집된 정보를 influxdb로 내보내기 위한 설정이다.

```
###############################################################################
#                            OUTPUT PLUGINS                                   #
###############################################################################


[[inputs.burrow]]
  servers = ["http://localhost:8000"]
  topics_exclude = [ "__consumer_offsets" ]
  groups_exclude = ["console-*"]
  <!-- prefix에 대한 설정도 주석 제거 -->

[[outputs.elasticsearch]]
  urls = [ "http://localhost:9200" ] 
  timeout = "5s"
  enable_sniffer = false
  health_check_interval = "10s"
  index_name = "burrow-%Y.%m.%d" 
  manage_template = false
  <!-- elasticsearch username/password 설정에 대한 부분도 주석 제거 -->
```
`주석을 제거할 때 반드시 항목 이름에 대한 부분도 같이 제거를 해줘야 한다.`

telegraf.conf 파일 수정이 되었다면, 이제 telegraf를 아래 명령으로 실행시켜주고, ES에서 누적된 burrow 데이터를 확인한다. 
```zsh
$telegraf --config telegraf.conf
```

grafana를 설치한다.

```zsh
$brew install grafana
```

grafana 시작

```zsh
$brew tap homebrew/services
$brew services start grafana
```

이제 `localhost:3000`을 통해 Grafana 서비스 페이지로 접속할 수 있다.(admin/admin)
접속한 후에 새로운 데이터 소스로 elasticsearch를 추가해준다. (`index name 및 기타 정보 입력`)
