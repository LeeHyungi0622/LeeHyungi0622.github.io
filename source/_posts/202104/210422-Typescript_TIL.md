---
title: 210422 TypeScript TIL - 암묵적 타입 정의과 명시적 타입 정의, 배열과 튜플, 변수를 상수로 정의해서 사용하기, 객체의 타입 지정, enum 타입, void, Overloading과 ? 연산자, never과 any 타입, 정의된 타입 재정의, 기존 JavaScript를 TypeScript로 전환하는 경우와 처음부터 TypeScript로 작성하는 경우
date: 2021-04-22 07:50:00
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

## <ins><b>암묵적 타입 정의, 명시적 타입 정의</b></ins>

아래와 같이 코드를 작성해주면 암묵적으로 num이라는 변수의 타입은 정의된 값 10에 의해 `number` 타입으로 타입지정이 된다.

```javascript
let num = 10;
```

그런데 만약에 값을 정의하지 않고 변수만 선언하는 경우, 이러한 경우에는 타입을 지정해서 해당 변수에 할당될 수 있는 값의 타입을 지정해주는 것이 좋다.

```javascript
let num: number;
```

간혹 타입을 지정할때 Number, String, Boolean, Object 등으로 작성을 해서 실수하는 경우가 있는데, 앞서 작성한 것들은 모두 자바스크립트에서 객체들이기 때문에 타입은 모두 맨 앞을 소문자로 표기해서 타입지정을 해주도록 하자.

## <ins><b>배열과 튜플</b></ins>

- ### **배열 표기법**

  ```javascript
  let arr1: Array<string> = ['1', '2', '3'];
  let arr2: (string | number | boolean)[] = [true, 2, '3'];
  let arr3: number[] = [1, 2, 3];
  ```

  타입스크립트에서 숫자타입의 배열을 명시적으로 작성할때 Array<number>와 number[] 두 가지 방법으로 작성할 수 있다.
  두 번째 작성한 것과 같이 복합적인 타입 값들로 구성된 배열 타입으로 명시적으로 지정할 수도 있다.

- ### **엄격한 배열의 사용**

  ```javascript
  let arr1: [boolean, number, string] = [true, 2, '3']; // tuple
  let arr2: [boolean, 1, string] = [true, 1, '3']; // 두번째 인자를 고정된 상수 타입으로 지정할 수도 있다.
  ```

  <!-- more -->

## <ins><b>상수로 사용하기</b></ins>

- ### **as const**

  아래 코드와 같이 정의된 값 뒤에 `as const`를 붙여서 상수로써 사용할 수 있다. 만약 변수의 선언을 `const`로 한 경우에는 효용성이 없고, 객체의 경우 유용하게 사용될 수 있다.
  객체는 const로 변수가 선언되어도 객체의 내부 속성값은 변경이 가능하다. 이러한 단점을 `as const`를 붙여줌으로써 객체의 내부 속성값이 바뀌지 않도록 할 수 있다.

  ```javascript
  let arr = [true, 2, '3'] as const; // type이 (boolean, number, string)[]
  let str = 'hello' as const; // type이 'hello'
  ```

  `객체에서 as const의 사용`

  ```javascript
  const obj1 = {a: 'b'};
  obj1.a = 'c'; // 객체의 속성 변경 가능

  const obj2 = {a: 'b'} as const;
  obj2.a = 'c' // 객체의 속성 변경 불가능
  ```

## <ins><b>객체의 타입 지정하기</b></ins>

객체의 타입을 지정할때 아래와 같이 지정을 해 줄 수도 있다.

```javascript
const obj: object = { a: 'b' };
```

하지만 타입을 object로 지정해주면 타입이 구체적이지 않기 때문에 아래와 같이 객체의 타입은 구체적으로 작성해주는 것이 좋다.

```javascript
const obj1: { a: string } = { a: 'b' };
const obj2: { a: string, b?: number } = { a: '1', b: 2 };
```

위와 같이 객체의 타입을 명시해주게 되면 중복되는 느낌과 코드가 길어져서 가독성이 안좋아진다. <ins><b>따라서 이 타입에 대한 부분을 별도의 `interface`나 `type`으로 분리를 해서 작성을 해주기도 한다.</b></ins>

만약 b속성을 나중에 정의해주거나 a 속성만 존재하는 경우, 위와 같이 ? 연산자를 변수 뒤에 붙여줌으로써 표현할 수 있다.
이 물음표 연산자의 위와같은 사용은 타입스크립트에서 생겼으며, 아래와 같이 객체 및 객체의 메서드에 접근시에도 사용될 수 있다.

```javascript
const a = obj?.name
const b = obj.<method>?.()
```

## <ins><b>enum 타입</b></ins>

열거형이란 해당 타입으로 사용할 수 있는 값을 열거하는 기법이다.
이전에 타입스크립트 노루책으로 공부하며 정리했던 블로그 포스팅을 참고하도록 하자.

[https://leehyungi0622.github.io/2021/02/19/202102/210219-Typescript/](https://leehyungi0622.github.io/2021/02/19/202102/210219-Typescript/)

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

enum 타입을 사용하는 경우에는 안전하게 `const enum`을 사용해서 enum 타입을 정의하고 사용하도록 한다. 잘만 사용하면 코드를 줄여줄 수 있다.

## <ins><b>TypeScript에서 void의 사용</b></ins>

```javascript
const n = void 0; // n은 undefined type

// 아무것도 반환하지 않는 함수의 반환 타입으로 void를 적어준다.
function print(): void {
  console.log('print console');
}
```

## <ins><b>TypeScript에서의 Overloading과 ? 연산자</b></ins>

overloading이란 같은 이름의 함수를 매개변수만 다르게 해서 정의하는 것을 말한다.
JavaScript에서는 타입이 없었기 때문에 overloading이라는 개념이 없었지만, TypeScript에서 타입이 등장하면서 overloading이라는 개념이 도입되었다.
간단한 예시로 아래와 같이 매개변수에 ?(물음표 연산자)를 사용해서 오버로딩을 구현할 수 있다.

아래 함수에서 매개변수는 (a, b)또는 (a, b, c)가 될 수도 있다. 이처럼 같은 함수에서 서로 다른 매개변수의 형태를 갖을 수 있는 것이다.

```javascript
const add(a: number, b: number, c?: number): number {
  //........
}
```

## <ins><b>TypeScript에서 never과 any 타입</b></ins>

타입스크립트에서는 never와 any라는 타입이 있다.

never 타입의 경우 실무에서 에러를 통해 접할 수 있다. 아래와 같이 타입을 실수로 빈 배열로 정의한다면, 이 변수는 아무것도 넣을 수 없는 `never` 타입으로 선언된다.
따라서 number 타입의 값 3을 push하려고 하면 아래와 같은 에러가 발생한다.
`not assignable to parameter of type 'never'`

```javascript
const arr: [] = [];
arr.push(3);
```

any 타입의 변수에는 아무 값이나 넣을 수 있다. 그러면 타입스크립트를 사용하는 것에 의미가 없기 때문에 불가피한 경우가 아니라면, 되도록 any 타입은 사용하지 않도록 한다.
(만약 타입을 정의할때 너무 복잡해서 타입을 정의할 수 없는 경우, any를 사용하도록 한다.)

```javascript
const hi: any = [];
```

## <ins><b>만약 다른 사람이 정의한 변수의 타입이 틀린 경우</b></ins>

만약 같이 협업하는 사람이 작성한 코드에서 특정 변수의 타입이 틀린경우, 그리고 직접적으로 해당 변수에 정의한 타입을 바꿀 수 없다면 어떻게 해야될까?

위와같은 상황이라면 해결방법은 `Type Casting`이 있다.

만약 아래와 같이 string 타입으로 정의되어야 할 name이라는 변수가 number 타입으로 정의되어 있는 경우, 우리는 name이라는 변수의 타입을 string으로 cating해줘야 한다.

우선 타입 캐스팅 이전에 바꾸려는 타입과 기존의 타입의 관계부터 생각해봐야 한다.
만약 기존의 타입과 바꾸려는 타입의 관계가 서로 겹치는 경우에는 아래와 같이 상위 개념의 타입으로 타입 캐스팅을 해주면 된다.

```javascript
// HTMLDivElement -> HTMLElement로 타입변환
const div = document.createElement('div');
const a = div as HTMLElement;
```

그러면 기존의 타입과 바꾸려는 타입의 관계가 서로 겹치지 않는다면 어떻게 해야될까?
바로 아래와 같이 두 가지 형태로 타입 변환을 해 줄 수 있다.

```javascript
// hello.d.ts
const name: number;

// main.ts
import name from 'hello';

// name substring하기
name.substr(1, 2); // number type은 substr 안됨
// 강제로 name 변수의 타입을 변환
// 방법 1
(name as unknown as string).substr(1, 2);
// 방법 2
(<string><unknown> name).substr(1, 2);
```

## <ins><b>기존의 JavaScript를 TypeScript로 바꿔주는 경우와 처음부터 TypeScript로 작성하는 경우</b></ins>

- ### **기존의 JavaScript를 TypeScript 코드로 전환하는 방식**

  최대한 타입을 안적고, 기본 타입이 문제가 되는 경우에만 type을 지정해주는 방식으로 type을 도입한다.

  - #### **JavaScript→TypeScript 전환시의 tsconfig.json 설정**
    ```zsh
    {
      "compileOptions": {
        "strict": true,
        "allowJs": true,# 자바스크립트를 타입스크립트로 모두 전환되었을 경우, allowJs는 꼭 비활성화 시켜준다.
        "checkJs": true # 자바스크립트 에러 체크
                        # 만약 ts로 전환하는 과정에서 에러가 너무 많이 발생하는 경우,
                        # 일단 "checkJs"는 비활성화한다.
      },
      "exclude": ["*.js"] # js 파일의 컴파일은 제외한다.
    }
    ```

- ### **처음부터 TypeScript 코드로 작성하는 방식**

  남이 만든 것은 암묵적으로 타입을 추론할 수 있도록 두고, 내가 만든 객체 또는 함수의 경우에는 타입을 직접 정의해서 작성하도록 한다.

  - #### **Custom type**

    ```javascript
    type stringOrNumber = string | number;
    ```

  - #### **내가 만든 객체 또는 함수의 타입 작성하는 방법**

    - **1)**별도의 `*.d.ts` 파일로 분리를 해서 type 작성하기
      \*.ts 파일내에서 `interface와 type alias 방식으로 작성한 실제 코드에서는 역할이 딱히 없고 type정의 목적만 가지는` 코드는 나중에 길어지게 되면, 별도의 \*.d.ts 파일로 빼서 관리한다.
    - **2)**`*.ts`파일 내에 type 작성하기

      ```javascript
      // (1)interface 방식으로 type 작성하기
      interface hello {
        a: string;
        b?: number;
      }
      // (2)type alias방식으로 type 작성하기
      type hello = {
        a: string,
        b?: number
      };

      // 아래와 같이 객체의 타입을 작성해주면 가독성이 좋지 않다.
      const hi: { a: string, b?: number } = { a: 'b' };
      // 따라서 아래와 같이 interface나 type으로 정의한 타입을 사용해서 변수의 타입을 정의해준다.
      const hi: hello = { a: 'b' };
      ```

      <ins>**interface로 타입을 정의해주면 다른 타입 interface를 상속받아서 타입을 정의할 수 있다.**</ins>
      만약에 인터페이스 내부에서 정의한 함수의 타입정의에 있어 공통 매개변수를 가지고 있는 함수의 경우에는 ? 연산자를 사용해서 정의할 수 있다.
      하지만 <ins><b>만약에 같은 함수이름에 매개변수가 너무 다른 경우, 다른 매개변수의 형태로 두 번 정의해준다.</b></ins>

      ```javascript
      interface hello {
        a: string;
        b?: number;
        // 함수 a의 매개변수의 형태가 너무 다른 경우, 두 번 정의해준다.
        a(f: number, s: number): number;
        a(f: string, s: boolean, t: number): string;
      }

      interface helloChild extends hello {
        c?: boolean;
      }
      ```

## <ins><b>TypeScript 자체는 별거아니다.</b></ins>

TypeScript 자체는 별거 아니다. 다만 남이 작성한 type interface를 읽고 적용을 하는 것이 어렵다.
따라서 지속적으로 다른 사람이 작성한 type interface를 읽고 해석하는 연습을 하는 것이 중요하다.

## <ins><b>최대한 \*.d.ts는 남이 만들어 놓은 것을 라이브러리로 불러서 사용하는 것이 좋다.</b></ins>

\*.d.ts 파일은 라이브러리로 남이 작성해놓은 것을 가져다가 쓰고 \*.ts 파일의 코드작성에 집중한다.

## <ins><b>다른 사람이 작성한 \*.d.ts 파일 읽고 분석하기</b></ins>

아래의 링크는 DefinitelyTyped의 fp.d.ts 파일의 링크이다. 많이 복잡해보이는 타입정의 인터페이스들로 구성이 되어있고, 대부분 제네릭으로 작성되어 있는 것을 볼 수 있다.

[https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/lodash/fp.d.ts](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/master/types/lodash/fp.d.ts)

```javascript
interface LodashFlowRight {
        <A extends any[], R1, R2, R3, R4, R5, R6, R7>(f7: (a: R6) => R7, f6: (a: R5) => R6, f5: (a: R4) => R5, f4: (a: R3) => R4, f3: (a: R2) => R3, f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R7;
        <A extends any[], R1, R2, R3, R4, R5, R6>(f6: (a: R5) => R6, f5: (a: R4) => R5, f4: (a: R3) => R4, f3: (a: R2) => R3, f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R6;
        <A extends any[], R1, R2, R3, R4, R5>(f5: (a: R4) => R5, f4: (a: R3) => R4, f3: (a: R2) => R3, f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R5;
        <A extends any[], R1, R2, R3, R4>(f4: (a: R3) => R4, f3: (a: R2) => R3, f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R4;
        <A extends any[], R1, R2, R3>(f3: (a: R2) => R3, f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R3;
        <A extends any[], R1, R2>(f2: (a: R1) => R2, f1: (...args: A) => R1): (...args: A) => R2;
        (...func: Array<lodash.Many<(...args: any[]) => any>>): (...args: any[]) => any;
    }
```
