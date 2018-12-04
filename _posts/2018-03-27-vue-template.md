---
layout: post
title: "Vue 템플릿 -6"
comments: false
description: ""
keywords: ""
---


#### Vue로 그리는 화면의 요소들, 함수, 데이터 속성을 모두 Templates 안에 포함된다.
- Vue는 DOM의 요소와 Vue 인스턴스를 매핑할 수 있는 HTML template 을 사용.
- Vue는 Template으로 렌더링 할 때 Virtual DOM을 사용하여 DOM조작을 최소화 하고 렌더링을 꼭 다시 해야만 하는 요소를 계산하여 성능 부하를 최소화 한다.
- 원핟다면 render function을 직접 구현하여 사용 가능하다.



#### Attributes
- HTML Attributes를 Vue의 변수와 연결할 때는 v-bind를 이용.

```html
<div v-bind:id="dynamicId"></div>
```


#### JS Expressions
{% raw %}
- {{ }} 안에 다음과 같이 javascript표현식도 가능하다.
{% endraw %}

```html
{% raw %}
<div>{{ number+1 }}</div> <!-- O -->

<div>{{ message.split('').reverse().join('') }}</div> <!-- O -->
{% endraw%}
```


#### Directives
- v- 접두사를 붙인 Attributes로, javscript 표현식으로 값을 나타내는게 일반적이다 :을 붙여 인자를 받아 취급할 수 있다.

```html
<p v-if="seen">Now you see me</p>

<!-- : 뒤에 선언한 href 인자를 받아 url 값이랑 매핑 -->
<a v-bind:href=url></a>

<!-- click 이벤트를 받아 vue에 넘겨준다 -->
<a v-on:click="doSomething"></a>
```


#### Filter

화면에 표시되는 텍스트의 형식을 편하게 바꿀 수 있도록 고안된 기능, '\|'을 이용하여 여러개의 필터를 적용할 수 있다.



```html
{% raw %}
<!-- message에 표시될 문자에 capitalize 필터를 적용하여 첫 글자를 대문자로 변경한다. -->
{{ message | capitalize}}
{% endraw %}
```

```javascript
new Vue({
  // ..
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```
