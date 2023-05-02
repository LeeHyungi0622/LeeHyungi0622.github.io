---
title: 210510 anchor 태그에서 target="_blank" 속성의 보안적 문제
date: 2021-05-10 21:33:00
tags:
  - HTML-CSS
  - Anchor-tag-security-issue
categories:
  - HTML/CSS
---

<div align="center">
  <img src="/images/post_images/210510_security_issue.png" alt="Security issue">
</div>

## <b>anchor 태그에서 target="\_blank" 속성의 보안적 문제</b>

anchor 태그에 연결되어있는 페이지를 새로운 창에 열고자 할때 속성에 target="\_blank"를 넣어주는데, 보안적으로 문제가 있다. 바로 `Tabnabbing이라는 공격의 위험성`이 있다는 것이다.
Tabnabbing이란 HTML 문서 내에서 링크를 클릭했을때 새롭게 열린 탭이나 페이지에서 기존 문서의 location을 피싱 사이트로 변경해서 정보를 탈취하는 공격기술을 말한다.
이 공격은 이메일이나 커뮤니티에서 쉽게 사용될 수 있기 때문에 주의해야 한다.
따라서 anchor 태그에 target="\_blank" 속성을 넣어 줄 때에는 반드시 `rel="noreferrer noopener"`라는 속성을 같이 넣어서 취약점을 보완해야 한다.
