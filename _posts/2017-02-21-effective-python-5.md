## Better Way 14
#### None을 반환하기 보다는 예외를 일으키자.

유틸리티 함수를 작성할 때 반환 값 None에 특별한 의미를 부여하는 경향이 있다.
예를 들면 숫자를 나눌때 0으로 나누는 경우에는 결과가 정의되어 있지 않기 때문에 None을 반환하는게 자연스럽다.

```python
def divide(a, b):
    try:
        return a / b
    except ZeroDivisionError:
        return None
        
# 일반적인 경우        
result = divide(x, y)
if result is None:
    print('Invalid inputs') 
    
# x = 0, y = 5인 경우
result = divde(x, y) 
if not result:
    print('Invalid inputs') # 잘못됨
```
분자가 0이되면 반환 값도 0이 되어버림

  
이러한 오류를 줄이는 방법은 두가지다.
1.  반환 값을 두 개로 나눠서 튜플에 담는것
```python
def divide(a, b):
    try:
        return True, a / b
    except ZeroDivisionError:
        return False, None
```
> 이 함수를 호출하는 쪽에서는 튜플을 풀어야 하기 때문에 결과만을 얻을게 아니라 튜플안의 상태까지 고려해야 한다.


2.  절대로 None을 반환하지 않는 것이다.
대신에 호출하는 쪽에 예외를 일으켜 그 예외를 처리하게 하는 것이다.


```python
# 호출하는 쪽에 입력값이 잘못됐음을 알리기위해 ZeroDivisionError을 ValueError로 변경함
def divide(a, b) :
    try:
        return a / b
    except ZeroDivisionError as e:
        raise ValueError('Invlid inputs') as e
```

호출하는 쪽에서는 잘못된 입력에 대한 예외를 처리해야 됨.
```python
# x = 5, y = 2
try:
    result = divide(x, y)
except ValueError:
    print('Invalid inputs')
else:
print('Result is %.1f' % result)
```

결론
> - None을 반환하는 함수가 오류를 일으키기 쉬운 이유는 None, 0, 빈 문자열이 조건식에서 False로 평가하기 때문이다.
> - 특별한 상황을 알릴때 None을 반환하는 대신에 예외를 일으키자
> - 점점 내용이 줄어든다...


