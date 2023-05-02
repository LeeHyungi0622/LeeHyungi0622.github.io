---
title: 210325 Web browser와 친해지기 1탄 브라우저의 동작과정
date: 2021-03-25 14:44:00
# hidden: true
# secret: true
tags:
  - Self-Development
  - Basic-term
  - Web-browser
categories:
  - Web-browser
---

![](/images/post_images/web_browser_img.jpeg)

이번 포스팅에서는 브라우저의 동작과정에 대해서 정리를 해보려고 한다.
웹 개발자가 좋은 소프트웨어를 개발하기 위해서는 그 Platform이 되는 웹 브라우저에 대한 정확한 이해가 필요하다. 웹 어플리케이션의 성능향상은 곧 웹 브라우저의 동작과 연관되어 있기 때문이다.

우리가 무심코 사용하는 웹 브라우저는 어떻게 동작을 할까? 한 번 자세하게 분석해보자.

자 그럼 웹 브라우저에 대해서 조금씩 알아가보자.
우리가 특정 웹 페이지에서 다른 페이지로 이동을 할때 아래 첨부한 Processing model의 과정의 순서로 새로운 웹 페이지가 로드된다.

## 웹 브라우저의 Processing model

![](/images/post_images/210325_w3c_navigation_timing_level_2.png)
**출처 : W3C Navigation Timing Level 2 Spec** [https://www.w3.org/TR/2015/WD-navigation-timing-2-20150717/](https://www.w3.org/TR/2015/WD-navigation-timing-2-20150717/)

<!-- more -->

(위에 첨부한 사진에서 괄호로 표기된 이벤트는 optional한 이벤트이다)
<ins><b>특정 웹 페이지에서 다른 페이지로 이동을 할때, 가장 먼저 unload과정이 발생</b></ins>한다. unloadEventStart에서는 `window: beforeunload`의 이벤트가 발생을 하게 된다. 내가 저번에 티스토리 블로그에서 특정 블로그 글을 작성하는 도중에 다르 웹 페이지로 이동하려고 하면, 아래와 같이 기존 페이지를 떠나겠냐는 확인 메시지가 담긴 창이 팝업되었었는데 이런 창이 바로 window: beforeunload 이벤트로써 발생하는 이벤트이다.

![](/images/post_images/210325_page_leave.png)

그 다음으로는 Redirect 과정으로 서버에서 redirect 신호를 보낸 경우 발생을 한다.

위에 첨부한 그래프에서 노란색 부분(백엔드에서 담당-캐시정책, 최적화 방법은 공부하기)은 웹페이지에서 벗어난 뒤에 본격적으로 페이지를 읽어들이기 전이기 때문에 `Network level`에서 발생하는 과정들이다. 따라서 별도의 자바스크립트 이벤트는 없다.

그 다음은 `AppCache`로, 실제 서버에서 데이터를 불러오기 이전에 Browser cache에 저장된 데이터가 있는지 확인하는 작업을 한다.
그 이후의 DNS, TCP, Request, Response 과정은 전부 네트워크 단계로 Name server를 조회해서 서버의 실제 IP를 받아오고, 실제로 서버에 요청을 보내게 된다. 그 후에 웹 페이지에 필요한 파일들(HTML, CSS, JavaScript, 이미지 파일)을 받아오게 된다.

이제 본격적으로 Processing 단계에서는 서버로부터 받아 온 웹 페이지에 필요한 파일들을 parsing하고 rendering하는 과정까지 포함한다.

이 Processing 과정의 시작과 끝은 `domInteractive`와 `domComplete`이벤트로 구성되어있는데 구체적인 내용은 아래 공식문서를 확인하도록 하자.

`[domInteractive]`
[https://developer.cdn.mozilla.net/en-US/docs/Web/API/PerformanceTiming/domInteractive](https://developer.cdn.mozilla.net/en-US/docs/Web/API/PerformanceTiming/domInteractive)

`[domComplete]`
[https://adeveloper.cdn.mozilla.net/en-US/docs/Web/API/PerformanceTiming/domComplete](https://adeveloper.cdn.mozilla.net/en-US/docs/Web/API/PerformanceTiming/domComplete)

아래 첨부한 노트에는 processing과 load단계에서 구체적으로 어떤 일들이 일어나고 processing 단계에서 세부적으로 어떤 이벤트가 발생을 하는지에 대해서 대해서 작성해보았다.

간단하게 정리를 해보면 <ins><b>Processing과정은 웹 페이지에서 필요한 파일들(HTML, CSS, JavaScript, Image)을 Parsing하고 rendering하는 과정을 포함한다.</b></ins>

<b>document: DOMContentLoaded단계에서는 JavaScript로 DOM에 기능을 추가할 수 있는 단계</b>이다.
이전에 HTML 파일을 다 읽고 파싱한 다음에 DOM을 그리고 JavaScript에서 DOM Tree를 그린 다음에 화면에 그리기 전 단계라고 이해하면 된다.

그리고 **브라우저의 주요 구성요소**와 구조, 그리고 실제 웹 페이지를 표현하는 역할을 하는 **Rendering Engine**에 대해서도 간단하게 정리를 해보았다.

렌더링 엔진(Rendering Engine)의 주된 역할은 요청 받은 내용을 브라우저 화면에 표시하는 것이다.

렌더링 엔진의 종류로는 `Firefox 브라우저에서 사용하는 게코(Gecko)엔진(Mozila에서 직접 만듦)`과 `Safari, Chrome 브라우저에서 사용하는 웹킷(Webkit)엔진`이 있다.

![](/images/post_images/210325_processing_and_rendering_engine.png)

## 렌더링 엔진의 렌더링 과정

렌더링 엔진이 요청 받은 내용을 브라우저 화면에 표시하는 과정은 총 5단계로 구분해서 살펴볼 수 있다.

**(1) HTML, CSS, JS 파싱** : 브라우저가 문서를 읽어서 **문법을 분석**하고 **코드를 이해**하는 과정이다.

**(2) DOM, CSSOM으로 변환** : 브라우저에서 실제로 사용할 수 있도록 HTML, CSS 코드를 객체의 형태로 변환한다. HTML을 객체의 형태로 변환한 객체가 DOM이고, CSS를 객체의 형태로 변환한 객체가 CSSOM이다.
우리가 실제로 JavaScript를 이용해서 HTML 태그를 조작하거나 CSS를 속성으로 접근할 수 있는 것은 HTML과 CSS가 DOM과 CSSOM이라는 객체의 형태로 변환되었기 때문에 가능한 것이다.

**(3) 렌더 트리 구축** : 2번 과정에서 만든 DOM, CSSOM 객체를 결합해서 화면에 어떻게 렌더링할 것인지에 대한 내용을 담은 렌더트리를 구축한다.

**(4) 렌더 트리 배치(Layout 단계)** : 3번 과정에서 만든 렌더트리를 <ins><b>실제로 그리지 않고 픽셀로써 각 요소의 크기와 위치를 계산한다.</b></ins> 브라우저 내부에는 포토샵과 같이 여러개의 layout으로 구분되어 구성되어 있다.
특정 웹 페이지의 레이아웃이 어떻게 구성되어 있는지 가시적으로 확인하기 위해서는 page inspector의 More Tools의 Layers 메뉴를 확인하면 된다.
(Layout의 전체 갯수는 30개 이하로 하는 것이 웹 페이지 성능상 좋다고 한다)

**(5) 렌더 트리 그리기**

- 요소를 실제로 픽셀로 변환해서 화면에 그린다. (`Paint/Rasterize`)
  cf) Rasterize : 텍스트와 이미지를 프린트 가능한 형태로 전환시키는 것을 말한다.
- 픽셀로 그려진 여러 레이어를 합성한다. (`compositing 단계`)
  실제로 viewport 범주에서 보여지는 것이 우리가 브라우저에서 보는 화면이다. 실제로 화면에 있는 레이어의 크기는 viewport의 크기보다 크다.

![](/images/post_images/210325_rendering_processing.png)

**[참고사이트]**
(1) NAVER D2

- [https://d2.naver.com/helloworld/59361](https://d2.naver.com/helloworld/59361)

(2) Google

- [https://developers.google.com/web/updates/2018/09/inside-browser-part1?hl=ko](https://developers.google.com/web/updates/2018/09/inside-browser-part1?hl=ko)
- [https://developers.google.com/web/updates/2018/09/inside-browser-part2?hl=ko](https://developers.google.com/web/updates/2018/09/inside-browser-part2?hl=ko)
- [https://developers.google.com/web/updates/2018/09/inside-browser-part3?hl=ko](https://developers.google.com/web/updates/2018/09/inside-browser-part3?hl=ko)
