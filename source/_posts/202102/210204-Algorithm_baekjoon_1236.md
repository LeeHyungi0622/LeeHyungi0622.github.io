---
title: Baekjoon Online Judge 1236번 성 지키기 문제
date: 2021-02-05 10:06:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 1236번 성 지키기 문제 Pseudo code + Python code

![](/images/post_images/210205_baekjoon_1236.png)

손 코딩에서 이중 for문 처리하는 부분에서 수정이 필요하다. 이중 for문에서 i,j를 이용해서 내부 반복처리를 해야하는데, 손 코딩할때 n과 m을 넣어 처리를 했다. 이 부분을 i와 j로 수정해서 넣어줘야 한다.

```python
n, m = map(int, input().split())
security_in_castle_list = []
for _ in range(n):
    security_in_castle_list.append(input())
castle_row = [0]*n
castle_column = [0]*m
for i in range(n):
    for j in range(m):
        if security_in_castle_list[i][j] == 'X':
            castle_row[i] += 1
            castle_column[j] += 1
castle_zero_row = 0
for row in castle_row:
    if row == 0:
        castle_zero_row += 1
castle_zero_column = 0
for col in castle_column:
    if col == 0:
        castle_zero_column += 1
print(max(castle_zero_row, castle_zero_column))

```

  <!-- more -->

이 문제는 (n x m) 행렬로 구성된 성 내부에서 각 행과 열에 최소 한 명의 경비원을 배치하도록 하는 문제이다. 문제 해결을 위해서는 행과 열의 크기만큼 리스트를 각각 선언하여 경비원이 아예 배치되어있지 않은 행과 열의 갯수를 구해야 한다.
구해진 행과 열의 갯수 중에 최대값을 출력하면 최소 배치해야 되는 경비원의 수를 구할 수 있다.

## ![](/images/post_images/210124_developer.jpg)
