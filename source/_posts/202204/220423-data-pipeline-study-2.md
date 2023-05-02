---
title: 220423 데이터 파이프라인 스터디 12일차 (Zeppelin notebook을 사용한 분석)
date: 2022-04-23 21:15:00
tags:
  - BigData
  - Data-Pipeline
  - EMR
categories:
  - Data-Pipeline
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220423_apache_zeppelin.jpeg" alt="Apache Zeppelin">
</div>

이번 포스팅에서는 Zeppelin을 사용해서 데이터를 처리실습을 한 내용을 정리해볼 것이다.

## <ins><b>Zeppelin 메뉴 구성</b></ins>

Zeppelin 페이지를 보면, Job 메뉴를 통해서는 실행되고 있는 좌표들을 모니터링할 수 있다. 그리고 Interpreters 메뉴를 통해서는 다른 Interpreter를 추가할 수도 있으며, Angular, File, Flink, Kotlin, R, Python, Spark 등에 대한 Interpreter를 default로 지원해주고 있음을 확인할 수 있다.

```scala
val csvDF = spark.read
  // 첫 번째 row가 header인 경우,
  .option("header", "true")
  // delimiter가 comma로 구분된 경우,
  .option("delimiter", ",")
  // schema를 구성을 할 때 알지 못하는 데이터의 경우에는 문자열로 정의한다.
  // 전체 데이터를 읽고 숫자 데이터만 있는 경우에는 Integer로, 문자만 있는 경우, String만 정의한다.
  // 단, 파일이 큰 경우에는 전체를 다 읽어야 되기 때문에 이 부분을 고려해서 작업을 진행해야된다.
  .option("inferSchema","true")
  .csv("s3://[s3-name]/[...]/[filename].csv")

csvDF.show // 파일 확인하기

csvDF.printSchema // DataFrame Schema 출력/확인
```

<!-- more -->

최초 실행을 하게 되면 약간 느린데 Spark과 통신 Session을 열어줘야되기 때문이다.

## <ins><b>Spark</b></ins>

SQL, Streaming, ML, Graph 처리를 위한 기본 제공 모듈이 있는 대규모 데이터 처리용 통합 분석 엔진이다. Spark는 Cloud의 Apache Hadoop, Apache Mesos, Kubernetes에서 자체적으로 실행될 수 있으며, 다양한 데이터 소스에 대해서 실행될 수 있다.

`Apache Spark와 Apache Hadoop은 둘 다 분산 시스템`이며, Hadoop은 주로 디스크 사용량이 많고, MapReduce 패러다임을 사용하는 작업에서 사용이 된다. 반면에 Spark는 더 유연하게 사용이 되며, 대체로 더 많은 비용이 드는 In-Memory Architecture라는 차이가 있다.

Spark를 사용하면 구조화 또는 구조화되지 않은 대량의 실시간 또는 아카이브 데이터를 처리 및 분석하는 까다롭고 계산된 집약적인 Task를 간소화시킬 수 있다.
Spark는 많은 언어로 프로그래밍 할 수 있는 옵션을 갖추고 있으며, 이러한 이유로 Spark을 사용해서 데이터 처리작업을 코딩하고 빌드한다.

```scala
// View Table 정의
csvDF.createOrReplaceTempView("data_master")

// View Table 확인하기
%sql // bind중에서 "sql을 사용한다"고 정의!
show tables

// isTemporary column(true)은 세션이 끊기면 사라지는 테이블이라는 의미이다.
```

## <ins><b>Spark SQL 수행</b></ins>

생성한 View Table을 참조해서 일반 SQL query를 수행할 수 있다.

## <ins><b>CSV -> JSON파일로 쓰기</b></ins>

Spark 자체가 여러 노드를 가지고 분산 컴퓨팅을 하기 때문에, 아래와같이 `coalesce(1)`라는 옵션을 주지 않으면 복수 개의 JSON파일이 생성될 것이다.

```scala
csvDF
  .coalesce(1)
  .write
  .mode("Overwrite")
  .format("json")
  .save("s3://[s3 bucket name]/silver/")
```

## <ins><b>JSON -> DataFrame으로 읽기</b></ins>

```scala
val jsonDF = spark.read.json("s3://[s3 bucket name]/silver/")

jsonDF.show # JSON 데이터 DataFrame으로 convert해서 출력하기
jsonDF.printSchema # DataFrame Schema 확인
```

## <ins><b>DataFrame -> Parquet format으로 쓰기</b></ins>

Spark같은 경우에는 파일이 큰 경우에는 동시성이 떨어진다.

```scala
jsonDF.write
      .mode("Overwrite")
      .format("parquet")
      .save("s3://[s3 bucket name]/target/master/parquet/")
```

<div align="center">
  <img src="/images/post_images/220423_parquet_format_file.png" alt="Parquet format file in Spark">
</div>

AWS에서 Parquet로 저장을 하게 되면, snappy라는 형태로해서 저장을 하게 된다. `(snappy도 gzip과 동일한 압축파일 확장자 형태로 보면 된다)` 빅데이터가 생겨나면서 압축률은 줄이면서, 읽는 것을 빠르게 하기 위해서 나온 압축형태이다.

## <ins><b>Parquet format data -> DataFrame으로 읽고, View Table 생성하기</b></ins>

```scala
val parquetDF = spark.read.parquet("s3://[s3 bucket name]/target/master/parquet/")

parquetDF.printSchema

parquetDF.createOrReplaceTempView("data_master_pq")
```

## <ins><b>DataFrame data를 CSV format 파일로 쓰기</b></ins>

```scala
parquetDF.coalesce(1)
         .write
         .made("Overwrite")
         .format("csv")
         .save("s3://[s3 bucket name]/target/master/parquet/csv/")
```
