---
title: 210219 TypeScript Book TIL
date: 2021-02-19 06:35:42
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - TypeScript
---

![](/images/post_images/typescript_logo.png)

> **이 포스팅은 O'Reilly TypeScript책을 통해 공부한 내용과 실습한 내용을 기반으로 작성하였습니다.**

## **오늘 공부한 내용**

### 타입별칭

#### 일반적으로 변수를 선언하듯이 type 키워드로 type을 선언해서 사용할 수 있다.

```javascript
type Age = number
type persono = {
  name: string
  age: Age
}
```

```javascript
type Person = {
  name: string
  age: number
}
type Age = number
let age: Age = 55
let driver: Person = {
  name: 'James May',
  age: age
}
```

#### 타입의 union과 intersection type

  <!-- more -->

실전에서는 intersection보다는 union을 자주 사용한다.

```javascript
// 타입의 union과 intersection type
type Cat = { name: string, purrs: boolean };
type Dog = { name: string, barks: boolean, wags: boolean };
type CatOrDogOrBoth = Cat | Dog;
// Union : Cat과 Dog의 모든 속성을 가질 수 있는 변수를 설정할 수 있다.
let catOrDog: CatOrDogOrBoth = {
  name: 'name',
  purrs: false,
  barks: true,
  wags: false
};
```

아래 함수는 조건이 참이면 반환 타입을 string으로, 그렇지 않으면 number로 한다는 의미의 함수이다. 즉, string | number를 반환한다.

```javascript
function(a: string, b: number){
  return a || b
}
```

### 배열

#### 이형배열(heterogeneous) - union

타입스크립트에서는 배열을 동형(homogeneous)로, 배열의 모든 항목이 같은 타입을 갖도록 설계한다.

```javascript
// 이형배열에서의 union사용
let a = [1, 2, 3]; // number[]
var b = ['a', 'b']; // string[]
let d = [1, 'a']; //(string | number)[]
const e = [2, 'b']; // (string | number)[]
```

타입스크립트에서는 아래와 같이 우선 배열에서 삽입한 데이터의 타입을 기준으로 배열의 타입을 추론한다.

```javascript
let f = ['red']; // string[] array로 정의
f.push('blue');
f.push(true); // error
```

아무 데이터도 넣지 않고 배열을 초기화시키면 `any[]`type으로 정의된다.

```javascript
let g = []; // any[]
g.push(1); // number[]
g.push('red'); // (string | number)[]
```

### 튜플

배열의 서브타입으로, 튜플은 길이가 고정되었고, 각 index의 type이 이미 알려진 배열의 일종이다. 튜플을 선언할때에는 타입을 명시해야 한다.
자바스크립트에서의 배열과 튜플은 대괄호를 사용하고, 타입스크립트에서는 대괄호를 배열 타입으로 추론한다.
튜플을 잘 활용하면 순수 배열에 비해 안전성을 높일 수 있다. 튜플의 사용은 권장된다.

```javascript
let b: [string, string, number] = ['malcolm', 'gladwell', 1963];
```

튜플은 타입선언부에 선택적 요소를 지정할 수 있다.

```javascript
let trainFares: [number, number?][] = [
  [3.75],
  [8.25, 7.70],
  [10.50]
]
// 아래의 표현과 위의 표현은 같다.
let moreTrainFares: ([number] | [number, number])[] = [
  //.......
]
```

나머지 요소(...)를 사용해서 최소의 길이를 갖도록 설정

```javascript
let friends: [string, ...string[]] = ['Sara', 'Tali', 'Chloe', 'Claire']
// 이형배열
let list: [number, boolean, ...string[]] = [1, false, 'a', 'b', 'c']
```

#### 읽기전용 배열과 튜플만들기

읽기전용타입의 배열에서는 읽기만 가능하다.

```javascript
let as: readonly number[] = [1, 2, 3] // readonly number[]
let bs: readonly number[] = as.concat(4) // readonly number[]
let three = bs[2] // number
```

#### 다양한 읽기 전용 배열과 튜플의 선언방식

```javascript
type A = readonly string[] // readonly string[]
type B = ReadonlyArray<string> // readonly string[]
type C = Readonly<string[]> // readonly string[]

type D = readonly [number, string] // readonly [number, string]
type E = Readonly<[number, string]> // readonly [number, string]

```

### null, undefined, void, never

자바스크립트에서의 null과 undefined는 값의 부재를 의미하지만 타입스크립트에서는 undefined 타입의 값은 undefined, null 타입의 값은 오직 null뿐임을 의미한다.

undefined: 아직 값을 정의하지 않았음을 의미

null : 값의 부재를 의미

void와 never는 값의 부재를 좀 더 세밀하게 분류하는 특수하고 특별한 용도의 타입니다.

**void** : 명시적으로 아무것도 반환하지 않는 함수 반환 타입

**never** : <ins><b>절대 반환하지 않는 함수 타입을 가르킨다.</b></ins> 예를들어 함수에서 exception을 던지거나 while과 같은 반복문으로 영원히 실행이 되며 반환하지 않는 경우가 이에 해당한다.

```javascript
// never를 반환하는 함수
function d() {
  throw TypeError('I always error');
}

// never를 반환하는 또 다른 함수
function e() {
  while (true) {
    doSomething();
  }
}
```

타입에 있어 unknown이 모든 타입의 상위타입이라면 never는 모든 타입의 서브타입니다. 이 말은 즉슨, 모든 타입에 never를 할당할 수도 있다는 의미이다.

### 열거형(enum)

열거형이란 해당 타입으로 사용할 수 있는 값을 열거하는 기법이다. 키가 컴파일될때 고정된 객체로서 정의되며 이 키를 값에 할당하는 순서가 없는 자료구조이다.
열거형의 예로 `문자열-문자열`의 형태나 `문자열-숫자`로 mapping하는 열거형이 있다.
작성법은 열거형의 이름은 `단수형 명사`를 쓰고, 첫 문자는 대문자로 작성하며, 키 또한 첫 문자는 대문자로 작성하는 것이 관례이다.

```javascript
enum Language {
  English,
  Spanish,
  Russian
}
```

열거형의 각 키값에 별도로 값을 할당하지 않으면 타입스크립트가 자동으로 적절한 숫자를 추론해서 할당한다.
하지만 필요에 따라 아래와 같이 명시적으로 설정을 해줄수도 있다.
명시적으로 설정해 줄 수 있는 값으로는 수식, 문자열, 문자열과 숫자의 혼합을 사용할 수 있다.

```javascript
enum Language {
  English = 0,
  Spanish = 1,
  Russian = 2
}
```

열거형 값에 접근을 할때에는 일반적으로 객체에서 값을 가져올때와 같이 점 또는 괄호 표기를 사용해서 값에 접근할 수 있다.

```javascript
let myFirstLanguage = Language.Russian; // Language
let mySecondLanguage = Language['English']; // Language
```

좀 더 안전하게 열거형(enum)을 사용하기 위해서는 `const enum`을 사용하도록 한다.
값에 대한 접근은 .(dot)이나 [문자열 literal]를 통해 접근할 수 있다. ([index]로는 접근할 수 없다)
ex. Language['English'] (0), Language.English (0), Language[0] (X)

```javascript
const enum Language {
  English,
  Spanish,
  Russian
}
```

열거형을 사용할때에는 아래와 같이 문자열 값을 갖는 열거형을 사용하도록 한다.

```javascript
const enum Flippable {
  Burger = 'Burger',
  Chair = 'Chair',
  Cup = 'Cup',
  Skateboard = 'Skateboard',
  Table = 'Table'
}

function flip(f: Flippable) {
  return 'flipped it'
}

flip(Flippable.Chair)
flip(Flippable.Cup)
```

## 연습문제

```javascript
// 연습문제 1
1. 값의 추론값 예상하기
let a = 1042 // number
let b = 'apples and oranges' // string
const c = 'pineapple' // 'pineapple'
let d = [true, true, false] // boolean[]
let e = {type: 'ficus'} // { type: string}
let f = [1, false] // (number | boolean)[]
const g = [3] // number[]
let h = null // any

// 연습문제 2
let l: unknown = 4
if (typeof l === 'number'){
    let m = l * 2
    console.log(m)
}
```
