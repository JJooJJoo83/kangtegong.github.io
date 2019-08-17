---
layout: post
title: "CentOS Server (8) YUM (Yellowdog Update Modified)"
image: ''
date: 2019-08-11 21:22:06
tags: 
- Linux
- CentOS 
- Server
description: RPM의 문제를 해결한 YUM
categories:
- CentOS Server Management
---

## YUM (Yellowdog Updater Modified)

### 등장배경 및 개념 

`rpm` 명령의 패키지 의존성 문제를 완전하게 해결

인터넷을 통하여 필요한 파일을 저장소(Repository)에서 자동으로 관련된 의존파일까지 모두 다운로드해서 설치하는 방식이다.

당연한 얘기지만, 인터넷이 정상적으로 동작하지 않으면 동작하지 않고, NPM 처럼 DVD가 필요하지 않다.


### YUM 기본 사용법

기본 설치 : `yum install 패키지이름` (패키지파일 이름이 아니다!)
주로 `yum  -y  install 패키지이름`으로 사용

`-y`는 사용자의 확인을 모두 yes로 간주하고 설치를 진행한다는 옵션

**RPM 파일** 설치 : `yum localinstall rpm파일이름.rpm`
업데이트 가능한 목록 보기 : `yum check-update`
업데이트 : `yum update 패키지이름`
정보 확인 : `yum info 패키지이름`
삭제 : `yum remove 패키지이름` 

> 이때, 의존성파일은 삭제되지 않는다. 
> 다른 패키지가 필요한 의존성 					  파일일 수도 있으니까.



저장소의 URL은 `/etc/yum.repos.d/` 디렉토리


## YUM 실습

mc 설치

    yum install mc

설치화면이 나온다.

mc 삭제 


    yum remove mc

굳이 y 누를 필요 없이 확실히 깔 경우 (일반적인 경우)

    yum -y install 패키지이름

npm 상의 의존성 문제의 해결

    yum install mysql-connector-odbc

설치 화면에서 아래 문구를 볼 수 있다

    Installing :
    		mysql-connector-odbc
    Installing for dependencies:
    		unix0DBC

설치를 승인하면 의존성 파일까지 설치가 마무리 된다.



## YUM 고급 사용법 

### 패키지 그룹 설치

    yum groupinstall  “패키지그룹이름”
 
패키지 그룹 : 여러 개의 패키지로 이루어져 있는 그룹 
e.g. JAVA 패키지 그룹 : JDK, JRE, Eclipse ...  

### 패키지 리스트 확인

    yum list 패키지이름

### 특정 파일이 속한 패키지 이름 확인

    yum provides 파일이름

### GPG 키 검사 생략

    yum install --nogpgcheck rpm파일이름.rpm

	CentOS  19에서  검증되지 않은 패키지를 강제로 설치할 때 사용

### 기존 저장소 목록 지우기

    yum clean all
가끔 yum에 문제가 있으면 clean all로 캐시를 지운 뒤 사용해보자