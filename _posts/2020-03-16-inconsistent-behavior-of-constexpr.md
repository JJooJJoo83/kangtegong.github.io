---
layout: post
title: 'Inconsistent behavior of constexpr'
author: minchul.kang
date: 2020-03-17 12:00
tags: [python-package-dependency]
---

## const & constexpr

### const

`const` 는 해당 함수 혹은 변수를 Read Only로 변경해주는 식별자이다.
`const`로 지정된 대상을 수정하려 들면 오류를 내보내는데,
이 과정은 런타임에서 감지된다.

### constexpr

`const`는 런타임에서 상수 오류를 감지한다면,
`constexpr`는 컴파일 과정에서 오류를 감지한다.

또한 `constexpr`는 값이 런타임 대신 컴파일 시간에 계산될 때, 
프로그램을 보다 빠르게 돌아가게 하고 보다 적은 메모리를 사용하도록 도움을 준다.

자세한 내용은 아래 내용 참고. 

[const란 - en](https://en.cppreference.com/w/cpp/keyword/const)

[const란2 - ko](https://docs.microsoft.com/ko-kr/cpp/cpp/const-cpp?view=vs-2019)

[constexpr란 - en](https://en.cppreference.com/w/cpp/language/constexpr)

[constexpr란2 - ko](https://docs.microsoft.com/ko-kr/cpp/cpp/constexpr-cpp?view=vs-2019)

## test code  : `const` 

우선 `const`의 동작을 알아보기 위해 아래 예제 코드를 보자.
간단한 피보나치 예제 코드이다.

{% highlight c++ %}
#include <cstdio>
#include <cstdlib>
const int fib(const int n)
{
    if (n <= 2)
        return 1;
    return fib(n - 1) + fib(n - 2);
}
int main(int argc, char* argv[])
{
    const int n = 7;
    const int result = fib(n);
    printf("%d\n", result);
    return fib(5);
}
{% endhighlight %}
 
위 코드를 동작 과정을 tracing 해 보며,
컴파일러 별로 `fib` 함수가 언제 계산되는지 확인해보자. 


## testing tool : uftrace

### compile

위 코드를 각각 `g++`과 `clang`으로 컴파일 한 뒤,
[uftrace](https://github.com/namhyung/uftrace)를 이용해 함수 호출 과정을 분석해 보았다.

uftrace의 적용을 위해 `pg` option을 주어 컴파일 했는데,
`-finstrument-functions`도 가능하다.

> g++ compile (w/ -pg option)

```
$ g++ -pg -std=c++14 const.cpp
```

> clang compile (w/ -pg option)

```
$ clang -pg -std=c++14 const.cpp
```
### trace

`fib`함수의 인자와 리턴값을 확인하기 위해 uftrace option으로 각각
`-A fib@arg1/u` 와  `-R fib@retval`를 주었다.

또한 `printf`의 string인자와 int인자를 확인하기 위해 
`printf@arg1/s,arg2/i` 옵션을 주었다.


```
$ uftrace -A fib@arg1/u -R fib@retval -A printf@arg1/s, args/i a.out
```

## output
 (당연하게도) `g++` compile 과 `clang` compile 의 object file은 동일했고,
 따라서 tracing 결과도 동일했다.


 `fib`함수는 상술했듯 컴파일타임이 아닌 런타임에서 연산이 되었고,
 그 과정은 다음의 uftrace 결과를 통해 확인할 수 있다.
 
```
# DURATION     TID     FUNCTION
   0.865 us [ 10493] | __monstartup();
   0.473 us [ 10493] | __cxa_atexit();
            [ 10493] | main() {
            [ 10493] |   fib(7) {
            [ 10493] |     fib(6) {
            [ 10493] |       fib(5) {
            [ 10493] |         fib(4) {
            [ 10493] |           fib(3) {
   0.126 us [ 10493] |             fib(2) = 1;
   0.086 us [ 10493] |             fib(1) = 1;
   1.074 us [ 10493] |           } = 2; /* fib */
   0.082 us [ 10493] |           fib(2) = 1;
   3.191 us [ 10493] |         } = 3; /* fib */
            [ 10493] |         fib(3) {
   0.090 us [ 10493] |           fib(2) = 1;
   0.080 us [ 10493] |           fib(1) = 1;
   0.560 us [ 10493] |         } = 2; /* fib */
   4.033 us [ 10493] |       } = 5; /* fib */
                (.. 중략 ..)
                
            [ 10493] |       fib(3) {
   0.088 us [ 10493] |         fib(2) = 1;
   0.078 us [ 10493] |         fib(1) = 1;
   0.519 us [ 10493] |       } = 2; /* fib */
   1.755 us [ 10493] |     } = 5; /* fib */
   8.141 us [ 10493] |   } = 13; /* fib */
            [ 10493] |   printf() {
  23.533 us [ 10493] |     /* linux:schedule */
  11.998 us [ 10493] |     /* linux:schedule */
  43.076 us [ 10493] |   } /* printf */
            [ 10493] |   fib(5) {
            [ 10493] |     fib(4) {
            [ 10493] |       fib(3) {
   0.107 us [ 10493] |         fib(2) = 1;
   0.077 us [ 10493] |         fib(1) = 1;
   0.665 us [ 10493] |       } = 2; /* fib */
   0.080 us [ 10493] |       fib(2) = 1;
   1.060 us [ 10493] |     } = 3; /* fib */
            [ 10493] |     fib(3) {
   0.090 us [ 10493] |       fib(2) = 1;
   0.080 us [ 10493] |       fib(1) = 1;
   0.514 us [ 10493] |     } = 2; /* fib */
   1.954 us [ 10493] |   } = 5; /* fib */

```

## inconsistent behavior in `constexpr`

하지만 컴파일러마다 `constexpr`를 동일하게 해석하지는 않았다.

`constexpr`의 사용 목적은 (상술했듯) 
컴파일 과정에서의 상수값 수정을 방지하는 데에도 있으나,

`constexpr` 함수를 컴파일 과정에서 미리 연산을 해
프로그램을 보다 빠르게 돌아가게 하게 하는 데에도 있는데,

후자의 경우 컴파일러에 따라 다른 양상을 보였다.
 
## test code : `constexpr`

다음의 피보나치 코드를 예로 들어보자.
위 예시 코드에서 `const` 를 `constexpr` 로 바꾼 것에 불과하다.

{% highlight c++ %}
#include <cstdio>
#include <cstdlib>
constexpr int fib(const int n)
{
    if (n <= 2)
        return 1;
    return fib(n - 1) + fib(n - 2);
}
int main(int argc, char* argv[])
{
    constexpr int n = 7;
    const int result = fib(n);
    printf("%d\n", result);
    return fib(5);
}
{% endhighlight %}

## testing tool : uftrace

> g++ compile (w/ -pg option)

```
$ g++ -pg -std=c++14 constexpr.cpp
```

> clang compile (w/ -pg option)

```
$ clang -pg -std=c++14 constexpr.cpp
```

이번에도 uftrace를 이용해 함수를 tracing 해 보자.

```
$ uftrace -A fib@arg1/u -R fib@retval -A printf@arg1/s, args/i a.out
```

## output

### gcc

아래 결과를 보면 알 수 있듯, 컴파일 시간에 이미 `fib`함수는 계산이 완료되었고,
a.out은 그 반환값인 13만을 가지고 있었음을 확인할 수 있다.

```
 DURATION     TID     FUNCTION
   0.786 us [ 10782] | __monstartup();
   0.448 us [ 10782] | __cxa_atexit();
            [ 10782] | main() {
            [ 10782] |   printf("%d\n", 13) {
  30.899 us [ 10782] |     /* linux:schedule */
  11.771 us [ 10782] |     /* linux:schedule */
 169.777 us [ 10782] |   } /* printf */
            [ 10782] |   fib(5) {
            [ 10782] |     fib(4) {
            [ 10782] |       fib(3) {
   2.288 us [ 10782] |         fib(2) = 1;
   0.078 us [ 10782] |         fib(1) = 1;
   3.211 us [ 10782] |       } = 2; /* fib */
   0.090 us [ 10782] |       fib(2) = 1;
   3.703 us [ 10782] |     } = 3; /* fib */
            [ 10782] |     fib(3) {
   0.080 us [ 10782] |       fib(2) = 1;
   0.090 us [ 10782] |       fib(1) = 1;
   0.550 us [ 10782] |     } = 2; /* fib */
   4.764 us [ 10782] |   } = 5; /* fib */
 175.702 us [ 10782] | } /* main */

```

### clang 

반면 clang 을 이용한 컴파일을 tracing했을 적에는 결과가 달랐는데, 컴파일 시간에 계산이 완료되지 않았고 실행시간에 계산을 하고 있음을 확인할 수 있다.

```
1.228 us [ 10858] | __monstartup();
   0.452 us [ 10858] | __cxa_atexit();
            [ 10858] | main() {
            [ 10858] |   fib(7) {
            [ 10858] |     fib(6) {
            [ 10858] |       fib(5) {
            [ 10858] |         fib(4) {
            [ 10858] |           fib(3) {
   0.128 us [ 10858] |             fib(2) = 1;
   0.091 us [ 10858] |             fib(1) = 1;
   1.134 us [ 10858] |           } = 2; /* fib */
   0.099 us [ 10858] |           fib(2) = 1;
   3.245 us [ 10858] |         } = 3; /* fib */
            [ 10858] |         fib(3) {
   0.079 us [ 10858] |           fib(2) = 1;
   0.092 us [ 10858] |           fib(1) = 1;
   0.564 us [ 10858] |         } = 2; /* fib */
   4.107 us [ 10858] |       } = 5; /* fib */
            [ 10858] |       fib(4) {
            [ 10858] |         fib(3) {
   0.082 us [ 10858] |           fib(2) = 1;
   0.089 us [ 10858] |           fib(1) = 1;
   0.542 us [ 10858] |         } = 2; /* fib */
   0.078 us [ 10858] |         fib(2) = 1;
   0.910 us [ 10858] |       } = 3; /* fib */
   5.333 us [ 10858] |     } = 8; /* fib */
            [ 10858] |     fib(5) {
            [ 10858] |       fib(4) {
            [ 10858] |         fib(3) {
   0.084 us [ 10858] |           fib(2) = 1;
   0.088 us [ 10858] |           fib(1) = 1;
   0.560 us [ 10858] |         } = 2; /* fib */
   0.079 us [ 10858] |         fib(2) = 1;
   0.932 us [ 10858] |       } = 3; /* fib */
            [ 10858] |       fib(3) {
   0.088 us [ 10858] |         fib(2) = 1;
   0.087 us [ 10858] |         fib(1) = 1;
   0.527 us [ 10858] |       } = 2; /* fib */
   1.741 us [ 10858] |     } = 5; /* fib */
   7.952 us [ 10858] |   } = 13; /* fib */
            [ 10858] |   printf("%d\n", 13) {

```


## Conclusion

`const`와 `constexpr` 식별자는 read-only로 변형해 준다는 공통점이 있으나,

함수나 생성자에 붙게 될 경우,  
언제 연산될지에 대한 식별자별 / 컴파일별 차이가 있었다. 

그리고 그 차이는 다음과 같았다.

||const |constexpr  |
|--|--|--|
|g++ | RunTime | CompileTime |
|clang | RunTime | RunTime |


`constexpr` 식별자는 **가능하면** 컴파일 타에 계산되는 식별자이지, 컴파일 타임에 **반드시** 계산되는 것이 아니다.

또한, 컴파일 타임에 계산이 되거나, 런타임에 계산이 되거나 하는 문제는
코드에 종속된 문제가 아니라 컴파일/버전에 따라 달라질 수 있다.