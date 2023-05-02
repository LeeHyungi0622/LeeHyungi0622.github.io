---
title: 210206 ReactJS의 개념과 기본사용
date: 2021-02-06 15:53:42
tags:
  - ReactJS
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

- # ReactJS

NPM에서 React library를 살펴보면, 매주 400만건 이상의 유저가 React Library를 다운받고 있고, Facebook에 의해서 관리가 되고 있다. React를 가장 좋은 기술로 만들기 위해 Facebook에서 전적으로 자원과 커뮤니티, 서포트를 아끼지 않고 있다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210206_front_end_library_ranking.png" alt="front_end_library_ranking"/>
    </td>
    <td>
      <img src="/images/post_images/210206_front_end_library_usage_ranking.png" alt="front_end_library_usage_ranking"/>
    </td>
  </tr> 
</table>

Front-end 개발의 생태계를 보면, 71.7%의 사용자가 이미 React를 이용했고, 다른 비슷한 프레임워크와 비교를 했을때 압도적으로 높은 수치의 사용자가 사용하고 있다.

  <!-- more -->

요즘 회사들의 대부분이 React를 사용하고 있고, Netflix, Slack과 같은 대형 회사들 또한 React를 사용하고 있다.
React만을 위해 존재하는 것은 모두 JavaScript로 구성되어 있기 때문에 쉽게 배울 수 있다는 장점이 있다.

기본적으로 browser는 React의 코드를 이해할 수 없기 때문에 `web pack, babel`을 이용해서 React code를 compile한 뒤에 비로소 browser가 이해를 할 수 있다.

<br/>

- # **ReactJS 프로젝트 생성**

<i>방법1)</i> **local환경에 create-react-app 설치후 ReactJS 프로젝트 생성**

```bash
# create-react-app 설치
$ npm i create-react-app

# create-react-app를 사용하여 새로운 ReactJS 프로젝트 생성
$ create-react-app [Project_folder_name]
```

<i>방법2)</i> **별도로 create-react-app를 local환경에 설치하지 않고, npx를 사용해서 가장 최신버전의 create-react-app로 ReactJS 프로젝트 생성**

```bash
# create-react-app를 사용하여 새로운 ReactJS 프로젝트 생성
$ npx create-react-app [Project_folder_name]
```

<table>
  <tr>
    <td>
      <img src="/images/post_images/210206_react_start.png" alt="front_end_library_ranking"/>
    </td>
    <td>
      <img src="/images/post_images/210206_react_start_terminal.png" alt="front_end_library_ranking"/>
    </td>
  </tr>
</table>

설치가 끝난 뒤에 terminal에서 `$ npm start` 해주면 아래와 같이 default 3000 port로 ReactJS 어플리케이션이 활성화되는 것을 확인할 수 있다. Network로 할당되는 별도의 IP주소로 휴대폰을 이용해서도 접속이 가능하다.

<br/>

- # **ReactJS 프로젝트의 동작원리**

ReactJS의 프로젝트의 동작원리를 내부 파일들을 분석해서 알아보자.
우선 public>index.html 파일 내부의 `<body>태그`를 보면, 아래와 같이 `id가 root인 <div>태그`를 찾을 수 있다.

**public/index.html**

```html
<body>
  <noscript>You need to enable JavaScript to run this app.</noscript>
  <div id="root"></div>
  <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.

      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.

      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
</body>
```

이제 src>index.js파일 내부를 보면 아래와 같이 `"root"라는 id를 가진 요소`에 `<App/> component를 rendering`해주고 있음을 알 수 있다.

**src/index.js**

```javascript
ReactDOM.render(<App />, document.getElementById('root'));
```

<App/> component의 내용은 src>App.js 파일의 내부에 정의가 되어있다.

**src/App.js**

```javascript
import React from 'react';

function App() {
  return (
    <div>
      <h1>Hello!!!!!</h1>
    </div>
  );
}

export default App;
```

위와같이 기존의 `<App/> component의 반환부분에 <h1>태그로 Hello!!!!! 문자를 넣어주면` 아래와 같이 웹 페이지에 해당 문자열이 출력됨을 알 수 있다.

![](/images/post_images/210206_react_start_hello_webscreenshot.png)

이처럼 React는 component로 분리되서 어플리케이션이 구성되어 있으며, 처음부터 화면에 보여질 HTML 코드를 넣지 않고, Virtual DOM(Virtual Document Object Mode) 개념으로 실제로 소스코드 상에는 존재 하지 않지만, 화면이 rendering될때 component로 작성된 부분이 화면에 보여지게 된다.

chrome browser에서 inspection을 사용해서 화면의 요소검사를 하면 실제 화면에 존재하는 요소로 나오지만, 실제 코드를 보면 실제 화면에 표시되는 요소들은 나오지 않는다.

**자 그럼 앞의 설명에서 자주 언급된 Component에 대한 개념을 정리해보자.**

<br/>

- # **Component?**

component는 HTML을 반환하는 함수라고 이해하면 된다. 앞에서 살펴보았던 <App/> component의 내부에서 실제 화면에 보여질 html코드를 가지고 있었던 것처럼 Component는 HTML코드를 반환하는 형태를 하고 있다.

이처럼 React는 화면에 보여질 각 구성요소들을 Component단위로 쪼개서 재사용이 가능한 형태로 화면을 구성한다.

모든 Component 파일은 `import React from "react"`를 포함해야하며,
React에서는 오직 한 번에 하나의 Component만을 rendering할 수 있다. 따라서 반환해주는 부분(return)에서 jsx코드를 하나의 객체로 묶어서 반환해줘야 한다.`(<></> fragment로 묶어주기)`

<br/>

- # **JSX?**

React에서는 이처럼 JavaScript 코드 내부에서 HTML을 혼합해서 사용을 하는데 이러한 코드를 jsx코드로 정의한다.

- ## **JSX의 특징**

정의된 Component에는 아래와 같이 Props라는 형태로 데이터를 전달할 수 있다.

```javascript
function NewComponent(props) {
  console.log(props);
  return <h1>NewComponent Message</h1>;
}
/**
props : 
{fmessage: "message1", smessage: true, tmessage: Array(3)}
*/

function App() {
  return (
    <div>
      <h1>App h1 message</h1>
      <NewComponent
        fmessage="message1"
        smessage={true}
        tmessage={['hello', 7, true]}
      />
    </div>
  );
}
```

Component에서 선언한 속성을 아래와 같이 **parameter부분에서 destructuring해서** 필요한 props 요소만 가져다가 사용할 수도 있다.

```javascript
function NewComponent({ fmessage }) {
  return <h1>NewComponent Message : {fmessage}</h1>;
}
```

Component를 활용해서 동적 데이터를 처리할때에도 `JSX + Props`의 특성을 활용하면 쉽게 처리할 수 있다.
