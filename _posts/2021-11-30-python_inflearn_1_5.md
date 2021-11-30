---
layout: article
title: 우리를 위한 프로그래밍 | Ch 5. 파이썬 일급 함수
tags: Python
aside:
  toc: true
---

[우리를 위한 프로그래밍 : 파이썬 중급](https://www.inflearn.com/course/%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A4%91%EA%B8%89-%EC%9D%B8%ED%94%84%EB%9F%B0-%EC%98%A4%EB%A6%AC%EC%A7%80%EB%84%90)을 듣고 정리한 내용

<!--more-->

---
# Chapter 05-01

## 일급 함수(first-class)

- First class Function
  
    - 프로그래밍 언어가 함수를 First-class citizen으로 취급하는 것을 의미함
  
- 파이썬 함수의 특징

- 파이썬 일급 함수 (일급 객체) 특징
  
    1. 런타임 초기화 : 실행 시점에 초기화 됨을 의미
       
    2. 함수를 변수에 할당 가능
       
    3. 다른 함수에서 함수를 인자로 전달 가능
       
    4. 함수에서 함수를 return 가능

### Everything is an Object

- python의 철학
  
    - 모든 것은 객체(object)이다
      
    - 함수 역시 object

```python
# 함수 객체
def factorial(n):
    '''Factorial Function -> n : int'''
    if n == 1:
        return 1
    # 재귀함수
    return n * factorial(n-1)


factorial(6)
# 만약 위와 같이 factorial(6)을 한다면 아래의 연산 과정을 거치게 됨
# 6 * factorial(5)
# 6 * 5 * factorial(4)
# 6 * 5 * 4 * factorial(3) ...


>>> type(factorial)
`<class 'function'>`


# dir(object): 객체에 __dir__() 메서드가 있으면 호출됨
# python -> 함수를 객체 취급함
>>> dir(factorial)
['__annotations__', '__call__', '__class__', '__closure__', '__code__', ... ,
 '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']
```

```python
# 함수만 가지고 있는 속성 확인하기

# class의 속성 확인 위해서 임의의 class 정의
class A:
  pass


# 함수가 가지고 있는 속성 - class가 가지고 있는 속성 = 함수만 가지고 있는 속성
>>> set(sorted(dir(factorial))) - set(sorted(dir(A)))
{'__defaults__', '__get__', '__closure__', '__code__', '__globals__',
 '__kwdefaults__', '__name__', '__annotations__', '__call__', '__qualname__'}


# 함수의 이름 
>>> factorial.__name__
'factorial'
```

### 함수를 변수에 할당 가능

```python
def factorial(n):
    if n == 1:
        return
    return n * factorial(n-1)


# 함수를 변수에 할당 가능
var_func = factorial


>>> var_func
<function factorial at 0x7f8afd8f1310>


# 변수에 할당한 상태로 함수 실행 가능
>>> var_func(10)
3628800


# 변수 상태로 function 위치에 올 수 있음
>>> list(map(var_func, range(1,11)))
[1, 2, 6, 24, 120, 720, 5040, 40320, 362880, 3628800]
```

### 다른 함수에서 함수를 인자로 전달 가능

- 고위 함수 (HOF : Higher-Order Function )
  
    - 함수를 인수로 받거나, 함수를 결과로 반환하는 함수
      
    - map, filter, reduce 등

```python
>>> list(map(var_func, filter(lambda x: x % 2, range(1,6))))
[1, 6, 120]


>>> [var_func(i) for i in range(1,6) if i % 2]
[1, 6, 120]

# sorted 함수도 key parameter의 인자로 함수를 받을 수 있기에 일급함수에 포함됨
```

### reduce()

```python
# reduce()
from functools import reduce
from operator import add

# reduce(function, sequence, initial=_initial_missing)
# 함수를 누적 적용하는 함수

# 여기서는 add를 누적으로 적용함
>> reduce(add, range(1,11))
55

>> sum(range(1,11))
55

# 익명함수(lambda) : 가독성이 떨어지므로 가급적으로 일반 함수 형태로 리팩토링 권장
>> reduce(lambda x, t: x + t, range(1,11))
55
```

참고하면 좋은 블로그 : [파이썬 reduce 함수 사용법](https://www.daleseo.com/python-functools-reduce/)
<br><br>

## callable()

- 함수, 인스턴스 등이 호출 가능한지 확인하는 함수
  
- 호출이 가능하면 **True** return
  
- \_\_call\_\_ 메서드가 존재하면 callable

```python
def factorial(n):
    if n == 1:
        return 1
    return n * factorial(n-1)


var_func = factorial


# callable인지 확인
# 함수 __call__ 속성 있으므로 callable
>>> callable(factorial)
True


# 변수로 지정한 함수도 callable
>>> callable(var_func)
True


# 3.14는 상수이므로 callable 하지 않음
>>> callable(3.14)
False
```

```python
# __call__이 있으면 함수가 아니라도 callable
>>> callable(str)
True


>>> callable(list)
True


# 위의 str, list는 type class
# type class는 __call__을 가지고 있음
>>> type(str)
`<class 'type'>`


>>> type(list)
`<class 'type'>`


# 위의 list, str은 __call__을 가지고 있는 type class이므로 callable함


# 아래의 list()는 list class
# list class는 __call__을 구현하고 있지 않기에 callable 하지 않음
>>> type(list())
`<class 'list'>`


>>> callable(list())
False
```

### signature()

- 함수의 인자에 대해 알 수 있음

```python
def test_function(param, param2='second', *args, **kwargs):
    print('test')
    return None

from inspect import signature

sg = signature(test_function)

>>> sg
<Signature (param, param2='second', *args, **kwargs)>

>>> sg.parameters
mappingproxy(OrderedDict([('param', <Parameter "param">), ('param2', <Parameter "param2='second'">), 
('args', <Parameter "*args">), ('kwargs', <Parameter "**kwargs">)]))

# 아래와 같이 parameter의 name, default value, kind 등을 알 수 있음
>>> for param in sg.parameters.values():
	    print(param.name)
	    print(param.default)
	    print(param.kind)
	    print('---')
    
param
<class 'inspect._empty'>
POSITIONAL_OR_KEYWORD
---
param2
second
POSITIONAL_OR_KEYWORD
---
args
<class 'inspect._empty'>
VAR_POSITIONAL
---
kwargs
<class 'inspect._empty'>
VAR_KEYWORD
---
```

## partial

- partial object를 return
    - partial object → func 처럼 동작함
    - 첫번째 인자로 받은 **함수**에 **나머지 인자를 고정**하여 partial object 생성

```python
# partial(func, /, *args, **keywords) -> return partial object
# partial -> 인수 고정
# 콜백 함수에 사용

from operator import mul
from functools import partial

>>> mul(10,10)
100

# 함수 인수로 전달 가능 -> 일급 객체이므로
# mul에는 2개의 값이 들어가야하는데 하나만 변수화해서 partial object로 생성함
five = partial(mul, 5)

>>> five
functools.partial(<built-in function mul>, 5)

>>> five(10)
50

# 고정 인자 추가
six = partial(five, 6)

>>> six
functools.partial(<built-in function mul>, 5, 6)

>>> six()
30

# 받아야하는 인자만큼 고정하면 변수처럼 사용가능
thirty = partial(mul, 6, 5)

>>> thirty()
30

# 아래와 같이 사용할 수 있음
>>> [five(i) for i in range(1,11)]
[5, 10, 15, 20, 25, 30, 35, 40, 45, 50]

>>> list(map(five, range(1,11)))
[5, 10, 15, 20, 25, 30, 35, 40, 45, 50]
```

### callback 함수

- **콜백**(callback) 또는 **콜애프터 함수**(call-after function)는 다른 코드의 인수로서 넘겨주는 실행 가능한 코드 의미
- 콜백을 넘겨받는 코드는 해당 콜백을 필요에 따라 **즉시 실행**할 수도 있고 **나중에 실행**할 수 있음
- python은 함수가 1급 객체이므로 인자 전달 시 함수를 전달할 수 있기에 callback function이 가능함

```python
def callback(n):
    print("Sum = {}".format(n))

def main(a, b, _callback = None):
    print("adding {} + {}".format(a, b))
    if _callback:
        _callback(a+b)

>>> main(1, 2, callback)
adding 1 + 2
Sum = 3
```

ref) [stack overflow : how-to-write-a-simple-callback-function](https://stackoverflow.com/questions/40843039/how-to-write-a-simple-callback-function/40843238)

### lambda vs partial

- partial 예제

```python
from functools import partial

funcs = []

>>> for i in range(5):
	    funcs.append(partial(print, i))

>>> funcs
[functools.partial(<built-in function print>, 0), functools.partial(<built-in function print>, 1), functools.partial(<built-in function print>, 2),
 functools.partial(<built-in function print>, 3), functools.partial(<built-in function print>, 4)]

>>> for f in funcs:
	    f()

0
1
2
3
4

# 0부터 순차적으로 출력
```

- lambda 예제

```python
funcs = []

>>> for i in range(5):
	    funcs.append(lambda : print(i))

>>> funcs
[<function <lambda> at 0x7fc0dc10a670>, <function <lambda> at 0x7fc0dc10a5e0>, <function <lambda> at 0x7fc0dc10a700>,
 <function <lambda> at 0x7fc0dc10a790>, <function <lambda> at 0x7fc0dc10a8b0>]

>>> for f in funcs:
	    f()
4
4
4
4
4

# 숫자 4만 5번 출력
```

- partial : 0부터 순차적으로 출력 /  lambda : 숫자 4만 5번 출력
    - lambda는 호출될 때 코드가 생성되기 때문에 loop에서 마지막 값인 4가 출력
        
        ```python
        i = 0
        for i in range(5):
            funcs.append(lambda: print(i))
            
        >>> i
        4
        
        # 처음에 i가 0 이었어도 loop 돈 후 4가됨
        # lambda 호출시 i(4) 사용함
        ```
        
    - partial은 생성될 때 인자를 받음

- lambda
    - same type as a standard function
    - instance method 처럼 행동하게 됨

- partial
    - 인자가 있는 func 처럼 동작하는 partial object를 return

ref) [https://wikidocs.net/13973](https://wikidocs.net/13973)

# Chapter 05-02

## python variable scope

![ref) [https://www.datacamp.com/community/tutorials/scope-of-variables-python](https://www.datacamp.com/community/tutorials/scope-of-variables-python)](5%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%8A%E1%85%A5%E1%86%AB%20%E1%84%8B%E1%85%B5%E1%86%AF%E1%84%80%E1%85%B3%E1%86%B8%20%E1%84%92%E1%85%A1%E1%86%B7%E1%84%89%E1%85%AE%20dcb43d608e0045a9a25947cef9a0d5c7/Untitled.png)

ref) [https://www.datacamp.com/community/tutorials/scope-of-variables-python](https://www.datacamp.com/community/tutorials/scope-of-variables-python)

## variable scope

- 변수가 선언된 위치에 따라 해당 변수가 영향을 미치는 범위가 달라짐

```python
# 파이썬 변수 범위(scope)

# Ex1
def func_v1(a):
    print(a)
    print(b)

# 예외
# func_v1(10)

# Ex2
b = 20

def func_v2(a):
    print(a)
    print(b)

func_v2(10)

# Ex3

c = 30

def func_v3(a):
    global c
    print(a)
    print(c)
    c = 40
# 참조전에 할당돼서
# 안에 같은 이름이 있어서 local 변수를 사용하려고 함 -> local이 참조 전에 할당돼서 에러
# 함수 내에서 global 써서 변수 변경하는 거 좋은 방법은 아님

print('>>',c)
func_v3(10)
print('>>>',c)

from dis import dis

print()
print(dis(func_v3))

print()
print()

# Closure(클로저) 사용 이유
# 클로저 ! remember! -> scope가 닫혀도 함수 끝나도 값 기억

# 서버 프로그래밍 -> 동시성(Concurrency)제어 -> 메모리 공간에 여러 자원이 접근 -> 교착상태(Dead Lock)
# 메모리를 공유하지 않고 메시지 전달로 처리하기 위한 -> Erlang
# 클로저는 공유하되 변경되지 않는(Immutable, Read Only) 구조 적극적으로 사용 -> 함수형 프로그래밍
# 클로저는 불변자료구조 및 atom(원자성), STM -> 멀티스레드(Coroutine -> 단일 스레드에서 병행성 처리) 프로그래밍에 강점
# 클로저 -> 불변 상태 기억!!! 이 포인트

a = 100

# 누적되지 않음
print(a + 100)
print(a + 1000)
# 누적하려면 재할당의 과정을 거쳐야함..!

# 결과 누적(함수 사용)
print(sum(range(1,51)))
print(sum(range(51,101)))

print()
print()

# 클래스 이용
# 평균 누적해서 구해주는 class
class Averager():
    def __init__(self):
        self._series = []

		# 함수처럼 사용 가능하게 하는 매직 메소드
    def __call__(self, v):
        self._series.append(v)
        print('inner >>> {} / {}'.format(self._series, len(self._series)))
        return sum(self._series) / len(self._series)

# 인스턴스 생성
averager_cls = Averager()

# 누적
# 아래의 예시는 class 인스턴스인데 함수처럼 실행함
# 값의 상태를 계속 가지고 있음 (class에 정의된 series가 저장해줌)

print(averager_cls(15))
print(averager_cls(35))
print(averager_cls(40))

# 핵심은 해당 영역에 상태를 기억하고 있다
# 그렇기 때문에 중단값을 기억하고 나중에 다시 그 시점부터 일을 시작할 수 있음
```

## 클로저

- 파이썬 변수 범위
- global 선언
- 클로저 사용 이유
- class → closure 구현 (class를 이용하여 scope의 상태를 기억하는 것을 구현해봄)

# Chapter 05-03

## 클로저 심화

- 클로저 사용

```python
# 파이썬 심화
# 클로저 : 외부에서 호출된 함수의 변수값, 상태 (레퍼런스) 복사 후 저장(스냅샷) -> 후에 접근(엑세스) 가능

# 클로저(Closure) 사용
def closure_ex1():
    # Free variable (자유 변수)
		# 클로저 영역
    series = []
    def averager(v):
        # series = [] # 주석 해제 후 확인
        series.append(v)
        print('inner >>> {} / {}'.format(series, len(series)))
        return sum(series) / len(series)
    
    return averager
		# 함수를 리턴(함수를 결과로 반환 가능)

avg_closure1 = closure_ex1()

print(avg_closure1)
print(avg_closure1(15))
print(avg_closure1(35))
print(avg_closure1(40))
# series -> 값을 보존하는 역할 (누적해서 보존..)

print()
print()

# function inspection

print(dir(avg_closure1))
print()
print(dir(avg_closure1.__code__))
print()
print(avg_closure1.__code__.co_freevars)
print()
print(dir(avg_closure1.__closure__[0]))
print()
print(avg_closure1.__closure__[0].cell_contents)

print()
print()

# 잘못된 클로저 사용
def closure_ex2():
    # Free variable
    cnt = 0
    total = 0

    def averager(v):
        cnt += 1 # cnt = cnt + 1
        total += v
        return total / cnt
    
    return averager

avg_closure2 = closure_ex2()

# print(avg_closure2(15)) # 예외

# Nonlocal -> Free variable
def closure_ex3():
    # Free variable
    cnt = 0
    total = 0
    
    def averager(v):
        nonlocal cnt, total
        cnt += 1
        total += v
        return total / cnt
    
    return averager

avg_closure3 = closure_ex3()

print(avg_closure3(15))
print(avg_closure3(35))
print(avg_closure3(40))

print()
print()

# nonlocal 좀 공부하기..하항
```

# Chapter 05-04

데코레이터

- 클로저 → 데코레이터 관계

```python
# 파이썬 심화
# 데코레이터

# 장점
# 1. 중복 제거, 코드 간결, 공통 함수 작성
# 2. 로깅, 프레임워크, 유효성 체크..... -> 공통 기능 
# 3. 조합해서 사용 용이

# 단점
# 1. 가독성 감소?
# 2. 특정 기능에 한정된 함수는 -> 단일 함수로 작성하는 것이 유리
# 3. 디버깅 불편

# 데코레이터 실습
import time

# 매개변수로 function 받음
# 자바의 annotation? 이랑 비슷하다고 함~
def perf_clock(func):
    def perf_clocked(*args):
        # 함수 시작 시간 
        st = time.perf_counter() 
        result = func(*args)
        # 함수 종료 시간 계산
        et = time.perf_counter() - st 
        # 실행 함수명
        name = func.__name__
        # 함수 매개변수 
        arg_str = ', '.join(repr(arg) for arg in args)
        # 결과 출력
        print('[%0.5fs] %s(%s) -> %r' % (et, name, arg_str, result)) 
        return result 
    return perf_clocked

def time_func(seconds):
    time.sleep(seconds)

def sum_func(*numbers):
    return sum(numbers)

# 데코레이터 미사용
none_deco1 = perf_clock(time_func)
none_deco2 = perf_clock(sum_func)

print(none_deco1, none_deco1.__code__.co_freevars)
print(none_deco2, none_deco2.__code__.co_freevars)

print('-' * 40, 'Called None Decorator -> time_func')
print()
none_deco1(1.5)
print('-' * 40, 'Called None Decorator -> sum_func')
print()
none_deco2(100, 150, 250, 300, 350)

print()
print()

# 데코레이터 사용
@perf_clock
def time_func(seconds):
    time.sleep(seconds)

@perf_clock
def sum_func(*numbers):
    return sum(numbers)

print('*' * 40, 'Called Decorator -> time_func')
print()
time_func(1.5)
print('*' * 40, 'Called Decorator -> sum_func')
print()
sum_func(100, 150, 250, 300, 350)
print()

```