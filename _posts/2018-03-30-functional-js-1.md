---
layout: post
title: "함수형 자바스크립트 -1"
comments: false
description: ""
keywords: ""
---


순수함수

동일한 인자를 넣으면 동일한 결과를 나타내는 함수


특징
- 평가시점이 중요하지 않다.


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




일급함수

함수를 값으로 다룰 수 있다.
