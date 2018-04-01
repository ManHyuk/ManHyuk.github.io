---
layout: post
title: "Vue 데이터 바인딩 -7"
comments: false
description: ""
keywords: ""
---


### Data Binding
- DOM 기반 HTML Template 에 vue데이터를 바인딩 하는 방법은 아래와 같이 크게 3가지가 있다.
  - Interpolation (값 대입)
  - Binding Expressions (값 연결)
  - Directives (디렉티브 사용)


#### Interpolation - 값 대입
  - Vue의 가장 기본적인 데이터 바인딩 체계는 Mustache {{ }}를 따른다.

```html
<span>this will never change: {{* msg}}</span>
<div id="item-{{ id }}"></div>
```

#### Binding Expressions - 값 연결
- {{ }} 이용한 데이터 바인딩을 할 때 자바스크립트 표현식을 사용할 수 있다.
- 복잡하지 않은 간단한 표현식에 사용

```html
<div>{{ number+1 }}</div> <!-- O -->
<div>{{ message.split('').reverse().join('') }}</div> <!-- O -->
<div>{{ if (ok) { return message } }}</div> <!-- X -->
```

```html
{{ message | capitalize }}
{{ message | capitalize | upcapitalize }}
```


#### Directives
- Vue 에서 제공하는 특별한 Attributes이며 v- 의 접두사를 갖는다.
- 자바스크립트 표현식, filter 모두 적용된다.

```html
<!-- login의 결과에 따라 p가 존재 또는 미존재 -->
<p v-if="login"> HELLO </p>

<!-- click={{doSomething}}와 같은 역할 -->
<a v-on:click="doSomething"></a>
```

#### Class Binding
- CSS스타일링을 위해서 Class를 아래2가지 방법으로 추가가 가능하다
  - class="{{ className }}"
  - v-bind:class
- 주의할 점은 두 방법을 함께 사용하지 않고 한 가지만 적용해야 미연에 에러를 방지 할 수 있다.

```html
<div class="static" v-bind:class="{'class-a':isA, 'class-b':isB}"></div>

<script>
  data: {
    isA:true,
    isB: false
  }
</script>

<!-- 결과 값은 -->
<div class="static class-a"></div>
```

- Array 구문도 사용할 수 있다.

```html
<div v-bind:class="[classA, classB]"></div>

<script>
  data: {
    classA:'class-a',
    classB:'class-b'
  }
</script>

<!-- 결과 값은 -->
<div class="static class-a"></div>
```
