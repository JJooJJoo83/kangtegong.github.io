---
layout: post
title: '1. pip 패키지 의존성 관리의 문제점'
author: minchul.kang
date: 2020-03-11 12:00
tags: [python-package-dependency]
---

> pip의 (의존성 관리에 있어서의) 문제점(개선점)을 알아봅니다.

> 참고 : pycharm 등 일부 IDE에서는 requirements를 인식하여 자동으로 필요할 패키지를 제안해주는 등 편의 기능을 제공해준다지만, 이 글에서는 "특정 IDE의 유용한 기능"이 아닌, 개발환경에 국한되지 않는 근본적인 해결/대안을 찾고자 합니다.
 
## 문제 인식

[이전 포스트](https://kangtegong.github.io/2020/03/06/python-package-management-0/)를 보고 오신 독자 분들은 아시겠지만, pip의 의존성 패키지 관리는 그다지 우아하지는 못하다. 필자는 python의 큰 팬인 만큼, '우아하지 못하다'는 표현 보다는 '개선의 여지가 있다' 정도로만 표현하겠다.

어쨌든, requirements.txt 로 모든 패키지를 때려넣는(?) 이런 방식에 어떤 문제가 있을까?
구체적으로 나열해보면 아래와 같다. 

1. 개발에서 발생하는 문제 : 상황별 패키지 관리의 어려움
2. **유지보수에서 발생하는 의존성 관리 문제 : package tree의 부재에서 오는 tracking의 어려움**
3. 설치에서 발생하는 문제 : 환경에 따른 설치 에러 문제

이제 이 문제들에 대해 알아보도록 하자. 그리고 필요하다면 이러한 문제 상황을 직접 재현해보도록 하자.

## ~~0. txt 확장자가 멋이 없다.~~

이건 필자의 동료 개발자가 보탠 의견이다. 공학적이지 못한 접근이지만 마음 속 깊은 곳에서 동의를 할 수 밖에 없어서 적었다.

![부울펴언](/files/py-packages1-1.png)

## 1. 상황별 패키지 관리의 어려움

이전 포스트의 npm과 yarn을 보면 알 수 있 듯, 관리하고자 하는 패키지는 그 활용 예에 따라 개발 전용 패키지와 배포(production) 전용 패키지 등으로 구분하는데 pip freeze를 통해 만드는 requirements.txt 는 그러한 구분이 없다.

참고로 (package.json 상에서 devDependencies로 표기되는) 개발 전용 패키지는 테스트 등의 이유로 개발 단계에서 사용할 패키지를 뜻하고, (package.json 상에서 Dependencies로 표기되는) production 전용 패키지는 실제 배포시 사용할 패키지, 실제 상품에서 사용할 패키지를 뜻한다.

물론, requirements.txt와 requirements-dev.txt 을 분리하여 사용할 수 있다곤 하지만, 이럴 경우 `pip freeze > requirements.txt`명령어는 쓸 수 없게 된다는 점, 따라서 패키지의 변경이 있을 때마다 매번 수동으로 requirements.txt를 업데이트 해야 한다는 점, 실수로 설치하거나 임시로 설치한 패키지들도 requirements.txt로 들어갈 수 있는 문제가 있다는 점에서 불편함이 따른다.

## 2. package tree의 부재에서 오는 tracking의 어려움 

개인적으로 필자는 이게 가장 큰 문제라고 생각한다. 텍스트 파일에 현 프로젝트의 모든 패키지들을 때려넣는 방식은 유저가 설치한 패키지의 의존성에 대한 정보까지 담지 못한다. 그래서 프로젝트 내 패키지 추가, 삭제와 같은 변동사항이 생길때, 수동으로 변동사항에 맞춰 텍스트 파일을 관리해줘야 하는 어려움이 있다. 이러한 어려움은 패키지의 업데이트가 빈번할수록, 프로젝트의 규모가 클수록, 섬세한 버전 관리가 필요한 버전에 예민한 프로젝트일수록 큰 문제를 야기할 수 있다.  

사견을 좀 보태자면, 패키지 관리는 최소한 "이 패키지가 어떤 패키지에서 파생된 패키지인지" 정도는 알 수 있게끔 tracking 이 가능한 형태로 관리되어야 한다고 생각한다. (e.g. tree 형태)

아래 예시를 보자. 아래는 가상환경에서 tensorflow를 설치한 뒤 pip freeze를 한 모습이다.

```
minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/test (master)
$ source myvenv/Scripts/activate
(myvenv)
minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/test (master)
$ pip install tensorflow
Collecting tensorflow
  Downloading https://files.pythonhosted.org/packages/34/d5/ce8c17971067c0184c9045112b755be5461d5ce5253ef65a367e1298d7c5/tensorflow-2.1.0-cp37-cp37m-win_amd64.whl (355.8MB)
Collecting keras-applications>=1.0.8 (from tensorflow)
  Using cached https://files.pythonhosted.org/packages/71/e3/19762fdfc62877ae9102edf6342d71b28fbfd9dea3d2f96a882ce099b03f/Keras_Applications-1.0.8-py3-none-any.whl
Collecting opt-einsum>=2.3.2 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/b2/49/2233e63052d5686c72131b579837ddfb98ba9dd0b92bb91efcb441ada8ce/opt_einsum-3.2.0-py3-none-any.whl (63kB)
Collecting protobuf>=3.8.0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/92/30/1b7ccde09bf0c535d11f18a574ed7d7572c729a8f754fd568b297be08b61/protobuf-3.11.3-cp37-cp37m-win_amd64.whl (1.0MB)
Collecting wrapt>=1.11.1 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/82/f7/e43cefbe88c5fd371f4cf0cf5eb3feccd07515af9fd6cf7dbf1d1793a797/wrapt-1.12.1.tar.gz
Collecting google-pasta>=0.1.6 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/c3/fd/1e86bc4837cc9a3a5faf3db9b1854aa04ad35b5f381f9648fbe81a6f94e4/google_pasta-0.1.8-py3-none-any.whl (57kB)
Collecting tensorboard<2.2.0,>=2.1.0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/d9/41/bbf49b61370e4f4d245d4c6051dfb6db80cec672605c91b1652ac8cc3d38/tensorboard-2.1.1-py3-none-any.whl (3.8MB)
Collecting keras-preprocessing>=1.1.0 (from tensorflow)
  Using cached https://files.pythonhosted.org/packages/28/6a/8c1f62c37212d9fc441a7e26736df51ce6f0e38455816445471f10da4f0a/Keras_Preprocessing-1.1.0-py2.py3-none-any.whl
Collecting tensorflow-estimator<2.2.0,>=2.1.0rc0 (from tensorflow)
  Downloading https://files.pythonhosted.org/packages/18/90/b77c328a1304437ab1310b463e533fa7689f4bfc41549593056d812fab8e/tensorflow_estimator-2.1.0-py2.py3-none-any.whl (448kB)

...

Collecting oauthlib>=3.0.0 (from requests-oauthlib>=0.7.0->google-auth-oauthlib<0.5,>=0.4.1->tensorboard<2.2.0,>=2.1.0->tensorflow)
  Using cached https://files.pythonhosted.org/packages/05/57/ce2e7a8fa7c0afb54a0581b14a65b56e62b5759dbc98e80627142b8a3704/oauthlib-3.1.0-py2.py3-none-any.whl
tensorboard 2.1.1 has requirement setuptools>=41.0.0, but you'll have setuptools 40.8.0 which is incompatible.
tensorflow-estimator, termcolor, gast, scipy, astor, tensorflow
  Running setup.py install for wrapt: started
    Running setup.py install for wrapt: finished with status 'done'
  Running setup.py install for absl-py: started
    Running setup.py install for absl-py: finished with status 'done'
  Running setup.py install for termcolor: started
    Running setup.py install for termcolor: finished with status 'done'
  Running setup.py install for gast: started
    Running setup.py install for gast: finished with status 'done'
Successfully installed absl-py-0.9.0 astor-0.8.1 cachetools-4.0.0 certifi-2019.11.28 chardet-3.0.4 gast-0.2.2 google-auth-1.11.2 google-auth-oauthlib-0.4.1 google-pasta-0.1.8 grpcio-1.27.2h5py-2.10.0 idna-2.9 keras-applications-1.0.8 keras-preprocessing-1.1.0 markdown-3.2.1 numpy-1.18.1 oauthlib-3.1.0 opt-einsum-3.2.0 protobuf-3.11.3 pyasn1-0.4.8 pyasn1-modules-0.2.8 requests-2.23.0 requests-oauthlib-1.3.0 rsa-4.0 scipy-1.4.1 six-1.14.0 tensorboard-2.1.1 tensorflow-2.1.0 tensorflow-estimator-2.1.0 termcolor-1.1.0 urllib3-1.25.8 werkzeug-1.0.0 wheel-0.34.2 wrapt-1.12.1
(myvenv)
minchul@DESKTOP-N87KQ5N MINGW64 ~/Desktop/test (master)
$ pip freeze
absl-py==0.9.0
astor==0.8.1
cachetools==4.0.0
certifi==2019.11.28
chardet==3.0.4
gast==0.2.2
google-auth==1.11.2
google-auth-oauthlib==0.4.1
google-pasta==0.1.8
grpcio==1.27.2
h5py==2.10.0
idna==2.9
Keras-Applications==1.0.8
Keras-Preprocessing==1.1.0
Markdown==3.2.1
numpy==1.18.1
oauthlib==3.1.0
opt-einsum==3.2.0
protobuf==3.11.3
pyasn1==0.4.8
pyasn1-modules==0.2.8
requests==2.23.0
requests-oauthlib==1.3.0
rsa==4.0
scipy==1.4.1
six==1.14.0
tensorboard==2.1.1
tensorflow==2.1.0
tensorflow-estimator==2.1.0
termcolor==1.1.0
urllib3==1.25.8
Werkzeug==1.0.0
wrapt==1.12.1

```

아래 패키지 목록들만 봐서는 어떤 것이 tensorflow와 함께 설치된 패키지인지, keras와 함께 설치된 패키지인지, django와 함께 설치된 패키지인지 알기 어렵다. 이 상황에서 앞으로 프로젝트를 진행하며 패키지들을 더 설치한다면, 패키지 의존성 파악은 매우 어려워질 겻이다.

## 3. 개발 환경에 따른 설치 에러 문제

다음은 `pip install -r requirements` 명령어에서 오는 문제이다.

여기까지 읽은 독자라면 이렇게도 반문할 수 있을 것 같다.

> 그냥 가상환경으로 나눠 담으면 되는 거 아닌가?

필자도 그렇게 생각을 했었고, 지금까지도 그렇게 개발을 해 왔지만, 여기서도 문제가 생긴다.
바로 IDE 에 종속적인 설치가 될 수 있다는 우려가 있다는 점이다.

conda environment에서 만든, 이를테면 requirements.yaml을 
venv로 만든 환경에서 `pip install` 하면 설치에 오작동이 생길 수 있다. 

실제 문제상황을 재연해보자.

![가상환경에 따른 설치오류](/232.gif)

conda로 가상환경(conda environment)을 만든 뒤,

```
conda create -n my_env python=3.6
activate my_env
```

이제 `conda install` 이 아닌 `pip install`을 해 보자.

```
pip install tensorflow
pip freeze
```

![안되거나 가상환경에 안담긴다](/232.gif)

위에서 보았듯, 가상환경이 패키지의존성문제의 근원적 해결책이 되지 못한다. 패키지 관리를 위해 가상환경(을 만드는 IDE)까지 통일해야 한다는 것은 배보다 배꼽이 더 큰 일이 되기 때문이다.

### Next Post : 더 나은 의존성 관리를 위한 노력1, pipenv

상술한 의존성 세 가지 문제를 해결하기 위한 노력들을 소개한다. text 파일 관리가 아닌, 총 두 세 가지의 솔루션을 제시할텐데, 상황별 장단점과 보완전 및 한계도 함께 알아보도록 하자.

