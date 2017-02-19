---
layout: post
title: "파이썬 코딩의기술 3일차"
comments: false
description: ""
keywords: ""
---

## Better Way 11
#### 이터레이터를 병렬로 처리하려면 zip을 사용하자

리스트 컴프리헨션(list comprehension)을 사용하면 소스 리스트(source list)에 표현식을 적용하여 파생 리스트(derived list)를 쉽게 얻을 수 있다.


파생 리스트와 소스 리스트는 서로의 인덱스로 연관되어있다.
두 리스트를 병렬로 순회하려면 소스 리스트인 names의 길이만큼 순회하면 된다.

```python
names = [ 'Cecilia', 'Lise', 'Marie']
letters = [ len(n) for n in names ]

longest_name = None
max_letters = 0
for i in range(len(names)):
	count = letters[i]
    if count > max_letters:
    	longest_name = names[i]
        max_letters = count

# names와 letters를 인덱스로 접근하면 코드 읽기가 어려워짐
# 루프의 인덱스 i로 배열에 접근하는 동작이 두 번 일어남
```

Better Way 10 처럼 enumerate를 사용하면 이런 문제점을 약간 개선할 수 있지만 완벽하지는 않다.
```python
for i, name in enumerate(names):
	count = letters[i]
    if count > max_letters:
    	longest_name = name
        max_letters = count

# 뭔가 아쉽다
```

파이썬3에서 zip은 지연 제너레이터롤 이터레이터 두 개 이상을 감싼다.
zip 제너레이터는 각 이터레이터부터 다음 값을 담은 튜플을 얻어온다.
**zip 제너레이터를 사용한 코드는 다중 리스트에서 인덱스로 접근하는 코드보다 훨씬 명료하다**
```python
for name, count in zip(names, letters):
	if count > max_letters:
		longest_name = name
		max_letters = count
# 깔끔!        
```

내장함수 zip의 두가지 문제
+  파이썬2에서의 zip은 제너레이터가 아님.
	+ 제공한 이터레이터를 모두 순회해서 zip으로 만든 모든 튜플을 반환한다
	+ 이 과정에서 메모리를 많이 사용하여 프로그램이 망가지는 원인이 되기도 함.
+ 입력 이터레이터들의 길이가 다르면 zip이 이상하게 동작한다.
	+ name 리스트에 다른 이름을 추가 했지만 letters의 카운터를 업데이트 하지 않는다면 예상치 못한 결과가 나오게 된다.
```python
names.append('Rosalind')
for name, count in zip(names, letters):
	print(name)
	# Cecilia
	# Lise
	# Marie
```
 **'Rosalind'가 결과에 없다!!!!**

결론
> - 내장 함수 zip은 이터레이터를 병렬로 순회할 때 사용할 수 있다.
> - 길이가 다른 이터레이터를 사용하면 zip은 그 결과를 **조용히** 잘라낸다
> - zip으로 실행할 리스트의 길이가 확신할 수 없다면, 대신 내장 모듈 itertools의 zip_longest를 사용해보자
- - -

## Better Way 12
#### for와 while 루프 뒤에는 else블록을 쓰지 말자

> - if/else문에서 else는 '이전 블록이 실행되지 않으면 이 블록이 실행된다'는 의미다.
> - try/except 문에서 except도 마찬가지로 '이전 블록에서 실패하면 이 블록이 실행된다'고 정의할 수 있다.
> - try/except/else의 else도 '이전 블록이 실패하지 않으면 실행하라'는 뜻!

```python
for i in range(3):
    print('Loop %d' % i)
    if i == 1:
    	break 	# break문을 사용해야 else 블록을 건너뛸 수 있다.
else:
    print('Else block!')

    # Loop 0
    # Loop 1
```
**빈 시퀀스를 처리하는 루프문에서는 else블록이 즉시 실행된다!**
```python
for x in []:
    print('Never runs')
else:
    print('For Else block!')
    # For Else block!

#else블록은 while루프가 처음부터 거짓인 경우에도 실행된다.

while False:
    print('Never runs')
else:
    print('While Else block!')
    # While Else block!
```

루프 다음에 오는 else 블록은 루프로 뭔가를 검색할 때 유용하다.
예를 들어 두 숫자가 서로소인지를 판별한다면, 모든 공약수를 구하고 모든 옵션을 시도한 후에 루프가 끝난다.
else블록은 루프가 break를 만나지 않아서 숫자가 서로소일 때 실행된다.
```python
a = 4
b = 9
for i in ragne(2, min(a, b) + 1):
    print('Testing', i)
    if a % i == 0 and b % i == 0:
        print('Not comprime')
        break
else:
   print('Coprime')

# Testing 2
# Testing 3
# Testing 4
# Coprime
```
**이런 코드를 작성하지 말고 헬퍼 함수를 사용하는게 좋다**

두 가지 스타일의 헬퍼 함수
```python
# 1. 찾으려는 조건을 찾았을 때 바로 반환, 루프가 실패로 끝나면 기본 결과(True)를 반환.
def coprime(a, b):
    for i in ragne(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            return False
     return True

# 2. 루프에서 찾으려는 대상을 찾았는지 알려주는 결과 변수를 사용. 뭔가를 찾으면 즉시 break로 루프를 중단.
def coprime2(a, b):
    is_comprime = True
    for i in ragne(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            is_comprime = False
     return is_comprime
```

결론
> - 파이썬에는 루프문 바로 뒤에 else블록을 사용할 수 있게하는 특별한 문법이 있다.
> - 루프 본문이 break문을 만나지 않은 경우에만 루프 다음에 오는 else블록이 실행된다.
> - **루프 뒤에 else블록 쓰지마라.** 혼동하기쉽고 직관적이지 않다.


- - -
