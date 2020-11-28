---
layout: post
title: "코틀린 코루틴"
comments: true
description: ""
keywords: ""
---

## 0. Coroutine


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


## 1. Basic

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




## 2. Cancellation And Timeouts


job.cacel()을 통해 수행중인 코루틴을 중지시킬 수 있다.

```kotlin
fun main() = runBlocking {
    val job = launch {
        repeat(1000) { i -> 
            println("job: I'm sleeping $i ...")
            delay(500L)
        }
    }
    delay(1300L)
    println("main: i'm tired of wating!")
    job.cancel()
    job.join()
    pringln("main: NowI I can quit")
}
```

500ms 마다 `println("job: I'm sleeping $i ...")`가 실행되고 1300ms 이후에 잡이 취소된다.

```console
job: I'm sleeping 0 ... [main]
job: I'm sleeping 1 ... [main]
job: I'm sleeping 2 ... [main]
main: I'm tired of wating!
main: Now I can quit
```


### Coroutine cancellation is cooperative

```kotlin
fun main() = runBlocking {
    val startTime = System.currentTimeMillis()
    val job = launch(Dispatchers.Default) {
        var nextPrintTime = startTime
        var i = 0
        while (i < 5) { // computation loop, just wastes CPU
            // print a message twice a second
            if (System.currentTimeMillis() >= nextPrintTime) {
                // delay(1L)
                // yield()
                println("job: I'm sleeping ${i++} ...")
                nextPrintTime += 500L
            }
        }
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancelAndJoin() // cancels the job and waits for its completion
    println("main: Now I can quit.")
}
```


코루틴이 취소 되려면 코틀린은 협조적이어야한다. (Coroutine cancellation is cooperative)

위 예제코드를 보면 1300ms이후 자연스럽게 코루틴이 취소 될것처럼 보이지만 취소되지 않고 내부에 반복문이 5번 다 수행된다.

그 이유는 코루틴에 협조적이지 않기 때문인데, 이전 예제코드를 본다면 `delay`라는 `suspend` 함수를 사용했는데 이번 예제에도 `delay` 또는 `yield`함수를 넣으면 기대와 같이 2번의 반복문만 수행하게 된다.

`yield`함수를 사용하게 되면 종료될때 `Exception`을 내뱉게 된다. 예외를 받아 코루틴 자체에 강제로 예외를 만들어 핸들링하는 방식이다.

```kotlin
fun main() = runBlocking {
    val startTime = System.currentTimeMillis()
    val job = launch(Dispatchers.Default) {
        try {
            var nextPrintTime = startTime
            var i = 0
            while (i < 5) { // computation loop, just wastes CPU
                // print a message twice a second
                if (System.currentTimeMillis() >= nextPrintTime) {
                    // delay(1L)
                    // yield()
                    println("job: I'm sleeping ${i++} ...")
                    nextPrintTime += 500L
                }
        }
        } catch (e: Exception) {
            println(e) // err
        }
        
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancelAndJoin() // cancels the job and waits for its completion
    println("main: Now I can quit.")
}
```

따라서 코루틴 스스로 종료를 할 수 있게 하는 방법을 알아보자.

### Making computation code cancellable
- way 1 
    - to periodically invoke a suspending (`suspend function` like `delay`,`yield`)
- way 2
    - explicty check the cancelation status (`isActive`)



```kotlin
fun main() = runBlocking {
    val startTime = System.currentTimeMillis()
    val job = launch(Dispatchers.Default) {
        var nextPrintTime = startTime
        var i = 0
        while (isActive) { // cancellable computation loop
            // print a message twice a second
            if (System.currentTimeMillis() >= nextPrintTime) {
                println("job: I'm sleeping ${i++} ...")
                nextPrintTime += 500L
            }
        }
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancelAndJoin() // cancels the job and waits for its completion
    println("main: Now I can quit.")
}
```



`isActive`는 코루틴의 확장 프로퍼티이며 코루틴의 잡이 활성 상태인지 체크한다.


### Closing resources with finally

코루틴을 종료할때 네트워크나, 디비 같은 리소스를 사용하다 갑자기 취소될때 어떻게 리소스를 해제해야 할까?

`finally`에서 해제해주면 된다.


```kotlin
fun main() = runBlocking {
    val job = launch {
        try {
            repeat(1000) { i ->
                println("job: I'm sleeping $i ...")
                delay(500L)
            }
        } finally {
            println("job: I'm running finally")
        }
    }
    delay(1300L) // delay a bit
    println("main: I'm tired of waiting!")
    job.cancelAndJoin() // cancels the job and waits for its completion
    println("main: Now I can quit.")
}
```

## 3.Composing Suspending Functions

```kotlin
suspend fun doSomethingUsefulOne(): Int {
    delay(1000L) // pretend we are doing something useful here
    // async call like restTemplte, db connecton...
    return 13
}

suspend fun doSomethingUsefulTwo(): Int {
    delay(1000L) // pretend we are doing something useful here, too
    // async call like restTemplte, db connecton...
    return 29
}


fun main() = runBlocking {
    val time = measureTimeMillis {
        val one = doSomethingUsefulOne()
        val two = doSomethingUsefulTwo()
        println("The answer is ${one + two}")
    }

    println("Completed in $time ms")
}
```

### the coroutine, just like in the regular code, is sequential by default. 

첫번째 비동기 작업의 결과를 두번째 비동기 작업에 매개변수로 필요하다면 어떻게 해야할까?

기본적으로 코루틴은 순차적으로 동작한다. (자바였다면 콜백을 생성한다거나 CompltedFuture? 같은걸로 한번 랩핑 했던것 같다.)

비동기 코드를 순차적으로 작성하면 알아서 순차적으로 순서를 맞춰준다. -> `Dream Code`

하지만 두 코드는 서로 의존성이 없는데 각 함수가 1초씩 두번 실행되기 때문에 총 2초의 시간이 걸린다.


### Coucurrent using async
```kotlin
fun main() = runBlocking {
    val time = measureTimeMillis {
        val one = async { doSomethingUsefulOne() }
        val two = async { doSomethingUsefulTwo() }
        println("The answer is ${${one.await()} + ${two.await}}")
    }

    println("Completed in $time ms")
}
```

각 비동기 함수가 의존성이 없다면 `동시적(concurrency)`으로 `명시` 하면 2초가 아닌 1초에 끝낼 수 있다. (JS로 생각한다면 `Promise.all()` 같은 느낌)

`async` 키워드가 뭐 길래 왜 `await`할 수 있을까의 대답은 async는 `job`을 상속받은 `Deferred`객체 이기 때문이다.


### Lazily started async
만약 비동기 처리를 바로 시작하는게 아닌 따로 시작하는 시점을 핸들링하는것도 가능하다

```kotlin
fun main() = runBlocking {
    val time = measureTimeMillis {
        val one = async(start = CoroutineStart.LAZY) { doSomethingUsefulOne() }
        val two = async(start = CoroutineStart.LAZY) { doSomethingUsefulTwo() }
        
        // some computation
        one.start() // start the first one
        two.start() // start the second one
        println("The answer is ${one.await() + two.await()}")
    }
    println("Completed in $time ms")
}
```

만약 `LAZY`모드에서 `START`를 하지 않는다면 동시에 실행되지 않는다.

### Async Style Fucntions



```kotlin
fun main() {
    val time = measureTimeMillis {
        val one = somethingUsefulOneAsync()
        val two = somethingUsefulTwoAsync()
        
        runBlocking {
            println("The answer is ${one.await() + two.await()}")
        }
    }
    println("Completed in $time ms")
}
// The result type of somethingUsefulOneAsync is Deferred<Int>
fun somethingUsefulOneAsync() = GlobalScope.async {
    doSomethingUsefulOne()
}

// The result type of somethingUsefulTwoAsync is Deferred<Int>
fun somethingUsefulTwoAsync() = GlobalScope.async {
    doSomethingUsefulTwo()
}
```

위와 같은 형태로 `GlobalScope`를 함수 감싸서 사용하게 된다면 어디에서나 사용할 수 있다는 장점을 가지고 있다.

이렇게 사용하는 경우 중간에 Exception이 발생해도 모든 코루틴이 작동하며, 에러를 트래킹할 수 없다. 따라서 코틀린 코루틴에서는 이런 방식을 권장하지 않는다.



### Structured concurrency with async

```kotlin

fun main() = runBlocking {
    val time = measureTimeMillis {
        println("The concurrentSum is ${concurrentSum()}")
        
    }
    println("Completed in $time ms")
}

suspend fun concurrentSum(): Int = coroutineScope {
    val one = async { doSomethingUsefulOne() }
    val two = async { doSomethingUsefulTwo() }
    one.await() + two.await()
}
```

`coroutineSocpe`를 통해 코루틴 함수를 받을 수 있는 구조에서만 사용이 가능한 함수이며 Exception이 발생한다면 모든 코루틴이 취소 된다.

