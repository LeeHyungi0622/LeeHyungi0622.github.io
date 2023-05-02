---
title: 210122 Python TIL - Dictionary와 Set의 활용
date: 2021-01-22 22:37:42
tags:
  - Python
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

## **오늘 배운 내용**

- Python 메인 강의

  - 과제점검(1월 22일자 파이썬 과제 풀이/해설)
  - <h1>Dictionary</h1> : Dictionary는 key와 value로 구성된 자료형으로, 간편하게 key값으로 value를 참조해서 가져올 수 있다.

    ```python
    ganji = {}
    ```

    - Dictionary 활용

      - Dictionary 데이터 초기화 하기

      ```python
      # 직접 데이터를 넣고 초기화 시키기
      ganji = {'ja':0, 'chuk':1} #{'ja': 0, 'chuk': 1}
      ganji = dict(ja=1, chuk=0)
      ganji = dict([('ja',1),('chuk',2)])
      ```

        <!-- more -->

      - Key값으로 Value 데이터 업데이트하기

      ```python
      # 기존의 dictionary에 데이터 추가하기
      ganji['in'] = 'tiger' # {'ja': 1, 'chuk': 0, 'in': 'tiger'}
      ```

      - del 키워드로 Dictionary 내의 데이터 삭제하기

      ```python
        # del 키워드는 값을 참조해서 삭제한다.
        del ganji['dragon']
        # del 키워드를 사용해서 선언되어있는 변수를 삭제할 수 있다.
        fast = 1
        del fast
        # ------ 이하에서는 fast 변수에 접근이 불가하다. (fast 변수는 메모릴상에서 제거된 상태)
        result = fast + 2
        print(result)
      ```

      - .keys()로 key값 참조하기

      ```python
      ganji.keys() # dict_keys(['ja', 'chuk', 'in', 'myo'])
      # 위의 .keys()를 for-loop내에서 활용할 수 있다.
      for key in ganji.keys():
          # 각 각의 value를 출력
          print(ganji[key])
      ```

      - .values()로 value값 참조하기

      ```python
      ganji.values() # dict_values(['rat', 'cow', 'tiger', 'rabbit'])
      ```

      - .items()로 [(key, value)] 형태(리스트 내 튜플 자료형)로 값 참조하기

      ```python
      ganji.items() # dict_items([('ja', 'rat'), ('chuk', 'cow'), ('in', 'tiger'), ('myo', 'rabbit')])

      ```

      - zip() 을 활용하여 튜플형태로 (key, value)로 묶어서 리스트에 담아 표현하기

      ```python
      list(zip(ganji.keys(), ganji.values()))
      # output : [('ja', 'rat'), ('chuk', 'cow'), ('in', 'tiger'), ('myo', 'rabbit')]
      ```

      - .pop(['key']) 을 활용하여 Dictionary 내 데이터 제거하기

      ```python
      ganji.pop('ja') # 'rat'
      ```

      - .popitem() 을 활용하여 Dictionary의 마지막 요소 제거하기 (list에서 .pop()과 같은 기능)

      ```python
      ganji.popitem() # 리스트의 가장 마지막 요소를 제거한다. ('myo', 'rabbit')
      ```

      ```python
      """
      hong_midterm = {}
      hong_midterm['Korean'] = 70
      hong_midterm['Math'] = 60
      hong_midterm['English'] = 100
      hong_midterm['Social'] = [45,30,20]
      hong_midterm['Science'] = [45,45,40,10]
      """
      ```

      - .get('['key']', [default value] ) : get의 첫번째 인자로 Key값을 넣고, 존재하지 않을 경우 반환할 Default 값을 선언할 수 있다.

      ```python
      hong_midterm.get('Ethics', False)
      ```

      - .setDefault('Ethics',[]) : Dictionary 변수에 Ethics 항목을 추가하고, 동시에 Default 값을 넣어 초기화 시켜 줄 수 있다.

      ```python
      hong_midterm.setdefault('Ethics', [])
      ```

      - 리스트만큼 잘 사용되지는 않지만, Dictionary도 len()을 사용해서 길이를 구할 수 있다.

      - \*\*를 활용하여 Dictionary 변수의 key값으르 Destructuring Assignment하기

      ```python
      some_dict = {'name': 'Hong Gil-dong', 'locale': 'Seoul, KR'
      print("Hello, I'm {name}, from {locale}".format(**some_dict))
      ```

  - <h1>Set</h1>: 
    set은 중복요소를 제거하고, 교집합(&, intersection), 합집합(|, union), 차집합(-, difference), 대칭차집합(symmetric difference)를 활용하여 두 집합변수의 관계형 데이터를 만들어낼 수 있다.

  ```python
  ppap = ['pen', 'pineapple', 'apple', 'pen']
  ```

  - Set 활용하기

    - set과 list는 상호 type casting이 가능하다.

    ```python
    a_word = 'fastcampus'
    b_word = 'python'

    a_set = set(a_word) #{'u', 'm', 'p', 'a', 'c', 's', 'f', 't'}
    b_set = set(b_word) # {'y', 'p', 'h', 'n', 't', 'o'}
    ```

    - 두 집합의 교집합 구하기

    ```python
    a_set & b_set
    a_set.intersection(b_set)
    # output : {'p', 't'}
    ```

    - 두 집합의 합집합 구하기

    ```python
    a_set | b_set
    a_set.union(b_set)
    # output : {'a', 'c', 'f', 'h', 'm', 'n', 'o', 'p', 's', 't', 'u', 'y'}
    ```

    - 두 집합의 차집합 구하기

    ```python
    a_set - b_set
    a_set.difference(b_set)
    # output : {'a', 'c', 'f', 'm', 's', 'u'}
    ```

    - 두 집합의 대칭차 구하기

    ```python
    a_set ^ b_set
    a_set.symmetric_difference(b_set
    # output : {'a', 'c', 'f', 'h', 'm', 'n', 'o', 's', 'u', 'y'}
    ```

## **간단한 연습문제**

1. 다음 인적사항을 입력한다. (list에 각 정보가 dict로 존재)

| Name              | Locale       | Age | Username |
| ----------------- | ------------ | --- | -------- |
| Johnny Silverhand | Night City   | 120 | Johnny   |
| John Doe          | CA, USA      | 40  | Doh      |
| Jane Doe          | Seoul, Korea | 24  | jane-doe |
| Foo Bar           | Busan, Korea | 31  | foo-bar  |

<br/>

**위에서 입력한 데이터를 활용하여 아래 문제를 해결하시오.**

1-1. Jane Doe의 Locale을 London, UK 로 수정하세요.
2-1. list의 모든 아이템에 대해 key와 value를 모두 출력하세요.(반복문 쓰지 말 것) 3. 다음 list의 중복 아이템을 제거한 뒤, 내림차순으로 정렬하여 출력하세요. (아래 cities 리스트 활용)

```python
cities = [
    'Daejeon', 'Ulsan', 'Seoul', 'Jeju',
    'Busan', 'Ulsan', 'Daegu', 'Daejeon',
    'Seoul', 'Seoul', 'Daejeon', 'Gwangju',
    'Busan', 'Daegu', 'Gwangju', 'Daejeon',
    'Ulsan', 'Jeju', 'Gwangju', 'Seoul'
]
```

1번의 경우, user_info 리스트 변수를 선언하여, 각각의 열에 dict 타입의 데이터를 초기화해서 넣어준다.

```python
user_info = []
user_row = dict([('name','Foo Bar'),
                 ('locale', 'Busan, Korea'),
                 ('age', 31),
                 ('username', 'foo-bar')
                ])
user_info.append(user_row)
user_info

"""
output :
[{'name': 'Johnny Silverhand',
  'locale': 'Night City',
  'age': 120,
  'username': 'Johnny'},
 {'name': 'John Doe', 'locale': 'CA, USA', 'age': 40, 'username': 'Doh'},
 {'name': 'Jane Doe',
  'locale': 'Seoul, Korea',
  'age': 24,
  'username': 'jane-doe'},
 {'name': 'Foo Bar',
  'locale': 'Busan, Korea',
  'age': 31,
  'username': 'foo-bar'}]
"""
```

1-1번의 경우, 각각의 행 요소에는 index로 접근을 하고, 접근한 후에는 dict 데이터에 key값으로 접근을 해서 해당 데이터를 수정해주면 된다.

```python
user_info[2]['locale'] = 'London, UK'
```

2-1번의 경우, 반복문을 사용하지 않고 각 행의 dict형 데이터들을 출력해야 되기 때문에 아래와 같이 각 행의 dict형 데이터에는 index로 접근을 하고, .items()로 key와 value를 Tuple 데이터형으로 묶어서 읽어온다.

```python
print(list(user_info[0].items()))
print(list(user_info[1].items()))
print(list(user_info[2].items()))
print(list(user_info[3].items()))

"""
output:

[('name', 'Johnny Silverhand'), ('locale', 'Night City'), ('age', 120), ('username', 'Johnny')]
[('name', 'John Doe'), ('locale', 'CA, USA'), ('age', 40), ('username', 'Doh')]
[('name', 'Jane Doe'), ('locale', 'London, UK'), ('age', 24), ('username', 'jane-doe')]
[('name', 'Foo Bar'), ('locale', 'Busan, Korea'), ('age', 31), ('username', 'foo-bar')]
"""
```

3번 문제의 경우, 중복제거는 set()을 사용해서 제거하고 내림차순으로 정렬하기 위해서 list()로 변환한 후에 sort() 메서드를 사용해서 정렬한다.

```python
unique_cities = list(set(cities))
unique_cities.sort(reverse=True)
```
