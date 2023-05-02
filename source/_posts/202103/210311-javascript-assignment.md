---
title: 210311 JavaScript assignment
date: 2021-03-11 16:14:42
secret: True
tags:
  - Self-Development
  - JavaScript

categories:
  - JavaScript
---

![](/images/post_images/javascript_logo.png)

  <!-- more -->

## 1. 변수 x가 10보다 크고 20보다 작을 때 변수 x를 출력하는 조건식을 완성하라

```javascript
var x = 15;

if (x > 10 && x < 20) {
  console.log(x);
}
```

### 1-1. 조건문을 사용하지 않고 삼항연산자로 풀이해보기

```javascript
console.log(x > 10 && x < 20 ? x : '');
```

### 1-2. 조건문을 사용하지 않고 단축평가를 활용해서 풀어보기

```javascript
console.log(x > 10 && x < 20 && x);
```

## 2. for문을 사용하여 0부터 10미만의 정수 중에서 짝수만을 작은 수부터 출력하시오.

```javascript
for (var i = 0; i < 10; i += 2) {
  console.log(i);
}
```

## 3. for문을 사용하여 0부터 10미만의 정수 중에서 짝수만을 작은 수부터 문자열로 출력하시오.

### 3-1. 문자열 변수로 연결해서 결과값 출력하기

```javascript
var result = '';
for (var i = 0; i < 10; i += 2) {
  result += i;
}
console.log(result);
```

### 3-2. 이 2) 결과값을 리스트에 담고 join()을 사용해서 문자열로 합치기 (파이썬과 동일 기능)

```javascript
var resultList = [];
for (var i = 0; i < 10; i += 2) {
  resultList.push(i);
}
console.log(resultList.join(''));
```

## 4. for문을 사용하여 0부터 10미만의 정수 중에서 홀수만을 큰 수부터 출력하시오.

```javascript
for (var i = 9; i >= 0; i -= 1) {
  if (i % 2 != 0) {
    console.log(i);
  }
}
```

## 5. while문을 사용하여 0부터 10미만의 정수 중에서 짝수만을 작은 수부터 출력하시오.

```javascript
conditionalValue = 0;

while (conditionalValue < 10) {
  if (conditionalValue % 2 === 0) {
    console.log(conditionalValue);
  }
  conditionalValue += 1;
}
```

## 6. while문을 사용하여 0부터 10 미만의 정수 중에서 홀수만을 큰수부터 출력하시오.

```javascript
conditionalValue = 10;

while (conditionalValue > 0) {
  if (conditionalValue % 2 !== 0) {
    console.log(conditionalValue);
  }
  conditionalValue -= 1;
}
```

## 7. for 문을 사용하여 0부터 10미만의 정수의 합을 출력하시오.

```javascript
sum = 0;
for (var i = 0; i < 10; i++) {
  sum += i;
}
console.log(sum);
```

## 8. 1부터 20미만의 정수 중에서 2 또는 3의 배수가 아닌 수의 총합을 구하시오.

```javascript
sum = 0;
for (var i = 1; i < 20; i += 1) {
  if (i % 2 !== 0 && i % 3 != 0) {
    sum += i;
  }
}
console.log(sum);
```

## 9. 1부터 20미만의 정수 중에서 2 또는 3의 배수인 수의 총합을 구하시오.

```javascript
sum = 0;
for (var i = 1; i < 20; i += 1) {
  if (i % 2 === 0 || i % 3 === 0) {
    sum += i;
  }
}
console.log(sum);
```

## 10. 두 개의 주사위를 던졌을 때, 눈의 합이 6이 되는 모든 경우의 수를 출력하시오.

```javascript
for (var i = 1; i <= 6; i += 1) {
  for (var j = 1; j <= 6; j += 1) {
    if (i + j === 6) {
      console.log(`[${i}, ${j}]`);
    }
  }
}
```

## 11. 삼각형 출력하기 - pattern1

```javascript
// 높이(line)가 5
for (var i = 1; i <= 5; i += 1) {
  var line = '';
  for (var j = 1; j <= i; j += 1) {
    line += '*';
  }
  console.log(line);
}
//output:
// *
// **
// ***
// ****
// *****
```

## 12. 삼각형 출력하기 - pattern2

```javascript
for (var i = 5; i >= 1; i -= 1) {
  var line = '';
  for (var k = 0; k <= 5 - i; k += 1) {
    line += ' ';
  }
  for (var j = 1; j <= i; j += 1) {
    line += '*';
  }
  console.log(line);
}
// output:
//  *****
//   ****
//    ***
//     **
//      *
```

## 13. 삼각형 출력하기 - pattern3

```javascript
for (var i = 5; i >= 1; i -= 1) {
  var line = '';
  for (var j = 1; j <= i; j += 1) {
    line += '*';
  }
  console.log(line);
}
// output:
// *****
// ****
// ***
// **
// *
```

## 14. 삼각형 출력하기 - pattern4

```javascript
for (var i = 1; i <= 5; i += 1) {
  var line = '';
  for (var k = 0; k <= 5 - i; k += 1) {
    line += ' ';
  }
  for (var j = 1; j <= i; j += 1) {
    line += '*';
  }
  console.log(line);
}
// output:
//      *
//     **
//    ***
//   ****
//  *****
```

## 15. 정삼각형 출력하기

```javascript
var count = 0;
for (var i = 1; i <= 10; i += 1) {
  var line = '';
  if (i % 2 !== 0) {
    count += 1;
    for (var j = 0; j <= 5 - count; j += 1) {
      line += ' ';
    }
    for (var k = 1; k <= i; k += 1) {
      line += '*';
    }
    console.log(line);
  }
}
// output :
//      *
//     ***
//    *****
//   *******
//  *********
```

## 16. 역정삼각형 출력하기

```javascript
var count = 5;
for (var i = 10; i >= 1; i -= 1) {
  var line = '';
  if (i % 2 !== 0) {
    for (var j = 5 - count; j >= 0; j -= 1) {
      line += ' ';
    }
    for (var k = i; k >= 1; k -= 1) {
      line += '*';
    }
    count -= 1;
    console.log(line);
  }
}
// output :
//  *********
//   *******
//    *****
//     ***
//      *
```
