---
title: 210203 Plotly + Pandas Practice
date: 2021-02-03 19:30:42
tags:
  - Python
  - Plotly
  - Pandas
  - Practice
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/210202_pandas_plotly_logo.jpeg" alt="Plotly+Pandas"/>
</div>

<br/>

### Pandas를 사용해서 csv파일을 읽어보는 간단한 실습을 해보았다.

```python
# -*- coding: utf-8 -*-
import pandas as pd

read_csv = pd.read_csv("data/test.csv")
```

### Excel sheet의 head(Column title)만 추출하기

```python
print(read_csv.head())
```

### 특정 Column name에 해당하는 데이터를 출력

```python
print(read_csv["Column name"])
```

### Column name(1), Column name(2), Column name(3)에 해당하는 데이터를 출력

```python
read_csv = read_csv[["Column name(1)", "Column name(2)", "Column name(3)"]]

# Column이름과 함께 지정한 특정 Column들의 데이터들이 출력된다.
print(read_csv)
```

  <!-- more -->

### Confirmed, Deaths,Recovered 각 칼럼에 해당하는 값들의 합을 구해서 출력

```python
read_csv = read_csv[["Column name(1)", "Column name(2)", "Column name(3)"]].sum()

# 각 Column 별로 값의 합을 출력한다.
# 출력은 column name  | sum 형태로 출력이 되며,
# type은 pandas.core.series.series 로 출력이 된다.
print(read_csv)

# 만약에 excel sheet 형태로 위에 column name 아래에 값을 출력하고 싶다면, series type을 data frame type으로 type을 바꿔주면 된다.
read_csv = read_csv[["Column name(1)", "Column name(2)", "Column name(3)"]].sum().reset_index("[총합을 표기하는 column의 이름]")
```

### 특정 Column의 title을 바꾸고 싶은 경우

```python
read_csv = read_csv.rename(columns={'[이름을 바꿀 대상 column]':'[새롭게 변경할 column 이름]'})
```

<br/>

### 특정 Column을 기준으로 Grouping한 뒤에 각 항목의 최종합(sum)을 표시하고 싶은 경우

```python
# Column1, Column2, Column3에 해당하는 데이터를 출력
read_csv = read_csv[["Column1", "Column2", "Column3"]]

# Column1을 기준으로 grouping (Grouping만 하면 DataFrameGroupBy object)
# read_csv 객체를 Column1을 기준으로 해서 정렬(groupby)
# Column1기준 Column2, Column3의 합계를 index와 함께 표시
read_csv = read_csv.groupby("Column1").sum().reset_index()
```
