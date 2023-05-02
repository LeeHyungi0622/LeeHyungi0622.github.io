---
title: 210306 React TDD Practice Mini Project
date: 2021-03-06 14:13:42
tags:
  - React-router-test
  - React-testing-library
  - Unit-testing
  - Jest
  - TDD
categories:
  - React-Test
---

![](/images/post_images/tdd_in_react_img.png)

<br/>

### <ins>**TDD방식으로 React 개발하기 위한 연습 프로젝트**</ins>

## <ins><b>test의 필요성</b></ins>

1. <ins><b>documentation (문서화 작업)</b></ins>
   테스트 코드는 코드가 어떻게 동작을 해야 되는지에 대한 정의를 하고 있다.

2. <ins><b>consistency (지속성)</b></ins>
   Software developer들이 팀을 위한 최선의 practices와 conventions을 따를 수 있게 도와준다.

3. <ins><b>Confidence (자신감)</b></ins>
   warm blanket 코드를 작성할때 좀 더 자신감 있게 코드를 작성할 수 있도록 도와준다.

4. <ins><b>Productivity (생산성)</b></ins>
   테스트는 높은 품질의 코드를 빠르게 작성할 수 있도록 도와준다

  <!-- more -->

## <ins><b>React에서의 단위 테스트</b></ins>

React환경에서 어떤 동작을 검증해야 하는지에 대해서 생각해보면, 아래 네 가지 동작에 대해서 검증이 필요하다.

- ### Testing basic component rendering
- ### Testing props
  - #### PropTypes를 이용해서 유효성 검사
    - component간에 주고 받는 props를 체크하여 사전에 발생하는 버그를 예방할 수 있다.
    - props관련하여 상세한 Type을 정의하여 코드의 가독성을 향상시킬 수 있다.
- ### Testing state
- ### Testing event handlers

## <ins><b>test의 종류 (Types of tests)</b></ins>

- ### End-to-end

  실제 사람이 개발된 어플리케이션에서 performing tasks를 수행한다.
  가장 복잡하고 느린 테스트 방식이다. (많이 수행되지 않는 테스트 방식이다)

- ### Integration

  하나의 컴포넌트가 다른 여러 컴포넌트들과 연관되어 있을때 테스트하는 방식이다.

- ### Unit

  개별 function과 component에 대한 기능 확인 테스트를 하는 방식이다.

- ### Static (type checking) (highly recommended)
  코드를 작성하는 동안 오타와 에러를 잡아내는 테스트 방식이다.

## <ins><b>Jest vs Enzyme vs React-Testing-Library</b></ins>

<table>
  <tr align="center">
    <td width="33%"><ins><b>Jest</b></ins></td>
    <td width="33%"><ins><b>react-testing-library</b></ins></td>
    <td width="33%"><ins><b>Enzyme</b></ins></td>
  </tr>
  <tr align="center">
    <td></td>
    <td align="left">
    개발자가 사용자의 입장에 놓일 수 있게 해준다. <br/>
    (사용자가 해당 component와 어떻게 상호작용을 하는지 정의)
    </td>
    <td align="left">
    React 개발자들이 React components를 테스트 할 수 있게 도와준다.
    </td>
  </tr>
  <tr align="center">
    <td>
    Jest는 Facebook에서 Jasmine을 기반으로 개발한 <b>JavaScript Test Framework</b>이다.
    </td>
    <td>
    <b>light library module</b>로써, refactoring시에 테스트 코드가 깨지는 것을 방지하기 위해 <b>구성 요소의 기능을 테스트하기 위한 코드 작성</b>을 권고한다.
    </td>
    <td>
    Airbnb에서 개발한 <b>React를 위한 JavaScript Testing utility</b> 이다.
    </td>
  </tr>
  <tr align="center">
    <td></td>
    <td>
    state, props를 체크하지 않고, 사용자에게 보여지는 dom 객체를 이용하여 검증한다.
    </td>
    <td>
    </td>
  </tr>
  <tr align="center">
    <td></td>
    <td>
    render 메소드만 제공된다.
    </td>
    <td align="left">
    mount, shallow와 같은 rendering method를 제공한다.<br/> 
    - <b>shallow</b> : 자기 자신만 렌더링하므로 독립적으로 특정 component만 독립적으로 테스트할 수 있다.<br/>
    - <b>mount</b> : 관련 components를 모두 rendering한다. component간의 관계를 테스트할 수 있다. <br/>
    - <b>render</b> : component를 static한 html로 rendering한다.
    </td>
  </tr>
</table>

`2021/03/07 update`

<ins><b>Issue</b></ins>

```text
You want to write maintainable tests for your React components. As a part of
this goal, you want your tests to avoid including implementation details of your
components and rather focus on making your tests give you the confidence for
which they are intended.
As part of this, you want your testbase to be maintainable in the long run so
refactors of your components (changes to implementation but not functionality)
don't break your tests and slow you and your team down.
```

반응 구성 요소에 대한 유지 관리 가능한 테스트를 작성하려고 합니다. 이 목표의 일부로, 구성요소의 구현 세부 정보를 포함하지 않고 구성요소가 의도한 대로 신뢰할 수 있도록 테스트에 초점을 맞추려고 합니다.
그 일환으로 테스트 기반을 장기적으로 유지할 수 있기를 원하므로 구성 요소의 리팩터(구현에 대한 변경 사항이 있지만 기능이 아님)가 테스트를 중단하지 않고 사용자와 팀의 작업 속도를 저하시키지 않습니다.

<ins><b>Solution</b></ins>

```text
The RTL(React Testing Library) is a very lightweight solution for testing React
components. It provides light utility functions on top of react-dom and
react-dom/test-utils, in a way that encourages better testing practices. Its
primary guiding principle is:

The more your tests resemble the way your software is used, the more confidence they can give you.
```

RTL(React Testing Library)는 React Components를 테스트하기 위한 매우 가벼운 솔루션이다. 이 라이브러리는 더 나은 테스트를 장려하는 방식으로 `react-dom`과 `react-dom/test-utils`위에서 가벼운 유틸리티 기능을 제공한다. 주요 원칙은 아래와 같다.

> 테스트가 소프트웨어의 사용방식과 비슷할수록 더 많은 자신감을 줄 수 있다.

## <ins><b>Jest?</b></ins>

<ins><b>RTL은 Jest의 대체 도구가 아니다. RTL과 Jest는 테스트를 위해 서로 필요한 관계이다.</b></ins>

modern React의 테스트에 있어서 개발자들은 테스트를 위해 Jest를 회피하지 않은 것이다. 그 이유는 JS를 위한 가장 인기 있는 테스트 프레임워크이기 때문이다.
Jest는 Facebook에서 Jasmine을 기반으로 개발한 `JavaScript Test Framework`이다.
페이스 북이 서포트하고 매우 큰 커뮤니티를 형성하고 있다.

## <ins><b>React Testing Library (RTL)</b></ins>

Kent C.Dodds.이 만들어졌다.
Airbnb Enzyme를 대체하는 테스트 라이브러리이다.
Enzyme은 React 개발자들이 내부의 React component들을 테스트 할 수 있게 해준다. 반면에 RTL은 개발자가 사용자의 입장에 놓일 수 있게 한다. (End user가 해당 component와 어떻게 상호작용하는지에 대해서 정의한다.)

## <ins><b>연습용 프로젝트 준비하기</b></ins>

TypeScript 기반으로 react-testing 프로젝트 생성하기

```bash
$ yarn create create-app react-testing --template typescript
```

configuration, babel, webpack 등의 기본 설정을 모두 포함시켜서 프로젝트를 생성할 수 있다.

설치가 끝난 뒤에 package.json을 살펴보면 기본적을 jest와 관련된 test dependencies(dev)가 설치되어 있는 것을 확인할 수 있다.

테스트와 TypeScript 관련 dependency는 dev dependency로 분류한다. 간단히 설명하자면 dependencies는 Production 단계에서 어플리케이션에 의해 요구되는 패키지를 말하며, `devDependencies`는 오직 로컬에서의 개발과 테스트를 위해 필요한 패키지를 말한다.

생성된 프로젝트에서 간단히 아래의 명령으로 테스트를 시작할 수 있다. <b>a</b>옵션을 선택하면 현재 프로젝트 내에 있는 테스트 파일들을 실행시켜서 테스트를 진행한다.

```bash
$ yarn test
```

## (유용한 Extension) VScode의 extension중에 `Jest`를 다운받으면 테스트 파일에서 테스트 결과를 가시적으로 확인할 수 있다.

우선 React component가 아닌 간단한 function를 위한 테스트 코드를 작성해보도록 하자.

`example.test.ts`

TDD의 원리에 대해서 간단하게 배워보고, 테스트 코드를 작성할 때 지켜야 할 convention에 대해서 알아보자.
앞에서 말했듯이 테스트 코드는 실제 코드가 어떻게 동작하는지에 대한 정의를 담은 문서이다.
다른 개발자들이 내가 작성한 테스트 코드를 보았을때 무엇을 위한 테스트 코드인지 알기 쉽게 convention을 지켜서 작성을 하도록 하자.

실제 실무에서는 TDD가 정의하는 work flow를 따르지 않는 경우도 있다. 때로는 테스트 코드의 작성을 우선하지 않고, 실제 코드를 작성하고 테스트 코드를 작성하는 경우도 있다.

## implicit assertion

아래의 코드를 보면, 기존에 작성해보았던 테스트 코드에는 있었던 expect() statement와 toBe 구문인 assertion 부분이 없다. 이는 암묵적인 방식으로 App component에 대한 assertion을 처리한 경우이다.

<ins><b>`app.test.tsx`</b></ins>

```javascript
import React from 'react';
import { render, screen } from '@testing-library/react';
import App from './App';

describe('When everything is ok', () => {
  test('should render the App component without crashing', () => {
    render(<App />);
  });
});
```

## RTL의 screen 활용

```javascript
import React from 'react';
import { render, screen } from '@testing-library/react';
import App from './App';

describe('When everything is ok', () => {
  test('should render the App component without crashing', () => {
    render(<App />);
    screen.debug();
  });
});
```

\<App> component를 렌더링 한 뒤에 `screen.debug()`를 해주게 되면 캡쳐와 같이 검사하고자 하는 component를 \<body>와 \<div> 태그로 감싼 형태로 출력됨을 알 수 있다.

이는 JSX가 아닌 HTML로써 출력이 된 것이다.

![](/images/post_images/210307_rtl_screen_debug.png)
