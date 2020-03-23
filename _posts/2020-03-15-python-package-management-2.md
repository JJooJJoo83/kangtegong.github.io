---
layout: post
title: '2. 더 나은 python 의존성 관리를 위한 노력(1) : pipenv'
author: minchul.kang
date: 2020-03-15 12:00
tags: [python-package-dependency]
---

> pip의 (의존성 관리에 있어서의) 문제점을 해결하기 위한 pipenv와 한계를 알아봅니다.
 
## 들어가며

[기존 포스트](https://kangtegong.github.io/2020/03/11/python-package-management-1/)를 보고오신 독자분들은 아시겠지만, `pip freeze > requirements.txt`하나만으로, requirements.txt에게 모든 패키지 (의존성) 관리를 일임하는 데에는 아래와 같은 문제들이 있었다.

1. **개발에서 발생하는 문제** : 상황별 패키지 관리의 어려움
2. **유지보수에서 발생하는 의존성 관리 문제** : tracking의 어려움
3. **설치에서 발생하는 문제** : 가상 환경에 따른 설치 에러 문제

이러한 문제를 그 동안 필자만 의식하고 있었던 것은 분명 아니었기에, '보다 나은 패키지 관리자'에 대한 열망으로 세계 각지에서 대안을 내놓기 시작했다.

이번 포스트에서는 그 노력들의 일환인, `pipenv`에 대해 설명하도록 하겠다.

## pipenv 소개 

<img src="https://pipenv.pypa.io/en/latest/_static/pipenv.png" alt="pipenv" width="70%">

[`pipenv`](https://pipenv.pypa.io/en/latest/)는 파이썬 기반 프로젝트를 위한 가상환경을 자동으로 생성해주면서, 동시에 패키지를 프로젝트 단위로 관리할 수 있게 해주는 패키지 관리자이다.

설치 명령어는 아래와 같다.

```
$ pip install pipenv
```

pipenv가 프로젝트를 자체 가상환경으로 관리해주기 때문에, 더 이상 pip와 가상환경을 따로 분리해 둘 필요가 없게 되었다. 
또한, pipenv는 npm의 package.json 이나 package-lock.json과 같은 패키지 의존성 관리 파일 Pipfile, Pipfile.lock을 자동 생성함으로써 수동으로 requirements.txt를 작성하는 수고로움도 덜 수 있게 해 주었다. 

이 밖에도 dependency graph와 같은 개발자 편의를 위한 기능들도 다수 제공한다.

## pipenv 기본 사용

그럼 pipenv를 직접 한 번 사용해보자. (참고로 굳이 설치하지 않고도 [이 곳](https://rootnroll.com/d/pipenv/
)에서 브라우저 상으로 실습해볼 수도 있다.) pipenv를 설치했다면, 프로젝트 폴더를 하나 만들어주고(필자는 test라고 이름을 지었다), 그 안에서 `pipenv install`명령어를 통해 이 폴더 안에서 가상환경과 패키지 관리를 시작해 보도록 하자.

```
$ mkdir test
$ cd test
$ pipenv install
```
그러면 프로젝트 폴더 안에 가상환경을 생성하였다는 문구와 함께 Pipfile, Pipfile.lock 이 생성된 걸 확인할 수 있다. 

이제 이 Pipfiles(Pipfile,Pipfile.lock)만 있다면, `pipenv install` 명령어 하나만으로 (새롭게 가동되는 가상환경과 함께) 프로젝트에 종속되어있는 의존성 패키지들을 한 번에 설치할 수 있게 된다. 

Pipfile 은 설치된 패키지들의 패키지 및 버전들을 기억하는 파일이다. 파일의 내용은 아래와 같다.

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
*개발 전용 패키지, (배포용)패키지와 기본적인 프로젝트 (버전 상의)요구사항이 명시되어 있다*

Pipfile.lock은 Pipfile이 업데이트된 시점의 의존성 정보를 기억하여, Pipfile 파일이 작성된 시점의 의존성 트리가 다시 생성될 수 있도록 보장한다.
패키지를 다운로드, 업데이트 할 때마다 자동으로 업데이트 되는 Pipfile과는 다르게 Pipfile.lock은 `pipenv lock`을 통해 사용자가 직접 업데이트 해 주어야 한다. Pipfile.lock의 내부 구성은 아래와 같다. 

> 자세히 보면 Pipfile이 업데이트 된 시점의 의존성 정보의 고유성을 유지하기 위해 해시값 (sha256) 을 이용한다는 걸 확인할 수 있다.

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

파일 내용만 봐서는 아직까지는 크게 와닿지 않을 수 있다. 그럼 Pipfile과 Pipfile.lock이 어떻게 패키지를 관리하는지 직접 눈으로 확인해보기 위해 django를 설치해보자.

> `pip install django`가 아닌 `pipenv install django`임에 유의하자

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

위와 같이 django를 설치하고 난 뒤, Pipfile을 열어보자.

```
$ cat Pipfile
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

django가 [packages] 항목에 잘 설치되었음을 확인할 수 있다.

이러한 정보를 `Pipfile.lock`에도 반영해주기 위해서 `pipenv lock` 명령어를 통해 Pipfile.lock에 반영해보자. 그 결과는 아래와 같다.

```
$ pipenv lock
Locking [dev-packages] dependencies…
Locking [packages] dependencies…
Success!
Updated Pipfile.lock (4f9dd2)!  
```

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

default에 django를 비롯한 의존성 패키지들의 정보가, 
`pipenv lock` 명령어를 친 시점 기준으로 반영되었다는 걸 확인할 수 있다.

## 개발환경 이원화 관리 (배포용, 개발용 나누어 관리)

위 Pipfile.lock 파일의 내용을 보면 하단에 develop이라는 항목이 있다는 걸 확인할 수 있다.
이미 예상하는 분들도 계실 수 있겠지만 pipenv는 배포용 패키지와 개발용 패키지를 이원화하여 관리할 수 있다.
(더 이상 `requirements.txt`와 `requirements-dev.txt` 로 나누어 관리할 필요가 없게 되었다!) 

개발용(테스트용) 패키지를 설치하기 위해서는 `--dev` 인자로 설치해주면 된다.
예를 들어, 개발 시 테스트용으로 pytest를 설치하고자 하는데, 배포 단계에서는 pytest를 포함하고 싶지 않은 상황을 가정해보자. 아래와 같이 설치를 진행하면 pytest가 개발용 패키지로 설치된다. 때문에 배표용 패키지와는 별개로 작업할 수 있어 조금 더 자유로운 개발이 가능해진다.

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

> 참고로 Pipfile.lock의 develop 항목에도 잘 반영이 된다.

## 기타 편의 기능 (의존성 그래프)

  `pipenv graph` 명령어를 통해 패키지 의존관계를 좀 더 가시적으로 확인할 수 있다.
  아래를 보면, pytest==5.4.1에는 어떤 패키지들이 의존적인지, Django==3.0.4에는 어떤 패키지들이 의존적인지, 그리고 그 의존성 패키지들에게 요구되는 버전(required)과 실제 설치된 버전(installed)은 무엇인지 한 눈에 확인할 수 있다.

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

## 기존 pip의 문제 해결

  분명 pipenv는 기존 pip의 의존성 관리에 있어서의 가려운 부분을 긁어주었다. 구체적으로 어떤 개선사항이 있었는지 알아보자.

### 1. 개발용, 배포용 패키지를 나누어 관리할 수 있다.
  
  큰 의의 중 하나이다. pipenv를 이용한다면 배포시에는 `$ pipenv install` 명령어로 배포용 패키지만 설치할 수 있고, 개발환경 세팅을 위해서는 `$ pipenv install --dev` 명령어를 통해 개발용 패키지들 포함한 모든 패키지들을 설치할 수 있다.

### 2. `requirements.txt`보다 훨씬 정갈하고 합리적으로 의존성 패키지들을 관리할 수 있다. (package tracking 용이)
  
  의존성 그래프를 통해 확인할 수 있었던 대목이다. 기존 requirements.txt 에서는 모든 패키지들의 이름과 버전을 단순 나열했기 때문에 어떤 패키지가 어떤 패키지에 의존적인지 알기 어려웠지만, `pipenv graph`등의 명령어를 통해 이제는 좀 더 명확하게 패키지들간의 의존성 관계를 확인해볼 수 있었다.

### 3. 더 이상 가상환경과 패키지관리자를 독립적으로 사용할 필요가 없다.
  
  개인적으로 pipenv에게 가장 감사한 부분이다. 통일되지 않은 수 많은 python 가상환경을 혼용하면서 사용할 때 발생하는 문제 (개발환경의 종류에 따른 설치 오류)를 사전에 방지할 수 있다.

## 그러나...

그러나, 최근 pipenv는 크고 작은 비판에 맞닥뜨리게 되었다.[이 링크](https://chriswarrick.com/blog/2018/07/17/pipenv-promises-a-lot-delivers-very-little/#id15)에서 (pipenv 유저들의 마음을 대변하는) 제법 신랄한 비판을 볼 수 있는데, 요약하자면 다음과 같다.

### 1. 늦은 업데이트

maintain이 되는지조차 의심스러울 만큼 업데이트가 느리다. 2018년 12월 업데이트가 있었고, 그 이후 2019년에는 새 버전이 릴리즈 되지도 않았다. (위 링크에서는 'pipenv is dead' 라는 표현까지 사용했다..)

### 2. 느리다

위 글의 예시에서 `pipenv install django` 명령어를 통해 django와 그에 따른 의존성 패키지, Pipfile과 Pipfile.lock이 설치되는 모습을 보여주었는데, 실은 그 과정이 30~40초가량이나 소요되었다. `pip install django`를 통해 django를 설치하면 10~20초 이내로 패키지 설치가 끝나는 점을 고려한다면 분명 단점이라면 단점이다.

실제 pipenv와 pip 등으로 설치할 경우 속도 차이가 얼마나 나는지는 추후 코드를 통해 시연을 보여드리도록 하겠다.

### 3. 부가 기능(script)의 performance

위 pipenv 소개 글에서 적지 않은 pipenv 기능이 있다면, `pipenv run`이나 `pipenv shell` 명령어를 통해 수행되는 스크립트 실행 기능을 꼽을 수 있을 것이다. 왜 안적었느냐고 여쭈어보신다면 ~~구려서~~ 전혀 편치 않은 '편의'기능이라 적지 않은 것이다. 이에 대한 자세한 내용은 [이 링크](https://chriswarrick.com/blog/2018/07/17/pipenv-promises-a-lot-delivers-very-little/#running-scripts-badly)를 참조해보면 좋을 것이다.

### Next Post : 더 나은 의존성 관리를 위한 노력(2) : poetry

역시 pipenv 하나로는 *"그 뒤로 그들은 행복하게 살았답니다"*와 같은 결론을 내릴 수는 없었다.
결국 패키지 의존성 관리에 있어 새로운 후발 주자가 필요한 셈인데, 다행히도 생겨난지 얼마 안 된 젊은(?) 패키지 관리자가 있다. 바로 poetry이다. 다음 포스트에서는 이 poetry에 대해서 알아보도록 하겠다.  

### (참고) pipenv usage : $ pipenv --help

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
