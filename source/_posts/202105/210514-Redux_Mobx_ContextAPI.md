---
title: 210514 Redux vs Mobx vs ContextAPI
date: 2021-05-14 16:21:00
tags:
  - Redux
  - Mobx
  - ContextAPI
categories:
  - ReactJS
---

<div align="center">
  <img src="/images/post_images/react-js-logo.png" alt="React" />
</div>

<table>
    <tr>
        <th align="center" width="33%">Redux</th>
        <th align="center" width="33%">Mobx</th>
        <th align="center" width="33%">ContextAPI</th>
    </tr>
    <tr>
        <td align="center">원리가 간단하다.</td>
        <td align="center"></td>
        <td align="center" rowspan="3">어플리케이션이 별로 크지 않은 경우에 사용하도록 한다.</td>
    </tr>
    <tr>
        <td align="center">에러에 대한 추적이 가능하다.</td>
        <td align="center">에러에 대한 추적이 불가능하다.</td>
    </tr>
    <tr>
        <td align="center">코드의 양이 많다.</td>
        <td align="center">코드의 양이 적다.(생산성이 높다)</td>
    </tr>
</table>

비동기 요청을 했을때에는 요청에 대한 실패에 대비를 해야 되기 때문에 이런 처리부분에 대해 해당 상태관리 라이브러리가 제공하는지에 대해 고려해서 사용해야 한다.<b>(대기</b>, <b>성공</b>, <b>실패)</b> 이 부분에 대해 Redux와 Mobx는 제공이 되지만, ContextAPI의 경우에는 직접구현을 해야한다.
