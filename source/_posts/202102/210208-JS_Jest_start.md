---
title: 210208 Jest의 개념과 기본사용
date: 2021-02-08 09:33:42
tags:
  - Jest
  - JavaScript-unit-test
categories:
  - JavaScript-Test
---

![](/images/post_images/jest_logo.png)

<br/>

# Jest

Jest는 Facebook에 의해서 개발된 JavaScript 테스트 프레임워크이며, 대규모 Web application의 테스트를 좀 더 심플하게 할 수 있도록 도와준다.

## Jest framework의 사용

간단한 JavaScript 코드를 Jest framework를 사용해서 테스트 해보도록 하자.

# **실습 내용**

**실습 Repository** : [https://github.com/LeeHyungi0622/javascript-jest-test-practice-repo](https://github.com/LeeHyungi0622/javascript-jest-test-practice-repo)

1. 프로젝트의 branch 기본 구성은 master branch로 구성한다.

2. README.md 파일의 documentation작업을 제외한 모든 작업은 별도의 branch를 새로 끊어서 작업을 시작한다.

<br/>
  <!-- more -->

# Basic setting in project

3.  Jest 프레임워크를 활용한 테스트를 위해 간단한 JavaScript 파일들을 추가해준다.
    (Add simple javascript files to do unit test using Jest framework.)

4.  프로젝트에 새로운 npm package를 초기화 시켜준다.
    (Generate project folder without asking any questions.)

    ```bash
    $ npm init -y
    ```

5.  Jest를 developer dependency로 구분해서 설치해준다.

    ```bash
    $ npm i -D jest
    ```

6.  package.json의 test script를 "jest"로 해준다.

    ```bash
    "scripts": {
        "test": "jest"
    },
    ```

7.  기본적으로 Jest에서는 **test** 폴더 아래에 있는 테스트 파일들을 검사하기 때문에 테스트를 하기 위해 작성한 파일들은 **test** 폴더의 아래에 위치시키도록 한다.

    - 작성하는 테스트 파일의 이름은 테스트 하고자 하는 function이 위치한 파일명.test.js 와 같은 형태로 파일명을 지어주도록 한다.

8.  **test code의 기본 작성법** - 작성한 test file에서는 테스트하고자 하는 function을 가진 파일을 import해서 해당 function를 테스트해야 한다.

    ```javascript
    const { test, expect } = require('@jest/globals');
    const sum = require('../sum');

    // 작성한 테스트코드가 무엇을 하는지에 대해서 첫번째 parameter로 작성해준다.
    test('properly adds two numbers', () => {
      // expected result
      expect(sum(1, 2)).toBe(3);
    });
    ```

    테스트하고자 하는 function은 위와 같이 `expect()`에 인자와 함께 넣어주고, 예상되는 값을 `toBe()`의 인자로 넣어서 검사를 하게 된다. toBe() 외에도 다양한 검사 조건이 있다.

9.  Test 결과 확인

    ```bash
    > jest

    PASS  __test__/sum.test.js
    ✓ properly adds two numbers (2 ms)

    Test Suites: 1 passed, 1 total
    Tests:       1 passed, 1 total
    Snapshots:   0 total
    Time:        1.844 s
    Ran all test suites.
    ```

10. 구체적인 Test의 결과 확인
    package.json에서 test command script를 아래와 같이 수정을 해준다.
    다음의 --coverage 옵션을 넣어주면 각 test파일별로 구체적인 test의 정보를 확인할 수 있다.

    ```bash
    "scripts": {
        "test": "jest --coverage"
    }
    ```

    ```bash
    > jest --coverage
    PASS  __test__/cloneArray.test.js
    PASS  __test__/sum.test.js
    PASS  __test__/subtract.test.js
    ---------------|---------|----------|---------|---------|-------------------
    File           | % Stmts | % Branch | % Funcs | % Lines | Uncovered Line #s
    ---------------|---------|----------|---------|---------|-------------------
    All files      |     100 |      100 |     100 |     100 |
    cloneArray.js  |     100 |      100 |     100 |     100 |
    subtract.js    |     100 |      100 |     100 |     100 |
    sum.js         |     100 |      100 |     100 |     100 |
    ---------------|---------|----------|---------|---------|-------------------

    Test Suites: 3 passed, 3 total
    Tests:       3 passed, 3 total
    Snapshots:   0 total
    Time:        3.778 s
    ```

    자동생성된 coverage 폴더의 Icov-report 폴더 내부의 index.html 파일을 열어보면 아래와 같이 test 정보를 웹 페이지에서 확인할 수 있다.

    ![](/images/post_images/210208_test_coverage_page.png)
