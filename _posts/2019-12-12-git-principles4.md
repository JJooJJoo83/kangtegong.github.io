---
layout: post
title: 'git log의 원리'
author: minchul.kang
date: 2019-12-16 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git log의 원리

새로운 저장소를 생성하고 이 곳에서 다시 git을 시작해보자

```
git init
```

 gistory를 보면 (앞서 살펴봤던 것 처럼) 다음과 같은 파일들이 생성될텐데,
이 파일들 중 우리가 살펴보지 않은 파일이 있다

바로 이 HEAD파일인데, 

![HEAD](/files/gs-26.png)

HEAD는 init하면 자동으로 생성되며
`ref: refs/heads/master`
라는 내용을 담고 있다는 걸 알 수 있다

refs/heads/master또한 파일인데, 아직 어떠한 내용도 갖고 있지 않다.

![HEAD](/files/gs-27.png)

그럼 한 번 커밋을 해 보며 이 파일에 어떤 변화가 생기는지 살펴보자.

```
$ vim f1.txt << 'a' 쓰고 저장
$ git add f1.txt
$ git commit -m "1"
```

gistory를 새로고침해 보면 

변경된 파일 목록 중 
`refs/heads/master` 가 새로 생성되었음을 알 수 있다.

이걸 클릭해보면 object 아이디 갑을 가지고 있고 이걸 클릭해보면
방금 커밋 한 내용을 가리킨다는 걸 알 수 있다

![HEAD](/files/gs-28.png)

커밋을 한 번 더 해보자.

```
$ vim f1.txt << 'b' 추가로 쓰고 저장
$ git add f1.txt
$ git commit -m "2"
```
위 과정을 거친 뒤 
gistory를 다시 새로고침해 보면
이번에도 `refs/heads/master`가 바뀌었음을 알 수 있다.

![HEAD](/files/gs-29.png)

이번에 안에 내용은 2를 가리킨다


정리해보자면,
`git init`으로 버전관리를 시작하면 `HEAD `파일이 반드시 생성되고,
`HEAD`파일은 `master`를 가리킨다.
그리고 `master`는 **가장 최신의 커밋을 가리킨다**.

즉, 

git은 `HEAD`라는 파일을 가지고 있고,
그 `HEAD`라는 파일은 (이 예제의 경우)
`refs/heads/master`를 가리키고 있고,
이 파일은 `가장 최근에 commit한 오브젝트의 아이디 값`을 가지고 있기 때문에,

> git log 명령을 하면  
> git은 head파일을 보고 -> 
> head파일이 가리키는 master파일을 보고 ->
> master파일이 가리키는 commit 오브젝트 아이디값을 통해
> 현재 가장 최신 커밋이 무엇인지 알아낼 수 있는 것이다.
> (그 이전 커밋은 최근 커밋의 parent를 통해 탐색해 나갈 수 있다)

```
$ git log
commit d189283f55ff7bf781db6244a1ea926db0a2f3c6 (HEAD -> refs/heads/master)
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 15:27:14 2019 +0900

    2

commit 77d156e4be71df3e810ea5366f91c55551640b7b
Author: Minchul Kang <tegongkang@gmail.com>
Date:   Thu Dec 27 15:24:33 2019 +0900

    1
```


## git branch 의 원리

git 에서 branch는 매우 강력한 도구이지만 
단지 `refs/` 밑에 있는 파일에 불과하다. 실습을 통해 직접 확인해보자.

```
$ git branch exp
$ git branch 	# exp 브랜치 확인
```

gistory를 새로고침하면 두 개의 파일이 바뀌었는데, 
그 중에 refs/heads/exp가 생겼다는 걸 알 수 있다.
![branch](/files/gs-30.png)

그리고 이 파일은 최신 커밋을 가리킨다.
(최신 커밋으로부터 가지가 뿜어져 나오는 것이니까 당연하다.)
![branch](/files/gs-31.png)

여기서 이 .git/refs/heads/exp가 exp 브랜치 그 자체이다.
CLI를 통해 진짜인지 확인해보자.
```
$ git branch
  exp
* master

$ rm refs/heads/exp

$ git branch
* master
```
rm 명령어를 통해 exp를 지우면
git branch를 해도 exp는 보이지 않게 된다.

이와는 반대로 exp를 다시 heads 안에 만들어내면
```
$ vim .git/refs/heads/exp 
$ git branch
  exp
* master
```
다음과 같이 `git branch` 명령어를 통해 새로운 branch로서 감지되게 된다.

> 그렇다고 해도 절대 이 방법으로 branch를 만들고 없애지는 말자.

즉, branch 는 강력하지만 단지  파일 하나에 지나지 않는다는 걸 알 수 있다.


## git checkout의 원리

이번에는 branch를 바꿔보자
```
$ git checkout exp
Switched to branch 'exp'
```

gistory를 새로고침하면 `HEAD` 파일이 바뀌었음을 알 수 있다.

`HEAD`파일은 이제 
ref: `refs/heads/exp`를 가리키고,
exp는 **exp의 최신 커밋**을 가리킨다

![branch](/files/gs-32.png)

그럼 exp 브랜치에서 임의로 커밋을 생성해보자.

```
$ vim f2.txt # 안에 a 작성
$ git add f2.txt
$ git commit -m "3"
[exp 14bac8d] 3
 1 file changed, 1 insertion(+)
 create mode 100644 f2.txt
```

gistory를 새로고침해 보면 

![branch](/files/gs-33.png)

`HEAD` 이번에도 역시 exp를 가리키고
> 방금 커밋을 exp 브랜치에서 했으니까

exp는 방금 한 3번 커밋을 가리킨다는 걸 알 수 있다.

---

그럼 마지막으로 master로 checkout 하면 어떻게 될까?

```
$ git checkout master
Switched to branch 'master'
```
![branch](/files/34.png)

gistory를 새로고침 해 보면 `HEAD`가 master를 가리키고 
master가 가리키는 최신 커밋이 mastaer의 최신 커밋인 2번 커밋이 된다는 걸 알 수 있다.

---

### 덧

```
$ git log --branches --decorate --graph
* commit 14bac8dd8cac4df5c07b0bb659c602f6883f6dca (exp)
| Author: Minchul Kang <tegongkang@gmail.com>
| Date:   Thu Dec 27 15:50:04 2019 +0900
|
|     3
|
* commit d189283f55ff7bf781db6244a1ea926db0a2f3c6 (HEAD -> master)
| Author: Minchul Kang <tegongkang@gmail.com>
| Date:   Thu Dec 27 15:27:14 2019 +0900
|
|     2
|
* commit 77d156e4be71df3e810ea5366f91c55551640b7b
  Author: Minchul Kang <tegongkang@gmail.com>
  Date:   Thu Dec 27 15:24:33 2019 +0900

      1

```

이제는 이 명령어를 `HEAD` 파일이 담는 내용을 토대로 해석할 수 있을 것이다.

현재 우리가 checkout한 branch가 무엇인지를 알려주는 약속된 기호가 
HEAD라고 배웠는데, 

사실 이 HEAD는 하나의 파일에 불과하다는 걸 알 수 있었고,
이 HEAD 파일은 **현재 Checkout한 가장 최근 커밋이 무엇인지를 가리키는 파일**에 불과하다는 걸 알 수 있다.


