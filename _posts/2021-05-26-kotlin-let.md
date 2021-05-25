---
layout: post
title: "코틀린 Nullable"
comments: true
description: ""
keywords: ""
---


Java8에 Optional 타입과 kotlin에 nullabe 타입에 대해 알아보자

코틀린은 Nullabe 타입을 지원하기 때문에 Optional 타입이 존재하지 않는다.
따라서 Optional과 함께 제공되는 api도 존재하지 않는다.


자바에선 Optional을 사용하는것에 대해서 많은 논란이 있지만

코틀린에서 Nullabe 타입을 사용하는것에 대한 논란은 존재하지 않는다
그러니 무조건 써라 ㅎㅎ

### 선언

```java
public interface Person {
    String getName();
    Optional<Integer> getAge();
}
```

자바에선 위와같이 Optional타입을 사용할 수 있다

하지만 코틀린에선 더 간단하게 표현할 수 있다.

```kotlin
interface Person {
    val name: String,
    val age: Int?
}
```

### 접근

또한 자바에서는 옵셔널로 감싸진 객체애 대해서는 map 메서드를 통해야만 안전한 접근이 가능하다

```java
public interface Car {
    Person getDriver();
}
Optional<Car> car = getNextCarIfPresent();
Optional<Integer> driverAge = car.map(c -> c.getDriver()).flatMap(p -> p.getAge());
```

Optional car에서 드라이버를 검색 한 다음 해당 드라이버의 Optional age를 조회한다

두 요소는 옵셔널이므로 중첩이되기 때문에 flatMap으로 접근해야한다


```kotlin
interface Car {
    val driver: Person
}
val car: Car? = getNextCarIfPresent()
val driverAge: Int? = car?.driver?.age
```

코틀린에서는 더 간단하게 접근이 가능하다


### map과 let

때로는 옵셔널 타입의 연결코드 내에서 외부 메소드를 사용해야 하는 경우가 있는데,

이때 자바 옵셔널 타입을 사용하면 map 메서드를 해당 용도로 사용할 수 있다.

```java
Optional<DriversLicence> driversLicence =
    car.map(c -> c.getDriver())
        .map(licenceService -> licenceService.getDriversLicense());
```

코틀린에서는 표준 라이브러리 let 함수를 사용해서 연결코드 내에서 외부 함수를 호출해야 한다.


```kotlin
val driversLicence: DriversLicence? = car?.driver?.let {
    licenceService.getDriversLicence(it) 
}
```

### 값이 없을 경우

옵셔널 타입으로 감싸진 값을 검색시 값이 없는 경우에 orElse 함수를 통해 디폴트 값을 설정할 수 있다.

```java
boolean isOfLegalAge =
    car.map(Car::getDriver).flatMap(Person::getAge).orElse(0) > 18;
```

동일하게 코틀린에서는 엘비스 연산자 '?:' 를 사용한다

```kotlin
val isOfLegalAge: Boolean = car?.driver?.age ?: 0 > 18
```


### 강제 할당

옵셔널의 값이 비어있지 않다는게 확실 하다면, get 메소드를 통해 값에 접근할 수 있다.

하지만 값이 없다면 예외가 발생한다.

```java
boolean isOfLegalAge =
    car.map(Car::getDriver).flatMap(Person::getAge).get() > 18;
```


코틀린에서는 !! 연산자를 사용하면 된다
```kotlin
val isOfLegalAge: Boolean = car?.driver?.age!! > 18
```


### filter

옵셔널에서 래핑된 값의 조건을 확인하기 위해 filter 메소드를 사용할 수 있는데

매번 옵셔널로 감싸진 객체 그 자체를 반환한다, 따라서 빈값일 경우도 고려해야한다.

```java
Optional<Person> illegalDriver =
    car.map(Car::getDriver).filter(p -> p.getAge().orElse(0) < 18);
```


코틀린에서는 표준 라이브러리 함수인 takeIf 를 사용하여 코드 수를 줄일 수 있다.

```kotlin
val illegalDriver: Person? = car?.driver?.takeIf { it.age ?: 0 < 18}
```


### ifPresent

옵셔널에서 값을 확인하고 가공하기 위해서 ifPresent 메소드를 사용한다

```java
car.map(Car::getDriver)
    .filter(person -> person.getAge().orElse(0) < 18)
    .ifPresent(illegalDriver -> {
        checkIdentity(illegalDriver);
        putInJail(illegalDriver);
    });
```

코틀린에서는 더 간단하게 let 함수를 사용해 구현 가능하다


```kotlin
car?.driver?.takeIf { it.age ?: 0 < 18 }?.let { illegalDriver ->
  checkIdentity(illegalDriver)
  putInJail(illegalDriver)
}
```



코틀린의 Nullabe 타입의 처리를 단순화 하는 표준 라이브러리 함수들을 사용한다면 

더 짧고 간결한 가독성 좋은 코드를 작성할 수 있다.

그 외에 장점으로는

1. 옵셔널로 감싸진 래퍼 객체를 따로 만들지 않기에 런타임시에 추가적인 오버헤드가 적다.
2. Nullabe 타입은 컴파일 타임에 널 안정성을 제공한다. 안전하지 않은 방법을 사용한다면 컴파일 타임 오류가 난다.
옵셔널의 경우 안전하지 않은 방법을 사용한다고 하더라도 컴파일러는 검사하지 않고 런타임시에 예외가 발생할 수 있다.


