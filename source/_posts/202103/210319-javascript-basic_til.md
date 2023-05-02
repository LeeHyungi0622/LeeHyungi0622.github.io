---
title: 210319 JavaScript와 친해지기 - 조금은 낯설은 스코프에 대한 이야기
date: 2021-03-19 08:12:00
tags:
  - Self-Development
  - basic-term
  - TIL
categories:
  - JavaScript
---

![](/images/post_images/210319_Front-end-development.png)

이번 포스팅에서는 아직은 조금 낯설은 스코프(Scope)에 대해서 정리를 해보려고 한다.

ECMAScript의 자바스크립트 명세를 보면 자료형, 문법, 연산자, 실행코드와 실행컨텍스트 등 다양한 명세로 구분이 되어있는데 그 중에서 살펴 볼 파트는 9장 `실행코드와 실행 컨텍스트(Executable Code and Execution Contexts)`이다.

`ft ECMA-262/March 18, 2021`

<ins><b>ECMAScript 2022 Language Specification(Chapter.9)</b></ins>
[https://tc39.es/ecma262/#sec-executable-code-and-execution-contexts](https://tc39.es/ecma262/#sec-executable-code-and-execution-contexts)

## 스코프(Scope)

<!-- more -->

### 스코프는 왜 등장했는가?

![](/images/post_images/210319_collision_img.jpeg)

스코프는 프로그램상의 식별자(이름)의 충돌을 막기 위해서 등장하였다.

초창기 프로그래밍 언어에서는 모든 식별자(이름)을 하나의 대응표에서 관리를 했는데, <ins><b>사용되는 식별자(이름)의 충돌(collision)로 인해 스코프(Scope)라는 개념이 등장했다.</b></ins> 스코프는 이러한 충돌 문제를 해결하기 위한 `규칙`으로써 정의된다.

### 대응표의 역할과 등장배경

프로그래밍에서는 변수와 함수에 식별자(이름)을 부여하여 메모리상의 주소와 매핑해서 변수 및 함수의 재사용성을 가능하게 한다.

따라서 프로그램에서는 이름과 값을 1:1로 매핑한 대응표를 만들어서 관리를 한다. 이 대응표의 이름을 통해 값의 조작이 용이해진다.

이 대응표는 프로그램내에 존재하는 이름(식별자)를 관리하는데 사용되는데 만약 하나의 대응표로 모두 관리를 한다면 이름(식별자)들 사이에서 충돌이 발생한다. 따라서 스코프라는 규칙을 적용하여 대응표를 작성하여 관리한다.

### 스코프와 함수의 관계

스코프의 동작 방식은 함수와 밀접한 관련이 있다. 1급 객체로서의 함수는 특징에 대한 명세의 내용을 보면, 스코프의 특징의 전반적인 내용에 대해서 명시하고 있다.

### 스코프의 규칙

최신 버전의 자바스크립트(ES6)에는 함수 레벨과 블록 레벨의 렉시컬 스코프 규칙을 따른다.

- #### 스코프 레벨 (함수 레벨과 블록 레벨)

  우선 함수레벨 스코프에 대해서 살펴보자.
  자바스크립트는 `전통적으로 함수 레벨의 렉시컬 스코프 규칙을 지원`했다. 전통적인 자바스크립트 변수 선언방식을 보면 var 키워드를 사용해서 선언을 하는데, 아래와 같이 특정 함수내의 if조건문 블럭에서 var 키워드로 변수를 선언하게 되면, 함수 레벨의 렉시컬 스코프 규칙을 갖기 때문에 함수 내부라면 어디서든 해당 변수의 값을 참조할 수 있다.

  `함수 레벨 스코프 예시)`

  ```javascript
  function getName() {
    if (true) {
      var name = 'Lee Hyungi';
      console.log(name);
    }
    console.log(name);
  }
  getName();
  ```

  그렇다면 블록 레벨의 렉시컬 스코프 규칙은 어떻게 다를까?
  우선 ES6의 let, const 키워드를 사용해서 변수를 선언하게 되면 블록레벨 스코프를 생성해준다. 따라서 if 선언문 블럭 내부에서 선언해준 변수는 해당 블럭이 종료되는 시점에 파괴되어 외부에서 참조가 불가능한 변수가 된다.

  `블록 레벨 스코프 예시)`

  ```javascript
  function getName() {
    if (true) {
      const name = 'Lee Hyungi';
      console.log(name);
    }
    console.log(name); // ReferenceError(참조에러)
  }
  foo();
  ```

- #### 스코프를 결정하는 규칙

  이제 스코프에 대한 기본적인 개념과 스코프 레벨(함수 레벨 스코프와 블록 레벨 스코프)에 대해서 살펴보았으니, `스코프를 결정하는데 사용되는 스코프 규칙에 대해서 정리`해보려고 한다.
  스코프 규칙에는 크게 동적 스코프(Dynamic scope)와 정적 스코프(Static scope)로 분류할 수 있다.
  동적 스코프란 런타임 도중의 실행 컨텍스트(Execution Context)나 호출 컨텍스트에 의해서 스코프가 결정되는 것을 말한다.
  반면, 정적 스코프(Static scope)란 렉시컬 스코프(lexical scope)라고도 불리며, 소스코드가 작성된 문맥에 의해 스코프가 결정된다.

<br/>

<table>
  <tr>
    <td>
      <img src="/images/post_images/210320_scope_study_note1.png" alt="스코프 공부 노트1"/>
    </td>
    <td>
      <img src="/images/post_images/210320_scope_study_note2.png" alt="스코프 공부 노트2"/>
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210320_scope_study_note3.png" alt="스코프 공부 노트3"/>
    </td>
  </tr>
</table>

- #### 실행 컨텍스트(Execution Context)

  그럼 잠깐 여기서 `실행 컨텍스트(Execution Context)`에 대해서 알아보자.
  실행 컨텍스트란 <ins><b>코드가 실행되는 위치를 설명한다는 의미</b></ins>에서 Execution Context라고 한다. 부가적으로 설명하자면 `자바스크립트 엔진이 코드를 실행하기 위해서는 코드에 대한 다양한 정보(변수, 함수, 스코프, this, arguments 등)가 필요한데 이러한 정보들을 묶어서 코드가 실행되는 위치를 설명`하는 것을 `Execution Context`라고 정의한다.
  간단히 말해 코드들이 실행되기 위한 환경으로 이해할 수 있다.
  자바스크립트 엔진은 Execution Context를 객체로 관리하고, 작성한 코드를 `Execution Context` 내에서 실행한다.

  그럼 Execution Context는 어떻게 생겼을까?

  ExecutionContext는 아래와 같이 LexicalEnvironment와 VariableEnvironment, 두 가지로 구성이 되어 있다.

  ```javascript
  ExecutionContext :{
    LexicalEnvironment:{
        Environment Records,
        Reference to the outer environment,
    },
    VariableEnvironment:{
        Environment Records,
        Reference to the outer environment,
    }
  }
  ```

  Lexical Environment와 VariableEnvironment의 <ins>`상세 내용과 관계에 대한 내용은 실행 컨텍스트(Execution Context)의 상단에 첨부한 노트 필기를 참고`</ins>하도록 하자.

  그럼 Execution Context에는 어떤 종류가 있을까?

  - <ins><b>Execution Context 종류</b></ins>
    - <ins><b>(1) Global Execution Context</b></ins>
      - 전역 실행 컨텍스트는 코드를 실행하는 역할을 하며, 단 한 개만 정의되는 Context이다.
      - 전역 실행 컨텍스트는 Call Stack에 가장 먼저 추가되며 어플리케이션이 종료될 때 삭제된다.
      - 전역 실행 컨텍스트는 global object를 생성하고 this 값에 global object를 참조한다.
    - <ins><b>(2) Functional Execution Context</b></ins>
      - 함수가 실행 될 때 마다 정의되는 Context이다.
      - 전역 실행 컨텍스트가 단 한 번만 정의되는 것과 달리 `함수 실행 컨텍스트는 매 실행시마다 정의`된다.
      - 함수 실행이 종료(반환문)되면 Call Stack에서 제거된다.
    - <ins><b>(3) Eval Context</b></ins>
      - eval() 함수로 실행한 코드의 Context이다.
      - 보안상 취약한 점이 있어 권장되지 않는 함수이다.

  <ins><b>그럼 Execution Context는 어떻게 관리가 되는지 구체적으로 알아보자.</b></ins>

  - <ins><b>Execution Context 관리</b></ins>

    앞서 Execution Context의 종류에 대해서 설명을 할때 언급을 했지만 자바스크립트 엔진이 각 각의 Execution Context들을 객체로써 Call Stack(호출스택) 내에서 관리한다.
    `자바스크립트는 단일 스레드(Single Thread)`이기 때문에 Runtime에 `단 하나의 Call Stack만 존재`한다.

    우선 제일 먼저 자바스크립트 엔진은 전역 범위의 코드를 실행하며 전역 실행 컨텍스트(Global Execution Context)를 생성해서 Call Stack(호출 스택)에 push를 한다.
    그 다음으로 함수가 함수가 실행 또는 종료 될 때마다 Global Execution Context 위로 `Functional Execution Context`를 추가(push)했다가 제거(pop)하는 작업을 반복합니다.

    이 Call Stack은 최대 stack 사이즈가 정해져있기 때문에 Call Stack에 쌓인 Context이 최대치를 넘을 경우, `RangeError: Maximum call stack size exceeded`라는 에러가 발생한다.
    이 에러를 `Stack Overflow`라고 한다.

    `Call Stack은 선입 후출(FILO-First In Last Out)로 내부 데이터가 추출`되며, Global Execution Context은 가장 먼저 push되어 어플리케이션이 종료되는 시점에 가장 마지막으로 pop된다.

    ```javascript
    let name = 'Lee Hyungi';

    function firstFn() {}

    function secondFn() {
      console.log(`Hi! Nice to meet you! My name is ${name}`);
    }

    firstFn();
    ```

    - <ins><b>실행 컨텍스트의 작동순서</b></ins>
      - (1) 코드의 전역 범위가 실행된다. `Global Execution Context`를 Call Stack에 push한다.
      - (2) firstFn이 실행된다.
      - (3) firstFn의 Functional Execution Context가 Call Stack에 push된다.
      - (4) secondFn이 실행된다.
      - (5) secondFn의 Functional Execution Context가 Call Stack에 push된다.
      - (6) console.log가 실행된다.
      - (7) secondFn내의 console.log의 Functional Execution Context가 Call Stack에 push된다.
        (Call Stack에 쌓여있는 Context 실행)
      - (8) `console.log의 실행이 완료`되며 console.log의 Functional Execution Context가 Call Stack으로부터 pop됩니다.
      - (9) `secondFn의 실행이 완료`되며 secondFn의 Functional Execution Context가 Call Stack으로부터 pop된다.
      - (10) `firstFn의 실행이 완료` firstFn의 Functional Execution Context가 Call Stack으로부터 pop된다.
      - (11) `어플리케이션이 종료될 때에 Global Execution Context가 pop`된다.
