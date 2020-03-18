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

상세한 사용법은 poetry documentation에 너무나 잘 나와 있으므로 [공식 documenation](https://python-poetry.org/docs/)을 참고하길 추천한다.

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

```
$ poetry init

This command will guide you through creating your pyproject.toml config.

Package name [poetry_test]:  poetry_test
Version [0.1.0]:
Description []:  project for poetry testing
Author [Minchul Kang <tegongkang@gmail.com>, n to skip]:
License []:
Compatible Python versions [^3.7]:

Would you like to define your main dependencies interactively? (yes/no) [yes]
You can specify a package in the following forms:
  - A single name (requests)
  - A name and a constraint (requests ^2.23.0)
  - A git url (git+https://github.com/python-poetry/poetry.git)
  - A git url with a revision (git+https://github.com/python-poetry/poetry.git#develop)
  - A file path (../my-package/my-package.whl)
  - A directory (../my-package/)
  - An url (https://example.com/packages/my-package-0.1.0.tar.gz)

Search for package to add (or leave blank to continue):

Would you like to define your development dependencies interactively? (yes/no) [yes]
Search for package to add (or leave blank to continue):

Generated file

[tool.poetry]
name = "poetry_test"
version = "0.1.0"
description = "project for poetry testing"
authors = ["Minchul Kang <tegongkang@gmail.com>"]

[tool.poetry.dependencies]
python = "^3.7"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"


Do you confirm generation? (yes/no) [yes]

```

npm 처럼 시작할 때 이것저것 물어본다


자동 생성된 pyproject.toml 보기

```
[tool.poetry]
name = "poetry_test"
version = "0.1.0"
description = "project for poetry testing"
authors = ["Minchul Kang <tegongkang@gmail.com>"]

[tool.poetry.dependencies]
python = "^3.7"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"

```

### 패키지 추가/ 삭제

`poetry add 패키지이름` (삭제할 때는 `poetry remove 패키지이름` 하면 의존성 패키지까지 함께 삭제)

```
$ poetry add django
Creating virtualenv poetry-test-o5ewtmFM-py3.7 in C:\Users\minchul\AppData\Local\pypoetry\Cache\virtualenvs
Using version ^3.0.4 for django

Updating dependencies
Resolving dependencies... (5.2s)

Writing lock file


Package operations: 4 installs, 0 updates, 0 removals

  - Installing asgiref (3.2.5)
  - Installing pytz (2019.3)
  - Installing sqlparse (0.3.1)
  - Installing django (3.0.4)

```

lock 파일 (poetry.lock)이 자동 생성되었음을 알 수 있다.

```
$[[package]]
category = "main"
description = "ASGI specs, helper code, and adapters"
name = "asgiref"
optional = false
python-versions = ">=3.5"
version = "3.2.5"

[package.extras]
tests = ["pytest (>=4.3.0,<4.4.0)", "pytest-asyncio (>=0.10.0,<0.11.0)"]

[[package]]
category = "main"
description = "A high-level Python Web framework that encourages rapid development and clean, pragmatic design."
name = "django"
optional = false
python-versions = ">=3.6"
version = "3.0.4"

[package.dependencies]
asgiref = ">=3.2,<4.0"
pytz = "*"
sqlparse = ">=0.2.2"

[package.extras]
argon2 = ["argon2-cffi (>=16.1.0)"]
bcrypt = ["bcrypt"]

[[package]]
category = "main"
description = "World timezone definitions, modern and historical"
name = "pytz"
optional = false
python-versions = "*"
version = "2019.3"

[[package]]
category = "main"
description = "Non-validating SQL parser"
name = "sqlparse"
optional = false
python-versions = ">=2.7, !=3.0.*, !=3.1.*, !=3.2.*, !=3.3.*"
version = "0.3.1"

[metadata]
content-hash = "5be8a614a2430ad00f83b7a61787e23c452003b8e6a766a27fe6df020d487725"
python-versions = "^3.7"

[metadata.files]
asgiref = [
    {file = "asgiref-3.2.5-py2.py3-none-any.whl", hash = "sha256:3e4192eaec0758b99722f0b0666d5fbfaa713054d92e8de5b58ba84ec5ce696f"},
    {file = "asgiref-3.2.5.tar.gz", hash = "sha256:c8f49dd3b42edcc51d09dd2eea8a92b3cfc987ff7e6486be734b4d0cbfd5d315"},
]
django = [
    {file = "Django-3.0.4-py3-none-any.whl", hash = "sha256:89e451bfbb815280b137e33e454ddd56481fdaa6334054e6e031041ee1eda360"},
    {file = "Django-3.0.4.tar.gz", hash = "sha256:50b781f6cbeb98f673aa76ed8e572a019a45e52bdd4ad09001072dfd91ab07c8"},
]
pytz = [
    {file = "pytz-2019.3-py2.py3-none-any.whl", hash = "sha256:1c557d7d0e871de1f5ccd5833f60fb2550652da6be2693c1e02300743d21500d"},
    {file = "pytz-2019.3.tar.gz", hash = "sha256:b02c06db6cf09c12dd25137e563b31700d3b80fcc4ad23abb7a315f2789819be"},
]
sqlparse = [
    {file = "sqlparse-0.3.1-py2.py3-none-any.whl", hash = "sha256:022fb9c87b524d1f7862b3037e541f68597a730a8843245c349fc93e1643dc4e"},
    {file = "sqlparse-0.3.1.tar.gz", hash = "sha256:e162203737712307dfe78860cc56c8da8a852ab2ee33750e33aeadf38d12c548"},
] poetry add django
Creating virtualenv poetry-test-o5ewtmFM-py3.7 in C:\Users\minchul\AppData\Local\pypoetry\Cache\virtualenvs
Using version ^3.0.4 for django

Updating dependencies
Resolving dependencies... (5.2s)

Writing lock file


Package operations: 4 installs, 0 updates, 0 removals

  - Installing asgiref (3.2.5)
  - Installing pytz (2019.3)
  - Installing sqlparse (0.3.1)
  - Installing django (3.0.4)
```

그리고 django 설치 직후 pyproject.toml

```
[tool.poetry]
name = "poetry_test"
version = "0.1.0"
description = "project for poetry testing"
authors = ["Minchul Kang <tegongkang@gmail.com>"]

[tool.poetry.dependencies]
python = "^3.7"
django = "^3.0.4"

[tool.poetry.dev-dependencies]

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"

```

개발 전용으로 패키지를 추가하려면 `--dev` 옵션을 주면 된다.

```
$ poetry add --dev pytest
Using version ^5.4.1 for pytest

Updating dependencies
Resolving dependencies... (15.8s)

Writing lock file


Package operations: 13 installs, 0 updates, 0 removals

  - Installing zipp (3.1.0)
  - Installing importlib-metadata (1.5.0)
  - Installing pyparsing (2.4.6)
  - Installing six (1.14.0)
  - Installing atomicwrites (1.3.0)
  - Installing attrs (19.3.0)
  - Installing colorama (0.4.3)
  - Installing more-itertools (8.2.0)
  - Installing packaging (20.3)
  - Installing pluggy (0.13.1)
  - Installing py (1.8.1)
  - Installing wcwidth (0.1.8)
  - Installing pytest (5.4.1)

```
pyproject.toml

```
[tool.poetry]
name = "poetry_test"
version = "0.1.0"
description = "project for poetry testing"
authors = ["Minchul Kang <tegongkang@gmail.com>"]

[tool.poetry.dependencies]
python = "^3.7"
django = "^3.0.4"

[tool.poetry.dev-dependencies]
pytest = "^5.4.1"

[build-system]
requires = ["poetry>=0.12"]
build-backend = "poetry.masonry.api"

```

### 개발환경 구현

pyproject.toml가 있는 프로젝트를 내 개발환경에 일괄 설치하기

`poetry install`

### 패키지 목록 확인

`poetry show`

설치된 패키지 목록 출력. 버전과 이게 뭐 하는 패키지인지 간략하게 써 주는 센스..

```
$ poetry show
asgiref            3.2.5  ASGI specs, helper code, and adapters
atomicwrites       1.3.0  Atomic file writes.
attrs              19.3.0 Classes Without Boilerplate
colorama           0.4.3  Cross-platform colored terminal text.
django             3.0.4  A high-level Python Web framework that encourages rapid development and clean, pragmatic design.
importlib-metadata 1.5.0  Read metadata from Python packages
more-itertools     8.2.0  More routines for operating on iterables, beyond itertools
packaging          20.3   Core utilities for Python packages
pluggy             0.13.1 plugin and hook calling mechanisms for python
py                 1.8.1  library with cross-python path, ini-parsing, io, code, log facilities
pyparsing          2.4.6  Python parsing module
pytest             5.4.1  pytest: simple powerful testing with Python
pytz               2019.3 World timezone definitions, modern and historical
six                1.14.0 Python 2 and 3 compatibility utilities
sqlparse           0.3.1  Non-validating SQL parser
wcwidth            0.1.8  Measures number of Terminal column cells of wide-character codes
zipp               3.1.0  Backport of pathlib-compatible object wrapper for zip files

```

`poetry show --tree`

현재 설치된 (개발 전용 패키지까지 포함한) 모든 의존성 패키지 트리 형태로 출력하기

```
$ poetry show --tree
django 3.0.4 A high-level Python Web framework that encourages rapid development and clean, pragmatic design.
▒▒▒▒▒▒ asgiref >=3.2,<4.0
▒▒▒▒▒▒ pytz *
▒▒▒▒▒▒ sqlparse >=0.2.2
pytest 5.4.1 pytest: simple powerful testing with Python
▒▒▒▒▒▒ atomicwrites >=1.0
▒▒▒▒▒▒ attrs >=17.4.0
▒▒▒▒▒▒ colorama *
▒▒▒▒▒▒ importlib-metadata >=0.12
▒▒   ▒▒▒▒▒▒ zipp >=0.5
▒▒▒▒▒▒ more-itertools >=4.0.0
▒▒▒▒▒▒ packaging *
▒▒   ▒▒▒▒▒▒ pyparsing >=2.0.2
▒▒   ▒▒▒▒▒▒ six *
▒▒▒▒▒▒ pluggy >=0.12,<1.0
▒▒   ▒▒▒▒▒▒ importlib-metadata >=0.12
▒▒       ▒▒▒▒▒▒ zipp >=0.5
▒▒▒▒▒▒ py >=1.5.0
▒▒▒▒▒▒ wcwidth *

```

`poetry show --no-dev --tree`

개발 전용 패키지를 제외한 의존성 트리 그리기

```
django 3.0.4 A high-level Python Web framework that encourages rapid development and clean, pragmatic design.
▒▒▒▒▒▒ asgiref >=3.2,<4.0
▒▒▒▒▒▒ pytz *
▒▒▒▒▒▒ sqlparse >=0.2.2

```

### 프로젝트 실행

`poetry run python main.py`

## 새로운 의존성 패키지 관리자의 희망이 될 수 있는 이유

1. pipenv에 비해 lock파일을 생성하는 속도도 빠르며, 실 사용 및 배포환경에서 활용중입니다.
2. docker 친화적
3. 기존 legacy를 존중한다
  poetry.lock파일로부터 requirements.txt생성
4. pipenv에 비해 dependency resolution이 더 뛰어나다 [참고](https://github.com/python-poetry/poetry#what-about-pipenv
)

## 그러나, 아직은 시기상조인 이유

버그, 에러 but 새로운 버전 계속해서 릴리즈 되는 중. 하지만 피드백 속도 하루 커밋 양, 릴리즈 노트 매우 빠르다.

## pythonista들이 나아가야 할 길

 1. requirements.txt 멈추기 (심지어는 pyconkr 공식 사이트도 requirements.txt 사용..)
 2. pip-tools 
 3. poetry 사용
 4. 지속적인 contribution