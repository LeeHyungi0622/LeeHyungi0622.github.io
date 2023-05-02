---
title: (Feedback)반응형 네이버 로그인 페이지 구현 및 웹 표준 & 접근성에 기반한 취약점 분석 및 개선안 적용
date: 2021-03-03 09:54:00
tags:
  - Self-Development
  - Assignment-feedback
categories:
  - HTML/CSS
---

<link rel="stylesheet" href="./style.css"/>

<div align="center">
    <img src="/images/post_images/feedback_img.jpeg" alt="피드백" width=600 height=350>
</div>

**프로젝트 Repository :** [https://github.com/LeeHyungi0622/responsive-naver-login-page-with-web-standards-and-accessibility](https://github.com/LeeHyungi0622/responsive-naver-login-page-with-web-standards-and-accessibility)

이번 3.1절 연휴때 했던 과제에 대해서 어제(03월 02일) 데레사 강사님으로부터 피드백을 받았다. 어제 속이 별로 안좋아서 밥을 안먹고 있다가 마침 강사님이 계셔서 과제에 대한 피드백을 부탁드렸었는데 흔쾌히 해주시겠다고 하셔서 너무 감사했다. 쉬셔야 되는 시간인데 이렇게 소중한 시간을 내주셔서 피드백을 주신 강사님께 다시 한 번 감사의 말씀을 드리고 싶다.

자 그럼 어제 받은 피드백에 관한 내용을 정리해보자.
과제를 수행하는 것도 중요하지만 받은 피드백을 통해 어느 부분이 개선이 필요한지 파악해서 내 것으로 만드는 것이 중요하다.

<br/>

## (화면 설계 및 개발 관련 피드백)

<br/>

- ### main 태그 내부의 form 태그

  main 태그에 바로 form 태그를 넣어줘도 되지만, form 태그를 section이나 div 태그로 한 번 감싸서 넣어주도록 하자. 그리고 main 태그에 heading 태그를 넣어서 주도록 하자.

<br/>

  <!-- more -->

- ### label 태그와 controller 태그의 배치

  경우에 따라 label 태그가 controller 태그의 다음에 위치하기도 하지만 보통은 label 태그 다음에 controller 태그가 위치하는 것이 일반적이다.

<br/>

- ### 사용 할 태그를 선정할 때에는 구체적으로 어떤 action을 위한 부분인지 우선 분석하도록 하자.

  예를들어, 네이버 로그인 페이지에서 IP 보안 ON, OFF 표시 부준을 보면 표면적으로는 IP 보안은 ON, OFF를 위한 Label로 보일 수 있다. 하지만 IP 보안 부분은 클릭이 되는 부분이고, 별도의 페이지로 이동을 하게 하는 링크 객체이기 때문에 Label 태그가 아닌 anchor 태그로 처리해주는 것이 좋다. 이렇게 되면 ON/OFF(controller)는 mapping되는 label이 없기 때문에 이런 경우에는 ON/OFF(controller 태그)에 aria-label로 label 속성을 넣어서 처리해줘야 한다.

<br/>

- ### tabindex에 대한 이벤트 처리

  키보드로 focus가 되지 않는 요소에 대해서 태그 속성에 `tabindex=0`을 넣어주게 되는데, 이런 경우에는 별도로 script단에서 해당 키 값에 대한 이벤트 처리를 해줘야 한다.

<br/>

- ### BEM 방식으로 클래스명을 짓는 연습을 꾸준히 하자.
  처음에는 Block Element Modifier 단위로 구분하는 것이 어렵게 느껴지지만 나중에 Sass로 스타일링을 할때 BEM 방식은 매우 유용하다.
  Sass는 실제 태그의 구조와 같이 nested된 방식으로 스타일링을 할 수 있기 때문에 BEM 방식으로 클래스명을 작성하게 되면 각 Block, Element로 구분이 되어 있기 때문에 클래스명만 보더라도 레이아웃의 구조를 쉽게 파악할 수 있다.

<br/>

- ### 개발을 할 때에는 `mobile-first` 방식으로 개발을 하도록 하자.

  이 부분에 대한 조언은 클래스의 구체성에 대한 이야기를 할 때 강사님께서 해주셨던 조언이었다.
  웹 페이지를 개발할때 모바일 개발을 우선 고려해서 개발을 진행하게 되면 나중에 데스크탑 기반의 웹 개발과의 구체성 문제를 사전에 예방할 수 있다.

<br/>

## (접근성 관련 피드백)

<br/>

- ### JAWS/NVDA 스크린 리더가 무조건 placeholder를 읽어주지 않는 것은 아니다.

  이는 브라우저의 조합에 따라 읽어주기도 하고, 읽어주지 않기도 한다. 스크린 리더와 브라우저의 궁합에 대해서는 접근성 연구소 유튜브 채널에 관련 영상이 있다.(아래 첨부)
  **브라우저와 스크린 리더의 궁합 관련 영상 :** [https://youtu.be/ja0RalXx_VA](https://youtu.be/ja0RalXx_VA)

<br/>

- ### 사용자 브라우저와 스크린 리더의 궁합

  **국외 접근성 연구/조사 관련 기관 홈페이지**: [https://webaim.org/projects/practitionersurvey2/](https://webaim.org/projects/practitionersurvey2/)
  접근성 연구소 유튜브 채널의 영상을 통해 배운 내용에 대해서 간단하게 정리를 해보자.

  - #### 브라우저 점유율

    `Chrome > Firefox > IE > Safari`

    사용자 브라우저의 점유율은 장애인과 비 장애인 모두 비슷한 브라우저 점유율을 보여주고 있다.

    <br/>

  - #### 스크린 리더 점유율

    `NVDA > JAWS > VoiceOver > Narrow`

    NVDA는 무료 스크린 리더이면서 사용자가 사용하기 편한 UI를 가지고 있기 때문에 기존 독과점을 했던 JAWS 스크린 리더의 1위 자리를 차지하였다.
    그 외 Window에서 무료로 제공해주는 Narrow 스크린 리더는 독자적인 특별한 기능을 제공해주기 때문에 사용자가 증가되고 있는 추세이다.

    <br/>

  - #### 스크린 리더 사용자의 웹 브라우저와 스크린 리더의 조합

    `스크린 리더와 웹 브라우저의 궁합에 따라 호환성을 극대화 시킬 수 있다.`

    특정 스크린 리더와 웹 브라우저가 만나면 호환성을 극대화 시킬 수 있다. 이 호환성이 극대화되면 스크린 리더가 브라우저 상의 콘텐츠를 잘 해석해주고, 인지해줘서 스크린 리더의 사용자가 빠르게 이해할 수 있도록 도와준다.

    스크린 리더의 명세는 W3C 만큼 빠르게 업데이트 되는 구조가 아니기 때문에 이러한 이유로 특정 스크린 리더에 대한 모든 브라우저의 호환성이 보장되지 않는다.

    `2019.09 기준`

    <table border="1">
        <tr align="center">
            <td>21.4%</td>
            <td>JAWS W/Chrome</td>
        </tr>
        <tr align="center">
            <td>19.6%</td>
            <td>NVDA W/Firefox</td>
        </tr>
        <tr align="center">
            <td>18.0%</td>
            <td>NVDA W/Chrome</td>
        </tr>
        <tr align="center">
            <td>11.5%</td>
            <td>JAWS W/IE</td>
        </tr>
        <tr align="center">
            <td>9.1%</td>
            <td>VoiceOver W/Safari</td>
        </tr>
    </table>

    <br/>

- ### 유효성 검사에 따른 화면의 에러 메시지 처리(feat. aria-live)

  아이디와 비밀번호의 입력과 관련한 화면의 에러 메시지 표시 부분에는 태그 속성에 `aria-live="assertive`를 넣어주도록 하자. 그러면 에러 메시지가 보여졌을때 스크린 리더가 해당 에러 메시지를 읽어주게 된다.
  aria-live의 속성값으로는 다른 옵션들도 있기 때문에 아래 공식 Reference를 참고하도록 하자.

  [https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA/ARIA_Live_Regions](https://developer.mozilla.org/ko/docs/Web/Accessibility/ARIA/ARIA_Live_Regions)

<br/>

- ### Light house 명도/채도에 대한 문제는 디자이너와 협업하도록 한다.

  디자인 시안대로 디자인을 했는데 접근성 검사(Accessibility check)에서 명도/채도 상의 문제가 발견되었다면, 이 부분에 대해서는 디자이너와 협의를 통해 조율을 해야 한다.
