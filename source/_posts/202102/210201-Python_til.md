---
title: 210201 Python TIL - List/Dictionary Comprehension
date: 2021-02-01 13:02:42
tags:
  - Python
  - Assignment
  - TIL
  - List-comprehension
  - Dictionary-comprehension
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

- # List / Dictionary Comprehension\*\*

  Python을 더 Python스럽게 작성하기위해서 이 Comprehension을 이해하고 적절히 잘 사용해야한다.

  - ## **List Comprehension**

    - **리스트의 각 요소에 2를 곱해서 새로운 리스트 생성하기 (for-loop + list comprehension)**

    ```python
    numbers = [1, 2, 3, 4, 5]
    new_numbers = []
    for i in numbers:
      new_numbers.append(i*2)
    print(new_numbers)
    # list comprehension을 사용해서 아래와 같이 간단하게 작성을 해줄 수 있다.
    new_numbers = [i*2 for i in numbers]
    ```

      <!-- more -->

    - **리스트의 요소중에 짝수인 수를 선별해서 해당 수에 제곱을 해서 새로운 리스트 생성하기 (for-loop + list comprehension)**

    ```python
    numbers = [1, 2, 3, 4, 5]
    new_numbers = []
    # numbers 자료 중 짝수일때만 제곱하여 new_numbers에 추가한다.
    for i in numbers:
      if i % 2 == 0:
        new_numbers.append(i**2)
    # 조건문이 하나일때에는 for문 뒤에 위치하도록 한다.
    new_numbers = [i**2 for i in numbers if i % 2 == 0]
    ```

    - **리스트의 요소중에 짝수는 제곱을, 홀수인 경우에는 세제곱을 해서 새로운 리스트를 생성하기 (list comprehension + Ternary operator)**

    ```python
    numbers = [1, 2, 3, 4, 5]
    new_numbers = []
    # numbers 자료 중 짝수일때만 제곱하여 new_numbers에 추가한다.
    # 그렇지 않을 경우, 세제곱하여 new_numbers에 추가한다.
    # expression내에 조건을 달아줄때에는 삼항연산자로 조건처리를 해준다.
    new_numbers = [item**2 if item % 2 == 0 else item**3 for item in numbers]
    ```

    - **FizzBuzz (for-loop & if-statement)**

    ```python
    # Fizzbuzz
    # 3의 배수 : Fizz, 5의 배수 : Buzz, 15의 배수 FizzBuzz
    numbers = [i for i in range(1, 100+1)]
    fizzbuzz_list = []
    for i in numbers:
      if i % 15 == 0:
        fizzbuzz_list.append('FizzBuzz')
      elif i % 3 == 0:
        fizzbuzz_list.append('Fizz')
      elif i % 5 == 0:
        fizzbuzz_list.append('Buzz')
      else:
        fizzbuzz_list.append(i)
    # list comprehension으로 convert
    ```

    - **FizzBuzz (list comprehension + Ternary operator)**

    ```python
    # fizzbuzz with list comprehension
    numbers = [i for i in range(1, 100+1)]
    fizzbuzz_list = ['fizzbuzz' if i%15==0 else \
                     'fizz' if i%3==0 else \
                     'buzz' if i%5==0 else \
                     i for i in numbers]
    ```

    - **Leap year(list comprehension + for-loop, if-statement)**

    ```python
    # Leap year
    # Constraint: 1999~2101
    year_list = [i for i in range(1999, 2101+1)]
    # 100으로 나눠지면 leap year가 아니다.
    # 4로 나눠지고, (100으로 나눠지지 않거나, 400으로 나눠지는 경우)
    is_leap_list = []
    # 2000 = leap_year
    # 2100 = plain_year
    year = 2000

    for year in year_list:
      if year%4==0 and (year%100!=0 or year%400==0):
        is_leap_list.append('leap_year')
      else:
        is_leap_list.append(year)
    ```

    - **Leap year(list comprehension with Ternary operator)**

    ```python
    # Leap year with list comprehension
    # Leap year: 4의 배수이면서, 100의 배수가 아니거나 400의 배수인 연도
    # Constraint: 1999~2101
    # Output: 'leap_year' if i is leap_year else, i
    years = [i for i in range(1999, 2101+1)]
    is_leap_list = ['leap_year' if year%4==0 and (year%100!=0 or year%400==0) else year for year in years]

    # 2000 = leap_year
    # 2100 = plain_year
    print(is_leap_list)
    ```

    - **Functionalize the process of filtering leap year**

    ```python
    def is_leap(year):
      if year%4==0 and (year%100!=0 or year%400==0):
        return True
      return False

    is_leap_list = ['leap_year' if isi_leap(year) else year for year in years]

    print(is_leap_list)
    ```

    <br/>

  - ## **Dictionary Comprehension**

    ```python
    fruits = {'a':'apple', 'b':'banana', 'c':'coconut', 'd':'durian'}
    ```

    ```python
    over_six_fruits = {}
    # fruite value 중에 문자열 길이가 6보다 큰 경우에 새로운 dict에 담아서 새로운 dict 변수를 생성
    for k, v in fruits.items():
      if len(v) > 6:
        over_six_fruits[k] = v
    ```

    - #### **위의 코드를 Dictionary comprehension으로 작성**

    ```python
    # Dictionary comprehension
    over_six_fruits = {k:v for k,v in fruits.items() if len(v) > 6}
    ```

    - #### **Dictionary + for-loop with if-statement**

    ```python
    number_dict = {'pi':3.1415, 'e':2.71828, 'year':2021, 'month':2, 'day':1}
    new_number_dict = {}
    for k,v in number_dict.items():
      if v < 3:
        new_number_dict[k] = v ** 2
    print(new_number_dict)
    ```

    - #### **위의 코드를 Dictionary comprehension으로 작성**

    ```python
    number_dict = {'pi':3.1415, 'e':2.71828, 'year':2021, 'month':2, 'day':1}
    # 아래와 같이 삼항연산자를 이용해서 조건처리를 해서 작성을 해 줄 수 있다.
    # 앞에서 key를 이미 정의해주었기 때문에 else구문에서는 key에 대한 정의를 생략하고 value에 대한 선언만 넣어준다.
    new_number_dict = {k:v**2 if v < 3 else v-1 for k,v in number_dict.items()}
    ```

    - #### **리스트의 각 문자열 요소의 길이를 구해서 새로운 리스트 변수 생성 (list comprehension + for-loop with map)**

    ```python
    a_word_list = ['aback','abacus','abandon','abandoned','abandonment','abashed','abate','abbey',]
    실행되는 순간에 나오는 것

    a_word_len_list = [len(s) for s in a_word_list]

    # dict.get()
    # dict.setdefault()

    a_word_len_list = []
    for len_ in map(len, a_word_list):
      a_word_len_list.append(len_)

    ```

    - #### dict.get([key], [default value])와 dict.setdefault([key], [default value]) 활용

    ```python
    #
    len_dict = {}

    # 현재 len_dict에 len(apple)를 key값으로 가지는 값이 없으면 해당 key에 해당하는 변수를 0으로 초기화해서 넣어주고, 1을 누적해서 더해준다.
    # 현재 leln_dict에 len(apple) 5라는 키가 있다면 기존 값에 1을 더해서 누적해준다.
    len_dict[len('apple')] = len_dict.get(len('apple'), 0) + 1
    # key로 len('amp')의 값인 3으로 조회를 했을때 값이 없으면 default로 0을 선언해주고, 초기값에 1을 누적해서 더해준다.
    # 존재한다면 기존 값에 1을 더해서 누적을 해준다.
    len_dict[len('amp')] = len_dict.get(len('amp'), 0) + 1
    ```

    ```python
    # 숙제) 위의 코드를 dictionary comprehension 으로 바꿔보기.
    len_dict = {}
    for word in a_word_list:
      len_dict[len(word)] = len_dict.get(len(word), 0) + 1
    print(len_dict)
    ```

    ```python
    """
    sample output:
    {
      5: ['apple', 'among']
    }
    """

    filtered_dict = {}
    filtered_dict.setdefault(len('among'), []).append('among')
    filtered_dict.setdefault(len('among'), []).append('among')
    filtered_dict.setdefault(len('among'), []).append('amp')

    # 단어의 길이를 기준으로 단어들을 분류
    for word in a_word_list:
        filtered_dict.setdefault(len(word), []).append(word)

    # 첫번째 알파벳 기준으로 단어들을 분류
    for word in a_word_list:
        filtered_dict.setdefault(word[0], []).append(word)
    print(filtered_dict)
    ```

      <br/>

- ## **File I/O**

  - #### **파일을 열고(open), 쓰고(write), 읽기(readline) 실습**

  ```python
  # numbers.txt 파일 열고 쓰기 설정 및 문자열 encoding 방식을 'utf-8'으로 설정
  f = open('./numbers.txt', 'w', encoding='utf-8')
  # 파일에 hello 쓰기
  f.write('hello')
  # file 객체의 life-cycle 종료
  f.close()
  ```

  ```python
  # numbers.txt 파일 열고 읽기 설정 및 문자열 encoding 방식을 'utf-8'으로 설정
  f = open('./numbers.txt', 'r', encoding='utf-8')
  # 파일의 한 줄을 읽고(readline) text 변수에 담기
  text = f.readline()
  # 파일로부터 읽은 text 변수를 출력
  print(text)
  # file 객체의 life-cycle 종료
  f.close()
  ```

  **위에서는 open으로 파일을 연 후에 그 내용을 표시하고 close하였다. 아래 코드는 `with`을 활용한 코드 작성으로, 바로 옆에 수행 할 함수를 명시하고 alias로 f를 해주었다.**
  `with 구문을 사용하면 명시적 클로즈 처리가 불필요하게 되므로 close를 안하는 실수를 사전에 방지할 수 있다.`

  ```python
  with open('./numbers.txt', 'w', encoding='utf-8') as f:
    for _ in range(10):
      f.write('hello')
  ```

  ```python
  with open('./numbers.txt', 'r', encoding='utf-8') as f:
  # 복수의 줄을 읽을때에는 .readlines()
    text_list = f.readlines()
    # 한 줄을 읽을때에는 .readline()
    for line in text_list:
      print(line.replace('\n',''))
  ```

  ```python
  # append mode의 사용

  # append mode를 사용하는 경우, 이전 데이터의 EOF(End Of File)부터 이어서 추가 데이터가 붙기 때문에 데이터를 입력할때 enter를 쳐서 개행(\n) 처리를 하고나서 그 다음 append할 데이터에 대해서 append mode를 실행해줘야 한다.
  # 이전에 삽입된 데이터 끝에 \n 가 없는 경우, 다음 append mode로 추가되는 데이터는 이전에 삽입된 데이터의 끝에 붙는다.
  numbers = [i for i in range(1,10+1)]

  with open('./numbers.txt', 'a', encoding='utf-8') as f:
    for i in numbers:
      # file 객체를 사용해서 데이터를 쓸때에는 반드시 str 타입으로 입력을 해주어야 한다.
      f.write('{}\n'.foramt(i))

  with open('./numbers.txt', 'w', encoding='utf-8') as f:
    print(f.readlines())
  ```

  <br/>

  - #### CSV module : CSV File Reading and Writing

  Python documentation : [CSV module reference](https://docs.python.org/3/library/csv.html?highlight=csv).

  ```python
  import csv

  with open('customers.csv', newline='') as customer_csv:
      customers = csv.reader(customer_csv)
      for row in customers:
          print(row)
      """
      ['CustomerID', 'CustomerName', 'ContactName', 'Address', 'City', 'PostalCode', 'Country']
      ['1', 'Alfreds Futterkiste', 'Maria Anders', 'Obere Str. 57', 'Berlin', '12209', 'Germany']
      ['2', 'Ana Trujillo Emparedados y helados', 'Ana Trujillo', 'Avda. de la Constitución 2222', 'México D.F.', '05021', 'Mexico']
      ['3', 'Antonio Moreno Taquería', 'Antonio Moreno', 'Mataderos 2312', 'México D.F.', '05023', 'Mexico']
      ['4', 'Around the Horn', 'Thomas Hardy', '120 Hanover Sq.', 'London', 'WA1 1DP', 'UK']
      ['5', 'Berglunds snabbköp', 'Christina Berglund', 'Berguvsvägen 8', 'Luleå', 'S-958 22', 'Sweden']
      ........
      """
  ```

  - #### JSON (dump, load 주로 사용)

  Python documentation : [json module reference](https://docs.python.org/3/library/json.html?highlight=dump#json.dump).

  ```python
  import json

  data = {'users': [
      {'name': 'KD Hong', 'locale': 'Seoul, KR'},
      {'name': 'John Doe', 'locale': 'New York, US'},
      {'name': 'Jane Doe', 'locale': 'London, UK'}
  ]}

  with open('users.json', 'w', encoding='utf-8') as f:
      json.dump(data, f)

  with open('users.json', 'r', encoding='utf-8') as f:
      # deserialize
      des_data = json.load(f)

  print(des_data)
  """
  {'users': [{'name': 'KD Hong', 'locale': 'Seoul, KR'}, {'name': 'John Doe', 'locale': 'New York, US'}, {'name': 'Jane Doe', 'locale': 'London, UK'}]}
  """

  for row in des_data['users']:
      print('{} is from {}'.format(row['name'], row['locale']))
  """
  KD Hong is from Seoul, KR
  John Doe is from New York, US
  Jane Doe is from London, UK
  """
  ```
