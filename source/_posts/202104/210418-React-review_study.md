---
title: 210514 React TIL - ContextAPI의 사용, Custom Hook(useContext + React.createContext), Strict mode, action 객체와 reducer 함수의 작성, React에서 상태관리의 위치
date: 2021-05-14 11:18:00
tags:
  - React
categories:
  - ReactJS
---

`20210418 Update - Custom hooks - context.js`
`20210514 Update - Custom hooks(input)`

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

`실습 Repository :` [https://github.com/LeeHyungi0622/react-basic-projects/blob/master/sidebar-modal/src/context.js](https://github.com/LeeHyungi0622/react-basic-projects/blob/master/sidebar-modal/src/context.js)

## ContextAPI의 사용 & Custom Hook(useContext + React.createContext)

여지까지 ContextAPI를 모든 하위 컴포넌트들의 상위 컴포넌트에서 정의해서 사용했었는데, 이 ContextAPI부분을 별도의 파일로 정의를 해서 사용할 수 있다.
이렇게 별도의 context.js파일로 작성해서 관리를 하면 코드를 좀 더 가독성 좋게 만들 수 있는 것 같다.

그리고 ContextAPI에서 전달하는 값을 사용하는 하위 컴포넌트들에서는 매번 useContext를 import해서 인자로 React.createContext() 객체를 인자로 넣어서 값을 호출해서 사용하였는데 이 부분은 별도의 custom Hook으로 정의해서 사용을 할 수도 있다.

(`custom Hook을 만들어서 사용할때에는 함수의 이름에 접두사 use를 꼭 붙여줘야 한다.`)

`context.js`

```javascript
import React, { useState, useContext } from 'react';

const AppContext = React.createContext();

const AppProvider = ({ children }) => {
  // modal, sidebar state
  const [isSidebarOpen, setIsSidebarOpen] = useState(false);
  const [isModalOpen, setIsModalOpen] = useState(false);

  const openSidebar = () => {
    setIsSidebarOpen(true);
  };

  const closeSidebar = () => {
    setIsSidebarOpen(false);
  };

  const openModal = () => {
    setIsModalOpen(true);
  };

  const closeModal = () => {
    setIsModalOpen(false);
  };

  return (
    <AppContext.Provider
      value={{
        isModalOpen,
        isSidebarOpen,
        openModal,
        openSidebar,
        closeModal,
        closeSidebar
      }}
    >
      {children}
    </AppContext.Provider>
  );
};

// Custom Hook(useContext + AppContext)
export const useGlobalContext = () => {
  return useContext(AppContext);
};

// index.js에서 최상위 App.js를 감싸주기 위해 AppProvider를 export해준다. (index.js)
export { AppProvider };
```

  <!-- more -->

## Strict 모드

StrictMode는 프로그램에서의 잠재적인 문제점을 밝혀내기 위한 도구이다. Fragment와 같이 StrictMode는 가시적으로 UI에 렌더링되지 않는다. StrictMode는 하위 요소에 대해서 추가적인 검사 및 경고를 활성화한다. (`개발모드에서만 실행, Production build에는 영향을 주지 않는다.`)

StrictMode를 사용하게 되면 아래의 항목들을 검사할 수 있다.

- 안전하지 않은 생명주기가 있는 구성 요소 식별 (레거시 라이프 사이클 메서드 - `componentWillMount, componentWillReceiveProps, componentWillUpate`)
- 레거시 문자열 참조 API 사용에 대한 경고
  React에서는 refs를 관리하기 위한 두 가지 방법을 제공했다. (legacy string ref API, callback API) ref API는 콜백 API보다 상대적으로 편리하지만 몇가지 문제점이 있기 때문에 공식적으로 콜백 API 사용을 권장하고 있다.

  `React 16.3` 버전부터 문제점이 개선된 ref API를 제공한다.

  ```javascript
  class MyComponent extends React.Component {
    constructor(props) {
      super(props);

      this.inputRef = React.createRef();
    }

    render() {
      return <input type="text" ref={this.inputRef} />;
    }

    componentDidMount() {
      this.inputRef.current.focus();
    }
  }
  ```

- 예기치 않은 부작용을 감지
- 레거시 컨텍스트 API 감지

## onContextMenu 속성 (마우스 오른쪽 클릭 이벤트 속성)

보통은 마우스 오른쪽 클릭 이벤트가 사용되지 않지만 게임을 만들때 종종 사용된다.

## action 객체와 reducer 함수의 작성

Action은 추상적으로 우선 작성하고 구체적인 구현은 reducer 함수에서 구현을 해준다.

## React에서 상태관리는 controller의 위치를 기준으로 한다

React에서 상태관리는 controller의 위치를 기준으로 한다. (`전역적으로 관리되는 상태의 경우에는 ContextAPI가 위치한 곳에서 정의`) 예를들어 Home 컴포넌트와 Modal 컴포넌트가 있는데, modal 창이 열려있는지, 닫혀있는지에 대한 상태관리는 modal 창을 열고 닫게 하는 버튼이 위치한 Home 컴포넌트에서 관리를 한다.

`20210514 Update`

## 반복되는 input 부분을 custom hook으로 공통화 처리

`useInput.js`

```javascript
import { useState, useCallback } from 'react';

export default (initialValue = null) => {
  const [value, setter] = useState(initialValue);
  const handler = useCallback((e) => {
    setter(e.target.value);
  }, []);
  return [value, handler];
};
```
