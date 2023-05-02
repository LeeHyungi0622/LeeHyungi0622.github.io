---
title: 210215 Python TIL 2/2 - Function, Closure, Decorator
date: 2021-02-15 20:35:41
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

## Function = First-class function

- 파이썬에서는 함수를 일급 시민(first class citizen)으로 취급한다.
  아래의 예시코드를 보면, 함수에서 또 다른 함수를 반환할 수 있으며, 함수에서 반환하는 lambda 함수의 경우 본래 heap에 저장이 되지만, 함수에서 반환된 lambda함수를 변수에 넣음으로써 data 영역에 저장된다.

  ```python
  def make_difference(operator):
    if operator == '+':
      return lambda x,y:x+y
    if operator == '-':
      return lambda x,y:x-y

  plus = make_difference('+')
  # lambda는 기본적으로 heap에 저장이 되지만, 변수로 다시 넣기 때문에 데이터의 영역으로 저장이 된다.
  print(plus(1,2)) #3
  ```

  <!-- more -->

<br/>

## Closure

- 함수 안에서 함수를 만들고 반환하는 형태
- 함수와 함께 함수 자신의 주변 환경을 저장한 레코드를 의미한다.

```python
def outer():
  text_a = 'John'
  def inner():
    b = 'Doe'
    print("My name is {} {}.".format(text_a, b))
  return inner #함수 객체를 전달해줘야 한다.

func = outer()
print(func) #
print(func.__dir__())
print(func.__closure__)
print(func.__closure__[0].cell_contents) # John이 출력된다.
# inner function에서는 outer function의 text_a를 가져와서 사용하고 있다.
# text_a는 closure라는 개념때문에 가능하다.
# closure의 element에 접근
```

<br/>

## Decorator

- decorator란 어떤 함수에 미리 만든 규격화된 처리를 적용할때 사용하는 것으로 정의할 수 있다.
- 프레임워크를 사용하다보면 @controller나 @app.route("/")와 같은 데코레이터를 method위에 작성하게 되는데, 이처럼 이미 프레임워크에서 제공하는 규격화된 처리를 적용할때 decorator가 사용된다.
- 함수로 decorator를 만들면, `함수 decorator`라고 하고 클래스로 decorator를 만들면 `클래스 decorator`라고 정의한다.
- `기존의 함수를 수정하지 않은 상태에서 추가 기능을 구현할때 사용`된다.

<br/>

- ### decorator의 기본 Syntax

  ```python
  # decorator1이라는 decorator 만들기
  def decorator1(func):
    def wrapper():
      # statements
    return wrapper()

  @decorator1
  def actual_work():
    # statements
  ```

<br/>

- ### @ decorator를 사용하지 않고 구현하기

  decorator 함수는 아래와 같이 실행할 함수를 outer function의 인자로 넣어주고 내부에서는 추가해 줄 기능과 인자로 받은 function을 작성해주며, 최종적으로 outer function에서는 inner function을 반환하는 형태로 적어주면 된다.

  ```python
  def trace(func): # 호출할 함수를 매개변수로 받는다.
    def wrapper(): # 호출할 함수를 감싸는 함수
      print(func.__name__, 'fuction start')
      func() # 인자로 받은 함수
      print(func.__name__, 'function end')
    return wrapper

  def hello():
    print('hello')

  def world():
    print('world')

  # decorator에 호출할 함수를 넣어준다.
  trace_hello = trace(hello)
  # 반환된 함수를 호출한다.
  trace_hello()
  # decorator에 호출할 함수를 넣어준다.
  trace_world = trace(world)
  # 반환된 함수를 호출한다.
  hello_world()
  ```

  <br/>

- ### @ decorator를 사용해서 구현하기

  위의 코드를 `@decorator`를 사용해서 간단하게 작성을 해보자.

  ```python
  def trace(func): # 호출할 함수를 매개변수로 받는다.
    def wrapper(): # 호출할 함수를 감싸는 함수
      print(func.__name__, 'fuction start')
      func() # 인자로 받은 함수
      print(func.__name__, 'function end')
    return wrapper

  @trace # @decorator
  def hello():
    print('hello')

  @trace # @decorator
  def world():
    print('world')

  hello() # 함수를 그대로 호출한다.
  world() # 함수를 그대로 호출한다.
  ```

  <br/>

- ### 매개변수와 반환값을 처리하는 decorator

  ```python
  def trace(func): # 호출할 함수를 매개변수로 받는다.
    def wrapper(a, b): # 호출할 함수의 매개변수와 똑같이 지정을 해준다.
      r = func(a, b) # func에 매개변수 a, b를 넣어서 호출하고 반환값을 변수에 저장한다.
      print('{0}(a={1}, b={2}) -> {3}'.format(func.__name__, a, b, r)) # 매개변수와 반환값을 출력해준다.
      return r # func의 반환값을 반환해준다.
      # func함수의 반환값을 반환해주지 않으면 add() 함수를 호출해도 아무값이 출력되지 않는다.
      # 만약에 func의 반환값이 필요가 없다면, return func(a, b)해주면 된다.
    return wrapper # wrapper 함수를 반환해준다.

  @trace
  def add(a, b):
    return a + b

  print(add(10, 20))
  # add(a=10, b=20) -> 30
  # 30
  ```

<br/>

- ### 가변 인수 함수 decorator

  - 함수의 매개변수(인수)가 고정되지 않은 경우에는 wrapper 함수를 가변 인수 함수로 만들면 된다.

    ```python
    def trace(func): # 호출할 함수를 매개변수로 받는다.
      def wrapper(*args, **kwargs): # 가변인수 함수로 만든다.
        r = func(*args, **kwargs) # func에 args, kwargs를 unpacking해서 넘겨준다.
        print('{0}(args={1}, kwargs={2}) -> {3}'.format(func.__name__, args, kwargs, r))
        return r # func의 반환값을 반환
      return wrapper # wrapper 함수를 반환

    def get_max(*args): # 위치 인수를 사용하는 가변 인수 함수 사용
      return max(args)

    def get_min(**kwargs): # 키워드 인수를 사용하는 가변 인수 함수 사용
      return min(kwargs.values())

    print(get_max(10, 20))
    print(get_min(x=10, y=20, z=30))

    """
    get_max(args=(10, 20), kwargs={}) -> 20
    20
    get_min(args=(), kwargs={'x': 10, 'y': 20, 'z': 30}) -> 10
    10
    """
    ```

<br/>

- ### Decorator Practice

<br/>

- #### 매개변수가 없는 decorator 작성해보기

  ```python
  def rapper(func):
    def wrapper():
      print('너와 나의 연결고리..')
      func()
    return wrapper

  @rapper
  def dok2():
    print('이건 우리 안의 소리')

  dok2()
  # 너와나의 연결고리..
  # 이건 우리 안의 소리
  # func()의 위치를 바꿔줌으로써 출력 순서를 바꿀 수도 있다.
  rapper(dok2)()
  # 너와나의 연결고리..
  # 너와나의 연결고리..
  # 이건 우리 안의 소리

  # decorator로 지정한 rapper() function이 기본적으로 한 번 실행해주고
  # @rapper dok2() function을 실행시켜준다.
  ```

<br/>

- #### decorator로 memoization 구현하기 (피보나치)

  - 재귀호출로 피보나치를 구현하면 아래와 같다.

  ```python
  def fibo_rec(num):
    if num < 2:
      return num
    else:
      return fibo_rec(num-2) + fibo_rec(num-1)
  ```

  - <ins><b>위의 재귀호출로 구현한 피보나치를 보면, 이미 연산된 수들의 조합으로 계속 누적해서 더해짐을 알 수 있다. 이미 연산된 수들의 조합을 momoization 개념을 사용해서 decoration 함수로 구현을 해보자.</b></ins>

  ```python
  def memoize(func):
    memo = {}
    def wrapper(seq):
      if seq not in memo: # 매개변수 seq가 memo에 없으면, func에 seq인자를 넣어서 momo 변수에 담아준다.
      #존재한다면, memo[seq]의 값을 반환해준다.
        # memo dictionary 객체에 해당 seq를 키값으로 하는 값이 없다면, func(seq)의 값을 넣어준다.
        memo[seq] = func(seq)
      return memo[seq]
    return wrapper

  @memoize
  def fibo_memo(num):
    if num < 2:
      return num
    else:
      return fibo_memo(num-2) + fibo_memo(num-1)
  ```

<br/>

- #### decorator를 활용해서 1호차입니다. ~ 5호차 입니다. 출력하기

  ```python
  # 1호차입니다. ~ 5호차 입니다.
  def call_train(func):
      def wrapper(num):
          for i in range(1, num+1):
              func(i)
      return wrapper

  @call_train
  def print_train(num):
      print("{}호차 입니다.".format(num))

  print_train(5)
  ```

- #### 반복문을 사용하지 않고 decorator를 활용해서 1호차입니다. ~ 5호차 입니다. 출력하기

  ```python
  def call_train(func):
    def wrapper(num):
        func(num)
        if num == 1:
            return
        else:
            call_train(wrapper(num-1))
    return wrapper

  @call_train
  def print_train(num):
      print("{}호차 입니다.".format(num))

  print_train(5)
  ```

<br/>

- #### decorator로 피보나치 time checker 구현하기

  ```python

  from time import time

  def time_checker(func):
    def wrapper():
      start_at = time()
      result = func()
      end_at = time()
      print("execution time: {}sec".format(end_at-start_at))
      return result
    return wrapper

    @time_checker
    def fibo_rec(num):
      if num < 2:
        return num
      else:
        fibo_rec(num-2) + fibo_rec(num-1)

    fibo_rec(10)

    #fibo_rec는 time_checker에서 func parameter이다.
    # 그런데 time_checker function의 내부에서 사용된 func()내부에는
    # parameter가 없기 때문에 wrapper에 num을 인자로 넘겨준다.
    # outer function은 decoratorated function(실행될 함수객체)을 넘겨받기 위한 역할을 하고
    # inner function은 실제로 넘겨받은 decorated function을 실행하기 위한 역할을 한다.
    def time_checker(func):
    def wrapper(num):
      start_at = time(num)
      result = func()
      end_at = time()
      print("execution time: {}sec".format(end_at-start_at))
      return result
    return wrapper
  ```

  recursion 방식에서는 보통 decorator를 사용하지 않는다.
  반복 출력에 대한 문제를 해결하기 위해서 아래와 같이 작성을 해준다.

  ```python
  # decorator로 구현된 함수를 직접 인자로 넣어서 처리를 해준다.
  time_checker(fibo_rec)(10)
  ```

## Do it yourself! (숙제)

> <br/>"Hi, {name}. You might be loved with {lang}" 이라는 문자열이 존재할 때,이 문자열의 앞 뒤로 \<h1>, \<em> 태그가 붙도록 하는 데코레이터를 생성하세요 <br/>ex output) <br/>\<h1>\<em> \{text\} \</em>\</h1> <br/>
> Advanced problem: Decorator 하나로 html 태그 이름을 지정할 수 있도록 수정 <br/>

```python
def appendHTMLTag(func):
    def wrapper(name, lang):
        return "<h1><em>{}</em></h1>".format(func(name, lang))
    return wrapper

@appendHTMLTag
def getString(name, lang):
    return f"Hi, {name}. You might be loved with {lang}"

print(getString("Lee Hyungi", "Korean"))
```

## 가변인수를 활용하여 인수를 하나로 받기

```python
def appendHTMLTag(func):
    def wrapper(*args):
        return "<h1><em>{}</em></h1>".format(func(*args))
    return wrapper

@appendHTMLTag
def getString(*args):
    return "Hi, {}. You might be loved with {}".format(*args)


print(getString("Lee Hyungi", "Korean"))
```
