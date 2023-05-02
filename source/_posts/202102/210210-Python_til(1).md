---
title: 210210 Python TIL 1/2 - class object, class variable, instance variable, Constructor/Deconstructed, Hide Information, Inheritance, Aggregation, Composition
date: 2021-02-10 13:26:41
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

## class object

- ### 얕은복사(shallow copy) vs 깊은복사(deep copy)

  ```python
  fruits = ['apple']

  a = fruits
  print(a) #['apple']

  b = fruits
  b.append('banana')
  print(b) #['apple', 'banana']

  print(fruits) #['apple', 'banana']
  ```

    <!-- more -->

  fruits 변수가 b에 의해서 업데이트된 값으로 초기화가 되었다.
  이러한 경우는 얕은 복사(shallow copy)로, 깊은 복사(deep copy)를 해주기 위해서는 아래와 같이 `참조변수에 대해서 type casting을 하게 되면 참조한 변수에 대해서 새로운 객체로 참조`된다.

  ```python
  fruits = ['apple']

  a = list(fruits)
  print(a) #['apple']

  b = list(fruits)
  b.append('banana')
  print(b) #['apple', 'banana']

  print(fruits) #['apple', 'banana']
  ```

<br/>

- ### dir([object])

  [https://docs.python.org/3/library/functions.html](https://docs.python.org/3/library/functions.html)

  > Without arguments, return the list of names in the current local scope. With an argument, attempt to return a list of valid attributes for that object.

  - argument를 넣어주지 않으면 현재 local scope 내의 이름을 리스트로 반환해준다.
  - argument를 같이 넣어주면 argument로 넣어준 object의 속성에 대한 리스트를 반환해준다.

  ```python
  class SeeAttributes:
      """
      You can see docstring.
      """
      # good job
      integer = 1024 # attribute
      def function(self): # attribute
          return 'fastcampus'

  LetsSee = SeeAttributes()
  print(dir()) # show the names in the module namespace
  """
  ['LetsSee', 'SeeAttributes', '__annotations__', '__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__']
  """

  print(dir(LetsSee)) # show the names in the struct module
  """
  ['__class__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__le__', '__lt__', '__module__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__weakref__', 'function', 'integer']
  """
  ```

  <br/>

- ### ([object]).\_\_doc\_\_

  struct module 내에 정의된 documentation정보를 출력한다.

  ```python
  print(LetsSee.__doc__)
  # struct module 내에 정의된 documentation정보를 출력
  """
  "
      You can see docstring.
  """
  ```

<br/>

## mutable한 class variable

mutable한 variable을 class variable로 선언을 해주게 되면, 해당 class로 class instance들 생성한 객체들은 모두 같은 class variable을 공유하며 사용을 하게 된다.

```python
# 예제 코드에서 inventory list variable은 class variable로 선언이 되었다.
class Hero: # class
    health = 100 # class variable
    inventory = []
    def __init__(self, name, weapon):
        self.name = name # instance variable
        self.weapon = weapon
    def attack(self): # instance method
        print("attack with {}".format(self.weapon))
    def save_item(self, item):
        self.inventory.append(item)
```

<br/>

## mutable한 instance variable

mutable한 variable을 class variable로 사용했을때의 문제점을 해결하기 위해서 constructor에서 사용할 변수를 초기화시켜서 instance variable로써 사용될 수 있도록 해야 한다.

```python
# 아래 코드에서 inventory variable은 instance variable로써 class를 사용해서 instance를 생성했을때 초기화가 될 수 있도록 작성하였다.
class Hero: # class
    health = 100 # class variable
    def __init__(self, name, weapon, inventory):
        self.name = name # instance variable
        self.weapon = weapon
        self.inventory = []
    def attack(self): # instance method
        print("attack with {}".format(self.weapon))
    def save_item(self, item):
        self.inventory.append(item)
```

## Deconstructor (소멸자)

constructor(생성자)가 존재하면, deconstructor(소멸자)도 존재한다. 생성자가 해당 class instance를 생성할때 호출되는 함수라면, 소멸자는 해당 class instance를 사용 후에 소멸시킬때 사용된다.

```python
# Deconstructor의 형태
def __del__(self):
  pass
```

## class 내의 method에서 class 내의 다른 method에 접근

class 내에서 선언된 함수에서 class 내의 다른 method를 호출해서 사용할 수 있다.

```python
class Hero: # class
    health = 100 # class variable
    def __init__(self, name, weapon, inventory):
        self.name = name # instance variable
        self.weapon = weapon
        self.inventory = []
    ......
    def save_item(self, item):
        self.inventory.append(item)
    def save_item_multiple(self, item, num):
      for _ in range(num):
        self.save_item(item)
```

## Hide Information

다른 언어와 달리 Python은 private, public, protected와 같은 접근제어자 키워드가 존재하지 않고, naming으로 접근제어를 합니다. naming의 기준은 variable, method name의 접두사(prefix)에 _(underscore)가 붙어있는지 유/무와 한 개 또는 두 개의 _(underscore)가 붙느냐에 따라서도 달라진다.

- () (public) : public은 접두사에 \_(underscore)가 없는 경우
- \_() (protected) : protected는 접두사에 \_(underscore)가 하나 붙어있는 경우
- \_\_() (private) : private는 접두사에 \_(underscore)가 두 개 붙어있는 경우

`protected와 private의 접미사는 _(underscore)를 한 개까지만 허용하고, private의 접미사가 _(underscore) 2개라면, public으로 간주한다.`

```python
class AccessControl:
    def __init__(self):
        self.public = "PUBLIC"
        self.__private = "PRIVATE"
        self._protected = "PROTECTED"

    def print_test(self):
        print(self.public)
        print(self.__private)
        print(self._protected)


obj = AccessControl()
obj.print_test()
"""
PUBLIC
PRIVATE
PROTECTED
"""
# print(obj.public)
print(obj._protected)  # PROTECTED

# AccessControl 객체 외부에서는 __private가 보이지 않기 때문에 접근할 수 없다.
# AttributeError: 'AccessControl' object has no attribute '__private'
print(obj.__private)
```

## 상속(Inheritance)

부모 클래스의 속성을 물려받아서 새로운 instance를 생성하는 것을 말한다.

- ### **상속의 특징**

  - 상속은 두 클래스 간에 `is-A` 관계가 성립한다.
    ex) 사자(`자식 클래스`)는 동물(`부모 클래스`)이다. (`상속관계 성림`)
  - 부모 클래스로부터 `상속받은 method를 override`할 수 있다.
  - Python에는 `오버로드(Overload)의 개념이 없다.`
    오버로드 : method의 이름은 같지만 parameter의 인자가 다르다면 중복 적재가 가능하다는 개념

  Python에서의 상속은 별도의 키워드 없이 class이름에 ()를 붙여서 내부에 상속하고자 하는 클래스의 이름을 넣어주면 된다.

  ```python
  class DerivedClassName(BaseClassName):
    #Statements
  ```

  치킨을 예로들어, 튀겨진 치킨을 부모 클래스로 하고, 각기 다른 Seasoning을 첨가한 치킨을 자식 클래스로 정의하여 클래스의 관계를 정의한다.

  ```python
  class Fried:
    def __init__(self, mixture, ,chicken):
      self.mixture = mixture
      self.chicken = chicken

    def place_into_fryer(self):
      print('chicken is now frying for 15 min..')

  class Seasoned(Fried):
    def __init__(self, mixture, chicken, sauce='red chili'):
      # 자식 클래스의 생성자에서는 부모 생성자의 생성자를 아래와 같이 정의한다. (=super())
      Fried.__init__(self, mixture, chicken)
      # 부모 생성자의 초기화에 사용되는 인자를 제외한 요소는 자식 클래스의 생성자에서 초기화시켜준다.
      self.sauce  = sauce
    def place_into_fryer(self):
      print('chicken is now frying for 13 min..')
    def mix_with_sauce(self):
      print('mix with {}'.format(self.sauce))
  ```

  스마트폰을 예로들어 SmartPhone 부모 클래스를 상속받는 Iphone 클래스와 Galaxy 클래스를 정의해본다.

  ```python
  class SmartPhone:
    def __init__(self, ap, cam):
      self.ap = ap
      self.cam = cam

    def open_ai(self):
      print('Default Setting')

    def __str__(self):
      return 'I am {}'.format(self.__class__.__name__)
  ```

  Iphone 클래스

  ```python
  class Iphone(SmartPhone):
    def __init__(self, ap, cam, touch_id):
      SmartPhone.__init__(self, ap, cam)
      self.touch_id = touch_id

    def open_ai(self):
      print('Hey, Siri')

    def __str__(self):
      return super(Iphone, self).__str__()
  ```

  Galaxy 클래스

  ```python
  class Galaxy(SmartPhone):
    def __init__(self, ap, cam, sam_pay):
      # with super() 위에서는 상속하는 클래스 이름에 .__init__을 해줬지만,
      # super()를 사용하면 자식클래스의 이름과 self를 super의 인자로 넘겨준다.
      super(Galaxy, self).__init__(ap, cam)
      self.sam_pay = sam_pay

    def open_ai(self):
      print('Hi, Bixby!')

    def __str__(self):
      # 부모의 __str__() method를 호출한다.
      return super().__str__()
  ```

## 또 다른 의존관계로 has-A(Aggregation, Composition) 관계가 있다.

이 has-A 관계는 Attribute로 사용하는 경우를 예로 한다. has-A관계를 `Aggregation`과 `Composition` 이 두 측면으로 살펴볼 수 있다. 좀 더 쉽게 이해하기 쉽게 자동차의 튜닝과 바퀴를 예로들어 작성해보겠다.

- ### **Aggregation** : Composition보다 좀 더 느슨한 관계이다. (응집력이 약하다.)

  예를들어, 자동차 구매한 후에 개인적으로 자동차를 튜닝했다고 가정하자. 어느날 튜닝한 것이 마음에 안들어서 제거를 했을때, 제거를 한다고 해도 자동차의 객체에는 문제가 되지 않는다.

  - A(container)와 B(contained class)의 관계에서 A(container)가 사라져도 B(contained class)는 사라지지 않는다.
  - contained class는 container에 강한 dependency를 가지지 않는다.
  - 전체와 부분이 독립적인 객체로써 존재할 수 있다.
  - 전체를 여러개의 객체로 볼 수 있다.

  ```python
  # 종속관계에 있는 클래스를 생성자에서 초기화시켜주지 않고, instance객체를 생성한 다음에 argument로써 넘겨서 초기화를 시켜준다.
  class Hammer:

    def __init__(self, name):
        self.name = name

    def fly(self):
        print('Flying..')

  class Thor:

    def __init__(self):
        self.weapon = None

    def recall_hammer(self, weapon):
        self.weapon = weapon

    def throw_hammer(self):
        weapon = self.weapon
        self.weapon = None
        return weapon

    def fly(self):
        if self.weapon:
            self.weapon.fly()
        else:
            print("You can't do this")

  Thor = Thor()
  molnir = Hammer('molnir')
  Thor.recall_hammer(molnir)
  Thor.fly()
  Thor.throw_hammer()
  Thor.fly()
  ```

<br/>

- ### **Composition**

  예를들어, 자동차의 일부 바퀴를 제거했다고 가정하자. 그럼 운전한다는 자동차의 본질적인 객체의 특성을 잃게 된다.

  - A(container)와 B(contained class)의 관계에서 A(container)가 사라지면 B(contained class)도 사라진다.
  - Aggregation 보다 좀 더 구체적인 관계를 정의한다.
  - container class와 contained class가 강한 dependency를 가진다.
  - container가 없어지면, 포함하고 있는 모든 contained clas도 없어진다.
  - 전체는 단일 객체로써 본다.
  - 전체를 통해서만 부분이 인식된다.

  ```python
  # 종속관계에 있는 class 객체 자체를 class의 생성자에서 직접적으로 초기화시켜주는 경우를 Composition이라고 한다.
  class AP:
    pass

  class Cam:
    pass

  class SmartPhone:
    def __init__(self):
      self.ap = AP()
      self.cam = Cam()
  ```
