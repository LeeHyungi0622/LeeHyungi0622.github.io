---
title: 210416 JavaScript와 친해지기 - 클로저(Closure)와 즉시실행 함수(IIEF)에 대한 이야기
date: 2021-04-16 11:00:00
tags:
  - Self-Development
  - basic-term
  - TIL
categories:
  - JavaScript
---

<div align="center">
  <img src="/images/post_images/210416_closure_iife_img.png" alt="Closure와 IIFE">
</div>

이번 포스팅에서는 아직은 조금 낯설은 클로저(Closure)에 대해서 정리를 해보려고 한다.

## 클로저(Closure)

클로저에 대한 정의는 ECMAScript에 없다. 다만 클로저는 자바스크립트가 채용하고 있는 기술적 기반/컨셉이다.
자바스크립트는 클로저를 이용하여 `스코프적 특징과 함수에 대한 명세를 구현`하였다.
다시 말해 클로저(Closure)는 ECMAScript의 명세에는 없지만 스코프와 스코프와 긴밀한 관련이 있는 1급 객체로서의 함수와 매우 관련이 깊다.

**자 그럼 누군가 클로저(Closure)가 뭐냐고 물어본다면 뭐라고 대답할 수 있을까?**

`inner function은 항상 outer function의 변수와 인자에 접근`을 할 수 있다. 비록 outer function이 이미 호출되어 값이 반환되었을지라도 outer function의 변수 객체에 접근이 가능한 이유가 바로 클로저(Closure) 덕분이다.

아직 설명이 부족하기 때문에 아래 간단한 예시를 살펴보자.

`simple example(1)`

```javascript
function add(a) {
  var x = '두 숫자의 합은 ';
  return function (b) {
    var sum = a + b;
    console.log(x + sum);
  };
}

var totalSum = add(10);
totalSum(5);

// output: 두 숫자의 합은 15

add(10);
/*
ƒ(b){
  var sum = a + b;
  console.log(x + sum);
}
*/

add(10)(4);
// output: 두 숫자의 합은 14
```

JavaScript에서 함수는 1급 객체(First-Class Objects)로써 정의되었기 때문에 위와같이 함수 내에서 함수를 반환할 수 있는 것이다.

자 그럼 위의 결과값부터 말을 하자면 `두 숫자의 합은 15`이다. 새로운 함수는 새로운 함수 실행 컨텍스트를 갖는다. 이전에 스코프에 대해서 블로그 포스팅을 했을때 이미 자세히 다뤘듯이 실행 컨텍스트 내부에는 코드가 실행되기 위한 다양한 정보(변수, 함수 등)들과 참조 속성에 대한 정보가 담긴다. 이 참조 속성이 상위 스코프의 실행 컨텍스트의 참조 속성으로 연결되어 변수를 참조하는 것을 `스코프 체인(Scope chain)`이라고 정의를 한다.

  <!-- more -->

자 그럼 위의 예시 코드에서 totalSum이라는 변수에 10이라는 인수로 add함수를 호출한 결과를 담았다. add 함수를 호출하는 과정에서 이 함수 실행 컨텍스트는 전역 실행 컨텍스트의 위에 쌓이게 된다. 그리고 실행되고 나서 호출스택(Call stack)으로부터 pop되었다. 그 다음에 5라는 인수를 넣어 totalSum이라는 함수를 호출하고 있다.
이미 앞에서 결과값을 말했듯이 출력내용은 `두 숫자의 합은 15`이다.
여기서 의문이 드는 점은 add 함수에 대한 함수 실행 컨텍스트는 호출 스택으로부터 pop되었고, 그렇다면 Scope chain에서도 존재를 하지 말아야 되는데, 결과값으로 15가 나온다는 것은 totalSum에 5의 인수를 넣어 호출하기 이전에 10이라는 인수를 넣어 add 함수를 호출한 것에 대한 변수 객체는 여전히 존재를 한다는 말이 된다.
`스코프 체인상에는 add 함수의 변수 객체`가 존재를 하고, 이후에 호출된 totalSum이라는 함수는 앞서 호출된 add 함수의 변수 객체를 참조할 수 있는 것이다. 이렇듯 `호출 스택에서 실행 컨텍스트가 사라진 뒤에도 사라진 실행 컨텍스트의 변수 객체를 참조할 수 있는 것이 바로 클로저(Closure) 덕분`이다.

두 번째 예시코드를 살펴보자. 앞서 살펴 본 예시에서 알 수 있듯이 <ins><b>Closure는 inner function으로부터 outer function의 스코프에 접근할 수 있도록 해주는 역할</b></ins>을 하며,
closure를 이용하면 private variable을 만들 수 있다.

아래의 예시코드에서 newAccount의 내부에는 인수로 받은 initialBalance로 값을 초기화하는 balance라는 변수가 있다. 이 변수가 newAccount라는 outer function에 의해서 보호되는 private variable이다.

`simple example(2)`

```javascript
// Closure

function newAccount(name, initialBalance) {
  let balance = initialBalance;
  function showBalance() {
    console.log(`Hey ${name}, your balance is ${balance}`);
  }
  return showBalance;
}

newAccount('susan', 500)();

const john = newAccount('john', 300);
const bob = newAccount('bob', 1000);

john();
bob();
```

`advanced example`

```javascript
function newAccount(name, initialBalance) {
  let balance = initialBalance;
  function showBalance() {
    console.log(`Hey ${name}, your balance is ${balance}`);
  }
  function deposit(amount) {
    balance += amount;
    showBalance();
  }
  function withdraw(amount) {
    if (amount > balance) {
      console.log(`Hey, ${name}, sorry not enough funds`);
      return;
    }
    balance -= amount;
    showBalance();
  }
  return { showBalance: showBalance, deposit: deposit, withdraw: withdraw };
}

const john = newAccount('john', 1000);
const bob = newAccount('bob', 1000);

john.showBalance();
john.deposit(400);
john.deposit(1000);
john.withdraw(400);
john.balance = 10000;
john.withdraw(2001);
bob.showBalance();
bob.deposit(400);
```

## **즉시 실행함수 표현(IIFE - Immediately-Invoked Function Expression)**

**즉시 실행함수 표현을 사용함으로써 생기는 이점**은 다음 두 가지가 있다.
**첫 번째,** `global scope pollution으로부터 회피할 수 있는 간단한 방법`이다.
**두 번째,** `함수와 함수내의 변수들의 스코프를 보호하기 위한 좋은 방법`이다.

하지만 다소 오래된 접근방법이기 때문에 module이라는 새로운 방식으로 코드를 작성하도록 하자.

간단하게 아래의 코드를 살펴보자.

```javascript
const num1 = 30;
const num2 = 50;

function add() {
  console.log(`the result is :  ${num1 + num2}`);
}

add();
```

위의 코드는 모두 전역 스코프에 변수와 함수를 선언하고 실행하고 있다. 이렇게 작성을 해주게 되면 변수 이름간의 충돌이 발생할 수 있다. 따라서 아래와 같이 즉시 실행함수 표현으로 함수를 실행해주게 되면 함수내에 선언된 변수를 보호할 수 있고 전역 스코프가 오염되는 것을 방지할 수 있다.

```javascript
(function () {
  const num3 = 30;
  const num4 = 50;
  console.log(`the result is :  ${num3 + num4}`);
})();
```

즉시 실행함수를 통해 실행하는 경우 인수를 넣어줄 수도 있고 실행한 결과값을 별도의 변수에 담아줄 수도 있다.

```javascript
const result = (function (num3, num4) {
  console.log(`the result is :  ${num3 + num4}`);
  return num3 + num4;
})(500, 900);

console.log(result);
```
