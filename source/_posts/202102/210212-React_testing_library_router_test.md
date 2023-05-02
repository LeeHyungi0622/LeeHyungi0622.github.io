---
title: 210212 React Router Unit Test
date: 2021-02-12 22:46:42
tags:
  - React-Jest
  - React-router-test
  - React-testing-library
  - Unit-testing
categories:
  - React-Test
---

![](/images/post_images/210212_react_testing_library.jpg)

<br/>

### <ins>**React Router Test**</ins>

이번 포스팅에서는 React router testing에 대해서 정리해보려고 한다.
React router test파일은 `<App/> component에 대한 test file을 작성`한다.

- #### 우선 기본적으로 작성할 테스트 케이스의 내용을 정리해보자.

  - **(Test case 1)** 처음 \<App/> component가 rendering될때 root path와 matching되는 Home component가 rendering되는가?
  - **(Test case 2)** \<nav> 태그로 만든 각 menu를 클릭했을때 각 각의 matching되는 component로 rendering이 되는가?
  - **(Test case 3)** route에 해당하는 path가 없는 경우에는 `404 Not Found` component가 rendering되는가?
  - **(Test case 4)** 페이지가 loading되는 동안 \<Loading /> component가 페이지가 표시되는가?

  <ins><b>Test case 4의 Loading component가 DOM에 그려지는 것을 확인하기 위해서 component를 Lazy하게 가져와야 한다.</b></ins>

<br/>
  <!-- more -->

- #### Component를 Lazy하게 가져오기

  ```javascript
  import React, { Suspense, lazy } from 'react';
  import { Route, Redirect, Switch } from 'react-router-dom';
  import Loading from './Loading';

  // page component를 가져올때 lazy하게 가져온다.
  const Home = lazy(() => import('../../pages/Home'));
  const Menu1 = lazy(() => import('../../pages/Menu1'));
  const Menu2 = lazy(() => import('../../pages/Menu2'));
  const NotFound = lazy(() => import('../../pages/NotFound'));

  // 기본적으로 component를 lazy하게 가져오기 위해서는 아래와 같이
  // Suspense의 fallback속성으로 Loading component를 넣어서
  // Switch를 감싸줘야 한다.
  <Suspense fallback={<Loading />}>
    <Switch>
      <Route exact path="/" component={Home} />
      <Route path="/menu1" component={Menu1} />
      <Route path="/menu2" component={Menu2} />
      <Route path="/notfound" component={Notfound} />
      <Redirect to="/notfound" />
    </Switch>
  </Suspense>;
  ```

### history - createMemoryHistory

createMemoryHistory() 객체를 만들어서 전이될 화면에 대한 정보를 push()를 이용해서 넣어 줄 수 있다. 준비된 history 객체는 Router의 history property로 넣어주면 자동으로 Route에서 해당 경로(path)에 맞는 component를 rendering을 해주게 되고, 결과적으로 화면에 표시된 정보를 확인할 수 있다.

### @testing-library/react - fireEvent

[https://testing-library.com/docs/dom-testing-library/api-events/](https://testing-library.com/docs/dom-testing-library/api-events/)

fireEvent는 화면상에서 Click과 같은 Event를 발생시킬때 사용된다.
이벤트를 발생시키고자 하는 `Component에 data-testid 속성으로 id를 지정`한 다음에 `test하는 component를 render시킴으로써 getByTestId로부터 해당 id에 접근을 해서 fireEvent를 통해 event를 발생`시킬 수 있다.

### wait()

wait()함수는 Promise를 객체를 반환하고 기본적으로 50ms마다 wait()에 넘겨준 callback을 실행하고 timeout은 4500ms이다. wait()에 argument로 별도로 넘겨주는 callback 함수가 없다면, 빈 함수를 실행한다.
서버와 api 통신하는 경우에는 timeout이 발생할 수 있으므로, 이 경우에는 별도로 callback함수를 넣어서 처리해준다.
testing-library의 version이 10이상인 경우에는 `waitFor()`함수를 사용하고, 이전 버전을 사용하는 경우에는 `wait()`함수를 사용하도록 하자.

### Lazy & Suspense

[https://ja.reactjs.org/docs/react-api.html#reactlazy](https://ja.reactjs.org/docs/react-api.html#reactlazy)

React.lazy()를 사용하면 동적으로 된 구성요소를 정의할 수 있다. 이렇게 정의를 하게되면, bundle의 크기가 감소하여 최초 render시에 사용되지 않는 구성요소의 로드를 지연시킬 수 있다.

Suspense를 사용하면 component가 rendering되기 전에 다른 작업이 먼저 이루어지도록 대기한다. 또한 React.lazy를 사용하여 Component를 동적으로 불러오는 것을 지원합니다.

### **Sample Test code**

```javascript
import React from 'react';
import { render, cleanup, wait, ,fireEvent } from '@testing-library/react';
import { createMemoryHistory } from 'history';
import { Route, Router, Switch } from 'react-router-dom';
import App from './App';
// page components
import Home from '../pages/Home';
import Menu1 from '../pages/Menu1';
import Menu2 from '../pages/Menu2';
import NotFound from '../pages/NotFound';

// 각 테스트가 끝난뒤에 초기화
afterEach(cleanup);

describe('<App />', () => {

  // Test case 1
  test('<App /> rendering시에 /(root)로 rendering이 되는가?', async() => {
    const { getByTestId, getByText } = render(<App />);
    const navbar = getByTestId('navbar');
    const link = getByTestId('HOME');

    // 각 각의 page componet를 lazy하게 가져오고 있기 때문에, 비동기로 wait() 처리를 해준다.
    await wait();

    expect(getByText(/Hello world/i)).toBeInTheDocument();
    expect(navbar).toContainElement(link);
  });

  // Test case 2
  test('MENU1 menu를 클릭시, MENU1 component가 화면에 rendering되는가?', async() => {
    const { getByTestId } = render(<App />);

    fireEvent.click(getByTestId('MENU1'));

    await wait();
    // menu1-wrapper id를 가진 component가 화면에 rendering되어있는지 확인
    expect(getByTestId('menu1-wrapper')).toBeInTheDocument();
  });

  // Test case 2
  test('MENU2 menu를 클릭시, MENU2 component가 화면에 rendering되는가?', async() => {
    const { getByTestId } = render(<App />);

    fireEvent.click(getByTestId('MENU2'));

    await wait();
    // menu2-wrapper id를 가진 component가 화면에 rendering되어있는지 확인
    expect(getByTestId('menu2-wrapper')).toBeInTheDocument();
  });

  // Test case 3
  test('Route에 해당되는 path가 없는 경우, /notfound component로 rendering되는가?', async() => {
    const history = createMemoryHistory();
    history.push('/not/found');

    const { getByTestId } = render(
      <Router history={history}>
        <Switch>
          <Route exact path="/" component={Home} />
          <Route path="/menu1" component={Menu1} />
          <Route path="/menu2" component={Menu2} />
          <Route component={NotFound} />
        </Switch>
      </Router>
    );

    await wait();
    expect(getByTestId('404-notfound')).toBeInTheDocument();
  });

});
```

<hr/>

`(20210213 업데이트)`

### < React router unit test code practice >

진행중인 ReactJS Side Project에서 Router에 대한 unit test code를 작성해서 테스트해보았다.

![](/images/post_images/210213_unit_test.png)

```javascript
import React from 'react';
import { render, cleanup, waitFor, fireEvent } from '@testing-library/react';
import '@testing-library/jest-dom';
import { createMemoryHistory } from 'history';
import { Route, Router, Switch } from 'react-router-dom';
import App from '../Components/App';
// Screen components
import IT from '../Screens/IT';
import Development from '../Screens/Development';
import Search from '../Screens/Search';
import NotFound from '../Screens/NotFound';

// 각 테스트가 끝난뒤에 초기화
afterEach(cleanup);

describe('Routing test in <App /> component', () => {
  // Test case 1
  test('<App /> component가 rendering될 시에 /로 rendering이 되는가?', async () => {
    const { getByText } = render(<App />);

    await waitFor(() => {
      // toBeInTheDocument는 @testing-library/jest-dom을 import해야만 사용할 수 있다.
      // rendering된 page의 text확인
      expect(getByText(/Computer\/Internet/)).toBeInTheDocument();
    });
  });

  // Test case 2
  test('Navigation Header에 세 개의 menu(it, development, search) link가 존재하는가?', async () => {
    const { getByTestId } = render(<App />);

    // 화면에 링크가 포함되어있는지 확인
    const navbar = getByTestId('navbar');
    const itLink = getByTestId('it');
    const devLink = getByTestId('development');
    const searchLink = getByTestId('search');

    await waitFor(() => {
      expect(navbar).toContainElement(itLink);
      expect(navbar).toContainElement(devLink);
      expect(navbar).toContainElement(searchLink);
    });
  });

  // Test case 3
  test('IT menu를 클릭시, IT component가 화면에 rendering되는가?', async () => {
    const { getByTestId, getByText } = render(<App />);

    fireEvent.click(getByTestId('it'));

    await waitFor(() => {
      // 화면에 it component의 wrapper class가 있는지 확인
      expect(getByTestId('it-wrapper')).toBeInTheDocument();
    });
  });

  // Test case 3-1
  test('Development menu 클릭시, Development component가 화면에 rendering되는가?', async () => {
    const { getByTestId } = render(<App />);

    fireEvent.click(getByTestId('development'));

    await waitFor(() => {
      //화면에 development component의 wrapper class가 있는지 확인
      expect(getByTestId('development-wrapper')).toBeInTheDocument();
    });
  });

  // Test case 3-2
  test('Search menu 클릭시, Search component가 화면에 rendering되는가?', async () => {
    const { getByTestId } = render(<App />);

    fireEvent.click(getByTestId('search'));

    await waitFor(() => {
      //화면에 search component의 wrapper class가 있는지 확인
      expect(getByTestId('search-wrapper')).toBeInTheDocument();
    });
  });

  // Test case 4
  test('Route에 해당되는 path가 없는 경우, NotFound component로 rendering이 되는가?', async () => {
    const history = createMemoryHistory();
    history.push('/not/found');

    const { getByTestId } = render(
      <Router history={history}>
        <Switch>
          <Route exact path="/" component={IT} />
          <Route path="/development" component={Development} />
          <Route path="/search" component={Search} />
          <Route component={NotFound} />
        </Switch>
      </Router>
    );

    await waitFor(() => {
      expect(getByTestId('404-notfound-wrapper')).toBeInTheDocument();
    });
  });
});
```
