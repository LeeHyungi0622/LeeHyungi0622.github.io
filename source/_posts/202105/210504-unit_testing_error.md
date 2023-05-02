---
title: 210504 Error) You should not use Link outside a <Router>
date: 2021-05-04 16:45:42
tags:
  - Unit-test
  - JavaScript
  - React
  - Installation
categories:
  - Resolved-Error
---

![](/images/post_images/error_solved_img.png)

<br/>

## \<StaticRouter>와 \<MemoryRouter>를 사용

이번에 컴포넌트 단위로 단위테스트를 하던 중에 `You should not use XXX and <withRouter> outside a <Router>`에러를 접했다.
Router의 내부에서만 사용할 수 있는 \<withRouter>와 \<Link>를 사용한 컴포넌트를 테스트를 할때에는 개별 컴포넌트만 호출해서 렌더링한 후에 테스트를 진행하기 때문에 실제로는 Router 내에 구현이 되어있다고 하더라도 위와같은 에러가 발생하게 되는 것이다.

위의 에러에 대한 해결책은 테스트 코드에서 `<MemoryRouter> 또는 <StaticRouter>`를 사용해서 테스트하려는 컴포넌트를 감싸서 렌더링해주면 된다.
`Header.test.js`

```javascript
import React from 'react';
import { fireEvent, render, screen } from '@testing-library/react';
import Header from './Header';
import { MemoryRouter } from 'react-router';



describe('header 컴포넌트 내부의 메인 메뉴관련 테스트 코드', () => {
    let header;
    beforeEach(() => {
        header = render(<MemoryRouter><Header/></MemoryRouter>)
    });

    describe('메뉴 클릭시 제대로 연결된 페이지가 렌더링되는지 확인하는 테스트 코드', () => {
        test('Movie 메뉴 클릭시 Movie page component가 렌더링된다.', () => {
            const { getByTestId } = header;
            expect(getByTestId('movie-menu')).toBeTruthy();
            fireEvent.click(getByTestId('movie-menu'));
            expect(screen.findByTestId('movie-page-container')).toBeInTheDocument;
        });
      ......
```
