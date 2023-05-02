---
title: 210207 API & Networking with Axios instance
date: 2021-02-07 14:55:42
tags:
  - ReactJS
  - ReactJS-Axios
categories:
  - ReactJS
---

![](/images/post_images/react-js-logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 ReactJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

- # Axios vs Fetch

<br/>

- # Fetch()

Fetch API는 Request나 Response와 같은 HTTP Pipeline에 접근 및 조작을 하기 위한 JavaScript interface를 제공한다.
Network를 통해 비동기방식으로 resource를 fetch하기 위해서 사용된다고 이해하면 된다.

사용방법은 아래와 같이 fetch() method에 자원을 얻기 위한 path를 넣어주고 request, response에 접근하면 된다.

```javascript
// fetch의 기본적인 사용
fetch('examples/example.json')
  .then((response) => {
    // Do stuff with the response
  })
  .catch((error) => {
    console.log('Looks like there was a problem: \n', error);
  });
```

  <!-- more -->

## Request object

```javascript
// Request object
{
  method: 'POST', // *GET, POST, PUT, DELETE, etc.
  mode: 'cors', // no-cors, *cors, same-origin
  cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
  credentials: 'same-origin', // include, *same-origin, omit
  headers: {
   'Content-Type': 'application/json'
  },
  redirect: 'follow', // manual, *follow, error
  referrerPolicy: 'no-referrer', // no-referrer, *client
  body: JSON.stringify(data) // body data type must match "Content-Type" header
}
```

## Response object

아래와같이 Response 객체를 통해서 서버로부터 넘겨받은 데이터를 여러 형식으로 받아 올 수 있다.
response.json(): Parse the response as JSON
response.text(): Read the response and return as text
response.formData(): Return the response as FormData object
response.blob(): Return the response as Blob
response.arrayBuffer(): Return the response as ArrayBuffer

<br/>

- # Axios?

Axios는 native JavaScript API가 아니기 때문에 사용하려면 우선 `npm i axios`를 통해 Axios를 설치해줘야 한다. Axios는 JavaScript library로, node.js 또는 XMLHttpRequests로부터 http requests 만들때 사용이 된다.

`그럼 Fetch() method와 비교했을때 Axios library는 어떤 장점이 있을까?`

<em><b>첫번재,</b></em> <ins>Fetch() method에서는 사용할때마다 매번 인자로 resource를 가져올 path를 넣어줘야 하는데, Axios는 baseURL과 기타 설정 부분을 공통처리해서 만든 `instance 객체로 재사용해서 사용이 가능`하다는 점이다.</ins>

![](/images/post_images/210207_axios_instance_creation.png)

```javascript
import axios from 'axios';

const api = axios.create({
  baseURL: 'https://api.baseurl.com/',
  params: {
    api_key: '06e43891a2b919ee11ba3f3894d63374',
    language: 'euc-kr'
  }
});

api.get('test');

export default api;
```

<em><b>두번째,</b></em> <ins>Axios는 download progress를 관리할 수 있는 build-in function이 있다.</ins>

<em><b>세번째,</b></em> <ins>Axios는 XSRF에 대항하여 client-side 보호가 가능하다.</ins>

**XSRF?**

> 사이트 간 요청 위조(또는 크로스 사이트 요청 위조, Cross-site request forgery, CSRF, XSRF)라고 한다. 웹 사이트 취약점 공격들 중에 하나로, 사용자가 자신의 의지와는 무관하게 공격자가 의도한 행위(수정, 삭제, 등록)를 특정 웹 사이트에 요청하게 하는 공격을 말한다.

[Reference website : XSRF-wikipedia](https://ko.wikipedia.org/wiki/%EC%82%AC%EC%9D%B4%ED%8A%B8_%EA%B0%84_%EC%9A%94%EC%B2%AD_%EC%9C%84%EC%A1%B0)

<em><b>네번째,</b></em> <ins>자동으로 request와 response를 변환해준다.</ins>

아래의 예시에서 볼 수 있듯이, data를 JSON으로 자동 convert해주고, 이것을 request body로써 전송해준다.

```javascript
// send a POST request
axios({
  method: 'post',
  url: '/login',
  data: {
    firstName: 'Hyungi',
    lastName: 'Lee'
  }
});
```

<em><b>다섯번째</b></em> <ins>Axios는 각기 다른 HTTP requests를 위한 shorthand methods를 제공한다.</ins>

- axios.**request**(config)
- axios.**get**(url[, config])
- axios.**delete**(url[, config])
- axios.**head**(url[, config])
- axios.**options**(url[, config])
- axios.**post**(url[, data[, config]])
- axios.**put**(url[, data[, config]])
- axios.**patch**(url[, data[, config]])

예를들어, post방식으로 /login 에 입력받은 firstName과 lastname을 request body에 담아 보내고자 한다면 아래와 같이 간단하게 작성해 줄 수 있다.

```javascript
axios
  .post('/login', {
    firstName: 'Hyungi',
    lastName: 'Lee'
  })
  .then(
    (response) => {
      console.log(response);
    },
    (error) => {
      console.log(error);
    }
  );
```

axios.post는 아래의 response 정보를 반환해준다.

```json
// `data` is the response that was provided by the server
  data: {},
  // `status` is the HTTP status code from the server response
  status: 200,
  // `statusText` is the HTTP status message from the server response
  statusText: 'OK',
  // `headers` the headers that the server responded with
  // All header names are lower cased
  headers: {},
  // `config` is the config that was provided to `axios` for the request
  config: {},
  // `request` is the request that generated this response
  // It is the last ClientRequest instance in node.js (in redirects)
  // and an XMLHttpRequest instance the browser
  request: {}
}
```
