---
title: 220412 Hadoop과 친해지기 일곱 번째 이야기(MAPREDUCE 이해)
date: 2022-04-12 10:39:00
tags:
  - AI
  - Hadoop
  - Hive
  - BigData
  - HDFS
  - MapReduce
categories:
  - Hadoop
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220412_hadoop_mapreduce.png" alt="Hadoop MapReduce">
</div>

이번 포스팅에서는 MapReduce의 정의에 대해서 알아보고, 어떻게 작동을 하는지 그리고 분산처리하는 방법과 간단한 MapReduce 연습문제를 통해 데이터를 분류해보도록 하겠다.

## <ins><b>MapReduce?</b></ins>

이전에 Hadoop의 Core System에 대해서 전반적으로 알아볼때 이미 학습했지만, MapReduce는 HDFS -> YARN -> MapReduce 순으로 구조화되어있다. 즉, MapReduce는 HDFS, YARN과 함께 제공되는 Hadoop의 핵심기술이다.
Hadoop을 이해하기 위해서는 MapReduce를 잘 이해하는 것이 필수이다.

MapReduce는 Hadoop에 내제된 기능으로, 클러스터에 데이터의 처리를 분배하는 역할을 한다.데이터를 파티션으로 나눠서 클러스터 전반에 걸쳐서 병렬 처리되도록 한다.
이러한 일련의 과정에서 관리를 하고 실패하였을때 감당하는 역할을 한다.

### **Data Mapping -> Mapper -> Data Transformation**

데이터를 변형(Transformation)하는 과정으로, 데이터가 한 줄씩 들어오면, Mapper는 해당 데이터를 변형시키게 된다. 들어오는 데이터에서 필요한 정보를 추출하고, 이해할 수 있는 구조로 정리를 해서 해결하고자하는 문제를 해결한다.

데이터를 집계하고자 하는 중요한 데이터를 `키-값의 형태로 연관시켜서 구조화`시킨다. (키:집계하고자하는 항목)
`input line -> (mapper) -> Data 추출 및 구조화해서 한 줄로 출력`

`Mapper의 처리 순서`는 아래와 같다.

INPUT DATA -> Mapper -> K1:V, K2:V, K3:V, K4:V, K5:V ....

### **Data Reducing -> Reducer -> Data Aggregation**

Reducer는 데이터를 집계한다. Mapper를 통해 key:value의 형태로 변형된 데이터가 shuffle & sort 과정을 거쳐서 `Reducer는 얻고자 하는 결과 데이터를 기준으로 key:value에서 value 데이터를 집계`한다.

### **[MapReducer를 통한 문제해결]**

Mapper와 Reducer를 창의적으로 조합하면, 놀랄만큼 다양한 분석문제를 해결할 수 있다.

<!-- more -->

## <ins><b>MapReduce의 실패처리 메커니즘</b></ins>

클러스터를 다룰때에는 항상 어떤 특정 노드가 다운되거나 잠기는 등의 상황을 다룰 줄 알아야 한다.

## <ins><b>MapReduce 예시 - MovieLens Dataset</b></ins>

**문제인지** : 현재 영화 평점관련 데이터가 주어졌다. 이 데이터를 활용해서 어떤 사용자가 몇 편의 영화를 평가를 했는지에 대한 데이터를 추출해야한다.

```
USER ID | MOVIE ID | RATING | TIMESTAMP
196        242       3       88125949
186        302       3       88125959
196        377       1       88123949
244        51        2       88122949
166        346       1       88121949
186        474       4       88125949
186        265       2       88125449
```

실제로 클러스터 네트워크에서 오고 가는 데이터가 적을수록 좋기 때문에 현재 필요한 USER ID와 MOVIE ID를 제외한 나머지 데이터는 필요가 없다. 이는 `최적화를 위해서 매우 중요`하다. 클러스터 네트워크에서 오고 가는 데이터가 적을 수록 좋다!

**[STEP1] Mapper를 통한 필터** : Mapper의 주된 목적은 필요한 정보를 추출하고 적절하게 구조화하는 것이다. Mapper함수가 작업을 끝내게 되면, MapReduce 프레임워크는 아래와같은 key:value 쌍의 목록을 클러스터의 어딘가에 보관하게 된다.

```
196:242 186:302 196:377 244:51 166:346 186:274 186:265
```

**[STEP2] MapReduce의 Shuffle & Sort**
별도의 코딩없이 MapReduce가 섞고 정렬해주는 작업을 해준다. 각 고유 키에게 주어진 값들을 묶어서 집계를 해주고, 더 나아가 키를 정렬해준다.

여기서 중요한것은 각 각의 값에 대한 데이터가 클러스터 내의 각기 다른 컴퓨터의 Mapper에서 왔을 수도 있다. `MapReduce는 클러스터 전체에 걸치 모든 값들을 집계하고 한 자리에 가져와서 필요에 따라 처리하는 강력한 힘을 가지고 있기` 때문이다.

```
166:346 186:302,474,265 196:242,377 244:51
```

**[STEP3] Reducer**
MapReduce의 Shuffle & Sort과정을 거쳐서 새롭게 필터된 값이 나왔는데, 이 값은 각 키마다 Mapping된 값의 목록이다.
Reducer는 이 출력으로 무엇을 할지 특정하는 역할을 한다.
여기서 각 사람마다 평가한 영화의 편 수를 구하는 것이 문제상황이므로, 간단하게 각 value list에 len()이라는 연산자를 사용해서 처리하면 된다.
Reducer는 데이터의 고유 키를 한 번씩만 호출한다.

만약에 key가 전체 클러스터에 걸쳐 저장이 되어있다면, 다시 호출될 수 있으며, 이 말은 `여러 Reducer가 여러 대의 컴퓨터에 걸쳐 동시에 병렬로 작업중이라면, 개별 Reducer는 주어진 양의 키를 처리해야하고, 최종적으로 Reducer는 각각의 키를 한 번만 호출한다는 의미`이다.

```
166:1 186:3 196:2 244:1
```

## <ins><b></b></ins>
