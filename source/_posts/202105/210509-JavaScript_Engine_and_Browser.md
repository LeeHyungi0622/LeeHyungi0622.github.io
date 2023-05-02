---
title: 210509 JavaScript engine과 Web API 그리고 Callback queue
date: 2021-05-09 21:10:42
tags:
  - JavaScript
  - Node
  - JavaScript-Engine
  - Web-API
categories:
  - JavaScript
---

![](/images/post_images/210509_javascript_runtime.png)

<br/>

`자바스크립트의 비동기 코드가 runtime에 의존적이라고 하는 이유`에 대한 의문을 시작으로 이번 포스팅을 시작하려고 한다. 이전에 비동기 함수를 테스팅하는 과정에서 에러가 발생을 했었는데 이때 비동기 코드가 runtime에 의존적인 결과코드이기 때문에 별도의 설정을 해줘야 했고, 이 에러를 겪고 해결해나가기 전에는 그 이유에 대해서 잘 몰랐었기 때문에 혹시 과거의 나와 같이 의문을 갖는 사람들을 위해 포스팅을 해보려고 한다.

이 이유에 대해서 이해하려면 자바스크립트 코드의 실행과정에 대해서 우선적으로 이해를 해야한다.

- ## 자바스크립트 코드의 실행과정

자바스크립트에서는 작성된 코드들은 call stack에 쌓인 후에 실행이 된다. 하지만 JS엔진에서는 비동기 작업을 지원하지 않기 때문에 코드들 중에서 `비동기 작업`을 Web API(browser에 의해 제공)에 위임을 한다.

- ## <ins><b>JS runtime engine의 역할</b></ins>

  JS runtime engine은 standard JS 코드를 실행하는 역할을 하는 JS engine(V8 for chrome)로 아래와 같은 프로그래밍 언어로써 독립적으로 요구되는 특징들을 지원한다.

  - variable, functions,
  - scoping, scope chaining, execution context, execution scope

- ## <ins><b>Web API의 역할</b></ins>

  - Web API는 Browser로부터 제공되는 wrapper로, 표준 JS 프로그래밍 언어의 일부가 아니며, 아래의 기능들을 지원한다.
  - ajax, events(onkeyPress, onBlur와 같은 이벤트들)
  - console.log, window object, DOM을 읽고 쓰기와 같은 기능
  - 비동기 작업(Asynchronous task)지원
  - 표준 JS 언어는 위와같은 기능들을 지원하지 않기 때문에 브라우저는 JS 엔진을 브라우저의 custom wrapper들로 감싸서 웹에서의 여러 조작들이 지원 가능하도록 한다.
    (`위와 같은 이유로 browser가 JS의 runtime으로 불린다`)

    `cf) Node는 JS에게 위와 같은 기능들을 제공하는 server side에서 동작하는 runtime이다.`

  <!-- more -->

JS 엔진은 비동기 작업들을 Web API에 위임한 후에 call stack으로 다시 돌아가서 동기 작업에 대한 작업을 지속한다.
그리고 Web API는 넘겨받은 비동기 작업들을 수행한 뒤에 콜백함수를 callback queue로 넘긴다.

이벤트 루프(event loop)는 `callback queue에서 대기 상태로 보관이 되었던 task(비동기 작업)`를 call stack에 쌓여져있는 실행 컨텍스트들이 모두 실행이 되는 시점에 call stack으로 넘기게 된다.

call stack으로 넘긴 비동기 작업의 실행이 끝나고 나면, 이벤트 루프는 task queue로부터 대기중인 또 다른 비동기 작업이 있는지 검사를 하고 있다면 call stack으로 다시 넘기게 된다.

(이벤트 루프(Event loop)는 call stack에 현재 실행 중인 실행 컨텍스트(execution context)가 없는지와 task queue에 실행할 task가 있는지 반복적으로 확인을 한다.)

`자바스크립트의 비동기 코드가 runtime에 의존적이라고 하는 이유`는 자바스크립트 엔진은 자바스크립트 언어로써의 특징만을 지원하며, 비동기와 같은 기능들은 지원을 하지 않기 때문에 browser와 같은 별도의 runtime에 의존할 수 밖에 없기 때문이다.

## 자바스크립트는 싱글 스레드 기반이지만, 비동기 처리가 가능하다.

자바스크립트는 싱글 스레드 기반이지만, 비동기 작업들을 runtime 환경에 위임함으로써 작업들이 완료될 때까지 다른 코드들을 실행할 수 있는 것이다.

`2021/05/10 Update`

## Synchronous와 Asynchronous 그리고 Blocking과 Non-Blocking

Synchronous는 요청을 보낸 뒤에 해당 요청에 대한 응답을 받아야 다음 동작을 실행하는 방식을 말하며, Asynchronous는 요청을 보낸 후의 응답과 관계없이 다음 동작을 순차적으로 실행하는 것을 말한다.
Blocking은 자신의 수행결과가 끝날 때까지 제어권을 갖고 있는 것을 말하며, Non-Blocking은 요청이 되었을때 제어권을 자신을 호출한 쪽으로 넘기고 자신을 호출한 쪽에서 다른 일을 할 수 있도록 하는 것을 말한다.
