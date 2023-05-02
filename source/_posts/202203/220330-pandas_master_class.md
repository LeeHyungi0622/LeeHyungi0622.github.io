---
title: 220330 Pandas TIL (작성예정...)
date: 2022-03-30 07:31:00
tags:
  - AI
  - Pandas
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/220330_pandas_til.png" alt="판다스 복습하기">
</div>

이번 포스팅에서는 본격적으로 Pandas 학습한 내용을 나중에 복습하기 위한 목적에서 간단하게 정리해보려고 한다.

<h2><b>Index 설정하고 재설정하기</b></h2>

- 판다스는 csv파일을 읽거나 데이터를 DataFrame의 형태로 저장하는데 사용된다. (numeric index는 기본적으로 설정된다)

```python
import pandas as pd

bank_df = pd.read_csv('bank_client_information.csv') # 자동으로 index를 숫자로 지정

bank_df
```

- 특정 column을 index column으로 할당

```python
bank_df.set_index('First Name', inplace = True) # inplace = True로 원본 데이터 업데이트
```

- 다시 numeric index를 index값으로 되돌릴때에는 `reset_index`메소드를 사용한다.

```python
bank_df.reset_index(inplace = True)
```

- 열을 판다스 데이터 프레임의 인덱스로 사용하고 싶을 때

```python
bank_df = pd.read_csv('bank_client_information.csv', index_col = 'First Name')

bank_df
```

- (CHALLENGE #4) 판다스 데이터 프레임 인덱스 설정하고 재 설정하기

```python
# 방법1
bank_df = pd.read_csv('bank_client_information.csv')
bank_df.set_index('Last Name', inplace = True)

# 방법2
bank_df = pd.read_csv('bank_client_information.csv', index_col = 'Last Name')
```

<!-- more -->

<h2><b>데이터 프레임에서 열을 선택하기</b></h2>

```python
bank_df = pd.read_csv('bank_client_information.csv')

bank_df

sample = bank_df['Email']
sample

type(sample) # pandas.core.series.Series

bank_df.Email # 이 방법에는 제한사항이 있기 때문에 대괄호 표기법을 사용하도록 한다. (예외: 이름에 공백이 있는 경우에는 작동을 하지 않는다.)

bank_df['Net Worth']

sample = bank_df[['First Name', 'Net Worth']] # 2개의 열 이상, 다차원일때에는 다음과 같이 내부에 괄호를 한 개 더 넣어 출력하고자 하는 칼럼의 이름을 넣어준다.

type(sample) # pandas.core.frame.DataFrame

# 이름과 순 자산만을 포함하는 데이터프레임
my_selected_columns = ['First Name', 'Net Worth']

sample = bank_df[my_selected_columns]
sample

bank_df[0:2] # 데이터프레임의 0, 1번째 행에 대한 정보만 추려서 출력을 해준다.
```

- (CHALLENGE #5) 순 자산(Net Worth), 거래 햇수(Years with Bank), 그리고 우편번호(Postal Code)에 해당하는 열을 데이터프레임에서 선택하시오.

```python
my_selected_columns = ['Net Worth', 'Years with Bank', 'Postal Code']

sample = bank_df[my_selected_columns]

sample
```

<h2><b>DataFrame에서 Columns을 추가하고 지우기</b></h2>

```python
bank_df = pd.read_csv('bank_client_information.csv')

bank_df

# Age column이 가장 마지막 열에 추가

bank_df['Age'] = [25, 26, 28, 30, 36, 22, 48, 55, 70, 69]

# 특정 index position에 새로운 칼럼 삽입
# 0번째 인덱스에 Credit Score 칼럼 삽입
bank_df.insert(0, column='Credit Score', value=[680,700, 750, 699, 550, 600, 750, 500, 520,  510])

# 특정 칼럼을 데이터프레임에서 삭제하기
del bank_df['Email']
bank_df.drop(labels = ['Last Name', 'Net Worth'], axis = 1, inplace = True) # axis=1 열 방향 모두 제거하고, 메모리에서 삭제하겠다.
```

<h2><b>특정 칼럼 추출하기</b></h2>

```python
Years_with_bank = bank_df.pop('Years with Bank')
Years_with_bank # 단일 판다 시리즈 데이터
```

- (CHALLENGE #6) 고객의 대출 여부를 알려주는 열을 추가하시오. 그리고 대출금을 알려주는 열을 기존의 데이터프레임에 추가하시오.

```python
bank_df = pd.read_csv('bank_client_information.csv')

# 고객 대출여부를 알려주는 열과 대출금을 알려주는 열을 넣기
bank_df['Has Mortgage'] = [1, 1, 0, 0, 0, 0, 1, 0, 0, 0]

#값이 1이라면 대출이 있다는 의미이고, 그렇다면 대출금을 달러로,
bank_df['Mortgage Value'] = [200000, 130000, 0, 0, 0, 0, 400000, 0, 0, 0]

```

<h2><b>DataFrame에서 LABEL-BASED로 요소선택하기</b></h2>

```python
import pandas as pd

bank_df = pd.read_csv('bank_client_information.csv', index_col='Last Name')

# alphabetical order로 dataframe 정렬 (LastName의 알파벳 순서로 정렬)
bank_df.sort_index(inplace = True)

# loc은 데이터 프레임에서 행과 열을 필터링하는데 사용된다.
# index가 정수기반이라면 iloc을 사용하지만, 문자열 기반이라면, loc을 사용한다.
bank_df.loc['Small'] # Type이 Series인 데이터
bank_df.loc['Steve'] # Type이 DataFrame인 데이터

bank_df.loc['Ahmed':'Patton'] # Ahmed ~ Patton 까지 모든 행이 출력 (Patton까지 모두 포함)
# cf. iloc[x:y]에서는 y-1 정수 범위내에서 출력

# Keller index까지 모두 출력
bank_df.loc[:'Keller']

# 출력을 원하는 index의 값을 배열로 넘겨준다.
bank_df.loc[['Keller', 'Steve', 'Mo']]

# 무작위로 5의 샘플을 출력한다.
# axis=0는 행을 의미, axis=1는 열을 의미한다.
bank_df.sample(n = 5, axis = 0)

# 데이터프레임의 30%를 무작위로 출력
bank_df.sample(frac=0.3, axis=0)
```

- (CHALLENGE #7) "first name" column을 index로 설정해서 csv 파일을 로드하고, 데이터프레임으로부터 랜덤으로 2개의 행 데이터를 선택한다.

```python
import pandas as pd

bank_df = pd.read_csv('bank_client_information.csv', index_col='First Name')

bank_df.sample(n = 2, axis = 0) # axis = 0은 행이며, n = 2는 랜덤으로 뽑을 데이터의 표본 객체 수이다.
```

<h2><b>DataFrame에서 INTEGER INDEX-BASED 요소 선택</b></h2>

```python
bank_df = pd.read_csv('bank_customer_information.csv')

bank_df.iloc[9] # 10번째 행의 데이터에 접근

bank_df.iloc[2:5] # 2~4까지의 행 데이터를 포함

bank_df.iloc[:4] # 0~4까지, 4는 포함하지 않고 모든 행을 가져온다는 의미

bank_df.iloc[[2, 4, 9]] # 2, 4, 9행의 데이터를 가져온다.

bank_df.iloc[4, 0:3] # 4행으로 가서 0:3 (0,1,2)열만 가져온다.
```

- (CHALLENGE #8) 두 가지 다른 방법을 사용해서 마지막 두 행 데이터를 가져와서 출력하시오.

```python
bank_df.iloc[-2:] # 끝에서 2번째부터 끝까지 출력
bank_df.iloc[8:] # 8행부터 끝까지 출력
```

<h2><b>브로드캐스팅 연산과 새로운 데이터 프레임값을 설정하는 방법</b></h2>

```python
bank_df = pd.read_csv('bank_client_information.csv')

# 모든 고객의 순 자산이 증가 각 각 1000만원 증가
bank_df['Net Worth'] = bank_df['Net Worth'] + 1000

bank_df['Net Worth'] = bank_df['Net Worth'].add(1000)

# 고객의 순 자산을 포함하는 열을 하나 추가 (캐나다 달러로 환산) 1USD = 1.3CAD
bank_df['Net Worth (CAD)'] = bank_df['Net Worth'].mul(1.3)
bank_df

# 특정 고객의 이메일 주소 업데이트
bank_df.loc['Kate','Email'] = 'kate.noor@hotmail.com' # index가 숫자 기반이기 대문에 다음과 같이 label 기반의 .loc[]으로 선택을 할 수 없다.
bank_df.iloc[4, 2] = 'kate.noor@gamil.com'

bank_df.iloc[[0, 3],[4]] = [6000, 15000] # 두 고객의 networth 정보를 업데이트한다. 0행과 3행의 4열(Net Worth)정보를 주어진 정보에 따라 업데이트한다.


```
