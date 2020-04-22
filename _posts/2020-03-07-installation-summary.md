---
layout: post
title: 'installation summary'
author: minchul.kang
date: 2020-03-07 12:00
tags: [system-engineering]
---

> Installation Summary에 대해서 간단하게 알아봅니다

## 설치 메뉴 (Installation Summmary) 설명

> 언어 설정은 필요한 언어에 맞게 설정하면 되므로 설명 생략. 참고로 설정한 언어에 따라 오류 메세지도 출력이 된다. (e.g. 한국어 선택시 에러 메세지도 한국어로 출력)

우선 Installation Summary 항목을 보면 아래와 같이 구성되어 있음을 확인할 수 있다.

![설치화면](/files/se0-7.png)

```
Localization
    - Date & Time
    - KeyBoard
    - Language Support
Software
    - Installation Source 
    - Software Selection
System
    - Installation Destination
    - KDUMP
    - Network & Hostname
    - Security Policy
```

### Date & Time
![Date & Time](/files/se0-8.png)

세계 표준시 기준으로 몇 시간 떨어져 있는지를 기준으로 시간이 설정된다.
서울은 세계 표준시 기준 + 9시간으로 설정된다.

> 참고 : 세계 표준시, 한국 표준시  
UTC: 협정 세계시 또는 UTC는 1972년 1월 1일부터 시행된 국제 표준시이다. UTC는 국제원자시와 윤초 보정을 기반으로 표준화되었다.  
GMT: GMT는 그리니치 평균시(그리니치 표준시)로, 런던을 기점으로 하는 세계시의 기준시간대이다. 요즈음은 표준시로 GMT보다는 UTC를 사용한다.  
KST: KST는 한국 표준시로, 일본 표준시와 같은 동경 135도를 기준으로 하여 UTC보다 9시간 빠른 표준시이다.


### Keyboard Layout
![Keyboard Layout](/files/se0-9.png)


### Language Support
![Language Support](/files/se0-10.png)
어떤 언어를 사용할지를 선택하는 것

### Installation Source 
![Installation Source](/files/se0-11.png)
CD에 있는 미디어로 설치한다는 의미이다

### Software Selection
![Software Selection](/files/se0-12.png)
어떤 패키지들을 깔 것인가
모든 패키지들을 다 직접 설치하는 건 매우 번거로우니 상황별로 redhat이 그루핑 해 둔 것

### Installation Destination
![Installation Destination](/files/se0-13.png)
파티션

### KDUMP
![Language Support](/files/se0-14.png)

### Network & Hostname
![Network & Hostname](/files/se0-15.png)

### Security Policy