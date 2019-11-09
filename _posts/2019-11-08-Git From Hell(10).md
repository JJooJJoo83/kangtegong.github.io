---
layout: post
title: "지옥에서 온 Git-10"
image: ''
date: 2019-10-08 09:30:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## Git의 원리 -1 : Git의 원리 소개 & Gistory

git의 원리에 대해 아는게 좋은 이유

1. 궁금하니까 (호기심)
2. 어렵지 않은 원리. 원리 없이 공부했을 때 보다 더 오래 기억할 수 있음
3. 또 다른 영감을 얻을 수 있을 것


## git의 원리 -2 : 분석도구 gistory 소개

현재 git 오픈소스 전체 소스 분석
git의 첫번째 초기 버전 분석
.git이라는 디렉토리 안에서 어떤 일이 일어나는가를 분석

```
pip install gistory
```

$ ls -al
.git 이 있는 곳으로 이동

```
$ gistroy
```

127.0.0.1:포트넘버 쓰면 로컬 구동이 된다
