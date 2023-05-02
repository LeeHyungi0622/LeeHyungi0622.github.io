---
title: 210516 ReactJS TIL - 컴포넌트 폴더 구조, 해쉬태그 구현을 위한 정규표현식
date: 2021-05-16 13:45:00
tags:
  - React
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

## <ins><b>컴포넌트 폴더 구조</b></ins>

컴포넌트를 작성할때 각각의 개별 컴포넌트를 하나의 개별 폴더로 분리해서 작성해주는 것이 좋다.
그 이유는 styled-components로 만든 컴포넌트들을 하나의 파일에 같이 작성을 해주게 되면 가독성에 좋지 않기 때문에 하나의 컴포넌트 폴더 내부에 `index.js`와 `styles.js`로 분리해서 styles.js 파일 내에는 styled-components로 작성해준 컴포넌트들만 따로 분리해서 작성해주도록 한다.

**ex)**
`/components/ImagesZoom/index.js`
`/components/ImagesZoom/styles.js`

## <ins><b>해쉬 태그를 위한 정규 표현식</b></ins>

참고 사이트 : [https://regexr.com/](https://regexr.com/)

**(1) //g :** g는 여러개 요소를 찾는 옵션
**(2) /#./g :** .은 # 뒤에 한 개 문자까지 선택
**(3) /#../g :** ..은 # 뒤에 두 개 문자까지 선택
**(4) /#.+/g :** .+는 # 뒤의 모든 문자들을 선택
**(5) /#[^\s#]+/g :** 공백(\s)을 제거하고 다음 해쉬 태그 기준으로 끊기

split 메서드와 함께 사용하는 경우에는 아래와 같이 정규표현의 조건부를 괄호로 감싸줘야 한다.

```javascript
/(#[^\s#]+)/g;

'첫 번째 게시글 #해시태그 #익스프레스'.split(/(#[^\s#]+)/g);
```
