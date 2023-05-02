---
title: 210202 ExpressJS Installation
date: 2021-02-02 06:51:42
tags:
  - NodeJS
  - Express Framework
categories:
  - NodeJS
---

![](/images/post_images/210202_npm_logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 NodeJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

# **ExpressJS 설치**

NPM(Node Package Manager) : 업데이트된 각종 NodeJS 관련 패키지의 중앙집중화된 개념으로 NodeJS 월드의 중심과 같은 곳이다.

NPM은 NodeJS를 다운받게 되면 자동으로 설치가 되기 때문에 별도로 설치할 필요는 없다.

**Package manager로 npm을 사용하기 위해서는 우선 npm이 정한 방식으로 프로젝트를 시작해야한다.**

1. **npm init** : npm 초기화 (package name/description/author 설정)
   → 결과적으로 package.json 파일이 생성이 된다.

  <!-- more -->

2. **npm 설치** : 'npm install express' 명령실행한다.
   (package.json 파일이 있는 폴더위치에서 실행을 해야한다)

3. 설치가 끝난뒤에 살펴보아야하는 것은 node modules 부분이다. 이 부분은 npm을 통해서 다운을 받은 것들인데 여기보면 dependency라는 것을 갖게 된다.
   어플리케이션 개발시에 필요한 것들을 자동으로 npm module에서 찾아서 사용해준다.

# 프로젝트 협업시에는 작성한 코드와 Package.json 파일만 넘겨주기

- 두 파일만 받아서 프로젝트 폴더 내부에서 `npm install`만 실행해주면 자동으로 필요한 파일들을 다시 전부 생성해준다.
