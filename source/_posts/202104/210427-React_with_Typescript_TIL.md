---
title: 210427 React with TypeScript TIL - useState에서 빈 배열([])로 초기화시키는 경우, props 타입(함수형, 클래스형 컴포넌트), webpack-dev-server 사용, setTimeout과 useRef의 사용
date: 2021-04-27 12:58:00
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

## <ins><b>useState에서 빈 배열([])로 초기화시키는 경우</b></ins>

typescript에서 useState hooks를 사용해서 초기 값을 빈 배열로 할 경우, 타입스크립트에서는 빈배열([])을 never type으로 인식하기 때문에 구체적인 타입을 지정해줘야 한다.

type 선언을 위해 사용한 interface는 같은 파일에서 정의를 하지 말고, 별도의 파일(`types.ts`)로 빼서 다른 파일에서 재사용 가능하도록 정의를 하는 것이 좋다.(코드의 가독성, 재사용성)
`types.ts`

```javascript
export interface IDetailInfo {
  id: string;
  contents: string;
}
```

`main.ts`

```javascript
import { IDetailInfo } from './types';
...
const [detail, setDetail] = useState<IDetailInfo[]>([]);
...
```

  <!-- more -->

## <ins><b>props의 타입지정</b></ins>

- ## 함수형 컴포넌트에서의 props 타입

  아래와 같이 props 매개변수에 직접적으로 typing을 해줘도 되지만, React의 함수형 컴포넌트에서는 이렇게 작성하지 않는다.

  ```javascript
  import { IDetailInfo } from './types';

  const ChildComponent = ({ detail }: { detail: IDetailInfo }) => {

  };
  ....
  ```

  원래 React에서는 propTypes를 설치해서 넘겨받은 props에 대해서 타입검사를 했는데, 타입스크립트에서는 이 역할을 대체해주기 때문에 propTypes를 사용할 필요가 없다.

  React에서는 제네릭 방법으로 간단하게 함수형 컴포넌트의 props에 대한 타입을 적을 수 있다.
  함수형 컴포넌트는 생략해서 사용을 해왔지만 FunctionComponent 타입으로 제네릭을 포함하고 있다.
  이 제네릭 요소에 아래와 같이 props의 타입을 정의를 해주면 된다.
  (`함수형 컴포넌트에서는 useState hooks를 사용하기 때문에 state자리는 필요가 없다.`)

  ```javascript
  import { IDetailInfo } from './types';

  const ChildComponent: FunctionComponent<{ detail: IDetailInfo }> = ({ deatil }) => {
    ...
  };
  ...
  ```

- ## 클래스형 컴포넌트에서의 props 타입

  클래스형 컴포넌트에서는 이전에 배웠듯이 React.Component의 제네릭 첫번째 인자가 props 타입을 정의하는 자리이다. 만약에 state에 대한 정의가 필요없다면, props에 대한 타입 정의만 넣어주면 된다.
  (단, state에 대한 타입정의만 필요한 경우에는 props자리에 {} 빈 객체로 표기해줘야 한다.)

  ```javascript
  import { IDetailInfo } from './types';

  ...
  class ChildComponent extends React.Component<{ detail: IDetailInfo }> {
      ...
  }
  ...
  ```

  <br/>

## <ins><b>webpack-dev-server 사용</b></ins>

매번 webpack을 구동시켜주기 귀찮기 때문에 webpack-dev-server를 사용해서 알아서 변화감지를 통해 구동시킬 수 있다.

- ## 설치

```zsh
$ npm i webpack-dev-server -D
```

`package.json`

```json
"script": {
  "dev": "webpack-dev-server --hot"
}
```

`webpack.config.js`

```javascript
...
  output: {
    ...
    // webpack-dev-server를 사용하기 위해 publicPath를 추가
    publicPath: '/dist',
  }
...
```

## <ins><b>setTimeout과 useRef의 사용</b></ins>

setTimeout을 하는 경우 반드시 clearTimeout을 해줘야 한다. 따라서 화면의 렌더링에는 영향을 주지 않는 useRef hooks를 사용해서 setTimeout에 대한 정보를 변수로 저장한다.

```javascript
let timeout = useRef<number | null>(null);
let startTime = useRef<Date>();
...
// nodejs의 setTimeout이 아닌 window의 setTimeout을 사용해야 한다.
timeout.current = window.setTimeout(() => {
  setState('now');
  startTime.current = new Date();
});
...
clearTimeout(timeout.current);
...
```

useRef의 타입을 지정할때 주의해야될 부분이 있는데, 아래와 같이 useRef의 타입은 3가지 경우로 overloading되어서 정의되어 있다.

경우에 따라 알맞은 useRef의 타입정의를 골라서 타입정의를 해야한다. 위에서는 두번째 useRef의 타입을 사용하였다.
기존의 useRef<number>(null)의 경우 내부의 useRef의 current가 read-only 속성이기 때문에 값을 넣을 수 없다. 하지만 useRef<number|null>(null) 로 정의를 해주게 되면, React.refObject<number>.current에서 React.MutableRefObject<number | null>.current로, current가 변경가능한 mutable한 속성이 된다.

`@types/react/index.d.ts`

```javascript
// intialValue의 타입과 useRef의 타입을 일치시켰을때 Mutable한 RefObject가 된다.
function useRef<T>(initialValue: T): MutableRefObject<T>;
//MutableRefObject interface

function useRef<T = undefined>(): MutableRefObject<T | undefined>;

interface MutableRefObject<T> {
    current: T;
}

function useRef<T>(initialValue: T|null): RefObject<T>;
// RefObject interface
interface RefObject<T> {
    readonly current: T | null;
}

```

## <ins><b>Class state에서의 주의점</b></ins>

class component에서 state를 constructor의 내부에 작성을 해주거나 constructor없이 작성을 해 줄 수 있다.

만약에 아래와 같이 React.Component에서 State 타입을 지정해주고, state를 constructor의 외부에 작성을 해주면, state에 빈 배열이 있는 경우 타입 에러가 난다.
즉, Component에서 제네릭으로 적용한 state의 타입적용이 안된다. 따라서 state에 직접 state의 타입을 지정해줘야 한다.

```javascript
interface IState {
  state: string;
  message: string;
  result: string[];
}
class Example extends Component<{}, IState> {
  state: IState = {
    state: '',
    message: '',
    result: []
  };
}
```
