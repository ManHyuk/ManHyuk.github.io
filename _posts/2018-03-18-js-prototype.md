---
layout: post
title: "자바스크립트 prototype -8"
comments: false
description: ""
keywords: ""
---



PROTOTYPE!!!



우선 마법의 삼각형의 이미지를 보자



![detail-triangle](/images/js-flow/js-detail-triangle)



Constructor의 prototype과 instance의 proto는 같은 객체를 참조한다. (proto는 생략가능)

![triangle](/images/js-flow/js-triangle.png)



```javascript
function Person(n, a){
    this.name = n;
    this.age = a;
}

var gomu = new Person('고무곰', 30);

var gomuClone1 = new gomu.__proto__.constructor('고무곰_클론1', 10);

var gomuClone2 = new gomu.constructor('고무곰_클론2', 25);

var gomuProto = Object.getPrototypeOf(gomu);
var gomuClone3 = new gomuProto.constructor('고무곰_클론3', 20);

var gomuClone4 = new Person.prototype.constructor('고무곰_클론4', 15);

// 모두 동일한 함수를 가리킴

```



생성자함수의 프로토타입에 접근가능 방법

- [CONSTRUCTOR].prototype
- [instance].____proto____
- [instance]
- Object.getPrototypeOf([instance])


생성자 함수에 접근 방법

- [CONSTRUCTOR]
- [CONSTRUCTOR].prototype.constructor
- Object.getPrototypeOf([instance]).constructor
- [instance].____proto____.constructor
- [instance].constructor
