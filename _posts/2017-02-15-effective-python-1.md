---
layout: post
title: "Effective Python 1일차"
comments: false
description: ""
keywords: ""
---

## Better Way 1
#### python 3버전 쓰자

- - -

## Better Way 2

#### PEP8 스타일 가이드를 따르자
몇개만 간추리면
1. 탭이 아닌 스페이스(4개)로 들여 쓴다.
2. 한 줄의 문자 길이는 79자 이하여야 한다. (천공카드가 한줄에 80칸이 있는것에 유래한거래 신기하지?, 근데 왜 79자?)
3. 함수, 변수, 속성은 lowercase_underscore 형식을 따른다.
4. if a is not b 사용
5. 빈 값은 임시적으로 False가 된다고 가정
6. 보호 인스턴스 속성은 앞에 underscore 하나
7. 비공개 인스턴스 속성은 앞에 underscore 두개
8. 모듈 수준 상수는 ALL_CAPS
9. import는 표준 라이브러리 모듈, 서드파티 모듈, 자신이 만든 모듈 순으로 구분해야한다

- - -
## Better Way3

#### bytes, str, unicode의 차이점을 알자

python3에서는 bytes, str 두가지 타입으로 문자 시퀀스를 나타냄
bytes는 raw 8bit값을 저장, str은 유니코드 문자를 저장

python2에서는 str과 unicode 두가지 타입으로 문자 시퀀스를 나타냄
str은 raw 8bit값을 저장, unicode는 유니코드 문자를 저장

유니코드 문자 -> 바이너리 데이터 // encode 메서드 사용
바이너리 데이터 -> 유니코드 문자 // decode 메서드 사용

문제점
> 유니코드 문자는 일반적으로 UTF-8 인코딩이 가능하지만
> python3의 str과 python2의 unicode는 연관된 **바이너리 인코딩이없다!!!**
> - UTF-8(혹은 다른 인코딩) 으로 인코드된 문자인 raw 8bit 값을 처리하려는 상황
> - 인코딩이 없는 유니코드 문자를 처리하려는 상황
>
> 그래서 이러한 문제점이 발생한다

해결방법
> 헬퍼 함수를 사용해서 처리할 입력값이 원하는 문자 타입(8bit값, UTF-8등)으로 되어있게 한다.


너무 어려우니 다음에 더 알아 보도록 하자!


- - -

## Better Way 4

#### 복잡한 표현식 대신 헬퍼 함수를 작성하자

```python
'red=5&blue=0&green='  # 이런 쿼리 스트링이 있다면
red = my_values.get('red', [''])[0]or0
green = my_values.get('red', [''])[0]or0
# 빈 문자열, 빈 리스트, 0 모두 암시적으로 False로 만듬
```
위 표현식은 읽기 어렵고 필요한 작업을 다 수행하지도 않음!
> 문장은 짧지만 **한줄에 모든 코드를 넣는건 의미가 없다!!**

```python
red = my_values.get('red', [''])
red = int(red[0]) if red[0] else 0
```
if/else(삼항 조건식)을 이용하면 짧고 더 명확하게 표현할 수 있다.
> 하지만 이러한 로직을 반복해서 사용해야 한다면 **헬퍼 함수**를 만드는게 더 좋다

```python
# 헬퍼 함수
def get_first_int(values, key, default=0):
	found = values.get(key, [''])
    if found[0]:
    	found = int(found[0])
    else:
    	found = default
    return found
```

> 복잡한 표현식이나 같은 로직을 반복한다면 헬퍼 함수를 사용하고 if/else 표현식을 이용하면 or/and 연산자보다 읽기 쉬울 수 있다.

어떻게보면 당연한 이야기지만 잘 안지켰던 부분이었던거 같네ㅋㅎ

- - -

## Better Way 5

#### 시퀀스 슬라이스하는 방법을 알자

슬라이싱 문법의 기본 형태
```python
somelist[start:end] # start 인덱스는 포함, end는 제외
```

리스트의 처음부터 슬라이스할 때는 보기 편하게 0 생략.
리스트의 끝까지 슬라이스 할 때도 마지막 인덱스 생략
```python
assert a[:5] == a[0:5] # 인덱스의 처음 생략
assert a[5:] == a[5:len(a)]
```
인덱스의 끝을 기준으로 계산할 때는 음수를 사용
```python
a[:-1]
a[-3:-1]
# 참고
a[:20] != a[-20:]
```
- - -


## Better Way 6

#### 한 슬라이스에 start, end, stride를 함께 쓰지 말자
```python
somelist[start:end:stride] # stride: 간격
```
```python
# 사용 예
a =['red', 'orange', 'yellow', 'green', 'blue', 'purple']
odds = a[::2] # ['red', 'yellow', 'blue']
evens = a[1::2] # ['orange', 'green', 'purple']
```
편리하지만 stride 문법이 종종 버그를 만듬
```python
# 일반적인 문자열을 역순으로 만드는 예
x = b'mongoose'
y = x[::-1] # b'esoognom'
```
바이트 문자열이나, 아스키 문자에는 잘 동작하지만,
UTF-8바이트 문자열로 인코드된 유니코드 문자에는 잘 동작하지 않음 (한자나 일본어같은?)



```python
# -1을 제외한 음수 값으로 스트라이드를 지정하는 경우
a = ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
a[::2] # ['a', 'c', 'e', 'g']
a[::-2] # ['h', 'f', 'd', 'b']

# 여기부터 헷갈리기 시작
a[2::2] # ['c', 'e', 'g']
a[-2::-2] # ['g', 'e', 'c', 'a']
a[-2:2:-2] # ['g', 'e']
a[2:2:-2] # []
```
대괄호 안에 숫자가 많으면 읽기 어려움, 그래서 start, end 인덱스가 stride와 어떻게 연계되는지 알기 쉽지 않다.
>**특히 stride가 음수인 경우에는 더 어렵다**

해결방법
> stride를 start, end와 함께 사용하지 말자 (stride는 양수로)
> 그래도 꼭 사용해야 한다면 적용한 결과를 변수에 할당해서 쓰자

추가 - 슬라이싱부터하고 스트라이딩을 하면 데이터의 얕은 복사본이 추가로 생김
> itertools의 islice 메서드를 사용하자
