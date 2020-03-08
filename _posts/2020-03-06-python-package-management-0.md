---
layout: post
title: '파이썬 의존성 패키지 관리[0] : pip'
author: minchul.kang
date: 2020-03-06 12:00
tags: [python-package-dependency]
---

> 파이썬의 패키지 관리를 타 언어/라이브러리와 비교하며 알아봅니다

패키지 의존성 관리는 중요하다. 요즈음 같이 새로운 패키지들이 무수히 생겨나고 업데이트 되는 상황에서는 비단 배포시 개발환경의 동기화만을 위해서만 필요한 건 아니게 되었다. 파이썬은 패키지들을 어떻게 관리할까?
그리고 파이썬과 비슷한 대패키지 시대를 맞이한 옆 동네에서는 어떻게 패키지를 관리하고 있을까?

![쉽게 다운로드받고, 많이 만들어지고, 빨리 업데이트되는 요즈음 패키지](/files/py-packages0-1.png)
*출처 https://pypistats.org/packages/__all__*

## pip 

많이들 알다시피 흔히 파이썬 패키지를 다운로드 받기 위해 pip를 이용한다. pip이란, 웹에 게시되어 있는 PyPI(Python Package Index) 아카이브에서 자동으로 패키지를 다운로드해서 설치해주는 유틸리티 중 하나이다.
`pip install 패키지이름` 명령어 하나만으로 해당 패키지뿐 아니라 의존성 있는 모듈이나 패키지까지 간편하게 설치할 수 있게 된, 고맙고도 강력한 도구라 하겠다.  `install` 명령 이외의 유용한 명령어들은 아래와 같다.

```
pip search 패키지          : 패키지 검색
pip install 패키지==버전   : 특정 버전의 패키지를 설치
pip list 또는 pip freeze   : 패키지 목록 출력
pip uninstall 패키지       : 패키지 삭제
```

## pip를 이용한 패키지 의존성 관리

상술했듯 pip를 이용해 간편하게 패키지를 설치했다면, 배포 등의 이유로 의존성 패키지를 관리해주기 위해서는 지금까지 해당 프로젝트에서 (어떤 버전대의) 무슨 패키지가 설치되었는지를 알아야 할 것이다.

의존 라이브러리를 한 번에 설치하는 이 작업은 으레 아래와 같은 순서로 이루어진다.

```
pip freeze > requirements.txt
```
명령어를 통해 텍스트 파일(requirements.txt)에 설치된 패키지 이름과 버전을 명시해 준 뒤,
의존성 패키지의 설치가 필요한 환경으로 가서

```
pip install -r requirements.txt

$ pip download $PATH -r requirements.txt
$PATH에는 다운로드할 경로를 지정하면 되고 딱히 지정하지 않으실 경우에는 현재 경로에 다운로드

```
명령어를 통해 텍스트 파일에 저장된 패키지 이름과 버전을 한 번에 pip로 설치해주는 방식으로 이루어졌다.

![requirements.txt 만드는 과정](/files/py-packages0-2.gif)

> 다시 말해, 지금까지의 파이썬 패키지 의존성 관리는 개발환경에서 패키지 리스트를 기록한 파일 (requirements.txt)을 만든 뒤, 클라이언트 환경에서 해당 파일 (requirements.txt)를 설치함으로서 이루어졌다.

## 옆동네는 패키지 관리를 어떻게 할까? : npm, yarn

pip와 같이 패키지 관리자임과 동시에 의존성 관리자역을 수행하고 있는 유틸리티로는 npm, yarn, cargo, brew 등이 있다. 이 포스트에서는 이들 중 npm과 yarn을 알아보기로 한다.

