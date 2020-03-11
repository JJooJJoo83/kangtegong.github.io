---
layout: post
title: 'git status의 원리'
author: minchul.kang
date: 2019-12-15 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git commit의 동작 원리



index라는 파일이 도대체 무엇일까?

그리고 커밋할 것이 없을 때 뜨는 이 화면,
```
$ git status
On branch master
nothing to commit, working tree clean
```
git은 어떻게 commit 할 게 없음을 아는 걸까?


index 파일과 
가장 최신 commit의 차이를 비교하면 
commit할것이 있는지 없는지 알 수 있음을 짐작해볼 수 있다.

f2.txt를 수정해보자

```
x
y
z
```

그 뒤 status를 보면 f2.txt가 수정되었음을 알 수 있는데,

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   f2.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

이 원리부터 우선 추정해 보자면 

index 파일에 적힌 f2.txt값과
f2.txt 자체 내용에서 generate한 내용이 다르면
f2.txt 파일이 수정되었음을 감지할 수 있다.

![f2 수정1](/files/20.png)
![f2 수정2](/files/21.png)

이번에는 f2.txt를 add 해보자.
```
$ git add f2.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   f2.txt
```

git은 이건 또 어떻게 아는 걸까?
수정하고, add 하여 staging area에 올린 건 어떻게 알아차릴 수 있는 걸까?

이건 최신 commit 과 방금 add한 비교하면 알 수 있다.

아래 사진을 보면 commit 된 f2.txt의 내용과 
add된 objects 내의 f2.txt 내용은 다르다.

![add vs commit ](/files/22.png)
![add vs commit ](/files/23.png)

만일 이 두 정보가 일치하지 않는다면,
새로운 수정 파일이 add 되었고 commit 대기 상태임을 짐작할 수 있을 것이다.

그럼 이제 commit 해보자

```
$ git commit -m "4" 
```
commit이 만들어지면

1. 그 commit이 만들어낸 해시값
2. index의 f2.txt의 해시값
3. 우리 로컬 내의 f2.txt이 해시값

즉 working - staging area - repository의 해시값이 모두 일치하게 된다. 

![add vs commit ](/files/24.png)
![add vs commit ](/files/25.png)

이렇게 세 hash 값이 모두 일치할 때야 비로소 git은 git status를 쳤을 때
```
$ git status
On branch master
nothing to commit, working tree clean
```
를 띄워도 될 것이라 판단하는 것이다.

![transport command](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F257E534353142A7D27)

도움이 될 만한 이미지로 정리를 해보자면,

working directory의 내용을
add하면 index 파일에 등록되고,
여기서 commit하면 index파일에 등록된 내용들이
repository 에 오브젝트로 저장된다,

그리고 여기서 modified / unstaged/ staged / nothing to commit 의 상태 메세지는 이 세 area내의 해시값 비교로 이루어 진다.
