---
title: 220428 Hadoop과 친해지기 열세번째 이야기(PySpark 실습) (작성중...)
date: 2022-04-28 13:00:00
tags:
  - AI
  - Hadoop
  - BigData
  - Spark
categories:
  - Hadoop
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220424_apache_spark.png" alt="Apache Spark">
</div>

이번 포스팅에서는 RDD 객체를 DataFrame Dataset으로 convert하고, Spark SQL로 데이터를 전처리한 실습내용에 대해서 정리해보려고 한다.

## <ins><b>RDD => DataFrame</b></ins>

RDD를 DataFrame으로 변경함으로써 Spark SQL을 통해 데이터를 쉽게 가공할 수 있다.
아래는 샘플 코드로, RDD데이터를 `spark.createDataFrame`을 통해서 DataFrame으로 convert하고, convert된 DataFrame 객체를 활용해서 Spark SQL을 활용해서 groupBy, avg, count, join, orderBy, take, 등을 수행한다.

```python
from pyspark.sql import SparkSession
from pyspark.sql import Row
from pysql.sql import functions

# Convert RDD to DataFrame
df = spark.createDataFrame([RDD DATA])

# movieID 기준으로 grouping하고, rating에 대한 평균값 column을 표시한다.
averageRatings = df.groupBy("movieID").avg("rating")

# 각 movieID에 대해서 rating을 count한다.
counts = df.groupBy("movieID").count()

# movieID, avg(rating), count 세 개의 column을 join한다.
averagesAndCounts = counts.join(averageRatings, "movieID")

# top 10 results를 출력한다.
topTen = averagesAndCounts.orderBy("avg(rating)").take(10)

# Stop the session
spark.stop()
```
