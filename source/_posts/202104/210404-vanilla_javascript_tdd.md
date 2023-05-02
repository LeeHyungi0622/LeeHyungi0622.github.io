---
title: 210404 Vanilla JavaScript TDD Practice
date: 2021-04-04 16:40:42
tags:
  - JavaScript-module-pattern
  - JavaScript-unit-test
  - Jasmine
categories:
  - JavaScript-Test
---

<div align="center">
  <img src="/images/post_images/210404_vanilla_javascript_img.jpeg" alt="Vanilla JavaScript TDD">
</div>

<br/>

`210405 Update`

이번 포스팅에서는 TDD방식으로 Vanilla JavaScript를 사용해서 개발을 하는 방법에 대해서 실습한 내용을 기반으로 블로그 포스팅을 해보려고 한다.

처음에는 이 TDD방식이 익숙하지 않아서 느릴지 모르지만 이런 과정이 견고한 Logic을 만들게 되고, 결국에는 개발속도가 빨라지는 결과를 낳게 된다고 한다.
여기서 포인트는 익숙해지는 것! 개발자 커뮤니티에서 어떤 개발자 분이 말씀하셨는데 `"개발의 가장 좋은 실력향상 방법은 반복"`이라고 하셨다. 누군가로부터 무언가 새로운 것을 배웠을 당시에 이해를 한 것처럼 착각하고 마치 내 것이 된거마냥 생각하는 사람들이 많은 것 같다. 이러한 개인적인 반복학습 없이 한 두 번 더 본다고 절대 내 것이 되었다고 볼 수 없다.

내가 이렇게 블로그에 배웠던 내용을 정리하고, 필기했던 노트를 첨부하는 이유는 `반복학습`을 위해서다.
이 TDD 방식 또한 처음에 적용할때 시간이 오래 걸린다고 생각하지 말고 스스로 생각하고 반복을 하면서 내 것으로 만들도록 노력을 해야겠다.

## 본론

`개선전 코드`
아래의 코드에는 문제가 많다. Increase button에는 여러 관심사들이 혼재되어 있다. 단순히 화면에 버튼을 출력하지 않고, click event가 binding되어있다.
또한 JavaScript 코드를 보면 count라는 변수를 전역 스코프에 작성을 하였다. 이는 변수이름이 충돌할 위험을 증가시킨다.
그리고 카운트 수를 출력할 span 태그의 id를 hard coded한 방식으로 작성을 해서 DOM element를 가져오고 있다. 만약에 markup이 변경된다면 변경된 id에 맞춰서 작성한 id를 JavaScript 코드에서 수정해줘야 한다.

아래의 `간단해보이는 코드에는 위와같은 복합적인 문제들이 혼재`되어있다.

  <!-- more -->

```html
<button onclick="counter++; countDisplay()">Increase</button>
<span id="counter-display">0</span>
<script>
  var count = 0;
  function countDisplay() {
    var el = document.getElementById('counter-display');
    el.innerHTML = count;
  }
</script>
```

`개선후 코드`

그럼 아래의 개선된 코드를 살펴보자.

`index.html`

```html
<button id="btn-increase">Increase</button>
<script src="ClickCounter.js"></script>
<script src="ClickCounterView.js"></script>
<script>
  (() => {
    const clickCounter = App.ClickCounter();
    const updateEl = document.querySelector('#counter-display');
    const triggerEl = document.querySelector('#btn-increase');
    // 두 객체에 대해 역할 위임을 하였다.
    const view = App.ClickCounterView(clickCounter, { updateEl, triggerEl });
    view.updateView();
  })();
</script>
```

`ClickCounter.js`

기존에 전역변수로 선언해서 변수이름이 충돌할 위험을 높였던 count 변수를 ClickCounter 함수내에서 value함수로 선언하였다.
이렇게 하면 value 변수는 ClickCounter라는 함수 스코프내에서만 유효한 변수이기 때문에 이름이 충돌할 위험은 적어진다.

```javascript
var App = App || {};
App.ClickCounter = () => {
  let value = 0;
  return {
    getValue() {
      return value;
    },
    increase() {
      value++;
    }
  };
};
```

그리고 기존에 hard coded한 방식으로 span 태그의 id를 작성한 방식을 options라는 객체를 parameter로 넘겨받아서 접근하고 있기 때문에 만약에 markup에서 id에 대한 변경이 있다고 하더라도 ClickCounterView.js 파일의 JavaScript 코드를 변경하지 않아도 된다.
이는 화면과 JavaScript 코드가 분리되었기 때문에 가능하다. (`유연한 코드`)

아래의 view 객체 안의 updateView(), increaseAndUpdateView(), event binding 부분을 보면, 각 각 `단일 책임의 원칙에 따라 기능이 분리되어 작성`되어있음을 알 수 있다.
이렇게 코드를 작성하게 되면 `유지보수가 간결`해진다.
`ClickCounterView.js`

```javascript
var App = App || {};

App.ClickCounterView = (clickCounter, options) => {
  if (!clickCounter)
    throw new Error(App.ClickCounterView.message.noClickCounter);
  if (!options.updateEl)
    throw new Error(App.ClickCounterView.message.noUpdateEl);
  if (!options.triggerEl)
    throw new Error(App.ClickCounterView.message.noTriggerEl);

  const view = {
    updateView() {
      options.updateEl.innerHTML = clickCounter.getValue();
    },
    increaseAndUpdateView() {
      clickCounter.increase();
      this.updateView();
    }
  };

  options.triggerEl.addEventListener('click', () => {
    view.increaseAndUpdateView();
  });
  return view;
};

App.ClickCounterView.message = {
  noClickCounter: 'clickCounter를 주입해야 합니다.',
  noUpdateEl: 'updateEl을 주입해야 합니다.',
  noTriggerEl: 'triggerEl을 주입해야 합니다.'
};
```

**실습 Repository**

→ [https://github.com/LeeHyungi0622/TDD-Practice-VanillaJS](https://github.com/LeeHyungi0622/TDD-Practice-VanillaJS)

나중에 공부한 내용을 상기시키기 위해서 공부하면서 필기했던 노트를 첨부한다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210404_vanilla_javascript_tdd_note1.png" alt="VanillaJS TDD beforeEach Note">
    </td>
    <td>
      <img src="/images/post_images/210404_vanilla_javascript_tdd_note2.png" alt="VanillaJS TDD Response Test Note">
    </td>
  </tr>
  <tr>
    <td>
      <img src="/images/post_images/210405_vanilla_javascript_tdd_note3.png" alt="VanillaJS TDD Practice Note">
    </td>
    <td>
      <img src="/images/post_images/210405_vanilla_javascript_tdd_note4.png" alt="VanillaJS TDD Practice Note">
    </td>
  </tr>
</table>
