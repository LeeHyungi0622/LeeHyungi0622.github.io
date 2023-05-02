---
title: 210307 HTML/CSS Flex (작성중...)
date: 2021-03-07 15:58:42
tags:
  - HTML-CSS
  - Flex
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/210307_flex_structure.jpg" alt="Flex"/>
</div>

우선 flex에 대해서 알아보기 전에 classic한 방식으로 block 요소를 배열하는 방법에 대해서 간단하게 알아보자.

## block과 inline-block

왼쪽은 display 속성이 block인 요소의 배열을 나타낸다. 배열의 형태에서 유추해 볼 수 있듯이 block요소의 옆에는 어떠한 element도 올 수 없다. 그럼 div 요소의 display 속성을 inline-block으로 바꾸게 되면 어떻게 될까?

바로 오른쪽 캡쳐와 같이 일렬로 배열되는 것을 확인할 수 있다. inline-block으로 display 속성을 바꾸게 되면, 서로 옆으로 정렬된 형태로 box들이 정렬된다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210307_css_block.png" alt="디스플레이 속성이 블럭인 요소 이미지">
    </td>
    <td>
      <img src="/images/post_images/210307_css_inline_block.png" alt="디스플레이 속성이 인라인 블럭인 요소 이미지">
    </td>
  </tr>
</table>
그렇다면 <ins><b>inline-block과 inline의 차이</b></ins>는 무엇일까?

**inline은 유동적이기 때문에 너비와 높이가 없는 요소** 이다. 대표적인 inline 요소는 anchor 태그가 있다.

  <!-- more -->

이러한 box 요소를 화면에 유연하게 배치할 수 있도록 등장한 modern한 방법이 바로 flex와 grid이다. 우선 간단히 flex에 대해서 알아보도록 하겠다.

## Flexbox의 규칙

Flexbox의 첫 번째 규칙은 **Don't talk to children** 이다. flex로 display 속성을 줘서 요소들을 배치하기 위해서는 child 요소를 아우르는 부모 요소의 display 속성에 display 속성 값을 넣어줘야 한다.
예를들어 앞서 살펴본 div 박스의 화면구성에서 div박스들을 정렬하기 위해서는 모든 div 박스들의 부모 요소인 body에 display: flex 속성을 넣어줘야 한다.

## Main Axis='row'(justify-content) and Cross Axis(align-items)

flex-direction의 default 값은 row이다. 포스팅의 main 이미지를 살펴보면 flex구조에서의 main axis와 cross axis의 방향 구조에 대해서 이해할 수 있다.

default 상태일 때에는 justify-content 속성 값을 사용해서 수평축을 기준으로 flex children의 위치를 정렬할 수 있다. 그리고 align-items 속성을 이용해서는 수직축을 기준으로 flex children의 위치를 정렬할 수 있다.

만약에 flex-direction을 통해서 default인 row 값을 column으로 바꾸게 되면, default 상태일 때와는 반대로 정렬의 기준이 적용된다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210307_flex_default_img.png" alt="디스플레이 속성이 flex인 요소의 이미지">
    </td>
    <td>
      <img src="/images/post_images/210307_align_items_stretch.png" alt="디스플레이 속성이 인라인 블럭인 요소 이미지">
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>justify-content: space-around + align-items: center</b>
    </td>
    <td align="center">
      <b>align-items: stretch(no height property)</b>
    </td>
  </tr>
</table>
