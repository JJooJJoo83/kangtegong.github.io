---
layout: post
title: "시스템 기초(2) Memory-1"
image: ''
date: 2019-08-17 12:24:06
tags: 
- System
- Computer_Architecture
- Tutorials
description: 시스템 프로그래밍과 리버싱의 기본 소양 - Memory편
categories:
- System_Basic
---

## 시스템 프로그래밍의 기초(2) : 메모리

### 사전지식 

#### 고급 언어의 데이터형이 나타내는 두 가지

1. 메모리 영역을 **차지하는 크기** 
2. 해당영역에 보관되는 **데이터의 형식**

#### *n*-bit addresss 환경?

포인터 변수의 크기는 당연히 *n* bit
(실제 메모리에서는 1byte 단위로 읽고 읽고 쓴다)

### 하드웨어에서 시작되는 포인터의 개념

메모리의 실체는 그냥 IC칩이다.
전원, address신호, data신호, 제어신호들을 받고
그것들을 입출력하기 위한 핀들로 구성되어 있다.

아래 RAM의 도식도를 예로 들어보자.



[img]


VCC, GND를 power,
A0~A9를 address signal,
D0~D7를 data signal,
RD, WR는 각각 read와 write를 위한 signal이라고 가정하자.

일반적으로 VCC와 GND에 5V의 직류 전압을 가하는 것을 1로,
아무 전압을 가하지 않는 것을 0으로 간주한다.

### 메모리의 크기 

이 예제의 경우에는 data signal pin이 8개 있으므로 
한번에 8bit(=1byte) 개의 데이터가 저장 가능하고

address signal pin이 10개 있으므로 총 1024개의 address를 지정할 수 있다.

즉, 1byte 데이터를 1024개 저장할 수 있는 메모리 이므로
1024=1K, 즉, 1KB 메모리라는 점을 알 수 있다.

### 쓰기 신호
이 1KB의 가상 메모리에 1바이트 데이터를 쓰고 싶다면(저장하고 싶다면) 다음의 과정을 거친다.

1. VCC에는 +5V
GND에는 0V의 전압을 가해 전원을 동작시키고
2. 데이터가 들어갈 주소를 A0~A9에 address signal을 보낸다
3. 그리고 저장하고 싶은 데이터값을 D0~D7에 신호로 입력하고
4. "나는 쓰기 신호를 보낼 것임"을 WR신호를 1로 만듦으로서 알린다.


### 읽기 신호 

이 1KB의 가상 메모리에 1바이트 데이터를 읽고 싶다면 다음의 과정을 거친다.

1. 읽고싶은 데이터의 주소를 A0~A9에 address signal을 보낸다
2. "나는 여기 데이터를 읽을 것임"을 RD신호를 1로 만듦으로서 알린다.
3. 그럼 해당 번지의 데이터가 D0~D7 data signal을 통해 출력된다.

