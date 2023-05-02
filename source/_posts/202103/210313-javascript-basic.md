---
title: 210313 JavaScript TIL 1일차 - 변수의 선언과 정의, Hoisting, var, let, const, TDZ
date: 2021-03-13 12:21:42
tags:
  - Self-Development
  - JavaScript
  - JavaScript-Basic
  - Incomplete

categories:
  - JavaScript
---

![](/images/post_images/javascript_logo.png)

이번주 월요일부터 새롭게 시작한 자바스크립트 강의를 들으면서 데레사 강사님으로부터 HTML/CSS 수업을 들었을 때와 마찬가지로 내가 제대로 JavaScript를 알지 못했었구나라는 생각이 들었다. 그래서 강사님이 말씀하시는 내용 하나 하나 놓치지 않으려고 집중하며 들어보았다.

상대적으로 다른 언어에 비해 러닝커브가 낮기 때문에 처음 배울때에는 마냥 쉽게만 느껴지는 자바스크립트지만, 제대로 그 언어의 특성을 알고 배우기란 여간 쉬운 일이 아니다.

그래서 수업시간에 배웠던 내용 중에 내가 정말 나중에 개발자로서 성장을 하면서 다시금 돌아보면 유익할 것 같은 내용을 위주로 개인적인 생각을 담아 정리를 해보려고 한다.

지금 알고 있는 것이 전부라고 생각하지는 않는다. 다만 현재의 지식의 한계를 인지하고 현재 알고 있는 부분에 대해 블로그에 정리를 해두고 나중에 알게 된 내용에 대해서 새롭게 업데이트를 할 생각으로 블로그 글을 남겨놓는다.

## **Hoisting?**

첫 번째 수업때 가장 기억에 남는 개념은 바로 이 Hoisting이라는 개념이었다.

우선 Hoisting에 대해서 살펴보기 이전에 `변수의 선언과 정의`에 대해서 이야기해보려고 한다.

  <!-- more -->

`변수의 선언이란` 컴파일러가 참조할 식별자(변수의 타입과 함수의 인수목록)와 이름(변수, 함수, 클래스의 이름)을 알리는 것을 의미한다. 이 과정에서는 별도의 메모리 영역에 영향을 주지 않기 때문에 여러번 중복되어도 문제가 되지 않는다.

`변수의 정의란` 식별자와 이름으로부터 코드를 생성하여 함수가 호출되거나 변수를 사용할 때 생성된 코드를 참조하는 것을 말한다.

이렇게 변수의 사용을 선언과 정의 두 가지로 나눠서 살펴 볼 수 있다.
실제로 우리가 `var a = 10`으로 a라는 변수를 10이라는 값으로 초기화해서 사용을 한다면 눈에는 하나의 문(statement)으로 보이지만, 실제로는 선언과 정의, 두 개의 파트로 나눠져 있는 것이다.

```javascript
var a;
a = 10;
```

앞에서 설명한 내용을 토대로 이제 hoisting에 대해서 살펴보자.

이 hoisting이란 컴파일 단계에서 컴파일러가 파싱을 하면서 선언에 해당하는 내용을 스코프 내의 최상단으로 끌어 올리는 것을 말한다.  
이러한 이유로 코드 구현시에 아래와 같은 코드 구조(선 정의 후 선언)가 가능하다.

```javascript
a = 10;
var a;
```

`2021.03.14 update`
함수는 선언된 위치와 상관없이 동일하게 호출되어 사용된다.

```javascript
greet('World'); // 'Hello, World!'
function greet(who) {
  return `Hello, ${who}!`;
}
greet('Earth'); // 'Hello, Earth!'
```

<ins><b>함수 선언문과 함수 표현식의 Hoisting</b></ins>
함수 선언문으로 정의한 함수를 함수 선언문 이전에 호출하게 되면, 함수 호이스팅에 의해 호출이 가능한 상태가 되지만, 함수 표현식으로 함수를 정의하면 함수 호이스팅이 발생하는 것이 아니라 변수 호이스팅이 발생한다.

```javascript
firstHello();
secondHello();

// 함수 선언문
function firstHello() {
  console.log('first hello');
}

// 함수 표현식
var sh = function secondHello() {
  console.log('second hello');
};
```

<b>JavaScript의 Parser 내부의 Hoisting의 결과</b>

```javascript
var sh; // 함수 표현식의 변수값 (선언)

// 함수 선언문
function firstHello() {
  console.log('first hello');
}

firstHello(); // OK
secondHello(); // 에러발생

sh = function secondHello() {
  console.log('second hello');
};
```

위와같이 hoisting은 함수 선언문과 함수 표현식에서 서로 다르게 동작하기 때문에 주의해야 한다.

그렇다면, 이제 다음주 월요일에 배우게 될 변수 사용시에 사용할 키워드인 let과 const는 hoisting이 일어나지 않는 것일까?

이 부분에 대해서 집에 돌아가는 길 지하철 안에서 궁금증을 참지 못하고 이래 저래 구글링을 해보았다.

우선 정답은 let과 const로 선언된 변수 또한 var로 선언된 변수와 같이 hoisting이 발생한다. 하지만 `var의 경우 정의 전에 해당 변수에 접근하려고 하면 undefined을 반환`하지만 `let/const의 경우에는 정의 전에 해당 변수에 접근하려고 하면, ReferenceError가 발생`한다.

var와 let/const의 선언에서 차이가 나는 이유는 바로 **TDZ(Temporal Dead Zone)에 의한 제약** 때문이다.
갑자기 뜬금없지만 TDZ라는 용어를 보자마자 생각났던 것이 군 복무시에 잠깐 파견을 나갔었던 DMZ(Demilitarized Zone)였고, 두 용어 모두 Zone이라는 공통점이 있다. 이렇게 나의 경험과 학습을 연관시키려는 이유는 HTML/CSS의 데레사 강사님의 수업방식으로부터 배웠다. 강사님은 어떤 예시를 설명해주실때 직접 경험하신 검험담으로 어려운 내용도 좀 더 확장해서 학습할 수 있도록 구체적으로 설명해주셨다. 그래서 나도 되도록이면 나의 경험과 연결시켜서 학습해보려고 하고 있다.

자 이제 돌아와서 TDZ와 DMZ는 Zone이라는 공통점을 가지고 있고, 뭔가 한정된 영역이라는 느낌을 준다. 자 그럼 이런 느낌으로 한 번 TDZ(Temporal Dead Zone)에 대해서 살펴보자.

앞서 이미 언급을 했지만, let과 const도 hoisting이 발생하지만, 선언 이전에 정의(엑세스)를 하려고 하면 에러가 발생한다. 이는 선언 이전에 정의(엑세스)를 하려는 영역은 TDZ에 의해 관리가 되고, ReferenceError를 반환하는 것이다.

TDZ 시맨틱은 선언 전에 정의(엑세스)를 금지한다.

```javascript
// TDZ(Temporal Dead Zone)

name; // ReferenceError (managed by TDZ(Temporal Dead Zone))

const name = 'Lee Hyungi'; // Declaration & Initialization

name;
```

앞서 작성한 코드에서 name은 const라는 키워드로 선언이 되기 전까지 TDZ라는 영역에 있다고 볼 수 있다.

TDZ에 영향을 받는 구문은 앞서 살펴본 const 변수와 let 변수, class 구문, 생성자 내부의 super(), 기본 함수 매개변수가 있다.

이 중에서 생성자 내부의 super()와 기본 함수 매개변수에 대해서 좀 더 자세히 알아보자.

## 생성자(constructor()) 내부의 super()

부모 클래스를 상속받았다면 생성자 안에서 super()를 호출하기 전까지 this binding은 TDZ 영역에 있다.

이전 파이썬 수업때 엘리베이터 추상 클래스를 구현하고, 추상클래스를 상속받아 엘리베이터 클래스를 구현해보았는데, 그때를 생각하며 아래의 Elevator 클래스를 상속받는 HydraulicElevator(유압식 엘리베이터) 클래스를 만들어서 클래스 상속의 예시를 들어보았다.

```javascript
class HydraulicElevator extends Elevator {
  constructor(speed, capacity) {
    this.capacity = capacity;
    super(speed);
  }
}

const myElevator = new HydraulicElevator(100, 10);
```

위와같이 코드를 작성함게 되면 결과적으로 객체 인스턴스를 생성하는 시점에서 `ReferenceError`가 발생을 하게 된다. 그 이유는 앞에서 이미 언급을 했듯이 상속을 받았다면 상속을 받은 클래스 내부의 생성자에서는 super가 호출되기 전까지 this를 사용할 수 없다. (super()를 호출하기 전까지 this binding은 TDZ 영역에 있다)
따라서 아래와 같이 우선 상속받은 부모 클래스의 생성자를 호출하고 this를 사용해야 한다.

```javascript
class HydraulicElevator extends Elevator {
  constructor(speed, capacity) {
    super(speed);
    this.capacity = capacity;
  }
}

const myElevator = new HydraulicElevator(100, 10);
```

## 기본 함수 매개변수

아래와 같이 기본 함수의 매개변수를 작성해주게 되면 TDZ에 의해 ReferenceError가 발생하게 된다. 그 이유는 기본 함수의 매개변수의 Scope에 대해서 알아보면 이해할 수 있다.
잠깐 이게 왜 에러가 나는지 이해가 되지 않았었는데, 기본 함수의 매개변수의 스코프는 전역(global)과 함수(function)스코프의 중간에 위치한다.

따라서 지금 함수의 외부에 선언된 b와 기본 매개변수를 초기화하기 위해 사용한 b는 서로 다른 변수이며, 기본 매개변수로써 사용한 b를 초기화되지 않은 변수 b를 사용해서 초기화시키려고 하고 있기 때문에 ReferenceError가 발생하게 되는 것이다.

```javascript
const b = 1;
function plusDouble(b = b) {
  return b + b;
}

plusDouble();
```

따라서 위의 문제를 해결하기 위해서 아래와 같이 기본 매개변수의 이름과 외부에 선언한 기본 매개변수의 초기화를 위한 변수의 이름은 다르게 지정해서 사용하도록 한다.

```javascript
const init = 1;
function plusDouble(b = init) {
  return b + b;
}

plusDouble();
```
