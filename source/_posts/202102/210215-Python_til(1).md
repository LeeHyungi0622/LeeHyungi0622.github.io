---
title: 210215 Python TIL 1/2 - Module & Package, Library & Framework, Poetry, Virtualenv
date: 2021-02-15 13:06:41
tags:
  - Python
  - Assignment
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

## Module, Package

<br/>

- ### Module?

  - Python 코드로 이루어진 파일로, 본체에 대한 하위 단위로 정의한다.

- ### Package?

  - 관련 여러 모듈들을 하나의 폴더로 묶은 것을 말한다.

  **(Module, Package관련 실습내용)**
  (1) fibo.py와 main.py 파일을 만들어서 실습을 진행한다.
  fibo에 정의되어있는 text변수의 값을 main.py에서 출력한다.

  ```python
  from fibo import text

  print(text)
  ```

    <!-- more -->

  (2) fibo directory 내에 binet.py와 rec.py 파일을 생성해서 각 각에 맞는 함수를 구현한다. main.py에서 moulde로써 import해서 사용한다. (VSCode에서는 module로써 사용할 디렉토리내에는 \_\_init\_\_.py파일을 넣어준다.)

  ```python
  #from fibos import fibo
  #from fibos import binet as bn
  #from fibos import fibo, binet
  #from fibos import *
  #import print_something
  #from . import print_something
  ```

<br/>

- ### Library & Framework

  라이브러리와 프레임워크의 차이에 대해서 알아보았다.
  이전에 개인적으로 블로그에 포스팅했던 내용이 있는데, 참고해도 좋을 것 같다.
  [https://leehyungi0622.github.io/2021/02/06/202102/210206-Self-Framework_and_Library_Difference/](https://leehyungi0622.github.io/2021/02/06/202102/210206-Self-Framework_and_Library_Difference/)

  - **Library**

    - 대표적인 예로 jQuery가 있다.
    - 함수와 기능의 모음을 말한다.
    - 파이썬의 print(내장함수)와 math(모듈)등과 같은 파이썬 표준 라이브러리도 라이브러리에 속한다.
    - You are in control, you call the library

  - **Framework**

    - 대표적인 예로 django, flask, expressJS가 있다.
    - 개념들의 추상화를 제공하는 클래스와 컴포넌트로 구성되어 있다.
    - Framework is in control, it calls you

    ```python
    from flask import Flask

    app = Flask(__name__)

    @app.route('/')
    def get_index():
      return 'this is home'
    ```

<br/>

- ### **Poetry?**

`Node.js에서 package.json과 같은 역할을 하는 것이 Django의 requirements.txt인데, Poetry를 사용해서 requirements.txt의 관리를 좀 더 편하게 할 수 있다.`

- ### poetry 설치

  설치를 완료하고 poetry init 까지 완료되었다면, pyproject.toml config파일을 확인할 수 있다. 이 파일을 통해서 설치된 dependency를 관리할 수 있다.

  `pyproject.toml`

  ```bash
    [too.poetry.dependencies]
    python = "^3.9"
    # 프로젝트 실행을 위한 dependencies
    [tool.poetry.dev-dependencies]
    # 개발을 위한 dependencies
  ```

  **( 참고 :** [poetry installation reference](https://python-poetry.org/docs/) **)**

  ```bash
  # Poetry 설치하기
  $ curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python -

  # shell 설정파일에 poetry bin폴더의 경로를 설정
  $ echo '$HOME/.poetry/bin:$PATH' >> ~/.zshrc
  ```

  <ins><b>zsh configuration 파일에 poetry bin폴더의 경로를 설정 넣어주기</b></ins>

  ```bash
  # pyenv의 PATH
  export PYENV_PATH=$HOME/.pyenv
  if which pyenv > /dev/null; then eval "$(pyenv init -)"; fi
  if which pyenv-virtualenv-init > /dev/null; then eval "$(pyenv virtualenv-init -)"; fi
  # poetry실행파일의 PATH가 pyenv의 PATH보다 우선되도록 설정
  export PATH=$HOME/.poetry/bin:$PATH
  ```

  `.commonrc 파일을 생성해서 관리를 하면 공통적인 개발환경을 관리하기에 좋다.`
  → 개발환경은 global하게 사용할 가상환경과 local하게 프로젝트별로 사용할 가상환경을 구분해서 설정해놓는 것이 좋다.

  pyproject.toml 파일이 있는 위치에서 `poetry add django==1.11`로 django package를 설치해주면 아래와 같이 설치된 package를 확인할 수 있다.

  ```bash
  [tool.poetry]
  name = "practice_project"
  version = "0.1.0"
  description = ""
  authors = ["Lee Hyungi <hyungi.lee.622@gmail.com>"]

  [tool.poetry.dependencies]
  # ^표시는 2.7이상의 python에서 실행해야 된다는 의미이다.
  python = "^2.7"
  Django = "1.11"

  [tool.poetry.dev-dependencies]

  [build-system]
  requires = ["poetry-core>=1.0.0"]
  build-backend = "poetry.core.masonry.api"
  ```

  <br/>

  - ### Poetry 사용관련 Commands

  ```bash
  # initialize
  $ poetry init
  # add package
  $ poetry add <package name>
  # remove package
  $ poetry remove <package name>
  # show installed package
  $ poetry show --no-dev --tree
  # export package list to requirements.txt
  $ poetry export -f requirements.txt > requirements.txt
  ```

  <br/>

  - ### Poetry 실습 순서

  ```bash
  (1) 가상환경 생성하기
  (2) 프로젝트 디렉토리 만들기
  (3) poetry init
  (4) poetry add <package name>

  requests
  flask
  pillow
  beautifulsoup4
  locust( poetry add --dev locust )

  ```

  가상환경을 생성할때에는 pyenv version번호를 virtualenv 이름에 넣어 생성해준다. (이름으로 어떤 버전이 설치되었는지 쉽게 유추할 수 있게 하기 위해)

  ```bash
  # awesome-390이름의 가상환경을 pyenv로 설치한 3.9.0로 생성해준다.
  $ virtualenv 3.9.0 awesome-390
  ```
