---
title: 220402 Mini project 회고 및 정리 - 타이타닉 생존자 예측 데이터 분석
date: 2022-04-02 08:46:00
tags:
  - self-development
  - TIL
  - Incomplete
categories:
  - Memoirs
---

<div align="center">
  <img src="/images/post_images/220402_titanic.jpeg" alt="타이타닉"/>
</div>

이번 포스팅에서는 학습 16일차에 Kaggle에 있는 타이타닉 생존자예측 dataset을 분석하고, 분석한 dataset을 시각화하는 연습한 내용을 정리하려고 한다.
이번 미니 프로젝트를 통해 얻었던 나 자신에 대한 피드백은 우선 첫 번째 `dataset에 대한 사전 분석이 부족`했다는 것이다. 그리고 두 번째, `DataFrame과 시각화 작업에 대해 연습이 부족`하여 작업함에 있어 미숙한 부분이 많았다.

이 피드백을 통해 알게된 개선해야 될 부분에 대해서는 앞으로 차근차근 채워가도록 해야겠다.

## <ins><b>타이타닉 생존자 예측 dataset</b></ins>

주어진 dataset은 `총 12개의 칼럼과 891개의 행`으로 구성되어있다. 즉, `한 사람당 총 12 종류의 정보를 포함`하고 있다.
특정 승객의 새존 여부를 알아보려면 survived 항목의 값을 살펴보면 된다. (1:생존)
주어진 test.csv 파일의 구조는 train.csv 파일과 거의 동일하지만, survived 항목이 없다.

```python
import pandas as pd
import numpy as np # 데이터 분석을 위한 NumPy
import matplotlib.pyplot as plt # 데이터 시각화 라이브러리
import seaborn as sns # 데이터 시각화 라이브러리

train_df = pd.read_csv('train.csv')

print(train_df)
```

<div align="center">
  <img src="/images/post_images/220402_titanic_dataframe.png" alt="타이타닉"/>
</div>

## <ins><b>타이타닉 dataset 분석</b></ins>

데이터셋을 시각화하고 결론을 도출해내기 전에는 우선적으로 주어진 dataset에 대한 분석이 필요하다. `이번 미니 프로젝트에서 간과했던 부분`이었는데, dataset이 주어졌다면, 우선적으로 주어진 dataset의 columns 구성(`df.columns.values`),데이터 자료 구성 정보(`df.info()`)를 확인해야한다.

<!-- more -->

### **dataset의 각 칼럼 항목 확인**

```python
# dataset의 column 항목을 확인
print(train_df.columns.values)
# ['PassengerId' 'Survived' 'Pclass' 'Name' 'Sex' 'Age' 'SibSp' 'Parch' 'Ticket' 'Fare' 'Cabin' 'Embarked']

# 'PassengerId' : 승객 번호
# 'Survived' : 생존여부 (0:사망, 1:생존)
# 'Pclass' : 객실등급 - (1:1등급, 2:2등급, 3:3등급)
# 'Name' : 이름
# 'Sex' : 성별
# 'Age' : 나이
# 'SibSp' : 동반한 형제자매와 배우자의 수
# 'Parch' : 동반한 부모와 자식의 수
# 'Ticket' : 티켓 번호
# 'Fare' : 티켓 요금
# 'Cabin' : 객실 번호
# 'Embarked' : 승선한 항구 (C:Cherbourg, Q:Queenstown, S:Southampton)

print(train_df.info())

# output :
<class 'pandas.core.frame.DataFrame'>
RangeIndex: 891 entries, 0 to 890
Data columns (total 12 columns):
 #   Column       Non-Null Count  Dtype
---  ------       --------------  -----
 0   PassengerId  891 non-null    int64
 1   Survived     891 non-null    int64
 2   Pclass       891 non-null    int64
 3   Name         891 non-null    object
 4   Sex          891 non-null    object
 5   Age          714 non-null    float64
 6   SibSp        891 non-null    int64
 7   Parch        891 non-null    int64
 8   Ticket       891 non-null    object
 9   Fare         891 non-null    float64
 10  Cabin        204 non-null    object
 11  Embarked     889 non-null    object
dtypes: float64(2), int64(5), object(5)
memory usage: 83.7+ KB

print(train_df.describe())

# output :

      PassengerId    Survived      Pclass         Age       SibSp  \
count   891.000000  891.000000  891.000000  714.000000  891.000000
mean    446.000000    0.383838    2.308642   29.699118    0.523008
std     257.353842    0.486592    0.836071   14.526497    1.102743
min       1.000000    0.000000    1.000000    0.420000    0.000000
25%     223.500000    0.000000    2.000000   20.125000    0.000000
50%     446.000000    0.000000    3.000000   28.000000    0.000000
75%     668.500000    1.000000    3.000000   38.000000    1.000000
max     891.000000    1.000000    3.000000   80.000000    8.000000

            Parch        Fare
count  891.000000  891.000000
mean     0.381594   32.204208
std      0.806057   49.693429
min      0.000000    0.000000
25%      0.000000    7.910400
50%      0.000000   14.454200
75%      0.000000   31.000000
max      6.000000  512.329200
```

### **탑승석 등급에 따른 생존률**

```python
# Pclass를 기준으로 그룹정렬해주고, Survived의 평균값을 Survived의 값을 기준으로 내림차순 정렬합니다.
pclass_survived = train_df[['Pclass','Survived']].groupby(['Pclass'], as_index=False).mean().sort_values(by='Survived', ascending=False)

sns.barplot(data = pclass_survived, x='Pclass', y='Survived')

# output :
Pclass	Survived
0	1	0.629630
1	2	0.472826
2	3	0.242363
```

`comment : 탑승석의 등급이 높을 수록 생존률이 높음을 확인할 수 있었다.`

<div align="center">
  <img src="/images/post_images/220402_pclass_survived.png" alt="탑승석 등급에 따른 생존률"/>
</div>

### **성별에 따른 생존률**

```python
sex_survived = train_df[['Sex','Survived']].groupby(['Sex'], as_index=False).mean().sort_values(by='Survived', ascending=False)

sns.barplot(data=sex_survived, x='Sex', y='Survived')

# output :
    Sex	  Survived
0	 female	0.742038
1	 male	  0.188908
```

`comment : 성별이 여성일수록 생존률이 높음을 확인할 수 있었다.`

<div align="center">
  <img src="/images/post_images/220402_sex_survived.png" alt="성별에 따른 생존률"/>
</div>

### **승선한 형제자매와 배우자 수에 따른 생존률**

```python
sibsp_survived = train_df[['SibSp','Survived']].groupby(['SibSp'], as_index=False).mean().sort_values(by='SibSp',ascending=True)
print(sibsp_survived)
sns.barplot(data=sibsp_survived, x='SibSp', y='Survived')

# output:

   SibSp  Survived
0      0  0.345395
1      1  0.535885
2      2  0.464286
3      3  0.250000
4      4  0.166667
5      5  0.000000
6      8  0.000000
```

`comment : 승선한 형제자매와 배우자의 수가 1명 -> 2명 -> 0명 -> 3명 -> 4명 순으로 생존률이 높음을 확인할 수 있었다. 즉, 승선한 형제자매와 배우자의 수가 0~2명인 경우에 생존률이 상대적으로 높았다.`

<div align="center">
  <img src="/images/post_images/220402_sibsp_survived.png" alt="승선한 형제자매와 배우자 수에 따른 생존률"/>
</div>
