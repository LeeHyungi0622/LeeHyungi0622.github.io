---
title: 210428 React TIL - React router, Dynamic route matching, props의 history, location, match 객체, QueryString과 URLSearchParams, render props, Switch와 exact
date: 2021-04-28 06:29:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>React Router</b></ins>

React router의 React에서의 역할과 Website 개발시에 어떤 역할을 하는지에 대한 내용을 중점으로 내용을 정리해보려고 한다.

```zsh
$ npm i react-router react-router-dom
```

react-router는 web/app에서 react-router를 사용하기 위한 기본 뼈대라고 이해하면 된다. 그리고 `react-router-dom가 우리가 웹 개발시에 실제로 사용하는 것`으로 react-router는 react-router-dom의 내부적으로 사용되는 것이다.

  <!-- more -->

## <ins><b>HashRouter, StaticRouter, BrowserRouter</b></ins>

router의 종류에는 HashRouter, StaticRouter, BrowserRouter가 있는데, BrowserRouter가 가장 많이 사용되고, StaticRouter는 주로 서버에서 사용이 되기 때문에 주로 사용되는 것은 BrowserRouter와 HashRouter이다.
이 Router들을 사용하기 위해서는 최상위 컴포넌트를 Router 태그로 감싸줘야 한다.

우리가 페이지로 만들 컴포넌트들은 Router 태그의 내부에 Route 태그로 작성을 해주게 되는데, 그 속성으로 path와 component가 있다.

Route 컴포넌트는 가상의 페이지 주소를 만들어서 component를 연결시켜준 것이다.
Link 컴포넌트에서는 react의 router만 가상으로 만들어서 알고 있는 페이지로 링크를 걸어서 이동시켜주는 것이다.

```javascript
import { Link } from 'react-router-dom';

return (
  <BrowserRouter>
    {/* 공통부분 */}
    <div>
      <Link to="/">
      <Link to="/about">
      <Link to="/project">
    </div>
    {/* 바뀌는 부분 */}
    <div>
      <Route path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/project" component={Project} />
    </div>
  </BrowserRouter>
)
```

## <ins><b>새로고침시에 페이지 표시할 수 없는 경우</b></ins>

페이지를 새로고침하게 되면, 페이지를 표시할 수 없다고 나오는데, 이는 Route 컴포넌트로 가상의 페이지 주소를 만들어서 각 컴포넌트를 연결시켜준 것이기 때문에 서버에서는 해당 주소를 찾을 수 없는 것이다. 따라서 이런 경우에는 `webpack.config.js`파일에서 아래와 같이 설정을 추가해줘야 한다.

```javascript
.....
devServer: {
  .....
  historyApiFallback: true,
}
.....
```

## <ins><b>HashRouter와 BrowserRouter의 차이</b></ins>

HashRouter를 사용하게 되면 URL에 /#/의 Hash가 표시되는 것을 볼 수 있다. 기존에 BrowserRouter를 사용하게 되면 페이지를 새로고침할 경우 별도의 설정을 해주지 않으면 페이지 새로고침이 되지 않았는데, HashRouter의 경우에는 별도의 설정을 해주지 않아도 페이지 새로고침이 문제없이 된다. 이러한 이유때문에 HashRouter를 사용하면 배포가 쉽다는 이야기가 나온다.
/#/를 포함한 이하의 주소는 react-router와 브라우저만 알고 있는 부분으로, 서버에서는 인식을 하지 못한다. 이러한 문제는 SEO(검색엔진 최적화)의 문제로 직결되는데, 검색엔진을 최적화하기 위해서 기본적으로 각 페이지에 대한 정보를 서버에 물어보기 때문이다. 위와같은 이유로 실무에서는 HashRouter를 사용하지 않고 BrowserRouter를 사용한다.
하지만 SEO와 같은 부분을 고려할 필요가 없는 관리자 페이지의 경우에는 HashRouter를 사용해서 구현을 해도 문제가 없다.
(`물론 BrowserRouter를 사용한다고 하더라도 SEO를 위해 별도의 설정이 필요하다.`)

## <ins><b>Dynamic Route Matching</b></ins>

\<Route />컴포넌트의 갯수가 늘어나게 되면, 코드의 양이 방대해지기 때문에 이 Route 컴포넌트를 동적 라우팅이 가능하도록 바꿔줘야 한다.

일단 아래와 같이 Route들이 설정되어있다고 가정한다. 만약에 프로젝트가 늘어나서 10개가 더 늘어난다고 하면, Route 컴포넌트를 10개 더 추가해야 할 것이다.
하지만 이러한 문제는 동적 라우팅으로 해결이 가능하다.

```javascript
import { Link } from 'react-router-dom';

return (
  <BrowserRouter>
    {/* 공통부분 */}
    <div>
      <Link to="/project/a">
      <Link to="/project/b">
      <Link to="/project/c">
    </div>
    {/* 바뀌는 부분 */}
    <div>
      <Route path="/project/a" component={AProject} />
      <Route path="/project/b" component={BProject} />
      <Route path="/project/c" component={CProject} />
    </div>
  </BrowserRouter>
)
```

우선 path는 아래와 같이 `/project/:name`으로 작성을 해주고 component의 속성에 작성해준 컴포넌트의 경우에도 별도로 새로 생성을 해준다. `:name`은 `/project/a`의 경우에는 a로 동적으로 바뀌는 부분이다.
동적으로 라우팅되는 원리는 Route 컴포넌트가 component 속성으로 넣어준 component에 props로 `history, location, match`속성을 넣어주는데, 이 속성들 중에서 match 속성을 이용해서 Project라는 컴포넌트내에서 분기처리를 통해 동적으로 라우팅을 해주게 되는 것이다.

```javascript
<Route path="/project/:name" component={Project} />
```

만약에 **Route와 연결이 되지 않은 컴포넌트에서 props의 history, location, match를 사용하고 싶은 경우,** 해당 컴포넌트를 내보내주는 부분에서 아래와 같이 withRouter로 감싸서 내보내주면 된다.

```javascript
import { withRouter } from 'react-router-dom';

.....

export default withRouter(Project);
```

## <ins><b>props의 history, location, match 객체</b></ins>

- ## **history** : 앞으로가기 뒤로가기 등 페이지를 넘나든 내역을 가지고 있는 객체 (goBack, goForward, push 등 프로그래밍적으로 페이지 전이가 가능하도록 메서드를 제공)

- ## **location** : pathname(url주소), search, hash의 속성을 가지고 있는 객체

- ## **match** : 동적 라우팅의 분기처리에 사용할 params의 name에 대한 속성을 가지고 있다.

```javascript
render(){
  const { match: {params: { name }}} = this.props;

  if(name === 'a') {
    return <AProject />
  } else if(name === 'b') {
    return <BProject />
  } else if(name === 'c') {
    return <CProject />
  }
}
```

- ## Browser에서 제공하는 history API
  기본적으로 브라우저에서 history API를 제공한다. `history.pushState("","","/about");`과 같이 작성을 해주게 되면, URL을 업데이트해준다.
  React에서도 내부적으로 browser의 history API를 사용한다.

## <ins><b>QueryString과 URLSearchParams</b></ins>

아래와 같이 주소에 데이터를 전달할 수 있다. 전달받은 데이터는 props의 location 객체의 search부분을 통해 참조할 수 있다. search는 아래의 주소를 예로들면, `?query=10&hello=lee`이다.
만약에 query key에 대한 값 또는 hello key에 대한 값을 참조하려면 어떻게 해야할까?
`URLSearchParams`객체로 search를 감싸서 객체를 참조하면 된다.

```javascript
<Link to="/project/a?query=10&name=lee" />;
// 맨 앞의 ? 를 제외한 문자열을 URLSearchParams 객체로 감싸준다.
let urlSearchParams = new URLSearchParams(this.props.location.search.slice(1));
console.log(urlSearchParams.get('query')); // 10
console.log(urlSearchParams.get('name')); // lee
```

## <ins><b>render props</b></ins>

만약에 Route 컴포넌트에서 부모의 props를 Route에서 연결된 컴포넌트에 전달하고자 한다면 어떻게 해야할까?

```javascript
<Route path="/project" render={(props) => <Project {...props} />} />
```

## <ins><b>Switch와 exact</b></ins>

Switch는 감싸진 Route 컴포넌트들 중에서 `일치하는 것들 중에 첫 번째만 화면에 표시`해준다.
exact는 Route 컴포넌트의 속성으로 넣어주는데, exact가 들어간 Route 컴포넌트의 경우에는 `완전히 path가 일치해야만 해당 컴포넌트를 화면에 표시`한다.
아래의 간단한 예시를 살펴보자.

아래의 예시에서 첫번째 Route에 exact가 없다면, /about의 path를 입력하면 첫번째 Route가 화면에 표시된다. 그 이유는 두 Route 모두 /(root)라는 공통분모를 가지고 있고, Switch는 일치하는 첫번째 Route의 component만 화면에 표시되기 때문이다.
그렇기 때문에 첫번째 Route(상위 주소)에 exact 속성을 넣어서 완벽하게 path가 일치하는 경우만 해당 component가 화면에 표시될 수 있도록 하면 해결할 수 있다.

```javascript
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
</Switch>
```
