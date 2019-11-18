---
layout: post
title: "지옥에서 온 Git-04"
image: ''
date: 2019-10-11 09:24:08
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -4 : git add

###  버전관리 파일 생성

`git init` 명령어를 친 이후에는 
임의의 파일을 생성하여 그 파일이 버전관리되는 모습을 관찰해보자.

f1.txt를 만들고 그 안에 1이라는 숫자를 써 보자

```
$ vim f1.txt 
```

참고로 알파벳 i를 누르면 입력모드로 전환된다.
(vi/vim의 사용법은 쉬우니 알아서 공부하자..)
f1.txt에 아래와 같은 내용을 입력하자

```
source : 1
```
를 친 뒤 다시 `ESC`를 눌러 입력모드에서 나와 명령어 입력 모드로 돌아가자

`:wq!`로 작성한 내용을 저장하자

```
$ ls -al
```
을 통해 f1.txt가 생성되었음을 확인할 수 있다.

f1.txt 내부의 내용을 보기 위해서

```
$ cat f1.txt
source : 1
```

### 버전 관리를 하기 전 제일 먼저 익혀야 할 명령어 : status

버전관리를 하기 전 `git status`로 현재 버전 상태를 확인해야 한다.
쳐 보자.

```
$ git status
On branch master

Initial commit 

Untracked files:
	(use "git add <file>..." to include in what will be committed)
	
	f1.txt

nothing added to commit but untracked files present (use "git add" to track)	 
```

Untracked files라는 말은 추적되고 있지 않다는 뜻이다. 즉, 
지금 생성한 f1.txt는
gitfth 폴더에 
이 f1.txt에게 이 파일을 (버전)관리하라고 말하지 않기 때문에 
git은 얘를 무시한다

```
git add f1.txt
```

다시 git status를 쳐 보면

```
$ git status
On branch master

Initial commit 

Changes to be commmited:
	(use "git rm --cached <file>.." to unstage)

	new file:	f1.txxt
```

그럼 이제 git이 f1.txt을 버전관리하기 시작할 것이다.

우리가 관리해야 할 파일이 무엇인지 git에게 명확히 알려준다
