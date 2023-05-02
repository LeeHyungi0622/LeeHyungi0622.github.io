---
title: 210204 Python Regular Expression Assignment
date: 2021-02-04 15:02:41
tags:
  - Python
  - Assignment
  - HackerRank
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/hackerrank-logo.jpg" alt="HackerRank"/>
</div>

<br/>

원래 HackerRank 문제의 경우, 별도로 풀이를 정리하지는 않지만, 이번에 정규표현식의 경우, 헷갈리는 부분과 새롭게 공부하게 된 내용이 있어서 별도로 정리를 해본다.

## 문제 1

[**Problem1 Link(HackerRank)**](https://www.hackerrank.com/challenges/re-findall-re-finditer/problem)

이 문제는 앞 뒤로는 자음만 위치하고, 가운데에는 2개 이상의 모음으로만 구성되어 있는 문자를 정규표현식을 통해 출력하는 문제이다.

> Task You are given a string . It consists of alphanumeric characters, spaces and symbols(+, -). Your task is to find all the substrings of that contains or more vowels. Also, these substrings must lie in between consonants and should contain vowels only.

우선 문제를 풀이하기에 앞서 다음 **긍정/부정예측(positive/negative lookahead assertion)**, **긍정 후 읽기**에 대한 개념에 대해서 알아보자.

### 긍정적인 예측

`x?=y` : 이 패턴은 직후에 y가 존재하는 문자열 x에 match한다.

```python
foo(?=bar)
# 직후에 bar가 있는 foo에 일치한다.
```

### 부정적인 예측

`x?!y` : 이 패턴은 직후에 y가 없는 문자열 x에 match한다.

```python
foo(?!bar)
# 직후에 bar가 없는 foo에 일치한다.
```

  <!-- more -->

### 긍정 후 읽기

`x?<=y` : 이 패턴은 직전에 문자열 x가 있는 문자열 y(x는 포함하지 않음)에 일치한다.

```python
(?<=bar)foo
# 직전에 bar가 있는 foo(bar는 포함하지 않음)에 일치한다.
```

### 부정 후 읽기

`x?<!y` : 이 패턴은 직전에 x가 없는 문자열 y에 일치한다.

```python
(?<!bar)foo
# 직전에 bar가 없는 foo(bar는 포함하지 않음)에 일치한다.
```

### **본 코드**

```python
import re

result = re.findall(
r'(?<=[QWRTYPSDFGHJKLZXCVBNMqwrtypsdfghjklzxcvbnm])([aeiouAEIOU]{2,})(?=[QWRTYPSDFGHJKLZXCVBNMqwrtypsdfghjklzxcvbnm])', input())

if result:
    print(*result, end='\n')
else:
    print(-1)
```

```python
find_object = re.finditer(
    r'(?<=[QWRTYPSDFGHJKLZXCVBNMqwrtypsdfghjklzxcvbnm])([aeiouAEIOU]{2,})(?=[QWRTYPSDFGHJKLZXCVBNMqwrtypsdfghjklzxcvbnm])', input()
)

for i in map(lambda x: x.group(), find_object):
    print(i)
```

<br/>

## 문제 2

[**Problem2 Link(HackerRank)**](https://www.hackerrank.com/challenges/re-sub-regex-substitution/problem)

이 문제는 입력받은 multiline statements에서 각 문자열에 &&가 있으면 and로, ||가 있으면 or로 변환하는 문제였다.
이 문제를 풀때 처음에는 [\s]로 앞/뒤 공백을 구분하려고 했는데 예상 결과값으로 출력이 안되어, (**(?<=[white space]**), **(?=[white space])**)로 다시 정규표현을 작성하여 풀이하였다.

> You are given a text of lines. The text contains && and || symbols. <br/> Your task is to modify those symbols to the following:
> `Both && and || should have a space " " on both sides.`
>
> > && → and
> > || → or

### 본 코드

```python
import re

def cvtLogicalOperator(match):
    if match.group(0) == "||":
        return "or"
    elif match.group(0) == "&&":
        return "and"


N = int(input())
statement = []
for _ in range(N):
    statement.append(input())

statement = '\n'.join(statement)

print(re.sub(r"(?<= )(&&|\|\|)(?= )", cvtLogicalOperator, statement))
```

<br/>

## 문제 3

[**Problem3 Link(HackerRank)**](https://www.hackerrank.com/challenges/validating-named-email-addresses/problem)

이 문제는 입력받은 이메일 주소를 정규표현식(regular expression)을 사용해서 유효성 검사를 하는 문제이다.
이 문제에서는 **이전에 배운 meta character를 다양하게 사용해 볼 수 있다.**

> The first line contains a single integer, , denoting the number of email address. <br/> Each line of the subsequent lines contains a name and an email address as two space-separated values following this format:
>
> `name <user@email.com>`

### 본 코드

```python
import re
import email.utils

n = int(input())

for _ in range(n):
    name, email = map(str, input().split())
    # < 특수문자로 ^(시작)하고, \w(alphanumeric character),
    # .을 문자 자체로 입력하기 위해 \(back slash)와 같이 입력,
    # 끝은 >로 끝나기 때문에 끝 문자 뒤에 $ 메타문자를 입력해서 처리해준다.
    ptn = r"^<[a-zA-Z](\w|-|\.|_)+@[a-zA-Z]+\.+[a-zA-Z]{1,3}>$"
    if re.match(ptn, email):
        print(name, email)
```

<br/>

## 문제 4

[**Problem4 Link(HackerRank)**](https://www.hackerrank.com/challenges/validating-the-phone-number/problem)

이문제는 입력받은 전화번호를 정규표현식을 사용해서 유효성 검사를 하는 문제이다.
문제에서 입력받는 전화번호의 조건은 아래와 같다.

> **condition1)** 전화번호는 총 10자리 수로 구성이 된다.
> **condition2)** 앞 자리는 7, 8, 9로 시작한다.
> **condition3)** 나머지 자리수는 숫자(\d)로 구성된다.

### 본 코드

```python
test_case = int(input())
for _ in range(test_case):
    phone_number = input()
    ptn = r"[7-9][0-9]{9}"
    print('YES' if len(phone_number) == 10 and re.match(ptn, phone_number) else 'NO')
```
