---
title: Baekjoon Online Judge 1543번 문서 검색 문제
date: 2021-02-01 11:28:42
tags:
  - Brute-force-search-Algorithm
  - String
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 1543번 문서 검색 문제 Pseudo code + Python code

![](/images/post_images/210201_baekjoon_1543.png)

> 손코딩한 코드에서 논리적 오류를 발견했다. document에서 검색하고자 하는 문자열의 길이만큼 slicing할때, 시작 인덱스(start_index)부터 검색 문자열의 길이(search_word)까지가 아닌, 시작 인덱스(start_index)에서 검색 문자열의 길이(len(search_word))만큼 더한 곳 까지 slicing한 문자열을 비교해야 한다.

```python
document = input()
search_word = input()
search_count = 0
start_index = 0

while (len(document)-start_index) // len(search_word) != 0:
# 아래의 조건도 위의 조건을 대체할 수 있다.
# while len(document)-start_index >= len(search_word):
  if document[start_index:start_index+len(search_word)] == search_word:
    start_index += len(search_word)
    search_count += 1
  else:
    start_index += 1

print(search_count)
```

  <!-- more -->

이 문제는

- ## **Brute force Algorithm (완전탐색)**

Brute Force Algorithm은 이름에서 유추해볼 수 있듯이 `맹목적으로 모든 경우의 수를 탐색하여 결과를 도출하는 알고리즘`이다. 이 알고리즘은 모든 경우의 수를 요구하는 문제를 풀때 사용된다.
1부터 100까지의 합을 구하는 문제도 1부터 100까지 완전탐색을 하여 그 합을 구하는 방식으로 Brute Force 기법의 문제라고 할 수 있다.
`Brute Force 기법`은 굉장히 단순한 기법이며, 문제해결에 있어서 첫번째로 고려되어야 할 기법이다.

## ![](/images/post_images/210124_developer.jpg)
