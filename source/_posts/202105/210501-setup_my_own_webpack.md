---
title: 210501 Setting up my own webpack with react and babel
date: 2021-05-01 20:26:00
tags:
  - babel
  - webpack
  - webpack
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/210501_my_own_webpack.png" alt="REACT, WEBPACK, BABEL" />
</div>

## <ins><b>CRA을 사용하지 말고, 나만의 React 프로젝트를 위한 웹팩을 만들자</b></ins>

React프로젝트를 만들때 CRA를 사용해서 손쉽게 React 프로젝트를 생성 할 수 있다. 하지만 이렇게 프로젝트를 생성하다보면, 막상 Webpack을 직접 커스텀해서 해야할때 마냥 어렵게만 느껴지고, 프로젝트에 불필요한 요소들이 자동으로 생성되기 때문에 좋지 않다.

기본적으로 설치해야 될 패키지들은 아래를 참고하자.
`package.json`

```json
"dependencies": {
    "react": "^17.0.1",
    "react-dom": "^17.0.1",
  },
  "devDependencies": {
    "@babel/core": "^7.11.6",
    "@babel/preset-env": "^7.11.5",
    "@babel/preset-react": "^7.10.4",
    "@pmmmwh/react-refresh-webpack-plugin": "^0.4.3",
    "babel-loader": "^8.1.0",
    "react-refresh": "^0.9.0",
    "webpack": "^5.3.2",
    "webpack-cli": "^4.1.0",
    "webpack-dev-server": "^3.11.0"
  }
```

  <!-- more -->

webpack5 부터 새롭게 react-refresh를 사용해서 Hot reloading을 구현한다. (아래 webpack 설정파일에 적용)
`webpack.config.js`

```javascript
const path = require('path');
const ReactRefreshWebpackPlugin = require('@pmmmwh/react-refresh-webpack-plugin');

module.exports = {
  name: 'webpack_with_hot_loader',
  mode: 'development',
  devtool: 'inline-source-map',
  resolve: {
    extensions: ['.js', '.jsx']
  },
  entry: {
    app: './index'
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        loader: 'babel-loader',
        options: {
          presets: [
            [
              '@babel/preset-env',
              {
                targets: { browsers: ['last 2 chrome versions'] },
                debug: true
              }
            ],
            '@babel/preset-react'
          ],
          plugins: ['react-refresh/babel']
        },
        exclude: path.join(__dirname, 'node_modules')
      }
    ]
  },
  plugins: [new ReactRefreshWebpackPlugin()],
  output: {
    path: path.join(__dirname, 'dist'),
    filename: '[name].js',
    publicPath: '/dist'
  },
  devServer: {
    publicPath: '/dist',
    hot: true
  }
};
```

`package.json`

```json
"scripts": {
  "dev": "webpack serve --env development"
},
```
