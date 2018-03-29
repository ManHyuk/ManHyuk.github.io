---
layout: post
title: "Vue 컴포넌트와 통신 -4"
comments: false
description: ""
keywords: ""
---

### Components


#### Global or Local Component


- Global Component
  - 컴포넌트를 뷰 인스턴스에 등록해서 사용할 때 다음과 같이 global하게 등록할 수 있다.

```javascript
Vue.component('my-component', {
  // ...
})
```


- Local Component

```javascript
var cmp = {
  data: function () {
    return {

    }
  },
  template: '<hr>',
  methods: {}
}
// 아래 Vue 인스턴스에서만 활용할 수 있는 로컬 컴포넌트 등록
new Vue({
  components: {
    'my-cmp' : cmp
  }
})
```



#### 부모와 자식 컴포넌트 간 통신

- 구조상 상-하 관계에 있는 컴포넌트의 통신은
  - 부모 -> 자식 : props down
  - 자식 -> 부모 : events up


- props
  - 모든 컴포넌트는 각 컴포넌트 자체의 스코프를 갖는다.
    - ex) 하위 컴포넌트가 상위 컴포넌트의 값을 바로 참조할 수 없는 형식.
  - **상위에서 하위로 값을 전달하려면 props 속성을 사용한다.**

```javascript
// in Script
Vue.component('child-component', {
  props: ['passedData'],
  template: '<p>{{passedData}}</p>'
});
Vue.component('sibling-component', {
  props: ['passedSibling'],
  template: '<p>{{passedSibling}}</p>'
})

var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue! from Parent Component',
    sibling : 'sibling'
  }
})

// in HTML
<div id="app">
  // props 명 = "상위 컴포넌트의 데이터"
  <child-component v-bind:passed-data="message"></child-component>

  <sibling-component v-bind:passed-sibling="sibling"></sibling-component>

</div>
```

> 주의 : js에서 props변수 명명을 카멜 기법으로 하면 html에서 접근은 케밥 기법(-)으로 가야한다.




#### 같은 레벨의 컴포넌트 간 통신

- 동일한 상위 컴포넌트를 가진 2개의 하위 컴포넌트 간의 통신은
  - Child -> Parent -> 다시 2개의 Children
  - 같은 레벨의 컴포넌트 간 직접적인 통신은 불가능하도록 되더 있는게 Vue의 기본 구조





#### Event Bus - 컴포넌트 간 통신
- Non Parent - Child 컴포넌트 간의 통신을 위해 Event Bus를 활용할 수 있다.
  - Event Bus를 위해 새로운 Vue를 생성하여 아래와 같이 Vue Root Instance가 위치한 파일에 등록한다.


```javascript
// Vue Root Instance 전에 꼭 등록!! 순서 중요!!
export const eventBus = new Vue();
new Vue({
  // ...
})
```

#### 이벤트 발생
  - 이벤트를 발생시킬 컴포넌트에 **eventBus** import 후 $emit으로 이벤트 발생
```javascript
import { eventBus } from '../../main';
eventBus.$emit('refresh', 10);
```

#### 이벤트 수신
  - 해당 이벤트를 받은 컴포넌트에도 동일하게 import후 콜백으로 이벤트 수신

```javascript
import { eventBus } from '../../main';

// 등록 위치는 해당 컴포넌트의 created 메서드에 등록
create() {
  eventBus.$on('refresh', function (data) {
    console.log(data); // 10
  })
}
```
