---
layout: post
title: "지옥에서 온 Git-12"
image: ''
date: 2019-10-08 09:32:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## Git의 원리 -3 : objects 파일명의 원리

내용을 기반으로 파일명이 결정되는 원리에 대해 알아보자

sha1 online

hi를 누르고 hash를 누르면 
c22b ... 하는 정보를 만들어준다

hello를 누르고 hash를 누르면 
aaf4c... 라는 정보를 만들어준다

git은 SHA에서 나온 결과값 처음 두 글자를 따서 
objects 디렉토리 이름으로 삼고
그 이하 문자열을 hello 라는 정보를 저장하는 수단으로 삼는다

hello
objects/aa/f4c61...4d

물론 hello라는 글자만 저장되는 건 아니니 해시값은 다를  수 있지만 
추가적인 동작은 동일하다

## add 의 원리

git add를 하면

git은 add한 파일의 내용을 보고
a라는 정보와 몇 가지 부가적인 정보를 추가해서
압축한다

그리고 그 압축한 값을 sha로 통과시킴

index 폴더 안에 파일 명을 저장

