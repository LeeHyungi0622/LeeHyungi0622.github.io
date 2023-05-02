---
title: 210127 Python + Self Development TIL - Function, *args, **kwargs, Recursive function
date: 2021-01-27 09:36:42
tags:
  - Python
  - TIL
  - Self-Development
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_plus_self_development_logo.png" alt="Python"/>
</div>

# **TO DO LIST**

- 백준 5397번 키로거 문제 (Pseudo code/손코딩 + 본 코딩)
  - https://leehyungi0622.github.io/2021/01/27/210127-Algorithm_baekjoon_5397/
- 210122 분 Git TIL 블로그 글 업데이트
  - https://leehyungi0622.github.io/2021/01/22/210122-git_til/
- 210128 분 Python + Self Development TIL 블로그 글 업데이트

# **Python**

## **오늘 수업을 통해 배운 내용**

- Python 메인 강의

  - 과제점검

    - Ethiopian Multiplication

      ```python
      result = 0
      print('{:7},{:7}, ,etc'.format('num1', 'num2'))
      while num > 0:
          if num1 % 2 == 0:
              print('{:7}, {:7}, Pass'.format(num1, num2))
          else:
              print('{:7}, {:7}, Keep'.format(num1, num2))
              result += num2
          num1 = num1//2
          num2 = num2*2
      print("The result is {}".format(result))
      ```

          <!-- more -->

    - Monty Hall Simulation

      ```python
      import random
      stay = 0
      switch = 0
      trial = int(input("How many times do you want?"))
      for _ in range(trial):
          # goats: 0, sportcar: 1
          doors = [0, 0, 1]
          random.shuffle(doors)
          # print(doors)
          users_choice = doors.pop()
          # print(users_choice, doors)
          doors.remove(0)
          if users_choice == 1:
              stay += 1
          else:
              switch += 1

      print("stay: {}, switch: {} for {}th trial".format(stay/trial, switch/trial, trial))
      ```

    <!-- more -->

  - ## **함수(function)**

    ```python
    def function(parameter):
        실행문1
        실행문2
        ...
        return output
    ```

    - function with input

    ```python
    # a, b는 parameter이다.
    def awe_sum(a, b):
        result = a + b
        return result
    a = 2
    b = 3
    # a, b는 함수의 실행단계에서 사용하는 argument이다.
    print(awe_sum(a, b))
    ```

    - function without input

    ```python
    def print_hello():
        return "hello"
    result_hello = print_hello()
    print(result_hello)
    ```

    - function with multiple return

    ```python
    def mul_return(a):
        b = a + 1
        return a,b
    ```

    - return skill

    ```python
    def id_check(id):
        if id == "admin":
            print("invalid id: admin")
            return
        print("valid id: ", id)
    ```

    - parameter with initialize

    ```python
    def say_hello(name="Fool", nick=True)
    print("Hi, ", name)
    if nick == True:
        print("But, you are Fool")
    else:
        print("Oh, you are not Fool")
    ```

    - arguments

    ```python
    def mul_sum(*args):
        # 넘겨받은 args는 튜플 자료형이다.
        sum = 0
        for i in args:
            sum += i
        return sum
    ```

    - keyword arguments :
      키와 값 타입으로 구성되어 있는 arguments이다.

    ```python
    def show_kwargs(**kwargs):
        print(str(kwargs))
    show_kwargs(a=10, b="google")
    # {'a': 10, 'b': 'google'}

    def get_user_info(**kwargs):
        print(kwargs, type(kwargs))
        for k,v in kwargs.items():
            print('{}: {}'.format(k, v))
        print('Your phone number is {}'.foramt(kwargs['phone']))
    ```

    - keyword arguments

    ```python
    # 사용자가 도메인을 입력한다.('www.google.com', naver, daum)
    # 앞에 프로토콜 붙여주기
    # path admin.html에 들어오면 된다.
    # query string을 마음대로 넘겨받으면 된다.
    # ex) uri_complete('www.google.com', utm_source='social', utm_media='campaign')
    # => https://www.google.com/admin.html?utm_source='social' & utm_media='campaign'

    # https://www.google.com/admin.html?utm_source=social&utm_media=campaign

    def uri_complete(domain, **query):
        uri = "https://"+domain+"/admin.html"
        for k in query.keys():
            if k == list(query.keys())[0]:
                uri += "?"
                uri += k+"="+query[k]
            if k != list(query.keys())[-1]:
                uri += "&"
        return uri

    print(uri_complete("www.google.com", utm_source='social', utm_media='campaign'))
    ```

    ```python
    # keyword arguments는 맨 앞에 위치할 수 없다. 맨 앞에 위치하면 그 뒤에 위치한 parameter에 값이 없다는 컴파일 에러가 발생한다. parameter상에서 initial value를 초기화할 것은 뒤에 정의해줘야 한다.
    def complete_uri(hostname,protocol='https', **query):
        url = '{}://{}/admin.html'.format(protocol,hostname)
        if not query:
            return url
        else:
            query_string = '?'
            for k, v in query.items():
                query_string += '{}={}&'.format(k,v)
            query_string = query_string[:-1]
        return url + query_string
    ```

    ```python
    def print_(*args, **kwargs):
        for arg in args:
            print(arg)
        for k, v in kwargs.items():
            print('{}={}'.format(k,v))
    ```

    ```python
    # **로 넘겨준 인자 중 지정되지 않은 나머지 인자에 대해 grouping 해서 표현할 수 있다. (key=value형태로 접근)
    def kwargs_url(server, port, **query):
        url = "https://"+server+":"+port+"?"
        for key in query.keys():
            url += key+"="+query[key]+"&"
        return url
    kwargs_url("localhost", "8080", utm_source="google", keyword="naver")
    ```

    - 외부 변수와 지역변수

    ```python
    x = "awesome"

    def myfunc():
        x = "fantastic"
        print("Python is " + x)
    myfunc()
    print("Python is " + x)
    # output:
    """
    # 지역변수 내에서 재 정의한 변수는 외부의 같은 이름의 변수에 변화를 주지 않는다.
    Python is fantastic
    Python is awesome
    """
    ```

    - global variable (전역변수)

      - use return

      ```python
      a = "hello"
      def glob_test(a):
          a += "world"
          return a

      a = glob_test(a)
      print(a)
      # output : helloworld
      ```

      - use global

      ```python
      # 안좋은 케이스 : 'global'이라는 명령을 사용하여 전역변수로 사용하게 되면, 함수는 독립성을 잃게 되어 함수가 외부 변수에 의존적이게 된다.
      a = "hello"
      def glob_test():
        global a
        a += "world"
        return a
      print(glob_test())
      # helloworld
      ```

      ## 간단한 연습문제

      1. Leap year(윤년) 구하는 문제

         > 사용자로부터 연도 (0~9999 사이의 정수)를 입력받아 4로 나뉘어 떨어지면 윤년, 100으로 나뉘어 떨어지면 평년, 400으로 나뉘어 떨어질땐 윤년을 출력하는 함수를 작성하시오.

      ```python
      def leap_year(year):
          if year % 400 == 0:
            return "Leap year"
          elif year % 100 == 0:
            return "Plain year"
          elif year % 4 == 0:
            return "Leap year"
          else:
            return "Plain year"

      y = int(input("연도(0~9999) 사이의 정수를 입력하시오."))

      print(leap_year(y))

      ```

      ```python
      leap = False
      def is_leap(y):
         if y % 4 == 0 and (y % 100 != 0 or y % 400 == 0):
             leap = True
      y = int(input("Is leap?? "))
      print(is_leap(y))
      ```

      2. numguess with function
         이전 시간에 작성한 numguess 문제를 함수화 시켜서 작성한다.

         ```python
         def guesser(guess):
            if guess == answer:
                print("Correct! The answer was ", str(answer))
                break
            else:
                print("That's not what I wanted! Try again!!")

         ```

      <br/>

  - ## **Recursive function(재귀함수)**

    재귀함수는 반복 호출하는 구조이기 때문에 함수 내에는 함수를 종료할 수 있는 탈출조건을 가지고 있어야 한다.

    ```python
    times_to_curse = int(input("How many times do you want to curse ? "))
    print(times_to_curse, type(times_to_curse))

    def recurse_to_beast(num):
        print('Abracadabra')
        print('Curse has no effect. {} times left.'.format(num-1))
        if num == 1:
            return "Beast is now dead"
        return recurse_to_beast(num-1)
    ```

    - Fibonacci(for-loop활용 풀이)

      ```python
      n = int(input())
      num = [0,1]
      for i in range(2, n+1):
          sum_ = num[i-1] + num[i-2]
          num.append(sum_)
      print(num[-1])
      ```

    - Fibonacci(재귀함수를 활용 풀이)
      ```python
      def Fibonacci(n):
          if n <= 1:
              return n
          else:
              return Fibonacci(n-1) + Fibonacci(n-2)
      print(Fibonacci(9))
      ```
    - Binet's Fibonacci formula :
      재귀호출을 하지 않고, Fibonacci를 한 번에 구하는 공식

      ```python
      from math import sqrt

      def binet_fibo(n):
        return ((1+sqrt(5))**n - (1-sqrt(5))**n)/(2**n*sqrt(5))

      print(int(binet_fibo(3)))
      ```

      ```python
      from time import time

      f_start_time = time()

      fibo(30)
      f_end_time = time()
      fibo_time = f_end_time - f_start_time
      print(fibo_time) # 6.294s

      b_start_time = time()
      binet(30)
      b_end_time = time()
      binet_time = b_end_time - b_start_time
      print(binet_time) # 3.155s

      ```

      피보나치 문제를 재귀함수로 풀이하면, 시간복잡도 O(N^2)만큼 걸리지만, 수학공식을 대입해서 구하는 경우, O(1) 상수만큼의 시간이 걸리기 때문에, 속도를 빠르게 하기 위해서는 공식을 활용한 처리가 유리하다.
      <br/>
