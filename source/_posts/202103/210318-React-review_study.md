---
title: 210318 React TIL - React hook / useEffect, useMemo, useCallback, React.memo, useReducer
date: 2021-03-18 05:32:00
tags:
  - React
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>useEffect를 사용하여 mount/unmount시 작업설정</b></ins>

useEffect를 사용하여 `생성한 컴포넌트가 처음 화면에 나타났을때`와 `화면에서 사라지게 될 때` 특정 작업을 수행하도록 만들 수 있다. 그리고 추가적으로 컴포넌트의 특정 props나 상태가 업데이트 될 때 마다 전/후에 특정 작업을 하게 만들 수도 있다.
우선 `컴포넌트가 mount될 때` 실행하는 callback 함수의 경우에는 useEffect의 첫 번째 인자로는 실행할 callback 함수를 넣어주고, 두 번째 인자로는 deps(dependency)로 의존되는 값을 넣어준다. deps에 빈 배열을 넣어주는 경우, 컴포넌트가 처음 화면에 나타날때만 해당 callback 함수가 실행된다.
`컴포넌트가 unmount될 때` 실행하는 callback함수는 useEffect의 callback함수 내부에서 반환문(return)으로 작성을 해준다.

`UserList.js`

```javascript
function User({ user, onRemove, active, onToggle }){
    const {username, email, id} = user;
    useEffect(() => {
        console.log('컴포넌트가 화면에 나타남');
        return () => {
            console.log('컴포넌트가 화면에서 사라짐');
        }
    },[]);

    ......
```

  <!-- more -->

그렇다면 구체적으로 컴포넌트가 mount될때 추가하는 처리에는 어떤 것들이 있으며, 컴포넌트가 unmount될때에 추가하는 처리에는 어떤 것들이 있는지 구체적으로 알아보자.
일반적으로 `mount의 경우`에는 컴포넌트의 특정 props로 받은 값을 컴포넌트의 state 값으로 설정, 외부 API요청(REST API), 라이브러리(D3, Video.js)를 사용할때의 처리, setInterval, setTimeout관련 처리를 한다. `useEffect()`에서 호출되는 시점에서는 UI가 화면에 나타난 이후이기 때문에 DOM 객체에 접근을 할 수 있다.
`unmount의 경우`에는 setInterval, setTimeout을 사용해서 등록했던 작업을 제거할때 (clearInterval, clearTimeout), 라이브러리 인스턴스 제거 작업 등 뒷정리하는 작업을 한다.

- ## deps

  만약에 빈 배열이 아닌 특정값을 넣어주게 되면 어떻게 될까? 바로 deps로 넣어준 값이 설정되거나 바뀔때마다 useEffect의 callback함수가 실행이 된다.(값이 업데이트된 이후에 콜백함수 실행)
  (빈 배열을 넣어서 처리를 해주는 경우에는 mount/unmount시에만 실행이 된다)

  값이 변경되었을 경우, useEffect() 내부의 cleaner 함수가 우선 실행이되고(변경 전) 값이 업데이트 된 후에는 useEffect의 첫번째 인자로 넣어준 콜백함수가 실행이 된다.
  (cleaner 함수의 경우에는 값이 업데이트되기 이전과 unmount되기 직전에 호출이 된다)
  `deps에 인자값을 넣어준 경우에는 빈 배열을 넣어줬을때와 같이 컴포넌트가 처음 화면에 나타날때도 실행이 된다.`

  만약에 useEffect내의 callback 함수 내부에서 컴포넌트의 props로 받아오는 값을 참조하거나 useState에서 관리하고 있는 값이 있는 경우 반드시 deps의 요소로 넣어줘야 한다.
  특별히 에러가 발생하거나 그런건 아니지만, useEffect내부에서 참조하고 있는 컴포넌트의 props나 useState의 값이 최신 상태로 업데이트되어 참조해야 되기 때문에 반드시 넣어서 처리해주도록 한다. ESLint를 사용하게 되면 deps에 값을 넣어주지 않는 경우, 별도의 경고를 확인할 수 있다.

  `그럼 deps의 인자로 아무것도 안넣어준다면` 화면이 Re-rendering될때마다 useEffect()내의 콜백함수가 매번 호출이 된다.
  그 이유는 리액트 컴포넌트에서는 부모 컴포넌트가 Re-rendering되면 자식 컴포넌트도 Re-rendering된다. 그렇다고 해서 실제 브라우저에서는 업데이트된 내용이 반영이 되지만, virtual DOM 상에서는 화면의 모든 컴포넌트를 렌더링하고 나서 비교를 해서 바뀐 부분만 적용을 하고 있다. 그런데 대부분의 경우 바뀌지 않은 내용에 대해서 vitual DOM상에서 렌더링을 한다고 해서 느려지거나 하지 않지만, 항목이 많다면 느려지게 될 가능성이 있다. 따라서 virtual DOM에서 렌더링되는 리소스 조차 아껴서 작업을 해줘야 한다.
  이러한 작업을 `컴포넌트 Re-rendering 최적화`라고 한다.

  `예시`로 블로그에서 포스팅 글을 볼때를 살펴보자. URL상 뒷쪽 값(URL Slug)을 props로 받아서 component가 mount될때 포스팅에 대한 정보를 읽어서 화면에 출력해주고, 다른 포스팅을 열 경우에는 주소가 바뀌게 되므로, 바뀐 URL상의 props 값을 이용해서 component의 useEffect()상에서 새로운 포스팅에 대한 정보를 읽어서 화면에 rendering 해주게 된다.

  여기서 deps 값으로는 URL의 주소가 될 수 있고, 이 주소가 업데이트 될때마다 useEffect()의 첫 번째 인자로 넣어준 callback함수를 실행하도록 처리를 해주면 된다. (업데이트 시에는 cleaner함수가 업데이트 직전에 실행이되고나서 useEffect의 첫 번째 인자로 넣어준 callback함수가 실행이 된다)

  ```javascript
  useEffect(() => {
    loadPost(username, urlSlug);
  }, [username, urlSlug]);
  ```

## <ins><b>useMemo를 사용하여 연산했던 값 재사용</b></ins>

이전에 연산된 값을 재사용, 즉 성능을 최적화할때 사용되는 hook 함수이다.
만약에 현재 화면에 표시된 사용자 정보중에 활성화 상태인 사용자 객체의 갯수를 확인하고자 한다면, 아래와 같이 filter 함수를 사용해서 사용자 중에서 활성화 상태인 사용자 객체의 갯수를 확인할 수 있다.

```javascript
function countActiveUsers(users){
  console.log('활성 사용자 수를 세는 중...');
  return users.filter(user => user.active).length;
}

function App(){
  ...
  const count = countActiveUsers(users);
  ...
}
```

하지만 이렇게 작성을 해주게 되면 컴포넌트가 Re-rendering될때마다 작성해준 countActiveUsers()가 호출이 된다. username, email 입력태그에서 값을 입력을 할때마다 onChange 함수가 실행이 되어 Re-rendering이 되고, 해당 함수가 매번 호출되고 있음을 확인할 수 있다.

이런 경우에 사용을 하는 것이 useMemo라는 hook 함수이다. 이 함수를 사용하면 `특정 값이 변경되었을때에만 함수가 호출될 수 있도록 처리할 수 있다.`

```javascript
const count = useMemo(() => countActiveUsers(users), [users]);
```

위와같이 useMemo() hook 함수로 감싸서 첫 번째 인자로 기존의 countActiveUsers() 함수를 화살표 함수의 형태로 넣어주고, 두 번째 인자로 deps, users 리스트 정보를 넣어준다. useEffect()에서와 같이 deps로 넣어준 값에 변화가 생기게 될때 마다 넣어준 callback 함수가 호출이 되어 실행되게 된다.

## <ins><b>useCallback를 사용하여 함수 재사용</b></ins>

useCallback 함수를 사용해서 기존에 만들었던 함수를 새로 만들지 않고 재사용할 수 있다.
useCallback 함수는 useMemo() 함수와 동일한 효과를 주는데 그 대상이 함수이다.
component가 Re-rendering될때마다 내부에서 만들어 준 함수들을 다시 만들어주고 있다. 이렇게 함수를 다시 만들어준다고 메모리/CPU상에서 많은 리소스를 차지하는 작업은 아니지만 한 번 만든 함수를 재사용하는 것이 좋다.
그 이유는 `컴포넌트의 Re-rendering 최적화 작업`을 위해서이다. 컴포넌트에 전달되는 props가 변경되지 않았다면 virtual DOM상에서 수행되는 Re-rendering을 안되게 끔 만들기 위해서는 우선적으로 선행되어야 하는 것이 각 컴포넌트 내부에서 생성한 함수들을 재사용 가능한 상태로 만들어줘야 하는 것이다.

예를들어 입력태그에서 속성으로 사용되는 onChange의 함수를 useCallback을 사용해서 처리해주기 위해서는 아래와 같이 기존의 함수 인자와 반환 부분 전체를 useCallback으로 감싸주고, 두 번째 인자로 deps를 넣어준다.

```javascript
const onChange = useCallback(
  (e) => {
    const { name, value } = e.target;
    setInputs({
      ...inputs,
      [name]: value
    });
  },
  [inputs]
);
```

위와같이 작성을 해주게 되면, inputs의 값이 바뀌었을때에만 작성해준 onChange 함수가 재생성이 되고, 그렇지 않은 경우에는 기존에 생성한 함수를 재사용하게 된다.

만약에 함수내부에서 컴포넌트의 props로 넘겨받은 또 다른 함수를 실행하는 경우에는 이 props로 넘겨받은 함수 또한 deps의 인수로써 작성을 해줘야 한다.

내부에서 사용되고 있는데 deps에 넣어주지 않는 경우에는 최신 상태 값을 보장해 줄 수 없기 때문에 반드시 신경써서 deps에 넣어줘야 한다.

이렇게 useCallback만을 사용해서 컴포넌트 내부의 함수의 재사용성을 가능하게 해줬다고 눈에 띄는 성능 최적화가 된 것은 아니다. 추가적으로 컴포넌트의 Re-rendering 최적화 작업을 해줘야 비로소 성능 최적화가 되었다고 할 수 있다.

- ### React Developer Tools

  React Dev Tools를 사용해서 현재 rendering되고 있는 컴포넌트를 Smart하게 확인할 수 있다.

  Chrome Inspector에서 설치한 React Dev Tools를 사용하면 현재 작업중에 rendering되고 있는 component를 가시적으로 Highlight해서 확인할 수 있다.
  (Chrome Inspector → Components → 톱니바퀴(Setting) → Highlight updates when components render 체크)

  ![](/images/post_images/210318_react_component_re-rendering.png)

  위에 첨부한 캡쳐를 보면 입력태그에 텍스트를 입력하게 되면 아래 사용자 리스트 값의 변화가 없는데도 Re-rendering되고 있음을 확인할 수 있다. 그럼 이런 경우에는 어떻게 컴포넌트를 관리해야 할까? 바로 아래에 정리한 React.memo를 사용해서 컴포넌트의 Re-rendering을 방지하면 된다. 좀 더 자세한 내용은 아래의 내용을 참고하자.

## <ins><b>React.memo를 사용한 컴포넌트 리렌더링 방지</b></ins>

`컴포넌트의 Re-rendering 최적화`
React.memo를 사용해서 불필요한 컴포넌트의 리렌더링을 방지할 수 있다.
사용법은 아래와 같이 매우 간단하게 사용할 수 있다.

```javascript
export default React.memo(CreateUser);
```

- ### React.memo([Component], [prevProps와 nextProps를 비교])

  만약에 두번째 인자의 prevProps와 nextProps를 비교 연산자로 비교했을때 True값이 반환되면 리렌더링 방지를 해주고, False값이 반환되면 리렌더링을 하도록 처리해준다.

  ```javascript
  function UserList({ users, onRemove, onToggle }) {
    return (
      <div>
        {users.map((user) => (
          <User
            user={user}
            key={user.id}
            onRemove={onRemove}
            active={user.active}
            onToggle={onToggle}
          />
        ))}
      </div>
    );
  }

  export default React.memo(
    UserList,
    (prevProps, nextProps) => prevProps.users === nextProps.users
  );
  ```

  위의 UserList 컴포넌트를 살펴보면 props로 받는 onRemove()와 onToggle() 두 함수는 초기에 함수를 생성한 뒤에 재사용을 할 것이기 때문에 users 값만 전/후 값을 비교해서 리렌더링할 것인지 결정해주면 된다. 따라서 export해주는 구문에서 React.memo의 두 번째 인자로 해당 조건 콜백함수를 넣어서 처리를 해주면 된다.

컴포넌트를 export해주는 구문에서 컴포넌트 이름을 `React.memo()`로 감싸주면 된다. 이렇게 해주게 되면 해당 컴포넌트에 전달되는 Props의 값이 바뀌지 않으면 렌더링하지 않는다.
함수 선언문으로 선언된 함수의 경우에는 화살표 함수를 사용하여 기존의 함수 선언문을 표현식으로써 작성을 한다음에 표현식 전체를 `React.memo()`로 감싸준다.
`(수정전) 코드`

```javascript
const onRemove = useCallback(
  (id) => {
    setUsers(users.filter((user) => user.id !== id));
  },
  [users]
);

const onToggle = useCallback(
  (id) => {
    setUsers(
      users.map((user) =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  },
  [users]
);
```

하지만 입력태그에 값을 입력할때에는 해당 입력 태그만 리렌더링되지만, 실제 새로운 항목이 리스트에 추가되거나 일부 항목이 업데이트 되면, 형제 레벨에 있는 다른 자식 컴포넌트들이 모두 같이 리렌더링 되는 것을 확인할 수 있다.
따라서 추가적인 작업이 필요하다.
우선 <ins><b>그 원인은 onToggle() 함수를 예로들었을때 deps로 users 정보를 참조하고 있기 때문</b></ins>이다.

그렇기 때문에 부모 컴포넌트에서 자식 컴포넌트로(App → UserList → User) onToggle() 함수를 props로 전달을 할때에 users의 값이 바뀌었기 때문에 전달되는 props도 변경되었다고 인식되어, 전달받은 모든 컴포넌트(React.memo()로 처리)들이 다시 Re-rendering되는 것이다.

해결방법은 `useState()의 함수형 업데이트`이다. 기존 useCallback() hook 함수를 사용할때에는 첫 번째 인자로 넣어 준 콜백함수의 내부에서 사용되는 props나 상태 정보를 최신상태로 만들기 위해서 deps에 요소를 넣어주었는데 현재 문제는 이 deps에 넣어주었기 때문에 문제가 발생하였다.
따라서 useCallback()의 첫번째 콜백함수의 내부에서 이 users라는 값을 deps의 요소로 넣어주지 않고, 해당 값을 사용하는 setter 부분에서 기존에 값으로써 넣어준 것을 함수형으로 변경해주면 된다.

```javascript
// 변경 전) 코드
setUsers(users.concat(user));
// 변경 후) 코드
setUsers((users) => users.concat(user));
```

위와같이 useState() 함수형 업데이트를 해주게 되면, useCallback()의 deps로 넣어주지 않아도 parameter에서 항상 최신 users 정보를 참조하게 되므로 위의 문제를 해결할 수 있다.

```javascript
// 변경 전 onCreate() 함수
const onCreate = useCallback(() => {
  const user = {
    id: nextId.current + 1,
    username,
    email
  };

  // setUsers([...users, user])
  setUsers(users.concat(user));

  setInputs({
    username: '',
    email: ''
  });

  console.log(nextId.current);
  nextId.current += 1;
}, [username, email, users]);

const onRemove = useCallback(
  (id) => {
    setUsers(users.filter((user) => user.id !== id));
  },
  [users]
);

const onToggle = useCallback(
  (id) => {
    setUsers(
      users.map((user) =>
        user.id === id ? { ...user, active: !user.active } : user
      )
    );
  },
  [users]
);

// 변경 후 onCreate() 함수
const onCreate = useCallback(() => {
  const user = {
    id: nextId.current + 1,
    username,
    email
  };

  // setUsers([...users, user])
  setUsers((users) => users.concat(user));

  setInputs({
    username: '',
    email: ''
  });

  console.log(nextId.current);
  nextId.current += 1;
}, [username, email]);

// 함수가 처음 컴포넌트가 생성되었을때 한 번만 생성되고 그 이후로는 재사용된다.
const onRemove = useCallback((id) => {
  setUsers((users) => users.filter((user) => user.id !== id));
}, []);

const onToggle = useCallback((id) => {
  setUsers((users) =>
    users.map((user) =>
      user.id === id ? { ...user, active: !user.active } : user
    )
  );
}, []);
```

## <ins><b>중간정리</b></ins>

- 연산된 값을 재사용하기 위해서는 `useMemo()` 사용
- 생성된 함수를 재사용하기 위해서는 `useCallback()` 사용
- 렌더링된 컴포넌트를 재사용하기 위해서는 `React.memo()` 사용
- 무조건 useCallback을 사용한다고 성능이 개선되는 것은 아니다. 사용함으로써 오히려 더 많은 코드를 실행하게 되는 결과를 낳을 수 있다. useCallback(), useMemo, React.memo()를 사용할때에는 반드시 최적화를 할 수 있는 상황을 판단해서 사용을 해야 한다.

## <ins><b>또 다른 컴포넌트의 상태관리 방법 = useReducer</b></ins>

컴포넌트의 현재상태 값을 업데이트 해 줄 때에는 여러가지 방법이 있는데 그 중에 하나인 useReducer()를 사용하는 방법에 대해서 정리해보려고 한다.

기존에 상태값을 업데이트할때 사용한 useState() hook 함수는 상태 값을 업데이트 시켜 줄때 다음 상태값을 직접 지정해주는 형태로 상태를 업데이트해주었지만, useReducer는 action 객체 기반으로 상태를 업데이트 해준다.
action 객체는 업데이트할때 참조하는 객체로, type이라는 값을 참조해서 어떤 상태값을 업데이트를 할지 선택할 수 있다.
업데이트할때 참조할 다른 값이 있다면 추가적으로 객체 내부에 key: value의 형태로 추가해줄 수 있다.

```javascript
dispatch({
  type: 'INCREMENT',
  diff: 4
});
```

useReducer() hook 함수를 사용하게 되면 기존에 컴포넌트 내부에 존재하던 상태 업데이트 로직을 컴포넌트의 밖으로 분리시키는 것이 가능하다.
심지어 다른 파일에 작성해서 불러올 수도 있다.

`reducer: 상태를 업데이트하는 함수`로 인자로 넘겨받은 action 객체의 type을 참조하여 구체적으로 어떤 현재 상태 값을 업데이트해 줄 것인지, 지정해서 처리할 수 있다.

```javascript
function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      return state;
  }
}
```

그럼 userReducer() hook 함수는 어떻게 사용할까?
사용법은 useState와 비슷하다.

number는 현재 상태를 의미하고, dispatch는 액션을 발생시키는 함수이다.
useReducer()함수의 인자로는 첫번째 인자는 앞서 작성해준 reducer함수를 넣어주고 두번째 인자로는 상태값의 초기 값을 넣어주도록 한다.

```javascript
const [number, dispatch] = useReducer(reducer, 0);
```

기존에 작성했던 Counter 컴포넌트의 상태값을 업데이트 하는 로직을 reducer라는 별도의 외부 함수로 구현을 하고, 내부에서는 각 버튼의 액션을 useReducer() hook 함수의 dispatch를 사용해서 상태변화 이벤트가 일어날 수 있도록 작성하였다.

`Counter.js`

```javascript
import React, { useReducer } from 'react';

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1;
    case 'DECREMENT':
      return state - 1;
    default:
      throw new Error('Unhandled action');
  }
}

function Counter() {
  const [number, dispatch] = useReducer(reducer, 0);

  const onIncrease = () => {
    dispatch({
      type: 'INCREMENT'
    });
  };

  const onDecrease = () => {
    dispatch({
      type: 'DECREMENT'
    });
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

- ### useReducer() hook 함수의 적용순서
  순서가 딱히 있는건 아니지만 순차적으로 적용하는 방법을 익혀서 useReducer()함수 사용법에 익숙해지도록 하자.
  - (1) 컴포넌트의 초기 상태값에 해당하는 변수를 컴포넌트의 외부에 선언을 해준다.
  - (2) reducer(state, action) 함수 만들기 (내부로직은 아직)
  - (3) 컴포넌트 내부에 const[state, dispatch] = useReducer(reducer, [initial state]) 선언
  - (4) 비구조 할당문을 통해서 state에 있는 내부 값을 분리해서 필요한 컴포넌트로 props로 형태로 전달
  - (5) 태그의 속성에 넣어 줄 함수는 처음에 작성할때부터 useCallback을 사용하여 wrapping해서 작성을 해준다. (초기 생성하고 이후에는 계속해서 재사용하는 함수라면, 빈 배열([])을, 내부에 props나 상태값이 참조되고 있다면 해당 상태값을 최신 상태로 업데이트해주기 위해서 deps의 인자로 넣어서 처리해준다.
  - (6) 컴포넌트 내부에서 작성한 이벤트 함수 내부에서 type과 전달할 상태값에 대한 정보를 key:value의 형태로 작성을 해준다.
  - (7) 이제 6번 항목에서 작성해준 type에 관한 내용을 reducer() 함수의 내부에서 action 객체로부터 type 값에 접근/사용하여 조건 처리를 해주도록 한다. 해당 타입값인 경우에 업데이트해서 반환할 상태값에 대한 부분은 내부의 반환문(return)에서 작성을 해주도록 한다.

## useReducer VS useState

딱히 정해진 것은 아니지만, 컴포넌트에서의 상태값 관리가 단순한 경우에는 useState를 사용하고, 상태값의 관리가 복잡한 경우에는 useReducer를 사용하도록 한다. setter를 한 함수에서 여러번 사용되는 경우가 생긴다면, useReducer를 사용을 고려해보는 것이 좋다.

```javascript
setUsers((users) => users.concat(user));
setInputs({
  username: '',
  email: ''
});
```

## 커스텀 Hook 만들어서 사용하기
