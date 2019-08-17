---
layout: post
title: "CentOS Server (3) 사용자와 그룹관리"
image: ''
date: 2019-08-04 20:20:06
tags: 
- Linux
- CentOS 
- Server
description: 사용자와 그룹을 관리해주는 리눅스 기본 명령어들
categories:
- CentOS Server Management
---



## 사용자 그룹

리눅스는 다중 사용자 시스템

리눅스는 다중 사용자 시스템(Multi-User System) 임 

기본적으로 root라는 이름을 가진 수퍼유저(Superuser) 가 있으며, 모든 작업을 할 수 있는 권한이 있음 

모든 사용자를 하나 이상의 그룹에 소속되어 있음 

사용자는 `/etc/passwd` 파일에 정의되어 있음

`/etc/passwd` 파일에 가 보면 

    사용자 이름: 암호: 사용자 ID : 사용자의 그룹 ID : 전체 이름 : 홈디렉토리 : 기본 셀

형식으로 사용자 계정이 정리되어 있음
굉장히 많은 계정들이 있지만 root와 (우리가 만든)centos를 제외하고는 쓸 일이 많지 않은 계정들이다

`:set number`로 행 번호 표시

centos 계정을 예로 들어 보자면 

    centos	: 사용자 이름이 centos
    x 		: 암호에 x 표시가 되어 있는데 비밀번호가 없다는게 아니라 따로 관리하겠다는 뜻. /etc/shadow 에서 관리된다
    1000 	: 사용자 ID. 마치 학번과 같은 것
    1000 	: centos가 소속된 그룹의 번호
    centos 	: 전체 이름. 이거는 생략이 가능하다
    /home/centos : centos 사용자의 홈 디렉토리
    /bin/bash : 우리가 사용할 기본 셸

그룹들의 이름들을 확인해보자

    vi /etc/group

   

> 그룹이름 : 그룹 비밀번호 : 그룹 번호

형식으로 되어 있다

맨 아래 centos를 보자.
`centos: x: 1000: centos` 라고 되어 있는데
첫번째 centos는 그룹이름
두번째 centos는 그룹에 속한 사용자 이름이다 (생략가능)
즉, centos 는 사용자 이름도 centos, 그룹 이름도 centos 임을 알 수 있다

보통 다중 사용자를 다루는 리눅스는 기본적으로 그룹의 소속을 전제로 하는데, 
그룹을 먼저 생성하지 않고 계정을 만들면 그 계정의 이름과 동일한 그룹을 자동으로 생성한 뒤, 이용자 계정을 그룹 안에 넣는 방식.

## 사용자가 그룹 관련 명령어

### `useradd`
새로운 사용자 추가

    e.g) #useradd username

사용자 생성시 옵션지정

> -u : ID 지정
> -g : 그룹 지정
> -d : 홈 디렉토리 지정
> -s : 셸  지정

### `passwd`
사용자의 비밀번호 지정 혹은 변경

    e.g) # passwd newuser

root는 자신을 포함한 모든 사용자들 비밀번호를 바꿀 수 있고,
root을 제외한 일반 사용자들은 자신의 비밀번호만 바꿀 수 있음

### `usermod`
사용자의 속성 변경

    e.g) # usermod -g root newuser

사용자 속성 변경시 옵션지정

> -u : ID 지정
> -g : 그룹 지정
> -d : 홈 디렉토리 지정
> -s : 셸  지정


### `userdel`
사용자 삭제

     e.g) #  userdel newuser

기본적으로 사용자가 삭제되더라도 홈 디렉토리까지 없어지진 않음
 

    userdel -r newuser
     

사용자가 사용하던 홈 디렉토리도 삭제하겠다


### `chage` 

사용자의 암호를 주기적으로 변경하도록 설정 

    e.g.) # chage -m 2 newuser

`-m` 옵션 : 설정한 암호를 최소 2일은 써야 한다
이 밖에도 다른 옵션들 많음

### `groups` 

현재 사용자가 속한 그룹을 보여줌 

    e.g.)  # groups

사용자는 여러 그룹에 속할 수 있음.
정확히는 주 그룹 / 보조그룹 방식으로 속할 수 있음

### `groupadd` 

새로운 그룹을 생성 

    e.g.) # groupadd newgroup

### `groupmod` 

그룹의 속성을 변경 

    e.g.)  # groupmod -n newgroup mygroup

 groupdel 그룹을 삭제 

     e.g.) # groupdel newgroup

### `gpasswd` 

그룹의 암호를 설정하거나, 그룹의 관리를 수행.
but 그룹의 암호를 설정하는 경우가 많지는 않음.. 
`e.g.) # gpasswd newgroup`


## 실습

 실습의 편의를 위해 root로 접속 권장

    useradd user1

user1이라는 사용자 만들기

    tail -5 /etc/passwd

잘 만들어졌음을 확인할 수 있다

비밀번호를 확인해보자 `tail - 5 /etc/shadow`

user1에 대한 비밀번호가 나오는데 암호화되어있기 때문에 볼 수 없다
(root일지라도 다른 사용자들의 비밀번호는 알 수 없다)
root가 리눅스시스템 전체에서 못하는 딱 하나가 이거다.
다른 사용자의 비밀번호를 아는 것
(근데 다른 사람들 비밀번호를 바꿀 수는 있...)

참고로 같은 비밀번호라고 하더라도 암호화된 형태는 다르게 생겼다

    tail -5 /etc/group

생성된 1001번 ID 그룹이 방금 만든 user1의 그룹임을 확인할 수 있다

그럼 방금 만든 임의의 사용자 user1은 지우고 (`userdel -r user1`)

centosGroup이라는 이름의 그룹을 만들어보자

tail -5 etc/group/ 으로 확인해보면 centosGroup : x: 1001: 
centosGroup이 잘 만들어졌음을 확인할 수 있다.
(물론 아직 소속된 사용자는 없음)

새로운 사용자를 그룹에 추가하기 위해
`useradd`에 `-g` 옵션을 추가한 
`useradd -g 그룹이름 유저이름` 명령어를 입력한다

    useradd -g centosGroup user1
    useradd -g centosGroup user2

로 추가해준다

추가된 user1과 user2의 소속 그룹을 확인해보면
`tail -5 /etc/passwd` 로 확인해보면
둘 다 1001번 그룹으로 같은 그룹에 속해있음을 확인할 수 있다

### 비밀번호 설정해보기

    passwd user1

### user home 폴더 보기

    ls -l /home 


### 만일 X윈도우 상에서 관리

만일 X윈도우 상에서 그룹을 관리하고 싶다면 
`system-config-user` 가 깔려 있어야만 한다

만일 없다면 설치한 뒤 `system-config-user` 쳐서 들어가기

    yum -y install system-config-users

하지만 X윈도가 없으면 사용하지 못하므로 CLI형식에 좀 더 친해지길.. 

