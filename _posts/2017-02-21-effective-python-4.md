---
layout: post
title: "Effective Python 4일차"
comments: false
description: ""
keywords: ""
---

## Better Way 13
#### try/except/else/finally에서 각 블록의 장점을 이용하자

- finally 블록
	- 예외가 발생해도 정리 콛를 실행하고 싶을때 try/finally를 사용하면 된다.
	- 일반적인 사용 예 중 하나는 파일 핸들러를 제대로 종료하는 작업이다.

```python
handle = open('tmp/random_data.txt') # IOError가 일어날 수 있음
try:
    data = handle.read() # UnicodeDecodeError가 일어날 수 있음
finally:
    handle.close() # try: 이후에 항상 실행됨
```

- else 블록
	- 코드에서 어떤 예외를 처리하고 어떤 예외를 전달할지를 명확하게 하려면 try/except/else를 사용한다.
	- try 블록이 예외를 일으키지 않으면 else 블록이 실행된다.

```python
def load_json_key(data, key):
    try:
        result_dict = json.loads(data)  # ValueError가 일어날 수 있음
    except ValueError as e:
        raise KeyError from e
    else:
        return result_dict[key]	# KeyError가 일어날 수 있음
```

- 모두 함께 사용하기
	- 복합문 하나로 모든 것을 처리하고 싶다면 try/except/else/finally를 사용 하면 된다.
	- 파일에서 수행할 작업 설명을 읽고 처리한 후 즉석에서 파일을 업데이트 한다고 가정한다면
		- try 블록은 파일을 읽고 처리하는 데 사용한다.
		- except 블록은 try 블록에서 일어난 예외를 처리 하는 데 사용한다.
		- else 블록은 파일을 즉석에서 업데이트하고 이와 관련한 예외가 전달되게 한다.


```python

UNDEFINED = object()

def divide_json(path):
    handle = open(path, 'r+') # IOError가 일어날 수 있음
    try:
        data = handle.read() # UnicodeDecodeError가 일어날 수 있음
        op = json.loads(data) # ValueError가 일어날 수 있음
        value - (
            op['numerator'] /
            op['denominator']) #ZeroDivisonError가 일어날 수 있음
    excpet ZeroDivisionError as e:
        return UNDEFINED
    else:
        op['result'] = value
        result = json.dumps(op)
        handle.seek(0)
        handle.write(result) # IOError가 일어날 수 있음
        return value
    finally:
    handle.close() # 항상 실행함
```
- - -
