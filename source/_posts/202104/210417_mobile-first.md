---
title: 210417 Mobile first 방식의 이점과 간단한 HTML 마크업과 CSS 스타일링 예시
date: 2021-04-17 16:59:00
tags:
  - HTML-CSS
  - Mobile-first-method
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/210417_mobile-first-design-and-responsive-design.png" alt="Mobile first & Responsive design">
</div>

## Mobile-First 방식 ?

이번 포스팅에서는 `모바일 우선(Mobile-First) 웹 디자인`에 대해서 정리를 하려고 한다.
요즘 간단한 화면의 구성요소들을 모듈형태로 나눠서 프로젝트식으로 만들어보고 있는데, 모두 Mobile-first 방식으로 웹 디자인을 하고 있다.
이전에 반응형 웹 페이지를 만들때는 아무 생각없이 처음에 Desktop 기준으로 웹 페이지를 디자인하는 경우가 많았는데, 모바일 우선 방식으로 웹 페이지를 디자인 하는 것이 더 효율적이라는 것을 알게 된 후부터는 시작할때 모바일 기준으로 HTML 마크업을 하고 CSS 스타일링을 하고 있다.

이번 포스팅에서는 왜 모바일 우선으로 웹 페이지를 디자인해야 되는지에 대해서 공부한 내용을 기반으로 내용을 작성해보려고 한다.

우선 Desktop과 Mobile의 사용률을 보았을때 2014년도를 기점으로 Mobile 사용률이 Desktop 사용률을 넘어섰다. 단순히 이러한 사용률만의 문제가 아니라 모바일 우선 방식으로 웹 페이지를 디자인했을때 생기는 이점들에 대해서 정리를 해보겠다.

<ins>**우선 첫 번째, 구글은 Mobile-First Index를 사용**</ins>한다. 구글은 우리가 모두 다 아는 대규모 검색 엔진으로 구글이 모바일에 올인하고 있다. 구글의 검색 알고리즘이 모바일 버전 사이트의 콘텐츠를 사용하여 해당 사이트의 검색 노출 페이지 순위를 매긴다는 의미이다. 만약 본인이 개발한 웹 페이지가 구글에서 많이 노출되길 원한다면 웹 페이지를 모바일 우선 방식으로 디자인하는 것이 좋다.

<ins>**두 번째, 좋은 사용자 경험(UX)을 줄 수 있다.**</ins> 일반적으로 모바일 사용자는 빠른 웹 사이트 로드를 기대한다. 만약 웹 페이지가 모바일 우선 방식으로 디자인이 되어있다면, 모바일 사용자가 초기에 웹 페이지를 로드했을때 빠르게 페이지가 로드되어 사용자에게 좋은 경험을 줄 수 있다.

<ins>**세 번째, 빠른 웹 사이트를 만들 수 있다.**</ins> 두 번째 이점과 거의 같은 맥락의 이야기지만, 모바일 화면에서 보여지는 페이지의 요소는 상대적으로 데스크탑 웹 페이지에 비해 화면에 보여지는 요소가 적다. 만약 반응형 디자인으로 페이지가 디자인되어 있고, 모바일에서 해당 웹 페이지를 로드한다면, 초기에 로드되어야 하는 화면의 요소가 데스크탑 기준으로 우선 로드되고, 미디어 태그로 조건 처리된 모바일 기준 스타일이 다음으로 적용되어 상대적으로 느린 웹 페이지 로드를 보여 줄 수 있다.

  <!-- more -->

## <ins><b>Mobile-First 방식의 HTML 마크업 및 CSS 스타일링 예시</b></ins>

우선 웹 페이지를 디자인할때 mobile화면에서 어떻게 페이지가 출력이 되는지 구상한다.
간단하게 네비게이션 바를 예로들어 설명을 해보겠다. 웹 페이지를 데스크탑과 모바일에서 보았을때 가장 눈에 띄게 다른 부분은 바로 이 네비게이션 바 부분이다.

보통 데스크탑 기준으로 웹 페이지의 네비게이션 바를 본다면 모든 메뉴들이 다 보여지고, 모바일 기준으로 화면의 viewport 사이즈를 줄였을 때 모든 메뉴들이 숨겨지고 toggle 버튼이 보여지게 된다.

간단하게 모바일 기준으로 네비게이션 바의 HTML 마크업을 해보면 아래와 같다.
네비게이션 바의 구성은 로고 이미지와 토글버튼 그리고 메인 메뉴로 구성이 되어있다.

```html
<!-- header -->
<header id="home">
  <!-- navbar -->
  <nav id="nav">
    <div class="nav-center">
      <!-- nav header -->
      <div class="nav-header">
        <img src="./logo.svg" alt="logo" class="logo" />
        <button class="nav-toggle">
          <i class="fas fa-bars"></i>
        </button>
      </div>
      <!-- links -->
      <div class="links-container">
        <ul class="links">
          <li>
            <a href="#home" class="scroll-link">home</a>
          </li>
          <li>
            <a href="#menu1" class="scroll-link">Menu1</a>
          </li>
          <li>
            <a href="#menu2" class="scroll-link">Menu2</a>
          </li>
        </ul>
      </div>
    </div>
  </nav>
</header>
```

이제 위에서 마크업한 HTML 태그를 모바일 우선 방식으로 CSS 스타일링을 해보겠다. 세세한 스타일링 부분은 제외하고 화면 배치와 관련있는 스타일링을 위주로 작성해보았다.

```css
/* nav의 배경색과 padding 설정 */
nav {
  background: var(--clr-white);
  padding: 1rem 1.5rem;
}
/* 로고와 토글버튼을 nav의 좌우 양쪽 끝에 배치 */
.nav-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

/* 토글버튼 스타일링 */
.nav-toggle {
  font-size: 1.5rem;
  color: var(--clr-grey-1);
  background: transparent;
  border-color: transparent;
  transition: var(--transition);
  cursor: pointer;
}

/* 
  모바일 페이지에서는 메인 메뉴 부분의 부모 요소의 높이를 0으로 설정하고 
  부모 요소 내부의 자식요소를 숨기기 위해서 overflow: hidden 속성을 준다.
*/
.links-container {
  height: 0;
  overflow: hidden;
  transition: var(--transition);
}

/* 토글버튼이 클릭되었을때 메인 메뉴 부분의 부모 요소에 높이를 주기 위한 css class */
/* 이 부분은 동적으로 height 속성을 javascript에서 인라인 스타일링한다.*/
.show-links {
  height: 200px;
}

/* 메인 메뉴의 메뉴 <a> 태그에 대한 스타일링 (모바일 페이지 기준) */
.links a {
  background: var(--clr-white);
  color: var(--clr-grey-5);
  font-size: 1.1rem;
  text-transform: capitalize;
  letter-spacing: var(--spacing);
  display: block;
  transition: var(--transition);
  font-weight: bold;
  padding: 0.75rem 0;
}

/* 데스크탑 기준 */
@media screen and (min-width: 800px) {
  nav {
    background: transparent;
  }
  /* 토글 버튼 화면에서 표시 안함 */
  .nav-toggle {
    display: none;
  }
  /* 
  자바스크립트에서 인라인 방식으로 스타일링하게 되면 우선순위가 
  CSS Stylesheet에 작성한 스타일링보다 높게 되어 토글 버튼 클릭(인라인 스타일링 적용)후에 
  viewport의 크기를 조정하여도 인라인 스타일링한 스타일 속성이 그대로 적용되어있어 화면 배치가 깨진다.
  따라서 아래와 같이 !important로 이전의 스타일링을 무시하고 최우선으로 스타일을 적용할 수 있도록 하였다. 
   */
  .links-container {
    height: auto !important;
  }
  /* 메인메뉴의 출력 */
  .links {
    display: flex;
  }
}
```
