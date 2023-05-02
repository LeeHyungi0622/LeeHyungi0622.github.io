---
title: 210121 Python TIL - List / List관련 내장함수 + Tuple / Destructuring assignment
date: 2021-01-21 22:37:42
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

- List (리스트)

  - 리스트 작성예시
    ```python
    fastcampus = [2.71828, 3.1415]
    zoo = ['rabbit','dog','cat']
    ```
  - 리스트관련 내장함수

    ```python
      zoo = ['rabbit', 'dog', 'cat']
    ```

    <!-- more -->

    - append()
      ```python
        zoo.append('owl')
        # ['rabbit', 'dog', 'cat', 'owl']
      ```
    - insert()
      ```python
        zoo.insert(2, 'horse')
        # ['rabbit', 'dog', 'horse' 'cat', 'owl']
      ```
    - remove()

    ```python
      zoo.remove('dog')
      # ['rabbit', 'horse' 'cat', 'owl']
    ```

    - pop()

    ```python
      # 아무 인자도 주지 않는다면, 리스트의 가장 끝 요소가 제거된다.
      zoo.pop(2) # 리스트의 index는 0부터 시작하기 때문에, 3번째 요소가 제거된다.
      # 만약에 index는 알지 못하지만 값을 알고 있는 경우, index()를 활용하여 index를 찾을 수 있다.
      zoo.pop(zoo.index('cat'))
    ```

    - remove(): pop()의 경우에는 index값으로 리스트의 값을 제거했지만, remove()의 경우에는 값으로 접근해서 값을 제거한다.

    ```python
      zoo.remove('cat')
    ```

    - sort()

    ```python
      zoo.sort() #zoo리스트에 있는 요소들을 오름차순으로 정렬한다.
      #내림차순으로 정렬하기 위해서는 sort()함수의 속성으로 reverse=를 사용하면 된다.
      zoo.sort(reverse=True)
    ```

    - reverse()

    ```python
      zoo.reverse() # zoo리스트의 요소를 거꾸로 정렬한다.
    ```

    - +, extend() :
      서로다른 두 개의 리스트를 접붙이기 위해서 사용된다.

    ```python
      numbers1 = [1, 2]
      numbers2 = [-1, -2]
      number1 + number2 # [1, 2, -1, -2]
      number1.extend([3, 4]) # [1, 2, 3, 4]
    ```

    - 문자열 (not) in 리스트

    ```python
      zoo = ['rabbit', 'owl', 'horse', 'aligator']
      'dog' in zoo # False
      'owl' in zoo # True
      'dog' not in zoo # True
    ```

- Tuple (튜플) :
  immutable 자료형으로 리스트와 달리 기존에 선언한 데이터를 수정할 수 없다.

  - 튜플은 index로 값에 접근할 수 있다.

  ```python
    grade = (90, 0, 16, 40)
    grade[1] # 0
  ```

  - 튜플 형태로 Destructuring Assignment를 할 수 있다.

  ```python
    def split_hello(word):
      result = word.split()
      return result[0], result[1]

    (greet, word) = split_hello('hello world')

    greet # 'hello'
    word # 'world'
  ```

  - 튜플을 활용하여 두 변수를 swapping할 수 있다.

  ```python
    (greet, word) = (word, greet)
  ```

  - 튜플을 리스트로, 리스트는 튜플로 type casting이 가능하다.

## 간단한 연습문제

### Practice(1)

> Practice(1)
> list와 tuple을 이용하여 다음의 문제를 해결하세요.<br/>

1. 변수 animals에 다음의 자료를 입력하세요. rabbit, cat, dog, aligator, tiger, lion<br/>
2. animals에 새로운 동물을 3마리 추가하세요.(insert, append, extend 꼭 쓸것)<br/>
3. animals를 알파벳 내림차순으로 정렬하세요.<br/>
4. 정렬된 animals의 짝수(index 기준) 동물을 even_animals라는 tuple에 할당하세요.

```python
animals = ['rabbit', 'cat', 'dog', 'aligator', 'tiger', 'lion']

animals.insert(3, 'kangaroo') # ['rabbit', 'cat', 'dog', 'kangaroo', 'aligator', 'tiger', 'lion']
animals.append('puma') # ['rabbit', 'cat', 'dog', 'kangaroo', 'aligator', 'tiger', 'lion', 'puma']
animals.extend(['cheetah'])
"""
['rabbit',
 'cat',
 'dog',
 'kangaroo',
 'aligator',
 'tiger',
 'lion',
 'puma',
 'cheetah']
"""

animals[::2] # 리스트에서 짝수번째 있는 동물들을 출력한다.
animals[1::2] # 홀수번째 있는 동물들을 출력한다.
even_animals = tuple(animals[::2])

```
