---
title: 210517 React with NextJS TIL - Next Redux Wrapper, HYDRATE, redux-thunk, 제너레이터 함수에 대한 이해, redux-saga
date: 2021-05-17 02:58:00
tags:
  - React-Framework
  - self-development
categories:
  - NextJS
---

`2021/05/17 - redux-thunk 내용추가`

<div align="center">
  <img src="/images/post_images/210426_reactjs-ssr-nextjs.jpeg" alt="ReactJS with NextJS"/>
</div>

## <ins><b>Next Redux Wrapper</b></ins>

일반적으로 React에 Redux를 붙일 때에는 하나의 Redux store만 존재하기 때문에 어렵지 않다.
`하지만 Next.js에서 Redux를 사용하게 되면 여러 개의 Redux store가 생성된다.`
그 이유는 Next.js에서는 User가 Request를 보낼때마다 Redux store를 새로 생성하기 때문이다. 그리고 Next.js에서 제공하는 `getInitialProps와 getServerSideProps에서도 Redux store에 접근이 가능`하도록 해야되기 때문에 NextJS에서 Redux를 붙일때 꽤나 복잡하다. 하지만 이를 간편하게 해주는 라이브러리가 있는데 바로 `Next Redux Wrapper`이다.

## <ins><b>next redux wrapper 설치</b></ins>

`/store/configureStore.js`

```javascript
import { createWrapper } from 'next-redux-wrapper';
import { createStore } from 'redux';
import reducer from '../reducers';

const configureStore = () => {
  const store = createStore(reducer);
  return store;
};

const wrapper = createWrapper(configureStore, {
  debug: process.env.NODE_ENV === 'development'
});

export default wrapper;
```

  <!-- more -->

`_app.js`

```javascript
...
import wrapper from '../store/configureStore';

const App = ({ Component }) => {
    return (
        <>
            <Head>
                <meta charset="utf-8" />
                <title>Next.js with Redux sample</title>
            </Head>
            <Component />
        </>
    )
}

App.propTypes = {
    Component: PropTypes.elementType.isRequired,
}

export default wrapper.withRedux(App);
```

Next에서는 버전 6 이후부터는 일반적으로 Redux를 사용했을때 Provider로 감싸주는 부분이 생략되었다.
(별도로 Provider로 감싸주지 않아도 알아서 처리해준다)

이제 각 각의 컴포넌트에서 만들어준 store 객체를 이용해서 action을 dispatch해주게 되면 store에 있는 상태값이 바뀌게 된다.

```javascript
store.dispatch({
  type: 'CHANGE_NICKNAME',
  data: 'kim'
});
```

## **HYDRATE**

만들어 준 reducer에서 action.type으로 사용할 HYDRATE를 `next-redux-wrapper`로부터 import해준다.
HYDRATE의 등장은 SSR을 위한 것으로, getInitialProps와 getServerSideProps에서도 Redux store에 접근이 가능하도록 하기 위한 처리이다.

```javascript
import { HYDRATE } from 'next-redux-wrapper';

const rootReducer = (state=INITIAL_STATE, action) => {
  switch(action.type) {
    case HYDRATE:
      return {
        ...state,
        ...action.payload
      }
    ...
    // reducer 초기화될때를 한 번 실행이 되기 때문에 default를 넣어줘야 한다.
    default:
      return state;
  }
}
```

분리된 reducer는 아래와 같이 combineReducers를 사용해서 합쳐준다.(`기존의 방식과 다른 부분은 SSR을 위해 HYDRATE 상태를 포함하는 reducer가 포함시키는 부분이다`)

```javascript
import { HYDRATE } from 'next-redux-wrapper';
import user from './user';
import post from './post';
import { combineReducers } from 'redux';

const rootReducer = combineReducers({
  index: (state = {}, action) => {
    switch (action.type) {
      case HYDRATE:
        return {
          ...state,
          ...action.payload
        };

      default:
        return state;
    }
  },
  user,
  post
});

export default rootReducer;
```

## **styled-components와 SSR**

SSR에서 styled-components를 적용하기 위해서는 별도의 설정이 필요하다.
SSR일때는 Front 서버에서 HTML을 데이터와 합쳐서 화면에 그려주게 된다.

styled-component는 SSR에 대한 별도의 설정이 없다면, styled-components가 적용이 안된 상태로 화면에 렌더링되는 것이다.

## **상태 데이터 속성 이름과 시퀄라이즈**

DB쪽에서는 시퀄라이즈를 통해 특정 정보가 또 다른 정보과 관계가 있다면, 해당 데이터들을 함쳐서 첫 문자를 대문자로 바꿔준다.
따라서 상태값 속성 중에 상태 자체로써만 사용되는 데이터 속성의 경우에는 전부 소문자 표기로 속성이름을 넣어주고, 다른 정보와 관련이 있는 속성의 경우에는 첫 문자를 대문자로 해서 넣어주도록 한다.

`front/reducers/post.js`
User, Images, Comment는 복수의 정보들을 합쳐서 주기 때문에 아래와 같이 첫 문자를 대문자 표기로 하였다. (프론트 개발시에는 서버쪽에서 어떻게 데이터를 보낼 것인지에 대해 서버 개발자에게 사전에 협의하는 것이 중요하다)

```javascript
const initialState = {
  mainPosts: [
    {
      id: 1,
      User: {
        id: 1,
        nickname: 'lee'
      },
      content: 'first content',
      Images: [
        {
          src: 'https://images.pexels.com/photos/3998365/pexels-photo-3998365.png'
        },
        {
          src: 'https://images.pexels.com/photos/12064/pexels-photo-12064.jpeg'
        },
        {
          src: 'https://images.pexels.com/photos/159775/library-la-trobe-study-students-159775.jpeg'
        }
      ],
      Comments: [
        {
          User: {
            nickname: 'lee'
          },
          content: 'comment1'
        },
        {
          User: {
            nickname: 'kim'
          },
          content: 'comment2'
        }
      ]
    }
  ],
  // 업로드된 게시글의 이미지 경로
  imagePaths: [],
  // 게시글 업로드가 완료되었을때
  postAdded: false
};
```

## <ins><b>redux-thunk</b></ins>

redux의 기능을 향상시켜주는 middleware인 redux-thunk는 redux가 비동기 액션을 dispatch할 수 있도록 도와준다.

하나의 비동기 액션에서 dispatch를 여러번 할 수 있다.
(비동기 액션에 여러 개의 동기 액션 처리 가능)

**공식 사이트 :** [https://github.com/reduxjs/redux-thunk](https://github.com/reduxjs/redux-thunk)

middleware는 3단 고차함수의 형태를 가진다.
본래 동기 액션의 경우에는 객체형태이지만, 비동기 액션의 경우에는 함수이기 때문에 지연함수로써 나중에 실행하도록 처리한다.

```javascript
// 동기 액션 처리하기 이전에 각 action의 속성을 logging해주는 middleware
// redux dev tools 대체
const loggerMiddleware =
  ({ dispatch, getState }) =>
  (next) =>
  (action) => {
    console.log(action);
    return next(action);
  };
```

로그인, 로그아웃 처리의 경우, 서버로 한 번 들렸다가 처리되기 때문에 단순 요청 액션 객체가 있는 것이 아닌, 각 각의 처리에 대해서 Request, Success, Failure에 해당하는 action이 분리해서 존재한다.

`로그인의 경우 redux-thunk 사용 예시`
thunk는 아래와 같이 dispatch를 여러번 처리할 수 있다.

```javascript
export const loginAction = (data) => {
  return (dispatch, getState) => {
    // getState는 initial state를 가져온다.
    const state = getState();
    dispatch(loginRequestAction());
    axios
      .post('/api/login')
      .then(() => {
        dispatch(loginSuccessAction());
      })
      .catch((err) => {
        dispatch(loginFailureAction(err));
      });
  };
};
```

## <ins><b>제너레이터 함수의 이해</b></ins>

redux-saga에서는 제너레이터 함수를 사용하기 때문에 우선적으로 제너레이터 함수에 대해 이해가 필요하다.

```javascript
const gen = function* () {};
// generator function은 .next()로 함수 내부를 실행시켜 줄 수 있다.
gen().next();

const gen = function* () {
  console.log(1);
  yield;
  console.log(2);
  yield;
  console.log(3);
  yield 4;
};
const generator = gen();
generator; // get {<suspended>}
generator.next(); // 1 {value: undefined, done: false}
generator.next(); // 2 {value: undefined, done: false}
generator.next(); // 3 {value: undefined, done: false}
generator.next(); // 4 {value: 4, done: false}
generator.next(); // {value: undefined, done: true}
```

제너레이터의 yield가 있는 부분은 함수의 중단점이다.
자바스크립트 함수의 특징은 몸체 부분이 전부 실행되지만, 제너레이터 함수의 경우에는 yield 지점에서 멈추고, yield 와 함께 숫자나 문자를 넣어주면 value로 반환이 된다.

함수 실행 도중에 중간에 멈추게 하기 위한 목적에서 제너레이터 함수가 사용되기도 하며, `제너레이터 함수는 테스트 시에 매우 유용`하다.

```javascript
// 절대 멈추지 않는 함수
const gen = function* () {
  while (true) {
    yield 'infinite';
  }
};

// saga에서는 무한 반복이 아니라 매번 .next()호출될때마다 중단된다.
const g = gen();
g.next(); // {value: 'infinite', done: false}
```

위와같은 특성을 이용해서 이벤트 리스너와 같은 처리를 할 수 있다.

## <ins><b>redux-saga</b></ins>

**실습 Repository :**[https://github.com/LeeHyungi0622/react-nextjs-twitter](https://github.com/LeeHyungi0622/react-nextjs-twitter)

- 처리에 대한 delay를 함으로써 비동기 처리와 같은 효과를 줄 수 있다.
- 더블 클릭에 대한 요청에 대해서 take latest처리(thunk에서는 클릭한 요청이 모두 요청된다)
- 스크롤 이벤트 리스너에 비동기 요청을 처리하는 경우, DDos 공격과 같이 무한으로 연속된 형태로 서버에 요청을 보낼 수 있다.
  이러한 문제를 redux-saga에서는 throttle과 debounce를 적용해서 1초에 3번이상 액션이 발생하면 차단하는 것과 같은 조건처리 기능을 추가할 수 있다.

  `throttle은 스크롤링 이벤트 구현시에 많이 사용`되고, `debounce의 경우에는 검색창에 검색어를 입력할때 매번 결과창이 업데이트되면 정신없을때, 입력한 검색어가 완성이 되었을때 결과창이 없데이트 될 수 있도록 처리`할때 사용한다.

- ## redux-sage 설치
  ```zsh
  $ npm i redux-saga
  # Next.js에서 redux-saga를 적용하기 위해
  $ npm i next-redux-saga
  ```

thunk와는 다르게 sagaMiddleware에는 추가적인 기능이 있다.

`configureStore.js`

```javascript
import createSagaMiddleware from 'redux-saga';

const configureStore = () => {
  const sagaMiddleware = createSagaMiddleware();
  // 개발용일때에만 devTools를 붙인다. (보안적 요소 고려)
  // history가 쌓이게 되면 메모리도 많이 잡아먹는다.
  const middlewares = [sagaMiddleware, loggerMiddleware];
  const enhancer =
    process.env.NODE_ENV === 'production'
      ? // 개발할때에는 redux-saga, thunk만 추가해준다.
        compose(applyMiddleware(...middlewares))
      : composeWithDevTools(applyMiddleware());
  const store = createStore(reducer, enhancer);
  store.sagaTask = sagaMiddleware.run(rootSaga);
  return store;
};
```

`_app.js`

```javascript
import withReduxSaga from 'next-redux-saga';
...
export default wrapper.withRedux(withReduxSaga(App));
```

`/front/sagas/index.js`

saga에는 다양한 effects (all, fork, call, put)가 존재한다.

**(1) all :** 배열 내에 작성해 준 제너레이터 함수들을 일괄 실행시켜준다.
**(2) fork :** 제너레이터 함수를 실행시켜주는 역할로 비동기 함수를 실행해준다. (response와 상관없이 요청을 보내고 바로 다음 요청을 실행한다)

**(3) call :** fork와 같이 제너레이터 함수를 실행하는 역할을 하지만 fork와는 다르다.
call은 동기 함수 실행을 담당한다.(response를 기다렸다가 다음을 실행)

**(4) take :** 첫번째 인수의 액션이 실행되면, 두번째 인수로 넣어준 제너레이터 함수가 실행된다.
take의 단점은 일회성이다. 따라서 제너레이터 함수 내에서 while(true){ }로 감싸주거나, `takeEvery를 사용`한다.
`takeLatest를 사용`해서 실수로 두 번 연달아서 클릭 이벤트가 발생했을때 마지막 이벤트만 실행될 수 있도록 처리해준다.
(`응답을 하나 취소하는 것이지 서버로의 요청은 취소가 되지 않는다`)

```javascript
function* watchLogIn() {
  yield takeLatest('LOG_IN_REQUEST', logIn);
}
```

throttle을 이용해서는 1회 요청의 제한시간을 지정해줄 수 있다.

```javascript
function* watchAddPost() {
  yield throttle('ADD_POST_REQUEST', addPost, 10000);
}
```

**(4) put :** dispatch 객체와 같은 기능

root saga를 만들어서 내부에 비동기 처리들을 일괄 작성해둔다.

thunk에서는 비동기 액션 creator를 직접 실행하지만, saga에서는 비동기 액션 creator가 event listener와 같은 느낌을 준다.

`redux-saga 예시코드`

```javascript
import { all, fork, call, put, take } from 'redux-saga/effects';
import axios from 'a';

// generator(*)
// 패턴대로 코딩을 하기 때문에 익숙해지면 된다.

// 제너레이터 함수가 아닌 일반함수이다.
function logInAPI(data) {
  return axios.post('/api/login', data);
}

// 로그인시 사용자 정보를 넘겨 줄 때에는 아래와 같이 action 매개변수를 통해 data를 받아서 넘겨준다.
function* login(action) {
  try {
    // result에 결과값이 담긴 상태로 다음 액션이 실행되야 하기 때문에 반드시 call로 작성해준다.
    // logInAPI에 action.data를 넣어준다.
    const result = yield call(logInAPI, action.data);
    yield put({
      type: 'LOG_IN_SUCCESS',
      // 성공 결과는 result.data에 담겨있다.
      data: result.data
    });
  } catch (err) {
    yield put({
      type: 'LOG_IN_FAILURE',
      // 실패 결과는 err.response.data에 담겨있다.
      data: err.response.data
    });
  }
}

function logOutAPI() {
  return axios.post('/api/logOut');
}

function* logout() {
  try {
    const result = yield call(logOutAPI);
    yield put({
      type: 'LOG_OUT_SUCCESS',
      data: result.data
    });
  } catch (err) {
    yield put({
      type: 'LOG_OUT_FAILURE',
      data: err.response.data
    });
  }
}

function addPostAPI() {
  return axios.post('/api/logOut');
}

function* addPost() {
  try {
    const result = yield call(addPostAPI);
    yield put({
      type: 'ADD_POST_SUCCESS',
      data: result.data
    });
  } catch (err) {
    yield put({
      type: 'ADD_POST_FAILURE',
      data: err.response.data
    });
  }
}

function* watchLogIn() {
  yield take('LOG_IN_REQUEST', login);
}

function* watchLogOut() {
  yield take('LOG_OUT_REQUEST', logout);
}

function* watchAddPost() {
  yield take('ADD_POST_REQUEST', addPost);
}

export default function* rootSaga() {
  yield all([fork(watchLogIn), fork(watchLogOut), fork(watchAddPost)]);
}
```

## redux-saga를 쪼개서 reducer와 연결하기

위와같이 일괄적으로 index.js에 redux-saga에 대한 코드를 작성하게 되면, 코드가 길어지게 된다.
reducer를 합칠때와 같이 combineReducer와 같은 것을 사용해서 합칠 필요가 없다.

**실습 Repository :**[https://github.com/LeeHyungi0622/react-nextjs-twitter](https://github.com/LeeHyungi0622/react-nextjs-twitter)

- ## reducer와 연결

sagas/ 폴더 이하의 파일에는 비동기 처리에 대한 코드를 작성해주고, reducer에서는 비동기 처리에 대한 각 각의 action.type에 대한 조건 처리를 추가해주면 된다.

redux-saga 관련 코드가 실행되는 흐름에 대해서 노트 필기를 해보았다. 복습할때 참고하도록 하자.

<div align="center">
  <img src="/images/post_images/210517_redux-saga-note.png" alt="Redux-saga 흐름"/>
</div>
