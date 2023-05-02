---
title: Baekjoon Online Judge 1302번 베스트 샐러 문제
date: 2021-02-10 10:28:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 1302번 베스트 샐러 문제 Pseudo code + Python code

![](/images/post_images/210210_baekjoon_1302.png)

> 손 코딩을 한 코드에서 sorted로 정렬을 한 뒤에 list로 변환을 하였는데, dictionary type의 데이터를 items()로 하게 되면, 리스트 내에 튜플(key, value쌍)로 변환이 되기 때문에 그럴 필요가 없었다. 정렬된 튜플 데이터 리스트에서 첫번째 요소의 첫번째(book title-(key))를 추출하게 되면 가장 높은 판매 수를 기록한 책의 제목을 추출할 수 있다.

```python
n = int(input())
sold_book = dict()
for _ in range(n):
    book_title = input()
    sold_book[book_title] = sold_book.get(book_title, 0) + 1
sorted_sold_book = sorted(sold_book.items(), key=lambda x: x[1], reverse=True)
print(sorted_sold_book[0][0])

```

  <!-- more -->

이 문제는 Python의 dict() 자료형을 활용하여 풀면 간단하게 해결할 수 있는 문제이다. dict() 자료형을 .items()로 변환하여 sorted() function내에서 lambda 함수식을 사용하여 간결하게 정렬하는 부분은 다른 알고리즘 문제에서도 유용하게 사용될 수 있는 부분인 것 같다.

## ![](/images/post_images/210124_developer.jpg)
