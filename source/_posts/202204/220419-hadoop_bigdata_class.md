---
title: 220419 Hadoop과 친해지기 열 번째 이야기(MapReduce Challenge)
date: 2022-04-19 09:59:00
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

이번 포스팅에서는 MapReduce 실습 도전과제에 대해 직접 해본 내용을 정리해보려고 한다.

이전 포스팅에서는 영화 정보 데이터가 주어졌을때, 각 평점을 기준으로 몇 편의 영화가 분포되어있는지 확인하는 MapReduce 코드를 작성해보았다.

이번 Self-Challenge 과제는 총 2개이며,
**첫 번째 챌린지 과제**는 영화별 인기순위를 분류하기 위한 MapReduce 작성하는 것이다.
**두 번째 챌린지 과제**는 평가 횟수를 기준으로 영화를 정렬하는 MapReduce를 작성하는 것이다.

## <ins><b>영화별 인기순위 분류하기 위한 MapReduce 작성</b></ins>

영화 인기순위는 영화 평가 횟수를 기준으로 보았을 때, 시청 횟수의 대용물로 평가가 될 수 있기 때문에 movieID를 기준으로 mapper를 작성해주고, reducer에서 해당 영화 평가 횟수를 종합해주면 된다는 결론이 나온다.

```python
from mrjob.job import MRJob
from mrjob.step import MRStep

class NumOfEvaluateBreakdown(MRJob):
  def steps(self):
    return [
      MRStep(mapper=self.mapper_get_num_of_evaluate,
            reducer=self.reducer_count_num_of_evaluate)
    ]
  def mapper_get_num_of_evaluate(self, _, line):
    (userID, movieID, rating, timestamp) = line.split('\t')
    yield movieID, 1

  def reducer_count_num_of_evaluate(self, key, values):
    yield key, sum(values)

  if __name__ == '__main__':
    NumOfEvaluateBreakdown.run()
```

<!-- more -->

<div align="center">
  <img src="/images/post_images/220419_challenge_1.png" alt="MapReduce challenge1 결과화면">
</div>

여기서 확인해야 될 중요한 부분은 Streaming은 모든 input과 output에 대해서 문자열로 다룬다는 것이다. 따라서 위의 출력 값에서 볼 수 있듯이, movieID를 numeric값이 아닌 string 값으로 정렬이 됨을 볼 수 있다.
따라서 다음 챌린지에서 평가 횟수를 기준으로 정렬할 때에는 zfill() method를 사용해서 일정 자리수로 맞춰주고, 나머지 자리수의 경우에는 0으로 padding값을 넣어 맞춰줘야 문자열로도 numeric으로 정렬한 것과 같은 동일한 결과값을 얻을 수 있다.

## <ins><b>평가 횟수를 기준으로 영화를 정렬하는 MapReduce를 작성</b></ins>

```python
from mrjob.job import MRJob
from mrjob.step import MRStep

class NumOfEvaluateBreakdown(MRJob):
  def steps(self):
    return [
      MRStep(mapper=self.mapper_get_num_of_evaluate,
            reducer=self.reducer_count_num_of_evaluate),
      # Shuffle and Sort 작업이 한 번 더 일어나며, sort_by
      MRStep(
            reducer=self.sort_by_rating_count
      )
    ]
  def mapper_get_num_of_evaluate(self, _, line):
    (userID, movieID, rating, timestamp) = line.split('\t')
    yield movieID, 1

  # 평가된 횟수를 총 5자리로 숫자로 만들어서 그 값을 기준으로 movieID를 정렬한다.
  def reducer_count_num_of_evaluate(self, key, values):
    yield str(sum(values)).zfill(5), key

  # 현재 복수 개의 동일한 movieID가 movies 값 배열에 들어가 있기 때문에
  # movies 배열을 순회하면서, 매핑되는 count 값을 출력해준다.
  def sort_by_rating_count(self, count, movies):
    for movie in movies:
      yield movie, count

  if __name__ == '__main__':
    NumOfEvaluateBreakdown.run()
```

### **[Multi-stage jobs]**

- 복수 개의 map/reduce 작업을 chaining 하는 방법은 아래와 같이 MRStep으로 연속해서 ,(comma)로 구분해서 이어주면 된다.

```python
def steps(self):
  return [
    MRStep(mapper=self.mapper_get_ratings,
           reducer=self.reducer_count_ratings),
    # MapReduce에 의해 Shuffle and Sort가 이뤄진다.
    MRStep(reducer=self.reducer_sorted_output)
  ]
```

<div align="center">
  <img src="/images/post_images/220419_challenge_2.png" alt="MapReduce challenge2 결과화면">
</div>
