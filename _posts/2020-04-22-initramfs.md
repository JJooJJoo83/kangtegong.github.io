---
layout: post
title: 'initramfs & kernel'
author: minchul.kang
date: 2020-04-22 12:00
tags: [system-engineering]
---


## What is initramfs (initial RAM file system)

Kernel은 사용자가 유저 프로그램을 조금 더 수월하게 이용할 수 있도록 하드웨어 자원을 적재적소에 할당해주거나, 시스템을 제어하는 역할을 한다. 

bootloader가 디스크에 있는 커널을 읽어들인 뒤, 디스크의 각종 드라이버들을 비롯한 설정파일을 읽어들일 때, 커널이 읽어들일 수 있는 모든 설정파일들이 당장 메모리에 올라올 수 없다. (만일 그렇게 되면 메모리를 너무 많이 차지할 것이다)

initramfs는 이런 상황에서, 지금 당장 필요한 스크립트, 드라이버, 설정파일들과 같은 kernel 초기 동작에 꼭 필요한 프로그램들을 kernel이 읽어들일 수 있게 하고, root filesystem이 mount 되게 돕는다.

initramfs의 목적은 root file system의 mount 이기 때문에, mount가 완료되어 kernel이 root filesystem을 읽어오게 되면 initramfs는 더이상 필요하지 않게 된다.

### initramfs가 망가졌다면? (부팅이 안될 경우 initramfs가 문제임을 추론하기)

커널은 올라오지만 부팅은 되지 않는다.

### bootloader가 망가졌다면? (부팅이 안될 경우 initramfs가 문제임을 추론하기)

boot entry조차 보이지 않는다


## What is Kernel

Kernel을 이해하기 위해서는 프로그램의 종류에는 OS 영역과 User Application 영역으로 나뉜다는 것을 이해해야 한다. User Application 영역은 말 그대로 컴퓨터의 사용자가 직접 사용하는 영역이고, OS 영역은 사용자가 이용하는 유저 프로그램이 필요로 하는 하드웨어 자원 (프린터, 화면, 마우스, 스피커.. )을 유저 프로그램에게 할당해주고, 프로세스를 관리해주는 등의 역할을 한다.유저 프로그램은 직접적으로 하드웨어 자원에 접근하는 것이 불가능하기 때문에, 반드시 자원을 이용하기 위해서는 OS 영역을 거쳐야 한다.

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/8/8f/Kernel_Layout.svg/1200px-Kernel_Layout.svg.png" alt="자원과 커널">

그리고 OS의 서비스 중 이러한 자원 할당과 프로세스 관리를 해주는 프로그램을 kernel이라고 한다. 즉, kernel은 유저 프로그램을 위한 자원을 할당하고 프로세스 관리를 해주는 등의 관리를 해 주는 OS 의 핵심 영역이다.

