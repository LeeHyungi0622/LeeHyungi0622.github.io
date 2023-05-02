---
title: 210214 Python HackerRank Assignment
date: 2021-02-14 12:59:41
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

설날 과제로 강사님이 내주신 HackerRank의 과제에 대해서 정리한다.

## 문제 1

[**Problem1 Link(HackerRank)**](https://www.hackerrank.com/challenges/30-inheritance/problem)

이 문제는 주어진 클래스의 상속관계를 완성하고, 주어진 조건으로 내부에 method를 완성시키면 되는 간단한 문제이다.

```python
class Student(Person):

    #   Class Constructor
    #
    #   Parameters:
    #   firstName - A string denoting the Person's first name.
    #   lastName - A string denoting the Person's last name.
    #   id - An integer denoting the Person's ID number.
    #   scores - An array of integers denoting the Person's test scores.
    #
    # Write your constructor here
    def __init__(self, _firstName, _lastName, _id, _scores):
        Person.__init__(self, _firstName, _lastName, _id)
        self.scores = _scores
    #   Function Name: calculate
    #   Return: A character denoting the grade.
    #
    # Write your function here
    def calculate(self):
        average = sum(self.scores) / len(self.scores)
        if 90 <= average <= 100:
            grade = "O"
        elif 80 <= average < 90:
            grade = "E"
        elif 70 <= average < 80:
            grade = "A"
        elif 55 <= average < 70:
            grade = "P"
        elif 40 <= average < 55:
            grade = "D"
        else:
            grade = "T"
        return grade
```

  <!-- more -->

## 문제 2

[**Problem2 Link(HackerRank)**](https://www.hackerrank.com/challenges/30-abstract-classes/problem)

이 문제는 추상클래스 관련 문제로, Book 추상 클래스를 MyBook에서 구현하는 문제이다.

```python
from abc import ABCMeta, abstractmethod
class Book:
    __metaclass__ = ABCMeta
    def __init__(self,title,author):
        self.title=title
        self.author=author
    @abstractmethod
    def display(): pass

#Write MyBook class
class MyBook(Book):
    def __init__(self, _title, _author, _price):
        Book.__init__(self, _title, _author)
        self.price = _price

    def display(self):
        print("Title: {}".format(self.title))
        print("Author: {}".format(self.author))
        print("Price: {}".format(self.price))
```

## 문제 3

[**Problem3 Link(HackerRank)**](https://www.hackerrank.com/challenges/class-1-dealing-with-complex-numbers/problem)

이 문제는 복소수의 덧셈, 뺄셈, 곱셈, 나눗셈, 나머지 연산에 대한 각 각의 class method를 작성하는 문제이다.

```python
import math

class Complex(object):
    def __init__(self, real, imaginary):
        self.real = real
        self.imaginary = imaginary
    # 실수부와 허수부를 각 각 나눠서 더해준다.
    def __add__(self, no):
        return Complex(self.real + no.real, self.imaginary + no.imaginary)
    # 실수부와 허수부를 각 각 나눠서 빼준다.
    def __sub__(self, no):
        return Complex(self.real - no.real, self.imaginary - no.imaginary)

    # (a + bi)(c + di) = (ac - bd) + (ad + bc)i
    # 실수부끼리의 곱과 허수부끼리의 곱을 빼고, 허수부와 실수의 곱과 실수와 허수의 곱을 더해준다.
    def __mul__(self, no):
        return Complex(self.real*no.real - self.imaginary*no.imaginary, self.imaginary*no.real + self.real*no.imaginary)


    def __div__(self, no):
        # 실수부의 제곱과 허수부의 제곱을 더한 값
        x = float(no.real ** 2 + no.imaginary ** 2)
        # imaginary의 부호를 반대로 하여 켤레수를 곱해준다.
        y = self * Complex(no.real, -no.imaginary)
        # 켤레수를 곱한 값의 실수값을 x로 나눠서 실수를 구한다.
        real = y.real / x
        # 켤레수를 곱한 값의 허수값을 x로 나눠서 허수를 구한다.
        imaginary = y.imaginary / x
        return Complex(real, imaginary)

    # 실수의 제곱과 허수의 제곱을 더해서 루트연산을 해준다.
    def mod(self):
        real = math.sqrt(self.real ** 2 + self.imaginary ** 2)
        return Complex(real, 0)

```
