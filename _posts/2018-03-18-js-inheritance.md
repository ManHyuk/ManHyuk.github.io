---
layout: post
title: "자바스크립트 method inheritance -8"
comments: false
description: ""
keywords: ""
---





```javascript
function Person(n ,a){
    this.name = n;
    this.age = a;
}

var gomu = new Person('고무곰', 30);
var iu = new Person('아이유', 25);

gomu.setOlder =function(){
    this.age +=1;
}
gomue.getAge = function(){
    return this.age;
}
iu.setOlder =function(){
    this.age +=1;
}
iu.getAge = function(){
    return this.age;
}
// 코드 중복의 문제를 가지고 있다.

```



```javascript
// prototype으로 메소드를 이동
function Person(n ,a){
    this.name = n;
    this.age = a;
}
Person.prototype.setOlder =function(){
    this.age +=1;
}
Person.prototype.getAge = function(){
    return this.age;
}
var gomu = new Person('고무곰', 30);
var iu = new Person('아이유', 25);

gomu.__proto__.setOlder();
gomu.__proto__.getAge();//NaN
// 메소드들의 this가 gomu가 아닌 gomu.__proto__를 가리키기 때문

gomu.setOlder(); 
gomu.getAge(); // 31
// this가 gomu를 가리킴
// __proto__는 생략이 가능하기 때문에 마치 자신의 메소드처럼 호출이 가능하다

--
// 만약 생성자함수의 prototype에 age라는 프로퍼티가 있다면 결과가 다르다
Person.prototype.age = 100;

gomu.__proto__.setOlder();
gomu.__proto__.getAge();// 101

gomu.setOlder(); 
gomu.getAge(); // 31

```



 메소드 호출시 this가 어떻게 bind되는지 생각해보자

잘 이해가 안되네