---
title: Baekjoon Online Judge 10814번 나이순 정렬문제
date: 2021-01-28 9:50:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 10814번 나이순 정렬문제 Pseudo code + Python code

![](/images/post_images/210128_baekjoon_10814.png)

> **(오류1)** 손코딩을 한 부분에 오류가 있다. age, name을 한 줄에서 입력받는데, 두 입력 데이터의 타입은 같지 않기 때문에 integer 형으로만 설정할 수 없다. <br/> 이 경우에는 string 형으로 우선 받고나서 데이터를 삽입할때 integer형으로 type casting을 해주면 된다. <br/> **(오류2)** 두번째 오류는 문제의 조건에서 튜플의 첫번째 요소(나이)로 우선 오름차순 정렬을 하고, 튜플의 두번째 요소(이름)으로 오름차순 정렬을 하는 문제가 아닌, 나이로 오름차순 정렬을 하고, 나이가 같으면 입력한 순서대로 정렬을 하는 문제였다. 따라서 정렬 조건을 key = lambda x:(x[0], x[1]) 가 아닌 key = lambda x: x[0]로만 해주면 된다.

```python
test_case = int(input())
join_members = []
for _ in range(test_case):
  age, name = map(str, input().split(' '))
  join_members.append((int(age), name))
join_members.sort(key=lambda x:x[0])
for i in join_members:
  print(i[0], i[1])

# input :
3
21 Junkyu
21 Dohyun
20 Sunyoung
# output :
20 Sunyoung
21 Junkyu
21 Dohyun
```

  <!-- more -->

이 문제는 리스트 내의 튜플을 이용해서 정렬하는 문제이다. 사용자로부터 나이와 이름에 대한 정보를 입력받고, 그 입력받은 정보를 튜플 자료형으로 묶은 다음에 리스트에 누적 저장한다. 최종 완성된 리스트는 sort() 메서드를 사용해서 조건에 맞게 정렬시켜서 출력시키면 된다.

## ![](/images/post_images/210124_developer.jpg)
