---
title: 210611 Angular - Angular 등장배경과 Angular 프로젝트 생성
date: 2021-06-11 09:07:00
tags:
  - Angular
categories:
  - Angular
---

<div align="center">
  <img src="/images/post_images/210611_angular.png" alt="Angular">
</div>

## <b>Angular가 등장하게 된 배경</b>

Angular가 등장하게 된 배경을 이해하기 위해서는 우선 웹 개발의 변화에 대한 이해가 필요하다.
변화는 Ajax - SPA(MVC) - Web Component 순으로 변화가 되었다.

Ajax를 사용하지 않는 웹 페이지의 경우, 브라우저에서 URL을 서버에 요청하게 되면 서버가 data(서버의 사용자 세션에 저장)를 사용해서 HTML 웹 페이지를 만들어서 응답으로 보내준다.

Ajax의 등장이후에는 한 번의 페이지를 받아온 이후에 ajax를 통해서 DOM element를 조작해서 페이지를 업데이트한다.

버튼 클릭과 같은 사용자 입력이 있는 경우, 데이터가 필요한 경우 ajax를 통해 서버로 요청을 보내고 서버로부터 json, xml 형태의 응답을 이용해서 DOM element를 업데이트하게 된다.

ajax를 이용하면, 화면이 바뀔때마다 서버로 요청을 보내는 것이 아니라, JavaScript를 사용해서 동적으로 브라우저에 올라가있는 데이터를 이용해서 DOM을 조작하게 된다.

장점으로는 서버에 요청이 줄어들기 때문에 좀 더 빠르고 서버쪽 부담이 줄어든다.

**하지만 규모가 큰 웹 어플리케이션에서 코드가 복잡해지고, 재사용이 어려웠기 때문에 코드에 대한 구조화가 필요**해졌고, 매번 새로운 페이지를 서버로부터 읽어오게 되면, 리소스를 서버로부터 다시 읽어오고, 브라우저 렌더링 다시 하기 때문에 성능저하가 문제되었다.

위와같은 문제를 해결하기 위해 등장하게 된 것이 `SPA라는 개발패턴`이다. Ajax의 확장판이라고 이해할 수 있다.

SPA 개발패턴은 URL 경로에 따라 자바스크립트로 화면을 동적으로 그리는 것을 말한다. 이때 웹 어플리케이션의 구조를 잡기 위한 프레임워크가 필요하게 되었는데, 이때 등장하게 된 프레임워크 중에 하나가 AngularJS이다.

\* `AngularJS` : 단일 페이지 웹 어플리케이션 개발을 위한 자바스크립트 프레임워크

하지만, AngularJS는 규모가 큰 어플리케이션의 개발에 있어, 성능이슈를 보이고 복잡한 지시자 문법이 개발시에 큰 어려움을 줬다.
이러한 어려움을 해결하기 위해 등장하게 된 개념이 `Web Component 기반의 개발`이다.
이 Web Component는 `Custom element, Shadow DOM, HTML Import, HTML template의 종합적인 개념`으로, 웹 플랫폼 자체에서 재사용이 가능한 컴포넌트 기반으로 개발하는 것이 가능해졌다.

이를통해 기존의 `성능이슈와 개발 생산성 문제`를 해결할 수 있었다. 이때부터 기존의 프레임워크였던 AngularJS가 플랫폼인 Angular2 혹은 Angular4로 발전하게 되었다.

<div>
    <img src="/images/post_images/from_framework_to_platform.png" alt="프레임워크에서 플랫폼으로 변화">
</div>

  <!-- more -->

**(1) 회색 부분** : Angular의 Core한 부분
**(2) Universal** : SSR(Server Side Rendering)을 위한 부분
**(3) Service Worker** : PWA(Progressive Web App)을 지원하는 부분
**(4) Material** : Mobile/Desktop에 사용가능한 UI 컴포넌트 지원
**(5) Angury** : 디버깅 툴

<table>
    <tr>
        <td></td>
        <td align="center"><b>Angular v1.x</b></td>
        <td align="center"><b>Angular(v2, v4, ...)</b></td>
    </tr>
    <tr>
        <td align="center">중첩 Scope<br/>("$scope", watchers)</td>
        <td align="center">O</td>
        <td align="center">X</td>
    </tr>
    <tr>
        <td align="center">지시자와 컨트롤러</td>
        <td align="center">O(v1.5에서 component api추가)</td>
        <td align="center">X</td>
    </tr>
    <tr>
        <td align="center">비즈니스 로직 구현</td>
        <td align="center">Controller를 구현한 함수</td>
        <td align="center">타입 스크립트(ES6) 클래스에 비즈니스 로직구현</td>
    </tr>
    <tr>
        <td align="center">자바스크립트 모듈 시스템</td>
        <td align="center">require.js를 통한 구현</td>
        <td align="center">타입 스크립트(ES6) 모듈 시스템 지원</td>
    </tr>
</table>

## <b>Angular CLI</b>

Angular CLI를 활용하여 터미널이나 콘솔을 통해 프로젝트 생성나 컴포넌트, 라우터, 서비스를 생성할 수 있다.

```zsh
# angular CLI 설치
$ sudo npm i -g @angular/cli
# Angular CLI 버전 확인
$ ng --version
# 프로젝트 생성
$ ng new [project name]
```

## <b>Angular 프로젝트 생성</b>

Angular는 기본적으로 TypeScript를 기반으로 작성을 하고, 프로젝트를 만들면 src 폴더 하위에 아래와 같은 구성으로 생성이된다.

**src 폴더구성**
(1) app : 실제 어플리케이션 관련 파일들 위치
(2) assets : 이미지, 리소스
(3) environment : Prod Mode로 실행을하면, \*.prod.ts에 있는 내용이 \*.ts파일로 덮어써지게 된다. (서버 URL과 같은 배포에 필요한 정보들이 덮어써진다)

**프로젝트 실행**

```zsh
$ ng serve
```

**자주 사용되는 명령어 확인**

```zsh
$ ng help
```

**(1) --aot(ahead of time compilation)**

**(2) --watch**

**(3) ng completion** : command line을 자동완성

**(4) ng doc** : Angular 공식 페이지 열기

**(5) ng e2e** : E2E 테스트 실행

**(6) ng eject** : Angular CLI가 webpack 기반인데 webpack configuration을 빼서 customize하고 싶을 때 사용한다.

**(7) ng generate** : 새로운 컴포넌트, 라우터, 모듈, 서비스, 클래스를 Angular CLI를 통해서 만들때 사용된다.

**(8) ng lint** : 코드의 스타일을 확인할때 사용된다.

**(9) ng serve** : 빌드후에 프로젝트를 서버로 띄어줄때 사용한다.
