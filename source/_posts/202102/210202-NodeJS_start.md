---
title: 210202 NodeJS의 기본 개념과 사용
date: 2021-02-02 05:36:42
tags:
  - NodeJS
categories:
  - NodeJS
---

![](/images/post_images/nodejs_logo.png)

> 본 포스팅 내용은 과거에 개인적으로 공부할때 정리했던 NodeJS의 내용을 복습의 목적으로 다시 정리하는 포스팅입니다.

<br/>

- # NodeJS

JavaScript는 브라우저에 내장이 되어있고, 브라우저상에서 동작한다. NodeJS는 이 JavaScript를 browser 밖으로 끄집어내서 유저가 browser 밖에서도 JavaScript를 사용가능하도록 한 것이다.
`NodeJS = 브라우저 밖의 JavaScript를 의미`

![](/images/post_images/210202_nodejs_img1.png)

따라서 NodeJS는 컴퓨터상에서 동작하기 때문에 JavaScript를 이용해서 `FileSystem에 접근`을 할 수 있고, `서버를 만들 수도 있고,` `WebScrapping도` 할 수 있다. JavaScript를 `브라우저와 독립적으로 활용`할 수 있도록 해주는 것이 NodeJS의 주된 개념이다.

기존에 간단한 JavaScript compile의 경우, chrome inspector에서 했었지만, NodeJS를 설치하면 위와 같이 PC의 로컬환경에서도 JavaScript코드를 compile해 볼 수 있다.

### `Browser와의 종속성을 깼다는 점에 있어 JavaScript언어의 가능성이 넓어졌다.`

## NodeJS의 선택과 사용

- Back-end, Sever를 Build해야하는 경우, Django(Python), Laravel(PHP) 등 다양한 언어와 프레임워크가 있지만 `JavaScript에 능숙하고 Front-end와 Back-end를 모두 JavaScript로 만들고 싶다면 NodeJS를 선택`한다.

- `두번째 만약 제한된 시간이 한정되어있고, 프로젝트가 빨리 시작되어야 한다면, 그리고 거의 모든 것을 다 customize할 수 있는 능력을 가지고 있다면 NodeJS를 선택`한다. 하지만 상대적으로 거의 대부분이 Configuration되어있는 것을 원한다면 Django나 Laravel을 선택하는 것이 좋다.
  왜냐하면 NodeJS는 아무것도 들어있지 않다. 작은 블럭들을 위로 쌓아서 큰 성을 짓는 것이 바로 NodeJS를 활용해서 개발을 하는 것을 의미한다.
  Django의 경우는 이미 완성되어 있는 큰 성과 같아서 전체적인 사용법을 이해하고 이용을 해야하는데 node.js는 그와 다르게 완전 아무 것도 없이 무에서 시작해서 모든 것을 하나씩 붙여가면서 개발을 한다.

  <!-- more -->

- `세번째로 많은 데이터를 다뤄야 할때 NodeJS를 선택`한다.database를 생성하고 삭제, 사용자에게 전송하고 저장하는 작업과 같이 `데이터를 다루는 성능에 있어서는 NodeJS는 성능이 매우 좋다.`(Data Science를 말하는 것이 아니라 `유저들 간에 메시지를 전송하고 하는 것과 같은 실시간 처리 데이터를 의미`한다)
  대신 Hardware쪽을 고려해서 개발을 해야한다면, `JavaScript는 Hardware의 memory에 접근할 수 없기 때문에` NodeJS는 전혀 도움이 되지 않는다.

## NodeJS의 현 주소

그럼 누가 어느 기업이 NodeJS를 사용하고 있을까?

거의 모든 사람이 아는, 그리고 사용하는 Paypal, Uber, Netflix, Linkedin, Walmart, Trello, New York Times, ... 이들의 모든 서비스들이 모두 NodeJS를 사용하여 만들어졌다.

이들의 서비스들이 NodeJS로 만들어진 이유는 앞서 말한 조건들 중에 `실시간 처리 데이터를 다루기 때문에 사용`되었다.

## 웹 서비스를 만들때 꼭 한 가지 언어로만 Back-end를 구성할 필요는 없다.

- Back-end 개발시에 여러 언어를 복합해서 쓸 수 있다. 물론 메인 언어는 존재하지만 다양한 언어로 만들 수 있다.
  `Netflix의 경우는` 엄청 많은 언어로 만들어졌다. 그 이유는 영상을 처리해야하기 때문이다. 영상을 압축하고 스트리밍하고 다운로드하고 다양한 처리들을 가능하게 하려면 한 가지 언어로는 어렵기 때문이다.
  `Paypal의 경우는` 금융거래를 다루기때문에 은행과 연결할때는 Java를 사용하고 웹 사이트를 개발할때는 프론트에서는 JavaScript, 백에서는 다양한 언어들이 사용된다.
  수많은 회사에서 NodeJS로 Back-end를 돌리고 있고 그 만큼 많은 수요를 필요로 하고 있다. 이 의미는 수 없이 많은 웹 서비스 회사에서 검증이 되었고 사용이 되고 있다는 의미이다S.

![](/images/post_images/210202_nodejs_img2.png)
