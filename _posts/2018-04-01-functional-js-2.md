---
layout: post
title: "함수형 자바스크립트 -2"
comments: false
description: ""
keywords: ""
---

```javascript

// add_maker
function add_maker(a) {
    return function(b) {
        return a+b;
    }
}

var add10 = add_maker(10);

console.log(add10(20)); // 30

var add5 = add_maker(5);
var 15 = add_maker(15);

console.log(add5(10)); // 15
console.log(add15(10)); // 25

```





```javascript
function f4(f1, f2, f3) {
    return f3(f1() + f2());
    // f3(2 + 1)
}

console.log(
	f4(
    	function() { return 2; },
        function() { return 1; },
        function(a) { return a*a;}
    )
) // 9

```



함수형 프로그래밍



순수함수들을 조합하여 사용하며 평가 시점이나 평가 방법을 생각하며 로직을 만들어가는 방법



함수형 프로그래밍은 성공적인 프로그래밍을 위해 부수 효과를 미워하고 좋바성을 강조하는 프로그래밍 패러다임이다.

- 부수 효과를 미워한다.
  - => 순수 함수를 만든다.
- 조합성을 강조한다.
  - =>  모듈화 수준을 높인다.
- 순수 함수
  - => 오류를 줄이고 안정성을 높인다.
- 모듈화 수준이 높다
  - => 생산성을 높인다.



>  **함수형 프로그래밍** 은 애플리케이션, 함수의 구성요소, 더 나아가서 언어 자체를 함수처럼 여기도록 만들고, 이러한 **함수 개념을 가장 우선순위** 에 놓는다.



>  **함수형 사고방식** 은 문제 해결 방법을 **동사** (함수)들로 **구성** (조합) 하는 것



```javascript
// 데이터(객체) 기준
duck.moveLeft();
duck.moveRight();
dog.moveLeft();
dog.moveRight();

// 함수 기준
moveLeft(dog);
moveRight(duck);
moveLeft({x: 5, y: 2});
moveRight(dog);
```





```javascript


//2. _filter, _map으로 리팩토링


//3. each 만들기
//   1. _each로 _map, _filter 중복제거

//   2. 외부 다형성
//      1. array_like, arguments, document.querySelectorAll

```















1. 명령형 코드

   1. 30세 이상인 users를 거른다.

      ```javascript
      var users = [
          {id: 1, name: 'MH', age: 28},
          {id: 2, name: 'AA', age: 29},
          {id: 3, name: 'BB', age: 30},
          {id: 4, name: 'CC', age: 31},
      ]

      var temp_ussers = [];
      for(var i =0; i<users.lenght< i++) {
          if(users[i].age >=30 ){
              temp_users.push(users[i]);
          }
      }
      console.log(temp_users);
      ```

   2. 30세 이상인 users의 name을 수집한다.

   ```javascript
   var names = [];
   for(var i=0;i<temp_users.length;i++){
       names.push(temp_users[i].name);
   }
   console.log(names) // BB, CC
   ```

   ​

   3. 30세 미만인 users를 거른다.

   ```javascript
   var temp_ussers = [];
   for(var i =0; i<users.lenght< i++) {
       if(users[i].age < 30 ){
           temp_users.push(users[i]);
       }
   }
   console.log(temp_users);
   ```

   ​

   4. 30세 미만인 users의 age를 수집한다.

   ```javascript
   var ages = [];
   for(var i=0;i<temp_users.length;i++){
       ages.push(temp_users[i].age);
   }
   console.log(ages) // 28, 29
   ```

   ​

2. _filter, _map으로 리팩토링

```javascript
function _filter(users, predi) {
    var new_list = [];
	for(var i =0; i<users.lenght< i++) {
        // 함수의 조건을 추상화
        if(predi(users[i])){
            new_list.push(users[i]);
    	}
	}
	return new_list;
}

console.log(
    _filter(users, function(user) {
        return user.age >= 30;
    })
); // BB , CC

console.log(
    _filter(users, function(user) {
        return user.age < 30;
    })
); // MH, AA


// filter는 users에 종속된 함수가 아니다
console.log(
    _filter([1,2,3,4], function(num) {
        return num % 2;
    })
); // 1, 3

console.log(
    _filter([1,2,3,4], function(num) {
        return !(num % 2);
    })
); // 2, 4

// _map을 이용한 중복 제거
// 코드 내부에는 데이터가 어떤 형태인지 알 수 없다.
// => 높은 다형성 => 재사용성 극대화
function _map(list, mapper) {
    var new_list = [];
    for(var i=0;i<list.length;i++){
       new_list.push(mapper(list[i]));
    }
    return new_list;
}
var over_30 = _filter(users, function(user) {return user.age>=30;})
console.log(over_30); // Object BB, CC

var names = _map(over_30, function(user){
    return user.name;
});
console.log(names) // BB, CC

// _map의 첫번째 매개변수로 _filter를 넣기
console.log(
	_map(
        _filter(users, function(user) {return user.age>=30;}),
        function(user) { return user.name;})
) // BB, CC

```



3. each 만들기

   1. _each로 -map, _filter 중복제거

   ```javascript
   function _filter(users, predi) {
     var new_list = [];

     _each(list, function (val) {
         if (predi(users[i])) {
           new_list.push(val);
         }
     });
     return new_list;
   }


   function _map(list, mapper) {
     var new_list = [];

     _each(list, function (val) {
       new_list.push(mapper(val));
     });
     return new_list;
   }


   function _each(list, iter) {
     for (var i = 0; i < list.length; i++) {
       iter(list[i]);
     }
     return list;
   }
   ```

   ​

   2. 외부 다형성

      1. array_like, arguments, document.querySelectorAll

      ```javascript
      // 명령형
      console.log(
          [1,2,3,4].map(function(val) {
              return val*2;
          })
      ) // 2,4,6,8

      console.log(
          [1,2,3,4].filter(function(val) {
              return val%2;
          })
      ) // 1, 3

      // 함수형
      console.log(document.querySelectorAll('*'));

      console.log(
          _map(document.querySelectorAll('*', function(node){
              return node.nodeName;
          }))
      ); // ['HEAD', 'BODY', ...]


      // 함수형은 데이터가 있기전에 함수가 먼저 있기 때문에 평가시점이 상대적으로 유연해진다.
      ```

   3. 내부 다형성

      1. predicate, iter, mapper

      ```javascript
      // 매개변수의 두번째 인자로 들어가는 함수는 단순히 결과를 반환하는 콜백함수가 아니다.
      // 배열안에 어떤 값이 들어가도 함수가 수행될 수 있게 만드는 것은 보조함수(predi, iter, mapper)의 몫이다.

      // 외부 다형성은 array_like나 arguments같은 모든 돌릴 수 있는 것들을 고차함수(map과 filter)가 어떻게 구현되어 있냐에 따라 결정된다.

      ```

      ​
