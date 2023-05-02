---
title: Baekjoon Online Judge 2212번 센서문제
date: 2021-02-01 10:23:42
tags:
  - Algorithm
  - Greedy-Algorithm
  - Sort
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 2212번 센서문제 Pseudo code + Python code

![](/images/post_images/210201_baekjoon_2212.png)

```python
sensor_num = int(input())
station_num = int(input())
sensor_loc_list = list(map(int, input().split()))
sensor_loc_list.sort()
distance = []
for i in range(1, sensor_num):
  distance.append(sensor_loc_list[i]-sensor_loc_list[i-1])
distance.sort(reverse=True)
for i in range(station_num-1):
  distance[i] = 0
print(sum(distance))
```

  <!-- more -->

이 문제는 설치하고자 하는 수신 기지국의 전파 수신 범위를 최소로 하기 위해서는 어떻게 수신 기지국을 설치를 하며, 최소한의 수신 범위는 어떻게 되는지 구하는 문제이다.
고속도로 위의 각 센서의 설치 위치에 대해서 오름차순으로 정렬을 하고, 각 센서간의 사이 거리에 대한 값을 별도의 리스트에 담아 내림차순으로 정렬하였다. 이는 최소 수신 범위를 만들기 위해서 가장 큰 수신 범위를 제외하기 위해서 이다. 내림차순으로 정렬하여 기지국 N개를 설치할때 기지국의 수신구역을 나누는 분기점의 갯수 N-1개를 센서 사이 거리에 대한 리스트에서 순차적으로 0으로 초기화 시켜주면 된다.

## ![](/images/post_images/210124_developer.jpg)
