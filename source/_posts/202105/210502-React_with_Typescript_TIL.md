---
title: 210502 React with TypeScript TIL - keyof와 typeof의 혼합 사용, JavaScript의 Curring 방식의 함수 작성
date: 2021-05-02 19:32:00
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - ReactJS-TypeScript
---

<div align="center">
  <img src="/images/post_images/ts-and-react.png" alt="React with TypeScript"/>
</div>

## <ins><b>keyof와 typeof의 혼합 사용</b></ins>

특정 변수의 타입을 정의할때 만약에 해당 타입이 다른 타입과 종속적인 관계에 있다면, 종속관계에 있는 해당 타입의 정의를 참조해서 타입을 정의해야 한다.

아래는 스프라이트 기법으로 이미지를 잘라내서 사용하기 위한 이미지 좌표의 타입 정의이다.

```javascript
const coords = {
  img1: '0',
  img2: '-142px',
  img3: '-284px'
} as const;

type a = keyof typeof coords; // coords 타입(typeof)의 key 값(img1 | img2 | img3)
type imgCoords = typeof coords[keyof typeof coords] // '0' | '-142px' | '-284px'
```

  <!-- more -->

## <ins><b>JavaScript의 Curring 방식의 함수 작성</b></ins>

일반적으로 버튼의 클릭이벤트 속성으로 함수를 넣어줄때에는 아래와 같이 해당 함수명만 작성을 해준다.

```javascript
const onClickBtn = () => {
  // 이벤트 처리
};
<button onClick={onClickBtn}> Button </button>;
```

만약에 버튼이 클릭되었을때 실행되는 함수에 특정 인수를 넣어 실행되게 하는 경우에는 curring 방식으로 함수를 작성해줘야 한다.

```javascript
// event 객체는 두 번째 인자로 넣어준다. (클릭 이벤트의 경우에는 타입이 React.MouseEvent)
const onClickBtn = (name) => (e: React.MouseEvent<HTMLButtonElement>) => {
  //이벤트 처리
};
<button onClick={onClickBtn('lee')}> Button </button>;
```

## <ins><b>useEffect는 별도로 typing 해줄 것이 없다.</b></ins>

## <ins><b>const로 컴포넌트를 가져오면 interface를 가져올 수 없다.</b></ins>

import를 사용해서 컴포넌트를 가져오게 되면, interface도 같이 가져올 수 있지만, const를 사용해서 컴포넌트를 가져오게 되면, interface를 가져올 수 없다.
