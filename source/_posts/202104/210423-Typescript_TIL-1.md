---
title: 210423 TypeScript TIL-1 - as const와 readonly, interface와 type alias의 비교 및 사용, interface의 다양한 활용 및 문제해결
date: 2021-04-23 12:00:00
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

## <ins><b>as const와 readonly의 사용</b></ins>

객체를 `as const`로 정의를 해주게 되면 객체내의 모든 타입 속성들이 `readonly`로 바뀌는 것을 확인할 수 있다. 만약 일부의 타입 속성들만 상수로써 바뀌지 않게 하고 싶다면 해당 타입 속성의 변수명의 앞에 `readonly`를 붙여주도록 한다.
만약 변수의 타입이 상수 타입으로 고정되는 경우, 이 경우에는 별도로 `as const`나 `readonly`를 붙여주지 않아도 상관없다.

## <ins><b>interface와 type alias 비교 및 사용</b></ins>

interface와 type alias를 사용해서 객체의 타입을 정의할때 그 용도를 일관되게 나눠서 사용하는 것이 좋다.
예를들어, `객체의 타입은 interface를 사용해서 정의`하고 `custom type은 type alias를 사용해서 정의`하도록 하는 것이다.
interface와 type 모두 아래와 같이 객체의 타입을 정의할 수는 있지만 코드를 작성할때 일관성을 지켜서 코드를 작성하는 편이 좋다.

### **interface의 특징**

- #### **interface내에 정의한 타입 속성은 ,(콤마)나 ;(세미콜론), \n(줄바꿈)으로 구분할 수 있다.**

- #### **다른 type interface를 상속할 수 있다.**

  ```javascript
  interface ParentExample{
    readonly A: string,
    readonly B: string
  }

  interface ChildExample extends ParentExample{
    readonly C: string
  }
  ```

  <!-- more -->

- #### **같은 이름의 interface를 여러번 정의할 수 있다.**

  interface에서는 위와 같이 동일한 이름으로 정의를 해 줄 수 있다. `이렇게 작성을 해주게 되면, 이름이 같은 두 interface는 서로 합쳐진다.`

  ```javascript
  interface Example {
    readonly A: string,
    readonly B: string
  }

  interface Example {
    readonly C: string
  }
  ```

  위의 특징은 남이 작성한 라이브러리를 사용해서 타입을 정의하는데, 만약에 타입의 정의에 문제가 있어서 기존의 라이브러리에 있는 타입의 정의를 수정해서 사용하고 싶을때 `라이브러리의 타입 interface와 동일한 이름으로 interface를 정의해서 타입을 추가해서 사용`할 수 있다.

### **type alias의 특징**

type alias는 interface보다 좀 더 넓은 범주이다.

- #### **type alias는 합쳐지지도 상속되지도 않는다.**

- #### **type alias로도 객체의 타입을 정의할 수 있다.**

  type alias로도 객체의 타입을 정의할 수는 있지만 되도록 일관성있게 객체의 타입은 interface로, custom type의 정의는 type alias를 사용하도록 한다.

  ```javascript
  type Example = {
    readonly A: string,
    readonly B: string
  }
  ```

- #### **type alias로는 | 연산자를 사용할 수 있다.**

  ```javascript
  type ObjectOrString = {
    readonly A: string,
    readonly B: string
  } | string;
  ```

- #### **type alias 방식은 주로 별칭으로 새로운 타입을 정의할때 사용된다.**

  ```javascript
  type Example = string | number; // string이나 number 타입
  ```

<br/>

## <ins><b>interface의 다양한 활용 및 문제해결</b></ins>

- #### **keyof (interface name)와 (interface name)[typeof (interface name)]**

  아래의 코드와 같이 interface 내에서 사용된 변수명 또는 변수의 타입이 특정 함수의 매개변수 혹은 반환값의 타입으로써 사용이 된다면, keyof 키워드를 사용해서 interface 내의 변수명과 변수의 타입을 재사용해서 코드의 중복없이 재정의할 수 있다.

  ```javascript
  interface Example {
    readonly A: '0',
    readonly B: '-142px',
    readonly C: '-284px'
  }

  // exampleFunc의 인자 imgCoords가 Example interface의 type들인 경우 아래와 같이 풀어서 작성해 줄 수 있지만,
  function exampleFunc(imgCoords: '0'|'-142px'|'-284px' ): 'A'|'B'|'C' {
    ......
  }
  // interface의 정의를 재활용할 수도 있다.
  // 또한 반환값도 Example interface의 타입 속성의 이름이기 때문에 아래와 같이 keyOf Example로 대체할 수 있다.
  function exampleFunc(imgCoords: Example[keyof Example]): keyOf Example{
    ......
  }
  ```

- #### **interface의 타입속성이 확실하지 않을때 동적으로 속성을 지정해줄 수 있다.**

  아래에서 Example interface에서는 속성값 a는 상수 3을 타입으로 갖고, b는 상수 7을 타입으로 갖는다고 정의하고 있다. 이를 이용해서 객체 example을 생성한다고 했을때, 만약 a, b이 외에 다른 number타입의 객체 속성을 넣어주고자 한다면 어떻게 해야할까?

  바로 아래와 같이 대괄호 표기법으로 내부에 [key: string] 으로 정의해서 속성의 이름이 동적으로 정의될 수 있도록 할 수 있다.
  하지만 되도록이면 타입스크립트를 사용할때에는 객체의 타입을 엄격하게 정의해서 사용하는 것이 좋다.

  ```javascript
  interface Example {
    a: 3;
    b: 7;
    [key: string]: number;
  }

  const example: Example = {
    a: 3,
    b: 7,
    d: 100
  };
  ```

- #### **기본 \*.d.ts 문제해결하기**

  일반적으로 타입스크립트에서 기본적으로 정의되어 있는 \*.d.ts를 사용해서 코드를 작성할때 문제가 되는 경우가 있다. 아래 예시를 살펴보자.
  아래의 코드에서는 Object.keys를 통해 타입을 정의한 interface에서 타입의 이름을 배열의 형태로 불러와서 Example을 타입으로 갖는 객체 변수 example의 키값으로 정의해서 매개변수 imgCoords와 엄격한 비교연산을 통해 비교하고 있다. 비교연산이 참인 경우의 키 값만을 반환하도록 하는 처리를 하고 있다.

  ```javascript
  Object.keys(Example).find((k) => example[k] === imgCoords);
  ```

  하지만 Object.keys가 정의되어있는 `lib.es5.d.ts` 파일을 살펴보면 아래와 같이 keys 메서드는 문자열 타입의 배열(string[])만을 반환하고 있다.

  ```javascript
  interface ObjectConstructor {
  ......
  keys(o: object): string[];
  }
  ```

  하지만 example 객체 변수는 interface Example을 타입으로 정의하고 있기 때문에 example[k]에서 k는 interface Example에서 정의하고 있는 타입의 이름이 되어야 한다.  
  따라서 위에서 interface에 정의되어있는 타입이름을 배열값으로 갖는 배열을 참조하기 위해 의도적으로 위와같은 코드를 작성했지만 실제로는 string 타입의 배열이 반환되기 때문에 find의 콜백함수 k에는 type 'string'이 반환된다.
  이러한 문제를 해결하고 위해서는 명시적으로 Object.keys를 통해 가져온 문자열 배열의 타입을 캐스팅해줘야 한다.

  ```javascript
  (Object.keys(example) as ['A','B','C']).find((k) => example[k] === imgCoords);
  ```

  위와같이 Example을 타입으로 갖는 객체변수 example의 키값을 배열로 호출했을때 단순 문자열 배열이 아닌 구체적으로 길이와 속성이 고정된 타입의 배열로써 호출이 된다.

- #### **자바스크립트 버전 호환문제 해결하기**

  위의 예시에서 사용된 find 메서드는 ES6에서 새롭게 추가된 기능이다. tsconfig 에서 lib 옵션은 기본값으로 `ES5`를 갖기 때문에 find 메서드를 사용하기 위해서는 `tsconfig.json의 lib옵션에 추가적으로 자바스크립트 라이브러리들을 추가`해줘야 한다.

  ES2014 - ES5
  ES2015 - ES6
  ES2016 - ES7
  ...

  ```json
  {
    "compilerOptions": {
      "strict": true,
      "lib": [
        "ES5",
        "ES6",
        "ES2016",
        "ES2017",
        "ES2018",
        "ES2019",
        "ES2020",
        "DOM"
      ]
    },
    "exclude": ["*.js"]
  }
  ```

- #### **타입 시스템과 프로그래머의 의도가 다른 경우**

  ```javascript
  find(predicate: (value: T, index: number, obj: T[]) => unknown, thisArg?: any): T | undefined;
  ```

  앞서 예시 코드에서 살펴본 find 메서드의 경우, T 또는 undefined을 반환한다고 정의가 되어있다.
  만약에 find의 콜백함수 내에서 사용된 매개변수가 특정 객체의 key값으로써 사용이 된다면 에러가 발생된다.
  따라서 이런 경우에는 명시적으로 프로그래머가 undefined가 발생하지 않는다는 것을 보증한다는 의미에서 아래와 같이 !를 붙여줘야 한다.

  ```javascript
  (Object.keys(example) as ['A','B','C']).find((k) => example[k] === imgCoords)!;
  ```

- #### **addEventListener에서 콜백함수**

  addEventListener에서 콜백함수에 화살표 함수가 아닌 익명 함수(일반 함수)의 형태로 넣었을때 첫 번째 인자로 바인딩해줄 this를 넣어줄 수 있다. 화살표 함수로 콜백함수를 넣어주게 되면 자동으로 상위 스코프의 객체를 this에 바인딩해주기 때문에 마침표 연산자 앞에 호출한 객체를 가르키게 된다. 하지만 익명 함수(일반 함수)의 형태로 넣어주게 되면 this에는 전역 객체가 바인딩된다.
  따라서 addEventListener의 콜백함수로 넣어준 일반 함수내에서 this를 사용하는 경우에는 addEventListener의 콜백함수의 첫 번째 인자로 this를 정의(두 번째 인자는 event 객체)해서 사용해야 한다.

  ```javascript
  btn.addEventListener('click', function (this: HTMLButtonElement, e: Event) {
    // button의 textContent를 this로 가져오기
    this.textContent;
    // button의 textContent를 event 객체로 가져오기
    e.target.textContent;
  });
  ```

  또한 this.textContent를 통해 참조한 버튼의 TextContent는 타입스크립트에서는 string | null 로써 인식을 하기 때문에 만약 this.textContent의 값을 다른 곳의 인자로써 사용하고자 한다면 구체적으로 해당 참조값의 타입을 명시해줘야 한다.

  ```javascript
  this.textContent as keyof Example;
  ```

### **타입스크립트를 사용할때에는 타입의 범위를 좁혀서 엄격하게 처리해줘야 한다.**
