---
title: 210213 React TIL - React가 만들어진 배경, 컴포넌트, Yarn, JSX의 규칙, Props, 조건부 렌더링, useState를 통한 동적 상태 관리, input 상태관리(단일 입력, 복수 입력)
date: 2021-02-13 08:50:00
tags:
  - React
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>클래스 컴포넌트에서 Hooks + 함수형 컴포넌트</b></ins>

## <ins><b>리액트가 만들어진 배경</b></ins>

- ### JavaScript를 사용한 DOM 조작

  만약에 어플리케이션의 규모가 커지게 되면, 상태와 이벤트 관리를 위한 코드가 복잡해진다. 따라서 Ember, Backbone, AngularJS라는 프레임워크가 등장을 해서, 자바 스크립트의 특정 값이 바뀌면 특정 DOM의 속성도 바뀌도록 규칙을 가지고 설계되었다.
  하지만 React의 경우에는 특정 값의 상태가 바뀌었을때 기존의 View를 날려버리고 다시 화면에 보여지는 View를 생성하는 형태로 동작하도록 구상되어 만들어졌다. 이런 방식으로 동작을 하게 되면, 특정 규칙에 의해 DOM속성을 조작하지 않아도 되기 때문에 개발면에서는 편하지만, 작은 어플리케이션의 경우에는 괜찮지만, 어느정도 규모가 있는 어플리케이션이라면 동적인 화면 표현에 있어, 성능상 문제가 된다.
  하지만 React는 Virtual DOM이라는 것을 사용했기 때문에 성능을 지켜가면서 가능하도록 설계되었다.
  **Virtual DOM이란** 말 그대로 가상 돔으로 브라우저에서 실제로 보여지는 DOM이 아니라 그냥 메모리에 가상으로 존재하는 DOM으로써 JavaScript 객체이기 때문에 작동 성능이 실제 브라우저에서 DOM을 보여주는 것보다 속도가 훨씬 빠르다.

  React에서는 상태가 업데이트되면, 업데이트가 필요한 UI를 메모리에 있는 Virtual DOM에 렌더링을 하게 된다.
  그 다음으로 React 팀이 개발한 비교 알고리즘을 통해서 실제 브라우저 상에서 보여지는 DOM과 메모리에 존재하는 Virtual DOM을 비교하고 차이점을 감지하고 나서 실제 DOM에 이를 패치(필요한 변화만 반영)를 해주게 된다.

![](/images/post_images/210316_virtual_dom_img.png)

  <!-- more -->

<br/>

## <ins><b>UI를 어떻게 보여줄지에 대해 집중</b></ins>

## <ins><b>컴포넌트 = UI 조각</b></ins>

## <ins><b>NodeJS</b></ins>

자바스크립트를 브라우저 환경이 아닌 곳에서 사용할 수 있도록 하는 자바스크립트 런타임이다. (webpack, babel과 같은 도구들은 이 NodeJS를 기반으로 만들어졌다)

## <ins><b>YARN</b></ins>

자바스크립트 패키지를 관리하기 위한 도구이다. yarn이 npm보다 패키지 설치시에 빠르다.

## <ins><b>JSX의 기본 규칙</b></ins>

JSX는 React에서 컴포넌트의 생김새를 나타낼때 사용된다.
JSX는 자바스크립트이다. 얼핏보면 HTML 태그처럼 보이지만, JSX 코드는 Babel을 통해서 자바스크립트 코드로 변환된다.

매번 `React.createElement`를 사용해서 화면에 보여질 컴포넌트를 만들 수 없기 때문에 JSX라는 문법을 사용해서 XML형태로 컴포넌트를 작성해주면, Babel이 JavaScript로 변환을 해준다.

`JSX 문법으로 XML형태로 컴포넌트 작성`

```jsx
<div>
  <b>Hello,</b> <span>React</span>
</div>
```

`Babel이 JSX 문법으로 작성한 컴포넌트를 JavaScript로 변환`

```javascript
'use strict';
/*#__PURE__*/
React.createElement(
  'div',
  null,
  /*#__PURE__*/
  React.createElement('b', null, 'Hello,'),
  ' ' /*#__PURE__*/,
  React.createElement('span', null, 'React')
);
```

  <br/>

- ### 태그는 반드시 닫혀있어야 한다.(opening tag - closing tag/self closing tag)

  <br/>

- ### 두 개이상의 태그는 반드시 하나의 태그로 감싸야 한다.

  불필요한 div태그를 사용하지 않고, <></> (fragment)를 사용해서 감싸줄 수 있다. fragment 태그는 실제 화면에 출력된 DOM에서 확인되지 않는 태그이다. 그리고 괄호(())로 감싸는 것은 가독성을 위한 처리이다.

  <br/>

- ### JSX 내부에서 자바스크립트 값을 표현하기

  중괄호({})로 감싸서 자바스크립트 값을 표현할 수 있다.

  <br/>

- ### 스타일과 클래스 이름

  - #### 스타일

    기존에 css 스타일 속성에서 -(dash)로 이름이 표현되어있는 것은 camelCase로 속성의 이름을 바꿔서 지정해주면 된다.

  - #### 클래스 이름

    JSX에서는 class이름을 지정해줄때 class가 아닌 className의 속성으로 지정해준다.

  - #### 주석처리

    주석을 작성할때에는 주석처리한 내용을 중괄호({/\* \*/})로 감싸주도록 한다.

    태그의 내부에서는 single comment (//)로 주석처리를 해서 작성을 한다.

    ```javascript
    import React from 'react';
    import Hello from './Hello';

    function App() {
      const name = 'react';
      const style = {
        backgroundColor: 'black',
        color: 'aqua',
        fontSize: 24,
        padding: '1rem'
      };
      return (
        <>
          {/* Hello component */}
          <Hello />
          <div
            // div태그 내부에 스타일 지정
            style={style}
          >
            {name}
          </div>
          <div className="my-style"></div>
        </>
      );
    }

    export default App;
    ```

## <ins><b>Props</b></ins>

부모 컴포넌트에서 자식 컴포넌트로 특정 속성을 전달하고자 할때 사용된다.

부모 컴포넌트로부터 props를 전달받은 자식 컴포넌트에서는 `비구조 할당/구조분해`를 통해 아래와 같은 형태로 전달받은 props를 사용할 수 있다.

`App.js`

```javascript
import React from 'react';
import Hello from './Hello';

function App() {
  return <Hello name="react" color="red" />;
}

export default App;
```

`Hello.js`

```javascript
import React from 'react';

function Hello({ name, color }) {
  return <div style={{ color }}> 안녕하세요. {name}</div>;
}

export default Hello;
```

  <br/>
  
  - ### Props 값의 기본값(default value) 설정하기
    ```javascript
    Hello.defaultProps = {
      name: '이름없음'
    };
    ```

  <br/>
  
  - ### Props Children
    Wrapper component를 만들어서 다른 component들을 감싸줄때 내부에 포함된 component들을 보여주기 위해서 props의 children을 사용해줘야 한다.

    ```javascript
    import React from 'react';

    function Wrapper({children}) {
        const style = {
            border: '2px solid black',
            padding: 16
        };

        return <div style = { style }> {children} </div>
    }

    export default Wrapper;
    ```

## 조건부 렌더링

`삼항연산자`(내용이 달라지는 경우)와 `단축평가`(출력되거나 출력이 안되는 경우)를 이용한 조건부 렌더링하기

```javascript
function Hello({ name, color, isSpecial }) {
  return (
    <div style={{ color }}>
      {isSpecial ? '*' : ''}
      {isSpecial && <b>*</b>}
      안녕하세요. {name}
    </div>
  );
}
```

JSX에서 `null, false, undefine`을 렌더링해주게 되면, 아무것도 렌더링되지 않는다.(예외적으로 0은 출력된다)

만약에 props를 전달할때 아무런 값을 전달해주지 않으면 `true`를 전달하는 것과 같은 효과가 나타난다.

## useState를 통한 동적 상태 관리

사용자와의 interaction을 통해서 화면에 표시된 값이 변화해야 될 때 어떻게 처리해야 되는지에 대해서 알아보자.

이전 React 16.8 이전 버전에서는 함수형 컴포넌트 내에서 상태를 관리할 수 없었다. 그런데 이후에 hooks라는 개념이 도입이 되면서 함수형 컴포넌트에서 상태관리가 가능해졌다.

- ### 카운터 구현
  `Counter.js`

```javascript
import React, { useState } from 'react';

function Counter() {
  // 구조분해를 통해서 아래와 같이 선언을 할 수 있다.
  // 현재상태와 현재상태를 바꾸는 함수로 구성
  const [number, setNumber] = useState(0);

  const onIncrease = () => {
    setNumber(number + 1);
  };

  const onDecrease = () => {
    setNumber(number - 1);
  };

  return (
    <div>
      <h1>{number}</h1>
      <button onClick={onIncrease}>+1</button>
      <button onClick={onDecrease}>-1</button>
    </div>
  );
}

export default Counter;
```

    <br/>

    - #### useState에서 함수형 업데이트하기
      `리액트 컴포넌트를 최적화하는 과정에서 필요하다(성능 최적화)`
      위의 코드에서 onIncrease 증가버튼의 이벤트 함수에서 useState의 상태변화 함수를 사용할때 내부에 현재 상태값을 받아서 처리했지만, 이를 현재 상태값이 아닌 함수로 받아서 처리를 해줄 수 있다.
      (함수로 처리하면 구체적으로 해당 이벤트 함수가 실행되는 로직을 작성해줄 수 있다)

      ```javascript
      ...
      const onIncrease = () => {
        setNumber(prevNumber => prevNumber + 1);
      }
      ...
      ```

## React에서 input 상태 관리하기

입력 태그의 상태를 관리하기 위해서는 입력태그의 속성에 onChange, value의 속성값으로 input 태그에 입력된 값이 업데이트 되었을때 상태 업데이트 메서드로부터 현재 상태값을 업데이트 될 수 있도록 처리를 해야 한다.

    `InputSample.js`
    ```javascript
    import React, { useState } from 'react';

    function InputSample(){
        const [text, setText] = useState('');

        const onChange = e => {
            setText(e.target.value);
        }

        const onReset = () => {
            setText('');
        };

        return(
            <div>
                <input onChange={onChange} value={text}/>
                <button onClick={onReset}>초기화</button>
                <div>
                    <b>값: </b>
                    {text}
                </div>
            </div>
        )
    }

    export default InputSample;
    ```

## 여러개의 input 상태 관리하기

    각 각의 input태그 속성에 name 속성으로 값을 넣어준다. 태그에 입력된 값에 변화가 생겼을때 어느 입력태그에서 변화가 생겼는지 구분해주기 위해서 name태그를 넣어 구분을 지어준다.

      ```javascript
      import React, { useState } from 'react';

      function InputSample(){
          // 객체 형태의 상태로 관리하기
          const [inputs, setInputs] = useState({
              name: '',
              nickname: '',
          });
          // name, nickname을 사용할 수 있도록
          const {name, nickname} = inputs;

          const onChange = e => {
              const { name, value } = e.target;
              // 객체 상태를 업데이트 할때에는 객체를 복사하고 업데이트된 속성값을 덮어써줘야 한다.
              // 객체를 업데이트 (기존의 객체를 복사(스프레드 문법))
              // name 속성에 따른 value값을 덮어쓰기
              // 아래 코드를 생략하고 위와같이 name을 대괄호로 묶어서 표기한다.
              // name은 실제 값으로써 추가되지 않고, value를 추가하기 위한 키 값으로만 활용된다.
              setInputs({
                  ...inputs,
                  [name]: value
              });
              // nextInputs[name] = value

          }

          const onReset = () => {
              setInputs({
                  name: '',
                  nickname: ''
              })
          };

          return(
              <div>
                  <input
                      name="name"
                      placeholder="이름"
                      onChange={onChange}
                      value={name}
                  />
                  <input
                      name="nickname"
                      placeholder="닉네임"
                      onChange={onChange}
                      value={nickname}
                  />
                  <button onClick={onReset}>초기화</button>
                  <div>
                      <b>값: </b>
                      {name} {nickname}
                  </div>
              </div>
          )
      }

      export default InputSample;
      ```

    onChange 함수 부분만 아래와 같이 간소화시켜서 작성해줄 수 있다.

    ```javascript
    const onChange = e => {
        const { name, value } = e.target;

        // 객체를 업데이트 (기존의 객체를 복사(스프레드 문법))
        // name 속성에 따른 value값을 덮어쓰기
        // 아래 코드를 생략하고 위와같이 name을 대괄호로 묶어서 표기한다.
        // name은 실제 값으로써 추가되지 않고, value를 추가하기 위한 키 값으로만 활용된다.
        setInputs({
            ...inputs,
            [name]: value
        });
        // nextInputs[name] = value

    }
    ```
