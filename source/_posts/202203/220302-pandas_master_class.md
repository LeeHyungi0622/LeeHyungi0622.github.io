---
title: 220302 AI를 위한 Pandas 본격적으로 시작하기 (데이터의 중요성과 Pandas series 속성)
date: 2022-03-02 13:58:00
tags:
  - AI
  - ML
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/220302_pandas.jpg" alt="pandas">
</div>

<br/>

이번 포스팅에서는 인공지능 모델을 개발할 때 필요한 필수 요소인 데이터를 준비하고 정제(전처리 및 결합)하는데 필요한 Pandas에 대한 학습을 하기 전에 데이터의 중요성과 왜 내가 지금 이 공부를 해야되는지에 대한 이유에 대해서 정리를 해보고자 한다.

<h2><b>데이터의 중요성</b></h2>
방대한 데이터를 유의미한 데이터로 가공해서 그 데이터를 통해서 가치있는 insight를 얻어서 매출 증가와 생산력을 증가시키는 기업들이 많다. 우리가 요즘 많이 시청하는 넷플릭스 또한 영화 추천 시스템을 제공하는데 이 또한 고객의 데이터를 활용해서 끊임없이 고객에게 고객이 선호하는 콘텐츠를 추천하고 제공한다.

<h2><b>내가 데이터 준비 및 정제에 대한 공부가 필요한 이유?</b></h2>

인공지능 모델을 개발 할 때 `DATA + MODEL + COMPUTE` 이 세 가지는 필수적인 요소이다. 이 세가지 요소는 인공지능 모델을 개발 할 때 충족해야되는 요소들로, 여기서 `DATA`란 어떻게 정제하고 합치고 시각화하는지가 중요시되며, `MODEL`은 DATA를 이용해서 인공지능을 만들 수 있는 마치 수학식과 같은 개념이라고 할 수 있다.
그리고 `DATA`를 사용해서 `MODEL`을 학습시키기 위해서는 `COMPUTE`의 과정이 필요하다.

<h2><b>앞으로의 공부계획</b></h2>

- 앞으로 데이터의 정제 및 시각화를 어떻게 하는지에 대해 학습 (수치계산에 특화된 `numpy`, 데이터 분석에 필요한 `pandas`, 데이터 시각화에 필요한 `matplot, seaborn` 활용)
- 머신러닝, 데이터 사이언스에서 사용되는 대용량 데이터 세트를 다루는 방법에 대해서 학습

  대용량 데이터의 샘플로는 `KAGGLE`, `UCI`, `ImageNet`에서 제공하는 `dataset`을 활용해 볼 것이다.

  (1)KAGGLE : [https://www.kaggle.com/datasets](https://www.kaggle.com/datasets)
  (2)UCI : [https://archive.ics.uci.edu/ml/index.php](https://archive.ics.uci.edu/ml/index.php)
  (3)ImageNet : [https://www.image-net.org/](https://www.image-net.org/)

<h2><b>Pandas</b></h2>

Pandas는 데이터 조작 및 분석 툴로써, Numpy를 기반으로 한다.
(Numpy는 파이썬을 통해서 데이터 분석을 할 때 기본적으로 사용되는 라이브러리로, C언어로 구현이 되어있는 파이썬 라이브러리이다. 고성능의 수치계산을 위해서 제작이 되었으며, `Numerical Python`의 줄임말이다. Numpy는 백터 및 행렬 연산에 있어서 매우 편리한 기능을 제공한다는 장점을 가지고 있으며, 데이터 분석을 사용 할 때 사용되는 라이브러리인 Pandas와 Matplotlib의 기반으로 사용되기도 한다.)

Pandas는 DataFrame으로 알려져있는 데이터 구조(MS Office excel의 Python 버전이라고 생각하면 된다)를 사용하며, DataFramems은 프로그래머로 하여금 표로 정리된 행과 열과 열 데이터의 저장과 조작을 용이하게 해준다.

`(Series는 DataFrame 데이터 구조에서 단일 칼럼(Single column)으로 간주한다)`

```python
import pandas as pd

my_favorite_movie = ['Dead Poets Society', 'Good Will Hunting', 'The Pursuit of Happyness']

my_series = pd.Series(data = my_favorite_movie)

print(my_series)
#0          Dead Poets Society
#1           Good Will Hunting
#2    The Pursuit of Happyness
#dtype: object

# index는 자동으로 생성이 되게 할 수도 있지만, Series의 두 번째 인수로 index 값을 넘겨주어 사용자 정의 index 값을 정의해줄 수도 있다.

my_favorite_movie = ['Dead Poets Society', 'Good Will Hunting', 'The Pursuit of Happyness']
idx = ['movie#1', 'Movie#2', 'movie#3']

my_series = pd.Series(data = my_favorite_movie, index = idx)

print(my_series)
#movie#1          Dead Poets Society
#Movie#2           Good Will Hunting
#movie#3    The Pursuit of Happyness
#dtype: object

type(my_series)
#pandas.core.series.Series


# 위에서는 pandas.Series의 인수로 리스트(data)를 넘겨서 series type의 데이터를 생성하였는데, dictionary 타입의 데이터도 넘겨서 처리할 수 있다.

stock_dict = {'Nvidia': 10000,
              'Microsoft': 20000,
              'Facebook': 15000,
              'Amazon': 20000,
              'Boeing': 30000}

type(stock_dict)

stock_dict_series = pd.Series(stock_dict)
stock_dict_series

#Nvidia       10000
#Microsoft    20000
#Facebook     15000
#Amazon       20000
#Boeing       30000
#dtype: int64
```

<h2><b>Data source / Type</b></h2>

Data source는 `image, audio/sound, time series/signals` 등 다양한 형태로 존재한다.
이러한 뎅니터 리소스를 얻는 것은 어려운데, 데이터를 얻기 위해서는 데이터 스크래핑 과정을 거쳐서 스크래핑한 데이터를 구조화시키고, 이를 분석해서 마케팅이나 기획에서 활용된다. Data scientist가 실무에서 80% 정도의 시간을 소요하는 부분이 바로 `데이터 준비 및 정제 / 데이터 전처리 및 결합`이다.

데이터의 타입에는 크게 labeled data와 unlabeled data로 나뉘게 되는데, 별도로 supervisor가 시간과 비용을 들여서 labeling 과정을 거쳐 raw data에 labeling을 하는 경우에 `labeled data`가 만들어지게 된다.

<h2><b>Pandas series 속성</b></h2>

```python
my_list = ['NVDA', 'MSFT', 'FB', 'AMZN', 'BA']
my_series = pd.Series(data = my_list)
print(my_series)
#0    NVDA
#1    MSFT
#2      FB
#3    AMZN
#4      BA
#dtype: object

print(my_series.values)
#array(['NVDA', 'MSFT', 'FB', 'AMZN', 'BA'], dtype=object)

print(my_series.index)
#RangeIndex(start=0, stop=5, step=1)

print(my_series.dtype)
#dtype('O') ('O' stands for 'object' datatype)

print(my_series.is_unique)
#True (모든 값이 Unique하기 때문에 True)

print(my_series.shape)
#(5,) (Pandas series가 One dimensional이기 때문에)

print(my_series.size)
#5 (Pandas series의 size를 구하기 위해서는 size method를 사용해야 한다)
```
