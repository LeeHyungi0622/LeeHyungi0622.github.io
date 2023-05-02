---
title: Baekjoon Online Judge 10825번 국영수 문제
date: 2021-03-05 10:28:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 10825번 국영수 문제 Pseudo code + Python code

<ins><b>문제풀이 접근 방식 : 튜플과 리스트 자료형을 사용한 정렬</b></ins>

### Pseudo code

![](/images/post_images/210305_baekjoon_10825.png)

### 본 코드 (통과/PyPy3 - 231312KB / 3372ms) (시간초과/Python3)

시간초과가 발생하는 경우 PyPy3를 사용해서 코드를 테스트해보도록 하자.
이 시점에서 간단하게 PyPy3에 대해서 살펴보자.

```python
n = int(input())
student_list = []

for _ in range(n):
    (name, korean, english, math) = input().split(' ')
    student_list.append((name, eval(korean), eval(english), eval(math)))

sorted_list = sorted(student_list, key=lambda x: (-x[1], x[2], -x[3], x[0]))

for student in sorted_list:
    print(student[0])
```

  <!-- more -->

### sys.stdin.readline() (통과/Python3 - 60320KB / 1820ms)

이 시점에서 sys.stdin.readline()에 대해서 알아보자.

```python
import sys

n = int(sys.stdin.readline())
student_list = []

for _ in range(n):
    (name, korean, english, math) = sys.stdin.readline().split(' ')
    student_list.append((name, eval(korean), eval(english), eval(math)))

sorted_list = sorted(student_list, key=lambda x: (-x[1], x[2], -x[3], x[0]))

for student in sorted_list:
    print(student[0])
```
