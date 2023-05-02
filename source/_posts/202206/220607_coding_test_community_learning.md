---
title: 220607 프로그래머스 코딩 테스트 커뮤러닝 참여 중 배운 유익한 내용 (1주차) (업데이트 중...)
date: 2022-06-07 08:46:00
tags:
  - Python
categories:
  - Algorithm-problem-solving
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220607_coding_test.jpeg" alt="알고리즘 문제풀이">
</div>
이번 포스팅에는 프로그래머스 코딩 테스트 커뮤러닝에 참여하면서 1주차 문제풀이를 통해 다른 사람의 코드와 강사님의 문제풀이를 보면서 배웠던 내용에 대해서 정리해보려고 한다. 
나중에 코딩 테스트 보기 전에 한 번 블로그에 정리한 포스팅 내용을 한 번 읽어보면서 정리하기 위한 목적에서 정리를 해본다.  
<br/>
<br/>

## <ins><b>collections의 Counter 활용</b></ins>

여지까지 리스트내의 값을 key:value(등장 횟수) 형태인 dictionary 자료형으로 만들때 일일이 for문으로 순회를 하면서 value 값을 증가 시켜주었는데, 이번 커뮤러닝을 통해 참여하신 분 중 한 분이 내 코드에 리뷰를 해주셨는데, `collections의 Counter`를 활용하면 더 간결하게 코드를 작성할 수 있다고 해서 코드를 다시 작성해보았는데, 리뷰해주신 내용대로 코드가 훨씬 간결해졌다.

<!-- more -->

```python
# (수정 전) - 시간 복잡도 O(N)
def solution(participant, completion):
    p_set = set(participant)
    p_hash = {name: 0 for name in p_set}

    for n in participant:
        p_hash[n] = p_hash.get(n) + 1

    for n in completion:
        p_hash[n] = p_hash.get(n) - 1

    for k, v in p_hash.items():
        if v != 0:
            return k

# (수정 후) - 시간 복잡도
from collections import Counter

def solution(participant, completion):
    p = Counter(participant)
    # Counter({'marina': 2, 'josipa': 1, 'nikola': 1, 'vinko': 1, 'filipa': 1})
    for c in completion:
        p[c] -= 1
    #  리스트 p에 최대값을 구하고, 최대값의 key값을 반환하는 구문이다.
    return max(p, key=p.get)
```

## <ins><b>문자열 숫자의 정렬</b></ins>

- ### **문자열 숫자 리스트의 요소를 조합하여 최대값 만들기 - (Stack 자료구조 활용)**
