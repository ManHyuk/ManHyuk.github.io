---
layout: post
title: "파이썬 코루틴, 제너레이터 간단 요약"
comments: false
description: ""
keywords: ""
---

파이썬을 공부하면서 코루틴, 제너레이터에 관한 글을 많이 봤는데

대략적인 개념만 이해하고 있으니 깊은 이해가 뒤따라 오지 않아

다시 정리해 본다.

#### 코루틴(Co-Rutine)

- Sub Rotine: 일반적인 함수를 이야기함
  - 진입점이 하나, 부모/자식의 종속적 관계 성립
  - 매 호출시 마다, Rotine내 context가 초기화됨

- Co Routine: 코루틴
  - 진입점이 여럿이며, **병렬(Concurrensy, not Parallelism)** 수행
  - 호출부와 대등한 관계
  - 여러번 호출이 되어도, Routine 내 Context가 유지

> 코루틴은 서로 대화하는 형식의 함수

#### 제너레이터

- 연속된 (Sequence) 값들을 생산해내는 함수
- 함수에 yield 키워드가 쓰여지면, Generator
- yield 한 값들이 순차적으로 생산된다.
- Generator에서 return문을 만나더라도 종료만 될 뿐, 리턴값이 사용 되지는 않는다.

값을 리턴하는 대신, yield 구문을 이용하여 값을 생성. (보통은 이를 루프에 연결해서 많이 사용한다.)

제너레이터 함수는 제너레이터 객체를 리턴한다. 그리고 이 객체는 자동으로 실행되지 않는다.

.next()가 호출되면 그제서야 실행을 시작한다. 그리고 제너레이터가 리턴을 하게되면 순회가 끝난다.

 순회가 끝난 제너레이터를 다시 실행하려고 하면 StopIteration 예외가 발생한다. (for 루프는 이 예외가 발생할 때까지 제너레이터를 돌리게 된다.)


#### 비교
제너레이터 = 반복이 목적

코루틴 = 외부와의 상호작용





---
참고 1) http://bluese05.tistory.com/56

참고 2) https://nomade.kr - 파이썬 코루틴, 제너레이트 에피소드

참고 3) http://haerakai.tistory.com/36

참고 4) https://soooprmx.com/archives/5622
