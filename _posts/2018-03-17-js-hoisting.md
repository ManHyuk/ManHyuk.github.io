---
layout: post
title: "자바스크립트 호이스팅 -2"
comments: false
description: ""
keywords: ""
---



#### Hoisting

변수 '선언' 과 함수'선언'을 끌어올린다.

호이스팅은 선언과 밀접한 관계를 가진다.

자바스크립트 엔진은 코드를 실행하기 전에 모든 선언문들을 위로 끌어올림.

```javascript
console.log(a()); // a
console.log(b()); // err
console.log(c()); // err

function a() {
    return 'a';
}
var b = function bb() {
    return 'bb';
}
var c = function() {
    return 'c';
}

// 내부 동작

function a(){ // 함수는 선언과 동시에 할당
    return 'a';
}
var b; // 변수는 선언이 먼저 됨
var c;
console.log(a());
console.log(b());
console.log(c());

b = function bb(){
    return 'bb';
}
c = function(){
    return 'c';
}
```



선언문은 항시 자바스크립트 엔진 구동시 가장 최우선으로 해석하고 **할당 구문은 런타임 과정에서 이루어지기 때문에,** 호이스팅 되지 않는다.

함수가 자신이 위치한 코드에 상관없이 함수 선언문 형태로 정의한 함수의 유효범위는 전체 코드의 맨 처음부터 시작한다. 함수 선언이 함수 실행 부분보다 뒤에 있더라도 자바스크립트 엔진이 함수 선언을 끌어올리는 것을 의미한다. 함수 호이스팅은 함수를 끌어올리지만 변수의 값은 끌어올리지 않는다.





> 참고 1) inflearn / jsflow
>
> 참고 2)
