---
title: 210329 JavaScript와 친해지기 - 이벤트 전파(Event propagation)이벤트 버블링(Bubbling)과 캡쳐링(Capturing)에 대한 이야기
date: 2021-03-29 13:12:00
tags:
  - Self-Development
  - basic-term
  - TIL
categories:
  - JavaScript
---

![](/images/post_images/210329_event_bubbling_and_capturing.png)

이번 포스팅에서는 이벤트 전파(Event propagation), 캡쳐링(Capturing)과 버블링(Bubbling)에 대해서 정리를 해보려고 한다.

## **이벤트 전파(Event propagation)?**

HTML태그는 중첩된 구조로 되어있다. 따라서 이벤트 또한 중첩적으로 장착될 수 있기 때문에 이러한 경우에 `어떠한 순서로 이벤트 핸들러들을 호출(실행)할 것인가에 대한 순서에 대한 규약이 바로 이벤트 전파`라고 할 수 있다.

<b>만약 상위 HTML 요소에 이벤트를 주고 그 하위 요소들에도 이벤트를 주었을때</b>

상위 HTML 요소에 적용한 이벤트 핸들러가 가장 먼저 호출이 되고 점점 내부로 들어오면서 가장 하위 요소(target element)의 이벤트 핸들러가 호출되는 형태가 된다면 이를 <ins>`이벤트 캡처링(Event Capturing)`</ins>이라고 한다.

그리고 가장 하위(target element) 요소에 적용한 이벤트 핸들러가 가장 먼저 호출되고, 그 상위 element에서 그 상위 element로, 그리고 상위 element로 타고 올라가면서 이벤트가 호출되는 것을 <ins>`이벤트 버블링(Event Bubbling)`</ins>이라고 한다.

## **캡처링(Capturing)과 버블링(Bubbling)**

앞서 이벤트 전파의 정의에서 캡처링과 버블링의 방향성에 대한 정의에 대해서 간단하게 살펴보았다.
<ins><b>웹 브라우저의 이벤트 모델은 캡처링과 버블링, 모두를 지원한다.</b></ins>
`(하지만 이벤트 캡처링은 예전 IE버전에서는 지원되지 않고 있다)`

캡처링과 버블링은 서로 비슷하지만 정반대의 방향성을 가지며, 버블링이 이벤트 전파에서 실세라고 할 수 있다.

캡처링을 사용하는 것은 매우 드문 편이지만 이벤트가 전달되는 흐름에 있어 유용한 개념이기 때문에 알아두면 유용하다.

<!-- more -->

### **그럼 캡처링과 버블링 방식은 어떻게 결정될까?**

이 캡처링과 버블링은 이벤트 적용시에 `addEventListener의 세번째 인자값`을 다르게 함으로써 방식을 다르게 적용할 수 있다.
`addEventListner의 세번째 인자 = Use capturing을 의미`한다. 두 번째 인자로 넣은 event handler가 capturing의 방식으로 동작하길 원한다면 `true`로 설정해주고, <ins><b>만약에 bubbling의 방식으로 동작하길 원한다면 `false`를 주거나 아무 인자값도 넣어주지 않는다면(`default`) bubbling의 방식<b></ins>으로 적용한 event handler가 동작하게 된다.

버블링은 모든 브라우저에서 지원이 되지만, 캡처링은 과거의 브라우저에서 지원이 되지 않기 때문에 왠만하면 사용을 하지 않는 것이 권장된다.

### **만약에 이벤트의 전파를 특정 지점에서 막고자 한다면 어떻게 해야될까?**

만약 이벤트가 전파되는 과정에서 특정 HTML element의 내/외부로는 이벤트 전파가 일어나지 않도록 하려면 어떻게 해야될까? 바로 이벤트 전파의 마지막 요소의 이벤트 핸들러에 `event.stopPropagation();`를 넣어주는 것이다.
이렇게 되면 해당 HTML element의 이후로는 더 이상의 이벤트 전파가 발생하지 않는다.

<ins>**이벤트 전파(Event propagation)예시**</ins>
`index.html`

```html
...
<body>
  <fieldset>
    <legend>event propagation</legend>
    <input type="propagation" id="target" value="target" />
  </fieldset>
</body>
...
```

`app.js`

```javascript
function handler(event) {
  // event.eventPhase :
  // 1 = capturing(Starting from window, document and the root element, the event dives down through ancestors of the target element),
  // 2 = target element(The event gets triggered on the element on which the user has clicked),
  // 3 = bubbling(The event bubbles up through ancestors of the target element until the root element, document, and window.)
  var phases = ['capturing', 'target', 'bubbling'];
  console.log(
    event.target.nodeName,
    this.nodeName,
    phase[event.eventPhase - 1]
  );
}

function stopHandler(event) {
  // event.eventPhase :
  // 1 = capturing,
  // 2 = target element,
  // 3 = bubbling
  var phases = ['capturing', 'target', 'bubbling'];
  console.log(
    event.target.nodeName,
    this.nodeName,
    phase[event.eventPhase - 1]
  );
  event.stopPropagation();
}

document.getElementById('target').addEventListener('click', handler, false);
document
  .querySelector('fieldset')
  .addEventListener('click', stopHandler, false);
document.querySelector('body').addEventListener('click', handler, false);
```
