---
title: 210217 Python TIL (작성중...)
date: 2021-02-17 15:10:41
tags:
  - Python
  - Assignment
  - TIL
  - Incomplete
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

## 네트워크

## Web scraping 실습하기

- ### 환경설정

  1. **requests, beautifulsoup4, lxml, jupyter 설치하기**

  ```bash
  $ poetry add requests
  $ poetry add beautifulsoup4
  $ poetry add lxml
  $ poetry add —dev jupiter
  ```

  2. **jupyter notebook 환경에서 requests를 사용해서 Web Scrapping하기**

  ```python
  import requests

  url = 'https://api.kurly.com/v2/categories?ver=1'
  response = requests.get(url)
  response
  # response data를 json 형태로 변환하기
  response.json()

  """
  {'data': {'categories': [{'no': '907',
    'name': '채소',
    'show_all_flag': True,
    'pc_icon_url': 'https://img-cf.kurly.com/shop/data/category/icon_veggies_inactive_pc@2x.1586324570.png',
    'icon_url': 'https://img-cf.kurly.com/shop/data/category/icon_veggies_inactive@3x.1586324413.png',
    'pc_icon_active_url': 'https://img-cf.kurly.com/shop/data/
    ...(생략)...
  """
  ```

    <!-- more -->

  PostgreSQL 데이터베이스는 JSON데이터를 그냥 밀어넣을 수 있기 때문에 Django와 궁합이 좋게 사용된다.
  response로부터 취득한 text를 파싱하는 도구에는 'html.parser'과 'lxml'이 있는데, lxml은 c++로 만들어진 html text를 파싱해주는 라이브러리로 파싱하는 속도가 빠르다.

  ```python
  from bs4 import BeautifulSoup
  import lxml

  url = 'https://en.wikipedia.org/wiki/Coronavirus'
  resonse = requests.get(url)
  html_text = response.text
  soup = BeautifulSoup(html_text, 'lxml')
  ```

  3. Flask를 사용한 routing 실습

  ```python
  from flask import Flask

  app = Flask(__name__)

  @app.route('/')
  def index():
      return "flask works"

  @app.route('/items')
  def items():
      return "items will be shown shortly"

  if __name__=='__main__':
      app.run(host='localhost', port=8080, debug=True)
  ```

  4. templates/index.html, items.html 파일생성해서 렌더링하기

  ```python
  from flask import Flask, render_template

  app = Flask(__name__)

  @app.route('/')
  def index():
      # string을 렌더링한 html파일로 넘겨줄 수 있다.
      msg='hello'
      return render_template('index.html', msg=msg)

  @app.route('/items')
  def items():
      return render_template('items.html')

  if __name__=='__main__':
      app.run(host='localhost', port=8080, debug=True)
  ```
