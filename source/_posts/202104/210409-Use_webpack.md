---
title: 210409 Webpack에 대해서 이해하기
date: 2021-04-09 17:13:42
tags:
  - Webpack
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210409_webpack.png" alt="웹팩(Webpack)" />
</div>

## 웹팩은 왜 사용하는 걸까?

실무에서 개발을 할때에는 여러개의 컴포넌트들을 복합적으로 사용한다. 만약에 이러한 컴포넌트들을 하나의 자바스크립트 파일에 작성을 해준다면 어떻게 될까?
아마 1000줄 10000줄? 그 이상이 될 것이다. 페이스북의 컴포넌트가 대략 2만개라고 하니 그 줄 수는 어마무시할 것이다.
이렇듯 한 개의 자바스크립트 파일에 모든 컴포넌트들을 일괄작성하게 되면 나중에 유지보수도 어려워지기 때문에 각 각의 컴포넌트들은 개별 자바스크립트 파일로써 작성되어야 한다.

이렇게 개별로 작성된 자바스크립트 파일들은 HTML파일에 의해 읽혀질때에는 `하나의 자바스크립트 파일로 합쳐져서 읽혀지게 되는데,` 이러한 여러개의 자바스크립트 파일들을 `하나의 자바스크립트 파일로 만들어주는 기술이 바로 웹팩(Webpack)`이다.

  <!-- more -->

```html
<body>
  <div id="root"></div>
  <!-- 결과 -->
  <script src="./dist/app.js"></script>
</body>
```

위의 코드에서 dist라는 폴더 내부의 app.js파일을 script태그의 src 속성으로 불러오고 있는데, 이 파일이 바로 `webpack 기술로 여러개의 자바스크립트 파일들을 합쳐서 만들어낸 하나의 자바스크립트 파일`이다.

이 웹팩(Webpack)이라는 기술은 소크라라는 개발자에 의해 개발이 되었는데, babel도 적용을 할 수 있고, 쓸데없는 코드(console.log와 같은)도 제거를 할 수 있다.

이 웹팩을 이해하기 위해서는 node를 이해해야 하는데, node란 V8 자바스크립트 엔진에 내장된 자바스크립트 실행환경이다. 웹팩도 자바스크립트로 만들어졌기 때문에 웹팩을 돌리기 위한 이 자바스크립트 실행환경을 이해해야 되는 것이다.

## **웹팩의 사용**

- ### **npm init**

- ### **npm i react react-dom**

  앞서 실습을 해봐서 알겠지만 기본적으로 react를 사용하기 위해서는 react의 기본적인 것들을 담고 있는 라이브러리(react)와 작성한 컴포넌트를 화면에 그리기 위한 react-dom 라이브러리가 필요하다.

- ### **npm i -D webpack webpack-cli**

  프로젝트에서 webpack을 사용하기 위해서는 webpack과 webpack-cli를 설치해야 한다. webpack은 실제 서비스시에는 필요가 없고 개발시에만 필요하기 때문에 DevDependency로 설치를 해준다.

- ### **webpack.config.js 파일 생성**

  webpack.config.js 파일내부에 webpack에 대한 구체적인 설정에 대한 내용을 작성해준다.

- ### **jsx, js 확장자의 차이**

  - 자바스크립트 파일 내에서 jsx 문법을 사용했으면 jsx로 작성을 해주고, 그렇지 않으면 js 파일 확장자를 붙여주도록 한다. 이러한 구분으로 확장자를 작성해주면, 개발자가 파일 확장자만 보더라도 해당 자바스크립트 파일이 jsx 문법이 포함되어있는 리액트 전용 파일이라고 이해를 할 수 있다.

- ### **HTML에서 하나의 자바스크립트 코드**

  웹팩을 사용하면 여러개의 자바스크립트 컴포넌트 파일들을 하나의 자바스크립트 코드로 만들어서 HTML에 넣어줄 수 있다.

- ### **Node에서 경로를 쉽게 조작하기 위해 제공하는 path**

  Node에서 시스템의 경로를 쉽게 조작할 수 있도록 path라는 모듈을 제공해준다. webpack의 설정파일(webpack.config.js)에서 합쳐진 자바스크립트 파일을 생성할 폴더의 위치를 path 모듈을 사용해 손쉽게 지정할 수 있다.

  ```javascript
  output: {
        path: path.join(__dirname, 'dist'),
        filename: 'app.js'
  }, // 출력 (app.js)
  ```

- ### **웹팩 설정파일(webpack.config.js) - 실행 순서로 코드 작성**

  ```javascript
  const path = require('path');

  module.exports = {
    // 웹팩 설정 이름
    name: 'webpack setting name',
    mode: 'development', //실제 서비스 시에는 production으로 작성
    devtool: 'eval',
    // 빠르게 실행하기 위한 코드,
    // 개발시에는 eval
    // 실제 서비스 시에는 hidden-source-map
    //entry section내에 작성할 파일들의 확장자 생략을 위해서 resolve 옵션을 사용
    resolve: {
      extensions: ['.js', '.jsx']
    },
    // 이제 webpack을 이용해서 합칠 자바스크립트 파일들을 지정해준다.
    // 만약에 서로서로 포함관계에 있는 자바스크립트 파일이라면 가장 최상단의 파일만 작성해주면 된다.
    entry: {
      app: ['./client']
    },
    // entry에서 지정한 파일을 읽고 module에서 정의한 여러개의 규칙을 적용한다.
    module: {
      rules: [
        {
          // 정규표현식으로 js와 jsx파일에 룰을 적용한다고 작성
          test: /\.jsx?/
        }
      ]
    },
    // 합칠 자바스크립트 파일들을 읽고 moudle에서 정의한 여러개의 규칙들을 적용하고
    // 결과로 나온 파일을 app.js라는 이름과 확장자로 현재위치에서 dist 폴더를 생성해서 하위에 파일을 넣어준다.
    output: {
      path: path.join(__dirname, 'dist'),
      filename: 'app.js'
    }
  };
  ```

  위와같이 웹팩 설정파일을 작성해주면 jsx관련 에러가 발생한다. 이전에 babel이 jsx코드를 React.createElement로 코드변환을 해준다고 했는데, 웹팩에는 이 babel에 대한 설정이 없기 때문에 별도로 설정을 해줘야 한다.

  우선 첫 번째로 필요한 library들을 설치해준다.
  **(1) @babel/core :** babel의 기본적인 것들을 포함하며, `최신문법을 변환`해주는 역할을 한다.
  **(2) @babel/preset-env :** `현재 사용하는 브라우저의 환경에 맞게` 최신 문법 코드를 변환해준다.
  **(3) @babel/preset-react :** `jsx를 지원`한다.
  **(4) babel-loader :** `babel과 webpack을 연결시켜주는 역할`을 한다.

  위의 모듈들을 설치한 뒤에는 module의 rules의 내부에 추가 규칙을 작성해줘야 한다.

  ```javascript
  .......
  module: {
    rules: [{
      test: /\.jsx?/,
      loader: 'babel-loader',
      options: {
        presets: ['@babel/preset-env', '@babel/preset-react'],
        plugins: ['@babel/plugin-proposal-class-properties']
      }
    }]
  },
  .......
  ```

- ### **웹팩 실행**

  - package.json에서 "scripts"에서 코드를 추가한다.
    `npm run dev`

  ```json
  "scripts": {
    "dev": "webpack"
  }
  ```

  - `npx webpack`을 사용해서 webpack 실행

  실행의 결과로 output section에서 지정한 경로의 폴더에 지정한 파일명과 확장자로 파일이 생성된다.(파일의 내용은 난독화되어있다)

이렇게 직접 웹팩에서 필요한 부분만 최소한으로 작성을 해주면 굳이 CRA를 사용하지 않아도 React 프로젝트를 시작할 수 있다.
최소한의 것들만 설치하고 에러가 나면 추가적으로 필요한 것을 설치해주는 것이 좋다.

- ### **Preset 구체적으로 알아보기**
  앞서 웹팩 설정을 작성할때 module section → rules section → options section → presets section을 살펴보면 아래와 같이 간략하게 배열의 내부에 설치한 모듈을 작성해주었다.

```javascript
presets: ['@babel/preset-env', '@babel/preset-react'];
```

하지만 preset은 여러개의 모듈이 모여서 구성되어 있기 때문에 구체적으로 옵션을 달아 지정해 줄 수 있다.
예를들어 위에서 지정한 `@babel/preset-env`의 경우에는 다양한 브러우저의 버전 호환성을 지원하는 모듈이다.
따라서 아래와 같이 구체적으로 어떤 버전의 브라우저의 호환성을 지원할 것인지 명시해 줄 수 있다.

```javascript
presets: [
  [
    '@babel/preset-env',
    {
      targets: {
        browsers: ['> 5% in KR', 'last 2 chrome versions']
      },
      debug: true
    }
  ],
  '@babel/preset-react'
];
```

구체적으로 지원할 브라우저의 종류를 지정해주는 이유는 구체적으로 지원할 브라우저를 지정하지 않을 경우, webpack에서 자체적으로 하는 작업량이 많아지기 때문에 어플리케이션 개발시에 회사에서 지원할 브라우저의 버전을 구체적으로 명시해주는 것이 좋다.
browsers의 옵션은 아래의 사이트를 참고하도록 하자.
[https://github.com/browserslist/browserslist](https://github.com/browserslist/browserslist)

- ### **웹팩의 설정은 흐름대로 기억하고 작성하자**
  웹팩의 공식 홈페이지에 들어가보면 왼쪽 상단에 아래와 같은 순서로 카테고리가 적혀져 있다.
  [https://webpack.js.org/concepts/](https://webpack.js.org/concepts/)
  > **Concepts**
  > Entry
  > Output
  > Loaders
  > Plugins
  > Mode
  > Browser Compatibility
  > Environment

웹팩 설정에 있어 가장 기본이 되는 항목들을 아래와 같은 기본 틀로써 기억하고 작성하도록 하자.

```javascript
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // (1) 웹팩 기타 설정
  name: '',
  mode: 'development',
  devtool: 'eval',

  // (2) 웹팩 entry 설정
  entry: {},

  // (3) 웹팩 module 설정
  module: {
    rules: [
      {
        test: '',
        loader: '',
        options: {
          presets: [],
          plugins: []
        }
      }
    ]
  },

  // (4) 웹팩 plugins 설정 (module 이외에 추가적으로 추가하고 싶은 plugins)
  plugins: [],

  // (5) 웹팩 output 설정
  output: {}
};
```

**plugin이란** 추가적으로 하고 싶은 작업에 대한 확장 프로그램을 의미한다.
plugin의 종류가 많이 있다. 실무 코드에서는 10개 이상의 plugin이 추가되어 있는 경우도 있으며, 이런 코드에서는 plugin과 최소한의 rule만 남겨두고 실행하면서 에러 발생시에 필요한 plugin, rule을 추가해가며 해당 plugin이 어떤 역할을 하는지 파악하는 것이 중요하다.

아래에 sample로 작성해 본 웹팩 설정파일(webpack.config.js)은 참고하도록 하자.

```javascript
const path = require('path');
const webpack = require('webpack');

module.exports = {
  // 웹팩 설정 이름
  name: 'webpack-setting',
  mode: 'development', // 실서비스: production
  // 빠르게 처리하기 위한 코드
  devtool: 'eval',

  // 확장자 생략을 위해서 resolve 옵션을 사용
  resolve: {
    extensions: ['.js', '.jsx']
  },
  // 이 부분이 중요
  // 목표 : 작성한 JavaScript파일을 하나로 합쳐서
  // html에서 스크립트를 실행하기
  entry: {
    app: ['./client']
  }, // 입력(client.jsx, WordRelay.jsx)

  module: {
    // 여러개의 규칙을 적용
    rules: [
      {
        // 정규표현식으로 js와 jsx파일에 룰을 적용하겠다.
        test: /\.jsx?/,
        // babel을 적용해서 최신 문법의 코드를 구형브라우저에서도 돌아갈 수 있도록
        // 호환되도록 바꿔주겠다.
        loader: 'babel-loader',
        options: {
          // presets: ['@babel/preset-env', '@babel/preset-react'],
          presets: [
            [
              '@babel/preset-env',
              {
                targets: {
                  // 한국에서 점유율이 5% 이상인 브라우저와 크롬의 특정 버전을 지정(최신 크롬 2개 버전)
                  browsers: ['> 5% in KR', 'last 2 chrome versions']
                },
                debug: true
              }
            ],
            '@babel/preset-react'
          ],
          plugins: ['@babel/plugin-proposal-class-properties']
        }
      }
    ]
  },
  // 아래의 plugin의 경우에는 위에서 작성해준 Loader의 options에 대해서 모두 debug를 true로 설정해주겠다는 의미이다.
  plugins: [new webpack.LoaderOptionsPlugin({ debug: true })],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'app.js'
  } // 출력 (app.js)
};
```

아래와 같이 preset-env의 DEBUG 옵션을 터미널에서 확인할 수 있다.

```zsh
@babel/preset-env: `DEBUG` option

Using targets:
{
  "chrome": "88",
  "edge": "89",
  "ie": "11",
  "ios": "14",
  "samsung": "13"
}
```

## **웹팩 Hot reload와 웹팩 dev Server**

이 웹팩의 Hot reload 기능을 추가하지 않은 상태에서는 작성하던 코드에서 변경사항이 생기면 `npm run dev`나 `npx webpack` 명령을 통해서 변경사항이 반영된 JavaScript 파일로 다시 생성해줘야 한다.
하지만 매번 코드상에 변경사항이 생길때마다 수동으로 webpack을 실행시키는 것은 여간 귀찮은 일이 아닐 수 없다.
따라서 이 Hot reload라는 설정을 추가해서 자동으로 코드상에서 변경점이 생겼을때 이를 감지해서 자동으로 webpack을 실행시켜줄 수 있다.

webpack-cli의 버전이 4버전으로 업데이트 되면서 기존에 hot reload기능을 위해서 사용하였던 react hot loader가 `react refresh`로 업데이트 되었다.

- ### **Hot reload 사용을 위한 설치 라이브러리**

  Hot reload 기능을 사용하기 위해서는 아래 3가지 라이브러리를 설치해줘야 한다.

  ```zsh
  $ npm i -D react-refresh @pmmmwh/react-refresh-webpack-plugin
  $ npm i -D webpack-dev-server
  ```

  webpack-dev-server는 프론트 개발의 편의를 위한 개발 서버이다.

- ### **package.json의 script 수정 및 webpack 설정파일(webpack.config.js) 업데이트**
  앞서 hot reload를 위한 라이브러리 설치가 완료되었다면 package.json의 script 수정 및 webpack 설정파일을 수정해줘야 한다.
  `package.json`
  ```json
  "scripts": {
    "dev": "webpack serve --env development"
  }
  ```
  `webpack.config.js`
  웹팩 설정파일에서는 설치한 `@pmmmwh/react-refresh-webpack-plugin` 라이브러리를 import해서 output section 이전의 plugins section에 넣어줘야 한다.
  ```javascript
  const RefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');
  ......
  plugins: [
    new RefreshWebpackPlugin()
  ],
  ......
  ```
  그리고 babel-loader의 옵션에 있는 plugins에 `react-refresh/babel`을 추가해줘야 한다.
  이렇게 작성을 해주게 되면, babel이 최신 문법의 자바스크립트 코드를 구형 자바스크립트 코드로 변환을 해줌과 동시에 `hot reload`기능도 같이 추가를 해준다.
  ```javascript
  ........
  loader: 'babel-loader',
  options: {
    presets: [
      ......
    ],
    plugins: [
      'react-refresh/babel'
    ]
  }
  ........
  ```
- ### **dev server 설정**

  여기서 설정하는 개발 서버는 프론트 개발의 편의를 위해 두는 서버이다.
  기존의 웹팩 설정에서 output section의 아래에 개발 서버에 대한 설정을 작성하도록 한다.
  이 개발서버의 역할은 webpack 설정파일에 작성한대로 build를 해주고 /dist/ 폴더에 결과물을 저장, index.html 파일에 반영을 해주는 역할을 한다.

  hot reloading기능은 코드상에 변경점이 생겼을때 이를 자동으로 감지하여 설정해둔 개발서버가 앞서 설명한 일련의 과정을 처리할 수 있게 도와준다.

  ```javascript
  devServer: {
    // output과 동일
    publicPath: '/dist/',
    hot: true
  }
  ```

  reloading은 기본적으로 지원하지만 hot reloading이 reloading과 다른점은 `기존의 데이터를 유지하느냐 안하느냐`에 있다.
  hot reloading은 기존의 데이터의 상태를 유지하면서 변경된 코드에 맞게 화면을 새로고침해준다.
  예를들어 1단계부터 4단계까지의 작업을 하는 도중에 3단계에서 코드를 변경할 필요성이 생겼다고 가정하자.
  hot reloading 기능을 사용하지 않는다면, 3단계에서 코드를 변경하면 기존의 상태 데이터도 같이 초기화가 되어 다시 1단계부터 데이터의 상태를 업데이트 해줘야 한다.
  하지만 hot reloading 기능을 사용한다면, 3단계에서 코드 변경을 해도 기존의 상태 데이터는 유지를 시켜준다. 따라서 즉각적으로 작업을 이어갈 수 있다.

- ### **hot reload관련 로그 확인하기**

  WDS(Webpack Dev Server), HMR(Hot Module Replacement)
  <div align="center">
    <img src="/images/post_images/210410_hot_reload_console_log.png" alt="Hot reload관련 로그">
  </div>
  어떤 컴포넌트가 바뀌어서 업데이트가 되었는지 확인한다. WDS가 실제 변경사항을 받아서 업데이트를 해준다.

- ### **path와 publicPath의 차이**
  웹팩 설정 파일의 output과 devServer 설정 부분을 보면 path와 publicPath에 대한 속성값이 있는 것을 볼 수 있다.
  여기서 path는 실제경로를 의미하고 publicPath는 가상경로를 의미한다.
  cf) NodeJS에서 `app.use('/dist', express.static(__dirname, 'dist'))`를 작성해주는 부분이 있는데 앞의 '/dist' 부분이 publicPath(가상경로)를 의미하고, 두 번째 인수가 path(실제경로)를 의미한다.

참고를 위해 작성한 웹팩 설정파일을 첨부해둔다.

```javascript
const path = require('path');
const webpack = require('webpack');
const RefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');

module.exports = {
  // 웹팩 설정 이름
  name: 'webpack-setting-with-hot-reloading',
  mode: 'development', // 실서비스: production
  // 빠르게 처리하기 위한 코드
  devtool: 'eval',

  // 확장자 생략을 위해서 resolve 옵션을 사용
  resolve: {
    extensions: ['.js', '.jsx']
  },
  // 이 부분이 중요
  // 목표 : 작성한 JavaScript파일을 하나로 합쳐서
  // html에서 스크립트를 실행하기
  entry: {
    app: ['./client']
  }, // 입력(client.jsx, WordRelay.jsx)

  module: {
    // 여러개의 규칙을 적용
    rules: [
      {
        // 정규표현식으로 js와 jsx파일에 룰을 적용하겠다.
        test: /\.jsx?/,
        // babel을 적용해서 최신 문법의 코드를 구형브라우저에서도 돌아갈 수 있도록
        // 호환되도록 바꿔주겠다.
        loader: 'babel-loader',
        options: {
          // presets: ['@babel/preset-env', '@babel/preset-react'],
          presets: [
            [
              '@babel/preset-env',
              {
                targets: {
                  // 한국에서 지원률이 5% 이상인 브라우저와 크롬의 특정 버전을 지정
                  browsers: ['> 1% in KR', 'last 2 chrome versions']
                },
                debug: true
              }
            ],
            '@babel/preset-react'
          ],
          plugins: [
            '@babel/plugin-proposal-class-properties',
            'react-refresh/babel'
          ]
        }
      }
    ]
  },
  plugins: [
    new webpack.LoaderOptionsPlugin({ debug: true }),
    new RefreshWebpackPlugin()
  ],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: 'app.js',
    publicPath: '/dist/'
  }, // 출력 (app.js)
  devServer: {
    // output와 동일
    publicPath: '/dist/',
    hot: true
  }
};
```
