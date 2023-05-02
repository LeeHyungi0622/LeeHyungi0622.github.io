---
title: 210412 React TIL - setTimeout(), useEffect()/useMemo()/useCallback(), useState와 useRef의 차이, jsx에서 if/for문 작성, React class/function - 라이프 사이클
date: 2021-04-12 13:20:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>React에서 jsx를 구조있게 작성하기</b></ins>

필요에 따라 jsx 작성 부분이 조건문이나 반복문으로 복잡해진다면, 별도의 함수나 자식 컴포넌트로 작성해주도록 한다.

## <ins><b>setTimeout() 사용법</b></ins>

setTimeout의 첫 번째 인수로는 콜백 함수를 넣어주고 두 번째 인수로는 ms단위로 interval time을 적어준다. setTimeout()은 비동기 함수이기 때문에 외부에 선언한 변수를 참조할 경우, 클로저 문제가 생긴다.
따라서 setTimeout()내에서 사용할 변수의 경우에는 setTimeout() 내에서 처리하도록 한다.
setTimeout()을 해줬다면 clearTimeout()을 해줘야 한다. 만약 clearTimeout()을 해주지 않으면, setTimeout()을 처리한 컴포넌트가 제거되더라도 call stack에 남아 계속 실행될 수 있다.

  <!-- more -->

```javascript
// timeout변수 선언
timeout;

// setTimeout()
timeout = setTimeout(() => {
  console.log('test');
}, 1000);

// setTimeout() 제거
clearTimeout(this.timeout);
```

`setTimeout은 call stack으로 넘어가서 실행이 되는데, settTimeout은 call stack으로 넘어간다고 하더라도 clearTimeout으로 취소를 할 수 있다.`

## <ins><b>useEffect()와 useMemo(), useCallback()</b></ins>

이전에 공부를 하면서 별도로 정리를 해둔 포스팅이 있는데, 아래 링크의 내용을 참고하도록 하자.

→ [https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/](https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/)

함수형 컴포넌트에서는 함수 내부에 선언된 함수들이 매번 상태가 업데이트 되면서 렌더링 될 때마다 다시 생성이 된다. 이미 한 번 생성된 함수들을 처음 한 번만 생성하고 이후에는 재사용하기 위해서는 `useCallback()을 사용`해야 한다.

## <ins><b>useState와 useRef의 차이</b></ins>

useState는 호출될 경우(setState), return 부분이 다시 실행된다. 하지만 useRef의 경우에는 return 부분이 다시 실행되지 않는다. 특정 변수값이 바뀌어도 다시 화면을 렌더링하고 싶지 않은 경우에는 useRef를 사용하도록 한다.
(useRef를 사용할때에는 항상 useRef 변수는 `current로 접근`해야 한다.)

## <ins><b>jsx 구문에서 if /for 문 작성해보기 </b></ins>

jsx 구문에서 if와 for 문을 사용할 수는 있지만 이는 실무에서도 별로 사용되지 않는 코드 작성법이다. 단축표기법이나 삼항연산자, map과 같은 함수를 대체해서 사용하도록 하자.

아래와 같이 즉시 실행함수의 형태로 작성해주면 그룹연산자 내에 작성한 조건문 및 반복문을 실행할 수 있다. 하지만 가독성이 좋지 않기 때문에 좋지 않은 코드 작성법이다.

```javascript
return (
  <>
    {(() => {
      if (result.length === 0) {
      } else {
      }
    })()}
  </>
);
```

return 반환문 내부에 작성해주는 jsx 코드에서 component를 배열의 형태로 반환해줄 수도 있다.

```javascript
return (
  <>
    {(() => {
      let array = [];
      for (let i = 0; i < result.length; i++) {
        array.push(<Child key={`${i + 'c'}`} />);
      }
      return array;
    })()}
  </>
);
```

위와 같이 배열을 반환해주게 되면, 아래와 같은 형태로 반환해주는 것과 동일한 형태가 된다.
아래와 같이 작성을 해주는 경우, 반드시 리스트 내의 각 컴포넌트 요소들은 key값을 가져야 한다.
하지만 이 또한 실무에서 사용되지 않는 방법이지만, 이러한 형태로 컴포넌트를 반환할 수 있다는 것도 알아두도록 한다.

```javascript
return [
  <div key="A">A</div>
  <div key="B">B</div>
  <div key="C">C</div>
]
```

## <ins><b>React class component - 라이프 사이클</b></ins>

React의 클래스 컴포넌트에서 render 함수가 실행되면, react가 jsx를 DOM에 붙여주게 되는데, 붙여주게 되는 그 순간에 특정 동작을 할 수 있게 해 줄 수 있다.

- ### **componentDidMount()**
  render가 처음 실행되어 성공적으로 렌더링이 되었을때 componentDidMount() 함수가 실행이 된다. setState 함수로 인해 re-rendering되는 경우에는 componentDidMount()가 실행되지 않는다.
- ### **componentWillUnmount()**

  부모 컴포넌트에 의해서 자식 컴포넌트가 제거될 수 있는데, 이렇게 컴포넌트가 제거되기 직전에 실행되는 함수이다. `componentDidMount()와 한 쌍으로, componentDidMount()에서 처리했던 작업을 종료`시키는 역할을 한다.

  `예시`

  ```javascript
  interval;

  componentDidMount(){
    this.interval = setInterval(() => {
      console.log('result');
    }, 1000)
  }
  // setInterval을 종료시켜주지 않으면, component 생성될때마다 계속해서 setInterval이 생성이 되므로, 비동기 요청은 반드시 componentWillUnmount에서 종료를 시켜주도록 한다.
  componentWillUnmount(){
    clearInterval(this.interval);
  }
  ```

- ### **componentDidUpdate()**

  setState 함수로 인해 컴포넌트가 re-rendering되는 경우에 실행되는 함수이다.

- **순서**
  **(1)** constructor()
  **(2)** render()
  **(3)** ref `(jsx 태그에 ref 속성을 넣은 경우)`
  **(4)** componentDidMount()
  **(5)** `(setState/props가 바뀌는 경우)`
  **(6)** shouldComponentUpdate(→ true)
  **(7)** rendering
  **(8)** componentDidUpdate()
  **(9)** `(부모 컴포넌트가 자식 컴포넌트를 제거하는 경우)`
  **(10)** componentWillUnmount()
  **(11)** 소멸

- ### **고차함수(Higher-Order-Function)**

  고차함수(HOF)란 함수를 인자로 받거나 반환값으로 갖는 함수를 말한다.
  만약에 아래와 같이 button의 onClick 속성에 함수 형태로 이벤트를 넣어주었다면, 아래와 같은 형태로 이벤트 함수를 정의해서 작성해 줄 수 있다.

  ```javascript
  const test = (value) => () => {

  }

  // <button onClick={() => test('value')}>
  <button onClick={test('value')}>
  ```

## <ins><b>React functional component - 라이프 사이클</b></ins>

그렇다면 클래스 컴포넌트에 있는 라이프 사이클이 함수형 컴포넌트에도 있을까? 별도의 라이프 사이클이 있는 것은 아니지만 라이프 사이클을 흉내낸 `useEffect()`가 있다.

이전에 공부를 하면서 별도로 정리를 해둔 포스팅이 있는데, 아래 링크의 내용을 참고하도록 하자.

→ [https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/](https://leehyungi0622.github.io/2021/03/18/202103/210318-React-review_study/)

```javascript

const interval = useRef();

useEffect(() => {
  //componentDidMount, componentDidUpdate의 역할 (1:1 대응은 아니다)
  interval.current = setInterval([function], 1000);

  return () => { //componentWillUnmount의 역할
    clearInterval(this.interval.current);
  }

},[ result ]) // result 상태값이 변화할때마다 re-rendering이 발생한다.
```

## <ins><b>class / hooks의 라이프 사이클 비교</b></ins>

hooks에서 각 각의 state마다 다른 effect를 적용시키고 싶을때, useEffect()를 여러번 사용할 수 있다.
반면 class의 경우에는 각 각의 state마다 다른 effect를 적용시키고 싶을때 `componentDidMount나 componentDidUpdate`에서 각 각의 state를 조건문으로 분기처리한다.

예를들어 a, b, c 세 개의 state가 있다고 가정하자. componentDidMount, componentDidUpdate, componentWillUnmount 이 세 개의 클래스 라이프 사이클 메서드에서는 세 개의 state 모두 동시에 처리한다.
반면 hooks의 useEffect의 경우에는 [a], [a,b], [a,b,c] 로 각 각의 state를 그룹화해서 각기다른 effect를 처리할 수 있다.
`ex)`

```javascript
// class component의 componentDidMount
componentDidMount(){
  this.setState({
    a: 1,
    b: 2,
    c: 3
  });
};

// functional component의 react hooks - useEffect
useEffect(() => {
  setA();
  setB();
},[a, b]);

useEffect(() => {
  setC();
},[c]);
```

## <ins><b>Q : hooks는 부모 컴포넌트에서 자식 컴포넌트를 가지고 있으면, 부모 컴포넌트가 렌더링 될때마다 무조건 자식 컴포넌트도 변경이 되는가?</b></ins>

- ### **A.**
  무조건 변경된다. 자식 컴포넌트가 전달되는 props가 바뀌면 re-rendering되기 때문에 React.memo로 함수 컴포넌트를 감싸주는 것이다.

## <ins><b>useLayoutEffect vs useEffect</b></ins>

useLayoutEffect는 화면의 크기를 resizing할때 useEffect가 발생하기 이전에 발생한다. useLayoutEffect는 화면 레이아웃의 변화를 감지할때 사용된다.
useEffect의 경우에는 화면이 렌더링 된 다음에 실행되지만, useLayoutEffect의 경우에는 화면이 바뀌기 전에 발생을 한다.
