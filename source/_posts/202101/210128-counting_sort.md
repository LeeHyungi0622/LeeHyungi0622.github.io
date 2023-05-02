---
title: 210128 계수정렬(Counting sort)
date: 2021-01-28 14:10:42
tags:
  - Self-Development
  - Python
  - Sorting
  - Counting-sort
categories:
  - Data-Structure-&-Algorithm
---

# **오늘 공부한 내용**

Baekjoon Online Judge의 10989문제의 풀이에서 사용한 계수정렬(Counting sort)에 대해서 정리를 해본다.

계수정렬은 O(N)의 시간복잡도를 갖는다.
계수 정렬을 사용하려면 정렬 대상 배열의 최소값과 최대값을 알고 있어야 한다. 최대값만큼 최대 길이를 설정해서 별도의 리스트를 생성해야 되기 때문이다.

정렬 이름대로 요소의 값에 해당하는 index의 위치에 요소의 갯수만큼 카운트해서 값을 증가시켜 주는 형태를 갖는다. <br/>
예를 들어 [1, 5, 1, 5, 6] 이라는 리스트가 있다고 가정하자. <br/>
총 5개의 숫자, 최소값은 1, 최대값은 6이라는 전제 조건이 성립하며, 계수정렬을 사용하기 위한 조건을 만족한다.

  <!-- more -->

자 그럼 카운팅을 위해서 길이가 6인 0으로 초기화된 리스트를 별도로 생성해준다. 그리고나서 리스트를 순회하며 해당 값을 index로 갖는 새로 생성한 리스트의 위치값을 1씩 증가시켜준다.
이제 index를 새로 카운트해서 넣어 준 값만큼 순회하며 값을 출력해주면 된다.

![](/images/post_images/210128_counting_sort.png)

> 이미지 출처 : https://brilliant.org/wiki/counting-sort/

<br/>

아래는 Big-O 표기법으로 표기한 시간복잡도의 관계를 표현하고 있다.
![](/images/post_images/functions_ranking.png)
