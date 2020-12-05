---
layout: post
title: "JS에서의 async와 코틀린에서의 async"
comments: true
description: ""
keywords: ""
---


개발을 자바스크립트로 접해서기 때문인지 새로운 언어를 접할때 자바스크립트 기준으로 생각하게 된다.


이번에도 계속 자바스크립트가 계속 떠올라서 두 언어의 차이를 보며 간단하게 맛만 보겠다.


기본적으로 코틀린은 비동기 프로그래밍을 하기위해 여러가지 방법을 제공한다 그중 `코루틴`이라는 편리한 도구를 제공한다.

자바스크립트에서 `async & await`을 생각하면 된다.

자바스크립트에서 비동기 api를 처리하기 위해서는 아래와 같은 형태의 코드를 작성하게 된다.

```javascript
// JS
const getData = async () => {
    retrun await axios.get();
}
```

코틀린에서도 비슷한 형태를 보인다.

```kotlin
// KT
suspend fun getData(): Response {
    return webClient.get()
}
```

자바스크립트에서 `async` 키워드를 코틀린에서 `suspend`를 사용하게 된다.

조금 다른점은 자바스크립트에서 `await` 키워드가 코틀린 코드에 없다. 코틀린의 경우 알아서 기다려 준다. (똑똑한듯)

흔히 비동기 처리를 병렬로 할 경우에는 자바스크립트에서는 `Promise.all` 을 사용하게 되는데 코틀린도 비슷한 형태로 사용이 가능하다.

```javascript
// JS
const getData = async () => {
    const res1 = axios.get();
    const res2 = axios.get();

    return await Promise.all([res1, res2]);
}
```

```kotlin
// KT
suspend fun getData() {
    val deferedList = corutineScope {
        val res1 = webClient.get()
        val res2 = webClient.get()
        listOf(res1, res2)
    }
    retrun deferedList.awaitAll()
}
```

여기서 코틀린의 경우 `corutinScope`라는 새로운 키워드가 등장한다. 

추가로 `globalScope, laucn, async` 등의 키워드가 나오게 되는데 따로 더 정리하도록 해야겠다 
