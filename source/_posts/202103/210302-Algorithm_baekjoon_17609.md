---
title: Baekjoon Online Judge 17609번  회문/유사회문 문제 (다각도에서 문제 바라보기)
date: 2021-03-02 12:09:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 17609번 회문/유사회문 문제 Pseudo code + Python code

<ins><b>문제풀이 접근 방식 : 재귀호출</b></ins>

### Pseudo code #1 RecursionError

![](/images/post_images/210302_baekjoon_17609_recursive-method-solution.png)

### 본 코드 #1

```python
shift_flag = False


def palindrome(input_str):
    global shift_flag
    if len(input_str) <= 1:
        if shift_flag:
            return 1
        return 0

    if input_str[0] != input_str[-1]:
        if input_str[0] == input_str[-2]:
            shift_flag = True
            input_str = input_str[0:-1]
        elif input_str[1] == input_str[-1]:
            shift_flag = True
            input_str = input_str[1:]
        else:
            return 2

    return palindrome(input_str[1:-1])


n = int(input())
for _ in range(n):
    input_str = input()
    print(palindrome(input_str))
```

  <!-- more -->

첫 번째 재귀함수를 사용한 풀이 방법에는 문제가 있다. 파이썬의 최대 재귀 깊이(maximum recursion depth)가 1000으로 정해져 있다.
따라서 문제의 조건 `주어지는 문자열의 길이는 3 이상 100,000 이하이고, 영문 알파벳 소문자로만 이루어져 있다.`라는 조건에 있어서 최장 문자열의 길이에 대한 적절한 해결책이 되지 못한다.

최소한으로 재귀호출을 해서 문제를 해결해야 한다.

### Pseudo code #2 최소 재귀호출

유사 재귀호출의 경우에만 재귀호출 형태로 함수를 호출하고 회문인 경우에는 조건처리가 끝나면 자동으로 회문인 것으로 간주한다.

![](/images/post_images/210302_baekjoon_17609_less_recursive-method-solution.png)

### 본 코드 #2

```python
# 유사회문 검사 function
def inner_palindrome(input_str, start, end):
    while start < end:
        if input_str[start] == input_str[end]:
            start += 1
            end -= 1
        else:
            return False
    return True

def palindrome(input_str, start, end):
    while start < end:
        if input_str[start] == input_str[end]:
            start += 1
            end -= 1
        else:
            fc = inner_palindrome(input_str, start, end-1)
            sc = inner_palindrome(input_str, start+1, end)
            #  fc와 sc 둘 중에 하나를 만족한다면, 유사 회문이기 때문에 값 1을 반환해준다.
            if fc or sc:
                return 1
            else:
                return 2
    return 0


test_case = int(input())
for _ in range(test_case):
    input_str = input()
    print(palindrome(input_str, 0, len(input_str)-1))
```
