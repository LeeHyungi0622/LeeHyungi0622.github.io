---
title: Baekjoon Online Judge 10989번 수 정렬하기3 문제 (시간/공간 복잡도 조건문제)
date: 2021-01-28 12:46:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 10989번 수 정렬하기3 문제 Pseudo code + Python code

> **[시간 제한] 시간복잡도 고려**
> Java 8: 3 초
> Java 8 (OpenJDK): 3 초
> Java 11: 3 초
> Kotlin (JVM): 3 초

> **[메모리 제한] 공간복잡도 고려**
> Java 8: 512 MB
> Java 8 (OpenJDK): 512 MB
> Java 11: 512 MB
> Kotlin (JVM): 512 MB

![](/images/post_images/210128_baekjoon_10989.png)

```python
n = int(input())
counting_sort_list = [0] * 10001

for _ in range(n):
  num = int(input())
  counting_sort_list[num] += 1

for i in range(1, 10001):
  for _ in range(counting_sort_list[i]):
    print(i)
```

  <!-- more -->

이 문제는 계수정렬(Counting sort)을 사용하여 입력한 숫자를 오름차순으로 정렬하는 문제이다. <br/>
수의 갯수가 최대 10,000,000개이므로, 반복문을 통해서 값을 입력받게 되면, 해당 문제의 조건에서 초과된 시간으로 실행이 된다. <br/>
계수정렬은 시간복잡도가 O(N)이기 때문에 문제에서 조건으로 제시한 시간 내에 실행이 가능하다.

## ![](/images/post_images/210124_developer.jpg)
