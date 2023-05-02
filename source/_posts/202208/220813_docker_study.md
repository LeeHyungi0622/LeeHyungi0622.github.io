---
title: 220813 Docker 스터디 
date: 2022-08-13 12:43:00
tags:
  - Docker
categories:
  - Docker&K8s
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/220531_docker.png" alt="docker">
</div>

<br/>
<br/>

이번 포스팅에서는 docker를 사용하면서 유용하다고 생각되는 명령어들을 정리해두려고 한다.

## <ins><b>Docker container 일괄 중지 및 삭제</b></ins>

  ```zsh
  $docker stop $(docker ps -a -q) # docker container 전체 중지
  $docker rm $(docker ps -a -q) # docker container 전체 삭제
  ```
## <ins><b>Docker container로 올린 애플리케이션 build 다시 하기</b></ins>

FastAPI로 만든 API 애플리케이션을 Docker로 올린 후 코드를 수정하면, 수정된 코드 내용이 반영이 안되는데 이때 아래 명령으로 다시 빌드하면 수정된 코드가 반영이 된다.

  ```zsh
  $docker-compose up --builds
  ```

