---
layout: post
title: "지옥에서 온 Git-05"
image: ''
date: 2019-10-08 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 버전관리의 본질 -5 : git commit

### 버전은 무엇인가

모든 "변화"를 버전이라 하지 않는다
"의미있는"변화를 버전이라 한다.

어떤 작업 단위가 완결된 상태를 버전이라 한다

### 버전 관리 하지 전 이름부터 세팅하자

딱 한 번만 하면 된다

$ git config --global user.name <이름>
$ git config --global user.email <이메일>


그럼 앞으로 우리가 작성한 버전은 위의 이름과 이메일로 작성이 되어
다른 사람도 알 수 있게 된다

### commit

이전 시간에 git add까지 했다면
이제 commit을 해 주자
```
$ git commit 
```
그러면 아래와 같은 메세지가 뜬다.

```
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
```

이 곳에 버전 메세지, 즉 commit 메세지를 적어주면 된다

이 변화가 어떤 변화이고 왜 변화했는지, 어떤 의미를 가지는지를 적어주면 된다.

> first commit message

`I`를 눌러 insert모드로 바꿔준 후 커밋 메세지를 남겨준 뒤 `ESC` + `:wq!`로 저장한다.

```
first commit message
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

```
그럼 이제 버전이 잘 만들어졌는지 확인해 보자

```
$ git log
```

```
$ git log
commit f4533bfd2e2127036e388917568624626936dcf1 (HEAD -> refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Nov 7 12:36:17 2019 +0900

    first commit message


```
언제 누가 어떤 버전을 만들었는지, 커밋 메세지에 대한 정보가 뜨게 된다.


### 버전 만들기 연습

vim f1.txt

source:1을 
source:2로 바꿔주자

다시 git status로 확인해주면 
modified라고 뜬다
```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   f1.txt

no changes added to commit (use "git add" and/or "git commit -a")

```
여기서 바로 `git commit`을 하면 안되고,
다시금 이 변경사항을 add해 줘야 한다

최초의 추적에서도 add
파일이 수정되어서 새로운 버전이 만들어지기 직전에도 add

```
& git commit
```

이번에는 커밋 메세지를

> second commit message

로 남겨주었다.


```

second commit message
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
# Changes to be committed:
#       modified:   f1.txt
#
```

commit이 된 후 (새 버전을 만들어 준 뒤) 로그를 확인하면


```
$ git log
commit 19f71c369b04d15f0702629eca46c5cb68734392 (HEAD -> refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Nov 7 12:46:04 2019 +0900

    second commit message

commit f4533bfd2e2127036e388917568624626936dcf1
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Nov 7 12:36:17 2019 +0900

    first commit message

```
아래와 같이 새로운 커밋이 생성되었음을 확인할 수 있다