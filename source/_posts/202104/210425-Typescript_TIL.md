---
title: 210425 TypeScript TIL - DefinitelyTyped에 없는 package인 경우(custom package typing), internal module/ external module/ ambient module, 외부 라이브러리의 Typing 5가지 경우, 라이브러리를 만드는 경우, *.d.ts파일과 *.ts파일의 사용, Type intersection, call/bind/apply를 사용한 type 구체화, TS 유틸리티, 데코레이터
date: 2021-04-25 03:42:00
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

## <ins><b>DefinitelyTyped에 없는 package인 경우</b></ins>

만약에 사용하고자 하는 package가 기본적으로 type을 제공하지 않고, DefinitelyTyped에도 없는 경우, 이런 경우에는 내가 직접 typing을 해줘야 한다. (전부 typing하지 않고 내가 사용할 부분만 부분 typing한다.)

예를들어, can-use-dom이라는 타입을 제공하지 않는 패키지를 설치해서 사용하는데, DefinitelyTyped에도 없는 경우, 이 경우에는 아래와 같이 프로젝트 폴더 안에 \*.d.ts파일을 작성해줘야 한다.
되도록이면 types와 같은 별도 폴더를 생성해서 내부에 \*.d.ts파일을 작성해주도록 한다.

`/<Project folder>/types/can-use-dom.d.ts`

```javascript
declare module "can-use-dom" {
  const canUseDOM: boolean;
  export default canUseDOM;
}
```

`/<Project folder>/index.ts`

```javascript
import canUseDOM from 'can-use-dom';

console.log(canUseDOM);
```

이제 작성해준 `can-use-dom.d.ts`파일을 \*.ts 파일 내에서 인식시키기 위해서 `tsconfig.json`파일에서 `"typeRoot"`옵션의 속성값으로 작성해준 \*.d.ts파일의 폴더 경로를 적어줘야 한다.

`tsconfig.json`

```json
{
  "compilerOptions": {
    ...
    "typeRoots": ["./types", "./node_modules/@types"],
    ...
  },
  "exclude": ["*.js"],
  // "include"에는 구체적으로 특정 파일을 작성하지 않도록 한다.
  // 만약 구체적으로 파일을 명시하게 되면 해당 타입스크립트 파일만 컴파일되기 때문에
  // 내가 작성한 *.d.ts 파일이 컴파일 되지 않는다.
  "include": [],
}
```

  <!-- more -->

만약에 \*.d.ts내에서 기존에 정의된 interface를 재정의해서 사용하고자 하는 경우에는 아래와 같이 작성을 해준다.

`index.d.ts`

```javascript
// window(global)인 경우에는 ambient module을 사용할 수 없기 때문에 external module로 만들어 줘야 한다.
// ambient module을 external module로 만들어주기 위해서 export {}를 최상단에 적어준다.
export {}
declare global {
  export interface Window {
    hello: string;
  }
  interface Error {
    code?: any;
  }
}
```

`index.ts`

```javascript
window.hello = 'a';
const error = new Error('');
// error 객체에 code 속성을 사용하고자 하는 경우,
// 위와같이 기존의 Error interface를 확장해서 타입을 정의해줘야 한다.
error.code;
```

## <ins><b>internal module, external module, ambient module</b></ins>

- ### **internal module**
  내부 모듈은 namespace이다.
- ### **external module**
  외부 모듈은 import/export를 사용하는 것을 말한다.
- ### **ambient module**
  남이 만든 경우에는 declare module을 해주지 않아도 되지만, 내가 만든 package인 경우, declare module을 해줘야 되는데 이 declare module을 작성해주는 것이 ambient module이다.

## <ins><b>typing이 틀리게 되어있는 경우</b></ins>

```zsh
$ npm i connect-flash @types/connect-flash
```

만약에 DefinitelyTyped에서 정의된 타입이 잘못되어 있는 경우, 기존에 설치한 @types/connect-flash를 지우고, 직접 d.ts 파일을 작성해서 typing을 해준다.

```zsh
$ npm rm @types/connect-flash
```

types/connect-flash.d.ts 파일을 생성해서 ambient module을 만들어준다.

아래의 코드는 @types/connect-flash설치후 자동 생성된 index.d.ts 파일의 일부이다.
아래의 코드를 코면 Express namespace 내부에 Request interface가 정의되어있는데, 이는 Express에 이미 정의되어있는 Request interface가 확장되어 있는 것이다.

이외에도 global을 ambient module로 감싸서 ambient module로 재정의할 수 있다.

```javascript
/// <reference types="express" />

declare namespace Express {
  export interface Request {
      flash(): { [key: string]: string[] };
      flash(message: string): string[];
      flash(type: string, message: string[] | string): number;
      flash(type: string, format: string, ...args: any[]): number;
  }
}
```

## <ins><b>외부 라이브러리의 Typing 5가지 경우 (총정리)</b></ins>

- **(1)TypeScript로 만들어진 Package**
- **(2)JavaScript로 개발이 되었지만 index.d.ts파일을 제공하는 Package**
- **(3)타입을 제공되지 않은 Package이지만 DefinitelyTyped에서 index.d.ts파일이 제공되는 경우**
- **(4)DefinitelyTyped에서도 type을 제공해주지 않는 경우**
  이 경우에는 내가 직접 \*.d.ts 파일을 생성해서 ambient module을 작성해주고, tsconfig.json 파일의 typeRoots 옵션에서 파일의 경로를 지정해서 연결시켜줘야 한다.
- **(5)DefinitelyTyped에서 제공된 index.d.ts파일에서 정의된 타입이 잘못된 경우**
  기존에 설치한 type package를 제거하고 직접 짠다.

## <ins><b>라이브러리를 만드는 경우</b></ins>

만약에 라이브러리를 만드는 경우, 아래와 같이 tsconfig.json파일에서 옵션을 작성해줘야 한다.
아래와 같이 옵션을 추가해주게 되면 "declarationDir"에서 지정한 폴더의 하위에 컴파일된 파일에 대해 자동으로 \*.d.ts파일을 생성해준다.

```json
"compilerOptions": {
  ...
  "declaration": true,
  "declarationDir": "./types",
  ...
}
```

## <ins><b>\*.d.ts 파일과 \*.ts파일의 사용</b></ins>

일반적으로 \*.d.ts 파일의 경우에는 라이브러리를 만들때와 같이 특별한 상황에서 사용하거나 남이 정의한 타입의 정의가 잘못된 경우에 만들어서 사용한다.
내가 프로젝트를 만들때 작성한 타입 선언과 관련된 코드들을 별도의 파일에 일괄적으로 모아서 재사용성 가능하도록 만드는 경우에는 \*.ts 파일내에서 작성을 해준다.

## <ins><b>type intersection</b></ins>

type intersection은 A & B 라고 했을때 A와 B 타입을 모두 만족해야 한다.

```javascript
interface A {
  hello: true;
}

interface B {
  bye: true;
}

const a: A = {
  hello: true
};

const b: B = {
  bye: true
};

const c: A & B = {
  hello: true,
  bye: true
};
```

위와같이 타입 A와 타입 B를 각 각 개별적인 interface로 선언해주는 이유는 타입의 중복선언 없이 경우에 따라 | 나 & 로 조합해서 타입을 정의하기 위해서 이다.

- ### <ins><b>interface와 type alias를 같이 조합 가능</b></ins>

  ```javascript
  interface A {
    hello: true;
  }
  interface B {
    bye: true;
  }
  type c = {
    hi: false
  };

  const c: A & B & C = {
    hello: true,
    bye: true,
    hi: false
  };
  ```

  <br/>

## <ins><b>call, bind, apply를 사용한 type 구체화</b></ins>

tsconfig.json 파일에서 `"strictBindCallApply": true` 옵션의 활성화하면 type checking이 좀 더 확실해진다. 좀 더 엄격하게 하면 type checking이 좀 더 확실해진다.

map을 사용해서 number 타입의 배열요소를 string 타입의 값으로 바꿔보도록 하자.

```javascript
// 간단하게 아래와 같이 코드를 작성할 수 있다.
[1, 2, 3].map((item) => item.toFixed(1));
// result: ['1.0', '2.0', '3.0']
```

위와같이 간단하게 코드를 작성해 줄 수 있지만, call을 사용해서 제네릭으로 args의 타입과 반환 값의 타입을 구체적으로 명시해서 타입 추론을 정확하게 할 수 있다.
마우스를 함수의 위에 올려보면 구체적으로 어떤식으로 제네릭 typing을 해야하는지 힌트를 얻을 수 있다. 얻은 힌트를 토대로 타입부분만 구체적으로 명시해주면 된다.

```javascript
// return type: string[]
// args type(in callback function): number
const result = Array.prototype.map.call<number[], [(item: number) => string], string[]>([1, 2, 3], (item) => {
  return item.toFixed(1);
});
```

## <ins><b>TS 유틸리티</b></ins>

TS Utility를 사용하면 interface에 정의한 타입을 손쉽게 조작해서 사용할 수 있다.

아래의 TypeScript 공식 홈페이지의 handbook에서 TS 유틸리티에 대한 내용을 참고하자.
참고를 위해 아래에 링크를 첨부했다.

[https://www.typescriptlang.org/docs/handbook/utility-types.html](https://www.typescriptlang.org/docs/handbook/utility-types.html)

TS 유틸리티 중에서 아래 항목들은 유용하게 사용될 수 있으니 꼭 실습을 통해 익숙해지도록 하자.

- **Partial<T>**
  interface에서 정의한 타입들의 속성 이름에 ?를 붙인 것과 같은 효과를 낸다.
  ```javascript
  interface A {
    a: number;
    b: boolean;
    c: string;
  }
  // 타입을 A로 지정하면 interface A에 들어있는 모든 요소를 넣어줘야 한다.
  const a: A = {
    a: 1,
    b: true,
    c: 'hello'
  };
  // 만약 부분적으로 속성을 넣어주고 싶다면, 별도의 interface 수정없이
  // Partial을 이용해서 간단하게 작성할 수 있다.
  const b: Partial<A> = {
    c: 'bye'
  };
  ```
- **Readonly<T>**
  만약에 interface에서 정의한 모든 타입 속성들에 readonly를 붙여주고 싶다면 일일이 붙여줘도 되지만,
  간편하게 Readonly<T> TS 유틸리티를 사용해서 처리할 수 있다.
- **Pick<T,K>**
  만약에 interface에서 정의한 타입 속성들 중에 일부만을 뽑아서 새로운 type으로 정의해서 사용하고 싶다면,
  Pick<T,K> TS 유틸리티를 사용해서 구현할 수 있다.
  ```javascript
  interface A {
    a: number;
    b: string;
    c: boolean;
  }
  type B = Pick<A, 'a' | 'c'>;
  ```
- **Omit<T,K>**
  pick TS 유틸리티는 특정 타입 속성을 뽑아서 새로운 타입을 정의했지만 Omit TS 유틸리티는 타입속성들 중에서 특정 타입 속성만 제거해서 새로운 타입을 정의할 수 있다.
- **Exclude<T,U>**
  Exclude TS 유틸리티는 T 타입 중에서 U 타입을 제외한 타입으로 새로운 타입을 정의할때 사용한다.
  ```javascript
  type T0 = Exclude<string | number | (() => void), Function>;
  //   T0 = string | number
  ```
- **Extract<T,U>**
  Extract TS 유틸리티는 T 타입과 U 타입의 공통 타입으로 새로운 타입을 정의할때 사용한다.
  ```javascript
  type T0 = Extract<'a' | 'b' | 'c', 'a' | 'f'>;
  //   T0 = "a"
  ```
- **ReturnType<T>**
  ReturnType TS 유틸리티는 함수의 return값의 타입을 가져와서 새로운 타입으로 정의할때 사용된다.
  이 TS 유틸리티는 Redux에서 많이 사용된다.
- **Required<T>**
  만약에 interface에서 작성한 타입 속성의 이름에 ?에 붙어있는 경우, 해당 타입 속성은 생략이 가능하다.
  하지만 모든 속성들로부터 ?를 제거하고 모두 필수요소로 만들어주려면 어떻게 해야할까?
  바로 Required TS 유틸리티를 사용하면 된다.

  ```javascript
  interface A {
    a?: number;
    b?: string;
  }

  const a: Required<A> = {
    a: 1,
    b: 'hello'
  };
  ```

- **OmitThisParameter<Type>**
  Type으로부터 this parameter를 제거할때 사용되는 TS 유틸리티이다.
  아래의 예시에서는 toHex라는 함수에서 OmitThisParameter TS 유틸리티를 사용해서 this parameter를 제거하고 제거한 결과에서는 number 타입의 값 5를 bind함수를 사용해서 this binding을 하고 있다.

  ```javascript
  function toHex(this: Number) {
    return this.toString(16);
  }

  const fiveToHex: OmitThisParameter<typeof toHex> = toHex.bind(5);

  console.log(fiveToHex());
  ```

  <br/>

## <ins><b>데코레이터</b></ins>

데코레이터는 JavaScript의 개념이다.
우선, 데코레이터를 사용하기 위해서 tsconfig.json에서 `"experimentalDecorators": true`를 컴파일 옵션에 넣어줘야 한다.
데코레이터의 사용은 중복없이 새롭게 기능을 추가할 수 있도록 도와준다. 클래스에서는 코드 중복의 제거가 어렵기 때문에 이는 매우 유용하다.
데코레이터의 종류에는 class 데코레이터, property 데코레이터, 함수 데코레이터, parameter 데코레이터가 있다.

```javascript
@makeGender
class Person {
  @validate name: string;
  age = 27;
  constructor() {
    this.name = name;
  }
  setName(name: string) {
    this.name = name;
  }
  @readonly
  sayName(): any {
    return this.name;
  }
}
```

- ### 데코레이터 함수 만들기

  makeGender 데코레이터 함수의 매개변수 target의 타입을 Person으로만 적어주면 Person 객체 인스턴스의 타입이 되지만, 데코레이터 속성으로써 꾸며주고자 할때에는 이 target 매개변수의 타입은 typeof를 붙여서 정의해주도록 한다.

  - **클래스에 gender관련 속성과 함수를 추가해주는 데코레이터 함수**

    ```javascript
    // target이 원본 class 객체라면 반환되는 class 객체는 새롭게 만들어낸 class이다.
    function makeGender(target: typeof Person) {
      return class extends target {
        gender = 'male';
        sayGender() {
          this.gender;
        }
      };
    }
    ```

  - **읽기 전용 readonly 데코레이터 함수**
    함수 decorator 함수의 경우에는 세번째 매개변수 인자가 descriptor이지만, 매개변수를 위한 decorator 함수를 만드는 경우에는 세번째 매개변수 인자가 index: number이다.

    descriptor의 속성에는 아래 세 가지가 있다.

    - **writable:** 수정가능 여부
    - **configurable:** 설정가능 여부
    - **enumerable:** 반복가능 여부

    ```javascript
    function readonly(target: any, key: any, descriptor: PropertyDescriptor) {
      descriptor.writable = false;
    }
    ```
