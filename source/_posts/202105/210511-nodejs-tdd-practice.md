---
title: 210511 Node.js TDD Practice 다섯번째 이야기 - 통합 테스트 (Integration Test) 코드 작성
date: 2021-05-11 12:40:00
tags:
  - NodeJS
  - Jest
  - Integration-Test
  - TIL
categories:
  - NodeJS
---

<div align="center">
  <img src="/images/post_images/210402_nodejs_mock_http_requests.jpg" alt="Nodejs Mocking Http Request">
</div>

## <b>통합 테스트(Integration Test)</b>

모듈을 통합하는 단계에서 수행하는 테스트로, 단위 테스트를 통해서 모듈들이 모두 잘 작동하는 것을 확인한 뒤에 모듈들을 서로 연동하여 테스트를 수행하는 것을 말한다.

## **Supertest**

단위 테스트에서 jest 모듈을 사용해서 테스트를 했다면, 통합 테스트는 supertest라는 모듈을 이용해서 구현을 한다.

단위 테스트에서는 MongoDB 부분은 문제가 없다는 가정하에 mock 함수로 처리를 했는데 통합 테스트에서는 supertest를 사용해서 실제로 요청을 보내서 테스트를 진행한다.

## **통합 테스트 코드 작성**

통합 테스트 코드는 실제 작성한 코드의 로직을 기반으로 작성한다.

예를들어 아래와 같이 MongoDB에 req.body를 통해 넘겨받은 데이터를 새로 추가하고 추가된 데이터를 별도의 변수에 담아 response의 status code가 201인 경우에 json 데이터로 함께 전달하고 있다.

```javascript
exports.createProduct = async (req, res, next) => {
  try {
    const newProduct = await productModel.create(req.body);
    console.log('createProduct', newProduct);
    res.status(201).json(newProduct);
  } catch (error) {
    next(error);
  }
};
```

`위의 코드를 통합 테스트 코드로 작성을 하면,` 아래와 같이 통합테스트 코드를 구현할 수 있다.

```javascript
const request = require('supertest');
const app = require('../../server');
const newProduct = require('../data/new-data.json');

test('POST /api/products', async () => {
  // 실제 코드와 같이 요청을 보내고 함께 보낸 데이터를 response 변수에 담는다.
  const response = await request(app).post('/api/products').send(newProduct);
  // 위의 response 객체의 상태코드를 확인
  expect(response.statusCode).toBe(201);
  // response의 body(json) 데이터를 확인을 한다.
  expect(response.body.name).toBe(newProduct.name);
  expect(response.body.description).toBe(newProduct.description);
});
```

  <!-- more -->

## **에러에 대응하는 통합 테스트 코드 작성**

아래와 같이 인위적으로 에러를 발생시키기 위해 MongoDB의 schema에 위반되는 데이터를 넣어주었다.
넣어준 다음에 response를 통해 에러코드가 500번인 것을 확인하고, response.body를 통해서 에러 메시지를 확인하도록 한다.
하지만 이 시점에서 에러 메시지를 확인하면 빈 객체 (Object {})가 반환되는 것을 알 수 있다.

```javascript
test('should return 500 on POST /api/products', async () => {
  const response = await request(app)
    .post('/api/products')
    // 에러를 인위적으로 발생시키기 위해 아래와 같은 데이터 전달
    .send({ name: 'phone' });
  expect(response.statusCode).toBe(500);
  console.log('response.body', response.body);
  expect(response.body).toStrictEqual({ message: '' });
});
```

**이 문제를 해결하기 위해서는 express가 에러를 어떻게 핸들링하는지에 대한 이해가 필요하다.**

express에서는 middleware에서 에러가 발생하면 이 에러를 에러 처리기(handler)로 보내준다.
즉시 에러를 보내주기 때문에 다음으로 실행되어야 할 middleware 중에 에러처리기가 아닌 middleware는 처리를 생략하게 된다.

에러처리기는 아래와 같이 인자로 총 4개의 인자가 들어간다.

```javascript
app.use(function (error, req, res, next) {
  res.json({ message: error.message });
});
```

**참고 :**[http://thecodebarbarian.com/80-20-guide-to-express-error-handling.html](http://thecodebarbarian.com/80-20-guide-to-express-error-handling.html)

하지만 비동기 요청으로 인한 에러는 controller에서 작성한 콜백함수의 세번째 인자인 next를 통해서 전달을 해줘야 한다. 그렇지 않으면, 에러 처리기에서 위의 에러 메시지를 받지 못하기 때문에 서버가 crash된다.

따라서 아래와 같이 발생한 에러에 대해서는 middleware의 세번째 인자인 next를 통해서 error를 넘겨줘야 한다.
(이 부분은 이미 단위 테스트 학습과정에서 학습을 하였다)

```javascript
const app = require('express');
app.get('*', function (req, res, next) {
  setImmediate(() => {
    next(new Error('woops'));
  });
});

app.use(function (error, req, res, next) {
  res.json({ message: error.message });
});

app.listen(3000);
```

## **에러 핸들러 작성**

```javascript
app.use((error, req, res, next) => {
  res.status(500).json({ message: error.message });
});
```
