---
title: 210424 TypeScript TIL-1 - 구조분해 매개변수의 타입지정, 타입 가드(Type Guard), 객체의 타입이 복잡해지는 경우의 타입지정(interface)
date: 2021-04-24 11:30:00
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - TypeScript
---

<div align="center">
  <img src="/images/post_images/typescript_logo.png" alt="TypeScript"/>
</div>

## <ins><b>복잡한 인수를 넘겨줄때는 객체 타입으로 작성</b></ins>

함수의 매개변수가 단순한 경우에는 그냥 각 각의 인수를 넣어서 호출해줘도 되지만, 만약에 함수의 매개변수로 넘겨줘야 되는 인수의 갯수가 많아지는 경우, 각 각의 인수가 어떤 역할을 하는지 파악하려면 다시 그 함수를 참조해야 한다.
따라서 넘겨줘야 되는 인수의 갯수가 늘어나는 경우 객체 타입으로 작성해서 각 인수의 역할에 대해서 유추할 수 있도록 작성을 해주면 코드의 가독성이 높아진다.

```javascript
Example({ flag: true, id: '1' });
```

## <ins><b>함수의 구조분해 매개변수에 타입 지정해주기</b></ins>

구조분해한 매개변수의 경우에는 구조분해한 변수의 뒤에 타입을 지정하지 않고 구조분해한 {}뒤에 콜론을 찍고 타입을 지정해준다.

```javascript
function Example({ flag, id }: { flag: boolean, id: string }) {
  ...
}
```

## <ins><b>타입 가드(Type Guard)</b></ins>

타입 가드를 사용하면 조건문에서 객체의 타입을 좁혀나갈 수 있다.
타입 가드란 함수의`특정 인자가 어떤 타입인지 구분`하기 위한 값을 반환하는 함수이다.

### <ins><b>사용자 정의 Type Guard</b></ins>

만약 Data 객체가 A 또는 B 객체로 구분된다면 아래와 같이 사용자 정의 Type Guards 방식으로 data 객체의 타입을 좁힐 수 있다.

  <!-- more -->

```javascript
function isA(data: Data): data is A {
  // 만약 data 객체에 valueA 속성이 있다면,
  // data는 객체 A이다.
  if(data.valueA){
    return true;
  }
  return false;
}
```

```javascript
function isB(data: Data): data is B {
  // 만약 data 깨체에 valueB 속성이 있다면,
  // data는 객체 B이다.
  if(data.valueB){
    return true;
  }
  return false;
}
```

자바스크립트에서는 풍부한 런타임 내부검사 (runtime introspection support)를 지원하지 않는다. 그래서 자바스크립트 객체를 사용할 때에는 instanceof 나 typeof와 같은 연산자로 엑세스 불가능하다.
그런데 타입스크립트에서는 사용자 정의 타입 가드를 사용해서 instanceof나 typeof와 같은 효과를 낼 수 있다.

```javascript
interface Foo {
  foo: number;
  common: string;
}

interface Bar {
  bar: number;
  common: string;
}

// 사용자 정의 Type Guard
function isFoo(arg: any): arg is Foo {
  return arg.foo !== undefined;
}

function doStuff(arg: Foo | Bar){
  if(isFoo(arg)){
    console.log(arg.foo);
  } else {
    console.log(arg.bar);
  }
}

doStuff({ foo: 123, common: '123' });
doStuff({ bar: 123, common: '123' });
```

### <ins><b>리터럴 Type Guard</b></ins>

리터럴 값의 경우 `===` / `==` / `!==` / `!=` 연산자를 사용해서 타입을 구분할 수 있다.

```javascript
type TriState = 'yes' | 'no' | 'unknown';

function logOutState(state: TriState) {
  if (state === 'yes') {
    console.log('사용자가 yes를 선택했습니다.');
  } else if (state === 'no') {
    console.log('사용자가 no를 선택했습니다.');
  } else {
    console.log('사용자가 아직 결정을 내리지 못했습다.');
  }
}
```

**참고 :** [https://radlohead.gitbook.io/typescript-deep-dive/type-system/typeguard](https://radlohead.gitbook.io/typescript-deep-dive/type-system/typeguard)

### <ins><b>객체의 타입이 복잡해지는 경우의 타입선언</b></ins>

객체의 타입이 복잡해지는 경우 별도로 interface로 빼서 타입을 정의해주면 코드가 간결해진다.

```javascript
interface IExample {
  data: number,
  flag: boolean,
  result: string
}

// 매개변수의 타입지정으로 인해 코드의 가독성이 떨어진다.
function getResult({ data, flag, result }: { data: number, flag: boolean, result: string }){
  ......
}

// 구조분해 매개변수의 타입을 별도의 interface로 빼서 코드를 간결하게 작성한다.(코드의 가독성 좋아진다)
function getResult({ data, flag, result }: IExample){
  ......
}
```
