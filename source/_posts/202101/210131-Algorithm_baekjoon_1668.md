---
title: Baekjoon Online Judge 1668번 트로피 진열문제
date: 2021-01-31 10:27:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 1668번 트로피 진열문제 Pseudo code + Python code

![](/images/post_images/210131_baekjoon_1668.png)

**Solution1)**

```python
n = int(input())
height_list = []

for _ in range(n):
  t_height = int(input())
  height_list.append(t_height)

left_max_height = 0
left_view_count = 0
for h in height_list:
  if h > left_max_height:
    left_max_height = h
    left_view_count += 1
  else:
    break

right_max_height = 0
right_view_count = 0
for h in reversed(height_list):
  if h > right_max_height:
    right_max_height = h
    right_view_count += 1

print(left_view_count, right_view_count, sep='\n')
```

  <!-- more -->

**Solution2) 함수로 공통화처리 하기**

```python
def ascending(array):
    now = array[0]
    result = 1
    for i in range(1, len(array)):
        if now < array[i]:
            result += 1
            now = array[i]
    return result


n = int(input())
array = []

for _ in range(n):
    array.append(int(input()))

print(ascending(array))
array.reverse()
print(ascending(array))
```

이 문제는 일렬로 정렬되어있는 트로피들을 왼쪽, 오른쪽 두 측면에서 보았을때 보이는 트로피의 갯수를 구하는 문제이다. 각 측면에서 트로피의 높이를 저장하고 있는 리스트를 순회하면서 최대 높이를 비교하며 카운트해주면 되는 간단한 문제이다.

## ![](/images/post_images/210124_developer.jpg)
