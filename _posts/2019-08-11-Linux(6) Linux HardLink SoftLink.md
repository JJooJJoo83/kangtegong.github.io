---
layout: post
title: "CentOS Server(6) 하드링크와 소프트링크"
image: ''
date: 2019-08-11 21:20:06
tags: 
- Linux
- CentOS 
- Server
- Tutorials
description: 원본파일을 가리키는 하드링크와 소프트링크
categories:
- CentOS Server Management
---

## 링크 

파일의 링크에는 하드링크와 심볼릭 링크 (=소프트 링크) 두 가지가 있다.

심볼릭 링크는 Windows의 바로가기 아이콘과 개념이 비슷하다.


![Link]({{ '/assets/images/centos/6_link/1.png' | relative_url }}){: .center-image }


### 원본파일

inode블록

실제 데이터파일에 대한 정보 (파일의 크기, 파일의 정보 등)을 갖고 있는 노드

원본파일이 생성되면 inode가 생기고 실제 데이터 블록에 그 데이터 내용이 저장된다.

### 하드링크 

하드 링크를 생성하면 “하드링크파일”만 하나 생성되며 inode를 가리킨다

    # ln  링크대상파일이름 링크파일이름

### 심볼릭 링크
심볼릭 링크를 생성하면 새로운 inode를 만들고, 원본파일을 가리키는 포인터가 Data 블록에 생성된다. 데이터는 원본 파일을 연결하는 효과를 가진다.  

    `# ln -s 링크대상파일이름 링크파일이름`

일반적으로 많이 쓰는건 심볼릭 링크이다.

## 실습

### 임의의 폴더 생성

    pwd
    mkdir linktest
    cd linktest/
    pwd	

### 원본파일 생성

    vi basefile

내부에 아무 내용이나 작성

> "이것은 원본파일입니다"

원본파일이 생겼으면 inode가 생겼을 것. 이 inode의 번호를 확인해 본다면 
ls 명령어에 i 속성을 추가로 주면 된다

    ls -il basefile
을 통해 basefile을 자세히  살펴보면 
맨 앞에 임의의 숫자가 적혀 있는 걸 확인할 수 있는데 그게 바로 inode 번호이다.

### 하드링크 만들기

    ln basefile hardlink

`ls -il`을 통해 원본파일인 basefile과 hardlink를 대조해보자.
inode번호가 같음을 확인할 수 있다.

    cat hardlink
   
   잘 뜬다.

### 소프트링크 만들기

    ln basefile softlink

`ls -il`을 통해 원본파일인 basefile과 softlink를 대조해보자.
inode번호가 다름을 확인할 수 있다.
파일의 속성도 일반 파일(`-`)이 아닌 링크 파일 (`l`)로 표기가 되어 있다.
또한 softlink는 결국 basefile을 가리키고 있음이 표기되어 있다
(softlink -> basefile)

    cat softlink
잘 뜬다.

### 원본파일을 삭제한다면?

    mv basefile ../
basefile을 상위 폴더로 이동시켜보자.

    cat hardlink
    cat softlink
로 각각 열어보면 hardlink는 잘 열리는데 반해, 
softlink는 가리키는 원본파일이 사라졌기 때문에 어디에 있는지 몰라
파일이 열리지 않음을 확인할 수 있다.

    mv ../basefile .
다시 basefile 이동시키기
