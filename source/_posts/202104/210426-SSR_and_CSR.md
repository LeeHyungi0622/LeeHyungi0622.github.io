---
title: 210426 SSR(Server-Side-Rendering)과 CSR(Client-Side-Rendering), SPA(Single Page Application)와 MPA(Multiple Page Application)의 차이
date: 2021-04-26 13:42:00
tags:
  - React-Framework
  - self-development
categories:
  - Web-browser
---

<div align="center">
  <img src="/images/post_images/210426_ssr-csr_pros-and-cons.jpeg" alt="SSR vs CSR"/>
</div>

## <ins><b>SSR(Server-Side-Rendering)과 CSR(Client-Side-Rendering)의 차이</b></ins>

렌더링이란 요청받은 내용을 브라우저 화면에 표시하는 작업을 말한다. 그렇다면 SSR(Server Side Rendering)과 CSR(Client Side Rendering)은 어떤 차이가 있을까?
간단하게 동작방식을 노트에 그려보았다.

<div align="center">
  <img src="/images/post_images/210426_SSR_CSR.png" alt="SSR과 CSR 동작"/>
</div>
간단히 말해 브라우저에서 보여주는 화면을 어느 시점에서 최종적으로 만들어서 보여주느냐에 따라 CSR, SSR로 구분한다.

  <!-- more -->

- ### **SSR(Server Side Rendering)**

  `서버 사이드 렌더링은 전통적인 렌더링 방식`으로, 브라우저에서 프론트엔드 서버로 요청을 보내면 프론트엔드 서버에서는 백엔드 서버로 필요한 데이터에 대한 요청을 보내게 된다. 벡엔드 서버에서는 데이터베이스로부터 받은 데이터를 프론트엔드 서버로 넘겨준다.
  프론트엔드 서버에서는 백엔드 서버로부터 넘겨받은 데이터와 HTML 및 static 파일들을 조합해서 브라우저로 넘겨주고 최종적으로 브라우저에 요청한 페이지에 대한 정보를 렌더링하게 된다.

  SSR 방식은 완전하게 만들어진 HTML파일을 받아오고 화면에 렌더링한다. 따라서 웹 서버에 요청을 할때마다 브라우저에 새로고침이 발생하고 서버에 새로운 페이지에 대한 요청을 하는 방식으로 이루어진다.
  `장점`으로는 초기 로딩 속도가 상대적으로 빠르기 때문에 사용자가 컨텐츠를 빠르게 볼 수 있다. 그리고 모든 검색엔진에 대해서 검색엔진 최적화(SEO)가 가능하다.
  `단점`으로는 매번 페이지를 요청할때마다 새로고침이 발생하기 때문에 사용자 UX가 떨어지고, 서버에 매번 요청을 하기 때문에 트래픽 및 서버의 부하가 커진다.
  <br/>

- #### **CSR(Client Side Rendering)**

  `클라이언트 사이드 렌더링은 React, Vue, Angular와 같은 SPA(Single Page Application)가 렌더링되는 방식`으로, SPA는 하나의 페이지에서 화면에 표시되는 컴포넌트만 바꿔치기해서 페이지가 넘어가는 것 처럼 보이는게 하는 것으로, 전통적인 렌더링 방식인 SSR과는 동작에 있어 차이가 있다.
  브라우저에서 프론트엔드 서버로 요청을 보내게 되면, 프론트엔드 서버에서는 브라우저로 하나의 HTML 및 static 파일들을 응답으로 보내게 된다.(`처음에는 데이터가 없는 문서를 반환한다`)
  받은 파일들이 로드되면서 만약에 화면에 표시되는 데이터가 있다면 일단은 데이터가 없기 때문에 로딩화면을 표시하게 된다.
  그 다음으로 브라우저에서는 백엔드 서버로 데이터에 대한 요청을 보내고 백엔드 서버는 데이터베이스로부터 필요한 데이터를 받아 브라우저로 데이터를 넘겨주고, 브라우저에서는 기존에 표시했던 로딩화면을 없애고 받은 데이터를 화면에 표시한다.

  브라우저가 초기에 프론트엔드 서버로부터 HTML과 static파일들을 요청한 뒤에 로드가 되면, 사용자의 상호작용에 따라 JavaScript를 통해 동적으로 화면이 렌더링이 된다.

  `장점`으로는 첫 로딩에서 HTML 및 static파일들만 받아오면, 이후에는 동적으로 빠르게 화면 렌더링이 가능하기 때문에 사용자 UX가 뛰어나다. 그리고 서버에 요청하는 횟수가 훨씬 적기 때문에 서버에서의 부담이 적다.
  `단점`으로는 모든 HTML과 static 파일들이 로드될때까지 기다려야 된다는 점이다. 또한 처음에는 HTML 파일이 비어있기 때문에 크롤러가 데이터를 수집할 수 없어 SEO(검색엔진 최적화)에 문제가 발생할 수 있다. (단, 구글 검색엔진의 경우에는 javascript까지 크롤링한다)

SSR과 CSR을 초기 로딩속도, 서버 부담, SEO를 기준으로 비교해보면 아래와 같은 차이가 있다.

<table>
  <tr>
    <td></td>
    <td align="center"><b>SSR</b></td>
    <td align="center"><b>CSR</b></td>
  </tr>
  <tr>
    <td align="center"><b>초기 로딩속도</b></td>
    <td align="center">CSR에 비해 다운 받는 파일이 많지 않아서 속도가 빠르다.</td>
    <td align="center">모든 JavaScript 파일을 다운 받아와야 하기 때문에 초기에 오래 걸린다.</td>
  </tr>
  <tr>
    <td align="center"><b>서버 부담</b></td>
    <td align="center">서버와 잦은 요청/응답(view 변경시)을 하기 때문에 서버에 부담이 크다.</td>
    <td align="center">data의 요청이 있을때만 서버에 요청을 하기 때문에 서버에 부담이 적다.</td>
  </tr>
  <tr>
    <td align="center"><b>SEO(Search Engine Optimization)</b></td>
    <td align="center">HTML에 대한 정보가 처음에 포함되어 있어 데이터를 수집할 수 있다.</td>
    <td align="center">맨 처음에는 HTML파일에 데이터가 비어있기 때문에 크롤러가 데이터를 수집할 수 없다.</td>
  </tr>
</table>

## <b>MPA(Multiple Page Application) vs SPA(Single Page Application)</b>

<table>
  <tr>
    <td align="center"><b>MPA</b></td>
    <td align="center"><b>SPA</b></td>
  </tr>
  <tr>
    <td>화면마다 HTML파일이 존재하고, 사용자가 화면을 요청할때마다 웹 서버가 필요한 데이터와 HTML을 파싱해서 보여주는 방식으로 동작한다.</td>
    <td>단 하나의 페이지로 이루어진 어플리케이션으로, 단 하나의 HTML을 기반으로 JavaScript를 이용해서 동적으로 화면의 컨텐츠를 바꾸는 방식으로 동작한다.</td>
  </tr>
  <tr>
    <td>동적이지 않은 페이지를 상황에 맞게 클라이언트에 보여주기 때문에 SSR 방식을 채택</td>
    <td>동적으로 DOM을 구성하여 Re-rendering되기 때문에 CSR 방식을 채택</td>
  </tr>
</table>

정리하자면, SPA와 MPA의 차이는 용어대로 페이지를 한 개 쓰냐 여러 개 쓰냐의 차이이고, CSR과 SSR은 렌더링을 어느 시점에서 하느냐의 차이이다.

자 여지까지 SSR과 CSR의 차이에 대해서 살펴보았다. SSR은 한 번에 완전한 HTML을 렌더링하기 때문에 초기 로딩속도는 빠르지만, 새로운 페이지로 이동할 경우 중복되는 파일들을 다시 내려받아야 되기 때문에 CSR보다 속도가 느리다.
반면 CSR은 초기 로딩속도는 느리지만, 첫 페이지 로딩때 모든 파일들을 내려받기 때문에 페이지를 이동할때마다 해당 페이지에서 필요한 데이터만 불러와서 사용하면 된다.

## <b>React의 기본 동작방식(CSR)과 NextJS를 통한 SSR구현</b>

React의 동작방식을 살펴보면 클래스 컴포넌트 기준으로 살펴보면, 우선 render()함수가 우선 실행이 되고, ComponentDidMount() 함수를 통해 데이터를 가져와서 다시 한 번 render()함수가 실행되는 방식으로 동작을 한다.(`CSR`)
React는 초기 렌더링에서 모든 컴포넌트를 내려받기 때문에 어플리케이션의 규모가 커지게 되면, 로딩속도가 지연된다.
따라서 `이제부터 공부해 볼 NextJS는 이러한 한계점을 극복하기 위해서 React를 SSR방식으로 쉽게 구현할 수 있도록 도와주는 React 프레임워크`이다.
NextJS는 초기 로딩때 모든 파일들을 내려받지 않고, 필요에 따라 파일들을 불러올 수 있도록 `Code Splitting`을 지원한다. 프레임워크이기 때문에 정해진 폴더구조가 있는데 아래와 같다.

- **pages 폴더** : 각 page / route들이 위치
- **components 폴더** : React component들이 위치
  즉, 처음 브라우저가 실행되면 모든 파일들을 다 내려받지 않고, 필요한 첫 페이지에 필요한 page 정보만 내려받게 되고, 이후에 다른 페이지로 이동하면 이동한 페이지에서 필요한 page만 내려받는 식으로 동작을 하게 된다.

이외에 다른 구체적인 기능들은 다른 포스팅에서 다뤄보도록 하겠다.
