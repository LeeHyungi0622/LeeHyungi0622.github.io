---
title: 210505 screen.getBy*와 getBy의 차이, findBy*/getBy*/queryBy* method의 차이
date: 2021-05-05 10:41:42
tags:
  - Unit-test
  - JavaScript
  - React
  - Installation
categories:
  - Resolved-Error
---

![](/images/post_images/210505_react_testing_library.png)

<br/>

테스트 코드를 작성하던 도중에 헷갈렸던 개념들에 대해서 반복학습을 위해 포스팅을 해두려고 한다.

## <ins><b>screen.getBy vs getBy</b></ins>

screen을 사용하는 이점은 필요한 query를 추가할 때 더 이상 render 함수로 컴포넌트를 렌더링해서 최신 상태를 유지 하지 않아도 된다는 점이다.
beforeEach()함수 내에서 공통적으로 컴포넌트를 렌더링했다면, 이하의 test 블럭에서는 screen을 통해 렌더링된 컴포넌트를 통해 테스트 코드를 작성할 수 있다.

## <ins><b>findBy\* / getBy\* / queryBy\* method의 차이</b></ins>

- ## getBy\*

  조건에 맞는 요소를 찾았을때 **query에 일치하는 노드를 반환**
  조건에 맞는 요소를 못 찾았을때 **에러를 반환**
  → 존재하지 않는 DOM element를 assert할때에는 try-catch문으로 발생한 에러를 별도의 함수에 담아 tobedefined() matcher로 확인을 해야 한다. 이 경우에는 queryBy\*를 사용해서 assert하는 것이 좋다.

- ## queryBy\*

  조건에 맞는 요소를 찾았을때 **query에 일치하는 노드를 반환**
  조건에 맞는 요소를 못 찾았을때 **null을 반환**
  → 존재하지 않는 DOM element를 assert할때 유용하다.

- ## findBy\*

  조건에 맞는 요소를 찾았을때 **resolved Promise 반환**
  조건에 맞는 요소를 못 찾았을때 **rejected Promise 반환**

  기존의 waitForElement()함수는 이제 deprecated되었기 때문에 findBy\* 함수를 사용해야 한다.

  ```javascript
  test('nowPlaying API method 테스트 - 데이터가 문제없이 화면에 로드되는지 확인', async () => {
    const { findByTestId } = render(<Movie />);

    await findByTestId('loading-text');
    await findByTestId('contents-list');
  });
  ```
