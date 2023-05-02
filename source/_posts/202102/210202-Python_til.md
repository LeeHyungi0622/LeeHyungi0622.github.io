---
title: 210202 Python TIL - Error exception, RegEx, Lambda, Function, map, filter, reduce
date: 2021-02-02 09:44:41
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

## Section0) Assignment & Review

- HackerRank 문제 (List comprehension)

```python
x, y, z, n = (int(input()) for _ in range(4))
result_ = [[i, j, k] for i in range(x+1) for j in range(y+1) for k in range(z+1) if (i+j+k) is not n]
print(result_)
```

### Section0-1)zip() 활용

```python
numbers = [i for i in range(1, 10+1)]
chars = list(set('abcdefghij'))
for i,c in zip(numbers, chars):
  print(i,c)
```

### Section0-2)File I/O 실습

- readlines, csv.reader()로 csv파일 읽기
  **readlines()** : readlines()로 파일을 읽으면, 아래와 같이 row 데이터가 구분자(, comma)로 구분되어 출력이 된다.

  ```python
  with open('customers.csv', 'r', encoding='utf-8') as f:
    rows = f.readlines()
    for row in rows:
        print(row)
    # output :
    # CustomerID,CustomerName,ContactName,Address,City,PostalCode,Country
    # 1,Alfreds Futterkiste,Maria Anders,Obere Str. 57,Berlin,12209,Germany
  ```

    <!-- more -->

  **csv.reader()** : csv.reader()를 사용해서 csv 파일을 읽으면 아래와 같이 row가 list형으로 출력이 된다.
  import csv

  ```python
  with open('customers.csv', 'r', encoding='utf-8') as f:
    customers = csv.reader(f)  # reader object
    for row in customers:
        # print(row)
        # output :
        # ['CustomerID', 'CustomerName', 'ContactName', 'Address', 'City', 'PostalCode', 'Country']
        # ['1', 'Alfreds Futterkiste', 'Maria Anders', 'Obere Str. 57', 'Berlin', '12209', 'Germany']

  ```

  **customers.csv 파일을 읽어서 customers.json파일로 추출하기**

  ```python
  # customers.csv -> customers.json
  import csv
  import json

  # csv 파일을 읽어서 아래와 같이 dictionary형으로 각 row의 데이터를 key-value 로 삽입을 해준다.

  # {'users':[
  #     {},
  #     {},
  # ]}

  users = {'users': []}

  with open('customers.csv', 'r', encoding='utf-8') as f:
      customers = csv.reader(f)
      customer_list = list(customers)
      # list -> dictionary
      for row in customer_list[1:]:
          user_dict = {}
          for index, key in enumerate(customer_list[0]):
              user_dict[key] = row[index]
              users['users'].append(user_dict)

  with open('customers.json', 'w', encoding='utf-8') as f:
    json.dump(users, f)
  ```

## **Exception(Error handling, try-except), RegEx, Lambda Function**

## Section1) Error handling(Exception, try-exception) (예외처리)

### Section1-1) Exception 처리의 기본 format

```python
try:
  실행문
except:
  실행문
```

```python
try:
  pass
except:
  pass
```

### Section1-2) 예외처리하기

```python
  try:
    result = 3/0
  except:
    print('You could divide by anything, not zero.')
```

**recursive하게 에러구문 처리하기**

```python
def get_num_from_user():
  try:
    guess = int(input('Guess the number'))
    return guess
  except:
    print("Plz, don't bother programmer.")
    return get_num_from_user()

get_num_from_user()
```

## Section2) RegEx (정규표현식)

- 특정한 규칙을 가진 문자열의 집합을 표현하는데 사용하는 형식언어
- Python은 `re` module로 정규표현식의 사용이 가능
- 기본적인 문법은 비슷하나 언어별로 사용법이나 문법이 조금씩 달라서 외우기 보다는 Reference를 참조하는 것이 좋다.

Python RegEx Reference : [Python RegEx Reference](https://docs.python.org/3/howto/regex.html)

### Section2-1) **Regular Expressions - match()**

```python
import re

a = 'penpineapple'
b = 'applepen'
# r: regex string 구분자
# match([regex pattern], [check string])
m = re.match(r'^pen', a)
n = re.match(r'.+pen$', b)
print(m,n)
```

### Section2-2) **Regular Expressions - match object**

```python
# match한 string을 뽑아낼때에는 group()을 사용한다.
m.group() # 'pen'
m.span() # (0, 3)
# group()말고 m.start(), m.end()사용해서 substring해보기
a[m.start():m.end()] # 'pen'
```

### Section2-3) Regular Expressions - Meta Characters

- **.(Dot)** : Any character (except newline character)
  줄바꿈 문자(\n)을 제외한 모든 문자와 match됨을 의미한다.

  ```python
  a.b # a + 모든문자 + b
      # a와 b라는 문자 사이에는 어떤 문자가 들어가도 모두 matching됨을 의미
  """
  "aab", "a0b", "abc"가 정규식 a.b 검사를 했을때,
  - "aab"는 가운데 문자 "a"가 모든 문자를 의미하는 .과 일치하므로 정규식과 매치된다.
  - "a0b"는 가운데 문자 "0"가 모든 문자를 의미하는 .과 일치하므로 정규식과 매치된다.
  - "abc"는 "a"문자와 "b"문자 사이에 어떤 문자라도 하나는있어야 하는 이 정규식과 일치하지 않으므로 매치되지 않는다.
  """
  ```

  ```python
  a[.]b # "a + Dot(.)문자 + b"
  # 따라서 정규식 a[.]b는 "a.b" 문자열과 매치되고, "a0b" 문자열과는 매치되지 않는다.
  """
  ※ 만약 앞에서 살펴본 문자 클래스([]) 내에 Dot(.) 메타 문자가 사용된다면 이것은 "모든 문자"라는 의미가 아닌 문자 . 그대로를 의미한다. 혼동하지 않도록 주의하자.
  """
  ```

<br/>

- **\*(Repetition)** : Zero or more occurrences \* 바로 앞에 있는 특정 문자가 0부터 무한대로 반복될 수 있다는 의미한다.

  ca\*t에서 \*앞에 a라는 문자가 있고, 이 문자가 0~무한대로 반복하는 조건을 만족한다면 match된다.

  ```python
  """
  정규식	문자열	Match 여부	        설명
  ca*t	 ct	    Yes	     "a"가 0번 반복되어 매치
  ca*t	 cat	   Yes	     "a"가 0번 이상 반복되어 매치 (1번 반복)
  ca*t	 caaat	 Yes	     "a"가 0번 이상 반복되어 매치 (3번 반복)
  """
  ```

<br/>

- **+(Repetition)** : One or more occurrences

  위에서 살펴본 반복을 나타내는 \*외에 또 다른 반복을 나타내는 메타 문자로 +가 있다. +는 최소 1번 이상 반복될 때 사용한다. 즉 \*가 반복 횟수 0부터라면 +는 반복 횟수 1부터인 것이다.

  ```python
  # ca+t 정규식이 있다고 가정했을때,
  # "c + a(1번 이상 반복) + t"을 의미한다.

  """
  정규식	문자열	Match 여부	            설명
  ca+t	 ct	    No	      "a"가 0번 반복되어 매치되지 않음
  ca+t	 cat	   Yes	    "a"가 1번 이상 반복되어 매치 (1번 반복)
  ca+t	 caaat	 Yes	    "a"가 1번 이상 반복되어 매치 (3번 반복)
  """
  ```

<br/>

- **{}(Curly brackets)** : Exactly the specified number of occurrences
  반복 횟수를 3회만 또는 1회부터 3회까지만으로 제한하고 싶을때 {}을 사용해서 범위를 지정해 줄 수 있다.

  > { } 메타 문자를 사용하면 반복 횟수를 고정할 수 있다. {m, n} 정규식을 사용하면 반복 횟수가 m부터 n까지 매치할 수 있다. 또한 m 또는 n을 생략할 수도 있다. 만약 {3,}처럼 사용하면 반복 횟수가 3 이상인 경우이고 {,3}처럼 사용하면 반복 횟수가 3 이하를 의미한다. 생략된 m은 0과 동일하며, 생략된 n은 무한대(2억 개 미만)의 의미를 갖는다.

  `※ {1,}은 +와 동일하고, {0,}은 *와 동일하다.`

  ```python
  # ca{2}t 정규식이 있다고 가정했을때
  # "c + a(반드시 2번 반복) + t"를 의미한다.
  """
  정규식	  문자열	  Match 여부	        설명
  ca{2}t	 cat	     No	      "a"가 1번만 반복되어 매치되지 않음
  ca{2}t	 caat	     Yes	    "a"가 2번 반복되어 매치
  """
  ```

  <br/>

- **?(Question mark)** : 반복은 아니지만 이와 비슷한 개념이다. 메타 문자가 의미하는 것은 {0,1}이다. 0번부터 1번까지 ?앞의 문자가 사용이 되었다면 match되는 경우로 본다.

  ```python
  """
  정규식	문자열	Match 여부	       설명
  ab?c	 abc	   Yes	    "b"가 1번 사용되어 매치
  ab?c	 ac	     Yes	    "b"가 0번 사용되어 매치
  """
  ```

### Section2-4) Regular Expressions 사용하기

```python
# 정규식 표현을 compile해두면 regular expression object를 재사용하는 것이 가능하다.

apples = ['a'+'p'*i+'le' for i in range(10)]

# 미리 regex를 compile해서 재사용 할 수 있다.
apple_finder = re.compile(r'^ap{2}le$')
for item in apples:
  m = apple_finder.match(item)
  print(itme, m)
```

- ^(Caret) : Starts with

```python
a = 'penpineapple'
b = 'applepen'
c = 'pen'
m = re.match(r'^.*(pen)$', b) # (0, 3)
```

- ()(Parentheses) : Capture and group

```python
fastcampus
r'(cam)' : cam이 연속 순서로 나와야한다.
r'(fastcampus)'
```

- |(Vertical bar) : Either or

```python
r'.*(fast|campus).*' : fast나 campus가 등장을 하는 경우
```

- \(Backslash): Signals a special sequence (can also be used to escape special characters)

```python
r'[A-Za-z0-9\-\_\.\+]@gmail\.com' # alphabet, number, -, ., _, +
```

- [](Square brackets) : A set of characters

```python
r'[A-Za-z0-9\-\_\.\+]@gmail\.com' # alphabet, number, -, ., _, +
# 위의 정규표현식은 한글자의 username@gmail.com 만 작성할 수 있다.
r'[A-Za-z0-9\-\_\.\+]*@gmail\.com' # alphabet, number, -, ., _, +
# 위와같이 *를 붙여서 주면, 1개이거나 없는 경우를 허용하므로, 이 경우는 사용하지 않는다.
r'[A-Za-z0-9\-\_\.\+]+@gmail\.com' # alphabet, number, -, ., _, +
# 위와같이 +를 붙여서 앞의 사용자 이름(아이디)가 1개 이상 있다는 것을 표현할 수 있다.
r'[A-Za-z0-9\-\_\.\+]{n,m}@gmail\.com' # alphabet, number, -, ., _, +
# 위와같이 사용자 이름(아이디)의 글자수 범위(n~m)를 지정할 수 있다.
# {4, 32} 보통 2의 배수인 4부터 32까지의 범위로 username을 잡는다.
```

- $(Dollar sign) : Ends with

### Section2-5) re module functions

파이썬은 정규 표현식을 지원하기 위해 re(regular expression)모듈을 제공한다. re module은 파이썬을 설치할 때 자동으로 설치되는 기본 라이브러리이다.

```python
import re
p = re.compile('ab*')
```

re.compile을 사용하여 정규 표현식을 컴파일한다. re.compile()의 결과로 돌려주는 객체 p(compile된 패턴 객체)를 사용하여 그 이후의 작업으르 수행할 수 있다.

<br/>

```python
import re
p = re.compile('[a-z]+')
```

- **re.match(patterns, string[, flags])** : 문자열의 처음부터 정규식과 매치되는지 조사한다.

  ```python
  m = p.match("python")
  # Python 문자열은 [a-z]+ 정규식에 부합되므로 match 객체를 돌려준다.
  print(m)
  <_sre.SRE_Match object at 0x01F3F9F8>

  m = p.match("3 python")
  print(m)
  # output : None
  ```

  match의 결과로 match 객체 혹은 None을 반환해주기 때문에 Python 정규식 프로그램은 보통 아래와 같은 흐름으로 작성한다.

  ```python
  p = re.compile('[regular expression]')
  m = p.compile('[string goes here]')
  if m:
    # match의 결과값이 있을때에만 다음의 작업을 수행
    print('Match found: ', m.group())
  else:
    print('No match')
  ```

- **re.search(patterns, string[, flags])** : 문자열 전체를 검색하여 정규식과 매치되는지 조사한다.

  ```python
  m = p.search("python")
  print(m)
  <_sre.SRE_Match object at 0x01F3FA68>
  ```

  ```python
  m = p.search("3 python")
  print(m)
  <_sre.SRE_Match object at 0x01F3FA30>
  ```

  "3 python"문자열의 첫 번째 문자는 "3"이지만, search는 문자열의 처음부터 순차검색을 하는 것이 아니라 문자열 전체를 검색하기 때문에 "3" 이후의 "python" 문자열과 매치된다. <br/>
  `이러한 match와 search 매서드는 문자열의 처음부터 검색할지 말지의 여부에 따라서 다르게 사용해야 한다.`

  `match, search는 정규식과 매치될 때는 match된 객체를 돌려주고, match되지 않을 때는 None을 돌려준다. `

<br/>

- **re.findall(patterns, string[, flags])** : 정규식과 매치되는 모든 문자열(substring)을 리스트로 돌려준다.

  ```python
  result = p.findall("life is too short")
  print(result)
  # ['life', 'is', 'too', 'short']
  # "life is too short 문자열의 각 단어르르 각각 [a-z]+ 정규식과 match해서 리스트로 반환한다.
  ```

  <br/>

- **re.finditer(patterns, string[, flags])** : 정규식과 매치되는 모든 문자열(substring)을 반복 가능한 객체로 돌려준다.

  ```python
  result = p.finditer("life is too short")
  print(result)
  <callable_iterator object at 0x01F5E390>

  for r in result: print(r)

  <_sre.SRE_Match object at 0x01F3F9F8>
  <_sre.SRE_Match object at 0x01F3FAD8>
  <_sre.SRE_Match object at 0x01F3FAA0>
  <_sre.SRE_Match object at 0x01F3F9F8>

  # finditer는 findall과 동일하지만 그 결과로 반복이 가능한 객체(iterator object)를 반환해준다. 반복 가능한 객체가 포함하는 각 각의 요소는 match의 객체이다.
  map(lambda x: x.group(), result) 로 작성을 해주면, for-loop을 활용해서 각각의 객체를 출력할 수 있다.
  ```

- **re.compile(pattern[, flags])** : 주어진 정규식 pattern을 compile해서 재사용 가능한 객체로 만든다.
- **re.split(patterns, string[,maxsplit=0])**
- **re.sub(pattern, repl, string[, count])** : sub(stands for substitution) 주어진 문자열을 pattern으로 평가해서 match되는 요소에 두 번째 인자로 넣어준 function(or lambda)를 적용한다.

  ```python
  import re

  def square(match):
    number = int(match.group(0))
    return str(number**2)

  print(re.sub(r"\d+", square, "1 2 3 4 5 6 7 8 9"))

  #output : 1 4 9 16 25 36 49 64 81
  ```

## Section3) Lambda Function (람다함수)

- 익명함수 (이름없는 함수)
- 간단한 수식을 함수로 지정해서 한 두번 쓸 용도로 사용
- 두 줄 이상 실행되는 함수는 별도로 함수로 선언해서 사용
- 코드의 가독성(Readibility)와 재사용성 고려하여 사용

### Section3-1) **Traditional function**

```python
# 최근 python에서는 parameter type과 return type을 지정해 줄 수 있다.
def get_next_integer(i:int -> int):

def get_next_integer(i):
  return i + 1

print(get_next_integer(10)) # 11
```

### Section3-2) **lambda - lambda function**

```python
# (lambda function)(argument)
(lambda a: a+1)(10) # 11
(lambda a,b: a+b+1)(10,11) # 22
```

### Section3-3) **map, filter, reduce의 사용**

- map의 사용

  - map의 기본 format
    ```python
    map(function, iter)
    # list의 각 element에 대해 특정한 함수를 적용
    ```
  - 사용예시

  ```python
  numbers = [i for i in range(1, 10+1)]

  def int_plus_one(a):
    return a + 1
  list(map(int_plus_one, numbers))

  # lambda function
  list(map(lambda a:a+1, numbers))
  ```

  **map은 generator를 생성해서 함수와 인자를 바인딩하고, 필요할 때 순차적으로 처리**

  ```python
  def do_square(num):
    return num ** 2

  list(map(do_square, numbers))
  ```

  ```python
  list(map(lambda a:a**2, numbers))
  ```

  `수업시간에 map의 사용까지 (2020/02/02)`

## Section4) Variable & Function in memory

- Lambda Function의 경우에는 힙(heap)에 저장이 되고 사용이 된다. 한 번 사용되고 제거된다. (일회성)
  Python은 Garbage collector를 가지고 있기 때문에 힙(Heap)의 메모리를 자동으로 관리해준다.
- 일반적으로 Function의 선언의 경우 스택(Stack)에 저장이 되고 사용이 된다.
  (FILO(First-In-Last-Out), LIFO(Last-In-First-Out))
