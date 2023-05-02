---
title: 210402 Node.js TDD Practice 세 번째 이야기
date: 2021-04-02 13:40:00
tags:
  - NodeJS
  - Jest
  - Unit-Test
  - TIL
categories:
  - NodeJS
---

## **beforeEach, response status code and value, mockReturnValue(), \_isEndCalled(), \_getJSONData()**

<div align="center">
  <img src="/images/post_images/210402_nodejs_mock_http_requests.jpg" alt="Nodejs Mocking Http Request">
</div>

## **beforeEach() 활용해서 공통 코드 처리하기**

여러개의 테스트 코드를 작성하면서 공통된 코드가 있다면 beforeEach 안에 작성을 해서 불필요한 코드의 반복을 줄여줄 수 있다.
`beforeEach의 위치는 describe의 내부와 외부 모두 가능`하다. describe 단위로 공통된 코드는 describe 내부에 beforeEach를 작성해서 공통된 코드를 처리해주고, 모든 describe에 공통적으로 참조해야하는 공통 코드가 있다면 이는 describe 외부에 작성을 해서 작성한 모든 테스트 케이스에서 공통 코드를 참조할 수 있도록 해야한다.
(`아래 첨부한 첫 번째 노트를 참고`)

## **response 객체를 통해 상태값 전달하기**

request 객체의 body 속성으로부터 저장할 데이터에 대한 정보를 받아 데이터베이스에 저장을 했다면 이제 제대로 저장이 되었는지, 제대로 저장이 되었다면 `상태값에 대한 정보를 보내줘야 한다.`
상태값은 `res.status(201)`과 같이 response 객체의 status로 상태코드를 인수로 넘겨준다.

테스트 코드에서는 mock response 객체의 statusCode 속성을 참고해서 전달된 상태값을 확인할 수 있다.
`expect(res.statusCode).toBe(201)`

## **response 객체를 통해 결과값 전달하기**

앞서 response 객체의 status로 상태코드를 인수로 넘겨서 상태값을 전달하였다.
그렇다면 추가적으로 결과값을 전달해야될 때에는 어떻게 해야할까?
(`아래 첨부한 세 번째 노트 필기 참고`)

  <!-- more -->

테스트 코드에서는 mock response 객체의 `_isEndCalled()`메서드를 사용(node-mocks-http 제공)해서 `res.status(201).send()`에서 send()나 json()과 같이 추가적인 결과값이 전달되고 있는지 확인할 수 있다.

테스트 코드에서 전달된 결과값은 mock 함수의 `mockReturnValue`를 사용해서 반환되는 값을 임의로 지정해줄 수 있다.

반환값을 지정하고, mock request, response, next 객체를 create함수의 인수로 넣어 호출한 뒤에 mock response 객체의 `_getJSONData()`를 통해 전달한 JSON 타입의 결과값을 참조할 수 있다.

**ex)** `expect(res._getJSONData()).toStrictEqual([JSONData])`

**실습 Repository**

→ [https://github.com/LeeHyungi0622/TDD-Practice-NodeJS](https://github.com/LeeHyungi0622/TDD-Practice-NodeJS)

나중에 공부한 내용을 상기시키기 위해서 공부하면서 필기했던 노트를 첨부한다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210402_nodejs_tdd_beforeeach_note1.png" alt="NodeJS TDD beforeEach Note">
    </td>
    <td>
      <img src="/images/post_images/210402_nodejs_tdd_response_test_note2.png" alt="NodeJS TDD Response Test Note">
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210402_nodejs_tdd_response_result_callback_test_note3.png" alt="NodeJS TDD Response result callback Test Note">
    </td>
    <td></td>
  </tr>
</table>
