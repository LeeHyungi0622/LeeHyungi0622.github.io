---
title: 210409 JavaScript의 this binding에 대한 이야기
date: 2021-04-09 00:13:42
tags:
  - this-binding

categories:
  - JavaScript
---

<div align="center">
  <img src="/images/post_images/210409_javascript_this_binding.jpeg" alt="자바스크립트에서의 this" />
</div>

이번 포스팅에서는 JavaScript에서의 this binding에 대해서 정리를 해보려고 한다.
이는 중요한 개념이기 때문에 나의 말로써 다른 사람에게 설명할 수 있을 정도로 연습을 해둬야 한다.

## 그럼 JavaScript에서 this란 무엇일까?

갑자기 누군가 JavaScript에서 this가 무엇이냐고 물어본다면 `간단하게 현재 실행되는 코드의 실행 컨텍스트를 가르킨다`라고 대답할 수 있다.
JavaScript는 Script 언어로, `interpreter`가 코드를 라인단위로 읽고 해석한 뒤에 실행을 시킨다.
이때 interpreter에 의해 현시점에서 실행되는 JavaScript 코드의 환경(스코프)를 실행 컨텍스트(execution context)라고 정의한다.
JavaScript에서는 내부에서 이런 실행 컨텍스트를 Call stack에서 관리를 하고 <ins><b>실행되는 시점에 자주 변경되는 실행 컨텍스트를 이 this라는 녀석이 가르키고 있다.</b></ins>

  <!-- more -->

## 그럼 this는 구체적으로 무엇을 가르키는가?

this binding(this에 binding되는 값)은 함수 호출 방식(함수가 어떻게 호출되었는지에 따라)에 따라 동적으로 결정된다.

이전에 스코프에 대한 블로그 포스팅을 했을때 JavaScript에서는 함수의 상위 스코프를 경정하는 방식인 Lexical scope는 함수 정의가 평가되어 함수 객체가 생성되는 시점(함수가 선언되는 지점)에 상위 스코프를 결정한다.
하지만 `this binding은 함수의 호출 시점에 의해 결정`이 된다.

여기서 주의해야 될 것은 동일한 함수도 다양한 방식으로 호출이 될 수 있으며, 경우에 따라 this에 binding되는 값도 달라진다는 것이다.

## **첫 번째, default binding**

기본적으로 this는 전역 객체를 가르킨다. <ins><b>Node환경에서는 global 객체를, Browser에서는 window 객체를 가르키게 된다.</b></ins>

`Browser 환경에서의 this`

<div align="center">
  <img src="/images/post_images/210409_this_binding.png" alt="브라우저 환경에서의 this" />
</div>

`NodeJS 환경에서의 this`

```zsh
Welcome to Node.js v12.16.0.
Type ".help" for more information.
> console.log(this);
Object [global] {
  global: [Circular],
  clearInterval: [Function: clearInterval],
  clearTimeout: [Function: clearTimeout],
  setInterval: [Function: setInterval],
  setTimeout: [Function: setTimeout] { [Symbol(util.promisify.custom)]: [Function] },
  queueMicrotask: [Function: queueMicrotask],
  clearImmediate: [Function: clearImmediate],
  setImmediate: [Function: setImmediate] {
    [Symbol(util.promisify.custom)]: [Function]
  }
}
```

## **두 번째, 함수의 호출 방식에 따른 this binding**

### **일반 함수 호출**

일반 함수를 호출하는 경우에는 기본적으로 this에 전역 객체(global object)가 binding된다.

```javascript
function checkThisInNormalFunc() {
  console.log(this); //window
}

checkThisInNormalFunc();
console.log(this); //window
```

만약에 일반 함수의 내/외부에서 `strict mode`를 사용한다면, 함수 내부에서의 this는 전역객체를 binding하지 않는다.

```javascript
function checkThisInNormalFunc() {
  'use strict';
  console.log(this === window); //false
}

checkThisInNormalFunc();
console.log(this === window); //true
```

일반 함수에서의 this는 객체의 property나 method를 참조하기 위한 자기 참조 변수이기 때문에 객체를 생성하지 않으면 일반 함수에서 this는 의미가 없다. 따라서 위와같이 일반 함수 내부에서 `'strict mode'`를 사용하게 되면 this는 전역 객체가 아닌 `undefined`가 binding된다.

만약에 메서드 내에서 정의한 중첩 함수를 일반 함수로 호출을 하게 되면 중첩 함수 내부의 this에는 전역 객체가 binding된다.

```javascript
// var 키워드로 선언된 전역 변수의 경우에는 전역 객체의 프로퍼티가 된다.
var value = 1;
// 단 const 키워드로 선언한 전역 변수의 경우에는 전역 객체의 프로퍼티가 아니다.

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // { value: 100, foo: f }
    console.log("foo's this.value: ", this.value); // 100

    // method 내에서 정의한 중첩 함수
    function bar() {
      console.log("bar's this: ", this); //window
      console.log("bar's this.value: ", this.value); // 1
    }
    // method 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩 된다.
    bar();

    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  }
};

obj.foo();
```

어떤 함수라도 일반함수로 호출(중첩함수, 콜백함수 포함)이 되면, this에는 전역 객체가 바인딩된다.
이 부분은 본래 중첩 함수나 콜백 함수가 외부 함수를 돕는 `헬퍼 함수의 역할(외부 함수의 일부 로직을 대신 처리)`을 한다는 것을 고려했을때, 외부 함수인 method와 중첩 함수 또는 콜백 함수의 this가 일치하지 않는 것은 중첩 함수 또는 콜백 함수가 헬퍼 함수로써 동작하기 어렵다는 의미가 된다.

앞서 예시로 작성한 `setTimeout()`함수에 전달된 callback 함수내의 this에는 전역 객체가 binding되기 때문에 this.value는 obj의 value 프로퍼티가 아닌 전역 객체의 value 프로퍼티를 참조한다.
`var 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 되기 때문에 window.value는 1로써 참조`된다.

method 내부의 중첩 함수나 콜백 함수의 this binding을 method의 this binding과 일치시키기 위해서는 아래와 같이 method내에서 this를 특정 변수에 담고 해당 변수를 이용해서 콜백함수 내부에서는 method내의 this를 참조할 수 있다.

```javascript
var value = 1;
const obj = {
  value: 100,
  foo() {
    const that = this;
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  }
};

obj.foo();
```

위와같이 method 내의 this를 별도의 변수에 담아 method 내부의 중첩 함수나 콜백 함수에서 해당 변수를 통해 method에서의 this를 참조하는 방식 이외에도 명시적으로 binding할 수 있는 `Function.prototype.apply, Function.prototype.call, Function.prototype.bind` 메서드를 활용한 방식들이 있다.

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // callback함수에서 명시적으로 this를 binding한다.
    setTimeout(
      function () {
        console.log(this.value); // 100
      }.bind(this),
      100
    );
  }
};

obj.foo();
```

## **화살표 함수를 사용해서 this를 바인딩**

화살표 함수(arrow function)에서는 this를 직접적으로 binding하지 않고 상위 스코프의 this를 가르키기 때문에 가능하다.

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가르킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  }
};

obj.foo();
```

## **메서드 호출**

메서드 내부에서의 this는 메서드를 호출한 객체(메서드를 호출할 때 메서드의 이름 앞의 마침표(.) 연산자 앞에 기술된 객체)가 바인딩된다. (`메서드를 소유한 객체가 아닌 호출한 객체에 바인딩이 된다`)

```javascript
const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 binding된다.
    return this.name;
  }
};
// 메서드 getName()을 호출한 객체는 person이다.
console.log(person.getName()); // Lee
```

위에서 정의한 getName() 메서드는 person 객체의 메서드로써 정의가 되어있다. 메서드는 프로퍼티에 바인딩된 함수이다. 따라서 person 객체의 `getName 프로퍼티가 가르키고 있는 함수 객체는 person 객체에 포함된 것이 아닌 독립적으로 존재하는 별도의 객체`이다.
getName 프로퍼티가 함수 객체를 가르키고 있는 것이다.

이 말은 즉슨 getName 프로퍼티가 가르키는 함수 객체인 getName 메서드는 다른 객체의 프로퍼티에 할당해서 다른 객체의 메서드가 될 수도 있고 일반 변수에 할당해서 일반 함수로써 호출될 수도 있다.

```javascript
const anotherPerson = {
  name: 'Kim'
};

// getName method를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName();

console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당해서 일반함수로써 호출
const getName = person.getName;

console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서의 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 built-in propert이고, default value는 ''이다.
// Node.js 환경에서의 this.name은 undefined이다.
```

<ins><b>메서드 내부의 this는 프로퍼티로 메서드를 가지고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩이 된다.</b></ins>

## **프로토 타입 메서드 내부에서의 this**

프로토 타입 메서드 내부에서의 this 또한 앞서 살펴본 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩된다.

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person('Lee');

// getName method를 호출한 객체는 me이다.
console.log(me.getName()); // Lee

Person.prototype.name = 'Kim';

// getName method를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // Kim
```

## **생성자 함수 내부에서의 this**

생성자 함수 내부의 this에는 생성자 함수가 생성할 객체 인스턴스가 바인딩된다.
생성자 함수는 객체(인스턴스)를 생성하는 함수이다.

```javascript
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 객체 인스턴스를 가르킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle1 = new Circle(5);
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

앞서 bind 메서드를 사용해서 콜백 함수의 메서드 내부에 this를 바인딩해주었다.
bind이외에도 apply, call 등의 메서드가 있는데, 이 메서드들은 Function.prototype의 메서드이기 때문에 이 메서드들은 모든 함수들이 상속받아 사용할 수 있다.

**apply와 call 메서드는 모두 함수를 호출하는 기능**을 가진다.
apply와 call 메서드는 ㅎ마수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 binding한다.

<ins><b>apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다르고 동일한 동작을 한다.</b></ins>

```javascript
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 바인딩할 객체
const thisArg = { a: 1 };

// apply 메서드는 호출할 함수의 인수를 배열로 묶어서 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}
```

apply와 call 메서드의 대표적인 용도는 `argument 객체와 같은 유사 배열 객체에 메서드를 사용하는 경우`이다.
argument 객체는 배열이 아니기 때문에 slice와 같은 배열의 메서드를 사용할 수 없다. 하지만 apply나 call 메서드를 이용하면 가능하다.

```javascript
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);
  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

Function.prototype.bind 메서드는 apply와 call 메서드와 달리 함수를 호출하지 않고 this로 사용할 객체만 전달한다.

```javascript
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding.bind(thisArg));
// bind 메서드는 함수를 호출하지 않으므로 명시적으로 호출해줘야 한다.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

앞서 실습해본 것 처럼 bind 메서드는 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해서 유용하게 사용된다.
