---
title: 210205 Python TIL - filter, reduce, isinstance, OOP, scope, class
date: 2021-02-05 14:25:41
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

## 오늘 배운내용

- **Section1)** filter, reduce
- **Section2)** OOP, Class

## filter, reduce

### filter

**filter의 기본 사용 format :** `filter([function], [iterable object])`

#### 1부터 10까지의 수 중에 짝수인 수로 구성된 리스트 만들기

```python
def even_selector(x):
  if x % 2 == 0:
    return True
  else:
    return False
# 선언한 function을 인자로 넣기
list(filter(even_selector, range(1, 10+1)))
# lambda function로 별도의 함수 선언없이 함수처리
list(filter(lambda a:a%2==0, range(1, 10+1)))
```

  <!-- more -->

#### isinstance('[check value]', [check type])

```python
**isinstance의 사용예)** isinstance('1', int) # True
**isinstance의 사용예)** isinstance('1', float) # False

# isinstance는 리스트 내부에 다양한 타입의 데이터가 존재할때 유용하게 사용이 가능하다.
some_list = [1, 3.14, 2.71828, 'tau', 'lambda', [1, 0, 0, 1], {'kay': 'bamboo'}]

# float인 요소만 filter해보기
def validation_check(x):
    if isinstance(x, float):
        return True

print(list(filter(validation_check, some_list)))

# filter는 반드시 lambda 함수식의 반환부분에서 True 또는 False를 반환해야 한다.
print(list(filter(lambda x: isinstance(x, float), some_list)))
```

<br/>

### reduce

**reduce의 기본 사용 format :** `reduce([function], [iterable object])`

- iterable object의 모든 element에 대하여 연산결과를 출력
- reduce는 Python3의 기본 내장함수에서 제외되어, functools에서 별도로 import해서 사용한다.
  `from functools import reduce`
  - Rossum은 map, filter, reduce에 대해 readibility가 떨어진다는 이유로 제외 기본 내장함수에서 제외하였다.

**reduce 사용하지 않고 단순 반복문으로 1부터 100까지의 합을 구하기**

```python
sum = 0
for i in range(1, 100+1):
  sum += i
print(sum)
```

**reduce를 사용하여 1부터 100까지의 합을 구하기**

```python
from functools import reduce

# reduce(function, iter)
def accumulator(x,y):
  return x+y

# reduce의 function은 두 개의 인자(x,y)를 받고 하나의 값(x+y)을 반환하는 구조이다.
print(reduce(accumulator, range(1, 100+1)))

# lambda
print(reduce(lambda x,y:x+y, range(1, 100+1))) # 5050

# 세번째 argument값으로 initial value를 줄 수 있다. (default:0)
print(reduce(lambda x,y:x+y, range(1, 100+1), 100)) # 5150
```

**옆 사람이랑 같이 pair coding하기**
아래의 간단한 예제 문제를 옆 사람과 해결방향을 모색해가며 문제해결을 한다.

```python
recycle_bin = [
1, 2, "Fastcampus", ['dog', 'cat', 'pig'], 5, 4, 5.6, False,
"패스트캠퍼스", 100, 3.14, 2.71828, {'name':'Kim'}, True,
]

# 정수이거나 실수인 수들로만 이루어진 리스트 생성
# True(1), Flase(0) 또한 integer로써 인식이 되기 때문에 별도로 bool type이 아니라는 조건을 추가해줘야 한다.
def validation_check(x):
    if (isinstance(x, int) or isinstance(x, float)) and not isinstance(x, bool):
        return True

print(list(filter(validation_check, recycle_bin)))

# lambda function 방식으로 위의 코드 refactoring한다.
print(list(filter(lambda x: (isinstance(x, int) or isinstance(x, float)) and not isinstance(x, bool), recycle_bin)))

# 정수로만 이루어진 리스트의 각 요소를 제곱해서 리스트 생성
# True(1), Flase(0) 또한 integer로써 인식이 되기 때문에 별도로 bool type이 아니라는 조건을 추가해줘야 한다.
def validation_check_second(x):
    if isinstance(x, int) and not isinstance(x, bool):
        return x**2

result = list(filter(validation_check_second, recycle_bin))

# lambda function 방식으로 위의 코드를 refactoring한다.
result = list(filter(lambda x: isinstance(x, int) and not isinstance(x, bool) , recycle_bin))

print(result)

# sum() function을 이용해서 리스트의 모든 구성요소의 합을 구한다.
result_sum = sum(result)
print(result_sum)
```

```python
# 위에서 작성한 방식과 다르게 filter해서 나온 결과 리스트를 다시 filter하는 방식으로 처리
result = list(filter(lambda b: not isinstance(b, bool),
              filter(lambda a:isinstance(a, (int, float)),
              recycle_bin)))
print(result)
```

```python
# 위에서 작성한 방식과 다르게 filter해서 나온 결과 리스트를 다시 filter하는 방식으로 처리
result = list(map(lambda x:x**2, filter(lambda b: not isinstance(b, bool),
                                 filter(lambda a:isinstance(a,int),
                                 recycle_bin))))
print(result)
```

<br/>

### OOP(Object-Oriented-Programming) : 객체지향 프로그래밍

- 특징
  - **Encapsulation**
    - 구현한 것을 드러나지 않도록 함
    - Information hiding: public, \_protected, \_\_private
  - **Abstraction**
    - 인터페이스로 클래스의 공통 특성을 묶어 표현
  - **Inheritance**
    - 자식 클래스가 부모 클래스의 특성과 기능을 물어본다.
  - **Polymorphism**
    - 변수, method가 다른 상태를 가지는 것
- 프로그래밍 언어의 변천
  - Imperative Programming(명령적 프로그래밍)
  - Procedural programming(절차지향 프로그래밍)
  - Object-Oriented Progrmming(객체지향 프로그래밍)
  - Functional Programming(함수형 프로그래밍)

### Scope

- **local :** outer_scope(함수내의 변수)의 binding을 바꾸지 못한다.
- **nonlocal :** outer_scope(함수내의 변수)의 binding을 바꾼다.
- **global :** 전역 binding을 바꾼다.

**아래의 간단한 예제를 통해 `global, local, nonlocal`의 키워드에 대해서 이해한다.**

```python
## Scope
msg = "fastcampus"

def outer_scope():
    def inner_local():
        msg = "Fast"

    def inner_nonlocal():
        nonlocal msg
        msg = "Campus"

    def inner_global():
        global msg
        msg = "FastCampus"

    msg = "Seong-Su"
    inner_local()
    # method가 내부 변수(msg)에 영향을 주지 않는다.
    print('inner local:', msg)
    inner_nonlocal()
    # nonlocal로 설정해주면 내부 변수에 영향을 준다.
    # Seong-Su를 Campus로 업데이트
    print('inner nonlocal:', msg)
    # 외부에 선언된 msg 변수에 대해 처리
    inner_global()
    # inner_global에서의 msg는 "fastcampus"이다.
    print('inner global:', msg)

outer_scope()
"""
inner local: Seong-Su
inner nonlocal: Campus
inner global: Campus
"""
```

<br/>

### **Class**

- **SOLID**
  - Single Responsibility Principle
    - 한 클래스는 하나의 책임만 가져야 한다.(goto 19)
  - Open/Closed Principle
    - 확장에는 열려있지만, 변경에는 닫혀 있어야 한다.(override)
  - Liskov's Substitution Principle
    - 프로그램의 정확성을 깨트리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 한다.(is-A)
  - Interface Segregation Principle
    - 사용하지 않는 method는 분리해야 한다.(Abstract)
  - Dependency Inversion Principle
    - 추상화에 의존하고, 구체화에 의존하지 않는다.
- **구성요소**
  - Class
  - Object
  - Method

```python
# class의 기본형태
class ClassName: #CamelCase
  pass # statement
  # hello_world: snake_case
```

<br/>

#### Class 작성 및 인스턴스 생성

```python
class Hero:
  pass

IronMan = Hero()
CaptainAmerica = Hero()
Thor = Hero()
```

<br/>

#### Class 작성 및 인스턴스 생성(+인스턴스 변수 초기화) 및 Class 내부 method 작성

```python
class Hero:
  hp = 100 # class variable
  def __init__(self, name, weapon):
    self.name = name # instance variable
    self.weapon = weapon
  # instance의 행동을 정의
  def attack(self):
    print('attack with {}',format(self.weapon))

  def get_damaged(self):
    pass

IronMan = Hero('IronMan', 'Suit')
print(IronMan.attack())
print(IronMan.name)
print(IronMan.weapon)
```

<br/>

#### 인스턴스 객체 참조를 통한 class 내부 변수의 값 변경하기

```python
class LetsSeeAttributes:
  """
  This is docstring
  """
  integer = 1024
  def function():
    return 'fastcampus'

fast = LetsSeeAttributes()
print(fast.integer) # 1024
fast.integer = 2048
print(fast.integer) # class 내부 변수의 값이 업데이트 된다.
campus = LetsSeeAttributes()
campus.integer # 1024
```
