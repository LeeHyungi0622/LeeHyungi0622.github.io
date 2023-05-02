---
title: 210226 HTML/CSS Layout practice (w/float)
date: 2021-02-26 09:38:00
tags:
  - Self-Development
  - TIL
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/html_css_logo.png" alt="HTML/CSS"/>
</div>

- <ins>**HTML/CSS float 및 구조 선택자를 활용한 레이아웃 배치 연습하기**</ins>

- <ins>**화면 구성 설계해보기**</ins>

우선 화면의 배치상 이미지는 왼쪽, 타이틀은 오르쪽 상단, 용어 설명은 오른쪽 하단에 위치한다.

그럼 왼쪽에 높이가 100% , 너비가 50% 비율로 위치하면 되고, 오른쪽에는 높이가 50%, 너비가 50% 비율로 두 개의 박스를 수직방향으로 배치시키면 된다는 결론이 나온다.

이제 float 속성을 이용해서 왼쪽에 배치해야되는 이미지(dd 첫번째 요소)의 경우 left 속성값을, 오른쪽에 배치해야되는 타이틀(dt)과 용어설명(dd 두번째 요소)은 right 속성값을 위에서 생각한 너비, 높이 비율값과 함께 선언해주면 될 것 같다.
그래서 아래와 같은 구조로 우선 생각을 해보았다.

  <!-- more -->

<table>
  <tr>
    <td>
      <img src="/images/post_images/210226_layout_design1.png" alt="설계단계 레이아웃 이미지" width=300 height=200/>
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong> <설계단계 레이아웃> </strong>
    </td>
  </tr>
</table>

그리고 용어설명 전체 컨텐츠를 담고 있는 부모요소가 자식요소의 높이 정보를 잃어버렸기 때문에 float를 해제시켜줘야 될 것 같다.

이제 코드로 구현해가면서 생각했던 부분과 달라진 부분을 수정해가면서 레이아웃을 잡아가보자.
원래 레이아웃 설계 단계에서 너비 비율을 50%로 생각을 했었는데, 구현을 해보니, 너비를 이미지 부분은 30%로, 타이틀과 설명 부분은 70%로 해줘야 sample 원본과 비슷하게 나오는 것 같다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210226_layout_design2.png" alt="설계단계 레이아웃 수정 이미지" width=300 height=200/>
    </td>
  </tr>
  <tr>
    <td align="center">
      <strong> <설계단계 레이아웃 수정> </strong>
    </td>
  </tr>
</table>

![]()

그리고 추가적으로 기타 용어 설명부분을 원본 페이지와 비슷하게 구성하기 위해서 자간 높이를 line-height 속성을 이용해서 조절해주고, 글자의 weight값과 타이틀 색상을 바꿔줘야겠다고 생각했다.

코드와 결과화면은 아래와 같다.

<img src="/images/post_images/210226_result1.png" alt="결과 이미지1" width=300 height=200/>

```css
.term {
  margin-top: 20px;
  padding: 16px;
  border-radius: 5px;
  background: linear-gradient(180deg, #ccc, #eee);
  border: 1px solid grey;
}

.term h2 {
  margin: 0;
  margin-bottom: 19px;
}

.term dd,
dt,
dl {
  margin: 0;
}

.term::after {
  content: '';
  display: block;
  clear: both;
}

.term-list div dd:nth-of-type(1) {
  float: left;
  width: 30%;
  height: 100%;
}

.term-list dt {
  float: right;
  width: 70%;
  font-size: 14px;
  color: rgb(12, 110, 172);
  font-weight: 400;
  margin: 0 0px 10px 0;
  padding-left: 5px;
}

.term-list div dd:nth-of-type(2) {
  float: right;
  width: 70%;
  font-size: 14px;
  font-weight: 400;
  line-height: 19px;
  padding-left: 5px;
}
```

<br/>
그리고 추가적으로, 두 개 이상의 컨텐츠를 좌우 대칭 형태로 교차해서 화면에 표시하는 방법을 구조 선택자를 사용해서 실습해보았다. 
각 컨텐츠가 div태그로 분리가 되어있고, 이 div태그가 홀수 번째인지, 짝수 번째인지 구분해서 내부 자식요소의 float 속성 값을 대칭으로 해주면 될 것 같고 생각을 했다.

결과 화면과 코드는 아래와 같다.

<img src="/images/post_images/210226_html_css_result2.png" alt="결과 이미지2" width=300 height=200/>

```css
/* 웹 관련 용어 */
.term {
  margin-top: 20px;
  padding: 16px;
  border-radius: 5px;
  background: linear-gradient(180deg, #ccc, #eee);
  border: 1px solid grey;
}

.term h2 {
  margin: 0;
  margin-bottom: 19px;
}

.term dd,
dt,
dl {
  margin: 0;
}

.term::after {
  content: '';
  display: block;
  clear: both;
}

/* div 태그가 홀수번째이면 */
.term-list div:nth-child(odd) dt {
  float: right;
  width: 70%;
  font-size: 14px;
  color: rgb(12, 110, 172);
  font-weight: 400;
  margin: 0 0px 10px 0;
  padding-left: 5px;
}

.term-list div:nth-child(odd) dd:nth-of-type(1) {
  float: left;
  width: 30%;
  height: 100%;
}

.term-list div:nth-child(odd) dd:nth-of-type(2) {
  float: right;
  width: 70%;
  font-size: 14px;
  font-weight: 400;
  line-height: 19px;
  padding-left: 5px;
}

/* div 태그가 짝수번째이면 */
.term-list div:nth-child(even) dt {
  float: left;
  width: 70%;
  font-size: 14px;
  color: rgb(12, 110, 172);
  font-weight: 400;
  margin: 0 0px 10px 0;
  padding: 20px 0 0 5px;
}

.term-list div:nth-child(even) dd:nth-of-type(1) {
  float: right;
  width: 30%;
  height: 100%;
  padding: 20px 0 0 0;
}

.term-list div:nth-child(even) dd:nth-of-type(2) {
  float: left;
  width: 70%;
  font-size: 14px;
  font-weight: 400;
  line-height: 19px;
  padding-right: 5px;
}
```
