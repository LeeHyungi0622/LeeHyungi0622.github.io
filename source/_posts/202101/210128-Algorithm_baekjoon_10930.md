---
title: Baekjoon Online Judge 10930번 Hash String 변환문제
date: 2021-01-28 11:06:42
tags:
  - Algorithm
  - PseudoCode
  - BaekjoonOnlineJudge
categories:
  - Algorithm-problem-solving
---

## 백준 저지 10930번 Hash String 변환문제 Pseudo code + Python code

![](/images/post_images/210128_baekjoon_10930.png)

```python
import hashlib

input_string = input()
encoded_input_string = input_string.encode()
hash_string = hashlib.sha256(encoded_input_string).hexdigest()
print(hash_string)
```

  <!-- more -->

이 문제는 입력받은 문자열을 hashlib를 활용하여 Hash string으로 변환하는 문제이다. <br/>
입력받은 문자열은 encode를 한 뒤에 Python의 hashlib에서 지원하는 sha256 함수를 사용하여 처리를 해준 다음에, hex decimal 값만을 포함하는 문자열을 생성하기 위해 hexdigest()로 처리를 해준다.

## ![](/images/post_images/210124_developer.jpg)
