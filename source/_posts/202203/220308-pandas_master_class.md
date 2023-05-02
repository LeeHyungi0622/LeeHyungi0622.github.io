---
title: 220308 AI를 위한 Pandas (Pandas series에서 특정 요소 찾기, Pandas series slicing)
date: 2022-03-08 02:30:00
tags:
  - AI
  - ML
  - Pandas
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/220302_pandas.jpg" alt="pandas">
</div>

<br/>

<h2><b>Parantheses() 그리고 Brackets[]</b></h2>

- 속성을 조회할때에는 Parantheses()를 사용하지 않는다.
  `ex)my_series.values, my_series.shape`

- Parantheses()를 사용하는 경우에는 arguments(인자)를 포함하거나 Pandas series를 대체하거나 바꾼다.
  `ex)my_series.tail(), my_series.head(), my_series.drop_duplicates()`

- Brackets[]를 사용하는 경우에는 Pandas series나 DataFrame에서 특정 요소에 접근하기 위함이다.
  `ex)`

<h2><b>Pandas series에서 특정 요소 찾기</b></h2>

```python
sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)
sp500[n-1] #n번째 index 값
```

<h2><b>Pandas series 슬라이싱</b></h2>

```python
import pandas as pd

sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)

# index 0 ~ 4 까지 슬라이싱
# 오른쪽 숫자는 범위에서 포함되지 않는다. N-1
sp500[0:5]

# index 0 ~ 9 까지 슬라이싱
# 가장 처음 index부터 시작이기 때문에 아래와 같이 생략할 수 있다.
sp500[:10]

# index 5 ~ end 슬라이싱
sp500[5:]

# Challenge#13 마지막 세 개 요소를 제외한 Pandas series 출력
sp500[:-3]
```
