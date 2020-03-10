---
layout: post
title: '파이썬 의존성 패키지 관리[0] : pip'
author: minchul.kang
date: 2020-03-06 12:00
tags: [python-package-dependency]
---

> 파이썬의 패키지 관리를 타 언어/라이브러리와 비교하며 알아봅니다

> 읽기 전 잠깐 용어 정리 : 모듈 vs 라이브러리 vs 패키지

패키지 의존성 관리는 중요하다. 요즈음 같이 새로운 패키지들이 무수히 생겨나고 업데이트 되는 상황에서는 비단 배포시 개발환경의 동기화만을 위해서만 필요한 건 아니게 되었다. 파이썬은 패키지들을 어떻게 관리할까?
그리고 파이썬과 비슷한 대패키지 시대를 맞이한 옆 동네에서는 어떻게 패키지를 관리하고 있을까?

![쉽게 다운로드받고, 많이 만들어지고, 빨리 업데이트되는 요즈음 패키지](/files/py-packages0-1.png)
*출처 https://pypistats.org/packages/__all__*

## pip 

많이들 알다시피 흔히 파이썬 패키지를 다운로드 받기 위해 pip를 이용한다. pip이란, 웹에 게시되어 있는 PyPI(Python Package Index) 아카이브에서 자동으로 패키지를 다운로드해서 설치해주는 유틸리티 중 하나이다.
`pip install 패키지이름` 명령어 하나만으로 해당 패키지뿐 아니라 의존성 있는 모듈이나 패키지까지 간편하게 설치할 수 있게 된, 고맙고도 강력한 도구라 하겠다. `install` 명령 이외의 유용한 명령어들은 아래와 같다.

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
$ pip freeze > requirements.txt
```
명령어를 통해 텍스트 파일(requirements.txt)에 설치된 패키지 이름과 버전을 명시해 준 뒤,
의존성 패키지의 설치가 필요한 환경으로 가서

```
$ pip install -r requirements.txt

$ pip download $PATH -r requirements.txt
$PATH에는 다운로드할 경로를 지정하면 되고 딱히 지정하지 않으실 경우에는 현재 경로에 다운로드

```
명령어를 통해 텍스트 파일에 저장된 패키지 이름과 버전을 한 번에 pip로 설치해주는 방식으로 이루어졌다.

![requirements.txt 만드는 과정](/files/py-packages0-2.gif)

> 다시 말해, 지금까지의 파이썬 패키지 의존성 관리는 개발환경에서 패키지 리스트를 기록한 파일 (requirements.txt)을 만든 뒤, 클라이언트 환경에서 해당 파일 (requirements.txt)를 설치함으로서 이루어졌다.

## 옆동네는 패키지 관리를 어떻게 할까? : npm, yarn

pip와 같이 패키지 관리자임과 동시에 의존성 관리자역을 수행하고 있는 유틸리티로는 npm, yarn, cargo, brew 등이 있다. 이 포스트에서는 이들 중 npm과 yarn을 알아보기로 한다.

npm과 yarn은 둘 다 JavaScript의 패키지 관리자이다. pip와 마찬가지로 의존성 패키지(모듈)도 한꺼번에 설치 및 설치가 가능하다.

### npm

<img src="https://github.com/kangtegong/kangtegong.github.io/blob/master/files/py-packages0-3.png?raw=true" width="80%" alt="npm img">

npm은 이름부터가 Node Package Manager이기 때문에 Node.js를 설치하면 자동적으로 설치된다.
또한 pip와 마찬가지로 `npm install 패키지이름` 명령어를 통해 의존성 패키지까지 간편하게 설치할 수 있다.
npm 초기화를 위해 `npm init` 명령어를 입력하고 package name, version, description, license 등을 입력하면 입력한 정보에 맞는 [package.json](https://docs.npmjs.com/files/package.json) 파일이 생성된다.

```
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (_posts) test
version: (1.0.0)
description: test for posting
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
About to write to 현재 경로 :

{
  "name": "test",
  "version": "1.0.0",
  "description": "test for posting",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}


Is this OK? (yes) yes
```

> 이런 번거로운 입력이 싫다면 `npm init -y` 혹은 `npm init -f` 로 초기화해주면 된다.

이 때 생성되는 package.json이 프로젝트 정보와 의존성을 관리해준다.
그리고, 앞으로 `npm install`을 통해 특정 패키지를 설치 하게 되면 package.json을 통해 node_modules 트리가 형성된다. 

하지만, 여러가지 이유로 인한 package.json을 통한 동일 개발환경 구축에 실패하는 상황을 방지하기 위하여, 
[package-lock.json](https://docs.npmjs.com/files/package-lock.json.html) 파일이 자동으로 만들어진다. 의존성 업데이트와 같이 변화되는 버전 변경에 대해서도 동일한 node_module의 생성을 보장할 수 있게 된다.

### yarn

<img src="https://github.com/kangtegong/kangtegong.github.io/blob/master/files/py-packages0-4.png?raw=true" width="80%" alt="yarn img">


yarn은 npm의 성능과 보안을 개선하기 위해 facebook 개발자들이 만든 패키지 매니저이다.
yarn 또한 npm과 마찬가지로 package.json 을 통해 이름, 설치된 패키지의 버전, 라이선스 등을 명시한다. 
그리고 npm이 package-lock.json을 만든 동일한 이유로 yarn.lock을 생성한다.

그리고, yarn 명령어를 통해 package.json 내의 패키지들의 의존성을 관리하게 된다.
대표적인 명령어는 아래와 같다.

```
$ yarn install              // node_models 에 패키지 설치하기
$ yarn upgrade [package]    // 의존성 패키지를 package.json에 따라 업데이트/삭제
$ yarn check                // 의존 패키지들이 프로젝트에서 이 프로젝트에서 유효한지 체크
$ yarn outdated             // 업데이트가 필요한 패키지 출력
```

### Next Post : npm/yarn vs pip

pip의 의존성 관리에 있어서의 문제와 더 나은 의존성 관리를 위한 노력
