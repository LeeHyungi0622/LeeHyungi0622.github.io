---
title: 210317 React TIL - React Hook - useRef 함수의 사용, 배열의 효율적인 렌더링, 배열에 새로운 항목 추가, 배열에 항목 제거, 배열에 항목 수정
date: 2021-02-17 10:14:00
tags:
  - React
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>React Hooks - useRef함수의 사용</b></ins>

- ### <b>useRef로 특정 DOM 선택</b>

  VanillaJS에서는 getElementBy나 querySelector를 사용해서 특정 DOM 객체를 조작한다. React에서도 `특정 DOM 객체를 조작`해야되는 경우가 있는데, `element의 크기나 위치, Scroller bar의 위치, focus 설정`하는 경우 등이 있다. 추가적으로 video.js, JWS HTML5 video관련 라이브러리의 사용이나 D3, chart.js와 같은 그래프 관련 라이브러리를 사용할때에도 적용할 특정 DOM을 선택해서 적용시켜야 하는 상황이 생긴다.
  React에서는 React Hook의 useRef라는 함수를 사용해서 변수를 생성해주고, 조작하고자 하는 특정 태그의 ref 속성값으로 생성한 useRef 변수를 넣어서 생성한 useRef 식별자를 통해 특정 DOM 객체에 접근한다.
  그외에 <ins><b>class형 컴포넌트에서는 `React.createRef()`</b></ins>를 사용해서 특정 DOM 객체를 선택하고, callback 함수를 사용해서도 가능하다.

  <b>input 입력 태그의 값을 초기화 시켜주고, cursor의 focus를 특정 input입력태그로 이동을 시킨다.</b>

  <!-- more -->

  ```javascript
  const nameInput = useRef();

  const onReset = () => {
      setInputs({
          name: '',
          nickname: ''
      })
      nameInput.current.focus();
  };

  ...
  <input
      name="name"
      placeholder="이름"
      onChange={onChange}
      value={name}
      ref={nameInput}
  />
  ...
  ```

  <br/>

- ### <b>useRef로 컴포넌트 내의 변수 생성</b>

  만약에 함수 컴포넌트 내에서 let을 사용해서 변수를 선언하게 되면 렌더링 될때마다 값이 초기화된다.
  그렇다면 변수의 값 상태를 유지하면서 관리하려면 어떻게 해야할까? state값으로써 관리를 할 수도 있겠지만, state 값의 경우에는 값에 변화가 생기는 경우, 화면이 다시 렌더링되는 결과를 낳기 때문에 효율적인 변수 관리 방법이 아니다.
  만약에 화면의 렌더링에는 영향을 주지 않고, 독립적으로 변수의 값도 유지를 하면서 관리하기 위해서는 `useRef()함수`를 사용해서 변수를 관리해줘야 한다.

  그렇다면 어떤 경우에 useRef()를 사용해서 변수를 생성해서 관리를 해주게 될까?
  예를들어, setTimeout, setInterval의 id, 외부라이브러리를 사용해서 생성된 인스턴스, Scroll 위치를 알고 있어야 할때 등의 상황에서 useRef()를 사용하여 특정 변수의 값을 관리한다.

  ```javascript
  import React, { useRef } from 'react';
  import Counter from './Counter';
  import InputSample from './InputSample';
  import UserList from './UserList';

  function App() {
    const users = [
      {
        id: 1,
        username: 'a',
        email: 'a@gmail.com'
      },
      {
        id: 2,
        username: 'b',
        email: 'b@gmail.com'
      },
      {
        id: 3,
        username: 'c',
        email: 'c@gmail.com'
      },
      {
        id: 4,
        username: 'd',
        email: 'd@gmail.com'
      }
    ];

    const nextId = useRef(4);

    const onCreate = () => {
      console.log(nextId.current);
      nextId.current += 1;
    };

    return <UserList users={users} />;
  }

  export default App;
  ```

## <ins><b>배열의 효율적인 렌더링</b></ins>

배열을 사용해서 컴포넌트를 렌더링할때에는 map 함수를 사용한다. 렌더링되는 자식 컴포넌트에는 `key`속성을 넣어줘야 효율적으로 화면을 Re-rendering해줄 수 있다.

컴포넌트의 key라는 속성의 역할은 각 원소들마다 고유값을 할당함으로써 re-rendering성능을 최적화해줄 수 있다. key값은 unique한 값으로 넣어줘야 한다. (아래의 예시에서는 객체의 id값)
만약에 특정할 고유값이 존재하지 않는다면, 배열 객체를 map으로 처리할때 두 번째 인자인 index를 받아서 key값으로 설정해줘도 된다.

만약 데이터가 10, 20개 정도의 소량의 데이터이면서 자주 업데이트되지 않는 데이터라면, 퍼포먼스상 문제가 되지 않지만 대량의 배열 데이터를 표시하면서 데이터가 자주 업데이트된다면, 이 경우에는 기존의 배열 데이터에 데이터를 추가, 삭제시에 퍼포먼스상(Re-rendering) 문제가 된다.
**따라서 되도록이면 index값을 자식 컴포넌트의 key값으로 넣어주지 않도록 해야한다.**

컴포넌트가 key값을 가지고 있어야만 특정 컴포넌트가 리스트의 어떤 데이터를 가르키고 있는지 알 수 있다.

```javascript
import React from 'react';

function User({ user }) {
  const { username, email } = user;
  return (
    <div>
      <b>{username}</b> <span>{email}</span>
    </div>
  );
}

function UserList() {
  const users = [
    {
      id: 1,
      username: 'a',
      email: 'a@gmail.com'
    },
    {
      id: 2,
      username: 'b',
      email: 'b@gmail.com'
    },
    {
      id: 3,
      username: 'c',
      email: 'c@gmail.com'
    },
    {
      id: 4,
      username: 'd',
      email: 'd@gmail.com'
    }
  ];

  return (
    <div>
      {users.map((user, index) => (
        <User user={user} key={user.id} />
      ))}
    </div>
  );
}

export default UserList;
```

## <ins><b>배열에 항목 추가</b></ins>

기존의 배열(원본)에는 데이터를 추가하지 않고, 기존의 배열(원본)을 복사해서 변화된 데이터 정보를 반영한 또 다른 배열 데이터를 생성하도록 한다.
push/splice/sort 함수는 원본 데이터에 영향을 주기 때문에 되도록 사용하지 않도록 한다.

- ### 배열의 불변성을 지키면서 기존의 배열에 데이터 추가하는 방법
  앞서 언급한대로 push/splice/sort 함수를 사용해서 변수를 수정할 경우, 원본 데이터 값이 수정이 된다. 배열의 불변성을 지키면서 기존 배열 데이터에 새로운 데이터를 추가하기 위해서는 아래 두 가지 방법으로 하기를 권장된다.
  **첫 번째, spread 연산자를 사용해서 원본 데이터를 복사해서 사용한다.**
  spread 연산자를 사용해서 기존의 배열 데이터를 복사하고, 새롭게 추가하는 배열 데이터의 정보를 추가해주면 기존 원본 데이터에 변화를 주지 않는다.
  **두 번째, concat() 함수를 사용해서 원본 배열 데이터에 데이터를 추가한다.**
  인수로는 배열이 아닌 값을 넣어줘도 기존의 원본 배열 데이터에 값이 추가된다.

## <ins><b>배열에 항목 제거</b></ins>

filter() 함수를 사용하여 특정 조건에 맞는 배열의 요소만으로 새로운 배열을 생성한다.

```javascript
const taskNotDone = todos.filter((todo) => todo.done === false);
// todos 리스트의 객체 값 중에서 done 속성이 false인 것만 따로 추출해서 새로운 배열로 만든다.
console.log(taskNotDone);
```

- ### onClick 속성으로 화살표 함수로 함수 정의해서 넣어주기
  만약에 버튼의 onClick 속성값으로 함수를 넣어서 처리할 때에는 원형 화살표 함수로 함수를 만들어서 넣어줘야 한다.

## <ins><b>배열에 항목 수정</b></ins>

배열 안에서 특정 항목을 수정할 때에는 map 함수를 사용한다.
객체 리스트가 있다고 가정했을때 리스트 내에 있는 객체 하나 하나를 순회하면서 할당된 id의 값이 일치하는 객체의 특정 속성값을 변경해줄때에는 아래와 같이 map 함수로 리스트를 순회하면서 조건처리를 해준다.

```javascript
const onToggle = (id) => {
  setUsers(
    // 원본 user의 값을 복제하고 해당 user의 속성 중에 active의 값을 기존의 값을 반전시킨 값으로 업데이트 한다.
    users.map((user) =>
      user.id === id ? { ...user, active: !user.active } : user
    )
  );
};
```

## <ins><b>useEffect를 사용하여 mount/unmount시 작업설정</b></ins>

## <ins><b>useMemo를 사용하여 연산했던 값 재사용</b></ins>

## <ins><b>useCallback를 사용하여 함수 재사용</b></ins>

## <ins><b>React.memo를 사용한 컴포넌트 리렌더링 방지</b></ins>

## <ins><b>useReducer</b></ins>
