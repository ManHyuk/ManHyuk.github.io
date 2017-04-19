---
layout: post
title: "Effective Python 6일차"
comments: true
description: ""
keywords: ""
---

## Better Way 15
#### 클로저가 변수 스코프와 상호 작용하는 방법을 알자

숫자 리스트를 정렬할 때 특정 그룹의 숫자들이 먼저 오도록 우선순위를 메기려고 한다고 하자.
이런 패턴은 사용자 인터페이스를 표현하거나, 다른 것 보다 중요한 메세지나 예외 이벤트를 먼저 보여줘야할 때
유용하다.

```python
def sort_priority(values, group):
    def helper(x):
        if x in group:
            return (0,x)
        return (1,x)
    values.sort(key=helper)

numbers = [8, 3, 1, 2, 5, 3, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)

# [2, 3, 5, 7, 1, 4, 6, 8]

```

동작하는 이유는 3가지
1. 파이썬은 클로저를 지원한다.
	> 클로저란 자신이 정의된 스코프에 변수를 참조하는 함수

2. 함수는 파이썬에서 일급객체(first-class object)다.
	> 함수를 직접 참조하고, 변수에 할당하고, 다른 함수의 인수로 전달하고, 표현식과 if 문 등 에서
	> 비교할 수 있다.  
3. 파이썬에는 튜플을 비교하는 특정한 규칙이 있다.
	> 인덱스 0으로 아이템을 비교하고 그 다음으로 인덱스 1, 인덱스 2와 같이 진행한다




다음에 알아보자
