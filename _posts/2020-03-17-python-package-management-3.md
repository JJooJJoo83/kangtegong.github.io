---
layout: post
title: '3. 더 나은 python 의존성 관리를 위한 노력(2) : poetry'
author: minchul.kang
date: 2020-03-17 12:00
tags: [python-package-dependency]
---

> pip의 (의존성 관리에 있어서의) 문제점을 해결하기 위한 poetry를 알아봅니다.
 
## 들어가며

이전 포스트에서는 pip가 가지고 있는 (패키지 의존성 관리 측면에서의) 고질적 문제를 해결해주는 pipenv에 대해서 알아보았다. 똫한 pipenv에 모든 패키지관리를 일임하기 어려운 이유에 대해서도 알아보았다. 이번 포스트에서는 비교적 후발주자인 [poetry](https://python-poetry.org)에 대해 알아보도록 하겠다.

## 설치 및 기본 사용법

### 설치

poetry는 pipenv에 비해 공식 문서가 비교적 친절하게 나와있다. 기본 설치는 curl을 이용하여 아래와 같이 설치할 수 있다.
(`pip install poetry` 로도 설치할 수 있지만 아래 설치법을 더 권장한다.)

```
$ curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python
Retrieving Poetry metadata

# Welcome to Poetry!

This will download and install the latest version of Poetry,
a dependency and package manager for Python.

It will add the `poetry` command to Poetry's bin directory, located at:

%USERPROFILE%\.poetry\bin

This path will then be added to your `PATH` environment variable by
modifying the `HKEY_CURRENT_USER/Environment/PATH` registry key.

You can uninstall at any time by executing this script with the --uninstall option,
and these changes will be reverted.

Installing version: 1.0.5
  - Downloading poetry-1.0.5-win32.tar.gz (10.64MB)

Poetry (1.0.5) is installed now. Great!

To get started you need Poetry's bin directory (%USERPROFILE%\.poetry\bin) in your `PATH`
environment variable. Future applications will automatically have the
correct environment, but you may need to restart your current shell.
```

### 프로젝트 시작

poetry로 관리되는 프로젝트는 `poetry init` 이나 `poetry new 프로젝트_이름` 명령어로 시작할 수 있다.
`poetry init` 은 이미 존재하는 프로젝트에서 의존성 관리를 시작할 경우, `poetry new` 명령어는 새로운 프로젝트를 만들며 관리를 시작하고자 할 경우 사용하는 명령어이다. 두 경우 모두 poetry 자체적으로 가상환경을 생성해주고, 
pyproject.toml 를 생성해준다. 

### 패키지 추가/ 삭제

`poetry add 패키지이름` (삭제할 때는 `poetry remove 패키지이름` 하면 의존성 패키지까지 함께 삭제)

개발 전용으로 패키지를 추가하려면 `--dev` 옵션을 주면 된다.

`poetry add --dev PACKAGE_NAME`

### 개발환경 구현

pyproject.toml 에 있는 프로젝트 일괄 설치하기

`poetry install`

### 패키지 목록 확인

`poetry show --no-dev --tree`

### 프로젝트 실행

`poetry run python main.py`

## 새로운 의존성 패키지 관리자의 희망이 될 수 있는 이유

1. pipenv에 비해 lock파일을 생성하는 속도도 빠르며, 실 사용 및 배포환경에서 활용중입니다.
2. docker 친화적
3. 기존 legacy 존중
  poetry.lock파일로부터 requirements.txt생성
4. pipenv에 비해 dependency resolution이 더 뛰어나다
https://github.com/python-poetry/poetry#what-about-pipenv

## 그러나, 아직은 시기상조인 이유

버그, 에러 but 새로운 버전 계속해서 릴리즈 되는 중. 하지만 피드백 속도 하루 커밋 양, 릴리즈 노트 매우 빠르다.

## pythonista들이 나아가야 할 길

 1. requirements.txt 멈추기 (심지어는 pyconkr 공식 사이트도 requirements.txt 사용..)
 2. pip-tools 
 3. poetry 사용
 4. 지속적인 contribution