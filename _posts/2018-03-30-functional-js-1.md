---
layout: post
title: "함수형 자바스크립트 -1"
comments: false
description: ""
keywords: ""
---


#### 순수함수

- 동일한 인자를 넣으면 동일한 결과를 나타내는 함수

- 사이드 이펙트가 없기 때문에 동일한 입력값을 넣으면 언제나 동일한 출력값이 나온다.



#### 특징
- 평가시점이 중요하지 않다.
  - 언제 실행하던 동일한 출력값이 나오기 때문에 평가 시점으로부터 자유롭다.


```javascript
var c = 20;
function add3(a, b) {
  c = b;
  return a + b;
}

console.log('first c', c); // 20
console.log(add3(20, 30));
console.log('second c', c); // 30
// 값이 변경된다면 순수함수가 아니다.


var obj1 = { val: 10};
function add4(obj, b) {
  obj.val += b;
}
console.log(obj1.val); // 10
add4(obj1, 20);
console.log(obj1.val); // 30
// 인자의 값을 변경함 -> 순수함수가 아니다


// 다시 순수 함수
var obj2 = { val: 10};
function add4(obj, b) {
  return {
    val: obj.val + b
  }
}
console.log(obj1.val); // 10
var result = add5(obj2.val);
console.log(obj1.val); // 10
console.log(result); // 30
```




#### 일급 시민

- 변수에 담을 수 있다.
- 매개변수로 전달할 수 있다.
- 반환값으로 전달할 수 있다.

#### 일급 객체

- 특정 언어에서 **객체를 일급 시민으로서 다룰 수 있느냐** 를 뜻한다.

```javascript
var obj = {name: 'mh', age:22}; // 변수에 담을 수 있다.
var fn = function(obj){ // 매개변수로 전달할 수 있다.
  return obj; // 반환값으로 전달할 수 있다.
}

// 일급 시민으로서의 조건을 모두 만족하기 때문에 자바스크립트의 객체는
// 일급 객체이다.
```

#### 일급 함수

- 특정 언어에서 **함수를 일급 시민으로서 다룰 수 있느냐** 를 뜻한다.

- 자바스크립트에서 함수는 위 조건을 만족하는 일급 시민이므로 일급 함수이다.

- 일급 시민에 더해 일급 함수는 다음과 같은 조건까지 있어야 주장하기도 한다.
  - 런타임 생성이 가능하다.
  - 익명으로 생성이 가능하다.


```javascript
var fn = function(){} // 변수에 담을 수 있다.
var _fn = function(fn){ // 매개변수로 전달할 수 있다.
  return fn; // 반환값으로 전달할 수 있다.
}
function a() {} // 함수 선언식은 스크립트 로딩 시점에 VO(Variable Object)에 저장됨
var b = function() {}; // 함수 표현식은 런타임 시에 해석 및 실행이 된다.
var c = function() { // 익명으로 생성이 가능하다.
  return function() {}
}
```

- 하지만 자바스크립트에서 함수는 객체이다.
  - 따라서 자바스크립트에서 함수는 일급 객체라고 주장하기도 한다.

```javascript
var a = function() {};
a.b =123; // 객체와 같이 프로퍼티 추가가 가능하다.
console.log(a.b); // 123
```

#### 고차 함수
1. 함수의 인자로 함수를 받는 경우
2. 함수의 출력값이 함수인 경우
3. 함수의 인자로 함수를 받으면서 출력값이 함수인 경우 (1+2번의 경우)

```javascript
var fn = function(fn) { return fn(); }  // 1. 함수의 인자로 함수를 받는 경우
var fn2 = function(a) { return functionb(){ return b} } // 2. 함수의 출력값이 함수인 경우
var fn3 = function(fn) { return fn }; // 함수의 인자로 함수를 받으면서 출력값이 함수인 경우

```


---

> 참고1 / https://www.inflearn.com/course/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D/

> 참고2 / https://blog.perfectacle.com/2017/06/30/js-func-00-what/
