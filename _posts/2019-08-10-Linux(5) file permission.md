---
layout: post
title: "CentOS Server(5) 파일의 소유권/허가권"
image: ''
date: 2019-08-10 21:20:06
tags: 
- Linux
- CentOS 
- Server
- Tutorials
description: 파일의 소유권과 허가권
categories:
- CentOS Server Management
---

## 파일의 리스트와 파일의 속성

touch sample.txt
ls -l 

로 sample.txt 를 확인해보자

    - rw- r-- r-- 1 root root 0 (날짜) (시간) sample.txt

형식으로 쓰여 있는 걸 확인할 수 있다.

각각의 의미에 대해 살펴보자

1. `-`    :    		    파일 유형
	`-`는 일반 파일, `d` 는 디렉토리  `l` 은 링크 파일 ...

2. `rw-r--r--`     : 		파일 허가권
	올 수 있는 알파벳은 rwx가 있고
	각각이 read, write, execute를 의미한다.

	처음 rwx는 소유자의 파일접근 권한
	다음 rwx는 그룹의 파일접근 권한
	마지막 rwx는 그 외의 사용자에 대한 파일접근 권한을 의미한다

	이는 rwx가 아니라 8진수의 숫자로도 표시가 가능하다
	(r=4, w=2, x=1, 최고 7까지의 숫자)
3. `1` : 링크 수
4. `root` : 파일 소유자 이름
5. `root` : 파일 소유 그룹 이름
6. `0` : 파일 크기 (Byte)
7. `(날짜,시간)` : 마지막 변경 날짜/시간
8. `sample.txt` : 파일 이름


## 파일과 디렉터리의 소유와 허가권 

### chmod 명령
	
파일 허가권 변경 명령어
 
	e.g.) # chmod 777 sample.txt


*파일 소유권 (OwnerShip) : 파일을 소유한 사용자와 그룹을 의미*

### chown/ chgrp 명령

파일의 소유권을 바꾸는 명령어

	e.g.) 

    # chown centos sample.txt 		// sample.txt 소유권을 centos 사용자에게 넘겨라
    # chgrp centos sample.txt 		// sample.txt 소유권을 centos 그룹에게 넘겨라
	# chown centos.centos sample.txt // sample.txt 소유권을 centos 사용자에게, centos 그룹에게 넘겨라 
				
명령어 친 뒤 `ls -l`로 소유권이 잘 이전되었는지 확인

다시 root로 소유권 변경하기

    chown root.root sample.txt

## 실습 : 파일의 허가권 및 소유권 연습

vi editor로 test라는 파일을 만들어보자
안에

> 안녕하세요 안녕하세요
 ls /var

라고 치고 `:wq!` (왜 이렇게 치라고 했는지는 후술)

`ls -l`로 확인해보자. 
(파일이 너무 많다면 `ls -l test`로 콕 집어도 좋다)

    -rw r-- r-- 1 root root 55 8월 10일 20:24 text
로 파일의 정보가 표기되어있음을 확인할 수 있는데, 
각각의 정보를 해석해보자.

`whoami`로 자신이 root라는 거 확인 후

test를 실행해보자

    ./test
그럼 (당연하게도) 허가가 거부될 것이다.
실행 주체가 소유자라고 할지라도 실행이 안될 수도 있음을 알려준다.
그럼 실행이 가능하도록 권한을 변경해보자

    chmod 755 test
그 뒤 다시 `./test` 로 실행을 해 본다면 

"안녕하세요 안녕하세요"를 **명령어로 인식**하려고 했으나 실패하고,
실제 명령어인 `ls /var`는 **명령어로 인식**해서 실행이 됨을 확인할 수 있다.

test 파일 안에 명령어들을 쭉 써두고 
실행할 수 있도록 속성을 변경하면 실행이 된다

### 소유권 변경 예제

    chown  centos.centos test

`ls -l` 로 소유권 변경 확인하기

centos 사용자로 test 파일을 실행해보자

    su - centos

(현재 접속 중인 계정이 root라면 암호를 물어보지 않을 것)

`whoami`로 centos 계정을 확인

`pwd` 로 centos의 home 디렉토리 ( `/home/centos` ) 로 이동

이제 아까 만든 root 폴더 아래 test 파일을 실행하기 위해 

    ls -l /root/test
    
를 실행해보자.

test 파일의 접근권을 변경했으므로 들어가지길 기대했지만,

> ls : cannot access /root/ test : 허가거부

메세지가 뜨며 접근이 불가하다고 나온다.

이는 test 파일에 대한 접근권은 얻었으나 root 폴더에 대한 접근권이 없어서 생긴 문제이다.

실제로 `ls -l /`로 root 디렉토리를 확인해보면 
권한이 
`dr-x-x---` 로, 일반 사용자에게 접근이 불가하다고 나와있음을 알 수 있다.

따라서 test를 centos가 실행하기 위해선, test를 centos의 홈 디렉토리로 이동시켜놓아야 한다. 다시 root로 접속하여,

    mv test ~ centos

로 test파일을 centos의 홈폴더로 이동시킨다.

이제 centos 계정으로 test 파일의 속성을 변경시켜보자

su - centos 로 접속하여 ls -l 을 통해 test 파일이 centos의 home 디렉토리에 잘 들어와있는지 확인을 거친 뒤, 

모든 사용자가 이 파일에 접근이 가능하도록 속성을 바꿔보자

    chmod 777 test

ls -l 로 잘 적용이 되었는지 확인을 거친 뒤에 test 파일의 권한을 다시
centos에게 돌려줘보자.

    chown root.root test

그러면 

> chown : changing ownership of 'test' : 명령을 허용하지 않음

이라는 메세지가 뜬다는걸 확인할 수 있다.
`chown` 명령어는 root만 사용가능하기 때문.
(`chown`은 아주 위험한 명령어 중 하나이다.)

헷갈리지 말 것 : `chmod`는 아무나 쓸 수 있지만 `chown`은 root만 쓸 수 있다.

## chmod  다른 표기법 : 심볼릭 표기

소유주 : u (user)
그룹 : g (group)
일반 사용자 : o (other)

더하고 뺀다 : + , -

권한 : rwx

e.g. ) test 파일에 대한 일반 사용자들의 실행 권한을 빼라

    chmod o-x test

e.g. ) test 파일에 대해 소유주와 그룹의 읽기쓰기 권한을 더하라

    chmod ug+rw test 
