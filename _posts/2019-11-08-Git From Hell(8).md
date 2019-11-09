---
layout: post
title: "지옥에서 온 Git-08"
image: ''
date: 2019-10-08 09:25:10
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -8 : 과거로 돌아가기

### commit을 취소하는 명령어

조금 어렵고 조심해서 사용하자

과거의 commit 때 (과거 버전)으로 돌아가기 위한 두 가지 방법

1. reset
2. revert

이 두 가지는 비슷한 듯 다르다
reset에도 여러가지 옵션이 있음 고로 어려울 수 있음


### reset

```
$ git log
```
5와 4를 삭제하고 3으로 돌아가고 싶다면?

가장 좋은건 reset이다

하지만 여기서 중요한 건 "경계값"

`git reset`을 3으로 한다는게
3을 reset한다는 건지
3까지 reset한다는건지 헷갈릴 수 있음

 5와 4에 해당하는 commit을 삭제하고 
 3의 commit id를 최신 commit id로 삼고 싶다면?

```
git rest <3번의 commit id> --hard
```

```
git log
```
쳤을 때 4와 5의 commit이 사라진 걸 확인할 수 있다
(3번 버전이 가장 최신 commit이 됨)

```
$ vim f1.txt
$ vim f2.txt
```
로 f1과 f2를 확인해보면 
버전 3때의 f1, f2라는 걸 알 수 있다.

여기서 절대 주의할 것 두 가지

1. 나중에 원격 저장소를 배우면 협업을 할 수 있게 되는데
그러면 내 버전을 다른 곳 (인터넷)에 공유할 수 있다

이 때, 공유한 이후에는 절대로 reset하면 안된다

즉, reset하는 commit은 공유하기 전에 
우리 컴퓨터에 있는 버전에 대해서만 reset 작업을 해야 한다

2.  `--hard` 는 굉장히 이해하기 쉽지만 다소 위험한 방법이다.


### revert

reset처럼 commit을 취소하는 건데
commit을 아예 날리는게 아니라
commit을 취소하며 새로운 버전을 생성해준다