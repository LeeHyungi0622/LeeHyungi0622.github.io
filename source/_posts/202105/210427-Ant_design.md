---
title: 210427 Ant Design - antd와 styled-components, emotion, antd Grid system, antd Form and Button, antd Card component
date: 2021-04-27 23:35:00
tags:
  - Ant-design
  - self-development
categories:
  - Ant-Design
---

<div align="center">
  <img src="/images/post_images/210510_ant_design.png" alt="Ant Design"/>
</div>

## <ins><b>antd와 styled-components</b></ins>

antd 디자인을 사용해서 디자이너가 없어도 그럴듯하게 디자인을 할 수 있다. 다만 개성이 없어지는 특징이 있지만, 보통 실무에서 고객사 프로젝트를 할때에는 디자이너가 직접 커스텀 디자인을 하게 된다. antd 디자인을 사용하면 버튼이나 아이콘 등 질좋은 디자인 컴포넌트들을 사용할 수 있다.

[https://ant.design/](https://ant.design/)

## <ins><b>styled-components와 emotion</b></ins>

react에서 css를 적용할때 전처리기(sass, less)를 사용하기도 하고, css를 적용해서 component를 생성할 수 있는 styled-components를 사용하기도 한다.
emotion은 styled-components와 매우 비슷하기 때문에 styled-components를 잘 익히고 나면 공식문서를 보고 바로 적용해서 쓸 수 있다. emotion은 SSR에 최적화해서 사용할 수 있다는 장점이 있기 때문에 시간날때 한 번 써보는 것을 권장한다.

  <!-- more -->

npm trends라는 사이트에서 현재 사용중인 패키지가 유사한 다른 패키지와 사용률에서 어떤 차이가 있는지 비교할 수 있다.

[https://www.npmtrends.com/](https://www.npmtrends.com/)

```zsh
$ npm i antd styled-components @ant-design/icons
```

antd와 @ant-design/icons를 별도로 설치하는 이유는 어플리케이션의 용량 대부분이 이미지파일의 용량이 차지하기 때문에 이러한 아이콘 이미지들을 최적화시키기 위해서 별도의 다른 라이브러리로 분리한다. 그래서 antd와 icons를 위한 @ant-design/icons 라이브러리가 분리된 것이다.

## <ins><b>antd와 react 연결</b></ins>

공식 사이트에서 `Ant Design of React` 공식문서를 보면 antd와 react를 연결하는 방법을 찾을 수 있다.
[https://ant.design/docs/react/introduce](https://ant.design/docs/react/introduce)

NextJS에는 기본적으로 내부에 webpack이 있다. 원래 import는 자바스크립트 파일만 가능하고, CSS파일은 할 수 없다. 하지만 webpack이 CSS파일이 import되어있으면 알아서 style tag로 바꿔서 HTML에 붙여준다.
webpack은 이미지도 별도의 처리를 해서 넣어주기도 하고 loader를 사용해서 여러가지 파일을 확장자와 상관없이 합쳐주는 역할을 한다.

## <ins><b>antd Grid system - Row, Col</b></ins>

[https://ant.design/components/grid/](https://ant.design/components/grid/)
antd에서 제공하는 Grid system을 활용해서 간편하게 반응형 페이지를 만들 수 있다. 화면의 레이아웃은 우선 가로로 나누고 나눠진 가로에서 세로로 나눠서 화면 레이아웃을 마크업한다.
xs(`모바일`), sm(`테블릿`), md(`데스크탑`), lg, xl(대형화면) 등의 속성을 Col 태그에 값과 함께 넣어서 손쉽게 반응형 페이지를 만들 수 있다.
`예시`

```javascript
// 모바일 화면일 경우에는 각 칼럼이 100%(24)의 너비로 스택과 같은 형태로 화면에 배치된다.
// 데스크탑 크기의 화면일 경우 25% 50% 25%의 너비로 칼럼이 세로로 배치된다.
// gutter는 화면의 칼럼들이 너무 붙어있는 경우 사이 간격을 약간씩 떨어뜨릴 수 있도록 하는 속성이다.
<Row gutter={8}>
  <Col xs={24} md={6}>
    first column
  </Col>
  <Col xs={24} md={12}>
    second column
  </Col>
  <Col xs={24} md={6}>
    third column
  </Col>
</Row>
```

## <ins><b>antd Form and Button</b></ins>

antd를 사용해서 간편하게 Form 태그와 Button태그를 만들 수 있다. Form 태그의 내용을 submit하기 위해서는 Button 태그의 속성에 htmlType="submit"을 넣어줘야 한다. Form 태그에는 onFinish 속성을 넣어 submit이 된 후의 처리에 대해서 별도로 작성해줄 수 있다. onFinish 속성에 넣을 함수의 경우 기본적으로 e.preventDefault()가 적용되어 있기 때문에 적어주지 않아도 된다.

## <ins><b>antd Card component</b></ins>

깔끔하게 하나의 section으로 분리해서 디자인이 가능하기 때문에 유용한 컴포넌트이다.
