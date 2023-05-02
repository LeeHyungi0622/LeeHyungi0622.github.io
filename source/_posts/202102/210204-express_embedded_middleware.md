---
title: 210330 bodyParser와 express embedded middleware(express.json())
date: 2021-03-30 17:11:42
tags:
  - NodeJS
  - Express-Framework
  - HTTP-Request-Response
categories:
  - NodeJS
---

![](/images/post_images/express_img.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 NodeJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

### **Front-end**

Front-end에서 post방식으로 body에 데이터를 담아 보내는 경우,

```javascript
// front-end에서 body를 함께 Request를 보내는 경우,
axios.post('/products', {
  name: 'Lee Hyungi',
  description: 'Hi!'
});
```

<!-- more -->

## **Back-end**

아래와같이 req.body를 통해 front-end로부터의 데이터를 넘겨받는다. 하지만 아래와같이 `req.body`로 값을 읽게 되면, undefined라고 출력됨을 확인할 수 있다.
이에 대해 Express v4.16.0이전에는 bodyParser module을 설치해서 app에 추가해서 해결을 했지만, Express v4.16.0 이후에는 express의 내장 middleware인 express.json()을 사용해서 대체할 수 있다.

```javascript
// back-end에서 요청을 받는다.
...
app.use(express.json());
...
app.post('/products', (req, res) => {
  console.log('req.body : ', req.body);
});
```
