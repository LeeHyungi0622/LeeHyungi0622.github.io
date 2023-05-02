---
title: 220502 코딩 테스트 문제 리스트 Web crawling해보기
date: 2022-05-02 14:59:00
tags:
  - Python
  - Selenium
  - Pandas
  - Excel
categories:
  - Python
# hidden: true
# secret: true
---

<div align="center">
  <img src="/images/post_images/220502_web_crawling.png" alt="Web crawling">
</div>

이번 포스팅에서는 코테준비를 하기 위해서 코테문제를 풀다가 집중도 안되고, 원래 체크리스트를 만들어 놓고 하나씩 지워나가면서 하면 뭔가 성취감도 느끼고 해서 프로그래머스의 연습문제 페이지에 있는 문제 리스트를 `Selenium + BeautifulSoup + Pandas + Python`의 조합으로 빠르게 크롤링을 해서 엑셀파일로 뽑아보았다.

<!-- more -->

## <ins><b>206개의 코딩 테스트 문제</b></ins>

파이썬 관련 코딩테스트 문제를 뽑아보니 총 206개의 문제가 나왔다. Level1부터 5까지 전체를 뽑아본거라 난이도가 많이 어려운 문제도 있을거라고 생각되지만, 일단 다 풀어보는 것을 목표로 한 번 시작해보려고 한다.
아무래도 혼자 공부하다보니 성취감이나 동기부여가 필요한데, 푼 문제는 엑셀파일에 하나씩 지워가면서 진행해볼 생각이다.

```python
# -*- coding: utf-8 -*-
from selenium import webdriver
from selenium.webdriver.common.by import By  # CSS선택자 서치 담당
from selenium.webdriver.chrome.service import Service  # 크롬드라이버의 동작 시작, 끝 담당
from bs4 import BeautifulSoup
import time
import pandas as pd

PAGES = 13

options = webdriver.ChromeOptions()
options.add_experimental_option('excludeSwitches', ['enable-logging'])

# webdriver
# 크롬을 기준으로 현재 사용하고 있는 버전에 맞춰서 webdriver를 다운로드
service = Service(executable_path="./chromedriver")
browser = webdriver.Chrome(service=service, options=options)

url = 'https://programmers.co.kr/learn/challenges'

browser.get(url)

problems = []


def extractCodingTestProblems(search_result_items):
    for item in search_result_items:
        problem_item = dict()
        language_anchor = item.find("div", "languages").find_all("a", href=True)
        for lang in language_anchor:
            if "python" in lang.get('href'):
                title = item.find("h4", "title").text.replace('\n', '').strip()[5:].strip()
                level = item.find("span", "level-badge").text.strip()
                info = item.find("h6", "level").text.strip()
                problem_item['title'] = title
                problem_item['level'] = level
                problem_item['info'] = info
                problem_item['link'] = f"https://programmers.co.kr{lang.get('href')}"
                problems.append(problem_item)
                break


for idx in range(1, PAGES+1):
    pagination_btn = browser.find_element(By.CSS_SELECTOR, f"a[href='/learn/challenges/filter_lessons?page={idx}']")
    pagination_btn.click()

    time.sleep(1)

    html = browser.page_source
    soup = BeautifulSoup(html, 'html.parser')

    search_result = soup.find("div", "list-positions")
    search_result_items = search_result.find_all("div", "col-item")

    extractCodingTestProblems(search_result_items)

# Python의 List 타입의 데이터를 DataFrame으로 만들고, Excel 파일로 추출
problems_df = pd.DataFrame(problems)

problems_df.to_excel('./problems_excel.xlsx',sheet_name="Algorithm problems", index=True, header=True)

print(problems_df)
```
