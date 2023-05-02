---
title: 210601 faker를 사용해서 dummy data 만들기, placeholder.com, Redux toolkit
date: 2021-06-01 00:36:00
tags:
  - faker
  - dummy-data
  - self-development
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210601_faker_image.png" alt="faker"/>
</div>

## <ins><b>faker</b></ins>

개발을 하다보면 임의로 데이터를 넣어 실제 페이지가 어떻게 출력이 되는지 확인해야될 때가 있다.
소수의 데이터는 괜찮지만 10개 이상의 데이터를 넣어서 확인을 해야 될 때에는 여간 번거로운 일이 아니다.
이런 경우에 facker라는 패키지를 사용해서 임의의 dummy 데이터를 넣어 줄 수 있다.
(`무한 스크롤링 테스트`)

**[참고]: [https://www.npmjs.com/package/faker](https://www.npmjs.com/package/faker)**

```zsh
$ npm i faker
```

## <ins><b>Placeholder.com</b></ins>

dummy 이미지 대체하여 사용할 수 있다.

**[참고] : [https://placeholder.com/](https://placeholder.com/)**

## <ins><b>Redux toolkit</b></ins>

createReducer, createAction을 사용해서 Redux를 사용할때 코드가 길어지는 것을 보완할 수 있다.

이전에 공부할때 정리한 내용을 참고하자.

[http://localhost:4000/2021/04/30/202104/210430-Redux_TIL/](http://localhost:4000/2021/04/30/202104/210430-Redux_TIL/)
