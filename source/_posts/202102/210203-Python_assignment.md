---
title: 210203 Python Assignment
date: 2021-02-03 16:27:41
tags:
  - Python
  - Assignment
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

## 공공데이터포털의 csv파일을 json으로 converting하기

`경기도 성남시_인구및세대_현황`

```python
import csv
import json

population = {'sungnam_population': []}

# csv 파일을 csv module로 읽어오기
with open('20210201_sungnam.csv', 'r', encoding='euc-kr') as f:
    sungnam_population = csv.reader(f)
    # list -> dictionary
    sungnam_population_list = list(sungnam_population)

    for row in sungnam_population_list[1:]:
        population_dict_row = dict()
        # 0번째 열에 있는 key값과 index를 이용해서 dictionary 데이터 만들기
        for index, key in enumerate(sungnam_population_list[0]):
            population_dict_row[key.replace(
                " ", "")] = row[index].replace(" ", "")
        population['sungnam_population'].append(population_dict_row)

# json.dump()를 활용해서 dictionary type의 데이터를 json 파일로 추출하기
with open('sungnam_population.json', 'w', encoding='euc-kr') as f:
    json.dump(population, f)

# 추출된 json 파일을 읽고 출력하기
with open('sungnam_population.json', 'r', encoding='euc-kr') as f:
    des_data = json.load(f)

print(des_data)

```

  <!-- more -->
