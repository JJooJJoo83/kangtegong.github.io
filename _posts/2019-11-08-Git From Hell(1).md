---
layout: post
title: "지옥에서 온 Git-01"
image: ''
date: 2019-10-07 09:24:06
tags: 
- Git
- Tutorials
description: Git From Hell Lecture note
categories:
- Git
---

## 1. 수업소개

### 버전관리시스템 

git은 버전관리시스템의 일종이다.
버전관리라고 함은 특별한 게 아니고,
그냥 우리는 계속 해 오던 것들이다.

예를 들면 처음 `report.pdf`를 작성 및 저장한 뒤,
약간 수정해서 `report_final.pdf` 로 저장하고,
나중에 "아 맞다 수정할 거 있다" 하고 `report_real_final.pdf`로 저장하는 게
따지고 보면 다 버전관리인 셈이다.

### 버전관리 시스템 (Version Control System)이 제공하는 가치

우리도 모르는 새 버전을 관리하고 있었다면 
분명히 어떤 효용이 있어서일 것이다.
그 효용에는 여러 가지가 있을 수 있는데,
대표적인 효용 몇 가지를 꼽자면

> 1. Version Management, not by changing the file name (파일의 이름을 더럽히지 않는 버전관리)
> 2. Backup
> 3. Recovery
> 4. Collaboration

정도를 꼽을 수 있을 것이다.

### Git에서 배울 것들

버전관리시스템의 종류에 git만 있는 건 아니다.
git은 버전관리시스템의 종류 중 하나로,
git 말고도 CVS, SVN 등도 버전관리를 도와준다.

이 때 여러 버전관리시스템들은 모두 각기 본질적인 요소와 혁신적인 요소를 가지고 있다.

여기서 본질적인 요소란 

> 여러 버전관리시스템의 공통적이고 보편적인 가치

를 뜻하고, 

혁신적인 요소란,

> SVN만의 가치, GIT만의 가치 처럼 각 버전관리시스템만의 특징 및 가치

를 뜻한다. 우리는 우선 버전관리시스템의 본질적인 요소를 먼저 파악하고 
git의 혁신적 요소를 배울 생각이다.

### Git의 학습에 앞서

GIT은 매우 복잡하다.

git을 굳이 쓰지 않아도 앞서 언급했듯  
Dropbox, Google Drive 등으로 버전관리가 가능하긴 하다.  

그렇다면 이 어려운 git을 왜 또 굳이 쓸까?  

> 우리의 현실은 git보다 훨씬 더 지옥같기 때문이다..

만일 프로젝트 복잡성이 git의 복잡성보다 경우,
즉 아래의 그림과 같은 경우라면,
git을 굳이 사용할 필요가 없을 것이다.

![왜배울까](/assets/img/hellgit/1/1.png)

하지만 만일 프로젝트 복잡성이 git 복잡성보다 클 경우,
비로소 git은 빛을 발하게 된다.
아래와 같은 상황을 위해 굳이 힘들게 git을 배우는 것이다.
즉, git을 배워두면 복잡한 프로젝트를 관리할 수 있는 힘이 생긴다.

![왜배울까](/assets/img/hellgit/1/2.png)