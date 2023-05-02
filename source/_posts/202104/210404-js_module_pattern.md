---
title: 210404 JavaScript module pattern
date: 2021-04-04 11:13:42
tags:
  - JavaScript-module-pattern
  - JavaScript-unit-test
  - Jasmine
categories:
  - JavaScript-Test
---

![](/images/post_images/210404_js_module_pattern.png)

<br/>

이번 포스팅에서는 JavaScript에서 문제해결 패턴으로 가장 많이 사용되는 module pattern에 대해서 포스팅을 하려고 한다.
`이 module pattern은 JavaScript의 코드 관리 기법 중 하나`로 함수로 데이터를 감추고, 모듈 API를 담고 있는 객체를 반환하는 형태로 코드를 작성하며, `JavaScript의 특성상 객체를 핸들링하기 위한 방법론` 중 하나이다.
`JavaScript의 모듈 패턴`은 유효범위를 지정하는 언어와 같이 private, public 등의 캡슐화를 사용하는 방법이라고 볼 수 있다.
그리고 module pattern에는 두 가지 패턴이 있는데 `임의 모듈 패턴`과 `즉시 실행 함수(IIFE) 모듈 패턴(Singleton instance)`이 있다.

아래 임의 모듈 패턴과 즉시 실행함수 모듈 패턴(IIFE)를 보면 가장 첫 줄에 namespace pattern이 사용이 되었는데 이는 자바스크립트에서 함수 또는 변수 객체를 다룰때 중복된 이름의 사용으로 인한 문제를 방지하기 위한 것이다.
`global영역에 객체 고유의 영역을 지정`하고 `변수와 함수의 할당을 해당 namespace의 하위`로 두게 해서 중복된 이름으로 인한 오류를 예방하는 방법이다.
(`즉 모듈패턴이란 이 namespace pattern에 언어적 유효범위를 추가해놓은 것이라고 이해하면 된다`)

## **namespace 패턴**

```javascript
var App = App || {}; // declare namespace
App.getName = function () {
  return 'hyungilee';
};
App.hello = function () {
  return 'hello';
};
```

## **임의 모듈 패턴 : 임의 함수를 호출하여 생성하는 모듈**

여러 객체가 필요한 경우에 사용되는 패턴 방식이다.

```javascript
// namespace 만들기
var App = App || {};
// namespace에 함수를 추가. 의존성있는 God function을 주입
// Person이라는 module(함수)를 넣어준다.
App.Person = function (God) {
  // God module이 name을 생성하는 역할을 한다.
  // God module을 통해 생성한 name값을 변수에 초기화한다.
  var name = God.makeName();

  // API 노출 (getter, setter 함수를 가지는 객체를 반환)
  return {
    getName: function () {
      return name;
    },
    setName: function (newName) {
      name = newName;
    }
  };
};

const person = App.Person(God);
person.getName();
```

  <!-- more -->

## **즉시 실행 함수(IIFE) 모듈 패턴(Singleton instance) : 즉시 실행 함수(IIFE) 기반의 모듈**

singleton일 경우(단일 객체가 필요한 경우)에 사용되는 패턴 방식이다.

`즉시 실행 함수(IIFE) 모듈 패턴(싱글톤 인스턴스) 예제1`

```javascript
var App = App || {};
App.Person = (function () {
  let name = '';
  return {
    getName(God) {
      name = name || God.makeName();
      return name;
    },
    setName(newName) {
      name = newName;
    }
  };
})(); //함수 선언 즉시 실행한다. singleton
```

`즉시 실행 함수(IIFE) 모듈 패턴(싱글톤 인스턴스) 예제2`

```javascript
var Person = { name: 'hyungi', greeting: 'hi' };

var App = (function (person) {
  var sayHi = person.greeting;
  var sayHiToFriend = person.greeting + ' ' + person.name;
  var printHi = function () {
    return sayHi;
  };
  var printHiToFriend = function () {
    return sayHiToFriend;
  };
  return {
    sayHi: sayHi,
    sayHiToFriend: printHiToFriend
  };
})(Person);

console.log(App.sayHi());
console.log(App.sayHiToFriend());
```

<ins><b>위와같이 모듈을 생성하는데에는 두 가지 원칙이 필요하다.</b></ins>

1. `단일 책임 원칙`에 따라 모듈은 한 가지 역할만 한다.
   해당 역할만 집중함으로서 모듈을 더욱 튼튼하고 견고하게 만들고 테스트하기 쉬워진다.
2. 모듈 자신이 사용할 객체가 있다면 `의존성 주입`형태로 제공한다.
   팩토리 주입형태로 제공하기도 하며 테스트하기 쉬워진다.

**모듈을 작성함으로써 반환하는 `return문을 통해 공개될 영역과 내부적으로 처리할 영역을 구분`할 수 있게 된다.**
