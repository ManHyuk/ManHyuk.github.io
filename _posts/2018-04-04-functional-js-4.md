---
layout: post
title: "함수형 자바스크립트 -4"
comments: false
description: ""
keywords: ""
---

### Section 3 - 컬렉션 중심 프로그래밍

- 수집하기 - **map**
  - values
  - pluck


- 거르기 - **filter**
  - reject
  - compact

- 찾아내기 - **find**
  - some
  - every

- 접기 - **reduce**
  - min
  - max
  - group_by
  - count_by


```javascript
var users = [
  { id: 10, name: 'aa', age: 36 },
  { id: 20, name: 'bb', age: 32 },
  { id: 30, name: 'cc', age: 32 },
  { id: 40, name: 'dd', age: 27 },
  { id: 50, name: 'ee', age: 25 },
  { id: 60, name: 'ff', age: 26 },
  { id: 70, name: 'gg', age: 31 },
  { id: 80, name: 'hh', age: 23 },
  { id: 90, name: 'ii', age: 13 },
];
```

```javascript
console.log(
  _map(users, functioin(user) {
    return user.name;
  })
);

// function _values(data) {
//   return _map(data, function(val) { return val; });
// }

function _values(data) {
  return _map(data, _identity) ;
}


function _identity(val) {
  return val;
}

var a = 10;
console.log(_identity(a));

console.log(user[0]); // { id: 10, name: 'aa', age: 36 }
console.log(_values(users[0])); // [10, 'aa', 36]
console.log(_keys(users[0])); // ['id', 'name', 'age']
```
