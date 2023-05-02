---
title: 210201 The purpose of using pull request on my own repository.
date: 2021-02-01 10:42:42
tags:
  - Self-Development
  - Git
  - Pull-Request
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/question_img.png" alt="Question"/>
</div>

side project를 하면서 궁금해진 부분이 생겼다. 그래서 구글링을 하다가 나와 비슷한 고민을 가진 외국인 개발자분이 개발자 커뮤니티에 질문을 올려서 답변을 받은 내용이 있는데 어느정도 나의 궁금증을 해결하는데 도움이 되어 포스팅으로 글을 남겨 놓으려고 한다.

[https://softwareengineering.stackexchange.com/questions/178402/](https://softwareengineering.stackexchange.com/questions/178402/is-there-a-purpose-for-using-pull-requests-on-my-own-repo-if-i-am-the-only-devel)

##

내가 문득 궁금해진 부분은 개인 프로젝트(Side project)를 하는데 내가 유일무이한 개발자라면 해당 Repository에서 작업을 할때 과연 Pull Request가 필요할까? 라는 점이었다.
여러 팀원끼리 일을 하고, PR과 DEV로 구성되어있다면, 수정사항을 main branch에 반영할때 pull request를 통해 PR에게 코드리뷰를 받고 적절한 코드수정을 통해 merge되어야 한다. 그런데 과연 나 혼자 작업하는 개인 프로젝트인데 Pull request가 필요할까?

<br/>

우선 여기에 대한 대답은 `YES`이다.

  <!-- more -->

아래에 답변중에 채택을 받은 답변을 첨부했다.

![](/images/post_images/210205_stackexchange.png)

이 답변의 서두에는 각 각의 개발자들이 스스로의 작업을 할때에 pull request를 생성하는 것은 아마 가치가 없는 일이지만, 한가지 잠재적인 이유로 해야 한다고 말한다.

Pull requests는 나의 프로젝트의 history를 더욱 쉽게 추척하는데 사용이 될 수 있다. 그리고 각 각의 Pull request는 issue ID가 있는데 commit message와 changing-log를 통해서 참조 될 수 있다.

<ins>아래의 링크에서는 GitHub로 개인 프로젝트를 시작할때 참고하면 좋은 내용을 담고있다.</ins>

[https://medium.com/@cwlsn/github-personal-project-7d9d40b62e39](https://medium.com/@cwlsn/github-personal-project-7d9d40b62e39)

# **Github에서의 개인 프로젝트 진행**

**Step1)** 개인 프로젝트를 위한 Repository 준비

**Step2)** 개인 프로젝트를 위한 계획 수립 단계
이 단계가 가장 까다롭고 힘들다. 프로젝트의 큰 그림을 작은 tasks와 함께 목표로 나눠서 계획해야 하기 때문이다. 전체적인 것을 당장 해야하는 것은 아니지만 어떻게 시작해야하는지에 대한 아이디어와 구상은 초반 코드작성을 위해서 어느정도 필요하다.

**Step3)** **GitHub issue를 생성**
코드 작성을 위해 editor를 열기전에 Github에서 issue를 작성하도록 한다.
이 issue작성은 개인 프로젝트에 있어 나의 tasks를 관리 및 추적을 할 수 있고, 경우에 따라서 option을 추가작성함으로써 구체적인 task관리가 가능하게 해준다.
GitHub는 여러 default labels을 제공해주는데, 개인의 취향에 다라 색상이나 이모티콘을 추가해 줄 수 있다.
나 자신을 스스로 assignee로 할당을 해줄 수 있고, 훗날 history를 통해서 나의 avatar를 확인할 수 있다.

> `issue 작성법`
>
> > `(1)Description` - 어떤 issue를 해결할 것인지 서술
> > `(2)Acceptance Criteria` - 어떠한 결과로 인해 해당 issue가 해결되었다고 고려되는지 서술
> > `(3)User persona` - 왜 해당 feature가 요구되는지에 대한 이유와 해당 feature를 통한 user의 사용효과. 예를 들어 "a user of my utility class will be able to save time and effort with this function" 과 같이 짧게 작성을 해줄 수 있다.

**Step3)** **Branch 생성**

작성한 코드의 작업은 Branch위에서 완성이 되어야 한다.
branch 이름 작성에 있어 naming convention이 있는데, issue number로 시작해서 branch의 목적을 묘사하는 각 의미단위의 단어는 -(dash)로 연결해서 naming하는 것이다.

```bash
$ git checkout -b 1-hello-world
```

작업이 완료되어 commit이 되었다면, local changes를 GitHub에 push해야 한다. 첫번째 push를 할때에는 아래와 같이 upstream으로 해줘야 한다.
그 다음부터는 git push만 해줘도 충분하다.

```bash
$ git push -u origin 1-hello-world
```

**Step4)** **Pull Request 생성**

일단 생성했던 issu에 대한 코드 작업이 다 되었다면, 이제 Pull request를 작성해 줄 차례이다.
이 단계에서는 작업을 통한 변화와 코드상에서 어떤 부분이 수정되었는지 확인할 수 있다.
팀 단위로 일할때에는 reviewers를 할당하거나 다른 멤버들을 통해 내가 작성한 code에 대한 comment도 받을 수 있다.

만약 merge하고자 하는 Pull request가 `issue #1`과 관련되어 있다면, 간단하게 `Fixes #1`라고 작성을 해주면 된다.

만약 branch에서 작업을 통한 변화가 생겼다면 GitHub는 Pull Request를 생성할 것을 권고할 것이다.
`A sole personal project에 있어서` 대부분의 작업 branch에 대한 pull requests는 master branch와 비교가 되고 merge된다.
`base: master <- compare: 1-hello-world`

`# 210206 업데이트`
<br/>
![](/images/post_images/210206_issue_number_auto_generating.png)
<br/>
![](/images/post_images/210206_pull_request_template.png)
<br/>
**Step5)** **Merge Pull Request**

merge할 준비가 되었다면, GitHub의 자동 issue close 기능을 포함해서 message를 수정해줘야 한다.

**Step6)** **README에 Issue page의 링크 추가해서 작성**

README파일에 issue page의 링크를 추가해서 viewer로부터 issue를 참조할 수 있도록 해야한다. 이러한 GitHub관리는 단순히 코드를 cloning하거나 tracking하는 것을 넘어서서 더 진보적인 GitHub flow를 따르고 있다는 것을 보여 줄 수 있다.

`210206 업데이트`

간단하게 손으로 Git의 전체 흐름을 작성해보았다.

![](/images/post_images/210206_project_flow.png)
