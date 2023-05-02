---
title: 210217 Development environment setting(pyenv, virtualenv, autoenv, pip, poetry 사용법)
date: 2021-02-17 17:30:00
tags:
  - Self-Development
  - Pyenv
  - Virtualenv
  - Autoenv
categories:
  - Dev-Environment
---

![](/images/post_images/210217_virtualenv.png)

이번 포스팅에서는 파이썬 가상개발환경을 구성하는데 사용되는 `pyenv, virtualenv, autoenv, pip, poetry`의 사용법에 대해서 정리를 해보겠다.

우선 가상개발환경을 구축해서 개발환경을 분리하는 이유에 대해서 알아보자.

### **왜 개발환경을 가상개발환경으로써 분리해서 관리해야 하는가?**

파이썬에는 다양한 버전이 존재하고, 각 각의 파이썬 어플리케이션에서 사용되는 파이썬의 버전 또한 다양하기 때문에 가상개발환경을 구축해서 베이스가 되는 파이썬의 버전을 다르게 설정해서 관리해야 한다.
그리고 파이썬은 많은 패키지들이 배포되고 있기 때문에 이러한 패키지들을 한 대의 컴퓨터에서 돌릴경우 `Python runtime의 version과 Python libray가 서로 충돌을 하는 문제가 발생`한다.
이러한 문제는 기본적으로 한 대의 컴퓨터에 런타임 및 라이브러리가 전역적으로 설치가 되어 사용이 되기때문에 발생한다.

### **그럼 해결책은?**

  <!-- more -->

<ins><b>앞에서 언급한 문제점을 해결하기 위해서는 Python application별로 독립적인 가상개발환경을 구축함으로써 해결할 수 있다.</b></isn><br/>

가상개발환경을 구축하기 위해 필요한 것이 이번 포스팅의 주제인 `pyenv, virtualenv, autoenv, pip`와 같은 툴이 필요하다.<br/>

자 그럼 각 각의 툴들에 대해서 정리를 해보자.

- **(1)pyenv**

  - 파이썬 버전을 관리하는 툴로 하나의 컴퓨터에 다양한 파이썬 버전을 설치하고 관리할 수 있게 해준다.
  - **xcode command line tool과 zlib 설치**
    Mac환경에서 pyenv를 설치하기 위해서는 xcode command line tool과 zlib의 설치가 필요하다.

    ```bash
    $ sudo xcode-select --install
    $ brew install homebrew/dupes/zlib
    ```

  - **homebrew를 이용한 pyenv 설치 및 업그레이드**

    ```bash
    $ brew install pyenv # installation
    $ brew upgrade pyenv # update
    ```

  - **환경변수 등록 및 업데이트(zsh기준)**

    ```bash
    $ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.zshrc
    $ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.zshrc
    $ echo 'eval "$(pyenv init -)"' >> ~/.zshrc
    $ source ~/.zshrc
    ```

  - **설치된 pyenv 점검**

    ```bash
    # 설치된 python version list확인
    $ pyenv install --list
    # 설치된 pyenv의 버전확인
    $ pyenv --version
    ```

  - **pyenv 사용방법**

    ```bash
    pyenv [서브 명령] [parameters]
    ```

  <table>
    <tr align="center">
      <td><b>서브 명령 옵션</b></td>
      <td><b>설명</b></td>
    </tr>
    <tr align="center">
      <td>local</td>
      <td>현재 directory의 python version 확인 및 지정</td>
    </tr>
    <tr align="center">
      <td>global {version}</td>
      <td>전역으로 설정된 python의 버전 확인 및 변경(+version option)</td>
    </tr>
    <tr align="center">
      <td>install {version}</td>
      <td>파이썬 버전을 설치</td>
    </tr>
    <tr align="center">
      <td>uninstall</td>
      <td>지정한 파이썬 버전을 삭제</td>
    </tr>
    <tr align="center">
      <td>version</td>
      <td>현재 활성화된 파이썬 버전 출력</td>
    </tr>
    <tr align="center">
      <td>versions</td>
      <td>pyenv로 설치되어 이용가능한 버전을 출력</td>
    </tr>
  </table>

  - **pyenv 전역설정**

    ```bash
    $ pyenv versions  # pyenv 활성 버전 확인
    $ python --version  # 현재 파이썬 버전확인
    $ pyenv global 3.5.3  # 글로벌 파이썬 설정 변경
    $ python --version   # 현재 파이썬 버전확인
    ```

  - **pyenv 로컬설정**
    특정 디렉토리에 활성화되는 파이썬의 버전을 지정한다. 기본적으로 파이썬 버전이 지정되지 않은 디렉토리에는 global 설정이 적용된다.

    ```bash
    # 특정 디렉토리를 만들고, 디렉토리 안에서 pyenv local [version]으로 local 설정한다.
    # 그럼 폴더 내부에 .python-version 파일이 생성되어있음을 확인할 수 있다.
    $ pyenv local 2.7.13

    ```

  <br/>

- **(2)virtualenv & pyenv-virtualenv**

  - virtualenv는 파이썬 환경을 격리하는 툴이고, pyenv-virtualenv는 virtualenv의 pyenv 확장 플러그인이다.
    앞에서 살펴본 `pyenv를 이용하게 되면` 컴퓨터에 파이썬 버전별로 한 개의 파이썬 runtime으르 설치하고 관리할 수 있다. 그리고 `virtualenv를 사용하면` 파이썬 버전을 세분화하여 여러 개발환경으로 구분하여 관리하는 것이 가능해진다.
    여기서 말하는 버전의 세분화는 <ins><b>한 개의 파이썬 버전에 여러개의 런타임을 구성하고 그 각 각의 런타임을 각기 다른 어플리케이션에 할당한다는 의미</b></ins>이다.
    따라서 pyenv와 virtualenv를 같이 사용하면 효과적이기 때문에 pyenv-virtualenv 플러그인을 사용하는 것이 일반적이다.

  - **pyenv-virtualenv 설치**

    ```bash
    $ brew install pyenv-virtualenv
    ```

    기본적으로 pyenv로 파이썬을 설치하면 .pyenv/versions 디렉토리에 각 각의 설치된 버전들이 관리가 되고, 파이썬 런타임이 관리가 된다. pyenv의 관리 최소단위는 파이썬 버전이고, virtualenv를 사용하면 동일한 파이썬 버전을 여러 개의 개별적인 환경으로 구분하여 관리한다.

  - **pyenv-virtualenv 환경변수 설정(zsh기준)**

    ```bash
    $ echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc
    ```

  - **<ins>(수업시간에 배운 내용)virtualenv를 사용해서도 pyenv로 설치한 python 버전을 지정해서 가상환경을 생성할 수 있다.</ins>**
    pyenv로 설치한 python버전을 지정해서 새로운 가상환경 설치

    ```bash
    # virtualenv name은 사용하는 python 버전이 유추 가능하게 작성하는 것이 좋다.
    $ virtualenv [python version] [virtualenv name]
    $ virtualenv 3.9.0 awesome-390
    ```

  - **pyenv-virtualenv 사용**
    virtualenv 플러그인을 사용하면 버전(옵션)과 추가적인 이름을 넣어서 파이썬의 런타임을 관리할 수 있다.
    pyenv-virtualenv의 특징은 python 프로젝트마다 각 각의 가상환경을 제공해준다는 점이다.

    ```bash
    # 버전은 샹략 가능하며, 버전을 생략할 경우 현재 시스템 버전으로 가상환경이 설정된다. 이렇게 생성된 가상환경의 경우, activate deactivate 명령을 사용해서 활성화/비활성화를 할 수 있다.
    $ pyenv virtualenv <vertualenv-name>
    $ pyenv virtualenv 2.7.10 my-virtual-env-2.7.10
    # 가상개발환경 활성화
    $ pyenv activate <vertualenv-name>
    # 가상개발환경 비활성화
    $ pyenv deactivate
    # 가상개발환경 삭제
    $ pyenv uninstall <vertualenv-name>
    ```

  - **가상환경생성 실습**

    ```bash
    $ pyenv version # 현재 버전확인
    $ pyenv versions # 설치 버전확인
    $ pyenv virtualenv 2.7.13 awesome-2713 # awesome-2713 가상환경 생성
    $ pyenv activate awesome-2713 # 생성한 가상환경 activate
    $ python -V # awesome-2713 가상환경에서의 파이썬 버전확인
    $ pyenv deactivate # awesome-2713 가상환경 비 활성화
    $ python -V # 현재 전역으로 설정되어 있는 파이썬 버전확인
    # 설치 버전확인
    $ pyenv versions
    #2.7.13/envs/awesome-2713
    #*system
    # 생성한 가상환경 제거하기
    $ pyenv uninstall 2.7.13/envs/awesome-2713
    ```

  <br/>

- **(3)autoenv**

  - 앞서 살펴본 pyenv-virtualenv를 사용하면 새로 생성한 가상환경을 활성화 하기 위해 `pyenv activate`를 해줘야하는 번거로움이 있다. 이러한 번거로움을 autoenv를 사용해서 Python 프로젝트 진입시점에 자동으로 virtualenv환경을 로딩하는 기능을 제공한다.

  - **autoenv 설치 및 환경변수 설정(zsh 기준)**

    ```bash
    $ brew install autoenv
    $ echo "source $(brew --prefix autoenv)/activate.sh" >> ~/.zshrc
    $ source ~/.zshrc
    ```

  - **autoenv 실습하기**
    새롭게 진행할 프로젝트를 위한 디렉토리를 생성하고, `.env 파일을 추가`한다.
    이렇게 추가를 해주게 되면, 해당 프로젝트 디렉토리로 이동시 자동으로 해당 가상개발환경으로 activate된다.

    **.env**

    ```bash
    echo “***********************************”
    echo “Python Virtual Env > [가상환경이름]”
    echo “***********************************”

    pyenv shell [가상환경이름]
    pyenv activate
    ```

  <br/>

- **(4)pip**

  - pip는 파이썬 패키지(라이브러리)를 관리하는 프로그램으로, 라이브러리를 설치할때 의존성을 갖는 모든 라이브러리를 자동으로 설치를 해주며, 개별 버전관리의 기능도 제공한다. (`파이썬 2.7.9 버전 이후와 파이썬 3.4 버전 이상에는 기본적으로 설치`)

  - pip버전 업데이트
    ```bash
    $ sudo pip install -U pip
    ```
  - pip package list 관리하기
    pip로 현재 사용중인 파이썬 환경의 모든 라이브러리를 조회하여 **requirement 파일**을 만드는 기능을 제공한다.
    `bash # 현재 파이썬 환경에 설치된 모든 라이브러리를 requirements.txt로 export하기 $ pip freeze > requirements.txt `

    <br/>

- **(5)poetry**
  앞서 requirements.txt를 활용하여 파이썬의 패키지를 관리할 수도 있지만, poetry라는 툴을 사용하면 좀 더 간편하게 파이썬의 패키지를 관리할 수 있다.
  poetry는 파이썬 의존성 관리툴로, `단순 의존성 관리를 넘어서 poetry.lock을 사용해서 프로젝트의 의존성을 다른 환경에서도 동일하게 유지`해줄 수도 있다.
  그리고 `build와 publish도 지원`을 해주고 있다.
  또한 사용법이 npm과 비슷한 부분이 많아서 npm을 사용해봤다면 빨리 익숙해질 수 있을 것 같다.
  - <ins><b>[참고]Poetry command와 관련 실습내용은 일전에 수업시간에 배운 내용들과 함께 정리해뒀다.</b></ins>
    → [https://leehyungi0622.github.io/2021/02/15/202102/210215-Python_til(1)/](<https://leehyungi0622.github.io/2021/02/15/202102/210215-Python_til(1)/>)
