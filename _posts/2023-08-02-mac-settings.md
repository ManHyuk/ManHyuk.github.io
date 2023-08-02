---
layout: post
title: "Mac Settings"
comments: true
description: ""
keywords: ""
---



### mkidr root

`sudo vi /etc/synthetic.conf`


```bash
// in conf
home1   /Users/user/home1
```

tab 으로 구분하여 작성한다면 
심볼릭 링크가 root에 생성된다

[root에 생성되었으면 하는 디렉토리명]\t[내가 만든 경로]


### 한글 씹힘
한글을 치다보면 한글이 종종 씹히게 되는경우에

설정 -> 키보드 -> 맞춤법 자동 수정을 끄면 해결

### 영문 반복
언제부터인지 모르겠지만 새로 맥 세팅을 하다보면 

영문키 반복이 안되는데 그럴때 아래 명령어를 터미널에 붙여넣자 
`defaults write -g ApplePressAndHoldEnabled -bool false`


### Maven http repositories blocking

maven 3.8.1 부터 http 에 대한 외부 연결을 막는 설정이 default로 변경됨

https://maven.apache.org/docs/3.8.1/release-notes.html 참고


레거시 프로젝트를 수정하기 번거롭기 때문에 maven 을 수정해야된다면

maven 3.8.1 하위버전을 설치해서 intellij 에 직접 경로를 설정해주면 해결된다

하지만 maven도 로컬에 설치하기 싫고 intellij 가 제공하는 maven을 사용한다면

{user.home}/.m2/settings.xml 또는 {maven.home}/conf/settings.xml 수정하거나 생성하자

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
http://maven.apache.org/xsd/settings-1.0.0.xsd">

<mirrors>
<mirror>
    <id>maven-default-http-blocker</id>
    <mirrorOf>external:dont-match-anything-mate:*</mirrorOf>
    <name>Pseudo repository to mirror external repositories initially using HTTP.</name>
    <url>http://0.0.0.0/</url>
</mirror>
</mirrors>


</settings>
```








---

#### 참고
- https://jjam89.tistory.com/201