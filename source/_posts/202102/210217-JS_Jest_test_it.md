---
title: 210217 Jest의 test, it, describe
date: 2021-02-17 10:07:42
tags:
  - Jest
  - JavaScript-unit-test
categories:
  - JavaScript-Test
---

![](/images/post_images/jest_logo.png)

<br/>

이번 포스팅에서는 Jest에서 테스트 코드를 작성할때 사용하는 `test와 it 키워드`의 사용과 여러 개의 테스트 케이스를 묶을때 사용하는 `describe 키워드`에 대해서 정리를 해보겠다.

## **test와 it 키워드의 사용**

이전 포스팅에서 test라는 키워드를 사용해서 테스트 케이스를 작성해보았다. 이 키워드를 대체해서 it이라는 키워드도 사용될 수 있는데 작동 방식은 같다.

```javascript
// test keyword를 사용한 테스트 코드 작성
const { test, expect } = require('@jest/globals');
const sum = require('../sum');

// 작성한 테스트코드가 무엇을 하는지에 대해서 첫번째 parameter로 작성해준다.
test('properly adds two numbers', () => {
  // expected result
  expect(sum(1, 2)).toBe(3);
});

// it keyword를 사용한 테스트 코드 작성
it('properly adds two numbers', () => {
  expect(sum(1, 2)).toBe(3);
});
```

  <!-- more -->

<br/>

## **describe 키워드의 사용**

describe keyword를 사용해서 작은 단위의 테스트 코드를 그룹화할 수 있다.
아래의 테스트 코드는 실제 side project를 하면서 작성했던 component의 rendering 및 routing test를 위해 작성했던 코드이다.
이처럼 작성하는 테스트 코드가 특정 component에 속하는 테스트 코드라면 아래와같이 describe 키워드를 사용해서 해당 테스트 코드들을 그룹화하는 것이 좋다. 이렇게 그룹화를 해주게 되면, 나중에 테스트 결과를 확인시 좀 더 가시적으로 보기 편하게 테스트 케이스들을 확인할 수 있다.

```javascript
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
  ......(생략)......
```

<br/>

## **코드 리팩토링(Code Refactoring)**

만약에 기존의 소스코드를 리팩토링했다고 가정하자. 코드는 바뀌었지만, 결과적으로 output은 같기 때문에 리팩토링을 한 뒤에 <ins><b>기존의 테스트 코드를 실행시켜서 결과값이 같은지 확인을 하면 쉽게 코드가 제대로 작동을 하는지 검증을 할 수 있다. </b></ins>
이처럼 테스트 코드의 작성은 코드 리팩토링시에도 빠른 코드 검증을 가능하게 해준다.
