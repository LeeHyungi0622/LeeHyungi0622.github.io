---
title: 210302 HTML/CSS TIL - 레이아웃 구성 순서, 태그 배치도를 그릴때는 태그의 특성이 보이게 그리기, 요소배치는 최대한 논리적 순서를 유지한 상태에서 하기, figure 태그, anchor 태그의 대체 텍스트, :root{}를 활용한 스타일 속성의 변수선언(재사용성), CSS 전처리기의 종류 및 장/단점, 실무에서의 스타일링 방법, positioning에 따른 element의 성격 변화, rgba()와 hsla()의 사용법, 1px보다 얇은 디자인, 스프라이트 기법 (CSS Sprites), aria-label과 IR 기법
date: 2021-03-02 19:40:42
tags:
  - HTML
  - CSS
  - TIL
  - CSS-Sprites
  - Sass
  - IR
  - Aria-label
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/html_css_logo.png" alt="HTML/CSS"/>
</div>

## <ins><b>HTML/CSS 여섯 번째 수업</b></ins>

오늘은 HTML/CSS 여섯 번째 수업날이었다. 오늘도 많은 유익한 내용들을 배울 수 있던 날이었다. 이 모든 것들을 내 것으로 만들기 위해 끊임없이 노력하자.

## <ins><b>레이아웃 구성할 때는 콘텐츠의 논리적 순서 흐름을 파악하고 나서 구체적인 태그의 배치(시맨틱 마크업)를 고려한다.</b></ins>

이 내용에 대해서는 HTML/CSS 수업 초반에 강사님께서 강조해주셨던 내용이다. 지금은 개인적으로 레이아웃 설계 연습을 할 때도 항상 되뇌이면서 연습을 하고 있다. 오늘 이 부분에 대해서 강사께서 어쩌면 실무에서 일을 하면서 이러한 콘텐츠의 논리적 순서를 고려하지 않고 레이아웃을 단순 디자인적 관점에서 보게 될 수도 있고, 상황에 따라서는 타협을 해야 될 때가 온다고 하셨다. 이럴때에는 최소한의 접근성을 고려해야 되는 요소만은 지켜서 레이아웃을 설계하면 된다고 하셨다.

`콘텐츠의 논리적 순서 → 시맨틱 마크업 → Class 네이밍`

  <!-- more -->

## <ins><b>구체적인 태그를 배치할 때에는 **Block element는 길게, inline element는 짧게 그려서** 각 태그의 속성을 가시적으로 구분할 수 있도록 하자.</b></ins>

태그의 배치도를 그릴 때 앞에서 명시한대로 Block element와 inline element를 구분해서 그려주게 되면, 가시적으로 각 element의 특성을 구분해서 화면 배치를 할때 처리해야 될 스타일링 요소도 같이 고려를 할 수 있다.

## <ins><b>화면의 요소를 배치할때 논리적 순서를 유지할 수 있는 부분은 최대한 유지하고 배치하도록 하자.</b></ins>

첫 번째 노트를 살펴보자. article 부분을 보면 Thumbnail Image와 title, date, content를 배치해야 되는 레이아웃이다. 처음 이 구조를 보았을 때에 전체적인 article의 position 속성을 relative로 처리하고 내부의 각 요소들의 position을 absolute로 처리해서 요소 배치를 하려고 하였다.
하지만, Thumbnail Image를 제외한 나머지 부분을 보면 기존에 작성한 태그들의 논리적 흐름을 유지한다.
따라서 article 요소의 position 속성은 relative로 주고, article 내의 요소들을 배치할때에는 Thumbnail Image만 position을 absolute로 하고, top, left 속성을 주어 요소 배치를 하면 쉽게 배치를 할 수 있다.

그리고 추가적으로 늘어날 가능성이 있는 요소의 경우, position 속성을 사용해서 화면에 배치하지 않도록 하는 것이 좋다. 예를 들어 콘텐츠의 내용은 얼마든지 늘어날 가능성이 있는 부분이기 때문에 이 부분은 되도록이면 position 속성을 지정해서 화면에 배치하지 않는 것이 좋다.

## <ins><b>figure 태그를 사용해서 이미지와 이미지 캡션을 배치하자.</b></ins>

이미지와 이미지 캡션을 배치할 때에는 별도의 div태그를 사용해서 wrapping해주지 말고(이미지의 크기를 손쉽게 조정하기 위한 목적으로 wrapping해준다) figure 태그를 사용해서 이미지와 이미지 캡션을 배치해주도록 하자.
이 figure 태그를 사용하게 되면 리스트 형태로 출력되는 기사 리스트 콘텐츠의 각 각의 아이템을 손쉽게 구현할 수 있다.
figcaption을 사용해서 이미지 캡션을 넣어주게 되면, img태그의 대체 텍스트(alt) 속성값을 생략할 수 있다. 하지만, 대체 텍스트를 생략하게 되면, SEO 검색률이 떨어진다.

## <ins><b>anchor 태그의 label은 아래 두 가지 경우로 대체할 수 있다.</b></ins>

> 1.  anchor 태그의 title 속성
> 2.  별도의 h2 태그에 label을 작성해주고, id 속성을 준 뒤에 anchor 태그의 속성에 aria-labelledby=[h2의 id] 를 넣어서 처리해준다.

Title 속성과 h2로 별도의 label을 mapping해주게 되면 필요이상의 선언으로 보여지지만, 부족한 정보보다는 필요이상의 정보가 낫다고 생각하는 개발자도 있고, 그보다 좀 부족해도 괜찮다는 생각을 가진 개발자도 있다.

- ### <ins><b>aria-labelledby 속성 활용하기</b></ins>
  만약 첨부된 파일 리스트가 있다고 가정했을때 첨부 파일 링크가 이미지로 처리되어 있고, 구체적으로 어떤 타입의 파일이 첩부되어 있는지 모를 경우, 첨부된 파일의 타이틀에 할당된 id 속성(동적할당-파일타입)을 첨부된 파일 다운로드 링크 태그(anchor)의 aria-labelledby속성으로 넣어주면 해당 태그가 어떤 종류의 파일인지 알 수 있다. (두 번째 노트 참고)

## <ins><b>CSS의 :root{ }로 스타일 속성 값을 변수로 선언해서 재 사용성을 높일 수 있다.</b></ins>

  <br/>

- ### <ins><b>스타일 속성 값을 변수로 선언하기</b></ins>

  ```css
  :root {
    --primary-color: #ce4827;
    --secondary-color: #eaac2e;
    --silver: #aaa;
  }
  ```

  <br/>

- ### <ins><b>선언한 스타일 속성 변수를 호출해서 사용하기</b></ins>
  ```css
  .news-heading {
    margin: 0;
    font-size: 1.5rem;
    color: var(--primary-color);
  }
  ```
  <br/>
- ### <ins><b>CaniUse 사이트에서 각 브라우저 별로 버전 호환성 체크해보기 (var())</b></ins>

  [https://caniuse.com/?search=var()](<https://caniuse.com/?search=var()>)

## <ins><b>Sass와 Less 그 외의 CSS 전처리기</b></ins>

대표적인 CSS 전처리기에는 Sass와 Less가 있다. Sass의 경우에는 Less에 비해서 초기 셋팅해야 되는 부분이 많지만 가장 대중적으로 사용되는 라이브러리이다. 간단하게 전처리기들을 비교해서 정리해보자.

  <table>
    <tr align="center">
      <td><ins><b>Sass</b></ins></td>
      <td><ins><b>Less</b></ins></td>
      <td><ins><b>Stylus</b></ins></td>
    </tr>
    <tr align="center">
      <td>
          - CSS 전처리 라이브러리로 가장 오래되었으며, 대중적으로 사용되고 있다.
          - 막강한 내장기능을 가지고 있으며 Compass와 병용해서 사용하면 리소스 경로를 참조하는 것이 가능해서 특정 폴더 내의 이미지의 크기와 위치정보를 참조할 수도 있다.
      </td>
      <td>
        - 브라우저에 내장된 JS Interpreter로만 Compile이 가능하기 때문에 dependency로부터 자유롭게 사용될 수 있따.
        - Sass 다음으로 활발하게 사용되고 있으며, 관련 library나 mixins 구현과 관련된 내용들을 쉽게 찾아 볼 수 있다.
      </td>
      <td>
        - 상대적으로 프로그래밍 언어의 특징을 많이 포함하고 있는 라이브러리이다.
        - 첫 번째 이유 덕분에 CSS 프로퍼티 내에서 연산자나 함수, 루프 등을 비교적 자유롭게 사용할 수 있다.
        - 하지만 문법이 혼재되어 있어서 처음 전처리기를 사용하는 사람에게는 상대적으로 어렵게 느껴질 수 있다.
      </td>
    </tr>
  </table>

## <ins><b>실무에서의 태그 스타일링 방법</b></ins>

직접 특정 태그에 스타일링을 해줘도 되지만, 반복적으로 적용되는 스타일이라면, 별도의 스타일 관련 클래스로 스타일을 지정해놓고 재사용하는 편이 좋다.

예를들어, 박스에 그림자 효과를 주는 경우나 여러 컴포넌트에서 똑같은 그라데이션 효과를 주는 경우, 일회성이 아닌 다회성으로 스타일링을 해야 될 때에는 해당 스타일을 정의해서 재사용성을 높이도록 한다.

## <ins><b>positioning에 따른 element의 성격 변화</b></ins>

일반적으로 inline element와 block element를 display 속성으로 그 본래 가지고 있는 성격을 바꿔 줄 수 있다는 것은 알고 있지만, <ins><b>position 에 따라서도 element가 본래 가지고 있는 성격을 바꿔줄 수도 있다.</b></ins>
우선 inline-element의 position 속성값을 absolute로 주게 되면, 해당 요소은 기존의 콘텐츠의 논리적 흐름에서 벗어나게 되면서 block element과 같은 성격을 갖게 된다. 기존의 inline element의 경우에는 width 속성과 상하 padding 속성을 갖지 못하지만, 이렇게 absolute로 positioning된 inline element의 경우에는 block element와 같이 기존에 갖지 못했던 너비와 상하 패딩 속성을 지정해 줄 수 있기 된다. (display: block 지정과 동일 효과)

두 번째 예시로 inline element에 position으로 relative 속성값을 주는 경우이다. 이러한 경우에는 기존의 inline element가 inline-block의 성격을 갖게 된다. (inline element와 inline-block의 차이에 대해서는 별도의 category로 분류해서 정리해두자)

## <ins><b>DOM + CSSOM Render Tree</b></ins>

페이지에 표시된 요소들이 rendering되기 위해서는 DOM요소와 CSSOM 요소가 준비되어야 한다. (구체적인 내용에 대해서는 공부해서 별도의 포스팅으로 정리를 해두도록 하자.)

## <ins><b>기존에 Presentation 특성의 태그가 HTML5부터 구체적인 의미를 갖게 되었다.</b></ins>

\<i> 태그의 경우 기존에는 presentation 태그의 특징으로써 기울림체를 표현했었지만, HTML5에서부터 선박에서의 사용이나 여러 구체적인 의미를 가진 태그로써 추가 정의되었다. 이외에도 \<b>, \<em> 등 presentation 성격의 태그들이 구체적인 의미를 담은 시맨틱 태그로써 재 정의되었다.

## <ins><b>rgba()와 hsla()의 사용법</b></ins>

```css
/* rgba()를 사용해서 빨간 색상 표현 */
/* rgba([Red],[Green],[Blue],[Alpha]) */
/* 빨간색에 0.3 alpha 값 적용 */
.title {
  background-color: rgba(255, 0, 0, 0.3);
}
/* hsla()를 사용해서 밝은 초록색 표현 */
/* hsl(hue, saturation, lightness) */
/* hue는 color wheel에서 선택한 색상의 degree값으로, 범위는 0 부터 360까지 이다.  0(또는 360)은 빨간색이며, 120은 초록색, 240은 파란색이다.*/
/* saturation은 채도 값으로 % 단위로 표현한다. */
/* lightness는 명도 값으로 채도 값과 같이 % 단위로 표현한다. */
.title {
  background-color: hsl(120, 100%, 75%);
}
```

## <ins><b>1px보다 얇은 디자인 (이듬의 브런치 글귀)</b></ins>

시간날때 이듬의 브런치에서 `1px보다 얇은 디자인`에 대한 글을 읽어보도록 하자.
실무에서 디자이너가 디자인 시안상에 1px보다 작은 0.5px로 디자인을 해서 넘겨주게 되는 경우가 있는데, 이러한 경우 원래 0.5px이라는 개념은 없지만 어떻게 트릭을 써서 1보다 작은 소수점 단위의 px로 만들 수 있는지에 대한 글이다.

→ [https://brunch.co.kr/@euid/6](https://brunch.co.kr/@euid/6)

## <ins><b>시맨틱적인 이름이 아닌 디자인적인 이름으로 클래스 이름을 작성해서 공통 CSS 스타일을 모듈화 하기</b></ins>

아래와 같이 반복적으로 사용할 스타일을 별도의 디자인적인 이름으로 클래스 이름을 지정해서 작성해준 다음에 스타일을 적용할 태그의 클래스로 넣어서 사용하도록 한다.

`style.css`

```css
.point-color {
  color: #ce4827;
}
```

`index.html`

```css
<span class="point-color">사이트</span>
```

## <ins><b>스프라이트 기법 (CSS Sprites)</b></ins>

화면에 보여지는 아이콘 이미지들을 개별적으로 서버쪽에 요청을 해서 화면에 뿌려주게 되면 서버쪽으로 수없이 Request를 보내줘야 한다. 이렇게 되면 페이지를 렌더링할때 퍼포먼스상 좋지 않다.
이러한 퍼포먼스 측면의 문제를 해결하기 위해서 사용되는 기법이 `스프라이트 기법`이다. 화면에서 보여지는 이미지들을 아래와 같이 통 이미지로 한 번에 서버로부터 받아 온 다음에 background-position을 사용해서 x, y축 기준으로 일부분 잘라내서 필요한 요소의 배경으로 처리해서 화면에 표시하는 기법이다.
원래 게임 분야에서 사용되던 기법이었는데 웹에서 퍼포먼스상의 문제를 해결하기 위해서 사용되었다고 한다.

## <ins><b>JS에서 별도로 컨트롤할 element의 집합은 별도의 wrapper container로 묶어서 처리한다.</b></ins>

별도의 wrapper container로 묶어서 처리하게 되면 나중에 해당 container의 id로 내부의 element에 접근하기가 용이해진다.

## <ins><b>img 태그에서의 대체 텍스트를 바라보는 시각 차이</b></ins>

강사님께서 아시는 디자이너 지인 분의 아이패드 당첨 에피소드로 재미있게 이 대체 텍스트에 대해서 이야기를 해주셨다. 일반인의 시각에서는 배너광고를 통해 당첨 이벤트라는 것을 알 수 있지만, 시각 장애인의 입장에서는 배너광고의 이미지를 대체 텍스트를 통해 해당 배너 광고가 어떤 내용인지 알 수 있다.
이 대체 텍스트를 바라보는 시각이 두 가지로 나뉘게 되는데, 첫 번째로 "대체 텍스트를 화면에 표시된 이미지 내의 텍스트 정보만으로 동일하게 담아서 처리를 하고, 부가 정보의 경우에는 title 속성에 담아준다."라는 시각과 두 번째로 "대체 텍스트를 부가 정보의 개념으로 어떠한 목적으로 해당 이미지가 삽입되었는지에 대한 부가 정보를 이 대체 텍스트에 넣어 준다." 라는 시각이 존재한다. 이는 개발자의 개별적인 시각에 따라 다르기 때문에 어떤 것이 맞고 틀린 것이 아니다.

## <ins><b>aria-label과 IR 기법으로 화면에 표시한 텍스트 숨김처리 하기</b></ins>

IR 기법으로 화면에 표시한 대체 텍스트를 숨김처리하는 연습예제는 수업 실습 프로젝트의 `ir.html` 파일을 참고하고, 수업 노트는 6번째 노트 필기를 참고하도록 하자. 예전에는 aria-label이라는 속성으로 대체 텍스트를 주지 않고 별도의 태그에 대체 텍스트를 작성하고 이를 IR기법을 사용해서 가시적으로 보이지 않도록 처리를 하였다.

IR기법의 종류
(1) 대체 텍스트로 사용하는 태그를 indentation으로 일부러 overflow를 발생시킨 다음 overflow: hidden 처리를 한다.
(2) 대체 텍스트로 사용되는 태그를 position: absolute로 이미지와 겹치도록 위치시킨 다음에 z-index값으로 태그의 뒷쪽으로 보이지 않도록 처리한다.
(3) 가상 클래스를 사용해서 대체 텍스트로 처리할 태그를 작성해준다.

<hr/>

<ins><b>remind를 위해 수업시간에 필기했던 노트를 첨부한다. </b></ins>

<table>
  <tr>
    <td><img src="/images/post_images/210302_html_css_note1.png" alt="개인공부 노트1"></td>
    <td><img src="/images/post_images/210302_html_css_note2.png" alt="개인공부 노트2"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210302_html_css_note3.png" alt="개인공부 노트3"></td>
    <td><img src="/images/post_images/210302_html_css_note4.png" alt="개인공부 노트4"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210302_html_css_note5.png" alt="개인공부 노트5"></td>
    <td><img src="/images/post_images/210302_html_css_note6.png" alt="개인공부 노트6"></td>
  </tr>
  <tr>
    <td><img src="/images/post_images/210302_html_css_note7.png" alt="개인공부 노트7"></td>
    <td></td>
  </tr>
  
</table>
