---
title: 210204 Build a server using ExpressJS & Babel
date: 2021-02-04 21:32:42
tags:
  - NodeJS
  - Express-Framework
categories:
  - NodeJS
---

![](/images/post_images/210204_babel_logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 NodeJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

### **Express 프레임워크를 사용해서 NodeJS 서버 작성**

```javascript
// express라는 이름의 파일을 찾아보고, 없으면 node_modules내부에서 찾아본다.
const express = require('express');
const app = express();

const PORT = 4000;

const handleListening = () => {
  console.log(`Listening on: http://localhost:${PORT}`);
};

app.listen(PORT, handleListening);
```

### **package.json에 entry command 만들어주기**

```javascript
// package.json
"scripts":{
      "start": "node index.js"
}
```

  <!-- more -->

위와같이 entry command를 작성해주면 매번 node 명령으로 index.js(server entry) 파일을 실행시키지 않고, `npm start` 명령으로 server entry 파일을 실행시킬 수 있다.

### **Babel**

Babel은 최신의 JavaScript코드를 이전 버전의 JavaScript 코드로 변환해주는 역할을 한다. Babel에는 다양한 Loader가 존재하는데 NodeJS에서 사용할 것이기 때문에 Babel node를 설치해준다.

- **npm install @babel/node** 설치

- **npm install @babel/preset-env** 설치

- **npm install @babel/core** 설치

- **.babelrc 파일을 추가**해준다.

<ins>babel에는 다양한 stage가 있는데 최신 stage인 -env를 사용해본다.</ins>

**.babelrc**

```javascript
{
  "presets": ["@babel/preset-env"]
}
```

**index.js**

```javascript
import express from 'express';

const app = express();
```

### **package.json에 entry command 수정하기**

```javascript
// package.json
// babel이 최신 JavaScript 코드를 변환해줌과 동시에 node로 작성한 index.js 파일을 실행시켜 줄 것이다.
"scripts":{
      "start": "babel-node index.js"
}
```

여지까지 작성한 방법으로는 server 코드에서 변화가 생기면 수동으로 서버를 끄고 다시 켜야 한다. 이러한 과정은 개발에 있어 매우 비효율적이므로, `nodemon`이라는 package를 설치해서 해결해보도록 하겠다.
여기서 설치하는 `nodemon`은 <ins>**개발자가 좀 더 편하게 개발할 수 있도록 도와주는 dependency이므로, 프로젝트의 실행과 직접적인 관련이 있는 dependency와 구별해서 설치해주는 것이 좋다.**</ins>

따라서 nodemon을 설치할때에는 `option으로 -D`를 붙여주도록 한다.

```bash
$ npm install nodemon -D
```

그럼 아래와 같이 별도의 `devDependencies` 객체로 구분되서 dependency가 설치되는 것을 확인할 수 있따.

```javascript
"devDependencies": {
    "nodemon": "^2.0.4"
}
```

이제 nodemon이 설치되었으니 package.json에서 entry command 수정해준다.
기존의 command에 `nodemon --exec`와 babel이 JavaScript 파일의 변환하는 것을 위해 2초간 delay될 수 있도록 `--delay 2`를 붙여준다.

```javascript
"scripts": {
    "start": "nodemon --exec babel-node index.js --delay 2"
}
```
