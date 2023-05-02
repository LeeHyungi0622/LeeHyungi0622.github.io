---
title: 210212 React의 Router개념과 기본사용
date: 2021-02-12 07:57:42
tags:
  - ReactJS
  - React-router
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

- ## <ins><b>ReactJS Router</b></ins>

웹페이지의 링크를 클릭하면, 다른 화면으로의 전이가 일어나는데, 이러한 화면 전이와 관련된 routing은 React의 `react-router-dom`이라는 Routing package를 사용해서 정의한다.

<br/>

- ### <ins><b>React Router Installation</b></ins>

  react-router-dom은 navigation을 만들어 주기 위한 package이다.

  ```bash
  $ yarn add react-router-dom
  ```

    <!-- more -->

  <br/>

- ### <ins><b>Router.js 작성하기</b></ins>

  Router와 Route를 사용하여 path와 해당 path로 이동했을때 화면에 보여질 component를 정의한다.

  ```javascript
  import React from 'react';
  import { HashRouter as Router, Route } from 'react-router-dom';
  import Home from 'Screens/Home';

  export default () => {
    <Router>
      <Route exact path="/" component={Home} />
    </Router>;
  };
  ```

  `Switch`는 child component의 Route에 match되는 첫번째 요소를 rendering해준다. Switch를 사용하면, 하나의 match되는 Route가 rendering되는 것을 보장한다.

  ```javascript
  import React from 'react';
  import { HashRouter as Router, Route } from 'react-router-dom';
  import Home from 'Screens/Home';

  export default () => {
    <Router>
      <Switch>
        <Route path="/" exact component={Home} />
      </Switch>
    </Router>;
  };
  ```

  <br/>

  - #### <ins><b>Switch와 exact 사용의 차이</b></ins>

    그럼 `Switch`의 사용과 Route의 속성으로 `exact`으로 넣어주는 것은 어떤 차이가 있을까?

    ```javascript
    // 아래 두 경우는 모두 같은 결과를 보여준다.
    // Switch와 exact 사용
    <Switch>
      <Route exact path="/" component={Home} />
      <Route path="/a" component={A} />
      <Route path="/b" component={B} />
    </Switch>
    // exact 속성 사용
    <Route exact path="/" component={Home} />
    <Route path="/a" component={A} />
    <Route path="/b" component={B} />
    ```

    만약 nested route를 사용하지 않는 경우에는 상관이 없지만, 아래와 같이 Home component의 내부에 중첩된 형태로 Route가 사용된다면, 그리고 사용자가 /dashboard 경로로 /dashboard 페이지에 접근하려고 한다면, top level의 Route에는 /dashboard와 일치하는 Route가 없기 때문에 page를 rendering할 수 없다.

    ```javascript
    // top level Router - Route
    <Route exact path="/" component={Home} />;

    // Home component - nested routes
    const Home = (props) => (
      <>
        <Route path="/dashboard" component={Dashboard} />
        <Route path="/layout" component={Layout} />
      </>
    );
    ```

    위와같이 중첩된 Route를 포함하여 모두 확인한 뒤에 일치하는 경로의 component를 rendering해주기 위해서는 아래와 같이 `Switch` 구문을 사용해서 top-level Route를 감싸줘야 한다.

    ```javascript
    <Switch>
      <Route path="/a" component={A} />
      <Route path="/b" component={B} />
      <Route path="/" component={Home} />
    </Switch>
    ```

    `'/'(root)에 해당되는 Route를 제일 마지막에 위치시킴으로써 별도로 '/'(root) Route에 exact를 넣어주지 않았다.`

    - #### <ins>작성시 주의사항</ins>

      SPA(Single Page Application)의 특성을 가진 React는 별도의 화면 전환 없이 페이지에 표시되는 component를 다르게 함으로써 화면 전환이 되는 것 처럼 보여지게 한다.
      이러한 특징으로인해 Router내에 여러 Route를 정의할때 만약 같은 path주소를 기반으로 시작하는 Route가 복수 개 정의되어있다면, 화면에 중첩되어 표시가 된다. 이러한 문제를 해결하기 위해서 <ins><b>Route의 props에 `exact={true}` 혹은 `exact`속성을 추가해주거나 `Switch`를 사용해서 Route를 감싸서 처리한다.</b></ins>
      그러면 path주소의 기반 주소가 같더라도 path 전체와 완전히 일치하는 경우에만 해당 component를 rendering해준다.

<br/>

- ### <ins><b>HashRouter와 BrowserRouter의 차이</b></ins>

  - **BrowserRouter**

    - HTML5의 history API를 활용해서 UI를 업데이트 한다.
    - 동적인 페이지에 적합하다. (서버에 있는 데이터들을 스크립트에 의해 가공처리한 후 생성되는 웹 페이지)
    - 새로고침을 하면 경로를 찾지 못해서 에러가 발생한다.
      → 해결을 위해서 서버에 추가적인 세팅이 필요하다. 페이지의 유무를 서버에 알려줘야하며, 서버 setting시에 search engine에 신경을 써야 한다.
    - GitHub Page에 배포시에 설정이 복잡하다.

  <br>

  - **HashRouter**
    - URL의 hash를 활용한 Router이다.
    - 주소에 #이 붙는다.
    - 정적인 페이지에 적합하다. (미리 저장된 페이지가 그대로 보여지는 웹 페이지)
    - 검색 엔진으로 읽지 못한다. #값으로 인해 서버가 읽지 못하고 서버가 페이지의 유무를 알지 못한다.
    - 새로고침을 해도 에러가 발생하지 않는다.
    - GitHub Pages에서 배포시에 설정이 간편하다.

<br/>

- ### <ins><b>withRouter의 사용</b></ins>

  - Router component의 하위에 있지 않지만, router의 기능을 사용해야될때 사용된다.
  - react-router-dom은 withRouter를 제공해주는데 component를 감싸서 Router로부터 어떠한 정보를 전달받는 역할을 한다.
  - HOC(Higher-Order-Component)로 with()명명 규칙을 따르며, Component를 인자로 받아서 새로운 Component를 반환하는 함수이다.
  - 예를들어, Header component를 withRouter로 감싸주게 되면, parameter를 통해 Router의 `location`정보를 읽어올 수 있다.

  사용예시) component export구문에서 component를 withRouter()로 감싸주기

  ```javascript
  const Header = (props) => (
    <Header>
      <List>
        <Item current={props.pathname === '/'}>
          <SLink to="/">Home</SLink>
        </Item>
        <Item current={props.pathname === '/about'}>
          <SLink to="/about">About</SLink>
        </Item>
      </List>
    </Header>
  );

  export default withRouter(Header);
  ```

  사용예시) 별도의 component정의 없이 바로 export 구문에서 withRouter()로 감싸주기

  ```javascript
  export default withRouter(() => (
    <Header>
      <List>
        <Item current={props.pathname === '/'}>
          <SLink to="/">Home</SLink>
        </Item>
        <Item current={props.pathname === '/about'}>
          <SLink to="/about">About</SLink>
        </Item>
      </List>
    </Header>
  ));
  ```

  <br/>

- ### <ins><b>\<a> tag 말고 react-router-dom의 \<Link> 사용하기</b></ins>

  일반적인 웹 어플리케이션에서 특정 링크로 화면을 전이하기 위해서는 a 태그가 사용된다. 하지만 React에서는 `react-router-dom module에서 Link를 제공`한다.
  a tag를 이용한 페이지 이동과 Link를 이용한 페이지 이동의 차이는 a tag를 이용한 페이지 이동은 매번 페이지가 이동이 될때마다 기존의 React가 새 페이지와 함께 새로고침되는 현상이 일어난다.
  기존의 React의 상태를 보존시키면서 페이지를 전이시키기 위해서 Link를 이용해서 페이지를 이동시키도록 한다.
  `Link의 사용조건은 반드시 Router 내부에서 사용이 되어야 한다는 것이다.`
  그래서 Link가 사용이 된 Navigation component가 Router가 사용된 Router component내부에 종속되어 사용이 되었다.

  ```javascript
  import React from 'react';
  import { Link } from 'react-router-dom';

  function Navigation() {
    return (
      <>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </>
    );
  }

  export default Navigation;
  ```

<br/>

- ### <ins><b>Routes 사이에서 Props 공유하기</b></ins>

  페이지 전환을 할 functional component 내에서 props를 확인해보면, 아래 첨부한 캡처와 같이 `history, location, match, staticContext`등의 항목이 전달되고 있음을 확인할 수 있다.

  (history의 속성을 보면, go, goBack, goForward, push 등의 속성을 확인할 수 있는데, 이를 이용해서 페이지를 전이할 수 있다. `예) history.push("/"))`

  ![](/images/post_images/210212_router_props_capture.png)
  앞서 Link를 활용하여 페이지 이동을 할 pathname을 넣어주었는데, 단순 pathname이 아닌 <ins>**객체의 형태로 여러 정보들을 일괄적으로 함께 보내줄 수도 있다.**</ins>
  예를들어, 상세보기 페이지로 이동을 한다고 했을때, 상세보기 페이지로 넘겨 줄 정보를 Link props의 state에 객체형태로 전달해 줄 수도 있다.
  전달한 정보는 props의 location에 있는 state에 객체의 형태로 전달받을 수 있다.

  ```javascript
  <Link
    to={{
      pathname: '/',
      search: '?sort=name',
      hash: '#the-hash',
      state: { fromDashboard: true }
    }}
  />
  ```

<br/>

- ### <ins><b>Class component에서 전달받은 Props 확인하기</b></ins>

  render()가 끝난 다음에 실행되는 ComponentDidMount() method를 통해서 location.state의 데이터를 확인해 볼 수 있다.

  ```javascript
  import React from 'react';

  class DetailPage extends React.Component {
    componentDidMount() {
      const { location } = this.props;
      console.log(location.state);
    }

    render() {
      return <span>Detail page</span>;
    }
  }
  ```

<br/>
