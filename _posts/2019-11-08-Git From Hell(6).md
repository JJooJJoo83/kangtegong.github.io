---
layout: post
title: "지옥에서 온 Git-06"
image: ''
date: 2019-10-08 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -6 : stage area

### git add는 왜 하는 걸까?

그냥 commit하면 안되나?
왜 commit 전 꼭 add를 해야 하는가?

(큰) 프로젝트를 진행하다 보면 소스코드를 쭉 수정하게 되는데
그러다보면 commit 시기를 놓칠 수가 있다

commit하나는 하나의 작업을 담고 있는게 가장 이상적

add라는 과정을 통해 우리가 commit하고자 하는 파일만 commit하게 할 수 있다

여러 변경사항 중 다음 버전으로 넣을 대상(commit할 대상)을 필터링 하는 기능

```
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        f1.txt
        f2.txt

nothing added to commit but untracked files present (use "git add" to track)

```

여기서 f1.txt만 add 시켜보자
```
$ git add f1.txt
```

`git status`로 확인해보면 다음과 같이 commit 예정 파일과 
untracked 파일이 나누어진다는 걸 알 수 있다.

```
No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   f1.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        f2.txt

```
이 상태에서 commit을 해 보자
```
$ git commit
```

그럼 다음과 같이 f1.txt만 commit 되는 걸 확인할 수 있다.

```
only f1.txt will be commited
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
#
# Initial commit
#
# Changes to be committed:
#       new file:   f1.txt
#
# Untracked files:
#       f2.txt
#
```

commit 이후 `git status`로 보면 여전히 f2.txt는 untracked

```
$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        f2.txt

nothing added to commit but untracked files present (use "git add" to track)

```

add하면 commit 대기 상태에 들어감

이 commit 대기 상태 == stage area

git에서 

변경내역 --> (stage area commit할 대상들 ) --> commit --> (repository = commit의 결과) --> 버전
