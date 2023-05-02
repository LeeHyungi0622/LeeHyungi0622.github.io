---
title: 210119 Python TIL - Python의 기본 자료형과 내장함수
date: 2021-01-19 16:37:42
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

오늘 배운내용

- Python의 기본 자료형
  - string
  - list
  - number
  - bool
  - int
  - float
- Python의 내장함수
  - print()
  - type()
  - round() : 반올림하고자 하는 값과 소숫점이하 N번째 자리까지 수를 표시하기 위해서 인자 N을 넣어준다.
- Python의 숫자 자료형과 연산
  - 사칙연산(+,-,\*,/)
  - // (몫 구하는 연산자), % (나머지 구하는 연산자)
  - \*\* (지수 연산자)
- Python 변수의 데이터 타입검사
  - 변수의 데이터 타입검사는 type() 내장함수를 사용하여 체크 할 수 있다.
- for-loop를 활용한 반복연산

  - for - in - range() 구문을 활용하여 일정 구간에 있는 숫자를 순회하며 처리할 수 있다.

  <!-- more -->

<br/>

### **간단한 연습문제**

오늘 배운 전반적인 내용을 활용하여 아래 문제를 해결한다.

> 반지름(r=10)을 선언한 뒤, 이를 이용하여 원의 지름, 둘레, 넓이, 구의 겉넓이, 부피를 각각 출력하는 파이썬 파일을 만들어보세요.(pi=3.1415)

**sample output**

r(반지름)의 길이가 10일때의 출력 sample

```python
r = 10 ==> print("r =", r)
d = 20
c = 62.830
a = 314.15
gnb = 1256.0000
v = 4188.666666666667
```

## **문제풀이**

```python
# 반지름과 pi값을 선언한다.
r = 10
pi = 3.1415

#  d: 지름, c: 둘레, a: 넓이
# gnb: 구의 넓이, v: 부피
d = 2 * r
c = 2 * pi * r
a = pi * r ** 2
gnb = 4 * pi * r ** 2
v = 4 / 3 * pi * r ** 3

print("r =", r)
print("c =", round(c, 4))
print("a =", a)
print("gnb =", round(gnb, 2))
print("v =", round(v,4))
```
