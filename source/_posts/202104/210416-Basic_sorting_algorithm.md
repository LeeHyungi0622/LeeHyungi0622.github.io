---
title: 210416 기본 정렬 알고리즘 자바스크립트로 구현하기 (선택정렬, 버블정렬, 삽입정렬)
date: 2021-04-16 05:14:42
tags:
  - Algorithm
  - sorting
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210416_sorting_algorithm.png" alt="Basic sorting algorithms">
</div>

## <ins><b>기본 정렬 알고리즘 구현하기</b></ins>

이 기본 정렬 알고리즘은 직접 손 코딩 할 수 있을 정도로 코드를 익혀야 하기 때문에 반복 복습을 위해 블로그 포스팅을 해두기로 했다.
코딩 테스트 문제를 풀때 내장된 sort 함수를 사용해서 간편하게 정렬을 해서 문제 해결을 하지만, 기존에 있는 정렬 알고리즘의 구현을 이해하고 필요에 따라 기존 정렬 알고리즘을 응용해서 새로운 알고리즘을 만들어 낼 수 있는 능력도 필요하다.
그럼 자바스크립트 언어로 코딩테스트를 준비하고 있기 때문에 자바스크립트로 각 기본 정렬 알고리즘을 구현 및 정리를 해보겠다.

## <ins><b>선택정렬(Selection sort) - `O(N^2) 시간복잡도/정수 6만개 기준, 10.842(sec)`</b></ins>

우선 선택 정렬에 대해서 코드 구현을 하기 전에 간단하게 선택정렬을 어떻게 구현해야 되는지 설명을 하겠다.
선택정렬은 버블정렬과 같이 1회 순회에 한 개 요소의 위치가 결정이 된다. (`가장 작은 값(왼쪽부터 오른쪽으로) 결정`)
이중 for-loop의 형태로 `i: 0 ~ arr.length`, `j: i+1 ~ arr.length`로 순회를 한다.
그리고 j의 범주로 순회를 하면서 `가장 작은 값의 index를 찾는다.` 가장 작은 값의 index를 찾았다면, `최소값의 index위치와 i번째 index위치를 바꿔준다.` 이 과정을 i의 범주로 순회를 하면 정렬이 완료된다.
그럼 코드로 한 번 구현을 해보겠다.

```javascript
for (let i = 0; i < arr.length; i++) {
  let index = i;
  for (let j = i + 1; j < arr.length; j++) {
    if (arr[j] < arr[index]) index = j;
  }
  [arr[i], arr[index]] = [arr[index], arr[i]];
}
```

  <!-- more -->

## <ins><b>버블정렬(Bubble sort) - `O(N^2) 시간복잡도/정수 6만개 기준, 22.894(sec)`</b></ins>

버블정렬도 선택정렬과 같이 1회 순회에 한 개 요소의 위치가 결정된다. 하지만 선택정렬과 다른 점은 가장 큰 값(오른쪽부터 왼쪽으로) 우선으로 결정된다는 점이다. 따라서 이중 for-loop 형태로 반복 순회를 할때 내부 for-loop의 경우에는 완성된 오른쪽 요소의 수만큼 차감시켜서 순회를 해야한다.

```javascript
for (let i = 0; i < arr.length; i++) {
  for (let j = 0; j < arr.length - i - 1; j++) {
    if (arr[j] > arr[j + 1]) {
      [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
    }
  }
}
```

## <ins><b>삽입정렬(Insertion sort) - `O(N^2) 시간복잡도/정수 6만개 기준, 7.438(sec)`</b></ins>

삽입정렬의 경우에는 앞서 구현해본 1회 순회에 한 개 요소의 최종 위치가 결정되는 선택정렬, 버블정렬과 달리 `정렬의 범주를 점진적으로 늘려가며 정렬을 하는 방식`이다.
가장 처음 요소는 정렬이 되어있다고 가정을 하고 두 번째 요소부터 배열의 길이만큼 순회를 하게 되는데, 가장 처음 요소는 정렬이 되어있다고 가정을 하였기 때문에 두 번째 요소를 key값으로 선언을 한다. key값으로 선언을 하는 이유는 j index의 값과 key값을 비교를 해서 j index의 값이 key값보다 큰 경우에 j+1위치의 값(key값 설정 위치)을 j위치의 값으로 덮어쓰는 형태로 진행이 되기 때문에 기존 j+1의 위치값을 저장해두기 위한 목적에서 key값으로써 별도로 저장해둔다.

코드구현시 주의해야 될 점은 i의 진행방향과 j의 진행방향이 반대라는 점이다. i는 왼쪽에서 오른쪽 방향으로 진행이 되고, j의 경우에는 오른쪽에서 왼쪽방향으로 진행이 된다.

`구현 1`

```javascript
for (let i = 1; i < arr.length; i++) {
  let key = arr[i];
  let j = i - 1;
  while (j >= 0 && arr[j] > key) {
    arr[j + 1] = arr[j];
    j--;
  }
  arr[j + 1] = key;
}
```

`구현 2`

```javascript
for (let i = 1; i < arr.length; i++) {
  let key = arr[i];
  let j;
  for (j = i - 1; j >= 0; j--) {
    if (arr[j] > key) arr[j + 1] = arr[j];
    else break;
  }
  arr[j + 1] = key;
}
```
