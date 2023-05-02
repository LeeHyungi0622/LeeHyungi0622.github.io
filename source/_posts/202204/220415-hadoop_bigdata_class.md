---
title: 220415 Hadoop과 친해지기 아홉 번째 이야기(HDP2.6.5에서 Python으로 작성한 MapReduce 코드 실행하기 (Local/Hadoop))
date: 2022-04-15 13:46:00
tags:
  - AI
  - Hadoop
  - Hive
  - BigData
  - HDFS
  - MapReduce
  - HDP
categories:
  - Hadoop
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220412_hadoop_mapreduce.png" alt="Hadoop MapReduce">
</div>

이번 포스팅에서는 이전에 작성했던 Python으로 작성한 MapReduce 코드를 HDP 2.6.5 환경에서 구동시켜볼 것이다. Hadoop을 사용하지 않고, local에 copy된 dataset을 가지고 로컬에서 실행해보고, Hadoop을 기반으로 실행해보는 두 가지 방법으로 실습을 해볼 것이다.

## <ins><b>HDP2.6.5에서 MapReduce 실습환경 구축</b></ins>

### **[STEP1] 가장 먼저 VirtualBox에 올린 HDP2.6.5 OS Image를 구동시킨다.**

### **[STEP2] Putty를 사용해서 가상 OS환경에 접속한다.**

**[Putty host/port configuration]**

```
host: maria_dev@127.0.0.1
port: 2222
```

### **[STEP3] PIP 설치를 위한 SETUP**

```zsh
# root 계정으로 switch

$su root # root account initial password : hadoop

$yum-config-manager --save --setopt=HDP-SOLR-2.6-100.skip_if_unavailable=true # HDP Solar라는 저장소를 무시하고 설치하기 위한 configuration

$yum install https://repo.ius.io/ius-release-el7.rpm https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm # IUS package를 수동으로 설치

$yum install python-pip # python-pip 설치

$pip install pathlib # pathlib 설치 *설치를 안하면 MRJob이 정상적으로 설치가 안됨

$pip install mrjob==0.7.4 #설치한 pip를 이용해서 MRJob 설치
```

<!-- more -->

<div align="center">
  <img src="/images/post_images/220415_mrjob_installation_on_hdp.png" alt="HDP에서 MRJob설치하기">
</div>

### **[STEP4] 실습에 필요한 DATASET 및 Python source code 준비**

```zsh
# nano editor 설치 (or vi editor 사용해도 됨)
$yum install nano

# 실습에 필요한 MovieLense dataset file 설치
$wget http://media.sundog-soft.com/hadoop/ml-100k/u.data

# Python MapReduce source code download
$wget http://media.sundog-soft.com/hadoop/RatingsBreakdown.py
```

### **[STEP5] MRJob프레임워크를 사용해서 MapReduce 실행하기**

MRJob 프레임워크를 사용해서 MapReduce를 실행하는 방법에는 총 두 가지가 있다.
`첫 번째는` Hadoop을 전혀 사용하지 않고 로컬에서만 실행하는 방법이다. MRJob 프레임워크를 사용해서 MapReduce를 시뮬레이션한다. 개인 PC에서 여러가지를 테스트하기에 유용한 방법이다.
Hadoop 클러스터에서 개발하는 대신에 텍스트 에디터(nano, vi 등)를 사용해서 데스크톱에서 개발을 할 수 있다. 아래는 로컬 환경에서 MRJob 프레임워크를 사용해서 MapReduce를 실행하는 방법이다.

```zsh
# 스크림트 파일과 입력 데이터 파일을 입력
$python RatingsBreakdown.py u.data
```

`(개인 PC에서 실행하는 경우에는 방대한 데이터 세트 전체를 사용하지 않고, 때때로 테스팅이나 개발 목적으로 데이터의 부분집합을 만드는 것도 좋은 방법이다.)`

`두 번째는` Hadoop에서 MapReduce를 실행하는 방법이다.

```zsh
$python MostPopularMovie.py -r hadoop --hadoop-streaming-jar
/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar
u.data


#(1) -r hadoop : 'MRJob' 프레임워크에게 이 스크립트를 실제 Hadoop 클러스터에서 실행할 것이라고 알려준다. (Resource Manager와 Application Master를 사용해서 작업을 실제로 분배한다)

#(2) '--hadoop-streaming-jar'은 Hortonworks의 특유 문구인데, 'MRJob'은 Hadoop Sandbox의 Hadoop Streaming 위치를 모르기 때문에 수동으로 어디에 있는지 알려주는 역할을 한다. Amazon EC2나 Elastic MapReduce 서비스 플랫폼에는 이러한 별도의 옵션이 필요 없다. 그 이유는 Hadoop streaming을 어디서 찾아야되는지 알고 있기 때문이다.

#(3) 입력 데이터의 마지막 매개 변수가 있는데, 이것은 HDFS에 자동으로 복사될 로컬 파일이 될 수 있고, 대부분의 실제 상황에서는 클라이언트 노드에는 존제하지 않는 거대한 대용량 데이터를 다루기 때문에 'hdfs://' 형식으로 HDFS 파일 시스템 경로를 지정해준다.
```

위의 commandline에서는 local file을 HDFS에 복사를 하고, 복사한 파일 데이터를 여러 노드에 걸쳐서 분배가 된다. 하지만 현재 실습하고 있는 dataset의 경우에는 아주 작은 작업이고, Virtual Machine도 한 개만 존재하기 때문에 HDFS에 복사해서 다른 여러 노드들로 분배하는 과정을 진행하지 않을 것이다.
이를 Uber Job이라고 불리는데 모든 걸 하나의 인스턴스에서 작동하는 작업을 말하는 용어이다. 데이터가 작기 때문에 가능한 이야기이다.

### **[Uber job]**

`Uber job이란` Mapper나 Reducer container를 생성하기 위해 RM(Resource Manager)와 서로 소통하기 보다는 MapReduce ApplicationMaster내에서 작업들이 실행되는 것을 말한다.
앞에서 말했듯이 처리되는 데이터가 작은 경우에는 별도로 Map/Reduce 처리를 위한 container를 생성해서 처리할 필요가 없기 때문에 AM(ApplicationMaster) 자체 내에서 Map과 Reduce 작업을 전반적으로 처리한다. 이러한 것을 Uber job이라고 한다.

<div align="center">
  <img src="/images/post_images/220415_ratingbreak_job_on_local.png" alt="MapReduce Python code local에서 실행하기">
</div>

<div align="center">
  <img src="/images/post_images/220415_ratingbreak_job_on_hadoop.png" alt="MapReduce Python code Hadoop에서 실행하기">
</div>

<div align="center">
  <img src="/images/post_images/220415_ratingbreak_job_on_hadoop-1.png" alt="MapReduce Python code Hadoop에서 실행하기">
</div>

<div align="center">
  <img src="/images/post_images/220415_ratingbreak_job_on_hadoop-2.png" alt="MapReduce Python code Hadoop에서 실행하기">
</div>

<div align="center">
  <img src="/images/post_images/220415_ratingbreak_job_on_hadoop-3.png" alt="MapReduce Python code Hadoop에서 실행하기">
</div>
