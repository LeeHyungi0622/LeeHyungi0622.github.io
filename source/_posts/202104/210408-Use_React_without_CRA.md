---
title: 210408 CRA없이 React 프로젝트 시작하기
date: 2021-04-08 06:58:42
tags:
  - React
categories:
  - ReactJS
---

첫번째 코드에서는 React 관련된 라이브러리 두 개를 script로 포함시켜주었다.
첫 번째로 import해 준 라이브러리는 `React의 핵심부분`과 관련된 라이브러리이며, 두 번째로 import해 준 라이브러리는 `React 코드를 웹에 표시해주는 역할`을 해주는 라이브러리이다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <!-- React 관련 라이브러리 -->
    <!-- React의 핵심적인 코드 -->
    <script
      crossorigin
      src="https://unpkg.com/react@16/umd/react.development.js"
    ></script>
    <!-- React 코드를 웹에 붙여주는 역할 -->
    <script
      crossorigin
      src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"
    ></script>
    <title>Document</title>
  </head>
  <body>
    <div id="root"></div>
    <!-- 결과 -->
    <script>
      const e = React.createElement;
      class LikeButton extends React.Component {
        constructor(props) {
          super(props);
          this.state = {
            liked: false
          };
        }
        // 버튼을 만들겠다.
        render() {
          // type, property, textNode
          return e(
            'button',
            {
              onClick: () => {
                this.setState({
                  liked: true
                });
                console.log('clicked');
              },
              type: 'submit'
            },
            this.state.liked === true ? 'Liked' : 'Like'
          ); // <button type="submit" onclick="onclick">Like</button>
        }
      }
    </script>
    <script>
      // 만든 버튼을 그린다.
      // 실제 화면에 렌더링을 해주는 역할(DOM)
      ReactDOM.render(e(LikeButton), document.querySelector('#root'));
    </script>
  </body>
</html>
```

하지만 위의 코드에서 React.createElement로 버튼 요소를 만드는 부분은 가독성이 좋지 않다. 따라서 React 팀에서는 JavaScript에서 JSX를 사용할 수 있도록 개선을 하였다.
JSX를 사용할 수 있게 되면서 아래의 코드와 같이 React.createElement를 사용해서 버튼을 생성하지 않고, 바로 \<button>과 같은 HTML 태그를 사용하여 가독성 좋은 코드를 작성할 수 있게 되었다.

이러한 JavaScript 내에서 JSX문법을 사용하기 위해서는 선행되어야 하는 작업이 있는데 바로 `Babel`을 import시켜줘야 한다.
원래 JavaScript에서 HTML태그를 쓰는 것은 문법상 맞지 않지만 <ins><b>Babel이 작성한 JSX문법의 코드를 createElement로 convert해준다. </b></ins>
import한 babel을 사용하기 위해서는 script 태그의 type의 속성으로 `text/babel`을 넣어줘야 한다.

간단하게 JavaScript상에서 최신 문법을 사용하고 싶을 때에는 `<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>`를 넣어주면 된다. 이렇게 되면 JavaScript ES6 문법의 코드가 각 브라우저에서 동작할 수 있도록 코드 변환을 해준다.

다만, babel에 대한 세부설정을 하기 위해서는 별도의 Webpack이나 Babel 툴이 필요하다.
그리고 부가적으로 최신 메서드나 객체를 사용하고 싶은 경우에는 `babel polyfill`을 추가해줘야 한다.

`<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-polyfill/7.12.1/polyfill.min.js" integrity="sha512-uzOpZ74myvXTYZ+mXUsPhDF+/iL/n32GDxdryI2SJronkEyKC8FBFRLiBQ7l7U/PTYebDbgTtbqTa6/vGtU23A==" crossorigin="anonymous"></script>`

  <!-- more -->

```javascript
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- React 관련 라이브러리 -->
    <!-- React의 핵심적인 코드 -->
    <script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <!-- React 코드를 웹에 붙여주는 역할 -->
    <script crossorigin src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <!-- Babel -->
    <script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
    <title>Document</title>
</head>

<body>
    <div id="root"></div>
    <!-- 결과 -->
    <!-- JavaScript 내부에서 실험적으로 html을 사용할 수 있도록 한다. -->
    <script type="text/babel">
        const e = React.createElement;
        class LikeButton extends React.Component {

            constructor(props) {
                    super(props);
                    this.state = {
                        liked: false,
                    };
                }
                // 버튼을 만들겠다.
            render() {
                // type, property, textNode
                // JS에서 html을 쓰는 것은 문법상 맞지 않다.
                // 따라서 babel을 사용해야 한다. (최신문법, 실험적인 문법을 자바스크립트에서 쓸 수 있도록 도와준다)
                return <button type="submit" onClick={() => { this.setState({ liked: true })}}>
                    {this.state.liked ? 'Liked' : 'Like'}
                </button>;
            }
        }
    </script>
    <script type="text/babel">
        // 만든 버튼을 그린다.
        // 실제 화면에 렌더링을 해주는 역할(DOM)
        ReactDOM.render(<div><LikeButton /><LikeButton /><LikeButton /><LikeButton /></div>, document.querySelector('#root'));
    </script>
</body>

</html>
```

**(1)** 실제로 실무에서는 생성자(Constructor)내부에서 state를 정의하지 않고, 별도로 분리를 해서 작성을 해준다.

**(2)** class component의 render 함수의 내부에서는 하나의 component를 반환해줘야 한다. 여러 개의 component를 전달하는 경우에는 `<div> 태그`로 묶어서 반환을 해줘도 되지만 불필요한 `<div> 태그`로 묶어주면 실제 화면에 `렌더링 될 때에 불필요한 <div>태그가 포함`이 되기 때문에 좋지 않다.
따라서 React에서 제공해주는 `<></> or <React.Fragment></React.Fragment>` (Fragment)를 사용하면 불필요한 태그의 사용없이 여러 개의 component를 묶어서 반환해줄 수 있다.

**(3)** class 내에서 정의한 함수의 경우에는 함수 선언형으로 작성해주지 않고 `화살표 함수(arrow function)`을 써주도록 한다. 함수 내부에서의 this와 화살표 함수 내부에서의 this의 의미가 달라지기 때문이다.
이전에 공부를 했을때 `화살표 함수는 직접적으로 this를 binding할 수 없고 내부에 this를 정의해서 사용하는 경우에는 상위 스코프의 this를 가르키게 된다.`

**(4)** 현재 상태 값과 미래의 상태 값을 구분해주기 위해서 class component의 내부에 정의해준 setState의 내부에 callback 함수로 처리를 해준다.
`setState는 비동기`이기 때문에 `하나의 이벤트에서 여러 개의 setState가 실행이 되어도 일괄적으로 딱 한 번만 처리`된다. 그래서 setState의 내부에 콜백함수를 넘겨서 처리해준다.
만약에 setState의 내부에서 this.state.[상태요소]가 사용이 된다면 콜백함수로 처리하도록 한다.
이렇게 처리를 하는 이유는 효율적으로 화면을 렌더링하기 위함이다.

```javascript
this.setState((prevState) => {
  return{
    console.log(prevState);
  }
});
```

**(5)** 특정 DOM element를 선택하기 위해서는 선택하고자 하는 element의 속성으로 `ref={(c) => {this.input=c}}`와 같이 ref 속성을 넣어준다. (input은 클래스 컴포넌트의 내부에 선언해준 변수이다)

**(6)** 함수형 컴포넌트는 본래 상태관리가 불필요한 Component를 만들때 사용했었다. 하지만 요청에 의해 `함수형 컴포넌트의 내부에서도 상태관리가 가능하게 되었고, 그것이 바로 React Hooks`이다.

React.useState(상태값 관리), React.useRef(특정 DOM 요소 선택)

함수형 컴포넌트에서는 별도의 render함수가 없기 때문에 상태값이 바뀌게 되면 함수 자체가 통째로 다시 실행이 된다. 그래서 클래스 컴포넌트와 비교했을때 조금 느릴 수 있다.
(4)에서 언급을 했듯이 setState는 비동기 함수이다. 하나의 이벤트 내에서 여러개의 setState 함수들을 사용해도 일괄적으로 딱 한 번만 실행된다. 만약에 이전 상태값의 참조가 필요한 경우에는 setState 함수 내부에서 함수형 업데이트를 해주게 되면 된다.

이전에 React를 공부하면서 효율적인 렌더링을 위한 useState()의 함수형 업데이트에 대해서 정리를 했었는데, 아래 포스팅 글을 같이 참고하도록 하자.

[https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/](https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/)

**(7)** jsx 작성시에는 태그의 class속성을 넣어줄때 `className`으로, label의 for속성을 넣어줄때는 `htmlFor`속성으로 넣어준다. 이는 class 작성시에 사용되는 키워드와 for-loop의 키워드와 같은 이름이기 때문에 구분을 해주기 위함이다.
