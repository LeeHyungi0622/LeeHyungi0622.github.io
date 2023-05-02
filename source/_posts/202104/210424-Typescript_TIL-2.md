---
title: 210424 TypeScript TIL-2 - NodeJS의 모듈 시스템과 TypeScript의 모듈 시스템
date: 2021-04-24 15:33:00
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

## <ins><b>NodeJS의 모듈 시스템과 TypeScript의 모듈 시스템</b></ins>

타입스크립트의 모듈 시스템을 이해하기 전에 자바스크립트의 모듈 시스템 아니 더 정확하게는 NodeJS의 모듈 시스템에 대해서 알아보도록 하겠다.

- **NodeJS의 모듈 시스템** = `commonJS`

- **TypeScript의 모듈 시스템** = `JavaScript의 최신 문법(ES2015(ES6))`을 모듈 시스템에서 그대로 계승

우선 module을 이해하기 이전에 스크립트와 모듈을 구분할 수 있어야 한다.
만약에 `자바스크립트 파일 안에 import와 export가 있는 경우, module이며, 없다면 script`이다.

그렇다면 모듈이란 무엇일까? `모듈은 다른 자바스크립트 파일에서 특정 자바스크립트 파일을 사용할 수 있도록 만들어주는 것`이다. 다시말해 파일을 잘개 쪼개는 효과를 줌으로써 `코드의 가독성과 재사용성`이 가능하도록 해주는 것이다.

자 이제 모듈의 개념에 대해서 간단하게 이해했으니 이제 NodeJS의 모듈 시스템과 TypeScript의 모듈 시스템의 차이에 대해서 살펴보도록 하겠다.

`commonJS`

```javascript
// module.js
exports.a = 'b';
exports.b = false;
module.exports = {
  a: 'b',
  b: false
};

// run.js
const { a, b } = require('./module');
```

  <!-- more -->

우선 commonJS에서 exports와 module.exports는 같은 exports이다. 따라서 위와같이 module.js 파일에서 exports와 module.exports를 같이 작성하게 되면, module.exports에서 이전에 작성해준 exports를 덮어쓰게 되기 때문에 run.js파일에서 호출해서 사용할 수 없게 된다.
따라서 하나의 파일에서는 둘 중에 하나만 사용하는 것이 좋다.
또한 module.exports는 하나의 파일에서 한 번만 써야 한다는 제약조건이 있다.

`NodeJS 모듈 시스템에서 함수를 exports`

```javascript
// module.js
module.exports = function () {};

// run.js
const func = require('./module');
func();
```

**자 그럼 TypeScript 모듈 시스템에 대해서 알아보자.**
이전에 NodeJS 모듈 시스템에서 exports와 module.exports가 같은 exports를 사용하기 때문에 같이 사용하게 되면 덮어써진다는 한계가 있다고 배웠다. 이러한 `한계점을 극복하기 위해서 TypeScript의 모듈 시스템에서는 default 개념을 도입`했다.

`JS 최신 문법(ES2015)을 계승한 모듈 시스템`

```javascript
// module.js
export const a = 'b';
export const b = false;
export default function () {}

// run.js
import func, { a, b } from './module';
console.log(a, b);
func();
```

위와같이 타입스크립트의 모듈 시스템에서는 export와 export default에서의 export가 같지 않기 때문에 덮어쓰지 않아 export한 a, b, Func을 다른 파일에서 import 해서 사용할 수 있다.

아래와같이 export를 해줄수도 있다.

```javascript
const a = 'b';
const b = false;

export { a };
export { b };
```

TypeScript의 모듈 시스템을 이해하기 위해서는 commonJS를 이해해야 한다. 그 이유는 TypeScript는 ES2015(ES6) 문법만 지원하는 것이 아닌 commonJS 문법도 지원하기 때문이다.

따라서 TypeScript에서 모듈을 처리하는 방법이 나뉜다.
만약에 특정 모듈이 commonJS로 작성(`module.exports`)이 되어있고 TypeScript 파일에서 이를 `import`해서 사용하려면 아래와 같이 작성을 해줘야 한다.

```javascript
// module.js
module.exports = function () {}; // export default와 다르다.

// 따라서 타입스크립트 파일에서 위의 module을 import할때는 아래와 같이 작성해줘야 한다.
import * as func from './module';
```

commonJS로 작성된 module들은 꼭 위와같이 _ as 를 붙여서 import해야한다.
tsconfig.json 파일에서 esModuleInterop 옵션을 활성화 시켜주면 _ as 를 빼고 import를 해 줄 수 있지만, 좋지 않은 방법이기 때문에 반드시 commonJS로 작성된 module을 TypeScript 파일에서 import할때에는 위와같은 방식으로 사용하도록 하자.

## <ins><b>타입의 정의부분을 별도의 파일로 분리하기</b></ins>

우리가 타입을 위해 선언해준 interface와 class, type alias는 보통 같은 파일에서 관리를 안하고 별도의 파일로 분리해서 재사용이 가능하게 만든다.
(파일 안에서 export interface로 외부 파일에서 import해서 사용할 수 있도록 할 수도 있음)

`types.ts`

```javascript
export interface IExample {
  ...
}

export class Example implements IExample {
  ...
}
```

## <ins><b>commonJS 문법으로 작성된 JS파일을 타입스크립트 파일에서 사용하기 위한 \*.d.ts 파일 정의</b></ins>

`common.js`

```javascript
module.exports = function () {
  console.log('hi');
};
```

TypeScript 파일에서 common.js 파일을 사용하기 위해서는 common.js 파일에 해당하는 타입을 만들어줘야 한다.

`common.d.ts`
\*.d.ts 파일은 **ambient module**이라고 불린다. \*.d.ts 파일은 보통 내 프로젝트가 라이브러리일 경우 사용한다.
`ambient: 주이(주변)의, 잔잔한, 은은한`

```javascript
declare function a(){}
// 대부분의 유명한 라이브러리가 아래와 같이 export = 으로 되어있다.
export = a;
```

이제 common.js 파일을 타입스크립트 파일에서 가져다가 사용하기 위해서는 아래와 같이 import를 해줘야 한다.

```javascript
// common.d.ts 파일에서 export = 방식으로  export를 해주었기 때문에 require로 호출해서 사용해야 한다.
import A = require('./common');
```

혹은 위의 방식을 아래와 같이 대체해서 쓸 수 있다.

```javascript
// A에 포함되어있는 모든 모듈들(*)을 가져온다는 의미
import * as A from './common';
```

`20210425 Update`

## <ins><b>외부 라이브러리 사용하기 in TypeScript</b></ins>

자바스크립트는 commonJS 문법으로 사용된 역사가 길다. 따라서 타입스크립트에서 외부 라이브러리를 사용할때에는 반드시 해당 라이브러리의 저장소를 확인하도록 하자.
해당 라이브러리가 `어떤 언어로 비중있게 개발이 되었는지`, `타입이 정의된 파일(index.d.ts)을 제공하는지`에 대한 확인이 필요하다.
대표적인 예시로 `redux`와 `axios` 라이브러리를 살펴보자.

- ### **redux**
  redux 라이브러리는 `TypeScript 76.6%`, JavaScript 19.0%로 개발이 된 라이브러리이다.
  한 번 프로젝트에서 npm 명령으로 redux를 설치한 뒤에 node_modules 폴더의 하위의 redux폴더를 살펴보면 index.d.ts 파일을 확인할 수 있다. 이 파일은 redux 관련 타입들을 정의하고 있다.
  대부분 최신 문법(ES2015/ES6)으로 작성이 되어있기 때문에 일반적인 import 구문으로 필요한 요소를 불러와서 사용하면 된다.
- ### **axios**
  redux와는 반대로 axios 라이브러리는 `JavaScript 93.3%`, TypeScript 3.9%의 비율로 개발이 된 라이브러리이다. 그럼 axios에서는 타입을 지원하지 않는 것일까? 아니다. axio도 적은 비율이지만, 3.9%의 TypeScript의 비율을 갖기 때문에 설치후 node_modules 폴더의 하위를 살펴보면 index.d.ts 파일을 제공한다.
  <ins><b>\*.d.ts 파일의 내부 코드를 살펴보면 `declare 구문`을 확인할 수 있는데 `타입이 없는 것을 새로 정의할때 사용하는 키워드`이다.</b></ins>
  또 생소한 ///(Triple slash)\<reference />를 볼 수 있다. 이는 \*.d.ts파일의 최상단에 위치하면서 특수한 기능을 한다. 이 ///\<reference />는 path와 type 등 다양한 속성을 갖는데, path는 경로에 대한 정보를, type은 package이름에 대한 정보를 갖는다.
  라이브러리를 만들때 필요한 외부 package의 타입을 참조하는 경우, reference의 type 속성에 해당 라이브러리의 이름을 속성값으로 넣어준다. (node_modules 폴더 하위에 설치된 이름) 그러면 해당 module의 index.d.ts(타입 정의 파일)을 해당 d.ts 파일에서 참조할 수 있게 되는 것이다.

## <ins><b>DefinitelyTyped</b></ins>

만약에 사용하고자 하는 외부 라이브러리에서 타입을 지원하지 않는 경우(index.d.ts파일을 제공 안하는 경우), 이런 경우에는 DefinitelyTyped 저장소에서 해당 라이브러리를 지원하는지 살펴보자.

**DefinitelyTyped/DefinitelyTyped**
→ [https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types](https://github.com/DefinitelyTyped/DefinitelyTyped/tree/master/types)

- **사용방법**
  사용방법은 아래와 같이 npm i @types/<module name>을 작성해주면 된다.
  ```zsh
  # jquery 모듈을 DefinitelyTyped에 정의된 타입파일과 같이 설치하는 경우
  $ npm i jquery @types/jquery
  ```
