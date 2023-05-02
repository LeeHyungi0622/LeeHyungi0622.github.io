---
title: 210131 Git Practice (Feature/Release/Hotfix)
date: 2021-01-31 11:30:42
tags:
  - Self-Development
  - Git
  - TIL
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210129_git_flow.png" alt="Git flow model"/>
</div>

오늘은 저번 수업시간(**29(금)**)에 배운 `Git branch 관리와 협업`에 대한 내용을 최대한 활용해서 연습을 해 볼 것이다. 물론 강사님 말처럼 입사하게 되면 **main(master) branch, release branch**를 건드릴 필요 없이, **develop, feature branch**만 가지고 작업을 하겠지만 개인 프로젝트할 때 버전별로 업데이트 해서 release도 해 볼 것이기 때문에 이를 위해 제대로 연습을 해 보겠다.

<br/>

Branching model : git flow

- ## **Release branch 실습**

  ![](/images/post_images/210131_git_flow_release.png)

- ## **Hotfix branch 실습**

  ![](/images/post_images/210131_git_flow_hotfix.png)

    <!-- more -->

## **git flow 활용 꿀팁**

git flow를 활용해서 start, finish를 하게 되면 branch가 merge가 된 뒤에 자동으로 생성했던 branch를 삭제하고 merge된 branch로 switch해주다. 편리하긴 하지만 만약에 graph 구조상에 생성했던 branch에 대한 내용도 keep하고 싶다면 아래와 같이 간단하게 `-k(eep)`옵션을 붙여주면 된다.

```bash
$ git flow feature finish -k [feature branch name]
```

성공적으로 develop branch에 feature branch를 merge하고 난 뒤에 feature branch를 제거하지 않는 것은 merge를 되돌리는 것을 손쉽게 할 수 있도록 도와준다.
간단하게 merge commit을 제거함으로써 merge를 되돌릴 수 있다.

```bash
# On develop branch
$ git reset --hard HEAD^
```
