---
layout: post
title: "CentOS Server (4) CD/DVD의 Mount"
image: ''
date: 2019-08-04 21:20:06
tags: 
- Linux
- CentOS 
- Server
description: CD, DVD mount와 dismount
categories:
- CentOS Server Management
---

## mount란?  

> 물리적인 장치와 특정한 위치(대게는 디렉토리)를 연결해주는 과정

리눅스에서는 mount가 아주 중요하다. 
물리적인 장치와의 연결을 관장하기 때문이다.

우린 Window상에서 는 CD나 DVD를 아주 쉽게 쓰곤 했다.
(그냥 넣으면 바로 연결이 되었으니)

하지만, 리눅스에서는 경우에 따라 이러한 자동 연결이 지원되지 않는다..
따라서 물리적인 장치와 내가 원하는 디렉토리를 연결해줘야 하는데,
이 과정을 마운트라고 한다.

## CD/DVD 넣는 법 

ㅑ사진ㅕ
Settings

Device Status의 Connected, Correct at power on을 체크

실습할 때는 ISO Image 파일을 활용하는 게 좋으니까
Centos ISO파일이 있는 경로를 설정해 준다

그렇다면 실제 DVD를 삽입한 것과 동일한 상황이 된다.

(실제로 자동으로 DVD가 인식이 되었다고 뜰 **수도** 있다. 버전에 따라 안뜰 수도 있고)

 

## X윈도우 상에서의 Mount

Settings에서 설정하면 자동 mount가 될 수도 있는데
이러한 자동 연결을 맹신해서는 안된다!!

어떤 Linux는 자동 연결이 될 수도 있지만
어떤 Linux는 불가능하다

또한 Text모드는 절대 자동연결이 불가능하다.

따라서 수동으로 연결하는 과정이 필요하다

## CLI 방식의 Mount 

우선 이미 자동으로 연결된 CD/DVD를 끊어야 한다

CD or DVD 장치는 `/dev/cdrom` 가 되는데,
연결 해제 명령어는 `umount` 이다.

따라서 CD, DVD 장치 연결 해제는

    umount /dev/cdrom

명령어로 끊을 수 있다.

하지만 가끔씩, umount 명령어가 안 먹힐 때가 있는데, 이미 연결된 것이 아무것도 없을 때 명령어가 안먹히게 된다

> not mounted

따라서 여기서 중요하게 가져가야 할 팁 중 하나는, 
CD나  DVD를 연결하면 연결해제를 먼저 해야 한다

자 이제 CD/DVD를 mount하면 될텐데,
보통 CD장치를 연결하기 위해서는 우리 임의로 만든 특정 폴더 상에서 연결한다.

으레 `/media/` 폴더 아래 `cdrom` 폴더를 많이 만든다.

    ls /media/
    mkdir /media/cdrom

이렇게 cdrom이라는 이름의 마운트할 폴더를 만들고 나면
mount 명령어로 실제로 mount 작업을 해 주면 된다.

mount  이 장치를  이 폴더로 형식으로 명령어를 써 주면 되는데
따라서 우리 예제의 경우에는 

    mount /dev/cdrom  /media/cdrom

명령어를 쳐 주면 된다.

이 때, 

> mount : /dev/sr0 is write-protected, mounting read-only

이라는 경고가 뜰 수 있다.

cd /media/cdrom/
ls
Centos 설치한 DVD의 파일 및 폴더들의 목록들 이 뜬다

Windows 계열에서는 자동으로 D드라이브에 설치 되지만 
리눅스는 이렇듯 다소 복잡한 절차를 거친다

DVD의 사용이 끝났으면 mount를 해제하는게 좋다

    umount /dev/cdrom

근데, 이 때 주의할 점이 있다.
umount 명령어를 입력할 때 cdrom 위에서 명령어를 치면

> CD/DVD가 사용 중이다, target is busy

경고창이 뜰 수도 있다.

따라서 반드시 cdrom을 빠져나간 뒤 umount를 치도록 하자.

참고로 그냥 cd 를 누르면 User의 홈 디렉토리로 이동한다.

    cd
    umount /dev/cdrom
	    >> 아무것도 안뜬다. 즉, umount가 성공했다.
    ls /media/cdrom/
		>> 아무것도 안뜬다. 즉, mount가 끊어졌다.


### 명령어 모아보기

    umount /dev/cdrom
    mkdir /mycdrom
    mount /dev/cdrom /mycdrom
    	>> mount : /dev/sr0 is write-protected, mounting read-only
    cd /mycdrom
    	>> ls로 cdrom 장치 내용 확인
    cd
    umount /dev/cdrom
    ls /mycdrom/
    	>> 아무것도 안뜸 확인

## 리눅스에서 ISO 파일 제작

(교재 참고) 

CD 레코딩 명령어 "genisoimage" 사용법 익히기
ISO 파일을 리눅스에서 마운트해서 사용하는 방법 익히기
