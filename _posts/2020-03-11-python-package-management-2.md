---
layout: post
title: '더 나은 python 의존성 관리를 위한 노력1, pipenv'
author: minchul.kang
date: 2020-03-11 12:00
tags: [python-package-dependency]
---

> pip의 (의존성 관리에 있어서의) 문제점을 해결하기 위한 pipenv와 한계를 알아봅니다.
 
## 들어가며

[기존 포스트](https://kangtegong.github.io/2020/03/11/python-package-management-1/)를 보고오신 독자분들은 아시겠지만, `pip freeze > requirements.txt`하나만으로, pip에게 패키지 (의존성) 관리를 일임하는 데에는 아래와 같은 문제들이 있었다.

1. 개발에서 발생하는 문제 : 상황별 패키지 관리의 어려움
2. 유지보수에서 발생하는 의존성 관리 문제 : tracking의 어려움
3. 설치에서 발생하는 문제 : 환경에 따른 설치 에러 문제

이러한 문제를 그 동안 필자만 의식하고 있었던 것은 분명 아니었기에, '보다 나은 패키지 관리자'에 대한 열망으로 세계 각지에서 대안을 내놓기 시작했다.

이번 포스트에서는 그 노력들의 일환인, `pipenv`에 대해 설명하도록 하겠다.

## pipenv 소개 

[`pipenv`](https://pipenv.pypa.io/en/latest/)는 프로젝트 자체를 위한 가상환경을 생성해주면서, 동시에 패키지를 프로젝트 단위로 관리할 수 있게 해주는 패키지 관리자이다.

설치는 아래 명령어로 설치할 수 있다.

```
$ pip install pipenv
```

pipenv를 이용하여 프로젝트를 개발하면 자동으로 가상환경이 설정이 되면서, 해당 가상환경에서 설치되는 패키지들을 관리하게 된다 (더 이상 pip와 가상환경을 따로 분리해 둘 필요가 없다!)

또한, 패키지 의존성 관리를 위해 `Pipfile`, `Pipfile.lock` 을 자동 생성하여 패키지들의 의존성을 관리해주고, dependency graph와 같은 개발자 편의를 위한 기능들도 다수 제공한다.

## pipenv 기본 사용

그럼 pipenv를 한 번 사용해보자. pipenv를 설치했다면, 프로젝트 폴더를 하나 만들어주고(필자는 test라고 이름을 지었다),
그 안에서 `pipenv install`명령어를 통해 이 폴더 안에서 가상환경과 패키지 관리를 시작해 보도록 하자.

```
$ mkdir test
$ cd test
$ pipenv install
```
그러면 프로젝트 폴더 안에서 가상환경을 생성한다는 문구와 함께 Pipfile, Pipfile.lock 이 생성되었다는 걸 확인할 수 있다. 만일 프로젝트를 git에 올릴 적에 이 Pipfiles(Pipfile,Pipfile.lock)를 함께 올린다면,
git을 clone한 뒤 `pipenv install` 명령어 하나만으로 (새롭게 가동되는 가상환경과 함께) 프로젝트에 달린 의존성 패키지들을 설치할 수 있게 된다. 그럼 Pipfile과 Pipfile.lock이 어떻게 의존성관리를 진행하는지 한 번 보도록 하자.

Pipfile
> 설치된 패키지들의 패키지 및 버전들을 기억한다.

```
$ cat Pipfile
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]

[requires]
python_version = "3.7"
```

Pipfile.lock 

> 이 파일은 Pipfile이 업데이트된 시점의 의존성 정보를 기억하여, Pipfile 파일이 작성된 시점의 의존성 트리가 다시 생성될 수 있도록 보장한다.

```
$ cat Pipfile.lock
{
    "_meta": {
        "hash": {
            "sha256": "7e7ef69da7248742e869378f8421880cf8f0017f96d94d086813baa518a65489"
        },
        "pipfile-spec": 6,
        "requires": {
            "python_version": "3.7"
        },
        "sources": [
            {
                "name": "pypi",
                "url": "https://pypi.org/simple",
                "verify_ssl": true
            }
        ]
    },
    "default": {},
    "develop": {}
}
```

아직까지는 코드만 봐서는 크게 와닿지 않을 수 있는데, 그럼 Pipfile과 Pipfile.lock이 어떻게 패키지를 관리하는 지 알아보기 위해 django를 설치해보자.

```
$ pipenv install django
Installing django…  
Adding django to Pipfile's [packages]…
Installation Succeeded
Pipfile.lock (4f9dd2) out of date, updating to (a65489)…
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Success!
Updated Pipfile.lock (4f9dd2)!
Installing dependencies from Pipfile.lock (4f9dd2)…
  ================================ 4/4 - 00:00:04
```

위와 같이 django를 설치하고 난 뒤, (`pip install django`가 아닌 `pipenv install django`임에 유의하자)
Pipfile을 열어보자.

> Pipfile

```
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]

[packages]
django = "*"

[requires]
python_version = "3.7"
```

Pipfile에 요구되는 패키지 정보와 파이썬 정보가 담겨있다는 걸 확인할 수 있다.


이러한 정보를 `Pipfile.lock`에도 반영해주기 위해 `pipenv lock` 명령어를 통해 Pipfile.lock을 업데이트하고 나면 Pipfile.lock은 아래와 같이 업데이트된다는 걸 알 수 있다.

```
$ pipenv lock
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Success!
Updated Pipfile.lock (4f9dd2)!  
```

> Pipfile.lock

```
$ cat Pipfile.lock 
{
    "_meta": {
        "hash": {
            "sha256": "627ef89f247ecee27e9ef0dabe116108d09c47abf171c900a8817befa64f9dd2"
        },
        "pipfile-spec": 6,
        "requires": {
            "python_version": "3.7"
        },
        "sources": [
            {
                "name": "pypi",
                "url": "https://pypi.org/simple",
                "verify_ssl": true
            }
        ]
    },
    "default": {
        "asgiref": {
            "hashes": [
                "sha256:3e4192eaec0758b99722f0b0666d5fbfaa713054d92e8de5b58ba84ec5ce696f",
                "sha256:c8f49dd3b42edcc51d09dd2eea8a92b3cfc987ff7e6486be734b4d0cbfd5d315"
            ],
            "version": "==3.2.5"
        },
        "django": {
            "hashes": [
                "sha256:50b781f6cbeb98f673aa76ed8e572a019a45e52bdd4ad09001072dfd91ab07c8",
                "sha256:89e451bfbb815280b137e33e454ddd56481fdaa6334054e6e031041ee1eda360"
            ],
            "index": "pypi",
            "version": "==3.0.4"
        },
        "pytz": {
            "hashes": [
                "sha256:1c557d7d0e871de1f5ccd5833f60fb2550652da6be2693c1e02300743d21500d",
                "sha256:b02c06db6cf09c12dd25137e563b31700d3b80fcc4ad23abb7a315f2789819be"
            ],
            "version": "==2019.3"
        },
        "sqlparse": {
            "hashes": [
                "sha256:022fb9c87b524d1f7862b3037e541f68597a730a8843245c349fc93e1643dc4e",
                "sha256:e162203737712307dfe78860cc56c8da8a852ab2ee33750e33aeadf38d12c548"
            ],
            "version": "==0.3.1"
        }
    },
    "develop": {}
}
```

default 부분에 django를 비롯한 의존성 패키지들의 정보가, 
`pipenv lock` 명령어를 친 시점 기준으로 반영되었다는 걸 확인할 수 있다.

## 개발환경 이원화 관리 (배포용, 개발용 나누어 관리)

위 Pipfile.lock 파일의 내용을 보면 아래에 develop이라는 항목이 있다는 걸 확인할 수 있다.
이미 예상하는 분들도 계실 수 있겠지만 pipenv는 배포용 패키지와 개발용 패키지를 나누어 관리할 수 있다.
(`requirements.txt`와 `requirements-dev.txt` 로 나누어 관리할 필요가 없게 되었다!) 

개발용(테스트용) 패키지를 설치하기 위해서는 `--dev` 인자로 설치해주면 된다.
예를 들어, 개발 시 테스트용으로 pytest를 설치하고자 하고, 배포용에는 포함하고 싶지 않다고 가정해보자.
아래와 같이 설치를 하게 되면 개발용 패키지로 설치되게 된다.

```
$ pipenv install --dev pytest
Installing pytest…
Adding pytest to Pipfile's [dev-packages]…
Installation Succeeded
Pipfile.lock (deb801) out of date, updating to (4f9dd2)…
Locking [dev-packages] dependencies…
Success!
Locking [packages] dependencies…
Success!
Updated Pipfile.lock (deb801)!
Installing dependencies from Pipfile.lock (deb801)…
  ================================ 17/17 - 00:00:11
```

> 참고로 Pipfile.lock의 develop 항목에도 잘 반영이 되었다.

### 기타 편의 기능 (의존성 그래프)

## 기존의 문제 해결

1. 개발용, 배포용 패키지를 나누어 관리할 수 있다.
  큰 의의 중 하나이다. pipenv를 이용한다면 배포시에는 `$ pipenv install` 명령어로 배포용 패키지만 설치할 수 있고, 개발환경 세팅을 위해서는 `$ pipenv install --dev` 명령어를 통해 개발용 패키지들 포함한 모든 패키지들을 설치할 수 있게 되었다.

2. `requirements.txt`보다 훨씬 정갈하고 합리적으로 의존성 패키지들을 관리할 수 있다. (package tracking 용이)

    ```
    $ pipenv graph
    Django==3.0.4
    - asgiref [required: ~=3.2, installed: 3.2.5]
    - pytz [required: Any, installed: 2019.3]
    - sqlparse [required: >=0.2.2, installed: 0.3.1]
    pytest==5.4.1
    - atomicwrites [required: >=1.0, installed: 1.3.0]       
    - attrs [required: >=17.4.0, installed: 19.3.0]
    - colorama [required: Any, installed: 0.4.3]
    - importlib-metadata [required: >=0.12, installed: 1.5.0]
        - zipp [required: >=0.5, installed: 3.1.0]
    - more-itertools [required: >=4.0.0, installed: 8.2.0]
    - packaging [required: Any, installed: 20.3]
        - pyparsing [required: >=2.0.2, installed: 2.4.6]
        - six [required: Any, installed: 1.14.0]
    - pluggy [required: >=0.12,<1.0, installed: 0.13.1]
        - importlib-metadata [required: >=0.12, installed: 1.5.0]
        - zipp [required: >=0.5, installed: 3.1.0]
    - py [required: >=1.5.0, installed: 1.8.1]
    - wcwidth [required: Any, installed: 0.1.8]
    ```

3. 더 이상 가상환경과 패키지관리자를 독립적으로 사용할 필요가 없다.
  개인적으로 pipenv에게 가장 감사한 부분이다. virtualenv (혹은 venv) 를 혼용하면서 사용할 때 발생하는 문제 (개발환경의 종류에 따른 설치 오류)를 사전에 방지할 수 있다.

## 그러나...

그러나, pipenv는 크고 작은 비판에 맞닥뜨리게 되었다.[이 링크](https://chriswarrick.com/blog/2018/07/17/pipenv-promises-a-lot-delivers-very-little/#id15)에서 (pipenv 유저들의 마음을 대변하는) 제법 신랄한 비판을 볼 수 있는데, 요약하자면 다음과 같다.

1. 늦은 업데이트
2. 느리다
3. 에러

### Next Post : 더 나은 의존성 관리를 위한 노력2, poetry

역시 pipenv 하나로는 "그 뒤로 그들은 행복하게 살았답니다"와 같은 결론을 내릴 수는 없었다.
결국 패키지 의존성 관리에 있어 새로운 후발 주자가 필요한 셈인데, 다행히도 생겨난지 얼마 안 된 패키지 관리자가 있다. 바로 poetry라는 녀석이다. 다음 포스트에서는 이 poetry에 대해서 알아보도록 하겠다.  

### 참고 1: $ pipenv --help

```
$ pipenv --help
Usage: pipenv [OPTIONS] COMMAND [ARGS]...

Options:
  --where             Output project home information.
  --venv              Output virtualenv information.
  --py                Output Python interpreter information.
  --envs              Output Environment Variable options.
  --rm                Remove the virtualenv.
  --bare              Minimal output.
  --completion        Output completion (to be eval'd).
  --man               Display manpage.
  --support           Output diagnostic information for use in GitHub issues.
  --site-packages     Enable site-packages for the virtualenv.  [env var:
                      PIPENV_SITE_PACKAGES]
  --python TEXT       Specify which version of Python virtualenv should use.
  --three / --two     Use Python 3/2 when creating virtualenv.
  --clear             Clears caches (pipenv, pip, and pip-tools).  [env var:
                      PIPENV_CLEAR]
  -v, --verbose       Verbose mode.
  --pypi-mirror TEXT  Specify a PyPI mirror.
  --version           Show the version and exit.
  -h, --help          Show this message and exit.


Usage Examples:
   Create a new project using Python 3.7, specifically:
   $ pipenv --python 3.7

   Remove project virtualenv (inferred from current directory):
   $ pipenv --rm

   Install all dependencies for a project (including dev):
   $ pipenv install --dev

   Create a lockfile containing pre-releases:
   $ pipenv lock --pre

   Show a graph of your installed dependencies:
   $ pipenv graph

   Check your installed dependencies for security vulnerabilities:
   $ pipenv check

   Install a local setup.py into your virtual environment/Pipfile:
   $ pipenv install -e .

   Use a lower-level pip command:
   $ pipenv run pip freeze

Commands:
  check      Checks for security vulnerabilities and against PEP 508 markers
             provided in Pipfile.
  clean      Uninstalls all packages not specified in Pipfile.lock.
  graph      Displays currently-installed dependency graph information.
  install    Installs provided packages and adds them to Pipfile, or (if no
             packages are given), installs all packages from Pipfile.
  lock       Generates Pipfile.lock.
  open       View a given module in your editor.
  run        Spawns a command installed into the virtualenv.
  shell      Spawns a shell within the virtualenv.
  sync       Installs all packages specified in Pipfile.lock.
  uninstall  Un-installs a provided package and removes it from Pipfile.
  update     Runs lock, then sync.
```

### 참고2 : pipenv 브라우저에서 사용해보기

https://rootnroll.com/d/pipenv/