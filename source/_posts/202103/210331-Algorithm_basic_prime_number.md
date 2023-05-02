---
title: 210331 Basic Algorithm 소수 구하기
date: 2021-03-31 07:48:42
tags:
  - Algorithm
  - Sort
  - PseudoCode
categories:
  - Algorithm-problem-solving
---

## 기본 알고리즘 문제 Pseudo code + JavaScript code

<div align="center">
  <img src="/images/post_images/210331_prime-number.jpeg" alt="소수(Prime number)">
</div>

내가 처음 풀이한 코드인데 소수인지 아닌지의 판단을 1부터 자기자신의 숫자 범위내의 숫자로 나눴을때 0으로 나누어 떨어지는 경우의 수가 2인 경우(1과 자기자신)에 결과 리스트에 담아서 정답을 출력하였다.

나의 풀이에서는 1부터 자기자신의 숫자까지 모두 순회를 하였는데 이렇게 순회를 할 필요가 없다.
2부터 자기자신의 제곱근까지의 범위의 숫자로 나눠서 0으로 떨어지는 경우가 있다면 이는 소수(Prime number)가 아니기 때문에 이런 식으로 문제를 해결할 수도 있다.

## **Solution 1**

<!-- more -->

```javascript
const fs = require('fs');
const stdin = (process.platform === 'linux'
  ? fs.readFileSync('/dev/stdin').toString()
  : `9
32 55 62 20 250 370 200 30 100`
).split('\n');
const input = (() => {
  let line = 0;
  return () => stdin[line++];
})();

{
  let numOfNumbers = 0;
  let numberList = [];
  for (let i = 0; i < 2; i++) {
    if (i === 0) numOfNumbers = Number(input());
    if (i === 1)
      numberList = input()
        .split(' ')
        .map((number) => Number(number));
  }

  console.log(numberList);
  // reversed된 number값을 기존의 numberList에 업데이트 해주기 위해서
  // index로 접근
  for (let i = 0; i < numberList.length; i++) {
    let reversedNumber = '';
    let temp = numberList[i];
    while (temp) {
      reversedNumber += temp % 10;
      temp = Math.floor(temp / 10);
    }
    numberList[i] = Number(reversedNumber);
  }
  let primeNumberList = [];
  for (let number of numberList) {
    let zeroRemainderCount = 0;
    for (let i = 1; i <= number; i++) {
      if (number % i === 0) {
        zeroRemainderCount += 1;
      }
    }
    if (zeroRemainderCount === 2) primeNumberList.push(number);
  }
  console.log(primeNumberList.join(' '));
}
```

## **Solution 2**

```javascript
function isPrime(num){
  if (num === 1) return false;
  for (let i = 2; i <= parseInt(Math.sqrt(num); i++) {
    if(num % i === 0) return false;
  }
  return true;
}

function solution(arr) {
  let answer = [];
  for(let x of arr) {
    let res = 0;
    while(x) {
      let t = x % 10;
      res = res * 10 + t;
      x = parseInt(x / 10);
    }
    if(isPrime(res)) answer.push(res);
  }
  return answer;
}

let arr = [32, 55, 62, 20, 250, 370, 200, 30, 100];
console.log(solution(arr));
```
