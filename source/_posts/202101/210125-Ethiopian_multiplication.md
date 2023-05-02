---
title: Ethiopian_multiplication
date: 2021-01-25 22:37:42
tags:
  - Algorithm
  - PseudoCode
categories:
  - Algorithm-problem-solving
---

## Ethiopian Multiplication Pseudo code + Python code

![](/images/post_images/210125_ethiopian_multiplication.png)

```python
m, n = map(int, input("Enter two numbers separated by space").split(' '))
result = 0
while m >= 1:
  if m % 2 != 0:
    result += n
  m //= 2
  n *= 2
print(result)
```

  <!-- more -->

Ethiopian multiplication 방식으로 입력받은 두 숫자의 곱으르 구하는 코드를 구현해보았다.

원리는 m과 n 두 숫자를 입력받고, 왼쪽의 숫자는 나눠지지 않을 때까지 계속 2로 나눠주며, 오른쪽 수는 왼쪽의 수가 나눠지지 않을때까지 2를 곱해준다.

만약에 m이 홀수인 경우, n의 값은 특정 변수에 누적해서 저장을 한다. 이 누적된 값이 최초 입력받은 m과 n의 곱의 값이 된다.
