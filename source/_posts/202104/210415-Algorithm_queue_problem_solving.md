---
title: 210415 Algorithm javascript로 queue를 사용한 문제풀이
date: 2021-04-15 08:19:42
tags:
  - Algorithm
  - queue
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210415_javascript_queue.png" alt="JavaScript shift method">
</div>

## 큐를 활용한 문제풀이

이번 포스팅에서는 자바스크립트로 큐(queue)를 이용한 문제풀이에 대해서 사용되는 배열의 메서드에 대해서 간단하게 정리하고 관련 문제를 풀이해보려고 한다.

우선 pop과 push의 경우에는 배열의 맨 마지막 요소를 빼거나(pop) 추가(push)할때 사용하고, shift는 배열의 맨 앞 요소를 빼고, unshift는 맨 앞에 요소를 추가할때 사용한다.

- ### 배열을 일정 길이로 초기화하는 두 가지 방법

```javascript
// index를 값으로 갖는 길이가 10인 배열 만들기
new Array(10).fill(null).map((v, i) => i);
// Array의 정적 메서드 from을 사용하여 길이 및 내부 요소값 초기화하기
Array.from({ length: 10 }, (v, i) => i + 1);
```

- ### 문제풀이
  길이가 N개인 배열에서 0번째 인덱스부터 M번째 떨어진 숫자를 배열내에 한 개의 숫자가 남을때까지 반복해서 제거한다고 했을때 최종적으로 남을 단 하나의 숫자는 무엇인가?

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux' ?
  fs.readFileSync('/dev/stdin').toString() :
  `8 3`).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const [n, m] = input().split(' ').map(n => +n);

  const queue = Array.from({ length: n, (v, i) => i + 1});
  let answer;

  while(queue.length){
    for(let i = 1; i < m; i++){
      queue.push(queue.shift());
    }
    // 세 번째 요소는 그냥 shift
    queue.shift();
    if(queue.length === 1) answer = queue.shift();
  }
  console.log(answer);
}
```
