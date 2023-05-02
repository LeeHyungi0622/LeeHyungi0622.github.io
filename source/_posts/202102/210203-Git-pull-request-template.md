---
title: 210203 Git PR template
date: 2021-02-03 07:12:42
tags:
  - Self-Development
  - Git
  - Pull-Request
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210203_pull_request_img.png" alt="Pull request"/>
</div>

다양한 사람들과 협업을 할 때 중요한 것은 `문서작성 능력`과 `커뮤니케이션 능력`이다. <br/>

Github 프로젝트를 다른 사람들과 같이 진행을 할때, pull request나 commit 메시지를 작성하는데, <br/>

적절한 format과 convention을 따라 작성을 하는 것은 매우 중요하다. 실제로 업무를 할때에도 이 convention을 따라 적절한 format으로 작성을 하게 되면, 해당 pull request나 commit 메시지를 확인하는 사람에게 업무처리에 대한 깔끔한 인상과 명확하게 처리한 업무에 대한 내용 전달을 할 수 있다. <br/>

<br/>

따라서 이번 포스팅에서는 Github project에서 어떻게 문서를 관리하는지와 PR Template을 어떻게 자동으로 generate하는지에 대해서 살펴보고, 잘 formatting된 Template의 sample을 살펴보며 어떻게 하면 좀 더 전달력있는 Pull Requst message를 작성할 수 있는지에 대해서 배워보도록 하겠다.

## PR Template 생성하기

Github 프로젝트를 위한 PR Template를 만들어보도록 하겠다.

  <!-- more -->

프로젝트를 위한 PR template을 생성하는 방법은 간단하다. PULL_REQUEST_TEMPLATE 파일을 생성해서 아래의 세 가지 위치 중에 한 곳에 위치시켜주면 된다.

GitHub가 `PULL_REQUEST_TEMPLATE`파일을 프로젝트 내에 있다는 것을 알고 새로운 PR을 생성할때 auto-populate 해 줄 것이다.

> (1) The root of your project
> (2) .github folder
> (3) docs folder

나는 project의 root에 .github folder를 생성해서 PULL_REQUEST_TEMPLATE 파일과 ISSUE_TEMPLATE 파일을 작성해서 관리할 것이다.

## PR를 작성할때 고려되어야 할 내용들

- What is this change?
- What does it fix?
- Is this a bug fix or a feature? Does it break any existing - functionality or force me to update to a new version?
- How has it been tested?

<br/>
