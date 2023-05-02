---
title: 210331 Basic Algorithm brute force(블루트 포스) 대표유형 문제
date: 2021-03-31 11:00:42
tags:
  - Algorithm
  - PseudoCode
  - Brute-force-problem
categories:
  - Algorithm-problem-solving
---

## 기본 알고리즘 문제 Pseudo code + JavaScript code

<div align="center">
  <img src="/images/post_images/210331_brute_force_img.png" alt="Brute force algorithm">
</div>

이번에 풀어 본 기본 알고리즘 문제는 완전탐색 알고리즘으로 이름에서 유추해 볼 수 있듯이 가능한 모든 경우의 수를 비교해서 풀이하는 방법의 알고리즘 기법이다.

이전에 이 Brute force 알고리즘에 대해서 포스팅한 적이 있다.

오늘 풀어 본 `멘토링 문제`도 이 완전탐색을 기반으로 풀어야 해결할 수 있는 문제였다.

문제의 조건은 다음과 같다.

총 4명의 학생이 N번의 테스트를 응시해서 각 테스트 별로 등수가 매겨진다. A와 B학생이 있다고 가정했을때, A학생이 B학생의 멘토가 되기 위해서는 시행된 모든 테스트에서 A학생은 B학생보다 등수가 우위에 있어야 한다.
이러한 조건으로 각 테스트별 해당 학생의 등수가 비교되는 학생보다 등수가 우위에 있는지 비교를 하고 우위에 있다면 count값을 증가시켜 count값이 응시하는 테스트 수와 같다면 멘토, 멘티의 관계가 성립되기 때문에 결과로 출력할 answer값을 증가시켜 값을 누적하도록 하면 해결할 수 있는 문제였다.

<!-- more -->

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `4 3
3 4 1 2
4 3 2 1
3 1 4 2`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  let firstInput = input();
  const numOfStudents = firstInput.split(' ')[0];
  const numOfTests = firstInput.split(' ')[1];
  let testList = [];
  let answer = 0;

  for (let k = 0; k <= numOfTests; k++) {
    if (k > 0)
      testList.push(
        input()
          .split(' ')
          .map((number) => Number(number))
      );
  }
  console.log(testList);
  for (let i = 1; i <= numOfStudents; i++) {
    for (let j = 1; j <= numOfStudents; j++) {
      let cnt = 0;
      for (let k = 0; k < numOfTests; k++) {
        let pi,
          pj = 0;
        for (let s = 0; s < numOfStudents; s++) {
          // 등수 넣기
          if (testList[k][s] === i) pi = s;
          if (testList[k][s] === j) pj = s;
        }
        if (pi < pj) cnt++;
      }
      if (cnt === +numOfTests) {
        answer++;
      }
    }
  }
  console.log(answer);
}
```

```javascript
function solution(test) {
  let answer = 0;
  m = test.length;
  n = test[0].length;

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= n; j++) {
      let cnt = 0;
      // k: test 시행 수
      for (let k = 0; k < m; k++) {
        let pi = (pj = 0);
        // s: 등수
        for (let s = 0; s < n; s++) {
          // i, j번째 학생의 등수 결정
          if (test[k][s] === i) pi = s;
          if (test[k][s] === j) pj = s;
        }
        // pi: mentor, pj: mentee
        // mentor, mentee 성립
        if (pi < pj) cnt++;
      }
      // 만약에 테스트 시행 수와 cnt된 값(mentor, mentee성립 수)이 일치한다면
      // mentor, mentee가 될 수 있는 필요충분 조건을 만족한다.
      if (cnt === m) answer++;
    }
  }
  return answer;
}

let arr = [
  [3, 4, 1, 2],
  [4, 3, 2, 1],
  [3, 1, 4, 2]
];
console.log(solution(arr));
```
