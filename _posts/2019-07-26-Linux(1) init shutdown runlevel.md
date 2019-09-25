---
layout: post
title: "CentOS Server (1) 시작과 종료, 런레벨"
image: ''
date: 2019-07-26 16:25:06
tags: 
- Linux
- CentOS 
- Server
description: 로그아웃, 종료, 런레벨, 히스토리 등.
categories:
- CentOS Server Management
---

## 우선 리눅스 상식 먼저 

	#프롬프트	: root 사용자 
	$프롬프트	: 일반 사용자


---

## 종료, 재부팅, 로그아웃<br>

### 종료하는 방법

1. Gui 방식으로 종료
2. `shutdown -p`
3. `halt -p`
4. `init 0`

### 시스템 재부팅

1. GUI 방식으로 재부팅
2. `shutdown -r now`
3. `reboot`
4. `init 6`

### 로그아웃

1. GUI 방식으로 로그아웃
2. `logout`
3. `exit`


---


## shutdown 명령어

### shutdown -h : 종료
    shutdown -h now 		지금 즉시 종료
    shutdown -h +10 		10분후 종료
    shutdown -h +20 		20분후 종료
    
### shutdown -c : shutdown 명령 취소

### shutdown -r : 재부팅
    shutdowon -r +20		20분뒤 재부팅
    shutdowon -r 23:00		23시에 재부팅

### shutdown -K
실제로 종료하지는 않고 메세지만 보내준다. 

이용자가 많아 리소스가 부족할 때 유용한 방법

    shutdown -K			실제로 종료하진 않고 메세지만 보냄

    shutdown -K +20		실제로 종료하진 않고 20분뒤에 꺼진다는 메세지만 보냄


리눅스는 다중사용자시스템으로 기본적으로 shutdown이 아니라 logout하는게 좋다


## 총 6개의 가상 콘솔

말 그대로 가상의 콘솔 (가상의 모니터)
CentOS는 6개의 가상콘솔을 지원한다.

1. 단축키

    `Ctrl` + `Alt` + `F1~F6`

(F1 == X윈도우)

2. 명령어

	chvt 가상콘솔번호

e.g.) chvt 2

## 리눅스의 런레벨


|번호 | 설명|
|------------ | -------------|
|0 | Power Off|
|1 | 단일 사용자, 시스템 복구모드 (Rescue)|
|3 | 다중 사용자, 텍스트모드|
|5 | 다중 사용자, 그래픽 모드|
|6 | Reboot|
{:.inner-borders}


### 런레벨 모드 직접 확인하기 
`/lib/systemd/system/` 디렉토리로 이동하여 `runlevel?`이라는 이름의 모든 target파일을 찾아보자.
(참고로 runlevel?에서 ?는 한 글자)

    ls /lib/systemd/system/runlevel?.target

그럼 0,1,2,3,4,5,6 까지의 `runlevel?.taget` 파일들이 나온다

이들을 자세히 알아보자

    ls -l /lib/systemd/system/runlevel?.target

그럼 각각의 target 파일들이 0부터 6까지 각각 

    poweroff.target
    rescue.target
    multi-user.target
    graphical.target
    reboot.target

을 가리키고 있음을 알 수 있다.

사실 파일 종류 (맨 앞)에 쓰여있는  `l` 을 통해 
이 `runlevel.target`이 진짜 파일이 아니라 링크파일 (바로가기 아이콘)임을 알 수 있다. 

### 서버에 설정된 런레벨 확인후 변경

    ls -l /etc/systemd/system/default.target 

이 명령어를 통해 처음 부팅을 할 때 
어떤 방식으로 부팅되는지를 알 수 있다 .

(default.target 이 시스템이 부팅될 때 디폴트로 지정된 런레벨을 가리키고 있는 셈. )

`/etc/systemd/system/default.target` 이 가리키는 녀석이 처음 부팅되는 방식인데, 이 녀석이 `graphical.target`을 가리키고 있으므로 X윈도 방식으로 부팅될 것임을 알 수 있다.

따라서 `default.target`이 가리키는 대상을 `graphical.target`이 아닌 `multiuser.target` 등으로 바꾸면
바뀐 방식으로 부팅된다.

    ln -sf /lib/systemd/system/multi-user.target /etc/systemd/system/default.target

위 명령어를 통해 default.target이 가리키는 대상을 바꿔주고

다시 `ls -l /etc/systemd/system/default.target`을 통해  런레벨이 잘 바뀌었음을 확인한 뒤 재부팅해보자

그럼 이번엔 텍스트모드로 부팅된다는걸 알 수 있다.

(참고로 `startx`를 통해 x윈도모드로 돌아갈 수 있다.)

    ln -sf /lib/systemd/system/graphical.target /etc/systemd/system/default.target

그래픽 모드로 부팅되도록 되돌아가자


참고)

### 명령어 기록 보기

    history

### 명령어 기록 삭제

    history -c
