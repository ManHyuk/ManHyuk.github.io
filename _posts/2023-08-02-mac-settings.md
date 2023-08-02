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

tab 으로 구분 


언제부터인지 모르겠지만 새로 맥 세팅을 하다보면 

영문키 반복이 안되는데 그럴때 아래 명령어를 터미널에 붙여넣자 
`defaults write -g ApplePressAndHoldEnabled -bool false`