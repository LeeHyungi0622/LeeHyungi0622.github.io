---
title: 210204 HTTP - Request/Response
date: 2021-02-04 22:03:42
tags:
  - NodeJS
  - Express-Framework
  - HTTP-Request-Response
categories:
  - NodeJS
---

![](/images/post_images/210204_http_request_response.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 NodeJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

### **GET/POST 방식 비교표**

![](/images/post_images/210204_get_post_comparision.png)

GET 방식과 POST 방식은 위와같이 정보 전송방식과 보안적 측면, 전송할 수 있는 데이터의 길이, 전송데이터의 Caching 가능 유/무 등에서 차이점을 보인다.

HTTP의 동작방식에서 URL을 통해서 browser에 접속하게 되면, browser가 GET method 방식으로 Browser의 Page를 읽어온다.

로그인과 같이 중요한 정보를 전송할때에는 POST method로 browser에서 server로 정보를 전달하게 된다.

간단하게 browser상에서 /(root) 경로로 이동했을때의 처리를 작성해보자.

**index.js**

```javascript
const handleHome = () => {
  console.log('Hi from home!');
};

app.get('/', handleHome);
```

  <!-- more -->

위와같이 작성을 해주면, console에서는 "Hi from home!"이라는 메시지를 확인할 수 있지만, 서버로 보내는 특정 response가 없기 때문에 웹 페이지가 무한 loading상태임을 확인할 수 있다.

```javascript
const handleHome = (req, res) => {
  console.log(req);
  res.send('Hello from home');
};

const handleProfile = (req, res) => {
  res.send('You are on my profile');
};

app.get('/', handleHome);
app.get('/profile', handleProfile);
```

위와같이 NodeJS는 서버와 Route를 생성하고 그것에 응답하는 방식으로 작동한다.

지금은 간단한 동작 확인을 위해서 위와같이 callback function과 router 처리를 같은 파일에서 작성을 해주었지만, 프로젝트 구성시에는 callback function은 controller로써 기능별로 분류를 해주고, router 기능을 하는 부분도 별도로 분류해서 작성을 해 줄 것이다.

실제로 웹 서버를 동작시킬때에는 위와같이 `res.send("[text]")`가 아닌 완전한 HTML파일을 rendering해준다.
