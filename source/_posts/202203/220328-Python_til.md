---
title: 220328 Python TIL
date: 2022-03-28 13:13:41
tags:
  - Python
  - TIL
categories:
  - Python
---

<div align="center">
  <img src="/images/post_images/python_logo.png" alt="Python"/>
</div>

<br/>

<h2><b>파이썬 클래스</b></h2>

```python
class Movie:

  def __init__(self, name, actor, review, score):
    self.name = name
    self.actor = actor
    self.review = review
    self.score = score

  def update(self, name, actor, review, score):
    self.name = name
    self.actor = actor
    self.review = review
    self.score = score

  def all(self):
    print("제목: {}\n배우: {}\n리뷰: {}\n별점: {}".format(self.name, self.actor, self.review, self.score))

  패터슨 = Movie('패터슨', '아담 드라이버', '아하!', 4.5)
  지구를지켜라 = Movie('지구를 지켜라', '신하균', '7번 봤다 더 말이 필요한가', 5)
  헤드윅 = Movie('헤드윅', '존 카메론 미첼', '위키드 리틀 타운', 4)

  for a in (지구를지켜라, 헤드윅, 패터슨):
    a.all()
    print('-----------')

  # update method 이용하여 가장 마지막 영화의 제목 뒤에 2를 넣어 수정
  패터슨.update('패터슨2', '아담 드라이버', '아하!', 5)
  패터슨.name
```

## **인스턴스 변수의 은닉, 클래스 변수 선언, static method 추가**

```python
class Movie:

  class_name = 'Movie'

  def __init__(self, name, actor, review, score):
    self._name = name
    self.actor = actor
    self.review = review
    self.score = score

  def update(self, name, actor, review, score):
    # _name을 숨겨놓고 조회만 하게 하려면 update method에서 제목 자체를 생략한다.
    self.actor = actor
    self.review = review
    self.score = score

  def all(self):
    print("제목: {}\n배우: {}\n리뷰: {}\n별점: {}".format(self.name, self.actor, self.review, self.score))

  # 파이썬에서 은닉성을 만들기 위해 - 함수를 이용해서 변수명인척 숨어있는 변수를 호출하는 능(볼 수만 있고, 해당 변수명은 알 수 없으며 수정도 할 수 없음.)
  @property
  def out_name(self):
    return self._name

  @staticmethod
  def staticMovie():
    print(Movie.class_name)

  @classmethod
  def staticMovie(cls):
    print(cls.class_name)
```

<!-- more -->

<h2><b>상속</b></h2>

자식이 부모 클래스를 상속 받고, 자식 클래스의 인스턴스 변수를 선언해주었을 때, 인스턴스 변수를 통해 자식 클래스와 부모 클래스의 공통된 메소드를 호출하게 되면, `자식 클래스의 하위에 있는 메소드가 호출된다.` 자식 클래스에서 부모 클래스를 상속받고, sample이라는 이름의 `method를 오버라이딩`하고 있다.

```python
class Parent:
  def sample(self):
    print("Call sample method in Parent class")

class Child(Parent):
  def sample(self):
    print("Call sample method in Child class")

lee = Child()
lee.sample() # "Call sample method in Child class" 출력
```

```python
class Parent:
  def __init__(self):
    print("Constructor in Parent class")

class Child(Parent):
  def __init__(self):
    print("Constructor in Child class")

lee = Child()
# "Constructor in Child class" 출력
# 생성자 자체도 오버라이딩된다.
```

<h2><b>부모클래스의 생성자 호출</b></h2>

부모 클래스를 상속받은 자식 클래스의 생성자에서 super() 키워드를 통해 상속받은 부모 클래스의 생성자를 호출 할 수 있다.

```python
class Parent:
  def __init__(self):
    print("constructor in Parent class")

class Child(Parent):
  def __init__(self):
    print("constructor in Child class")
    super().__init__()
```
