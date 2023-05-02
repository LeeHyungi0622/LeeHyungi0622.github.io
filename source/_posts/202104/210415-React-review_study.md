---
title: 210415 React TIL - useReducer, setState의 비동기적 처리, useReducer에서 dispatch를 통한 state 업데이트(비동기 처리), 성능 최적화
date: 2021-04-15 08:49:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>useReducer</b></ins>

React에서 Redux의 핵심 기능을 useReducer로써 구현하였다. useReducer + context API의 조합으로 Redux를 대체할 수 있을 것이라고 생각할 수 있다. 작은 앱의 경우에는 괜찮지만, 비동기 처리의 한계로 어플리케이션의 규모가 커지게 되면 Redux를 사용해서 구현하는 편이 좋다.
state의 갯수가 늘어나게 되면, useState를 통한 state관리 코드가 많아진다. 이러한 경우 `useReducer`를 사용해서 하나의 state와 setState로 통합해 줄 수 있다.

state 객체는 아무도 직접적인 수정이 불가하다. 다만 `dispatch를 통해 action object를 인수로써 호출`하게 되면 action object의 `type`에 정의된 action의 종류에 따라 reducder 함수에서 정의된 state 업데이트 방식에 의해 state가 업데이트된다.

```javascript
// useReducer의 첫 번째 인수로는 정의한 reducer 함수를 넣어주고, 두 번째 인수로는 state 초기 상태를 정의해준다.
const [state, dispatch] = useReducer(reducer, initialState);

// event 처리부분
dispatch({ type: 'SET_RESULT', result: 'ABC' });
```

`reducer 함수 예시`

```javascript
const reducer = (state, action) => {
  switch (action.type) {
    case 'SET_RESULT':
      return {
        ...state,
        result: action.result
      };
  }
};
```

<ins><b>나중을 위해 action의 이름은 개별 상수로 따로 빼서 관리</b></ins>하도록 한다.
추가적으로 관리해야 될 state가 있다면 action을 추가하고 해당 action에 대한 구체적인 state 업데이트 방식을 reducer 함수에 정의하도록 한다.

기존의 state를 업데이트할때 사용할 상태값은 reducer 함수의 두 번째 인자(action)로부터 넘겨준다.

(Redux는 Facebook의 Flux와 함수형 프로그래밍에 영감을 받아 2015년 6월경에 Dan Abramov에 의해 개발되었다)

  <!-- more -->

## <ins><b>React에서 setState의 비동기적 처리</b></ins>

React에서는 setState가 비동기적으로 동작하기 때문에 동일 함수내에서 state를 업데이트하고 state를 바로 확인해보면 값이 업데이트되기 이전의 state로 확인이 된다.
따라서 `class component`에서는 다음과 같이 callback function으로 해결을 할 수 있다.

```javascript
function changeValue(e) {
  this.setState({ value: e.target.value });
  // 이전 value값 출력
  console.log(this.state.value);
}
// solution
function changeValue(e) {
  this.setState({ value: e.target.value }, function () {
    console.log(this.state.value);
  });
}
```

그렇다면 `functional component`에서는 어떻게 해결할 수 있을까?
바로 Hooks의 `useEffect`를 사용하면 해결할 수 있다.

```javascript
const changeValue = (e) => {
  setValue(e.target.value);
  // 이전 value값 출력
  console.log(value);
};

// solution
const changeValue = (e) => {
  setValue(e.target.value);
};

useEffect(() => {
  console.log(value);
}, [value]);
```

value의 값이 변할때마다 useEffect내의 console.log()가 호출되기 때문에, changeValue 함수 내에서 setValue를 통해 값을 바꾸고 나서 value 값이 바뀐 것을 useEffect가 감지를 하고 console.log를 실행하기 때문에 제대로 바뀐 상태값 value를 console.log에 출력하게 된다.

## <ins><b>useReducer에서 dispatch를 통한 state 업데이트 - 비동기</b></ins>

useReducer에서 dispatch를 통해 state 값을 바꿀때 비동기적으로 바뀐다. 따라서 dispatch를 통해서 state를 업데이트 할 때에는 useEffect내에서 처리하도록 한다.
(참고로 Redux에서는 state가 동기적으로 바뀐지만, useReducer에서는 state가 비동기적으로 바뀐다.)

## <ins><b>성능 최적화</b></ins>

성능 최적화를 위해서는 어떤 요소가 불필요한 렌더링을 유발시키는지 확인을 해야 한다.
파악하는 방법은 이전에 넘겨받은 props(useRef를 통해 기억)와 현재 넘겨받은 props를 비교해서 서로 다르다면 해당 요소때문에 렌더링이 발생하는 것이다.

```javascript
const ref = useRef([]);
useEffect(() => {
  console.log(
    rowIndex === ref.current[0],
    cellIndex === ref.current[1],
    dispatch === ref.current[2],
    cellData === ref.current[3]
  );
  ref.current = [rowIndex, cellIndex, dispatch, cellData];
}, [rowIndex, cellIndex, dispatch, cellData]);
```

가장 손쉽게 해결할 수 있는 방법은 `React.memo`로 함수 전체를 감싸서 처리해주는 것이다. 만약 React.memo를 적용했는데도 성능개선이 되지 않는다면, jsx 구문에서 반환하는 자식 component를 useMemo로 감싸서 component 자체를 기억시키도록 할 수도 있다.

```javascript
return (
  <tr>
    {Array(rowData.length)
      .fill()
      .map((td, i) =>
        useMemo(
          () => (
            <Tr
              key={i}
              dispatch={dispatch}
              rowIndex={i}
              cellData={rowData[i]}
            />
          ),
          [rowData[i]]
        )
      )}
  </tr>
);
```

## <ins><b>ContextAPI</b></ins>

부모와 자식간의 관계가 깊어지면 props를 물려줄때 힘들기 때문에 contextAPI를 사용한다.
사용법은 부모 컴포넌트에서 React.createContext()를 초기값과 함께 초기화를 시켜주고, 부모 컴포넌트에서 전달하는 값들을 전달받아서 사용할 자식 컴포넌트 전체를 작성한 createContext의 Provider로 감싸준다. 감싸준 태그에는 value의 속성으로 contextAPI를 통해 전달할 값을 객체형태로 전달을 해주면 된다.

`contextAPI - ParentComponent`

```javascript
import React, { createContext, useReducer } from 'react';

// createContext의 초기값은 값의 형태만으로 정의해준다.
export const SampleContext = createContext({
  data: [],
  dispatch: () => {},
});

const initialState = {
  data: []
};

const reducer = (state, action) => {
  switch(action.Type){
    default:
      return state;
  }
};

// component내
....
const [ state, dispatch ] = useReducer(reducer, initialState);
return (
  <SampleContext.Provider value={{ data: state.state }}>
    <FirstChildContext />
    <SecondChildContext />
    <ThirdChildContext />
  </SampleContext.Provider>
)
....
```

위에서 작성한 코드와 같이 SampleContext.Provider의 value 속성으로 자식 컴포넌트에 넘겨 줄 객체 자체를 넣어주게 되면, 함수 컴포넌트가 재실행될 때 마다 value 속성으로 넣어준 객체가 재생성된다.
이 말은 ContextAPI를 통해 부모로부터 값을 넘겨받는 자식 컴포넌트들도 모두 새로운 객체를 넘겨받는 것으로 인식을 해서 다시 렌더링된다는 의미이다.
따라서 `value의 값에 객체 그대로를 넣어주지 말고 반드시 useMemo를 사용해서 값을 기억`하도록 작성해줘야 한다.
`dispatch는 절대로 바뀌지 않기 때문에 useMemo의 deps값으로 넣어주지 않아도 된다.`

`ContextAPI 성능 최적화`

```javascript
import React, { useMemo } from 'react';
....
const value = useMemo(() => ({ data: state.data, dispatch }), [state.data]);
return (
  <SampleContext.Provider value={ value }>
    <FirstChildContext />
    <SecondChildContext />
    <ThirdChildContext />
  </SampleContext.Provider>
)
```

`contextAPI - ChildComponent`

```javascript
import React,{ useContext } from 'react';
import { SampleContext } from './ParentComponent';
// component내
....
const { data, dispatch } = useContext(SampleContext);
....
```
