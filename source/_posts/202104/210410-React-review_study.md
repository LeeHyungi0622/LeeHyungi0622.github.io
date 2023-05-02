---
title: 210410 React TIL - import와 require 비교, React component의 key 속성, React component의 props, Top-Down & Bottom-Top, Redux와 Context, Class내에서 화살표 함수가 아닌 일반함수의 형태로 메서드를 정의하는 경우
date: 2021-04-10 13:12:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>import와 require 비교</b></ins>

간단하게 설명하면 import 구문은 리액트 코드 작성시에 사용하고 require는 Node 관련 코드를 작성시에 사용한다.

첫 번째, `import 구문은 ES2015 module 문법`이다. export 구문은 아래와 같이 두 가지 형태로 export를 해줄 수 있는데, 각 각 다른 형태로 import해야한다.
export default 구문은 파일에서 한 번만 쓸 수 있고, export const 구문은 제한 없이 여러번 쓸 수 있다.

  <!-- more -->

`작성 예시`

```javascript
export const hello = 'hello'; // import { hello } from './Hello';
export default Hello; // import Hello from './Hello';
```

두 번째, `require 구문은 Node의 module 문법 = CommonJS`이다. import와 export 구문은 아래와 같이 작성을 해주면 된다.
Webpack 설정 파일의 내용도 node가 실행시켜주기 때문에 import 구문을 사용할 수 없다.

`작성 예시`

```javascript
const React = require('react');
exports.hello = 'hello';
module.exports = Hello;
```

<ins><b>babel이 ES2015 module 문법을 Node의 module 문법으로 바꿔준다.</b></ins>

## <ins><b>리액트 컴포넌트의 key 속성</b></ins>

리액트 컴포넌트 내의 key 속성은 컴포넌트 요소가 추가/삭제될때 참조되는 속성이다. 이 부분은 `성능 최적화와 관련`이 있으며 단순 index값을 key값으로 넣어주는 것은 좋지 않은 방법이다.
따라서 index가 아닌 unique한 값을 할당해줄 수 있도록 해야 한다.

## <ins><b>리액트 컴포넌트의 props</b></ins>

부모 컴포넌트에서 자식 컴포넌트로 속성값을 넘겨 줄 수 있는데 이를 props라 한다.

## <ins><b>Redux, Context</b></ins>

부모 컴포넌트에서 자식 컴포넌트로 속성값을 넘겨주는데 그 깊이가 깊어지는 경우, 불필요한 props의 전달이 이루어지기 때문에 이런 경우에는 Redux, Context를 사용하도록 한다.
Context가 Redux보다 나중에 등장했지만, Context는 Redux보다 간단한 작업을 하는데 도움을 주고, react-redux의 형태로 Redux 내부에서 Context를 사용한다.

## <ins><b>리액트 컴포넌트의 작성순서</b></ins>

리액트에서 컴포넌트를 작성할때, 큰 하나의 컴포넌트를 작성한 다음에 작은 컴포넌트 단위로 쪼개서 분리하는 것을 `Top - Down`방식이라고 하며, 작은 컴포넌트를 우선적으로 만든 다음에 큰 컴포넌트를 만드는 것을 `Bottom - Up`방식이라고 한다.

## <ins><b>class 내에서 함수를 화살표 함수가 아닌 프로토타입 메서드로 정의하는 경우</b></ins>

클래스 내에서 함수를 화살표 함수(arrow function)가 아닌 프로토타입 메서드로 작성하게 되면 메서드내에서 this를 참조하는 경우 문제가 생긴다.
아래의 코드를 참조해보자. 화살표 함수의 경우 자체적으로 this를 바인딩하지 않고, 상위 스코프의 this를 바인딩하기 때문에 화살표 함수내의 this는 클래스 객체 자체를 가르킨다.
하지만 클래스 내부의 메서드를 화살표 함수가 아닌 프로토타입 메서드로 작성해주게 되면, 생성자(constructor)에서 별도로 클래스 내부의 프로토타입 메서드에 this를 바인딩해줘야 한다.

이는 클래스 내부의 프로토타입 메서드의 this는 메서드를 호출한 객체를 바인딩하기 때문에 별도의 호출없이 프로토타입 메서드 안의 this를 확인해보면 `undefined`가 출력됨을 확인할 수 있다.

```javascript
class SampleClass extends Component {
  constructor(props) {
    super(props);
    this.state = {
      result: '',
      value: ''
    };
    // class 내부 매서드에 this 바인딩
    this.onSubmitForm = this.onSubmitForm.bind(this);
    this.onChangeInput = this.onChangeInput.bind(this);
  }

  // 프로토타입 메서드
  onSubmitForm(e) {
    e.preventDefault();
    this.setState({
      result:
    })
  }

  // 프로토타입 메서드
  onChangeInput(e) {
    this.setState({
      value: e.target.value
    });
  }
}
```
