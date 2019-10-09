---
layout: post
title: "STL comparison : find vs set::find"
image: ''
date: 2019-10-07 09:24:06
tags: 
- Uftrace
- Analysis
description: STL comparison using tracing tool 
categories:
- uftrace
---

# STL comparison : std::find vs std::set::find

This document is written by [Minchul Kang](https://github.com/kangtegong)

## std::find & std::set::find

### std::find

```
template  <class InputIterator, class T> InputIterator find(InputIterator first, InputIterator last, const T& val);
```

특정 범위 안에 원하는 값을 찾을 수 있다.

범위 안 (`first`  부터  `last`  전 까지) 의 원소들 중 
 `val`  과 일치하는 첫 번째 원소를 가리키는 반복자를 리턴한다. 

만일 일치하는 원소를 찾지 못할 경우  `last`  를 리턴한다.


### std::set::find

`
find(element)
`

STL set의 검색 함수로,
찾고자 하는 대상 (`element`)에 해당하는 iterator를 반환한다.

이번 포스트에서는 find와 set::find가 각각 어떤 동작 원리(알고리즘)로 동작하는지, 그에 따른 성능 상의 차이가 있는지에 대해 알아보려고 한다. 

find와 set::find에 대한 자세한 내용은 하단 문서 참고. 

[find란 -en](http://www.cplusplus.com/reference/algorithm/find/?kw=find)

[set::find란 - en](http://www.cplusplus.com/reference/set/set/find/)



## test code  : `std::find` 

우선 `find`의 동작을 알아보기 위해 아래 예제 코드를 보자.

1부터 1000까지의 정수 중 225를 찾아내는 간단한 코드이다.

```
#include <iostream>
#include <vector>
#include <algorithm>
#include <stdio.h>
using namespace std;


int main(){

    vector<int> v;
    int i;

    // search set
    for(i=0; i<1000; i++){
        v.push_back(i);
    }

    vector<int>::iterator iter;
    iter = find(v.begin(), v.end(), 225);
    cout << *iter << endl;

    return 0;
}

```

위 코드를 동작 과정을 tracing 해 보며,
225를 몇 번의 trial을 거쳐, 어떤 알고리즘으로 찾아내는지 알아보자. 


## compile

위 코드를 각각 `g++` 로 컴파일한 뒤,
[uftrace](https://github.com/namhyung/uftrace)를 이용해 함수 호출 과정을 분석해 보았다.

uftrace의 적용을 위해 `pg` option을 주어 컴파일 했는데,
`-finstrument-functions`도 가능하다.

> g++ compile (w/ -pg option)

```
$ g++ -pg -std=c++14 find.cpp -o find

```

## trace output

우선 전체 오브젝트 파일의 큰 그림을 보기 위해 trace depth를 2로 하여 tracing 해 보자

```
$ uftrace -D 2 find
```
그럼 다음과 같은 결과를 얻을 수 있다.

```
# DURATION     TID     FUNCTION
            [ 10983] | _GLOBAL__sub_I_main() {
            [ 10983] |   __static_initialization_and_destruction_0() {
 559.399 us [ 10983] |     /* linux:schedule */
  40.468 us [ 10983] |     /* linux:schedule */
   1.259 ms [ 10983] |   } /* __static_initialization_and_destruction_0 */
   1.260 ms [ 10983] | } /* _GLOBAL__sub_I_main */
            [ 10983] | main() {
   0.448 us [ 10983] |   std::vector::vector();
   3.875 us [ 10983] |   std::vector::push_back();
   6.104 us [ 10983] |   std::vector::push_back();
   2.038 us [ 10983] |   std::vector::push_back();
   0.185 us [ 10983] |   std::vector::push_back();
   1.893 us [ 10983] |   std::vector::push_back();
   0.166 us [ 10983] |   std::vector::push_back();
		( .. 중략 .. )
   0.147 us [ 10983] |   std::vector::push_back();
   0.161 us [ 10983] |   std::vector::push_back();
   0.145 us [ 10983] |   std::vector::push_back();
   0.148 us [ 10983] |   std::vector::push_back();
   0.151 us [ 10983] |   std::vector::push_back();
   0.088 us [ 10983] |   std::vector::end();
   0.085 us [ 10983] |   std::vector::begin();
  53.260 us [ 10983] |   std::find();
   0.064 us [ 10983] |   __gnu_cxx::__normal_iterator::operator*();
            [ 10983] |   std::basic_ostream::operator<<() {
   9.203 us [ 10983] |     /* linux:schedule */
 121.930 us [ 10983] |   } /* std::basic_ostream::operator<< */
            [ 10983] |   std::basic_ostream::operator<<() {
  78.656 us [ 10983] |     /* linux:schedule */
  13.418 us [ 10983] |     /* linux:schedule */
 110.324 us [ 10983] |   } /* std::basic_ostream::operator<< */
   1.112 us [ 10983] |   std::vector::~vector();
 583.353 us [ 10983] | } /* main */
   2.133 us [ 10983] | std::ios_base::Init::~Init();

```

`std::vector::push_back();`은 1부터 1000까지의 정수를 밀어넣는 과정일 것이고, 
31.987us나 걸린 std::find()가 우리가 분석하고자 하는 find함수일 것이다.

다른 함수들은 무시하고, 
std::find 함수만 필터링해보자

```
$ uftrace -F std::find -A std::find@arg1 -R std::find@retval find
```
```

# DURATION     TID     FUNCTION
            [ 11077] | std::find(0x248a2f0) {
            [ 11077] |   __gnu_cxx::__ops::__iter_equals_val() {
   0.099 us [ 11077] |     __gnu_cxx::__ops::_Iter_equals_val::_Iter_equals_val();
   1.059 us [ 11077] |   } /* __gnu_cxx::__ops::__iter_equals_val */
            [ 11077] |   std::__find_if() {
   0.062 us [ 11077] |     std::__iterator_category();
            [ 11077] |     std::__find_if() {
            [ 11077] |       __gnu_cxx::operator-() {
   0.056 us [ 11077] |         __gnu_cxx::__normal_iterator::base();
   0.052 us [ 11077] |         __gnu_cxx::__normal_iterator::base();
   0.451 us [ 11077] |       } /* __gnu_cxx::operator- */
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.056 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.209 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
   0.057 us [ 11077] |       __gnu_cxx::__normal_iterator::operator++();
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.052 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.212 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
   0.057 us [ 11077] |       __gnu_cxx::__normal_iterator::operator++();
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.050 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.210 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
		( .. 중략 .. )
   0.058 us [ 11077] |       __gnu_cxx::__normal_iterator::operator++();
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.047 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.192 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
   0.059 us [ 11077] |       __gnu_cxx::__normal_iterator::operator++();
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.052 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.193 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
   0.057 us [ 11077] |       __gnu_cxx::__normal_iterator::operator++();
            [ 11077] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.052 us [ 11077] |         __gnu_cxx::__normal_iterator::operator*();
   0.193 us [ 11077] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */
 411.453 us [ 11077] |     } /* std::__find_if */
 411.839 us [ 11077] |   } /* std::__find_if */
 414.282 us [ 11077] | } = 0x248b100; /* std::find */
```


`-A`를 통해 std::find의 인자를, -R 를 통해 리턴값도 덩달아 확인해보았다.

```
   0.112 us [  7119] |       __gnu_cxx::__normal_iterator::operator++();
            [  7119] |       __gnu_cxx::__ops::_Iter_equals_val::operator() {
   0.115 us [  7119] |         __gnu_cxx::__normal_iterator::operator*();
   0.420 us [  7119] |       } /* __gnu_cxx::__ops::_Iter_equals_val::operator() */

```
위 반복되는 과정이 225를 찾기 위한 trial이라고도 볼 수 있는데, 
실은 무더기로 쌓이는 위 trial만 봐도 대충 std::find의 검색 알고리즘이 뭔지 짐작이 가겠지만

조금 더 확실히 알아보기 위해 
함수가 몇 번 호출되었는지에 대한 report를 받아보자.

```
$ uftrace record find
$ uftrace report
```

이하는 find 오브젝트 파일의 실행 과정에서 호출된 모든 function에 대한 call 수 및 시간이다.
	
```
 Total time   Self time       Calls  Function
  ==========  ==========  ==========  ====================
    9.620 ms  174.649 us           1  main
    6.677 ms    6.677 ms           5  linux:schedule
    6.060 ms  394.850 us        1000  std::vector::push_back
    5.442 ms  598.787 us        1000  std::allocator_traits::construct
    4.110 ms  924.018 us        1000  __gnu_cxx::new_allocator::construct

	... (중략) ... 

   27.030 us   15.987 us          22  std::vector::max_size
   22.919 us    3.066 us          11  std::allocator_traits::deallocate
   21.995 us   21.995 us         227  __gnu_cxx::__normal_iterator::operator*
   21.823 us   21.823 us         225  __gnu_cxx::__normal_iterator::operator++
   19.853 us   12.248 us          11  __gnu_cxx::new_allocator::deallocate

	(후략)
```

위 결과를 보면 알 수 있듯, std::find는 **선형 검색** 알고리즘을 사용한다.

검색의 과정으로 상정했던 
```
   21.995 us   21.995 us         227  __gnu_cxx::__normal_iterator::operator*
   21.823 us   21.823 us         225  __gnu_cxx::__normal_iterator::operator++

```
이 찾고자 하는 원소인 225번 호출된 점을 통해 이를 알 수 있다.

> 참고)
> std::find 함수만 tracing한 결과에 대한 report
```
 Total time   Self time       Calls  Function
  ==========  ==========  ==========  ====================
  801.040 us  156.864 us           2  std::__find_if
  402.436 us    0.550 us           1  std::find
  197.304 us  151.636 us         901  __gnu_cxx::__ops::_Iter_equals_val::operator()
   46.077 us   46.077 us         900  __gnu_cxx::__normal_iterator::operator++
   45.668 us   45.668 us         901  __gnu_cxx::__normal_iterator::operator*
    1.150 us    1.070 us           1  __gnu_cxx::__ops::__iter_equals_val
    0.438 us    0.336 us           1  __gnu_cxx::operator-
    0.102 us    0.102 us           2  __gnu_cxx::__normal_iterator::base
    0.080 us    0.080 us           1  __gnu_cxx::__ops::_Iter_equals_val::_Iter_equals_val
    0.053 us    0.053 us           1  std::__iterator_category

```




만일 찾고자 하는 원소가 225가 아닌 900으로 하면 해당 함수는 

```
54.592 us   54.592 us         900  __gnu_cxx::__normal_iterator::operator++
54.298 us   54.298 us         902  __gnu_cxx::__normal_iterator::operator*

```
이와 같은 결과를 보인다는 점을 통해 이를 재확인 할 수 있다.

 
## test code : `std::set::find`

이번엔 std::set::find을 살펴볼 차례다.
test code는 std::find와 동일한 논리의 코드이다.

1부터 1000까지의 정수 중 225를 찾는 알고리즘이다.

```
#include<set>
#include<string>
#include<stdio.h>

int main(){
    std::set<int> setNum;
    int i;

    for(i=0; i<1000; i++){
        setNum.insert(i);
    }

    std::set<int>::iterator it = setNum.find(225);
    
    return 0;
}
```

## compile

`set::find` 때와 동일하다.

> g++ compile (w/ -pg option)

```
$ g++ -pg -std=c++14 setfind.cpp -o setfind

```


## trace output
우선 전체 오브젝트 파일의 큰 그림을 보기 위해 trace depth를 2로 하여 tracing 해 보자

```
$ uftrace -D 2 setfind
```
그럼 다음과 같은 결과를 얻을 수 있다.

```
# DURATION     TID     FUNCTION
            [ 11180] | main() {
   0.435 us [ 11180] |   std::set::set();
  10.884 us [ 11180] |   std::set::insert();
   2.279 us [ 11180] |   std::set::insert();
   1.981 us [ 11180] |   std::set::insert();
   1.865 us [ 11180] |   std::set::insert();
   1.976 us [ 11180] |   std::set::insert();
   1.968 us [ 11180] |   std::set::insert();
   2.162 us [ 11180] |   std::set::insert();
   2.209 us [ 11180] |   std::set::insert();
   2.129 us [ 11180] |   std::set::insert();
	(..중략..)
   4.134 us [ 11180] |   std::set::insert();
   4.065 us [ 11180] |   std::set::insert();
   4.114 us [ 11180] |   std::set::insert();
   4.095 us [ 11180] |   std::set::insert();
   4.115 us [ 11180] |   std::set::insert();
   4.080 us [ 11180] |   std::set::insert();
   4.099 us [ 11180] |   std::set::insert();
   2.317 us [ 11180] |   std::set::find();
 454.589 us [ 11180] |   std::set::~set();
   6.327 ms [ 11180] | } /* main */

```

`std::set::insert();`은 1부터 1000까지의 정수를 밀어넣는 과정일 것이고, 
2.627us 걸린( 아까보다 빠르다! :) ) `std::set::find()`가 우리가 분석하고자 하는 `set::find`함수일 것이다.

다른 함수들은 무시하고, 
`std::set::find` 함수만 필터링해보자

```
$ uftrace -F std::set::find -A std::set::find@arg1 -R std::set::find@retval setfind
```

```
# DURATION     TID     FUNCTION
            [ 11245] | std::set::find(0x7ffc250e1ae0) {
            [ 11245] |   std::_Rb_tree::find() {
   0.098 us [ 11245] |     std::_Rb_tree::_M_end();
   0.066 us [ 11245] |     std::_Rb_tree::_M_begin();
            [ 11245] |     std::_Rb_tree::_M_lower_bound() {
            [ 11245] |       std::_Rb_tree::_S_key() {
            [ 11245] |         std::_Rb_tree::_S_value() {
            [ 11245] |           std::_Rb_tree_node::_M_valptr() {
            [ 11245] |             __gnu_cxx::__aligned_membuf::_M_ptr() {
   0.050 us [ 11245] |               __gnu_cxx::__aligned_membuf::_M_addr();
   0.318 us [ 11245] |             } /* __gnu_cxx::__aligned_membuf::_M_ptr */
   0.469 us [ 11245] |           } /* std::_Rb_tree_node::_M_valptr */
   0.622 us [ 11245] |         } /* std::_Rb_tree::_S_value */
   0.052 us [ 11245] |         std::_Identity::operator();
		( .. 중략 . )
            [ 11245] |     std::_Rb_tree::_S_key() {
            [ 11245] |       std::_Rb_tree::_S_value() {
            [ 11245] |         std::_Rb_tree_node::_M_valptr() {
            [ 11245] |           __gnu_cxx::__aligned_membuf::_M_ptr() {
   0.050 us [ 11245] |             __gnu_cxx::__aligned_membuf::_M_addr();
   0.238 us [ 11245] |           } /* __gnu_cxx::__aligned_membuf::_M_ptr */
   0.359 us [ 11245] |         } /* std::_Rb_tree_node::_M_valptr */
   0.481 us [ 11245] |       } /* std::_Rb_tree::_S_value */
   0.051 us [ 11245] |       std::_Identity::operator();
   0.767 us [ 11245] |     } /* std::_Rb_tree::_S_key */
   0.052 us [ 11245] |     std::less::operator();
  14.506 us [ 11245] |   } /* std::_Rb_tree::find */
   0.053 us [ 11245] |   std::_Rb_tree_const_iterator::_Rb_tree_const_iterator();
  16.555 us [ 11245] | } = 0xcf0d00; /* std::set::find */

```

위 탐색 과정을 보면 (함수 이름만 봐도) 알 수 있듯 set::find 는 rb-tree를 사용한다

이 또한 조금 더 확실히 알아보기 위해 
`std::set::find`함수가 몇 번 호출되었는지에 대한 report를 받아보자.

```
$ uftrace record -F std::set::find setfind
$ uftrace report
```


```
  Total time   Self time       Calls  Function
  ==========  ==========  ==========  ====================
   31.419 us    1.009 us           1  std::set::find
   30.289 us    3.932 us           1  std::_Rb_tree::find
   23.389 us    5.500 us           1  std::_Rb_tree::_M_lower_bound
   16.653 us    4.696 us          10  std::_Rb_tree::_S_key
   10.819 us    2.634 us          10  std::_Rb_tree::_S_value
    8.185 us    2.754 us          10  std::_Rb_tree_node::_M_valptr
    5.431 us    4.295 us          10  __gnu_cxx::__aligned_membuf::_M_ptr
    1.942 us    1.942 us          10  std::less::operator()
    1.138 us    1.138 us          10  std::_Identity::operator()
    1.136 us    1.136 us          10  __gnu_cxx::__aligned_membuf::_M_addr
    0.586 us    0.586 us           5  std::_Rb_tree::_S_left
    0.544 us    0.434 us           1  std::_Rb_tree::end
    0.453 us    0.453 us           4  std::_Rb_tree::_S_right
    0.301 us    0.301 us           1  std::_Rb_tree::_M_end
    0.233 us    0.233 us           2  std::_Rb_tree_iterator::_Rb_tree_iterator
    0.141 us    0.141 us           1  std::_Rb_tree::_M_begin
    0.121 us    0.121 us           1  std::_Rb_tree_const_iterator::_Rb_tree_const_iterator
    0.114 us    0.114 us           1  std::_Rb_tree_iterator::operator==

```

실제로 225가 아닌 500을 찾는 것으로 코드를 변경하면 다음과 같은 결과를 얻게 된다.

```
  Total time   Self time       Calls  Function
  ==========  ==========  ==========  ====================
   23.114 us    0.838 us           1  std::set::find
   22.190 us    2.895 us           1  std::_Rb_tree::find
   17.301 us    4.746 us           1  std::_Rb_tree::_M_lower_bound
   11.620 us    3.994 us          11  std::_Rb_tree::_S_key
    6.650 us    2.323 us          11  std::_Rb_tree::_S_value
    4.327 us    3.353 us          11  std::_Rb_tree_node::_M_valptr
    1.096 us    1.096 us          11  std::less::operator()
    0.976 us    0.976 us          11  std::_Identity::operator()
    0.974 us    0.974 us          11  std::__addressof
    0.540 us    0.540 us           6  std::_Rb_tree::_S_right
    0.458 us    0.366 us           1  std::_Rb_tree::end
    0.373 us    0.373 us           4  std::_Rb_tree::_S_left
    0.192 us    0.192 us           2  std::_Rb_tree_iterator::_Rb_tree_iterator
    0.160 us    0.160 us           1  std::_Rb_tree::_M_end
    0.105 us    0.105 us           1  std::_Rb_tree::_M_begin
    0.097 us    0.097 us           1  std::_Rb_tree_iterator::operator==
    0.086 us    0.086 us           1  std::_Rb_tree_const_iterator::_Rb_tree_const_iterator

```

