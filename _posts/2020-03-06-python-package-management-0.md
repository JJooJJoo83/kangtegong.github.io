---
layout: post
title: '0. pip와 옆동네 javascript 패키지 관리자, npm & yarn'
author: minchul.kang
date: 2020-03-06 12:00
tags: [python-package-dependency]
---

> 파이썬의 패키지 관리를 타 언어/라이브러리와 비교하며 알아봅니다

> 읽기 전 잠깐 용어 정리 : [모듈 vs 라이브러리 vs 패키지](https://kangtegong.github.io/2020/03/05/module-library-package/)

패키지 의존성 관리는 중요하다. 요즈음 같이 무수한 패키지들이 생겨나고, 쉽게 다운로드 받고, 빠르게 업데이트 되는 시대에서는 더더욱 중요하다. "일단 깔고 보자"는 마구잡이 식으로 패키지 관리를 소홀히 경우, 개발환경 재현이 어려워 "아까까지는 (내 컴퓨터에서) 됐는데.." 와 같은 대사를 하고 있는 자신을 발견하게 될 수 있기 떄문이다. 그렇다면 파이썬은 그 많고 많은 패키지들을 어떻게 관리할까? 그리고 파이썬과 비슷한 대패키지 시대를 맞이한 옆 동네 javascript에서는 어떻게 패키지를 관리하고 있을까?

![쉽게 다운로드받고, 많이 만들어지고, 빨리 업데이트되는 요즈음 패키지](/files/py-packages0-1.png)
*출처 https://pypistats.org/packages/__all__*

## pip 

많이들 알다시피 흔히 파이썬 패키지를 다운로드 받기 위해 pip를 이용한다. pip는, 웹에 게시되어 있는 PyPI(Python Package Index) 아카이브에서 자동으로 패키지를 다운로드해서 설치해주는 유틸리티 중 하나이다.
`pip install 패키지이름` 명령어 하나만으로 해당 패키지뿐 아니라 의존성 있는 모듈이나 패키지까지 간편하게 설치할 수 있게 된, 고맙고도 강력한 도구라 하겠다. `install` 명령 이외의 유용한 명령어들은 아래와 같다.

```
pip search 패키지          : 패키지 검색
pip install 패키지==버전   : 특정 버전의 패키지를 설치
pip list or pip freeze   : 패키지 목록 출력
pip uninstall 패키지       : 패키지 삭제
```

## pip를 이용한 패키지 의존성 관리

pip를 이용해 간편하게 패키지를 설치한 뒤에, 배포 등의 이유로 의존성 패키지를 관리해주기 위해서는 지금까지 내 프로젝트에서 (어떤 버전대의) 어떤 패키지들이 설치되었는지를 알아야 할 것이다.

내 개발환경에 설치된 (의존 라이브러리를 포함한) 모든 패키지들을 한 번에 기록하는 작업은 으레 아래와 같은 순서로 이루어진다.

1. 텍스트 파일(requirements.txt)에 설치된 패키지 이름과 버전을 몽땅 기록하기

  ```
  $ pip freeze > requirements.txt
  ```

  `pip freeze`는 해당 프로젝트에 설치된 모든 패키지들의 목록을 출력하는 명령어이다.  
  이 명령어의 결과를 requirements.txt 라는 이름의 텍스트 파일에 기록하라는 의미이다. (물론 텍스트 파일의 이름은 달라도 무방하다.)

2. requirements.txt 에 적힌 패키지 목록들 설치하기

  `pip freeze > requirements.txt` 명령어를 통해 패키지들의 목록과 버전을 requirements.txt에 적어주었다면, 다른 개발환경에서 requirements 파일 안에 있는 내용들을 아래 명령어를 통해 한 번에 설치해주었다.
  
  ```
  $ pip install -r requirements.txt
  ```

  혹은 

  ```
  $ pip download $PATH -r requirements.txt
  $PATH에는 다운로드할 경로를 지정하면 되고 딱히 지정하지 않는 경우에는 현재 경로에 다운로드
  ```

![requirements.txt 만드는 과정](/files/py-packages0-2.gif)

> 정리해보자면, 지금까지의 파이썬 패키지 의존성 관리는 개발환경 상에 설치된 모든 패키지들의 리스트를 기록한 파일 (requirements.txt)을 만든 뒤, 클라이언트 환경에서 해당 파일 (requirements.txt)를 설치함으로서 이루어졌다.

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

그리고, 다른 개발 환경에서도 `npm install`을 통해 특정 패키지를 설치 하게 되면 package.json을 통해 node_modules 트리가 형성된다. 

하지만, 여러가지 이유로 인한 package.json을 통한 동일 개발환경 구축에 실패하는 상황을 방지하기 위하여, 
[package-lock.json](https://docs.npmjs.com/files/package-lock.json.html) 파일이 자동으로 만들어진다. 이 package-lock 파일 덕분에 의존성 업데이트와 같은 버전 변경에 대해서도 동일한 node_module의 생성을 보장할 수 있게 되었다. 

또한, 개발 전용 패키지와 배포 전용 패키지를 나누어 관리할 수 있게 되어있는데, 이 덕분에 개발 단계에서 테스트용 패키지, 임시 설치 패키지, 실수로 설치한 패키지 등이 배포 단계에서 전혀 영향을 끼치지 않게 되어 자유로운 개발이 가능해졌다.

### yarn

<img src="https://github.com/kangtegong/kangtegong.github.io/blob/master/files/py-packages0-4.png?raw=true" width="80%" alt="yarn img">

yarn은 npm의 성능과 보안을 개선하기 위해 facebook 개발자들이 만든 패키지 매니저이다.
yarn 또한 npm과 마찬가지로 package.json 을 통해 이름, 설치된 패키지의 버전, 라이선스 등을 명시한다. 
그리고 npm이 package-lock.json을 만든 동일한 이유로 yarn.lock을 생성한다. yarn 또한 npm과 동일하게 개발 전용 패키지와 배포 전용 (production) 패키지를 구분하여 관리한다.

그리고, `yarn` 명령어를 통해 package.json 내의 패키지들의 의존성을 관리하게 된다.
대표적인 명령어는 아래와 같다.

```
$ yarn install              // node_models 에 패키지 설치하기
$ yarn upgrade [package]    // 의존성 패키지를 package.json에 따라 업데이트/삭제
$ yarn check                // 의존 패키지들이 프로젝트에서 이 프로젝트에서 유효한지 체크
$ yarn outdated             // 업데이트가 필요한 패키지 출력
```

### Next Post : pip 의존성 관리의 문제점>

여기까지 읽은 독자라면, 뭔가 pip의 의존성 관리 (requirements.txt) 방식이 npm이나 yarn에 비해 그다지 우아하지는 못하다는 생각이 어렴풋이 들 수도 있다. 다음 포스트에서는, 타 의존성 관리자(npm과 yarn)들을 통해 알 수 있는 pip의 패키지 의존성 관리 측면에서의 문제점 및 개선점을 명확하게 실험과 구현을 통해 알아보고, 더 나은 의존성 관리를 위한 노력들을 알아보도록 하겠다.