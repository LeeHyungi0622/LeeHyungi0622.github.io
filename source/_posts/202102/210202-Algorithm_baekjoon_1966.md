---
title: Baekjoon Online Judge 1966번 프린터 큐 문제
date: 2021-02-02 12:53:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 1966번 프린터 큐 문제 Pseudo code + Python code

![](/images/post_images/210202_baekjoon_1966.png)

```python
test_case = int(input())
for _ in range(test_case):
    task_num, search_index = map(int, input().split())
    test_priority_list = list(map(int, input().split()))
    print_task_list = [(p, i) for i, p in enumerate(test_priority_list)]

    print_task_count = 0
    while True:
        if print_task_list[0][0] == max(print_task_list, key=lambda x: x[0])[0]:
            print_task_count += 1
            if print_task_list[0][1] == search_index:
                print(print_task_count)
                break
            else:
                print_task_list.pop(0)
        else:
            print_task_list.append(print_task_list.pop(0))
```

  <!-- more -->

이 문제는 프린트의 task에 priority를 부여하여, 기존에 FIFO 정책으로 동작하는 프린트를 우선순위 우선 방식으로 프린트가 작동하도록 만드는 프로그램을 작성하는 문제였다. max 내에서 tuple의 첫 번째 요소(우선순위 값)를 기준으로 최대값을 찾고 [0] (priority) 값이 우선인 프린트를 우선적으로 프린트 될 수 있도록 작성하는 부분이 포인트이다.

## ![](/images/post_images/210124_developer.jpg)
