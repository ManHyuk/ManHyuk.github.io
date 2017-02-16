---
layout: post
title: "파이썬 코딩의기술 2일차"
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
> list comprehension 쓰도록 하자
> 딕셔너리와 set도 사용할 수 있다
> map과 filter는 멋있지만 자제 하자

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
>표현식이 두개가 넘어가면 사용을 자제하자
>위처럼 코드를 작성하면 길지만 읽기 쉽다

---
## Better Way 9
#### 컴프리헨션이 클 때는 제너레이터 표현식을 고려하자

리스트 컴프리헨션의 문제점
> 입력 시퀀스에 있는 각 값별로 아이템을 하나씩 담은 새 리스트를 통째로 생성함
> 입력값이 적을 때는 괜찮지만 클때는 메모리를 많이 소모함
