---
layout: post
title: "지옥에서 온 Git-07"
image: ''
date: 2019-10-08 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -7 : 변경내역 확인하기

다시 들으며 내용 보강하기

### 우리가 만든 버전(commit)의 효용 두 가지
	

1. 과거 시점의 내역을 알 수 있음
2. 그 시점으로 돌아갈 수 있음

## 과거 시점의 내역을 보기

```
$ git log
```
```
$ git log -p
```
각 commit 사이의 소스 상의 차이를 보여줌
commit 별 소스 상의 차이를 보고 싶을 때

```
$ git diff
```
각 commit들은 각자 고유한 자신들의 id가 있다.
commit id는 그 commit이 만든 버전의 고유한 주소이다.
commit id는 git log를 쳤을 때 commit 뒤에 쓰인 해시이다

```
$ git log <commit_id>
```
라고 치면 그 commit_id (버전)의 고유한 주소를 볼 수 있음

두 commit 사이의 차이를 알고 싶다면?

```
$ git diff <commit_id>..<commit_id>
```

두 commit 사이의 소스상의 차이점을 보여줌

```
$ git diff
```
내가 수정한 내역을 볼 수 있음

commit하기 전 내가 작업한 내용이 문제가 있는지 없는지 
마지막으로 확인할 수 있는 기회인 셈이다.

단, git diff는 stage area로 들어가기 전에 내용만을 보여준다

그러니까 
$ git add f1.txt
$ dit diff

해도 아무것도 보이지 않는다.

