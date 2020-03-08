---
layout: post
title: 'System Engineering [0] : Linux Anaconda Installer'
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

그렇다면 Linux (RHEL 7.6) 설치 과정에서의 anaconda를 직접 눈으로 확인해보도록 하자.

## 설치 과정에서의 anaconda

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

그러면 아래와 같이 설치화면이 나온다. 이 곳 또한 anaconda installer의 도움을 받아 띄워진 GUI 형식의 설치화면이다. 이후 설치과정은 installation summary 창에서 진행하는데, 이는 다음 포스트에서 다루도록 한다.