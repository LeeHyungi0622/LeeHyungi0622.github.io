---
title: 210426 React with NextJS TIL - NextJ의 pages와 layout, _app.js, NextJS - Head, NextJS 전용 router, eslint 설정, NextJS v9의 새로운 기능들, front-end/back-end 서버간의 CORS 설정
date: 2021-04-26 17:24:00
tags:
  - React-Framework
  - self-development
categories:
  - NextJS
---

<div align="center">
  <img src="/images/post_images/210426_reactjs-ssr-nextjs.jpeg" alt="ReactJS with NextJS"/>
</div>

## <ins><b>NextJS 프로젝트 기본 설정</b></ins>

**next** `v9`

```zsh
$ npm i next@9
```

`package.json`

```json
"script": {
  "dev": "next",
  "build": "next build"
}
```

## <ins><b>NextJS의 js파일에서는 `import React from 'react';`구문이 필요없다.</b></ins>

그 이유는 NextJS 프레임워크가 pages 폴더내의 js파일들을 code splitting된 개별적인 page component로 만들어주기 때문이다.

## <ins><b>pages와 layout</b></ins>

React에서는 react-router로 routing 경로를 설정해줘야 했는데, NextJS는 front server를 가지고 있기 때문에 pages폴더내의 파일의 이름을 url로 자동 mapping 시켜준다.

**pages/profile.js** => `/profile`
**pages/signup.js** => `/signup`
**pages/about/lee.js** => `/about/signup`

  <!-- more -->

## <ins><b>\_app.js: page들의 공통처리를 작성</b></ins>

만약에 page들에서 공통적으로 처리하는 것이 있다면 pages/\_app.js 파일의 내부에서 작성해주도록 한다. 예를들어 모든 페이지에서 antd.css 파일을 사용한다면, 아래와같이 \_app.js파일을 작성해준다.
\_app.js는 전체 페이지 컴포넌트들의 공통적인 부분을 작성한다면, layout은 일부 페이지 컴포넌트들의 공통 부분을 처리하기 위해서 작성한다.

  <!-- more -->

```javascript
import React from 'react';
import 'antd/dist/antd.css';
import PropTypes from 'prop-types';
import Head from 'next/head';

const App = ({ Component }) => {
  return (
    <>
      <Head>
        <meta charset="utf-8" />
        <title>Main Title</title>
      </Head>
      <Component />
    </>
  );
};

App.propTypes = {
  Component: PropTypes.elementType.isRequired
};

export default App;
```

위와같이 작성해주면 pages의 하위에 작성된 javascript파일들의 return문에 있는 JSX 부분이 App 컴포넌트의 인자 Component로 들어가서 \_app.js 파일에 적용된 내용들이 공통적으로 적용된다.

## <ins><b>NextJS에서 head 부분을 수정할 수 있는 Head를 제공한다.</b></ins>

NextJS에서 html의 head section의 내용을 수정할 수 있는 Head를 제공한다.
위에 첨부한 코드와 같이 간편하게 `'import Head from next/head'`를 작성해주면 Head 부분의 meta 태그나 title 부분을 손쉽게 수정할 수 있다.

## <ins><b>NextJS 자체 router가 있다.</b></ins>

NextJS에서는 React router를 사용하지 않고, NextJS자체의 router를 사용한다.
href는 내부의 anchor tag가 아닌 Link 태그의 속성에 넣어줘야 한다.
처음 Link 태그를 클릭하고 화면이 전이되는 시간이 약간 더딘 것을 볼 수 있는데, 이는 현재 개발모드(dev mode)이기 때문이다.
배포모드(prod mode)에서는 빠르기 때문에 이 부분은 걱정하지 않아도 된다.

```javascript
import Link from 'next/link';

<Link href="/">
  <a>Home</a>
</Link>;
```

## <ins><b>NextJS에는 기본으로 react hot loader가 적용되어있다.</b></ins>

## <ins><b>SSR을 위한 서버</b></ins>

반드시 React와 NodeJS를 사용할 필요는 없다. 단지 SSR을 위해서 서버가 필요할 뿐 Sprint, Django와의 조합으로 모두 사용이 가능하다.

## <ins><b>NextJS v9의 새로운 기능</b></ins>

NextJS v9부터 `Dynamic routing`과 `API routing`기능이 새로 추가되었기 때문에 별도로 custom front-end 서버를 만들 필요가 없고 모두 NextJS 안에서 가능하다.

## <ins><b>front-end 서버와 back-end 서버간의 CORS 설정</b></ins>

브라우저와 프론트 간에는 CORS 문제가 없지만, port 번호만 달라도 CORS가 달라지기 때문에 `프론트엔드와 백엔드 서버간에는 CORS 설정이 필요`하다. 따라서 백엔드 서버에서 별도로 CORS 설정을 해줘야 되고, 프론트엔드 서버에서는 cookie를 보내는 것에 대한 with Credentials에 대한 설정을 해줘야 한다.
보통 실무에서 React - Node(Front) - Node(Back) 의 구성으로 두 개의 노드 서버를 구축해서 사용한다.
CSR에서는 Front-end 서버로부터 일괄적으로 모든 페이지에 대한 파일을 다운받는데, 이 부분이 비효율적이기 때문에 별도로 `code splitting`이 이뤄진다. code splitting의 특성상 요청한 url과 연관된 페이지에 대한 파일을 다운받기 때문에 별도로 요청을 받아 처리하는 방식으로 동작한다. (NextJS에서 알아서 처리해준다)

## <ins><b>MongoDB의 사용은 추천하지 않는다.</b></ins>

대부분의 데이터들은 서로 N:N(다대다) 방식으로 서로 연관성이 있다. 따라서 NoSQL인 MongoDB를 사용하는 것보다 SQL을 사용해서 데이터를 테이블에 관리하는 것이 효율적이다.

## <ins><b>Loading 페이지를 없애기 위한 목적으로 SSR 사용</b></ins>

CSR의 경우, 초기 화면이 로딩될때 화면에 로딩중인 화면이 표시되는 경우가 있다. 이러한 화면 처리를 없애기 위한 목적으로도 SSR을 사용하기도 한다. 서버에서는 한 번 방문한 페이지와 데이터에 대해서 캐싱을 해준다. 캐싱이 적용되면, 다음에 같은 페이지를 방문했을때 캐싱되어있는 페이지를 통으로 받아오기 때문에 훨씬 빠른 화면 로딩을 보여준다.
