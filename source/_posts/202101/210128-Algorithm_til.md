---
title: 210128 List와 Tuple 자료형을 이용한 정렬
date: 2021-01-28 13:48:42
tags:
  - Self-Development
  - Python
  - List
  - Tuple
  - Sorting
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

![](/images/post_images/self_development_logo.jpg)

# **오늘 공부한 내용**

리스트와 튜플을 활용한 정렬에 대해서 정리해보려고 한다. 코딩테스트 문제에서 자주 활용할 수 있는 부분이기 때문에 사용에 대해서 숙지해두면 좋다.
<br/>

- ## **Tuple sorting**

```python
v = [(3,4),(2,2),(3,3),(1,2),(1,-1)]
```

- 리스트 v를 튜플의 첫 번째 원소를 기준으로 오름차순 정렬하기

```python
v.sort(key = lambda x : x[0])
print(v)
# [(1, 2), (1, -1), (2, 2), (3, 4), (3, 3)]
```

  <!-- more -->

- 리스트 v를 튜플의 첫 번째 원소로 내림차순 정렬하기

```python
# 방법 1
v.sort(key = lambda x : -x[0])
print(v)
# [(3, 4), (3, 3), (2, 2), (1, 2), (1, -1)]

# 방법 2
v.sort(key = lambda x : x[0], reverse=True)
print(v)
# [(3, 4), (3, 3), (2, 2), (1, 2), (1, -1)]
```

- 리스트 v를 튜플의 두 번째 원소로 오름차순 정렬하기

```python
v.sort(key=lambda x : x[1])
print(v)
#[(1, -1), (2, 2), (1, 2), (3, 3), (3, 4)]
```

- 리스트 v를 튜플의 첫번째 원소로 오름차순 정렬하고, 첫 번째 원소가 같은 경우, 두 번째 원소로 오름차순 정렬하기

```python
v.sort()
# [(1, -1), (1, 2), (2, 2), (3, 3), (3, 4)]

v.sort(key=lambda x : (x[0],x[1]))
# [(1, -1), (1, 2), (2, 2), (3, 3), (3, 4)]
```

- 리스트 v를 튜플의 첫번째 원소로 내림차순 정렬하고, 첫 번째 원소가 같은 경우, 두 번째 원소로 오름차순 정렬하기

```python
v.sort(key=lambda x:(-x[0],x[1]))
print(v)
# [(3, 3), (3, 4), (2, 2), (1, -1), (1, 2)]
```
