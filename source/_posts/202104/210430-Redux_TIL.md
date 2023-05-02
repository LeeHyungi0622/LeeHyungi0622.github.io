---
title: 210430 React Redux
date: 2021-04-30 16:28:00
tags:
  - ReactJS
  - self-development
  - TIL
categories:
  - ReactJS
---

`2021/05/13 Update - createSelector shopping cart 연습예제`

<div align="center">
  <img src="/images/post_images/210430_redux_toolkit_with _react.webp" alt="React Redux"/>
</div>

## <ins><b>React는 프레임워크가 아닌 라이브러리?</b></ins>

상태관리 라이브러리에 대해서 포스팅을 하는 글에서 갑자기 뜬금없이 React가 프레임워크인지 아닌지에 대한 이야기를 하는 이유는 `React에서는 상태관리와 라우팅을 기본적으로 제공하지 않기 때문에 라이브러리로 분류`한다는 이야기를 하기 위해서이다. Vue와 Angular에서는 자체적으로 상태관리를 지원하기 때문에 프레임워크라고 하지만 React는 다르다.
하지만 생태계 자체로써 봤을때에는 React도 프레임워크이다.

## <ins><b>React의 상태관리 라이브러리의 선택지 Redux와 MobX</b></ins>

React는 Vue와 Angular와 다르게 자체적으로 상태관리를 지원하지 않기 때문에 Redux나 MobX를 선택적으로 사용해서 상태를 관리한다.

## <ins><b>ContextAPI + useReducer가 Redux와 MobX를 대체할 수 있다?</b></ins>

JavaScript를 ContextAPI+useReducer라고 하고, jQuery를 Redux나 MobX라고 비유한다면, ContextAPI+useReducer의 조합으로 Redux나 MobX를 구현할 수 있다는 결론이 나온다. 하지만 손쉽게 사용할 수 있도록 이미 만들어져 있는 Redux나 MobX를 사용하는 것이 좋다.

  <!-- more -->

## <ins><b>양방향, 단방향 modeling</b></ins>

화면과 데이터가 있다고 가정을 하자. 만약에 화면을 바꾸면 데이터도 바뀌고, 데이터를 바꿔도 화면이 바뀌는 양방향 Pattern이라면, 구조가 복잡해졌을때 버그가 생기기 쉽다. (Angular)
이러한 문제를 해결하기 위해 Facebook에서는 단방향 Pattern을 개발했는데 이것이 바로 `flux`이다.
[https://facebook.github.io/flux/](https://facebook.github.io/flux/)
이 flux pattern을 통해 alt나 reflux, redux가 개발이 되었고, redux를 개발한 Dan Abramov가 Facebook에 합류를 하고, hooks를 만드는데 큰 기여를 했다고 한다.

## <ins><b>Redux를 쓰면 각 컴포넌트의 state를 안써도 된다?</b></ins>

Redux를 쓰면 각 컴포넌트의 state를 안 쓸 수는 있지만, 안써야 된다는 의미는 아니다. 만약에 컴포넌트에서 독립적으로 쓰이는 state라면(단일 종속), 개별적으로 state를 써서 관리를 하는 편이 좋고, 컴포넌트간에 서로 연관성이 있는 state라면, Redux를 써서 관리를 해주는 것이 좋다.

## <ins><b>Redux의 장점/단점</b></ins>

- 상태를 단방향으로만 바꿀 수 있다. (action - dispatch - reducer - state update)
- action이 dispatch되면 기록이 남기 때문에 history 기능을 통해 에러가 발생했을때 디버깅하기 쉽고, 기록을 통해 action 이전의 상태로 되돌릴 수도 있다.(상태를 업데이트할때 불변성을 지켜서 작성해야 한다)
- reducer에서 action을 받아서 해당 action에 맞게 state의 새로운 객체를 만들어서 기존의 상태를 업데이트한다.

<div align="center">
  <img src="/images/post_images/210429_redux_structure.png" alt="Redux structure"/>
</div>

## <ins><b>Redux의 사용</b></ins>

- **설치**
  ```zsh
  $ npm i redux
  ```
- **공식문서**
  [https://redux.js.org/introduction/getting-started](https://redux.js.org/introduction/getting-started)
- **사용순서**
  reducer/initial state - store 초기화 - action 작성 - dispatch w/action

  ```javascript
  import { createStore } from 'redux';

  const reducer = (prevState, action) => {
    switch (action.type) {
      case 'LOG_IN':
        // state의 구조가 복잡해지면 spread 문법을 사용해서 얕은 복사를 하는 것이 지저분해질 수 있기 때문에 immutable이나 immer를 사용해서 코드를 깔끔하게 만들 수 있다.
        return {
          ...prevState,
          user: action.data
        };
      case 'LOG_OUT':
        return {
          ...prevState,
          user: null
        };
      case 'ADD_POST':
        return {
          ...prevState,
          posts: [...prevState.posts, action.data]
        };
      // 만약에 action.type에 대한 이름이 오타나는 경우를 대비해서 default는 작성해야 한다.
      // reducer에서는 기존의 state를 업데이트하기 위해서 새로운 state 객체를 생성하기 때문에 문제가 될 여지가 있다.
      default:
        return prevState;
    }
  };

  const initialState = {
    user: null,
    posts: []
  };

  const store = createStore(reducer, initialState);

  store.subscribe(() => {
    // react-redux 안에 들어있다.
    // 화면을 바꿔주는 코드는 여기서 알아서 처리해준다.
    // 디버깅하는 경우 사용
  });

  // action을 만들때에는 아래와 같이 업데이트하고자 하는 값을
  // 추상적으로 매개변수를 받아서 작성해주는 것이 좋다.
  const logIn = (data) => {
    return {
      type: 'LOG_IN',
      data
    };
  };
  const logOut = (data) => {
    return {
      type: 'LOG_OUT',
      data
    };
  };
  const addPost = (data) => {
    return {
      type: 'ADD_POST',
      data
    };
  };
  store.dispatch(
    logIn({
      id: 1,
      name: 'lee',
      admin: true
    })
  );

  store.dispatch(logout());

  store.dispatch(
    addPost({
      userId: 1,
      id: 1,
      content: 'sample content'
    })
  );

  console.log(store.getState()); // state 확인
  ```

  <br/>

## <ins><b>Redux를 caching 목적으로 사용</b></ins>

게시판의 리스트 정보의 경우에는 redux에 저장해서 게시물 상세페이지에 이동했다가 다시 게시판 리스트로 돌아왔을때 서버로 데이터를 요청하지 않고, redux에 있는 리스트 정보를 가져오게 할 수 있다. 게시물 상세페이지에 대한 정보도 redux에 담아서 caching해서 사용할 수 있다.
redux를 global한 상태관리 개념으로만 생각하지 말고 caching의 관점에서도 생각해서 사용해보도록 하자.

## <ins><b>Redux의 폴더구조</b></ins>

redux에서 action과 reducer의 코드가 길어질 수 있기 때문에 별도의 폴더와 파일로써 분리를 해주는 것이 좋다.
(action과 reducer는 매개변수와 함수 내부의 변수만 참조하는 순수함수이기 때문에 분리하기 용이하다)
action 객체를 나누는 기준은 initialState의 항목을 기준으로 한다. (공통화 시킬 수 있는 부분은 최대한 공통화 처리)

`userAction.js`

```javascript
const login = (data) => {
  return {
    type: 'LOG_IN',
    data
  };
};

const logOut = () => {
  return {
    type: 'LOG_OUT'
  };
};

export default {
  login,
  logOut
};
```

`postAction.js`

```javascript
const addPost = (data) => {
  return {
    type: 'ADD_POST',
    data
  };
};

export default {
  addPost
};
```

reducer는 redux에서 제공해주는 combineReducers를 사용해서 객체로 묶어준다.

`userReducer.js`

```javascript
// Reducer를 분리했기 때문에 초기값에 대한 범위도 줄어들었다.
const INITIAL_STATE = {
  isLoggingIn: false,
  data: null
};
export const userReducer = (prevState = INITIAL_STATE, action) => {
  switch (action.type) {
    case 'LOG_IN':
      // state의 구조가 복잡해지면 spread 문법을 사용해서 얕은 복사를 하는 것이 지저분해질 수 있기 때문에 immutable이나 immer를 사용해서 코드를 깔끔하게 만들 수 있다.
      return {
        ...prevState,
        data: action.data
      };
    case 'LOG_OUT':
      return {
        ...prevState,
        data: null
      };
    default:
      return prevState;
  }
};
```

`postReducer.js`

```javascript
// Reducer를 분리했기 때문에 초기값에 대한 범위도 줄어들었다.
const INITIAL_STATE = [];

export const postReducer = (prevState = INITIAL_STATE, action) => {
  switch (action.type) {
    case 'ADD_POST':
      return [...prevState.posts, action.data];
    default:
      return prevState;
  }
};
```

`root-reducer.js`

```javascript
import { userReducer } from './userReducer.js';
import { postReducer } from './postReducer.js';

// initialState의 객체 속성이름 사용
export default combineReducers {
  user: userReducer,
  posts: postReducer,
}
```

action의 타입의 경우에는 reducer와 action 객체에서 공통으로 참조되는 속성이기 때문에 별도의 파일로 빼서 사용하는 것이 좋다.

`user.types.js`

```javascript
export const UserActionTypes = {
  LOG_IN: 'LOG_IN',
  LOG_OUT: 'LOG_OUT'
};
```

`post.types.js`

```javascript
export const PostActionTypes = {
  ADD_POST: 'ADD_POST'
};
```

## <ins><b>Redux - middleware</b></ins>

action은 객체로, 기본적으로 동기방식으로 처리되고 dispatch 함수는 action을 받아서 처리하기 때문에 중간단계에서 비동기 처리할 틈이 없다.
하지만 dispatch와 reducer 사이에서 동작하는 middleware를 사용하면 사이에서 비동기 처리를 해 줄 수 있다. 이때 사용되는 `비동기 처리 middleware에는 redux-thunk와 redux-saga`가 있다.
(middleware는 반드시 비동기 처리를 하기 위해 사용되는 것이 아닌, dispatch와 reducer 사이에서 특정 처리를 하기 위해서 사용된다는 것을 알아두자)

```javascript
const enhancer = applyMiddleware();
// 아래와 같이 compose를 생략가능
// compose는 합성해주는 역할로, redux devtool 플러그인을 추가적으로 붙일때 compose함수를 합성할 수 있다.
const enhancer = compose(applyMiddleware(), devtool);

// 세 번째 인수가 enhancer인데, 기능 증강의 의미로 이해한다.
const store = createStore(reducer, initialState, enhancer);
```

applyMiddleware 함수의 인수로는 아래와 같이 3단 함수가 들어간다.

```javascript
// 3단 함수가 들어간다.
// 특정 시점을 구분해서 처리할 필요가 없는 경우에 아래와 같이 작성해준다.
const firstMiddleware = (store) => (dispatch) => (action) => {
  console.log(action); // 부가적인 동작 추가 (1)
  // 기능추가를 전 단계에서 추가할 수 있다.
  dispatch(action); // 기본 기능 (dispatch(action)을 reducer에 전달)
  // 기능추가를 후 단계에서 추가할 수 있다.
};

// 중첩을 해둔 이유는 store - next 사이, next - action 사이, action에서만 실행되도록 처리하고 싶을때 구분할 수 있다.
function firstMiddleware(store) {
  return function (next) {
    return function (action) {};
  };
}
```

## <ins><b>redux-thunk</b></ins>

위의 firstMiddleware 함수에서 기본 기능의 전/후로 코드를 추가해 줄 수 있다고 했는데, 바로 이 부분에서 비동기 처리를 할 수 있다.
원래 redux에서는 비동기 action이라는 것은 없었는데, middleware를 사용해서 redux기능을 확장하여 구현할 수 있다.
`action이 객체인 경우에는 동기방식(sync action creator)`으로 처리가 되는데, `함수인 경우에는 비동기 처리(async action creator)`가 되도록 조건문으로 처리해준다.
일반 객체(동기 방식)의 경우에는 일반적인 처리로 action 객체(sync action creator)가 reducer 함수로 바로 전달이 되고, 비동기 방식의 경우에는 store.dispatch와 store.getState가 async action creator 함수의 인자로 전달이 되어 실행이 된다.
(비동기 처리 함수인 login은 우선 서버로 로그인을 요청하고, try 블럭에 있는 비동기 처리 부분을 실행한 다음에 실행에 성공하면 비동기 함수 내부의 함수를 한 번 더 실행한다.)

`userAction.js`

```javascript
// 로그인 요청 sync action creator
const logInRequest = (data) => {
  return {
    type: 'LOG_IN_REQUEST',
    data
  };
};

const logInSuccess = (data) => {
  return {
    type: 'LOG_IN_SUCCESS',
    data
  };
};

const loginFailure = (error) => {
  return {
    type: 'LOG_IN_FAILLURE',
    error
  };
};

const login = (data) => {
  // async action creator
  return (dispatch, getState) => {
    // 서버로 로그인 요청 보내기
    dispatch(logInRequest(data));
    try {
      // 서버로의 로그인 요청이 성공한 경우,
      // setTimeout으로 비동기 처리 테스트 (2초뒤에 loginSuccess)
      // setTimeout 부분에 axios.post().then().catch()로 하기
      setTimeout(() => {
        dispatch(
          logInSuccess({
            userId: 1,
            name: 'hyungi'
          })
        );
      }, 2000);
    } catch (e) {
      // 서버로의 로그인이 실패하는 경우,
      dispatch(loginFailure());
    }
  };
};
```

`index.js`

```javascript
const firstMiddleware = (store) => (dispatch) => (action) => {
  console.log(action); // 부가적인 동작 추가 (1)
  // 기능추가를 전 단계에서 추가할 수 있다.
  dispatch(action); // 기본 기능 (dispatch(action)을 reducer에 전달)
  // subscribe 실행
  // 기능추가를 후 단계에서 추가할 수 있다.
};

// thunk middleware는 매우 간단하기 때문에 직접 구현해서 사용해도 된다.
// STORE: redux store
// dispatch와 reducer 구간 사이에서 동기와 비동기 처리를 구분한다.
const thunkMiddleware = (store) => (dispatch) => (action) => {
  // 원래 action은 객체(동기)인데,
  // redux와 약속을 한다. 비동기인 경우에는 action을 함수로 두겠다.
  if (typeof action === 'function') {
    // 전달되는 곳 -
    // dispatch와 store.dispatch는 같다.
    return action(store.dispatch, store.getState);
  }
  return dispatch(action);
};
//middleware는 인수 제한없이 추가가 가능하다.
const enhancer = applyMiddleware(firstMiddleware, thunkMiddleware);
// createStore의 세 번째 인자로 enhancer를 넣어준다.
const store = createStore(reducer, initialState, enhancer);

// 로그인 시도
// dispatch에서 반환되는 결과값의 타입이 함수인 경우,
store.dispatch(
  login({
    id: 1,
    name: 'lee',
    admin: true
  })
);
```

thunk로는 간단한 비동기 처리를 하고, 만약에 thunk로 비동기 처리를 하는데 있어 한계가 있을때(복잡한 비동기 처리)에는 redux-saga를 사용하도록 하자.

## <ins><b>react-redux</b></ins>

React와 Redux를 연겨시키기 위해서는 react-redux를 사용해야 한다.

```zsh
$ npm i react-redux
```

```javascript
import { Provider } from 'react-redux';
import storage from 'storage';
...
ReactDOM.render(
  <Provider storage={storage}>
    <App />
  </Provider>
)
...
```

최상위 component를 `react-redux에서 제공해주는 Provider`로 감싸서 storage의 속성으로 앞서 작성해준 storage 객체를 값으로 넣어준다.
이제 감싸진 하위 컴포넌트들에서 dispatch의 접근이 가능하다.

```javascript
import { useDispatch, useSelector } from 'react-redux';

// state : initial state
const user = useSelector((state) => {
  state.user.data;
});
const posts = useSelector((state) => {
  state.posts;
});
const dispatch = useDispatch();

const onClick = useCallback(() => {
  // useDispatch를 통해서 action함수를 인수와 함께 실행해준다.
  dispatch(
    logIn({
      id: 'lee',
      password: '123'
    })
  );
}, []);

return (
  <div>
    {user ? <div>{user.name}</div> : 'Please login'}
    <button onClick={onClick}> LOGIN </button>
  </div>
);
```

## <ins><b>react-redux devtools와 연결하기</b></ins>

redux-devtools

[https://github.com/zalmoxisus/redux-devtools-extension](https://github.com/zalmoxisus/redux-devtools-extension)

```javascript
$ npm i redux-devtools-extension -D
```

```javascript
import { composeWithDevTools } from 'redux-devtools-extension';

...
//middleware는 인수 제한없이 추가가 가능하다.
const enhancer = composeWithDevTools(
                  applyMiddleware(
                    firstMiddleware,
                    thunkMiddleware,
                  ),
                );
// createStore의 세 번째 인자로 enhancer를 넣어준다.
const store = createStore(reducer, initialState, enhancer);
...
```

- ## **배포환경인 경우 구분해서 enhancer 설정하기**

(`주의`)배포환경에서 devtools를 사용하게되면 redux의 데이터 구조가 개발자 도구에서 노출이 된다.

```javascript
const enhancer =
  process.env.NODE_ENV === 'production'
    ? compose(applyMiddleware(firstMiddleware, thunkMiddleware))
    : composeWithDevTools(applyMiddleware(firstMiddelware, thunkMiddleware));
```

<div align="center">
  <img src="/images/post_images/210514_redux_dev_tools.png" alt="Redux dev tools"/>
</div>

Redux dev tools를 사용하면 각 action이 dispatch될때마다 업데이트되는 상태의 변화를 확인할 수 있다. 이는 reducer에서 불변성을 지켜서 기존의 상태값을 업데이트해주고, history로 쌓아주기 때문에 가능한 것이다.

## <ins><b>class component에서의 redux 사용 - connect</b></ins>

functional component에서와 동일하게 state와 dispatch를 가져온다.

`functional component에서의 react-redux 사용`

```javascript
import { useDispatch, useSelector } from 'react-redux';

// state : initial state
const user = useSelector((state) => {
  state.user.data;
});
const posts = useSelector((state) => {
  state.posts;
});
const dispatch = useDispatch();
```

`class component에서의 react-redux 사용`
아래와 같이 export 구문에서 connect로

```javascript
import { connect } from 'react-redux';

// 컴포넌트가 렌더링될때마다 매번 아래 함수가 실행이 된다. (성능상 문제)
// 위의 문제를 해결하기 위해서 reselect를 써서 해결한다.
// hooks를 쓰면 reselect를 사용하지 않아도 된다.
// 하나의 객체이기 때문에 uesr나 posts 중에 하나만 바뀌어도 전부 다 계산을 다시한다.
// hooks는 분리가 되어있다.
const mapStateToProps = (state) => ({
  user: state.user,
  posts: state.posts
});

// this.props.dispatchLogIn 로 불러와서 사용
// this.props.dispatchLogOut 로 불러와서 사용
const mapDispatchToProps = (dispatch) => ({
  dispatchLogIn: (data) => dispatch(logIn(data)),
  dispatchLogOut: () => dispatch(logOut())
});

// 확장하는 HOC를 붙여줘서 class 내부에서 dispatch와 state를 불러서 사용할 수 있다.
export default connect(mapStateToProps, mapDispatchToProps)(App);
```

## <ins><b>container component</b></ins>

React에서 데이터를 가져오는 컴포넌트를 컨테이너 컴포넌트라고 하는데, redux를 사용해서 데이터를 가져오는 컴포넌트를 `컨테이너 컴포넌트`라고 한다.

## <ins><b>immer</b></ins>

spread문법을 사용해서 불변성을 지키는 방법은 직관적인 방법이 아니다. 만약 객체의 구조가 깊어지면 불변성을 지키기 위해 spread 문법을 많이 사용하게 되는데 코드가 복잡해지고 가독성이 안좋아진다.
따라서 `immer`라는 라이브러리를 사용해서 직관적으로 불변성을 지키면서 상태 데이터를 업데이트할 수 있다.

immer도 다음 state 객체를 만들어내는 역할을 한다.

```javascript
// immer의 기본 형태
// draft 부분을 prevState의 복사본으로 보면 된다.으로 보면 된다.
nextState = produce(prevState, (draft) => {});
```

아래의 기존 reducer 함수를 immer를 사용해서 간략하게 작성해본다.
`기존 reducer`

```javascript
const reducer = (prevState, action) => {
  switch (action.type) {
    case 'LOG_IN':
      // state의 구조가 복잡해지면 spread 문법을 사용해서 얕은 복사를 하는 것이 지저분해질 수 있기 때문에 immutable이나 immer를 사용해서 코드를 깔끔하게 만들 수 있다.
      return {
        ...prevState,
        user: action.data
      };
    case 'LOG_OUT':
      return {
        ...prevState,
        user: null
      };
    case 'ADD_POST':
      return {
        ...prevState,
        posts: [...prevState.posts, action.data]
      };
    // 만약에 action.type에 대한 이름이 오타나는 경우를 대비해서 default는 작성해야 한다.
    // reducer에서는 기존의 state를 업데이트하기 위해서 새로운 state 객체를 생성하기 때문에 문제가 될 여지가 있다.
    default:
      return prevState;
  }
};
```

`immer 적용 reducer`
reducer 내부의 switch case문을 produce 함수로 감싸준다.

```javascript
import produce from 'immer';

const reducer = (prevState, action) => {
  // draft는 복사본
  // 복잡한
  return produce(prevState, (draft) => {
    switch (action.type) {
      case 'LOG_IN':
        draft.data = null;
        draft.isLoggingIn = true;
        break;
      case 'LOG_OUT':
        draft.data = action.data;
        draft.isLoggingIn = false;
        break;
      case 'ADD_POST':
        // 사본 데이터에 action에서 받은 데이터를 push해준다.
        draft.push(action.data);
        break;
      default:
        break;
    }
  });
};
```

## <ins><b>redux toolkit</b></ins>

redux toolkit은 redux에서 자주 쓰이는 기능을 리덕스 팀에서 라이브러리로 만들었는데, redux toolkit을 사용하면, 기존의 redux, devtools, redux-thunk, redux-saga, immer를 사용안하고, redux toolkit의 내장된 기능으로 사용할 수 있다.

중요한 메인 기능은 `createSlice`와 `createAsyncThunk`이다.

```zsh
$ npm i @reduxjs/toolkit
```

[https://redux-toolkit.js.org/](https://redux-toolkit.js.org/)

## <ins><b>react hotloader에서 react-refresh로 변경</b></ins>

기존에 react에서 지원하던 hotloader가 react-refresh로 업데이트가 되었고,
`@pmmmwh/react-refresh-webpack-plugin`가 추가되었다. (둘 다 devDependency로 설치)

`webpack.config.js`

```js
const reactRefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');
// 기존 webpack 설정 파일에서 일부 변경
plugins: ['react-refresh/babel'],
....
plugins: [
  new ReactRefreshWebpackPlugin()
],
```

기존에 최상위 컴포넌트를 hot으로 감싸서 처리했던 것을 이제는 감싸서 처리를 안해줘도 된다.

## <ins><b>webpack 버전 업데이트에 따른 변경사항</b></ins>

```json
// webpack v5, webpack-cli v4
// 기존
"dev": "webpack-dev-server --hot"
// 변경
"dev": "webpack serve --env development --hot"
```

## <ins><b>기존에 redux store에 연결하던 부분을 redux toolkit을 사용해서 업데이트</b></ins>

```javascript
// 기존의 redux와 devtools 제거
import { configureStore } from '@reduxjs/toolkit';

// initial state, thunk middleware는 내장되어있기 때문에 제거, devtools을 달아줬던 enhancer도 사라진다.
// 간단하게 store는 아래와 같이 초기화시켜준다.
// thunk, immer, devtools
// initial state는 preloadedState 속성인데,
// 나중에 SSR할때 서버로 부터 초기 데이터를 받아올때 해당 속성에 초기 데이터를 넣어주면 된다.
const store = configureStore({ reducer });
```

만약에 custom middleware를 사용하고 싶은 경우,

```javascript
//기존의 thunk middleware도 포함시키기 위해서 getDefaultMiddleware 가져오기
const {configureStore, getDefaultMiddleware} from '@reduxjs/toolkit';

const firstMiddleware = (store) => (next) => (action) => {
  console.log('logging', action);
  next(action);
};

const store = configureStore({
  reducer,
  middleware: [firstMiddleware, ...getDefaultMiddleware()],
  // production 모드가 아닐때만 devTools 사용
  devTools: process.env.NODE_ENV !== 'production',
});
```

## <ins><b>기존에 hook으로 구현했던 부분을 toolkit으로 대체</b></ins>

`기존의 reducer를 대체`

```javascript
import userSlice from './user';
import postSlice from './post';

export default combineReducers({
  // slice 자체가 아닌 slice안의 reducer를 넣어줘야 한다.
  user: userSlice.reducer,
  posts: postSlice.reducer
});
```

reducer와 action이 분리되어 있었는데, 합친 개념이 slice이다. 특정 action은 특정 reducer에 종속되어있다. 따라서 나누지 말고 합치게 되었다.

```javascript
// immer 내장되서 별도로 가져올 필요 없음
import { createSlice } from '@reactjs/toolkit';
import { logIn } from './actions/user';

const initialState = {
  data: []
};

const userSlice = createSlice({
  // reducer이름
  name: 'user',
  initialStae,
  // 동기적인 액션 or 내부적인 액션
  reducers: {
    // 동기적인 액션
    // action creator를 따로 만들어서 사용할 필요가 없다.
    logOut(state, action) {
      state.data = null;
    }
  },
  // 비동기적인 액션 or 외부적인 액션)
  // toolkit에서는 action으로 받는 데이터를 action.payload로 고정해서 받는다.
  // pending, fullfilled, rejected는 하나의 set로써 작성해주면 된다.
  extraReducers: {
    [logIn.pending](state, action) { // user/logIn/pending = [logIn.pending]
      state.isLoggingIn = true;
    },
    [logIn,fullfilled](state, action){ // user/logIn/fullfilled =  [logIn.fullfilled]
      // 성공했을때의 반환값 payload
      state.data = action.payload;
      state.isLoggingIn = false;
    },
    [logIn.rejected](state, action){ // user/logIn/rejected =  [logIn.rejected]
      // 실패했을때에는 action.error
      state.data = null;
      state.isLoggingIn = false;
    }
  }
});

export default userSlice;
```

`app.js`

```javascript
import userSlice from './reducers/user';

// 비동기 action creator의 경우에는 따로 외부로 빠져나가 있기 때문에
// 별도로 호출해서 dispatch로 호출을 한다.
const onClick = useCallback(() => {
  dispatch(
    // logIn에 넣어준 객체값은 pending의 meta.arg에 들어있다.
    logIn({
      id: 'lee',
      password: '1q2w3e4r'
    })
  );
}, []);

// 동기 action creator의 경우에는 slice.actions. 로 참조햇
// dispatch로 호출한다.
const onLogout = useCallback(() => {
  dispatch(userSlice.actions.logOut());
}, []);
```

이전에는 동기/비동기 action creator를 한 곳에서 모두 관리를 했는데, toolkit을 사용할때는 reducer에서는 직접적으로 동기적인 action creator를 생성해서 관리를 해주고, 비동기적인 action creator는 기존에 별도의 action 파일에서 관리를 해주게 된다.

`actions/user.js`

```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';
// action의 이름, action을 호출할때 받는 데이터 (data),
const logIn = createAsyncThunk('user/logIn', async (data, thunkAPI) => {
  // ex) const response = await axios.post('/login', {email, password})
  // reducer의 state를 가져올 수 있다.
  // const state = thunkAPI.getState();
  // state.user.data;
  // return 한 데이터는 성공했을때의 데이터
  // throw error exception은 실패했을때의 데이터
  // loading - success - failure
  // pending - fullfilled, rejected 로 thunk에서는 구분
  // createAsyncThunk에서는 try-catch 분기 처리안해줘도 된다.
});

// dispatch 부분에서 넘겨준 객체 데이터가 위의 action에서 data로 넘겨지고,
// 해당 data를 사용해서 서버로 요청을 보내서 받게되는 response를 처리하는 것이라고 생각하면 된다.

// dispatch(logIn({
//  userId: 1,
//  name: 'lee',
//}))
```

```javascript
import { createAsyncThunk } from '@reduxjs/toolkit';
// action의 이름, action을 호출할때 받는 데이터 (data),
const logIn = createAsyncThunk('user/logIn', async (data, thunkAPI) => {
  // 서버로부터 비동기 요청을 해서 받은 데이터를 return해주게 되는데,
  // 이 데이터가 바로 userSlice에서 비동기 처리 데이터를 받는 부분에서의 action.payload가 된다.
});
```

<ins><b>toolkit의 특징이 request마다 고유의 requestId를 부여해주는데, 같은 요청을 여러번 했을때 구분을 하기 위한 목적으로 사용할 수 있다.</b></ins>

## <ins><b>extraReducers를 builder로 작성하는 방법</b></ins>

builder로 chaining된 형태로 작성을 하게 되면, 나중에 타입스크립트를 적용했을때 타입추론이 잘된다.
그리고 switch 문의 default 처리도 작성해 줄 수 있다.

```javascript
...
  extraReducers: (builder) => builder
    .addCase(logIn.pending, (state, action) => {
      state.isLoggingIn = true;
    })
    .addCase(logIn.fullfilled, (state, action) => {
      state.data = action.payload;
      state.isLoggingIn = false;
    })
    .addCase(logIn.rejected, (state, action) => {
      state.data = null;
      state.isLoggingIn = false;
    })
    // 공통 처리를 해주고 싶을때 addMatcher를 사용할 수 있다.
    // switch case문에서 조건을 연달아서 처리하는 경우와 동일 처리
    .addMatcher((action) => {
      return action.type.includes('/pending');
    })
    // switch case문일때 default의 처리와 동일 처리
    .addDefaultCase((state, action) => {
      // default
    })
});
```

## <ins><b>redux를 쓰지 말아야 할 때 (input/async)</b></ins>

- ## **input**

input은 redux를 사용하지 않는 것이 좋다. 굳이 redux에서 써야한다면, input 태그의 blur 속성의 함수에서 처리를 해주거나 마지막 한 번만 form 태그의 onSubmit에서 hooks로 정의된 값을 일괄적으로 dispatch해주는 편이 좋다.

useSelector를 사용해서 state값을 가져올때에도 문제점이 있는데, 아래와 같이 useSelector를 사용해서 값을 참조하게 되면, 참조하고 있는 user state의 값이 업데이트 될 때마다 컴포넌트가 re-rendering된다.

```javascript
const { email, password } = useSelector((state) => state.user);
```

물론 useState를 사용해도 input 태그에 한 글자 쓸때마다 해당 state에 대해서 re-rendering이 된다.
하지만, useSelector를 사용해서 user state정보를 객체로 참조한다면, 다른 컴포넌트들에서도 user state의 정보중에 하나만 참조하게 되었을때도 참조하고 있는 모든 컴포넌트들이 모두 re-rendering되어 성능상 문제가 생긴다.

따라서 useSelector를 사용해서 state의 값을 참조할때에는 최대한 작은 단위로 나눠서 참조하도록 한다.

```javascript
const eamil = useSelector((state) => state.user.email);
const password = useSelector((state) => state.user.password);
```

다 풀어서 사용하기 힘들기 때문에 성능상 문제가 없다면 객체로 묶어서 참조해도 된다. 너무 빠른 최적화는 변화에 대처하기 힘들어진다. 필요에 따라 적절하게 판단해서 최적화를 시켜나가도록 하자.

- ## reselector를 사용해서 문제해결

  reselector로 유명한 라이브러리를 toolkit에 포함시켰다. redux를 사용하는 경우에는 import해서 사용하도록 하자.
  만약에 JSX에서 함수 연산이 되고, input 태그에 값이 입력될때마다 함수가 다시 렌더링이 된다면, 성능문제가 될 수 있다.
  따라서 별도로 useMemo로 JSX의 함수 연산부분을 빼서 처리를 한다면, 값을 기억할 수 있다. 하지만 useMemo로 문제가 해결된 것은 아니다.
  useMemo를 사용함으로써 deps에 넣은 값이 바뀌었는지 아닌지 별도로 검사를 해줘야 되기 때문에 실제 캐싱한 연산보다 deps에서 비교검사해야 되는 부분의 처리가 크다면 효율적이지 않은 문제해결 방법이다.

  따라서 component의 윗 단(밖)에서 createSelector 함수를 사용해서 해결할 수 있다.

  ```javascript
  import { createSelector } from '@reduxjs/toolkit';

  // selector는 useSelector 내에서 작성해준 함수이다.
  const priceSelector = (state) => state.user.prices;
  //createSelector가 memoization 역할을 해준다.
  const sumPriceSelector = createSelector(
    priceSelector,
    // state.user.prices
    (prices) => prices.reduce(a, c) => a + c, 0),
  );

  const App = () => {
    ...
    const totalPrice = useSelector(sumPriceSelector);
    ...
  }

  ```

  `2021/05/13 Update`
  `createSelector 연습예제`
  쇼핑카트 예제로 createSelector를 연습해보았다.

  ```javascript
  // 다음과 같이 hidden과 cartItems를 속성으로 갖는 상태값이 있다고 가정한다.
  const state = {
    hidden: true,
    cartItems: []
  };

  // createSelector가 caching의 역할을 해주기 때문에 selector에서 가져오고자 하는 값이 변하지 않으면 두번째 인자로 넣어준 콜백함수를 처리하지 않고 기존의 값을 재사용하도록 한다.
  const selectCart = (state) => state.cart;

  // 상태값에서 cartItem만을 추출해서 가져오는 함수 (state에서 값이 변하지 않으면 캐싱된 값을 가져다 쓴다.)
  export const selectCartItems = createSelector(
    [selectCart],
    (cart) => cart.cartItems
  );

  // 상태값에서 cartItem만을 추출해서 가져오는 함수
  export const selectCartHidden = createSelector(
    [selectCart],
    (cart) => cart.hidden
  );

  // cartItem의 총 갯수를 체크하는 함수
  export const selectCartItemsCount = createSelector(
    [selectCartItems],
    (cartItems) =>
      cartItem.reduce(
        (accumulatedQuantity, cartItem) =>
          accumulatedQuantity + cartItem.quantity,
        0
      )
  );
  // cart에 담긴 상품의 총 가격을 체크하는 함수
  export const selectCartTotal = createSelector(
    [selectCartItems],
    (cartItems) =>
      cartItems.reduce(
        (accumulatedPrice, cartItem) =>
          accumulatedPrice + cartItem.quantity * cartItem.price,
        0
      )
  );
  ```

- ## **비동기 처리(async)**

만약에 비동기 서버요청이 특정 컴포넌트 하나에서만 실행이 되고, 다른 컴포넌트들에게는 영향을 주지 않는다면, 해당 컴포넌트에서만 axios를 사용해서 비동기 처리를 해주면 된다. (별도로 redux에서 비동기처리로 작성해 줄 필요가 없다)

```javascript
const [isLoading, setLoading] = useState(false);
const [error, setError] = useState(false);
const [done, setDone] = useState(false);

const onClick = useCallback(async () => {
  setLoading(true);
  setDone(false);
  setError(false);
  try {
    const response = await axios.post('/login');
    setDone(true);
  } catch (e) {
    setError(e);
  } finally {
    setLoading(false);
  }
}, []);
```

만약에 비동기 액션이 더 생겨난다면 hooks로 관리해야 되는 상태들이 많이 늘어난다. 그리고 같은 비동기 요청을 여러번 연달아서 한다면, 여러개의 요청에 대한 정보를 전부 담을 수 없다.
