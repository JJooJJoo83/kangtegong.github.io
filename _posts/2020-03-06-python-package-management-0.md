---
layout: post
title: '파이썬 의존성 패키지 관리 1 : pip'
author: minchul.kang
date: 2020-03-06 12:00
tags: [python-package-dependency]
---

> 파이썬의 패키지 관리를 타 언어/라이브러리와 비교하며 알아봅니다

패키지 의존성 관리는 중요하다. 요즈음 같이 새로운 패키지들이 무수히 생겨나고 업데이트 되는 상황에서는 비단 배포시 환경의 동기화만을 위해서만 필요한 건 아니다. 파이썬은 패키지들을 어떻게 관리할까?
그리고 파이썬과 비슷한 대패키지 시대를 맞이한 옆 동네에서는 어떻게 패키지를 관리하고 있을까?

- 쉽게 설치하고 많이 만들어지고 빨리 업데이트 되는 요즈음
- 프로젝트 하나 평균 패키지 몇 개씩 생긴다 (사진)

## 

## pip 

웹에 게시되어 있는 PyPI(Python Package Index) 아카이브에서 자동으로 패키지를 다운로드해서 설치해주는 pip 유틸

```
pip search 패키지: 패키지 검색
pip install 패키지==버전: 특정 버전의 패키지를 설치(예: pip install requests==2.9.0)
pip list 또는 pip freeze: 패키지 목록 출력
pip uninstall 패키지: 패키지 삭제
```

