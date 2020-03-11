---
layout: post
title: 'git reset의 원리'
author: minchul.kang
date: 2019-12-17 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git reset의 동작 원리

reset checkout이 동작하는 과정을 gistory를 통해 확인해보자.
새로운 저장소를 만들었다고 가정하고 새로 실습을 진행한다.
우선 아래의 상황까지 실습 세팅을 만들어둔다. (설명은 생략한다)

```
$ git init
$ vim f1.txt # a 입력
$ git add f1.txt
$ git commit -m "1"

$ vim f1.txt # b 추가
$ git add f1.txt
$ git commit -m "2"

$ vim f1.txt # c 추가 
$ git add f1.txt
$ git commit -m "3"

$ vim f1.txt # d 추가
$ git add f1.txt
$ git commit -m "4"
 ```

```
$ git log
commit 5df360a8ad0380fb48011fb57b0da217ac3603ed (HEAD -> refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:40 2019 +0900

    4

commit f25019faad31bd3df0a3c6111062ea057fde5aac
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:21 2019 +0900

    3

commit 53b42bd38299e05fb7af949cc275d24dadce6202
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:00 2019 +0900

    2

commit 1096b51c8d80fe47be3446358148852b5898dafb
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:05:13 2019 +0900

    1

```


자, 이 상황에서 3번으로 돌아가고 싶다면, 즉
4번 커밋을 취소하고 싶다면 어떻게 해야 할까?

```
$ git reset --hard <돌아가고 싶은 버전>
```
명령어를 통해 되돌아갈 수 있다.
즉, 위 경우에는 아래와 같이 입력하면 된다.

```
$ git reset --hard f25019faad31bd3df0a3c6111062ea057fde5aac
```

HEAD is now at 3 라는 메세지와 함께 4번 커밋이 사라졌음을 확인할 수 있다.

```
$ git reset --hard f25019faad31bd3df0a3c6111062ea057fde5aac
HEAD is now at f25019f 3

minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/git-tutorial (master)
$ git log
commit f25019faad31bd3df0a3c6111062ea057fde5aac (HEAD -> refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:21 2019 +0900

    3

commit 53b42bd38299e05fb7af949cc275d24dadce6202
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:00 2019 +0900

    2

commit 1096b51c8d80fe47be3446358148852b5898dafb
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:05:13 2019 +0900

    1

```
![ref](/files/35.png)

gistory로 확인보면 `refs/heads/master` 가 3번으로 수정되었음을 확인할 수 있다. (현재 내가 checkout한 브랜치인 master가 가지고 있는 최신 커밋이 3번으로 바뀐 셈)

---

그럼 우리가 삭제한 4번 커밋은 지워진걸까?
git 왠만하면 커밋을 지우지 않는다. 분명 어딘가에는 (높은 확률로) 보관되어 있을 것이다. 이를 .git에서 찾아보자.

우리가 앞서 수행한 reset 명령을 취소하고 싶다고 해 보자.

그 때 활용할 수 있는게 
`ORIG_HEAD` 또는 `logs/refs/heads/master`이다

`logs/refs/heads/master`는 master브랜치에서 일어나는 여러가지 중요한 사건들을 기록하는 역할을 한다.

`ORIG_HEAD`는 위험한 명령/ 정보를 잃을 수도 있는 명령들을 내리기 전
git이 현재 브랜치의 최신 커밋 (HEAD이 가리키고 있는 내용)을 담아주는 역할을 한다.


`ORIG_HEAD`를 보면 우리가 삭제한 4번을 가리키고 있음을 알 수 있다

![ref](/files/36.png)


그리고 logs resfs heaads master를 보면 
하단에
```
5df360a8ad0380fb48011fb57b0da217ac3603ed f25019faad31bd3df0a3c6111062ea057fde5aac Minchul Kang <tegongkang@gmail.com> 1577344224 +0900	reset: moving to f25019faad31bd3df0a3c6111062ea057fde5aac
```
다음과 같이 reset 명령이 수행되었다는 로그 정보와 함께
reset을 수행하기 전 4번 커밋에 대한 정보도 같이 가지고 있음을 확인할 수 있다.

![ref](/files/37.png)


우리가 reset을 하기 전의 정보 (4번 커밋)가 어디에 있는지 알았으니
reset을 reset해보자.

```
$ git reset --hard ORIG_HEAD
HEAD is now at 5df360a 4
```

다음과 같이 다시 reset이 취소되었음을 알 수 있다.

`ORIG_HEAD` 보다 추천할 만한 것은 `log`를 보는 것이다. git의 ref log은 
`git reflog` 명령어를 통해 확인할 수 있다.

```
$ git reflog
5df360a (HEAD -> refs/heads/master) HEAD@{0}: reset: moving to ORIG_HEAD
f25019f HEAD@{1}: reset: moving to f25019faad31bd3df0a3c6111062ea057fde5aac
5df360a (HEAD -> refs/heads/master) HEAD@{2}: commit: 4
f25019f HEAD@{3}: commit: 3
53b42bd HEAD@{4}: commit: 2
1096b51 HEAD@{5}: commit (initial): 1
```
---

## 덧. branch가 아닌 commit으로 checkout


이번에는 checkout을 써 보자. exp 브랜치를 만들어 exp로 checkout 해보자.

```
minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/git-tutorial (master)
$ git branch exp

minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/git-tutorial (master)
$ git checkout exp
Switched to branch 'exp'
```

`git checkout` 명령어 뒤에는 branch 이름 뿐 아니라 
commit id도 직접 적을 수 있다.
```
git checkout <커밋해시>
```
방식으로 특정 commit으로 체크아웃 해 보자.

우선 git log를 통해 commit id를 확인해 보고, 
```
minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/git-tutorial (exp)
$ git log
commit 5df360a8ad0380fb48011fb57b0da217ac3603ed (HEAD -> refs/heads/exp, refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:40 2019 +0900

    4

commit f25019faad31bd3df0a3c6111062ea057fde5aac
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:21 2019 +0900

    3

commit 53b42bd38299e05fb7af949cc275d24dadce6202
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:06:00 2019 +0900

    2

commit 1096b51c8d80fe47be3446358148852b5898dafb
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 16:05:13 2019 +0900

    1
```
3번 커밋으로 돌아가고 싶다는 가정 하에 3번 커밋의 해시를 checkout 뒤에 붙여 보자 
```
$ git checkout f25019faad31bd3df0a3c6111062ea057fde5aac
Note: checking out 'f25019faad31bd3df0a3c6111062ea057fde5aac'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at f25019f 3

```
여기서 현재 branch 를 확인 하면 
master가 아니라
f25..라고 하는 detached를 가리킨다고 나온다.

```
$ git branch
* (HEAD detached at f25019f)
  exp
  master
```


이 쯤에서 gistroy를 새로고침해보자.

![ref](/files/38.png)

HEAD파일이 바뀌었음을 알 수 있는데,

우리가 알고 있는 HEAD파일은 
`refs/heads/master` , `refs/heads/exp` 와 같은 branch 만을 가리켜 왔는데,
이제는 특정 commit을 가리키고 있음을 알 수 있다.

`git checkout master` 로 master 브랜치로 돌아가면 
`HEAD` 는 다시 
`refs/heads/master`
를 가리킬텐데, 이건 생략한다.