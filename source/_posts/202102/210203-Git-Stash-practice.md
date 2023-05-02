---
title: 210203 Git Stash Practice
date: 2021-02-03 20:14:42
tags:
  - Self-Development
  - Git-Stash
  - TIL
categories:
  - Git
---

<div align="center">
  <img src="/images/post_images/210203_git_stash_img.png" alt="Git stash"/>
</div>

## Git Stash Practice

branch를 끊어서 작업을 하는데, 일부 작업한 내용을 잠시 stage에 올려서 다른 branch에서 작업을 하고자 할때 stash를 활용할 수 있다.

아직 파일의 수정이 끝나지 않아서 commit 하기가 곤란하고, revert로 인해 다른 branch로 checkout을 하기도 곤란한 상황을 처리할 때 git stash를 사용한다.

`git stash 명령은 git add를 통해서 트래킹 중인 파일에 대해서만 사용이 가능하다.`

<br/>

### **새로운 stash 생성**

```bash
# saved to `refs/stash`
$ git stash
    or
$ git stash save
```

  <br/>

### **생성된 stash 리스트 확인**

```python
$ git stash list
```

  <!-- more -->

  <br/>

### **생성된 stash 제거**

```python
$ git stash drop stash@{n}
```

  <br/>

### **git stash apply**

- git stash를 사용해서 stage에 올렸던 수정중인 파일의 상태를 원 파일상태로 복구하는 방법

```bash
$ git stash apply
```

  <br/>

### **가장 최신의 commit 상태로 working space의 상태를 변경**

commit 하지 않은 수정중인 파일 내용을 삭제한다.

```bash
$ git reset -hard
```

```bash
$ git reset --hard HEAD # 최신 commit으로 reset (변경중인 파일 내용 삭제)

$ git stash list
stash@{0}: WIP on exp: ba5adba 1 # 가장 최신의 stash 내용
stash@{1}: WIP on exp: ba5adba 1
stash@{2}: WIP on exp: ba5adba 1

$ git stash apply # 가장 최신의 stash 내용으로 다시 복원
On branch exp
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   f1.txt

$ git stash drop # 가장 최신 stash 삭제

$ git stash pop # stash 복원 및 삭제 (상기명령 동일)
```
