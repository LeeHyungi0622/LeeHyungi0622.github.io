---
title: 210316 JavaScript TIL 5일차/6일차 - ES6 함수의 추가 기능과 배열
date: 2021-03-16 14:26:42
tags:
  - Self-Development
  - JavaScript
  - JavaScript-Basic
  - Incomplete

categories:
  - JavaScript
---

![](/images/post_images/javascript_logo.png)

## Preview

이번 예습 범위는 26장 ES6 함수의 추가 기능과 27장 배열이었다. 이전 예습범위를 공부하면서 자바스크립트가 프로토 타입 기반의 언어라는 부분과 함수형 객체 인스턴스가 어떻게 생성이 되는지에 대해 좀 자세히 공부를 했었는데, 그 덕분에 여러가지로 수월하게 26장의 내용을 이해할 수 있었던 것 같다. 이번 기회로 프로토 타입과 관련한 내용을 개인적으로 찾아가며 학습할 수 있었던 좋은 기회였던 것 같다.
콜백함수와 고차 함수의 개념, 프로토 타입 메서드, 정적 메서드 등의 개념을 이해하고 27장을 보니 이전에는 단편적으로만 보였던 메서드들이 다양하게 구분되어 보이기 시작했다. 역시 아는만큼 보이는 것 같다.
아직 개념적으로 공부해야 될 부분이 많기 때문에 지금 느끼는 재미로 좀 더 확장성 있게 공부하도록 해야겠다.

## <ins><b>정적 메서드, 프로토타입 메서드, 인스턴스 메서드</b></ins>

  <!-- more -->

## <ins><b>얕은 복사(shallow copy), 깊은 복사(deep copy)</b></ins>

- ### spread 연산자와 concat() 함수를 통한 얕은 복사(shallow copy)

결론부터 말하면 실무에서 객체를 복사(얕은 복사)할때 spread 연산자를 사용해서 작성을 한다. 실제로 객체를 요소로 갖는 리스트를 얕은 복사를 하게 되면 리스트 내부의 모든 객체들을 복사하지 않는다. 모든 객체를 복사한다는 것은 메모리상 부담이 되고, 퍼포먼스상 문제가 된다.
얕은 복사의 경우에는 원본과 사본의 식별자를 서로 비교할 경우 참조주소는 다르기 때문에 비교 연산자를 이용해서 비교를 하게 되면 서로 다름을 확인할 수 있다. 하지만 객체 속성 자체를 비교 연산자를 이용해서 비교하게 되면 서로 같는 결과를 확인할 수 있다.
반면에 깊은 복사를 하는 경우에는 속성 자체를 비교 연산자를 이용해서 비교해도 다르다는 결과가 나온다.
깊은 복사를 할때에는 리스트 내부에 있는 객체의 속성 중에서 원시타입이 아닌 객체 타입을 별도로 구분해서 복사를 하게 된다.

- ### Lodash clone deep
  → [https://lodash.com/docs/](https://lodash.com/docs/)

## Review

<table>
  <tr>
    <td>
      <img src="/images/post_images/210316_javascript_note1.png" alt="노트필기 첫번째 사진">
    </td>
    <td>
      <img src="/images/post_images/210316_javascript_note2.png" alt="노트필기 두번째 사진">
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210316_javascript_note3.png" alt="노트필기 세번째 사진">
    </td>
    <td>
      <img src="/images/post_images/210316_javascript_note4.png" alt="노트필기 네번째 사진">
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210318_javascript_review_note1.png" alt="노트필기 다섯번째 사진">
    </td>
    <td>
    </td>
  </tr>
</table>
