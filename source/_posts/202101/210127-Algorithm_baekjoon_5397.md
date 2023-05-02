---
title: Baekjoon Online Judge 5397번 키로거 문제
date: 2021-01-27 14:18:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 5397번 키로거 문제 Pseudo code + Python code

![](/images/post_images/210127_baekjoon_5397.png)

> 위에서 손코딩한 코드에서는 left_stack과 right_stack을 for문 외부에서 초기화 해주었지만, 이렇게 해주면 매 테스트 케이스마다 초기화된 스택을 사용할 수 없기 때문에 for-loop 내에서 left_stack과 right_stack을 초기화 시켜주어야 한다.

```python
test_case = int(input())

for _ in range(test_case):
    left_stack = []
    right_stack = []
    p = input()
    for i in p:
        if i == '-':
            if left_stack:
                left_stack.pop()
        elif i == '<':
            if left_stack:
                right_stack.append(left_stack.pop())
        elif i == '>':
            if right_stack:
                left_stack.append(right_stack.pop())
        else:
            left_stack.append(i)
    left_stack.extend(reversed(right_stack))
    print(''.join(left_stack))
```

  <!-- more -->

이 문제는 키로거로부터 읽은 키보드의 입력을 읽어, 사용자가 입력한 비밀번호가 무엇인지 알아내는 프로그램을 작성하는 문제이다.

커서를 기준으로 좌, 우 이동 그리고 삭제하는 처리가 있기 때문에 좌, 우 이동시 리스트 내의 문자의 이동을 처리하기 위해 두 개의 스택을 준비해서 처리한다.

주의해야 될 부분은 오른쪽 스택은 반대 방향으로 데이터가 쌓이기 때문에 최종적으로 오른쪽 스택은 좌우 반전을 하여 왼쪽 스택과 같이 이어줘야 알맞는 결과를 얻을 수 있다.

## ![](/images/post_images/210124_developer.jpg)
