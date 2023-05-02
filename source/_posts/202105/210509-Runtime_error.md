---
title: 210509 ReferenceError regeneratorRuntime is not defined
date: 2021-05-09 19:37:42
tags:
  - Unit-test
  - JavaScript
  - Node
categories:
  - Resolved-Error
---

![](/images/post_images/error_solved_img.png)

<br/>

NodeJS 프로젝트를 진행하면서 비동기로 작성한 함수를 위한 테스트 코드를 작성하고 실행을 했는데 `ReferenceError: regeneratorRuntime is not defined` 에러가 발생했다.
이전에 ReactJS 프로젝트를 진행하면서 테스트 코드를 작성했을때도 위와 똑같은 에러가 발생했었는데 이러한 경우에는 아래의 두 방법으로 해결을 할 수 있다.

**Solution 1)** `regenerator-runtime`를 설치해서 비동기 함수를 작성한 파일의 최상단에 `import 'regenerator-runtime/runtime';`를 선언해주면 간단하게 해결할 수 있다.

**Solution 2)** .babelrc 의 설정을 아래와 같이 업데이트한다.

> Using @babel/preset-env without that target set will result in the tests using async failing with ReferenceError: regeneratorRuntime is not defined.

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "node": "current"
        }
      }
    ]
  ]
}
```

이 regenerator-runtime이 무엇인가 한 번 구글링을 해보니,
`regenerator-runtime`은 compiled/transpiled 비동기 함수들을 위한 runtime을 지원해주는 녀석이라고 한다.
Babel과 같은 컴파일러는 자바스크립트 syntax에 대해서 최신 문법을 이전 문법으로 변환을 해주지만, runtime에 의존적인 결과 코드(비동기 코드)들은 `regenerator-runtime`으로부터 지원을 받는다고 한다.
