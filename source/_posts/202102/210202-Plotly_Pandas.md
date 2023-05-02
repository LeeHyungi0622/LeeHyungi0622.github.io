---
title: 210202 Plotly + Pandas Project
date: 2021-02-02 19:52:42
tags:
  - Python
  - Plotly
  - Pandas
categories:
  - Pandas
---

<div align="center">
  <img src="/images/post_images/210202_pandas_plotly_logo.jpeg" alt="Plotly+Pandas"/>
</div>

<br/>

- # Pandas

  - 방대한 양의 데이터를 가져와서 데이터를 깔끔하게 만들고, 처리하고, 구조를 변경(데이터 가공)하는데 사용된다.

  - 데이터 분석과 처리를 쉽게 할 수 있도록 도와준다.

  ```bash
  $ pip install pandas
  ```

<br/>

- # Plotly

  - 모든 그래픽(그래프)를 만드는데에 사용된다.
  - No JavaScript required

  ```bash
  $ pip install dash==1.14.0
  ```

    <!-- more -->

<br/>

- # Project setting

  ## virtualenv & virtualenvwrapper 설치 및 환경설정

  - virtualenvwrapper는 virtualenv 도구에 대한 확장 세트이기 때문에 우선적으로 `virtualenv`를 설치해줘야 한다.

    ```python
    $ pip install virtualenv
    ```

  - virtualenv의 위치에 대한 환경설정을 해줘야 한다.

    ```python
    $ export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
    ```

    [virtualenvwrapper reference](https://virtualenvwrapper.readthedocs.io/en/latest/)

  - **virtualenvwrapper** : virtualenvwrapper는 lan Bicking의 `virtualenv 도구에 대한 확장 세트`이다. 확장된 기능에는 가상 환경을 생성 및 삭제하고 개발 work flow를 관리하기 위한 wrapper가 포함되어있어 종속성에 충돌을 일으키지 않고 한 번에 둘 이상의 프로젝트에서 더 쉽게 작업을 할 수 있다.

    ```bash
    $ pip install virtualenvwrapper
    ...
    $ export WORKON_HOME=~/Envs
    $ mkdir -p $WORKON_HOME
    $ source /usr/local/bin/virtualenvwrapper.sh
    $ mkvirtualenv env1
    Installing
    setuptools..........................................
    ....................................................
    ....................................................
    ...............................done.
    virtualenvwrapper.user_scripts Creating /Users/dhellmann/Envs/env1/bin/predeactivate
    virtualenvwrapper.user_scripts Creating /Users/dhellmann/Envs/env1/bin/postdeactivate
    virtualenvwrapper.user_scripts Creating /Users/dhellmann/Envs/env1/bin/preactivate
    virtualenvwrapper.user_scripts Creating /Users/dhellmann/Envs/env1/bin/postactivate  New python executable in env1/bin/python
    (env1)$ ls $WORKON_HOME
    env1 hook.log
    ```

    - zsh환경설정 파일에 virtualenv 관련 환경설정 넣기 (`~/.zshrc`)

    ```bash
    # Setting PATH for Python 3 installed by brew
    export PATH=/usr/local/share/python:$PATH

    # Configuration for virtualenv
    export WORKON_HOME=$HOME/.virtualenvs
    export VIRTUALENVWRAPPER_PYTHON=/usr/local/bin/python3
    export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
    source /usr/local/bin/virtualenvwrapper.sh
    ```

    [virtualenv setting](https://stackoverflow.com/questions/49470367/install-virtualenv-and-virtualenvwrapper-on-macos)

    - env1 가상 환경에 설치된 패키지 리스트 확인하기

    ```bash
    (env1)$ lssitepackages
    ```

    - 가상환경 간 전환하기

    ```bash
    $ workon env2
    ```

    가상환경으로 설정을 변경하면 VSCode에서 Python관련 파일을 생성시, 자동으로 Interpreter설정을 바꾸는 알람이 뜨는데, 이곳에서 가상환경의 Python interpreter를 선택할 수 있다.
    `~/.virtualenvs/env1/bin/python`

  ## Jupyterlab 설치

  ```bash
  $ pip install jupyterlab
  ```
