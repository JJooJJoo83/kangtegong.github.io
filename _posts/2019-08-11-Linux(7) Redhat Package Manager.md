---
layout: post
title: "CentOS Server(7) RPM (Redhat Package Manager)"
image: ''
date: 2019-08-11 21:20:06
tags: 
- Linux
- CentOS 
- Server
description: 리눅스 상의 프로그램 설치를 위한 RPM
categories:
- CentOS Server Management
---


## RPM (Redhat Package Manager)

초기 리눅스는 설치가 어려웠다.
원칙적으로 설치를 위해서는 소스코드를 갖고 와서 컴파일을 했었는데, 때문에 일반 사용자들이 이용하기가 어려웠다.

이 때, Redhat사에서 내놓은 간편한 설치방식. 
Windows에서 setup.exe만 더블클릭 다음 다음 누르면 설치 되는 것처럼
간단한 rpm 파일에 대한 명령어 한 두개로 프로그램을 설치한다는 의의 

확장명은 *.rpm이며, 이를 ‘패키지(Package)’라고 부른다.
이 rpm 파일은 /dev 안에 엄청나게 많이 들어있다.


### 파일의 의미

예시를 위해 gedit의 rpm을 살펴보자

    pwd
	    >> /run/media/root/CentOS 7 x86_64/Packages
	ls -l geidt-3.*
		>> gedit-3인 모든 확장자 보기
  
 gedit-3.8.3-6. el7. x86_64.rpm 이라는 rpm을 확인할 수 있다. 이는

      패키지이름-버전-릴리즈번호.CentOS버전.아키텍처.rpm

의 의미를 가지는 rpm 파일이다.

1. 패키지이름 : `gedit` → 패키지(프로그램)의 이름
	프로그램 이름이 아니라 패키지라고 부르는 이유
	이 패키지 안에 여러 개의 명령어들 (프로그램들) 이 들어있는 경우도 			있음
	
2. 버전 : `3.8.3`  
	대개 3자리수로 구성. 주버전, 부버전, 패치버전
3. 릴리즈번호  : `-6` 
	문제점을 개선할 때마다 붙여지는 번호
4. CentOS 버전 : `el7`  
	el은 enterprise linux의 약자 (Centos = Redhat Enterprise Linux). 7은 Centos 7을 의미. CentOS에서 배포할 경우에 붙여지는 부분이라고 보면 된다.
5. 아키텍처 : `x86_64`  
	64비트 CPU를 의미

참고로 아키텍쳐는 쉽게 말해 CPU인데, 다음과 같이 나뉜다.
`x86_64` : 64비트 CPU
`i386~686` : 32비트 CPU
`src` : 설치프로그램이 아닌 컴파일을 할 소스가 설치된다. 주로 c 소스. 
`noarch` : 모든 CPU

## 자주 사용하는 RPM 명령어 옵션

### 설치 : `rpm -Uvh  패키지파일이름.rpm`

`–U` : **(대문자)** 패키지가 설치/업그레이드
`–v` : 설치과정의 확인
`–h` : 설치진행과정을 "#"마크로 화면에 출력

### 삭제 : `rpm -e 패키지이름`

### 이미 설치된 패키지 질의

패키지가 설치되었는지 확인

    rpm -qa  패키지 이름

파일이 어느 패키지에 포함된 것인지 확인

    rpm -qf  파일의절대경로

###  아직 설치되지 않은 rpm 파일에 대한 질의

패키지 파일에 어떤 파일들이 포함되었는지 확인

    `–rpm -qlp  패키지파일이름.rpm`

패키지 파일의 상세정보

    `–rpm -qip  패키지파일이름.rpm`

## RPM의 단점

### ‘의존성’ 문제

A패키지가 설치되기 위해서 B패키지가 필요할 경우, RPM으로는 해결이 까다로움. 이를 해결하기 위해 YUM이 등장함

## RPM 실습

