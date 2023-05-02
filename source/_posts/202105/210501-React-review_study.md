---
title: 210501 babel / webpack / react-scripts
date: 2021-05-01 11:04:00
tags:
  - babel
  - webpack
  - react-script
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210501_react-webpack-babel.png" alt=" React/Babel/Webpack" />
</div>

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

## <ins><b>react-scripts</b></ins>

react-based project를 사용할때 직접 빌드 환경을 셋팅하는 것은 많은 시간이 소요된다. 그래서 React 개발팀이 `react-scripts`라는 npm 패키지를 만들었는데, 이 패키지에는 평균적인 React app에서 많은 사람들이 필요로하는 것들에 대한 기본적인 setup을 포함하고 있다.
`위에서 설명한 Babel과 Webpack 또한 react-scripts의 dependency로써 포함되어있다.`

## <ins><b>babel</b></ins>

babel은 높은 버전의 ECMAScript(unsupported or cutting-edge)를 ES5로 변환해주는 역할을 하는 transpiler이다. (ES5는 범용적인 브라우저에서 지원을 하기 때문)

## <ins><b>webpack</b></ins>

webpack은 dependency를 분석기이자 module bundler이다. 예를들어 module A가 B를 dependency로 요청을하고, module B가 C를 dependency로 요청을 한다면 webpack은 C-B-A 와 같이 dependency map을 생성한다. 실제로는 매우 복잡하지만, 기본적인 컨셉은 webpack이 모듈들을 복잡한 dependency 관계들과 함께 번들들로 통합한다.

**webpack과 babel의 관계:** 웹팩이 종속성을 처리할 때, 웹팩은 자바스크립트 위에서 작동하기 때문에 모든 것을 자바스크립트로 변환해야 한다. 그 결과, 다른 로더를 사용하여 다른 유형의 리소스/코드를 javascript로 변환한다. <ins><b>ES6 또는 ES7에 대한 변환이 필요할 때에는 babel-loader를 사용해서 webpack과 babel을 연결시켜 사용한다.</b></ins>
