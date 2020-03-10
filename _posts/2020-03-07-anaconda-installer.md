---
layout: post
title: 'System Engineering[0] : Linux Anaconda Installer'
author: minchul.kang
date: 2020-03-07 12:00
tags: [system-engineering]
---

> RHEL을 설치하는 과정에서 anaconda에 대해 알아봅니다.

## anaconda installer란 무엇인가?

Anaconda는 Linux의 설치를 조금 더 사용자 친화적으로 수행할 수 있도록 도와주는 installer이다.
(파이썬 IDE anaconda와는 다르다!)

즉, 그래픽 형식의 설치 화면에서 설정값을 입력하는 것 만으로도 설치가 완료될 수 있게 된 게 anaconda 덕택인 셈이다. Anaconda가 없다면 파일시스템 공간 할당부터, 부팅을 어떻게 시켜야 하며, 커널은 어디에 있고,
키보드 언어설정은 어떻게 할 것인지 등등 설정 파일을 하나하나 만들며 수동으로 집어 넣어야 했을 것이다. 

그렇다면 Linux (RHEL 7.6) 설치 과정에서의 anaconda를 GUI 방식으로 한 번, CLI 방식으로 한 번 직접 눈으로 확인해보도록 하자.

## 설치 과정에서의 anaconda

### anaconda GUI

> vmware로 작업하는 경우 easy installer 제거한 후 설치해야 한다. 그렇지 않으면 후술할 설치 과정에서의 항목들을 볼 겨를도 없이 모두 지나가버리게 된다..

![설치 초기 화면](/files/se0-1.png)

설치 초기 화면이다. 

```
Install Red Hat Enterprise Linux 7.6  
Test This media & Install Red Hat Enterprise Linux 7.6
Troubleshooting
```
세 개의 메뉴가 뜨는 걸 확인할 수 있다.

`Install Red Hat Enterprise Linux 7.6`은 말 그대로 RHEL 7.6을 설치하라는 의미 같은데,
`Test This media`는 무엇일까? (무엇을 Test 하는 것일까?)

답은 Checksum을 테스트하는 것이다. Checksum은 에러를 검출하는 방법 중 하나로, 검사 주체가 가지고 있는 파일에 대한 Checksum을 기준으로 검사 대상이 가지고 있는 파일에 대한 체크섬을 검사하게 되는데, 만일 이 Checksum이 서로 일치하지 않는다면 파일에 오류가 있다 (손상되었다) 고 판단하는 방법 중 하나이다. Checksum에 대한 보다 더 자세한 설명은 [이 링크](https://ko.wikipedia.org/wiki/%EC%B2%B4%ED%81%AC%EC%84%AC)를 참조하면 된다.

즉, `Test This media & Install Red Hat Enterprise Linux 7.6`를 누르게 되면 미디어 파일 안에 여러 설정파일들에 대한 체크섬을 검사한 두 설치를 하게 된다.

`Troubleshooting`은 서버에 문제가 생겼을 떄 cd를 통해 복구할 수 있는 방법인데, 추후에 자세히 다시 알아볼 예정이다.

우선은 `Test This media & Install Red Hat Enterprise Linux 7.6`로 media test를 돌린 후에 설치해보도록 하자. 이를 누른 시점부터 anaconda가 설치를 도와준다. `Ctrl Alt F1`를 누르면 아나콘다가 설치하는 과정을 직접 볼 수도 있다.

![Checking 중이라고 하단에 표시된다](/files/se0-3.png)

미디어 테스트가 끝나면 다음과 같이 본격적으로 설치 준비를 시작한다

![Ctrl Alt F1](/files/se0-4.png)
![하단에 anaconda라고 표시된다](/files/se0-5.png)

그러면 아래와 같이 설치화면이 나온다. 이 곳 또한 anaconda installer의 도움을 받아 띄워진 GUI 형식의 설치화면이다. 

### anaconda CLI

이번에는 다른 방식으로 설치해보자.

아래 사진과 같이 처음 install 화면에서 `TAB`을 누르면 `vmlinuz initrd=initrd.img inst.stage2=hd.:LABEL=RHEL-7.6\x20Server.x86_64 quiet`라는 문구가 뜰텐데 그 뒤에 text 라고 치면 아까와는 다른 설치화면으로 이동하게 된다.

![Ctrl Alt F1](/files/se0-5.png)
![Ctrl Alt F1](/files/se0-6.png)

조금 더 기다리면 아래 사진과 같은 설치화면으로 이동하게 되는데, 

1. Language Settings
2. Time Settings
3. Installation Source
4. Software selection
5. Installation Destination
6. Kdump
7. Network Configuration
8. Root Password
9. User Creation

> 참고

[x] : 이미 설정한 항목
[!] : 아직 설정은 안했지만 꼭 해야하는 항목
[ ] : 선택 항목

자세히 알아보면 위의 GUI 설치과정에서 본 [Installation Summary 항목](https://kangtegong.github.io/2020/03/07/installation-summary/)과 동일한 항목을 단지 CLI 형식으로 설정하는 것임을 알 수 있다.


### Referece
[공식 사이트](https://fedoraproject.org/wiki/Anaconda)   
https://access.redhat.com/documentation/ko-kr/red_hat_enterprise_linux/6/html/installation_guide/s1-guimode-interface-x86   
https://anaconda-installer.readthedocs.io/en/latest/  
https://fedoraproject.org/wiki/Anaconda