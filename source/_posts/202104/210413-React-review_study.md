---
title: 210413 React TIL - PureComponent와 React.memo, useMemo와 useCallback, 자식 컴포넌트의 props로 함수를 넘겨줄때, hooks의 순서
date: 2021-04-13 14:28:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>PureComponent와 React.memo</b></ins>

클래스형 컴포넌트에서는 PureComponent를 상속받아서 컴포넌트 최적화를 시켜줄 수 있고, 함수형 컴포넌트에서는 함수몸체를 React.memo로 감싸서 PureComponent와 같이 컴포넌트를 최적화 시켜 줄 수 있다. 이렇게 컴포넌트로 다른 컴포넌트를 감싸는 것을 `HOC(Higher Order Component`라고 한다.

## <ins><b>useCallback과 useMemo의 비교</b></ins>

useCallback은 함수자체를 기억하고, useMemo는 함수의 반환값 자체를 기억한다.
함수형 컴포넌트의 경우에는 재실행될 때 함수 전체가 재실행되기 때문에 한 번 생성한 함수를 재생성하지 않고 재활용하기 위해서는 `useCallback`을 사용한다.
`useCallback`의 내부에서 사용되는 state 값이 있는 경우에는, 반드시 두 번째 인자인 []내에 작성해줘야 한다.

이렇게 되면 비효율적이기 때문에 특정 값이 변경될때에만 해당 함수가 호출되어 초기 상태값을 업데이트 할 수 있도록 `useMemo`를 사용해서 작성해줘야 한다.
만약 특정 함수로부터 반환받은 값을 상태의 초기값으로 사용하는 경우, 함수 자체를 useState의 초기값에 넣어주게 되면, 해당 함수는 함수 컴포넌트가 실행될 때마다 재생성된다. 따라서 아래와 같이 `useMemo`를 사용해서 상태값을 갱신해서 사용해야 한다.

  <!-- more -->

```javascript
function getStatusValue(){
  ........
  return values;
}

const TestComponent = () => {
  // refValues가 바뀌면 getStatusValue의 함수로부터 값을 가져와서 갱신한다.
  const statusValue = useMemo(() => getStatusValue(), [refValues]);
  const [winStatus, setWinStatus] = useState(statusValue);
  ........
}
```

## <ins><b>자식 컴포넌트의 props로 함수를 넘겨줄때</b></ins>

자식 컴포넌트의 props로 함수를 넘겨줄때 반드시 넘겨주는 함수는 `useCallback`으로 처리해준다. useCallback으로 처리를 안해주게 되면, 매번 새로운 함수로 생성이되어 자식 컴포넌트에 전달될때 자식 컴포넌트가 새로운 함수를 props로 넘겨준다고 인식한다.(`불필요한 re-rendering`)

## <ins><b>hooks는 순서가 중요하다</b></ins>

hooks는 순서가 중요하기 때문에 항상 함수의 최상위에 배치하도록 하고, 조건문이나 함수, 반복문 안에는 웬만하면 넣지 않도록 한다.

## <ins><b>useEffect, useMemo, useCallback 동작</b></ins>

```javascript
useEffect('[f]', []); // [] 내의 deps가 바뀔때 [f]가 실행된다.
useMemo('[f]', []); // [] 내의 deps가 바뀌기 전까지 [f]의 반환값을 기억한다.
useCallback('[f]', []); // [] 내의 deps가 바뀌기 전까지 [f] 함수 자체를 기억한다.
```

## <ins><b>componentDidMount/componentDidUpdate와 useEffect를 비교</b></ins>

useEffect의 두 번째 인자(deps)를 아무것도 주지 않으면 `componentDidMount`와 같이 실행하고, deps에 특정 상태값을 주게 되면, `componentDidMount + componentDidUpdate`의 효과를 준다.

componentDidUpdate에서는 prevProps, prevState의 인자를 이용해서 함수 내에서 이전 상태와 현재 상태를 비교해서 분기문으로 처리를 해주면 되지만, useEffect에서는 state마다 다른 처리를 해주고자 한다면, useEffect를 여러번 사용해서 특정 상태값에 따른 다른 동작을 정의해주면 된다.

만약에 useEffect에서 componentDidUpdate와 같은 효과를 주기 위해서는 아래와 같은 패턴으로 코드를 작성해준다.
처음 실행때는 useRef로 정의한 mounted의 값만 `true`로 바꿔주고, 그 이후에 대한 작업만 deps의 값이 바뀌었을때 실행되게 된다.

```javascript
const mounted = useRef(false);

useEffect(() => {
  if (!mounted.current) {
    mounted.current = true;
  } else {
    // componentDidUpdate와 같은 처리
  }
}, [deps]);
```

## <ins><b></b></ins>

## <ins><b></b></ins>

## <ins><b></b></ins>
