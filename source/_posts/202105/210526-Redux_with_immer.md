---
title: 210526 immer
date: 2021-05-26 10:41:00
tags:
  - React-Framework
  - self-development
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210526_immer.png" alt="immer"/>
</div>

## <ins><b>immer</b></ins>

state의 데이터 구조가 복잡해지면 불변성을 지키기 위해 변하지 않는 값에 대해 spread 문법을 사용해서 얕은 복사를 하는 것이 지저분해질 수 있는데 immer를 사용하면 이 문제를 해결할 수 있다.

```javascript
$ npm i immer
```

Redux에서만 아니라 React의 useState, setState에서도 immer를 사용할 수 있으며,hooks를 위한 useImmer도 있다.(useState 대체 가능)

우선 Redux의 reducer 함수에서의 immer의 사용에 대해서 알아보자.

**reducer함수는** 이전 상태를 action을 통해 다음 상태로 만들어내는 역할을 한다.
이때 새로운 상태는 반드시 불변성을 지켜서 구현을 해줘야 하는데, 구조가 복잡한 객체의 불변성을 지켜서 상태값을 업데이트하는 것이 복잡한 경우에는 immer를 사용한다.

`immer의 기본 사용 형태`
기존 reducer함수의 switch-case 문을 produce의 내부에 작성해준다.

```javascript
const reducer = (state = initialState, action) => {
  return produce(state, (draft) => {});
};
```

immer를 사용하게 되면, 기존에 state로 작성했던 부분이 draft로 대체가 된다. draft는 별도의 불변성과 상관없이 작성을 해줘도 immer가 알아서 새로운 state를 불변성 지켜서 만들어준다. (`state 인자는 건들이지 말고 draft만 조작하도록 한다` - **return produce()부분이 불변성을 지킨 새로운 state 값을 반환해준다**)

  <!-- more -->

`기존 reducer`

```javascript
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case ADD_POST_REQUEST:
      return {
        ...state,
        addPostLoading: true,
        addPostDone: false,
        addPostError: null
      };
  }
};
```

`immer 적용후`
기존의 reducer에서는 state를 spread 문법으로 복사하고 변화된 항목만을 작성해주었지만, immer를 사용하게 되면, 업데이트해야하는 항목의 값만 작성해주면 된다.

```javascript
const reducer = (state = initialState, action) => {
  return produce(state, (draft) => {
      switch (action.type) {
        case ADD_POST_REQUEST:
          draft.addPostLoading = true;
          draft.addPostDone = false;
          draft.addPostError = null;
          break;
      }
  }
}
```

`immer 적용 전/후 예시`
아래의 예시는 개별 Post에 대한 Comment를 업데이트해주는 부분이다.
기존의 reducer에서는 특정 Post의 Comment를 업데이트 해주기 위해 해당 Post를 찾아서 새로운 Post객체로 생성한 뒤에 새로 생성된 Post 객체의 Comments 객체 속성에서 추가된 새로운 Comment를 추가해주고, mainPosts 객체도 새로운 mainPosts 객체를 생성해서 Comment가 새로 추가된 Post 객체를 업데이트 해주었다.

이렇게 업데이트해야하는 부분과 유지해야 되는 부분을 고려해서 매번 새로운 객체로 생성하게 되면 코드의 가독성이 좋지 않다.

하지만, immer를 사용하게 되면, 아래와 같이 `간단하게 Comment를 업데이트해 줄 Post를 찾고, 해당 포스트의 Comments 속성에 새로운 Comment만 추가`해주고, 이외에 업데이트 해야되는 속성도 개별 지정해서 업데이트를 해주면 끝난다.

```javascript
case ADD_COMMENT_SUCCESS: {
  const post = draft.mainPosts.find((v) => v.id === action.data.postId);
  post.Comments.unshift(dummyComment(action.data.content));
  draft.addCommentLoading = false;
  draft.addCommentDone = true;
  break;
  // action.data.content, postId, userId
  // 우선 넘겨받은 게시물의 id를 통해서 어떤 게시물인지 index를 통해 찾는다.
  // 바뀌는 것만 새로운 객체로 만들고 나머지 객체는 참조를 유지해줘야 한다. (불변성)
  // const postIndex = state.mainPosts.findIndex((v) => v.id === action.data.postId);
  // const post = { ...state.mainPosts[postIndex] };
  // post.Comments = [dummyComment(action.data.content), ...post.Comments];
  // const mainPosts = [...state.mainPosts];
  // mainPosts[postIndex] = post;
  // return {
  //   ...state,
  //   mainPosts,
  //   addCommentLoading: false,
  //   addCommentDone: true,
  // };
}
```

## <ins><b>SWR</b></ins>

## <ins><b>Redux Toolkit</b></ins>

Redux 사용시에 코드양이 많아지는데, 코드양을 줄여주기 위한 방법 중 하나로 Redux Toolkit이 있다.
