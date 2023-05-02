---
title: 220604 Docker 스터디 4일차
date: 2022-06-04 19:28:00
tags:
  - Docker
categories:
  - Docker&K8s
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220531_docker.png" alt="docker">
</div>

<br/>
<br/>

## <ins><b>실습1) Nginx를 이용한 정적 페이지 서버 만들기</b></ins>

- ### **Docker 이미지 생성하기(Dockerfile)**
  ```dockerfile
  FROM nginx
  COPY ./index.html /user/share/nginx/html/index.html
  EXPOSE 80
  ```
- ### **생성한 이미지를 활용해서 배포하기**
  ```zsh
  $docker build -t hyungi/test1 .
  $docker run -d --rm \
   -p 50000:80 \
   hyungi/test1
  ```

## <ins><b>실습2) NodeJS 어플리케이션 이미지 빌드 및 배포</b></ins>

(1) 우선 디렉토리 내에 NodeJS 프로젝트를 구성한다. (`server.js 및 package.json`)

(2) 이미지 생성을 위해 Dockerfile 작성한다.

```yml
FROM    node:12-alpine
COPY    ./package* /usr/src/app/
WORKDIR /usr/src/app
RUN     npm install
COPY    . /usr/src/app
EXPOSE  8080
CMD     ["node", "/app/server.js"]
```

<!-- more -->

(3) .dockerignore 파일을 작성해서 이미지 빌드시에 node_modules 폴더가 빌드되지 않도록 한다.
`.dockerignore`

```json
node_modules/
```

(4) `작성한 Dockerfile로 이미지를 빌드`한다.

```zsh
$docker build -t hellonode .
$docker run --rm -d \
 -p 60000:8080 \
 hellonode
```

## <ins><b>Docker 추가 학습</b></ins>

여지까지 가장 기본적이면서 핵심적인 도커에 관한 내용에 대해서 학습하였다. 그런데 가장 기초적인 부분을 배운 것이기 때문에 아직 익숙하지 않은 부분도 많아서 실습이 필요할 듯 하다.
기초적인 부분이 실습이 끝나면, 아래의 내용에 대해서도 추가적으로 공부하도록 하다.

- 이미지를 만들기 위한 다양한 쉘 스크립트와 환경변수를 사용
- CI/CD 자동빌드와 자동배포, BLUE & GREEN 배포 / 무중단 배포 (`컨테이너는 무조건 죽였다가 띄워야하는데, 무중단으로 배포도 가능하다`)
- 모니터링, 로그
- 가상 네트워크
- 보안
- 쿠버네티스(Kubernetes)
- 이스티오(istio) 서비스매시
