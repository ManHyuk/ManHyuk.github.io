---
layout: post
title: "코틀린에서 Unit과 Nothing"
comments: true
description: ""
keywords: ""
---



코틀린에는 여러 타입이 존재한다.

무언가를 포함하는 타입도 존재하고 무언가에 속해져있는 타입도 존재한다.


그렇다면 최상위에 모든걸 대변할 수 있는 타입은 무엇이 있을까? 라는 생각을 해볼 수 있다.


## Any

자바에서 최상위 타입은 `Object`이고 코틀린에서 최상위 타입은 `Any`이다.

쉽게말해 `Any`는 root 타입이다. 바이트코드로 컴파일시에 정확하게 `Any`는 `Object`로 변환된다.

```kotlin
val greeting: Any = "Hi there!"
```

```java
private final Object greeting = "Hi there!";
```



## Unit

Unit의 기능은 자바에서의 void를 의미 한다. 함수의 반환 타입을 Unit으로 지정하게 된다면, 자바의 void처럼 실질적인 반환 값이 없어야한다. 그리고 Unit은 명시적으로 표현할 필요가 없다.

```kotlin
fun returnsUnit(): Unit {
}
fun returnsUnitExplicitly(): Unit {
    return Unit
}
```

```java
public final void returnsUnit() {
}

public final void returnsUnitExplicitly() {
}
```

함수를 종료하는 표현 방식은 다르지만, 컴파일 결과는 모두 동일하다.


### What is Unit then?
코틀린의 Unit과 자바의 void가 같다면 왜 새로운 Unit을 만들었을까?

코틀린의 Unit은 두 가지 특징이 있다.
1. Unit은 싱글톤 인스턴스이다. 그래서 코틀린에서 Unit이라는 키워드는 타입이면서 동시애 객체이기도 하다.

```kotlin
val unit: Unit = Unit
```

2. Unit은 객체이기 때문에 Any의 자식이다.
```kotlin
val unit: Any = Unit
```

## Nothing

`Nothing`은 어떠한 값도 포함하지 않는 타입이다.
Nothing은 private constructor로 정의되어 있어 인스턴스를 정의할 수 없다.
도큐먼트에는 이렇게 명시되어 있다. `Nothing has no instances. You can use Nothing to represent “a value that never exists`

생성자로 인스턴스를 만들 수 없고, 어떠한 값도 없다면 왜 존재할까?

### Typing functions that never return (or throw exceptions)

- 함수가 반환 될 일이 없을 경우

Unit은 `아무런 값도 반환하지 않는다` 라는 의미를 갖는다. 즉, 반환의 대상이 없을 뿐이지 반환이라는 행위 자체를 하지 않는다는것은 아니다.
하지만 Nothing은 `반환이라는 행위 자체를 하지 않음` 을 뜻한다.

```kotlin
fun infiniteLoop(): Nothing {
    while (true) {
        println("Hi there!")
    }
}
```
위 함수는 무한루프를 돌기 때문에 infinteLoop() 함수가 종료될 일이 없다.

코틀린 컴파일러는 함수가 제대로 반환되지 않을 것이라고 추론할만큼 충분히 똑똑하다.
그렇기 때문에 만약 반환 가능한 함수가 반환 타입을 Nothing으로 정의한다면 컴파일 에러가 나타난다.
즉, 함수가 반환될 경우가 없기에 Nothing을 사용할 수 있다.


- 예외를 던지는 함수를 반환 하는 경우
```kotlin
fun throwException(): Nothing {
    throw IllegalStateException()
}
```

함수에서 예외를 던지는 것은 정상적인 함수의 종료가 아니기 때문에 "함수가 반환되었다" 라고 보지 않는다.

코틀린의 모든 타입은 non-null이 기본이다. 즉 타입이 null이 아님을 보장하고 null 값을 가질 수 있는 경우에는 Nullable을 명시해준다.

### Nothing?

함수의 리턴 타입이 Nothing? 일 경우는 어떨까?

```kotlin
fun mayThrowAnException(throwException: Boolean): Nothing? {
    return if (throwException) {
        throw IllegalStateException()
    } else {
        println("Exception not thrown :)")
        null
    }
}
```

"반환 하지 않는다" 와 "null을 리턴한다" 는 공존할 수 없어 보이지만

1. 종료되지 않는 함수
2. 예외를 던지는 함수
3. null을 리턴하는 함수 (추가)

이렇게 한 가지 더 선택사항이 늘어날 뿐이다.

`Nothing?` 를 반환 타입으로 지정한다면 값은 반드시 `null` 을 리턴 해야 한다

```kotlin
fun main() {
    val result = mayThrowAnException(true)
    if (result == null) { // Always true
        println("Ignored code")
    }
}
```

만약 위와같이 main함수를 호출하게 된다면, 컴파일러는 result가 항상 null일것이라고 힌트를 준다.


---

### Converting throw and return into expressions

예외와 반환을 expression으로 변환하기

함수의 반환 타입으로의 Nothing을 보았다면 이번에는 expression으로서의 Nothing을 보자

expression이란 결과를 갖고있는 연산식이다.

```kotlin
val nullableValue: String? = null
val value = nullableString ?: throw IllegalStateException()
```

nullableValue는 null 값이 들어올 수 있는 변수이다.
그리고 value는 엘비스 연산자를 사용해 null이라면 예외를 던지게 되어있다.

이때 엘비스 연산자에 대응되는 nullableString은 String? 타입이고, throw IllegalStateException()은 Nothing 타입이라서
컴파일 에러를 띄워야할것 같지만 정상적으로 컴파일이 가능하다.

그 이유는 Nothing이 모든 타입의 서브 클래스이기 때문이다.

그렇기 때문에 위 코드의 value는 변수에 대입되는 expression은 항상 String 타입으로 평가 된다.

```kotlin
val nullableValue: String? = null
val value: Int = nullableValue?.toInt() ?: return

// Int? ?: Nothing
```

전 예제와 비슷하지만 throw대신 return이 사용되었다.

return의 타입은 Nothing이다. 앞에서 본 예제와 비슷하다

코틀린에서 `Any`는 모든 타입의 조상이다. 그리고 코틀린에서 `Nothing`은 모든 타입의 자식 클래스이다.

물론 내가 새로만든 클래스의 자식이기도 하다







--- 
참고
- https://itnext.io/any-unit-nothing-and-all-their-friends-e39613b48235
- https://readystory.tistory.com/143?category=815287








