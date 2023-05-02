---
title: 210821 Hotfix branch - local master branch와 remote master branch의 차이
date: 2021-08-21 18:40:00
tags:
  - git
  - hotfix-branch
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210821_branch_img.png" alt="local master branch와 remote master branch의 차이">
</div>

이번 포스팅에서는 실제 업무에서 master branch를 base로 hotfix branch를 끊어서 작업하면서 겪었던 문제에 대해서 작성해보려고 한다.

## <b>Hotfix branch에서의 작업</b>

이번에 프로모션 페이지 컴포넌트 작업을 stage를 base branch로 두고 작업을 했었다. 이렇게 작업을 하게 되면, 개별 프로모션 페이지 컴포넌트 작업내용 뿐만 아니라 stage에서 여지까지 merge되었던 작업내용들까지 같이 master branch로 merge가 되기 때문에 `master branch에서 개별 hotfix branch를 끊어서 작업을 해야했다.`

그런데 local master branch에서 remote master branch를 pull해서 최신 상태로 업데이트를 한 뒤에 local master branch를 base로 새로운 hotfix branch를 끊어서 작업을 했는데, 여기서 문제가 발생했다.

## <b>local master branch와 remote master branch의 차이에 대한 이해</b>

앞서 언급한 문제 상황에 대한 결론을 우선적으로 말하자면, `local master branch는 현재 stage와 merged back된 상태이고, remote master branch는 stage에 merged back되지 않는 개별 branch 상태이다.`
따라서 local master branch를 기반으로 hotfix branch를 끊게 되면, 작업 후에 remote master branch로 push를 하게 되면, merged back된 stage의 작업 내용들까지 같이 넘어가게 된다.

이러한 문제로 인해 <ins>`hotfix branch는 반드시 origin/master branch를 base로 끊어서 작업`</ins>을 해야한다.

## <b>origin/master를 base로 hotfix branch 작업</b>

```zsh
$ git checkout -b hotfix/promotion-page master (X)
$ git checkout -b hotfix/promotion-page origin/master (O)
```

  <!-- more -->

local master branch와 remote master branch에서 각 각 hotfix branch를 끊었을 때의 차이를 확인해보기 위해 개인 Github Repository에서 시뮬레이션을 해보았다.

[https://github.com/LeeHyungi0622/hotfix-practice-repo](https://github.com/LeeHyungi0622/hotfix-practice-repo)

우선, 실제 업무의 상황과 동일하게 master branch와 stage branch를 끊고, stage branch의 상태를 아래와 같이 master branch의 뒤쪽에 배치되도록 branch를 구성하였다.

<div align="center">
  <img src="/images/post_images/210821_remote-local-repository.png" alt="local/remote repository">
</div>

<br/>

다음으로 local master branch와 remote master branch를 각 각 base branch로 hotfix branch를 끊어 보았다.

<div align="center">
  <img src="/images/post_images/210821_hotfix_branch.png" alt="hotfix branch based on local and remote master branch">
</div>

따라서 hotfix branch를 생성할 때에는 remote master branch(origin/master)를 base로 생성해야 한다.
