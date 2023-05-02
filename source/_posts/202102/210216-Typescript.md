---
title: 210216 TypeScript Book TIL
date: 2021-02-16 13:30:42
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

- ### **타입(type)에 대한 이해**

  이번에 독서를 통해서 타입(type)대해서 다시 정의해 볼 수 있었다. 기존에는 type에 대한 정의를 단순 data의 형식(string, number, list)에 국한된 개념으로만 알고 있었다.<br/>
  하지만 이 책에서는 `타입을 값과 이 값으로 할 수 있는 일의 집합`으로 정의하고 있다.
  <ins><b>예를들어, number 타입을 정의하자면 number라는 type으로써의 값과 숫자에 적용할 수 있는 모든 연산(+,-,\*,/,%,||,&&,?등)과 숫자에 호출할 수 있는 모든 메서드(.concat, .toUpperCase 등)를 포괄적으로 포함하는 개념으로 정의할 수 있다.</b></ins><br/>
  이렇게 정의된 타입(type)의 정의를 통해 타입검사기(typechecker)는 주어진 값으로 할 수 있는 유효한 동작과 유효하지 않은 동작을 구분해서 유효하지 않은 동작이 실행되는 일을 예방할 수 있다.

  <br/>

    <!-- more -->

  - #### **타입의 종류**

    - **any**
      타입들의 대부라고 할 수 있는 any. 이 any type은 꼭 필요한 상황이 아니라면 사용하지 않는 것이 좋다. 타입스크립트에서는 컴파일 타임에 모두가 타입이 있어야 하기 때문에 프로그래머와 타입스크립트 둘 다 타입을 알 수 없는 상황에서 any타입으로 가정한다. any를 사용해서 변수의 타입을 지정하게 되면, 자바스크립트처럼 동작하게 된다.

      - **(TSC Flag: noImplicitAny)** : 타입스크립트는 기본적으로 any로 추론되는 값을 발견하더라도 예외를 발생시키지 않는다. 만약에 any type에 대한 예외를 발생시키고 싶다면, `tsconfig.json`에서 strict을 true로 정의하거나 noImplicitAny flag를 활성화시켜주면 된다. (noImplicitAny는 strict의 family이기 때문이다)

    <br/>

    - **unknown**
      타입을 미리 알 수 없는 어떤 값에 대해 타입을 정의할때 `any보다는 unknown을 사용하는 것이 권장`된다.
      그 이유는 unknown을 사용해서 타입을 미리 알 수 없는 값에 대한 타입을 정의하게 되면, unknown type을 검사해 정제하기 전까지는 타입스크립트가 unknown 타입의 값을 사용할 수 없게 강제한다. unknown 타입은 비교연산(==,===,||,&&,?)과 반전(!)을 지원하고 다른 타입들과 마찬가지로 typeof, instanceof 연산자로 정제할 수 있다.
      ```javascript
      let a: unknown = 30;
      let c = a + b; // unknown type의 값은 + 연산자를 지원하지 않는다.(a 변수의 타입을 정제한 뒤에 기본 사칙연산 사용가능)
      // typeof를 사용해서 unknown type의 변수를 정제할 수 있다.
      if (typeof a === 'number') {
        let d = a + 10; // number
      }
      ```

    <br/>

    - **boolean**
      기본적으로 개발자들은 대개 타입스크립트가 타입을 추론하도록 작성한다.
      타입스크립트에서는 값이 특정 boolean임을 명시적으로 타입스크립트에게 알릴 수 있다.
      - **타입 리터럴(type literal)**
        아래의 예시 코드에서 변수 e는 true라는 특정한 하나의 값으로 타입을 한정시켰다. 이처럼 오직 하나의 값을 나타내는 타입을 `타입 리터럴(type literal)`이라고 한다.
        이 타입 리터럴은 모든 곳에서 일어날 수 있는 실수를 방지해서 안전성을 추가로 확보해주는 강력한 언어의 기능이다.
      ```javascript
      const c = true; // 어떤 값이 특정 boolean인지 타입스크립트가 추론하게 만든다.
      let e: true = true;
      let f: true = false; // error TS2322 'false'type을 'true' type에 할당할 수 없다.
      ```

    <br/>

    - **number**
      number type 또한 boolean과 같이 타입스크립트가 타입을 추론하도록 작성을 한다.
      아래와 같이 number도 타입 리터럴(type literal)을 사용해서 특정 값을 타입으로 한정시켜서 정의할 수 있다.

      ```javascript
      let f: 2.218 = 2.218;
      let g: 2.218 = 10; // error TS2322: '10'타입을 '26.218'타입에 할당할 수 없다.
      ```

    <br/>

    - **bigint**
      bigint 또한 타입 리터럴(type literal)을 사용해서 특정 값을 타입으로 한정시켜서 정의할 수 있다.
      bigint는 자바스크립트와 타입스크립트에 새롭게 추가된 타입으로, 253까지의 정수를 표현할 수 있는 number 대비 bigint를 사용하면 더 큰 수도 표현할 수 있다.
      bigint는 기본 사칙연산을 포함한 연산을 지원한다.
      `일부 자바스크립트 엔진에서는 지원을 안할 수도 있기 때문에 사용하기 전에 대상 플랫폼이 지원을 하는지 확인이 필요하다.`

      ```javascript
      let a = 1234n; //bigint
      const b = 5678n; //bigint
      var c = a + b; //bigint
      let g: 100n = 100n;
      let h: bigint = 100; // 100 type은 bigint 타입에 할당할 수 없다.
      ```

    <br/>

    - **string**
      string 또한 타입 리터럴(type literal)을 사용해서 특정 값을 타입으로 한정시켜서 정의할 수 있다.
      string은 모든 문자열의 집합으로 연결(+)과 슬라이스(.slice)등의 연산을 수행할 수 있다.

      ```javascript
      let f: 'john' = 'john'; // 'john'
      let g: 'john' = 'zoe'; // 'zoe'type을 'john'type에 할당할 수 없다.
      ```

    <br/>

    - **symbol**
      symbol은 실무에서는 자주 사용하지 않는다.
      주로 객체와 맵에서 문자열 키 를 대신하는 용도로 사용된다.
      Symbol('a')는 주어진 이름으로 새로운 symbol(unique)을 만든다는 의미이다. Symbol타입의 값을 타입스크립트가 추론하도록 코드를 작성하면,
      추론되는 타입이 symbol이 아닌 typeof (variable)로 정의된다.
      Symbol type의 값을 const 변수에 넣어주면, unique symbol로 정의할 수 있다.

      ```javascript
      const e = Symbol('e') // typeof e
      const f: unique symbol = Symbol('f') // typeof f
      ```

    <br/>

    - **객체**
      타입스크립트의 객체(Object) 타입은 객체의 형태(Shape)를 정의한다.
      객체의 타입만으로 간단한 객체({}로 생성)와 복잡한 객체{new로 생성}를 구분할 수 없다.
      이는 자바스크립트가 `구조 기반 타입(structural type`을 갖도록 설계가 되었기 때문이다)

      > 구조 기반 타입화에서는 객체의 이름에 상관없이 객체가 어떤 프로퍼티를 갖고 있는지를 따진다. 이를 일부 언어에서는 덕 타이핑(duck typing)이라고 정의한다.

      - **객체를 서술하는 방법**
        (1) **object 선언을 통한 객체 서술**
        object는 any보다 조금 더 좁은 타입으로, object로 선언된 객체에 서술한 값에 관한 정보를 참조할 수 없다. 단지 해당 값 자체가 null이 아닌 자바스크립트의 객체라고 명시해준다.

        (2) **타입스크립트가 추론하도록 작성**
        `객체 리터럴 문법`을 사용해서 객체 타입을 만들 수 있다. 타입스크립트가 객체의 형태를 추론하게 하거나 중괄호 안에 명시적으로 지정해 줄 수 있다.
        객체 리터럴 문법은 "이런 형태의 물건이 있어" 라고 타입스크립트에게 알려주는 기능을 한다.

        ```javascript
        let a = {
          // {b: string}
          b: "x"
        };

        let b = {
          // {c: {d: string}}
          c: {
            d: "f"
          }
        };

        let c: { firstName: string, lastName: string } = {
          firstName: "john",
          lastName: "barrowman"
        };

        class Person {
          constructor(
            public firstName: string,
            public lastName: string
          ){}
        }

        c = new Person('matt', 'smith')

        // 타입스크립트에서는 형태를 지정해주면 지정해준 형태에 해당하는 값은 반드시 내부에 선언을 해줘야 한다.
        // 만약에 선택적으로 선언이 되는 속성이라면 아래와 같이 ?로 nullable한 속성이라고 정의를 해주면 된다.
        let d : { firstName: string, lastName?: string} = {
          firstName: "merry"
        }
        ```

        `인덱스 시그니처(index signature)`란 [key: T]: U와 같은 문법을 말한다. 어떤 객체가 여러 키를 가질 수 있음을 알려주며, 해당 객체에서 모든 T 타입의 키는 U 타입의 값을 갖는다고 정의한다. 인덱스 시그니처를 이용하면 명시적으로 정의한 키 외에 다양한 키를 객체에 안전하게 추가할 수 있다.
        자바스크립트에서는 문자열을 키의 타입으로 사용하고 있기 때문에 인덱스 시그니처의 키(T)는 반드시 number나 string 타입에 할당할 수 있는 타입이어야 한다.

        ```javascript
        let a: {
          b: number
          c?:string
          [key: number]: boolean
        }

        a = {b: 1} // 인덱스 시그니처로 작성한 인자의 경우, optional 하게 넣어줄 수 있다.
        a = {b: 1, c: undefined}
        a = {b: 1, c: 'd'}
        a = {b: 1, 10: true}

        let airplaneSeatingAssignments: {
          [seatNumber: string]: string
        } = {
          "34D": "Boris Cherny",
          "34E": "Bill Gates"
        };
        ```

        객체의 타입을 정의할때에 `선택형(?)`만 사용할 수 있는 것이 아닌 필요하면 `readonly 한정자`를 이용해서 특정 필드를 읽기 전용으로 정의할 수 있다.
        즉, readonly로 정의한 특정 필드값은 초깃값을 할당한 다음에 그 값을 바꿀 수 없다.

        ```javascript
        let user: {
          readonly firstName: string
        } = {
          firstName: 'abby'
        }
        ```

        `2020/02/19 업데이트 내용`
        또 다른 객체 리터럴 표기법에는 `빈 객체 타입({})과 객체: Object`이라는 특별한 경우도 있다.
        빈 객체 타입에는 null과 undefined를 제외한 모든 타입이 할당 될 수 있다. 따라서 가능한 한 빈 객체는 피하는 것이 좋다.
        두 객체 리터럴 표기법은 서로 비슷하며 사용하는 것을 권장하지 않는다.

        ```javascript
        let danger: {};
        danger = {};
        danger = { x: 1 };
        danger = [];
        ```

        <ins><b>object타입과 Object타입을 사용한 객체 정의</b></ins><br/>
        object로 객체를 정의하는 것은 객체의 구체적인 필드 형태는 중요하지 않고 `단지 객체 자체가 필요할때` 사용된다.
