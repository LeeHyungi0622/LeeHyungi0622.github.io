---
title: 220423 [비공개]데이터 파이프라인 스터디 12일차 (종합복습 및 세미 프로젝트 준비)
date: 2022-04-23 18:53:00
tags:
  - BigData
  - Data-Pipeline
  - EMR
categories:
  - Data-Pipeline
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/220408_data-pipeline.webp" alt="데이터 파이프라인(Data Pipeline)">
</div>

오늘은 데이터파이프라인 1일차부터 11일차까지 학습했던 내용들은 종합적으로 복습하면서 앞으로 개인적으로 진행하게 될 세미 프로젝트는 어떤 방향으로 진행을 하게 될 것인지에 대해서 구상을 해보았다. `(참고로 개인프로젝트를 채워넣을 개인 포트폴리오 웹 사이트를 아주 심플하게 만들었다. 뭔가 좋은 동기부여가 될 것 같아 하나 하나씩 채워가기 위해서 만들었는데, 확실히 좋은 동기부여가 되는 것 같긴하다)`

아직은 총 11차 중에 2일분 밖에 복습을 진행하지 못했다. 하지만, 2일차까지 내용을 천천히 되새기면서 정리를 해보니 정말 주옥같은 내용들이 많았고, 내가 새로운 개념들을 배워가다가 잊고 있었던 부분들도 있었다. `앞으로 남은 복습분량들도 충분히 시간을 투자해서 진행할 계획`이다.

<div align="center">
  <img src="/images/post_images/220423_data_pipeline_note1.jpg" alt="복습노트-1">
</div>

우선 총 11일 동안 진행했던 데이터 파이프라인 학습은 나름 만족스러운 진행상태이다. `총 학습 표의 44.7% 달성`을 했고, 그 사이 사이에 블로깅이며, 다른 공부와 병행을 하고, 개인적으로 복습시간을 갖았던 것을 고려하면, 나름 괜찮은 성과이다.

<div align="center">
  <img src="/images/post_images/220423_data_pipeline_note2.jpg" alt="복습노트-2">
</div>

## <ins><b>세미 프로젝트의 방향</b></ins>

이번 포트폴리오에 넣을 프로젝트의 내용은 각 프로젝트별로 테마 주제를 정하여 진행할 예정이다.

예를들어, `첫 번째,` 아래와같이 서비스 운영적 측면에서 cost를 고려하여 두 개의 Architecture를 구상하여 비교할 수 있다.

### **서비스 운영/cost측면을 고려한 두 Architecture 구상 및 비교**

Cloud service를 기반으로 Data Pipeline을 구축하게 되면, 사용되는 서비스에 따 가격부담이 달라질 수 있다.

`Architecture1)` Amazon API Gateway => Amazon Kinesis Stream => Amazon Kinesis Firehose => S3

Architecture1의 구성으로 데이터 수집 파이프라인을 구성하게 되면, 가장 이상적으로 좋지만, Amazon API Gateway에서 가격 단가가 부담이 될 수 있다.

`Architecture2)` Amazon Pinpoint => Amazon Kinesis stream => Amazon Kinesis Firehose => S3

### **CSP(Cloud Service Provider)가 바뀌어도 유연하게 해당 클라우드 환경에 맞게 파이프라인을 구축할 수 있도록 Architecture 구성해보기**

결국에 실무에서 어느정도 파이프라인이 구축이 되어있는 상태에서 업무를 할 수도 있겠고, 처음부터 새로운 파이프라인을 구축하는 업무를 할 수도 있겠다. 이 모든 경우의 수에서 중요한 것은 어느 환경이 되었건 내가 전체적인 데이터 파이프라인의 구조를 알고 있고, 같이 업무하는 사람과 제대로 소통을 할 수 있는가를 보여줄 수 있어야한다.

`가장 기본이 중요하다.` 포트폴리오를 통해서 대단한 것을 보여주는 것 보다는 얼마나 기본에 충실했고, 이 프로젝트를 통해서 어떤 것들을 겪었고, 얻었는지, 그리고 추가적으로 어떤 것들을 학습해서 내 것으로 만들었는지에 대해서 구체적으로 기술해준다면 잠재적으로 내가 어떤 잠재성을 가진 데이터 엔지니어로 성장할 수 있는지 보여줄 수 있을 것이라고 생각한다.

### **"서울 따릉이 일일 데이터 웨어하우스 및 BI 대시보드 구축"**

### **"블로그 유입자 분석 백엔드 시스템 구축"**

1. 먼저, 데이터를 뽑아낼 수 있는 소스가 필요하다. 직접 어플리케이션을 만들 수도 있고, 오픈 API 등을 이용해 타 서비스의 데이터를 받아올 수 있죠. 이역할을 하는 코드를 짜야합니다.

2. 데이터를 어떤 형태로 만들지 고민 해야 합니다. 데이터가 로그라면, 어떤 식의 로그가 우리에게 최적일지 고민해봐야 합니다. 코드에 비해 데이터의 모형, 즉 원본 데이터 모델은 추후에 바꾸기 쉽지 않습니다. 데이터 모델링에 대해 고민해보게 됩니다.

3. 코드를 돌리고, 데이터를 쌓을 수 있는 컴퓨팅 리소스가 필요합니다. 일반적으로 클라우드를 사용하게 되죠. 클라우드 서비스가 무엇이 있는지 공부하고, 사용법을 조금 익히게 됩니다.

4. 더 구체적으로 들어가, 데이터를 어떻게 뽑아내고 옮기고 쌓을 지 고민해야 합니다. 데이터를 주기적으로 뽑아내야 한다면, 에어플로우와 같은 프레임 워크를 고려하게 됩니다. 또한 어떤 데이터 베이스를 사용할지 결정하고, 데이터를 append 로 쌓을 건지, truncate 하고 insert 할 것인지 결정하게 됩니다.

`일단 이정도의 프로젝트로 3~4개 프로젝트를 진도와 같이 병행하면서 진행할 예정`이다.
지금 이 상황에서 진행할 수 있는 프로젝트 테마/주제로는 아래 두 가지가 있을 것 같다.

### **CSP(Cloud Service Provider)가 바뀌어도 유연하게 해당 클라우드 환경에 맞게 파이프라인을 구축할 수 있도록 Architecture 구성해보기 - (Lambda Architecture)**

### **서비스 운영/cost측면을 고려한 두 Architecture 구상 및 비교**

`Architecture1)` Amazon API Gateway => Amazon Kinesis Stream => Amazon Kinesis Firehose => S3

Architecture1의 구성으로 데이터 수집 파이프라인을 구성하게 되면, 가장 이상적으로 좋지만, Amazon API Gateway에서 가격 단가가 부담이 될 수 있다.

`Architecture2)` Amazon Pinpoint => Amazon Kinesis stream => Amazon Kinesis Firehose => S3

AWS Pinpoint reference : https://docs.aws.amazon.com/ko_kr/pinpoint/latest/developerguide/integrate-events.html

### **HDP2.6.5 환경에서 MapReduce Python script/Pig Latin/Spark로 작성하고 실행해보기**

우선 국내 빅데이터 제공 sample 대용량 데이터를 구한다. 흥미로운 데이터를 구해서
같은 처리를 MapReduce Python script, Pig Latin script, Spark로 작성을 하고 그 차이에 대해서 스스로 분석을 해본다.
Pig와 MapReduce/TEZ와의 각각의 궁합에 대해서 분석적으로 접근하면서 어떠한 차이가 있는지에 대해서도 분석할 수 있는 방법이 있는지 (HORTONWORKS SANDBOX) 찾아보는 것이 좋을 것 같다.

### **일단 주제선정이 확실해지면, 30만원 credit 신청을 하고 받으면 큰 프로젝트로 2개 정도 더 하기**

`(2022/05/07 $300 Credit 신청이 거절되었다)`
