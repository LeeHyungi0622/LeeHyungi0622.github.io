---
title: 210210 Python TIL 2/2 - class method, instance method, static method,  Polymorphism, Abstract class
date: 2021-02-10 14:26:41
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

오늘 배운 내용은 전반적으로 객체지향 프로그래밍 언어로써 Python을 잘 활용하기 위해 중요한 내용이기 때문에 포스팅을 두 개로 나눠서 작성해보았다.

## 오늘 배운내용

## Class, instance, static method

- ### Class method: @classmethod decorator를 사용하며, cls를 인자로 사용
  - cls 인자를 받는다.
  - class variable과 연관된 일을 할때 사용이 된다.
- ### Instance method: self를 인자로 사용
  -객체의 고유 속성값을 사용한다.
- ### Static method: 아무 인자 없이 사용
  - 일반 함수와 같은 역할을 한다.
  - class와 관련있는 함수로써, class내부에서 정의하고자 할 때 사용된다.

```python
class Wallet:
  def __init__(self, account):
      self.account = account
  balance = 0
  name = 'Your wallet'

  @classmethod
  def set_default(cls, amount):
      while amount < 1:
          print("Err: You should set default value over 1. Try again!")
          amount = int(input("Set default value: "))
      cls.balance = amount
      print('Set default balance to {}'.format(amount))
  #instance method
  def add_to_account(self, amount):
      self.account += amount
      print('Your total balance is {}'.format(self.account + Wallet.balance))

  @staticmethod
  def see_static_name():
      print(Wallet.name)

  def get_class_name(cls):
      print(cls.name)
class MyWallet(Wallet):
  name = 'My wallet'
```

  <!-- more -->

## 다형성(Polymorphism)

앞의 포스팅에서 정리한 method의 override가 가능한 이유가 바로 이 다형성(Polymorphism)이라는 특성때문이다. 각 object가 상속받은 부모클래스의 method를 다르게 재정의 함으로써 본래 정의되어있던 method와 다른 행동과 기능 그리고 결과를 나타낸다.

## 추상 클래스(Abstract Base Class)

[https://docs.python.org/3/library/abc.html](https://docs.python.org/3/library/abc.html)

공통 특성을 가진 부모 클래스인데, 가지고 있는 method들이 모두 method로서의 기능이 없고, 선언만 되어있는 클래스를 말한다. 추상 클래스는 한 개 이상의 추상 클래스를 포함하고 있어야 하며, 별도의 class instance를 생성할 수 없다.

- ### 추상 클래스(Abstract class의 정의와 사용)

  ```python
  from abc import ABC

  class MyABC(ABC):
    @abstractmethod
    def abstract_method(self):
        pass
  ```

  ```python
  from abc import ABCMeta

  class MyABC(metaclass=ABCMeta):
    @abstractmethod
    def abstract_method(self):
        pass
  ```

  ```python
  from abc import *

  class MyABC(metaclass=ABCMeta):
    @abstractmethod
    def abstract_method(self):
        pass
  ```

  ## 추상클래스 예시

  ```python
  from abc import ABCMeta, abstractmethod

  class AbstractClassExample(metaclass=ABCMeta):

      def __init__(self, value):
          self.value = value

      @abstractmethod
      def do_something(self):
          pass

  class DoAdd42(AbstractClassExample):
      def do_something(self):
          return self.value + 42

  class DoMul42(AbstractClassExample):
      def do_something(self):
          return self.value * 42

  x = DoAdd42(10)
  y = DoMul42(10)

  print(x.do_something()) # 52
  print(y.do_something()) # 420
  ```
