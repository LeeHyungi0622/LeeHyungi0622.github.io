---
title: 210223 HTML/CSS - float TIL
date: 2021-02-23 07:14:42
tags:
  - Float
  - HTML
  - CSS
  - TIL
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/210223_float_img.png" alt="Float"/>
</div>

## **float**

이번 포스팅에서는 float의 개념과 기본적인 사용에 대해서 정리해보려고 한다. 이미 과거에 짜여진 화면 레이아웃을 파악하거나 예전 버전의 브라우저와 호환되는 웹 레이아웃을 만들기 위해서는 기본적으로 알고 있어야 되는 내용이다.

Thumbnail 이미지를 보면 알겠지만, float란 단어 자체의 의미처럼 떠있는 상태를 말한다. 물리적인 상태는 이해가 되었으니, 이제 이 float라는 속성이 HTML에서 어떠한 특성을 갖는지 살펴보도록 하겠다.

## **개별 요소에서의 float 적용**

HTML에서 이 floating된 요소는 기존의 속성과 관계없이 block 요소가 된다. 예를들어 흔히 링크를 삽입할때 많이 사용되는 \<a> anchor tag의 경우 원래 inline-element이지만, 속성으로 float을 주게 되면, \<div> 태그와 같이 하나의 block element처럼 변한다. 그리고 자기자신만의 영역을 가지고 있는 inline-block처럼 화면에 rendering된다.

위의 설명을 간단하게 정리하면, `floating element는 inline-block과 같은 효과를 낸다`라고 말할 수 있다.

  <!-- more -->

이제 기본적으로 HTML 내부에 존재하는 개별요소에 float를 적용하게 되면 어떠한 변화가 있는지 살펴보았으니 이제 전체 document의 관점에서 float를 적용한 element의 변화에 대해서 살펴보도록 하겠다.

## **전체 document 관점에서의 float 요소**

Document내에 존재하는 float 속성이 적용된 자식요소는 페이지에 표시된 document의 흐름에서 벗어난 상태로, 레이아웃의 배치를 무너뜨리게 하는 요인이 된다.
이러한 레이아웃 배치를 무너뜨리게 되는 현상을 해결하기 위해서는 float가 적용된 요소의 주변에 있는 요소들은 영향을 받지 않도록 해야한다.

주변 요소들로 영향이 없도록 하기 위해서는 주변 요소의 속성에서 float를 해제해야 하는데 그 방법에는 clear 속성을 적용하는 방법과 적용하지 않는 방법, 두 가지로 크게 분류해서 살펴볼 수 있다.

<br/>

## **float 해제하는 방법**

이제부터 주변 요소로의 float 영향을 제거하기 위한 float 해제의 방법에 대해서 정리해보겠다. 방법은 크게 clear 속성을 사용하지 않은 방법들과 clear 속성을 사용한 방법으로 분류하여 정리를 해보겠다.

### <ins>우선 clear 속성을 사용하지 않고, float 해제방법에 대해서 정리해보도록 하겠다.</ins>

<span><h1><b>첫 번째,</b></h1></span> <ins>우선 `수업시간에 배운 overflow: hidden 속성 지정을 통한 float 해제`에 대해서 알아보도록 하겠다.</ins>
자식요소가 부모요소의 크기보다 클 경우, 부모요소는 자식요소의 넘치는 컨텐츠를 숨기고 보이지 않게 한다. overflow의 속성 값 중에 auto도 있는데, 이 auto 속성값을 주게 되면, 자식요소의 너비가 부모요소보다 큰 경우에 스크롤이 생기게 되기 때문에 추천하지 않는 방법이다.

부모요소가 float 속성을 가진 자식요소의 높이를 인지하지 못한 상황에서 overflow: hidden 속성을 주게되면 부모요소는 자식요소의 overflow된 contents영역을 숨기기 위해 자식요소의 높이값을 자동으로 계산하게 되는데 이를 통해 화면상에서는 부모요소가 자식요소를 감싸안는 형태로 보이게 된다.

**<ins>`float해제에서 overflow: hidden 사용의 단점`</ins>**
<ins><b>이 방법은 동적으로 데이터를 보여주는 경우에는 적합하지 않다.</b></ins> 동적으로 보여지는 데이터가 부모 컨텐츠보다 큰 경우에는 넘치는 데이터 표시부분을 모두 숨겨버리기 때문이다.

<span><h1><b>두 번째,</b></h1></span><ins>가장 널리 사용되는 방법으로 가상 클래스(Pseudo class)를 사용한 float 해제에 대해서 알아보자.</ins>

이 방법은 불 필요한 요소의 추가없이 CSS를 통해 가상 요소를 생성하여 float를 해제한다.
display가 block일때만 clear를 적용시킬 수 있기 때문에 아래와 같이 display를 block으로 선언해준다.

```css
.parent::after {
  content: '';
  display: block;
  clear: both;
}
```

인위적으로 float가 적용된 자식요소의 마지막에 의미없는 형제요소를 만들어서 clear 속성을 넣어 float를 해제시켜 줄 수도 있는데, 불필요한 요소를 추가하게 되므로, 추천되지 않는 방법이다. 이 방법의 단점을 보완한 방법이 가상 클래스를 사용한 방법이다.

이 가상 클래스를 사용하는 방법은 `IE7에서는 지원되지 않기 때문에` 가상 클래스 요소 대신에 부모요소에 `zoom:1;`를 별도로 속성으로 지정해줘야 한다.

<span><h1><b>세 번째,</b></h1></span><ins>부모요소에 inline-block의 특징을 갖게 하는 방법이 있다.</ins>

이 방법에는 크게 두 가지 방법이 있는데, 부모요소에도 자식요소와 똑같이 **float 속성을 지정**해는 방법과 **display 속성에 inline-block 속성값을 지정**해주는 방법이 있다.
부모요소에 float 속성을 주게 되면, 자식요소와 동일하게 float한 상태가 되기 때문에 자식요소의 높이를 인지하게 되지만 부모 요소도 inline-block의 특징을 갖게되어, 부모 요소에 지정한 너비만큼만 너비를 갖게 되기 때문에 레이아웃의 전체적인 구성을 고려해서 지정해줘야 한다.

### <ins>이제 clear 속성을 사용한 float 해제방법에 대해서 정리해보도록 하겠다.</ins>

<span><h1><b>네 번째,</b></h1></span><ins>float를 지정한 자식요소의 부모요소에 clear 속성을 지정함으로써 float 해제하는 방법이 있다.</ins></ins>

이 방법은 clearfix라는 방법으로 불리는데, float 속성이 적용된 자식요소를 감싸는 부모요소에 clear 속성을 지정함으로써 부모요소가 자식요소를 감싸는 형태가 될 수 있도록 한다.
clear 속성에는 `left, right, both, none`등 다양한 속성값이 있으며, <ins><b>내부 자식요소에 float: left를 주었다면, clear:left를, float: right를 주었다면 clear: right를 주면 된다. 그리고 left, right 자식요소 모두 flot해제를 해 줄때에는 both값을 넣어주면 된다.</b></ins>

<span><h1><b>다섯 번째,</b></h1></span><ins>micro clearfix라는 방법이 있다.</ins></ins>

이 방법은 아래와 같이 CSS로 float 속성을 가진 자식요소를 감싸는 부모요소에 지정을 해서 float를 해제하는 방법이다.

```css
.parent {
  /* For IE 6/7 only */
  *zoom: 1;
}
.parent::before,
.parent::after {
  content: '';
  display: table;
}
.parent::after {
  clear: both;
}
```

### 실습코드 (micro clearfix)

<img  src="/images/post_images/210223_float_practice.png" alt=""/>

**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <link rel="stylesheet" href="./style.css" />
    <title>Document</title>
    <div class="above_container">0</div>
    <div class="parent_container">
      <div class="child_container">1</div>
      <div class="child_container">2</div>
      <div class="child_container">3</div>
    </div>
    <div class="below_container">4</div>
  </head>
  <body></body>
</html>
```

**style.css**

```css
.parent_container::before,
.parent_container::after {
  content: '';
  display: table;
  border: 1px solid red;
}

.parent_container::after {
  clear: both;
}

.child_container {
  float: left;
  width: 50px;
  height: 50px;
  background-color: cadetblue;
  border: 1px solid black;
}

.above_container,
.below_container {
  border: 1px solid green;
  width: 200px;
}
```
