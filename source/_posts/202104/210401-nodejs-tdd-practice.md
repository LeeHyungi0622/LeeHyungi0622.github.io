---
title: 210401 Node.js TDD Practice 두 번째 이야기 - jest.fn() and node-mocks-http
date: 2021-04-01 15:50:00
tags:
  - NodeJS
  - TIL
categories:
  - NodeJS
---

## **jest.fn() Mock function**

<div align="center">
  <img src="/images/post_images/210401_jest_mock_function_img.jpeg" alt="expressjs mongodb image">
</div>

## **jest.fn()**

이번 포스팅에서는 jest에서 제공해주는 Mock 함수(`jest.fn()`)에 대해서 정리를 해보려고 한다.
mock은 한글로 직역하면 '모의'라는 의미를 가지며 가짜, 흉내내는 이라는 뜻을 가지고 있다.

Mock 함수는 단위 테스트를 작성할 때에 테스트하려는 코드가 의존하고 있는 부분을 가짜로 대체하는 일을 해준다.

## **여기서 의존하는 부분을 가짜로 대체하는 이유는 무엇일까?**

의존하는 부분을 가짜로 대체하는 이유는 우선 `첫 번째, 의존적인 부분을 개별적으로 구현하기 까다로운 경우`가 있다. 그리고 `두 번째, 의존적인 부분의 상태에 따라 테스트 결과가 다르게 나온다면 안되기 때문`에 의존적인 부분에 의해 테스트 결과가 영향을 받지 않도록 해야한다.

## **데이터베이스의 데이터 조작을 테스트**

데이터베이스의 데이터를 추가하는 부분을 테스트한다고 가정했을때, 실제 데이터베이스를 가지고 테스트를 한다면 `Network, I/O task, Transaction creation, Query transmission etc...`의 다양한 작업과 데이터 베이스에서 `변경된 데이터를 직접 원상복귀하거나 Transaction rollback 해야하는 경우`에 데이터 베이스에 데이터를 저장하는 부분 테스트에 고려해야 되는 작업이 많아 비효율적인 방법이 될 것이다.
혹여나 테스트 도중에 실 데이터베이스가 죽어버린다면 테스트 결과에 영향을 미치게 될 것은 뻔하디 뻔한 상황일 것이다.

그래서 이러한 의존적인 부분을 jest.fn()을 이용해서 가짜 함수를 생성함으로써 해결할 수 있다.

이 jest.fn() 함수는 매우 유용하게 사용될 수 있는데, `생성한 가짜 함수에 어떤 일들이 발생`했는지, `다른 코드들에 의해서 어떻게 호출`이 되는지 기억해주는 역할을 해주기 때문이다.

함수가 내부적으로 어떻게 사용되는지 `검증`할 수 있다.

`구체적인 mock 함수 사용은 아래 첨부한 노트 1~2을 참고하도록 하자.`

  <!-- more -->

데이터베이스에 request객체의 body 속성으로 받은 실제 데이터를 추가해주는 부분을 테스트하기 위해서는 reqest와 response 객체가 필요한데 이 경우에는 `node-mocks-http module`을 사용해서 아래와 같이 request, response 객체를 초기화시켜줄 수 있다.

`해당 부분에 대한 내용은 아래 첨부한 세 번째 노트를 참고하도록 하자`

`/test/unit/products.test.js`

```javascript
const httpMocks = require('node-mocks-http');

let req = httpMocks.createRequest();
let res = httpMocks.createResponse();
let next = null;
```

`/test/data/new-product.json`

```json
{
  "name": "Gloves",
  "description": "good to wear",
  "price": 15
}
```

`/test/unit/products.test.js`

```javascript
const { describe, test, expect } = require('@jest/globals');
const productController = require('../../controllers/products.controller');
// Create, Update, Delete를 하기 위해 필요한 model
const productModel = require('../../models/Product');
// 실제 데이터를 추가할때 사용할 req, res 객체 생성을 위한 node-mocks-http
const httpMocks = require('node-mocks-http');
// req.body에 추가해줄 newProduct json 데이터
const newProduct = require('../data/new-product.json');

// 단위 테스트이기 때문에 model에 직접적으로 영향을 받으면 안된다.(mock 함수 사용)
// mock 함수를 사용해서 호출되는 함수를 정의해주면,
// 어떤 것에 의해서 호출되는지, 어떤 것과 함께 호출이 되는지 알 수 있다.
// 아래에서 productController.createProduct()가 호출되었을때, productModel.create가
// 호출이 되었는지 안되었는지 spy해서 추적할 수 있다.
productModel.create = jest.fn();

describe('Product Controller Create', () => {
  test('should have a createProduct function', () => {
    expect(typeof productController.createProduct).toBe('function');
  });
  test('Should call ProductModel.create', () => {
    // product data를 데이터베이스에 추가할때 필요한 product객체를
    // 넘겨받을 req 객체
    let req = httpMocks.createRequest();
    let res = httpMocks.createResponse();
    let next = null;
    req.body = newProduct;
    // createProduct() 함수가 호출이 될때,
    // 위에서 httpMocks로 생성해준 req, res, next를 인수로 넘겨서
    // createProduct함수를 호출해준다.
    productController.createProduct(req, res, next);
    // productModel의 create 메소드가 같이 호출되는지 확인
    expect(productModel.create).toBeCalledWith(newProduct);
  });
});
```

**실습 Repository**

→ [https://github.com/LeeHyungi0622/TDD-Practice-NodeJS](https://github.com/LeeHyungi0622/TDD-Practice-NodeJS)

나중에 공부한 내용을 상기시키기 위해서 공부하면서 필기했던 노트를 첨부한다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210401_create_tdd_note.png" alt="NodeJS TDD Practice Note1">
    </td>
    <td>
      <img src="/images/post_images/210401_jest_mock_function_note.png" alt="NodeJS TDD Practice Note2">
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210401_node_mocks_http_note.png" alt="NodeJS TDD Practice Note3">
    </td>
    <td></td>
  </tr>
</table>
