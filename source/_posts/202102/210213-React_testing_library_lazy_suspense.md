---
title: 210213 Bundle size와 Lazy loading
date: 2021-02-13 11:40:42
tags:
  - React-router-test
  - React-testing-library
  - Unit-testing
  - Bundle-size
  - Lazy
  - Suspense
categories:
  - React-Test
---

![](/images/post_images/210213_lazy_loading.jpeg)

<br/>

### <ins>**Bundle size와 Lazy loading**</ins>

React routing test를 하면서 각 각의 Component를 lazy loading하여 테스트를 진행하였다.

이 <ins><b>lazy loading(지연로딩)에 대해서 좀 더 자세하게 알아두고자 포스팅으로 정리</b></ins>를 해둔다.

React app의 크기가 커짐에 따라 개발자는 bundle size에 대한 걱정을 시작해야 할 수 있다. 이 bundle size란 사용자가 React app을 load하기 위해 download해야만 하는 JavaScript의 양을 의미한다.
이 Bundle size가 클수록 사용자가 app으르 보는데 시간이 오래걸린다.

따라서 이 Lazy를 사용해서 component가 rendering 될때에 <ins><b>사용되지 않는 구성요소에 대한 로드를 지연시킬</b></ins> 수 있다.

  <!-- more -->

> As your React app gets larger, you may have to start worrying about its bundle size. An app's bundle size is the amount of JavaScript a user will have to download to load your app. The bigger the bundle size is, the longer it will take before a user can view your app.

`Googling하면서 React app의 bundle 사이즈를 분석하고 코드 분할(Code splitting)을 통해 bundle 사이즈를 줄이는 것과 관련된 괜찮은 내용의 블로그를 찾아서 아래에 첨부해두었다.`

React.lazy와 Suspense를 사용한 Code-splitting도 bundle size를 줄이는 방법 중에 하나이다.

[https://www.emgoto.com/react-bundles-and-code-splitting/](https://www.emgoto.com/react-bundles-and-code-splitting/#:~:text=As%20your%20React%20app%20gets,user%20can%20view%20your%20app.)
