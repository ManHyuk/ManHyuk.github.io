---
layout: post
title: "코틀린 코루틴"
comments: true
description: ""
keywords: ""
---


`코루틴`이란 루틴의 일종이며 자신이 마지막으로 `중단`되었던 지점에서 다시 `재개` 가 가능하다.

일반적으로 사용하는 쓰레드보다 더 가볍다고 한다.


```kotlin
// the dream code
val user = fetchUserData() // NetworkOnMainThreadException
textView.text = user.name
```

`featchUserData` 함수는 서버에서 유저 데이터를 가져오는 기능이라고 가정했을때 

위와 같은 코드에서 서버 통신 에러가 난다면 메인 스레드에서 에러가 나는것이기 때문에 앱이 터져버리게 된다.

이를 개선하기 위해서 아래와 같은 코드를 떠올리게 된다.

```kotlin
// the dream code v2
thread {
    val user = fetchUserData()
    textView.text = user.name // CalledFromWrongThreadException
}
```

메인 스레드가 아닌 스레드를 하나 생성하여 API 호출을 하게 된다. 하지만 이 코드에선
UI를 그리는 메서드는 메인 스레이드에서 호출되어야 하기 때문에 에러가 나타난다.
(`textView.text`를 메인 스레드가 아닌 새로 생성한 스레드에서 호출했기 때문에)

그럼 어떻게 해야할까? 흔히 사용 하는 방법은 `Callback` 이다.

```kotlin
featchUserData { user -> // callback
    textView.text = user.name
} 
```

`callback`으로 넘기는 순간 컨텍스트 스위칭이 되며 메인 스레드로 넘기게 된다.

코드는 동작하지만 콜백이 많아지게 된다면 콜백 지옥을 볼 수 있게 된다.

요약 하자면

```kotlin
fun loadUser() {
    val user = api.fetchUser()
    show(user)
}
```

위와 같은 코드는 메인 스레드만 사용하기 때문에 fetchUser에서 에러가 난다면 show 함수까지 도달하지 못하게 된다.

fetchUser -> onDraw -> onDraw -> onDraw -> show

하지만 콜백을 사용하게 된다면

```kotlin
fun loadUser() {
    api.fetchUser{ user -> show(user) }
}
```

fetchUser -> NetworkThread -> onDraw -> onDraw -> onDraw -> show

새로운 스레드를 생성하여 안전하게 호출이 가능하다.

하지만 여기서 더 나아가 코틀린은 더 편한 방법을 제시한다

```kotlin
suspend fun loadUSer() {
    val user = api.fetchUser()
    show(user)
}
```

`suspend` 키워드를 붙이게 된다면 내부적으로 콜백함수와 같이 새로운 스레드를 생성하게 된다. 그리고 코드를 더 읽기 쉽게 해준다는 장점도 가지고 있다.


여기서부터 코틀린의 코루틴이 시작된다!



```kotlin
fun main() {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    Thread.sleep(2000L)
}
```

위에서 아래로 코드를 읽게 된다면 `World!` -> `Hello,` 순으로 출력 될것 같지만 `Hello,` -> `World!` 순으로 출력되게 된다.

`GlobalScope.launch`에서 코루틴이 생성되게 된다. 즉 새로운 스레드가 생성 된다. 

```kotlin
fun main() {
    thread {
        Thread.sleep(1000L)
        println("World!")
    }
    println("Hello,")
    Thread.sleep(2000L)
}
```

스레드를 직접 생성하고 실행해도 동일한 결과를 볼 수 있다.



```kotlin
fun main() {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    runBlocking {
        delay(2000L)
    }
}
```

`Thread.sleep` 함수는 메인 스레드를 `blocking` 하는 함수이다, 위 코드에선 `Thread.sleep` 와 `delay`가 혼용되고 있으니 `delay`로 변경하겠다.

하지만 `delay` 함수는 `suspend`함수 또는 `CorutineScope` 작동하는 함수기 때문에 `runBlocking`을 선언하고 그 안에 함수를 넣어줬다.

`runBlocking`은 `launch`와 같은 코루틴 빌더인데, runBlocking은 자신을 호출한 스레드가 완료될때까지 막는 특성을 가지고 있다.


예제 코드를 조금 더 관용적인 코드로 변경해보겠다.


```kotlin
fun main() = runBlocking {
    GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    delay(2000L)    
}
```

`runBlocking`을 최상단으로 올렸다. 하지만 매번 기다리기 위해 `delay` 함수를 사용하는건 좋은 방법이 아닌것 같다, 여기서 한번 더 수정을 하게 된다면

```kotlin
fun main() = runBlocking {
    val job = GlobalScope.launch {
        delay(1000L)
        println("World!")
    }
    println("Hello,")
    job.join()
}
```

`job.join()`을 통해 명확하게 `Hello` 출력후에 코루틴을 실행시킨다는것을 알 수 있게 되었다.

하지만 job이 여러개가 된다면 여러번 join을 해줘야 하고, 혹시나 join을 잊게 된다면 예상치 못한 동작을 초래할 수 있다. 이렇게 된다면 유지보수가 어려워진다.


```kotlin
fun main() = runBlocking {
    launch {
        delay(1000L)
        println("World!")
    }
    launch {
        delay(1000L)
        println("World!!!!!")
    }
    println("Hello,")
}
```

`GlobalScope`를 지우고 `runBlocking` 내부의 스코프를 사용한다면 더 직관적으로 사용이 가능하다












