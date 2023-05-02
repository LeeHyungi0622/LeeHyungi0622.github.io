---
title: Git Repository 이전하기
date: 2021-01-21 08:06:42
tags:
  - Git
  - repository
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210124_github.jpg" alt="GitHub"/>
</div>

오늘은 기존에 관리하던 Github 계정에서 필요한 Repository만 선별해서 새롭게 만든 계정으로 이전하는 작업을 하려고 한다. 이제 정말 필요한 Repository를 생성하고, 모든 Repository는 README 파일을 꼼꼼하게 적는 습관을 갖을 것이다.

새롭게 만든 Github 계정에서는 좀 더 꼼꼼하고 잘 관리된 개발자로서의 모습을 보여 줄 수 있도록 노력해야 겠다.

![](/images/post_images/210124_repository.png)

---

  <!-- more -->

단순하게 기존의 코드만 이전을 하려면 이전하려는 Repository에 Push를 해주면 되지만, 나는 그동안 작성했던 커밋 로그까지 같이 옮기고 싶기 때문에 그 방법에 대해서 찾아보고 정리해보려고 한다.

# Repository 이삿짐 싸기

```
$ git clone --mirror {git Repository 주소}
```

git clone 명령과 --mirror 옵션과 함께 복사하려는 repository 주소를 입력해준다.

위의 작업을 하게 되면 **[Repository 이름].git** 을 가진 디렉토리가 생성된 것을 확인 할 수 있다. 이 디렉토리의 이름을 **.git** 으로 변경을 해주도록 한다.

# 이사할 주소 지정하기

이제 Repository의 정리가 되었으면 이제 이전할 새로운 Repository의 주소를 알려줘야 한다.

```
$ git remote set-url origin {새로 이사갈 레파지토리 주소}
```

# Repository 이사하기

```
git push --mirror
```
