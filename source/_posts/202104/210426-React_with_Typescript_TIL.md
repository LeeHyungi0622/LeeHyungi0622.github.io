---
title: 210426 React with TypeScript TIL - Webpack + TypeScript, react/react-dom - DefinitelyTyped, webpack.config.json, event handler와 useRef typing, class state typing, useState의 타입추론, useCallback typing, class component의 createRef typing, react-hot-loader 사용
date: 2021-04-26 18:29:00
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - ReactJS-TypeScript
---

<div align="center">
  <img src="/images/post_images/ts-and-react.png" alt="React with TypeScript"/>
</div>

## <ins><b>프로젝트 기본 구성</b></ins>

typescript, react, react-dom, webpack, webpack-cli 설치

```zsh
$ npm i typescript
# 웹 환경에서는 react-dom, 모바일은 react-native
$ npm i react react-dom

$ npm i -D webpack webpack-cli
```

이전에 React에서 webpack + babel을 사용해서 JavaScript의 최신문법과 JSX 문법을 이전의 JavaScript 문법으로 변환해주었는데, 이번 TypeScript에서는 webpack + TypeScript 조합으로 변환을 해준다.

TypeScript는 자체적으로 babel처럼 최신문법이나 jsx 문법을 이전 문법(ES3 or ES5)으로 바꿔준다. 따라서 별도로 Babel을 사용할 필요는 없다. 하지만 TypeScript + Babel의 조합으로 사용하는 경우도 있기 때문에 이 부분을 알아두도록 하자.

  <!-- more -->

`package.json`

```json
"script": {
  "dev": "webpack",
}

$ npm run dev
    or
$ npx webpack
```

## <ins><b>Webpack + TypeScript 이어주기</b></ins>

웹팩과 타입스크립트를 이어주기 위해서 loader를 사용해야 되는데, 유명한 loader에는 아래 두 가지가 있다.

- ### ts-loader
- ### awesome-typescript-loader (atl)

두 loader의 차이점은 atl의 경우에는 use babel 옵션을 사용해서 babel과 함께 사용할 수 있게 해준다. 따라서 잠재적으로 babel을 함께 사용하는 경우에는 atl을 사용하도록 한다. 또한 atl은 ts-loader 보다 속도가 빠르다고 한다.

```zsh
$ npm i -D awesome-typescript-loader
```

이전의 React에서는 webpack + babel을 위해서 babel-loader를 사용했다.

## <ins><b>react, react-dom - DefinitelyTyped</b></ins>

react와 react-dom의 경우에는 자체적으로 타입을 제공하지 않지만, 유명한 라이브러리이기 때문에 DefinitelyTyped에서 제공해주는 type을 사용하면 된다.

antd design과 같은 경우는 TypeScript로 만들어져있기 때문에 별도의 설치없이 사용할 수 있다.

```zsh
$ npm i @types/react @types/react-dom
```

## <ins><b>webpack.config.json</b></ins>

기존에 react에서의 webpack 설정파일과 다른점은 babel을 사용하지 않기 때문에 babel의 설정부분만 약간 차이가 있다.

devtools의 경우에는 production mode일때 반드시 hidden-source-map으로 속성을 바꿔줘야 한다. 만약에 source-map으로 써주면 브라우저의 개발자 모드에서 내가 작성한 코드가 전부 노출이 된다.

```json
const path = require('path');
const webpack = require('webpack');

module.exports = {
    mode: 'development', // production
    devtool: 'eval', // hidden-source-map
    resolve: {
        extensions: ['.jsx', '.js', '.tsx', '.ts'],
    },

    entry: {
        app: './client'
    },
    module: {
        rules: [{
            test: /\.tsx?$/,
            loader: 'awesome-typescript-loader',
        }]
    },
    output: {
        filename: '[name].js',
        path: path.join(__dirname, 'dist'),
    }
}
```

간단하게 위의 웹팩 설정파일을 해석하면 entry에 작성해준 client라는 이름의 파일이 main으로 module에 작성해준 rules의 내용에 맞춰 .ts 혹은 .tsx 확장자의 파일이 `awesome-typescript-loader`를 통해 자바스크립트의 이전 문법으로 변환이 된다. 원래 자바스크립트로 리액트를 했을때 웹팩의 설정에서는 이 부분을 babel-loader로 해주었지만, TypeScript에서는 `awesome-typescript-loader`를 넣어준다.
최종적으로 변환된 .js파일은 output에서 작성한 내용대로 file이름은 entry에서 작성해준 app 이름을 기준으로 js파일이 생성되는데 경로는 프로젝트 폴더의 하위에 dist라는 폴더를 새로 생성해서 그 하위에 생성된다.

## <ins><b>event handler, useRef typing</b></ins>

함수를 별도로 빼주지 않고 인수로 넣어주게 되면 자동으로 타입추론이 된다. 하지만 보통은 React에서 성능 최적화를 위해서 함수를 별도로 빼서 작성을 해주게 된다.
타입스크립트에서 별도로 빼준 함수는 타입 추론을 할 수 없기 때문에 함수의 매개변수에 대해서 별도의 typing이 필요하다.
아래의 예시 코드에서 onSubmitForm 함수는 별도의 함수로 빼서 작성해주었기 때문에 매개변수 e에 대해서 별도의 타입 정의를 해주었다. Form에 들어가는 이벤트이면서 FormElement에 들어가기 때문에 제네릭으로 `React.FormEvent<HTMLFormElement>`타입으로 작성을 해주었다.

useRef의 경우, 아래와 같이 제네릭으로 useRef로 어떤 DOM element를 지정하기 위한 것인지 구체적으로 타입을 지정해줄 수 있다. 타입을 지정하지 않는다면, .focus()를 사용하는 경우, 속성을 제대로 찾을 수 없다.
inputEl 요소가 처음에 null로 초기화가 되어있고, input 태그의 내부에서 값이 초기화되기 때문에 외부 함수에서는 존재하는지 확실하지 않기 때문에 존재를 보증하는 !를 쓰거나 if 조건분기문 처리를 해서 작성해주도록 한다.

```javascript
const inputEl = useRef<HTMLInputElement>(null);

const onSubmitForm = (e: React.FormEvent<HTMLFormElement>) => {
  ...
  const input = inputEl.current;
  if(input){
    input.focus();
  }
  // or input!.focus();
}
...
return(
  ...
  <form onSubmit={onSubmitForm}>
  <input
    ref={inputEl}
    type="number"
    value={value}
    onChange={(e) => setValue(e.target.value)}
  />
  ...
)
```

## <ins><b>class state typing</b></ins>

setState를 할때 이전 상태를 참조하기 위해 함수형으로 업데이트를 하는 경우, 이전 상태에 대해 타입을 추론할 수 없는 경우가 생긴다.
따라서 state에 대한 interface를 작성하고, 클래스에서 상속받은 React.Component에 제네릭으로 처리한다. 제네릭의 첫번째 인자는 props에 대한 타입이고, 두번째 인자는 state에 대한 타입이다.

```javascript
interface IState {
  first: number;
  second: number;
  value: string;
  result: string;
}

class Example extends React.Component<{}, IState> {
  ...
  // null로 초기화한 경우에는 타입도 null을 넣어 일치시켜줘야 한다.
  inputEl: HTMLInputElement | null = null;

  inputRef = (c: HTMLInputElement) => {
    this.inputEl = c;
  };
  ...

  render() {
    return (
      ...
      <input
        ...
        ref={this.inputRef}
        ...
      />
      ...
    )
  }
}
```

## <ins><b>React 공식사이트에서는 hooks를 사용할 것을 권장한다.</b></ins>

이전 코드를 해석하기 위해서 class 컴포넌트로 작성하는 법도 알아야 되지만, 실제로 hooks로 적었을때 코드가 간결해지고 리액트 공식사이트에서도 hooks의 사용을 권장한다.

## <ins><b>useState의 타입추론</b></ins>

만약에 useState에서 타입추론을 할 수 없는 경우, useRef에서와 같이 useState\<T>()과 같은 형태로 타입을 명시해줄 수 있다.

## <ins><b>useCallback typing</b></ins>

함수 전체가 실행될때 불필요하게 함수내에 선언한 함수가 다시 생성되는 것을 방지하기 위해 함수를 useCallback으로 감싸준다. 이렇게 다른 함수로 감싸주게 되면, 타입추론이 방해된다.
따라서 useCallback 자체에 함수 typing을 해주거나 콜백함수의 event 객체에 직접 typing을 해줌으로써 확실한 타입 추론이 가능하도록 해준다.

```javascript
// event 객체에 대한 typing을 해준다.
const onSubmitForm = useCallback((e: React.FormEvent<HTMLFormElement>) => {
  ...
}, [value])

// 제네릭에 함수 자체를 typing해준다.
const onChange = useCallback<(e: React.ChangeEvent<HTMLInputElement>) => void>((e) => {
  ...
}, [])
```

## <ins><b>class component의 createRef typing</b></ins>

class component에서 useRef hooks와 비슷하게 사용할 수 있는 React.createRef가 있다.

```javascript
import { createRef } from 'react';

// Declaration in class component
onRefInput = createRef<HTMLInputElement>();

// Use ref object
const input = this.onRefInput.current;
input.focus();

// input element
<input  ref={this.onRefInput} />
```

## <ins><b>react-hot-loader 사용하기</b></ins>

react-hot-loader는 \*.d.ts를 제공한다.

- ## 설치
  ```zsh
  $ npm i react-hot-loader
  ```
- ## 사용

  ```javascript
  import * as React from 'react';
  import { hot } from 'react-hot-loader/root';
  import Example from './Example';

  const Hot = hot(Example); //HOC

  ReactDOM.render(<Hot />);
  ```
