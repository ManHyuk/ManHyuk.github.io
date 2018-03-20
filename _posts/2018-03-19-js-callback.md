---
layout: post
title: "자바스크립트 함수와 콜백함수"
comments: false
description: ""
keywords: ""
---


값으로서의 함수
> 자바스크립트에서는 함수도 객체다. 다시 말해서 일종의 값이다. 거의 모든언어가 함수를 가지고있지만 자바스크립트의 함수가 다른 언어의 함수와 다른 점을 함수가 값이 될 수 있다는 점이다.

```javascript
a = {
  b: function (){}
};
// 함수는 객체의 값으로 포함될 수 있다. 객체의 속성 값으로 담겨진 함수를 메소드라고 부른다.
// 메소드 -> 객체안에 정의되어있는 함수.
```

함수는 값이기 때문에 다른 함수의 인자로 전달 될수도 있다.
```javascript
function cal(func, num){
  return func(num); // 함수를 인자로 전달하여 사용
}
function increase(num){
  return num+1;
}
function decrease(num){
  return num-1;
}
alert(cal(increase, 1));
alert(cal(decrease, 1));
```

함수는 함수의 리턴 값으로도 사용할 수 있다.
```javascript
function cal(mode){
  var funcs = {
    'plus': function(){},
    'minus': function(){}
  }
  return funcs[mode]
}
alert(cal('plus')(2,1));
alert(cal('minus')(2,1))
```

배열의 값으로도 사용할 수 있다.
```javascript
var process = [
  function(input){return input+10},
  function(input) {return input*input}
];
var input =1;
for (var i = 0; i < process.length; i++) {
  input = process[i](input);
}
alert(input);
```

First-Class-Citizen(Object) 의 조건
- 변수
- 매개변수
- 리턴값



콜백함수!!

Something will **call** this function **back** sometime somehow.


```javascript
setInterval(function (){
  console.log('1초마다 실행')''
}, 1000)
// 첫번째 인자: 콜백함수
// 두번째 인자: 주기
```

콜백함수의 특징
- 다른함수 A의 매개변수로 콜백함수 B 를 전달하면, A가 B의 제어권을 갖게된다.
- 특별한 요청(bind)이 없는 한, A에 미리 정해진 방식에 따라 B를 호출한다.
- 미리 정해진 방식이란 this에 무엇을 바인딩할지, 매개변수에는 어떤 값들을 지정할지
어떤 타이밍에 콜백을 호출할지 등이다.

주의
- 콜백은 '함수'다!.
