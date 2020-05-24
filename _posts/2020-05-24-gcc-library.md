---
layout: post
title: 'GCC로 라이브러리 다루기'
author: minchul.kang
date: 2020-05-24 12:00
tags: [system-programming]
---

## 라이브러리

라이브러리는 자주 사용되는 유용한 함수에 대한 오브젝트 파일의 모음이다. 리눅스의 경우, `/usr/lib` 디렉터리 아래 `.a`확장자를 가진 파일이 시스템에서 제공하는 라이브러리이다.

```
minchul@DESKTOP-N87KQ5N:/usr/lib/gcc/x86_64-linux-gnu/7$ pwd
/usr/lib/gcc/x86_64-linux-gnu/7
minchul@DESKTOP-N87KQ5N:/usr/lib/gcc/x86_64-linux-gnu/7$ ll
total 57716
drwxr-xr-x 1 root root      512 May 24 01:21 ./
drwxr-xr-x 1 root root      512 May 24 01:21 ../
-rw-r--r-- 1 root root     1464 Dec  4 23:25 crtoffloadtable.o
-rw-r--r-- 1 root root     3368 Dec  4 23:25 crtprec32.o
-rw-r--r-- 1 root root     3376 Dec  4 23:25 crtprec64.o
-rw-r--r-- 1 root root  2925518 Dec  4 23:25 libasan.a
-rw-r--r-- 1 root root   119194 Dec  4 23:25 libatomic.a
-rw-r--r-- 1 root root    70242 Dec  4 23:25 libbacktrace.a
-rw-r--r-- 1 root root   277408 Dec  4 23:25 libcilkrts.a
-rw-r--r-- 1 root root    58898 Dec  4 23:25 libgcov.a
-rw-r--r-- 1 root root   352138 Dec  4 23:25 libgomp.a
-rw-r--r-- 1 root root    25732 Dec  4 23:25 libmpx.a
-rw-r--r-- 1 root root    12100 Dec  4 23:25 libmpxwrappers.a
-rw-r--r-- 1 root root     1486 Dec  4 23:25 libssp_nonshared.a
-rw-r--r-- 1 root root  2036204 Dec  4 23:25 libtsan.a
-rw-r--r-- 1 root root   811530 Dec  4 23:25 libubsan.a
```

## `ar` 를 이용하여 라이브러리 만들기

### 1. 라이브러리로 만들고 싶은 소스코드 오브젝트 파일로 만들기

```
minchul@DESKTOP-N87KQ5N:~/mylib$ ls
minus.c  plus.c
minchul@DESKTOP-N87KQ5N:~/mylib$ cat plus.c
int plus(int x, int y)
{
    return x+y;
}
minchul@DESKTOP-N87KQ5N:~/mylib$ cat minus.c
int minus(int x, int y)
{
    return x-y;
}
```

오브젝트 파일 만들기

```
minchul@DESKTOP-N87KQ5N:~/mylib$ gcc -c plus.c minus.c
minchul@DESKTOP-N87KQ5N:~/mylib$ ls
minus.c  minus.o  plus.c  plus.o
```
### 2. `ar` 명령어 (`r`옵션)을 이용하여 `.a`파일 만들기

```
minchul@DESKTOP-N87KQ5N:~/mylib$ ar r libmy.a plus.o minus.o
ar: creating libmy.a
```

### 3. `ar` 명령어 (`s`옵션)을 이용하여 라이브러리 목록에 추가하기 

```
minchul@DESKTOP-N87KQ5N:~/mylib$ ar s libmy.a
minchul@DESKTOP-N87KQ5N:~/mylib$ mkdir libmy
minchul@DESKTOP-N87KQ5N:~/mylib$ mv libmy.a libmy
```

그리고 이 라이브러리 (libmy.a) 는 libmy 디렉터리 안에 넣어준 뒤, 
이렇게 만든 libmy.a를 이제 사용해보자.

```
minchul@DESKTOP-N87KQ5N:~/mylib$ cat test.c
#include<stdio.h>
int plus(int x, int y);
int minus(int x, int y); // 직접 정의한 라이브러리 함수

int main(void)
{
    printf("%d %d \n", plus(2, 3), minus(2,3));
    return 0;
}
```

## 표준 라이브러리가 아닌 라이브러리를 사용하고 싶을 때 

gcc의 `-l` 옵션은 표준 라이브러리가 아닌 라이브러리를 사용하고 싶을 대 그 라이브러리를 지정해 컴파일해 주어야 한다. 

예를 들어 표준이 아닌 libmy.a라는 라이브러리가 있을 때, `gcc test.c -lmy` 와 같이 lib라는 글자와 확장자 a를 뺀 이름만을 옵션에 붙여 쓴다. 

그런데 이 상태에서 컴파일하면 에러가 나온다. `-lmy` 를 찾을 수 없다고 한다.

```
minchul@DESKTOP-N87KQ5N:~$ gcc test.c -lmy
/usr/bin/ld: cannot find -lmy
collect2: error: ld returned 1 exit status
```


## 사용할 라이브러리의 위치를 지정하고 싶을 때

이 이유는 링커 ld가 라이브러리르 찾을 때 /lib, /usr/lib 같이 정해진 디렉터리만 찾기 때문이다. 현재 우리가 사용하고자 하는 라이브러리 libmy는 현재 디렉터리의 하위 디렉터리 libmy에 있는데, gcc에게 이 외부 라이브러리는 현재 libmy라는 폴더에 있음을 말해주어야 한다.

```
.
├── libmy
│   └── libmy.a
├── minus.c
├── minus.o
├── plus.c
├── plus.o
└── test.c
```

`-L`옵션 뒤에 라이브러리가 위치한 디렉터리를 적어준다.

```
minchul@DESKTOP-N87KQ5N:~/mylib$ gcc test.c -lmy -Llibmy
```

그러면 성공적으로 실행 파일이 생성되는 걸 확인할 수 있다.

```
minchul@DESKTOP-N87KQ5N:~/mylib$ ./a.out
5 -1
```



---

> 이 포스트는 [초보자를 위한 Linux/Unix 프로그래밍 - 김종훈 김종진 김동균]을 토대로 작성되었습니다.