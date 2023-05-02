---
title: 210327 Basic Algorithm 문자열 압축
date: 2021-03-27 15:33:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
categories:
  - Algorithm-problem-solving
---

## 기본 알고리즘 문제 Pseudo code + JavaScript code

![](/images/post_images/210327_basic_algorithm_compress_string.png)

처음 이 문제를 보았을때 생각났던 구현방법으로 `Set()`자료형이 생각이 났다. 우선 주어진 문자열을 압축하는 것이기 때문에 반복되는 문자를 생략해서 표기해야 되기 때문이다.
그래서 아래에 첨부한 코드와 같이 처음에 입력받은 문자열을 Set() 객체로 만들어서 중복되는 문자를 제거해줬다.

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux' ?
    fs.readFileSync('/dev/stdin').toString() :
    `KKHSSSSSSSE`).split('\n');
const input = (() => {
    let line = 0;
    return () => stdin[line++];
})()

{
    const inputWord = input();
    let noRepeatWord = new Set(inputWord);
    let noRepeatWordList = [...noRepeatWord];

    for (let i = 0; i < noRepeatWordList.length; i++) {
        let repeatCount = 0;
        for (let w of inputWord) {
            if (noRepeatWordList[i] === w) repeatCount += 1;
        }
        if (repeatCount >= 2) noRepeatWordList[i] += repeatCount;
    }
    console.log(noRepeatWordList.join(''));
```

<!-- more -->

```javascript
function solution(s) {
  let answer = '';
  let cnt = 1;
  s = s + ' ';
  for (let i = 0; i < s.length - 1; i++) {
    if (s[i] === s[i + 1]) cnt++;
    else {
      answer += s[i];
      if (cnt > 1) answer += String(cnt);
      cnt = 1;
    }
  }
  return answer;
}

let str = 'KKHSSSSSSSE';
console.log(solution(str));
```
