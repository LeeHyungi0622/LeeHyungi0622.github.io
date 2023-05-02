---
title: 210224 HTML/CSS TIL - ARIA(role), reset-css, 숨김 컨텐츠 관리, 스타일링과 CSS, Post CSS, Animation 효과
date: 2021-02-24 19:50:42
tags:
  - HTML
  - CSS
  - TIL
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/html_css_logo.png" alt="HTML/CSS"/>
</div>

## <ins><b>HTML/CSS 세번째 수업</b></ins>

오늘은 HTML/CSS 세번째 수업날이었다. 오늘도 어김없이 너무 즐겁고 유익한 수업시간이었다.
HTML/CSS 이외에도 강사님께서 여러가지 개발자로서 성장하는데 좋은 팁들을 주셔서 너무나 감사하다고 느꼈던 하루였다.

## <ins><b>ARIA(Accessible Rich Internet Applications)에서 제공하는 태그의 role 속성</b></ins>

ARIA이란 Accessible Rich Internet Applications의 약자로 다양한 role들을 제공한다.
ARIA에서 제공하는 role들은 Landmark roles, Document structure roles, Window Role 등 다양한 role들을 제공한다.
자세한 내용은 아래 링크를 참조하자.

  <!-- more -->

[https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques](https://developer.mozilla.org/en-US/docs/Web/Accessibility/ARIA/ARIA_Techniques)
HTML5의 시맨틱 태그를 살펴보면 \<nav>와 같은 native하게 제공되는 태그가 있는데 이러한 native tag를 사용하는 경우에는 문제없지만, 기존에 \<div>태그를 이용해서 구성을 했다면 Landmark Roles 중에 하나인 navigation 속성값을 \<div> 태그의 role의 속성값으로 넣어주면 된다. (`<div role="navigation" />` - HTML태그의 구성은 아래의 노트를 참고하도록 하자.)

## <ins><b>reset-css / normalized css의 사용</b></ins>

HTML태그들의 기본 스타일을 초기화시켜주는 reset-css를 사용하는 방법에는 두 가지가 있다. 첫번째로 HTML의 \<link>태그로 파일을 import해서 사용하는 방법과 CSS 파일 내부에서 `@import url([reset css file])`의 형태로 선언해서 import 해주는 방법이 있다. 스타일을 초기화시켜주는 기본 코드가 길기 때문에 파일로 분리해서 import 시키지만, 이렇게 처리를 하게 되면 브라우저 상에서 페이지를 띄울때 import된 파일을 우선적으로 불러오고나서 페이지를 렌더링하기 때문에 퍼포먼스에 문제가 생기게 된다.
이러한 퍼포먼스의 문제를 해결하기 위해 최근 개발 트랜드는 `개발(development)와 배포(production)`을 분리해서 프로젝트 파일들을 관리한다. 개발을 할때에는 src 폴더 아래에 있는 파일들을 사용해서 개발을 하지만, 배포시에는 dist라는 폴더 내부에 작성한 코드 파일들을 minify된 형태의 코드 파일형태로 merge시켜서 배포한다.

## <ins><b>숨김 컨텐츠 관리</b></ins>

nav태그의 경그럼 어떻게 숨김 컨텐츠를 처리해줘야 하나? 부분인지 작성을 해줘야 한다. 이러한 숨김 컨텐츠의 경우에는 `Screen reader가 읽어주는 부분`이 될 수도 있으며, `Headings map을 통해서 웹 페이지의 구조를 파악`할 수 있게 해준다.

- ### <ins><b>그럼 어떻게 숨김 컨텐츠를 처리해줘야 하나?</b></ins>

  보통 개발자들은 이 숨김 컨텐츠를 `display: none`으로 처리를 하게 되는데, 이렇게 해주면 screen reader가 읽어서 처리해줄 수 없다. 그 이유는 screen reader는 페이지 위에 사이즈로써 존재하는 태그를 읽어주기 때문이다. 그렇다면 어떤 식으로 처리를 해줘야 할까?
  우선, 숨김 컨텐츠를 아래와 같이 position: absolute, top: -9999px;로 처리해서 작성해주도록 하자.

  ```css
  .a11y-hidden {
    position: absolute;
    top: -9999px;
  }
  ```

  위와같이 처리를 해주게 되면 문제점은 screen reader가 현재 위치해 있는 페이지를 다 읽어주고 나서 페이지의 맨 위 위치로 튀어 올라가는 상황이 생긴다. 스크린 리더는 보통 시각 장애인이 많이 사용한다고 생각을 하지만 실제로 저시력자들도 많이 사용하기 때문에 이런식으로 페이지가 튀어 올라가는 현상은 좋지 않다. 그럼 이러한 페이지 튀어오름 현상을 보완한 방법에는 어떤 방법이 있을까? 아래 코드를 살펴보자.

  ```css
  .a11y-hidden {
    background-color: red;
    position: absolute;
    width: 1px;
    height: 1px;
    margin: -1px;
    overflow: hidden;
    clip: rect(0 0 0 0);
    clip-path: polygon(0 0, 0 0, 0 0);
  }
  ```

  margin은 padding과 달리 음수값을 지정해 줄 수 있다. 위와 같이 너비와 높이를 각 각 1px씩 지정을 해주고, margin을 -1px로 만들어주면 화면에 표시되는 텍스트는 없읕 것이다. 그렇지만 1px 너비와 높이를 설정해주었기 때문에, 배경색을 처리해보면 미세하게 페이지상에 표시되어 있음을 확인할 수 있다. 이러한 미세한 화면표시는 `clip-path: polygon(x, y, z)`이라는 속성을 활용하여 제거를 해 줄 수 있다. 이렇게 처리를 할거라면 왜 굳이 너비와 높이를 1px씩 줬는가 의문이 들 수도 있는데, 이는 스크린 리더가 페이지에서 특정 요소를 읽게 하기 위해서는 해당 요소는 화면에 없는 요소가 아닌 `최소한의 사이즈로 존재`해야 되기 때문이다.

  clip-path의 polygon 설정은 x,y 좌표 세 점을 만들어서 해당 영역에 있는 컨텐츠를 표시해주는 역할을 한다. 자세한 예제는 아래 링크를 확인해보도록 하자.
  만약에 IE 구형버전의 호환성을 고려해야 한다면, `clip: rect(0 0 0 0)`으로 처리를 해주면 된다.

  **참고 :** [https://css-tricks.com/almanac/properties/c/clip-path/](https://css-tricks.com/almanac/properties/c/clip-path/)

## <ins><b>가고싶은 기업의 홈페이지를 분석</b></ins>

만약에 가고 싶은 기업이 있다면, 해당 기업이 서비스하고 있는 웹 페이지를 분석해서 `버그 리포트`를 만들어보는 것도 좋은 방법이다. 이 버그 리포트를 만들어서 나중에 회사 지원시에 첨부를 한다면, 해당 기업에 지원하는 그 누구보다 경쟁력에서 우위를 갖게 될 것이다.

버그리포트를 작성 자세한 방법은 아래의 링크를 확인해보자.
[https://www.softwaretestinghelp.com/how-to-write-good-bug-report/](https://www.softwaretestinghelp.com/how-to-write-good-bug-report/)

## <ins><b>스타일을 적용시킬때에는 유지보수를 생각해서 class를 이용한 스타일링을 해야한다. </b></ins>

navigation menu를 리스트의 항목으로 작성을 한 경우에는 리스트의 기본 스타일을 제거해줘야 한다. 아래는 직접적으로 태그를 지정해서 스타일을 적용시키는 방법이다.

```css
ul,
ol {
  list-style: none;
  margin: 0;
  padding-left: 0;
}
```

만약에 개발중인 웹 어플리케이션이 규모가 작다면 상관없지만, 나중에 유지보수를 고려해야되는 어느정도 규모가 있는 개발이라면 직접 태그를 걸어서 스타일링을 주는 것보다는 태그의 class 속성을 활용해서 스타일링하는 방법이 좋다.

```css
.reset-list {
  list-style: none;
  margin: 0;
  padding-left: 0;
}

[class] {
  box-sizing: border-box;
}
```

## post css - auto prefix

css를 적용할때 몇 몇 태그들은 호환성의 문제로 브라우저에 따라 각기 다른 prefix가 붙게 된다.
이러한 prefix를 자동으로 넣어주는 `autoprefixer`와 같은 post css도 있으니 나중에 프로젝트할때에 참고를 하도록 하자.
[https://autoprefixer.github.io](https://autoprefixer.github.io)

## <ins><b>오늘까지 배운 header 메뉴 구성을 처음부터 끝까지 실습해보자.</b></ins>

수업시간 예제 웹 페이지는 강사님이 다양한 예제들을 연습할 수 있도록 구성하셨다고 한다. 이 예제를 모던한 flex로도 레이아웃을 구성해보고, float로도 구성을 해보면서 연습을 해야겠다. 이 예제 웹 페이지만 연습해도 충분한 연습이 될 것 같다.

## icon font

fontello.com 를 사용하면 다양한 아이콘 폰트를 웹 페이지에서 사용할 수 있다. 나중에 개인 프로젝트나 팀 프로젝트를 할때에 참고해서 사용하도록 하자.
[https://fontello.com](https://fontello.com)

## white-space: nowrap = 줄바꿈 금지

position을 absolute로 설정을 하게되면 position의 속성이 relative로 설정된 부모요소의 너비를 기준으로 자식 컨텐츠의 너비가 결정되어, 내부에 출력되는 컨텐츠에 자동 줄 바꿈이 일어날 수 있다. 이러한 경우에는 자식요소에 `white-space: nowrap`을 넣어줘서 자동 줄 바꿈을 해제시킬 수 있다.

## Animation 효과주기

애니메이션 효과를 주기 전에 아래와 같이 `애니메이션 효과에 대한 시나리오를 작성`해야한다.

```css
/* # 애니메이션 효과 시나리오
1. 이름 : textAni
2. 액션 : 이동은 (왼쪽 상단으로 00px -> 오른쪽 하단으로 00px)구체적으로 좌표 위치도 명시해줘야 한다.
         글자의 크기는 점진적으로 커지는 형태로 구현한다.
         투명도는 점진적으로 진해지는 형태로 구현을 한다.
3. 이동 : 컨텐츠의 이동은 아래의 4가지 속성을 이용해서 구현을 할 수 있다. 다만 퍼포먼스를 고려한다면(reflow/repaint로 인한 퍼포먼스 저하) 위치 속성을 바꿔주는 속성 대신에 transform과 같은 애니메이션 처리를 활용하는 것이 좋다. 애니메이션을 선언해서 사용을 할때에는 아래와 같이 @keyframe으로 애니메이션 효과를 정의해주고, 적용시킬 대상 태그의 속성에 작성한 애니메이션 효과를 적용시켜줘야 한다. */

/* 예시) */
keyframes textAni {
  0% {
    font-size: 12px;
  }
  100% {
    font-size: 24px;
  }
}

.visual-text {
  /* 적용할 애니메이션 */
  animation-name: textAni;
  /* 애니메이션 시간 */
  animation-duration: 2000ms;
  /* 최종 변화된 애니메이션 효과 상태에서 고정 */
  animation-fill-mode: forwards;
}
```

애니메이션 효과를 어플리케이션의 퍼포먼스 측면에서 분석하여 네이버의 한 주니어 개발자 분이 발표를 하신 자료라고 강사님이 소개해주셨다. 어플리케이션을 구현하는 것에만 그치지 않고 다양한 방법으로 같은 기능이 구현되었을때 어떤한 퍼포먼스의 차이가 있는지 분석한 PPT자료는 정말 인상깊었다. 나도 나중에 이런 발표자료를 만들어서 많은 사람들 앞에서 발표를 해보고 싶다는 생각에 나중을 기약하며 아래에 링크를 첨부해놓는다.

[https://www.slideshare.net/wsconf/css-animation-wsconfseoul2017-vol2?qid=2fcf41db-7f5e-4ae4-b47d-4c96f3266901&v=&b=&from_search=15](https://www.slideshare.net/wsconf/css-animation-wsconfseoul2017-vol2?qid=2fcf41db-7f5e-4ae4-b47d-4c96f3266901&v=&b=&from_search=15)

<hr/>

<ins><b>remind를 위해 수업시간에 필기했던 노트를 첨부한다. </b></ins>

<table>
  <tr>
    <td><img src="/images/post_images/210224_html_css_note.png" alt="개인공부 노트1"></td>
    <td><img src="/images/post_images/210224_html_css_note2.png" alt="개인공부 노트2"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210224_html_css_note3.png" alt="개인공부 노트3"></td>
    <td></td>
  </tr>
  
</table>
