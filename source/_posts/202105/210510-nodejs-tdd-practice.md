---
title: 210510 Node.js TDD Practice 네번째 이야기 - 비동기 요청 에러에 대한 테스트 코드 작성
date: 2021-05-10 11:32:00
tags:
  - NodeJS
  - Jest
  - Unit-Test
  - TIL
categories:
  - NodeJS
---

<div align="center">
  <img src="/images/post_images/210402_nodejs_mock_http_requests.jpg" alt="Nodejs Mocking Http Request">
</div>

## <b>Error handling</b>

이번 포스팅에서는 req.body로부터 넘겨받은 데이터를 MongoDB에 저장할때 발생할 수 있는 에러 케이스에 대해 `에러 처리를 하기 위한 테스트 코드 작성에 대한 내용`을 작성해보려고 한다.

현재 테스트에 있어서 프론트단이 별도로 개발되어있지 않기 때문에 Postman을 이용해서 임의로 만든 API를 테스트 할 것이다. Postman을 사용하면 API 개발의 생산성을 높여 줄 수 있으며, Request를 임의로 전달해서 테스트를 할 수 있다.

<div align="center">
  <img src="/images/post_images/210510_postman.png" alt="Postman"/>
</div>

Postman의 사용은 위의 캡처와 같이 우선 `테스트할 Request의 method를 선택`을 하고, `Endpoints`를 작성해준다. 요청시에 Body에 별도로 데이터를 담아서 전달해야되는 경우에는 Body 옵션을 선택해서 전송하고자하는 데이터의 타입을 지정해서 데이터를 작성해주면 된다.

모든 설정이 끝난뒤에 `Send`버튼을 클릭해주면, 앞서 endpoint에 작성해준 URL과 mapping되는 router의 callback 함수(controller)가 앞서 Postman에서 설정한 내용을 기반으로 실행이 된다.
(Body에 별도의 데이터를 담아서 전달한 경우에는 callback 함수(controller)의 req.body를 통해 전달이 된다.)

```javascript
app.use('/api/products', productRoutes);

router.post('/', productController.createProduct);

// 비동기 요청에서 발생되는 에러에 대한 예외처리
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

  <!-- more -->

동기함수의 경우에는 Request(요청)에 대한 Response(응답)가 와야 그 다음 Request를 받아서 처리할 수 있다. 비동기 함수의 경우에는 Request에 대한 Response와 상관없이 다음 요청을 연달아 받고 받은 비동기 요청의 순서에 맞게 순차적으로 Response를 받아 처리하게 된다.

<div align="center">
  <img src="/images/post_images/210510_sync_async.png" alt="Postman"/>
</div>

따라서 별도로 비동기 요청(Async Request)에 대한 결과를 출력할때에는 `await-async` 또는 `.then()`을 통해서 비동기 요청에 대한 결과 값을 출력해야 한다.

실제 단위 테스트 코드에서도 비동기 처리 코드에 대한 테스트 코드의 경우에 `await-async`로 비동기 처리를 해줘야 한다.

## <b>임의로 에러 발생시키기</b>

Postman에서 Request의 조건을 만족하지 않는 데이터를 body를 통해 전달하게 되면, 처리가 종료되지 않고 hang에 걸리게 된다.
그 이유는 API 부분에서 별도로 에러를 처리하는 부분을 작성해주지 않았기 때문이다.

```javascript
test('should handle errors', async () => {
  const errorMessage = { message: 'description property missing' };
  const rejectedPromise = Promise.reject(errorMessage);
  productModel.create.mockReturnValue(rejectedPromise);
  await productController.createProduct(req, res, next);
  expect(next).toBeCalledWith(errorMessage);
});
```

위의 코드에서 작성한 테스트 코드의 전제 조건은 MongoDB에서 처리하는 부분은 문제가 없다는 것을 가정으로 하기 때문에 MongoDB에서 처리하는 에러 메시지 부분은 Mock 함수를 이용해서 작성을 해준다.

비동기 요청(Async Request)에 대해서 성공을 하는 경우에는 Promise.resolve(value)를 값으로 반환하고, 에러인 경우에는 Promise.reject(reason)이 반환된다. resolve 메소드의 인자 값은 then 메소드를 통해 처리가 가능하다.

따라서 위의 테스트 코드의 경우에도 임의로 만들어준 에러 메시지 객체를 `Promise.reject()로 감싸서 create 함수의 반환값으로 임의 지정`하고 있다.

동기요청에 대한 에러의 경우에는 Express가 알아서 처리를 해주지만, 비동기 요청에 대한 에러는 Express에서 별도의 처리를 해주지 않기 때문에 아래와 같이 next 인자를 통해서 비동기 에러 부분을 넘겨줘야 한다.

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
