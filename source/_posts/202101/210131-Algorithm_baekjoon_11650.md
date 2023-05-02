---
title: Baekjoon Online Judge 11650번 x, y 좌표 정렬문제
date: 2021-01-31 11:02:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 11650번 x, y 좌표 정렬문제 Pseudo code + Python code

![](/images/post_images/210131_baekjoon_11650.png)

```python
n = int(input())
axis_list = []
for _ in range(n):
  x, y = map(int, input().split())
  axis_list.append((x, y))
axis_list.sort(key=lambda x:(x[0], x[1]))
# Tuple은 정렬을 해주면, 첫 번째 요소에 대해서 오름차순 정렬을 해주고, 첫 번째 요소가 같은 경우, 두 번째 요소에 대해서 자동으로 오름차순 정렬을 해준다.
# 아래의 정렬은 위와 같은 결과를 갖는다.
# axis_list.sort()
for i in axis_list:
  print(i[0], i[1])
```

  <!-- more -->

이 문제는 리스트 내의 튜플 자료형을 활용하여 정렬을 하는 문제이다. 입력된 데이터를 순차적으로 정렬을 하는데, 첫번째 요소에 대해서 오름차순, 두번째 요소에 대해서 내림차순 등의 복합 조건 정렬을 하는 문제라면, 리스트에 입력 데이터를 튜플 자료형으로 삽입을 하여 정렬하면 유용하다.

## ![](/images/post_images/210124_developer.jpg)
