---
title: 220307 AI를 위한 Pandas (Pandas series의 method, Pandas를 활용한 csv파일 읽기, Pandas 빌트인 함수, Pandas series 정렬(index, values 기준), Pandas series 연산)
date: 2022-03-07 15:33:00
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

이번 포스팅에서는 Pandas series의 method에 대한 내용을 정리해보고자 한다.

<h2><b>Pandas series의 method</b></h2>

```python
import pandas as pd

#Series.sum() : 합계
my_series = pd.Series(data = [100, 200, 500, 1000, 5000])

#Series.product(): 곱
my_series.product()

#Series.mean(): 평균
my_series.mean()

#Series.head(n): 위에서부터 n개 출력
my_series.head(2) #최 상단에서 2개까지 출력

#Series.tail(n): 아래에서부터 n개 출력
my_series.tail(2) #최 하단에서 2개까지 출력

#Series.memory_usage(): Pandas series가 사용하고 있는 용량 확인(bytes)
my_series.memory_usage() #example:168(bytes)
```

<h2><b>Pandas 활용해서 CSV 파일 읽기</b></h2>

```python
#Pandas의 read_csv method를 활용해서 csv파일을 읽기 위해서는 squeeze=True option은 필수이다.
#squeeze=True : One dimentional table data를 읽기 위한 옵션
sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)
type(sp500) #pandas.core.series.Series



#squeeze=False : Multi dimentional(다차원) table data를 읽기 위한 옵션이다.
sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=False)
#squeeze=False or default
type(sp500) #pandas.core.frame.DataFrame

```

위의 각 결과를 보면 알 수 있듯이, Pandas series의 경우에는 formatting 없이 단순하게 (1차원의)하나의 열로 테이블화 된 형태로 데이터가 출력이 되고, DataFrame의 경우에는 각 셀에 스타일이 적용되어 테이블이 formatting되어 데이터가 출력된다.
`데이터 프레임(DataFrame)이란 1개의 열이 아닌, 여러 행과 열을 가진 다차원 데이터 테이블`이다.

<h2><b>Pandas 빌트인 함수</b></h2>

Pandas는 이미 존재하는 python의 수 많은 함수들과 상호동작한다.

```python
sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)

#Pandas의 Data type을 얻을 때
type(sp500)

#Pandas series의 data길이를 얻을 때
len(sp500)

#Pandas series에서 최대값을 얻을 때
max(sp500)

#Pandas series에서 최소값을 얻을 때
min(sp500)

# 주어진 Pandas series type의 데이터(리스트)에서 모든 양의 값은 음의 값으로 Python의 built-in function을 활용하여 구하시오.
# 주어진 Pandas series의 값에서 중복되지 않은 Unique한 값을 Python의 built-in function을 활용하여 구하시오.
my_series = pd.Series(data = [-10, 100, -30, 50, 100])

result = [ -i for i in my_series ]

set(result)

#solution
#모든 값을 양의 값으로 치환하는 문제였다.
abs(my_series)

set(my_series)
```

<h2><b>Pandas series sorting(ascending order)</b></h2>

```python
import pandas as pd

sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)
print(sp500)

#sort the values in the dataframes
#최솟값 ~ 최대값 순으로 정렬
sp500.sort_values()

#sort_values()의 결과값은 별도로 변수에 저장해줘야한다. (메모리 정렬)
sp500.sort_values(inplace = True)


#index 순으로 정렬
sp500.sort_index(inplace = True)

#challenge#8 (descending sorting)
sp500.sort_values(ascending=False, inplace=True)
```

<h2><b>Pandas Series Math operation</b></h2>

```python
sp500 = pd.read_csv('S&P500_Prices.csv', squeeze=True)

#Sum method on Pandas series
sp500.sum()

#Count method on Pandas series
sp500.count()

#Obtain the maximum value
sp500.max()

#Obtain the minimum value
sp500.min()

#새로운 dataset을 다룰때 describe method를 사용한다.
# count, mean, std(표준편차), min, 25%, 50%, 75%, max 정보의 summary 정보를 알 수 있다.
sp500.describe()

#Challenge#9 Obtain the average price of the S&P500 using two different method
sp500.mean()
sp500.sum()/sp500.count()


#주어진 요소가 값으로 존재하는지 체크
1295.500000 in sp500.values

#주어진 요소가 인덱스로 존재하는지 체크
1295.500000 in sp500.index

#주어진 요소가 값이 아닌 인덱스로 존재하는지 체크 (in 은 기본적으로 index를 기준으로 찾는다)
1295.500000 in sp500

#Challenge#10
3349 in sp500.values

#sp500의 values를 반올림한다.
rounded_sp500 = round(sp500)

#반올림된 값 리스트에서 3349가 값이 존재하는지 확인한다.
3349 in rounded_sp500.values
```
