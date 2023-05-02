---
title: 210505 RTL(React Testing Library)를 활용한 테스트 (+Axios Mockup 테스트)
date: 2021-05-05 11:03:42
tags:
  - React-Jest
  - API-Testing
  - React-testing-library
categories:
  - React-Test
---

## **게시물 업데이트**

`2021/02/09` : **RTL을 활용한 테스트 (+ Axios Mockup 테스트)**
`2021/05/05`: **Axios Mockup 테스트 추가 포스팅**

<br/>

![](/images/post_images/210209_react_testing_library.png)

<br/>

**실습 Repository :**[https://github.com/LeeHyungi0622/react_testing_library_mock_axios_request_practice_repo](https://github.com/LeeHyungi0622/react_testing_library_mock_axios_request_practice_repo)

### <ins>**React Testing Library 설치**</ins>

```bash
$ npm install --save-dev @testing-library/react
```

  <!-- more -->

<br/>

### (1) <ins>**react-testing-library에서 지원하는 render()function을 이용해서 테스트하고자 하는 component의 rendering 테스트하기**</ins>

```javascript
import { render } from "@testing-library/react";
import Fetch from "./Fetch";
....
const { getByTestId } = render(<Fetch url={url} />);
```

<br/>

### (2) <ins><b>rendering한 component의 요소에 `data-testid`를 지정해서 해당요소를 검사할 수 있다. </b></ins>

```javascript
import '@testing-library/jest-dom/extend-expect';

it('fetches and displays data', async () => {
  const url = '/greeting';
  // Fetch component를 rendering하게 되면 rendering된 객체를 확인할 수 있다.
  // rendering된 객체 중에 속성으로 data-testid로 id를 지정해준 부분을 체크할 수 있다.
  const { getByTestId } = render(<Fetch url={url} />);
  expect(getByTestId('loading')).toHaveTextContent('Loading data...');
});
```

<br/>

### (3) <ins><b>각 테스트 단계가 끝나면 테스트 상태를 초기화 시키기 위해서 `afterEach(cleanup)`를 사용한다. (cleanup은 react-testing-library에서 지원한다)</b></ins>

```javascript
import { cleanup } from '@testing-library/react';

afterEach(cleanup);
```

<br/>

### (4) <ins>**component에 전달할 props를 직접 작성한 뒤에 주입해서 테스트할 수 있다.**</ins>

```javascript
it('fetches and displays data', async () => {
  // Fetch component에 전달할 url props
  const url = '/greeting';
  const { getByTestId } = render(<Fetch url={url} />);
  expect(getByTestId('loading')).toHaveTextContent('Loading data...');
});
```

<br/>

### (5) <ins><b>방법1) nodemodules를 mocking - Axios를 통한 데이터 처리를 테스트할때 실제 HTTP를 통한 데이터 취득은 이루어지지 않는다. 그 이유는 실제 서버로부터 데이터를 취득하면 시간도 상대적으로 오래 걸리는 경우도 있기 때문이다.</b></ins>

**따라서 Axios를 통한 데이터 취득을 할때, 실제 서버로부터 취득하는 데이터를 mockup해서 테스트를 진행한다.**

- Step1) src 폴더 아래에 \_\_mocks\_\_ 폴더를 만들고, axios module의 mockup을 위해 `axios.js`파일을 생성한다.

**axios.js**

```javascript
export default {
  // axios get() mockup function
  get: jest.fn().mockResolvedValue({ data: {} })
};
```

이렇게 **mocks** 폴더 내부에서 mockup axios 파일을 만들어주면, 테스트 파일내에서 axios module을 호출할때, 실제 axios module이 아닌, `__mocks__` 폴더내에서 mockup한 axios module을 호출하게 된다.

<br/>

테스트 파일에서 이 mockup한 axios module을 override해서 값을 새롭게 정의한 다음에 새롭게 customize된 값을 가진 axios module을 이용해서 테스트를 진행할 수 있다.

```javascript
import axiosMock from 'axios';

axiosMock.get.mockResolvedValueOnce({ data: { greeting: 'hello world' } });
```

`sample code`
출처 : [https://www.leighhalliday.com/mocking-axios-in-jest-testing-async-functions](https://www.leighhalliday.com/mocking-axios-in-jest-testing-async-functions)

```javascript
import mockAxios from 'axios';
import unsplash from '../unsplash';

it('fetches data from unsplash', async () => {
  // setup
  mockAxios.get.mockImplementationOnce(() =>
    Promise.resolve({
      data: { results: ['cat.jpg'] }
    })
  );

  // work
  const images = await unsplash('cats');

  // expect
  expect(images).toEqual(['cat.jpg']);
  expect(mockAxios.get).toHaveBeenCalledTimes(1);
  expect(mockAxios.get).toHaveBeenCalledWith(
    'https://api.unsplash.com/search/photos',
    {
      params: {
        client_id: process.env.REACT_APP_UNSPLASH_TOKEN,
        query: 'cats'
      }
    }
  );
});
```

<br/>

### (6) <ins><b>비동기 처리부분을 테스트하는 경우, 해당 부분이 화면에 rendering되는 것을 기다려줘야 한다.</b></ins> (`waitForElement()`은 deprecated되었기 때문에 `waitFor()`로 대체 사용한다)

```javascript
// 실제 데이터가 넘어갔을때 화면에 보여지는 tag 부분에 "resolved" id를 삽입하였다.
const resolvedSpan = await waitFor(() => getByTestId('resolved'));

// 이제 mockup으로 넣어준 데이터가 테스트 component의 span tag에 제대로 출력이 되는지 요소검사를 한다.
expect(resolvedSpan).toHaveTextContent('hello world');
```

<br/>

### (7) <ins><b>mockup axios function이 제대로 호출되고 있는지 확인한다.</b></ins>

```javascript
// axiosMock.get mockup function이 한 번 호출되었는지 체크
expect(axiosMock.get).toHaveBeenCalledTimes(1);
// axiosMock.get이 호출될때 같이 넘겨준 argument를 체크
// axios.get() function의 argument로 지정 url을 넘겨주었는지 체크
expect(axiosMock.get).toHaveBeenCalledWith(url);
```

### (8) <ins><b>`TypeError: _axios.default.create is not a function` 에러가 발생</b></ins>

axios.create로 axios 인스턴스를 생성해서 사용하는 경우에는 `axios-mock-adapter`라이브러리를 사용해서 mocking을 하도록 하자.

### (9) <ins><b>방법2) axios-mock-adpater를 이용한 axios mocking</b></ins>

앞서 실습해 본 방법말고 이번에는 axios-mock-adapter를 이용해서 axios mocking을 해보려고 한다. mocking을 하게 되면 실제로 요청은 발생하지 않지만 마치 발생한 것처럼 작동하게

[https://github.com/ctimmerm/axios-mock-adapter](https://github.com/ctimmerm/axios-mock-adapter)

실제 프로젝트를 진행하면서 작성한 코드를 첨부한다.
MockAdapter 인스턴스를 생성할때의 첫 번째 인자로는 axios 라이브러리를 넣어주고, 두번째 인자로는 가짜로 200ms delay 시간을 설정해준다.

`Api.test.js`

```javascript
import React from 'react';
import '@testing-library/jest-dom';
import { render, waitFor, screen } from '@testing-library/react';
import 'regenerator-runtime/runtime';
import MockAdapter from 'axios-mock-adapter';
import axios from 'axios';
import Movie from './Pages/Movie';
import { MemoryRouter } from 'react-router';

describe('Movie 컨텐츠 관련 API 테스트', () => {
  const mock = new MockAdapter(axios, { delayResponse: 200 });
  const data = {
    adult: false,
    backdrop_path: '/9yBVqNruk6Ykrwc32qrK2TIE5xw.jpg',
    genre_ids: [14, 28, 12, 878, 53],
    id: 460465,
    original_language: 'en',
    original_title: 'Mortal Kombat',
    overview: 'Washed-up MMA fighter Cole Young,',
    popularity: 6382.461,
    poster_path: '/xGuOF1T3WmPsAcQEQJfnG7Ud9f8.jpg',
    release_date: '2021-04-07',
    title: 'Mortal Kombat',
    video: false,
    vote_average: 7.8,
    vote_count: 2004
  };

  mock.onGet('https://api.themoviedb.org/3/movie/now_playing').reply(200, {
    data
  });
  test('nowPlaying API method 테스트 - 데이터가 문제없이 화면에 로드되는지 확인', async () => {
    render(
      <MemoryRouter>
        <Movie />
      </MemoryRouter>
    );
    // callback 함수 안의 함수가 에러를 발생시키지 않을 때까지 대기
    await waitFor(() => screen.findByTestId('loading-text'));
    // callback 함수 안의 함수가 에러를 발생시키지 않을 때까지 대기
    await waitFor(() => screen.findByTestId('section-title'));
  });
});
```

- ## reset과 restore 사용
  mock instance에는 reset과 restore이라는 함수를 제공한다. reset은 mock handler를 제거하는 목적으로 사용되며, 테스트 케이스별로 다른 mock 설정을 하고 싶은 경우 이 함수가 사용된다.
  restore은 axios에서 완전히 mocking 기능을 제거를 하며, 테스트 도중에 요청을 실제로 날릴때 사용된다.
