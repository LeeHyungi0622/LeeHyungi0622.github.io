---
title: 221008 Apache NiFi 스터디 (작성중...)
date: 2022-10-08 14:27:00
tags:
  - NiFi
categories:
  - NiFi
---

<div align="center">
  <img src="/images/post_images/221008_nifi_logo.jpeg" alt="nifi">
</div>

<br/>
<br/>

이번 포스팅에서는 최근에 새롭게 접하게 된 ETL 툴인 Apache NiFi에 대해서 정리해보려고 한다.
 
## <ins><b>NiFi의 기술적 배경</b></ins>
NiFi는 NAS라는 미국의 국가 안보국에서 Apache에 기부한 Dataflow 엔진으로, `ETL툴의 일종`이다.
NiFi는 과거에 NSA에 의해 개발되었다가 2014년 기술 전송 프로그램의 일부로, 오픈소스화된 나이아가라파일즈(NiagaraFiles)에 기반을 두고 있다.

NiFi는 분산환경에서 대량의 데이터를 수집, 처리한다. => FBP개념을 구현한 오픈소스 프로젝트
여기서 FBP란 사전에 Data Flow를 정의하고, 이를 지속적으로 유지하면서 데이터를 교환하는 프로그래밍 패러다임이다. 
(`마치 Apache Airflow에서 DAG를 작성해서 전체적인 Task flow를 구성한 뒤에 반복적인 데이터 처리를 자동화 시키는 것과 같은 맥락이다`)

## <ins><b>NiFi의 필요성</b></ins>
그래서 왜 NiFi가 필요할까? 앞서 이미 정의했듯이 NiFi는 ETL툴의 일종으로, 클러스터로 구성이 되어있기 때문에 대량의 데이터를 분산시켜서 처리할 수 있다.
NiFi는 A 시스템에서 B 시스템으로 데이터를 이관하는 것을 손쉽게 할 수 있도록 도와주는 서비스 툴로, 데이터를 이관하는 중간에 데이터를 변형(정제)할 수 있다. 그 외에도 관리 및 모니터링이 가능하다.

## <ins><b>NiFi의 구성</b></ins>
NiFi는 FlowFile, Processor, Connection, 이 세 가지로 구성이 되어있다.

- <b>[FlowFile]</b>

  FlowFile은 NiFi가 인식하는 데이터 단위로, 속성과 내용이 Key/Value 혀태로 구성이 되어있다. Processor마다 이동시 복사본이 생성되어, 추적이 용이하다.

  `ref.` query를 작성할때 [table명] 대신에 FLOWFILE을 작성하게 되면, 파이프라인 상에서 흘러가는 파일 정보를 읽을 수 있다.

- <b>[Processor]</b>

  FlowFile을 수집, 변형 및 저장하는 기능이 있으며, 자주 사용되는 프로세서로는 `http, kafka, db, ftp`와 관련된 프로세서, 속성을 변경하는 `updateattribute`, 데이터를 합치는 `mergecontent`, 데이터를 분할하는 `split`, 데이터 타입을 변경하는 `convert`등이 있다. 


- <b>[Connection]</b>

  각 Processor별로 연결해서 FLOWFILE을 전달하는 역할을 담당하고, FlowFile의 우선순위, 만료, 부하조절 기능도 제한하고 있다. 

## <ins><b>NiFi 아키텍쳐</b></ins>

- <b>[Web Server]</b>
  
  발생하는 이벤트를 모니터링, 소프트웨어를 시각적으로 제어하기 위해서 사용된다. (`HTTP기반 구성요소`)

- <b>[Flow controller]</b>

  NiFi 동작의 뇌 역할을 담당한다. `NiFi 확장기능의 실행을 통제`하고, 이를 위한 `자원 할당을 스케줄링`한다.

- <b>[Extensions]</b>

  NiFi가 다양한 종류의 시스템과 통신할 수 있게 하는 다양한 플러그인이다.

- <b>[FlowFile Repository]</b>

  NiFi가 현재 실행중인 FlowFile의 상태를 추적하고, 정비하기 위해 사용된다.

- <b>[Content Repository]</b>

  전송 대상의 데이터가 관리된다.