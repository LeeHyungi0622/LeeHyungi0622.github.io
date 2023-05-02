---
title: 210411 React TIL - 상태의 불변성, React DevTools, 배포모드, 성능에 대한 이야기, React.createRef, props와 state 연결하기
date: 2021-04-11 14:22:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>상태의 불변성</b></ins>

push를 이용해서 컴포넌트의 상태를 직접 바꿔주게 되면, React가 구체적으로 무엇이 바뀌었는지 감지하지 못한다. 따라서 원본은 그대로 두고, 바뀌는 상태 데이터는 원본을 복사해서(얕은 복사) 상태를 업데이트 해주도록 한다.

`React의 Rendering 기준`은 이전 상태값과 현재 상태값이 다른 경우 Re-rendering을 해준다.

- arr1 === arr2 // `false` (Re-rendering) - 이전 상태와 현재 상태가 같지 않은 경우만 렌더링
- arr1 === arr2 // `true`

## <ins><b>내부에서 this를 사용하지 않는 함수의 경우에는 외부로 빼주도록 한다.</b></ins>

## <ins><b>setState에서의 함수형 업데이트</b></ins>

상태를 업데이트 해주는 setState 함수는 비동기 방식이기 때문에 setState함수를 여러번 연달아 사용하는 경우 문제가 생길 여지가 있다. 따라서 setState 내부에서 또 다른 상태값을 참조해서 상태를 업데이트 시키는 경우에는 `함수형으로 이전 상태값을 받아서 새로운 상태로 업데이트`시켜줘야 한다.
(class component, functional component(hooks) 둘 다 )

```javascript
const [tries, setTries] = useState([]);

setTries((tries) => [...tries, { try: value, result: 'good!' }]);
```

  <!-- more -->

클래스 컴포넌트의 경우에는 states 전체를 이전상태로 받아오기 때문에 states.tries로 참조하고자 하는 특정 상태값을 참조하면 된다.

## <ins><b>React DevTools</b></ins>

React 개발 툴(React, Redux)을 사용해서 화면의 각 컴포넌트가 어느 시점에서 렌더링이 되고 있는지 확인할 수 있다.

## <ins><b>배포 모드로 바꿔주기</b></ins>

개발모드에서 배포 모드로 바꿔주기 위해서는 웹 팩 설정파일에서 두 가지 부분을 수정해주면 된다.
우선 첫 번째로 `process.env.NODE_ENV = "production"`를 작성해준다.
두 번째로 기존의 mode: 'development'를 `mode: production`으로 업데이트해준다.

## <ins><b>Redux를 사용할때에는 툴로 Redux의 구조가 노출되지 않게 처리하기</b></ins>

Redux를 사용하는 경우에는 웹 어플리케이션에서 사용중인 데이터의 구조가 노출되지 않도록 처리를 해줘야 한다.
포트폴리오 전용으로 React 프로젝트를 할때에는 현재 실제 서비스를 하고 있는 웹 어플리케이션을 분석해서 어떤 폴더구조로 작성을 했는지, 확인이 가능하다면 데이터 구조는 어떤 식으로 구성을 했는지 참고를 해서 만들어보도록 한다.

## <ins><b>성능에 대한 이야기</b></ins>

React의 class component에서는 state와 props가 바뀌었을때 그리고 setState가 호출되었을때 렌더링이 발생한다. 불필요한 컴포넌트의 렌더링을 제거하기 위해서는 render와 같이 기본적으로 제공되는 `shouldComponentUpdate()`함수의 내부에 렌더링되는 조건을 작성해서 렌더링하는 경우와 안하는 경우를 작성해줘야 한다.

```javascript
shouldComponentUpdate(nextProps, nextState, nextContext){
  // 이전 state의 value값이 다음 state의 value값과 일치하지 않는 경우에 렌더링을 한다.
  if(this.state.value !== nextState.value){
    return true;
  }
  return false;
}
```

앞서 설명한 shouldComponentUpdate()가 복잡하다면, `PureComponent`를 사용하도록 한다.
사용법은 class 작성시 기존에 상속받았던 Component 대신에 PureComponent를 상속받아서 구현을 한다. 그럼 PureComponent가 알아서 클래스 내부의 shouldComponentUpdate를 알아서 구현해준다.
단, PureComponent를 사용하는 경우의 단점은 배열이나 객체와 같이 참조 관계가 있는 구조의 경우에는 PureComponent가 판단하기 힘들다. 배열A []와 배열B[]는 서로 메모리의 다른 곳을 참조하고 있기 때문에 다른 값으로 간주된다. 따라서 배열A에 배열B를 업데이트 하여도 다른 값으로 알고 해당 컴포넌트를 렌더링하게 된다.
state에는 되도록 객체구조를 사용하지 않도록 하고, 배열 내부에 객체를 넣고 그 객체 안에 배열을 넣는 복잡한 구조를 사용하지 않도록 한다. (간단한 구조로 state를 관리)
그리고 `객체를 상태값으로 사용하는 경우에는 값을 업데이트할때 불변성을 지켜주도록 해야한다.`
좀 더 디테일한 조건부로 렌더링을 하고자 한다면 기존의 React.Component를 상속받아서 `shouldComponentUpdate()`에서 별도로 렌더링 조건을 작성해주도록 한다.

그렇다면 함수형 컴포넌트(functional component)의 경우에는 어떻게 처리해야 할까? 바로 `React.memo`를 사용하도록 한다.
사용방법은 작성해준 함수형 컴포넌트의 몸체(인자와 반환부)를 그룹 연산자를 사용해서 React.memo로 감싸주면 된다. (구조 분해 문법을 사용해서 간단하게 memo로 작성해줄 수 있다)
React.memo로 함수형 컴포넌트의 몸체를 감싸주면, props나 state가 바뀌었을때에만 렌더링을 해준다.

자식 컴포넌트들을 모두 PureComponent나 React.memo로 처리를 해주었다면, 부모 컴포넌트도 PureComponent나 memo로 처리하도록 한다.

성능최적화를 고려하여 Component 대신에 PureComponent를 사용하도록 한다.

## <ins><b>React.createRef의 사용</b></ins>

이전에 React에서 특정 DOM Element를 선택하는 경우에 ref라는 속성을 사용하는 것에 대해서 배웠다. 사용법은 class component와 functional component, 두 가지 경우에 따라 달랐는데 사용법은 아래와 같다.
`class component`

```javascript
inputRef;

onInputRef = (c) => {
  this.inputRef = c;
};

<input ref={this.onInputRef} />;
```

`functional component`

```javascript
import React, { useState, useRef } from 'react';

inputEl.current.focus();

const inputEl = useRef(null);

<input ref={inputEl} />;
```

class component와 functional component 두 경우에는 약간의 사용 방식에 차이가 있음을 알 수 있다.
하지만 class component에서 `React.createRef`를 사용하게 되면 hooks와 같이 처리를 해줄 수 있다.

`class component - React.createRef 사용`

```javascript
import React, { Component, createRef } from 'react';

this.inputRef.current.focus();

inputRef = createRef();

<input ref={ this.inputRef }>
```

<b><ins>위의 React.createRef를 사용하게 되면, React Hooks의 useRef()와 통일성이 있는 코드를 작성할 수 있다.</ins></b>

위의 React.createRef를 사용하게 되면 좀 더 간단하게 DOM element의 선택이 가능하다. 하지만 좀 더 미세한 처리를 하고자 하는 경우에는 기존의 방식으로 함수 내에 미세한 처리에 대해 작성을 해줄 수 있다.
같은 맥락으로 초기 state에 대한 작성에 있어, 생성자(constructor)내에 작성을 해줘도 되고 안해줘도 되는데 만약 초기 생성자 내에 작성해주는 상태값을 한 번 가공해서 넣어줘야 한다면, constructor 블럭 안에서 처리를 해서 초기화를 시켜 줄 수 있다.

## <ins><b>props와 state 연결하기</b></ins>

부모 컴포넌트로부터 넘겨받은 props의 경우 자식 컴포넌트에서 수정할 수 없다. 만약 자식 컴포넌트에서 props를 수정해야되는 경우에는 자식 컴포넌트의 state에서 넘겨받은 props의 값을 상태의 초기값으로 받아 값을 수정해서 사용해야 한다.
`class component`

```javascript
state = {
  result: this.props.result,
  value: this.props.value
};
```

`functional component`

```javascript
const Sample = ({ info }) => {
  const [result, setResult] = useState(info.result);

  return (
    <li>
      <div>{result}</div>
    </li>
  );
};
```

## <ins><b>컴포넌트 간의 props 전달 - Context API</b></ins>

컴포넌트 간에 props를 전달하는 경우에 있어 컴포넌트간에 깊이가 깊어져서 불필요한 props의 전달이 발생하는 경우, Context API를 사용해서 불필요한 props의 전달 없이 props를 전달할 수 있다.
Context가 props의 진화형으로 이해하면 된다.
