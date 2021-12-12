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
# Chapter 05 - 01

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
  
    - **모든 것은 객체(object)이다**
      
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

### 함수를 인자로 전달 가능

- 다른 함수에서 함수를 인자로 전달 가능
  
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
`<class 'inspect._empty'>`
POSITIONAL_OR_KEYWORD
---
param2
second
POSITIONAL_OR_KEYWORD
---
args
`<class 'inspect._empty'>`
VAR_POSITIONAL
---
kwargs
`<class 'inspect._empty'>`
VAR_KEYWORD
---
```

## partial

- partial object를 return
    - partial object → func 처럼 동작함
    - 첫번째 인자로 받은 <span style="color:#DD2525">**함수**</span>에 <span style="color:#0067a3">**나머지 인자를 고정**</span>하여 partial object 생성

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
<br>
      
- partial
  
    - 인자가 있는 func 처럼 동작하는 partial object를 return

---

# Chapter 05 - 02

## python variable scope

![image](https://user-images.githubusercontent.com/60612551/144049348-75ed890a-9052-4fcb-a0cc-4bcc5837cf53.png)

<br>
- LEGB Rule
  
  - Python에서 변수에 값을 바인딩하거나 변수의 값을 참조하는 경우 **L, E, G, B** 순으로 탐색
    <br><br>
    
    ![image](https://user-images.githubusercontent.com/60612551/144050685-4b4601f7-836b-4796-9bdb-e31ce827709d.png)
    <br>
    
- 변수가 선언된 위치에 따라 해당 변수가 영향을 미치는 범위가 달라짐

```python
# 파이썬 변수 범위(scope)

# Ex1
def func_v1(a):
    print(a)
    print(b)


# 'b'가 정의 되어 있지 않아서 에러 발생
>>> func_v1(10)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "<input>", line 3, in func_v1
NameError: name 'b' is not defined



# Ex2
b = 20

def func_v2(a):
    print(a)
    print(b)


# local scope에 정의된 b가 없기 때문에 global scope에 정의된 b 사용함
>>> func_v2(10)
10
20



# Ex3-1
c = 30

def func_v3_1(a):
    print(a)
    print(c)
    c = 40

    
>>> c
30
    

# 에러 발생
>>> func_v3_1(10)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "<input>", line 5, in func_v3
UnboundLocalError: local variable 'c' referenced before assignment

# local scope에 c가 정의되어 있으므로 global 변수가 아닌 local 변수를 사용하려고 함
# local 변수 c가 할당 전에 참조돼서 에러 발생


# Ex3-2
c = 30

def func_v3_2(a):
    global c
    print(a)
    print(c)
    c = 40

# global 키워드 이용하여 local scope의 변수 c를 global 변수로 변경 
# 함수 내에서 global 사용하여 변수 변경하는 것이 좋은 방법은 아님 → 코드에 혼란을 줄 수 있음

>>> c
30


>>> func_v3_2(10)
10
30


# 함수 실행 이후 global 변수 c의 값이 변경됨
>>> c
40

```

### nonlocal

- enclosed scope의 변수
- outer 함수 안에 있으며 동시에 inner 함수 밖에 있는 영역
- outer 함수 밖의 영역은 global scope

```python

# Ex1
# global 변수
a = 1
b = 100

def outer_func():
  # enclosed scope 
  # nonlocal 변수
  # inner_func 입장에서 nonlocal, outer_func 입장에서는 local
  b = 2
  
  def inner_func():
    # local 변수
    c = 3
    print(b)
    print(c) 
    
  return inner_func()


# nonlocal scope에 있는 b 호출
>>> outer_func()
2
3


# Ex2-1
def outer_func():
  b = 2
  
  def inner_func():
    c = 3
    print(b)
    b = 5 
    
  return inner_func()


# 에러 발생
>>> outer_func()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "<input>", line 9, in outer_func
  File "<input>", line 6, in inner_func
UnboundLocalError: local variable 'b' referenced before assignment

# local scope에 b가 정의되어 있으므로 nonlocal 변수가 아닌 local 변수를 사용하려고 함
# local 변수 b가 할당 전에 참조돼서 에러 발생


# Ex2-2
def outer_func():
    b = 2
    def inner_func():
        nonlocal b
        c = 3
        print(b)
        b = 5
        print(b)
    
    return inner_func()


# nonlocal keyword를 사용하여 local scope의 변수 b를 nonlocal 변수로 변경
>>> outer_func()
2
5

```

### 누적하여 계산하기

- 일반적인 계산

  ```python
  
  a = 100
  
  
  # 계산을 반복해도 계산이 누적되지 않음
  >>> a + 100
  200
  
  
  >>> a + 1000
  1100
  
  
  # 계산을 누적하려면 재할당의 과정을 거쳐야함!
  
  a = a + 100
  a = a + 1000
  
  
  >>> a
  1200
  ```

- 함수를 사용하여 결과 누적

  ```python
  # 함수를 사용하여 결과 누적
  >>> sum(range(1,51))
  1275
  ```

- class를 사용하여 결과 누적
  
  ```python
  
  # 평균 누적해서 구해주는 class
  class Averager():
      def __init__(self):
          self._series = []
  
      # 함수처럼 사용 가능하게 하는 매직 메소드
      def __call__(self, v):
          self._series.append(v)
          print('inner : {} / {}'.format(self._series, len(self._series)))
          return sum(self._series) / len(self._series)
  
  
  # 인스턴스 생성
  averager_cls = Averager()
  
  # 누적하여 계산할 수 있음
  # 아래의 예시는 class 인스턴스인데 함수처럼 실행함 (__call__이 정의되어 있기에 가능)
  # 값의 상태를 계속 가지고 있음 (class에 정의된 series가 값의 상태를 저장해줌)
  
  >>> averager_cls(15)
  inner : [15] / 1
  15.0
  
  
  >>> averager_cls(35)
  inner : [15, 35] / 2
  25.0
  
  
  >>> averager_cls(40)
  inner : [15, 35, 40] / 3
  30.0
  
  # 해당 영역의 상태를 기억하고 있음 → 중단값 기억가능 → 나중에 해당 시점부터 일을 다시 시작할 수 있음  
  ```

--- 

# Chapter 05-03

## Closure (클로저)
- **first-class functions**의 개념을 이용

- 자신을 둘러싼 scope의 **<span style="color:#DD2525">상태값을 기억</span>**하는 함수
  
  - 함수의 실행이 종료되면 함수 내에 정의되었던 local 변수들은 소멸됨
  
  - closure는 자신이 정의된 scope(nonlocal scope)에 있는 변수를 참조함
  
  - **closure는 nonlocal 영역의 변수를 사용하기에 함수의 실행이 종료되어도 상태값을 기억함**
  
  - 값을 기억하고 있기 때문에 함수가 끝났어도 다음 시점부터 일 할 수 있음

- 중첩 함수(inner func)가 상위 함수(outer func)의 변수를 가두어 사용
  
  - 중첩 함수가 상위 함수 scope인 nonlocal scope에 접근하여 사용

### closure 함수가 되기 위한 조건
- 해당 함수는 어떤 함수 내의 <span style="color:#0067a3">**중첩된 함수**</span>여야 함
- 해당 함수는 자신을 둘러싼(enclose) 함수 내의 <span style="color:#0067a3">**상태값을 반드시 참조**</span>해야 함
- 해당 함수를 둘러싼 함수는 이 함수를 <span style="color:#0067a3">**return**</span>해야 함


### 클로저 사용 예시
```python
# class는 자신의 상태를 기억함
# closure를 이용하여 class와 같이 자신의 상태를 기억하는 함수를 구현할 수 있음


# 클로저(Closure) 사용
def closure_ex1():
	# 클로저 영역
    series = []
    
    def averager(v):
        series.append(v)
        print('inner : {} / {}'.format(series, len(series)))
        return sum(series) / len(series)
    
    # 함수를 리턴(함수를 결과로 반환 가능)
    return averager



avg_closure1 = closure_ex1()


>>> avg_closure1
<function closure_ex1.<locals>.averager at 0x7fbc4d1a44c0>


>>> avg_closure1(15)
inner : [15] / 1
15.0


>>> avg_closure1(35)
inner : [15, 35] / 2
25.0


>>> avg_closure1(40)
inner : [15, 35, 40] / 3
30.0
# series -> 값을 보존하는 역할 (누적하며 보존함)
```

#### Free Variable

- free variable (자유변수) 
  - Definition of a free variable: Used, but neither global nor bound
  - enclosed scope의 변수
  
- free variable 저장 위치

```python

def outer():
    # 클로저 영역
    test_var = []
    test_var2 = 4
    test_var3 = 5
    
    def inner(v):
        nonlocal test_var2
        test_var.append(v)
        # local 변수
        test_var4 = 6
        
        return None
    
    return inner


closure = outer()

closure(100)

closure(1000)


>>> dir(closure)
['__annotations__', '__call__', '__class__', '__closure__', '__code__',
 ...,
'__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__']



# ✅ 1. free variable의 저장위치를 확인하기 위해 위의 attribute 중 code를 확인
#  __code__ attribute를 확인하면 아래와 같음
>>> dir(closure.__code__)
['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', 
 '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__',
 ...,
 'co_argcount', 'co_cellvars', 'co_code', 'co_consts', 'co_filename',
 'co_firstlineno', 'co_flags', 'co_freevars', 'co_kwonlyargcount',
 'co_lnotab', 'co_name', 'co_names', 'co_nlocals', 'co_posonlyargcount',
 'co_stacksize', 'co_varnames', 'replace']



# ⭐️ co_freevars : inner 함수에서 사용하는 자유 변수를 tuple로 return
# test_var3는 inner 함수에서 사용하지 않았기 때문에 return되지 않음
>>> closure.__code__.co_freevars
('test_var', 'test_var2')


# ⭐️ co_cellvars: outer 함수에서 정의한 변수 중 inner 함수에서 사용되는 변수를 tuple로 return
# outer 함수에서 사용!
>>> outer.__code__.co_cellvars
('test_var', 'test_var2')


# ⭐️ co_nolocals : 함수에서 사용되는 local 변수의 개수 return (arguments의 개수 포함)
# 여기서는 test_var4 와 argument v로 2개가 return 됨
>>> closure.__code__.co_nlocals
2




# ✅ 2. free variable의 저장위치를 확인하기 위해 위의 attribute 중 closure를 확인
# closure 속성을 조회하면 아래와 같이 tuple에 cell이 담겨있음
>>> closure.__closure__
(<cell at 0x7fbc4d1a5b50: list object at 0x7fbc4d1bb400>,
 <cell at 0x7fbc4d1a54c0: int object at 0x108c97af0>)


# 아래와 같이 index를 통해 cell에 접근 가능
>>> closure.__closure__[0]
<cell at 0x7fbc4d1a5b50: list object at 0x7fbc4d1bb400>


# cell의 속성들을 확인하면 아래와 같은 속성들이 나타남 
>>> dir(closure.__closure__[0])
['__class__', '__delattr__', '__dir__', '__doc__', '__eq__',
 ...,
 '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'cell_contents']



# 위에서 조회한 속성 중 cell_contents를 이용하여 free variable 확인 가능

# ⭐️ 첫번째 cell_contents로 test_var에 정의된 내용을 확인할 수 있음!
# 위에서 100, 1000을 test_var에 append 했기에 append한 내용이 담겨서 나타 
>>> closure.__closure__[0].cell_contents
[100, 1000]


# ⭐️ 두번째 cell_contents로 test_var2에 정의된 내용을 확인할 수 있음!
>>> closure.__closure__[1].cell_contents
4


```

### 잘못된 클로저 사용 예시

```python

# Ex1
def closure_ex1():
	# 클로저 영역
    series = []
    
    def averager(v):
        series = [] 
        series.append(v)
        print('inner : {} / {}'.format(series, len(series)))
        return sum(series) / len(series)
    
    return averager


closure_1 =  closure_ex1()


>>> closure_1(10)
inner : [10] / 1
10.0


>>> closure_1(15)
inner : [15] / 1
15.0


>>> closure_1(35)
inner : [35] / 1
35.0


# closure_1은 위의 예시에서 정의된 avg_closure1과는 다르게 동작함
# inner function 안에 series를 정의함 → LEGB rule에 따라 local 영역에 정의된 series를 사용하게 됨
# local에 정의된 변수는 해당 함수가 종료되면 소멸되기 때문에 값이 누적되지 않게됨



# Ex2
def closure_ex2():
    # Free variable
    cnt = 0
    total = 0

    def averager(v):
        cnt += 1 
        total += v
        return total / cnt
    
    return averager


avg_closure2 = closure_ex2()


# 에러 발생
>>> avg_closure2(15)
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "<input>", line 7, in averager
UnboundLocalError: local variable 'cnt' referenced before assignment


# 위와 같은 에러가 발생하지 않으려면 아래와 같이 nonlocal keyword를 사용해주어야 함
def closure_ex2_fix():
    # Free variable
    cnt = 0
    total = 0
    
    def averager(v):
        nonlocal cnt, total
        cnt += 1
        total += v
        return total / cnt
    
    return averager


avg_closure3 = closure_ex2_fix()


>>> avg_closure3(15)
15.0


>>> avg_closure3(35)
25.0


>>> avg_closure3(40)
30.0



# Ex3
# inner func는 상위 함수 외부에서 호출 불가!!
def outer():
    x = 100
    
    def inner():
        x = 1000
        return x
    
    return inner


>>> outer()
<function outer.<locals>.inner at 0x7fbc4d1994c0> 


# 에러 발생
# 내부 함수는 상위 함수(외부 함수) 밖에서 호출 불가능
>>> inner()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
NameError: name 'inner' is not defined
```

---

# Chapter 05-04

## Decorator (데코레이터)

- 함수를 인자로 받아 새로운 함수를 만들어 반환하는 함수
- 외부함수가 내부함수를 return함
- 장점
  -  중복 제거, 코드 간결, 공통 함수 작성
  - 로깅, 프레임워크, 유효성 체크 등 공통 기능에 활용 가능 
  - 조합해서 사용 용이
- 단점
  - 가독성 감소
  - 특정 기능에 한정된 함수는 단일 함수로 작성하는 것이 유리
  - 디버깅 불편
  
### Decorator 구조

```python

# decorator 구조
# 기능 추가할 function을 인자로 받음
def decorator(func):
    # 내부함수에서 기능 추가
    def wrapper(*args, **kwargs):
        print('decorator execution')
        func(*args, **kwargs)
    # 내부함수를 return 
    return wrapper



# decorator 사용
@decorator
def test_func_1(*args, **kwargs):
    print('test_func_1 execution')
    print(f'args : {args}')
    print(f'kwargs: {kwargs}')
    return None


>>> test_func_1()
decorator execution
test_func_1 execution
args : ()
kwargs: {}


>>> test_func_1(1, 2, a=1, b=3)
decorator execution
test_func_1 execution
args : (1, 2)
kwargs: {'a': 1, 'b': 3}
```

### Decorator 동작 이해하기

decorator는 아래와 같은 동작 원리

```python
def decorator(func):
    # 내부함수에서 기능 추가
    def wrapper(*args, **kwargs):
        print('decorator execution')
        func(*args, **kwargs)
    # 내부함수를 return 
    return wrapper


def test_func_2(*args, **kwargs):
    print('test_func_2 execution')
    print(f'args : {args}')
    print(f'kwargs: {kwargs}')
    return None


decorator_func = decorator(test_func_2)


# decorator_func에 전달한 인자가 test_func_2로 전달됨
>>> decorator_func()
decorator execution
test_func_2 execution
args : ()
kwargs: {}


>>> decorator_func(1, 2, a=1, b=3)
decorator execution
test_func_2 execution
args : (1, 2)
kwargs: {'a': 1, 'b': 3}
```

### Parameter를 가지는 Decorator

Decorator에 parameter를 전달하고 싶을 때는 parameter를 전달해주는 function으로 한번 더 wrapping 하면 됨

```python
# parameter를 전달해주는 function
def parameter_decorator(params):
    def decorator(func):
        def wrapper(*args, **kwargs):
            print('parameter_decorator')
            print(params)
            print('decorator execution')
            func(*args, **kwargs)
        return wrapper
    return decorator


# decorator를 사용할 때는 parameter를 전달하는 함수 이름으로!
@parameter_decorator('test_params')
def test_func_3(*args, **kwargs):
    print('test_func_3 execution')
    print(f'args : {args}')
    print(f'kwargs: {kwargs}')
    return None


>>> test_func_3()
parameter_decorator
test_params
decorator execution
test_func_3 execution
args : ()
kwargs: {}


>>> test_func_3(1, 2, a=3)
parameter_decorator
test_params
decorator execution
test_func_3 execution
args : (1, 2)
kwargs: {'a': 3}




# 위의 동작은 아래의 동작과 같음
def test_func_4(*args, **kwargs):
    print('test_func_4 execution')
    print(f'args : {args}')
    print(f'kwargs: {kwargs}')
    return None


params_decorator_func = parameter_decorator('test_params')

decorator_func = params_decorator_func(test_func_4)


>>> decorator_func()
parameter_decorator
test_params
decorator execution
test_func_4 execution
args : ()
kwargs: {}


>>> decorator_func(1, 2, a=4)
parameter_decorator
test_params
decorator execution
test_func_4 execution
args : (1, 2)
kwargs: {'a': 4}
```

### Decorator 적용

```python
import time


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



# ✅ 데코레이터 미사용

def time_func(seconds):
    time.sleep(seconds)

    
def sum_func(*numbers):
    return sum(numbers)


none_deco1 = perf_clock(time_func)
none_deco2 = perf_clock(sum_func)


# decorator를 통해 전달받는 인자도 자유변수!
>>> none_deco1.__code__.co_freevars
('func',)


>>> none_deco2.__code__.co_freevars
('func',)


>>> none_deco1(1.5)
[1.50056s] time_func(1.5) -> None


>>> none_deco2(100, 150, 250, 300, 350)
[0.00000s] sum_func(100, 150, 250, 300, 350) -> 1150
1150



# ✅ 데코레이터 사용

@perf_clock
def time_func(seconds):
    time.sleep(seconds)

@perf_clock
def sum_func(*numbers):
    return sum(numbers)


# decorator를 통해 전달받는 인자도 자유변수!
>>> time_func.__code__.co_freevars
('func',)


>>> sum_func.__code__.co_freevars
('func',)


>>> time_func(1.5)
[1.50128s] time_func(1.5) -> None


>>> sum_func(100, 150, 250, 300, 350)
[0.00000s] sum_func(100, 150, 250, 300, 350) -> 1150
1150
```


--- 

**Reference**

[stack overflow : how-to-write-a-simple-callback-function](https://stackoverflow.com/questions/40843039/how-to-write-a-simple-callback-function/40843238)<br>
[wikidocs - partial](https://wikidocs.net/13973)<br>
[datacamp - python variable scope](https://www.datacamp.com/community/tutorials/scope-of-variables-python)<br>
[wikidocs - legb rule](https://wikidocs.net/80519)<br>
[nonlocal / closure](https://shoark7.github.io/programming/python/closure-in-python#2c)
[co_freevars](https://stackoverflow.com/questions/32221063/where-does-python-store-the-name-binding-of-function-closure)<br>
[co_cellvars](https://stackoverflow.com/questions/14413946/what-exactly-is-contained-within-a-obj-closure)<br>
[cell_contents](http://schoolofweb.net/blog/posts/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%ED%81%B4%EB%A1%9C%EC%A0%80-closure/)<br>

<br>
