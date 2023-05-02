---
title: 210208 Jest의 다양한 matcher
date: 2021-02-08 21:57:42
tags:
  - Jest
  - Jest-matcher
  - JavaScript-unit-test
categories:
  - JavaScript-Test
---

![](/images/post_images/jest_logo.png)

<br/>

# **Jest Matcher**

이전에 사용했던 toBe() matcher는 숫자나 문자와 같은 기본 타입(Primitive Type)의 데이터를 비교할때 사용했다. 그럼 그 외에 다른 matcher에는 어떤 것들이 있는지 알아보고 실습을 해보도록 하겠다.

### **Jest의 다양한 matcher**

- **toEqual()** : primitive type의 변수나 객체를 비교할때 사용된다.
  객체는 참조변수이기 때문에 값은 같더라도 참조하는 주소가 다르다.
- **toStrictEqual()** : 객체를 좀 더 엄격하게 검사할때 사용된다.

  ```javascript
  // makeUser()를 toEqual()로 검사를 하게 되면 passed
  // toStrictEqual()를 toEqual()로 검사를 하게 되면 failed
  const fn = {
    add: (a, b) => a + b,
    makeUser: (name, age) => ({ name, age, gender: undefined }),
    throwErr: () => {
      throw new Error('xx');
    }
  };
  ```

    <!-- more -->

<br/>

- **toBeNull()** : Null이 되는 경우를 검사한다.

  ```javascript
  test('null은 null입니다.', () => {
    expect(null).toBeNull();
  });
  ```

- **toBeUndefined()**
- **toBeDefined()**

<br/>

- **toBeTruthy()**

  ```javascript
  // 빈 문자열이 아닌, helloworld 문자열을 반환하기 때문에 true를 반환한다.
  test('helloworld 문자열은 True입니다.', () => {
    expect(fn.add('hello', 'world')).toBeTruthy();
  });
  ```

- **toBeFalsy()**

  ```javascript
  test('0은 false입니다.', () => {
    expect(fn.add(1, -1)).toBeFalsy();
  });
  // .not.toBeFalsy()
  test('helloworld 문자열은 False가 아닙니다.', () => {
    expect(fn.add('hello', 'world')).not.toBeFalsy();
  });
  ```

<br/>

- **toBeGreaterThan()** : 초과
- **toBeGreaterThanOrEqual()** : 이상
- **toBeLessThan()** : 미만
- **toBeLessThanOrEqual()** : 이하

  ```javascript
  test('입력한 ID는 10자 이하여야 합니다.', () => {
    const id = 'USER_ID';
    expect(id.length).toBeLessThanOrEqual(10);
  });
  ```

<br/>

- **toBeCloseTo()**
  JavaScript에서는 0.1 + 0.2가 0.3이 아니다.
  <>Expected: 0.3</>
  <b>Received: 0.30000000000000004</b>
  컴퓨터는 이진법을 사용하기 때문에 몇 몇 연산은 무한소수로 표현되어 버린다.
  이러한 경우에 `toBeClose()`를 사용하면 된다.
  ```javascript
  test('0.1 더하기 0.2은 0.3이다.', () => {
    expect(fn.add(0.1, 0.2)).toBeCloseTo(0.3);
  });
  ```

<br/>

- **toMatch()**
  정규표현식과 toMatch()를 사용해서 정규식 검사를 할 수 있다.
  정규표현식의 대소문자 구분을 안하기 위해서는 정규표현식 뒤에 i 옵션을 붙여주면 된다.
  ```javascript
  test("Hello World 에 'a'라는 글자가 있는지 확인", () => {
    expect('Hello World').toMatch(/h/i);
  });
  ```

<br/>

- **toContain()**
  배열에서 특정 요소가 있는지 확인한다.
  ```javascript
  test('유저 리스트에 Mike 사용자가 있는지 확인', () => {
    const user = 'Mike';
    const userList = ['Tom', 'Mike', 'Kai'];
    expect(userList).toContain(user);
  });
  ```

<br/>

- **toThrow()**
  함수실행시 예외가 발생하는 경우,
  toThrow()를 사용해서 에러가 발생하는지 확인할 수 있다.
  toThrow()의 인자로 발생한 에러를 비교할 수도 있다.
  ```javascript
  test('이거 에러 나나요?', () => {
    expect(() => fn.throwErr()).toThrow('xx');
  });
  ```
