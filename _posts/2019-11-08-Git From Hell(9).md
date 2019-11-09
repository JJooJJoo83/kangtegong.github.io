---
layout: post
title: "지옥에서 온 Git-9"
image: ''
date: 2019-10-08 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 스스로 공부하는 법

빈도수가 가장 많은 걸 밑천으로 깔기
+
모르는 걸 알아낼 수 있는 방법만 알면 된다

### git에서 자주 쓰이는 명령어
>	commit
>	push
>	pull
>	clone
>	checkout
>	add
>	branch
>	log
>	diff
>	fetch
>	merge
>	init
>	...

### 스스로 알아내기

예를 들어 commit을 하는 과정에서
도움말이 필요하면

```
git commit --help
```

git manual 페이지로 이동하여
commit 메세지에 대한 도움말을 볼 수 있음

SYNOPSIS
DESCRIPTION
OPTIONS

````
$ git commit -a 
```
우리가 수정하거나 삭제한 파일은 자동으로 add 시켜줌

```
$ git commit -m "string"
```
"string"을 commit 메세지로 적는다


한번에 쓰기 

```
$ git commit -am "hello"
```


1. 틈틈히 메뉴얼을 보는 버릇을 들이자
2. 커뮤니티 질문
3.검색

-----

## 수련해봅시다

지금까지 배운 건 모든 버전관리시스템에서 공통적으로 다루는
본질적인 부분

dropbox, google drive같은 곳에
.git 디렉토리를 포함해서 백업시켜두자