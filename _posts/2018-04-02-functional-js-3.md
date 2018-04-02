---
layout: post
title: "함수형 자바스크립트 -3"
comments: false
description: ""
keywords: ""
---

### Section 2

#### currying

- 커링 / 함수와 인자를 다루는 기법

  - 평가시점을 미뤄 뒀다가 원하는 시점에 평가 하는 방법

  - curry, curryr

  ```javascript
  function _curry(fn) {
    return function(a){
      return function(b) {
        return fn(a, b);
      }
    }
  }

  var add = _curry(function(a,b){
    return a+b;
  })

  var add10 = add(10);
  console.log( add10(5) ); // 15

  console.log( add(5)(3)); // 8

  console.log( add(1, 2)); // function이 리턴된다.

  // 인자를 2개를 받았을때 원하는 값을 리턴받고 싶다면 arguments.length 를 이용한다.

  function _curry(fn) {
    return function(a, b){
      if (arguments.length == 2) return fn(a,b) // 또는 3항연산자 사용
      // return arguments.length == 2 ? fn(a,b) : function(b){return fn(a,b)}
      return function(b) {
        return fn(a, b);
      }
    }
  }
  console.log( add(1, 2)); // 3

  var sub = _curry(a, b){
    return a-b;
  }
  console.log( sub(10, 5)); // 5
  var sub10 = sub(10);
  console.log(sub10(5)); // 5
  // 값은 올바르게 출력되지만 변수와 인자를 봤을때 표현력이 떨어진다.
  // curry의 경우 인자를 왼쪽에서 부터 읽지만(a->b) curryr은 오른쪽부터 읽는다(b->a)

  function _curryr(fn){
    return function(a,b) {
      return arguments.length == 2 ? fn(a,b) : function(b){return fn(b,a)}
    }
  }
  console.log(sub10(5)); // -5
  // 올바르게 표현됨

  ```

  - get 만들어 좀 더 간단하게 하기

  ```javascript
  function _get(obj, key) {
    return obj == null ? undefined : obj[key];
  }
  var users = [
    {'name': 'mh'},
    {'name': 'aa'}
  ]
  var user1 = users[0];
  console.log(user1.name); // mh
  console.log(_get(user1, 'name')); // mh

  console.log(users[10].name); // Error
  console.log(_get(users[10], 'name')); // undefined

  // curryr을 사용하여 인자 순서를 바꿀 수 있다.

  var _get = _curryr(function _get(obj, key) {
    return obj == null ? undefined : obj[key];
  })
  console.log(_get('name')(user1)); // mh

  // 내부 동작
  var get_name = _get('name'); // get_name은 유저네임을 가져오는 함수가 된다.
  console.log(get_name(user1)); // mh

  ```



#### reduce

```javascript
_each(list, function(val) {
    memo = iter(memo, val)
});
return memo
}

console.log(
_reduce([1, 2, 3], function (a,b) {
    return a+b; // add
}, 0)
);


console.log(
_reduce([1,2,3], add, 0)
);

// memo = add(0,1)
// memo = add(memo, 2);
// memo = add(memo, 3);
// return memo

// => add(add(add(0, 1), 2), 3);

// function _reduce(list, iter, memo){
//    return  iter(iter(iter(0, 1), 2), 3);
// }


// reduce 는 축약하는 함수



// reduce의 3번째 인자를 생략하는 경우

var slice = Array.prototype.slice;
function _rest(list, num) {
return slice.call(list, num || 1);
}

function _reduce(list, iter, memo){
if (arguments.length === 2) {
    memo = list[0];
    // list = list.slice(1);
    // slice는 arr의 메소드이기 때문에 arr만 받을 수 있다.
    // array_like와 같이 querySelectorAll은 arr 같지만 slice메소드가 없다.
    // 위의 문제점을 해결하기 위해서 rest함수를 사용한다.

    list = _rest(list);


}
_each(list, function(val) {
    memo = iter(memo, val)
});
return memo
}

console.log(
_reduce([1, 2, 3], add, 10)
); // 16

console.log(
_reduce([1, 2, 3], add)
); // 6

console.log(
_reduce([1, 2, 3, 4], add, 10)
); // 20
```


#### pipe
  - 함수를 리턴하는 함수
  - 연속적으로 함수를 실행해주는 함수

  ```javascript
  function _pipe() {
    var fns = arguments;
    return function(arg) {
      return _reduce(fns, function (arg, fn) {
        return fn(arg);
      }, arg)
    }
  }

  var f1 = _pipe(
    function(a){ return a+1 }, // 1 + 1
    function(a){ return a*2 } // 2 * 2
  );

  console.log(f1(1));
  ```


#### go
  - 즉시 실행되는 pipe 함수
