---
title: 210423 TypeScript TIL-2 - TypeScript에서의 DOM element 조작, TypeScript 인라인 스타일, strictNullCheck 옵션, JavaScript에서의 클래스와 TypeScript에서의 클래스 정의, interface에서 객체내의 메서드 타입 정의, 제네릭(generic), 제네릭 예시 - addEventListener 함수
date: 2021-04-23 14:45:00
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

## <ins><b>TypeScript는 HTML을 인지하지 못한다.</b></ins>

TypeScript는 HTML 자체를 인식하지 못하기 때문에 HTML 내의 요소를 참조할때 null로 인식하는 경우가 있다.

따라서 확실하게 HTML에 요소가 존재하는 경우에는 개발자가 !로 존재를 보증해주거나 if 문으로 조건처리를 해서 작성을 해줘야 한다.

```javascript
if (document.querySelector('#id')) {
    (document.querySelector('#id') as HTMLElement).style.background = '......';
}
```

## <ins><b>TypeScript에서 inline styling</b></ins>

타입스크립트에서 inline 스타일링을 하기 위해 HTML element를 참조하게 되면 .style을 하거나 .textContent를 하는 경우에 에러가 발생한다.
그 이유는 아래와 같이 querySelector로 HTML 요소를 참조하게 되면 해당 참조 요소의 타입은 Element를 상속받는 제네릭 타입 E가 된다. 이 Element에는 style, textContent 속성이 없다.

```javascript
querySelector<E extends Element = Element>(selectors: string): E | null;
```

`따라서 Element type을 HTMLDivElement로 타입의 범위를 좁게 잡아줘야 한다.`

```javascript
document.querySelector('#id').style.background = '...'; // error

// as로 Type casting
(document.querySelector('#id') as HTMLDivElement).style.background = '...';

// generic으로 Type casting
document.querySelector<HTMLDivElement>('#id').style.background = '...';
```

## <ins><b>tsc로 타입스크립트 파일을 컴파일할때에는 파일명을 넣지 않는다.</b></ins>

타입스크립트 파일을 컴파일할때 `npx tsc -w`과 같이 컴파일할 파일을 명시하지 않도록 한다. 그 이유는 파일명을 구체적으로 명시해주게 되면, 프로젝트 폴더 내에 정의한 타입스크립트 설정파일(tsconfig.json)이 무시가 되서 설정된 사항들이 적용이 안될 수 있기 때문이다.

## <ins><b>비슷한 속성의 객체는 같은 interface로 타입을 정의할 수 있다.</b></ins>

타입스크립트의 장점은 interface에 작성해준 타입의 정의를 보고 프로그램의 구조에 대한 힌트를 얻을 수 있다.

아래에 사람(Person)과 레스토랑(Restaurant) 인터페이스를 작성해보았다. 아래 예시 코드를 통해 `"strictNullCheck": true`일 경우의 null과 undefined에 대한 내용도 한 번 정리해보겠다.

  <!-- more -->

```javascript
interface Person {
  name: string;
  age: number;
  address: string;
}

interface Restaurant {
  waiter: Person[];
  table: HTMLDivElement;
  chair: HTMLDivElement;
  dish: HTMLDivElement;
  guestData?: Person[] | null;
  queueData?: Person[] | null;
}
```

위의 코드를 보면 앞서 타입스크립트의 interface 정의를 통해 프로그램의 구조에 대한 힌트를 얻을 수 있다고 한 내용이 이해가 될 것이다. 레스토랑 interface를 살펴보면 waiter, table, chair, dish, guestData, queueData 등의 정보들로 구성이 되어있고 각 각의 타입을 통해 waiter는 Person타입의 배열이라는 것을 알 수 있다. 그리고 그외에 table, chair, dish는 HTML 상의 div 태그로써 존재한다는 것도 알 수 있다.
따라서 코드를 분석할때 타입의 정의를 통해서 손쉽게 파악을 할 수 있다.

## <ins><b>`tsconfig.json`에서의 "strictNullCheck": true</b></ins>

타입스크립트의 컴파일 옵션에 대한 내용을 담고 있는 `tsconfig.json`파일의 내부에 이전에 `"strict": true`를 작성해주었다. 이는 `"strictNullCheck": true`에 대한 옵션도 포함하고 있는 옵션이다.
여기서 `"strictNullCheck": true`옵션을 활성화 해준다는 의미는 타입스크립트에서 undefined과 null 타입을 구분해주겠다는 의미이다.
위의 레스토랑 interface에서 손님데이터(guestData)와 대기열데이터(queueData)에 대한 정보를 살펴보자.
guestData 타입명뒤에 ? 는 undefined와 같은 의미이다. 따라서 아래와 같은 코드이다.

```javascript
guestData: Person[] | null | undefined
```

이처럼 `"strictNullCheck": true`옵션이 활성화된 경우에는 null과 undefined를 구분해서 타입스크립트가 인식하기 때문에 `빈값을 의도적으로 넣었다는 null에 대한 정의와 값의 부재를 의미하는 undefined을 각 각 개별적으로 정의`를 하였다.
그렇다면 `"strictNullCheck": false`로 옵션을 비활성화 해주게 되면 어떻게 될까? 바로 null과 undefined를 구분하지 않기 때문에 `null에 대한 정의를 생략`해도 된다.

하지만 타입스크립트를 사용하는 이유는 엄격한 타입을 적용하기 위함이다. 따라서 옵션을 활성화시켜서 null과 undefined을 구분해서 작성해주도록 하는 것이 좋다.

## <ins><b>JavaScript의 클래스를 TypeScript의 클래스로 재정의하기</b></ins>

`Class in JavaScript`

```javascript
class Example {
  first;
  second;
  #third; //private

  constructor(first, second, third) {
    this.first = first;
    this.second = second;
    this.third = third;
  }
}
```

자바스크립트에서의 클래스에서 타입스크립트의 클래스로 재정의할때에는 클래스 내에서 `'this.x'로 정의된 속성과 'constructor의 매개변수'에 대한 타입을 정의`를 해줘야 한다.

- ### 타입스크립트의 장점

  - #### private, public, protected 접근 제어자 키워드를 제공한다.

    기존의 자바스크립트에서는 private 클래스 속성을 구현하기 위해서 `즉시실행함수`와 `클로저`개념을 사용해야만 했다. 최신 자바스크립트 문법에서 클래스 속성 이름 앞에 `#`을 붙여서 private 속성으로 만들어 줄 수 도 있지만 최신 문법이기 때문에 브라우저 간의 호환성 문제가 있다.

    하지만 타입스크립트에서는 `private, public, protected` 접근 제어자 키워드를 제공한다.

    - **(1) public :** 클래스 내부와 상속받는 자식, 생성한 객체 인스턴스를 통해 접근이 가능하다.
    - **(2) private :** 클래스 내부에서만 접근이 가능하다.
    - **(3) protected:** 클래스 내부와 클래스를 상속한 자식 클래스에서 접근이 가능하다. (`자식 클래스에서 정의된 protected 클래스 속성은 부모 클래스에서 접근할 수 없다.`) protected는 public 보다는 타이트하지만, private 보다는 느슨하다는 특징을 가지고 있다.

`Class in TypeScript`

클래스 속성의 접근 제어자를 작성할때에는 처음에는 private으로 적은 다음에 점차 필요에 따라 protected에서 public 순으로 권한을 늘려주며 작성해주는 것이 좋다.
실제로 아래와 같이 `interface를 정의하는 이유는 class의 형태를 잡아주기 위한 목적으로 정의`한다.
`class에서 무조건적으로 정의해야 되는 속성이 있다면 interface로 별도로 구현해서 class에서 상속`받아서 작성해주도록 한다.

```typescript
// interface명의 맨 앞글자를 대문자로 하는 것은 일종의 convention이다.
interface IExample {
  // private와 protected는 부모가 접근할 수 없는 요소이기 때문에
  // interface에서는 public 속성만을 갖는다.
  first: number;
}

class Example implements IExample {
  public first: number;
  private second: string;
  protected third: boolean;

  constructor(first, second, third) {
    this.first = first;
    this.second = second;
    this.third = third;
  }
}
```

## <ins><b>interface로 객체 말고 함수나 클래스를 정의할 수도 있다.</b></ins>

interface로 객체 말고 함수나 클래스의 타입을 정의할 수도 있지만, `interface로는 주로 객체의 타입을 정의하는데 사용하도록 하자.`

```javascript
// interface로 함수의 타입 정의하기
interface Example {
  (a: number, b: number): number;
}

// interface로 객체 정의하기
interface Example {
  new(a: number, b: number): number;
}
```

그렇다면 객체 내의 메서드를 interface 내에서는 어떻게 정의를 할까?

```javascript
interface Example {
  add: (a: number, b: number) => number;
}

const example: Example = {
  add(a, b) {
    return a + b;
  }
};

// or
// 화살표 함수(Arrow function)로 객체의 메서드 정의
const example: Example = {
  add: (a, b) => {
    return a + b;
  }
};
```

위와 같이 interface로 객체 내의 메서드의 타입을 정의 할 수 있다.

## <ins><b>제네릭(generic)</b></ins>

만약에 객체 obj에 더하기 메서드가 정의되어있다고 가정하자.
이 더하기 메서드는 두 개의 매개변수를 갖는데, 이 두 매개변수의 타입은 number가 될수도 있고, string이 될 수도 있다. (단, 각 각의 두 매개변수의 타입은 number 또는 string으로 동일하다.)
메서드를 두 개 작성하지 않고 하나의 메서드만 작성한다고 했을때 어떻게 작성할 수 있을까?

```javascript
interface obj<T> {
  add: (a: T, b: T) => T;
}

// number 타입의 두 매개변수를 더하는 메서드
const a: obj<number> = {
  add: (a, b) => a + b;
}

// string 타입의 두 매개변수를 더하는 메서드
const b: obj<string> = {
  add: (a, b) => a + b;
}

console.log(a.add(1, 2)); // 3
console.log(b.add('a', 'b')); // ab

```

위에서 interface를 사용하여 각 객체의 타입을 정의할때 <T>의 타입을 구체화해서 정의하고 있다.

## <ins><b>제네릭(generic) 예시 - addEventListener </b></ins>

이벤트 핸들러를 DOM element에 적용할때 사용하는 addEventListener 함수의 타입 정의를 살펴보자.

`lib.dom.d.ts`

```javascript
function addEventListener<K extends keyof WindowEventMap>(type: K, listener: (this: Window, ev: WindowEventMap[K]) => any, options?: boolean | AddEventListenerOptions): void;

function addEventListener(type: string, listener: EventListenerOrEventListenerObject, options?: boolean | AddEventListenerOptions): void;
```

addEventListener 함수는 각각 다른 매개변수로 함수 오버로딩(Overloading)되어 있다.

`addEventListener 함수 사용 예시`

```javascript
button.addEventListener('submit', () => {
  ...
});
button.addEventListener<'submit'>('submit', () => {
  ...
});
```

우리가 일반적으로 첫 번째와 같은 방식으로 addEventListener 함수를 사용해서 이벤트 핸들러를 DOM element에 적용을 하는데, 두 번째로 정의된 함수를 보면 addEventListener 함수의 뒤에 <'submit'>이 있다.
사실은 우리가 무심결에 생략을 해서 사용을 하여도 실제로는 암묵적으로 추론을 하고 있는 것이다.

`lib.dom.d.ts`의 첫 번째 예시 함수를 보면 `<K extends keyof WindowEventMap>(type: K, listener:`부분을 찾을 수 있는데, <ins><b>WindowEventMap이라는 interface의 `key값으로 K를 제한`하고 있고 매개변수 type이 이 K를 타입으로 정의하고 있기 때문에 매개변수 type에 입력된 값이 WindowEventMap의 키값으로 존재를 한다면 암묵적으로 추론</b></ins>을 할 수 있는 것이다.
만약에 type에 입력된 값이 WindowEventMap이라는 interface의 key 값에 존재하지 않는다면, `lib.dom.d.ts`의 두 번째 예시 함수 `function addEventListener(type: string,`(`제네릭 부분이 샹략된 함수`)가 참조된다.

## <ins><b>제네릭에서 extends는 타입을 제한하는 기능을 한다.</b></ins>

```javascript
interface obj<T> {
  ....
}

interface obj<T extends string> {

}
```

첫 번째 interface에서는 type T에 모든 타입의 정의가 가능했지만 두 번째 interface에서는 string이거나 string의 자식 혹은 string을 구현한 type만 타입으로써 정의가 가능하다. 이처럼 `extends는 상속이 아닌 제한의 의미`를 포함하고 있다.

## <ins><b>제네릭 예시 - forEach</b></ins>

`lib.es5.d.ts`

```javascript
interface Array<T> {
  ...
  forEach(callbackfn: (value: T, index: number, array: T[]) => void, thisArg?: any): void;
  ...
}
```

`lib.es5.d.ts`파일에서 Array interface에서 정의된 forEach 함수를 보면 callback 함수의 value가 배열 interface에서 정의된 타입 T로 정의가 되어있는 것을 볼 수 있다. 실제로 마우스로 아래 코드에서 forEach의 콜백함수의 item에 마우스를 올려보면 타입이 number로 추론되고 있음을 확인할 수 있다.
이처럼 대부분의 타입추론은 제네릭으로 작성이 되어있다.

```javascript
[1, 2, 3].forEach((item) => {
  console.log(item);
});
```

위의 배열을 [1, true, 'a']로 작성하게 되면 타입을 `number | boolean | string 으로 추론`한다. any보다 제네릭으로 타입을 정의하는 것이 정밀하게 타입을 잡아내는 것 같다.

## <ins><b>제네릭으로 직접 구현해보는 forEach</b></ins>

```javascript
// 함수이름 뒤에 매개변수에서 사용할 제네릭 타입 T를 정의해준다.
// 아래 매개변수 arr의 타입을 T[] 라고 작성할 수도 있으나 나중에 T를 다른 타입으로 제한을 하기 위해
// extends를 하는 경우 가독성을 고려했을때 Array<T>로 표기해주는 것이 좋다.
function forEach<T>(arr: Array<T>, callback: (item: T) => void): void {
  for (let i: number = 0; i < arr.length; i++) {
    callback(arr[i]);
  }
}

// 직접 구현한 forEach 함수 사용
forEach <
  string >
  (['1', '2', '3'],
  (item) => {
    console.log(item);
  });
```

## <ins><b>제네릭 예시 - find</b></ins>

`lib.es5.d.ts`의 배열 interface에서 정의한 find 함수를 살펴보면 제네릭이 정의되어 있는 함수와 그렇지 않은 함수, 두 가지로 함수 오버로딩이 되어있다.
제네릭이 정의되어 있는 함수와 제네릭이 정의되어 있지 않은 함수 모두 predicate 함수의 value 타입이 find 함수를 호출할때 참조한 배열 요소의 타입에 따라 정의가 되는 것을 볼 수 있다.

다른점은 predicate 함수의 첫 번째 인자로 this를 사용하는 경우에는 제네릭으로 타입을 정의해주고, 그렇지 않은 경우에는 제네릭으로 타입을 정의하지 않는다.

```javascript
interface Array<T> {
  ...
  find<S extends T>(predicate: (this: void, value: T, index: number, obj: T[]) => value is S, thisArg?: any): S | undefined;
  find(predicate: (value: T, index: number, obj: T[]) => unknown, thisArg?: any): T | undefined;
  ...
}
```

## <ins><b>callback과 predicate</b></ins>

callback함수들 중에서 `true 혹은 false를 반환하는 함수`를 `predicate`라고 한다.
forEach함수에서 두번째 인자로 넣어주는 것은 callback 함수지만, find 함수의 콜백함수는 predicate라고 한다.

## <ins><b>클래스에서의 제네릭</b></ins>

```javascript
class Example<T> implement IExample{
  public first: T;
  public second: T;
  protected third: T;
  ....
}
```
