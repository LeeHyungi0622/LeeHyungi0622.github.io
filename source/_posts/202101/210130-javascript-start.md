---
title: 210130 JavaScript의 탄생과 발전
date: 2021-01-30 20:59:42
tags:
  - Self-Development
  - JavaScript

categories:
  - JavaScript
---

![](/images/post_images/javascript_logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 JavaScript의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

# **JavaScript의 탄생과 발전**

![](/images/post_images/210130_javascript_img1.png)

JavaScript는 1995년에 만들어졌고, Netscape 브라우저에서 당시에 다른 브라우저와 차별화를 두기 위한 기술로써 사용되었었다.
현대에 들어서는 장바구니 담기, 좋아요 클릭, instagram - javascript , 가상현실, VR 게임/앱, 드론 컨트롤, 로봇 컨트롤 등 다양한 분야에서 Javascript가 사용이 되고 있다.

![](/images/post_images/210130_javascript_img2.png)

  <!-- more -->

JavaScript는 과거에 웹 브라우저에 종속되었던 특성에서 웹 브라우저와 독립적으로 사용이 가능해짐에 따라 React, NodeJS, ExpressJS 등 front-end와 back-end에서 모두 JavaScript 사용이 가능하게 되었다.

# **JavaScript를 이용해서 할 수 있는 브라우저상의 기본적인 작업들**

> 1.  페이지에 있는 HTML 요소들을 변경할 수 있다.
> 2.  페이지에 있는 HTML의 속성들을 변경할 수 있다.
> 3.  페이지에 있는 CSS 스타일들을 변경할 수 있다.
> 4.  기존의 HTML 요소나 속성을 제거할 수 있다.
> 5.  새로운 HTML 요소나 속성들을 추가할 수 있다.
> 6.  자바스크립트는 페이지에 있는 기존의 HTML 이벤트와 상호작용할 수 있다.
> 7.  자바스크립트는 페이지에서 새로운 HTML 이벤트들을 생성할 수 있다.

위의 브라우저의 조작은 DOM(Document Object Model)을 활용해서 가능하다.

![](/images/post_images/210130_javascript_img3.png)

### **HTML 요소에 접근하기**

```javascript
document.getElementsByTagName("h1");
document.getElementByClassName("second");
document.getElementById("first");

document.querySelector("h1") : selector가 찾은 태그의 첫번째 요소를 찾는다.
document.querySelectorAll("li") : selector가 찾은 태그의 모든 요소를 찾는다.

getAttribute : 속성값 찾기
ex) document.querySelector("li").getAttribute("random");

setAttribute : 속성값 셋팅
ex) document.querySelector("li").setAttribute("random", "1000");
```

### **스타일 변경하기**

```javascript
//style.{property}
document.querySelector('h1').style.background = 'yellow';
```

**일반적으로 HTML은 텍스트에서 분리되고, CSS는 스타일에 집중된다. 그리고 Javascript는 액션에 집중한다.**

### **JavaScript로 action 이벤트 변경하기**

```javascript
document.querySelector('li').classList;

// 클래스이름에 리스트를 추가할 수 있다.
document.querySelector('li').classList.add('coolTitle');

document.querySelector('li').classList.toggle('done');

// innerHTML
document.querySelector('h1').innerHTML = '<strong>!!!!!</strong>';
document.querySelectorAll('li')[1].parentElement.parentElement.children;

// It is important to CACHE selectors in variables
/** 
※ 이렇게 페이지를 새로고침하기 전까지 h1 변수에 h1태그에 대한 querySelector를 지정해준 것을 저장해서 사용하는 것을 CACHE selector라고 한다. 
*/
var h1 = document.querySelector('h1');
```

### **DOM EVENT**

```javascript
var button = document.getElementsByTagName('Button')[0];

button.addEventListener('mouseleave', function () {
  console.log('CLICK!!!!');
});
```

**Event reference**
[Mozila Event Reference!](https://developer.mozilla.org/en-US/docs/Web/Events)

```html
<input id="userinput" type="text" placeholder="enter items" />
<button id="enter">Enter</button>
```

```javascript
var button = document.getElementById('enter');
var input = document.getElementById('userinput');
var ul = document.querySelector('ul');

function inputLength() {
  return input.value.length;
}

function createListElement() {
  var li = document.createElement('li');
  li.appendChild(document.createTextNode(input.value));
  ul.appendChild(li);
  input.value = '';
}

function addListAfterClick() {
  console.log('click is working');
  if (inputLength() > 0) {
    createListElement();
  }
}

function addListAfterKeypress(event) {
  console.log(event.which);
  //Enter key event(which):13
  if (inputLength() > 0 && event.keyCode === 13) {
    createListElement();
  }
}

button.addEventListener('click', addListAfterClick);

input.addEventListener('keypress', addListAfterKeypress);
```

### **Callback function**

앞서 코드를 refactoring할때 버튼 클릭 리스너와 input 태그에 대한 이벤트 리스너의 이벤트 메소드에서 argument가 있는 메소드 형태가 아닌 변수 형태로 작성을 해주었다

> 1.  button.addEventListener("click", addListAfterClick);
> 2.  input.addEventListener("keypress", addListAfterKeypress);

이러한 작성을 콜백함수라고 한다. 자바스크립트에서는 클릭 이벤트가 호출될때, 함수를 실행하지 않고, 함수에 대한 참조를 전달한다.

리스트의 항목을 클릭했을때, class="done"을 toggle로써 추가/제거될 수 있도록 작성, 각각의 리스트 옆에 삭제할 수 있는 delete 버튼을 삽입

**solution1)**

```javascript
// Toggle event
function toggleListItemClass() {
  li.classList.toggle('done');
}

//1. toggles the .done class on and off
li.addEventListener('click', toggleListItemClass);

//2. Add delete button beside of the list item.
var li = document.querySelectorAll('li');

for (var i = 0; i < li.length; i++) {
  li[i].innerHTML +=
    "<button id='delete_btn" +
    i +
    "' onclick='deleteListItem(" +
    i +
    ")'>delete</button>";
}

function deleteListItem(i) {
  ul.removeChild(ul.childNodes[i]);
}
```

**solution2)**

```html
<ul id="foodList">
  <li class="bold red" random="23">
    Notebook<button class="delete">Delete</button>
  </li>
  <li>Jello<button class="delete">Delete</button></li>
  <li>Spinach<button class="delete">Delete</button></li>
  <li>Rice<button class="delete">Delete</button></li>
  <li>Birthday Cake<button class="delete">Delete</button></li>
  <li>Candles<button class="delete">Delete</button></li>
</ul>
```

```javascript
var button = document.getElementById('enter');
var input = document.getElementById('userinput');
var ul = document.querySelector('ul');
var li = document.querySelector('li');
var deletebtn = document.getElementsByClassName('delete');

// class="delete"인 버튼에 click 이벤트 set up
for (var i = 0; i < deletebtn.length; i++) {
  deletebtn[i].addEventListener('click', removeParent, false);
}

function inputLength() {
  return input.value.length;
}

function createListElement() {
  var li = document.createElement('li');
  var btn = document.createElement('button');
  btn.innerHTML = 'Delete';
  btn.onclick = removeParent;
  li.appendChild(document.createTextNode(input.value));
  li.appendChild(btn);
  ul.appendChild(li);
  input.value = '';
}

function addListAfterClick() {
  if (inputLength() > 0) {
    createListElement();
  }
}

function addListAfterKeypress(event) {
  if (inputLength() > 0 && event.keyCode === 13) {
    createListElement();
  }
}

button.addEventListener('click', addListAfterClick);

input.addEventListener('keypress', addListAfterKeypress);

// Toggle event
function toggleListItemClass() {
  li.classList.toggle('done');
}

//toggles the .done class on and off
li.addEventListener('click', toggleListItemClass);

//delete parent function
function removeParent(evt) {
  evt.target.removeEventListener('click', removeParent, false);
  evt.target.parentNode.remove();
}
```

- ## **JQuery의 등장**

  DOM을 이용해서 화면의 구성요소를 조작할때에는 각 브라우저에서 호환이 되는지 확인을 해야했으며, querySelector를 지원하는 브라우저도 몇 안되었다. 이러한 호환문제와 DOM 객체를 이용한 웹 페이지 요소 조작을 간편하게 할 수 있도록 등장한 것이 바로 JQuery이다. 하지만 ES6 이후부터는 JavaScript가 많이 발전되어 JQuery를 사용하지 않는 추세이다.
  (JQuery는 간단하게 JQuery CDN을 추가하고, $sign을 이용해서 간단하게 화면 구성요소들을 호출해서 사용할 수 있다.)

  ```javascript
  ex. $(document).ready(function(){
    $("p").click(function(){
         $(this).hide();
    });
  });
  (= p.addEventListener("click", function(){ });)
  ```

  > JQuery는 매우 Imperative한 언어이다. 따라서 프로그램에 정확히 무엇을 해야하는지 하나 하나씩 알려야 한다. 하지만 프로그램이 커지면 다른 조치는 또 다른 조치로 종속되고, 오류와 버그가 많아지며 해결하기 위한 추적이 매우 힘들어진다.<br/><ins>Imperative : 반드시 해야하는, 긴요한, 명령적인</ins>

Javascript를 대중화시키고 편리하게 사용하는데에 JQuery는 많은 것을 기여를 했지만, 점점 시스템은 커져가고 있고, 단순히 모든 것들을 절차지향 언어와 같이 하나 하나 알려줘야하는 프로그램이라면 나중에 운용보수면에서 보았을때 힘들 것이라고 생각한다.

- ## **개발자의 입장에서 본 DOM 조작**

  DOM 조작의 양을 최소화하는 것이 좋다. 이 것은 곧 웹 페이지의 퍼포먼스로 직결되는 문제이며, 페이지에서의 일부변경이 전체 페이지의 렌더링되어버리면 매우 효율이 떨어지는 어플리케이션이 될 것이다.
  웹 응용 프로그램을 구축할 때 매우 중요한 개념이며 똑똑하고 최상의 방법은 필요한 부분만 re-painting하는 것이다.

- ## **JavaScript Engine**
  각 브라우저는 JavaScript engine을 가지고 있으며, 아래와 같이 각기 다른 engine을 사용하고 있다.

> (1) Chrome - 'V8'
> (2) Edge - 'Chalker Core'
> (3) Safari - 'Nitro'
> (4) Firefox - 'SpiderMonkey'

JavaScript engine은 웹 브라우저상에 동작하는 자바스크립트 파일을 읽는 역할을 한다.
