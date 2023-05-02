---
title: 210224 Sectioning practice
date: 2021-02-24 10:19:00
tags:
  - Self-Development
  - TIL
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/210224_sample_web_page.png" alt="HTML 섹션 나누기 연습"/>
</div>

![]()

## **sample 웹 페이지의 main section을 sectioning**

우선 main content 부분을 크게 세 부분으로 나눠보겠다.
단순 div 태그를 사용해서 각 section을 나누지 않고, 기계가 알아들을 수 있는 의미있는 적절한 semantic tag를 이용해서 나눠보도록 하겠다.

우선,`sectioning element`의 특징에 대해서 알아보자.

sectioning element에는 `<body>,<nav>,<aside>,<article>,<section>`가 있으며, 기본적으로 아래와 같이 내부에 \<header>와\<footer>를 가질 수 있다. 하지만, \<header>와 \<footer> 내부에는 또 다시 nested한 형태로 \<header>와 \<footer>를 가질 수 없다는 것에 주의하자.

  <!-- more -->

<table>
  <tr>
    <td>
      <img src="/images/post_images/210224_section_img1.png" alt="섹션 이미지1"/>
    </td>
    <td>
      <img src="/images/post_images/210224_section_img2.png" alt="섹션 이미지2"/>
    </td>
    <td>
      <img src="/images/post_images/210224_section_img3.png" alt="섹션 이미지3"/>
    </td>
  </tr>
</table>

**출처 :** [https://css-tricks.com/how-to-section-your-html/](https://css-tricks.com/how-to-section-your-html/)

<ins><b>나는 main contents 부분을 세 개의 파트로 나눌때에 section 태그를 사용해서 나눠보려고 한다.</b></ins>
자, 그럼 \<section>태그는 어느때에 사용을 해야될까? 위의 css-tricks 페이지에서는 `if you can't think of a meaningful heading to apply to a <section>, then it probably shouldn't be a <section>`이라고 정의하고 있다.
<ins><b>따라서 \<section>를 사용할때에는 해당하는 부분에 의미있는 heading을 부여할 수 있는지 없는지 생각해서 \<section>태그를 사용하면 된다.</b></ins>

위의 예제의 main contents 부분을 논리적 순서로 왼쪽에서 오르쪽으로 위에서 아래 방향으로 살펴보자.

<ins><b>가장 왼쪽에는</b></ins> 로그인, W3C Validation관련 링크, 웹 관련 용어로 각 각 의미있는 heading을 부여할 수 있다.

<ins><b>가운데에는</b></ins> 검색하는 부분, 공지사항 및 자료실 탭, 새소식 부분으로 각 각 의미있는 heading을 부여해서 section을 구분할 수 있다.

<ins><b>마지막으로 오른쪽은</b></ins> 신규 이벤트, 관련사이트, 인기 사이트 부분으로 section을 구분할 수 있다. 여기서 신규 이벤트와 관련 사이트 부분을 같은 section으로 묶어서 관리되고 있다.

각 컨텐츠의 구성을 분석하여, \<section>태그를 사용해서 웹 페이지의 레이아웃을 구분할 수 있는지 살펴보았다.

결과적으로 main contents 부분은 아래와같이 의미있는 태그로 구분해보았다.

<img src="/images/post_images/210224_web_main_sectioning.png" alt="섹션 이미지3"/>

\<aside> 태그 사용에 대해서 정리를 해보면, \<aside>태그는 `페이지의 다른 컨텐츠들과 약간의 연관성은 가지고 있지만, 해당 컨텐츠들로부터 독립적으로 사용될 수 있는가`를 기준으로 사용한다. \<main> 태그 양옆으로 \<aside> 태그를 사용해서 구분을 하였는데, 로그인, Validation관련 링크, 웹 관련 링크, 신규 이벤트, 관련 사이트, 인기 사이트는 페이지에 있는 컨텐츠들과 웹이라는 공통 분모를 가지고 있지만, 각 각 독립적으로 사용될 수 있기 때문에 \<aside> 태그로 그룹화해서 분류하였다.

그럼 더 나아가 \<article>태그를 사용해서 <section>태그 내부를 좀 더 구체적으로 sectioning해보자.

우선 \<article>태그의 사용에 대해서 이해하자. \<article>태그란 문맥에서 벗어나도 그 자체로 의미가 있을때 (독립적인 컨텐츠) \<article>태그를 사용해서 구분할 수 있다.
대표적인 예로 블로그의 글(댓글포함)과 위젯이 있다.

그럼 현재 실습중인 main contents에서 어떤 부분을 \<article>태그로 감싸줄 수 있는지 살펴보자.
맨 왼쪽의 제일 하단에 위치한 `웹 관련용어 웹 관련용어의 내부 컨텐츠(이미지, 글)`와 `맨 우측의 신규 이벤트의 내부 컨텐츠(이미지, 글)`도 \<article>태그로 감싸서 표현해 줄 수 있을 것 같다.

<img src="/images/post_images/210224_main_contents.png" alt="섹션 이미지4"/>

**Q1.** 그런데 궁금한 점이 \<section>태그는 의미있는 heading을 줄 수 있는 부분에서 사용을 하고, \<article>태그는 독립적인 컨텐츠로써 사용이 될 수 있는 부분에서 사용이 된다고 했는데, 위에서 내가 생각한 것 처럼 \<section> 태그의 내부에 \<article> 태그를 포함시켜서 작성을 해도 되는지 궁금하다.

**→ A1.** 위의 질문에 대해서 강사님께 여쭤보았다. 답변은 \<section> 태그가 \<article>태그의 내부에 위치할 수도 있고, \<article>태그가 \<section>태그의 내부에 위치할 수도 있다고 한다. 다만 \<section> 태그와 \<article> 태그를 개념적으로 접근하지 말고, 그 차이를 heading을 주느냐 마느냐를 기준으로 해서 생각하는 것이 좋다고 한다. heading을 주는 부분은 \<section>으로, 그렇지 않은 부분을 \<article>로 생각해서 작성해주면 된다.

<br/>

**Q2.** 만약에 된다면, 스타일을 적용시킬때 외부에서 감싸고 있는 \<section>태그의 class를 사용해서 스타일링을 해주는지 아니면 내부에서 \<article>태그에 정의한 class를 사용해서 스타일링을 해주는지 궁금하다.

**→ A2.** 위의 질문에 대해서 강사님께 여쭤보았다. 답변은 실무에서는 되도록 작은 범주로 나눠서 스타일을 적용시킨다고 설명해주셨다.
