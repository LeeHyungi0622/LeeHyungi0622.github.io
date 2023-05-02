---
title: 210504 React TDD Practice with RTL
date: 2021-05-04 13:48:42
tags:
  - React-router-test
  - React-testing-library
  - Unit-testing
  - Jest
  - TDD
categories:
  - React-Test
---

<div align="center">
  <img src="/images/post_images/210504_rtl_img.png" alt="RTL(React Testing Library)" />
</div>

<br/>

RTL(React-Testing-Library)는 `모든 테스트를 DOM 위주`로 한다. `별도로 props나 state를 조회하는 테스트는 하지 않는다.` 그 이유는 컴포넌트를 리팩토릴할때 내부 구조와 네이밍은 많이 바뀔 수 있어도 실제 동작방식은 크게 바뀌지 않기 때문이다.
따라서 RTL에서는 컴포넌트의 기능이 똑같이 동작한다면, 컴포넌트의 내부 구현 방식이 바뀌어도 실패하지 않도록 테스트를 지원한다.

- DOM 시뮬레이션은 JSDOM이라는 도구를 사용하여 document.body에 React 컴포넌트를 렌더링한다.

- `@testing-library/jest-dom` 은 DOM관련 matcher를 사용할 수 있게 지원해주는 라이브러리이다.

## <ins>**snapshot 테스트**</ins>

```javascript
import { render } from '@testing-library/react';
import Profile from './Profile';

describe('<Profile /> 컴포넌트 테스트', () => {
  test('snapshot 테스트',() => {
    const snapshot = render(<Profile surname="lee", givenname="hyungi" />);
    // container는 검사하는 컴포넌트의 최상위 DOM을 가르킨다.
    expect(snapshot.container).toMatchSnapshot();
  });
});
```

  <!-- more -->

## <ins>**getByText**</ins>

텍스트를 사용해서 원하는 DOM을 선택할 수 있다.
`App.js`

```javascript
const CustomInput = ({ children, value, onChange }) => {
  return (
    <div>
      <label htmlFor="search">{children}</label>
      <input
        id="search"
        type="text"
        placeholder="PlaceholderText"
        value={value}
        onChange={onChange}
      />
    </div>
  );
};

const App = () => {
  const [text, setText] = useState('');

  const handleChange = (e) => {
    setText(e.target.value);
  };

  return (
    <div>
      <CustomInput value={text} onChange={handleChange}>
        Input:
      </CustomInput>
      <p>You typed: {text ?? '...'}</p>
    </div>
  );
};

export default App;
```

`App.test.js`

```javascript
import { render, screen } from '@testing-library/react';

describe('When everything is OK', () => {
  test('should render the App component without crashing', () => {
    render(<PosterSection />);
    screen.debug();
  });

  test('should select the children that is being passed to the CustomInput component', () => {
    render(<App />);
    screen.getByText('Input:');
    // getByText의 인수로 정규표현식을 넣어줄 수 있다.
    screen.getByText('/Input/');

    // screen.getByText("Input")에서 에러가 발생하기 때문에 not.toBeInTheDocument에 대한 체크를 할 수 없다.
    expect(screen.getByText('Input')).not.toBeInTheDocument();
  });
});
```

screen에 없는 텍스트를 체크하면 에러가 발생하기 때문에 .not.toBeInTheDocument matcher를 이용해서 테스트를 할 수 없다.
이 경우에는 아래와 같이 발생한 에러를 변수에 담아서 해당 변수에 값이 정의되어있는지 확인을 통해 테스트를 진행할 수 있다.

```javascript
let error;
try {
  // screen에서 Input은 존재하지 않기 때문에
  // error 발생
  screen.getByText('Input');
} catch (err) {
  // 발생한 에러에 대한 정보를 error 변수에 담기
  error = err;
}

// error 변수에 에러에 대한 정보가 정의되어 있는지 확인
expect(error).toBeDefined();
```

## <ins>**getByRole**</ins>

getByText 대신에 태그의 ARIA role 속성을 이용(getByRole)해서 screen에 표시된 텍스트를 참조/테스트할 수 있다.

```javascript
test('should select the input element by its role', () => {
  render(<PosterSection />);
  screen.getByRole('textbox');
  // input tag에 대한 role(ARIA property)로부터
  expect(screen.getByRole('textbox')).toBeInTheDocument();
});
```

label 태그의 text가 children으로 정의된 텍스트로 되어있기 때문에 아래와 같이 getByLabelText를 사용해서 label 요소를 선택할 수 있다.

```javascript
test('should select a label element by is text', () => {
  render(<PosterSection />);
  screen.getByLabelText('Input:');
});
```

input 태그의 placeholder 텍스트를 이용해서 DOM

```javascript
test('should select input element by placeholder text', () => {
  render(<PosterSection />);
  screen.getByPlaceholderText('PlaceholderText');
});
```

## queryBy vs getBy

getBy\*의 경우에는 존재하지 않는 요소에 대한 검사를 하는 경우, 에러를 반환하지만, queryBy\*의 경우에는 에러를 반환하지 않는다.
따라서 null이나 존재하지 않는 DOM요소를 검사할때에는 getBy말고 queryBy를 사용해서 테스트를 하도록 하자.

- ## queryByRold

```javascript
// 존재하지 않는 요소를 검사
const result = screen.queryByRole('textbox');
console.log(result); // null
test('should not find the role 'whatever' in our component',() => {
  expect(screen.queryByRole('whatever')).toBeNull();
});
```

jest --watch로 테스트하면 여러 옵션들이 있는데, t 옵션을 이용해서 사용한 test 이름을 regex 패턴을 사용해서 검색할 수 있다.
`(Press t to filter by a test name regex pattern.)`
위와같이 console.log를 사용해서 결과값을 확인하고자 할때 사용하면 유용한 옵션이다.

## findBy search variant

비동기 이벤트에 대한 테스트 코드 작성

- ## jest-environment-jsdom-sixteen 패키지 설치
  ```zsh
  $ npm i -D jest-environment-jsdom-sixteen
  ```
- ## test command script 작성
  ```json
  "test": "jest --watch --env=jest-environment-jsdom-sixteen"
  ```

`jest.config.js`

```javascript
module.exports = {
  testEnvironment: 'jest-environment-jsdom-sixteen'
};
```

`App.js`

```javascript
function getUser() {
  return Promise.resolve({ id: '1', name: 'lee' });
}

const App = () => {
  const [user, setUser] = useState(null);

  useEffect(() => {
    const fetchUser = async () => {
      const user = await getUser();
      setUser(user);
    };
    fetchUser();
  }, []);
};
```
