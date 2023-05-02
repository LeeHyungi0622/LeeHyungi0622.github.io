---
title: 210304 HTML/CSS TIL - 키워드 & 세부내용 작성중...
date: 2021-03-04 22:40:42
tags:
  - HTML
  - CSS
  - TIL
  - Incomplete
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/html_css_logo.png" alt="HTML/CSS"/>
</div>

## <ins><b>HTML/CSS 여덟번째 수업</b></ins>

오늘은 HTML/CSS 여덟번째 수업날이었다. 오늘도 새로운 것에 대한 배움에 즐거운 하루였다.

<hr/>

## 적응형 웹 디자인(AWD)과 반응형 웹 디자인(RWD)

이번 수업의 시작은 반응형 웹 디자인을 실습하기 전에 반응형 웹과 관련된 이론적인 부분에 대해서 살펴보았다. 내용의 시작은 <ins><b>적응형과 반응형 웹에 대한 내용</b></ins>이었다.
이전에 개인적으로 네이버 포털사이트의 도메인 주소가 모바일에서는 m.으로 시작되고 데스크탑의 웹 주소에서는 www.으로 시작되는 것에 대해서 강사님께 질문을 했던 적이 있는데 이는 <ins><b>네이버에서는 적응형으로 어플리케이션을 개발했기 때문이다.</b></ins> 적응형은 각 플랫폼에 맞게 개별화된 웹 어플리케이션을 만드는 것을 의미하고, 반응형의 경우에는 하나의 코드에서 스타일링만 다르게 적용해서 화면 사이즈에 따라 다르게 콘텐츠의 구조를 출력해주는 것을 말한다.

  <!-- more -->

## 서버의 이원화

앞서 언급한 네이버 포털사이트의 적응형 어플리케이션에 대해서 배워보았다. 그렇다면 batch 서버의 구조는 어떻게 되어있는지 궁금했다. 그래서 문득 같은 batch 서버를 공유하고 있지는 않을까 생각했는데 강사님께 여쭤보니 네이버는 mobile용 batch server와 데스크탑용 batch server가 이원화 되어 처리를 하고 있다고 설명해주셨다.
그러고 보니 실제 모바일과 데스크탑 웹 브라우저가 서로 다르게 동작하는 것을 본 적이 있는데 이것이 바로 서버의 이원화로 인한 것이었다.

## AWD와 RWD의 장단점

## 개발은 mobile-first 방식으로 구성하기

개발을 할때에 작은 디바이스부터 대응하고 그 다음으로 큰 디바이스에 대해서 대응하도록 하자.
실행의 측면에서 본다면 가장 화면에 렌더링 될 요소가 많은 페이지는 데스크탑용 레이아웃 페이지이다. 이 부분을 고려하더라도 처음에는 가장 가벼운 모바일 환경과 관련된 처리를하고 제일 마지막에 데스크탑 레이아웃과 관련된 처리를 해주면 된다.

## 관심있는 기업의 웹 페이지를 클론해보자.

클론을 할 때에 단순 클론을 하지 말고, 기존 웹 페이지를 분석하고 개선점을 적용시켜보도록 하자.

## RWD Architecture (Target / Context = Result)

반응형 웹 디자인은 화면의 viewport 크기에 따라서 요소들의 사이즈가 달라져야 되기 때문에 `px/em, %, vw/vh`와 같이 상대단위를 사용해서 요소의 너비(width)와 높이(height)의 사이즈를 정의해야 한다.

- px은 절대 단위가 아닌 상대단위다? (화면의 해상도에 따라 1px은 다르다. 구체적인 자료 찾아서 공부해보기)

## Media Quries

- viewport 크기에 따라 디자인을 다르게 적용
- 개발의 순서는 `Mobile device → Tablet & Desktop → Tablet device → Desktop device`　순으로 고려를 해서 개발을 해야한다.
- Desktop device를 제일 마지막에 작성을 하는 이유는 `Desktop device`의 레이아웃이 가장 복잡하고 화면에 렌더링 되어야 하는 요소가 많기 때문이다.
- 개발을 할때에 Component 단위로 디자인을 해야 기기별로 레이아웃을 제대로 분리해서 개발을 할 수 있다)

## Retina

## srcset과 sizes

## CanIuse 사이트 활용하기(Resources-poly pill)

## Picture 태그

## Device-Pixel-Ratio

## (SEO) Open graph/Twitter card

URL을 복사해서 카톡의 채팅방에 붙여넣기를 하면, 주소 이외에 Thumbnail 이미지가 같이 표시되는 경우가 있는데 `Open graph`와 `Twitter card`에 대한 처리를 head 태그에 작성해주었기 때문이다. 다음에 프로젝트를 할 때에 적용시켜보도록 하자.

## 이디야 카페 메인 페이지

## Native tag를 사용하지 않고 div 태그로 각 세션을 나눌 때에는 `role=""` 속성을 넣어서 처리를 해주도록 하자.

## 반응형 요소를 위한 스타일링 요소

반응형 요소로 만들기 위해서는 최대 너비와 최대 높이에 대해서 아래와 같이 스타일링을 적용시켜줘야 한다.
반복되는 스타일링의 경우에는 별도의 클래스로 정의해서 태그의 클래스 이름으로 작성해서 스타일을 적용시켜주면 된다.

```css
.respons {
  max-width: 100%;
  max-height: auto;
}
```

## title 속성은 장애인이 마우스를 화면의 element에 mouse over를 했을때에 읽어주는 부분이다.

## 비트맵 이미지와 svg 이미지 적용의 차이

## 요소의 전체화면 배치

<ins><b>remind를 위해 수업시간에 필기했던 노트를 첨부한다. </b></ins>

<table>
  <tr>
    <td><img src="/images/post_images/210304_html_css_note1.png" alt="개인공부 노트1"></td>
    <td><img src="/images/post_images/210304_html_css_note2.png" alt="개인공부 노트2"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210304_html_css_note3.png" alt="개인공부 노트3"></td>
    <td><img src="/images/post_images/210304_html_css_note4.png" alt="개인공부 노트4"></td>
  </tr>
</table>
