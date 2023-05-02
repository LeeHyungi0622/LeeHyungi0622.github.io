---
title: 210515 React with NextJS TIL - Next.js에서 styled-components를 사용하기 위한 설정
date: 2021-05-15 16:37:00
tags:
  - React-Framework
  - self-development
categories:
  - NextJS
---

<div align="center">
  <img src="/images/post_images/210426_reactjs-ssr-nextjs.jpeg" alt="ReactJS with NextJS"/>
</div>

**참고 :** [https://github.com/vercel/next.js/tree/master/examples/with-styled-components](https://github.com/vercel/next.js/tree/master/examples/with-styled-components)

## <ins><b>Next.js에서 styled-components 사용하기</b></ins>

Next.js에서 styled-components를 사용하려면 몇 가지 설정이 필요하다.

(1) babel-plugin-styled-components 설치하기

이 패키지를 설치하는 이유는 Next에서 최초로 SSR이 된 이후에 내부 라우팅을 통해 페이지가 이동하면서 CSR을 하게 되는데, className의 해시값이 서버에서 생성되는 값과 브라우저에서 생성되는 값이 불일치하게 되면서 에러가 발생하기 때문에 설치해줘야 한다.

```zsh
npm i -D babel-plugin-styled-components
```

(2) .babelrc 파일 생성하기

```json
{
  "presets": ["next/babel"],
  "plugins": [["styled-components", { "ssr": true }]]
}
```

(3) pages 폴더 아래에 \_document.js 파일 생성하기

**참고:** [https://nextjs.org/docs/advanced-features/custom-document](https://nextjs.org/docs/advanced-features/custom-document)

  <!-- more -->

```javascript
import Document from 'next/document';
// Import styled-components ServerStyleSheet
import { ServerStyleSheet } from 'styled-components';

export default class MyDocument extends Document {
  static async getInitialProps(ctx) {
    // ServerStyleSheet instance를 생성한다.
    const sheet = new ServerStyleSheet();
    // Page에 있는 컴포넌트에서 style을 검색한다.
    const originalRenderPage = ctx.renderPage;

    try {
      ctx.renderPage = () =>
        // page의 컴포넌트에 적용된 스타일을 검색한다.
        originalRenderPage({
          enhanceApp: (App) => (props) =>
            sheet.collectStyles(<App {...props} />)
        });

      const initialProps = await Document.getInitialProps(ctx);

      return {
        ...initialProps,
        styles: (
          <>
            {initialProps.styles}
            {sheet.getStyleElement()}
          </>
        )
      };
    } finally {
      sheet.seal();
    }
  }
}
```
