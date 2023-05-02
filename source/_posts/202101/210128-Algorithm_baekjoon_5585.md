---
title: Baekjoon Online Judge 5585번 거스름돈 문제
date: 2021-01-28 10:38:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 5585번 거스름돈 문제 Pseudo code + Python code

![](/images/post_images/210128_baekjoon_5585.png)

```python
product_price = int(input())
coin_unit = [1, 5, 10, 50, 100, 500]
coin_unit.sort(reverse=True)
changes = 1000 - product_price
coin_count = 0
for unit in coin_unit:
  if changes >= unit:
    num_of_coin = changes // unit
    changes -= unit * num_of_coin
    coin_count += num_of_coin
print(coin_count)
```

  <!-- more -->

이 문제는 대표적인 그리디 알고리즘 문제 유형으로, 최소한의 동전 갯수를 사용해서 거스름돈을 만드는 문제이다.
사용될 동전의 종류를 저장하고 있는 리스트를 역정렬한 뒤에, 해당 리스트를 순회하면서 사용될 동전의 갯수를 카운트하면 되는 간단한 문제이다.

## ![](/images/post_images/210124_developer.jpg)
