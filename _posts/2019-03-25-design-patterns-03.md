---
layout: post
title: "파이썬 디자인 패턴 - 03"
comments: true
description: ""
keywords: ""
---

## 팩토리 패턴


### 개요

객체지향에서 팩토리란 클래스의 객체를 생성하는 클래스를 일컫는다. 클라이언트는 특정 인자와 함께 메소드를
호출하고 팩토리는 해당 객체를 생성하고 반환한다.


클라이언트가 직접 객체를 생성할 수 있는데도 불구하고 팩토리가 필요한 이유는 다음과 같다.

- 객체 생성과 클래스 구현을 나눠 상호 의존도를 줄인다.
- 클라이언트는 생성하려는 객체 클래스 구현과 상관없이 사용할 수 있다. 객체를 생성할 때 필요한 인터페이스와
메소드, 인자 등의 정보만 있으면 된다.
- 코드를 수정하지 않고 간단하게 팩토리에 새로운 클래스를 추가할 수 있다.
- 이미 생성된 객체를 팩토리가 재활용할 수 있다.


---

팩토리 패턴에는 3가지 종류가 있다.
- 심플 팩토리 패턴: 인터페이스는 객체 생성 로직을 숨기고 객체를 생성한다.
- 팩토리 메소드 패턴: 인터페이스를 통해 객체를 생성하지만 서브 클래스가 객체 생성에 필요한 클래스를 선택한다.
- 추상 팩토리 패턴: 객체 생성에 필요한 클래스를 노출하지 않고 객체를 생성하는 인터페이스다. 내부적으로는 다른
팩토리 객체를 생성한다.

---

### 심플 팩토리 패턴

팩토리를 사용하면 여러 종류의 객체를 사용자가 직접 클래스 호출하지 않고 생성할 수 있다.

클라이언트 클래스는 create_tye()메소드가 있는 팩토리 클래스를 사용한다. 클라이언트가 객체형을 나타내는 인자와 함께
create_tye() 메소드를 초루하면 팩토리는 Product1 또는 Product2을 반환한다.



Animal 은 추상 기본 클래스이고 do_say() 메소드를 포함한다. Animal 인터페이스를 통해 Cat과 Dog라는 상품을
생성하고 각 동물의 울음소리를 출력하는 do_say() 함수를 구현한다.

```python
class Animal(metaclass = ABCMeta):
  @abstractmethod
  def do_say(self):
    pass

class Dog(Animal):
  def do_say(self):
    print('Bhow Bhow!')

class Cat(Animal):
  def do_say(self):
    print('Meow Meow!')

# forest factory
class ForestFactory(obejct):
  def make_sound(self, object_type):
    return eval(object_type)().do_say()



# client code
if __name__ = '__main__':
  ff = ForestFactory()
  animal = input('Dog or Cat')
  #  animal = Cat
  ff.make_sound(animal)


# result  
# Meow Meow!
```



---


### 팩토래 메소드 패턴

- 인터페이스를 통해 객체를 생성하지만 팩토리가 아닌 서브클래스가 해당 객체 생성을 위해 어떤 클래스를 호출할지
결정한다.
- 팩토리 메소드 패턴은 인스턴스화가 아닌 **상속** 을 통해 객체를 생성한다.
- 팩토리 메소드 디자인은 유동적이다. 심플 팩토리 메소드처럼 특정 객체가 아닌 같은 인스턴스나 서브 클래스 객체를 반환할 수 있다.



링크드인, 페이스북 같은 서비스에 특정 사용자나 기업의 프로필을 작성하는 경우를 예를 든다.
두 서비스는 공통적으로 입력하는 프로필 정보가있고 다른 프로필 정보가 있다. 서비스에 종류에 따라 알맞은
프로필 정보를 입력하는 코드를 구현한다.

먼저 Product 인터페이스를 선언한다 Section 추상 클래스는 프로필의 각 섹션을 나타낸다.
간단하게 describe() 추상 메소드만 포함한다. 그리고 프로필 정보를 입력하는 다양한 섹션 클래스를 생성한다.

```python
class Section(metaclass=ABCMeta):
  @abstractmethod
  def describe(self):
    pass


class PersonalSection(Section):
  def describe(self):
    print('PersonalSection')

class AlbumSection(Section):
  def describe(self):
    print('AlbumSection')

class PatentSection(Section):
  def describe(self):
    print('PatentSection')

class PublicationSection(Section):
  def describe(self):
    print('PublicationSection')

```


그 뒤에 Creator 추상 클래스를 Profile이라는 이름으로 선언한다. Profile[Creator] 추상 클래스는
createProfile() 팩토리 메소드를 제공한다.

linkedin 과 facebook이라는 두개의 ConcreteCreator 클래스를 생성하고 각 클래스에 여러 섹션을 생성하는
createProfile() 추상 메소드를 정의한다.

```python
class Profile(metaclass=ABCMeta):
  def __init__(slef):
    self.sections = []
    self.createProfile()

  @abstractmethod
  def createProfile(self):
    pass

  def getSections(self):
    return self.sections
  def addSections(self, section):
    seelf.sectiosn.push(section)

class linkedin(Profile):
  def createProfile(self):
    self.addSections(PersonalSection())
    self.addSections(PatentSection())
    self.addSections(PublicationSection())

class facebook(Profile):
  def createProfile(self):
    self.addSections(PersonalSection())
    self.addSections(AlbumSection())

# client code
if __name__ == '__main__':
  profile_type = input('facebook or linkedin') # profile_type = facebook
  profile = eval(profile_type)()
  print('creating profile', type(profile).__name__)
  print('profile has sections', profile.getSections())

# result
# createing profile facebook
# profile has sections PersonalSection, AlbumSection
```



#### 팩토리 메소드 패턴의 장점
- 유연성과 포괄성을 갖추며 한 클래스에 종속되지 않는다. ConcreteProduct가 아닌 인터페이스(Product)에 의존한다.
- 객체를 생성하는 코드와 활용하는 코드를 분리해 의존성이 줄어든다. 새로운 크래스 추가 등의 유지보수가 쉽다.



---




### 추상 팩토리 패턴

추상 팩토리 패턴에서 클라이언트는 객체 생성 로직과 상관없이 생성된 객체를 사용한다. 클라이언트는 오직 인터페이스를 통해
객체에 접근할 수 있다. 객체 집단 중 일부가 필요할 때는 추상 팩토리 패턴을 통해 접근할 수 있다.
예를들어 앱이 플랫폼 독립적이라면 OS나 파일 시스템 호출 등의 종속성을 없애야한다.


#### 구현

인도식 피자와 미국식 피자를 판매한다고 가정한다.

먼저 추상 기본 클래스 PizzaFactory를 선언한다. 이 클래스에는 ConcreteFactory가 상속받을 createVegPizza()
와 createNonVegPizza(), 두 개의 추상 메소드가 있다.

```python
class PizzaFactory(metaclass=ABCMeta):
  @abstractmethod
  def createVegPizza(self):
    pass

  @abstractmethod
  def createNonVegPizza(self):
    pass

class IndianPizzaFactory(PizzaFactory):
  def createVegPizza(self):
    return DeluxVeggiePizza()

  def createNonVegPizza(self):
    return ChickPizza()

class USPizzaFactory(PizzaFactory):
  def createVegPizza(self):
    return MexicanVegPizza()

  def createNonVegPizza(self):
    return HamPizza()
```

다음은 AbastractProduct를 선언한다. VegPizza 와 NonVegPizza 추상 클래스가 있다. 이 클래스들에는
prepare()와 serve() 메소드가 선언되어 있다.

이 예제의 핵심은 채식 피자는 크러스트와 채소, 양념으로만 조리하고 일반 피자는 비채식 토핑을 채식 피자위에 첨가해 조리한다는 것이다.

```python
class VegPizza(metaclass=ABCMeta):
  @abstractmethod
  def prepare(self, VegPizza):
    pass

class NonVegPizza(metaclass=ABCMeta):
  @abstractmethod
  def serve(self, VegPizza):
    pass

class DeluxVeggiePizza(VegPizza):
  def prepare(self):
    print('prepare', type(self).__name__)

class ChickendPizza(NonVegPizza):
  def serve(self, VegPizza):
    print(type(self).__name__, 'is served with Chicken on ', type(VegPizza).__name__)


class MexicanVegPizza(VegPizza):
  def prepare(self):
    print('prepare', type(self).__name__)

class HamPizza(NonVegPizza):
  def serve(self, VegPizza):
    print(type(self).__name__, 'is served with Ham on ', type(VegPizza).__name__)
```


사용자가 PizzaStore에 접근해 비채식 미국식 피자를 요청하면 USPizzaFactory가 채식 피자를 준비하고 햄 토핑을
얹어 비채식 피자를 제공한다. (?? 그럼 채식 피자가 아니잖아 ??)


```python
class PizzaStore:
  def __init__(self):
    pass

  def makePizzas(self):;
  for factory in [IndianPizzaFactory(), USPizzaFactory()]:
    self.factory = factory
    self.NonVegPizza = self.factory.createNonVegPizza()
    self.VegPizza = self.factory.createVegPizza()
    self.VegPizza.prepare()
    self.NonVegPizza.serve(self.VegPizza)

pizza_store = PizzaSotre()
pizza_store.makePizzas()

# result
# prepare DeluxVeggiePizza
# ChickenPizza is served with Chciken on DeluxVeggiePizza
# Prepare MexicanVegPizza
# HamPizza is served with Ham on MexicanVegPizza
```



## 팩토리 메소드 vs 추상 팩토리 메소드

팩토리 메소드
- 객체 생성에 필요한 메소드가 사용자에게 노출된다.
- 어떤 객체를 생성할지 결정하는 상속과 서브 클래스가 필요하다.
- 한 개의 객체를 생성하는 팩토리 메소드를 사용한다.

추상 팩토리 메소드
- 관련된 객체 집단을 생성하기 위해 한 개 이상의 팩토리 메소드가 필요하다.
- 다른 클래스 객체를 생성하기 위해 컴포지션을 사용한다.
- 관련된 객체 집단을 생성한다.


## 정리

- 팩토리 메소드 패턴은 인터페이스를 통해 객체를 생성하지만 실제 생성은 서브 클래스가 담당한다.
- 추상 팩토리 메소드는 콘크리트 클래스를 명시하지 않고 관련된 객체의 집단을 생성한다.
