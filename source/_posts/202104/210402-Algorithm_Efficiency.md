---
title: 210402 Algorithm Efficiency (Two pointer algorithm, Sliding window, Hash)에 대한 이야기
date: 2021-04-02 09:47:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
categories:
  - Algorithm-problem-solving
---

## 알고리즘 효율성 (Two pointer algorithm, Sliding window, Hash)

<div align="center">
  <img src="/images/post_images/210402_algorithm_efficiency.png" alt="Algorithm basic efficiency classes">
</div>

이번 포스팅에서는 알고리즘의 효율성에 대해서 이야기해보려고 한다.

아래의 코드는 주어진 두 배열을 합쳐서 합쳐진 배열을 오름차순으로 정렬하는 문제를 구현한 코드이다.
처음에 이 문제를 보았을때 들었던 생각은 spread 문법을 사용해서 주어진 두 배열을 하나의 배열로써 unpacking한 다음에 sort() 함수를 사용해서 오름차순 정렬하는 방법을 생각했다. (`solution 1`)

그런데 `sort()함수를 사용해서 N개의 숫자를 정렬하는 경우에는 시간복잡도 O(NlogN)만큼의 시간 복잡도`를 갖는다.

**그렇다면 좀 더 시간복잡도상 효율적인 코드를 작성할 수는 없을까?**

단순히 결과값이 나왔다고 좋아하지 말고 좀 더 효율적인 코드를 작성할 수 있도록 생각하고 또 생각해야 한다.

<!-- more -->

**그 해결책으로는 Two Pointer Algorithm**가 있다.

arr1, arr2 두 개의 배열이 주어지고, 두 개의 배열을 합친 후 오름차순으로 정렬을 해야 되는 문제가 주어졌다고 가정하자.
이런 경우에 각 배열의 start index에 해당하는 p1, p2 변수를 0으로 초기화 해서 선언을 한다.
arr1[p1]과 arr2[p2]의 값을 서로 비교해서 작은 값을 새로운 배열 변수(answer)에 담아주고 작은 값이 arr1에 있는 값인 경우에는 p1++, arr2에 있는 값인 경우에는 p2++를 해주도록 한다.

이 일련의 과정은 p1, p2가 arr1.length, arr2.length보다 작을때까지 looping한다.

구체적인 내용은 아래 첨부한 필기노트를 참고하자.

<img src="/images/post_images/210402_two_pointer_algorithm_note.png" alt="two pointer algorithm note">

## **Solution 1(use spread and sort) - O(nlogn) Time complexity**

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux' ?
    fs.readFileSync('/dev/stdin').toString() :
    `3
1 3 5
5
2 3 6 7 9`).split('\n');
const input = (() => {
    let line = 0;
    return () => stdin[line++];
})();

{
    const n = Number(input());
    const nList = input().split(' ').map(number => Number(number));
    const m = Number(input());
    const mList = input().split(' ').map(number => Number(number));

    // n개의 숫자를 sort함수를 사용해서 정렬을 하면, nlogn만큼의 시간 복잡도를 갖는다.
    // Two pointer 알고리즘을 사용하면, n 만큼의 시간 복잡도를 갖는다.
    // 두 리스트를 순회한다고 하더라도 (n+m) 만큼의 시간 복잡도를 갖기 때문에 sort함수를 사용해서
    // 해결하는 것보다 시간 복잡도상 더 효율적이다.
    console.log([...nList, ...mList].sort((f, s) => f - s).join(' '));
```

## **Solution 2(Two pointer algorithm) - O(n) Time complexity**

```javascript
function solution(arr1, arr2) {
  let answer = [];
  let n = arr1.length;
  let m = arr2.length;
  let p1 = (p2 = 0);
  while (p1 < n && p2 < m) {
    if (arr1[p1] <= arr2[p2]) answer.push(arr1[p1++]);
    else answer.push(arr2[p2++]);
  }
  while (p1 < n) answer.push(arr1[p1++]);
  while (p2 < m) answer.push(arr2[p2++]);
  return answer;
}

let a = [1, 3, 5];
let b = [2, 3, 6, 7, 9];
console.log(solution(a, b).join(' '));
```

`두 배열의 공통원소를 찾아서 정렬하는 문제`도 Two pointer algorithm을 사용해서 구현할 수 있다.

## **solution 1 (Set 자료구조와 sort() 활용)**

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `5
1 3 9 5 2
5
3 2 5 7 8`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const n = Number(input());
  const nList = input()
    .split(' ')
    .map((number) => Number(number));
  const m = Number(input());
  const mList = input()
    .split(' ')
    .map((number) => Number(number));
  const nSet = new Set(nList);
  const mSet = new Set(mList);

  const intersectionSet = new Set(
    [...nSet].filter((number) => mSet.has(number))
  );
  console.log([...intersectionSet].sort((f, s) => f - s).join(' '));
}
```

## **solution2 (Two pointer algorithm 활용)**

```javascript
function solution(arr1, arr2) {
  let answer = [];
  arr1.sort();
  arr2.sort();
  let p1 = (p2 = 0);
  while (p1 < arr1.length && p2 < arr2.length) {
    if (arr1[p1] === arr2[p2]) {
      answer.push(arr1[p1++]);
      p2++;
      // 값이 작은 쪽의 index(p1, p2)를 증가시킨다.
    } else if (arr1[p1] < arr2[p2]) p1++;
    else p2++;
  }
  return answer;
}

let a = [1, 3, 9, 5, 2];
let b = [3, 2, 5, 7, 8];
console.log(solution(a, b));
```

## **연속 부분수열 문제**

<ins><b>Two pointer algorithm 대표유형</b></ins>

<img src="/images/post_images/210402_continuous_partial_sequence.png" alt="two pointer algorithm 대표유형">

위에서 풀이한 알고리즘 문제는 주어진 숫자배열에서의 숫자들의 합이 특정 숫자 m이 되는 경우의 수를 구하는 문제를 `Two pointer algorithm`으로 풀이한 문제이다.

Two pointer algorithm에 대해서 설명을 할 때에는 위의 문제를 활용해서 설명하는 경우가 많기 때문에 위에 첨부한 노트의 좌측에 있는 코드를 제대로 이해하고 있어야 한다.

```javascript
function solution(m, arr) {
  let lt = 0;
  let sum = 0;
  let count = 0;
  for (let rt = 0; rt < arr.length; rt++) {
    sum += arr[rt];
    if (sum === m) count++;
    while (sum >= m) {
      sum -= arr[lt++];
      if (sum === m) count++;
    }
  }
  return count;
}
```

아래의 코드는 초기에 내가 직접 Two pointer algorithm을 활용하여 풀이했던 코드이다. numberCombine과 sumNumberList는 더해준 숫자 요소의 그룹을 확인하기 위한 목적으로 사용한 변수이다.

내가 처음에 작성한 코드의 경우, sum값이 target sum value(m)보다 작거나 같은 경우와 큰 경우로 나눠서 p1값이 주어진 배열의 길이(numOfNumbers)의 값보다 작은 경우에 반복 순회하도록 작성하였다.

위의 대표유형 코드와 다른 부분은 대표유형 코드의 경우, sum값이 target sum value(m)보다 크거나 같은 경우에 다른 포인트 (lt)를 index로 하는 배열의 위치값을 빼주었지만, 내가 작성한 코드에서는 커지면 p2의 값을 1증가시키고, 증가시킨 p2의 값을 기존의 p1의 값으로 대체하고 누적 합(sum)의 값을 0으로 초기화시켜주었다.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `8 6
1 2 1 3 1 1 1 2`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const firstInput = input().split(' ');
  const numOfNumbers = Number(firstInput[0]);
  const sumOfNumbers = Number(firstInput[1]);
  const numberList = input()
    .split(' ')
    .map((number) => Number(number));

  let p1 = (p2 = 0);
  let sum = 0;
  let count = 0;
  let sumNumberList = [];
  let numberCombine = [];
  while (p1 < numOfNumbers) {
    if (sum <= sumOfNumbers) {
      if (sum === sumOfNumbers) {
        sumNumberList.push(numberCombine);
        numberCombine = [];
        count++;
      }
      numberCombine.push(numberList[p1]);
      sum += numberList[p1++];
    } else {
      numberCombine = [];
      sum = 0;
      p2++;
      p1 = p2;
    }
  }
  console.log(count);
  console.log(sumNumberList); // [ [ 2, 1, 3 ], [ 1, 3, 1, 1 ], [ 3, 1, 1, 1 ] ]
}
```

`2021.04.03 업데이트`

## **Sliding window 기법**

Sliding window 기법이란 정해진 구간을 정해진 방향으로 밀고 나가면서 정해진 구간만큼의 값을 계산하는 것을 말한다.
Sliding window라는 말도 창문이라는 정해진 너비만큼 옆으로 이동하는 모습에서 착안한 이름이 아닐까 생각된다.

주어진 N개의 숫자를 K개씩 묶어서 더할때 최대합을 구하는 문제로 Sliding window 기법을 정리해본다.

초기 합은 주어진 숫자 리스트의 초기 K개의 합으로 변수를 초기화한다.
그리고나서 numberList[i]의 값을 더하고 numberList[i-k]의 값을 빼주는 과정을 반복한다.

이중 for문을 사용해서 위와같은 과정을 구현할 수도 있지만, 이중 for문을 사용하게 되면 O(N^2)만큼의 시간 복잡도를 갖기 때문에 효율적이지 않다.
따라서 아래와 같이 Sliding window 기법으로 코드를 작성하도록 하자.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `10 3
12 15 11 20 25 10 20 19 13 15`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const [n, k] = input()
    .split(' ')
    .map((n) => +n);
  const numberList = input()
    .split(' ')
    .map((n) => +n);
  let answer = 0;
  let sum = 0;
  for (let i = 0; i < k; i++) sum += numberList[i];
  // 첫 번째 3일을 더한 값을 초기화
  answer = sum;
  for (let i = k; i < numberList.length; i++) {
    sum += numberList[i] - numberList[i - k];
    answer = Math.max(answer, sum);
  }
  console.log(answer);
}
```
