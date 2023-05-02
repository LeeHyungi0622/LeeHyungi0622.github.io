---
title: 210222 HTML/CSS TIL - HTML, CSS, float
date: 2021-02-22 13:00:42
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

## **HTML/CSS 첫 수업**

오늘 HTML/CSS 첫 수업을 들으면서 너무 좋았다. 내가 정말 원했던 수업내용과 방식이었고, HTML과 CSS를 정말 제대로 배울 수 있겠다는 느낌을 받았다.
프론트엔드 쪽으로 커리어를 쌓아 나갈 것이기 때문에 가장 기본이 되는 HTML/CSS를 제대로 알고 사용하는 개발자가 되기 위해서 정말 노력을 많이 해야겠다.

시작은 수업진행을 위한 전반적인 개발환경 구축 및 수업자료를 local환경에 준비하는 것으로 시작을 하였다.

본 수업 커리큘럼은 기본 HTML5/CSS3 ~ Sass(css 전처리기)사용까지 진행할 계획이라고 하셨다.

Sass에 대해 부가적인 설명은 Sass 중에서 Ruby Sass, Node Sass, Dart Sass가 있는데 Ruby Sass와 Node Sass는 이미 deprecated 되었기 때문에 <ins><b>Sass를 사용하기 위해서는 Dart Sass를 사용해야 한다</b></ins>고 설명해주셨다.

포스팅 내용 정리는 수업 전체 내용을 정리하는 것이 아닌, 내가 기존에 제대로 알고 있다고 생각되는 부분은 생략하고 <ins><b>제대로 알고 있지 않았던 부분과 새롭게 알게 된 내용 그리고 중요하다고 생각되는 내용을 위주로</b></ins> 정리를 해 나갈 계획이다.

  <!-- more -->

## HTML5 이전과 이후

HTML5의 이전에는 HTML Tag의 요소를 block/inline 요소로만 구분을 했었다. 하지만 HTML5 이후에는 `콘텐츠 모델`이라는 개념이 등장을 하게 되었고, 기존에 block/inline 측면의 HTML 구조상에서 유효하지 않았던 태그 구조들이 <ins><b>콘텐츠 모델을 기준으로 가능</b></ins>하게 되었다.
콘텐츠 모델이라는 기준으로 작성한 HTML태그가 유효한 코드 구조인지 알기 위해서는 validation 체크를 하면서 작성을 해야한다.

[https://validator.w3.org/#validate_by_uri+with_options](https://validator.w3.org/#validate_by_uri+with_options)

## W3C Markup Validation Service

내가 작성한 HTML 코드의 태그가 제대로 배치되어서 사용되고 있는지 체크해주는 Vlidation check service이다.
수업시간에 anchor tag와 button tag는 둘 다 interactive한 태그요소이기 때문에 중첩시켜서 작성하면 안된다고 설명해주셨다. 이러한 예처럼 각 태그의 특성을 제대로 파악하고 HTML의 구조를 만들어야 한다.

## HTML 태그에 class이름을 명명하는 방법

![](/images/post_images/210223_bem_img.jpg)

BEM이란? HTML 태그의 class이름을 Block 요소를 기준으로 작성을 하는 방법이다.
HTML 태그의 class이름은 그냥 작성을 하는 것이 아니라 BEM과 같은 규칙으로 작성을 해야한다.

<hr/>

## HTML 태그로 화면구성 구상하기

화면구성을 HTML태그로 구성하기 위해서는 화면 각 각의 Segment를 아래와 같은 sequence로 구성해나간다.

**첫 번째,** 3단으로 구성할지 4단으로 구성할지 생각한다. 일반적으로 Semantic HTML을 살펴보면, 기본적으로 정해놓은 프레임이 있다. 하지만 페이지 구성에는 정해진 구조와 정답이 있는 것이 아니다.
예를들어, Header 태그와 nav 태그를 별도의 segment로 분리시킴으로써 4단으로 구성할 수도, 아니면 Header내부에 nav 태그를 위치시켜서 3단으로 구성할 수도 있다. 위에서 아래로 논리적인 흐름으로 화면 구성을 생각하도록 하자.

**두 번째,** 어떤 구조와 태그로 화면을 구성했는가에는 이유가 있어야 한다. 내가 구성한 페이지 구성에서 사용된 모든 것들에는 직접 설명할 수 있어야 한다.

**세 번째,** 기계가 알아들을 수 있는 각 각의 의미있는 태그로 페이지를 구성해야 한다. 이전에는 단순 div 태그로 페이지를 구성하였다. 하지만 HTML5 이후부터는 기본 화면 구성에 있어 다양한 태그들이 등장했다. 단순히 의미없이 화면의 Partition을 div 태그로 나누지 말고 의미있는 태그를 사용해서 나누는 연습을 하자.

**네 번째,** 화면 전체 컨텐츠를 하나의 container로 묶어주는 작업을 한다. 나중에 화면전체를 한 단위로 컨트롤하기 편하기 때문에 전체를 container class로 묶어주도록 하자.

**다섯 번째,** 페이지의 구성을 직접 손으로 그려가보면서 연습을 하도록 하자.

**여섯 번째,** HTML 태그는 id, class 중에 class를 선언해서 각 element의 스타일을 조작한다.

<hr/>

## SEO (Search Engine Optimization)

검색 엔진 최적화. 이 부분은 HTML 태그의 title 부분에 대해서 설명을 해주시면서 나온 개념이다.

이 title에 대한 이해없이 그냥 아무거나 작성하는 경우가 있는데 이 부분은 웹에서 검색을 했을때 해당 웹 페이지를 노출시킬 수 있는데 크게 기여하는 부분이다.
title 부분 이외에도 검색 엔진 최적화를 고려해서 개발을 할때 중요한 부분도 있으니 개별적으로 찾아보면서 공부를 할 필요가 있다.
프로젝트를 진행할때 SEO를 최대한 녹여내서 진행해보도록 한다.

<hr/>

## 웹 브라우저의 작동과 화면 rendering에 대한 기본 개념 이해

기술적인 부분에 치중해서 개발을 공부하는 경우가 많은데, 전체적인 골격을 우선 공부하는 것이 중요하다.
전체적인 골격이 되는 부분을 우선적으로 공부하고, 세세한 부분에 대해서는 나중에 추가적으로 공부를 하는 것이 중요하다.

<hr/>

## Emmet을 제대로 사용해서 생산성을 높이자.

Emmet 공식 홈페이지에 들어가면 사용하는 방법에 대한 documentation이 잘 되어있다.
아래의 코드는 Emmet을 활용하여 빠르게 HTML 태그들을 작성하는 예시이다.

```html
<!DOCTYPE html>
<html lang="ko-KR">
  <head>
    <meta charset="UTF-8" />
    <title>웹카페-HTML5, CSS3, 웹 표준, 접근성</title>
  </head>

  <body>
    <!-- div.container>header.header+div.visual -->
    <!-- > : child, + : sibling -->
    <div class="container">
      <header class="header">
        <nav class="navigation">내비게이션</nav>
      </header>
      <div class="visual">비주얼</div>
      <main class="main">
        <!-- div.group.group$*3 -->
        <!-- <div class="group group1"></div>
            <div class="group group2"></div>
            <div class="group group3"></div> -->
        <!-- div.group.group${그룹$}*3 -->
        <div class="group group1">그룹1</div>
        <div class="group group2">그룹2</div>
        <div class="group group3">그룹3</div>
      </main>
      <article class="slogan">슬로건</article>
      <footer class="footer">푸터</footer>
    </div>
  </body>
</html>
```

<hr/>

## Classic & Modern 레이아웃 배치

과거에는 페이지의 Layout 요소들을 배치할때 float, position을 사용하여 배치하였다. 하지만 요즘에는 flex나 grid를 사용해서 좀 더 편리하게 화면의 element들을 배치할 수 있다.

이 부분에 대해서는 별도로 포스팅하여 내용정리를 해 볼 것이다.

<hr/>

## **그외 도움되는 이야기** : Clone coding은 어떻게 제대로 진행을 해야하는가?

클론코딩은 단순히 똑같이 만드는 것에 의미를 두는 것은 의미가 없다. 기존의 서비스를 클론코딩 해보았으면, 추가적으로 필요한 요소가 있는지 분석을 하고 추가를 하거나 개선되었으면 좋겠다는 부분도 추가적으로 생각해서 반영을 하는 것이 중요하다.

많은 사람들이 사용을 하고 있는 네이버의 `로그인 화면의 배치에도 개선할 점(로그인 상태 유지의 위치)`이 있다. 이처럼 이미 서비스하고 있는 유명한 웹이라고 하더라도 반드시 개선점은 있으니, 이 부분을 염두해두고 분석을 해보도록 하자.

<hr/>
