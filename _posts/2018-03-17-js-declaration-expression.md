---
layout: post
title: "자바스크립트 함수선언문, 함수표현문 -4"
comments: false
description: ""
keywords: ""
---



```javascript
function a(){ // 함수선언문
    return 'a';
}

var b = function bb(){ // 기명 함수표현식
    return 'bb';
}
var c = function() { // 익명 함수표현식
    return 'c';
}
```



이전의 브라우저는 익명함수표현식을 사용한다면 디버깅이 어려웠지만 현재는 익명함수도 디버깅 시에 무리 없다



```javascript
var c = function(){
    return'c';
}
// 변수 c선언
// 익명함수 선언
// 변수 c에 익명함수를 할당
```





선언한 함수를 변수에 할당하는것이 함수 표현식



둘의 차이는 '할당'



할당을 하지않으면 전체가 호이스팅

할당하면 변수만 호이스팅



```javascript
function sum(a, b){
    return `${a} + ${b} = ${a+b}`;
}
/* 중략 */
function sum(a,b){
    return a+b;
}
sum(3,4);

// 호이스팅에 의해 아래 sum함수만 실행됨
```



함수표현식을 사용하면 안전하고 예측가능하다.

