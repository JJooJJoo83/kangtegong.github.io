---
layout: post
title: 'commit의 원리'
author: minchul.kang
date: 2019-12-14 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git commit의 동작 원리

앞선 tutorial에서 add 한 파일들을 commit 한 뒤 gistory를 관찰해보자.
```
$ git commit -m "1"
$ git log
```
![git commit](/files/9.png)

just now에 해당되는 게 5개나 생겼고,
시간 상 index, objects도 commit 직전에 영향을 받음을 확인할 수 있다.

objects 파일을 눌러보면 

```
[tree] e42e333fda358e829fd6f5d169037317953ee494

100644 blob 78981922613b2afb6025042ff6bd878ac1994e85	f1.txt
100644 blob b68025345d5301abad4d9ec9166f455243a0d746	f2.txt
100644 blob 78981922613b2afb6025042ff6bd878ac1994e85	f3.txt
```

라고 나와 있음을 알 수 있는데, 

방금 작성한 commit에 대한 정보도 
objects 디렉토리 안에 함께 기록된다는 걸 알 수 있다.

즉, 

> 우리가 commit을 해도 
> 우리가 한 그 commit(버전)도 마치 git add 했을 때의 파일처럼
> objects 폴더 안에 들어간다

다시 말해, 

> commit 도 하나의 object이다

라는 것을 알 수 있다.

커밋의 내용 중 `tree` 에는 SHA1으로 만들어진 값(`e42e333fda358e829fd6f5d169037317953ee494`)이 또 생성되어 있음을 
확인할 수 있다

즉, `[tree] e42e333fda358e829fd6f5d169037317953ee494` 라는 `tree` 는 
f1, f2, f3 라는 각각의 파일의 **내용**이 무엇인가에 대한 정보를 담고 있는 파일인 셈이다.

> tree 내의 해시값을 눌러보면 각각의 해시값이 f1/f2/f3의 내용을 담고 있음을 알 수 있다.

![tree](/files/10.png)


정리하자면, commit  한 결과는 object 파일로 저장되고
그 object는 tree로 저장되는데, 그 tree를 보면 현재 우리가 작성한 버전에 
해당하는 파일의 이름과 내용이 링크되어 있다.

---

그렇다면 버전(커밋)을 한번 더 만들어보자

이번엔 f2.txt 안의 내용을 

```
y
z
```
로 바꾸어 보자

```
$ git add
```
를 하면 지금껏 그래왔던 것 처럼 
index와 objects에 변경이 이루어지는데, 

index에는 add 된 f2의 정보가,
objects/27/95c87096b42d4b7b8fda82a67dabded5c72c97 에는 
f2.txt 안의 내용이 들어간다는 걸 알 수 있다.

![f2 git add ](/files/11.png)
![f2 git add index ](/files/12.png)

이 상태에서 
```
$ git commit -m "2"
```

를 하면 

커밋이 담긴 객체인 
`objects/3d/09135ffd802731c47e87386719d99e20e8895a`가 생긴다

![f2 commit](/files/13.png)
![f2 commit](/files/14.png)

내부를 확인해 보면 

parent라는게 생겼음을 확인할 수 있다.

그리고 짐작해볼 수 있겠지만 
parent를 클릭하면 이 커밋의 이전 커밋을 볼 수 있다.

![f2 commit](15.png)

그리고 여기서 알 수 있는 중요한 사실은, 

첫 번째 커밋과 두 번째 커밋에서 tree 값이 다르다는 것이다.

![f2 commit](/files/16.png)
![f2 commit](/files/17.png)


첫번째 커밋이 가리키는 f2.txt 객체를 보면
두번째 커밋이 가리키는 f2.txt 객체이 서로 다르다.

![f2 tree1](/files/18.png)
![f2 tree](/files/19.png)


정리하자면, 
커밋에는 주요한 정보가 두 개 있다

1. 이전 커밋이 누구인가 parent값 
2. 그 커밋이 일어난 시점에 우리 작업 디렉토리에 있는 파일이름과 내용 정보(tree)

각각의 버전마다 서로 다른 tree를 가리키고 있고
그 tree에는 파일의 이름과 내용이 각각 링크되어 있기 때문에

우리는 버전에 적힌 트리를 통해 
그 버전이 만들어진 시점의 프로젝트 폴더에 대한 상태를 얻어낼 수 있다.

그리고 버전이 만들어진 시점의 프로젝트 폴더의 상태를
**스냅샷** 이라고 한다.

즉, 각각의 버전은 그 버전이 만들어지는 시점의 스냅샷을 트리라고 하는 자료구조로 갖고 있는 셈이다

---

objects 디렉토리 내부에 들어가는 object 파일은 크게 셋 중 하나다
(object id를 가지는 세 가지 파일 유형)

1. 파일의 내용르 담고 있는 blob
2. 어떤 디렉토리의 파일명과 그 파일명에 해당하는 blob 정보를 담고 있는 tree
3. commit
