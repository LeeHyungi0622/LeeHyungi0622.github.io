---
title: 220601 Docker 스터디 2일차
date: 2022-06-01 10:20:00
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

이번 포스팅부터는 본격적으로 Docker를 설치해보고 실행해보는 부분에 대해서 다뤄보려고 한다.
Docker는 다양한 데이터베이스를 손쉽게 생성 및 삭제할 수 있기 때문에 개발할때 많이 사용된다.

## <ins><b>Docker 설치</b></ins>

docker 공식 홈페이지에서 docker를 설치하면 된다.
기본적으로 도커는 리눅스를 지원하기 때문에 Mac의 경우에는 가상머신에 설치가 된다.(`Mac의 경우에는 xhyve를 사용하고, Windows는 Hyper-V를 사용한다`)

<!-- more -->

```zsh
$docker version
```

<div align="center">
  <img src="/images/post_images/220601_docker_version.png" alt="docker version 확인">
</div>

client와 server가 출력되는 이유는 현재 Mac의 localhost에 docker server가 설치되고, 그 위에 client가 동작하고 있기 때문이다. 그래서 터미널에서 `작성한 명령어는 client server로 전달`이 되고, 반환된 결과는 client로 전달해서 출력을 하게 되는 것이다.
(`docker CLI는 docker host에 명령을 전달하고, 결과를 받아서 client에 출력한다.`)

<div align="center">
  <img src="/images/post_images/220601_docker_structure.webp" alt="docker 구조">
</div>

## <ins><b>Docker 기본 명령어</b></ins>

- ### **(1) run : 컨테이너 실행 명령**

  - ### **다양한 옵션**
    - `-d` : detached mode(터미널 상에서 계속 실행된 상태로 표시되지 않게 Background로 실행)
    - `-p` : host와 container의 Port를 정의해서 연결한다.(`host port:container port`)
    - `-v` : host와 container의 directory를 서로 mount
    - `-e` : container 내에서 사용할 환경변수를 설정
    - `--name` : container의 이름 설정
    - `--rm` : 프로세스 종료시 컨테이너를 자동으로 제거
    - `-it` : -i와 -t를 동시에 사용한 것으로, 터미널 입력을 위해 사용
    - `--network` : 네트워크를 연결하기 위해 사용

  ```zsh
  # [-v option 예시]

  $docker stop mysql
  $docker rm mysql
  # docker를 삭제하고, 다시 새로운 컨테이너를 띄우게 되면, 내부의 데이터도 같이 삭제된다. 따라서 컨테이너의 데이터 저장 directory를 localhost의 directory로 mount시켜서 데이터를 영구적으로 저장해서 사용할 수 있도록 해야한다.
  $docker run -d -p 3306:3306 \
    -e MYSQL_ALLOW_EMPTY_PASSWORD=true \
    --network=app-network \
    --name mysql \
    -v /Users/hyungi/Workspace/~:/var/lib/mysql \
    mysql:5.7
  ```

<br/>

- ### **(2) exec : run 명령어와는 달리 실행중인 도커 컨테이너에 접속할 때 사용된다. 보안상의 문제로 컨테이너 안에 ssh server를 설치하지 않고, edxec 명령어로 접속을 한다.**

<br/>

- ### **(3) ps : 현재 실행중인 docker 컨테이너 확인**

  - ### **다양한 옵션**

    - -a : 중지 상태인 docker 컨테이너 리스트 확인 (중지된 컨테이너의 경우에는 삭제되지 않고 남아있는 경우가 있기 때문에 반드시확인 후 rm 명령으로 삭제)

<br/>

- ### **(4) stop [한개 or 복수 개(space로 구분)] : 현재 실행중인 docker 컨테이너 중지**

<br/>

- ### **(5) rm : 종료된 docker 컨테이너를 완전히 제거**

<br/>

- ### **(6) log : 현재 실행중인 docker 컨테이너의 로그 확인(컨테이너가 정상적으로 동작하는지 확인하는 좋은 방법)**

  - ### **다양한 옵션**
    - -f : watch 상태로 새롭게 올라오는 로그 기록 follow

<br/>

- ### **(7) images : 도커가 다운로드한 이미지 목록을 출력**

<br/>

- ### **(8) pull : 이미지를 다운로드**

<br/>

- ### **(9) rmi : images 명령에서 출력된 컨테이너 리스트에서 이미지 ID를 사용해서 docker 컨테이너 이미지를 삭제(단, 실행중인 컨테이너가 사용하고 있는 이미지는 삭제되지 않음)**

<br/>

- ### **(10) network create : docker container끼리 이름으로 통신할 수 있는 가상 네트워크 생성**

  ```zsh
  # 생성한 network에 서로 연결할 container들을 연결시킨다. (bridge 역할)
  $docker network create [network name]

  # mysql, wordpress 연결
  # 기존에 생성된 mysql에 위에서 생성한 네트워크를 추가한다.
  $docker network connect app-network mysql

  # wordpress 실행시에 위에서 생성한 네트워크를 추가한다.
  # 같은 네트워크 상에 mysql이 떠있기 때문에 바로 연결할 수 있다.
  # 앞서 --name 옵션으로 mysql이라고이름을 지정해주었기 때문에 이름으로 접근을 할 수 있다.
  $ docker run -d -p 8080:80 \
    --network=app-network
    -e WORDPRESS_DB_HOST=mysql \
    -e WORDPRESS_DB_NAME=wp \
    -e WORDPRESS_DB_USER=wp \
    -e WORDPRESS_DB_PASSWORD=wp \
    wordpress
  ```

<br/>

<br/>

## <ins><b>docker-compose 명령</b></ins>

위의 docker의 기본 명령어를 입력할때에 띄어쓰기 등의 입력이 편하지는 않기 때문에 이러한 불편함을 개선하고자 docker-compose 명령이 생겨났다.

기본적으로 Mac에서 docker를 설치하면, docker-compose는 설치가 된다.

기존의 terminal에서 입력하였던 명령들을 docker-compose.yml 파일(`띄어쓰기로 데이터를 표현하는 방식`)에 작성하여 관리할 수 있다.

```yml
version: '2'
services:
  db:
    image: mysql:5.7
    volume:
      - ./mysql:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROO_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    image: wordpress:latest
    volumes:
      - ./wp:/var/www/html
    ports:
      - '8000:80'
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_PASSWORD: wordpress
```

restart는 container가 죽게 되면, docker가 자동으로 다시 띄워주도록 주는 옵션이다.

위와같이 작성된 docker-compose.yml파일은 아래의 명령어들을 사용해서 손쉽게 실행시킬 수 있다.

## <ins><b>docker-compose 기본 명령어</b></ins>

- ### **(1) up : docker compose를 이용하여 정의된 컨테이너 실행**

```zsh
$docker-compose up -d
```

<br/>

- ### **(2) down : docker compose를 이용하여 실행중인 컨테이너 종료하고 삭제**

```zsh
$docker-compose down
```

<br/>

- ### **(3) start : docker compose를 이용하여 중지상태인 컨테이너 시작**

```zsh
$docker-compose start #멈춘 모든 컨테이너를 재시작
$docker-compose start wordpress #지정한 특정 컨테이너만 시작
```

<br/>

- ### **(4) restart : docker compose를 이용하여 실행중인 컨테이너 재시작**

<br/>

- ### **(5) stop : docker compose를 이용하여 실행중인 컨테이너 중지**

<br/>

- ### **(6) logs : docker compose를 이용하여 실행중인 컨테이너의 로그 출력**
  -f 옵션으로 로그 follow 가능

<br/>

- ### **(7) ps : docker compose를 이용하여 실행중인 컨테이너의 목록 출력**

<br/>

- ### **(8) exec : docker compose를 이용하여 실행중인 컨테이너에서 명령어 실행**

```zsh
$docker-compose exec {container name} {command}

$docker-compose exec wordpress bash
```

<br/>

- ### **(9) build : docker 컨테이너에 정의된 build 부분의 내용만 빌드(단, image 대신 build로 선언된 컨테이너만 빌드된다)**

```zsh
$docker-compose build # build로 정의된 container(s) build

$docker-compose build wordpress #wordpress container만 build
```

## <ins><b>docker compose 기본 문법</b></ins>

- ### **version** : docker compose에 정의된 버전에 따라 지원하는 docker engine의 버전도 달라진다.

- ### **services** : 실행할 컨테이너를 정의한다. ($docker run --name [name] 명령과 동일)

- ### **image** : 컨테이너에 사용할 이미지 이름과 태그를 명기하고, 태그를 생략하면 latest, 이미지가 없으면 자동으로 pull(설치)한다.

- ### **ports** : 컨테이너와 연결할 localhost의 포트를 정의한다.{host port:container port}(-p 옵션과 동일)

- ### **environment** : 컨테이너에서 사용할 환경변수들을 정의한다. {환경변수 이름}:{값}

- ### **volumes** : mount하려는 디렉토리(들) {호스트 디렉토리}:{컨테이너 디렉토리}

- ### **restart** : 컨테이너를 재시작한다. restart의 옵션으로는 no, always, on-failure, unless-stopped가 있다.

- ### **build** : image 속성 대신에 사용하며, 이미지를 자체 빌드한 후에 사용한다. build 부분에서 정의할 dockerfile이 별도로 필요하다.
  image 정의대신에 build로 선언을 한 경우, `$docker-compose up --build`명령을 사용해서 도커 이미지를 다시 빌드하고 container를 실행할 수 있다.
  ```yml
  django:
    build:
      context: .
      dockerfile: ./compose/django/Dockerfile-dev
  ```

<br/>

## <ins><b>(실습1) /bin/sh 실행하기</b></ins>

```zsh
# ubuntu:2.04 container를 실행함과 동시에 /bin/sh 쉘 프롬프트를 띄운다. (terminal상에서 command 입력을 위해서 -it옵션 추가)
# container가 종료되면 자동으로 제거되도록 한다.
$docker run --rm -it ubuntu:2.04 /bin/sh
```

## <ins><b>(실습2) 워드 프레스 블로그 실행하기</b></ins>

wordpress를 실행하기 전에 MySQL을 우선 실행한다.

```zsh
$docker run -d -p 3306:3306 \
  -e MYSQL_ALLOW_EMPTY_PASSWORD=true\
  --name mysql \
  mysql:5.7

# 실행된 mysql container에 mysql 명령을 실행한다.
$docker exec -it mysql mysql
$create database wp CHARACTER SET utf8;
$grant all privileges on wp.* to wp@'%' identified by 'wp';
$flush privileges;
$quit
```

```zsh
# MySQL을 실행한 상태에서 워드 프레스 컨테이너를 실행한다.
$docker run -d -p 8080:80 \
  -e WORDPRESS_DB_HOST=host.docker.internal \
  -e WORDPRESS_DB_NAME=wp \
  -e WORDPRESS_DB_USER=wp \
  -e WORDPRESS_DB_PASSWORD=wp \
  wordpress
```

## <ins><b>(실습3) hashicorp 서버 실습</b></ins>

pc의 5678 포트가 hashicorp/http-echo 서버

```zsh
$docker run --rm -p 5678:5678 hashicorp/http-echo -text="hello world"
```

**curl** : 브라우저처럼 http에 접속을 해서 결과를 받아오는 명령어

```zsh
$ curl localhost:5678 #요청을 보내서 결과 출력
```

## <ins><b>(실습4) Redis 실행 실습</b></ins>

메모리 기반 데이터 베이스인 Redis를 docker 명령으로 간단하게 올릴 수 있다. (`port만 다르게 해서 복수 개의 Redis를 동일 서버에 올릴 수 있다`)

```zsh
$docker run --rm -p 1234:6379 redis

$telnet localhost 1234

$set hello world

$get hello
#$5
#world
$quit
```
