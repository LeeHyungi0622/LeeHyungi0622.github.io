---
title: 210421 TypeScript TIL - JavaScript와 TypeScript, TypeScript 설치, HTML 태그의 타입, 타입스크립트의 점진적 도입, 문법적 설탕(Syntactic sugar), tsconfig.json에서 자주 사용되는 옵션들, 공식문서 handbook 읽기
date: 2021-04-21 07:48:00
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - TypeScript
---

<div align="center">
  <img src="/images/post_images/210421_deno.jpeg" alt="Deno"/>
</div>

## <ins><b>JavaScript와 TypeScript</b></ins>

Typescript는 JavaScript의 superset이라고 불리며, 자바스크립트의 `변수/함수의 매개변수와 리턴값`에 명시적으로 타입을 지정한다.

항상 그러하듯이 공식 사이트를 참고하면서 공부하면 좋다.

[https://www.typescriptlang.org/](https://www.typescriptlang.org/)

Typescript는 언어이자 라이브러리로, 프로그래밍 언어는 실행기가 필요하다. JavaScript의 실행기는 'Browser'와 'Node'가 있다. 이 실행기가 프로그래밍 언어를 해석해주는 역할을 해준다. 그렇다면 TypeScript의 실행기는 무엇일까? TypeScript의 실행기는 현재 개발중에 있는 `Deno`이다. 아직은 안정적인 버전이아니기 때문에 `라이브러리가 TypeScript의 실행기`이다. 실행기가 라이브러리라서 좋은 점은 빠르게 release할 수 있다는 점이 있지만 단점으로는 여러가지 버그가 존재한다는 것이다.
TypeScript의 라이브러리(실행기)를 설치하기 위해서는 Node를 설치해줘야 되기 때문에 Node에 대해서 어느정도는 이해해야 한다.
이 TypeScript 라이브러리가 TypeScript를 JavaScript로 코드 변환을 해주는데 이는 브라우저는 JavaScript 코드만 이해할 수 있기 때문이다.

  <!-- more -->

## 특수기호

가끔 특수기호에 대해서 말을 할때 어떻게 말을 해야되는지 모르는 경우가 있다.
간단하게 몇 개정도만 정리를 해보겠다.
**(1) ^ :** Circumflex, Caret
**(2) & :** Ampersand
**(3) ~ :** Tilde

## <ins><b>TypeScript 설치</b></ins>

```zsh
# typescript 설치
$ npm i typescript
# typescript를 명령어로써 설치
$ npm i -g typescript
```

이제 터미널에서 `tsc`명령을 통해서 _.ts 파일을 _.js 파일로 컴파일할 수 있다.
컴파일을 하면 에러가 발생할 경우 터미널에서 `TS XXXX 에러코드`도 같이 표시가 되는데, 이 에러코드를 구글링해서 에러를 해결할 수 있다.

## <ins><b>HTML 태그의 타입</b></ins>

아래와 같이 코드를 작성하고 변수이름에 마우스 포인터를 올려보면, `HTMLDivElement` 타입이라는 것을 알 수 있다.

```typescript
// HTMLDivElement
const word: HTMLDivElement = document.createElement('div');
```

이는 타입스크립트에서 이미 만들어 놓은 것으로, 변수명에서 오른쪽 클릭한 뒤에 `"Go to type definition"`을 클릭해보면 `*.d.ts` 파일에 정의된 타입내용들을 확인할 수 있다.
_.d.ts 파일에는 타입들만 적어두었고, 이 _.d.ts 파일은 개발자가 필요에 의해 직접 작성을 하고 loading을 해서 _.ts 파일에서 사용을 할 수도 있다. loading을 하는 방법은 TypeScript에 대한 환경설정 파일인 `tsconfig.json`에서 `--types`나 `--typeRoots`의 옵션에 _.d.ts파일의 경로를 지정해주면 된다.
`tsconfig.json`의 설정에서 자주 사용되는 옵션에 대한 내용은 별도의 category로 작성을 해보겠다.

```javascript
/** Provides special properties (beyond the regular HTMLElement interface it also has available to it by inheritance) for manipulating <div> elements. */
interface HTMLDivElement extends HTMLElement {
    /**
     * Sets or retrieves how the object is aligned with adjacent text.
     */
    /** @deprecated */
    align: string;
    addEventListener<K extends keyof HTMLElementEventMap>(type: K, listener: (this: HTMLDivElement, ev: HTMLElementEventMap[K]) => any, options?: boolean | AddEventListenerOptions): void;
    addEventListener(type: string, listener: EventListenerOrEventListenerObject, options?: boolean | AddEventListenerOptions): void;
    removeEventListener<K extends keyof HTMLElementEventMap>(type: K, listener: (this: HTMLDivElement, ev: HTMLElementEventMap[K]) => any, options?: boolean | EventListenerOptions): void;
    removeEventListener(type: string, listener: EventListenerOrEventListenerObject, options?: boolean | EventListenerOptions): void;
}
```

interface는 객체로, 위에서는 타입관련된 내용을 interface로 객체로써 정의하였다. 타입에 대한 내용도 공통 타입속성에 대해서는 상속을 통해서 구현을 하고 있다.
이렇게 이미 정의되어있는 타입에 대한 정의 파일로 인해 IDE를 사용하게 되면, 코드 작성시에 관련 메소드 및 속성에 대한 내용을 확인해가며 작업을 할 수 있기 때문에 실수를 줄이고 코드 작성에 있어 생산성을 높여줄 수 있다.

`자신이 직접 타입을 정의해서 사용할 수도 있지만 다른 사람이 작성한 타입을 읽고 이해하는 것 또한 중요하다.`

## <ins><b>타입스크립트의 도입</b></ins>

만약 개발중인 어플리케이션이 잠정적으로 시스템 확장을 고려해야 한다면, 간단하더라도 타입 스크립트를 도입해서 개발을 하는 것이 좋다.
기존에 자바스크립트로 구현된 어플리케이션도 타입스크립트를 점진적으로 도입해가면서 업그레이드할 수 있다. 이 작업을 할때에는 `tsconfig.json`파일에서 `--allowJs: true` 옵션을 넣어줘야 한다. 이는 js 파일의 컴파일을 허용한다는 옵션으로, TypeScript에서는 기본적으로 \*.ts 파일에 대한 컴파일만 허용하기 때문이다.

## <ins><b>문법적 설탕(Syntactic sugar) ?</b></ins>

문법적 설탕이란 `사람이 프로그래밍 언어를 달달하게 사용할 수 있도록 도와주는 문법`, `더욱 더 간결하고 명확하게 표현이 가능한 문법`, `사람이 이해하고 표현하기 쉽게 디자인된 프로그래밍 언어 문법`으로 정의한다.
예를들어, 기존에 ES6이전의 문법에서는 자바스크립트로 클래스를 정의할때 prototype으로 상속을 구현해서 사용을 하였는데, ES6부터는 클래스 기반의 언어와 같이 클래스를 정의해서 손쉽게 상속을 구현해서 사용할 수 있다. (`여전히 프로토타입 기반의 언어이지만 문법상 클래스 기반의 언어와 같이 클래스를 정의할 수 있다는 말`)

## <ins><b>^3.7.4의 의미</b></ins>

package를 설치하고 나면 package.json 파일 안에서 각 dependency 항목의 버전들이 `^3.7.4`로 표기되어 있는 것을 볼 수 있다. 이는 `3.7.4 <= x < 4.0.0`이라는 의미로, caret뒤의 숫자(3) 버전의 최신 버전을 설치하겠다는 의미이다.(버전 4는 미포함) 따라서 `'^3.7.4'`가 아닌 `'^3'`로만 적어줘도 된다. 그럼 정확히 어떤 버전이 설치되었는지는 어떻게 확인할 수 있을까?
바로 `package-lock.json` 파일내에서 확인을 할 수 있다. 이 곳에서는 구체적으로 어떤 버전의 package가 설치되었는지 확인할 수 있다. 따라서 프로젝트 폴더를 Github에 업로드할때에는 반드시 `package-lock.json`파일을 함께 업로드 해줘야 한다.

## <ins><b>npx tsc 명령을 통한 컴파일</b></ins>

`$ npm i -g typescript`를 통해 설치된 typescript의 버전과 프로젝트의 타입스크립트 버전이 일치하지 않는 경우 문제가 생길 여지가 있다. 따라서 타입스크립트 파일을 컴파일 할 때에는 `$ npx tsc`를 사용해야 한다. npx 명령을 쓰면 프로젝트에 설치된 타입스크립트의 버전으로 맞춰서 컴파일을 해준다.

## <ins><b>tsconfig.json 에서 자주 사용되는 옵션들</b></ins>

tsconfig.json에는 TypeScript 코드에서 JavaScript 코드로 변환을 할때 필요한 설정에 대한 정보를 담고 있다.
타입스크립트 파일과 컴파일된 자바스크립트 파일을 동시에 켰을때 타입스크립트 파일내의 코드에 에러표시가 나는 경우가 있는데, 열려있는 컴파일된 자바스크립트 파일을 닫았을때 해결이 되었다. 이는 tsconfig.json 파일에서 설정과 관련이 있다.
아래에서 tsconfig.json 파일에서 자주 사용되는 옵션들을 위주로 살펴보도록 하겠다.

`공식 사이트`: [https://www.typescriptlang.org/docs/handbook/tsconfig-json.html](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)

- ### **--allowJs**
  **\*.js 파일의 컴파일을 허용할때 사용되는 옵션이다.** (true) 기존에 JS로 작성된 어플리케이션을 TS로 점진적으로 업그레이드 하는 경우, 이 옵션을 true로 해서 넣어줘야 한다. typescript에서는 기본적으로 \*.ts 파일에 대한 컴파일만 허용을 하기 때문이다.
- ### **--baseUrl**
  기본 경로 지정할때 사용되는 옵션이다.
- ### **--checkJs**
  allowJs 옵션과 같이 사용되는 옵션이다.
- ### **--declaration / -d**
  _.d.ts 파일을 작성하는 경우, true로 옵션을 활성화 시켜준다.
  주로 객체와 함수를 사용할때 type을 지정할 필요성이 생기기 때문에 _.d.ts 파일을 작성한다. 타입에 대한 정의 파일을 별도로 분리함으로써 다른 파일에서 해당 타입에 대한 정의파일을 재사용할 수도 있다.
- ### **--esModuleInterop**
  사용에 있어 주의가 필요한 옵션이다.
  원래 `import * as React from 'react'`를 사용하는 것이 공식적으로 맞지만,
  일반적으로 개발자들이 `import React from 'react'`를 사용할때 에러가 발생해서 이 옵션을 활성화시키는 경우가 있다. 이는 옳지 않으며 되도록 공식적으로 권장하는 방식으로 코드를 작성하도록 하자.
- ### **--emitDecorator ~ **
  데코레이터를 사용하는 경우에 사용하는 옵션이다.
- ### **--init**
  `tsc init`을 하면 tsconfig.json 파일을 자동으로 생성해주는 옵션이다.
- ### **--jsx**
  TypeScript는 ts 파일을 js 파일로 변환해 줄 뿐만 아니라, React나 React Native에서 tsx 파일을 jsx 파일로 변환해주기도 한다. 이러한 경우에 활성화시켜야 되는 옵션이다.
- ### **--lib**

  만약 ts 파일에서 자바스크립트의 최신 문법을 인식하지 못하는 경우, 아래와 같이 추가적으로 라이브러리를 추가해줘야 한다.
  `ESNEXT` (가장 최신 JS문법)

  ```zsh
  "--lib": ["DOM", "ES5", "ES2015", "ES2020"]
  ```

  ES2015(ES6)와 ES2017(ES8)은 매우 중요하기 때문에 --lib 옵션을 사용하는 경우에 반드시 옵션 값으로 넣어줘야 한다.

  ```zsh
  "--lib": ["ES6", "ES2016", "ES2017", "ES2018", "ES2019", "ES2020"]
  ```

- ### **--outDir**
  \*.ts 파일과 컴파일된 \*.js 파일의 경로를 다르게 지정하고 싶을때 사용되는 옵션이다.
- ### **--target / -t**
  기본적으로 타입스크립트가 JS `ES3`로 코드 변환을 시켜준다. (`ES3는 IE8까지 지원`)
  반면, Babel은 `ES5`로 코드 변환을 해준다. 따라서 과거에는 개발자들이 IE8까지 지원을 해야되는 경우에 어쩔 수 없이 타입스크립트를 사용하는 경우도 있었다고 한다. (`ES5는 IE11까지 지원`)
  만약에 컴파일되는 JS파일에서 변수의 선언이 var 키워드로 되는 것이 싫다면, `"--target": "ES6"`로 해주도록 하자.
  하지만 "ES6"의 경우에는 IE에서 지원을 하지 않는다. 만약에 IE를 지원해야 되는 경우에는 Babel로 추가적으로 ES5로 컴파일해줘야 한다.
- ### **--types / --typeRoots**
  직접 작성한 \*.d.ts 파일에 대한 경로를 지정할때 사용하는 옵션이다. \*.ts파일에서 직접 작성한 \*.d.ts 파일을 로드해서 사용해야되는 경우 필요한 옵션이다.
- ### **--strict**
  `strict`나 `noImplicit`으로 시작되는 옵션은 모두 `true`로 해두는 것이 좋다. 이것은 TypeScript의 엄격한 장점을 사용하기 위한 옵션들이다.
  ```zsh
  "noImplicitAny": true
  "noImplicitThis": true
  "noImplicitReturn": true
  ```
  <ins><b>초보자의 경우 `"strict": true` 옵션만 넣어주더라도 TS의 대부분의 기능을 사용할 수 있다.</b></ins>
- ### **--module / -m**
  대부분 `"CommonJS"`을 사용하지만 주의해야 될 옵션은 `--esModuleInterop` 옵션이다.
  `import React from 'react'`와 `import * as React from 'react'`는 `export default` 할때 차이가 있기 때문에 주의해야 한다.
  만약 최신 문법만 지원을 하는 경우에는 `"ES6"`로 옵션 값을 지정해주면 된다.
  만약 IE를 지원해야 될 때에는 `"CommonJS"`로 옵션 값을 지정해주면 된다.
- ### **--watch**
  타입스크립트 파일에서의 변경사항을 자동으로 감지해서 자바스크립트 파일로 자동 컴파일 시켜주는 경우 사용되는 옵션
- ### **include: []**
  컴파일할 파일들을 넣어주는 옵션이다. 만약 특정 파일만 컴파일하는 경우에는 아래와 같이 작성을 해준다.
  ```zsh
  "include": ["main.ts"]
  ```
- ### **exclude: []**
  include옵션과는 반대로 컴파일을 하지 않을 파일들을 넣어주는 옵션이다.
  ```zsh
  "exclude": ["*.js"]
  ```
- ### **extends: " "**
  이 옵션은 하나의 폴더 안에 여러 개의 타입스크립트 프로젝트가 있고, `공통으로 사용할 tsconfig.json 파일`과 `개별 프로젝트별로 사용할 tsconfig.json 파일`이 있는 경우, 각 각의 개별 tsconfig.json 파일 내에서 공통 tsconfig.json 파일을 확장해서 사용할 필요가 있는 경우, 사용될 수 있는 옵션이다.

## <ins><b>공식문서의 Handbook 읽기</b></ins>

공식문서에서 타입스크립트 핸드북을 처음부터 끝까지 읽어보자.
타입스크립트에서 어떤 기능이 어떤 버전에서 추가가 되었는지 알 수 있다.

[https://typescript-kr.github.io/](https://typescript-kr.github.io/)
