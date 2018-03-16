---
layout: post
title: "자바스크립트 데이터 타입 -1"
comments: false
description: ""
keywords: ""
---
#### DATA TYPES

1. Primitive type ( 값을 그대로 할당 )
   - Number
   - String
   - Boolean
   - null
   - undefined
2. Reference Type ( 값이 저장된 주소값을 할당 / 참조 )
   - Object
     - Array
     - Function
     - RegExp





Primitive Type Example

```javascript
var a; // 변수 선언과 동시에 컴퓨터는 메모리안에 데이터가 닮길 공간을 확보함
a = 10; // 변수 a를 찾아서 a의 주소값을 찾아 그 주소값에 10을 할당함


var c = b; // c에 b의 값을 복사한다
c = 20; // c에 20 할당 (b와 다른 주소 false를 덮어 씀)

// b !== C

```

![primitive](/images/js-flow/js-primitive.png)



Reference Type Example

```javascript
// Reference Type
var obj = {
    a: 1,
    b: 'b'
};
var obj2 = obj; // 주소 참조
obj2.a = 10; // 참조된 값을 변경
console.log(obj2.a); // 10
console.log(obj.a); // 10

// obj === obj2
```

![reference](/images/js-flow/js-reference.png)
