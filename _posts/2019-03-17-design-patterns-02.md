---
layout: post
title: "파이썬 디자인 패턴 - 02"
comments: true
description: ""
keywords: ""
---

## 싱글톤 패턴


### 개요

싱글톤 패턴은 글로벌하게 접근 가능한 단 한개의 객체만을 허용하는 패턴이다.
로깅, 디비 접근등 앱에서 동일한 리소스에 대한 동시 요청의 충돌을 막기 위해 한 개의 인스턴스만 필요한 경우에 주로 쓰인다.

싱글톤 패턴의 목적은 다음과 같다.
- 클래스에 대한 단일 객체 생성
- 전역 객체 제공
- 공유된 리소스에 대한 동시 접근 제어


생성자(Constructor)를 private로 선언하고 객체를 초기화하는 static 함수를 만들면 간단하게 싱글톤을
구현할 수 있다.

파이썬에서 생성자를 private 선언할 수 없기 때문에 다른 방법으로 구현해야 한다.


### 구현

- 한 개의 싱글톤 클래스 인스턴스를 생성한다.
- 이미 생성된 인스턴스가 있다면 재사용한다.

```python
class Singleton(object):
  def __new__(cls):
    if not hasattr(cls, 'instance'):
      cls.instance = super(Singleton, cls).__new__(cls)
      return cls.instance

s = Singleton()
print('Object created s', s)

s1 = Singleton()
print('Object created s1', s1)


# result
# Object created s <__Main__.Singleton object at 0x123123>
# Object created s1 <__Main__.Singleton object at 0x123123>
```

new 함수 (파이썬 전용 특수 생성자) 를 오버라이드해 객체를 생성한다. 그리고 new 함수는 s 객체가 존재하는지
확인하고 hasattr 함수 (속성을 가지고 있는지 확인하는 함수) 를 통해 cls 객체가 instance 속성을 가지고 있는지 확인한다.

s1 객체를 요청하면 hasattr()은 이미 객체가 생성됐음을 확인하고 해당 인스턴스를 반환한다.


### 게으른 초기화

게으른 초기화는 인스턴스가 꼭 필요할 때 생성한다. 즉 모듈을 임포트할 때 아직 필요하지 않은 시점에 객체를 미리 생성해 두지 않는다.


사용할 수 있는 리소스가 제한적인 상황일때 객체가 꼭 필요한 시점에 생성하는 방법이다.


```python
class Singleton:
  __instance__ = None
  def __init__(self):
    if not Singleton.__instance:
      print('__init__ method called')
    else :
      print('Instance already created', self.getInstace())

  @classmethod
  def getInstace(cls):
    if not cls.__instance:
      cls.__instance = Singleton()
    return cls.__instance


# 클래스를 초기화했지만 객체는 생성하지 않음
s = Singleton()

# 객체 생성
print('Object created ', Singleton.getInstace())

# 위 라인에서 이미 객체는 생성됨
s1 = Singleton()
```


### 모듈 싱글톤

파이썬의 임포트 방식 때문에 모든 모듈은 기본적으로 싱글톤이다.

- 파이썬의 작동 방식
  1. 파이썬 모듈이 임포트됐는지 확인한다.
  2. 임포트됐다면 해당 객체를 반환하고 안 됐다면 임포트하고 인스턴스화 한다.
  3. 모듈은 임포트와 동시에 초기화된다. 하지만 같은 모듈을 다시 임포트하면 초기화하지 않는다.


### 모노스테이트 싱글톤 패턴

모노스테이트 싱글톤 패턴은 모든 객체가 같은 상태를 공유하는 패턴이다.


```python
class Borg:
  __shared_state = {'a':'1'}
  def __init__(self):
    self.x = 1
    self.__dict__ = self.__shared_state
    pass


b = Borg()
b1 = Borg()
b.x = 4

print('b ', b)
print('b1 ', b)
print('b state', b.__dict__)
print('b1 state', b1.__dict__)

# result

# b, b1 주소값이 다름
# b <__main__.borg object at 0x123123>
# b1 <__main__.borg object at 0x123124>

# b state {'x':4 , 'a':'1'}
# b1 state {'x':4 , 'a':'1'}
```


### 싱글톤 패턴의 단점

- 전역 변수의 값이 실수로 변경된 것을 모르고 앱에서 사용될 수 있다.
- 같은 객체애 대한 여러 참조자가 생길 수 있다.
  - 싱글톤은 한 개의 객체만을 만들기 때문에 같은 객체에 대한 여러 개의 참조자가 생긴다
- 전역 변수에 종속적인 모든 클래스 간 상호관계까 복잡해진다.
  - 전역 변수 수정이 의도치 않게 다른 클래스에도 영향을 줄 수 있다.


### 요약
- 개발할 때 스레드 풀과 캐시, 레지스트리 설정 등 한객체만 필요한 경우에 사용한다.
- 싱글톤은 글로벌 액세스 지점을 제공하는, 단점이 거의 없는 검증된 패턴이다.
- 단점은 전역 변수가 의도치 않게 다른 클래스에게 영향을 줄 수 있으며 리소스를 많이 사용하는 구조가 될 수 있다.
