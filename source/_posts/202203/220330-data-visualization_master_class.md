---
title: 220330 데이터 시각화(Matplotlib) TIL
date: 2022-03-30 13:44:00
tags:
  - AI
  - Matplotlib
categories:
  - Data-Visualization
---

<div align="center">
  <img src="/images/post_images/220330_data-visualization.png" alt="데이터 시각화">
</div>

이번 포스팅에서는 Matplotlib에 대해서 학습한 내용에 대해서 작성해보려고 한다.

<h2><b>Matplotlib</b></h2>

우선 matplotlib를 어떻게 가져와서 사용하는지에 대해서 알아보자.
우선 가장 기본적인 형태의 그래프를 Matplotlib 라이브러리를 사용해서 그려보려고 한다.

```python
import matplotlib.pyplot as plt
import pandas as pd

investment_df = pd.read_csv('crypto_daily_prices.csv')
investment_df

# matplotlib을 사용해서 Pandas DataFrame의 data를 시각화하기
investment_df.plot(x = 'Date', y = 'BTC-USD Price', label = 'Bitcoin Price', linewidth = 3, figsize = (14, 6))
plt.ylabel('Price [$]')
plt.xlabel('Date')
plt.title('가상화폐 시각화 실습 (matplotlib)')

# 기본 범례 위치 변경해보기
plt.legend(loc = 'upper right')
# 그리드 그려주기
plt.grid()
```

<div align="center">
  <img src="/images/post_images/220331_bitcoin_dataframe_list.png" alt="가상화폐 데이터프레임 리스트">
</div>

<div align="center">
  <img src="/images/post_images/220331_matplotlib_graph_coin.png" alt="가상화폐 matplotlib 그래프">
</div>

<!-- more -->

### **MINI CHALLENGE #1** Ethereum 가격 정보에 해당하는 부분만 matplotlib를 사용해서 그래프로 표현해보기

새로운 조건 : 그래프의 선 색상을 파란색(default)에서 빨간색으로 변경해보고, 범례의 위치는 최 우측 상단에 배치하며, 선의 두께는 4로 한다.

```python
import matplotlib.pyplot as plt
import pandas as pd

investment_df = pd.read_csv('crypto_daily_prices.csv')
investment_df

investment_df.plot(x = 'Date', y = 'ETH-USD Price', label = 'ETH Priced', linewidth = 4, color = 'r', figsize = (14, 6))

plt.ylabel('Price [$]')
plt.xlabel('Date')
plt.title('ETH-USD Price Matplotlib graph')
plt.legend(loc = 'upper right')
```

<div align="center">
  <img src="/images/post_images/220331_eth_price_matplot.png" alt="가상화폐 matplotlib 그래프">
</div>

<h2><b>Yahoo finance 데이터 활용하기</b></h2>

### **야후 파이낸스 라이브러리를 설치**

```console
!pip install yfinance
import yfinance as yf
```

```python
# 가져오고자하는 정보에 대한 항목 리스트를 작성한다.
investments_list = ['BTC-USD']

# 시작과 끝 날짜 변수를 선언한다.
start = datetime.datetime(2010, 1, 1)
end = datetime.datetime(2021, 5, 16)

# 위에서 선언한 변수를 인수로 넣어 야후 파이넨스로부터 데이터를 다운받는다.
df = yf.download(investments_list, start = start, end = end)
df
```

날짜별 시가와 고가, 저가, 종가, 조정된 종가 등의 정보들을 확인 할 수 있다.
마지막 Volume은 일일 거래 양이다.

<div align="center">
  <img src="/images/post_images/220331_yahoo_finance_data.png" alt="야후 파이낸스 데이터 필터">
</div>

### **MINI CHALLENGE #2** Ethereum data를 야후 파이낸스에서 다운받아서 출력해보고, BTC, ETH 그리고 LTC 코인에 대한 정보도 야후 파이낸스에서 다운받아서 출력해보자.

```python
# 출력 1) Ethereum data를 야후 파이낸스에서 다운받아서 출력
investments_list = ['BTC-USD']
start = datetime.datetime(2010, 1, 1)
end = datetime.datetime(2022, 3, 30)

df = yf.download(investments_list, start = start, end = end)
df

# 출력 2) BTC, ETH 그리고 LTC 코인에 대한 정보도 야후 파이낸스에서 다운받아서 출력
investments_list = ['BTC-USD','ETH-USD','LTC-USD']
start = datetime.datetime(2010, 1, 1)
end = datetime.datetime(2022, 3, 30)

df = yf.download(investments_list, start = start, end = end)
df
```

<h2><b>다중 플롯(Multiple plots) 구현하기</b></h2>

처음 matplotlib로 간단한 그래프를 그렸을 때, df.plot()의 인수로 x축 데이터 명, y축 데이터 명, linewidth, figsize를 정의해줬다. 여기서 다중 플롯을 구현해주기 위해서는 인수 중에서 **y를 문자열인 단일 인수가 아닌 배열의 형태로 넘겨주면 된다.**

```python
investment_df.plot(x = 'Date', y = ['BTC-USD Price', 'ETH-USD Price'], linewidth = 3, figsize = (14, 6))
plt.ylabel('Price')
plt.title('Crypto Prices')
plt.grid()
```

<div align="center">
  <img src="/images/post_images/220331_multiple_plots.png" alt="다중플롯(Multiple plots) 그래프">
</div>

<h2><b>여러 개의 그래프를 하나의 그림에 표현하기 (subplot() 함수 활용)</b></h2>

```python
investment_df.plot(x = 'Date', title = 'Crypto Prices', subplots = True, grid = True, figsize = (15, 25))
```

<div align="center">
  <img src="/images/post_images/220331_subplot.png" alt="복수의 하위 Matplotlib 그래프 그리기">
</div>

`그럼 subplots 인수의 값을 False로 하면 어떻게 될까?`
실행하기 전에 내 예상은 일단 y축 데이터에 대한 데이터를 특정하지 않았기 때문에 모든 코인에 대해서 한 그래프에 표시가 되지 않을까? 마치 다중 플롯(Multiple plots)처럼..
실행해보니, figsize의 y축 높이를 길게 잡아서 그런지 좀 길죽하게 나오긴 했어도 예상했던 것처럼 다중 플롯의 형태로 세 개의 코인 정보가 하나의 그래프에 모두 표시됨을 확인할 수 있었다.
`(y = [...all args...]) == (~y and subplots == False)`

<h2><b>산점도(SCATTERPLOT)</b></h2>

산점도 구상하기 연습의 일환으로 비트코인과 이더리움 사이의 산점도를 구상해보자.

```python
# 비트코인과 이더리움의 날짜별 수익
# 비트코인과 이더리움의 수익에 대한 정보가 점으로 표시
# 각 점들은 다양한 날짜나 행들을 가르킨다.
daily_return_df.plot.scatter('BTC', 'ETH', grid = True, figsize = (12, 7))
```

<div align="center">
  <img src="/images/post_images/220331_scatterplot.png" alt="BTC vs ETH Scatterplot 그래프">
</div>

<h2><b>PI CHART</b></h2>
각기 다른 비율로 투자한 금액을 원 모양으로 각 각 다르게 할당해서 표현

```python
# 각 각의 가상화폐를 각기 다른 비율로 투자
my_dict = {'allocation %': [20, 55, 5, 17, 3]}
crypto_df = pd.DataFrame(data = my_dict, index = ['BTC', 'ETH', 'LTC', 'XRP', 'ADA'])

crypto_df.plot.pie(y = 'allocation %', figsize = (8, 8))
plt.title('Crypto Portfolio Pie Chart')
```

<div align="center">
  <img src="/images/post_images/220331_pi_chart.png" alt="가상화폐 투자 그래프">
</div>

### **MINI CHALLENGE #3** 투자 비율을 XRP(리플)을 60%, 나머지 코인에 대해서 각 각 10%씩 배당하여 원 그래프를 수정하자. 그리고 explode 메소드를 사용해서 XRP와 다른 코인들 간의 분리를 더 도드라지게 만들어보자.

```python
alloc = {'allocation %': [60, 10, 10, 10, 10]}
explo = [0.10, 0, 0, 0, 0]
crypto_df = pd.DataFrame(data = alloc, index = ['XRP', 'BTC', 'ETH', 'LTC','ADA'])

crypto_df.plot.pie(y = 'allocation %', figsize = (8, 8), explode=explo)
plt.title('Crypto Portfolio Pie Chart')
```

<div align="center">
  <img src="/images/post_images/220331_explode_pi_chart.png" alt="MINI CHALLENGE #3 파이 그래프">
</div>

<h2><b>히스토그램(HISTOGRAMS)</b></h2>
히스토그램은 기본적으로 데이터를 대변해주는 역할을 한다. 다양한 높이들의 바(bars)가 사용되고, 각 각의 바 그룹 숫자들은 특정 범위 안에 들어간다.

`표준편차 : 자료가 평균을 중심으로 얼마나 퍼져있는지를 나타낸다.`

```python
# 평균
avg = daily_return_df['BTC'].mean()
# 표준편차
sigma = daily_return_df['BTC'].std()

# plot의 figsize 정의
plt.figure(figsize = (20, 15))
#
daily_return_df['BTC'].plot.hist(bins = 50) # bins에는 원하는 칸의 수를 입력해서 한 칸의 크기를 조절할 수 있다.
# bins 속성과 함께 alpha 속성으로 0.5(최대값 1) 값을 넣어서 색을 흐리게도 할 수 있다.
daily_return_df['BTC'].plot.hist(bins = 50, alpha = 0.5)
plt.title('Histogram: mu=' + str(mu) + ', sigma=' + str(sigma))

```

아래 히스토그램 그래프를 통해 비트코인은 위험도가 높은 투자라는 것을 보여준다.
평균적이지는 않지만, 하루에 20~25%의 수익을 내는 경우도 있으며, 그 이상의 수익을 내는 경우도 있다.
하지만 수익이 2% 이하로 떨어지는 경우가 많은 것을 알 수 있다.

<div align="center">
  <img src="/images/post_images/220331_bitcoin_histogram.png" alt="비트코인 투자 수익 히스토그램">
</div>

### **MINI CHALLENGE #4** ETH에 대한 히스토그램을 bins=30, 색상은 빨간색으로 해서 출력해보기

```python
plt.figure(figsize = [20, 15])
daily_return_df['ETH'].plot.hist(bins = 30, color='r')
```

이더리움과 비트코인은 연관성이 있다. 비트코인이 떨어졌을 때, 이더리움도 같이 가격이 떨어짐을 알 수 있었다.

<div align="center">
  <img src="/images/post_images/220331_eth_histogram.png" alt="이더리움 수익 히스토그램">
</div>
