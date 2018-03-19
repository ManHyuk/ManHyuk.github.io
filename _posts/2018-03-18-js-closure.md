---
layout: post
title: "자바스크립트 closure -6"
comments: false
description: ""
keywords: ""
---





#### What is Closure?

> A closure is the combination of a function and the lexical environment within which that functions was declared. - MDN
>
> 클로저는 함수와 함수가 선언된 어휘적 환경의 조합이다.


내부함수를 포함하고 있는 외부함수에 접근할 수 있을 뿐만 아니라 외부함수가 종료된 이후에도

내부함수에 접근할 수 있다.


선언 당시의 환경에 대한 정보를 담는 객체(구성환경)

함수와 그 함수가 선언되는 당시의 정보가 담기는 어휘적 환경조합?!



즉, 함수와 '그 함수가 **선언될 당시(Scope)**의 환경정보' 사이의 조합

함수 내부에서 생성한 **데이터**와 그 **유효범위**로 인해 발생하는 특수한 **현상/상태**



외부에 정보를 제공할 수 있는 유일한 수단은 **return**



function 을 return해도 scope 및 lexical environment는 변하지 않는다.



**핵심은 최초 선언시의 정보를 유지!!**



클로저의 이점

- 접근 권한 제어
- 지역변수 보호
- 데이터 보존 및 활용



```javascript
// 접근 권한 제어, 지역변수 보호
function a(){
    var x = 1;
    function b(){
        console.log(x); // 1 변수 x접근가능
    }
    b();
}
a();
console.log(x); // 변수 x 접근 불가
```



```javascript
// 데이터 보존 및 활용
function a(){
    var x = 1;
    return function b(){
        console.log(x);
    }
}
var c = a();
c();
// 외부에서 x의 값을 호출 가능
// 하지만 외부에서 임의로 x의 값을 바꿀 수 없다.
```



```javascript
// 접근 권한 제어, 데이터 보존 및 활용
function a(){
    var _x = 1;
    return {
        get x() {return _x;},
        set x(v) {return _x = v;}
    }
}
var c = a();
c.x = 10;
// 외부에서 x의 값을 변경할 수 있다.
// _x의 값도 외부에 노출되지 않음
```







```javascript
function setName(name){
    return function(){
        return name;
    }
}
var sayMyName = setName('mh');
sayMyName();
```

![closure](/images/js-flow/js-closure.png)





##### 스코프는 정의될 때 결정된다!!



```javascript
function setCounter(){
    var count = 0;
    return function(){
        return ++count;
    }
}
var count = setCounter();
count();
```

![closure2](/images/js-flow/js-closure2.png)



클로저란 이미 생명주기가 끝난 외부함수의 변수를 참조하는 함수 - 인사이드 자바스크립트
