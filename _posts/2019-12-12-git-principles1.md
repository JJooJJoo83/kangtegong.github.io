---
layout: post
title: 'add, object 파일명의 원리'
author: minchul.kang
date: 2019-12-13 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git add 의 동작 원리

git의 명령어들을 입력하고 분석할 새로운 프로젝트 폴더를 만들자.

```
$ mkdir gitfth
$ cd gitfth
```
`git init`으로 이 폴더 내부에 .git 폴더 만들고,
.git 내부 파일을 분석하기 위해 .git 폴더 내부로 들어가보자.

(.git 파일 분석 도구인 gistory도 .git 내부로 들어가야만 실행이 가능하다.)

```
$ cd .git
```
분석 대상인 파일들을 쭉 한 번 눈도장을 찍어보자.
```
$ ls -al
total 11
drwxr-xr-x 1 minchul 197121   0 12월 20 15:20 ./
drwxr-xr-x 1 minchul 197121   0 12월 20 15:31 ../
-rw-r--r-- 1 minchul 197121 130 12월 20 15:20 config
-rw-r--r-- 1 minchul 197121  73 12월 20 15:20 description
-rw-r--r-- 1 minchul 197121  23 12월 20 15:20 HEAD
drwxr-xr-x 1 minchul 197121   0 12월 20 15:20 hooks/
drwxr-xr-x 1 minchul 197121   0 12월 20 15:20 info/
drwxr-xr-x 1 minchul 197121   0 12월 20 15:20 objects/
drwxr-xr-x 1 minchul 197121   0 12월 20 15:20 refs/
```

이제 gistory 로 서버 열고, [http://localhost:8805/](http://localhost:8805/)에 접속하여 위에서 확인한 .git의 파일 및 폴더들이 잘 잡혔는지 확인하자. 
```
$ gistory
```
![gistory실행화면](/files/0.png)

지금 띄워진 좌측의 리스트가 .git 내부의 파일 전체라고 보면 된다.

이제 분석할 git 명령어를 입력할 수 있는 새로운 터미널을 새로 하나 열자.
> 이 때 gistory를 실행한 기존 터미널을 끄면 gistory가 종료되므로 끄지 말자.

---
이제 f1.txt 라는 이름의 텍스트 파일을 하나 만들고, 내부에는 'a' 라는 문자열을 넣어보자.
```
vim f1.txt 
```

f1.txt를 새로 만든 뒤, 
그리고 gistory에 새로고침을 해도 변함이 없다.

f1.txt는 버전 track을 시작도 안했기 때문이다.

![gistory실행화면](/files/1.png)

그렇다면 이번에는 f1.txt를 add 한 뒤 gistory를 새로고침해보자.

```
git add f1.txt
```

gistory 내부에 변화가 생겼다.
두 개의 파일이 변경되었는데, 

```
./index
./objects/78/9819...  
  # objects 폴더 안의 78 디렉토리의 9819... 라는 파일

```

이 두 파일이 추가 되었음을 알 수 있다.


obejcts의 파일이 무엇인지 눌러보자.

![objects](/files/2.png)

objects 디렉토리 안에는 f1.txt의 내용(즉, a 라는 문자)이 담겨있다.

그리고 하단을 보면 

> f1.txt (This information was not included the original message)

라고 f1.txt의 파일 이름이 나와 있을 텐데, 이건 그냥 gistory의 편의 기능이다.

실제로 objects/78/9819.. 에는
git add 한 f1.txt 파일**내부의 내용**(이 예제의 경우, a)에 관련된 정보만 있지,
**파일 이름**과 관련된 정보는 없다.

그렇다면 git add한 파일의 이름 정보(이 예제의 경우, f1.txt)는 어디에 담기느냐?
파일의 이름은 index라는 파일에 담긴다.

이번에는 index를 눌러보자. 

![index](/files/3.png)

> f1.txt라는 파일은 78981922613b2afb6025042ff6bd878ac1994e85 로 시작하는 어떠한 정보에 담겨있다 

라는 정보가 나와 있다.

이 `78981922613b2afb6025042ff6bd878ac1994e85` 의 의미는,
78번 디렉토리의 9819... 로 시작하는 파일을 가리킨다

즉, 파일의 **이름**은 index에 담겨있고 
파일의 **내용**은 objects에 담긴다는 것을 알 수 있다.

---

자, 그럼 이번에는 파일을 하나 더 만들어 보자

```
vim f2.txt  # 안에 z라고 적고 저장
```

git add f2.txt 했을 때 
gistory는 어떤 변화가 생길까?

두 개의 파일이 달라졌다.

![f2 add](/files/4.png)

index 안에는 f2.txt의 내용은 이것입니다 라고 적혀있다.

![f2 index](/files/5.png)


그리고 그에 따라 objects/b6/802 ...  가 보면 그 내용이 담겨있음을 확인할 수 있다.

![f2 objects](/files/6.png)

---

그럼 이번에는 f1과 동일한 내용으로 f3을 만들고,
그리고 f3을 add 해 보자.

```
$ cp f1.txt f3.txt
$ git add f3.txt
``` 

gistory를 확인해보면 
이번에도 바뀐 건 두 개인데, 

> objects/78/9819..

f1을 add했을 때와 동일한 object가 생겼다는 걸 알 수 있고,

![f3object](/files/7.png)

index를 가 봐도 f1과 f3가 같은 object를 가리키고 있음을 확인할 수 있다.

![f3index](/files/8.png)


즉, 이를 통해 알 수 있는 것은,

> git은 어떤 파일을 저장할 때,
> 파일의 이름이 달라도 파일의 내용이 같으면 
> 같은 object 파일을 가리킨다

는 것이다.

즉, 아무리 많은 파일이 10000개가 있어도 모두 내용이 같다면 
index에는 10000개의 파일명만 담기고 
같은 object를 가리키는 셈이다.

그리고 이렇게 중복 제거효과를 얻을 수 있다는 걸 알 수 있다.


## objects 파일 명의 원리


그럼 git은 어떻게 내용이 같아지면 파일 이름까지 같아질까?

즉, 내용을 기반으로 파일의 이름이 결정되는 메커니즘은 무엇일까?

간단하다. SHA1방식을 적용한다. 

(궁금한 사람들은 인터넷에 SHA1 online을 검색해보고, 
여러 문자열들이 SHA1를 거쳐 어떻게 해쉬값으로 매핑되는지 체험해보자.)

> 즉, object가 될 문자열을 SHA1을 통과시켜 
> 처음 두 문자는 디렉토리 명으로, 
> 나머지 문자는 object 이름으로 삼는다

 
예를 들어 hello는 SHA1를 통과하면 

aaf4c61.. 의 결과를 얻게 되는데, 

objects 디렉토리 내부에 aa라는 하위 폴더를 만들어 

aa/f4c61... 로서 저장된다고 보면 된다.