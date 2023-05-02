---
title: 210207 Side Project Diary 2일차
date: 2021-02-07 12:16:42
tags:
  - Self-Development
  - Side-Project-Diary
categories:
  - Side-Project-Diary
hidden: true
secret: true
---

![](/images/post_images/side_project_diary_img.jpg)

## **Side Project Diary 2일차**

오늘은 어제 `새로 kick-off한 ReactJS project로 시작을 할 계획`이다. 새로 만든 GitHub 계정에서의 첫 번째 나의 Side Project이기 때문에 README 파일 작성, Commit, Pull Request, Issue, Branch 관리 등 전반적으로 제대로 구성해서 진행해 볼 것이다.
그래서 오늘은 `Issue3`(Organize the fundamental folder structure of project.)에 해당하는 작업과 추가적으로 `Open API 관련 method를 작성하는 새로운 Issue6를 추가`하여, `Issue2`와 Issue6를 같이 진행할 예정이다.

Open API에서 어떤 Data를 취득해서 화면에서 처리해야 하는지 구분하기 위해 우선적으로 <ins><b>Main page의 Wireframe을 손으로 그려보았다.</b></ins>

<table>
  <tr>
    <td><img src="/images/post_images/210207_reactjs_project_wireframe.png" alt="210207_reactjs_project_wireframe" /></td>
  </tr>
  <tr>
    <td align="center">
      <b>Main page의 Wireframe(handwriting)</b>
    </td>
  </tr>
</table>

  <!-- more -->

Open API menual을 보면서 사용법을 어느정도 숙지했다. 각 분류된 책 정보는 제공된 엑셀 시트에 명시된 category id를 이용해서 접근할 수 있는데, 지정된 api path로 request로 보낼때 params값으로 이 category id를 같이 보내주면 response로 해당 책과 관련된 리스트 정보를 받을 수 있다.
개발 서적과 관련된 category id는 별도로 부여되어 있지 않아서 일단 `search.api에 search params를 보내는 방법으로 처리`를 하고, 구체적으로 나눌 section은 다시 생각해 볼 예정이다.
이렇게 main page의 wireframe을 잡아보니 Open API 관련 method를 작성할때 어떤 데이터 취득을 목적으로 처리를 해야하는지 윤곽이 잡혔다.
