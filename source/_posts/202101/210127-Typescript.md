---
title: 210127 TypeScript TIL
date: 2021-01-27 06:00:42
tags:
  - TypeScript
  - self-development
  - TIL
categories:
  - TypeScript
---

![](/images/post_images/typescript_logo.png)

> **이 포스팅은 Udemy : Understanding TypeScript - 2021 Edition 에서 개별 학습한 내용을 기반으로 작성하였습니다.**

## **오늘 공부한 내용**

- **TypeScript에 대한 개념 이해**

  - TypeScript는 JavaScript의 Superset으로, 기존의 자바스크립트 위에 building된 언어이다.
    기존에 JavaScript에는 없던 각종 이점들을 추가한 언어라고 생각하면 된다. 하지만 브라우저에서는 실행할 수 없기 때문에 작성한 TypeScript는 JavaScript로 컴파일 된 후에 실행된다.

    <br/>

- **TypeScript의 이점**

  - JavaScript에서의 변수는 단순히 상수(const)와 변수(let)으로만 구분해서 사용하고, 별도로 타입(Type)을 지정하지 않았다. 이로인해 함수의 인자로 넘겨주는 인자값이 어떤 것이든 입력이 되었고, 이로 인해 발생하는 에러는 컴파일(compile)단계에서 잡아서 수정할 수 있었다. 하지만 TypeScript를 사용해서 개발하게 되면, 개발단계에서 미리 에러를 잡아서 수정할 수 있다.
  - 물론 JavaScript에서도 변수의 타입을 검사해서 유효성 검사 처리를 추가할 수 있다. 하지만 TypeScript를 사용해서 Type을 지정해서 처리하는 것 만큼 효율적이지 않다.

  ```javascript
  function add(num1, num2) {
    if (typeof num1 === 'number' && typeof num2 === 'number') {
      return num1 + num2;
    } else {
      return +num1 + +num2;
    }
  }
  console.log(add('2', '3'));
  ```

    <!-- more -->

  - Non-JavaScript Features인 Interfaces와 Generics을 활용할 수 있다.
  - Meta-Programming 특징인 Decorators를 활용하여 개발할 수 있다.

  <br/>

- **TypeScript 설치 및 사용**

  - npm command를 사용하기 위해 node.js를 설치한다.
  - global하게 TypeScript를 설치한다.

  ```shell
  $ npm install -g typescript
  ```

  - tsc를 사용하여 TypeScript 파일을 컴파일할 수 있다.

  ```shell
  $ tsc test.ts
  ```

  <br/>

  **앞서 작성했던 JavaScript 코드에 TypeScript를 적용해서 작성해보도록 한다.**

  **using-ts.ts**

  ```typescript
  const button = document.querySelector('button');
  const input1 = document.getElementById('num1')! as HTMLInputElement;
  const input2 = document.getElementById('num2')! as HTMLInputElement;

  function add(num1: number, num2: number) {
    return num1 + num2;
  }

  button.addEventListener('click', function () {
    console.log(add(+input1.value, +input2.value));
  });
  ```

# **TypeScript 공부순서**

- TypeScript Basics
- Compiler & Configuration Deep Dive
- Working with Next-gen JS Code
- Classes & Interfaces
- Advanced Types & TypeScript Features
- Generics
- Decorators
- Time to Practice - Full Project
- Working with Namespace & Modules
- Webpack & TypeScript
- Third-Party Libraries & TypeScript
- React + TypeScript & NodeJS + TypeScript

# **공부하는 방법**

(1) Watch the lecture
(2) Code Along (Pause & Rewind)
(3) Practice (Advance on your Own(Partly))
(4) Debug & Search (Use attached Code, Google, Udemy Search)
(5) Ask & Answer (Ask in Q&A section, but also help others!)
