---
layout: post
title: "Effective Python 2일차"
comments: false
description: ""
keywords: ""
---



## Better Way 7
#### map과 filter 대신 리스트 컴프리헨션을 사용하자

파이썬에선 리스트에서 다른 리스트를 만들어내는 문법이 있다.
이 문법을 사용한 표현식을 리스트 컴프리헨션(list comprehension)이라고함
```python
a = [1,2,3,4]
squares = [x**2 for x in a] # [1, 4, 9, 16]
```
간단한 연산에서는 list comprehension이 더 명확함
심지어 출력값을 삭제도 가능하다
```python
even_squares = [x**2 for x in a if x % 2 ==0]
# [4, 16]
```

내장함수 map과 filter를 이용하면 같은 결과를 얻을 수 있지만
읽기 어렵다
```python
alt = map(lambda x: x**2, filter(lambda x: x % 2 ==0, a))
```
뭔가 있어보이지만 간결하지 않다

결론
> - list comprehension 쓰도록 하자.
> - 딕셔너리와 set도 사용할 수 있다.
> - map과 filter는 멋있지만 자제 하자.

---
## Better Way 8
#### 리스트 컴프리헨션에서 표현식을 두 개 넘게 쓰지 말자
리스트 컴프리헨션에서는 다중 루프와 다중 조건을 지원한다

```python
# 사용 예
matrix = [[1,2,3], [4,5,6], [7,8,9]]
flat = [x for row in matrix for x in row]
# [1, 2, 3, 4, 5, 6, 7, 8, 9]
```
코드가 짧지만 읽기 어렵다.
안어렵다면 다음 코드를 보자
```python
filtered = [[x for x in row if x % 3 ==0]
	    for row in matrix if sum(row) >= 10]
```
반복문안에 조건문까지 포함되어있다 복잡하다!!

```python
flat = []
for sublist1 in my_list:
	for sublist2 in sublist1:
    	flat.extend(sublist2)
```
결론
> - 표현식이 두개가 넘어가면 사용을 자제하자.
> - 위처럼 코드를 작성하면 길지만 읽기 쉽다.

---
## Better Way 9
#### 컴프리헨션이 클 때는 제너레이터 표현식을 고려하자

리스트 컴프리헨션의 문제점
> - 입력 시퀀스에 있는 각 값별로 아이템을 하나씩 담은 새 리스트를 통째로 생성함.
> - 입력값이 적을 때는 괜찮지만 클때는 메모리를 많이 소모함.

참고
> 리스트에 특정 값(a[0]와 같은)을 수정할때는 생성된 리스트에서 직접적으로 값을 수정 하지만,
> 값을 추가하거나 전체를 수정하면 리스트를 메모리에서 재할당하기에 메모리소모가 커짐.

파이썬은 이러한 문제를 해결하기 위해 제너레이터 표현식(generator expression)을 제공한다.
> - 제너레이터 표현식은 실행될 때 출력 시퀀스를 모두 구체화(메모리에 로딩) 하지 않는다.
> - 대신 이터레이터로 평가된다. (한번씩 진행된다는 뜻)

사용방법
```python
# list comprehesion
value = [len(x) for x in open('/tmp/my_file.txt')] # [100, 57, 15, 1 ... ]

# generator
it  = (len(x) for x in open('/tmp/my_file.txt')) # <generator object <genexpr> at 0x123123123>
print(next(it)) # 100
print(next(it)) # 57
```

**제너레이터의 강점은 또 다른 제너레이터와 사용 가능하다는 것!!!**
```python
roots = ((x, x**0.5) for x in it)
print(next(it)) # (15, 3.872983346207417)
```

제너레이터를 연결하면 파이썬에서 매우 빠르게 실행 할 수 있다.
큰 입력 스트림에 동작하는 기능을 결합하는 방법을 찾을 때는 제너레이터 표현식이 최선의 도구다.
단, 제너레이터 표현식이 반환한 이터레이터에는 **상태**가 있으므로 이터레이터를 한 번 넘게 사용하지 않도록 주의해야 한다. (Better Way 17에서 다룰 예정)

결론
> - 리스트 컴프리헨션은 큰 입력을 처리할때 메모리 소모가 심해서 문제를 일으킬 수 있다.
> - 제너레이터는 이터레이터로 한 번에 한번씩 출력 하므로 메모리 문제를 피할 수 있다.
> - 제너레이터 표현식은 서로 연결되어 있을 때 짱짱 빠르다.

---


## Better Way 10
#### range보다는 enumerate를 사용하자

내장 함수 range는 정수 집합을 순회(iterate)하는 루프를 실행할때 유용하다.

```python
flavor_list = ['syntha-6', 'chocolate', 'banana', 'python']
for i in range(len(flavor_list)):
	flavor = flavor_list[i]
    print('%d: %s' %(i+1, flavor))
    # 1: syntha-6
    # 2: chocolate ...
```
range를 사용하여 flavor 리스트를 순차적으로 출려하는 코드인데
이 코드는 리스트의 길이를 알아내야 하고, 배열을 인덱스로 접근해야하며, 읽기 불편하다

이런 상황에서는 내장 함수 **enumerate**를 사용하자!!!

enumerate는 lazy generator로 이터레이터를 감싼다.
```python
# enumerate 사용 예
for i, flavor in enumerate(flavor_list):
	print('%d: %s' %(i+1, flavor)) # 출력 값은 위의 코드와 같음

# enumerate로 세기 시작할 숫자를 지정할 수 있다.
for i, flavor in enumerate(flavor_list, 1):
	print('%d: %s' %(i, flavor)) # 출력 값은 위의 코드와 같음
```

결론
> - enumerate는 이터레이터를 순회하면서 이터레이터에서 각 아이템의 인덱스를 얻어오는 간결한 문법을 제공한다.
> - range로 루프를 실행하고 인덱스로 접근하기 보다는 enumerate를 사용하자.
> - enumerate에 두 번째 파라미터를 사용하면 세기 시작할 숫자를 지정할 수 있다. (기본값은 0)

---
