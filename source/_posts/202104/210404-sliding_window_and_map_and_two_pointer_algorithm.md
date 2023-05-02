---
title: 210404 Sliding window + Map + Two pointer algorithm 문제풀이
date: 2021-04-04 07:36:42
tags:
  - Algorithm
  - Sliding-window
  - JavaScript-Map
  - Two-pointer-algorithm
categories:
  - Algorithm-problem-solving
---

<div align="center">
  <img src="/images/post_images/210404_anagram_img.png" alt="Anagram">
</div>

이번 포스팅에서는 Sliding window, Map, Two pointer algorithm을 복합적으로 사용하여 풀이한 알고리즘 문제에 대해서 정리를 해보려고 한다.

효율성을 전혀 고려하지 않은 이중 for문의 사용을 자제하고 앞의 세 가지 개념을 활용하여 코드를 구현한다면 효율성을 극대화시켜서 코드를 작성할 수 있을 것이라고 생각한다.

우선 하나의 문제를 예시로 내가 처음에 구현한 코드와 다른 사람이 구현한 코드 두 가지를 비교분석해보자.

문제는 `문자열 S와 T가 주어졌을때 문자열 S에서 T문자열과 아나그램이 되는 부분 문자열의 갯수를 출력하는 문제`이다.

이 문제를 처음 보았을때 Sliding window, Map, Slice method를 사용하여 문제를 풀이해야 겠다고 생각했고 아래와 같이 코드를 작성했다.

우선, 아나그램인지 아닌지 판별하기 위한 개별함수를 선언(checkAnagram() function)한다.
그 다음에 초기에는 입력받은 문자열(is)를 검색 문자열(ss)의 길이만큼 slice해서 확인용 문자열 변수에 별도로 저장을 해준다.

  <!-- more -->

저장된 문자열 변수와 검색 문자열은 만들어 둔 checkAnagram() function에 인수로 넣어 아나그램인지 아닌지 판별한다.
이제 입력받은 문자열의 검색 문자열의 길이부터 입력 문자열(is)의 길이까지 순회를 하며 추가적으로 문자를 붙여주고, 붙인 다음에는 첫 번째 문자를 제외한 나머지를 문자열을 slice해서 checkAnagram() function을 통해 아나그램인지 아닌지 판별한다.

나는 처음에 이런 방식으로 코드 구현을 해보았다.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `bacaAacba
abc`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  // input stirng
  const is = input();
  // search string
  const ss = input();
  const checkAnagram = (f, s) => {
    const fm = new Map();
    for (let w of f) {
      if (fm.has(w)) fm.set(w, fm.get(w) + 1);
      else fm.set(w, 1);
    }
    for (let w of s) {
      if (!fm.has(w) || fm.get(w) === 0) return false;
      else fm.set(w, fm.get(w) - 1);
    }
    return true;
  };

  let count = 0;
  let checkString = is.slice(0, 3);
  // console.log(checkString);
  if (checkAnagram(ss, checkString)) count += 1;
  for (let i = ss.length; i < is.length; i++) {
    checkString += is[i];
    const slicedString = checkString.slice(i - ss.length + 1, i + 1);
    //console.log(slicedString);
    if (checkAnagram(ss, slicedString)) count += 1;
  }
  console.log(count);
}
```

그 다음에는 다른 사람이 작성한 코드인데, 이 사람은 내가 사용한 slice() method를 사용하지 않고, Two pointer algorithm을 사용하였다.
각 function과 코드가 어떤 의도로 작성되었는지 분석을 하면서 comment를 작성해보았다.
이 코드는 compareMaps()라는 별도의 함수를 만들어서 아나그램인지 아닌지 판별을 하였고, 비교 문자열에 문자를 추가하고 비교하고 빼는 과정을 반복 순회하며 처리를 하였다.

```javascript
// 두 개의 map을 인자로 받아서 사이즈를 비교한다. 두 map의 key속성 길이(size)가 다르다면
// 비교하는 것이 의미가 없기 때문에 false를 반환한다.
// 길이가 같다면 첫 번째 인자로 받은 map1의 key, value를 순회하며 map2에 같은 key가 존재하는지,
// 존재한다면 해당 key값이 가지는 value가 map2에서의 key값의 value와 일치하는지 확인한다.
function compareMaps(map1, map2) {
  if (map1.size !== map2.size) return false;
  for (let [key, val] of map1) {
    if (!map2.has(key) || map2.get(key) !== val) return false;
  }
  return true;
}

function solution(s, t) {
  let answer = 0;
  let tH = new Map();
  let sH = new Map();
  for (let x of t) {
    if (tH.has(x)) tH.set(x, tH.get(x) + 1);
    else tH.set(x, 1);
  }
  let len = t.length - 1;
  // s문자열에서 index 0부터 비교문자열 t의 길이-1만큼(우선 앞의 2개만) sH에 업데이트해준다.
  for (let i = 0; i < len; i++) {
    if (sH.has(s[i])) sH.set(s[i], sH.get(s[i]) + 1);
    else sH.set(s[i], 1);
  }

  let lt = 0;
  // 빼고 => 추가하고 => 비교 (반복)
  for (let rt = len; rt < s.length; rt++) {
    // 새로 추가하는 문자(처음에는 문자 'c')가 기존의 sH에 존재한다면 해당 요소의 값을 1증가 시켜준다.
    if (sH.has(s[rt])) sH.set(s[rt], sH.get(s[rt]) + 1);
    // 그렇지 않다면 default value를 1로 해서 요소를 추가한다.
    else sH.set(s[rt], 1);
    // compareMaps에 sH와 tH를 인수로 넘겨서 호출을 해서 true를 반환할 경우 answer값을 1증가 시킨다.
    if (compareMaps(sH, tH)) answer++;
    // sH에서 lt index위치에 해당하는 key의 값에서 1만큼 빼준다. (오른쪽으로 sliding window)
    sH.set(s[lt], sH.get(s[lt]) - 1);
    // 만약에 값을 뺀 뒤에 해당 lt index위치의 값이 0이 되는 경우, sH Map에서 해당 값을 delete해준다.
    if (sH.get(s[lt]) === 0) sH.delete(s[lt]);
    // lt index의 값을 1 증가시켜준다.
    lt++;
  }
  return answer;
}

let a = 'bacaAacba';
let b = 'abc';
console.log(solution(a, b));
```
