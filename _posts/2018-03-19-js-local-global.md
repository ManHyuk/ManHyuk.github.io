---
layout: post
title: "자바스크립트 지역변수와 전역변수 그리고 유효범위"
comments: false
description: ""
keywords: ""
---


```javascript
function a(){
  var i = 0;
}
for (var i = 0; i < 5; i++) {
  a();
  document.write(i); // 0, 1, 2, 3, 4
}

function a(){
  i = 0;
}
for (var i = 0; i < 5; i++) { // i는 전역변수
  a(); // 계속  i는 0으로 초기화
  document.write(i); // 무한루프
}
```

전역변수의 사용방법

```javascript
// (function(){ // 익명함수를 호출함으로서 전역변수를 사용하지 않을 수 있다.
  var MYAPP = {}; // 전역변수를 하나만 만들고 내부에 추가
  MYAPP.calculator = { // 1 + 2
    'left':null, // 1 left
    'right': null // 2 right
  };

  MYAPP.coordinate = {
    'left': null, // 위치상의 left
    'right': null // 위치상의 right
  }

  MYAPP.calculator.left = 10;
  MYAPP.calculator.right = 20;

  function sum(){
    return MYAPP.calculator.left + MYAPP.calculator.right;
  }
  document.write(sum());
//}()) //함수 즉시호출
```


유효범위의 대상 (함수)
자바스크립트는 함수에 대한 유효범위만을 제공한다. 많은 언어들이 블록에 대한 유효범위를 제공하는 것과 다른점이다.
```javascript
for (var i = 0; i < 1; i++) {
  var name = 'hihi';
}
alert(name); // hihi
// 자바스크립트의 지역변수는 함수에서만 유효하다.
```

자바에서는 허용되지 않는다. name은 지역변수로  for문 안에서 선언 되었는데 이를 for문 밖에서 호출하고 있기 때문이다.
```java
for (int i = 0; i<10; i++) {
  String name = "hihi";
}
System.out.println(name); // Error
```

자바스크립트는 함수가 선언된 시점에서의 유효범위를 갖는다. 이러한 유효범위의 방식을 정적유효범위(static scoping) 혹은 렉시컬(lexical scoping)이라고한다.

```javascript
var i = 5;
function a(){
  var i = 10;
  b();
}
function b(){
  alert(i); // 5
}
a();
// 사용될 때가 아닌 정의될 때의 전역변수를 사용하게 된다.
```
