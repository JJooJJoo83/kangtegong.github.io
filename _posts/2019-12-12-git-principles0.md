---
layout: post
title: 'git의 분석 방법과 gistory'
author: minchul.kang
date: 2019-12-12 12:00
tags: [git-analysis]
---

이 포스트는 [생활코딩-지옥에서 온 git 수업]([https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk](https://www.youtube.com/watch?v=hFJZwOfme6w&list=PLuHgQVnccGMA8iwZwrGyNXCGy2LAAsTXk)) 을 토대로 만들어졌습니다

## git의 동작을 분석하는 방법

git의 원리를 분석하는 두 가지 합리적인 방법에는 
1. git이 세상에 처음으로 나왔을 당시의 초기 버전을 분석하는 방법
2. 어떠한 명령을 내렸을 때 .git 폴더 안에서 어떤 일이 일어나는 지 분석하는 방법

이 두 가지 방법이 있을 수 있다.

이 튜토리얼에서는 후자, 즉 **각종 git 명령어에 따른 .git 폴더 내부의 작동 원리**를 중심으로 git 의 원리에 대해 알아볼 예정이다.

## 분석 도구 Gistory 소개

gistory는 git의 내부 동작 과정을 분석하는 데에 도움을 주는 툴이다.
(물론 파이썬 기반이니 파이썬을 우선적으로 다운로드 받아야 한다.)

터미널에 
```
pip install gistory
```
를 치면 쉽게 설치가 가능하다.
gistory는 .git 폴더 내부 동작을 tracing 해주는 툴이니 만큼 
실행을 .git 폴더 안에서만 할 수 있다. 
따라서 .git 폴더를 만들어주기 위해 git을 실행해보자.

```
git init
```
.git 폴더가 생성되었다면 .git 폴더 안으로 들어간 뒤,
`gistory` 명령어를 통해 gistory를 실행시켜보자.

로컬 호스트의 포트번호 8805에서 실행될 것이다.
[http://localhost:8805/](http://localhost:8805/) 

gistory의 기본 인터페이스는 다음과 같다.
![gistory실행화면](/files/0.png)

좌측에 리스트로 보이는 것이 .git 폴더 안에 있는 모든 파일들의 목록이다.

이제 이런 저런 git 명령어들을 치며 이 gistory가 어떻게 동작하는지를 살펴볼 것이다.
