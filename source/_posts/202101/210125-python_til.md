---
title: 210125 Python TIL - 삼항연산자(Ternary operator)와 for-loop, while-loop
date: 2021-01-25 22:37:42
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

## **오늘 배운 내용**

- Python 과제 점검
  - 21년 1월 3주차 파이썬 과제 풀이 정리하기
- Python 메인 강의
  - 과제점검
  - 삼항연산자 (Ternary operator)
  - Iteration(Loop)
    - for
    - while
  - 문자열 \* True / 문자열 \* False
  - break, continue in conditional statement

<br/>

## **21년 1월 3주차 파이썬 과제 풀이 정리하기**

기본적으로 문제풀이에서 요구하는 것은 isupper()와 islower()를 사용하여 주어진 문자열, 배열의 값을 소문자는 대문자로, 대문자는 소문자로 토글하는 문제였다.

아래는 내가 작성한 문제의 답으로, solution1에서는 isupper()와 islower()를 사용해서 입력된 값을 조건처리하여 작성하였다.
solution2에서는 입력된 값의 조건처리를 알파벳의 ASCII 코드 값을 활용하여 접근하였다.

solution1과 solution2에서 공통적으로 입력된 값이 문자열인지 리스트인지 구분하여, 문자열인 경우 문자열의 값을 리스트화하고, 출력을 할때에도 입력된 값이 문자열인 경우와 그렇지 않은 경우를 구분하여 출력하는 구문도 조건처리하였다.

  <!-- more -->

```python
# solution 1) isupper(), islower()를 활용
# string이 list로 convert되었는지 구분하기 위한 flag변수
flag = False
iv = ['A', 'b', 'C']
# iv = "AbCdEf"
# 만약 입력한 값이 문자열인 경우, 리스트로 변환하고 flag변수를 True로 변환한다.
if type(iv) == str:
    iv = [w for w in iv]
    flag = True
for idx, w in enumerate(iv):
    if w.isupper():
        iv[idx] = w.lower()
    else:
        iv[idx] = w.upper()
if flag:
    print(''.join(iv))
else:
    print(iv)



# solution 2) ASCII CODE를 활용
# string이 list로 convert되었는지 구분하기 위한 flag변수
flag = False
iv = ['A', 'b', 'C']
# iv = "AbCdEf"
# 만약 입력한 값이 문자열인 경우, 리스트로 변환하고 flag변수를 True로 변환한다.
if type(iv) == str:
    iv = [w for w in iv]
    flag = True
for idx, w in enumerate(iv):
    # w의 ASCII 코드가 65이상 90이하인 경우 (대문자의 경우)
    if ord(w) >= 65 and ord(w) <= 90:
        iv[idx] = chr(ord(w)+32)
    else:
        iv[idx] = chr(ord(w)-32)
if flag:
    print(''.join(iv))
else:
    print(iv)
```

# **Python 메인 강의**

## **과제 점검**

(1) 기존의 Numguess 코드에 추가할 요소에 대해서 생각해보기

(2) Hacker Rank문제 풀이 (2문제)

- https://www.hackerrank.com/challenges/py-if-else/problem
- https://www.hackerrank.com/challenges/find-second-maximum-number-in-a-list/problem

**Hacker Rank 두번째 문제 풀이 참고 :** 리스트(list)를 집합(set)으로 변환해서 중복을 제거하고 sort와 sort의 reverse= 속성을 활용해서 내림차순으로 정렬을 한다.

**삼항연산자(Ternary operator)**

- Conditional Expressions
  ```python
  # a if condition else b
  True if 3>2 else False
  True if 3<2 else False
  ```
  ```python
  # a if condition else b if condition else c (다중 if문)
  # 2~3개의 조건은 삼항연산자로 작성해도 괜찮지만, 그 이상 작성을 하면, 가독성이 떨어진다.
  'a' if 3<2 else 'b' if 4>3 else 'c'
  ```
  삼항연산자를 사용해서 직접적으로 변수의 값을 초기화 해 줄 수 없다.
  ```python
  result = 'pass' if 3>2 else result = 'Non-pass' #Error
  ```

---

## **Practice(1)**

### 내가 작성한 코드 (코드 점검하기)

```python
day = input("요일을 입력하세요 : ")


# conditional statement
day_list = ['월', '화', '수', '목', '금', '토', '일']

if day in day_list[:5] or day.replace("요일", "") in day_list[:5]:
    print("평일이네요ㅜㅜ")
elif day in day_list[5:] or day.replace("요일", "") in day_list[5:]:
    print("주말입니다^^")
else:
    print("요일을 입력하세요")


# conditional express
print("평일이네요ㅜㅜ") if day in day_list[:5] or day.replace("요일", "") in day_list[:5] else \
print("주말입니다 ^ ^") if day in day_list[5:] or day.replace("요일", "") in day_list[5:] else \
print("요일을 입력하세요")
```

### 강사님이 작성하신 코드 솔루션

```python
weekday = ('월','화','수','목','금')
weekend = ('토','일')
```

튜플의 형태로 평일과 주말을 분류하고, 입력한 문자열이 각각의 튜플의 내부에 있는지 검사해서 출력한다.

---

## **For**

```python
for 변수 in (리스트 or 문자열):
	    실행문1
    ...
```

```python
for i in ["python", "java", "golang"]:
	print(i)
```

```python
# 복수 변수는 변수이름을 명명할때 복수로 명사로 선언한다.
animals = ['cat', 'dog', 'aligator', 'tiger', 'lion']
for animal in animals:
    print('There is {} in cage.'.format(animal))
```

```python
# 1부터 N까지의 합을 구하기
# (수식 : n(n+1)/2) - O(1)
# For-loop를 사용한 풀이 -  O(N)
# 가독성을 높이기 위해 101이 아닌 100+1의 형태로 숫자를 작성해준다.
for i in range(1, 100+1):
    result += i
print(result)
```

## 시간복잡도 이해

> 만약에 위의 문제를 for-loop를 사용해서 해결하지 않고, n(n+1)/2라는 수식으로만 해결을 한다면, 빅오 표기법으로 <i>O(1)</i>의 시작 복잡도를 갖는다. 반면에 for-loop를 사용해서 해결을 한다면, N번만큼 반복해야되므로 빅오 표기법으로 <i>O(N)</i>만큼의 시간복잡도를 갖는다.

## **While**

```python
while 조건:
	실행문1
	...
```

```python
while True:
    message = input('Type anything: ')
    if message == 'exit':
        break
    print(message)
```

```python
import random

magic_number = random.randint(1,20)
print("Stop at ", magic_number)
for i in range(1, 100+1):
    if i == magic_number:
        break
    elif i%2==0:
        print('skip for now')
        # continue는 다음 index에 대해서 새로운 cycle을 시작하라는 의미이다.
        continue
    print(i)
```

**continue는 현재 실행한 cycle을 끝내고 다음 cycle로 시작하고, break는 구문(for-loop)을 끝낸다.**

## **Practice(2)**

### 내가 작성한 코드

**1번 문제)**

```python
num = int(input())
result = 1
while num > 0:
    result *= num
    num -= 1
```

**2번 문제)**

```python
num_list = [[1, 0, 7, 0], [0, 2, 0, 9], [0, 6, 3, 7], [4, 0, 5, 1],]
sum = 0
for i in range(len(num_list)):
    for j in range(len(num_list[i])):
        sum += num_list[i][j]
print(sum)
```

### 강사님이 작성하신 코드 솔루션

```python
user_num = int(input('Enter just one number(2-100): '))
print(user_num, type(user_num))
result = 1
for i in range(1, user_num+1):
    result *= i
print(result)
```

강사님이 올려주신 파일보고, 코드 다시 점검하기

```python
lists_in_list = [
    [1, 0, 7, 0],
    [0, 2, 0, 9],
    [0, 6, 3, 7],
    [4, 0, 5, 1],
]

final_result = 0
for list_ in lists_in_list:
    result = 0
    for item in list_:
        result += item
    final_result += result
print(final_result)

# 출력값
# 8
# 11
# 16
# 10
# 45

```

```python
# 위의 코드를 Refactoring하기
for list_ in lists_in_list:
    # sum함수를 사용함으로써 for-loop를 하나 생략할 수 있다.
    print(sum(list_))

# [1, 0, 7, 0]
# [0, 2, 0, 9]
# [0, 6, 3, 7]
# [4, 0, 5, 1]
```

```python
result = 0
for list_ in lists_in_list:
    result += sum(list_)
print(result)

# 45
```

<br />

## **Fizzbuzz 문제**

내가 작성한 코드

```python
for i in range(1, 100+1):
    if i % 15 == 0:
        print("FizzBuzz")
    elif i % 3 == 0:
        print("Fizz")
    elif i % 5 == 0:
        print("Buzz")
    else:
        print(i)
```

### **강사님이 작성하신 코드 솔루션**

Python에서는 True*"string" = string이고, False*"string" = ''이다. bool 값을 문자열에 곱해서 해당 문자열을 표시할 수도 표시를 안할 수도 있다.

연산속도를 생각하면, 연산을 통한 값 출력이 더 빠르다는 것을 알 수 있다.

```python
    for i in range(1, 30+1):
        if i%3==0 or i%5==0:
            print('fizz'*(i%3==0) + 'buzz'*(i%5==0))
        else:
            print(i)
```

## **Numguess**

```python
    import random


answer = random.randint(1, 100)
print('DEBUG: {}'.format(answer))
chance = int(input('Choose level(10-easy 1-hell): '))

while chance > 0:
    guess = int(input('Guess the number(1-100): '))
    print(guess, type(guess))
    if guess-answer == 0:
        print('Correct')
        break
    elif guess < answer:
        msg = 'Up!'
    elif guess > answer:
        msg = 'Down!'
    chance -= 1
    print('{} You have {} life(s).'.format(msg, chance))
    if chance == 0:
        print('Wrong! The answer was {}'.format())
```
