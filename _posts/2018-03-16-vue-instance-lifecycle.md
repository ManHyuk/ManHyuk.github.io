---
layout: post
title: "Vue 라이프 사이클 -3"
comments: false
description: ""
keywords: ""
---

Vue Instance 라이프사이클 초기화

Vue 객체가 생성될 때 아래의 초기화 작업을 수행한다.
- 데이터 관찰
- 템플릿 컴파일
- DOM에 객체 연결
- 데이터 변경시 DOM 업데이트


초기화 작업 외에도 개발자가 의도하는 커스텀 로직을 추가할 수 있다.
```javascript
var vm - new Vue({
  data: {
    a: 1
  },
  created: function () {
    // this는 vm을 가리킴
    console.log(`a is ${this.a}`);
  }
})
```
초기화 메서드로 커스텀 로직을 수행하기 때문에 Vue에서는 따로 Controller를 갖고 있지 않다.


![lifecycle](/images/vue/vue-lifecycle.png)
