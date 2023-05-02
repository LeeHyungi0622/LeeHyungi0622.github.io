---
title: 210120 Python TIL - Type casting과 String관련 내장함수 그리고 String formatting
date: 2021-01-20 16:37:42
tags:
  - Python
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

# **Python**

## **오늘 배운내용**

- 형변환(Type Casting) :
  변환하고자 하는 변수를 변환하고자하는 타입으로 감싸주면 형 변환이 된다.

  - string
  - list
  - eval() :
    매개변수로 받은 문자열 값을 알맞은 형으로 변환

    - ex) eval("2") => 2 (integer type)
    - 매개변수로 받은 수식을 문자열로 받아서 실행
      - ex) eval("1+2") => 3

    <!-- more -->

- 외부입력(User input)
  - input() :
    input의 인자로 문자열을 넣을 수 있다. 이는 사용자로부터 입력을 받을때 instruction역할을 한다. 입력받은 값은 기본적으로 str타입이다. 필요에 따라 입력과 동시에 형 변환을 할 수 있다.
- index를 활용하여 문자열 slicing
  - 문자열의 index의 범위를 지정하여 slicing할 수 있다.
    - ex) "Fastcampus"[4] => 'c'
    - ex) "Fastcampus"[2:5+1] => 'stca'
    - ex) "Fastcampus"[-2:-7:-2] => 'umc'
    - ex)
      ```python
         target = 'Fastcampus'
         target[:1] #'F' 마지막 인덱스 -1 만큼의 범위까지 문자열을 자른다.
         target[2:] #두번째 인덱스부터 끝까지 문자열을 자른다.
      ```
- 내장함수를 활용 (string)

  ```python
    sentence = 'Python is very easy, powerful and awesome LANGUAGE, I think.'
    word_list = ['F', 'a', 's', 't', 'c', 'a', 'm', 'p', 'u', 's']
    target = '   ....,,,,hello, python....,,fastcampus,,    '
  ```

  위의 sentence 변수를 활용하여 아래 내장함수의 활용을 이해한다.

  - count() :

  ```python
    sentence.count('y') # 주어진 문자열 내에 문자 'y'가 몇 번 등장하는지 체크 (3번)
    sentence.count('very easy') # 주어진 문자열내에 문자열 'very easy'가 몇 번 등장하는지 체크 (1번)
  ```

  - find() :

  ```python
    sentence.find('is') # 주어진 문자열내에서 'is'가 몇 번째 index부터 시작되는지 체크 (7번 인덱스부터 시작)
  ```

  - join() :

  ```python
    # 배열의 요소를 전부 붙여서 하나의 문자열로 표기하기 위해서 아래와 같이 작성한다.
    ''.join(word_list) #Fastcampus 출력
    ','.join('python') #'p,y,t,h,o,n'
  ```

  - split() :

  ```python
    to_be_list = ' '.join('Fastcampus')
    to_be_list.split(' ') #space를 기준으로 문자를 나눠서 리스트화 한다. ['F', 'a', 's', 't', 'c', 'a', 'm', 'p', 'u', 's']
  ```

  - replace() :

  ```python
    sentence = sentence.replace('Python', 'Golang') # 주어진 문장 내에 있는 'Python' 문자를 'Golang'으로 교체한다.
  ```

  - strip() :

  ```python
    target.strip().strip('.').strip(',') #target 문자열에서 공백과 '.', ','를 제거한다. 문자여려 앞과 뒤에서는 제거가 되지만, 문자와 문자 사이사이에 있는 지정문자는 제거되지 않는다.
    #위의 코드를 아래와 같이 간략하게 작성할 수 있다.
    target.strip(' .,')
  ```

  - isalpha() :

  ```python
    'LeeHyungi'.isalpha() #대상 문자열이 알파벳으로 구성되어있는지 체크
  ```

  - isalnum() :

  ```python
    'LeeHyungi'.isalnum() #대상 문자열이 알파벳과 숫자 혼합으로 구성되어있는지 체크
  ```

  - isdigit() :

  ```python
    '2021'.isdigit() #대상 문자열이 숫자로만 구성되어있는지 확인
  ```

  - islower() :

  ```python
    'leehyungi'.islower() #대상 문자열이 소문자로만 구성되어 있는지 확인
  ```

  - isupper()

  ```python
    'LEEHYUNGI'.isupper() #대상문자열이 대문자로만 구성되어 있는지 확인
  ```

  - print()의 sep, end 속성활용

  ```python
    print(target[:1], 'e', target[2:], sep = '', end='python') #첫번째 문자(target[0]) 다음에 문자 'e'를 붙여주고, 그 다음에 target 문자열의 두번째 index부터 끝까지 슬라이싱한 문자를 붙여준다. sep는 빈 공백으로 해서 붙이고, end= 속성을 이용해서 문자열 맨 마지막에 'python'을 붙여준다.
  ```

- String Formatting
  - 예전 방식 :
    Python의 옛 방식으로 문자열을 formatting하게 되면 아래와 같다.
  ```python
    #C언어와 비슷하게 출력하는 문자열 내에 %s, %d 와 같은 데이터 타입를 %와 같이 붙여서 지정해주고, %('value')를 넣어서 작성해준다.
    'I have a %s, I have a %s.'%('apple', 'pen')
  ```
  - 새로운 방식 :
    Python의 String formatting을 새로운 방식으로 .format()를 사용해서 작성할 수 있다.
  ```python
    'I have a {}, I have a {}'.format('pen','apple') # 순차 formatting
    #위에서는 format에 넣어준 인자의 순서대로 값을 대입하는 방법으로 작성하였다. 다음은 인덱스를 지정해서 대입하는 방법이다.
    'I have a {1}, I have a {0}'.format('pen',10)
  ```
  ```python
    # >, < , ^ (왼쪽에 space에 채워줄 문자를 작성해주고, 오른쪽에는 문자열의 최대길이를 설정해준다.)
    # >, < 각각 부등호 반대방향으로 문자열이 정렬되며, ^는 가운데 정렬을 한다.
     print('I have a {1:_>10}, I have a {0}'.format('pen','apple'))
    print('I have a {1:*^10}, I have a {0}'.format('pen','apple'))
    print('I have a {1:-<10}, I have a {0}'.format('pen','pineapple'))
    # 출력내용
    # I have a _____apple, I have a pen
    # I have a **apple***, I have a pen
    # I have a pineapple-, I have a pen
  ```

## **간단한 연습문제**

오늘 배운 전반적인 내용을 활용하여 아래 문제를 해결한다.

1. 사용자가 입력한 전화번호를 저장하려합니다. 전화번호의 구분자로 - 또는 `를 혼합하여 사용할 때 이를 split과 join을 이용하여-`로 통일하여 입력 받아 결과물을 출력하세요.

2. 사용자가 다음과 같은 입력을 할때, 그 자료가 알파벳 단독인지, 숫자 단독인지, 알파벳 숫자인지, 소문자인지, 대문자인지를 formating 하여 출력하세요.

```python
FAST
campus12
FastCampus
1107
dynamite
```

## **문제풀이**

```python

"""
campus12
FastCampus
1107
dynamite
"""

user_input = input("Type some words: ")
"{} is {},{},{},{},{}".format(
    user_input,
    user_input.isalpha(),
    user_input.isdigit(),
    user_input.isalnum(),
    user_input.islower(),
    user_input.isupper()
)

```
