---
layout: post
title: "VueJS? -1"
comments: false
description: ""
keywords: ""
---

MVVM 패턴의 ViewModel 레이어에 해당하는 View 단 라이브러리!!

VueJS 특징
- 데이터 바인딩과 화면 단위를 컴포넌트 형태로 제공
- 2 way data bindings을 제공
- 하지만 Component간 통신의 기본 골격은 React의 1 way data flow와 유사
- Virtual DOM을 이용한 렌더링 방식 (React와 유사)
- 가볍고 빠름
- 러닝커브가 낮음


![mvvm](/images/vue/vue-mvvm.png)

MVVM 패턴이란?

Backend 로직과 Client의 마크업 & 데이터 표현단을 분리하기 위한 구조로 전통적인 MVC 패턴의 방식에서 기인함.

앞단의 화면 동작 관련 로직과 뒷단의 DB데이터 처리 및 서버 로직을 분리하고, 뒷단에서 넘어온 데이터를 Model에 담아 View로 넘겨주는 중간 지점


웹페이지는 돔과 자바스크립트의 연합으로 만들어지게 되는데 돔이 View 역할을 하고, 자바스크립트가 Model 역할을 한다. ViewModel이 없는 아키텍처에서는 getElementById 같은 돔 API를 직접 이용해서 Model과 View를 연결해야 한다. 자바스크립트에서 Controller 혹은 Presenter 역할까지 하면서 코드양이 증가하는 단점이 생기기게 되는데 제이쿼리를 이용해 돔에 데이터 뿌려주는 코드들이 대부분 그랬다.

하지만 그것을 ViewModel이 대신 수행해 주는 것이 MVVM 모델이다. ViewModel에 자바스크립트 객체와 돔을 연결해 주면 ViewModel은 이 둘간의 동기화를 자동으로 처리한다. 이것이 VueJs가 하는 역할이다.




----
> 참고 1) 인프런 강의 https://www.inflearn.com/course/vue-pwa-vue-js-%EA%B8%B0%EB%B3%B8/

> 참고 2) http://blog.jeonghwan.net/vue/2017/03/27/vue.html
