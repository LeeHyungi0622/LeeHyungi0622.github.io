---
title: 210429 React TIL - 프론트엔드 개발시 dummy 데이터 활용, Container - Presenter 구조, Re-rendering과 태그내에서의 인라인 스타일링, 함수형 컴포넌트에서의 리렌더링, eslint 설정
date: 2021-04-29 06:29:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>프론트엔드 개발시 dummy 데이터 활용</b></ins>

만약 백엔드가 개발이 안된 상태에서 프론트엔드 개발을 진행해야 되는 경우라면 더미 데이터를 활용해서 우선적으로 프론트엔드 개발을 하도록 한다.
예를들어 로그인 화면을 만드는데, 로그인과 로그아웃등 상황에 따른 다른 프론트 페이지를 만들어야 한다면, 이 경우에 간단하게 더미로 상태 데이터를 만들어서 화면을 만들도록 한다.
실무에서는 Redux나 Mobx를 사용해서 dummy state를 만든다.

## <ins><b>Container - Presenter 구조</b></ins>

이전에 클래스 컴포넌트로 작성을 했을때는 데이터를 받아서 로직을 처리하는 부분(Container)과 화면에 출력해주는 부분(Presenter)을 나눠서 구조를 짰는데, hooks가 도입된 이후에는 위와같이 구조를 나누는 것을 추천하지 않는다고 한다.

## <ins><b>반복되는 작업은 처음만 수작업으로, 그 다음부터는 라이브러리 사용</b></ins>

수작업으로 만드는 하나 하나 만들어보는 것도 의미가 있다. 하지만 매번 하나 하나 만들어보는 것보다는 라이브러리를 사용해보는 연습을 하는 것도 중요하다. 실제 실무에서 모든 것을 수작업으로 만들지는 않기 때문이다.

## <ins><b>자식 component에 props로 넘겨주는 함수의 경우에는 반드시 useCallback으로 감싸서 처리한다.</b></ins>

## <ins><b>Re-rendering과 태그내에서의 인라인 스타일링</b></ins>

React에서는 VirtualDOM을 검사해서 이전 VirtualDOM 상태와 달라지는 부분이 있다면 해당 부분만 다시 렌더링한다. 렌더링을 최적화 시키기 위해서는 `태그 내에서 인라인 스타일링`하는 것을 지양해야 한다. 왜냐하면 태그내에서 스타일링을 해주는 경우 `스타일을 객체 형태로 정의`하게 되는데, 객체는 주소참조로, 값이 같더라도 서로 비교를 하면 다른 값으로 인식된다.
따라서 styled-components를 사용해서 스타일을 적용한 개별 컴포넌트로 생성을 하거나 `useMemo`를 사용해서 style을 별도로 정의해서 인라인 스타일로 정의해야 한다. useMemo는 값 자체를 캐싱하기 때문에 deps에서 설정한 값이 바뀌지 않는 한 재사용을 한다.

```javascript
import styled from 'styled-components';

const wrapper = styled.div`
  margin-top: 10px;
`;
```

```javascript
const style = useMemo(() => ({ marginTop: 10 }), []);
<div style={ style }>
```

  <!-- more -->

## <ins><b>함수형 컴포넌트에서의 리렌더링</b></ins>

함수형 컴포넌트는 렌더링 될때 함수 안의 내용들이 다시 실행되는 것은 맞지만, useCallback, useMemo로 처리된 함수나 값은 deps의 값이 바뀌지 않는 이상, 다시 실행되지 않고, return 부분에 정의된 VirtualDOM 중에서도 이전과 비교했을때 바뀐 부분만 다시 렌더링한다.

## <ins><b>실무에서의 components 폴더 내부 구조</b></ins>

실무에서는 components 폴더내에 바로 컴포넌트 파일들을 넣지 않고, layout, forms 폴더로 나눠서 구성한다.

## <ins><b>component의 props type 검사</b></ins>

```zsh
$ npm i prop-types
```

```javascript
import PropTypes from 'prop-types';

const AppLayout = ({ children }) => {
  ...
}

AppLayout.propTypes = {
  // node: 화면에 그려지는 모든 것 (return 내)
  children: PropTypes.node.isRequired
};

export default AppLayout;
```

## <ins><b>eslint 설정</b></ins>

여러사람들이 한 팀을 이뤄서 코드를 작성할때에는 코드의 공통 규칙을 정해두는 것이 좋다.
그래야 마치 한 사람이 코드를 작성한 것처럼 코드가 깔끔해진다.
아래의 .eslintrc를 추가해준 다음에 IDE에서 별도의 설정을 해줘야 한다.
extends 부분이 구체적인 규칙을 설정하는 부분인데 위의 설정은 느슨하게 규제를 하였다. 필요에 따라 엄격하게 바꿔서 사용해보도록 하자.

- ## eslint 관련 package 설치
  ```zsh
  $ npm i eslint -D
  $ npm i eslint-plugin-import -D
  $ npm i eslint-plugin-react -D
  $ npm i eslint-plugin-react-hooks -D
  ```
- ## eslint 설정파일

  `.eslintrc`

  ```json
  {
    "parserOptions": {
      "ecmaVersion": 2020,
      "sourceType": "module",
      "ecmaFeatures": {
        "jsx": true
      }
    },
    "env": {
      "browser": true,
      "node": true,
      "es6": true
    },
    "extends": ["eslint:recommended", "plugin:react/recommended"],
    "plugins": ["import", "react-hooks"]
  }
  ```
