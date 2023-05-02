---
title: 210422 Are stack frame and execution context the same thing in JavaScript?
date: 2021-04-22 12:19:42
tags:
  - Stack-frame
  - Execution-context

categories:
  - JavaScript
---

<div align="center">
  <img src="/images/post_images/210422_terminology.jpeg" alt="Programming Terminology" />
</div>

이번 포스팅에서는 자바스크립트를 공부하다가 용어상 혼동되는 부분이 있어 간단하게 정리하고 넘어가고자 한다.
계속 공부를 해나가면서 혹시 용어상 혼동되는 부분이 있으면 이 포스팅 글에 지속적으로 업데이트를 해나가도록 하겠다.

## Stack frame vs Execution context

알고리즘 관련 공부를 하다가 함수 실행과 관련된 글을 읽던 중에 스택 프레임(Stack frame)이라는 말이 나왔다. 함수가 실행되면 함수의 매개변수와 지역변수, 복기주소로 구성된 스택 프레임이 스택에 쌓이게 되고, 함수가 최종적으로 실행이 되면 복기주소를 통해서 자기가 호출되었던 주소로 되돌아 간다고 한다.
이전에 자바스크립트에서 스코프에 대해서 공부를 했을때, 실행 컨텍스트에 대해서도 공부를 했었는데 스택 프레임과 같은 개념인 것 같아서 찾아보니 용어만 다를 뿐 같은 것이라는 것을 알게 되었다.

이전에 스코프와 실행 컨텍스트에 대해서 공부하면서 정리했던 포스팅의 링크를 아래에 첨부해둔다.
다시 한 번 반복해서 읽어보자.
[https://leehyungi0622.github.io/2021/03/19/202103/210319-javascript-basic_til/](https://leehyungi0622.github.io/2021/03/19/202103/210319-javascript-basic_til/)

## Call stack vs Execution stack

콜 스택(Call stack)과 컨텍스트 스택(Context stack)이 있는데 콜 스택은 자바스크립트 코드가 실행될때 생성된 실행 컨텍스트를 보관하는 LIFO 스택이라는 것은 공부를 통해 알고 있다. 그런데 다른곳에서는 같은 개념의 설명을 컨텍스트 스택이라고 하는 곳도 있었다.
이 역시 스택 프레임과 실행 컨텍스트와 같이 다른 용어지만 같은 것이라고 한다.
