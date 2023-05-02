---
title: 210525 React eslint 설정, shortid
date: 2021-05-25 10:58:00
tags:
  - eslint
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210525_react-eslint.png" alt=" React eslint 설정" />
</div>

## <ins><b>React ESLint</b></ins>

ESLint는 자바스크립트 문법 중에 에러가 있는 곳에 표시를 해주는 도구이다. 개발자가 직접 정의한 내용대로 코드를 점검하고 에러가 있으면 코드에 직접 표시를 해주기 때문에 매우 편리하며, 문법 에러뿐만 아니라 다른 개발자들과 협업을 할때 코딩 스타일을 정할 수 있어 매우 유용하다.

   <!-- more -->

```zsh
$ npm i -D babel-eslint eslint-config-airbnb eslint-plugin-import eslint-plugin-react-hooks
eslint-plugin-jsx-a11y
```

`.eslintrc`

```json
{
  // 기본 eslint가 코드를 해석하지 않고,
  // babel-eslint가 코드를 해석하기 때문에
  // 최신 문법도 에러를 발생시키지 않는다.
  "parser": "babel-eslint",
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
  // recommend는 코드에 대한 규제가 약하기 때문에 airbnb
  "extends": ["airbnb"],
  "plugins": ["import", "react-hooks"],
  "rules": {
    "jsx-a11y/label-has-associated-control": "off",
    "jsx-a11y/anchor-is-valid": "off",
    "no-console": "off",
    "no-underscore-dangle": "off",
    "react/forbid-prop-types": "off",
    "react/jsx-filename-extension": "off",
    "react/jsx-one-expression-per-line": "off",
    "object-curly-newline": "off",
    "linebreak-style": "off"
  }
}
```

## <ins><b>shortid로 랜덤한 id 만들어주기(dummy data)</b></ins>

**설치**

```zsh
$ npm i shortid
```

**사용**

```javascript
import shortId from 'shortid';

shortId.generate();
```

## <ins><b>faker</b></ins>

개발할때 dummy로 넣어서 사용할 데이터 제공해준다.

## <ins><b>분리된 reducer에서 서로 종속관계에 있는 데이터를 업데이트하는 방법</b></ins>

상태 데이터를 변경하기 위해서는 action을 통해서 변경할 수 있다. 따라서 reducer로 분리는 되어있지만 각 상태 데이터 내에 서로 종속관계에 있는 데이터를 포함하고 있다면, 이런 경우에는 이에 맞는 새로운 action을 추가해주면 된다.
