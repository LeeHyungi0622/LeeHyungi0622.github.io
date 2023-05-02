---
title: 210330 JavaScript와 친해지기 - 이벤트 위임(Event delegation)에 대한 이야기
date: 2021-03-30 14:25:00
tags:
  - Self-Development
  - basic-term
  - TIL
categories:
  - JavaScript
---

![](/images/post_images/210330_event_delegation_img.jpeg)

이번 포스팅에서는 앞서 포스팅한 이벤트 전파(Event propagation), 캡쳐링(Capturing)과 버블링(Bubbling)과 매우 밀접한 관련이 있는 이벤트 위임(Event delegation)에 대해서 정리를 해보려고 한다.

## **이벤트 위임(Event delegation)?**

이벤트 위임은 Vanilla JS로 웹 앱을 구현할때 자주 사용하게 되는 코딩 패턴이다.
이벤트 위임은 간단하게 말하자면 `'하위요소에 각각 이벤트를 붙이지 않고 상위 요소에서 하위 요소의 이벤트들을 제어하는 방식`이라고 정의할 수 있다.

아래에 간단한 TO DO LIST 예시 코드를 작성해보았다.

```html
<h1>TO DO LIST</h1>
<ul class="itemList">
  <li>
    <input type="checkbox" id="item1" />
    <label for="item1">Learn about event delegation</label>
  </li>
  <li>
    <input type="checkbox" id="item2" />
    <label for="item2">Event delegation pattern</label>
  </li>
</ul>
```

```javascript
const inputs = document.querySelectorAll('input');
inputs.forEach((input) => {
  input.addEventListener('click', (event) => {
    console.log('clicked');
  });
});
```

만약에 아래와 같이 새로운 리스트 아이템을 추가해준다면, 이전의 input태그는 정상적으로 이벤트가 동작하지만 새롭게 추가된 input태그는 이벤트가 적용되지 않은 것을 볼 수 있다.

이벤트를 적용할 당시에 단 두개의 input 태그만 존재했기 때문이다.

<!-- more -->

```javascript
const todoList = document.querySelector('.todosList');
const li = document.createElement('li');
const input = document.createElement('input');
const label = document.createElement('label');
const labelText = document.createTextNode('new todo item');

input.setAttribute('type', 'checkbox');
input.setAttribute('id', 'item3');
iabel.setAttribute('for', 'item3');
label.appendChild(labelText);
li.appendChild(input);
li.appendChild(label);
todoList.appendChild(li);
```

한 두개의 새로 추가될 li 요소에 대한 이벤트 추가는 괜찮지만, 만약에 추가해야 될 리스트 아이템이 엄청 많다면 이는 엄청 번거로운일이 아닐 수 없다.
이러한 번거로운 상황을 해결해 줄 해결책이 바로 `이벤트 위임(Event delegation)코딩 패턴`이다.

아래의 예시코드는 각 li 태그의 삭제 버튼에 대한 이벤트를 일일이 주지 않고, li태그의 부모 요소인 ul태그에 onclick 이벤트를 주고 클릭된 요소가 삭제 버튼(.todos-list > .todo-item > .remove-todo)인 경우에만 클릭된 자식요소의 부모노드로부터 id값을 취득해서 삭제하는 함수의 인수로 넣어 함수를 호출하는 방식으로 작성하였다.

이와같이 `상위 요소에 이벤트를 달아놓고 하위에서 발생한 클릭 이벤트를 감지하는 것을 이벤트 버블링(Event bubbling)`이라고 한다.

이와 같은 기본적인 브라우저의 이벤트 감지 방식은 상식으로 알고 있어야 한다.

```javascript
// Remove button event function
const deleteTodoItem = (id) => {
  todos = todos.filter((todo) => todo.id !== +id);
  render();
};

// Remove button event 처리 (Event delegation - Event bubbling)
todosList.onclick = (event) => {
  if (!event.target.matches('.todos-list > .todo-item > .remove-todo')) return;
  const deleteTodoId = event.target.parentNode.id;
  deleteTodoItem(deleteTodoId);
};
```
