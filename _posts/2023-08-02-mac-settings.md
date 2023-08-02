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