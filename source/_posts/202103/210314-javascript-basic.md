---
title: 210314 JavaScript TIL 2일차/3일차 - 원시타입과 객체 타입, 값/참조에 의한 전달, 유사배열 객체 String
date: 2021-03-14 13:11:42
tags:
  - Self-Development
  - JavaScript
  - JavaScript-Basic
  - Incomplete

categories:
  - JavaScript
---

![](/images/post_images/javascript_logo.png)

## 원시타입과 객체 타입

원시타입 변수의 경우 값을 재정의 하는 경우, 기존에 사용하던 메모리 위치에 다시 덮어쓰지 않고, 다시 새로운 메모리 위치에 새로운 값을 저장한다.

반면 객체 타입 변수의 경우, 객체를 저장하고 있는 공간의 참조값(주소)을 저장하고 있는 공간과 객체의 값을 저장하고 있는 공간, 두 공간을 갖는다.
따라서 객체 값을 저장하고 있는 변수의 값을 변경하는 경우, 메모리상에 저장되어있는 객체의 값을 수정하게 되는 것이다.

<br/>

- ### 값에 의한 전달

  만약 변수에 원시 값을 갖는 변수를 할당하는 경우, 할당되는 변수에 원시 값이 복사되어 전달된다. (다른 메모리 주소 공간에 복사)

<br/>

  <!-- more -->

- ### 참조에 의한 전달

  객체는 원시 값과 달리 확보해야 할 메모리 공간의 크기를 사전에 정해 둘 수 없다. (프로퍼티의 수가 정해지지 않고 동적으로 추가/삭제)
  객체를 할당한 변수는 <ins><b>참조 값(Reference value)</b></ins>을 값으로 갖는다. (객체가 저장된 메모리 공간의 주소) 해당 메모리 공간은 실제 객체의 값을 저장하고 있는 또 다른 메모리 공간을 참조한다.
  따라서 아래와 같이 참조 값을 복사해서 또 다른 변수에 할당하는 경우, 참조 값(주소)을 저장하고 있는 메모리 공간이 복사되어, 원본과 사본 모두 같은 객체를 공유한다. 따라서 어느 한 쪽에서 객체를 변경하는 경우 <ins><b>두 변수의 값이 모두 영향을 받는다.</b></ins>

  ```javascript
  var human = {
    welcome: 'Hello'
  };

  var copyHuman = human;

  copyHuman.welcome = 'Halo';

  console.log(human);
  console.log(copyHuman);
  ```

  `output:`

  ```javascript
  VM280:9 {welcome: "Halo"}welcome: "Halo"__proto__: Object
  VM280:10 {welcome: "Halo"}welcome: "Halo"__proto__: Object
  ```

## String은 유사배열 객체 (Array-like-Object)이면서 반복 가능한 객체

유사배열 객체가 되기 위해서는 우선 `length 속성이 필요`하고, `index가 0부터 시작하여 1씩 증가`해야 한다.
String 타입은 유사배열 객체로 반복문을 통해 순회가 가능하다. (iterable)
