---
title: 210122 Hexo 사용법
date: 2021-01-22 16:37:42
tags:
  - Hexo
categories:
  - Hexo
---

<div align="center">
  <img src="/images/post_images/210122_hexo-logo.png" alt="Hexo"/>
</div>

# Hexo 사용법

---

### Hexo에 파일을 수정/업데이트 한 뒤에 사용하는 명령어에 대해서 정리

![]()

hexo generate는 로컬 환경에서 Hexo 페이지의 변경된 내용을 확인할 때 사용되며, hexo deploy는 업데이트 된 내용을 Git에 최종적으로 deploy함으로써 Github pages에 변경사항을 적용한다.

```
$ hexo clean && hexo generate

$ hexo clean && hexo deploy
```

  <!-- more -->

`2021.02.11 update`
Hexo의 내용을 수정하고 generate한 뒤에 deploy하는 명령어를 좀 더 간단하게 적는 방법

```bash
$ hexo generate && hexo deploy
# 아래의 명령어로 간단하게 generate 및 deploy를 할 수 있다.
$ hexo g -d
```
