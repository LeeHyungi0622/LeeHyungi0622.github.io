---
title: 210203 [SyntaxError] Non-ASCII Character
date: 2021-02-03 19:10:42
tags:
  - Python
  - Plotly
  - Pandas
  - Resolved-Error
categories:
  - Resolved-Error
---

<div align="center">
  <img src="/images/post_images/error_solved_img.png" alt="Resolve error"/>
</div>

<br/>

## Python과 Pandas를 활용하여 csv파일을 읽는 간단한 처리를 하던 중에 `Non-ASCII Character 에러`가 발생했다.

```zsh
SyntaxError: Non-ASCII character '\xec' in file /Users/hyungilee/Documents/dev/side-projects/corona-dashboard/pandas_practice.py on line 3, but no encoding declared; see http://python.org/dev/peps/pep-0263/ for details
```

Python의 경우, 기본적인 설정 상태에서 코드내에 한글이 있는 경우 코드 내의 한글을 Python 코드를 읽어들이지 못해서 위의 에러가 발생한다.

해결방법은 아래와 같이 파이썬 코드 맨 위 첫번째 혹은 두번째 줄에 한글 인코딩을 하도록 명령해주면 된다.

```python
# -*- coding: utf-8 -*-
# -*- coding: euc-kr -*-
```

  <!-- more -->
