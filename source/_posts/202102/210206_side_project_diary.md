---
title: 210206 Side Project Diary 1일차
date: 2021-02-06 12:23:42
tags:
  - Self-Development
  - Side-Project-Diary
categories:
  - Side-Project-Diary
hidden: true
secret: true
---

![](/images/post_images/side_project_diary_img.jpg)

## **Side Project Diary 1일차**

이전에 GitHub로 Repository를 생성해서 개인프로젝트를 하면서 **조금 아쉬웠던 부분이 뭔가 내가 프로젝트를 진행했을 당시에 느꼈던 느낌과 생각이 시간이 지나면 잊혀진다는 것이었다.** 물론 Repository에 commit된 내용과 README 파일의 내용을 보면 "아, 이때 이 기능을 넣었지"라고 생각은 할 수 있지만, 뭔가 그 사이 사이에 느꼈던 느낌까지 세세하게 채우는 것은 어렵다고 생각한다.
그래서 이번 Side Project를 시작하면서 새롭게 Side Project Diary를 작성하려고 한다.
매일 매일 일기로 기록을 남기면 자연스럽게 프로젝트의 진행에 대해 스스로 self feedback하는 시간도 갖을 수 있을 것 같다.

훗날 회사에 가서 일을 할때 참여하는 프로젝트도 보안상 세부적인 내용은 남길 수 없겠지만, 대략적인 내용으로 일기를 써볼 수 있을 것 같다. 그럼 내가 개발자로서 어떻게 성장을 해왔는지 다시 되돌아보며 더욱 성장해나갈 수 있는 발판이 될 수 있을 것 같다.

그럼 나의 첫번째 Side Project Diary를 시작해본다.

  <!-- more -->

이번 첫 프로젝트는 ReactJS를 사용한 간단한 책 정보 검색 사이트를 만들어 보려고 한다.
어제 강사님이 개발자를 위한 책을 추천해주셨는데, 이때 간단하게 요즘 IT/개발 관련 서적들 중에 어떤 책이 베스트샐러이고, 새로 출간되었는지 볼 수 있는 사이트가 있으면 좋지 않을까 생각되어 이 아이디어를 구상하게 되었다.
물론 이미 좋은 여러 책 관련 사이트들이 많지만, 나는 심플하게 개발과 컴퓨터 공학 관련 서적들 위주로 main category를 구성해서 웹 어플리케이션을 만들어보려고 한다.

이렇듯 개발이란 무언가 필요에 의해서 시작되는 것 같다. 나중에 회사에 가면 고객의 needs에 의해서 개발을 시작하겠고, 지금은 나의 일상에서 뭔가 필요하다고 생각되는 부분에서 아이디어를 착안해서 side project의 개발 아이템으로 시작을 하면 되는 것 같다.

솔직히 이번에 개발 블로그를 시작하면서 단순 코더가 아닌 생각할 줄 아는 개발자가 될 수 있을 것 같다는 생각이 많이 들었다.
이전에 많은 회의감이 들었었고, 찐 개발자가 되려면 노력이 많이 필요하다는 것은 알고 있기 때문에 남들보다 100배는 열심히 해야 겠다는 생각이 들었다.

<table>
  <tr>
    <td>
      <img src="/images/post_images/210206_book_recommendation_project_start.png" alt="210206_book_recommendation_project_start.png">
    </td>
  </tr>
  <tr>
    <td align="center">
      <b>첫 번째 프로젝트 구상내용 적어보기</b>
    </td>
  </tr>
</table>

우선 프로젝트의 주제는 앞에서 언급했듯이 개발자를 위한 책 정보제공 사이트이다. 카테고리의 구성은 개발 서적, 컴퓨터 공학 서적을 메인 카테고리로 하고, 검색하는 메뉴를 하나 추가하려고 한다.
Open API 중에 적절한 API를 찾던 중에 `InterPark에서 제공하는 Book Open API`가 있어서 이 API를 사용하기로 했다.
언어로는 HTML, CSS, JavaScript,그리고 Library는 ReactJS를 사용하려고 한다.
전체적인 프로젝트의 디자인 패턴은 React의 Container-Presenter Pattern을 사용할 것이다.
간단한 Application이지만, Data logic 부분과 View 부분을 분리해서 작성하면 코드의 가독성과 유지보수시에도 도움이 될 것이기 때문이다.

<img src="/images/post_images/210206_Container-Presenter-pattern.png" alt="210206_book_recommendation_project_start" width=250 height=250>

### **Task를 Category별로 분류하기 (Functional Classification)**

- 프로젝트 기본 폴더구조 구성 **(Project basic folder structure)**
- API
  - interpark Open API의 사용법 API menual 참고
  - API 호출하는 Method 작성
- 소프트웨어 개발 카테고리 **(Software Development Category)**
  - Category별 베스트샐러 서적, 새로 출간된 서적 등의 category로 나눠서 출력하는 페이지(category-main-page)
  - 각 Category별 책의 세부 정보 페이지
- 컴퓨터 공학 카테고리 **(Computer Engineering Category)**
  - Category별 베스트샐러 서적, 새로 출간된 서적 등의 category로 나눠서 출력하는 페이지(category-main-page)
  - 각 Category별 책의 세부 정보 페이지
- 검색 카테고리 **(Search Category)**
  - 검색 결과출력 페이지

**처음부터 너무 세세하게 Task를 분류할 수 없기 때문에 우선, 큰 덩어리(Chunk)로 나눠서 Task를 분류해보았다.
이제 이 Task를 기준으로 세부 Task항목으로 쪼개서 프로젝트를 진행해 볼 것이다.**
