---
title: 210403 Algorithm Consecutive number subsequence와 Number subsequence에 대한 이야기
date: 2021-04-03 13:42:42
tags:
  - Algorithm
  - Sort
  - Consecutive-number-subsequence
  - Number-subsequence
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210403_consecutive_subsequence.png" alt="Consecutive subsequence">
</div>

## Consecutive number subsequence(연속 부분수열)과 Number subsequence(부분수열)

이번 포스팅에서는 연속 부분수열과 부분수열에 대해서 이야기해보려고 한다.

위 두 개념에 대해서는 알고리즘 문제풀이를 하면서 접하게 되었는데, 그 풀이방법에 대해서 왠지 정리해두면 나중에 유용할 듯 싶어 블로그 포스팅하기로 했다.

우선 연속 부분수열에 대한 문제풀이에서 사용한 코드 패턴을 살펴보자.
이름에서 예상할 수 있듯이 연속된 수들의 부분집합으로 이해할 수 있다. 만약에 N개의 숫자가 주어졌을때 수들의 합이 주어진 값인 S와 같은 연속 부분수열의 갯수를 구해야 한다면 어떻게 코드 구현을 해야할까?  
바로 아래와 같이 코드를 구현할 수 있다.

  <!-- more -->

```javascript
const n = 8;
const arr = [1, 2, 1, 3, 1, 1, 1, 2];
const s = 6;

let lt = 0;
let sum = 0;
let answer = 0;

for (let rt = 0; rt < n; rt++) {
  sum += arr[rt];
  if (sum === s) answer += 1;

  while (sum >= s) {
    sum -= arr[lt++];
    if (sum === s) answer += 1;
  }
}
console.log(answer);
```

그렇다면 연속 부분수열이 아닌 부분수열이라면 어떤 식으로 코드를 구현해야 될까?
이 부분은 백준 알고리즘의 1182번 문제를 풀이해보면서 코드를 작성해보았다.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `5 0
-7 -3 -2 5 8`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const firstInput = input()
    .split(' ')
    .map((number) => +number);
  const n = firstInput[0];
  const s = firstInput[1];
  const numberList = input()
    .split(' ')
    .map((number) => +number);
  let answer = 0;
  let dp = new Array(n).fill(null).map((v, i) => [numberList[i]]);

  for (let i = 1; i < n; i++) {
    for (let j = 0; j < i; j++) {
      for (let k = 0; k < dp[j].length; k++) {
        dp[i].push(dp[i][0] + dp[j][k]);
      }
    }
  }

  dp.forEach((row) => {
    row.forEach((col) => {
      if (col === s) answer += 1;
    });
  });
  console.log(answer);
}
```
