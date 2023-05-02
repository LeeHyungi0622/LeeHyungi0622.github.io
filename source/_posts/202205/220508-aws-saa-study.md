---
title: 220509 AWS Certified Solutions Architect Associate Certificates (SAA-C02) (작성중...)
date: 2022-05-08 22:56:00
tags:
  - Certificates
  - AWS
categories:
  - AWS
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220509_aws_saa_c02_training.jpeg" alt="AWS Architect Certification Training">
</div>

<br/>
<br/>

이번 포스팅에서는 이번에 취득한 AWS Practitioner 자격시험에 이어서 `SAA-C02 시험에 대비`하기 위해 Udemy에서 강의를 수강하면서 직접 AWS에서의 각종 서비스들을 실습해보면서 본격적으로 시험에 대비해보려고 한다.
이번 AWS Practitioner 시험과 AWS 클라우드 환경에서 데이터 파이프라인 구축해보는 실습을 통해서 일부 AWS 서비스들은 다뤄보았지만, 아직 기존에 다뤘던 서비스들도 그렇고, 다른 서비스들에 대해서도 잘 알지 못하는 부분이 많다.

그래서 이번 기회에 자격증 시험준비를 하면서 나중에 실무에서도 프로페셔널하게 직접 클라우드환경을 구축하고 사용할 수 있도록 이론과 실습을 많이 해보기로 결심했다.
아무래도 스스로 환경을 만들어서 익혀야되는 환경이다보니 자격증과 같은 동기부여 요소가 있어야 좀 더 신경써서 현재 내 지식 수준을 체크하면서 진행할 수 있는 것 같다. :)

오늘은 SAA-C02 시험 대비 겸 AWS 실습 첫 날로, 아래의 내용들에 대해서 개괄적으로 살펴보았다.

<!-- more -->

## <ins><b>앞으로 실습하게 될 AWS의 서비스들</b></ins>

<div align="center">
  <img src="/images/post_images/220509_aws_services.png" alt="앞으로 다루게 될 AWS의 서비스들">
</div>

위의 서비스들은 앞으로 약 두 달간 하루에 조금씩 실습을 해나가면서 완성해나갈 AWS의 서비스들 중에 일부이다. 이따가 아래에서 정리를 하겠지만, 오늘 잠깐 Global service 중에 하나인 IAM 서비스에 대해 실습을 했었는데, AWS Practitioner 자격시험을 공부하면서 알던 서비스였지만, 다시 실습을 하면서 다시 머리속에 차곡차곡 정리되는 느낌이었다.
첫 시작이 이렇게 좋으니 앞으로 학습하게 될 서비스들에 대한 기대도 크다. 이전에 데이터 파이프라인 구성에 대한 실습을 하면서 Kinesis stream도 다뤄본 적이 있는데, 뭔가 또 다시 복습하면서 정리하면 또 다른 느낌일 듯 싶다.

## <ins><b>AWS Cloud의 역사</b></ins>

`2002년`에 AWS 내부적으로 Cloud 서비스가 런칭되었다. 이후 `2003년`에 Amazon Infrastructure가 그들의 핵심 강점 중 하나로 여겨져서 시장에 등장하였다.  
`2004년`에 Amazon SQS(Simple Queue Service)는 완전관리형 메시지 대기열 서비스를 출시하였다.
`2006년`에는 SQS, S3 & EC2 서비스를 출시하였고, `2007년`에는 유럽에서 출시를 하였다. 그 이후에는 Dropbox, NETFLIX, Airbnb, NASA 등에서 AWS 서비스를 사용하기 시작했다.

AWS 클라우드 서비스를 사용하면, 복잡하고 확장 및 축소 가능한 어플리케이션을 제작하는 것이 가능하다.

## <ins><b>AWS Global Infrastructure</b></ins>

AWS Infrastructure는 대륙과 대륙이 여러 Region들로 열결이 되어있으며, Region내에는 기본적으로 세 개의 AZ(Availability Zone)s이 존재한다.

## <ins><b>AWS Regions 개념 및 선택시 고려사항</b></ins>

대부분의 AWS 서비스들은 region scope이다. Region은 data center들의 클러스터이며, 전 세계적으로 분포한다.

AWS Region의 선택시에 영향을 주는 요소에는 아래 4가지 요소가 있다.

- `데이터 관리와 합법적 요구에 따른 준수(Compliance)`에 따라 데이터는 명시적 허가없이 region을 절대 벗어나서는 안된다.

- `(거리, 시간상의)고객으로의 가까움, 근접 (Proximity)`지연을 줄일 수 있는 지리적 region을 선정한다.

- `선택한 Region에서 사용가능한 서비스(Available service)인지`에 대한 확인이 필요하다. AWS에서 제공해주는 서비스들이 모든 region에서 제공되는 것이 아니다. 따라서 사용하려는 서비스가 특정 region에서 사용가능한지에 대해 확인해야한다.

- `가격(Pricing)` : region에 따라서 가격은 달라진다. 따라서 내가 운영하는 서비스의 특성을 잘 파악해서 최적의 가격을 맞출 수 있도록 region을 선택해야 한다.

## <ins><b>AWS Availability Zones 개념</b></ins>

## <ins><b>AWS Points of Presence(Edge Locations)</b></ins>

## <ins><b>AWS Global-scoped service와 Region-scoped service</b></ins>

## <ins><b>IAM 서비스</b></ins>

- ### **IAM : Users와 Groups**

- ### **IAM : Permissions**

- ### **IAM 정책 상속**

- ### **IAM 정책 구조**
