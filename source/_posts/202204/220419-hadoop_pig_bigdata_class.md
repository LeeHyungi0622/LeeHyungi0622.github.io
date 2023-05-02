---
title: 220422 Hadoop과 친해지기 열 번째 이야기(Pig로 Hadoop 프로그래밍)
date: 2022-04-22 15:28:00
tags:
  - AI
  - Hadoop
  - Hive
  - BigData
  - HDFS
  - MapReduce
  - HDP
  - Pig
categories:
  - Hadoop
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220419_hadoop_apache_pig.jpeg" alt="Hadoop & Pig">
</div>

이번 포스팅에서는 구조상으로 보면, MapReduce의 상위에 위치해있는 Pig라는 친구를 사용해서 실습을 해보도록 하겠다. 실습은 Ambari를 사용해서 해보도록 한다.

## <ins><b>Ambari에서 관리자 계정 접근 활성화</b></ins>

```zsh
$su root
# switch to root account
$ambari-admin-password-reset
# type the password for admin account
```

## <ins><b>Pig ?</b></ins>

Ambari 웹 브라우저의 화면 우측 상단에 격자 무늬 아이콘을 클릭하면, Pig View 메뉴를 확인할 수 있는데, 이곳에 Pig Scripts 추가하고 실행해볼 수 있다.

여지까지 Hadoop에 대해서 학습하면서 Hadoop의 가장 핵심기술인 MapReduce에 대해서 중점적으로 배웠다. MapReduce는 Hadoop의 시작을 함께 한 기술이긴 하지만 오래된 데이터 처리 기술이고, 어렵다. **이러한 어려운 데이터 처리 기술을 Pig는 좀 더 쉽게 할 수 있도록 도와준다.**

<!-- more -->

Pig는 정확히 말하면, Pig Latin이라는 스크립팅 언어이고, Hadoop과 MapReduce 위에 구축되어있다.(신속한 반복처리(iteration)가 가능한 편리한 개발환경에서는 다양한 실험을 통해 데이터를 창의적으로 사용할 수 있다) Pig를 사용하면 Mapper와 Reducer를 별도로 작성하지 않아도 Pig Latin script로 MapReduce 작업을 대체할 수 있다. 정확히 말하면 Pig script가 MapReduce로 번역이 되지만, 성능상에 문제는 되지 않는다.
`MapReduce의 가장 큰 문제는 개발 사이클 타임`인데, MapReduce 프로그램을 개발해서 실행하고, 원하는 작업을 수행하기까지 오랜시간이 걸리기 때문에 이 Pig라는 친구가 등장하게 된 것이다.
Pig Latin script는 SQL과 비슷하고, 절차형 언어이며, 아주 간단한 스크립트 양식을 사용해서 단계별로 데이터 간에 여러 관계를 설정한다. (데이터 선택/필터/변형)

Pig는 MapReduce 위에 구축되어있다고 했지만, `정확히 말하면 MapReduce와 TEZ 사이`에 있다. 즉, MapReduce외에도 TEZ를 기반으로 사용하기도 한다. TEZ는 MapReduce보다도 훨씬 빠르기 때문에 Pig를 사용함으로써 얻는 장점이 많다.
Pig Latin은 UDF(User Define Function)을 지원하기 때문에 사용자 지정함수를 사용해서 스크립트를 작성할 수 있다. 또한 Pig는 Hadoop 클러스터나 종속된 기술읕 사용하지 않고도 독립적으로 간단한 작업을 수행할 수 있다.

TEZ는 기본적으로 작업을 훨씬 효율적으로 체계화하는 방식(방향성 비사이클 그래프)을 가지고 있으며, MapReduce의 매퍼와 리듀서가 있고, 처리된 데이터를 또 다른 매퍼와 리듀서가 받아서 데이터를 출력하면 다음 매퍼와 리듀서가 받는 등의 순차적인 관계가 있는 것과 상반되는 특성을 가지고 있다.
TEZ는 상호 의존성을 파악하고, 의도한 바를 이루기 위한 가장 효율적인 경로를 계산한다. `(MapReduce보다 10배 빠른 속도)`

## <ins><b>Pig 사용법</b></ins>

### **(1) Master node에서 'pig'입력, Grunt prompt 후 스크립트 입력**

### **(2) 스크립트를 파일에 저장, commandline에서 Pig 명령으로 실행**

### **(3) 브라우저 상 편집기에서 작성/실행/저장 작업**

## <ins><b>예제문제를 통해서 Pig Latin script 실습</b></ins>

### **[실습] 평균 평점 4점을 초과하는 영화를 출시일 기준으로 정렬하기**

```python
# HDFS Cluster location (path)
ratings = LOAD '/user/maria_dev/ml-100k/u.data' AS (userID:int, movieID:int, rating:int, ratingTime:int);

metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdbLink:chararray);

# DUMP command는 디버깅 등에 굉장히 유용하고, 내가 생각한대로 데이터가 추출되는지 확인할 수 있다.
DUMP metadata;
```

### **다른 관계 데이터에서 또 다른 관계 생성하기; FOREACH / GENERATE**

```python
metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|')AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdbLink:chararray);

nameLookup = FOREACH metadata GENERATE movieID, movieTitle, ToUnixTime(ToDate(releaseDate, 'dd-MMM-yyyy')) AS releaseTime;
```

### **[Reduce in Pig] Group By**

이전에 각 평점 점수에 따른 영화의 분포를 알아보기 위해서 count를 했었는데, 평점 점수가 기준이 되어 Grouping되었었다.
Pig에서 Group By는 MapReduce에서 Reduce에서와 같은 처리를 해준다.

```python
ratingsByMovie = GROUP ratings BY movieID;
DUMP ratingsByMovie;

#(1,{(.....),(.....),(.....),.....})
#(2,{(.....),(.....),(.....),.....})

avgRatings = FOREACH ratingsByMovie GENERATE group AS movieID, AVG(ratings.rating) AS avgRating;

DUMP avgRatings;

DESCRIBE ratings;
DESCRIBE ratingsByMovie;
DESCRIBE avgRatings;

# 관계성 스키마 출력
# ratings: {....}
# ratingsByMovie: {....}
# avgRatings:{....}
```

### **FILTER**

```python
fiveStarMovies = FILTER avgRatings BY avgRating > 4.0;
```

### **JOIN**

```python
DESCRIBE fiveStarMovies;
DESCRIBE nameLookup;

# movieID를 기준으로 fiveStarMovies와 nameLookup 테이블을 JOIN한다.
fiveStarsWithData = JOIN fiveStarMovies BY movieID, nameLookup BY movieID;

DESCRIBE fiveStarsWithData;
DUMP fiveStarsWithData;
```

### **ORDER BY**

```python
oldestFiveStarMovies = ORDER fiveStarsWithData BY nameLookup::releaseTime;

DUMP oldestFiveStarMovies;
```
