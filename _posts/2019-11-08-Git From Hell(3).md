---
layout: post
title: "지옥에서 온 Git-03"
image: ''
date: 2019-10-08 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -2 : git init

### 프로젝트 폴더 만들기

`User/egoing/Documents`에 `gitfth`이라는 프로젝트 폴더를 만들자
```
$ pwd
/c/Users/egoing

$ cd Documents/

$ mkdir gitfth

$ cd gitfth
```
### 버전관리를 하려고 하는 디렉토리를 git에게 알려주기

프로젝트를 진행할 폴더에서 

```
$ git
```
을 치면 사용할 수 있는 명령이 나온다

```
(... 생략)

start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one
(생략 ...)
```

프로젝트를 진행할 현재 디렉토리가 
버전관리의 대상이라는 걸 git에게 알려주는 명령어
 
 한번 쳐 보자
```
$ git init

Initialized empty Git repository in <현재 디렉토리 경로>
```

git을 initialize한 뒤 다음의 명령어를 쳐 보자
```
$ ls -al
```
.git이라는 버전관리를 해 주는 
(숨겨진) 디렉토리가 생겼음을 확인할 수 있다.