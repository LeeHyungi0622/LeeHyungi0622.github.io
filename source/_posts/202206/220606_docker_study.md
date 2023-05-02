---
title: 220606 Docker 스터디 5일차
date: 2022-06-06 02:46:00
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

## <ins><b>실습1) ghost 블로그 docker 컨테이너 생성</b></ins>

```yml
# Ghost는 무료 오픈소스 블로깅 플랫폼으로, 자바스크립트로 작성되었다고 한다.

# *.yml 파일은 docker-compose 명령을 사용해서 up, down을 할 수 있도록 도와준다.
# 기존의 콘솔에서 명령어 입력할 때 띄어쓰기등의 입력의 불편한 점을 개선하고자 *.yml파일은 유용하게 사용된다.

version: '3' # docker compose에 정의된 버전에 따라 지원하는 docker의 engine도 달라진다.

services: # 실행할 컨테이너를 정의한다. ($docker run -name [name] [service])
  ghost:
    image: ghost:latest
    ports:
      - '60000:2368'
    volumes:
      - ./ghost/content:/var/lib/ghost/content
    environment:
      url: http://localhost:60000
```

<!-- more -->

## <ins><b>실습2) FrontEnd - BackEnd - DB로 이루어진 웹 서비스를 배포</b></ins>

```yml
version: '3'
services:
  frontend:
    image: subicura/guestbook-frontend:latest
    environment:
      PORT: 8000
      GUESTBOOK_API_ADDR: backend:8000
    ports:
      - '62000:8000'
  backend:
    image: subicura/guestbook-backend:latest
    environment:
      PORT: 8000
      GUESTBOOK_DB_ADDR: mongodb:27017
    restart: always
  mongodb:
    image: mongo:4
```

## <ins><b>실습3) 투표 앱 생성</b></ins>

clone한 프로젝트 폴더 내의 각 디렉토리 안에 있는 Dockerfile로 각 각의 도커 이미지를 생성한다.

```yml
version: '3'
services:
  vote:
    image: voting-vote
    ports:
      - '60001:80'
  redis:
    image: redis:alpine
  worker:
    image: voting-worker
  db:
    image: postgres:9.4
    environment:
      POSTGRES_HOST_AUTH_METHOD: trust
  result:
    image: voting-result
    ports:
      - '60002:80'
```

## <ins><b>실습4) 실시간 채팅 앱</b></ins>

clone한 프로젝트 폴더 내에 있는 Dockerfile을 build해서 도커 이미지를 생성한다.

```yml
version: '3.6'
services:
  chatapp:
    image: chatapp
    ports:
      - '60003:8000'
  postgres:
    image: postgres
    restart: always
    volumes:
      - db_data:/var/lib/postgresql/data
    environment:
      POSTGRES_HOST_AUTH_METHOD: trust
  graphql-engine:
    image: hasura/graphql-engine:latest.cli-migrations
    ports:
      - '60004:8080'
    depends_on:
      - 'postgres'
    volumes:
      - ./hasura/migrations:/hasura-migrations
    restart: always
    environment:
      HASURA_GRAPHQL_DATABASE_URL: postgres://postgres:@postgres:5432/postgres
      HASURA_GRAPHQL_ENABLE_CONSOLE: 'true' # set to "false" to disable console
      HASURA_GRAPHQL_ENABLED_LOG_TYPES: startup, http-log, webhook-log, websocket-log, query-log
volumes:
  db_data:
```
