---
title: 220808 FastAPI, ASGI, Uvicorn
date: 2022-08-08 08:11:00
tags:
  - Python
categories:
  - Python
hidden: true
secret: true
---

<div align="center">
  <img src="/images/post_images/" alt="">
</div>

<br/>
<br/>

## <ins><b>FastAPI?</b></ins>
FastAPI는 Python web framework로, API를 만들 수 있고, Python 버전은 3.6이상에서 적용이 가능하다. 그리고 Uvicorn ASGI Server를 사용한다.

## <ins><b>ASGI(Asynchronous Server Gateway Interface)</b></ins>
ASGI는 비동기 웹 서버를 의미하고, DB나 API 연동 과정에 발생하는 대기시간을 없이 CPU가 복수의 작업을 할 수 있도록 하는 방식을 말한다. 동기 방식은 WSGL을 사용한다.

## <ins><b>WSGI(Web Server Gateway Interface)</b></ins>

WSGI는 ASGI가 나오기 전에 사용되던 것인데, 비동기적인 처리를 제공하기 위해서 ASGI가 등장하였다. 사용자가 서버에 요청을 보내면, 해당 요청에 대해서 결과를 반환하게 되는데, 서버에서 만든 API 프로그램이 작동할 수 있도록 `서버와 API 프로그램을 연결해주는 입구 역할`을 해주는 것이 바로 ASGI(WSGI)이다.

ASGI는 작성한 FastAPI 애플리케이션 프로그램의 실행 결과를 웹 서버에 전달해주고, 웹 서버는 ASGI로부터 전달받은 응답 결과를 웹 클라이언트인 브라우저에 전송을 한다. 

### **Uvicorn(프로세스 관리자, 실행기)**

uvloop과 httptools를 사용하여 ASGI 서버를 구현한 구현체이다. 다시말해, Uvicorn은 ASGI 웹 애플리케이션을 실행하는 서버이다.

<!-- more -->

## <ins><b>Linter, Formatter 설정</b></ins>
Linter: flake8
Formatter: Black

## <ins><b>FastAPI 및 Uvicorn 설치</b></ins>

```zsh
$pip install fastapi
$pip install uvicorn
```

## <ins><b>프로젝트 폴더 구성</b></ins>

실행할 FastAPI 소스코드는 생성한 별도의 app 폴더 하위에 위치하고, root path에 있는 server.py에서는 app 폴더 하위의 FastAPI 코드를 실행해주는 역할을 하게 된다.

```zsh
# uvicorn을 사용해서 app폴더 하위의 main.py(app.main) app을 실행한다는 의미
$uvicorn app.main:app --reload 
```

Uvicorn은 ASGI의 구현체로, FastAPI로 작성된 애플리케이션을 실행시켜주는 역할을 한다.

## <ins><b>jinja2 및 aiofiles 설치</b></ins>

```zsh
$pip install jinja2
$pip install aiofiles
```

## <ins><b>Fast API + MongoDB</b></ins>

(1) Fast API와 MongoDB를 연결하기 위해서 odmantic을 사용한다.

(2) models 디렉토리를 사용해서 추상화를 한다.

(3) data model을 작성한다.

(4) DB에 데이터를 삽입한다.
