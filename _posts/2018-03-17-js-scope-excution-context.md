---
layout: post
title: "자바스크립트 스코프와 실행 컨텍스트 -3"
comments: false
description: ""
keywords: ""
---

 



Scope 유효범위 (변수)

- 정의 될때 결정



Execution Context 코드 실행 덩어리(추상적 개념)

- 실행될 때 생성
- 호이스팅, this 바인딩 등의 정보가 담긴다.
- 사용자가 함수를 호출했을때 외부적으로 해당 함수를 호출하기 위한 필요한 정보들을 모아놓은 집합체



```javascript
var a = 1;
function outer(){
    console.log(a); // 1 1
    function inner(){
        console.log(a); // 2 undefined
        var a = 3;
    }
    inner();
    console.log(a); // 3 1
}
outer();
console.log(a); // 4 1
```



![scope](/images/js-flow/js-scope-context.png)

