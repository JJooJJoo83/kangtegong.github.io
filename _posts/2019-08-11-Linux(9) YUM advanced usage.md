---
layout: post
title: "CentOS Server (9) YUM 고급 사용법"
image: ''
date: 2019-08-11 23:22:06
tags: 
- Linux
- CentOS 
- Server
description: YUM의 동작원리와 고급 사용법
categories:
- CentOS Server Management
---

## YUM의 작동 방식 설정 파일

`yum install 패키지 이름` 명령어가 동작하는 방식

![Link]({{ '/assets/images/centos/9_yum_advanced/1.png' | relative_url }}){: .center-image }


1. yum install 입력 
2. `/etc/yum.repos.d/` 디렉토리의 확장명이 repo 파일을 열어 URL 주소 확인. 	CentOs7 패키지 저장소의 URL주소가 이 디렉토리에 있음
CentOS7 패키지 저장소는 원본 패키지, 업데이트 패키지, 추가 패키지를 따로 관리한다.
실제로 `cd /etc/yum.repos.d` 에 들어가 `ls -l`로 목록을 확인해보면 

	     CentOS-Base.repo			> 원본 패키지 URL을 알려줌
	     CentOS-Debuginfo.repo
	     CentOS-Sources.repo
	     CentOS-Vault.repo
파일이 있는 걸 확인할 수 있다.

vi를 이용해서 CentOS-Base.repo를 열어보면 

[base], [updates], [extras]의 \#baseurl이 각각 원본패키지, 업데이트패키지, 추가 패키지의 주소(경로)이다.

3. 전체 패키지 **목록 파일**을 요청
의존성 파일을 포함한 전체 패키지 목록파일

4. 전체 패키지 **목록 파일**만 다운로드
5. 설치할 패키지와 관련된 패키지의 이름을 화면에 출력
6. y를 입력하면 설치에 필요한 패키지 파일을 요청함
이때가 다운로드가 시작되는 지점이다.

7. 설치할 패키지 파일을 다운로드해서 자동 설치

### yum 관련 파일 


    /etc/yum.conf  파일  

특별히 변경할 필요 없음

    /etc/yum.repos.d/ 디렉터리
`yum` 명령을 입력했을 때 검색하게 되는 네트워크의 주소가 들어 있는 여러 개의 파일이 있음

    /etc/yum.repos.d/ 디렉터리의 *.repo 파일

CentOS-Base.repo : [base], [extra]만 남기고 [updates] 부분은 삭제했음. 즉, 출시 시점의 원본 패키지만 설치됨.


## yum 고급 실습

- CentOS  DVD에서 직접 설치하기 위한 방법을 알아보기.
- *.repo 파일을 직접 편집해 보기
- 네트워크와 DVD를 동시에 사용하는 효율적인 방법을 알아보기.
- '패키지 그룹'을 설치하는 방법을 익히기