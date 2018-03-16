---
layout: post
title: "Vue Instance"
comments: false
description: ""
keywords: ""
---


# Vue Instance

vue.js 라이브러리를 로딩했을 때 존재하는 Vue 생성자로 인스턴스를 생성

```javascript
var vm = new Vue({
  template: ..., // 화면에 나타나는 요소들 ex) div, a
  el: ..., // 뷰가 그려지는 시작점
  methods: {
    ... // 클릭하거나 기타 http 요청에 대한 구현 메소드
  },
  created: {
    ... // 생명주기에 관련
  }
  // ....
})
```

각 options으로 미리 정의한 vue 객체를 확장하여 재사용이 가능하다.

하지만 아래 방법 보다는 template에서 **custom element** 로 작성하는것이 더 좋다.

```javascript
var MyComponent = Vue.extend({
  template: `<p>Hello {{ message }}</p>`,
  data: {
    message: 'Vue'
  }
  // ...
})
// 위에서 정의한 내용을 기본으로 하는 컴포넌트 생성
var MyComponentInstance = new MyComponent()
```
