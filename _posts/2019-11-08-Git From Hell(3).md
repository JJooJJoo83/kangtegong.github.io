---
layout: post
title: "지옥에서 온 Git-03"
image: ''
date: 2019-10-10 09:24:07
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -2 : git init

### 프로젝트 폴더 만들기

실습을 위해 버전관리의 대상이 되는 폴더를 만들자.
`User/egoing/Documents`에 `gitfth`이라는 프로젝트 폴더를 만들 것이다.

```
$ pwd
/c/Users/egoing

$ cd Documents/

$ mkdir gitfth

$ cd gitfth
```
### 버전관리 시작

우선 버전관리를 하려고 하는 디렉토리를 git에게 알려줘야 한다.
프로젝트를 진행할 폴더에서 

```
$ git
```
을 치면 사용할 수 있는 명령이 쭉 나오는데, 
그 많은 항목 중 이 부분을 보자.

```
(... 생략)
start a working area (see also: git help tutorial)
   clone      Clone a repository into a new directory
   init       Create an empty Git repository or reinitialize an existing one
(생략 ...)
```

버전관리를 시작하기 위해서는 (start a working area)
`clone` 과 `init` 명령어를 치면 된다고 나와있다.

이 중 `init`은
프로젝트를 진행할 현재 디렉토리가 
버전관리의 대상이라는 걸 git에게 알려주는 명령어이다.
 
 한번 쳐 보자
```
$ git init

Initialized empty Git repository in <현재 디렉토리 경로>
```

git을 initialize한 뒤 다음의 명령어를 쳐 보자.
```
$ ls -al
```

.git이라는 버전관리를 해 주는 
(숨겨진) 디렉토리가 생겼음을 확인할 수 있다.
(만약 안보이면 숨김파일 표시하도록 설정에서 바꾸자)

![숨김파일](/assets/img/hellgit/2/1.png)