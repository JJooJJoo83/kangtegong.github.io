---
layout: post
title: "지옥에서 온 Git-11"
image: ''
date: 2019-10-08 31:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## Git의 원리 -2 : git add

새 프로젝트 폴더 생성

mkdir gitfh2

git init

ls -al
하면 .git이라는 숨김폴더가 생김

cd .git을 통해 .git 내부로 들어가면

```
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 ./
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 ../
-rw-r--r-- 1 minchul 197121 130 11월  8 17:08 config
-rw-r--r-- 1 minchul 197121  73 11월  8 17:08 description
-rw-r--r-- 1 minchul 197121  23 11월  8 17:08 HEAD
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 hooks/
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 info/
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 objects/
drwxr-xr-x 1 minchul 197121   0 11월  8 17:08 refs/

```
다음과 같은 파일 및 폴더를 볼 수 있다.

이것들의 역할을 한번 알아보자.

그럼 .git 내부에서 gistory를 실행시켜보자

$ gistory

그럼 localhost:8805 에 웹사이트가 열린다


gistory가 실행되고 있는 동안은 명령어를 입력하기 어려우므로
새로운 배시(터미널)창을 하나 더 열어주자


새 터미널에서
파일을 우선 추가해보자

vim f1.txt
안에 내용은 a 입력

gistory에서 새로고침해도 변화 없음

이번에는 git add f1.txt로 add 시켜보자

이번에는 gistory에서 새로고침하면 

index
objects/78/9819....

이렇게 두 개의 대상이 추가되었음을 확인할 수 잇다

objects/78/9819....

를 클릭해보면

우리가 추가한 f1.txt의 내용이 담겨있다.

gistory는 "a라는 내용이 담긴 파일의 이름은 f1.txt다" 라는 걸 보여주긴 하지만
이건 gistory만의 기능이지
실제로는 a라는 내용을 담은 파일의 이름은 objects/78/981... 에 담겨있지 않다.

그렇다면 파일의 이름은 어디에 적혀 있을까?

index에 적혀 있다.

index를 열어보면 
f1.txt라는 파일은 789819... 로 시작하는 어떠한 정보에 담겨 있다
고 적혀있다

그리고  789819...  이 녀석이 방금 봤던 이 녀석이다.

즉,  789819... 이렇게 생긴 id는 
objects 디렉터리 안의  789819...라는 파일을 가리킨다

그리고 그 파일은 a가 되는 것이다.

정리하자면, 

파일의 이름은 index에 담겨있고
파일의 내용은 objects에 담겨있다

그리고 objects 디렉터리 안에 있는 파일들을 우리는
오브젝트(객체)라고 부른다



이번에는 파일을 하나 더 만들어보자

vim f2.txt

안에 z
라고 적어본 뒤 저장하자

git add f2.txt

했을 때 gistory상에서는 어떤 변화가 생길까

새 objects를 누르면 z라는 내용을 담고 있음을 알려준다
또
index에서는 f2.txt 라는 파일명이 적혀있다 

이번엔 조금 방법을 달리 해서

cp f1.txt f3.txt

git add f3.txt

바뀐 파일은 두 개인데 
그 중 78/98... 는 아까도 있었는데 이번에 업데이트가 되어 있다

a 라고 적혀있다

index를 클릭하면 (이부분 중요)

f1.txt는 7898...
f3.txt도 7898...

즉, f1.txt 와 f3.txt는 같은 오브젝트를 가리킨다는 뜻이 된다

즉, git은 어떤 파일을 저장할 때 파일의 이름이달라도 
파일의 내용이 같으면 같은 오브젝트 파일을 가리킨다
 
 즉, 중복의 제거에 크게 한 몫 하는 셈
git 은 어떻게 내용이 같으면 파일도 같아질까?
 
 다음시간에