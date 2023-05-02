---
title: 210219 [PyenvError] Pyenv BUILD FAILED ERROR
date: 2021-02-19 20:15:42
tags:
  - Python
  - Pyenv
  - Installation
  - Virtualenv
categories:
  - Resolved-Error
---

![](/images/post_images/error_solved_img.png)

<br/>

## Pyenv로 새로운 Python version 설치시에 BUILD FAILED 에러 발생

이번에 프로젝트를 진행할때에는 Pyenv를 사용해서 프로젝트별로 파이썬 버전을 다르게 적용하고, Virtualenv로 별도의 가상환경을 구축하여 진행하였다. 문제가 발생한 시점은 Pyenv로 사용할 파이썬 버전을 설치하려고 하는데 아래와 같은 `BUILD FAILED`에러가 발생하였다.

### <ins><b>문제(Issue)</b></ins>

![](/images/post_images/210219_pyenv_build_fail_capture.png)

  <!-- more -->

### <ins><b>해결책(Solution)</b></ins>

우선 에러를 보고 어떤 문제인지 이해해보았다.

`python-build: use zlib from xcode sdk` 부분 이후에 build failed error가 발생한 것을 보아 뭔가 zlib의 설치에 문제가 있거나 시스템에 설정되어 있는 xcode의 sdk 환경변수 설정이 잘못되어있는 것이 아닌가하는 의심을 하게 되었다.

그 다음으로 개발자 커뮤니티에서 나와 같은 에러를 경험하고 해결한 사람이 있을꺼라는 생각에 구글링을 해보았다.
완전 똑같은 에러 메시지는 아니었지만, 유사에러에 대한 여러 솔루션들을 stackoverflow와 같은 개발관련 커뮤니티에서 찾을 수 있었다.

[https://github.com/pyenv/pyenv/issues/1746](https://github.com/pyenv/pyenv/issues/1746)

대부분의 사람들이 말하기를, XCode를 설치하게 되면 기본적으로 zlib은 설치가 되기때문에 근본적인 문제는 zlib에 있지 않고, 새롭게 업데이트 된 Mac OS인 `Big Sur로의 업데이트에 따른 XCode의 새로운 업데이트가 이 문제를 야기`했다고 말한다.

나는 다양한 솔루션들 중에서 하나인 아래의 명령을 복사해서 pyenv install --patch 뒤에 있는 버전만 내가 설치하고자 하는 파이썬의 버전으로 변경해서 설치를 하였다. 결과적으로 문제없이 설치가 된 것을 확인할 수 있었다.

```bash
CFLAGS="-I$(brew --prefix openssl)/include -I$(brew --prefix bzip2)/include -I$(brew --prefix readline)/include -I$(xcrun --show-sdk-path)/usr/include" LDFLAGS="-L$(brew --prefix openssl)/lib -L$(brew --prefix readline)/lib -L$(brew --prefix zlib)/lib -L$(brew --prefix bzip2)/lib" pyenv install --patch 3.6.12 < <(curl -sSL https://github.com/python/cpython/commit/8ea6353.patch\?full_index\=1)
```
