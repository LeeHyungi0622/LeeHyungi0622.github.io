---
title: Baekjoon Online Judge 2747번 피보나치 수 문제 (다양한 방법으로 풀기)
date: 2021-02-16 18:00:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 2747번 피보나치 수 문제 Pseudo code + Python code

![](/images/post_images/210217_fibonacci.png)

이 문제는 피보나치 수를 구하는 문제로, for-loop와 memoization, recursive 개념을 활용해서 풀이를 해보았다.
피보나치 수열의 값은 이미 계산된 값들의 누적 합으로 구성이 된다. 따라서 이미 계산된 부분은 별도의 변수에 값을 저장했다가 필요할때 참조하게 되면 빠르게 원하는 값을 도출해낼 수 있다.(fibonacci-memoization)

![](/images/post_images/210216_baekjoon_2747.png)

`손코딩으로 작성한 코드 중 for-loop로 풀이한 부분의 초기리스트로 [1,1]이 아닌 [0,1]로 초기화를 시켜줘야 한다.(수정)`

  <!-- more -->

```python
# for-loop
n = int(input())
num_list = [0, 1]
for i in range(2, n+1):
    num_list.append(num_list[i-1]+num_list[i-2])
print(num_list[-1])

# memoization
__fibo_memo = {}

def fibo(n):
    if n in __fibo_memo:
        return __fibo_memo[n]
    else:
        __fibo_memo[n] = n if n < 2 else fibo(n-1) + fibo(n-2)
        return __fibo_memo[n]

# recursive function
def fibo(n):
    return n if n < 2 else fibo(n-1) + fibo(n-2)

print(fibo(5))
```

## ![](/images/post_images/210124_developer.jpg)
