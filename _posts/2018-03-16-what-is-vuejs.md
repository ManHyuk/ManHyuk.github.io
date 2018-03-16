---
layout: post
title: "VueJS?"
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
