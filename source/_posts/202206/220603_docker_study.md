---
title: 220603 Docker 스터디 3일차
date: 2022-06-03 15:36:00
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

이번 포스팅에서는 docker 실습한 내용과 docker image를 만들고, 생성한 이미지를 docker hub에서 관리하고 배포하는 부분에 대해서 정리해보려고 한다.

## <ins><b>실습1) nginx 컨테이너 만들기</b></ins>

```zsh
# local에서 생성한 index.html파일을 생성할 container 내의 index.html 파일로 -v 옵션으로 생성한다.
$ docker run -d --rm \
  -p 5000:80
  -v $(pwd)/index.html:/usr/share/nginx/html/index.html \
  nginx
```

-v 에서 $(pwd)를 통해 현재 위치 정보를 입력할 수 있다는 것을 배웠고, local에서 생성한 파일을 -v 옵션을 사용해서 container 내의 파일을 mapping 시킬 수 있다는 것도 다시 한 번 정리할 수 있었다.

## <ins><b>실습2) php 컨테이너 실행하기</b></ins>

```zsh
$ docker run --rm \
  -v $(pwd)/hello.php:/app/hello.php \
  php:7 \
  php /app/hello.php
```

php 컨테이너에서 /app 하위에 hello.php 파일을 mapping 시키고, 실행시키고자 하는 service의 버전을 지정한 뒤에 바로 php 명령을 사용해서 mapping시킨 컨테이너 내의 php 파일을 실행시킬 수 있다.

<!-- more -->

## <ins><b>Docker 이미지 만들기</b></ins>

도커는 레이어드 파일 시스템을 기반으로 한다. 이미지의 구성 방식은 AUFS, BTRFS, Overlayfs 등의 파일시스템을 사용해서 구성된다.(`layered된 형태로 이미지 생성`)

**ref.**
(1) `AUFS(Advanced multi layered Unification FileSystem)` : 리눅스 파일 시스템의 union mount를 구현하기 위해서 시작한 프로젝트

(2) `BTRFS` : B-tree 파일 시스템 또는 배더 파일 시스템

(3) `Overlayfs` : Union mount를 지원하는 파일 시스템 중 하나로, AUFS가 그 중 하나이다. AUFS는 리눅스 커널의 Mainline에 포함되어 있지 않지만, Overlayfs는 2010년에 개발된 Union mount fs으로, 2014년에 리눅스 커널 Mainline에 통합되었다.
(`AUFS는 소스코드가 읽기 힘들고 주석이 없다는 이유로 리눅스 커널에 통합되지 않았다고 한다.`)

도커의 이미지는 프로세스가 실행되면, 파일이 하나 하나 생성이 되는데, 이런 파일들이 이미지에 하나씩 차곡 차곡 쌓인다. 이렇게 차곡 차곡 쌓인 파일들이 새로운 이미지를 생성한다.

이전까지는 누군가 만든 이미지를 가져다가 사용했는데, 직접 이미지를 만들어서 사용할 수 있다.

## <ins><b>Docker 이미지</b></ins>

이미지에는 읽기 전용과 쓰기 전용이 있다. 이전에 사용했던 다른 사람들이 만든 이미지는 `Base Image(rootfs + Base Image)라는 수정이 불가능한 영역`이 존재했고, 사용자는 새로운 기능을 추가/수정/삭제함으로써 또 다른 형태의 이미지를 생성 할 수 있다.

- (1) Base Image를 실행 -> (2) 실행된 Container 내에 응용프로그램 설치 -> (3) docker commit 명령을 통해 Custom Image를 생성

```zsh
$docker commit git ubuntu:git
```

이름이 git인 이미지를 ubuntu:git(태그)으로 새로운 이미지로 commit한다.

- ### **새로운 docker image 생성하기**

  ```zsh
  #docker build -t {image name : image tag} {build context}

  $docker build -t hyungi(이름)/ubuntu(이미지 이름):git01(태그) .(빌드 컨텍스트)
  ```

  `한 번에 성공하는 빌드는 없기 때문에 빌드 성공이 될 때까지 많은 시행착오를 경험하게 된다. 빌드가 성공해도 리팩토링을 통해 더욱 최적화된 이미지를 생성해야 한다.`

  - #### **Build context인 Dockerfile의 작성 규칙**

    - FROM : 기본 이미지
    - RUN : 쉘 명령어 실행
    - CMD : 컨테이너 기본 실행 명령어 (Entrhypoint의 인자로 사용)
    - EXPOSE : 오픈되는 포트 지정
    - ENV : 환경변수 설정
    - ADD : 파일 또는 디렉토리 추가. URL/ZIP 사용가능
    - COPY : 파일 또는 디렉토리를 복사해서 추가
    - ENTRYPOINT : 컨테이너 기본 실행 명령어
    - VOLUME : 외부 마운트 포인트 생성
    - USER : RUN, CMD, ENTRYPOINT를 실행하는 사용자
    - WORKDIR : 작업 디렉토리 설정 (cd 명령과 유사)
    - ARGS : 빌드타임 환경변수 설정
    - LABEL : key - value 데이터
    - ONBUILD : 다른 빌드의 베이스로 사용될때 사용하는 명령어

    ```zsh
    # node image 사용
    FROM node:12-alpine # 이미 만들어진 base 이미지를 사용하고, *-alpine은 only node 패키지만 설치된 이미지이므로 용량이 95MB로 작다.(full package: 900MB 이상)

    # 패키지 우선 복사 (변경된 파일이 포함되어있으면, cache가 되지 않기 때문에 변화가 없는 /package 파일을 우선적으로 url/src/app에 복사해서 cache를 사용하도록 한다)
    COPY      ./package* /usr/src/app/
    WORKDIR   /usr/src/app
    # cache된 package* file을 읽어서 npm package 설치
    RUN       npm install

    # 변경된 소스코드를 포함한 소스를 복사한다. (cache 되지 않은 영역)
    COPY . /usr/src/app

    # WEB 서버를 실행한다. (Listen port setup)
    EXPOSE    3000
    CMD       node app.js
    ```

  - #### **docker build 옵션**

    - -f <Dockerfile path> : 다른 위치의 Dockerfile 빌드 가능
    - -t 명령어로 Docker image 이름을 지정 가능
    - {namespace}/{image name}:{tag} 포멧으로 이름 작성
    - 빌드 컨텍스트가 위치한 디렉토리를 {빌드 컨텍스트}에 작성

  - #### **.dockerignore**

    - .gitignore과 비슷한 역할을 한다.
    - Dockerfile이 빌드될때 .dockerignore에 작성된 패턴의 파일은 무시한다.

    `Dockerfile` / Build context

    ```
    FROM ubuntu:latest
    RUN apt-get update
    RUN apt-get install -y git
    ```

    Dockerfile build 후 새롭게 생성된 git-dockerfile 이름의 이미지를 확인

    ```zsh
    $docker build -t ubuntu:git-dockerfile .
    $docker images | grep ubuntu

    # 생성한 ubuntu:git-dockerfile 이미지를 git3 이름의 컨테이너로 실행하고, 동시에 git 명령어를 실행한다.(-it option)
    $docker run -it -name git3 ubuntu:git-dockerfile git
    ```

<br/>

## <ins><b>Docker 이미지 저장소</b></ins>

여지까지 생성한 Docker 이미지 파일은 모두 내 PC에 있기 때문에 다른 사람들이 쓰게 하기 위해서는 dockerhub라는 곳에 저장해야 한다.

```zsh
$docker login
$docker push {ID}/example # docker hub로 이미지 올리기
docker pull {ID}/example # docker hub로부터 이미지 가져오기
```

Docker hub는 무료 계정의 경우, 한 개까지만 비공개이고, 그 이상은 전체공개가 되기때문에 주의해서 사용하도록 한다. (유료계정은 모두 비공개)

## <ins><b>배포하기</b></ins>

컨테이너 실행이 곧 배포이다. `(이미지 PULL + 컨테이너 START)`

```zsh
$docker run -d -p 3000:3000 hyungi/app
```
