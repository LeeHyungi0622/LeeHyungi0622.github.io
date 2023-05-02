---
title: 210204 Plotly + Pandas Practice
date: 2021-02-04 18:32:42
tags:
  - Python
  - Plotly
  - Pandas
  - Practice
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/210202_pandas_plotly_logo.jpeg" alt="Pandas"/>
</div>

<br/>

```python
import pandas as pd
df = pd.read_csv("data/time_confirmed.csv")
```

### **Table에서 Column 특정 Column 제거하기**

- #### 제거할 Column은 drop()을 사용해서 제거해준다.

  ```python
  # 제거할 column명을 column 속성을 명시해서 제거하거나
  df = df.drop(column=["Column1","Column2","Column3","Column4"]).sum().reset_index(name="total")
  # 명시하지 않고 axis=1를 추가해서 제거할 수 있다.
  df = df.drop(["Column1","Column2","Column3","Column4"], axis=1).sum().reset_index(name="total")
  # 제거된 column을 제외한 column을 기준으로 합계(sum)를 구하고 series를 data frame으로 바꿔준다. 바꾼 후에 새로 추가된 합계 column의 이름은 "total"로 지정해준다. (.reset_index("total"))
  ```

    <!-- more -->

### **기존의 column명을 rename()을 사용해서 수정해준다.**

```python
df = df.rename(column={'[target_column_name]':'[new_column_name]'})
```

### **공통으로 묶을 수 있는 처리는 함수화 시켜서 작성해준다.**

```python
condition = ["new_column_name1","new_column_name2","new_column_name3"]

def make_df(condition):
  df = pd.read_csv("data/{}.csv".format(condition))
  df = df.drop(column=["Column1","Column2","Column3","Column4"]).sum().reset_index(name=condition
  df = df.rename(column={'[target_column_name]':'[new_column_name]'})
  return df

for condition in conditions:
  condition_df = make_df(condition)
  print(condition_df)

```
