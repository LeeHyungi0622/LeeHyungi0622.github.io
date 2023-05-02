---
title: 210327 Basic Algorithm 가장 짧은 문자거리
date: 2021-03-27 14:16:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
categories:
  - Algorithm-problem-solving
---

## 기본 알고리즘 문제 Pseudo code + JavaScript code

![](/images/post_images/210327_basic_shortest_string.png)

오랜만에 이런저런 생각을 해 볼 수 있었던 알고리즘 문제인 것 같아 블로그에 포스팅 글로 남기기로 했다. 표면적으로는 매우 간단해보이는 문제이지만 그 접근방식에는 다양한 것 같다.

처음 이 문제를 보았을때 생각났던 구현방법으로 `split()`과 `재귀호출` 이 두 가지 방법이 생각이 났다.
주어진 문자열에서 특정 문자를 기준으로 구분되는 각 문자열들의 특정 문자로부터의 거리를 구하는 프로그램을 작성하는 문제인데 처음에는 아래와 같은 방식으로 Pseudo code와 JavaScript 코드를 작성해보았다. 결과값은 정답과 근사하게 출력이 되지만 정답이 되는 코드는 아니다.

다만 이 코드를 첨부하는 이유는 나의 문제해결 접근방식과 또 다른 문제해결 접근방식을 비교하기 위한 목적에 있다.

```javascript
let word = 'teachermode';
let resultArr = Array.from(word.length).fill(0);
const splitWord = word.split('e');
console.log(splitWord);
const result = splitWord.map((word) => {
  const wordLength = word.length;
  for (let i = 0; i < Math.floor(wordLength / 2); i++) {
    if (wordLength % 2 !== 0) {
      resultArr[Math.ceil(wordLength / 2)] = i + 1;
    }
    resultArr[i] = i + 1;
    resultArr[wordLength - i - 1] = i + 1;
  }
  return resultArr.join('');
});
console.log(result);
```

우선 위의 코드에서 생각한 방식으로 코드를 구현해서 정답을 구할 수도 있다고 생각한다. 하지만 재귀호출 방식을 사용했기 때문에 `시간 복잡도`를 고려했을때 효율적이지 않은 코드가 된다.

<!-- more -->

그 다음으로 주어진 문자열을 왼쪽 끝에서 오른쪽 끝으로, 오른쪽 끝에서 왼쪽 끝으로 완전 탐색을 하면서 문제를 해결하는 방식으로 코드를 작성해보았다. 이 방식의 시간 복잡도는 O(N)이다. for문을 두번 사용하기때문에 O(N)만큼의 시간복잡도를 갖게 된다.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `teachermode e`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  const iv = input().split(' ');
  const inputWord = iv[0];
  const searchWord = iv[1];

  let p1 = 1000;
  let answer = [];
  for (let w of inputWord) {
    if (w === searchWord) p1 = 0;
    else p1 += 1;
    answer.push(p1);
  }
  let p2 = 1000;
  for (let i = inputWord.length - 1; i >= 0; i--) {
    if (searchWord === inputWord[i]) {
      p2 = 0;
    } else {
      p2 += 1;
      answer[i] = Math.min(answer[i], p2);
    }
  }
  console.log(answer.join(' '));
}
```

주어진 문자로부터 얼마나 떨어졌는지 최소거리를 표현해야 되기 때문에 처음 왼쪽 끝에서 오른쪽 끝으로 순회를 할때에는 주어진 특정 문자와 다른 경우에는 0으로 초기화를 하고, 그렇지 않은 경우에는 1++로 누적한 값으로 해당 위치의 값을 넣어준다.

그 다음에 오른쪽 끝에서 왼쪽 끝으로 순회를 할때에는 기존에 있는 값이 순회하면서 1++로 누적한 값보다 크다면 작은 쪽의 값으로 초기화를 시켜준다.
